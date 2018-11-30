---
layout: post
title:  "Android 数据库创建和增删改查"
date:   2013-10-12
image: password.jpg
description: 数据库的基本用法
category: android
tags: [android, sqlite, SqliteOpenHelper, transaction]

---

## 创建数据库帮助类
继承SQLiteOpenHelper

{% highlight java %}
public class DBHelper extends SQLiteOpenHelper {
 
    public DBHelper(Context context) {
        //上下文、数据库名、游标工厂（null为使用系统默认）、数据库版本号（不小于1）
        super(context, "hello.db", null, 1);
    }
 
    //在第一次创建数据库时被调用
    @Override
    public void onCreate(SQLiteDatabase db) {
        //这里可以写创建表代码
    db.execSQL("create table seesee (" +
        "_id integer primary key autoincrement," +
        "name varchar(20)," +
        "age integer)");
    }
 
    //数据库升级，当新版本号大于旧版本时调用
    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
    //可以删除表和调用onCreate()重新创建表
    if(oldVersion < newVersion){
        db.execSQL("DROP TABLE IF EXISTS seesee");
        onCreate(db);
    }
         
    }
}
{% endhighlight %}

## 增删改查

{% highlight java %}
public class DBDao {
 
    private DBHelper dbHelper;
     
    public DBDao(Context context){
    dbHelper = new DBHelper(context);
    }
 
    //插入一条数据
    public void insert(){
    SQLiteDatabase db = dbHelper.getWritableDatabase();
    if(db.isOpen()){
       db.execSQL("insert into seesee (name,age) values( ?, ? )" , new Object[]{ "see" , 15});
 
           //或者用使用系统封装好的api进行插入如下
       //ContentValues values = new ContentValues();
       //values.put("name", "see");
       //values.put("age", 16);
       //db.insert("seesee", null, values);
 
       db.close(); //用完关闭
    }
    }
    //删除一条数据
    public void delete(){
    SQLiteDatabase db = dbHelper.getWritableDatabase();
    if(db.isOpen()){
        db.execSQL("delete from seesee where name=?" , new String[]{ "see" });
 
            //db.delete("seesee", "name=?", new String[]{ "see" });
 
        db.close();
    }
    }
    //更新一条数据
    public void update(){
    SQLiteDatabase db = dbHelper.getWritableDatabase();
    if(db.isOpen()){
        db.execSQL("update seesee set (name) values ( ? ) where name=?" , new String[]{"see" ,"jack"});
 
            //ContentValues values = new ContentValues();
        //values.put("name", "jack");
        //db.update("seesee", values, "name=?", new String[]{"see"});
 
        db.close();
    }
    }
    //查询数据
    public int select(){
    SQLiteDatabase db = dbHelper.getWritableDatabase();
    int id = -1;
    if(db.isOpen()){
        Cursor cursor = db.rawQuery("select _id from seesee where name=?", new String[]{ "see" });
 
            //cursor = db.query("seesee", null, " name=?", new String[]{"see"}, null, null, null);
 
        if(cursor.moveToFirst()){
            id = cursor.getInt(0);
        }
        cursor.close();
        db.close();
        }
    return id;
    }
}
{% endhighlight %}

## 事务

{% highlight java %}
//1.打开数据库事务
db.beginTransaction();
//2.执行语句
db.execSQL("delete from see where name=?",new String[]{"see"});
//3.设置事务状态（true）,
db.setTransactionSuccessful();
//4.事务状态为true提交数据,false使回滚操作
db.endTransaction();
{% endhighlight %}
