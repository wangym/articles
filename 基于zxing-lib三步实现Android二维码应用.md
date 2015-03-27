只需要基于开源项目zxing-lib，便可轻松创建独立二维码App，也可将二维码功能集成进App内。本文也是zxing-lib的使用文档。同时抛砖引玉，欢迎大家一起共建zxing-lib。  

项目地址：https://github.com/wangym/zxing-client-android  
遵循协议：Apache License Version 2.0  
简介：zxing-lib是一款开源二维码扫码库，基于ZXing进行剪裁和二次开发，支持更多的个性扩展功能，全库仅52KB，以Android library project输出，能被快速使用和集成。  
 
(以下步骤基于已有Android开发经验为前提)  
 
#1、环境准备：
检出zxing-lib项目并导入IDE  
git clone git@github.com:wangym/zxing-client-android.git  
导入IDE后将zxing-lib设为Android library project，并以library方式引入你正在开发的Android代码工程内  
 
#2、调用扫码：
调用zxing-lib库的扫码界面  
```Java
/**
 * @author yumin
 */
public class MainActivity extends Activity {

    /**
     *
     */
    private static final int REQUEST_CODE = 200;

    @Override
    protected void onCreate(Bundle savedInstanceState) {

        //......
        initActivity();
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {

        if (null != data && requestCode == REQUEST_CODE) {
            switch (resultCode) {
                case Activity.RESULT_OK:
                    data.setClass(this, CaptureResultActivity.class);
                    startActivity(data);
                    break;
                default:
                    break;
            }
        }
    }

    private void initActivity() {

        //......
        Button btnScanUtf8 = (Button) findViewById(R.id.scan_utf_8);
        btnScanUtf8.setOnClickListener(new Button.OnClickListener() {
            @Override
            public void onClick(View view) {
                callCapture("UTF-8");
            }
        });
    }

    private void callCapture(String characterSet) {

        Intent intent = new Intent();
        intent.setAction(Intents.Scan.ACTION);
        // intent.putExtra(Intents.Scan.MODE, Intents.Scan.QR_CODE_MODE);
        intent.putExtra(Intents.Scan.CHARACTER_SET, characterSet);
        intent.putExtra(Intents.Scan.WIDTH, 600);
        intent.putExtra(Intents.Scan.HEIGHT, 400);
        // intent.putExtra(Intents.Scan.PROMPT_MESSAGE, "type your prompt message");
        intent.setClass(this, CaptureActivity.class);
        startActivityForResult(intent, REQUEST_CODE);
    }
}
```
调用CaptureActivity即可呼出扫码界面，支持设定个性参数，当扫码成功后返回到CaptureResultActivity  
 
#3、获取结果：
获取二维码扫码之后的结果  
```Java
/**
 * @author yumin
 */
public class CaptureResultActivity extends Activity {

    /**
     *
     */
    private TextView tvResult;
    private TextView tvResultFormat;
    private TextView tvUri;

    @Override
    protected void onCreate(Bundle savedInstanceState) {

        //......
        initActivity();
        initData();
    }

    private void initActivity() {

        tvResult = (TextView) findViewById(R.id.tv_result);
        tvResultFormat = (TextView) findViewById(R.id.tv_result_format);
        tvUri = (TextView) findViewById(R.id.tv_uri);
    }

    private void initData() {

        Intent intent = getIntent();
        if (null != intent) {
            tvResult.setText(intent.getStringExtra(Intents.Scan.RESULT));
            tvResultFormat.setText(intent.getStringExtra(Intents.Scan.RESULT_FORMAT));
            tvUri.setText(intent.toUri(intent.getFlags()));
        }
    }
}
```
二维码扫码成功后调出CaptureResultActivity，上例将结果直接显示，本质上是通过Intent获取扫码结果的。  
 
完整示例见zxing-lib-example。但请不要局限于示例中的使用方式，比如在二维码扫码成功后，并非只能通过一个Activity才能取到扫码的结果。请结合具体场景灵活运用。  
 
如有任何疑问或建议，请随时联系我 :)
