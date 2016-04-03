---
title: Docker Monitoring方案
layout: post
tags:
  - daily
  - docker
---

目前主流方案是使用cAdvisor+InfluxDB+Grafana进行Docker监控.**这三个工具都以Docker镜像的方式运行在主机上,不依赖于一个Monitor服务器,这样就解决了第一个问题.**

cAdvisor是Google发布的监控工具,已在Google内部使用了很久.它可以采集Docker的数据,在网页上可视化的呈现这些数据,并且配合InfluxDB可以将数据存储在数据库中.cAdvisor可以采集这些数据:'time', 'sequence_number', 'rx_errors', 'machine', 'fs_usage', 'memory_usage', 'fs_device', 'container_name', 'cpu_cumulative_usage', 'fs_limit', 'memory_working_set', 'rx_bytes', 'tx_bytes', 'tx_errors',**可以改变参数控制cAdvisor采集数据的频率,数据留存时间等信息,这样就解决了第三个问题.具体可参考[cAdvisor Runtime Options](https://github.com/google/cadvisor/blob/master/docs/runtime_options.md).**

InfluxDB是一个专为时序数据设计的数据库,将cAdvisor与InfluxDB连接后,写入的数据有三个字段:name,columns,points.name可以理解为数据库中表的名字,columns为一个list,保存刚才说到的cAdvisor可以采集的数据的名字,points为一个list,points的每个元素也是一个list,保存某一时刻采集到的数据,与columns中元素一一对应.可以通过Python接口访问InfluxDB.

Grafana是一个更好的数据可视化工具,目前没有深入研究这个工具.

**为了解决第二个问题,可以考虑使用Kubernetes等Docker集群管理工具.**如果暂时不使用这类工具的话,需要在每台节点上部署cAdvisor,在数据存储节点部署InfluxDB, Grafana在任意节点部署一次即可.

具体的部署方法如下,注意版本号与教程中一致,否则可能无法运行.

部署InfluxDB:

	docker run -d --volume=/var/influxdb:/data -p 8083:8083 -p 8086:8086 --expose 8090 --expose 8099 -e PRE_CREATE_DB=cadvisor --name influxsrv tutum/influxdb:0.8.8
	
--volume=/var/influxdb:/data可以将container中的/data目录映射到/var/influxdb,这样删除InfluxDB的container后仍然可以保留数据.
	
部署cAdvisor:

	docker run \
	--volume=/:/rootfs:ro \
	--volume=/var/run:/var/run:rw \
	--volume=/sys:/sys:ro \
	--volume=/var/lib/docker/:/var/lib/docker:ro \
	--publish=8080:8080 \
	--link=influxsrv:influxsrv  \
	--detach=true \
	--name=cadvisor \
	google/cadvisor:0.14.0 \
	-storage_driver=influxdb \
	-storage_driver_db=cadvisor \
	-storage_driver_host=influxsrv:8086 \
	--global_housekeeping_interval=1m0s \
	--housekeeping_interval=10s

最后两个参数分别指定检测新的container的频率和检测container状态的频率.-storage_driver_host的值为部署了InfluxDB的节点的ip+对应端口.此时访问http://host_ip:8080已经可以看到主机中Docker资源使用情况.

部署Grafana:
	
	docker run -d -p 3000:3000 \
	-e HTTP_USER=admin \
	-e HTTP_PASS=admin \
	-e INFLUXDB_HOST=localhost \
	-e INFLUXDB_PORT=8086 \
	-e INFLUXDB_NAME=cadvisor \
	-e INFLUXDB_USER=root \
	-e INFLUXDB_PASS=root \
	--link=influxsrv:influxsrv  \
	grafana/grafana:2.0.2

关于如何部署更详细的内容可以参考参考[这里](https://dockerhanoi.wordpress.com/2015/08/19/docker-monitoring-with-cadvisor-influxdb-and-grafana/).

Reference:

- [cadvisor](https://github.com/google/cadvisor)
- [influxdb](https://github.com/influxdb/influxdb)
- [influxdb-python](https://github.com/influxdb/influxdb-python)
- [Kubernetes技术分析之监控](http://www.dockone.io/article/569)
- [Docker monitoring with cAdvisor, InfluxDB and Grafana](https://dockerhanoi.wordpress.com/2015/08/19/docker-monitoring-with-cadvisor-influxdb-and-grafana/)
- [HOW TO SETUP DOCKER MONITORING](https://www.brianchristner.io/how-to-setup-docker-monitoring/)