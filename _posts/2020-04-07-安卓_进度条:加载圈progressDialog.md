---
layout: post
title: 安卓_进度条/加载圈progressDialog
date: 2020-04-07
tags: 安卓
---

![demo]({{ "/assets/img/runaround.png" | absolute_url }})
### 1.菊花加载圈
```java
progressDialog = new ProgressDialog(this);
progressDialog.setTitle("菊花转啊转");
progressDialog.setMessage("正在努力加载中...");
progressDialog.show();
// 五秒后退出
Timer timer = new Timer();
timer.schedule(new TimerTask() {

    @Override
    public void run() {

        runOnUiThread(new Runnable() {
            public void run() {
                progressDialog.dismiss();
            }
        });

    }
}, 5000);
```

![demo]({{ "/assets/img/progress.png" | absolute_url }})
### 2.水平进度条
```java
// 通知
private static final int DOWNLOAD_FINISH = 0x0002;
private static final int DOWNLOAD_PROGRESS = 0x0001;
private Handler handler = new Handler(){
    @Override
    public void handleMessage(@NonNull Message msg) {
        switch (msg.what){
            case DOWNLOAD_PROGRESS:
                // 设置进度
                progressDialog.setProgress(msg.arg1);
                break;
            case  DOWNLOAD_FINISH:
                // 退出弹窗
                progressDialog.dismiss();
                break;
        }
    }
};


progressDialog = new ProgressDialog(this);
progressDialog.setTitle("进度条对话框");
progressDialog.setMessage("当前下载进度");
// 设置为进度条模式 默认是STYLE_SPINNER模式
progressDialog.setProgressStyle(ProgressDialog.STYLE_HORIZONTAL);
// 点击空白处不允许退出
progressDialog.setCancelable(false);
progressDialog.show();

// 模拟多线程下载
new Thread(){
    @Override
    public void run() {
        for (int i = 1; i <= 100; i++) {
            Message message = handler.obtainMessage();
            message.arg1 = i;
            message.what = DOWNLOAD_PROGRESS;
            handler.sendMessage(message);
            try {
               TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        handler.sendEmptyMessage(DOWNLOAD_FINISH);
    }
}.start();
```
