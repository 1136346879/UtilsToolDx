# UtilsToolDx

工具类


## 在gradle中添加相应的依赖[^code]

     repositories {
            maven { url "https://raw.githubusercontent.com/1136346879/UtilsToolDx/master" }
        }
    dependencies {

     implementation 'com.dx:xfs:1.0'
    }

> * 点击左上角的
    Sync Now
    同步一下库文件


  在manifest中注册相应的  [^code]
  
      <activity android:name="com.xfs.fsyuncai.mavendemo.CropImageActivity"/>
        <activity android:name="com.xfs.fsyuncai.mavendemo.MainActivity"/>
        
  调用下方代码 进入aar中的类
    这里的packetname 一定记住填写 调用方的packetname 因为 aar最终会被合并到调用方的manifest文件  [^code]
    
            intent.component = ComponentName("com.hazz.kotlinmvp", //packagename
                    "com.xfs.fsyuncai.mavendemo.MainActivity")//classname//
            startActivity(intent)
        
   ##     即集成完毕
