
前言：最近做项目用到了讯飞语音，遂搞了一个简单的教程，供大家使用。
 讯飞语音  使用步骤：语音转文字：
 
1，首先去讯飞开放平台（ http://www.xfyun.cn/）注册，账号：

 
2，注册后登录：

3，点击选择我的语音云：
 
 
4，点击左侧边栏，创建新的应用：
 
  
5，创建好应用后：如图：复制appid:

 
6，下载sdk:  点击边栏左侧sdk下载中心：
选择我们所需要的功能和平台：




7，点击下载sdk,保存文件，并打开。

//-----------------------------------------------------------
8，使用androidStudio创建demo：
布局文件内容：
<Button
    android:id="@+id/btn_click"
    android:text="点击打开讯飞语音识别"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    />

<EditText
    android:id="@+id/result"
    android:layout_below="@id/btn_click"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="此处显示你所录下的内容"
    />

效果：
 
 
9，将解压文件中libs中  下 的所有jar包，考到项目的  libs文件夹下，刷新gradle（eclipse的同学们可以直接全全部考如libs下，并添加类库）：

 
9.1在main文件夹下新建文件夹jniLibs。拷贝所有sdk下libs中所有除了.jar的文件夹，里面为.so文件。
9,2,拷贝assets文件夹到main下面：
效果如下：


 
 
 
//注册文件中添加权限：
<uses-permission android:name="android.permission.RECORD_AUDIO" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.READ_CONTACTS" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.CAMERA"/>


在：mainActivity中添加如下代码：
注意需要将自己的 appId拷贝到代码中：
 
package zhaoq_qiang.xunfeidemo;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import com.iflytek.cloud.RecognizerResult;
import com.iflytek.cloud.SpeechConstant;
import com.iflytek.cloud.SpeechError;
import com.iflytek.cloud.SpeechUtility;
import com.iflytek.cloud.ui.RecognizerDialog;
import com.iflytek.cloud.ui.RecognizerDialogListener;

import org.json.JSONArray;
import org.json.JSONObject;
import org.json.JSONTokener;

public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    private Button btn_click;

    private EditText mResultText;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        btn_click = (Button) findViewById(R.id.btn_click);
        mResultText = ((EditText) findViewById(R.id.result));

        SpeechUtility.createUtility(this, SpeechConstant.APPID + "= 此处需要填写你所申请的appId");

        btn_click.setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        btnVoice();
    }

    //TODO 开始说话：
    private void btnVoice() {
        RecognizerDialog dialog = new RecognizerDialog(this,null);
        dialog.setParameter(SpeechConstant.LANGUAGE, "zh_cn");
        dialog.setParameter(SpeechConstant.ACCENT, "mandarin");

        dialog.setListener(new RecognizerDialogListener() {
            @Override
            public void onResult(RecognizerResult recognizerResult, boolean b) {
                printResult(recognizerResult);
            }
            @Override
            public void onError(SpeechError speechError) {
            }
        });
        dialog.show();
        Toast.makeText(this, "请开始说话", Toast.LENGTH_SHORT).show();
    }

    //回调结果：
    private void printResult(RecognizerResult results) {
        String text = parseIatResult(results.getResultString());
        // 自动填写地址
        mResultText.append(text);
    }

    public static String parseIatResult(String json) {
        StringBuffer ret = new StringBuffer();
        try {
            JSONTokener tokener = new JSONTokener(json);
            JSONObject joResult = new JSONObject(tokener);

            JSONArray words = joResult.getJSONArray("ws");
            for (int i = 0; i < words.length(); i++) {
                // 转写结果词，默认使用第一个结果
                JSONArray items = words.getJSONObject(i).getJSONArray("cw");
                JSONObject obj = items.getJSONObject(0);
                ret.append(obj.getString("w"));
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return ret.toString();
    }
}

运行程序：
效果如图：
 

 
Demo：https://github.com/229457269/XunFeiVoiceDEmo

后续：后面需要上线到讯飞应用市场的步骤，笔者不在缀述，开发者完成项目打包后，通过审核，即可将该程序发布，并获取讯飞市场用户的收益。
 
 
