> 文档模板：v0.4.2

<p align="center">
  <h1 align="center"> <code>react-native-sound-player</code> </h1>
</p>

本项目基于 [react-native-sound-player@0.14.5](https://github.com/johnsonsu/react-native-sound-player/tree/0.14.5) 开发。

该第三方库的仓库已迁移至 Gitcode，且支持直接从 npm 下载，新的包名为：`@react-native-ohos/react-native-sound-player`，版本所属关系如下：

| 三方库名称 | 三方库版本（npm地址） | 发布信息 | 支持RN版本 | Autolink | 编译API版本 | 社区基线版本 | 源码地址 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| @react-native-ohos/react-native-sound-player | [~ 0.15.0（开发中）](https://www.npmjs.com/package/@react-native-ohos/react-native-sound-player) | [Gitcode Releases](https://gitcode.com/CPF-RN/rntpc_react-native-sound-player/releases) | 0.82.* | 是 | API12+ | 0.14.5 | [br_rnoh0.82](https://gitcode.com/CPF-RN/rntpc_react-native-sound-player/tree/br_rnoh0.82) |
| @react-native-oh-tpl/react-native-sound-player | [<= 0.14.6@deprecated](https://www.npmjs.com/package/@react-native-oh-tpl/react-native-sound-player) | [Github Releases(deprecated)](https://github.com/react-native-oh-library/react-native-sound-player/releases) | 0.72.*/0.77.* | 否 | API12+ | 0.14.5 | [sig](https://github.com/react-native-oh-library/react-native-sound-player) |

## 简介

react-native-sound-player 是一个 React Native 音频播放三方库，用于播放或流式传输音频文件。<br/>
支持播放本地音频文件、资源文件音频和网络 URL 音频，提供播放、暂停、恢复、停止、seek、音量调节、循环次数设置等播放控制能力，并支持订阅"播放完成"、"加载完成"等事件。

## 下载安装

进入到工程目录并输入以下命令：

**npm**

```bash
npm install @react-native-ohos/react-native-sound-player
```

**yarn**

```bash
yarn add @react-native-ohos/react-native-sound-player
```

## Link

| 版本 | 是否支持autolink | RN框架版本 |
|------|----------------|-----------|
| <=0.14.6@deprecated | 否 | 0.72.* |
| <=0.14.6@deprecated | 否 | 0.77.* |

使用AutoLink的工程需要根据该文档配置，Autolink框架指导文档：https://gitcode.com/CPF-RN/ohos_react_native/blob/main/docs/zh-cn/02-开发/02-开发指南/Autolinking.md

ManualLink: 此步骤为手动配置原生依赖项的指导

首先需要使用 DevEco Studio 打开项目里的 HarmonyOS 工程 `harmony`。

### 1. Overrides RN SDK

为了让工程依赖同一个版本的 RN SDK，需要在工程根目录的 `oh-package.json5` 添加 overrides 字段，指向工程需要使用的 RN SDK 版本。替换的版本既可以是一个具体的版本号，也可以是一个模糊版本，还可以是本地存在的 HAR 包或源码目录。

关于该字段的作用请阅读[官方说明](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V5/ide-oh-package-json5-V5#zh-cn_topic_0000001792256137_overrides)

```json
{
  "overrides": {
    "@rnoh/react-native-openharmony": "~0.72.38" // ohpm 在线版本
    // "@rnoh/react-native-openharmony" : "./react_native_openharmony.har" // 指向本地 har 包的路径
    // "@rnoh/react-native-openharmony" : "./react_native_openharmony" // 指向源码路径
  }
}
```

### 2. 引入原生端代码

目前有两种方法：

- 通过 har 包引入；
- 直接链接源码。

方法一：通过 har 包引入（推荐）

> [!TIP] har 包位于三方库安装路径的 `harmony` 文件夹下。

打开 `entry/oh-package.json5`，添加以下依赖

```json
"dependencies": {
    "@react-native-ohos/react-native-sound-player": "file:../../node_modules/@react-native-ohos/react-native-sound-player/harmony/rn_sound_player.har"
  }
```

点击右上角的 `sync` 按钮

或者在命令行终端执行：

```bash
cd entry
ohpm install
```

方法二：直接链接源码

> [!TIP] 如需使用直接链接源码，请参考[直接链接源码说明](https://gitcode.com/CPF-RN/usage-docs/blob/master/zh-cn/link-source-code.md)

### 3. 配置 CMakeLists 和引入 RNSoundPlayerPackage

打开 `entry/src/main/cpp/CMakeLists.txt`，添加：

```diff
+ set(OH_MODULES "${CMAKE_CURRENT_SOURCE_DIR}/../../../oh_modules")

# RNOH_BEGIN: manual_package_linking_1
+ add_subdirectory("${OH_MODULES}/@react-native-ohos/react-native-sound-player/src/main/cpp" ./sound-player)
# RNOH_END: manual_package_linking_1

# RNOH_BEGIN: manual_package_linking_2
+ target_link_libraries(rnoh_app PUBLIC rnoh_sound_player)
# RNOH_END: manual_package_linking_2
```

打开 `entry/src/main/cpp/PackageProvider.cpp`，添加：

```diff
#include "RNOH/PackageProvider.h"
#include "generated/RNOHGeneratedPackage.h"
+ #include "RNSoundPlayerPackage.h"

using namespace rnoh;

std::vector<std::shared_ptr<Package>> PackageProvider::getPackages(Package::Context ctx) {
    return {
      std::make_shared<RNOHGeneratedPackage>(ctx),
+      std::make_shared<RNSoundPlayerPackage>(ctx)
    };
}
```

### 4. 在 ArkTs 侧引入 RNSoundPlayerPackage

打开 `entry/src/main/ets/RNPackagesFactory.ts`，添加：

```diff
  ...
+ import {RNSoundPlayerPackage} from '@react-native-ohos/react-native-sound-player/ts';

export function createRNPackages(ctx: RNPackageContext): RNPackage[] {
  return [
+   new RNSoundPlayerPackage(ctx)
  ];
}
```

### 运行

点击右上角的 `sync` 按钮

或者在命令行终端执行：

```bash
cd entry
ohpm install
```

然后编译、运行即可。

## 约束与限制

### 兼容性

请到三方库相应的 Releases 发布地址查看配套的版本信息：[@react-native-oh-tpl/react-native-sound-player Releases](https://github.com/react-native-oh-library/react-native-sound-player/releases)。

### 权限要求

无特殊权限要求。

## 使用示例

下面的代码展示了这个库的基本使用场景：

> [!WARNING] 使用时 import 的库名不变。

```js
import React from 'react';
import { Button, View, ScrollView } from 'react-native';
import SoundPlayer from 'react-native-sound-player';

const SoundPlayerDemo = () => {
    return (
        <>
            <ScrollView>
                <View style={{ marginTop: 10, display: 'flex', flexDirection: 'column', alignItems: 'center' }}>
                    <View style={{ width: 180 }}>
                        <Button
                            title="playSoundFileAsync"
                            onPress={async () => {
                                await SoundPlayer.playSoundFileAsync("tone", "mp3");
                            }}
                        />
                        <View style={{ marginTop: 10 }} ></View>
                        <Button
                            title="loadSoundFileAsync"
                            onPress={async () => {
                                await SoundPlayer.loadSoundFileAsync("tone", "mp3");
                            }}
                        />
                        <View style={{ marginTop: 10 }} ></View>
                        <Button
                            title="playAssetAsync"
                            onPress={async () => {
                                await SoundPlayer.playAssetAsync(require('./assets/tone.mp3'));
                            }}
                        />
                        <View style={{ marginTop: 10 }} ></View>
                        <Button
                            title="loadAssetAsync"
                            onPress={async () => {
                                await SoundPlayer.loadAssetAsync(require('./assets/tone.mp3'));
                            }}
                        />
                        <View style={{ marginTop: 10 }} ></View>
                        <Button
                            title="playAsync"
                            onPress={async () => {
                                await SoundPlayer.playAsync();
                            }}
                        />
                        <View style={{ marginTop: 10 }} ></View>
                        <Button
                            title="pauseAsync"
                            onPress={async () => {
                                await SoundPlayer.pauseAsync();
                            }}
                        />
                        <View style={{ marginTop: 10 }} ></View>
                        <Button
                            title="resumeAsync"
                            onPress={async () => {
                                await SoundPlayer.resumeAsync();
                            }}
                        />
                        <View style={{ marginTop: 10 }} ></View>
                        <Button
                            title="stopAsync"
                            onPress={async () => {
                                await SoundPlayer.stopAsync();
                            }}
                        />
                        <View style={{ marginTop: 10 }} ></View>
                        <Button
                            title="seek"
                            onPress={() => {
                                SoundPlayer.seek(10);
                            }}
                        />
                        <View style={{ marginTop: 10 }} ></View>
                        <Button
                            title="setVolume"
                            onPress={() => {
                                SoundPlayer.setVolume(1);
                            }}
                        />
                        <View style={{ marginTop: 10 }} ></View>
                    </View>
                </View>
            </ScrollView>
        </>
    )
}

export default SoundPlayerDemo;
```

## 使用说明

**播放本地音频文件**

```javascript
// 播放 name 为 tone、类型为 mp3 的音频文件
await SoundPlayer.playSoundFileAsync('tone', 'mp3');
```

**播放网络音频**

```javascript
await SoundPlayer.playUrlAsync('https://example.com/audio/tone.mp3');
```

**播放资源文件中的音频**

```javascript
await SoundPlayer.playAssetAsync(require('./assets/tone.mp3'));
```

**播放控制**

```javascript
await SoundPlayer.pauseAsync();    // 暂停
await SoundPlayer.resumeAsync();   // 恢复播放
await SoundPlayer.stopAsync();     // 停止播放
SoundPlayer.seek(10);              // 跳转到第 10 秒
SoundPlayer.setVolume(1);          // 设置音量（不改变设备音量）
SoundPlayer.setNumberOfLoops(0);   // 0 表示仅播放一次，其他数字表示无限循环
```

**事件监听**

```javascript
const subscription = SoundPlayer.addEventListener(
  'FinishedPlaying',
  ({ success }) => {
    console.log('finished playing', success);
  }
);

// 组件卸载或不再需要监听时移除
subscription.remove();
```

## 接口说明

> [!TIP] "Platform"列表示该属性在原三方库上支持的平台。

> [!TIP] "OpenHarmony Support"列为 yes 表示 OpenHarmony平台支持 该属性；no 则表示不支持；partially 表示部分支持。使用方法跨平台一致，效果对标 iOS 或 Android 的效果。

| 名称 | 描述 | 类型 | 必填 | 平台 | OpenHarmony平台支持 |
| ---- | ---- | ---- | ---- | ---- | ------------------- |
| playSoundFileAsync | 异步播放名为 `fileName`、类型为 `fileType` 的音频文件。 | function | no | / | yes |
| loadSoundFileAsync | 异步加载名为 `fileName`、类型为 `fileType` 的音频文件，但不播放。 | function | no | / | yes |
| playUrlAsync | 异步播放来自 URL 的音频。 | function | no | / | yes |
| loadUrlAsync | 异步加载来自指定 `url` 的音频，但不播放。 | function | no | / | yes |
| playAssetAsync | 异步播放来自资源文件的音频，资源编号可通过 `require('./assets/tone.mp3')` 获取。 | function | no | / | yes |
| loadAssetAsync | 异步加载来自资源文件的音频，但不播放。 | function | no | / | yes |
| playAsync | 异步播放已加载的音频文件。该方法与 `resumeAsync` 相同。 | function | no | / | yes |
| pauseAsync | 异步暂停当前正在播放的文件。 | function | no | / | yes |
| resumeAsync | 异步从暂停状态恢复，并继续播放同一个文件。该方法与 `playAsync` 相同。 | function | no | / | yes |
| stopAsync | 异步停止播放。 | function | no | / | yes |
| playSoundFile | 播放名为 `fileName`、类型为 `fileType` 的音频文件。 | function | no | Android、iOS | no |
| loadSoundFile | 加载名为 `fileName`、类型为 `fileType` 的音频文件，但不播放。 | function | no | Android、iOS | no |
| playUrl | 播放来自 URL 的音频。 | function | no | Android、iOS | no |
| loadUrl | 加载来自指定 `url` 的音频，但不播放。 | function | no | Android、iOS | no |
| playAsset | 播放来自资源文件的音频，资源编号可通过 `require('./assets/tone.mp3')` 获取。 | function | no | Android、iOS | no |
| loadAsset | 加载来自资源文件的音频，但不播放。 | function | no | Android、iOS | no |
| onFinishedPlaying | 订阅"播放完成"事件。文件播放结束时会调用 `callback` 函数。该方法即将废弃，请改用 `addEventListener`。 | function | no | All | yes |
| onFinishedLoading | 订阅"加载完成"事件。文件加载完成后会调用 `callback` 函数，也就是文件已可用于 `play()`、`resume()`、`getInfo()` 等操作。该方法即将废弃，请改用 `addEventListener`。 | function | no | All | yes |
| addEventListener | 订阅任意事件。返回一个订阅对象。通过此方法创建的订阅不能通过调用 unmount() 移除。你需要在不再需要该事件监听时，或者组件卸载时，调用 `yourSubscriptionObject.remove()`。 | function | no | All | yes |
| play | 播放已加载的音频文件。该方法与 `resume` 相同。 | function | no | Android、iOS | no |
| pause | 暂停当前正在播放的文件。 | function | no | Android、iOS | no |
| resume | 从暂停状态恢复，并继续播放同一个文件。该方法与 `play` 相同。 | function | no | Android、iOS | no |
| stop | 停止播放。 | function | no | Android、iOS | no |
| seek | 跳转到当前播放文件的指定秒数位置。 | function | no | All | yes |
| setVolume | 设置当前播放器的音量，不会修改设备音量。 | function | no | All | yes |
| setNumberOfLoops | 0 表示仅播放一次，其他数字表示无限循环。 | function | no | All | yes |
| getInfo | 获取当前挂载音频媒体的 currentTime 和 duration。该方法返回一个 promise，解析为包含 currentTime 和 duration 属性的对象。 | function | no | All | yes |
| unmount | 取消订阅"播放完成"和"加载完成"事件。该方法即将废弃，请改用 `addEventListener`，并在不再需要监听时通过调用 `yourSubscriptionObject.remove()` 进行移除。 | function | no | All | yes |
| playSoundFileWithDelay | 仅 iOS 可用。将在当前设备时间的 `delay` 秒后播放名为 `fileName`、类型为 `fileType` 的音频文件。 | function | no | iOS | no |
| setSpeaker | 将默认音频输出切换为扬声器。 | function | no | iOS | no |
| setMixAudio | 如果设置此选项，你的音频将与后台应用中播放的音频混合，例如音乐应用。 | function | no | iOS | no |
| setMixAudioAsync | 异步方法。如果设置此选项，你的音频将与后台应用中播放的音频混合，例如音乐应用。 | function | no | / | yes |
| setSpeakerAsync | 异步方法。将默认音频输出切换为扬声器。 | function | no | / | yes |

## 遗留问题

- 因系统侧暂未提供AVPlayer播放器生成和控制的同步方法，库依赖AVPlayer实现的同步功能暂未实现，需系统侧提供后补充。问题：[issue#6](https://github.com/react-native-oh-library/react-native-sound-player/issues/6)。
- 系统媒体播放器AVPlayer在使用playUrlAsync和loadUrlAsync接口方法播放m4a、aac、wav、flac、amr和ape等网络音频格式时异常。问题：[issue#12](https://github.com/react-native-oh-library/react-native-sound-player/issues/12)。
- RN的JS侧不支持ogg、flac、amr、ape格式，导致无法通过import或require方式导入文件，因而无法通过playAssetAsync、loadAssetAsync方式进行播放。问题：[issue#11](https://github.com/react-native-oh-library/react-native-sound-player/issues/11)。

## 其他

由于系统avplayer未提供同步方法，导致库的同步接口能力无法实现。因此被替换为同名追加Async的异步方法，具体方法如下：

- playSoundFile
- loadSoundFile
- playUrl
- loadUrl
- playAsset
- loadAsset
- play
- pause
- resume
- stop
- setSpeaker
- setMixAudio

以下接口系统没有对应能力，不支持：

- playSoundFileWithDelay

目前，系统全量支持以下音频格式的播放和控制：mp3

## 目录结构

````
/react-native-sound-player  # 项目根目录
├── harmony                       # 鸿蒙适配代码
│   ├── rn_sound_player.har       # har包
│   └── rn_sound_player           # 鸿蒙适配核心代码
│       ├── ts.ets                # TypeScript 导出入口
│       └── src/main
│           ├── cpp               # C++ 适配代码（RNSoundPlayerPackage 及 Codegen 生成代码）
│           ├── ets               # ArkTS 适配代码（RNSoundPlayerPackage、RNSoundPlayerModule、Logger）
│           ├── module.json5      # 模块配置文件
│           └── resources         # 资源文件
├── doc                           # 归档文档
│   ├── zh-cn.md                  # 中文文档（归档）
│   └── en.md                     # 英文文档（归档）
├── index.js                      # RN 侧入口文件
├── index.d.ts                    # RN 侧类型声明文件
├── NativeRNSoundPlayer.ts        # Codegen TurboModule 声明文件
├── package.json                  # 包配置文件
├── LICENSE                       # 开源协议文件
├── README.OpenSource             # 开源信息声明
├── OAT.xml                       # 开源审计配置文件
├── README.md                     # 中文安装使用方法
└── README_en.md                  # 英文安装使用方法
````

## 贡献代码

使用过程中发现任何问题都可以提交 [Issue](https://gitcode.com/CPF-RN/rntpc_react-native-sound-player/issues)，当然，也非常欢迎提交 [PR](https://gitcode.com/CPF-RN/rntpc_react-native-sound-player/pulls) 。

## 开源协议

本项目基于 [The MIT License (MIT)](https://github.com/johnsonsu/react-native-sound-player/blob/master/LICENSE) ，请自由地享受和参与开源。
