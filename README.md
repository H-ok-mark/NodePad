# NotePad

## 一、项目概述

这是一个 Android 笔记应用（Notepad），旨在为用户提供一个简洁、易用的记事工具。项目基于基础的笔记管理功能（如创建、编辑、删除笔记），并在此基础上进行了拓展和优化。目标是提高用户体验和提升功能的多样性。

### 基础功能

+  **1.时间戳：** 每次保存笔记时自动生成时间戳，方便记录笔记时间。

+  **2.笔记搜索：** 通过模糊搜索笔记标题，帮助你高效找到所需的笔记

### 拓展功能

+ **1.UI美化**

  + 更换笔记条目背景
  
  + 增加笔记条目的摘要显示
  
+ **2.笔记文本导出：** 支持将笔记内容导出为文本文件，便于备份和分享。

+ **3.可自选改变笔记颜色：** 支持自选笔记内容的字体颜色，增强用户体验。

## 二、效果展示

### + 时间戳+UI+摘要显示
![image](https://github.com/user-attachments/assets/a589ef6b-a9b0-4fdc-a77c-5c2156b39a91)
### + 搜索功能
<div>
  <img src="https://github.com/user-attachments/assets/996b7f00-11bc-404e-8fa3-ddd8751106f7" width="45%" style="display:inline-block; margin-right:5%">
  <img src="https://github.com/user-attachments/assets/85d1bb61-0ca3-4748-b0be-e06f0e141629" width="45%" style="display:inline-block;">
</div>

### + 导出笔记

<div>
  <img src="https://github.com/user-attachments/assets/76a11ffa-53f2-430c-aeee-a48c41b493ed" width="30%" />
  <img src="https://github.com/user-attachments/assets/ebabf3e7-b2a6-4820-8e44-03953821dd9c" width="30%" />
  <img src="https://github.com/user-attachments/assets/dff220ca-f8b3-41b4-b88a-d0b934f6ce19" width="30%" />
</div>

### + 改变字体颜色
<div>
  <img src="https://github.com/user-attachments/assets/a273e7af-dc39-405d-b025-e2b6d851bf6f" width="30%" />
  <img src="https://github.com/user-attachments/assets/602d97ea-6fd4-408f-af40-d0d4b25ff1c8" width="30%" />
  <img src="https://github.com/user-attachments/assets/f7a201e2-e9e8-4bc0-9633-3fd42dc9dea5" width="30%" />
</div>

## 三、具体实现
### 1. 时间戳显示
#### 1.1 noteslist_item.xml 
+ 添加timetamp控件布局
![image](https://github.com/user-attachments/assets/e8d64179-3889-4b7b-a5a2-657f62d4c935)
```
<TextView
    android:id="@android:id/text2"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:layout_gravity="center_vertical"
    android:paddingLeft="5dp"
    android:textSize="15sp"
    android:singleLine="true"
    android:textColor="@color/text_color"/>
```
#### 1.2 NotesList.java
+ 增加PROJECTION
  ![image](https://github.com/user-attachments/assets/4c31f161-f11e-4e4a-b1e2-2acc6f5f14c5)
  ```
private static final String[] PROJECTION = new String[] {
      NotePad.Notes._ID, // 0
      NotePad.Notes.COLUMN_NAME_TITLE, // 1
      NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE // Date
};

+ 显示

protected void onCreate(Bundle savedInstanceState)方法中：
 ```
 // 创建映射列和视图ID
    String[] dataColumns = { NotePad.Notes.COLUMN_NAME_TITLE, NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE };
    int[] viewIDs = { android.R.id.text1, android.R.id.text2 };

    // 创建SimpleCursorAdapter
    SimpleCursorAdapter adapter = new SimpleCursorAdapter(
            this,                                 // 上下文
            R.layout.noteslist_item,              // 列表项布局
            cursor,                               // 游标
            dataColumns,                          // 数据列
            viewIDs                               // 显示视图ID
    );

    // 设置ViewBinder来自定义时间显示
    adapter.setViewBinder(new SimpleCursorAdapter.ViewBinder() {
        @Override
        public boolean setViewValue(View view, Cursor cursor, int columnIndex) {
            if (columnIndex == cursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE)) {
                long timestamp = cursor.getLong(columnIndex);
                String formattedDate = formatDate(timestamp);
                TextView textView = (TextView) view;
                textView.setText(formattedDate);
                return true;
            }
            return false; // 对其他列不做处理
        }
    });

    // 设置ListView的适配器
    setListAdapter(adapter);
}


+ 格式化时间戳为日期字符串
![image](https://github.com/user-attachments/assets/cae1e780-a805-49ec-8cbd-1bf2af6bbdc6)

```
private String formatDate(long timestamp) {
    // 创建一个日期格式化器
    SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm", Locale.getDefault());

    // 获取当前时区的时区对象
    TimeZone timeZone = TimeZone.getDefault();

    // 设置 SimpleDateFormat 使用当前时区
    dateFormat.setTimeZone(timeZone);

    // 将时间戳转换为 Date 对象，并格式化为字符串
    return dateFormat.format(new Date(timestamp));
}

### 2.摘要内容展示
#### 2.1 noteslist_item.xml
线性布局中，外部容器是水平布局
内部嵌套一个标题和内容的垂直布局
![image](https://github.com/user-attachments/assets/ae12668a-912d-40fe-a9ff-c5966255f679)
```
<TextView
    android:id="@android:id/text1"
    android:layout_width="match_parent"
    android:layout_height="?android:attr/listPreferredItemHeight"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:gravity="center_vertical"
    android:paddingLeft="10dip"
    android:textSize="30sp"
    android:singleLine="true"
    android:textColor="@color/text_color"/>
```
#### 2.2 NoteLists.java
+ 增加内容列
![image](https://github.com/user-attachments/assets/96c7d768-978b-4d39-998d-20c730c65c52)
+ 创建映射列和视图ID
![image](https://github.com/user-attachments/assets/a0d535f7-fc30-4417-a899-2d6a899a01db)
#### 2.3 效果展示
![image](https://github.com/user-attachments/assets/e974d0d2-0293-4c99-8600-94425c0f9f82)

### 3.搜索功能
#### 3.1 list_options_menu.xml
+ 增加搜索的控件和图片
  ![image](https://github.com/user-attachments/assets/8e081fcd-3088-4fbc-9ed6-652a8de6e9d1)

```
<item android:id="@+id/menu_search"
    android:icon="@drawable/ic_menu_search"
    android:title="@string/menu_search"
    android:alphabeticShortcut='s'
    android:showAsAction="ifRoom"/>
```
#### 3.2 NotesList.java
+ 显示输入操作
```
public boolean onOptionsItemSelected(MenuItem item) {
    //搜索
case R.id.menu_search:
    // 启动一个搜索界面或弹出输入框
    // 在这里假设你使用一个 EditText 来获取搜索关键词

    // 例如，你可以使用一个对话框来输入查询内容：
    AlertDialog.Builder builder = new AlertDialog.Builder(this);
    builder.setTitle("搜索笔记");

    // 设置输入框
    final EditText input = new EditText(this);
    builder.setView(input);

    builder.setPositiveButton("搜索", new DialogInterface.OnClickListener() {
        public void onClick(DialogInterface dialog, int which) {
            String searchQuery = input.getText().toString().trim();
            if (!searchQuery.isEmpty()) {
                // 创建一个Intent传递搜索关键词
                Intent intent = new Intent(NotesList.this, NotesList.class);
                intent.putExtra("searchQuery", searchQuery); // 将搜索内容传递给NotesList
                startActivity(intent);
            } else {
                Toast.makeText(NotesList.this, "请输入搜索内容", Toast.LENGTH_SHORT).show();
            }
        }
    });
    builder.setNegativeButton("取消", null);

    // 显示对话框
    builder.show();
    return true;
}
```
+ 执行搜索操作
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    // The user does not need to hold down the key to use menu shortcuts.
    setDefaultKeyMode(DEFAULT_KEYS_SHORTCUT);

    // 获取传递过来的搜索条件
    String searchQuery = getIntent().getStringExtra("searchQuery");

    // 获取Intent
    Intent intent = getIntent();

    // 如果没有传递URI，设置默认URI
    if (intent.getData() == null) {
        intent.setData(NotePad.Notes.CONTENT_URI);
    }

    // 设置ListView的上下文菜单监听
    getListView().setOnCreateContextMenuListener(this);

    // 定义查询条件
    String selection = null;
    String[] selectionArgs = null;

    // 如果有搜索条件，设置查询条件
    if (searchQuery != null && !searchQuery.isEmpty()) {
        selection = NotePad.Notes.COLUMN_NAME_TITLE + " LIKE ?"; // 根据标题过滤
        selectionArgs = new String[]{"%" + searchQuery + "%"}; // 搜索关键字
    }

    // 执行查询，传入过滤条件
    Cursor cursor = managedQuery(
            getIntent().getData(),                // 使用默认内容URI
            PROJECTION,                           // 返回的列
            selection,                            // 设置筛选条件
            selectionArgs,                        // 设置筛选条件的参数
            NotePad.Notes.DEFAULT_SORT_ORDER      // 默认排序
    );

    // 创建映射列和视图ID
    String[] dataColumns = { NotePad.Notes.COLUMN_NAME_TITLE, NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE };
    int[] viewIDs = { android.R.id.text1, android.R.id.text2 };

    // 创建SimpleCursorAdapter
    SimpleCursorAdapter adapter = new SimpleCursorAdapter(
            this,                                 // 上下文
            R.layout.noteslist_item,              // 列表项布局
            cursor,                               // 游标
            dataColumns,                          // 数据列
            viewIDs                               // 显示视图ID
    );
    setListAdapter(adapter);
}
#### 3.3 效果展示
![image](https://github.com/user-attachments/assets/b73b88b5-72d6-4af4-88e8-9c8940c9361d)
![image](https://github.com/user-attachments/assets/d52ff520-8593-4052-80b3-07dcd1e05898)
### 4.UI美化
#### 4.1 color.xml
+ 自定义背景资源文件
  ![image](https://github.com/user-attachments/assets/b03cb62e-83eb-4dd4-806b-7da7cb4b8115)
  ```
  <resources>
    <color name="my_custom_color">#FF0000</color> <!-- 红色 -->
    <color name="background_color">#FDFDFD</color> <!-- 白色 -->
    <color name="context_color">#A5FDFDFD</color> <!-- 灰白色 -->
    <color name="item_background_color">#B1FDFDFD</color> <!-- 暗白色 -->
    <color name="text_color">#000000</color> <!-- 黑色 -->
</resources>

#### 4.2 2.noteslist_item.xml
+ 更改Item条目的颜色
  android:background="@color/item_background_color"
+ 设置item字体显示
  ```
<!--title-->
android:textColor="@color/text_color"
<!--content-->
android:textColor="@color/content_color"


<!-- timetamp-->
 android:textColor="@color/text_color"

### 5.导出文件
#### 5.1 editor_options_menu.xml
![image](https://github.com/user-attachments/assets/15c97f11-a401-4892-afca-b4b6483445f2)
```
<item android:id="@+id/menu_export"
    android:icon="@drawable/ic_menu_export"
    android:title="@string/export"/>
```
#### 5.2 NoteEditor.java
![image](https://github.com/user-attachments/assets/3bb22a67-5b33-42a9-afe3-2ea0b399f330)
+ 在本类中增加方法
```
private static final int REQUEST_CODE_EXPORT = 100; // 100 是一个随意选定的
private void export(){
    // 创建一个输入框
    EditText input = new EditText(this);
    input.setHint("请输入文件名");

    // 弹出对话框
    AlertDialog dialog = new AlertDialog.Builder(this)
            .setTitle("导出笔记")
            .setView(input)
            .setPositiveButton("确定", (dialogInterface, which) -> {
                String fileName = input.getText().toString().trim();
                if (fileName.isEmpty()) {
                    Toast.makeText(this, "文件名不能为空", Toast.LENGTH_SHORT).show();
                } else {
                    // 启动文件选择器
                    openFilePicker(fileName);
                }
            })
            .setNegativeButton("取消", null)
            .create();
    dialog.show();
    dialog.getButton(AlertDialog.BUTTON_POSITIVE).setTextColor(Color.BLACK);

}

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    if (requestCode == REQUEST_CODE_EXPORT && resultCode == RESULT_OK) {
        Uri fileUri = data.getData();

        if (fileUri != null) {
            saveNoteToFile(fileUri);
        } else {
            Toast.makeText(this, "文件创建失败", Toast.LENGTH_SHORT).show();
        }
    }
}
private void saveNoteToFile(Uri fileUri) {
    try {
        // 获取笔记内容
        String noteContent = mText.getText().toString();

        // 打开输出流并写入数据
        try (OutputStream outputStream = getContentResolver().openOutputStream(fileUri)) {
            if (outputStream != null) {
                outputStream.write(noteContent.getBytes());
                outputStream.flush();
                Toast.makeText(this, "笔记导出成功", Toast.LENGTH_SHORT).show();
            } else {
                Toast.makeText(this, "无法打开文件", Toast.LENGTH_SHORT).show();
            }
        }
    } catch (Exception e) {
        e.printStackTrace();
        Toast.makeText(this, "导出失败：" + e.getMessage(), Toast.LENGTH_SHORT).show();
    }
}

private void openFilePicker(String fileName) {
    Intent intent = new Intent(Intent.ACTION_CREATE_DOCUMENT);
    intent.addCategory(Intent.CATEGORY_OPENABLE);
    intent.setType("text/plain");
    intent.putExtra(Intent.EXTRA_TITLE, fileName + ".txt"); // 用户输入的文件名
    startActivityForResult(intent, REQUEST_CODE_EXPORT);
}
```
#### 5.3 效果展示
![image](https://github.com/user-attachments/assets/954c513c-5c62-4b8c-9281-ece1355da11f)
![image](https://github.com/user-attachments/assets/24e5b58f-b5e2-4252-9916-bcde9c30a008)

### 6.改变字体颜色
#### 6.1 editor_options_menu.xml
+ 增加控件
![image](https://github.com/user-attachments/assets/9214b5ef-07a2-4951-b241-a34cb639688a)

#### 6.2 NotePadProvider.java
+ 在原有的数据库表中增加颜色字段
  ![image](https://github.com/user-attachments/assets/702d3f1a-82bf-485b-97b0-4ab6f854cddb)
+ 增加字体颜色的映射
  ![image](https://github.com/user-attachments/assets/de692151-60cb-41df-9874-f8c0d8f6a9ae)
public Uri insert(Uri uri, ContentValues initialValues)中
```
增加字体默认颜色
//字体默认
if (!values.containsKey(NotePad.Notes.COLUMN_NAME_FONT_COLOR)) {
    values.put(NotePad.Notes.COLUMN_NAME_FONT_COLOR, "#000000");
}
```
#### 6.3 NoteEditor.java
+ 增加switch选项
  ![image](https://github.com/user-attachments/assets/f12e74f1-7900-4551-b0c2-fc1c55a46e7c)
+ 实现相应的方法
```
  //字体颜色
    private void showFontPickerDialog() {
        // 创建颜色列表
        List<ColorItem> colorItems = Arrays.asList(
                new ColorItem(Color.BLACK, "黑色"),
                new ColorItem(Color.RED, "红色"),
                new ColorItem(Color.GREEN, "绿色"),
                new ColorItem(Color.BLUE, "蓝色"),
                new ColorItem(Color.YELLOW, "黄色"),
                new ColorItem(Color.MAGENTA, "洋红"),
                new ColorItem(Color.CYAN, "青色"),
                new ColorItem(Color.GRAY, "灰色")
        );

        // 创建适配器
        ColorAdapter adapter = new ColorAdapter(this, colorItems);

        // 创建对话框
        new AlertDialog.Builder(this)
                .setTitle("选择字体颜色")
                .setAdapter(adapter, new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialog, int which) {
                        // 获取用户选择的颜色
                        int selectedColor = colorItems.get(which).getColor();
                        // 更新 EditText 的字体颜色
                        mText.setTextColor(selectedColor);
                        // 将选择的颜色同步到数据库
                        updateFontColor(selectedColor);
                    }
                })
                .show();
    }
    private void updateFontColor(int color) {
        // 创建 ContentValues 对象以存储要更新的值
        ContentValues values = new ContentValues();
        values.put(NotePad.Notes.COLUMN_NAME_FONT_COLOR, String.format("#%06X", (0xFFFFFF & color))); // 将颜色转换为字符串

//         更新数据库中对应的记录
        getContentResolver().update(
                mUri,         // 要更新的 URI
                values,       // 要更新的值
                null,         // 不需要选择条件
                null          // 不需要选择参数
        );
    }
    private class ColorAdapter extends ArrayAdapter<ColorItem> {
        private Context context;
        private List<ColorItem> colorItems;

        public ColorAdapter(Context context, List<ColorItem> colorItems) {
            super(context, 0, colorItems);
            this.context = context;
            this.colorItems = colorItems;
        }

        @Override
        public View getView(int position, View convertView, ViewGroup parent) {
            if (convertView == null) {
                convertView = LayoutInflater.from(context).inflate(R.layout.color_item, parent, false);
            }

            ColorItem colorItem = colorItems.get(position);

            View colorPreview = convertView.findViewById(R.id.color_preview);
            TextView colorName = (TextView) convertView.findViewById(R.id.color_name);

            colorPreview.setBackgroundColor(colorItem.getColor());
            colorName.setText(colorItem.getName());

            return convertView;
        }
    }

    private static class ColorItem {
        private int color;
        private String name;

        public ColorItem(int color, String name) {
            this.color = color;
            this.name = name;
        }

        public int getColor() {
            return color;
        }

        public String getName() {
            return name;
        }
    }
```
#### 6.4 展示效果
![image](https://github.com/user-attachments/assets/9f1aad8c-c7f3-4c31-8787-35621a073f91)
![image](https://github.com/user-attachments/assets/9e0d5543-ae72-4341-b3ef-cf6aa3d53da0)
![image](https://github.com/user-attachments/assets/2fb051b2-0e7d-4cfd-bbd1-5f5f0d070800)

