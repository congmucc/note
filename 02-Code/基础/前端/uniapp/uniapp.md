# Uniapp-微信小程序



## 1 启动： 

## 1.1 在官网通过命令行拉取项目文件

[uni-app官网 (dcloud.net.cn)](https://uniapp.dcloud.net.cn/quickstart-cli.html)

```shell
npx degit dcloudio/uni-preset-vue#vite-ts my-vue3-project
```

## 1.2 安装插件

1. uniapp插件

2. 安装组件库 

   > [组件库和配置easycom](https://uniapp.dcloud.net.cn/component/uniui/quickstart.html) 
   >
   > 将`npm`直接改成`pnpm`即可`pnpm i 链接`
   >
   > 注意，`easycom`添加的是`pages.json`,而不是`package.json`
   >
   > 按照官方文档来就行了

3. 安装ts的类型声明文件

   > 首先到[官网](https://uniapp.dcloud.net.cn/tutorial/typescript-subject.html)将tsconfig.json复制一遍到项目中，其次
   >
   > ```
   > pnpm i -D @uni-helper/uni-ui-types
   > ```
   >
   > ```
   > // tsconfig.json
   > {
   >   "compilerOptions": {
   >     // ...
   >     "types": [
   >       "@dcloudio/types", // uni-app API 类型
   >       "miniprogram-api-typings", // 原生微信小程序类型
   >       "@uni-helper/uni-app-types", // uni-app 组件类型
   >       "@uni-helper/uni-ui-types" // uni-ui 组件类型  // [!code ++]
   >     ]
   >   },
   >   // vue 编译器类型，校验标签类型
   >   "vueCompilerOptions": {
   >     "nativeTags": ["block", "component", "template", "slot"]
   >   }
   > }
   > ```
   >
   > 

4. **json注释问题**

   > 将json文件进行文件关联

## 1.3 VScode中启动

启动到VScode查看package.json，查看调试项中包含微信小程序的。

```
"dev:mp-weixin": "uni -p mp-weixin",
```

> 在终端中： pnpm install，之后pnpm dev:mp-weixin即可运行

在manifest.json中添加微信小程序的appid

## 1.4微信小程序打开界面

首先将微信小程序中 **设置-安全设置-服务端口** 打开

将生成的dist文件夹中`dist\dev\mp-weixin`这个路径下的文件打开即可

