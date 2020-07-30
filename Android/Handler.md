Handler

https://blog.csdn.net/qq_37321098/article/details/81535449

https://blog.csdn.net/Sean_css/article/details/79767180?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.edu_weight&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.edu_weight

1.作用：1）传递消息 2)子线程通知主线程更新UI

2.Handler.post(Runnable)其实就是生成一个what为0的Message,调用

```java
myHandler.removeMessages(0);
```

会使runnable任务从消息队列中清除。

3.Handler通过sendMessage()方法发送Message到MessageQueue队列，当前Thread中Looper通过调用loop()，不断去除达到触发条件的Message，通过对应target(Handler)的dispatchMessage()方法，将Message交给Handler的handlerMessage()来处理。一个线程对应一个Looper，一个Looper对应一个MessageQueue，一个MessageQueue可以对应对各Message，但是一个Message只能让一个handler来处理（就是Message中target所指定的handler).