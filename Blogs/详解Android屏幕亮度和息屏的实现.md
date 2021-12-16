## 1 屏幕亮度
对于屏幕的亮度修改有两种方式，一种是修改基于当前Activity的修改，该修改的效果仅仅针对于当前界面有效果，切换至其他界面时，便恢复至系统的亮度值。还有一种是基于对于系统的屏幕亮度的修改，该修改的效果如同我们在安卓的“设置–显示–亮度”中修改的效果一样，是对于安卓系统的整体修改。
### 1.1 获得亮度值
系统的亮度区间为0-255，可通过系统提供的ContentResolver接口的访问系统的ContentProver的数据，在Activity中可通过getContentResolver()方法得到当前的ContentResolver实例，从而实现对系统的ContentProver数据的访问。
下面代码为获得当前系统设置的亮度值，该段代码不需要权限。
```java
	 int screenBrightness = 255;
        try {
            screenBrightness = Settings.System.getInt(MyApplication.mContext.getContentResolver(), Settings.System.SCREEN_BRIGHTNESS);
        } catch (Exception localException) {

        }
```

### 1.2 设置当前屏幕界面亮度
```java
 		Window localWindow = activity.getWindow();
        WindowManager.LayoutParams localLayoutParams = localWindow.getAttributes();
        float f = paramInt / 255.0F;
        localLayoutParams.screenBrightness = f;
        localWindow.setAttributes(localLayoutParams);
```
上述代码依旧不需要额外权限。

### 1.3 设置系统屏幕亮度
系统亮度值的设置需要``WRITE_SETTINGS``权限，在6.0版本上还需要动态申请权限。
6.0后除了常见[9大危险权限](https://www.jianshu.com/p/2fe4fb3e8ce0)外，对于设置悬浮窗(SYSTEM_ALERT_WINDOW)和修改系统设置(WRITE_SETTINGS)这两个权限还需要特殊设置。
对于非系统签名应用是无法直接修改Settings界面中的信息的，必须授权。
首先在AndroidManifest.xml中添加权限声明：``<uses-permission android:name="android.permission.WRITE_SETTINGS"/>``
然后通过startActivityForResult来启动系统设置的授权界面，引导用户动态授权：
```java
/**
     * 申请权限
     */
    private void requestWriteSettings() {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
            //大于等于23 请求权限
            if (!Settings.System.canWrite(getApplicationContext())) {
                Intent intent = new Intent(Settings.ACTION_MANAGE_WRITE_SETTINGS);
                intent.setData(Uri.parse("package:" + getPackageName()));
                startActivityForResult(intent, REQUEST_CODE_WRITE_SETTINGS);
            }
        } else {
            //小于23直接设置
        }
    }
 @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == REQUEST_CODE_WRITE_SETTINGS) {
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
                //Settings.System.canWrite方法检测授权结果
                if (Settings.System.canWrite(getApplicationContext())) {
                    Toast.makeText(MainActivity.this, "获取了权限", Toast.LENGTH_SHORT).show();
                } else {
                    Toast.makeText(MainActivity.this, "您拒绝了权限", Toast.LENGTH_SHORT).show();
                }
            }
        }
    }
```
权限申请成功后，就可以对系统亮度值进行设置了。在设置亮度值之前，需要先设置亮度模式，该模式包括以下两种：
- SCREEN_BRIGHTNESS_MODE_AUTOMATIC=1 为自动调节屏幕亮度
自动亮度调节模式下可以根据光传感器传输的光照信息动态修改系统屏幕亮度。
- SCREEN_BRIGHTNESS_MODE_MANUAL=0 为手动调节屏幕亮度
只有设置为手动调节模式时，才可以动态设置系统的亮度值，因此系统屏幕亮度的设置分为两步：
- 判断是否为手动模式，如不是设置为手动模式
- 设置系统屏幕亮度

```java
	 ContentResolver resolver = MyApplication.mContext.getContentResolver();
        try {
            if (Settings.System.SCREEN_BRIGHTNESS_MODE_MANUAL != (Settings.System.getInt(MyApplication.mContext.getContentResolver(), Settings.System.SCREEN_BRIGHTNESS_MODE))) {
                //设置为手动调节模式
                Settings.System.putInt(resolver, Settings.System.SCREEN_BRIGHTNESS_MODE,
                        Settings.System.SCREEN_BRIGHTNESS_MODE_MANUAL);
            }
        } catch (Settings.SettingNotFoundException e) {
            e.printStackTrace();
        }
        //保存到系统中
        Uri uri = android.provider.Settings.System.getUriFor(Settings.System.SCREEN_BRIGHTNESS);
        android.provider.Settings.System.putInt(resolver, Settings.System.SCREEN_BRIGHTNESS, brightness);
        resolver.notifyChange(uri, null);
```

## 2 亮屏和息屏
对于安卓屏幕亮屏与息屏的操作，有两种方式，一种是通过WakeLock方法，通过锁定和解锁的方式控制屏幕的亮和灭；还有一种是通过设置FLAG_KEEP_SCREEN_ON方式实现。
对于屏幕亮度的熄灭都是针对于当前界面而言的，如切换界面后，屏幕的亮熄就受系统的设置的亮屏时间限制。

### 2.1 WakeLock方式
该方式需要申请WAKE_LOCK权限``<uses-permission android:name="android.permission.WAKE_LOCK"/>``，且在使用时acquire()和release()需要成对出现，避免出现死锁的情况。
```java
	    PowerManager pm = (PowerManager) MyApplication.mContext.getSystemService(Context.POWER_SERVICE);
        wakeLock = pm.newWakeLock(PowerManager.SCREEN_DIM_WAKE_LOCK, "TAG");
        wakeLock.acquire();//保持屏幕常亮
         // wakeLock.acquire(30*1000);//保持屏幕常亮30s

        new Handler().postDelayed(new Runnable() {
            @Override
            public void run() {
                wakeLock.release();//10s后息屏
            }
        },10000);
```
该方式的原理是，wakelock是一种锁的机制，只要有应用获取到该锁，CPU就无法进入休眠状态，一直处于工作状态。在通过newWakeLock(PowerManager.SCREEN_DIM_WAKE_LOCK, “TAG”)方式，第一个参数设置锁的级别及标志位，可通过查看PowerManager获取需要参数值，第二个是设置应用的标志位，通过为类名。
设置成功后，通过 wakeLock.acquire()使其保持常亮状态，或通过设置拥有时间（wakeLock.acquire(30*1000)），在时间结束后便自动释放该锁，即屏幕熄灭。也可通过 wakeLock.release()方式主动释放。

### 2.2 设置FLAG方式
设置FLAG的方式的实现由两种方式，一种是直接在该Activity的xml中设置，还有一种是在代码中动态设置。
- xml配置
该方式比较简单，直接在使用的Activty的xml根目录中添加keepScreenOn即可。
```xml
<android.support.constraint.ConstraintLayout
    .....
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:keepScreenOn="true"
    tools:context=".MainActivity">
```

- 动态设置FLAG
该方式可以根据业务需要动态设置是否常亮或息屏，通过以下方式实现。
```java
//保持常亮
getWindow().addFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);

//息屏
getWindow().clearFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);
```
通过给当期的Window添加FLAG和清除FLAG的方式，实现屏幕熄灭。

### 2.3 设置系统息屏时间
除了以上简单粗暴的方式外，我们还同样可以参考上文中中亮度调节方案，通过getContentResolver()方法获取系统屏幕息屏时间和设置该时间。实现原理基本类似，只需替换key即可。如下所示。
```java
//获取息屏时间
 int result = 0;
        try {
            result = Settings.System.getInt(MyApplication.mContext.getContentResolver(),
                    Settings.System.SCREEN_OFF_TIMEOUT);
        } catch (Settings.SettingNotFoundException e) {
            e.printStackTrace();
        }
      //设置息屏时间
      Settings.System.putInt(MyApplication.mContext.getContentResolver(),
                Settings.System.SCREEN_OFF_TIMEOUT, time);
        Uri uri = Settings.System
                .getUriFor(Settings.System.SCREEN_OFF_TIMEOUT);
        MyApplication.mContext.getContentResolver().notifyChange(uri, null);
```
需要注意的是该方式设置的 不是对于当前界面的息屏时间，而是设置系统的息屏时间，因此如独立使用时仍然需要添加和申请系统权限(WRITE_SETTINGS)。



``原文：https://blog.csdn.net/xk7298/article/details/93300488``


## 补充
补充一个设置系统屏幕亮度的全代码(基本原理同上文，部分地方实现逻辑有细微差别)
```java
package com.example.test;

import android.app.Activity;
import android.app.AlertDialog;
import android.content.ContentResolver;
import android.content.Context;
import android.content.DialogInterface;
import android.content.Intent;
import android.net.Uri;
import android.os.Build;
import android.os.Bundle;
import android.provider.Settings;
import android.view.Window;
import android.view.WindowManager;
import android.widget.Toast;

public class MainActivity extends Activity {

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		//获取屏幕亮度
		getScreenBrightness(this);
		Toast.makeText(this, "系统屏幕亮度值" + getScreenBrightness(this),
				Toast.LENGTH_SHORT).show();
		// 设置APP 屏幕亮度后，系统Setting亮度将对此app 不生效
		setAppScreenBrightness(100);
		allowModifySettings();
		setContentView(R.layout.activity_main);

	}

	/**
	 * 1.获取系统默认屏幕亮度值 屏幕亮度值范围（0-255）
	 * **/
	private int getScreenBrightness(Context context) {
		ContentResolver contentResolver = context.getContentResolver();
		int defVal = 125;
		return Settings.System.getInt(contentResolver,
				Settings.System.SCREEN_BRIGHTNESS, defVal);
	}

	/**
	 * 2.设置 APP界面屏幕亮度值方法
	 * **/
	private void setAppScreenBrightness(int birghtessValue) {
		Window window = getWindow();
		WindowManager.LayoutParams lp = window.getAttributes();
		lp.screenBrightness = birghtessValue / 255.0f;
		window.setAttributes(lp);
	}

	/**
	 * 3.关闭光感，设置手动调节背光模式
	 * 
	 * SCREEN_BRIGHTNESS_MODE_AUTOMATIC 自动调节屏幕亮度模式值为1
	 * 
	 * SCREEN_BRIGHTNESS_MODE_MANUAL 手动调节屏幕亮度模式值为0
	 * **/
	public void setScreenManualMode(Context context) {
		ContentResolver contentResolver = context.getContentResolver();
		try {
			int mode = Settings.System.getInt(contentResolver,
					Settings.System.SCREEN_BRIGHTNESS_MODE);
			if (mode == Settings.System.SCREEN_BRIGHTNESS_MODE_AUTOMATIC) {
				Settings.System.putInt(contentResolver,
						Settings.System.SCREEN_BRIGHTNESS_MODE,
						Settings.System.SCREEN_BRIGHTNESS_MODE_MANUAL);
			}
		} catch (Settings.SettingNotFoundException e) {
			e.printStackTrace();
		}
	}

	/**
	 * 4.非系统签名应用，引导用户手动授权修改Settings 权限
	 * **/
	private static final int REQUEST_CODE_WRITE_SETTINGS = 1000;

	private void allowModifySettings() {
		// Settings.System.canWrite(MainActivity.this)
		// 检测是否拥有写入系统 Settings 的权限
		if (!Settings.System.canWrite(MainActivity.this)) {
			AlertDialog.Builder builder = new AlertDialog.Builder(this,
					android.R.style.Theme_Material_Light_Dialog_Alert);
			builder.setTitle("请开启修改屏幕亮度权限");
			builder.setMessage("请点击允许开启");
			// 拒绝, 无法修改
			builder.setNegativeButton("拒绝",
					new DialogInterface.OnClickListener() {
						@Override
						public void onClick(DialogInterface dialog, int which) {
							Toast.makeText(MainActivity.this,
									"您已拒绝修系统Setting的屏幕亮度权限", Toast.LENGTH_SHORT)
									.show();
						}
					});
			builder.setPositiveButton("去开启",
					new DialogInterface.OnClickListener() {
						@Override
						public void onClick(DialogInterface dialog, int which) {
							// 打开允许修改Setting 权限的界面
							Intent intent = new Intent(
									Settings.ACTION_MANAGE_WRITE_SETTINGS, Uri
											.parse("package:"
													+ getPackageName()));
							startActivityForResult(intent,
									REQUEST_CODE_WRITE_SETTINGS);
						}
					});
			builder.setCancelable(false);
			builder.show();
		}
	}

	@Override
	protected void onActivityResult(int requestCode, int resultCode, Intent data) {
		// TODO Auto-generated method stub
		super.onActivityResult(requestCode, resultCode, data);
		if (requestCode == REQUEST_CODE_WRITE_SETTINGS) {
			if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
				// Settings.System.canWrite方法检测授权结果
				if (Settings.System.canWrite(getApplicationContext())) {
					// 5.调用修改Settings屏幕亮度的方法 屏幕亮度值 200
					ModifySettingsScreenBrightness(MainActivity.this, 200);
					Toast.makeText(this, "系统屏幕亮度值" + getScreenBrightness(this),
							Toast.LENGTH_SHORT).show();
				} else {
					Toast.makeText(MainActivity.this, "您已拒绝修系统Setting的屏幕亮度权限",
							Toast.LENGTH_SHORT).show();
				}
			}
		}
	}

	/**
	 * 5.修改Setting 中屏幕亮度值
	 * 
	 * 修改Setting的值需要动态申请权限 <uses-permission
	 * android:name="android.permission.WRITE_SETTINGS"/>
	 * **/
	private void ModifySettingsScreenBrightness(Context context,
			int birghtessValue) {
		// 首先需要设置为手动调节屏幕亮度模式
		setScreenManualMode(context);

		ContentResolver contentResolver = context.getContentResolver();
		Settings.System.putInt(contentResolver,
				Settings.System.SCREEN_BRIGHTNESS, birghtessValue);
	}
}


```