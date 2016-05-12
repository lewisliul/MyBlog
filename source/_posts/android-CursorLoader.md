---
title: Android Loader解析
date: 2016-05-09 13:31:04
tags: android_data_cursorloader
---
&emsp;&emsp;在Android日常开发中，时常会请求数据到Cursor，然后再通过Cursor获取数据。像SQLiteDatabase和ContentProvider都使用了Cursor。在这些应用中，往往希望当数据发生改变时，Cursor也会自动的更新数据。这篇文章，我就会向你阐述如何通过Android自身的API实现Cursor的自动更新。 

<!-- more --> 
## 1.Cursor使用步骤

1. 在activity或fragment中getLoaderManager()
2. oncreate中调用initLoader(id, null, LoaderManager.Callbacks);

*LoaderManager Callbacks有如下方法*

**onCreateLoader() **— 创建loader（可以为android提供的cursorLoader或者自己继承AsyncLoader）,供loadmanager管理。

**onLoadFinished()** — 每次数据加载完时回调（包括数据源更改时loader监听变化时加载）。

**onLoaderReset()**— loader被重置时（不再使用）时回调。

## 2.Cursor使用原理
Android的Cursor自动更新是通过**观察者**模式实现的。

![Cursor使用原理](/images/cursor_1.png)

1. 通过ContentPorvider和ContentResolver使得数据发生了改变。
2. ContentProvider通知Cursor的观察者数据发生了改变。
3. Cursor通知CursorLoader的观察者数据发生了改变。
4. CursorLoader通过ContentProvider加载新的数据。
5. ContentPovider向DataBase请求新的数据。
6. CursorLoader调用CursorAdapter#changeCursor,用封装了新数据的Cursor替换旧的Cursor。
7. CursorAdapter告知AdapterView的观察者有新的数据。
8. AdapterView重新加载并显示数据。

在Android的android.database包下，有一个ContentObserver。Android正是通过他来实现观察者模式的。当数据改变之后，观察者会将数据改变的消息通知相应的对象，进而做出反馈。在代码中，当数据改变之后，我会调用ContentResolver#notifyChange，发出ContactContract.SYNC_SIGNAL_URI信号，通知数据发生了改变。而在此之前，从ContentProvider#query中获得的Cursor已经通过Cursor#setNotificationUri对ContactContract.SYNC_SIGNAL_URI信号进行了监视。当该信号出现，Cursor就会将信息改变的消息告诉CursorLoader的观察者（在此之前CursorLoader已经对该Cursor设立了观察者）。CursorLoader会开始重新开始加载数据。当数据加载成功，CursorLoader会通过CursorAdapter#changeCursor设置封装了新数据的Cursor。而后CursorAdapter又会通知AdapterView的观察者数据发生了改变（在此之前AdapterView已经对CursorAdapter设立了观察者）。最后AdapterView就会重新加载并显示新的数据。

![Cursor使用原理](/images/cursor_2.png)

在整个过程当中，我要做的就是在改变数据时发出信号，对封装数据的Cursor设置需要监视的信号。具体的说就是在query中调用Cursor#setNotificationUri，在insert、update、delete中调用ContentResolver#notifyChange。这里需要补充的是Cursor和ContentResolver的信号机制同样是通过观察者模式实现的。

## 3.使用代码
将所有的数据都放在数据库中，并且集合contentprovider使用。

**实体类**

```bash
public class Person {

    private int id;
    private String name;
    private String age;

    /**
     * 对应的数据库biao名 以及列名
     */
    public static final String TABLE_NAME="tb_person";
    public static final String COLUMN_NAME = "name";
    public static final String COLUMN_AGE = "age";

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getAge() {
        return age;
    }

    public void setAge(String age) {
        this.age = age;
    }
}
```

**DBopenhelper类，只创建了一张数据表**

```bash
public class PersonDbOpenHelper extends SQLiteOpenHelper {

    private static final String DB_NAME= "person.db";
    private static final int DB_VERSION= 1;

    private static PersonDbOpenHelper mHelper;

    public static PersonDbOpenHelper getInstance(Context context){
        if (mHelper==null){
            synchronized (PersonDbOpenHelper.class){
                if (mHelper==null){
                    mHelper = new PersonDbOpenHelper(context);
                }
            }
        }
        return mHelper;
    }

    private PersonDbOpenHelper(Context context) {
        super(context.getApplicationContext(),DB_NAME, null, DB_VERSION);
    }



    @Override
    public void onCreate(SQLiteDatabase db) {

        String sql = "create table " + Person.TABLE_NAME +" ("+
                " _id integer primary key autoincrement , "+
                Person.COLUMN_NAME + " text, "+
                Person.COLUMN_AGE +" text "+
                ")";
        db.execSQL(sql);
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {

    }
}
```

**provider类**实现了数据库的插入和查询操作

```bash、
public class PersonProvider extends ContentProvider {
    private static final String AUTHORITY = "com.gl.person.provider.personprovider";
    public static final Uri URI_PERSOM_ALL = Uri.parse("content://"+AUTHORITY+"/person");

    private static UriMatcher matcher;

    private static final int PERSON_ALL = 0;
    private static final int PERSON_ONE = 1;

    static{
        matcher = new UriMatcher(UriMatcher.NO_MATCH);
        matcher.addURI(AUTHORITY,"person",PERSON_ALL);
        matcher.addURI(AUTHORITY,"person/#",PERSON_ONE);
    }

    private PersonDbOpenHelper mHelper;
    private SQLiteDatabase mDb;

    @Override
    public boolean onCreate() {

        mHelper = PersonDbOpenHelper.getInstance(getContext());

        return true;
    }

    @Override
    public Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder) {
        int match = matcher.match(uri);
        switch (match){
            case PERSON_ALL:

                break;
            case PERSON_ONE:
                long id = ContentUris.parseId(uri);
                selection="_id = ?";
                selectionArgs = new String[]{String.valueOf(id)};
                break;
            default:
                throw new IllegalArgumentException("Wrong Uri:"+uri);


        }
        mDb = mHelper.getReadableDatabase();
        Cursor c= mDb.query(Person.TABLE_NAME, projection, selection, selectionArgs, null, null, sortOrder);
        ***c.setnotificationuri(getcontext().getcontentresolver(),uri_persom_all);***
        return c;
    }

    @Override
    public String getType(Uri uri) {
        return null;
    }

    @Override
    public Uri insert(Uri uri, ContentValues values) {

        int match = matcher.match(uri);
        if (match!=PERSON_ALL){
            throw new IllegalArgumentException("Wrong Uri:"+uri);
        }
        mDb = mHelper.getReadableDatabase();
        long rowId = mDb.insert(Person.TABLE_NAME,null,values);
        if (rowId>0){
           ** notifyDataSetChanged();**
            return ContentUris.withAppendedId(uri,rowId);
        }
        return null;
    }

    **private void notifyDataSetChanged() {
        getContext().getContentResolver().notifyChange(URI_PERSOM_ALL,null);
    }**

    @Override
    public int delete(Uri uri, String selection, String[] selectionArgs) {
        return 0;
    }

    @Override
    public int update(Uri uri, ContentValues values, String selection, String[] selectionArgs) {
        return 0;
    }
```

**Loader**

```bash
private void initLoader() {
        getLoaderManager().initLoader(1, null, new LoaderManager.LoaderCallbacks<Cursor>() {
            @Override
            public Loader<Cursor> onCreateLoader(int id, Bundle args) {
                CursorLoader loader = new CursorLoader(MainActivity.this,PersonProvider.URI_PERSOM_ALL,null,null,null,null);
                return loader;
            }

            @Override
            public void onLoadFinished(Loader<Cursor> loader, Cursor data) {

                mAdapter.swapCursor(data);
            }

            @Override
            public void onLoaderReset(Loader<Cursor> loader) {
                mAdapter.swapCursor(null);
            }
        });
    }
```

这个swapCursor方法是将要用的新的游标替换就得，说白了就是游标下移(个人理解)，并且通知数据变化。

**Adapter**

```bash
public class PersonAdapter extends CursorAdapter {

    public PersonAdapter(Context context, Cursor c, boolean autoRequery) {
        super(context, c, autoRequery);
    }

    //返回布局
    @Override
    public View newView(Context context, Cursor cursor, ViewGroup parent) {
        View view = LayoutInflater.from(context).inflate(R.layout.list_item,parent,false);
        return view;
    }
    //给view绑定数据
    @Override
    public void bindView(View view, Context context, Cursor cursor) {
        TextView tv_name = (TextView) view.findViewById(R.id.person_name);
        TextView tv_age = (TextView) view.findViewById(R.id.person_age);

        tv_name.setText("姓名："+cursor.getString(cursor.getColumnIndex(Person.COLUMN_NAME)));
        tv_age.setText("年龄："+cursor.getString(cursor.getColumnIndex(Person.COLUMN_AGE)));
    }
}
```

只需重写2个方法，newView，返回子view，bindView，绑定数据。 