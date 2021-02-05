# Push Notifications

Push Notifications notes. Primary sources of information are [this push notification book](https://www.raywenderlich.
com/books/push-notifications-by-tutorials), [this Apple document on generating a remote notification]
(https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
,
[this APple artlice on sending notification requests to APNs](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)

## Remote Notifications

Your server, called a **provider**, utilizes TLS to send notification requests to Apple, and the device uses an opaque
Data instance — referred to as a **device token** — which contains a unique identifier that the APNs is able to decode.
The iOS device receives a (possibly new) token when it authenticates with the APNs; the token is then sent to your
provider so that a notification can be received in the future.

You should never cache a device token on the user's device as there are multiple instances in which APNs generate a new
token, such as installing the app on a new device or performing a restore from a backup.

The device token is now the address that a provider users to reference a user's specific device. When the provider
service wishes to send a notification, it will tell APNs the specific device token(s) that need to be sent a message

### Notification message flow

1. During application(_:didFinishLaunchingWithOptions:), a request is sent to APNs for a device token via
   registerForRemoteNotifications.
2. APNs will return a device token to your app and call application
   (_:didRegisterForRemoteNotificationsWithDeviceToken:) or emit an error message to application(_:
   didFailToRegisterForRemoteNotificationsWithError:).
3. The device sends the token to a provider in either binary or hexadecimal format. The provider will keep track of the
   token.
4. The provider sends a notification request, including one or more tokens, to APNs.
5. APNs sends a notification to each device for which a valid token was provided.

## Remote Notification Payload

The notification payload is a JSON structure with only a few keys being mandatory.

For regular remote notifications, the maximum payload size is currently 4KB (4,096 bytes). If your notification is too
large, Apple will simply refuse it and you’ll get an error from APNs.

### The aps dictionary key

The `aps` dictionary key is the main hub of the notification payload wherein everything defined and owned by Apple
lives. Within the object at this key, you’ll configure such items as:

* The message to be displayed to the end user.
* What the app badge number should be set to.
* What sound, if any, should be played when the notification arrives.
* Whether the notification happens without user interaction.
* Whether the notification triggers custom actions or user interfaces.

The most common `aps` keys are:

* Alert
* Localisation (language)
* Grouping notifications `thread-id`, is different from collapsing
* Badge (absolute number, not mathematical, therefore server needs to know or get the current badge number is adding or
  subtracting more)
* Sound (can be custom)

### Custom data

Everything outside of `aps` is for personal data. Examples include coordinates or maybe changing to a specific screen

### HTTP headers

Can send additioanal HTTP header fields to specify how Apple should handle your notification. It is unsure why these
were chosen to be in headers rather than the payload.

Common headers include:

* `apns-collapse-id` for collapsing notifications when a newer one supersedes an older one.
* Push type, such as `alert` or `background`
* `apns-priority` for priority

## Setup

In a SwiftUI App (with SwiftUI App lifecycle) add the push notification capabilities by:

1. Press ⌘ + 1 (or View ▸ Navigators ▸ Project) to open the Project Navigator and click on the top-most item (i.e. your
   project).
2. Select the target, not the project.
3. Open the Signing & Capabilities tab.
4. Click the + Capability button.
5. Search for and select Push Notifications from the menu that pops up. If you don’t see the Push Notifications
   capability, you’re not using a paid Apple Developer account. Double-check that you selected the correct Team and
   check that you have a valid Provisioning Profile for your team and bundle ID.
6. Notice the Push Notifications capability added below your signing information.

Create an app delegate as we need this service to run at the start of every app load.

``` swift
import UIKit
import UserNotifications

class AppDelegate: NSObject, UIApplicationDelegate {
  func application(_ application: UIApplication,
                   didFinishLaunchingWithOptions launchOptions:
                        [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
    UNUserNotificationCenter.current().requestAuthorization(options: [
      .badge, .sound, .alert
    ]) { granted, _ in
      guard granted else { return }

      DispatchQueue.main.async {
        application.registerForRemoteNotifications()
      }
    }

    return true
  }
}
```

Call the app delegate in the apps entry struct (i.e. the struct with `@main` property wrapper) which is usually in the
app entry file.

``` swift
@UIApplicationDelegateAdaptor(AppDelegate.self)
private var appDelegate
```