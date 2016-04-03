---
title: 用word2vec表示短文本特征
layout: post
tags:
  - python
  - nlp
  - word2vec
---

今年做人工智能课的TA,第一个实验是用KNN计算文本的情感分布,具体任务是挑战[SemEval-2007](http://nlp.cs.swarthmore.edu/semeval/)中的Task 14.有200多个训练文本,1000个测试文本,文本是文章标题,都是短文本.文本对应6种情感,已知训练文本中每个文本属于某个情感的概率,要预测测试文本属于每个情感的概率,然后计算预测值和真实值间的相关系数.

开始用词带模型+KNN,k=10的时候六种情感平均相关系数是0.27.想试一下word2vec可不可以表示短文本的特征,然后在[word2vec官网](https://code.google.com/p/word2vec/)下载了用Google News数据训练好的模型,然后用word2vec表示短文本特征,具体做法是每篇文本的特征表示为这篇文本中词的特征乘以词出现的次数然后相加,最后归一化.用这种方法+KNN得到的平均相关系数为0.30(k=10).下面是具体代码:

{% highlight python %}
# -*- coding: utf-8 -*-
__author__ = 'lufo'

from sklearn.feature_extraction.text import CountVectorizer
from sklearn.decomposition import PCA
from sklearn.preprocessing import normalize
from gensim.models import Word2Vec
import numpy as np
import os

NUMBER_OF_TRAIN_TEXT = 246
NUMBER_OF_TEST_TEXT = 1000
EMOTION_LIST = ['anger', 'disgust', 'fear', 'joy', 'sad', 'surprise']


def get_text_feature():
    """
    获取训练集和测试集文本的特征
    :return: narray,特征,i行j列表示第i个文本第j个特征的值
    """
    # 读入stopwords
    stop_words = []
    with open('stopwords.txt') as fr:
        for word in fr.readlines():
            stop_words.append(word.strip())
    stop_words = set(stop_words)
    # 读入训练集和测试集并用sklearn中的CountVectorizer方法获得每个文本中每个词出现的次数
    vectorizer = CountVectorizer(min_df=1)
    with open('Dataset_words.txt') as fr:
        corpus = []
        for text in fr.readlines()[1:]:
            corpus.append(' '.join(set(text.strip().split('\t')[1].split()) - stop_words))

    train_feature_mat = vectorizer.fit_transform(corpus[:NUMBER_OF_TRAIN_TEXT])
    test_feature_mat = vectorizer.transform(corpus[NUMBER_OF_TRAIN_TEXT:])
    word_list = vectorizer.get_feature_names()
    feature_mat = np.zeros((NUMBER_OF_TRAIN_TEXT + NUMBER_OF_TEST_TEXT, 300))
    num_of_word_in_every_text = np.concatenate(
        (train_feature_mat.toarray(), test_feature_mat.toarray()))  # i行j列表示第i个文本第j个单词出现的次数
    # 用word2vec表示文本特征,每个文本特征为所有单词的词向量相加
    model = Word2Vec.load_word2vec_format('/Users/lufo/Downloads/GoogleNews-vectors-negative300.bin', binary=True)
    for i in xrange(NUMBER_OF_TRAIN_TEXT + NUMBER_OF_TEST_TEXT):
        for j, word in enumerate(word_list):
            if num_of_word_in_every_text[i][j] and word in model:
                feature_mat[i] += model[word] * num_of_word_in_every_text[i][j]
    print feature_mat.shape
    return normalize(feature_mat, norm='l1')


def get_train_emotion():
    """
    获取训练集各个文本的情感分布,结果进行l1归一化
    :return: array,i行j列表示第i个训练集在第j个情感上的概率,情感分别是['anger', 'disgust', 'fear', 'joy', 'sad', 'surprise']
    """
    emotion_mat = []
    for emotion in EMOTION_LIST:
        temp = []
        with open(''.join(['Dataset_words_', emotion, '.txt'])) as fr:
            for text in fr.readlines()[1:NUMBER_OF_TRAIN_TEXT + 1]:  # 有246个训练文本
                temp.append(float(text.strip().split('\t')[2]))
        emotion_mat.append(temp)
    return normalize(np.array(emotion_mat).transpose(), norm='l1')


def pca(n_components, data):
    """
    对数据进行pca降维
    :param n_components: 保留的特征数
    :param data: 原始数据
    :return: 降维后的数据
    """
    pca = PCA(n_components=n_components)
    new_data = pca.fit_transform(data)
    return new_data


def knn(k, train, train_label, test):
    """
    用knn预测
    :return: array,i行j列为第i个测试文本属于第j个情感的概率
    """
    predict_emotion_mat = np.zeros((NUMBER_OF_TEST_TEXT, 6))
    for i, test_item in enumerate(test):
        dis = []
        for train_item in train:
            dis.append(np.linalg.norm(test_item - train_item))  # 欧氏距离
        nearest_item = range(NUMBER_OF_TRAIN_TEXT)
        nearest_item.sort(key=lambda x: dis[x])
        for index in nearest_item[:k]:
            predict_emotion_mat[i] += train_label[index]
    return normalize(predict_emotion_mat, norm='l1')


def nb(train, train_label, test):
    """
    使用朴素贝叶斯预测
    :return: array,i行j列为第i个测试文本属于第j个情感的概率
    """
    train = train.transpose()
    word_emotion_mat = np.dot(train, train_label)  # i行j列为第i个单词与对应的情感的相关度
    word_count = np.sum(train)  # 训练集所有单词总数
    word_count_mat = np.sum(train, axis=1)  # 1*词的个数,第i个元素为第i个词出现的次数
    word_emotion_mat = word_emotion_mat * word_count_mat.reshape(word_count_mat.shape[0], 1) / word_count
    predict_emotion_mat = np.dot(test, word_emotion_mat)
    return normalize(predict_emotion_mat, norm='l1')


def write_to_file(predict_emotion_mat, path):
    """
    将预测结果写入path中的文件,每个情感写入一个文件,如joy写入joy_predict.txt,每行为预测的概率
    :param predict_emotion_mat: array,i行j列为第i个测试文本属于第j个情感的概率
    """
    for i, emotion in enumerate(EMOTION_LIST):
        with open(os.path.join(path, emotion + '_predict.txt'), 'w') as fw:
            for p in predict_emotion_mat[:, i]:
                fw.write(str(p) + '\n')


def main():
    feature_mat = get_text_feature()
    # feature_mat = pca(100, feature_mat)
    emotion_mat = get_train_emotion()
    predict_emotion_mat = knn(10, feature_mat[:NUMBER_OF_TRAIN_TEXT], emotion_mat, feature_mat[NUMBER_OF_TRAIN_TEXT:])
    # predict_emotion_mat = nb(feature_mat[:NUMBER_OF_TRAIN_TEXT], emotion_mat, feature_mat[NUMBER_OF_TRAIN_TEXT:])
    write_to_file(predict_emotion_mat, '/Users/lufo/PycharmProjects/AI/Lab2/predict_test')


if __name__ == '__main__':
    main()

{% endhighlight %}