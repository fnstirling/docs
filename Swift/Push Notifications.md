# Push Notifications

Push Notifications notes. Primary sources of information are:

* [Push notifications book](https://www.raywenderlich.com/books/push-notifications-by-tutorials)
* [Push notifications book resources](https://github.com/raywenderlich/not-materials)
* [Generating a remote notification](https://apple.co/2Ia9iUf)
* [Sending notification requests to APNs](https://apple.co/2mn04ih)
* [Sending Push Notifications With Vapor](https://www.raywenderlich.com/11238593-sending-push-notifications-with-vapor)

## Remote Notifications

Your server, called a **provider**, utilizes TLS to send notification requests to Apple, and the device uses an opaque Data instance — referred to as
a **device token** — which contains a unique identifier that the APNs is able to decode. The iOS device receives a (possibly new) token when it
authenticates with the APNs; the token is then sent to your provider so that a notification can be received in the future.

You should never cache a device token on the user's device as there are multiple instances in which APNs generate a new token, such as installing the
app on a new device or performing a restore from a backup.

The device token is now the address that a provider users to reference a user's specific device. When the provider service wishes to send a
notification, it will tell APNs the specific device token(s) that need to be sent a message

### Notification message flow

1. During application(_:didFinishLaunchingWithOptions:), a request is sent to APNs for a device token via registerForRemoteNotifications.
2. APNs will return a device token to your app and call application
   (_:didRegisterForRemoteNotificationsWithDeviceToken:) or emit an error message to application(_:
   didFailToRegisterForRemoteNotificationsWithError:).
3. The device sends the token to a provider in either binary or hexadecimal format. The provider will keep track of the token.
4. The provider sends a notification request, including one or more tokens, to APNs.
5. APNs sends a notification to each device for which a valid token was provided.

## Remote Notification Payload

The notification payload is a JSON structure with only a few keys being mandatory.

For regular remote notifications, the maximum payload size is currently 4KB (4,096 bytes). If your notification is too large, Apple will simply refuse
it and you’ll get an error from APNs.

### The aps dictionary key

The `aps` dictionary key is the main hub of the notification payload wherein everything defined and owned by Apple lives. Within the object at this
key, you’ll configure such items as:

* The message to be displayed to the end user.
* What the app badge number should be set to.
* What sound, if any, should be played when the notification arrives.
* Whether the notification happens without user interaction.
* Whether the notification triggers custom actions or user interfaces.

The most common `aps` keys are:

* Alert
* Localisation (language)
* Grouping notifications `thread-id`, is different from collapsing
* Badge (absolute number, not mathematical, therefore server needs to know or get the current badge number is adding or subtracting more)
* Sound (can be custom)

### Custom data

Everything outside of `aps` is for personal data. Examples include coordinates or maybe changing to a specific screen

### HTTP headers

Can send additioanal HTTP header fields to specify how Apple should handle your notification. It is unsure why these were chosen to be in headers
rather than the payload.

Common headers include:

* `apns-collapse-id` for collapsing notifications when a newer one supersedes an older one.
* Push type, such as `alert` or `background`
* `apns-priority` for priority

## Setup

In a SwiftUI App (with SwiftUI App lifecycle) add the push notification capabilities by:

1. Press ⌘ + 1 (or View ▸ Navigators ▸ Project) to open the Project Navigator and click on the top-most item (i.e. your project).
2. Select the target, not the project.
3. Open the Signing & Capabilities tab.
4. Click the + Capability button.
5. Search for and select Push Notifications from the menu that pops up. If you don’t see the Push Notifications capability, you’re not using a paid
   Apple Developer account. Double-check that you selected the correct Team and check that you have a valid Provisioning Profile for your team and
   bundle ID.
6. Notice the Push Notifications capability added below your signing information.

Create an app delegate as we need this service to run at the start of every app load.

``` swift
import UIKit
import UserNotifications

class AppDelegate: NSObject, UIApplicationDelegate {
  /// Request authorization for push notifications
  /// Whenever the app starts, you request authorization from the user to send badges, sounds and alerts to the user. If any of those items are granted by the user, you register the app for notifications.
  func application(
    _ application: UIApplication,
    didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?
  ) -> Bool {
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
  
  /// Convert device token opacque data type `Data` to `String`
  ///
  /// iOS will call this method once the device has sucessfully registered for push notifications. The token is a set of hex characters; the above code simply turns the token into a hex string
  func application(
    _ application: UIApplication,
    didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data
  ) {
    let token = deviceToken.reduce("") { $0 + String(format: "%02x", $1) }
    print(token)
  }
}

```

Call the app delegate in the apps entry struct (i.e. the struct with `@main` property wrapper) which is usually in the app entry file.

``` swift
@UIApplicationDelegateAdaptor(AppDelegate.self)
private var appDelegate
```

Additional authorisation options are available:

* Provisional authorization
* Critical alerts (if app requires it, such as heath and medicine apps)

Create your Authentication Token, i.e. JSON Web Token (JWT) `.p8` file.

* [Go to keys section of developer site](https://developer.apple.com/account/resources/authkeys/list)
* In the side-bar, click on Keys.
* Click on the blue “plus” button.
* Name the key Push Notification Key.
* Enable the Apple Push Notifications service (APNs) checkbox.
* Press Continue.
* Now click Register on the screen that appears
* Download the file
* Click Done

By default, the key will download to your Downloads directory and will be named something like AuthKey_689R3WVN5F.p8. The 689R3WVN5F part is your Key
ID, which you’ll need when you’re ready to send a push. You’ll also need to know your Team ID. At the very top-right of your browser window you’ll see
your Team ID listed right after your account name. It’s a 10 character long string of letters and numbers.

In a production app, you’d probably want to connect to secure website with the TLS protocol (i.e. https websites). Configure your iOS application
`info.plist`.

* Edit the Info.plist file.
* Right-click in the empty whitespace and choose Add Row.
* Set the key to be App Transport Security Settings.
* Expand your newly created App Transport Security Settings using the chevron on the left.
* Right-click on App Transport Security Settings and choose Add Row.
* Set the key to be Allow Arbitrary Loads and the value to be YES.

### Testing on simulator

Test your service by creating a file `payload.apns` with the below. Make sure you change the value for key `Simulator Target Bundle`. You’ve used an
apns extension on the filename. When you drag a file to the simulator with the apns extension, it knows that the file is a push notification payload,
as opposed to a file to save.

``` swift
{
  "aps": {
    "alert": "Hello"
  },
  "Simulator Target Bundle": "com.raywenderlich.PushNotifications"
}
```

Build and run the app in simulator. Once your app is running, send it back to the home screen by selecting Devices ▸ Home (⇧ + ⌘ + H). Currently your
app will not accept push notifications while running in the foreground. Open Finder on your Mac and drag the `payload.apns` file onto the simulator
window, a push notification should appear.

### Testing on device

Test your service on a real device by using an app like:

* [Push Notifications](https://github.com/onmyway133/PushNotifications)
* [Push Hero (paid, requires macOS 11)](https://onmyway133.com/pushhero/)

You will need the device token string and you will need to move app to background once running as foreground notifications not setup yet

## Server-Side Pushes

There is a slew of services online that will handle the server-side. Some of the most popular ones are:

* [Amazon Simple Notification Service (SNS)](aws.amazon.com/sns/)
* [Braze](bit.ly/2yM4hx7)
* [Firebase Cloud Messaging](bit.ly/2Nq4b5x)
* [Kumulos](bit.ly/2FIQ8Dy)
* [OneSignal](bit.ly/1Ukk3WL)
* [Airship](bit.ly/1QymqCY)

Alternatively you can build your own server with something like Vapor. For information on this setup please read the Server-Side Pushes chapter in
this push notifications book (https://www.raywenderlich.com/books/push-notifications-by-tutorials)

## Displaying foreground notifications

If you’d like to have iOS display your notification while your app is running in the foreground, you’ll need to implement the
UNUserNotificationCenterDelegate method userNotificationCenter(_:willPresent:withCompletionHandler:), which is called when a notification is delivered
to your app while it’s in the foreground. The only requirement of this method is calling the completion handler before it returns. Here, you can
identify what you want to happen when the notification comes in.

```swift
extension AppDelegate: UNUserNotificationCenterDelegate {
  func userNotificationCenter(
    _ center: UNUserNotificationCenter,
    willPresent notification: UNNotification,
    withCompletionHandler completionHandler:
    @escaping (UNNotificationPresentationOptions) -> Void
  ) {
    completionHandler([.banner, .sound, .badge])
  }
}
```

It used to be that you’d specify .alert in your completion handler if you wanted the notification to display to the end user. As of iOS 14, Apple now
provides you the ability to decide whether or not you’d like the alert to display when the app is in the foreground. If you do want foreground
notifications, choose the new .banner enum value. If you only wish alerts to appear when the app is running in the background, use the new .list enum.

If you want no action to happen, you can simply pass an empty array to the completion closure. Depending on the logic that pertains to your app, you
may want to investigate the notification.request property of type UNNotificationRequest and make the decision about which components to show based on
the notification that was sent to you.

In order for the delegate to be called, you have to tell the notification center that the AppDelegate is the actual delegate to use.

```swift
extension AppDelegate {
   func registerForPushNotifications(application: UIApplication) {
     let center = UNUserNotificationCenter.current()
     center.requestAuthorization(options: [.badge, .sound, .alert]) { [weak self] granted, _ in
   
       guard granted else {
         return
       }
   
       center.delegate = self
   
       DispatchQueue.main.async {
         application.registerForRemoteNotifications()
       }
     }
   }
}
```

Call the above in

```swift 
func application(
 _ application: UIApplication,
 didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?
) -> Bool {
   //...
}
```

## Tapping the notification

Used for taking action off tapping of the notification, this could be navigation change or logs or something else. This function will handle tapping
on notifications. Add this to `AppDelegate` or its own delegate and add a property to that delege in `AppDelegate`

```swift
func userNotificationCenter(
  _ center: UNUserNotificationCenter, 
  didReceive response: UNNotificationResponse, 
  withCompletionHandler completionHandler: @escaping () -> Void
) {
  defer { completionHandler() }

  guard response.actionIdentifier 
    == UNNotificationDefaultActionIdentifier else {
    return
  }
  
  // Perform actions here
}
```

Note: There is an actionIdentifier called `UNNotificationDismissActionIdentifier`. Don’t be fooled into thinking this method will be called if the
user simply dismisses the notification — it won’t!

## Silent Notifcations

Used to perform some task silently (no visual or audio que), such as prefetch data so the app responds quicker but no reason to notify the user. These
tasks need to complete within 30 seconds as IOS will wake up app in the background and set a 30 second time limit. For this you need to:

* Update the payload.
* Add the Background Modes capability.
* Implement a new `UIApplicationDelegate` method.

The following outlines which methods are called and in that order depending on whether the app is in the foreground or background and whether or not
the `content-available` flag (i.e. silent notification) is present with a value of `1`

* `userNotificaitonCenter(_:willPresent:withCompletionHandler:)`
	* Foreground Yes
	* `content-available` No
* `userNotificationCenter_:willPresent:withCompletionHandler:)` `application(_:didReceiveRemoteNotification:fetchCompletionHandler:)`
	* Foreground Yes
	* `content-available` Yes
* iOS displays the alert as appropriate
	* Foreground No
	* `content-available` No
* `application(_:didReceiveRemoteNotification:fetchCompletionHandler`
	* Foreground No
	* `content-available` Yes

### Updating the payload

Make a new key-value pair to your payload. Inside of the aps dictionary, add a new key of `content-available` with a value of `1`. If you don’t want a
silent notification — do not include the `content-available` key. Set the `apns-priority` HTTP header to `5`, as explained in Chapter 3.
Setting `content-available` to `1` will tell iOS to wake your app when it receives a push notification, so it can prefetch any content related to the
notification. The below example will prefetch an image.

```json
{
  "aps": {
    "content-available": 1
  },
  "image": "https://bit.ly/3dfsW2n",
  "text": "A nice picture of the Earth"
}
```

### Adding background modes capability

Open the project navigator (⌘ + 1), select your project and then select your app target.

Now, on the Signing & Capabilities tab, press the + Capability button and add the Background Modes capability. From the Background Modes options check
the Remote notification checkbox at the bottom of the list.

### App delegate updates

Need to validate the silent notification has the right data and then process the updates. For example

```swift
func application(
  _ application: UIApplication, 
  didReceiveRemoteNotification userInfo: [AnyHashable: Any], 
  fetchCompletionHandler completionHandler: 
  @escaping (UIBackgroundFetchResult) -> Void
) {
  guard 
    let text = userInfo["text"] as? String,
    let image = userInfo["image"] as? String,
    let url = URL(string: image) else {
    completionHandler(.noData)
    return
  }
}
```

You are expecting both text and an image as part of the payload, and you need to ensure that the image specified is actually something you can turn
into a URL.

If there is any issues, you can tell iOS that you don’t have the needed data by passing .noData to your completionHandler. You probably don’t want to
specify .failed since technically this just wasn’t a payload for an image.

## Custom Actions

iOS allows for buttons to be attached notifications. For example if you had a meeting notification you might want to easily accept or reject

### Categories

Notification categores all you to specify up to four custom actions per category that will be displayed with your push notification. The system will
only display the first 2 actions if your notification appears in a banner. As of 7 February 2020 you need to test this on a physical device as the
simulator doesn't display categories. Below is an accepte and reject banner example.

```swift
// Add this to top of AppDelegate or standalone seperate file below import statements
// I added into AppDelegate because I believe these relate to the AppDelegate and it gives it more context
public let categoryIdentifier = "AcceptOrReject"

public enum ActionIdentifier: String {
  case accept, reject
}

// Add this to an AppDelegate extension to perform the registration
private func registerCustomActions() {
  let accept = UNNotificationAction(
    identifier: ActionIdentifier.accept.rawValue,
    title: "Accept")

  let reject = UNNotificationAction(
    identifier: ActionIdentifier.reject.rawValue,
    title: "Reject")

  let category = UNNotificationCategory(
    identifier: categoryIdentifier,
    actions: [accept, reject],
    intentIdentifiers: [])

  UNUserNotificationCenter.current().setNotificationCategories([category])
}

// Call this in application(_:didRegisterForRemoteNotificationsWithDeviceToken:)
registerCustomActions()

// Add this to userNotificationCenter(_:didReceive:withCompletionHandler:)
let identity = response.notification.request.content.categoryIdentifier
guard identity == categoryIdentifier,
  let action = ActionIdentifier(rawValue: response.actionIdentifier) else {
  return
}

print("You pressed \(response.actionIdentifier)")
```

## Modifying the payload

Using a service extension can act as a middleware between the APNs and UI. This can be a great thing to modify the payload such as decryption or
working with a limited payload but maybe getting more data. Good examples of this are decryption, localization, badge counts

* In Xcode, select File ▸ New ▸ Target….
* Make sure iOS is selected and choose the Notification Service Extension.
* Name it.
* Press Finish.
* When asked about scheme activation, select Cancel.

You don’t actually run a service extension so that’s why you didn’t let it make the new target your active scheme.

You have roughly 30 seconds to perform whatever actions you need to take. If you run out of time, iOS will call the second method,
serviceExtensionTimeWillExpire to give you one last chance to hurry up and finish. If you’re using a restartable network connection, the second method
might give you just enough time to finish. Don’t try to perform the same actions again in the serviceExtensionTimeWillExpire method though. The intent
of this method is that you perform a much smaller change that can happen quickly. You may have a slow network connection, for example, so there’s no
point in trying yet another network download. Instead, it might be a good idea to tell the user that they got a new image or a new video, even if you
didn’t get a chance to download it.

If you haven’t called the completion handler before time runs out, iOS will continue on with the original payload.

You may make any modification to the payload you want — except for one. You may not remove the alert text. If you don’t have alert text, then iOS will
ignore your modifications and proceed with the original payload.

### Service extension payloads

You don’t necessarily always want an extension to run every time you receive a push notification — just when it needs to be modified. In the above
example, you’d obviously use it 100% of the time as you’re decrypting data. But what if you were just downloading a video? You don’t always send
videos.

To tell iOS that the service extension should be used, simply add a mutable-content key to the aps dictionary with an integer value of 1.

Note: If you forget to add this key, your service extension will never be called. You’re most likely going to forget to do this and have a heck of a
time figuring out why your code doesn’t work!

### Sharing data between seperate processes

Your primary app target and your extension are two separate processes. You can’t share data between them by default. If you do more than the most
simplistic of things with your extension, you’ll quickly find yourself wanting to be able to pass data back and forth. This is easily accomplished via
Application Groups, which allows access to group containers that are shared between multiple related apps and extensions.

To enable this capability, press ⌘ + 1 to go back to the Project navigator and click on your main target. Next, navigate to the Signing & Capabilities
tab again. Click the + Capability button in the top-left and you’ll see App Groups near the top of the list. Double-click it.

You should see a new section pop up called App Groups in the tab. Press the + button and then set the name you wish to use. Generally, you’ll want the
same name as your bundle identifier, just prefixed with group:

You’ll notice, in this image, that an App Group has already been created for another project, so that’s also shown in the image. Be sure that you only
select the one group you want if there are multiple listed.

Now, go into your Service target target’s capabilities tab and enable the App Groups there as well, selecting the same app group you selected for your
app target.

### Adding a file to a target

Bring up the File inspector by pressing ⌥ + ⌘ + 1 and in the Target Membership section check the boxes next to your service extension as well as the
primary target:

### Accessing Core Data

See Chapter 10 of Push notification book

### Localization

See Chapter 10 of Push notifications book

### Debugging

To debug a target you need to go through extra processes

* Open up your service file and set a breakpoint on the line where you decode the title.
* Build and run your app.
* In Xcode’s menu bar, choose Debug ▸ Attach to Process by PID or Name….
* In the dialog window that appears, enter the name of your target.
* Press the Attach button.

Be aware that debugging service extensions is a bit finicky and sometimes it just plain doesn’t work. If you aren’t able to find your process listed,
you might have to go through a full restart of Xcode and possibly even a reboot of your device.

## Modifying the content

Custom interfaces are implemented as separate targets in your Xcode project, just like the service extension. You can do things like create a
notification that displays a location on the map, with the ability to comment on that location right from the notification, all without opening the
app. See Chapter 11 and 12 of Push Notifications book.

## Local notifications

For details about this see Chapter 13 of Push Notifications book

There are three types of local notifications:

* Calendar: Notification occurs on a specific date.
* Interval: Notification occurs after a specific amount of time.
* Location: Notification occurs when entering a specific area.

You still need permissions. The only difference when requesting permissions locally is that you do not call the registerForRemoteNotifications method
on success:

```swift
func registerForLocalNotifications(application: UIApplication) {
  let center = UNUserNotificationCenter.current()
  center.requestAuthorization(
    options: [.badge, .sound, .alert]) {
    [weak center, weak self] granted, _ in
    guard granted, let center = center, let self = self 
    else { return }

    // Take action here
  }
}
```

### Triggers

A primary difference between remote and local notifications is how they are triggered. ou’ve seen that remote notifications require some type of
external service to send a JSON payload through APNs. Local notifications use all the same type of data that you provide in a JSON payload but they
instead use Swift objects to define what is delivered to the user.

Local notifications utilize what is referred to as a trigger, which is the condition under which the notification will be delivered to the user. There
are three possible triggers, each corresponding to one of the notification types:

* `UNCalendarNotificationTrigger`
* `UNTimeIntervalNotificationTrigger`
* `UNLocationNotificationTrigger`

All three triggers contain a repeats property, which allows you to fire the trigger more than once.

`UNCalendarNotificationTrigger` uses `DateComponents` to trigger at different times. The below will trigger at 8:30am in the morning on a Monday

```swift 
let components = DateComponents(hour: 8, minute: 30, weekday: 2)
let trigger = UNCalendarNotificationTrigger(
  dateMatching: components,
  repeats: true)
```

`UNTimeIntervalNotificationTrigger` will display a notification after a set amount of time. The below is a notification to head out in 10 minutes

```swift
let trigger = UNTimeIntervalNotificationTrigger(
  timeInterval: 10 * 60,
  repeats: false)
```

`UNLocationNotificationTrigger` allows you to specify a `CLCircularRegion` that you wish to monitor. When the device enters said area, the
notification will fire. You need to know the latitude and longitude of the center of your target location as well as the radius that should be used.
Those three items define a circular region on the map, which iOS will monitor for entry. You must have authorization to use Core Location and must
have permission to monitor the user’s location while they’re using the app. You do not need to request to always have permission as just regions are
being monitored. You will also need to notify iOS if you care if the user is enters, exits, or both, from the area.
See [Core Location Tutorial for iOS: Tracking Visited Locations](https://bit.ly/2MLc1GG) for more information on Core Location, privacy concerns and
requesting permissions if you’re not already familiar with that framework. The below will trigger a notification whenever the user enters 1 mile
radius around 1 Infinite Loop, Cupertino, California.

```swift
let oneMile = Measurement(value: 1, unit: UnitLength.miles)
let radius = oneMile.converted(to: .meters).value
let coordinate = CLLocationCoordinate2D(
  latitude: 37.33182,
  longitude: -122.03118)
let region = CLCircularRegion(
  center: coordinate,
  radius: radius,
  identifier: UUID().uuidString)

region.notifyOnExit = false
region.notifyOnEntry = true

let trigger = UNLocationNotificationTrigger(
  region: region,
  repeats: false)
```

### Defining content

This is where the `UNMutableNotificationContent` class comes into play. Be sure to note the “Mutable” in that class’s name. There’s also a class
called `UNNotificationContent`, which you won’t use here or you’ll end up with compiler errors.

This class is similar to the JSON payload used in remote notifications. The elements from the aps dictionary exist as properties right on the object.
For your custom content, you simply add that to the `userInfo` dictionary. Everything outside of your aps dictionary, meaning - your custom content,
falls under the userInfo dictionary. The two code snippets below are equivalent.

```swift
// Local notification
let content = UNMutableNotificationContent()
content.title = "New Calendar Invitation"
content.badge = 1
content.categoryIdentifier = "CalendarInvite"
content.userInfo = [
  "title": "Family Reunion",
  "start": "2018-04-10T08:00:00-08:00",
  "end": "2018-04-10T12:00:00-08:00",
  "id": 12
]
```

```json
// Remote notification
{
  "aps": {
    "alert": {
      "title": "New Calendar Invitation"
    },
    "badge": 1,
    "mutable-content": 1,
    "category": "CalendarInvite"
  },
  "title": "Family Reunion",
  "start": "2018-04-10T08:00:00-08:00",
  "end": "2018-04-10T12:00:00-08:00",
  "id": 12
}
```

#### Sounds

f you’d like your notification to play a sound when it’s delivered, you must either store the file in your app’s main bundle, or you must download it
and store it in the Library/Sounds subdirectory of your app’s container directory. Generally, you’ll just want to use the default
sound: `content.sound = UNNotificationSound.default`

#### Localization

There’s one small “gotcha” when working with localization and local notifications. Consider the case wherein the user’s device is set to English, and
you set the content to a localized value. Then, you create a trigger to fire in three hours. An hour from then, the user switches their device back to
Arabic. Suddenly, you’re showing the wrong language!

The solution to the above problem is to not use the normal NSLocalizedString methods. Instead, you should use localizedUserNotificationString(forKey:
arguments:) from NSString. The difference is that the latter method delays loading the localized string until the notification is actually delivered,
thus ensuring the localization is correct.

Always use `localizedUserNotificationString(forKey:arguments:)` when localizing local notifications.

#### Grouping

If you’d like your local notification to support grouping, simply set the threadIdentifier property with a proper identifier to group them by. For
example: `content.threadIdentifier = "My group identifier here"`

### Scheduling

```swift
let identifier = UUID().uuidString
let request = UNNotificationRequest(
  identifier: identifier,
  content: content,
  trigger: trigger)

UNUserNotificationCenter.current().add(request) { error in
  if let error = error {
    // Handle unfortunate error if one occurs.
  }
}
```

### Foreground notifications

You need an extra step to allow local notifications to be displayed when the app is running in the foreground.

Simply adopt `UNUserNotificationCenterDelegate` somewhere in your app and implement the following method:

```swift
func userNotificationCenter(
  _ center: UNUserNotificationCenter, 
  willPresent notification: UNNotification, 
  withCompletionHandler completionHandler: 
  @escaping (UNNotificationPresentationOptions) -> Void) {
  
  completionHandler([.badge, .sound, .banner])
}
```

You can, of course, take other actions here such as updating the user interface directly based on what the notification is for!

