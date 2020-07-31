AIDL

1.跨进程通信

2.简单将两个进程分为客户端和服务端，aidl目的是达成在客户端调用服务端的接口

3.做法：

​	3.1：在服务端写好aidl文件，文件内全是接口方法（如需用到实体类需要另外序列化）

​	3.2：服务端写完aidl文件后，继续在服务端的service中创建一个内部类，继承刚创建的aidl中的Stub类实现接口方法，在service中的onBind方法中返回该内部类实例

​	3.3：将整个aidl文件从服务端拷贝到客户端

​	3.4：在客户端中进行绑定服务bindService(...)，实现serviceConnected中的onServiceConnected和onServiceDisconnected方法，在onServiceConnected方法中通过***.Stub.asInterface(service)得到返回的aidl对象，直接操作对象中的方法（即刚刚在服务端实现的接口方法）

4.进阶（Callback)：

​	4.1：同样在服务端写好aidl文件，并写好callback的aidl文件同样复制到客户端

​	4.2：普通的aidl文件同样进行上述3中的做法

​	4.3：callback的接口方法在客户端进行实现，最终实现回调的流程如下：

​	普通aidl通过客户端绑定服务获取对象调用相应的方法，该方法的实现位于服务端，于是调用到服务端方法，服务端方法被调用后进行方法处理，并进行操作所需的关于callback中的方法的调用；

​	在此前callback已经在客户端通过register方法注册进入一个rometecallbacklist中被监听，等待被调用。此时服务端调用到所需要的callback中的方法被监听到，于是服务端调用到客户端中callback关于该方法的实现，最终实现方法回调的整个流程。

​	4.4：callbacklist的写法（举例）：

`private void callbackFirmwareVersion(String version)
	{
		int N = mRemoteCallbackList.beginBroadcast();
		for (int i = 0; i < N; i++)
		{
			try
			{
				mRemoteCallbackList.getBroadcastItem(i).onFirmwareVersion(version);
			}
			catch (RemoteException e)
			{
				e.printStackTrace();
			}
		}
		mRemoteCallbackList.finishBroadcast();
	}`

5.项目中流程验证：

​	5.1：在蓝牙软件APP中绑定服务获取对象，调用对象的getFirmwareVersion()方法，该方法会调用到android/packages/apps/IvtBTCore/src/com/apical/ivtbtcore中的BtService里实现好的接口方法，调用到mBtIVTManager.getFirwareVersion();（这里是为了避免高耦合所以写到另一个文件当中），随后调用sendAt2Bt(BtIVTCmder.BT_GET_FIRMWARE_VERSION);（通过在BtIVTCmder文件中查询BT_GET_FIRMWARE_VERSION可得到对应的命令，此处为AT+B GVER。）

​		此处继续调用到sendAt2Bt方法，方法内又调用SerialPoreUtils文件中的sendSerialPort方法最终对字符串进行解析。

![image-20200709173301265](https://github.com/iizhengzh/MyNoteAboutAnything/blob/master/Android/img/aidl.png)

*此处代码将解析完的字符串发送到另一个方法中进行调用callback方法并传入一些信息*

​	通过判断解析出来的字符串对应的命令获取到相关的数据，然后调用服务端的callback中的方法，由于蓝牙软件APP中已对callback进行了注册监听，所以在服务端调用方法时就会回调到蓝牙软件APP中的关于callback的接口方法实现。

