CodePushDoc 管家小旺使用微软热更新文档
===========================

##目录
* [使用说明](#使用说明)

    * [CodePush简介](CodePush简介)
    * [安装CodePush CLI](安装CodePush CLI)
    * [在CodePush服务器注册app](在CodePush服务器注册app)
    * [在app上添加CodePush SDK ,配置升级相关代码](在app上添加CodePush SDK ,配置升级相关代码)
    * [更新代码，发布一个应用更新到服务器](更新代码，发布一个应用更新到服务器)
    * [app收到更新进行更新](app收到更新进行更新)
    * 更多请参考（https://github.com/Microsoft/code-push/blob/master/cli/README-cn.md#%E5%BA%94%E7%94%A8%E7%AE%A1%E7%90%86）
    
* [优缺点](#优缺点)
  
    * CodePush之于跨平台应用的优点
    * CodePush存在的缺点
    * CodePush适合使用的场景
  
* [其他插件对比](#其他插件对比)

使用说明
------
### CodePush简介
    CodePush是一个云服务，它能让Cordova和React Native的开发者将手机应用的更新直接部署到用户的设备上。
    它担任类似中间库的角色，开发者可以把更新的（JS、HTML、CSS和图片）发布到这个仓库上，然后哪些Apps就能查询到更新了。
    这就让你可以与你的用户群有一个更确定且直接的交互模式，当你定位到Bug或添加小功能时，
    就不需要重新构建二进制文件在AppStore或者安卓应用市场重新发布了.
------

### 安装CodePush CLI
  在控制台输入 ```npm install -g code-push-cli```,安装CodePush CLI
  安装完毕之后再控制台输入 ```code-push -v ```
  
------
### 在CodePush服务器注册app
  * 创建账号
    创建一个CodePush账号，```code-push register```
  * 身份认证
    在管理你的账号之前，需要使用GitHub或者微软账号注册和登录， 
      ```code-push login```
      ```code-push whoami```
      ```code-push logout```...
  * 应用管理
    在你发布更新前，需要用如下命令在CodePush服务上注册一个App：
      ```code-push app add "你的应用的名称" ```
  
优缺点
------

其他插件对比
------
