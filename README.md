<p align="center">
<img src="https://github.com/medricsCO/CollectaSdk/raw/master/medrics_logo.png" alt="MEDRICS Healthcare Solutions Corp" title="MEDRICS Healthcare Solutions Corp" width="557"/>
</p>

# Collecta Push SDK

[![Version](https://img.shields.io/cocoapods/v/CollectaSdk.svg?style=flat)](https://cocoapods.org/pods/CollectaPushSDK)
[![Platform](https://img.shields.io/cocoapods/p/CollectaSdk.svg?style=flat)](https://cocoapods.org/pods/CollectaPushSDK)

## Requirements

- iOS 10.0+
- Xcode 10.2+
- Swift 5+
- [Set up a Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/ios/client)
## Installation
### Using CocoaPods

[CocoaPods](https://cocoapods.org) is a dependency manager for Cocoa projects. For usage and installation instructions, visit their website. To integrate Our SDK into your Xcode project using CocoaPods, specify it in your `Podfile`:
```ruby
target 'your_project_name' do
  pod 'CollectaPushSDK'
end
```

Then install the pods using `pod install --repo-update`

## Usage
### Initialize CollectaPushSDK in your app
You can add CollectaPushSDK initialization code to your application either at startup ,or at the desired point in your application flow. Import the CollectaPushSDK module and configure a shared instance as shown:
1. Import the CollectaPushSDK module in your `UIApplicationDelegate`:
   ```swift
   import CollectaPushSDK
   ```
2. Configure a CollectaPushSDK shared instance, typically in your app's `messaging(_:didReceiveRegistrationToken:)` method to be notified whenever the firebase token is updated:
   ```swift
   CollectaPushSDK.shared.initialize(username:"your-user-name",
                                     password: "your-password",
                                     source:"source-1",
                                     uuid:"user-id",
                                     fcmToken:fcmToken)
   ```   
   Parameters:
   
    - `username` -> username we provide
    - `password` -> password we provide
    - `source` -> source we provide
    - `uuid` -> unique id for the user
    - `fcmToken` -> Firebase registration token
 
3. To respond to the delivery of notifications, you must implement a delegate for the shared `UNUserNotificationCenter`object in `application(_:didFinishLaunchingWithOptions:)`:
   ```swift
   UNUserNotificationCenter.current().delegate = self
   ```
4. Your delegate object must conform to the `UNUserNotificationCenterDelegate` protocol ,and implement the `userNotificationCenter(_:didReceive:withCompletionHandler:)` and `userNotificationCenter(_:willPresent:withCompletionHandler:)` methods.
   ```swift
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
                                didReceive response: UNNotificationResponse, 
                                withCompletionHandler completionHandler: @escaping () -> Void) {
        let userInfo = response.notification.request.content.userInfo
        CollectaPushSDK.shared.didReceiveMessage(userInfo:userInfo)
        completionHandler()
    }
    
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
                                willPresent notification: UNNotification, 
                                withCompletionHandler completionHandler:@escaping(UNNotificationPresentationOptions)->Void) {
       completionHandler([.alert,.sound,.badge])
    }
   ``` 

### Add Notification Service Extension
We want to modify the payload of a remote notification before it's displayed on the user’s iOS device so we use [UNNotificationServiceExtension](https://developer.apple.com/documentation/usernotifications/unnotificationserviceextension).
1. In Xcode Select `File` > `New` > `Target`...
2. Select `Notification Service Extension` then press Next.
3. Enter the product name as `MyNotificationServiceExtension` and press Finish.
4. Press Cancel on the Activate scheme prompt.
   By cancelling, you are keeping Xcode debugging your app, instead of just the extension. If you activate by accident, you can always switch back to debug your app within Xcode (next to the play button).
5. In the project navigator, select the top level project directory and select the `MyNotificationServiceExtension` target in the project and targets list.
Unless you have a specific reason not to, you should set the `Deployment Target` to be iOS 10.
6. Open `NotificationService.swift` and add our `didReceiveNotificationExtensionRequest` method to `didReceive(_ request:withContentHandler:)` method as shown below
   ```swift
   override func didReceive(_ request: UNNotificationRequest, withContentHandler contentHandler: @escaping (UNNotificationContent) -> Void) {
        self.contentHandler = contentHandler
        bestAttemptContent = (request.content.mutableCopy() as? UNMutableNotificationContent)
        
        CollectaPushSDK.shared.didReceiveNotificationExtensionRequest(request, withContentHandler:contentHandler)
    }
   ``` 
7. Add the CollectaPushSDK dependency under your project name target as well as `MyNotificationServiceExtension` target like below
   ```ruby
   target 'your_project_name' do
   pod 'CollectaPushSDK'
   end

   target 'MyNotificationServiceExtension' do
   pod 'CollectaPushSDK'
   end
   ```
Then install the pods using `pod install --repo-update` 

## Credits

Collecta SDK is owned and maintained by the [MEDRICS Healthcare Solutions Corp](http://medrics.us/). You can follow us on Linkedin at [MEDRICS](https://www.linkedin.com/company/medrics/) for project updates and releases.

## Copyright
Copyright © 2020 [MEDRICS Healthcare Solutions Corp](http://medrics.us/). All rights reserved.
