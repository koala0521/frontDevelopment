### quickApp 快应用采坑之路

按照官方文档一直走，到 npm run build 时，报错： "Cannot find module '.../node_modules/hap-tools/webpack.config.js'"

主要是因为创建项目后就有一个 node_module 文件夹了，里面有一个 hap-tools 包。如果 npm install 安装依赖，高版本的 npm 可能会把 node_module 原有的包清空再安装依赖，这时只要再手动安装下 hap-tools 就行了

```

npm install hap-tools

```
### manifest文件

manifest.json文件中包含了应用描述、接口声明、页面路由信息。主要配置如下：
    
    "config":{}  用于定义系统配置和全局数据。

    "features":[]   快应用提供的接口列表，需要在这里声明才可以用。

    "router": {}  路由配置信息。

    "display": {}     用于定义与UI显示相关的配置。
