### Android学习笔记

#### 一、活动(Activity)

#### 1、手动创建和销毁活动

> New→Activity→Empty Activity

+ 创建和加载布局(Android程序的设计讲究逻辑和视图分离，最好每一个活动都能对应一个布局，布局就是用来显示界面内容的)

  > res目录→New→Directory

+ 在AndroidManifest文件中注册

  > <activity android:name=".MainActivity">

+ 在活动中使用Toast（Toast是Android系统提供的一种非常好的提醒方式）

~~~java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    Button btn = (Button) findViewById(R.id.button_1);
    btn.setOnClickListener(new View.OnClickListener(){
    @Override
    public void onClick(View v) {
        Toast.makeText(MainActivity.this, "you click btn1 ",
        Toast.LENGTH_SHORT).show();
        }
    });
}
~~~

+ 在活动中使用Menu（手机右上角那三个点）

  > 首先在res目录下新建一个menu文件夹，右击res目录→New→Directory，输入文件夹名menu，点击OK。接着在这个文件夹下再新建一个名叫main的菜单文件，右击menu文件夹→New→Menuresource file

​    main.xml

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id="@+id/add_item"
        android:title="Add"
            />
    <item
        android:id="@+id/remove_item"
        android:title="Remove"
        />
</menu>
~~~

重写onCreateOptionsMenu()方法，重写方法可以使用Ctrl+ O

~~~java
    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        //inflate()方法接收两个参数，第一个参数用于指定我们通过哪一个资源文件来创建菜单，这里当然传入R.menu.main。第二个	参数用于指定我们的菜单项将添加到哪一个Menu对象当中，这里直接使用onCreateOptionsMenu()方法中传入的menu参数。然后给这个方法返回true，表示允许创建的菜单显示出来，如果返回了false，创建的菜单将无法显示
        getMenuInflater().inflate(R.menu.main, menu);
        return true;
    }
~~~

+ 定义菜单响应事件

  > 重写onOptionsItemSelected()方法

~~~java
    @Override
    public boolean onOptionsItemSelected(@NonNull MenuItem item) {
        switch (item.getItemId()){
            case R.id.add_item:
                Toast.makeText(this,"you click add ",Toast.LENGTH_SHORT).show();
                break;
            case R.id.remove_item:
                Toast.makeText(this,"you click remove",Toast.LENGTH_SHORT).show();
                break;
            default:
        }
        return true;
    }
~~~

+ 销毁一个活动（Back键就可以销毁当前的活动）

代码销毁：Activity类提供了一个finish()方法，修改按钮监听器中的代码，如下所示

~~~java
 btn.setOnClickListener(new View.OnClickListener(){
     @Override
     public void onClick(View v) {
         Toast.makeText(MainActivity.this, "you click btn1 ",
                        Toast.LENGTH_SHORT).show();
         finish(); //销毁当前活动
     }
 });
~~~

#### 2、活动之间的通信

+  使用显式Intent

新建一个空白Activity，编写对应的布局，新加一个按钮

修改活动1的代码

~~~java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    Button btn = (Button) findViewById(R.id.button_1);
    btn.setOnClickListener(new View.OnClickListener(){
        @Override
        public void onClick(View v) {
            /**
                 * 我们首先构建出了一个Intent，传入FirstActivity.this作为上下文，传入Second-Activity.class作为目标活动，
                 * 这样我们的“意图”就非常明显了，即在FirstActivity这个活动的基础上打开SecondActivity这个活动。
                 * 然后通过startActivity()方法来执行这个Intent
                 */
            Intent intent = new Intent(MainActivity.this,SecondActivity.class);
            startActivity(intent);
        }
    });
}
~~~

+ 隐式Intent（它并不明确指出我们想要启动哪一个活动，而是指定了一系列更为抽象的action和category等信息，然后交由系统去分析这个Intent，并帮我们找出合适的活动去启动。）

在SecondActivity 注册标签内添加<intent-filter>标签

~~~xml
<activity android:name=".SecondActivity">
    <intent-filter>
        <action android:name="com.example.new_android.SecondActivity"/>
        <category android:name="android.intent.category.DEFAULT"/>
    </intent-filter>
</activity>
~~~

在主活动中添加点击事件跳转

~~~java
//隐式Intent
Intent intent = new Intent("com.example.new_android.SecondActivity");
startActivity(intent);
~~~

使用隐式Intent，我们不仅可以启动自己程序内的活动，还可以启动其他程序的活动，这使得Android多个应用程序之间的功能共享成为了可能

> 点击按钮打开网页

~~~java
Intent intent = new Intent(Intent.ACTION_VIEW);
intent.setData(Uri.parse("http://www.baidu.com"));
startActivity(intent);
~~~

> 点击按钮拨打电话 tel

~~~java
intent.setData(Uri.parse("tel:10086"));
~~~

+ 向下一个活动传递数据（向下传递数据）

  > Intent中提供了一系列putExtra()方法的重载，可以把我们想要传递的数据暂存在Intent中，启动了另一个活动后，只需要把这些数据再从Intent中取出就可以了

发送数据

~~~java
String data = "Hello SecondActivity";
Intent intent = new Intent(MainActivity.this,SecondActivity.class);
intent.putExtra("data_d",data);
startActivity(intent);
~~~

接收数据

~~~java
//读取别的活动传递的数据
Intent intent = getIntent();
String data = intent.getStringExtra("data_d");
Log.d("SecondActivity",data);
~~~

+ 返回数据给上一个活动（向上传递数据）

  >  Activity中还有一个startActivityForResult()方法也是用于启动活动的，但这个方法期望在活动销毁的时候能够返回一个结果给上一个活动

startActivityForResult()方法接收两个参数，第一个参数还是Intent，第二个参数是请求码，用于在之后的回调中判断数据的来源

~~~java
button1.setOnclickListener(new View.OnclickListener(){
    @Override
    public void onClick(View v){
        Intent intent = new Intent(FirstActivity.this,SecondActivity.class);
        //startActivityForResult来启动SecondActivity，请求码 1 只要是唯一值就可以。
        startActivityForResult(intent,1);
    }
});
~~~

接下来在SecondActivity添加按钮注册点击事件，并在事件中添加返回逻辑，

~~~java
public class SecondActivity extends AppCompatActivity{
    @Override
    protected void onCreate(Bundle saveInstanceState){
        supper.onCreate(saveInstanceState);
        setContentView(R.layout.second_layout);
        Button button2 = (Button) findViewById(R.id.button_2);
        button2.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick(View v){
                //构建了一个Intent，只不过这个Intent仅仅是用于传递数据而已，它没有指定任何的“意图”
                Intent intent = new Intent();
                intent.putExtra("data return","Hello FirstActivity");
                //调用了setResult()方法。这个方法非常重要，是专门用于向上一个活动返回数据的。setResult()方法接收两个参数，第一个参数用于向上一个活动返回处理结果，一般只使用RESULT_OK或RESULT_CANCELED这两个值，第二个参数则把带有数据的Intent传递回去，然后调用了finish()方法来销毁当前活动
                setResult(RESULT_OK,intent);
                finish();
            }
        });
    }
}
~~~

由于我们是使用startActivityForResult()方法来启动SecondActivity的，在SecondActivity被销毁之后会回调上一个活动的onActivityResult()方法，因此我们需要在FirstActivity中重写这个方法来得到返回的数据

~~~java
@Override
protected void onActivityResult(int requestCode,int resultCode,Intent data){
    switch(requestCode){
            case 1:
            	if(resultCode == RESULT_OK){
                    String returnData = data.getStringExtra("data return");
                    Log.d("FirstActivity",returnData);
                }
            	break;
        	default:
    }
}
~~~

如果用户在SecondActivity中并不是通过点击按钮，而是通过按下Back键回到FirstActivity，这样数据不就没法返回了吗？没错，不过这种情况还是很好处理的，我们可以通过在SecondActivity中重写onBackPressed()方法来解决这个问题

~~~java
@Override
public void onBackPressed(){
    //当用户按下Back键，就会去执行onBackPressed()方法中的代码，我们在这里添加返回数据的逻辑就行了
    Intent intent = new Intent();
    intent.putExtra("data return","Hello FirstActivity");
    setResult(RESULT_OK,intent);
    finish();
}
~~~

#### 3、活动的生命周期

+ 运行状态
+ 暂停状态
+ 停止状态
+ 销毁状态

>  Activity类中定义了7个回调方法，覆盖了活动生命周期的每一个环节

+ onCreate()   这个方法你已经看到过很多次了，每个活动中我们都重写了这个方法，它会在活动第一次被创建的时候调用。你应该在这个方法中完成活动的初始化操作，比如说加载布局、绑定事件等
+ onStart()。这个方法在活动由不可见变为可见的时候调用
+ onResume()。这个方法在活动准备好和用户进行交互的时候调用。此时的活动一定位于返回栈的栈顶，并且处于运行状态
+ onPause()。这个方法在系统准备去启动或者恢复另一个活动的时候调用。我们通常会在这个方法中将一些消耗CPU的资源释放掉，以及保存一些关键数据，但这个方法的执行速度一定要快，不然会影响到新的栈顶活动的使用。
+ onStop()。这个方法在活动完全不可见的时候调用。它和onPause()方法的主要区别在于，如果启动的新活动是一个对话框式的活动，那么onPause()方法会得到执行，而onStop()方法并不会执行
+ onDestroy()。这个方法在活动被销毁之前调用，之后活动的状态将变为销毁状态
+ onRestart()。这个方法在活动由停止状态变为运行状态之前调用，也就是活动被重新启动了。

> 以上7个方法中除了onRestart()方法，其他都是两两相对的，从而又可以将活动分为3种生存期

+ **完整生存期**。活动在onCreate()方法和onDestroy()方法之间所经历的，就是完整生存期。一般情况下，一个活动会在onCreate()方法中完成各种初始化操作，而在onDestroy()方法中完成释放内存的操作
+ **可见生存期**。活动在onStart()方法和onStop()方法之间所经历的，就是可见生存期。在可见生存期内，活动对于用户总是可见的，即便有可能无法和用户进行交互。我们可以通过这两个方法，合理地管理那些对用户可见的资源。比如在onStart()方法中对资源进行加载，而在onStop()方法中对资源进行释放，从而保证处于停止状态的活动不会占用过多内存
+ **前台生存期**。活动在onResume()方法和onPause()方法之间所经历的就是前台生存期。在前台生存期内，活动总是处于运行状态的，此时的活动是可以和用户进行交互的，我们平时看到和接触最多的也就是这个状态下的活动

当一个活动进入到了停止状态，是有可能被系统回收的,如果被回收的活动中存在用户输入的数据，怎么恢复？

> Activity中还提供了一个onSaveInstanceState()回调方法，这个方法可以保证在活动被回收之前一定会被调用，因此我们可以通过这个方法来解决活动被回收时临时数据得不到保存的问题。

MainActivity中添加如下代码就可以将临时数据进行保存:

~~~java
@Override
protected void onSaveInstanceState(Bundle outState){
    //onSaveInstanceState()方法会携带一个Bundle类型的参数，Bundle提供了一系列的方法用于保存数据，比如可以使用putString()方法保存字符串，使用putInt()方法保存整型数据，以此类推。每个保存方法需要传入两个参数，第一个参数是键，用于后面从Bundle中取值，第二个参数是真正要保存的内容。
    super.onSaveInstanceState(outState);
    String tempData = "Something you just typed";
    outState.putString("data_key",tempData);
}
~~~

在onCreate()方法中取出Bundle的值

~~~java
@Override
protected void onCreate(Bundle saveInstanceState){
    super.onCreate(savedInstanceState);
    Log.d(TAG,"onCrate");
    setContentView(R.layout.activity_main);
    if(saveInstanceState != null){
        String tempData = saveInstanceState.getString("data_key");
        Log.d(TAG,tempData);
    }
    
}
~~~

#### 4、活动的启动模式

+ standard（默认模式）

  在standard模式（即默认情况）下，每当启动一个新的活动，它就会在返回栈中入栈，并处于栈顶的位置。对于使用standard模式的活动，系统不会在乎这个活动是否已经在返回栈中存在，**每次启动都会创建该活动的一个新的实例**

+ singleTop

  AndroidManifest.xml修改activity标签  android:launchMode="singleTop"，当活动的启动模式指定为singleTop，在启动活动时如果**发现返回栈的栈顶已经是该活动，则认为可以直接使用它，不会再创建新的活动实例**

+ singleTask

  每次启动该活动时系统首先会在返回栈中检查是否存在该活动的实例，如果发现已经存在则直接使用该实例，并把在这个活动之上的所有活动统统出栈，如果没有发现就会创建一个新的活动实例。

+ singleInstance

  singleInstance模式应该算是4种启动模式中最特殊也最复杂的一个了，你也需要多花点功夫来理解这个模式。不同于以上3种启动模式，指定为singleInstance模式的活动会启用一个新的返回栈来管理这个活动（其实如果singleTask模式指定了不同的taskAffinity，也会启动一个新的返回栈）。那么这样做有什么意义呢？想象以下场景，假设我们的程序中有一个活动是允许其他程序调用的，如果我们想实现其他程序和我们的程序可以共享这个活动的实例，应该如何实现呢？使用前面3种启动模式肯定是做不到的，因为每个应用程序都会有自己的返回栈，同一个活动在不同的返回栈中入栈时必然是创建了新的实例。而使用singleInstance模式就可以解决这个问题，在这种模式下会有一个单独的返回栈来管理这个活动，不管是哪个应用程序来访问这个活动，都共用的同一个返回栈，也就解决了共享活动实例的问题

  

使用活动的技巧：

+ 知晓当前是在哪一个活动

  新建一个BaseActivity，不需要让BaseActivity在AndroidManifest.xml中注册，所以选择创建一个普通的Java类就可以了。然后让BaseActivity继承自AppCompatActivity，并重写onCreate()方法，让BaseActivity成为ActivityTest项目中所有活动的父类，这样就可以在BaseActivity做一些通用操作，例如getClass().getSimpleName()获取当前实例的类名

  

+ 随时随地的退出程序

  需要用一个专门的集合类对所有的活动进行管理，新建一个ActivityCollector类作为活动管理器

  ~~~java
  public class ActivityColler{
      //在活动管理器中，我们通过一个List来暂存活动，然后提供了一个addActivity()方法用于向List中添加一个活动，提供了一个removeActivity()方法用于从List中移除活动，最后提供了一个finishAll()方法用于将List中存储的活动全部销毁掉
      public static List<Acticity> activities = new ArrayList<>();
      public static void addActivity(Acticity activity){
          activities.add(activity);
      }
      public static void removeActicity(Acticity activity){
          activities.remove(activity);
      }
      public static void finishAll(){
          for(Acticity activity : activities){
              if(!activity.finish().isFinishing()){
                  activity.finish();
              }
          }
          activities.clear();
      }
  }
  ~~~

  

当然你还可以在销毁所有活动的代码后面再加上杀掉当前进程的代码，以保证程序完全退出，杀掉进程的代码如下所示：

android.os.Process.killProcess(android.os.Process.myPid());

> killProcess()方法用于杀掉一个进程，它接收一个进程id参数，我们可以通过myPid()方法来获得当前程序的进程id。需要注意的是，killProcess()方法只能用于杀掉当前程序的进程，我们不能使用这个方法去杀掉其他程序

+ 启动活动的最佳写法

  SecondActivity中添加了一个actionStart()方法，在这个方法中完成了Intent的构建，另外所有SecondActivity中需要的数据都是通过actionStart()方法的参数传递过来的，然后把它们存储到Intent中，最后调用startActivity()方法启动SecondActivity

  

### 二、安卓UI

#### 1、TextView（文本）

+ android:layout_width：控件的宽度；android:layout_height：高度 

  可选值有3种：match_parent、fill_parent和wrap_content。其中match_parent和fill_parent的意义相同，现在官方更加推荐使用match_parent。match_parent表示让当前控件的大小和父布局的大小一样，也就是由父布局来决定当前控件的大小。wrap_content表示让当前控件的大小能够刚好包含住里面的内容，也就是由控件内容决定当前控件的大小

+ android:gravity：对齐方式

  可选值有top、bottom、left、right、center等，可以用“|”来同时指定多个值

+ android:textSize属性可以指定文字的大小
+ android:textColor属性可以指定文字的颜色，在Android中字体大小使用sp作为单位

#### 2、Button（按钮）

+ 系统会对Button中的所有英文字母自动进行大写转换，可以使用android:textAllCaps="false"来禁用

#### 3、EditText（编辑框）

+ android:maxLines="3" 最大行数，超过不会自动扩展高度
+  android:hint="请输入文字" 输入框里显示一些提示性的文字  

#### 4、ImageView（图片）

+ 图片通常都是放在以“drawable”开头的目录下的

#### 5、ProgressBar（进度条）

+ ProgressBar用于在界面上显示一个进度条

> Android控件的可见属性:所有的安卓控件都有这个属性 android:visibility：可选值有3种：visible、invisible和gone。visible表示控件是可见的，这个值是默认值，不指定android:visibility时，控件都是可见的。invisible表示控件不可见，但是它仍然占据着原来的位置和大小，可以理解成控件变成透明状态了。gone则表示控件不仅不可见，而且不再占用任何屏幕空间。我们还可以通过代码来设置控件的可见性，使用的是setVisibility()方法，可以传入View.VISIBLE、View.INVISIBLE和View.GONE这3种值

通过style属性可以将它指定成水平进度条

>  style="?android:attr/progressBarStyleHorizontal"

#### 6、AlertDialog（警告框）

> AlertDialog可以在当前的界面弹出一个对话框，这个对话框是置顶于所有界面元素之上的，能够屏蔽掉其他控件的交互能力，因此AlertDialog一般都是用于提示一些非常重要的内容或者警告信息

#### 7、ProgressDialog（进度条对话框）

#### 8、ListView（列表）

~~~xml
 <ListView
     android:id="@+id/list_view"
     android:layout_width="match_parent"
     android:layout_height="match_parent"/>
~~~

数组中的数据是无法直接传递给ListView的，我们还需要借助适配器来完成（ArrayAdapter）

~~~java
public class ListActivity1 extends AppCompatActivity {
    private String[] data = {
            "apple","banana","origin","pear","cherry"
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list1);
        //使用了android.R.layout.simple_list_item_1作为ListView子项布局的id，这是一个Android内置的布局文件，里面只有一个TextView，可用于简单地显示一段文本。这样适配器对象就构建好了。
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(
                ListActivity1.this,android.R.layout.simple_list_item_1,data
        );
        ListView listview = (ListView) findViewById(R.id.list_view);
        listview.setAdapter(adapter);
    }
}
~~~

创建一个水果列表：

1、创建水果实体类 fruit.java

~~~java
public class Fruit {
    private String name;
    private int imgId;

    public Fruit(String name,int imgId){
        this.imgId = imgId;
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getImgId() {
        return imgId;
    }

    public void setImgId(int imgId) {
        this.imgId = imgId;
    }
}
~~~

2、创建水果适配器 FruitAdapter.java

~~~java
public class FruitAdapter extends ArrayAdapter<Fruit> {
    private int resourced;
    public FruitAdapter(@NonNull Context context, int resource, List<Fruit> objects) {
        super(context, resource,objects);
        resourced = resource;
    }
//FruitAdapter重写了父类的一组构造函数，用于将上下文、ListView子项布局的id和数据都传递进来。另外又重写了getView()方法，
// 这个方法在每个子项被滚动到屏幕内的时候会被调用。在getView()方法中，首先通过getItem()方法得到当前项的Fruit实例，
// 然后使用LayoutInflater来为这个子项加载我们传入的布局。这里LayoutInflater的inflate()方法接收3个参数，前两个参数我们已经知道是什么意思了，
// 第三个参数指定成false，表示只让我们在父布局中声明的layout属性生效，但不会为这个View添加父布局，因为一旦View有了父布局之后，它就不能再添加到ListView中了
    @Override
    public View getView(int position , View contextView, ViewGroup parent){
        Fruit f = getItem(position); //获取当前的Fruit实例
        View view = LayoutInflater.from(getContext()).inflate(resourced,parent,false);
        ImageView fruitImg = (ImageView) view.findViewById(R.id.f_image);
        TextView fruitText = (TextView) view.findViewById(R.id.f_text);
        fruitImg.setImageResource(f.getImgId());
        fruitText.setText(f.getName());
        return view;
    }
}
~~~

3、创建自定义水果list布局

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ImageView
        android:id="@+id/f_image"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"/>
    <TextView
        android:id="@+id/f_text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_vertical"
        android:layout_marginLeft="10dp"
        />
</LinearLayout>
~~~

4、主程序引入自定义的水果适配器

~~~java
public class ListActivity1 extends AppCompatActivity {
    private String[] data = {
            "apple","banana","origin","pear","cherry","apple","banana","origin","pear","cherry"
            ,"apple","banana","origin","pear","cherry"
    };
    private List<Fruit> fruitList = new ArrayList<>();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list1);
        initFruit();
        FruitAdapter adapter = new FruitAdapter(ListActivity1.this,R.layout.f_item,fruitList);
//        ArrayAdapter<String> adapter = new ArrayAdapter<String>(
//                ListActivity1.this,android.R.layout.simple_list_item_1,data
//        );
        ListView listview = (ListView) findViewById(R.id.list_view);
        listview.setAdapter(adapter);
    }

   private void  initFruit(){
        for(int i = 0;i<10;i++){
            Fruit apple = new Fruit("apple",R.drawable.apple);
            fruitList.add(apple);
        }
    }
}
~~~

结果图示：

<img src="Server/android/image/result_1.png" alt="结果示意图" style="zoom: 50%;" />



#### 9、RecyclerView（滚动控件）

> RecyclerView也属于新增的控件，为了让RecyclerView在所有Android版本上都能使用，Android团队采取了同样的方式，将RecyclerView定义在了support库当中。因此，想要使用RecyclerView这个控件，首先需要在项目的build.gradle中添加相应的依赖库才行

~~~xml
implementation 'com.android.support:recyclerview-v7:28.0.0'
~~~



###  三、4种基本布局

#### 1、线性布局 LinearLayout

> android:orientation:指定排列方向  vertical（垂直排列）| horizontal（水平排列，属于默认方式）

**这里需要注意，如果LinearLayout的排列方向是horizontal，内部的控件就绝对不能将宽度指定为match_parent，因为这样的话，单独一个控件就会将整个水平方向占满，其他的控件就没有可放置的位置了。同样的道理，如果LinearLayout的排列方向是vertical，内部的控件就不能将高度指定为match_parent**

> android:layout_weight。这个属性允许我们使用比例的方式来指定控件的大小，它在手机屏幕的适配性方面可以起到非常重要的作用

为什么将android:layout_weight属性的值同时指定为1就会平分屏幕宽度呢？其实原理也很简单，系统会先把LinearLayout下所有控件指定的layout_weight值相加，得到一个总值，然后每个控件所占大小的比例就是用该控件的layout_weight值除以刚才算出的总值。因此如果想让EditText占据屏幕宽度的3/5, Button占据屏幕宽度的2/5，只需要将EditText的layout_weight改成3, Button的layout_weight改成2就可以了

#### 2、相对布局 RelativeLayout

>  android:layout_alignParentLeft、android:layout_alignParentTop、android:layout_alignParentRight、android:layout_alignParentBottom、android:layout_centerInParent

控件相对于控件进行定位

> android:layout_below表示让一个控件位于另一个控件的下方，
>
> android:layout_toLeftOf表示让一个控件位于另一个控件的左侧，
>
> android:layout_toRightOf表示让一个控件位于另一个控件的右侧

android:layout_above属性可以让一个控件位于另一个控件的上方，需要为这个属性指定相对控件id的引用，这里我们填入了@id/button3，表示让该控件位于Button 3的上方

#### 4、帧布局 FrameLayout

**这种布局没有方便的定位方式，所有的控件都会默认摆放在布局的左上角**

> layout_gravity属性来指定控件在布局中的对齐方式

#### 5、百分比布局

Android团队将百分比布局定义在了support库当中，我们只需要在项目的build.gradle中添加百分比布局库的依赖，就能保证百分比布局在Android所有系统版本上的兼容性了。

引入：打开app/build.gradle文件，在dependencies闭包中添加如下内容

~~~js
implementation 'com.android.support:percent:24.2.1'
~~~

### 四、创建自定义控件

#### 1、创建自定义标题栏

+ 新建标题栏Activity

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    tools:context=".UIActivity1"
    android:background="@drawable/a">

    <Button
        android:id="@+id/title_back"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Back"
        android:background="@drawable/back"
        android:textColor="#fff"
        android:layout_gravity="center"
        android:layout_margin="5dp"
        />

    <TextView
        android:id="@+id/title_text"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="title_text"
        android:layout_gravity="center"
        android:layout_weight="1"
        android:textColor="#fff"
        android:textSize="24sp"
        >
    </TextView>
    <Button
        android:id="@+id/title_edit"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Edit"
        android:background="@drawable/back"
        android:textColor="#fff"
        android:layout_gravity="center"
        android:layout_margin="5dp"
        />
</LinearLayout>
~~~

+ 主任务引入自定义标题栏

  ~~~xml
   <include layout="@layout/activity_u_i1"></include>
  ~~~

+ 隐藏系统自带标题栏

  ~~~java
  //隐藏系统自带的   标题栏
  ActionBar actionBar =  getSupportActionBar();
  if(actionBar != null){
      actionBar.hide();
  }
  ~~~

  

#### 2、制作Nine-Patch图片

>  在Android sdk目录下有一个tools文件夹，在这个文件夹中找到draw9patch.bat文件，我们就是使用它来制作Nine-Patch图片的。不过，要打开这个文件，必须先将JDK的bin目录配置到环境变量当中才行，比如你使用的是Android Studio内置的jdk，那么要配置的路径就是<Android Studio安装目录>/jre/bin

**目前新版的sdk已经没有这个文件了，集成到了as编辑器里面，右键png图片就可以看到** 

### 五、广播

#### 1、简介

> Android提供了一套完整的API，允许应用程序自由地发送和接收广播，
>
> Android内置了很多系统级别的广播，我们可以在应用程序中通过监听这些广播来得到各种系统的状态信息。比如手机开机完成后会发出一条广播，电池的电量发生变化会发出一条广播，时间或时区发生改变也会发出一条广播，等等

+ 标准广播

  > 标准广播（Normal broadcasts）是一种完全异步执行的广播，在广播发出之后，所有的广播接收器几乎都会在同一时刻接收到这条广播消息，因此它们之间没有任何先后顺序可言。这种广播的效率会比较高，但同时也意味着它是无法被截断的

+ 有序广播

  > 有序广播（Ordered broadcasts）则是一种同步执行的广播，在广播发出之后，同一时刻只会有一个广播接收器能够收到这条广播消息，当这个广播接收器中的逻辑执行完毕后，广播才会继续传递。所以此时的广播接收器是有先后顺序的，优先级高的广播接收器就可以先收到广播消息，并且前面的广播接收器还可以截断正在传递的广播，这样后面的广播接收器就无法收到广播消息了

#### 2、接收系统广播

> 广播接收器可以自由地对自己感兴趣的广播进行注册，这样当有相应的广播发出时，广播接收器就能够收到该广播，并在内部处理相应的逻辑

> 注册广播的方式一般有两种，在代码中注册和在AndroidManifest.xml中注册，其中前者也被称为动态注册，后者也被称为静态注册

+ 注册广播

  > 那么该如何创建一个广播接收器呢？其实只需要新建一个类，让它继承自Broadcast-Receiver，并重写父类的onReceive()方法就行了。这样当有广播到来时，onReceive()方法就会得到执行，具体的逻辑就可以在这个方法中处理

  ~~~java
  /**
   * 广播测试
   */
  public class BroadcastActiviy extends AppCompatActivity {
      private IntentFilter intentFilter;
      private NetworkChangeReceiver networkChangeReceiver;
  
      @Override
      protected void onCreate(Bundle savedInstanceState) {
          super.onCreate(savedInstanceState);
          setContentView(R.layout.activity_broadcast_activiy);
          intentFilter = new IntentFilter();
          intentFilter.addAction("android.net.conn.CONNECTIVITY_CHANGE");
          networkChangeReceiver = new NetworkChangeReceiver();
          registerReceiver(networkChangeReceiver,intentFilter);
      }
  
      @Override
      protected void onDestroy(){
          super.onDestroy();
          unregisterReceiver(networkChangeReceiver);
      }
      class NetworkChangeReceiver extends BroadcastReceiver {
          @Override
          public void onReceive(Context context, Intent intent){
              Toast.makeText(BroadcastActiviy.this,"network change",Toast.LENGTH_SHORT).show();
          }
      }
  }
  ~~~

  > 动态注册的广播接收器可以自由地控制注册与注销，在灵活性方面有很大的优势，但是它也存在着一个缺点，即必须要在程序启动之后才能接收到广播，因为注册的逻辑是写在onCreate()方法中的

+ 静态注册（Android8.0在AndroidManifest.xml文件中静态注册广播接收失效是由于**官方对耗电量的优化，避免APP滥用广播的一种处理方式**。除了少部分的广播仍支持静态注册（如开机广播），其余的都会出现**失效**的情况）

  > 可以让程序在未启动的情况下就能接收到广播

  ~~~java
  public class BootCompleteReceiver extends BroadcastReceiver {
  
      @Override
      public void onReceive(Context context, Intent intent) {
          Toast.makeText(context,"Boot Complete",Toast.LENGTH_SHORT).show();
      }
  }
  
  
  <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>
      
  <receiver
      android:name=".BootCompleteReceiver"
      android:enabled="true"
      android:exported="true">
      <intent-filter>
           <action android:name="android.intent.action.BOOT_COMPLETED"/>
      </intent-filter>
  </receiver>
  ~~~

  

#### 3、发送广播

+ 标准广播

  ~~~
  //广播接收器
  public class MyReceiver extends BroadcastReceiver {
      @Override
      public void onReceive(Context context, Intent intent) {
          Toast.makeText(context,"接收到自定义广播",Toast.LENGTH_SHORT).show();
      }
  }
  
  @Override
  protected void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      setContentView(R.layout.activity_broadcast_activiy);
      intentFilter=new IntentFilter();
      //这里定义接受器监听广播的类型，这里添加相应的广播
      intentFilter.addAction("com.example.fileedit.MY_BROADCAST");
      //实例化接收器
      MyBroadcastReceiver m = new MyBroadcastReceiver();
      //注册事件，将监听类型赋给对应的广播接收器----所以这叫动态注册
      registerReceiver(m,intentFilter);
      Button btn = (Button) findViewById(R.id.btn_brod);
      btn.setOnClickListener(new View.OnClickListener() {
          @Override
          public void onClick(View v) {
              Intent intent = new Intent("com.example.fileedit.MY_BROADCAST");
              sendBroadcast(intent);
              }
      });
  }
  ~~~

+ 有序广播

  > 1、将sendBroadcast()方法改成send-OrderedBroadcast()方法
  >
  > 2、注册的时候android:priority属性给广播接收器设置了优先级,优先级比较高的广播接收器就可以先收到广播
  >
  > 3、在onReceive()方法中调用了abortBroadcast()方法，就表示将这条广播截断

#### 4、本地广播（LocalBroadcastManager）

> 首先是通过LocalBroadcastManager的getInstance()方法得到了它的一个实例，然后在注册广播接收器的时候调用的是LocalBroadcastManager的registerReceiver()方法，在发送广播的时候调用的是LocalBroadcastManager的sendBroadcast()方法，仅此而已。这里我们在按钮的点击事件里面发出了一条com.example.broadcasttest.LOCAL_BROADCAST广播，然后在LocalReceiver里去接收这条广播

### 六、数据存储全方案

#### 1、文件存储

> 文件存储是Android中最基本的一种数据存储方式，它不对存储的内容进行任何的格式化处理，所有数据都是原封不动地保存到文件当中的，因而它比较适合用于存储一些简单的文本数据或二进制数据

+ Context类中提供了一个openFileOutput()方法，可以用于将数据存储到指定的文件中。这个方法接收两个参数，
+ 第一个参数是文件名，在文件创建的时候使用的就是这个名称，注意这里指定的文件名不可以包含路径，因为所有的文件都是默认存储到/data/data/<package name>/files/目录下的
+ 第二个参数是文件的操作模式，主要有两种模式可选，MODE_PRIVATE和MODE_APPEND。其中MODE_PRIVATE是默认的操作模式，表示当指定同样文件名的时候，所写入的内容将会覆盖原文件中的内容，而MODE_APPEND则表示如果该文件已存在，就往文件里面追加内容，不存在就创建新文件。其实文件的操作模式本来还有另外两种：MODE_WORLD_READABLE和MODE_WORLD_WRITEABLE，这两种模式表示允许其他的应用程序对我们程序中的文件进行读写操作，不过由于这两种模式过于危险，很容易引起应用的安全性漏洞，已在Android 4.2版本中被废弃
+ Context类中还提供了一个openFileInput()方法，用于从文件中读取数据。这个方法要比openFileOutput()简单一些，它只接收一个参数，即要读取的文件名，然后系统会自动到/data/data/<package name>/files/目录下去加载这个文件，并返回一个FileInputStream对象，得到了这个对象之后再通过Java流的方式就可以将数据读取出来了

#### 2 、SharedPreferences存储

>如何获取到SharedPreferences对象
>
>1、Context类中的getSharedPreferences()方法
>
>2、Activity类中的getPreferences()方法
>
>3、PreferenceManager类中的getDefaultSharedPreferences()方法

~~~java
public class SPActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_s_p);
        Button btn = (Button) findViewById(R.id.btn_sp);
        btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
            	//保存数据
                SharedPreferences.Editor editor = getSharedPreferences("data",MODE_PRIVATE).edit();
                editor.putString("name","张三");
                editor.putInt("age",12);
                editor.putBoolean("isStudy",true);
                editor.apply();

                //读取数据
                SharedPreferences spf = getSharedPreferences("data",MODE_PRIVATE);
                String name = spf.getString("name","");
                Toast.makeText(SPActivity.this,"name="+name,Toast.LENGTH_SHORT).show();
            }
        });
    }
}
~~~

#### 3、SQLite数据库存储

> 使用开源项目 LitePal 操作数据库
>
> 1、引入 implementation 'org.litepal.android:core:1.4.1'
>
> 2、编写实体类 继承DataSupport，public class Book extends DataSupport 
>
> 3、编写litepal.xml配置文件  main-->assets-->litepal.xml
>
> 4、进行初始化库、增删改查等操作

~~~java
<?xml version="1.0" encoding="utf-8" ?>
<litepal>
    <dbname value ="BookStore"></dbname>
    <version value ="1"></version>
    <list>
        <mapping class="com.example.fileedit.vo.Book"></mapping>
    </list>
</litepal>
           
@Override
 public void onClick(View v) {
   //初始化 数据库
   LitePal.getDatabase();
   //保存数据
   book.save();
   //更新数据
   book.updateAll(...);
   //删除数据
   book.delete();
   DataSupport.deleteAll(....);//其中deleteAll()方法的第一个参数用于指定删除哪张表中的数据，Book.class就意味着删除Book表中的数据，后面的参数用于指定约束条件
   //查询数据
   DataSupport.findAll(Book.class);
 }
~~~

### 七、内容提供器（Content Provider）

> 内容提供器（Content Provider）主要用于在不同的应用程序之间实现数据共享的功能，它提供了一套完整的机制，允许一个程序访问另一个程序中的数据，同时还能保证被访数据的安全性。目前，使用内容提供器是Android实现跨程序共享数据的标准方式
>
> 如果一个应用程序通过内容提供器对其数据提供了外部访问接口，那么任何其他的应用程序就都可以对这部分数据进行访问。Android系统中自带的电话簿、短信、媒体库等程序都提供了类似的访问接口，这就使得第三方应用程序可以充分地利用这部分数据来实现更好的功能

#### 1、运行时权限	

> Android现在将所有的权限归成了两类，一类是普通权限，一类是危险权限
>
> 1、普通权限只需要在AndroidManifest.xml文件中添加一下权限声明
>
> 2、危险权限需要进行运行时权限处理

#### 2、如何在程序运行时申请权限

1. 先判断用户是否已经给我们授权了

   ~~~java
   //checkSelfPermission()方法接收两个参数，第一个参数是Context，这个没什么好说的，第二个参数是具体的权限名，比如打电话的权限名就是Manifest.permission.CALL_PHONE，然后我们使用方法的返回值和PackageManager.PERMISSION_GRANTED做比较，相等就说明用户已经授权，不等就表示用户没有授权
   if(ContextCompat.checkSelfPermission(xxx,xxx) != PackageManager.PERMISSION_GRANTED){
       //无授权
   }else{
       //已授权
   }
   ~~~

2. 申请授权

   ~~~java
   //第一个参数要求是Activity的实例，第二个参数是一个String数组，我们把要申请的权限名放在数组中即可，第三个参数是请求码，只要是唯一值就可以了
   ActivityCompat.requestPermissions(xxx,xxx,xxx);
   //调用完了requestPermissions()方法之后，系统会弹出一个权限申请的对话框，然后用户可以选择同意或拒绝我们的权限申请，不论是哪种结果，最终都会回调到onRequest-PermissionsResult()方法中，而授权的结果则会封装在grantResults参数当中
   ~~~

3. 根据用户是否授权做下一步操作

   ~~~java
   @Override
   public void onRequestPermissionsResult(int requestCode,String[] premissions,int[] grantResults){
       switch(requestCode){
           case 1 : //这个就是上面的唯一请求码
               if(grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED){
                   //说明用户已经授权，进行下一步操作
                   ....
               }else{
                   //说明用户拒绝了授权，可以进行提示
                   Toast.makeText(xxx,"需要授权才能进行下一步操作",xxx).show();
   			}
               break;
           default:
       }
   }
   ~~~

#### 3、ContentResolver的基本用法

+ 使用ContentResolver做增删改查

  ~~~java
  //通过Context中的getContentResolver()方法获取到该类的实例
  Cursor cursor = getContentResolver().query(
  	uri,
      projection, //查询指定的列名 select age,name
      selection,  //where的约束 where name = "张三"
      selectionArgs, //为where中的占位符提供具体的值
      sortOrder //结果的排序方式 order by name,age
  );
  //内容URI给内容提供器中的数据建立了唯一标识符，它主要由两部分组成：authority和path。authority是用于对不同的应用程序做区分的，一般为了避免冲突，都会采用程序包名的方式来进行命名。比如某个程序的包名是com.example. app，那么该程序对应的authority就可以命名为com.example.app. provider。path则是用于对同一应用程序中不同的表做区分的，通常都会添加到authority的后面。比如某个程序的数据库里存在两张表：table1和table2，这时就可以将path分别命名为/table1和/table2，然后把authority和path进行组合，内容URI就变成了com.example.app.provider/table1和com.example.app.provider/table2。不过，目前还很难辨认出这两个字符串就是两个内容URI，我们还需要在字符串的头部加上协议声明
  Uri uri = Uri.parse("content://com.example.app. provider/table1");
  
  //遍历结果
  if(cursor != null){
      while(cursor.moveToNext()){
          String col1 = 	cursor.getString(cursor.getColumnIndex("name"));
          int col2 = cursor.getInt(cursor.getColumnIndex("age"));
      }
      cursor.close();
  }
  ~~~

  

































### 第三方、引入百度API

#### 1、注册成为百度开发者

> http://developer.baidu.com/user/reg/

#### 2、

### 人脸识别：

#### 虹软 人脸识别