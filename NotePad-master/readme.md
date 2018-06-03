# NotePad笔记本应用
## 1.NoteList中显示条目增加时间戳显示
## 截图以及实现：
![image](https://note.youdao.com/yws/public/resource/fa8f6069257df0b7ff7567c9aea37695/xmlnote/CD807EE98796478CA71C6D4A93953112/699)![image](https://note.youdao.com/yws/public/resource/fa8f6069257df0b7ff7567c9aea37695/xmlnote/64D7EB9BE1E7434394315F5D6C8C86DA/703)
![image](https://note.youdao.com/yws/public/resource/fa8f6069257df0b7ff7567c9aea37695/xmlnote/C003A48772574E68895D6F60DE9ABA5D/707)
### 实现代码及解释：
#### 首先要给增加的时间戳在noteslist_item.xml里面增加一个TextView,我们打开原本的noteslist_item可以看到原本的TextView如下：

```
<LinearLayout  xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <TextView xmlns:android="http://schemas.android.com/apk/res/android"
        android:id="@android:id/text1"
        android:layout_width="match_parent"
        android:layout_height="?android:attr/listPreferredItemHeight"
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:gravity="center_vertical"
        android:paddingLeft="5dip"
        android:textColor="@color/colorBlack"
        android:singleLine="true"
        />
```
#### 给它增加一个TextView以后的TextView：

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout  xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <TextView xmlns:android="http://schemas.android.com/apk/res/android"
        android:id="@android:id/text1"
        android:layout_width="match_parent"
        android:layout_height="?android:attr/listPreferredItemHeight"
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:gravity="center_vertical"
        android:paddingLeft="5dip"
        android:textColor="@color/colorBlack"
        android:singleLine="true"
        />
<!--添加一个垂直的显示布局-->
<!--添加 显示时间 的TextView-->
    <TextView
        android:id="@+id/text1_time"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textAppearance="?android:attr/textAppearanceSmall"
        android:paddingLeft="5dip"
        android:textColor="@color/colorBlack"/>

</LinearLayout>
```
#### 然后通过看NotePadProvider.java的代码可以发现，notepad数据库已经存在了时间信息，代码如下：

```
@Override
public void onCreate(SQLiteDatabase db) {
    db.execSQL("CREATE TABLE " + NotePad.Notes.TABLE_NAME + " ("
            + NotePad.Notes._ID + " INTEGER PRIMARY KEY,"
            + NotePad.Notes.COLUMN_NAME_TITLE + " TEXT,"
            + NotePad.Notes.COLUMN_NAME_NOTE + " TEXT,"
            + NotePad.Notes.COLUMN_NAME_CREATE_DATE + " INTEGER,"
            + NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE + " INTEGER,"
            + NotePad.Notes.COLUMN_NAME_BACK_COLOR + " INTEGER" //颜色数据后面补充的
            + ");");
}
```
#### 在通过看NotesList.java文件中可以知道Activity所用到的数据被定义在PROJECTION中，所以可以在PROJECTION增加扩展显示时间：

```
PROJECTION中，所以可以在PROJECTION增加扩展显示时间：
private static final String[] PROJECTION = new String[] {
        NotePad.Notes._ID, // 0
        NotePad.Notes.COLUMN_NAME_TITLE, //扩展显示时间 
        NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, // 2
        NotePad.Notes.COLUMN_NAME_BACK_COLOR,
};
```
#### 同时在dataColumns和viewIDs中补充时间部分：

```
String[] dataColumns = { NotePad.Notes.COLUMN_NAME_TITLE ,  NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE } ;
int[] viewIDs = { android.R.id.text1 , R.id.text1_time };
```
#### 做完这些，可以看到标题下确实多了一行数据，就是时间戳，可是这不是我们平常所见到的时间格式，所以需要对这些数据进行转化，转化代码如下：

```
Long now = Long.valueOf(System.currentTimeMillis());
Date date = new Date(now);
SimpleDateFormat format = new SimpleDateFormat("yyyy.MM.dd HH:mm:ss");
String dateTime = format.format(date);
```
#### 这样就实现了时间戳了
## 2.添加笔记查询功能（根据标题查询）
## 截图以及实现：
![image](https://note.youdao.com/yws/public/resource/fa8f6069257df0b7ff7567c9aea37695/xmlnote/CF07B98946F740EEAD54467C68B81BC0/740)![image](https://note.youdao.com/yws/public/resource/fa8f6069257df0b7ff7567c9aea37695/xmlnote/2C7CCE83212348258FA52E2415F68B30/744)
![image](https://note.youdao.com/yws/public/resource/fa8f6069257df0b7ff7567c9aea37695/xmlnote/9FE6397B99694B7EBCD41F0BEA9F83DC/748)![image](https://note.youdao.com/yws/public/resource/fa8f6069257df0b7ff7567c9aea37695/xmlnote/4C5CDF34FAC34726B7F502CA6CA56AA8/751)
### 代码及解释：
#### 首先和时间戳一个得先添加一个搜索的“按钮”，就是在list_options_menu.xml里面添加一个搜索的item，代码如下：

```
<item
    android:id="@+id/menu_search"
    android:title="@string/menu_search"
    android:icon="@android:drawable/ic_search_category_default"
    android:showAsAction="always">
</item>
```
#### 然后在NotesList里面添加一个搜索选项：

```
//添加搜素
case R.id.menu_search:
    Intent intent = new Intent();
    intent.setClass(NotesList.this,NoteSearch.class);
    NotesList.this.startActivity(intent);
    return true;
```
#### 接着在layout里面增加一个搜索的布局note_search_list:

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent">
    <SearchView
        android:id="@+id/search_view"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:iconifiedByDefault="false"
        android:queryHint="请输入想要搜索的内容..."
        android:layout_alignParentTop="true">
    </SearchView>

    <ListView
        android:id="@android:id/list"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
    </ListView>
</LinearLayout>
```
#### 为了动态地显示搜索结果，需要对SearchView文本设置监听，所以需要在NoteSearch里面实现SearchView.OnQueryTextListener接口：

```
@Override
public boolean onQueryTextSubmit(String query) {
    return false;
}
@Override
public boolean onQueryTextChange(String newText) {
    String selection = NotePad.Notes.COLUMN_NAME_TITLE + " Like ? ";
    String[] selectionArgs = { "%"+newText+"%" };
    Cursor cursor = managedQuery(
            getIntent().getData(),            
            PROJECTION,                       
            selection,                        // 条件左边
            selectionArgs,                    // 条件右边
            NotePad.Notes.DEFAULT_SORT_ORDER  // Use the default sort order.
    );
    String[] dataColumns = { NotePad.Notes.COLUMN_NAME_TITLE ,  NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE };
    int[] viewIDs = { android.R.id.text1 , R.id.text1_time };
    MyCursorAdapter adapter = new MyCursorAdapter(
            this,
            R.layout.noteslist_item,
            cursor,
            dataColumns,
            viewIDs
    );
    setListAdapter(adapter);
    return true;
}
@Override
protected void onListItemClick(ListView l, View v, int position, long id) {
    Uri uri = ContentUris.withAppendedId(getIntent().getData(), id);
    String action = getIntent().getAction();
    if (Intent.ACTION_PICK.equals(action)  Intent.ACTION_GET_CONTENT.equals(action)) {
        setResult(RESULT_OK, new Intent().setData(uri));
    } else {
        startActivity(new Intent(Intent.ACTION_EDIT, uri));
    }
}

```
#### 下一步需要在MyCursorAdapter里面的onQueryTextChange方法中，为SearchView注册监听：

```
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.note_search_list);
    Intent intent = getIntent();
    if (intent.getData() == null) {
        intent.setData(NotePad.Notes.CONTENT_URI);
    }
    SearchView searchview = (SearchView)findViewById(R.id.search_view);
    searchview.setOnQueryTextListener(NoteSearch.this);  //为查询文本框注册监听器
}
```
#### 最后在AndroidManifest.xml添加NoteSearch：

```
<!--添加搜索activity-->
    <activity
        android:name="NoteSearch"
        android:label="@string/title_notes_search">
</activity>
```
#### 这样就实现了笔记查询功能
## 附加功能：根据自身实际情况进行扩充，以下是实现的扩展功能
## 3.UI美化
## 4.更改记事本的背景
## 截图以及实现：
![image](https://note.youdao.com/yws/public/resource/fa8f6069257df0b7ff7567c9aea37695/xmlnote/E19CFA4C37D041E0A380CD6886523619/781)![image](https://note.youdao.com/yws/public/resource/fa8f6069257df0b7ff7567c9aea37695/xmlnote/DAEE522AF20D43AAB684A293B61FFEB3/785)
![image](https://note.youdao.com/yws/public/resource/fa8f6069257df0b7ff7567c9aea37695/xmlnote/0B9E2709F75C4F95BF3C21788CDA11AA/789)![image](https://note.youdao.com/yws/public/resource/fa8f6069257df0b7ff7567c9aea37695/xmlnote/26DBD12ACDBF4B61AD5CEE4E392F1349/791)
![image](https://note.youdao.com/yws/public/resource/fa8f6069257df0b7ff7567c9aea37695/xmlnote/CC877EDB99C34C8C9679BC2FD7DA6C46/796)![image](https://note.youdao.com/yws/public/resource/fa8f6069257df0b7ff7567c9aea37695/xmlnote/19BCB44B68F8423FB6575BD4A3DF1E01/798)
### 代码及解释：
#### UI美化：把NotesList背景颜色换成白色，代码是在AndroidManifest.xml中NotesList的Activity中添加：

```
android:theme="@android:style/Theme.Holo.Light"
```
#### 更改记事本的背景：
#### 在values增加一个colors.xml来添加颜色，代码如下：

```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="colorBlack">#000000</color>
    <color name="colorYellow">#FFFF00</color>
    <color name="colorBlue">#0000FF</color>
    <color name="colorGreen">#00FF00</color>
    <color name="colorRed">#FF0000</color>
    <color name="colorWhite">#FFFFFF</color>
</resources>
```
#### 在layout里面增加一个颜色布局，用来显示颜色选项，代码如下：

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent">
    <ImageButton
        android:id="@+id/color_white"
        android:layout_width="50dp"
        android:layout_height="50dp"
        android:layout_weight="1"
        android:background="@color/colorWhite"
        android:onClick="white"/>
    <ImageButton
        android:id="@+id/color_yellow"
        android:layout_width="50dp"
        android:layout_height="50dp"
        android:layout_weight="1"
        android:background="@color/colorYellow"
        android:onClick="yellow"/>
    <ImageButton
        android:id="@+id/color_blue"
        android:layout_width="50dp"
        android:layout_height="50dp"
        android:layout_weight="1"
        android:background="@color/colorBlue"
        android:onClick="blue"/>
    <ImageButton
        android:id="@+id/color_green"
        android:layout_width="50dp"
        android:layout_height="50dp"
        android:layout_weight="1"
        android:background="@color/colorGreen"
        android:onClick="green"/>
    <ImageButton
        android:id="@+id/color_red"
        android:layout_width="50dp"
        android:layout_height="50dp"
        android:layout_weight="1"
        android:background="@color/colorRed"
        android:onClick="red"/>
</LinearLayout>
```
#### 在NoteEditor中增加颜色选项并读取颜色数据，代码如下：

```
private static final String[] PROJECTION =
        new String[] {
            NotePad.Notes._ID,
            NotePad.Notes.COLUMN_NAME_TITLE,
            NotePad.Notes.COLUMN_NAME_NOTE,
            NotePad.Notes.COLUMN_NAME_BACK_COLOR
    };
 //读取颜色数据做准备
        int x = mCursor.getInt(mCursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_BACK_COLOR));

        switch (x){
            case NotePad.Notes.DEFAULT_COLOR:
                mText.setBackgroundColor(Color.rgb(255, 255, 255));
                break;
            case NotePad.Notes.YELLOW_COLOR:
                mText.setBackgroundColor(Color.rgb(255, 255, 0));
                break;
            case NotePad.Notes.BLUE_COLOR:
                mText.setBackgroundColor(Color.rgb(0, 0, 255));
                break;
            case NotePad.Notes.GREEN_COLOR:
                mText.setBackgroundColor(Color.rgb(0, 255, 0));
                break;
            case NotePad.Notes.RED_COLOR:
                mText.setBackgroundColor(Color.rgb(255, 0, 0));
                break;
            default:
                mText.setBackgroundColor(Color.rgb(255, 255, 255));
                break;
        }
    } else {
        setTitle(getText(R.string.error_title));
        mText.setText(getText(R.string.error_message));
    }
}
```
#### 在editor_options_menu.xml增加一个更改背景选项：

```
<item android:id="@+id/menu_color"
        android:title="@string/menu_color"
        android:icon="@drawable/ic_menu_color"
        android:showAsAction="always"/>
```
#### 同样的在NoteEditor中的onOptionsItemSelected()方法中，switch里面添加更换颜色背景选项：

```
case R.id.menu_color:
        changeColor();
        break;
```
#### 并且添加函数changecolor（）跳转到更改了颜色的activity中：

```
private final void changeColor() {
        Intent intent = new Intent(null,mUri);
        intent.setClass(NoteEditor.this,NoteColor.class);
        NoteEditor.this.startActivity(intent);
    }
```
#### 最后添加一个Notecolor实现跳转，并在AndroidMainfest.xml里面添加颜色背景的替换：

```
Notecolor.java:
package com.example.android.notepad;

import android.app.Activity;
import android.content.ContentValues;
import android.database.Cursor;
import android.net.Uri;
import android.os.Bundle;
import android.view.View;

/**
 * Created by zhx on 2018.6.2
 */

public class NoteColor extends Activity {

    private Cursor mCursor;
    private Uri mUri;
    private int color;
    private static final int COLUMN_INDEX_TITLE = 1;

    private static final String[] PROJECTION = new String[] {
            NotePad.Notes._ID, // 0
            NotePad.Notes.COLUMN_NAME_BACK_COLOR,
    };

    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.note_color);
        mUri = getIntent().getData();
        mCursor = managedQuery(
                mUri,        // The URI for the note that is to be retrieved.
                PROJECTION,  // The columns to retrieve
                null,        // No selection criteria are used, so no where columns are needed.
                null,        // No where columns are used, so no where values are needed.
                null         // No sort order is needed.
        );

    }

    @Override
    protected void onResume(){
        if (mCursor != null) {
            mCursor.moveToFirst();
            color = mCursor.getInt(COLUMN_INDEX_TITLE);
        }
        super.onResume();
    }

    @Override
    protected void onPause() {
        super.onPause();
        ContentValues values = new ContentValues();
        values.put(NotePad.Notes.COLUMN_NAME_BACK_COLOR, color);
        getContentResolver().update(mUri, values, null, null);

    }

    public void white(View view){
        color = NotePad.Notes.DEFAULT_COLOR;
        finish();
    }

    public void yellow(View view){
        color = NotePad.Notes.YELLOW_COLOR;
        finish();
    }

    public void blue(View view){
        color = NotePad.Notes.BLUE_COLOR;
        finish();
    }

    public void green(View view){
        color = NotePad.Notes.GREEN_COLOR;
        finish();
    }

    public void red(View view){
        color = NotePad.Notes.RED_COLOR;
        finish();
    }

}
```
#### AndroidMainfest.xml:

```
<!--换背景色-->
<activity android:name="NoteColor"
    android:theme="@android:style/Theme.Holo.Light.Dialog"
    android:label="ChangeColor"
    android:windowSoftInputMode="stateVisible"/>
```
## 5.按需排序
## 截图以及实现：
## 刚开始：
![image](https://note.youdao.com/yws/public/resource/fa8f6069257df0b7ff7567c9aea37695/xmlnote/FB18401416B445949408E13C8979A6B9/829)![image](https://note.youdao.com/yws/public/resource/fa8f6069257df0b7ff7567c9aea37695/xmlnote/F8362CC820DE4CF08D4FE15921985ED3/831)
## 按创建时间排序：
![image](https://note.youdao.com/yws/public/resource/fa8f6069257df0b7ff7567c9aea37695/xmlnote/26863521A687423F9F245994D95611BB/838)
## 按修改时间排序：
![image](https://note.youdao.com/yws/public/resource/fa8f6069257df0b7ff7567c9aea37695/xmlnote/A6CC269974BF40DC9A2774BD539F1005/847)
## 按背景颜色排序： 
![image](https://note.youdao.com/yws/public/resource/fa8f6069257df0b7ff7567c9aea37695/xmlnote/5B28D00119D048639F5A955C78780AEB/849)
### 代码以及解释：
#### 笔记排序的原理是将Cursor的排序参数变换就可以实现排序了。在菜单文件list_options_menu.xml中添加：

```
<item
    android:id="@+id/menu_sort"
    android:title="@string/menu_sort"
    android:icon="@android:drawable/ic_menu_sort_by_size"
    android:showAsAction="always" >
    <menu>
        <item
            android:id="@+id/menu_sort1"
            android:title="按笔记创建时间排序"/>
        <item
            android:id="@+id/menu_sort2"
            android:title="按笔记修改时间排序"/>
        <item
            android:id="@+id/menu_sort3"
            android:title="按皮肤背景颜色排序"/>
    </menu>
</item>
```
#### 在NotesList菜单switch下添加case：

```
//创建时间排序
case R.id.menu_sort1:
    cursor = managedQuery(
            getIntent().getData(),
            PROJECTION,
            null,
            null,
            NotePad.Notes._ID
    );
    adapter = new MyCursorAdapter(
            this,
            R.layout.noteslist_item,
            cursor,
            dataColumns,
            viewIDs
    );
    setListAdapter(adapter);
    return true;

//修改时间排序
case R.id.menu_sort2:
    cursor = managedQuery(
            getIntent().getData(),
            PROJECTION,
            null,
            null,
            NotePad.Notes.DEFAULT_SORT_ORDER
    );

    adapter = new MyCursorAdapter(
            this,
            R.layout.noteslist_item,
            cursor,
            dataColumns,
            viewIDs
    );
    setListAdapter(adapter);
    return true;

//颜色排序
case R.id.menu_sort3:
    cursor = managedQuery(
            getIntent().getData(),
            PROJECTION,
            null,
            null,
            NotePad.Notes.COLUMN_NAME_BACK_COLOR
    );
    adapter = new MyCursorAdapter(
            this,
            R.layout.noteslist_item,
            cursor,
            dataColumns,
            viewIDs
    );
    setListAdapter(adapter);
    return true;
```
#### 最后定义类外函数adapter,cursor,dataColumns,viewIDs：

```
private MyCursorAdapter adapter;
private Cursor cursor;
private String[] dataColumns = { NotePad.Notes.COLUMN_NAME_TITLE ,  NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE } ;
private int[] viewIDs = { android.R.id.text1 , R.id.text1_time }；
```
