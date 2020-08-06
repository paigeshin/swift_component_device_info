# swift_component_device_info

# Push Message - Device Token

### ask for permission
```swift

let app: UIApplication = UIApplication.shared
if #available(iOS 10.0, *) {
    // For iOS 10 display notification (sent via APNS)
    UNUserNotificationCenter.current().delegate = self
    let authOptions: UNAuthorizationOptions = [.alert, .badge, .sound]
    UNUserNotificationCenter.current().requestAuthorization(
    options: authOptions) { (granted, error) in
        DispatchQueue.main.async {
            if let error: Error = error {
                print("\(self.TAG) - setPushNotifcation: \(error.localizedDescription)")
                return
            }
        }
    }
} else {
    let settings: UIUserNotificationSettings =
        UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
    app.registerUserNotificationSettings(settings)
}
app.registerForRemoteNotifications()


```

### get Device token from AppDelegate method

```swift

func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
    let deviceTokenString = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    if !deviceTokenString.isEmpty {
        Defaults.setDeviceToken(deviceToken: deviceTokenString)
        
        //사실 push notification 물어보는 순간 device token이 생성된다.. 그렇지만 혹시 몰라서 넣어둔다
        //device token이 없을 때만... device token만 update해주는 router를 호출해준다.
        if Defaults.getDeviceToken() == nil {
            //유저의 값이 있다면...
            if let token: String = Defaults.getToken() {
                //유저가 허락했을 시에 server에 post 요청
                APIService.shared.postUpdateDeviceToken(token: token, success: { (success) in
                    if success {
                             print("\(self.TAG) - setDeviceToken(): succeded in updating user device token")
                         }
                }, processError: { (message) in
                    print("\(self.TAG) - setDeviceToken(): process error happened, \(message)")
                }) { (error) in
                    print("\(self.TAG) - setDeviceToken(): Error happened while updating device token")
                }
            }
        }
    }
}

```
# Get Device UUID

```swift

if let deviceUUID: String = UIDevice.current.identifierForVendor?.uuidString {
    if !deviceUUID.isEmpty {
        Defaults.setDeviceUUID(deviceUUID: deviceUUID)
    }
}

```

# Get Device Model Name

```swift

let deviceModel: String = UIDevice.modelName

```

# Get Device OS Version


``` swift

let osVersion: String = UIDevice.current.systemVersion

```

# Get Device Name

``` swift

    let deviceName: String = UIDevice.current.name

```
