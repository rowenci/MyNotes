#Android中SQLite数据库的应用

##1.创建一个表类
表类中存在一个无参的构造方法，该构造方法的作用是防止在MainActivity中实例化出该类的对象。

	private InfoDatabase(){}

第二个为静态抽象类，继承自BaseColumns。其中可以添加表名，和该表内的各种属性。

	public static abstract class InfoData implements BaseColumns{

		public static final String TABLE_NAME = "text_data";

		public static final String COLUMN_NAME_NAME = "name";

		public static final String COLUMN_NAME_AGE = "age";

	}

**注：数据库中的表除了在该类中所写出的几个属性外，还存在一个_ID属性，该属性是由该类继承自BaseColumns得来的**

##2.创建一个SQLliteHelper类
该类用来创建数据库和数据库中的表，继承自SQLiteOpenHelper

该类的构造方法是用来创建数据库的方法。

	public MyDBHelper(Context context){
		super(context,DB_NAME,null,DB_VERSION);
	}

super中第一个参数为上下文，第二个参数为数据库名字，第三个参数为CursorFactory,第四个参数为数据库的版本（一般为1）。

该类中还存在两个继承自SQLiteOpenHelper的方法：onCreate()和onUpgrade()方法。

onCreate方法的作用：创建数据库中的表

	public void onCreate(SQLiteDatabase sqLiteDatabase){

		sqLiteDatabase.execSQL(SQL_CREATE_TABLE);

SQL_CREATE_TABLE为一个字符串，该字符串是创建表的SQL语句。

onUpgrade方法的作用：当版本号大于上一个版本号时，会执行该方法中的代码，对数据库进行更新。

**注：要创建数据库，还必须调用getWritableDatabase()方法或getReadableDatabase()方法，否则数据库不会创建**

##3.数据的插入
首先创建一个SQLiteDatabase对象并调用getWritableDatabase()方法或getReadableDatabase()方法。

	SQLiteDatabase db = myDBHelper.getWritableDatabase();

当执行该语句后，数据库创建成功。

若要插入数据，其中一个方法为实例化一个ContentValues对象，并调用put()方法。

	ContentValues contentValues = new ContentValues();
	contentValues.put(列名，值);

该方法存储的是一个键值对。put进去之后，调用db的insert()方法。

	db.insert(表名，_ID,contentValues);
	db.close();

**注：在使用完db之后，需要调用close()方法关闭数据库**

##4.数据的查找
首先创建一个SQLiteDatabase对象并调用getWritableDatabase()方法或getReadableDatabase()方法。

	SQLiteDatabase db = myDBHelper.getWritableDatabase();

若要查询数据，可以使用Cursor对象。该对象中的query()方法提供了一个返回值，可以用来当做数据的查询工具

	Cursor cursor = db.query(InfoDatabase.InfoData.TABLE_NAME, null, null, null, null, null, null);

Cursor还提供一个游标方法，用来标记读取的位置，可以用一个循环来遍历表中的数据

	while(cursor.moveToNext()){
		String name = cursor.getString(cursor.getColumnIndex(列名));
		int age = cursor.getint(cursor.getColumnIndex(列名));
	}
	cursor.close();

**注：与db相同，cursor在使用完之后也需要close()**

##5.数据库的删除
首先创建一个SQLiteDatabase对象并调用getWritableDatabase()方法或getReadableDatabase()方法。

	SQLiteDatabase db = myDBHelper.getWritableDatabase();

调用delete()方法来删除

	db.delete(表名，where字句，where的实际参数)

若要删除所有行，则后两个参数均为null
	
	db.close();
