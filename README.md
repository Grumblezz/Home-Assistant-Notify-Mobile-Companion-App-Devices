# Home-Assistant-Notify-Mobile-Companion-App-Devices
**Struggling to make use of the notification options of the [Home Assistant Companion App](https://companion.home-assistant.io) on your mobile device? Then this script can help you in the right direction.**

Ever gone through all of the options of the [Companion app](https://companion.home-assistant.io/docs/notifications/notifications-basic)?, then you may have lost your self :thinking: in all of the options you can pick you might be dazzled :dizzy_face: what you can do with it, let alone reapply it in another automation :confused:.

And so I noticed over the last year or so that I made more and more use of the notifications via the Home Assistant Companion App on the various mobile devices, and I loved it so much that I applied it for many automations. During that journey I was struggling with adding and fixing the code in various automations (there are +/- 40 automations where I was using it) so maintaining all of it was a nightmare. And thus I made one script that has all the logic in it to notify all or specific devices, which have the app installed, and let the automation call the script. And meanwhile use the [selectors](https://www.home-assistant.io/docs/blueprint/selectors) to have a UI to guide the process with all sorts of options.

![This is an image](./notify_devices.png)

***Note***
*Most of our mobile devices at home are Android based, so the script is build to favour Android, but there are various options for the iOS users :nerd_face:.*

I tried to add as many options as I went along (all options are from the [Introduction | Home Assistant Companion Docs](https://companion.home-assistant.io/docs/notifications/)), but not every thing is there (yet), but it should get you quite far (hopefully :smiley: ).

## Features of this script ##

 * Use an UI to select options instead of reading the documentation 😵‍💫🥴🤔🥲🥱😫😝

 * Able to send notifications to one or more specific device(s) or all devices
 * Send notifications based on the device(s) state: 
   * Home
   * Away
   * Both (Home and Away)
 * Send notifications to Android and iOS devices (!). The script then filters the input to what is supported per platform. Thus Android features are not send to iOS devices and visa versa.
 * Send notifications with actionable actions. The script then filters the actions per  platform. Thus Android actions are not send to iOS devices and visa versa.
 * Support notifications with camera images/live feed for camera devices, also for [Google Nest camera devices](https://www.home-assistant.io/integrations/nest/) (!).

## Where to start?
+ First thing is of course following the [Getting Started](https://companion.home-assistant.io/docs/getting_started/) to configure and install the Home Assistant Companion App
+ Once the Home Assistant Companion App is installed and connected you should have a devices in the [Integrations section](https://www.home-assistant.io/integrations/mobile_app/)


## Then what?
+ Click on this link to [![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FGrumblezz%2FHome-Assistant-Notify-Mobile-Companion-App-Devices%2Fblob%2Fmain%2Fnotify_devices.yaml)
+ Follow the instuctions to create a new script.
+ Go to the Developer Tools → Services and test the new script [![Open your Home Assistant instance and show your service developer tools.](https://my.home-assistant.io/badges/developer_services.svg)](https://my.home-assistant.io/redirect/developer_services/)
  + Use the 'Go to YAML mode' and click on the "Fill example data" to send notification example to all mobile devices :nerd_face:
  + Try out the examples (at the end of this article)
+ Use the script in your automation to send notifications to your mobile device :partying_face: :partying_face: :partying_face:

## Usefull examples

### Example 1: Send a persistent notification, that the user can’t remove
```
service: script.notify_devices
data:
  notify_home_or_away: Both
  data_notification_icon: mdi:human-greeting-variant
  notify_title: Example 1
  notify_message: Send a persistent notification, that the user can't remove
  data_group: Example
  data_tag: Use this tag when you need to remove it
  data_importance: high
  data_sticky: true
  data_persistent: true
```
### Example 2: Send a clean_persistent notification, so the notification is removed
```
service: script.notify_devices
data:
  notify_home_or_away: Both
  data_tag: Use this tag when you need to remove it
  data_importance: high
  notify_message: clear_notification
```
### Example 3: Send a persistent notification, that the user can’t remove, but automatically is removed
```
service: script.notify_devices
data:
  notify_home_or_away: Both
  data_notification_icon: mdi:human-greeting-variant
  notify_title: Example 3
  notify_message: Send a persistent notification, that the user can't remove
  data_group: Example
  data_tag: Use this tag when you need to remove it
  data_importance: high
  data_sticky: true
  data_persistent: true
  data_timeout: 30
```

### Example 4 ###

Send a notification with camera image file. The data_camera can accept various
options, but the results are different for Android or iOS.


**More information:**
 * https://companion.home-assistant.io/docs/notifications/notification-attachments
 * https://www.home-assistant.io/integrations/nest/

**Examples:**
 * data_camera: camera.name
 * data_camera: /api/nest/event_media/{\{trigger.event.data.device_id}}/{\{trigger.event.data.nest_event_id}}/thumbnail
 * data_camera: /api/nest/event_media/{\{trigger.event.data.device_id}}/{\{trigger.event.data.nest_event_id}}
 * data_camera: /media/local/video.mp4
 * data_camera: https://example.com/video.mp4
 * data_camera: /media/local/photo.jpg
 * data_camera: https://example.com/image.jpg
 * data_camera: /media/local/audio.mp3
 * data_camera: https://example.com/audio.mp3

```
service: script.notify_devices
data:
  notify_home_or_away: Both
  data_notification_icon: mdi:cctv
  notify_title: Camera
  notify_message: Send a camera picture notification
  data_group: Camera
  data_importance: high
  data_sticky: true
  data_camera: camera.name
```
 Sends a Nest camera image file 
```
service: script.notify_devices
data:
  notify_home_or_away: Both
  data_notification_icon: mdi:cctv
  notify_title: Camera
  notify_message: Send a Nest camera picture notification
  data_group: Camera
  data_importance: high
  data_sticky: true
  data_camera: >- 
    /api/nest/event_media/{{trigger.event.data.device_id}}/{{trigger.event.data.nest_event_id}}/thumbnail
```


### Example 5 ###

Actionable notifications are a unique type of notification as they allow the user to add buttons to the notification which can then send an event to Home Assistant once clicked. This event can then be used in an automation allowing you to perform a wide variety of actions. These notifications can be sent to either iOS or Android.


**Some useful examples of actionable notifications:**
 * A notification is sent whenever motion is detected in your home while you're away or asleep. A "Sound Alarm" action button is displayed alongside the notification, that when tapped, will sound your burglar alarm.
 * Someone rings your front doorbell. You receive a notification with a live camera stream of the visitor outside along with action buttons to lock or unlock your front door.
 * Receive a notification whenever your garage door opens with action buttons to open or close the garage.

Click [here](https://companion.home-assistant.io/docs/notifications/actionable-notifications#building-actionable-notifications) for the documentation


**This script filters the actions per device. This means you can send information to Android and iOS device simultaneously 🙂 .**

```
service: script.notify_devices
data:
  notify_home_or_away: Both
  data_notification_icon: mdi:gesture-tap-button
  notify_title: Actionable Notifications
  notify_message: Send a notification with actions to Android and iOS device
  data_group: Notifications
  data_importance: high
  data_actions:
    - action: ALARM
      # This is visible on both devices
      title: Sound Alarm
    - action: SILENCE
      # This is visible on both devices, but the icons are only shown on iOS devices
      title: Silence Alarm
      icon: sfsymbols:bell.slash
    - action: URI
      # This is visible on both devices
      title: Open Url
      uri: https://google.com
    - action: "URI"
      # This is only visible on Android devices
      title: "Open an Android app"
      # Name of package for application you would like to open
      uri: "app://com.twitter.android"
    - action: "OPEN"
      # This is only visible on iOS devices
      title: "Open iOS URL"
      uri: "https://google.com"
    - action: "CALL"
      # This is only visible on iOS devices
      title: "Call - iOS only"
      uri: "tel:2125551212"
```

I hope that it helps you as much as it did for me :robot:. Happy notifying!
