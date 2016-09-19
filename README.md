CodePushDoc 管家小旺使用微软热更新文档
===========================

##目录
* [使用说明](#使用说明)

    * CodePush简介
    * 安装CodePush CLI
    * 在CodePush服务器注册app
    * 在app上添加CodePush SDK ,配置升级相关代码
    * 更新代码，发布一个应用更新到服务器
    * app收到更新进行更新
    * 更多请参考（https://github.com/Microsoft/code-push/blob/master/cli/README-cn.md#%E5%BA%94%E7%94%A8%E7%AE%A1%E7%90%86）
    
* [优缺点](#优缺点)
  
    * CodePush之于跨平台应用的优点
    * CodePush存在的缺点
    * CodePush适合使用的场景
  
* [其他插件对比](#其他插件对比)
* [总结](#总结)
* [参考链接](#参考链接)

使用说明
------
### CodePush简介
 > CodePush是一个微软开发的云服务器。通过它，开发者可以直接在用户的设备上部署手机应用更新。CodePush相当于一个中心仓库，开发者可以推送当前的更新。（包括JS/HTML/CSS/Image）推送到CodePush，然后应用将会查询是否有更新。
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
      1. ```code-push login```  登录
      * ```code-push access-key ls```  列出登录的token     
      * ```code-push access-key rm <accessKey>```  删除某个access-key
      * ```code-push logout```
      
  * 应用管理
    在你发布更新前，需要用如下命令在CodePush服务上注册一个App：
      1. ```code-push app add "你的应用的名称" ```  添加一个新的app
      * ```code-push app rename "旧的名字" "新的名字"```  重命名一个存在的app
      * ```code-push app rm "你的应用的名字"```   在账号里面移除一个app
      * ```code-push app ls ```  列出你的账户下的所有app
      * ```code-push app transfer ``` 把app的所有权转移到另外一个账号上去
    

### 在app中添加SDK，配置相关代码（以android为例）

   * 在应用中安装react-native-code-push插件,```npm install --save react-native-code-push```
   * 安装rnpm，```npm i rnpm```，如果React Native的版本在v0.27或以上的话，rnpm link已经被集成到React Native CLI里面了。
   * ```rnpm link react-native-code-push``` 或者 ```react-native link react-native-code-push``` (v0.27或以上)
   
### Plugin Installation(Android)
 1. 在android文件夹里的settings.gradle里配置如下内容：
```Java
include ':app',':react-native-code-push'
project(':react-native-code-push').projectDir = new File(rootProject.projectDir,'../node_modules/react-native-code-push/android/app')
```
 2. 获取**部署密钥**，```code-push deployment ls "管家小旺"

 3. 如果rnpm link 没有成功配置，可以手动修改android文件夹里面的MainApplication.java
     
```Java
import com.microsoft.codepush.react.CodePush; //加入codepush的包

@Override
        protected String getJSBundleFile() { 
            return CodePush.getJSBundleFile();
        } //获取app打包更新的路径
        
@Override
        protected List<ReactPackage> getPackages() {
            return Arrays.<ReactPackage>asList(
                    new MainReactPackage(),
                    new CodePush("5LIPMY4Po6MdZeyq995xSKlfiV56VJCzo2e3W", getApplicationContext(), BuildConfig.DEBUG),
                    new RNDeviceInfo(),
                    new ExtraDimensionsPackage(),
                    new BarcodeScannerPackage(),
                    new JPushPackage(),
                    new UMengAgentPackage()
            );
        }   //此处添加new CodePush("5LIPMY4Po6MdZeyq995xSKlfiV56VJCzo2e3W", getApplicationContext(), BuildConfig.DEBUG),
            
```
> 若不知道app的deployment key 可通过命令: code-push deployment ls "管家小旺" -k 获取，默认为Staging

4. 在android/app/build。gradle 中的 android.defaultConfig.versionName属性,将应用版本改为2.0.0（三位数）
5. 部署管家小旺app
   * ```code-push deployment add "管家小旺" ``` 部署管家小旺
   * ```code-push deployment rename "管家小小旺" ``` 重命名
   * ```code-push deployment rm "管家小旺"``` 删除部署的管家小旺
   * ```code-push deployment ls "管家小旺"``` 列出管家小旺的部署情况
   * ```code-push deployment ls "管家小旺" -k```  查看管家小旺部署的key
   * ```code-push deployment history "管家小旺" Staging``` 查看管家小旺历史版本（Staging或Production）

### 更新代码，发布一个应用更新到服务器

第一步  在工程根目录创建一个bundles文件夹，

第二步  运用命令行打包
```shell
react-native bundle --platform <平台> --entry-file <启动文件> --bundle-output <打包js输出文件> --assets-dest <资源输出目录> --dev <是否调试>
```
以管家小旺(安卓版)为例：
```shell
react-native bundle --platform android --entry-file index.android.js --bundle-output ./bundles/index.android.bundle --dev false
```
> 注意：输出后会覆盖原来的bundle文件，输出后的bundle文件名就叫index.android.bundle，此处的平台可以选择ios或者android

第三步  打包bundle结束后，就可以通过CodePush发布更新了。输入以下命令：
```code-push release <应用名称> <bundles所在目录> <对应的应用版本>```

**--deploymentName** 更新环境
**--description** 更新描述
**--mandatory** 是否强制更新

如图：
   ![](https://github.com/syun0216/codePushDoc/raw/master/img/img3.png)


以管家小旺为例：
```shell
code-push release "管家小旺" ./bundles 2.0.0 --des "2.0.0 update"
```

### app收到更新进行更新

推送我们的app之后，我们需要在根component中获取推送的更新，具体细节见（https://github.com/Microsoft/react-native-code-push#javascript-api-reference），中的plugin usage
此处提供一个例子，使用**codePush.sync()**获取更新包：

```javascript
import React, { Component } from 'react';

import {
    AppRegistry,
} from 'react-native'
import SplashScreenView from './common/view/SplashScreenView'
import codePush from 'react-native-code-push';

let codePushOptions = { checkFrequency: codePush.CheckFrequency.MANUAL };

class abc extends Component {
  componentDidMount() {
    codePush.sync({
      updateDialog: {
        title:"有更新啦！",
        optionalUpdateMessage:"获取到更新，你想现在安装吗？",
        optionalInstallButtonLabel:"好的",
        optionalIgnoreButtonLabel:"取消"
      },
      installMode: codePush.InstallMode.IMMEDIATE
    });
  }


  render() {
    return (
     <SplashScreenView/>
    );
  }
}
abc = codePush(codePushOptions)(abc);

AppRegistry.registerComponent('abc', () => abc);
```

#### 效果如图：

   ![](https://github.com/syun0216/codePushDoc/raw/master/img/img2.png)


优缺点
------
### CodePush之于跨平台应用的优点
   
   ReactNative和Cordova等知名的跨平台开发框架，其着力于缩减开发时间，做到"write once,run everywhere"的思想，且跟原生相比，跨平台应用的代码重用率高。CodePush的诞生，正是因为javascript是脚本语言，不需要编译就可以运行的特点。CodePush让开发跨平台应用的开发者能够摆脱发版的限制，一个月更新20次也不在话下，这是原生所不能够的。CodePush官方提供CodePush命令。

### 缺点
   * CodePush只能更新js或者图片，不能更新原生代码。
   * 开始以为CodePush是增量更新，后面使用发现其增量升级仅仅是针对图片资源的，只是对bundle做了zip压缩，通常推送的package大小在600kb左右，还是比较大的。
   * 实际使用中发现有时候存在推送的速度较慢和部分手机始终无法收到推送的问题，CodePush由微软维护，服务器在国外，在国内访问的网速不是很理想。

### CodePush适合使用的场景
   综上，我们可以得出CodePush允许我们更新js、css、image等文件，但是如果修改了原生的android或者ios的代码，CodePush是不支持更新的。所以我们平时新增图片和js页面是可用的。

其他插件对比
------
### 国外插件
   1. react-native-code-push (star 1623)
      1. 官方提供CodePush Service,服务器端目前没有开源,不能用自己的服务器
      2. 通过deployment key 可以take advantage of the Staging and Production deployments，
      3. 有history、RollBack功能
   2. react-native-auto-updater (star 995)   
      1. 只能进行全量更新，不是增量更新，只是js更新，不支持assets更新，需要自己配置好服务端
   3. AppHub （star 149）
      1. 官方提供AppHub Developer Dashboard 做后端版本管理，也可以有开发者配置自己的后端
      2. 收费情况： 日活用户1000以内免费
      
### 国内插件
   1. react-native-pushy
      1. 官方提供Update Service，如果要搭建自己的热更新服务，需要编写自己的js模块与不同的热更新服务器通讯
      
         你可以单独使用本组件的原生部分(不包括js模块)和命令行工具中的bundle、diff、diffFromIpa、diffFromApk四个功能。 这些功能都不会使用我们的热更新服务，也无需注册或登录账号。但你可能要编写自己的js模块来与不同的热更新服务器通讯。
         如果您有兴趣使用我们的成果，搭建私有云服务，可以联系我们。
      * 收费情况
          
         > 目前我们的热更新服务完全免费，但限制每个账号不超过3个应用；每个应用不超过10个活跃的包和100个活跃的热更新版本；每个应用每个月不超过10000次下载。iOS和Android版本记 做不同的应用。已经移除的应用、包版本、热更新版本不在统计之列，所以你可以移除测 试时产生的和已过期版本来更有效的利用空间。我们会在将来推出付费的升级版本，针对用户量较大、版本迭代较快的用户提供扩容方 案。如果您有急迫的需求，可以联系我们。

总结
------
   1. react-native-pushy 由reactnative.cn维护，服务器在国内，可以配置自己的服务端，但是并没有看到开源的服务端示例代码。
   2. react-native-auto-updater 不是增量更新，也不支持assets更新。不用第三方服务，要配置自己的服务端。
   3. AppHub收费。
   4. react-native-code-push 由微软维护，文档详尽，功能丰富，缺点是服务器在国外，可能会因为网络问题影响推送的速度和覆盖率，服务端未开源，无法使用自己的服务器。（成本较低）
   
参考链接
------
   * https://github.com/Microsoft/code-push
   * https://github.com/Microsoft/react-native-code-push
   * http://blog.csdn.net/fengyuzhengfan/article/details/52003798
   * http://hammercui.github.io/post/react-native-android%E5%AE%9E%E6%88%98%EF%BC%9A4-CodePush/
   * http://bbs.reactnative.cn/topic/725/code-push-%E7%83%AD%E6%9B%B4%E6%96%B0%E4%BD%BF%E7%94%A8%E8%AF%A6%E7%BB%86%E8%AF%B4%E6%98%8E%E5%92%8C%E6%95%99%E7%A8%8B
   
