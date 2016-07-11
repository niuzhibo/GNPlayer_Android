 
# 极牛Android播放SDK
***
# 概述
极牛播放SDK为Android平台开发使用的SDK，为Android开发者提供简单、快捷的接口和开源调用示例，帮助开发者实现Android平台上的多媒体播放应用。不仅极大降低开发门槛，同时支持客户快速在多个平台发布产品。

***
#功能

- 本地全媒体格式支持, 并对主流的媒体格式(mp4, avi, wmv, flv, mkv,   mov, rmvb 等 );

- 支持广泛的流式视频格式, HLS, RTMP, HTTP Rseudo-Streaming 等

- 低延时直播体验，配合极牛推流sdk，可以达到全程直播稳定的4秒内延时；

- 实现快速满屏播放，为用户带来更快捷优质的播放体验；

- 业内一流的H.265解码

- 小于4M大小的超轻量级直播sdk；

#配置方法
---
- [工程环境](#1)
  * [运行环境](#1.1)
  * [lib引用](#1.2)
  * [所需权限](#1.3)
- [SDK使用示例](#2)
   * [xml设置](#2.0)
   * [初始化](#2.1)
   * [设置事件监听](#2.2)
   * [视频渲染相关](#2.3)
   * [主要接口](#2.4)
   * [接口用法](#2.5)
   
   
### 运行环境<h3 id = "1.1">
- 极牛SDK可运行于手机移动端、平板电脑、电视以及其他设备;
- 支持 Android 2.1 及以上版本; 
- 支持 armv5/armv7a/arm64/x86以及虚拟机运行。



### lib引用<h3 id = "1.2">
- libs/armeabi-v7a/libksyplayer.so
- libs/libgnplayer1.0.0.jar

在AndroidStudio中的`app/build.gradle`添加以下代码;

     sourceSets {
        main {
            jniLibs.srcDirs = ['libs']
        }
        }

        compile files('libs/libgnplayer1.0.0.jar')


### 所需权限<h3 id = "1.3">
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
 
 
### xml主要设置<h3 id = "2.0">
     <SurfaceView
        android:id="@+id/player_surface"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_centerInParent="true"/>
        
### 初始化<h3 id = "2.1">
    GnMediaPlayer gnMediaPlayer = GnMediaPlayer.getInstance();
    gnMediaPlayer.init(this,String room_id);
    
        
### 设置事件监听,建议用户全部都设置，需要强调的是跟`gnMediaPlayer.setOnPreparedListener`为必须设置的回调<h3 id = "2.2">
    // 播放器在准备完成，可以开播时会发出onPrepared回调
    private IMediaPlayer.OnPreparedListener mOnPreparedListener；
    // 播放完成时会发出onCompletion回调
    private IMediaPlayer.OnCompletionListener mOnCompletionListener;
    // 播放器遇到错误时会发出onError回调
    private IMediaPlayer.OnErrorListener mOnErrorListener;
    private IMediaPlayer.OnInfoListener mOnInfoListener;
    private IMediaPlayer.OnVideoSizeChangedListener mOnVideoSizeChangeListener;
    private IMediaPlayer.OnSeekCompleteListener mOnSeekCompletedListener;
    // SurfaceView需在Layout中定义，此处不在赘述
    private SurfaceView mVideoSurfaceView;
    private SurfaceHolder mSurfaceHolder
  
  																										 mVideoSurfaceView = (SurfaceView) findViewById(R.id.player_surface);
   				gnMediaPlayer.setOnBufferingUpdateListener(mContext,mOnBufferingUpdateListener);    
	gnMediaPlayer.setOnCompletionListener(mOnCompletionListener);
	gnMediaPlayer.setOnPreparedListener(mOnPreparedListener);
	gnMediaPlayer.setOnInfoListener(mOnInfoListener);				gnMediaPlayer.setOnVideoSizeChangedListener(mOnVideoSizeChangeListener);											
	gnMediaPlayer.setOnErrorListener(mOnErrorListener);      	gnMediaPlayer.setOnSeekCompleteListener(mOnSeekCompletedListener);
	
	
	
	
	
	
### 设置监听列子`gnMediaPlayer.setOnCompletionListener`跟 `gnMediaPlayer.setOnPreparedListener()`;为例
	private IMediaPlayer.OnCompletionListener mOnCompletionListener = new IMediaPlayer.OnCompletionListener() {
    @Override
    public void onCompletion(IMediaPlayer mp) {
        // 播放完成，用户可选择释放播放器
        if(gnMediaPlayer != null) {
            gnMediaPlayer.stop();
            gnMediaPlayer.release();
        }
    }
    };
  
  
 -
       
       
        private IMediaPlayer.OnPreparedListener mOnPreparedListener = new     IMediaPlayer.OnPreparedListener() {
        @Override
        public void onPrepared(IMediaPlayer mp) {
        if(gnMediaPlayer != null) {
            // 设置视频伸缩模式，此模式为裁剪模式
            gnMediaPlayer.setVideoScalingMode(GnConstants.VIDEO_SCALING_MODE_SCALE_TO_FIT_WITH_CROPPING);
            // 开始播放视频
           gnMediaPlayer.start();
        }
    }
}    
    
    
### 视频渲染相关<h3 id = "2.3">
`gnMediaPlayer`提供了`setDisplay`接口设置视频渲染所需的Surface,`此回调必须加`


     
     protected void onCreate(Bundle savedInstanceState) {
     		..........
     		//此处省略代码
         mSurfaceHolder = mVideoSurfaceView.getHolder();
     	 mSurfaceHolder.addCallback(mSurfaceCallback);

    }
          
      private final Callback mSurfaceCallback = new Callback() {
        @Override
        public void surfaceChanged(SurfaceHolder holder, int format, int width, int height) {
            if (gnMediaPlayer != null && gnMediaPlayer.isPlaying())
                gnMediaPlayer.setVideoScalingMode(GnConstants.VIDEO_SCALING_MODE_SCALE_TO_FIT_WITH_CROPPING);
        }

        @Override
        public void surfaceCreated(SurfaceHolder holder) {
            if (gnMediaPlayer != null)
                gnMediaPlayer.setDisplay(holder);
        }

        @Override
        public void surfaceDestroyed(SurfaceHolder holder) {
            Log.d(TAG, "surfaceDestroyed");
            if (gnMediaPlayer != null) {
                gnMediaPlayer.setDisplay(null);
            }
        }
    };


    

### 主要接口<h3 id = "2.4">

 - init（Context context,String room_id）
   
   初始化播放器并传入room_id
   
 - start（）
 	
 	方法用于控制播放 
 	
 - pause（）
 
   方法用于暂停播放
   
 - release() 
 
   方法用于释放播放器，等播放完成后调用 
   
### 接口用法<h3 id = "2.5">
	
当设置完成以上主要方法，在以下面的 `Activity`生命周期分别调用:

 - onCreate
 
       gnMediaPlayer.init(Context context,String room_id);
 
 - onPause()
 
  		if (gnMediaPlayer != null) {
            gnMediaPlayer.pause();
            mPause = true;
        }
        
 - onResume
 
        if (gnMediaPlayer != null) {
            gnMediaPlayer.start();
            mPause = false;
        }
        }  
 
 
 - 当播放完成回调的监听中调用：
         
        if (gnMediaPlayer != null) {
            gnMediaPlayer.release();
            gnMediaPlayer = null;
        }
     
     


 
  