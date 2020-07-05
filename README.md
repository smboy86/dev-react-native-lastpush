# 리액트 네이티브 푸시 알람 라이브러리 스터디

## react-native-push-notification 사용방법

파이어베이스 생성 및 연동

Android, Ios 플랫폼 모두에서 작동하는

완벽한 가이드 - 2020.07.05 Android & IOS 기준 정상 작동 확인(Local, remote)

---


# react-native-push-notification 설치 및 환경 설정 방법

> 공식 라이브러리 주소
[https://github.com/zo0r/react-native-push-notification](https://github.com/zo0r/react-native-push-notification)

> 설치 후 Test sample code 참고
[https://github.com/zo0r/react-native-push-notification/tree/master/example](https://github.com/zo0r/react-native-push-notification/tree/master/example)

> 자세한 코드 및 설치 과정은 github 주소 참고
[https://github.com/smboy86/dev-react-native-lastpush](https://github.com/smboy86/dev-react-native-lastpush)
[https://github.com/smboy86/dev-react-native-lastpush/commits/master](https://github.com/smboy86/dev-react-native-lastpush/commits/master)

> 시작전 알아야할 내용
1. FCM에 대한 개념
2. IOS 테스트를 위해서는 개발자 계정 등록이 필요함
3. IOS는 리얼디바이스에서만 테스트 가능 (테스트용기기일시 인터넷 사용이 가능 필수)

# 1. React-native 프로젝트 생성 및 설정

## 1.1 프로젝트 생성

```jsx
$ react-native init lastpush (원하는 이름으로)
$ cd lastpush
$ code . (코드 편집기 열어두기 vscode)

// (vscode 편집기 내에서 터미널 열기 - 편의성 up)
$ yarn start
$ yarn android or ( yarn ios ) 

// 실행 확인 - 이상없이 실행이 되어야 함
```

## 1.2 프로젝트 번들 아이디 설정

example) com.devpoi.last.push

[2-1) RN 패키지 명 바꾸기 pakage rename (IOS, Android)](메뉴얼 작성중) 

# 2. 파이어베이스

## 2.1 파이어베이스 프로젝트 생성 (기존 플젝 사용가능)

## 2.2 파이어베이스 프로젝트내 App 생성 (IOS, Android)

### 2.2.1 공통

1. 번들 아이디 설정 (프로젝트와 동일해야함) - ex) com.devpoi.last.push
2. 파이어베이스 구성파일 다운로드 (google-service.json / GoogleService-info.plist.plist)

### 2.2.2 Android

1. 다운받은 구성파일 프로젝트로 단순 복사
    - ~android/app/google-service.json  (폴더 경로 중요)

### 2.2.3 IOS

1. 프로젝트 파일  Add 를 통한 파일 복사

    ![./readme.img/_2020-07-03__1.11.42.png](./readme.img/_2020-07-03__1.11.42.png)

2. 프로젝트 Capabilities 추가
    - Background Modes 추가 >> 2 check
    - Push Notifications 추가

    ![./readme.img/_2020-07-05__8.04.14.png](./readme.img/_2020-07-05__8.04.14.png)

### 2.2.4 공통

1. 스크린샷과 같은 상태로 대기 or 무시하고 이 단계 건너뛰기 해도 프로젝트 구성에는 문제가 없음

    ![./readme.img/_2020-07-05__4.44.11.png](./readme.img/_2020-07-05__4.44.11.png)

### 2.2.5 IOS - 프로젝트 생성 이후 APNs 키 등록 (.p8 파일)

1. 애플 개발자 센터에서 Key 생성
    - [https://developer.apple.com/account/resources/authkeys/list](https://developer.apple.com/account/resources/authkeys/list)
2. 생성한 key 다운로드 (1회만 가능 잘 간직하자)
3. 해당파일 파이어베이스에 등록 (생성한 IOS 앱 설정 > 클라우드 메시징 > APN 인증 키)
    - .p8 파일 업로드
    - Key 이름 입력
    - Apple Team ID 입력

# 3. 라이브러리 설치

## 3.1 공통

### 3.1.1 라이브러리 설치

```bash
// 위 라이브러리는 설치하지 않고 설치 가이드만 따라했다. (확인중)
~~~~$ yarn add react-native-push-notification

// for IOS
$ yarn add @react-native-community/push-notification-ios
```

### 3.1.2 소스 수정

- ~App.js (수정 or 전체 대치)

    ```bash
    /**
     * Sample React Native App
     * https://github.com/facebook/react-native
     *
     * @format
     * @flow
     */

    import React, {Component} from 'react';
    import {
      TextInput,
      StyleSheet,
      Text,
      View,
      TouchableOpacity,
      Alert,
    } from 'react-native';
    import NotifService from './NotifService';

    export default class App extends Component {
      constructor(props) {
        super(props);
        this.state = {};

        this.notif = new NotifService(
          this.onRegister.bind(this),
          this.onNotif.bind(this),
        );
      }

      render() {
        return (
          <View style={styles.container}>
            <Text style={styles.title}>
              Example app react-native-push-notification
            </Text>
            <View style={styles.spacer}></View>
            <TextInput
              style={styles.textField}
              value={this.state.registerToken}
              placeholder="Register token"
            />
            <View style={styles.spacer}></View>

            <TouchableOpacity
              style={styles.button}
              onPress={() => {
                this.notif.localNotif();
              }}>
              <Text>Local Notification (now)</Text>
            </TouchableOpacity>
            <TouchableOpacity
              style={styles.button}
              onPress={() => {
                // this.notif.localNotif('sample.mp3');
                this.notif.localNotif();
              }}>
              <Text>Local Notification with sound (now)</Text>
            </TouchableOpacity>
            <TouchableOpacity
              style={styles.button}
              onPress={() => {
                this.notif.scheduleNotif();
              }}>
              <Text>Schedule Notification in 30s</Text>
            </TouchableOpacity>
            <TouchableOpacity
              style={styles.button}
              onPress={() => {
                // this.notif.scheduleNotif('sample.mp3');
                this.notif.scheduleNotif();
              }}>
              <Text>Schedule Notification with sound in 30s</Text>
            </TouchableOpacity>
            <TouchableOpacity
              style={styles.button}
              onPress={() => {
                this.notif.cancelNotif();
              }}>
              <Text>Cancel last notification (if any)</Text>
            </TouchableOpacity>
            <TouchableOpacity
              style={styles.button}
              onPress={() => {
                this.notif.cancelAll();
              }}>
              <Text>Cancel all notifications</Text>
            </TouchableOpacity>
            <TouchableOpacity
              style={styles.button}
              onPress={() => {
                this.notif.checkPermission(this.handlePerm.bind(this));
              }}>
              <Text>Check Permission</Text>
            </TouchableOpacity>
            <TouchableOpacity
              style={styles.button}
              onPress={() => {
                this.notif.requestPermissions();
              }}>
              <Text>Request Permissions</Text>
            </TouchableOpacity>
            <TouchableOpacity
              style={styles.button}
              onPress={() => {
                this.notif.abandonPermissions();
              }}>
              <Text>Abandon Permissions</Text>
            </TouchableOpacity>

            <View style={styles.spacer}></View>

            {this.state.fcmRegistered && <Text>FCM Configured !</Text>}

            <View style={styles.spacer}></View>
          </View>
        );
      }

      onRegister(token) {
        this.setState({registerToken: token.token, fcmRegistered: true});
      }

      onNotif(notif) {
        Alert.alert(JSON.stringify(notif.title), JSON.stringify(notif.message));
      }

      handlePerm(perms) {
        Alert.alert('Permissions', JSON.stringify(perms));
      }
    }

    const styles = StyleSheet.create({
      container: {
        flex: 1,
        justifyContent: 'center',
        alignItems: 'center',
        backgroundColor: '#F5FCFF',
      },
      welcome: {
        fontSize: 20,
        textAlign: 'center',
        margin: 10,
      },
      button: {
        borderWidth: 1,
        borderColor: '#000000',
        margin: 5,
        padding: 5,
        width: '70%',
        backgroundColor: '#DDDDDD',
        borderRadius: 5,
      },
      textField: {
        borderWidth: 1,
        borderColor: '#AAAAAA',
        color: 'red',
        margin: 5,
        padding: 5,
        width: '70%',
      },
      spacer: {
        height: 10,
      },
      title: {
        fontWeight: 'bold',
        fontSize: 20,
        textAlign: 'center',
      },
    });
    ```

- ~NotifService.js (신규)

    ```bash
    import PushNotification from 'react-native-push-notification';
    import NotificationHandler from './NotificationHandler';

    export default class NotifService {
      constructor(onRegister, onNotification) {
        this.lastId = 0;

        NotificationHandler.attachRegister(onRegister);
        NotificationHandler.attachNotification(onNotification);

        // Clear badge number at start
        PushNotification.getApplicationIconBadgeNumber(function (number) {
          if (number > 0) {
            PushNotification.setApplicationIconBadgeNumber(0);
          }
        });
      }

      localNotif(soundName) {
        this.lastId++;
        PushNotification.localNotification({
          /* Android Only Properties */
          id: this.lastId, // (optional) Valid unique 32 bit integer specified as string. default: Autogenerated Unique ID
          ticker: 'My Notification Ticker', // (optional)
          autoCancel: true, // (optional) default: true
          largeIcon: 'ic_launcher', // (optional) default: "ic_launcher"
          smallIcon: 'ic_notification', // (optional) default: "ic_notification" with fallback for "ic_launcher"
          bigText: 'My big text that will be shown when notification is expanded', // (optional) default: "message" prop
          subText: 'This is a subText', // (optional) default: none
          color: 'red', // (optional) default: system default
          vibrate: true, // (optional) default: true
          vibration: 300, // vibration length in milliseconds, ignored if vibrate=false, default: 1000
          tag: 'some_tag', // (optional) add tag to message
          group: 'group', // (optional) add group to message
          ongoing: false, // (optional) set whether this is an "ongoing" notification

          /* iOS only properties */
          alertAction: 'view', // (optional) default: view
          category: '', // (optional) default: empty string
          userInfo: {}, // (optional) default: {} (using null throws a JSON value '<null>' error)

          /* iOS and Android properties */
          title: 'Local Notification', // (optional)
          message: 'My Notification Message', // (required)
          playSound: !!soundName, // (optional) default: true
          soundName: soundName ? soundName : 'default', // (optional) Sound to play when the notification is shown. Value of 'default' plays the default sound. It can be set to a custom sound such as 'android.resource://com.xyz/raw/my_sound'. It will look for the 'my_sound' audio file in 'res/raw' directory and play it. default: 'default' (default sound is played)
          number: 10, // (optional) Valid 32 bit integer specified as string. default: none (Cannot be zero)
          actions: '["Yes", "No"]', // (Android only) See the doc for notification actions to know more
        });
      }

      scheduleNotif(soundName) {
        this.lastId++;
        PushNotification.localNotificationSchedule({
          date: new Date(Date.now() + 30 * 1000), // in 30 secs

          /* Android Only Properties */
          id: this.lastId, // (optional) Valid unique 32 bit integer specified as string. default: Autogenerated Unique ID
          ticker: 'My Notification Ticker', // (optional)
          autoCancel: true, // (optional) default: true
          largeIcon: 'ic_launcher', // (optional) default: "ic_launcher"
          smallIcon: 'ic_notification', // (optional) default: "ic_notification" with fallback for "ic_launcher"
          bigText: 'My big text that will be shown when notification is expanded', // (optional) default: "message" prop
          subText: 'This is a subText', // (optional) default: none
          color: 'blue', // (optional) default: system default
          vibrate: true, // (optional) default: true
          vibration: 300, // vibration length in milliseconds, ignored if vibrate=false, default: 1000
          tag: 'some_tag', // (optional) add tag to message
          group: 'group', // (optional) add group to message
          ongoing: false, // (optional) set whether this is an "ongoing" notification

          /* iOS only properties */
          alertAction: 'view', // (optional) default: view
          category: '', // (optional) default: empty string
          userInfo: {}, // (optional) default: {} (using null throws a JSON value '<null>' error)

          /* iOS and Android properties */
          title: 'Scheduled Notification', // (optional)
          message: 'My Notification Message', // (required)
          playSound: !!soundName, // (optional) default: true
          number: 10, // (optional) Valid 32 bit integer specified as string. default: none (Cannot be zero)
          soundName: soundName ? soundName : 'default', // (optional) Sound to play when the notification is shown. Value of 'default' plays the default sound. It can be set to a custom sound such as 'android.resource://com.xyz/raw/my_sound'. It will look for the 'my_sound' audio file in 'res/raw' directory and play it. default: 'default' (default sound is played)
        });
      }

      checkPermission(cbk) {
        return PushNotification.checkPermissions(cbk);
      }

      requestPermissions() {
        return PushNotification.requestPermissions();
      }

      cancelNotif() {
        PushNotification.cancelLocalNotifications({id: '' + this.lastId});
      }

      cancelAll() {
        PushNotification.cancelAllLocalNotifications();
      }

      abandonPermissions() {
        PushNotification.abandonPermissions();
      }
    }
    ```

- ~NotificationsHandler.js (신규)

    ```bash
    import PushNotification from 'react-native-push-notification';

    class NotificationHandler {
      onNotification(notification) {
        console.log('NotificationHandler:', notification);

        if (typeof this._onNotification === 'function') {
          this._onNotification(notification);
        }
      }

      onRegister(token) {
        console.log('NotificationHandler:', token);

        if (typeof this._onRegister === 'function') {
          this._onRegister(token);
        }
      }

      attachRegister(handler) {
        this._onRegister = handler;
      }

      attachNotification(handler) {
        this._onNotification = handler;
      }
    }

    const handler = new NotificationHandler();

    PushNotification.configure({
      // (optional) Called when Token is generated (iOS and Android)
      onRegister: handler.onRegister.bind(handler),

      // (required) Called when a remote or local notification is opened or received
      onNotification: handler.onNotification.bind(handler),

      // IOS ONLY (optional): default: all - Permissions to register.
      permissions: {
        alert: true,
        badge: true,
        sound: true,
      },

      // Should the initial notification be popped automatically
      // default: true
      popInitialNotification: true,

      /**
       * (optional) default: true
       * - Specified if permissions (ios) and token (android and ios) will requested or not,
       * - if not, you must call PushNotificationsHandler.requestPermissions() later
       */
      requestPermissions: true,
    });

    export default handler;
    ```

## 3.2 IOS

- Podfile (수정 - 1 add)

    ```bash
    platform :ios, '9.0'
    require_relative '../node_modules/@react-native-community/cli-platform-ios/native_modules'

    def add_flipper_pods!(versions = {})
      versions['Flipper'] ||= '~> 0.33.1'
      versions['DoubleConversion'] ||= '1.1.7'
      versions['Flipper-Folly'] ||= '~> 2.1'
      versions['Flipper-Glog'] ||= '0.3.6'
      versions['Flipper-PeerTalk'] ||= '~> 0.0.4'
      versions['Flipper-RSocket'] ||= '~> 1.0'

      pod 'FlipperKit', versions['Flipper'], :configuration => 'Debug'
      pod 'FlipperKit/FlipperKitLayoutPlugin', versions['Flipper'], :configuration => 'Debug'
      pod 'FlipperKit/SKIOSNetworkPlugin', versions['Flipper'], :configuration => 'Debug'
      pod 'FlipperKit/FlipperKitUserDefaultsPlugin', versions['Flipper'], :configuration => 'Debug'
      pod 'FlipperKit/FlipperKitReactPlugin', versions['Flipper'], :configuration => 'Debug'

      # List all transitive dependencies for FlipperKit pods
      # to avoid them being linked in Release builds
      pod 'Flipper', versions['Flipper'], :configuration => 'Debug'
      pod 'Flipper-DoubleConversion', versions['DoubleConversion'], :configuration => 'Debug'
      pod 'Flipper-Folly', versions['Flipper-Folly'], :configuration => 'Debug'
      pod 'Flipper-Glog', versions['Flipper-Glog'], :configuration => 'Debug'
      pod 'Flipper-PeerTalk', versions['Flipper-PeerTalk'], :configuration => 'Debug'
      pod 'Flipper-RSocket', versions['Flipper-RSocket'], :configuration => 'Debug'
      pod 'FlipperKit/Core', versions['Flipper'], :configuration => 'Debug'
      pod 'FlipperKit/CppBridge', versions['Flipper'], :configuration => 'Debug'
      pod 'FlipperKit/FBCxxFollyDynamicConvert', versions['Flipper'], :configuration => 'Debug'
      pod 'FlipperKit/FBDefines', versions['Flipper'], :configuration => 'Debug'
      pod 'FlipperKit/FKPortForwarding', versions['Flipper'], :configuration => 'Debug'
      pod 'FlipperKit/FlipperKitHighlightOverlay', versions['Flipper'], :configuration => 'Debug'
      pod 'FlipperKit/FlipperKitLayoutTextSearchable', versions['Flipper'], :configuration => 'Debug'
      pod 'FlipperKit/FlipperKitNetworkPlugin', versions['Flipper'], :configuration => 'Debug'
    end

    # Post Install processing for Flipper
    def flipper_post_install(installer)
      installer.pods_project.targets.each do |target|
        if target.name == 'YogaKit'
          target.build_configurations.each do |config|
            config.build_settings['SWIFT_VERSION'] = '4.1'
          end
        end
      end
    end

    target 'rnpush' do
      # Pods for rnpush
      pod 'FBLazyVector', :path => "../node_modules/react-native/Libraries/FBLazyVector"
      pod 'FBReactNativeSpec', :path => "../node_modules/react-native/Libraries/FBReactNativeSpec"
      pod 'RCTRequired', :path => "../node_modules/react-native/Libraries/RCTRequired"
      pod 'RCTTypeSafety', :path => "../node_modules/react-native/Libraries/TypeSafety"
      pod 'React', :path => '../node_modules/react-native/'
      pod 'React-Core', :path => '../node_modules/react-native/'
      pod 'React-CoreModules', :path => '../node_modules/react-native/React/CoreModules'
      pod 'React-Core/DevSupport', :path => '../node_modules/react-native/'
      pod 'React-RCTActionSheet', :path => '../node_modules/react-native/Libraries/ActionSheetIOS'
      pod 'React-RCTAnimation', :path => '../node_modules/react-native/Libraries/NativeAnimation'
      pod 'React-RCTBlob', :path => '../node_modules/react-native/Libraries/Blob'
      pod 'React-RCTImage', :path => '../node_modules/react-native/Libraries/Image'
      pod 'React-RCTLinking', :path => '../node_modules/react-native/Libraries/LinkingIOS'
      pod 'React-RCTNetwork', :path => '../node_modules/react-native/Libraries/Network'
      pod 'React-RCTSettings', :path => '../node_modules/react-native/Libraries/Settings'
      pod 'React-RCTText', :path => '../node_modules/react-native/Libraries/Text'
      pod 'React-RCTVibration', :path => '../node_modules/react-native/Libraries/Vibration'
      pod 'React-Core/RCTWebSocket', :path => '../node_modules/react-native/'

      pod 'React-cxxreact', :path => '../node_modules/react-native/ReactCommon/cxxreact'
      pod 'React-jsi', :path => '../node_modules/react-native/ReactCommon/jsi'
      pod 'React-jsiexecutor', :path => '../node_modules/react-native/ReactCommon/jsiexecutor'
      pod 'React-jsinspector', :path => '../node_modules/react-native/ReactCommon/jsinspector'
      pod 'ReactCommon/callinvoker', :path => "../node_modules/react-native/ReactCommon"
      pod 'ReactCommon/turbomodule/core', :path => "../node_modules/react-native/ReactCommon"
      pod 'Yoga', :path => '../node_modules/react-native/ReactCommon/yoga', :modular_headers => true

      pod 'DoubleConversion', :podspec => '../node_modules/react-native/third-party-podspecs/DoubleConversion.podspec'
      pod 'glog', :podspec => '../node_modules/react-native/third-party-podspecs/glog.podspec'
      pod 'Folly', :podspec => '../node_modules/react-native/third-party-podspecs/Folly.podspec'

      pod 'RNCPushNotificationIOS', :path => '../node_modules/@react-native-community/push-notification-ios' << 1) add

      target 'rnpushTests' do
        inherit! :complete
        # Pods for testing
      end

      use_native_modules!

      # Enables Flipper.
      #
      # Note that if you have use_frameworks! enabled, Flipper will not work and
      # you should disable these next few lines.
      add_flipper_pods!
      post_install do |installer|
        flipper_post_install(installer)
      end
    end

    target 'rnpush-tvOS' do
      # Pods for rnpush-tvOS

      target 'rnpush-tvOSTests' do
        inherit! :search_paths
        # Pods for testing
      end
    end
    ```

- AppDelegate.h (수정 - 2 add)

    ```bash
    #import <React/RCTBridgeDelegate.h>
    #import <UIKit/UIKit.h>
    #import <UserNotifications/UNUserNotificationCenter.h>  << add

    @interface AppDelegate : UIResponder <UIApplicationDelegate, RCTBridgeDelegate, UNUserNotificationCenterDelegate  << add>

    @property (nonatomic, strong) UIWindow *window;

    @end
    ```

- AppDelegate.m (수정 - 4 add)

    ```bash
    #import "AppDelegate.h"

    #import <React/RCTBridge.h>
    #import <React/RCTBundleURLProvider.h>
    #import <React/RCTRootView.h>
    #import <UserNotifications/UserNotifications.h> << add
    #import <RNCPushNotificationIOS.h> << add

    #if DEBUG
    #import <FlipperKit/FlipperClient.h>
    #import <FlipperKitLayoutPlugin/FlipperKitLayoutPlugin.h>
    #import <FlipperKitUserDefaultsPlugin/FKUserDefaultsPlugin.h>
    #import <FlipperKitNetworkPlugin/FlipperKitNetworkPlugin.h>
    #import <SKIOSNetworkPlugin/SKIOSNetworkAdapter.h>
    #import <FlipperKitReactPlugin/FlipperKitReactPlugin.h>

    static void InitializeFlipper(UIApplication *application) {
      FlipperClient *client = [FlipperClient sharedClient];
      SKDescriptorMapper *layoutDescriptorMapper = [[SKDescriptorMapper alloc] initWithDefaults];
      [client addPlugin:[[FlipperKitLayoutPlugin alloc] initWithRootNode:application withDescriptorMapper:layoutDescriptorMapper]];
      [client addPlugin:[[FKUserDefaultsPlugin alloc] initWithSuiteName:nil]];
      [client addPlugin:[FlipperKitReactPlugin new]];
      [client addPlugin:[[FlipperKitNetworkPlugin alloc] initWithNetworkAdapter:[SKIOSNetworkAdapter new]]];
      [client start];
    }
    #endif

    @implementation AppDelegate

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
    #if DEBUG
      InitializeFlipper(application);
    #endif

      RCTBridge *bridge = [[RCTBridge alloc] initWithDelegate:self launchOptions:launchOptions];
      RCTRootView *rootView = [[RCTRootView alloc] initWithBridge:bridge
                                                       moduleName:@"rnpush"
                                                initialProperties:nil];

      UNUserNotificationCenter *center = [UNUserNotificationCenter currentNotificationCenter];
      center.delegate = self; << add

      rootView.backgroundColor = [[UIColor alloc] initWithRed:1.0f green:1.0f blue:1.0f alpha:1];

      self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];
      UIViewController *rootViewController = [UIViewController new];
      rootViewController.view = rootView;
      self.window.rootViewController = rootViewController;
      [self.window makeKeyAndVisible];
      return YES;
    }

    // add
    // Required to register for notifications
    - (void)application:(UIApplication *)application didRegisterUserNotificationSettings:(UIUserNotificationSettings *)notificationSettings
    {
     [RNCPushNotificationIOS didRegisterUserNotificationSettings:notificationSettings];
    }
    // Required for the register event.
    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
     [RNCPushNotificationIOS didRegisterForRemoteNotificationsWithDeviceToken:deviceToken];
    }
    // Required for the notification event. You must call the completion handler after handling the remote notification.
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
    fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
      [RNCPushNotificationIOS didReceiveRemoteNotification:userInfo fetchCompletionHandler:completionHandler];
    }
    // Required for the registrationError event.
    - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error
    {
     [RNCPushNotificationIOS didFailToRegisterForRemoteNotificationsWithError:error];
    }
    // Required for the localNotification event.
    - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification
    {
     [RNCPushNotificationIOS didReceiveLocalNotification:notification];
    }

    //Called when a notification is delivered to a foreground app.
    -(void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      completionHandler(UNAuthorizationOptionSound | UNAuthorizationOptionAlert | UNAuthorizationOptionBadge);
    }

    - (NSURL *)sourceURLForBridge:(RCTBridge *)bridge
    {
    #if DEBUG
      return [[RCTBundleURLProvider sharedSettings] jsBundleURLForBundleRoot:@"index" fallbackResource:nil];
    #else
      return [[NSBundle mainBundle] URLForResource:@"main" withExtension:@"jsbundle"];
    #endif
    }

    @end
    ```

소스 모두 수정 후 팟 인스톨 후 기기 시작

```bash
$ cd ios
$ pod install & cd ..
$ yarn start & yarn ios 
```

## 3.3 Android

- ~android/build.gradle  (수정 - 1 add)

    ```bash
    // Top-level build file where you can add configuration options common to all sub-projects/modules.

    buildscript {
        ext {
            buildToolsVersion = "28.0.3"
            minSdkVersion = 16
            compileSdkVersion = 28
            targetSdkVersion = 28
        }
        repositories {
            google()
            jcenter()
        }
        dependencies {
            classpath("com.android.tools.build:gradle:3.5.2")

            classpath('com.google.gms:google-services:4.3.3') << add

            // NOTE: Do not place your application dependencies here; they belong
            // in the individual module build.gradle files
        }
    }

    allprojects {
        repositories {
            mavenLocal()
            maven {
                // All of React Native (JS, Obj-C sources, Android binaries) is installed from npm
                url("$rootDir/../node_modules/react-native/android")
            }
            maven {
                // Android JSC is installed from npm
                url("$rootDir/../node_modules/jsc-android/dist")
            }

            google()
            jcenter()
            maven { url 'https://www.jitpack.io' }
        }
    }
    ```

- ~android/app/build.gradle (수정 - 1 add)

    ```bash
    apply plugin: "com.android.application"

    import com.android.build.OutputFile

    /**
     * The react.gradle file registers a task for each build variant (e.g. bundleDebugJsAndAssets
     * and bundleReleaseJsAndAssets).
     * These basically call `react-native bundle` with the correct arguments during the Android build
     * cycle. By default, bundleDebugJsAndAssets is skipped, as in debug/dev mode we prefer to load the
     * bundle directly from the development server. Below you can see all the possible configurations
     * and their defaults. If you decide to add a configuration block, make sure to add it before the
     * `apply from: "../../node_modules/react-native/react.gradle"` line.
     *
     * project.ext.react = [
     *   // the name of the generated asset file containing your JS bundle
     *   bundleAssetName: "index.android.bundle",
     *
     *   // the entry file for bundle generation. If none specified and
     *   // "index.android.js" exists, it will be used. Otherwise "index.js" is
     *   // default. Can be overridden with ENTRY_FILE environment variable.
     *   entryFile: "index.android.js",
     *
     *   // https://facebook.github.io/react-native/docs/performance#enable-the-ram-format
     *   bundleCommand: "ram-bundle",
     *
     *   // whether to bundle JS and assets in debug mode
     *   bundleInDebug: false,
     *
     *   // whether to bundle JS and assets in release mode
     *   bundleInRelease: true,
     *
     *   // whether to bundle JS and assets in another build variant (if configured).
     *   // See http://tools.android.com/tech-docs/new-build-system/user-guide#TOC-Build-Variants
     *   // The configuration property can be in the following formats
     *   //         'bundleIn${productFlavor}${buildType}'
     *   //         'bundleIn${buildType}'
     *   // bundleInFreeDebug: true,
     *   // bundleInPaidRelease: true,
     *   // bundleInBeta: true,
     *
     *   // whether to disable dev mode in custom build variants (by default only disabled in release)
     *   // for example: to disable dev mode in the staging build type (if configured)
     *   devDisabledInStaging: true,
     *   // The configuration property can be in the following formats
     *   //         'devDisabledIn${productFlavor}${buildType}'
     *   //         'devDisabledIn${buildType}'
     *
     *   // the root of your project, i.e. where "package.json" lives
     *   root: "../../",
     *
     *   // where to put the JS bundle asset in debug mode
     *   jsBundleDirDebug: "$buildDir/intermediates/assets/debug",
     *
     *   // where to put the JS bundle asset in release mode
     *   jsBundleDirRelease: "$buildDir/intermediates/assets/release",
     *
     *   // where to put drawable resources / React Native assets, e.g. the ones you use via
     *   // require('./image.png')), in debug mode
     *   resourcesDirDebug: "$buildDir/intermediates/res/merged/debug",
     *
     *   // where to put drawable resources / React Native assets, e.g. the ones you use via
     *   // require('./image.png')), in release mode
     *   resourcesDirRelease: "$buildDir/intermediates/res/merged/release",
     *
     *   // by default the gradle tasks are skipped if none of the JS files or assets change; this means
     *   // that we don't look at files in android/ or ios/ to determine whether the tasks are up to
     *   // date; if you have any other folders that you want to ignore for performance reasons (gradle
     *   // indexes the entire tree), add them here. Alternatively, if you have JS files in android/
     *   // for example, you might want to remove it from here.
     *   inputExcludes: ["android/**", "ios/**"],
     *
     *   // override which node gets called and with what additional arguments
     *   nodeExecutableAndArgs: ["node"],
     *
     *   // supply additional arguments to the packager
     *   extraPackagerArgs: []
     * ]
     */

    project.ext.react = [
        enableHermes: false,  // clean and rebuild if changing
    ]

    apply from: "../../node_modules/react-native/react.gradle"

    /**
     * Set this to true to create two separate APKs instead of one:
     *   - An APK that only works on ARM devices
     *   - An APK that only works on x86 devices
     * The advantage is the size of the APK is reduced by about 4MB.
     * Upload all the APKs to the Play Store and people will download
     * the correct one based on the CPU architecture of their device.
     */
    def enableSeparateBuildPerCPUArchitecture = false

    /**
     * Run Proguard to shrink the Java bytecode in release builds.
     */
    def enableProguardInReleaseBuilds = false

    /**
     * The preferred build flavor of JavaScriptCore.
     *
     * For example, to use the international variant, you can use:
     * `def jscFlavor = 'org.webkit:android-jsc-intl:+'`
     *
     * The international variant includes ICU i18n library and necessary data
     * allowing to use e.g. `Date.toLocaleString` and `String.localeCompare` that
     * give correct results when using with locales other than en-US.  Note that
     * this variant is about 6MiB larger per architecture than default.
     */
    def jscFlavor = 'org.webkit:android-jsc:+'

    /**
     * Whether to enable the Hermes VM.
     *
     * This should be set on project.ext.react and mirrored here.  If it is not set
     * on project.ext.react, JavaScript will not be compiled to Hermes Bytecode
     * and the benefits of using Hermes will therefore be sharply reduced.
     */
    def enableHermes = project.ext.react.get("enableHermes", false);

    android {
        compileSdkVersion rootProject.ext.compileSdkVersion

        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }

        defaultConfig {
            applicationId "com.devpoi.rnpush"
            minSdkVersion rootProject.ext.minSdkVersion
            targetSdkVersion rootProject.ext.targetSdkVersion
            versionCode 1
            versionName "1.0"
        }
        splits {
            abi {
                reset()
                enable enableSeparateBuildPerCPUArchitecture
                universalApk false  // If true, also generate a universal APK
                include "armeabi-v7a", "x86", "arm64-v8a", "x86_64"
            }
        }
        signingConfigs {
            debug {
                storeFile file('debug.keystore')
                storePassword 'android'
                keyAlias 'androiddebugkey'
                keyPassword 'android'
            }
        }
        buildTypes {
            debug {
                signingConfig signingConfigs.debug
            }
            release {
                // Caution! In production, you need to generate your own keystore file.
                // see https://facebook.github.io/react-native/docs/signed-apk-android.
                signingConfig signingConfigs.debug
                minifyEnabled enableProguardInReleaseBuilds
                proguardFiles getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro"
            }
        }

        packagingOptions {
            pickFirst "lib/armeabi-v7a/libc++_shared.so"
            pickFirst "lib/arm64-v8a/libc++_shared.so"
            pickFirst "lib/x86/libc++_shared.so"
            pickFirst "lib/x86_64/libc++_shared.so"
        }

        // applicationVariants are e.g. debug, release
        applicationVariants.all { variant ->
            variant.outputs.each { output ->
                // For each separate APK per architecture, set a unique version code as described here:
                // https://developer.android.com/studio/build/configure-apk-splits.html
                def versionCodes = ["armeabi-v7a": 1, "x86": 2, "arm64-v8a": 3, "x86_64": 4]
                def abi = output.getFilter(OutputFile.ABI)
                if (abi != null) {  // null for the universal-debug, universal-release variants
                    output.versionCodeOverride =
                            versionCodes.get(abi) * 1048576 + defaultConfig.versionCode
                }

            }
        }
    }

    dependencies {
        implementation fileTree(dir: "libs", include: ["*.jar"])
        //noinspection GradleDynamicVersion
        implementation "com.facebook.react:react-native:+"  // From node_modules

        implementation "androidx.swiperefreshlayout:swiperefreshlayout:1.0.0"

        debugImplementation("com.facebook.flipper:flipper:${FLIPPER_VERSION}") {
          exclude group:'com.facebook.fbjni'
        }

        debugImplementation("com.facebook.flipper:flipper-network-plugin:${FLIPPER_VERSION}") {
            exclude group:'com.facebook.flipper'
        }

        debugImplementation("com.facebook.flipper:flipper-fresco-plugin:${FLIPPER_VERSION}") {
            exclude group:'com.facebook.flipper'
        }

        if (enableHermes) {
            def hermesPath = "../../node_modules/hermes-engine/android/";
            debugImplementation files(hermesPath + "hermes-debug.aar")
            releaseImplementation files(hermesPath + "hermes-release.aar")
        } else {
            implementation jscFlavor
        }

        // implementation 'com.google.firebase:firebase-analytics:17.3.0'
    }

    apply plugin: 'com.google.gms.google-services' << add

    // Run this once to be able to run the application with BUCK
    // puts all compile dependencies into folder libs for BUCK to use
    task copyDownloadableDepsToLibs(type: Copy) {
        from configurations.compile
        into 'libs'
    }

    apply from: file("../../node_modules/@react-native-community/cli-platform-android/native_modules.gradle"); applyNativeModulesAppBuildGradle(project)
    ```

- ~androud/app/google-services.json (생성)
- ~android/app/src/main/AndroidManifest.xml (수정 - 2 add)

    ```bash
    <manifest xmlns:android="http://schemas.android.com/apk/res/android"
      package="com.devpoi.rnpush">

        <uses-permission android:name="android.permission.INTERNET" />

    		<< add
        <uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>
        <uses-permission android:name="android.permission.WAKE_LOCK" />
        

        <application
          android:name=".MainApplication"
          android:label="@string/app_name"
          android:icon="@mipmap/ic_launcher"
          android:roundIcon="@mipmap/ic_launcher_round"
          android:allowBackup="false"
          android:theme="@style/AppTheme">
          <activity
            android:name=".MainActivity"
            android:label="@string/app_name"
            android:configChanges="keyboard|keyboardHidden|orientation|screenSize|uiMode"
            android:launchMode="singleTask"
            android:windowSoftInputMode="adjustResize">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
          </activity>
          <activity android:name="com.facebook.react.devsupport.DevSettingsActivity" />

          <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationPublisher" />
          <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationBootEventReceiver">
              <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
              </intent-filter>
          </receiver>
          <service
              android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationListenerService"
              android:exported="false" >
              <intent-filter>
                  <action android:name="com.google.firebase.MESSAGING_EVENT" />
              </intent-filter>
          </service>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_foreground"
                      android:value="true"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_channel_name"
                      android:value="Example-Channel"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_channel_description"
                      android:value="Super channel description"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_color"
                      android:resource="@android:color/white"/>  << add
          
        </application>

    </manifest>
    ```

- ~android/app/src/main/raw/sample.mp3 (생성) - 효과음 파일 (Optional)

```jsx
// 실행준비
$ cd android
$ ./gradlew clean  // 초기화
$ cd ..
$ yarn start && yarn android
```

# 4. 설치 완료 후 테스트

## 4.1 설치완료 확인

![./readme.img/Screenshot_20200705-175131.png](./readme.img/Screenshot_20200705-175131.png)

빨간색 글씨로 된 토큰 값 복사

example)

eHa0ibFKSMCuJVw_TEO8YH:APA91bHw-6dEi55UTeb_s6wpFdT6BrXEFfMocCdK0tFHvZQ-wW-ymx3Kt91R8ftcYojgRI2CChJKU1p2L9IVEoE75vQiv-AbxcjNZ0dVIpQpDfrN5-wGbHGzyFFqrRB4n1fsQAzlui8s

![./readme.img/_2020-07-05__5.55.56.png](./readme.img/_2020-07-05__5.55.56.png)

### 4.1.1 IOS 토큰이 특이하다!?

45b7685a72105f8445ad0bbea22080fda24e00b6a5de60028b0c7833fd881a62

IOS 디바이스로 테스트 했을때 위와 같이 Android와는 상당히 짧은 토큰이 올 수 있다.

이런 경우 테스트 환경에서 발행하는 APNs 의 토큰이므로 이것을 FCM 토큰으로 변경해주어야 한다.

이런 경우 푸시 메시지 전송시 다음과 같은 메시지가 반환된다.

```jsx
{
    "multicast_id": 4694717167209999484,
    "success": 0,
    "failure": 1,
    "canonical_ids": 0,
    "results": [
        {
            "error": "InvalidRegistration"
        }
    ]
}
```

### 4.1.2 APNs 토큰 변환 방법

[https://github.com/zo0r/react-native-push-notification/issues/1251](https://github.com/zo0r/react-native-push-notification/issues/1251)

위 안내를 참고하여 아래와 같이 API로 토큰을 변경한다.

```jsx
HTTP HOST : https://iid.googleapis.com/iid/v1:batchImport

Headers : 푸시알람과 동일

Body :
{
  "application": "com.devpoi.last.push", (패키지 ID)
  "sandbox":true,
  "apns_tokens":[
      "APNS 토큰"
   ]
 }

// 결과
{
    "results": [
        {
            "registration_token": "fxArqQ8EeSo:APA91bF_E8r4-oplEb0SVe65-H5QDMkArlLMD8O08QKLiQgXiAOZt5TjsW0cXJBsprR8UH9DUpeSJVD9nevcyIohs4U6uBl0YfpH25U62-T1C7AO3cCQvASfBXXFbg6T984EWNGN4zHn",
            "apns_token": "45b7685a72105f8445ad0bbea22080fda24e00b6a5de60028b0c7833fd881a62",
            "status": "OK"
        }
    ]
}

//
registration_token 이것을 가지고 테스트하면 오케이!!
```

## 4.2 파이어베이스 발송 테스트 (2 종류)

### 4.2.1 파이어베이스 클라우드 메시징 이용

1. 클라우드 메시징 서비스 실행

    ![./readme.img/_2020-07-05__5.59.08.png](./readme.img/_2020-07-05__5.59.08.png)

2. 토큰 입력 후 발송

### 4.2.2 Post man 을 활용한 발송 테스트

[https://firebase.google.com/docs/cloud-messaging/migrate-v1?hl=ko](https://firebase.google.com/docs/cloud-messaging/migrate-v1?hl=ko) (마이그레이션)

1. 파이어베이스 서버 키 취득
2. Post 방식으로 발송

```jsx
// url
https://fcm.googleapis.com/fcm/send

// header - 2 param
key : Authorization
value key=파이어베이스 서버키

key : Content-Type
value : application/json

// body (json)  - noti + data
{
    "to" : "eHa0ibFKSMCuJVw_TEO8YH:APA91bHw-6dEi55UTeb_s6wpFdT6BrXEFfMocCdK0tFHvZQ-wW-ymx3Kt91R8ftcYojgRI2CChJKU1p2L9IVEoE75vQiv-AbxcjNZ0dVIpQpDfrN5-wGbHGzyFFqrRB4n1fsQAzlui8s",
    "notification" : {
    	"title" : "Postman Post 메시지 테스트",
        "body" : "테스트 메세지 내용",
        "content_available" : true,
        "priority" : "high"
    },
    "data" : {
        "title" : "Postman Post 메시지 테스트",
        "body" : "테스트 메세지 내용",
        "content_available" : true,
        "priority" : "high"
    }
}
```

### 4.2.3 발송 테스트 방식

> 보통 3가지 방식으로 테스트 하며 
Noti 의 방식 1) Noti Only, 2) Data Only 3) Noti + Data 를 테스트 한다.
즉 앱환경 3 * Noti Type 3 이렇게 테스트를 해본다.

1. 앱 환경에 따른 구분
    - 앱이 열려 있을때 (foreground)
    - 앱이 백그라운드에 있을때 (background)
    - 앱이 완전히 종료되있고, 휴대폰 잠금 상태일때 (closed)
2. 노티 종류에 따른 구분
    - Notification Only
    - Data Only
    - Notification + Data

![./readme.img/Screenshot_20200705-175517.png](./readme.img/Screenshot_20200705-175517.png)

Test - Android

![./readme.img/83830FA7-C505-4CBC-872D-FF2C1FBE8749.png](./readme.img/83830FA7-C505-4CBC-872D-FF2C1FBE8749.png)

Test - IOS
