> Document Template: v0.4.2

<p align="center">
  <h1 align="center"> <code>react-native-sound-player</code> </h1>
</p>

This project is based on [react-native-sound-player@0.14.5](https://github.com/johnsonsu/react-native-sound-player/tree/0.14.5).

This third-party library has been migrated to Gitcode and is now available for direct download from npm, the new package name is: `@react-native-ohos/react-native-sound-player`, the version correspondence details are as follows:

| Name | Version(Npm Address) | Release Information | Supported RN Version | Supported Autolink | Compile API Version | Community Baseline Version | Source code address |
| --- | --- | --- | --- | --- | --- | --- | --- |
| @react-native-ohos/react-native-sound-player | [~ 0.15.0 (In development)](https://www.npmjs.com/package/@react-native-ohos/react-native-sound-player) | [Gitcode Releases](https://gitcode.com/CPF-RN/rntpc_react-native-sound-player/releases) | 0.82.* | Yes | API12+ | 0.14.5 | [br_rnoh0.82](https://gitcode.com/CPF-RN/rntpc_react-native-sound-player/tree/br_rnoh0.82) |

## Introduction

react-native-sound-player is a React Native library for playing or streaming audio files.<br/>
It supports playing local audio files, audio assets and audio from URLs, provides playback controls such as play, pause, resume, stop, seek, volume adjustment and loop count setting, and supports subscribing to events such as "Finished Playing" and "Finished Loading".

## Installation

Go to the project directory and execute the following instruction:

**npm**

```bash
npm install @react-native-ohos/react-native-sound-player
```

**yarn**

```bash
yarn add @react-native-ohos/react-native-sound-player
```

## Link

| Version | Supported Autolink | Supported RN Version |
|------|--------------------|----------------------|
| ~0.15.0 | Yes | 0.82.* |

Projects using AutoLink need to be configured according to this document, AutoLink framework guide: https://gitcode.com/CPF-RN/ohos_react_native/blob/main/docs/en/02-development/02-development-guide/autolinking.md

If the version you are using supports Autolink and the project has integrated Autolink, you can skip the ManualLink configuration.
<details>
  <summary>ManualLink: This step provides guidance for manually configuring native dependencies.</summary>

Open the `harmony` directory of the OpenHarmony project in DevEco Studio.

### 1. Overrides RN SDK

To ensure the project relies on the same version of the RN SDK, you need to add an `overrides` field in the project's root `oh-package.json5` file, specifying the RN SDK version to be used. The replacement version can be a specific version number, a semver range, or a locally available HAR package or source directory.

For more information about the purpose of this field, please refer to the [official documentation](https://developer.huawei.com/consumer/en/doc/harmonyos-guides-V5/ide-oh-package-json5-V5#en-us_topic_0000001792256137_overrides).

```json
{
  "overrides": {
    "@rnoh/react-native-openharmony": "~0.82.18" // ohpm version
    // "@rnoh/react-native-openharmony" : "./react_native_openharmony.har" // a locally available HAR package
    // "@rnoh/react-native-openharmony" : "./react_native_openharmony" // source code directory
  }
}
```

### 2. Introducing Native Code

Currently, two methods are available:

- Use the HAR file.
- Directly link to the source code.

Method 1 (recommended): Use the HAR file.

> [!TIP] The HAR file is stored in the `harmony` directory in the installation path of the third-party library.

Open `entry/oh-package.json5` file and add the following dependencies:

```json
"dependencies": {
    "@react-native-ohos/react-native-sound-player": "file:../../node_modules/@react-native-ohos/react-native-sound-player/harmony/rn_sound_player.har"
  }
```

Click the `sync` button in the upper right corner.

Alternatively, run the following instruction on the terminal:

```bash
cd entry
ohpm install
```

Method 2: Directly link to the source code.

> [!TIP] For details, see [Directly Linking Source Code](https://gitcode.com/CPF-RN/usage-docs/blob/master/en/link-source-code.md).

### 3. Configuring CMakeLists and Introducing RNSoundPlayerPackage

Open `entry/src/main/cpp/CMakeLists.txt` and add the following code:

```diff
+ set(OH_MODULES "${CMAKE_CURRENT_SOURCE_DIR}/../../../oh_modules")

# RNOH_BEGIN: manual_package_linking_1
+ add_subdirectory("${OH_MODULES}/@react-native-ohos/react-native-sound-player/src/main/cpp" ./sound-player)
# RNOH_END: manual_package_linking_1

# RNOH_BEGIN: manual_package_linking_2
+ target_link_libraries(rnoh_app PUBLIC rnoh_sound_player)
# RNOH_END: manual_package_linking_2
```

Open `entry/src/main/cpp/PackageProvider.cpp` and add the following code:

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

### 4. Introducing RNSoundPlayerPackage to ArkTS

Open `entry/src/main/ets/RNPackagesFactory.ts` and add the following code:

```diff
  ...
+ import {RNSoundPlayerPackage} from '@react-native-ohos/react-native-sound-player/ts';

export function createRNPackages(ctx: RNPackageContext): RNPackage[] {
  return [
+   new RNSoundPlayerPackage(ctx)
  ];
}
```
</details>

### Running

Click the `sync` button in the upper right corner.

Alternatively, run the following instruction on the terminal:

```bash
cd entry
ohpm install
```

Then build and run the code.

## Constraints

### Compatibility

Please go to the Releases address of the third-party library to view the supporting version information: [@react-native-ohos/react-native-sound-player Releases](https://gitcode.com/CPF-RN/rntpc_react-native-sound-player/releases).

### Permission Requirements

No special permission is required.

## Example

The following code shows the basic use scenario of the repository:

> [!WARNING] The name of the imported repository remains unchanged.

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

## How to Use

**Playing a local audio file**

```javascript
// Play the audio file named tone with type mp3
await SoundPlayer.playSoundFileAsync('tone', 'mp3');
```

**Playing audio from a URL**

```javascript
await SoundPlayer.playUrlAsync('https://example.com/audio/tone.mp3');
```

**Playing an audio asset**

```javascript
await SoundPlayer.playAssetAsync(require('./assets/tone.mp3'));
```

**Playback control**

```javascript
await SoundPlayer.pauseAsync();    // Pause
await SoundPlayer.resumeAsync();   // Resume
await SoundPlayer.stopAsync();     // Stop
SoundPlayer.seek(10);              // Seek to the 10th second
SoundPlayer.setVolume(1);          // Set the volume (does not change the device volume)
SoundPlayer.setNumberOfLoops(0);   // 0 means play the sound once, other numbers mean an indefinite loop
```

**Event listener**

```javascript
const subscription = SoundPlayer.addEventListener(
  'FinishedPlaying',
  ({ success }) => {
    console.log('finished playing', success);
  }
);

// Remove the listener when the component unmounts or it is no longer needed
subscription.remove();
```

## Available APIs

> [!TIP] "Platform" column indicates the platform where the properties are supported in the original third-party library.

> [!TIP] If the value of **OpenHarmony Support** is **yes**, it means that the OpenHarmony platform supports this property; **no** means the opposite; **partially** means some capabilities of this property are supported. The usage method is the same on different platforms and the effect is the same as that of iOS or Android.

| Name | Description | Type | Required | Platform | OpenHarmony Platform Support |
| ---- | ----------- | ---- | -------- | -------- | ---------------------------- |
| playSoundFileAsync | Asynchronous Play the sound file named `fileName` async with file type `fileType`. | function | no | / | yes |
| loadSoundFileAsync | Asynchronous Load the sound file named `fileName` with file type `fileType`, without playing it. | function | no | / | yes |
| playUrlAsync | Asynchronous Play the audio from url. | function | no | / | yes |
| loadUrlAsync | Asynchronous Load the audio from the given `url` without playing it. | function | no | / | yes |
| playAssetAsync | Asynchronous Play the audio from an asset, to get the asset number use `require('./assets/tone.mp3')`. | function | no | / | yes |
| loadAssetAsync | Asynchronous Load the audio from an asset like above but without playing it. | function | no | / | yes |
| playAsync | Asynchronous Play the loaded sound file. This function is the same as `resumeAsync`. | function | no | / | yes |
| pauseAsync | Asynchronous Pause the currently playing file. | function | no | / | yes |
| resumeAsync | Asynchronous Resume from pause and continue playing the same file. This function is the same as `playAsync`. | function | no | / | yes |
| stopAsync | Asynchronous Stop playing. | function | no | / | yes |
| playSoundFile | Play the sound file named `fileName` with file type `fileType`. | function | no | Android, iOS | no |
| loadSoundFile | Load the sound file named `fileName` with file type `fileType`, without playing it. | function | no | Android, iOS | no |
| playUrl | Play the audio from url. | function | no | Android, iOS | no |
| loadUrl | Load the audio from the given `url` without playing it. | function | no | Android, iOS | no |
| playAsset | Play the audio from an asset, to get the asset number use `require('./assets/tone.mp3')`. | function | no | Android, iOS | no |
| loadAsset | Load the audio from an asset like above but without playing it. | function | no | Android, iOS | no |
| onFinishedPlaying | Subscribe to the "finished playing" event. The `callback` function is called whenever a file is finished playing. This function will be deprecated soon, please use `addEventListener`. | function | no | All | yes |
| onFinishedLoading | Subscribe to the "finished loading" event. The `callback` function is called whenever a file is finished loading, i.e. the file is ready to be `play()`, `resume()`, `getInfo()`, etc. This function will be deprecated soon, please use `addEventListener`. | function | no | All | yes |
| addEventListener | Subscribe to any event. Returns a subscription object. Subscriptions created by this function cannot be removed by calling unmount(). You NEED to call yourSubscriptionObject.remove() when you no longer need this event listener or whenever your component unmounts. | function | no | All | yes |
| play | Play the loaded sound file. This function is the same as `resume`. | function | no | Android, iOS | no |
| pause | Pause the currently playing file. | function | no | Android, iOS | no |
| resume | Resume from pause and continue playing the same file. This function is the same as `play`. | function | no | Android, iOS | no |
| stop | Stop playing. | function | no | Android, iOS | no |
| seek | Seek to seconds of the currently playing file. | function | no | All | yes |
| setVolume | Set the volume of the current player. This does not change the volume of the device. | function | no | All | yes |
| setNumberOfLoops | 0 means to play the sound once, other numbers indicate an indefinite loop. | function | no | All | yes |
| getInfo | Get the currentTime and duration of the currently mounted audio media. This function returns a promise which resolves to an Object containing currentTime and duration properties. | function | no | All | yes |
| unmount | Unsubscribe the "finished playing" and "finished loading" events. This function will be deprecated soon, please use `addEventListener` and remove your own listener by calling `yourSubscriptionObject.remove()`. | function | no | All | yes |
| playSoundFileWithDelay | Only available on iOS. Play the sound file named `fileName` with file type `fileType` after a delay of `delay` in seconds from the current device time. | function | no | iOS | no |
| setSpeaker | Overwrite default audio output to speaker. | function | no | iOS | no |
| setMixAudio | If you set this option, your audio will be mixed with audio playing in background apps, such as the Music app. | function | no | iOS | no |
| setMixAudioAsync | Asynchronous method. If you set this option, your audio will be mixed with audio playing in background apps, such as the Music app. | function | no | / | yes |
| setSpeakerAsync | Asynchronous method. Overwrite default audio output to speaker. | function | no | / | yes |

## Known Issues

- Since the system side has not yet provided synchronization methods for generating and controlling the AVPlayer, the synchronization functions implemented by the library, which relies on AVPlayer, remain unimplemented and will be supplemented once the system side provides the necessary methods. Issue: [issue#6](https://github.com/react-native-oh-library/react-native-sound-player/issues/6).
- The system media player AVPlayer encounters exceptions when playing network audio formats such as m4a, aac, wav, flac, amr, and ape using the playUrlAsync and loadUrlAsync interface methods. Issue: [issue#12](https://github.com/react-native-oh-library/react-native-sound-player/issues/12).
- The JS side of RN does not support ogg, flac, amr, and ape formats, which makes it impossible to import files through import or require, and therefore they cannot be played through playAssetAsync or loadAssetAsync. Issue: [issue#11](https://github.com/react-native-oh-library/react-native-sound-player/issues/11).

## Other

Since the system's avplayer does not provide a synchronization method, the library's synchronization interface capabilities cannot be implemented. Therefore, they have been replaced with asynchronous methods with the same name appended with `Async`. The specific methods are as follows:

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

The following interface has no corresponding system capability and is not supported:

- playSoundFileWithDelay

At present, the system fully supports the playback and control of the following audio format: mp3

## Directory Structure

````
/react-native-sound-player  # Project root directory
├── harmony                       # HarmonyOS adaptation code
│   ├── rn_sound_player.har       # har package
│   └── rn_sound_player           # Core HarmonyOS adaptation code
│       ├── index.ets             # Entry file of the HarmonyOS adaptation code
│       ├── ts.ets                # TypeScript export entry
│       └── src/main
│           ├── cpp               # C++ adaptation code (RNSoundPlayerPackage and Codegen-generated code)
│           ├── ets               # ArkTS adaptation code (RNSoundPlayerPackage, RNSoundPlayerModule, Logger)
│           ├── module.json5      # Module configuration file
│           └── resources         # Resource files
├── index.js                      # Entry file of the RN side
├── index.d.ts                    # Type declaration file of the RN side
├── NativeRNSoundPlayer.ts        # Codegen TurboModule declaration file
├── package.json                  # Package configuration file
├── LICENSE                       # License file
├── README.OpenSource             # Open-source information statement
├── OAT.xml                       # Open-source audit tool configuration file
├── README.md                     # Chinese installation and usage instructions
└── README_en.md                  # English installation and usage instructions
````

## How to Contribute

If you find any problem when using react-native-sound-player, submit an [Issue](https://gitcode.com/CPF-RN/rntpc_react-native-sound-player/issues) or a [PR](https://gitcode.com/CPF-RN/rntpc_react-native-sound-player/pulls).

## License

This project is based on [The MIT License (MIT)](https://github.com/johnsonsu/react-native-sound-player/blob/master/LICENSE). Feel free to enjoy and participate in open source.
