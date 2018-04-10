+++
Categories = ["Development"]
Tags = ["Development"]
date = "2017-03-24T19:53:01+08:00"
title = "新浪微博授权登录"
Description = ""
menu = "main"

+++

## 使用新浪微博授权登录

* 先登录新浪微博开发者平台，注册一个开发者账号或者登录自己的微博号

* 新浪微博开发者平台：[http://open.weibo.com](http://weibo.com) 

* 在网站主页面点击微连接，移动应用，添加新应用。

* 创建完成后就生成一个APP Key。

* 去下载新浪微博SDK,选择对应版本。

* 把app_signatures.apk装入测试机或模拟器，点击运行填入你正在开发的包名，生成MD5标签

* 在新浪微博开发网站进入我的应用，应用信息，基本应用，编辑，把包名和MD5签名写入保存。

* 最后不要忘了，注册一下回调地址，授权回调页和取消或授权回调页都写: http://www.sina.com

* 把weiboSDKCore_3.1.2.jar包复制到AS中libs目录下，点击该jar包，右键，as a library ，把libs中的文件也放到libs下。

* 记得在AndroidMainfest.xml中添加权限

<code>
    
    <activity
    android:name = "com.sina.weibo.sdk.component.WeiboSdkBrowser"
    android:configChanges = "keyboardHidden|orientation"
    android:windowSoftInputMode = "adjustResize"
    android:exported = "false" >
    </activity>
    
<code/>

* 新建一个接口

<code>

    public interface Constants {
    public static final String APP_KEY      = "2703292856";          // 应用的APP_KEY
    public static final String REDIRECT_URL = "http://www.sina.com";// 应用的回调页根据注册的回调页填写
    public static final String SCOPE =                          // 应用申请的高级权限
            "email,direct_messages_read,direct_messages_write,"
                    + "friendships_groups_read,friendships_groups_write,statuses_to_me_read,"
                    + "follow_app_official_microblog," + "invitation_write";
    }

<code/>

* 新建一个activity

<code>

    public class MainActivity extends Activity {
    private AuthInfo authInfo;
    private Oauth2AccessToken accessToken;
    private SsoHandler ssoHandler;
    private Button button;//登录按钮
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_wbauth);
        button= (Button) findViewById(R.id.btn);
      authInfo=new AuthInfo(this, Constants.APP_KEY,Constants.REDIRECT_URL,Constants.SCOPE);
        ssoHandler=new SsoHandler(this,authInfo);
    //按钮点击事件
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                ssoHandler.authorize(new AuthListener());
            }
        });
    }
    /**
     * 当 SSO 授权 Activity 退出时，该函数被调用。
     * @see {@link Activity#onActivityResult}
     */
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        // SSO 授权回调
        // 重要：发起 SSO 登陆的 Activity 必须重写 onActivityResults
        if ( ssoHandler!= null) {
            ssoHandler.authorizeCallBack(requestCode, resultCode, data);
        }
    }
    /**
     * 微博认证授权回调类。
     * 1. SSO 授权时，需要在 {@link #onActivityResult} 中调用 {@link SsoHandler#authorizeCallBack} 后，
     *    该回调才会被执行。
     * 2. 非 SSO 授权时，当授权结束后，该回调就会被执行。
     * 当授权成功后，请保存该 access_token、expires_in、uid 等信息到 SharedPreferences 中。
     */
    class AuthListener implements WeiboAuthListener {
        @Override
        public void onComplete(Bundle values) {
            // 从 Bundle 中解析 Token
            accessToken = Oauth2AccessToken.parseAccessToken(values);
            //从这里获取用户输入的 电话号码信息
            String  phoneNum =  accessToken.getPhoneNum();
            if (accessToken.isSessionValid()) {
        // 可以获取各种信息
                Toast.makeText(WBAuthActivity.this,"授权成功", Toast.LENGTH_SHORT).show();
            } else {
                // 以下几种情况，您会收到 Code：
                // 1. 当您未在平台上注册的应用程序的包名与签名时；
                // 2. 当您注册的应用程序包名与签名不正确时；
                // 3. 当您在平台上注册的包名和签名与您当前测试的应用的包名和签名不匹配时。
                String code = values.getString("code");
                Toast.makeText(WBAuthActivity.this, "shibai", Toast.LENGTH_LONG).show();
            }
        }
        @Override
        public void onCancel() {
            Toast.makeText(WBAuthActivity.this,"取消授权", Toast.LENGTH_LONG).show();
        }
        @Override
        public void onWeiboException(WeiboException e) {
          Toast.makeText(WBAuthActivity.this,"exception:" +e.getMessage(),Toast.LENGTH_LONG).show();
        }
    }
    }
<code/>

## 我的GitHub：

* https://github.com/pugongyingbo