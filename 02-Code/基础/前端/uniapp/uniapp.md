# Uniapp-微信小程序



## 1 启动： 

### 1.1 VScode中启动

启动到VScode查看package.json，查看调试项中包含微信小程序的。

```
"dev:mp-weixin": "uni -p mp-weixin",
```

> 在终端中： pnpm install，之后pnpm dev:mp-weixin即可运行

### 1.2微信小程序打开界面

首先将微信小程序中 **设置-安全设置-服务端口** 打开

将生成的dist文件夹中`dist\dev\mp-weixin`这个路径下的文件打开即可

