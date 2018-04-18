### quickApp 快应用采坑之路

1. PC安装toolkit工具

    需要电脑安装node 版本推荐 推荐v6.11.3 及 以上
    注意: 不要使用8.0.*版本．这个版本内部ZipStream实现与node-archive包不兼容，会引起报错

    安装hap-toolkit：
        npm install -g hap-toolkit
        
        hap -V 检查安装是否成功
    
2. 手机安装调试器、 平台预览版调试器 见官网介绍


3. 创建项目
    hap init <ProjectName>

    
    目录结构： 这个项目已经包含了项目配置与示例页面的初始代码，项目根目录主要结构如下。
    
        ├── sign                      rpk 包签名模块
        │   └── debug                 调试环境
        │       ├── certificate.pem   证书文件
        │       └── private.pem       私钥文件
        ├── src
        │   ├── Common                公用的资源和组件文件
        │   │   └── logo.png          应用图标
        │   ├── Demo                  页面目录
        │   |   └── index.ux          页面文件，可自定义页面名称
        │   ├── app.ux                APP 文件，可引入公共脚本，暴露公共数据和方法等
        │   └── manifest.json         项目配置文件，配置应用图标、页面路由等
        └── package.json              定义项目需要的各种模块及配置信息

4. 安装依赖
    npm install

5. 启动项目 

    编译：
        npm run build
    
    编译生成的 dist 目录里才是最终产物：rpk 文件 手机会下载该文件进行安装，才可以启动项目 


    按照官方文档一直走，到 npm run build 时，报错： "Cannot find module '.../node_modules/hap-tools/webpack.config.js'"

    主要是因为创建项目后就有一个 node_module 文件夹了，里面有一个 hap-tools 包。如果 npm install 安装依赖，高版本的 npm 可能会把 node_module 原有的包清空再安装依赖，这时只要再手动安装下 hap-tools 就行了

    ```

    npm install hap-tools

    ```

    如果要监听源码变化自动编译，可以运行 watch 命令。

    npm run watch




### manifest文件

manifest.json文件中包含了应用描述、接口声明、页面路由信息。主要配置如下：
    
    "config":{}  用于定义系统配置和全局数据。

    "features":[]   快应用提供的接口列表，需要在这里声明才可以用。

    "router": {}  路由配置信息。
|
    "display": {}     用于定义与UI显示相关的配置。


### 重要接口

#### 请求接口

    1.请求接口：fetch

    2.声明：系统接口，需要先在 manifest文件中声明： 
        {"name": "system.fetch"}

    3.导入模块

        import fetch from '@system.fetch' 
        或 
        const fetch = require('@system.fetch')
    
    4. 接口定义

        fetch.fetch(OBJECT)
    5.示例：
        fetch.fetch({
        url: 'http://www.example.com',
        success: function (data) {
            console.log(`title: ${JSON.parse(data.data).title}`)
        },
        fail: function (data, code) {
            console.log(`handling fail, code = ${code}`)
        }
        })

#### 数据存储 storage

    1.接口声明
        
        {"name": "system.storage"}

    2.导入模块
        i
        mport storage from '@system.storage'         
        或         
        const storage = require('@system.storage')
    
    3.API

        storage.get(OBJECT) 读取存储内容

        storage.set(OBJECT)  设置缓存

        storage.clear(OBJECT) 清空存储内容（所有缓存数据都会清空）

        storage.delete(OBJECT) 删除个给定key值对应的存储内容

#### 地理位置  geolocation


    1. 接口声明
        {"name": "system.geolocation"}

    2. 导入模块
    
        import geolocation from '@system.geolocation'         
        或         
        const geolocation = require('@system.geolocation')

    API 
        geolocation.getLocation(OBJECT) 获取地理位置

#### 网络状态

    1.接口声明：
        {"name": "system.network"}

    2.导入模块

    import network from '@system.network'
    或 
    const network = require('@system.network')

#### 设备信息

    1.接口声明：
        {"name": "system.device"}
    
    2.导入模块
    
    import device from '@system.device' 
    或 
    const device = require('@system.device')

    3.API

        device.getInfo(OBJECT) 获取设备信息，具体信息如下：
        
            参数值	类型	说明
            brand	String	设备品牌
            manufacturer	String	设备生产商
            model	String	设备型号
            product	String	设备代号
            osType	String	操作系统名称
            osVersionName	String	操作系统版本名称
            osVersionCode	Integer	操作系统版本号
            platformVersionName	String	运行平台版本名称
            platformVersionCode	Integer	运行平台版本号
            language	String	系统语言
            region	String	系统地区
            screenWidth	Integer	屏幕宽
            screenHeight	Integer	屏幕高

        device.getId(OBJECT) 获取设备标识，支持 device、mac、user、advertising 四种类型。根据传入的参数，可提供一至多个

            device.getId({
                type: ['device', 'mac'], // 最少一个，最多四个
                success: function (data) {
                    console.log(`handling success: ${data.device}`)
                },
                fail: function (data, code) {
                    console.log(`handling fail, code = ${code}`)
                }
            })

        device.getDeviceId(OBJECT) 正式版本可用 获取设备唯一标识。需要用户授权
        设备唯一标识。在Android上返回IMEI或MEID


        device.getUserId(OBJECT) 正式版本可用 获取用户唯一标识  

        用户唯一标识。在Android上返回androidid