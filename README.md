    说明：本SDK为公司内部使用，可参考共同探讨学习，如想使用，请联系商务 
## 一、     理论知识  ##

请转向[https://github.com/Oslanka/SocketPush/blob/master/socket.md](https://github.com/Oslanka/SocketPush/blob/master/socket.md)

## 二、            Push sdk android用法  ##
## 1. 引入pushlib 库 ##
    compile project(':pushlib')
## 2.初始化 ##
    Push.init(this,apiId);

    /*初始化,如果 不用start 只用通知功能，可只调用init 而不调用。。。其中 apiId由服务端与客户端协商。
    在PAD点餐项目中，这个apiId使用了商户的id，推送时根据商户的id推送，其他商家注册的apiId不会被推送。*/

    Push.setDebug(true);//是否使用测试环境 服务器  10.168.31.69
## 3.使用Push ##
     public class MainActivity extends AppCompatActivity {
    	private TextView textValue;

	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);
	        textValue = (TextView) findViewById(R.id.text_value);

			//Push  begin 
	        Push.start(this, "1234", new MessageListener() {
	            @Override
	            public void onGetKey(String id) {
					//获取的服务端 唯一指定id
	            }
					
	            @Override
	            public void onMessageReceive(String message) {
	                textValue.setText(message);
    				//接收到的数据
	            }
	
	            @Override
	            public void onHeartBeat(String beatTime) {
	                Log.i("push------>time", beatTime);
    				//心跳检测
	            }
	
	            @Override
	            public void onError(String str) {
	                Log.i("push------>", str);
    				//异常错误
	            }
	        });
	
	    }

		@Override
	    protected void attachBaseContext(Context base) {
	        Push.proguard(base);
    		//调用此方法，会启动进程守护，会在程序被杀死的情况下依然连接socket
	        super.attachBaseContext(base);
	    }

	    @Override
	    protected void onDestroy() {
	        super.onDestroy();
	        Push.stop(this);
	    }
    }
## 4.PushSDK推送数据结构 ##
   说明：pushSDK 推送数据结构 采用json形式，json形式无限制，但action字段必须固定.

	    {
		    "open":"notification",
		    "orderid":"38468",
		    "type":"new"
	    }
### "open":"notification" ###
   如想使用Notification，只需指定
 `"open":"notification"`
，当action不指定为"notification"，为普通消息。

![avatar](https://github.com/Oslanka/SocketPush/blob/master/notification.gif)
## 5.其他 ##
### android进程守护参考 ：[https://github.com/Marswin/MarsDaemon](https://github.com/Marswin/MarsDaemon)

   说明：A lite library, you can make your project depend it easily, and your project will be UNDEAD
   （这是一个轻量级的库，保证你的程序不死）本使用已对MarsDaemon做修改，不必继承DaemonApplication
   直接调用 Push.proguard(base);

**进程守护实现**

        1.提高进程优先级，降低被回收或杀死概率
    	2.在进程被干掉后，进行拉起

   程序保活方法很多种，不一一列举实现。

### 启用进程守护与否 ###

![avatar](https://github.com/Oslanka/SocketPush/blob/master/proguard.gif)
   
## 三、            PushLib 我们做了什么  ##
	
## 1.唯一身份ID的生成 ##

Push服务器生成ID标准：
需客户端上传参数：apiId（A），包名，和设备唯一Id（B）；A+包名+B 共同生成了后台的ID，可严格的去重，只有同apiId下设备Id重复，才可能导致后台生成的ID重复（小概率事件）。可以认为一个ID唯一标示一个设备。从而达到推送指定设备目的。

如，用户X想与用户Y通过Push服务器通信，那么X，与Y 初始化时的apiId和包名必须相同，而设备id不同，X与Y可通过Push服务通信。 



## 2.Socket连接中的SSL双向认证 ##

请转向[https://github.com/Oslanka/SocketPush/blob/master/SSL.md](https://github.com/Oslanka/SocketPush/blob/master/SSL.md)

## 3.Socket 字节规定 ##
## Socket 传送内容字节规定 ##

规定字节排列方式，用于取对应字节，处理逻辑

![avatar](https://github.com/Oslanka/SocketPush/blob/master/byte.png)

packet 4字节，header 2字节，version 2字节，operation 4字节，sequence 4 字节，后面是body 理论上不限制。


## 4.流程 ##
    init=>获取唯一id=>启动服务=>socket请求连接=>连接成功（socket有连接失败重连机制）

![avatar](https://github.com/Oslanka/SocketPush/blob/master/liuchengtu.png)

##  Contact me  ##

Email: [cainingning@lashou-inc.com](cainingning@lashou-inc.com)