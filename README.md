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

### 发布更新

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

以管家小旺为例：
```shell
code-push release "管家小旺" ./bundles 2.0.0 --des "2.0.0 update"
```

第四步 推送我们的app之后，我们需要在根component中获取推送的更新，具体细节见（https://github.com/Microsoft/react-native-code-push#javascript-api-reference），中的plugin usage
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


优缺点
------

其他插件对比
------
