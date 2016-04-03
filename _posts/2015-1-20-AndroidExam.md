---
title: Android期末考资料
layout: post
tags:
  - android
---

Android期末机考资料。

**Activity跳转与返回**

    mButton.setOnClickListener(new View.OnClickListener() {//第一个Activity跳转到第二个
        public void onClick(View v) {
            Intent mIntent = new Intent(MyActivity.this, NewActivity.class);
            Bundle mBundle = new Bundle();
            String s = mEditText.getText().toString();
            mBundle.putString("info", s);
            mIntent.putExtras(mBundle);
            startActivityForResult(mIntent, 1);
        }
    });
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {//第一个Activity从第二个接收信息
        String s = data.getExtras().getString("result");
        mTextView.setText(s);
    }
    //第二个Activity从第一个获取信息
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_new);
    Bundle mBundle = this.getIntent().getExtras();
    String s = mBundle.getString("info");
    //第二个Activity向第一个发送信息
    Intent intent = new Intent();
    String s = mEditText.getText().toString();
    intent.putExtra("result", s);
    NewActivity.this.setResult(RESULT_OK, intent);
    NewActivity.this.finish();

**ListView与SQLite**

    db.execSQL("create table contacts (_id integer primary key autoincrement, student_id varchar(10))");//创建DataBaseHelper extends SQLiteOpenHelper，onCreate内执行
    private DataBaseHelper helper;
    private SimpleCursorAdapter cursorAdapter;
    private Cursor cursor;
    Context context=this;
    public void inflateListView(Cursor cursor) {//更新ListView
        cursorAdapter = new SimpleCursorAdapter(context,R.layout.item,cursor,new String[]{"student_id"},new int[]{R.id.student_id}, 0);
        //其他ListView类似，把cursor改为ArrayList<Map<String, Object>>
        mListView.setAdapter(cursorAdapter);
    }
    String s = mEditText.getText().toString();//增
    ContentValues values = new ContentValues();
    values.put("student_id", s + " ");
    SQLiteDatabase db = helper.getWritableDatabase();
    db.insert("contacts", null, values);
    cursor = helper.getReadableDatabase().query("contacts", null, null, null, null, null, null);
    inflateListView(cursor);
    mListView.setOnItemLongClickListener(new AdapterView.OnItemLongClickListener() {//删
        @Override
        public boolean onItemLongClick(AdapterView<?> parent, View view, final int position, long id) {
            AlertDialog.Builder builder = new AlertDialog.Builder(MyActivity.this);
            builder.setMessage("delete?");
            builder.setPositiveButton("confirm", new DialogInterface.OnClickListener() {
                @Override
                public void onClick(DialogInterface dialogInterface, int i) {
                    cursor = helper.getReadableDatabase().query("contacts", null, null, null, null, null, null);
                    cursor.moveToPosition(position);
                    long id = cursor.getLong(0);
                    SQLiteDatabase db = helper.getWritableDatabase();
                    db.execSQL("delete from contacts where _id =" + id);
                    cursor = helper.getReadableDatabase().query("contacts", null, null, null, null, null, null);
                    inflateListView(cursor);
                }
            });
            builder.setNegativeButton("cancel", new DialogInterface.OnClickListener() {
                @Override
                public void onClick(DialogInterface dialogInterface, int i) {
                    dialogInterface.dismiss();
                }
            });
            builder.create().show();
            return true;
        }
    });
    String whereClause = "_id=?";//改
    String[] whereArgs = {String.valueOf(id)};
    db.update("contacts", values, whereClause, whereArgs);

**自动补全(SharedPreferences)**

    private static final String DATABASE = "Database";
    private static final String PATH = "/data/data/com.example.lufo.ex/shared_prefs/Database.xml";
    private SharedPreferences mSharedPreferences;
    private SharedPreferences.Editor mEditor;
    private ArrayList<String> autoStr = new ArrayList<String>();
    private ArrayAdapter<String> autoAdapter;
    mSharedPreferences = getSharedPreferences(DATABASE, Activity.MODE_PRIVATE);
    mEditor = mSharedPreferences.edit();
    Map<String, ?> mMap = mSharedPreferences.getAll();
    for (Map.Entry entry : mMap.entrySet())
        autoStr.add(entry.getKey().toString());
    autoAdapter = new ArrayAdapter<String>(this, android.R.layout.simple_dropdown_item_1line, autoStr);
    mEditor.putString(s, "");//add
    autoStr.add(s);
    mEditor.commit();
    autoAdapter = new ArrayAdapter<String>(MyActivity.this, android.R.layout.simple_dropdown_item_1line, autoStr);
    mEditor.remove(usernameInput.getText().toString());//delete
    mEditor.commit();
    autoStr.remove(usernameInput.getText().toString());
    autoAdapter = new ArrayAdapter<String>(MyActivity.this, android.R.layout.simple_dropdown_item_1line, autoStr);
    mEditText.setAdapter(autoAdapter);
    mEditText.setThreshold(1);

**多线程**

    final Handler handler = new Handler() {
        @Override
        public void handleMessage(Message msg) {
            Bundle b = msg.getData();
            String state = b.get("state").toString();
        }
    };
     class DownloadThread implements Runnable {
        @Override
        public void run() {
            Bundle b = new Bundle();
            b.putString("state", "test");
            Message msg = handler.obtainMessage();
            msg.setData(b);
            handler.sendMessage(msg);
        }
    }
    new Thread(new DownloadThread()).start();