---
layout: post
title: "Android demos"
description: Android demos
modified: 2017-05-20
category: Android
tags: [Android]
---

Github地址：[https://github.com/zhhgit/Android_demos](https://github.com/zhhgit/Android_demos)

# 一、Demos

## Demo1

点击activity1中的按钮通过intent将信息传递到activity2并显示

用到的:
Button,
Intent,
ViewGroup,
TextView

## Demo2

Activity中加载一个WebView

用到的：
WebView,
Toast,
后退

## Demo3

点击按钮去获取网页内容

用到的：
Thread,
Handler,
Message,
HttpURLConnection,
InputStream

## Demo4

点击按钮去获取图片

用到的：
Thread,
Handler,
Message,
HttpURLConnection,
InputStream,
ByteArrayOutputStream,
ImageView,
BitmapFactory,
Bitmap

## Demo5

两个按钮，一个拍照存默认相册并显示，一个拍照存自定义文件并显示。build.gradle中targetSdkVersion 22，否则targetSdkVersion 23时文件IO在6.0上有权限问题。

用到的：
View.OnClickListener,
MediaStore.ACTION_IMAGE_CAPTURE,
ImageView,
Bitmap,
File

## Demo6

右上角按钮。

用到的：
Menu,
Toast

## Demo7

所有Activity继承自BasicActivity，当中打印log。ActivityController管理所有的Activity，可以一次性finish。

用到的：
BasicActivity,
ActivityController

## Demo8

常用组件使用。

用到的：
Toast,
EditText,
Button,
ProgressBar,
AlertDialog,
ProgressDialog

## Demo9

include方式引入自定义布局。自定义控件。

用到的：
LayoutInflater,
ActionBar

## Demo10

简单ListView，自定义ListView。

用到的：
LayoutInflater,
ImageView,
TextView,
ArrayAdapter,
AdapterView.OnItemClickListener

## Demo11

纵向RecyclerView，横向RecyclerView，瀑布RecyclerView，绑定事件。

用到的：
LayoutInflater,
ImageView,
TextView,
RecyclerView.Adapter,
RecyclerView.ViewHolder,
LinearLayoutManager,
StaggeredGridLayoutManager

## Demo12

动态注册、静态注册BroadcastReceiver，发送自定义标准广播。

用到的：
IntentFilter,
registerReceiver,
unregisterReceiver,
ConnectivityManager,
NetworkInfo,
getSystemService

## Demo13

文件存储与读取

用到的：
outputStream,
BufferedWriter,
FileInputStream,
BufferedReader,
openFileInput,
openFileOutput

## Demo14

SharedPreferences存储与读取

用到的：
SharedPreferences,
getSharedPreferences

## Demo15

SQLite存储与读取

用到的：
SQLiteOpenHelper,
SQLiteDatabase,
Cursor,
ContentValues

## Demo16

使用LitePal进行SQLite存储与读取

用到的：
Connector.getDatabase
DataSupport.findAll,
DataSupport.deleteAll,
updateAll,
save

## Demo17

运行时授权拨打电话

用到的：
ContextCompat.checkSelfPermission,
Manifest.permission.CALL_PHONE,
PackageManager.PERMISSION_GRANTED,
ActivityCompat.requestPermissions,
onRequestPermissionsResult,
Intent.ACTION_CALL,
SecurityException

## Demo18

ContentProvider读取通讯录联系人，显示为列表。

用到的：
ContextCompat.checkSelfPermission,
Manifest.permission.READ_CONTACTS,
PackageManager.PERMISSION_GRANTED,
ActivityCompat.requestPermissions,
onRequestPermissionsResult,
ArrayAdapter,
notifyDataSetChanged,
getContentResolver,
ContactsContract.CommonDataKinds.Phone.CONTENT_URI,
ContactsContract.CommonDataKinds.Phone.DISPLAY_NAME,
ContactsContract.CommonDataKinds.Phone.NUMBER,
Cursor

## Demo19

创建并响应提示。

用到的：
NotificationManager,
NotificationCompat.Builder,
getSystemService(NOTIFICATION_SERVICE),
PendingIntent.getActivity

## Demo20

拍照并显示。

用到的：
File,
getExternalCacheDir,
createNewFile,
Uri.fromFile,
FileProvider.getUriForFile,
Build.VERSION.SDK_INT,
android.media.action.IMAGE_CAPTURE,
MediaStore.EXTRA_OUTPUT,
startActivityForResult,
onActivityResult,
getContentResolver,
openInputStream,
BitmapFactory.decodeStream

## Demo21

选择图片并显示。

用到的：
ContextCompat.checkSelfPermission,
Manifest.permission.WRITE_EXTERNAL_STORAGE,
PackageManager.PERMISSION_GRANTED,
ActivityCompat.requestPermissions,
DocumentsContract.isDocumentUri,
DocumentsContract.getDocumentId,
ContentUris.withAppendedId,
getContentResolver,
BitmapFactory.decodeFile

## Demo22

播放音频。

用到的：
MediaPlayer,
Environment.getExternalStorageDirectory

## Demo23

加载网页。

用到的：
getSettings,
setJavaScriptEnabled,
setWebViewClient,
loadUrl

## Demo24

HttpURLConnection发请求。

用到的：
HttpURLConnection,
Thread,
Runnable,
BufferedReader,
StringBuilder,
runOnUiThread

## Demo25

OkHttp发请求。

用到的：
OkHttpClient,
Thread,
Runnable,
Request,
Response,
runOnUiThread

## Demo26

OkHttp发请求，Gson解析JSON数据。

用到的：
OkHttpClient,
Gson,
TypeToken

## Demo27

子线程通过Message通知主线程更新UI。

用到的：
Handler,
Message,
Thread,
Runnable

## Demo28

启动、停止、绑定、解绑服务。

用到的：
startService,
stopService,
bindService,
unbindService,
ServiceConnection

## Demo29

Material Design风格页面。

用到的：
ToolBar,
DrawerLayout,
NavigationView,
FloatingActionButton,
RecyclerView,
CardView,
SwipeRefreshLayout

# 二、参考

1.[Android官网](https://developer.android.google.cn/index.html)

2.[Android中文API](http://www.android-doc.com/)

3.[菜鸟Android教程](http://www.runoob.com/w3cnote/android-tutorial-intro.html)

4.[《第一行代码Android》代码](https://github.com/zhhgit/booksource)