# API Documentation

## Geolocation Options

The following **Options** can all be provided to the plugin's `#configure` method:

```
bgGeo.configure(successFn, failureFn, {
	desiredAccuracy: 0,
	distanceFilter: 50,
	.
	.
	.
});

// Use #setConfig if you need to change options after you've executed #configure

bgGeo.setConfig(function() {
	console.log('set config success');
}, {
	console.log('failed to setConfig');
}, {
	desiredAccuracy: 10,
	distanceFilter: 10
});

```

| Option | Type | Opt/Required | Default | Note |
|---|---|---|---|---|
| [`desiredAccuracy`](#param-integer-desiredaccuracy-0-10-100-1000-in-meters) | `Integer` | Required | 0 | Specify the desired-accuracy of the geolocation system with 1 of 4 values, `0`, `10`, `100`, `1000` where `0` means **HIGHEST POWER, HIGHEST ACCURACY** and `1000` means **LOWEST POWER, LOWEST ACCURACY** |
| [`distanceFilter`](#param-integer-distancefilter) | `Integer` | Required | `30`| The minimum distance (measured in meters) a device must move horizontally before an update event is generated. @see Apple docs. However, #distanceFilter is elastically auto-calculated by the plugin: When speed increases, #distanceFilter increases; when speed decreases, so does distanceFilter (disabled with `disableElasticity: true`) |
| [`stopAfterElapsedMinutes`](#param-integer-stopafterelapsedminutes) | `Integer`  |  Optional | `0`  | The plugin can optionally auto-stop monitoring location when some number of minutes elapse after being the #start method was called. |
| [`stationaryRadius`](#param-integer-stationaryradius-meters) | `Integer`  |  Required (**iOS**)| `20`  | When stopped, the minimum distance the device must move beyond the stationary location for aggressive background-tracking to engage. Note, since the plugin uses iOS significant-changes API, the plugin cannot detect the exact moment the device moves out of the stationary-radius. In normal conditions, it can take as much as 3 city-blocks to 1/2 km before staionary-region exit is detected. |
| [`disableElasticity`](#param-boolean-disableelasticity-false) | `bool`  |  Optional (**iOS**)| `false`  | Set true to disable automatic speed-based `#distanceFilter` elasticity. eg: When device is moving at highway speeds, locations are returned at ~ 1 / km. |
| [`activityType`](#param-string-activitytype-automotivenavigation-othernavigation-fitness-other) | `String` | Required (**iOS**)| `Other` | Presumably, this affects ios GPS algorithm.  See [Apple docs](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instp/CLLocationManager/activityType) for more information | Set the desired interval for active location updates, in milliseconds. |
| [`useSignificantChangesOnly`](#param-boolean-usesignificantchangesonly-false) | `Boolean` | Optional (**iOS**)| `false` | Defaults to `false`.  Set `true` in order to disable constant background-tracking and use only the iOS [Significant Changes API](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CLLocationManager_Class/index.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges).  If Apple has denied your application due to background-tracking, this can be a solution.  **NOTE** The Significant Changes API will report a location only when a significant change from the last location has occurred.  Many of the configuration parameters **will be ignored**, such as `#distanceFilter`, `#stationaryRadius`, `#activityType`, etc. |
| [`pausesLocationUpdatesAutomatically`](#param-boolean-pauseslocationupdatesautomatically) | `Boolean` | Optional (**iOS**)| `true` | The default behaviour of the plugin is to turn off location-services automatically when the device is detected to be stationary.  When set to `false`, location-services will **never** be turned off (and `disableStopDetection` will automatically be set to `true`) -- it's your responsibility to turn them off when you no longer need to track the device.  This feature should **not** generally be used.  `preventSuspend` will no longer work either.| 
| [`locationAuthorizationRequest`](#param-boolean-locationauthorizationrequest) | `Always`,`WhenInUse` | Optional (**iOS**)| `Always` | The desired iOS location-authorization request, either `Always` or `WhenInUse`.  You'll have to edit the corresponding key in your app's `Info.plist`, `NSLocationAlwaysUsageDescription` or `NSWhenInUseUsageDescription`.  `WhenInUse` will display a **blue bar** at top-of-screen informing user that location-services are on. |

## Activity Recognition Options

| Option | Type | Opt/Required | Default | Note |
|---|---|---|---|---|
| [`activityRecognitionInterval`](#param-integer-millis-10000-activityrecognitioninterval) | `Integer` | Required | `10000` | The desired time between activity detections. Larger values will result in fewer activity detections while improving battery life. A value of 0 will result in activity detections at the fastest possible rate. |
| [`stopTimeout`](#param-integer-minutes-stoptimeout) | `Integer` | Required | `5 minutes` | The number of miutes to wait before turning off the GPS after the ActivityRecognition System (ARS) detects the device is `STILL` (**Android:** defaults to 0, no timeout, **iOS:** defaults to 5min).  If you don't set a value, the plugin is eager to turn off the GPS ASAP.  An example use-case for this configuration is to delay GPS OFF while in a car waiting at a traffic light.  **WARNING** Setting a value > 15 min is **not** recommended, particularly for Android.|
| [`minimumActivityRecognitionConfidence`](#param-integer-millis-minimumactivityrecognitionconfidence) | `Integer` | Optional (**Android**)| `80` | Each activity-recognition-result returned by the API is tagged with a "confidence" level expressed as a %.  You can set your desired confidence to trigger a state-change.  Defaults to `80`.|
| [`stopDetectionDelay`](#param-integer-minutes-stopdetectiondelay-0) | `Integer` | Optional (**iOS**)| 0 | Allows the stop-detection system to be delayed from activating.  When the stop-detection system is engaged, the GPS is off and only the accelerometer is monitored.  Stop-detection will only engage if this timer expires.  The timer is cancelled if any movement is detected before expiration | 
| [`disableMotionActivityUpdates`](#param-boolean-disablemotionactivityupdates) | `Boolean` | Optional (**iOS**)| 0 | Disable iOS motion-activity updates (eg: "walking", "in_vehicle").  This feature requires a device having the **M7** co-processor (ie: iPhone 5s and up).  **NOTE** This feature will ask the user for "Health updates".  If you do not wish to ask the user for the "Health updates", set this option to `false`; However, you will no longer recieve activity data in the recorded locations. |
| [`disableStopDetection`](#param-boolean-disablestopdetection-false) | `Boolean` | Optional (**iOS**)| `false` | Disable iOS accelerometer-based **Stop-detection System**.  When disabled, the plugin will use the default iOS behaviour of automatically turning off location-services when the device has stopped for exactly 15 minutes.  When disabled, you will no longer have control over `stopTimeout`| 

## HTTP / Persistence Options

| Option | Type | Opt/Required | Default | Note |
|---|---|---|---|---|
| [`url`](#param-string-url-undefined) | `String` | Optional | - | Your server url where you wish to HTTP POST recorded locations to |
| [`params`](#param-object-params) | `Object` | Optional | `{}` | Optional HTTP params sent along in HTTP request to above `#url` |
| [`extras`](#param-object-extras) | `Object` | Optional | | Optional `{}` to attach to each recorded location |
| [`headers`](#param-object-headers) | `Object` | Optional | `{}` | Optional HTTP headers sent along in HTTP request to above `#url` |
| [`method`](#param-string-method-post) | `String` | Optional | `POST` | The HTTP method.  Defaults to `POST`.  Some servers require `PUT`.
| [`autoSync`](#param-string-autosync-true) | `Boolean` | Optional | `true` | If you've enabeld HTTP feature by configuring an `#url`, the plugin will attempt to HTTP POST each location to your server **as it is recorded**.  If you set `autoSync: false`, it's up to you to **manually** execute the `#sync` method to initate the HTTP POST (**NOTE** The plugin will continue to persist **every** recorded location in the SQLite database until you execute `#sync`). |
| [`batchSync`](#param-string-batchsync-false) | `Boolean` | Optional | `false` | Default is `false`.  If you've enabled HTTP feature by configuring an `#url`, `batchSync: true` will POST all the locations currently stored in native SQLite datbase to your server in a single HTTP POST request.  With `batchSync: false`, an HTTP POST request will be initiated for **each** location in database. |
| [`maxBatchSize`](#param-integer-maxbatchsize-undefined) | `Integer` | Optional | `undefined` | If you've enabled HTTP feature by configuring an `#url` and `batchSync: true`, this parameter will limit the number of records attached to each batch.  If the current number of records exceeds the `maxBatchSize`, multiple HTTP requests will be generated until the location queue is empty. |
| [`maxDaysToPersist`](#param-integer-maxdaystopersist) | `Integer` | Optional | `1` |  Maximum number of days to store a geolocation in plugin's SQLite database when your server fails to respond with `HTTP 200 OK`.  The plugin will continue attempting to sync with your server until `maxDaysToPersist` when it will give up and remove the location from the database. |
| [`maxRecordsToPersist`](#param-integer-maxrecordstopersist--1) | `Integer` | Optional | `null` |  Maximum number of records to persist in plugin's SQLite database.|

## Application Options

| Option | Type | Opt/Required | Default | Note |
|---|---|---|---|---|
| [`debug`](#param-boolean-debug-false) | `Boolean` | Optional | `false` | When enabled, the plugin will emit sounds for life-cycle events of background-geolocation!  **NOTE iOS**:  In addition, you must manually enable the *Audio and Airplay* background mode in *Background Capabilities* to hear these debugging sounds. |
| [`stopOnTerminate`](#param-boolean-stoponterminate-true) | `Boolean` | Optional | `true` | Enable this in order to force a stop() when the application terminated (e.g. on iOS, double-tap home button, swipe away the app). On Android, stopOnTerminate: false will cause the plugin to operate as a headless background-service (in this case, you should configure an #url in order for the background-service to send the location to your server) |
| [`startOnBoot`](#param-boolean-startonboot-false) | `Boolean` | Optional | `true` | Set to `true` to enable background-tracking after the device reboots. |
| [`preventSuspend`](#param-boolean-preventsuspend-false) | `Boolean` | Optional **iOS** | `false` | Enable this to prevent **iOS** from suspending.  Must be used in conjunction with a `heartbeatInterval`.  **WARNING**: `preventSuspend` should only be used in **very** specific use-cases and should typically **not** be used as it will have a **very serious impact on battery performance.** |
| [`heartbeatInterval`](#param-integer-heartbeatinterval-60) | `Integer(seconds)` | Optional **iOS** | `60` | Used in conjunction with `preventSuspend`, an **iOS** app can continue to monitor the accelerometer while in the **stationary-state**.  If the *slightest* movement is detected during a `hearbeatInterval`, the plugin will request a high-accuracy location in order to determine if the device has begun moving.  If the plugin *is* moving, it will immediately switch state to **moving-state**.|

## Events

| Event Name | Notes
|---|---|
| [`onMotionChange`](#onmotionchangecallbackfn-failurefn) | Fired when the device changes stationary / moving state. |
| [`onGeofence`](#ongeofencecallbackfn) | Fired when a geofence crossing event occurs |
| [`onHttp`](#onhttpsuccessfn-failurefn) | Fired after a successful HTTP response. `response` object is provided with `status` and `responseText`|
| [`onHeartbeat`](#onheartbeatsuccessfn-failurefn) | **iOS only** Fired each `heartbeatInterval` while the plugin is in the **stationary** state with `preventSuspend: true`.  Your callback will be provided with a `params {}` containing the parameters `shakes {Integer}` as well as the current `location {Object}` |

## Methods

| Method Name | Arguments | Notes
|---|---|---|
| [`configure`](#configurelocationcallback-failurecallback-config) | `successFn`, `failureFn`, `{config}` | Configures the plugin's parameters (@see following Config section for accepted config params. The locationCallback will be executed each time a new Geolocation is recorded and provided with the following parameters |
| [`setConfig`](#setconfigsuccessfn-failurefn-config) | `successFn`, `failureFn`, `{config}` | Re-configure the plugin with new values |
| [`start`](#startsuccessfn-failurefn) | `callbackFn`| Enable location tracking.  Supplied `callbackFn` will be executed when tracking is successfully engaged |
| [`stop`](#stopsuccessfn-failurefn) | `callbackFn` | Disable location tracking.  Supplied `callbackFn` will be executed when tracking is successfully engaged |
| [`getState`](#getstatesuccessfn) | `callbackFn` | Fetch the current-state of the plugin, including `enabled`, `isMoving`, as well as all other config params |
| [`getCurrentPosition`](#getcurrentpositionsuccessfn-failurefn-options) | `successFn`, `failureFn`, `{options} | Retrieves the current position. This method instructs the native code to fetch exactly one location using maximum power & accuracy. |
| [`changePace`](#changepaceenabled-successfn-failurefn) | `isMoving` | Initiate or cancel immediate background tracking. When set to true, the plugin will begin aggressively tracking the devices Geolocation, bypassing stationary monitoring. If you were making a "Jogging" application, this would be your [Start Workout] button to immediately begin GPS tracking. Send false to disable aggressive GPS monitoring and return to stationary-monitoring mode. |
| [`getLocations`](#getlocationscallbackfn-failurefn) | `callbackFn` | Fetch all the locations currently stored in native plugin's SQLite database. Your callbackFn`` will receive an `Array` of locations in the 1st parameter |
| [`getCount`](#getcountcallbackfn-failurefn) | `callbackFn` | Fetches count of SQLite locations table `SELECT count(*) from locations` |
| [`clearDatabase`](#cleardatabasecallbackfn-failurefn) | `callbackFn` | Delete all records in plugin's SQLite database |
| [`sync`](#synccallbackfn-failurefn) | - | If the plugin is configured for HTTP with an `#url` and `#autoSync: false`, this method will initiate POSTing the locations currently stored in the native SQLite database to your configured `#url`|
| [`getOdometer`](#getodometercallbackfn-failurefn) | `callbackFn` | The plugin constantly tracks distance travelled. The supplied callback will be executed and provided with a `distance` as the 1st parameter.|
| [`resetOdometer`](#resetodometercallbackfn-failurefn) | `callbackFn` | Reset the **odometer** to `0`.  The plugin never automatically resets the odometer -- this is **up to you** |
| [`playSound`](#playsoundsoundid) | `soundId` | Here's a fun one.  The plugin can play a number of OS system sounds for each platform.  For [IOS](http://iphonedevwiki.net/index.php/AudioServices) and [Android](http://developer.android.com/reference/android/media/ToneGenerator.html).  I offer this API as-is, it's up to you to figure out how this works. |
| [`addGeofence`](#addgeofenceconfig-callbackfn-failurefn) | `{config}` | Adds a geofence to be monitored by the native plugin. Monitoring of a geofence is halted after a crossing occurs.|
| [`addGeofences`](#addgeofencesgeofences-callbackfn-failurefn) | `{geofences}` | Adds a list geofences to be monitored by the native plugin. Monitoring of a geofence is halted after a crossing occurs.|
| [`removeGeofence`](#removegeofenceidentifier-callbackfn-failurefn) | `identifier` | Removes a geofence identified by the provided `identifier` |
| [`removeGeofences`](#removegeofences-callbackfn-failurefn) |  | Removes all geofences |
| [`getGeofences`](#getgeofencescallbackfn-failurefn) | `callbackFn` | Fetch the list of monitored geofences. Your callbackFn will be provided with an Array of geofences. If there are no geofences being monitored, you'll receive an empty `Array []`.|
| [`getLog`](#getlogcallbackfn) | `callbackFn` | **Android ONLY**.  Fetch the entire contents of the current Android circular log and return it as a String.|

# Geolocation Options

## Common Options

####`@param {Integer} desiredAccuracy [0, 10, 100, 1000] in meters`

Specify the desired-accuracy of the geolocation system with 1 of 4 values, ```0, 10, 100, 1000``` where ```0``` means HIGHEST POWER, HIGHEST ACCURACY and ```1000``` means LOWEST POWER, LOWEST ACCURACY

- [Android](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html#PRIORITY_BALANCED_POWER_ACCURACY)
- [iOS](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CLLocationManager_Class/index.html#//apple_ref/occ/instp/CLLocationManager/desiredAccuracy) 

####`@param {Integer} distanceFilter`

The minimum distance (measured in meters) a device must move horizontally before an update event is generated.  @see [Apple docs](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instp/CLLocationManager/distanceFilter).  However, #distanceFilter is elastically auto-calculated by the plugin:  When speed increases, #distanceFilter increases;  when speed decreases, so does distanceFilter.

distanceFilter is calculated as the square of speed-rounded-to-nearest-5 and adding configured #distanceFilter.

  `(round(speed, 5))^2 + distanceFilter`

For example, at biking speed of 7.7 m/s with a configured distanceFilter of 30m:

  `=> round(7.7, 5)^2 + 30`
  `=> (10)^2 + 30`
  `=> 100 + 30`
  `=> 130`

A gps location will be recorded each time the device moves 130m.

At highway speed of 30 m/s with distanceFilter: 30,

  `=> round(30, 5)^2 + 30`
  `=> (30)^2 + 30`
  `=> 900 + 30`
  `=> 930`

A gps location will be recorded every 930m

Note the following real example of background-geolocation on highway 101 towards San Francisco as the driver slows down as he runs into slower traffic (geolocations become compressed as distanceFilter decreases)

![distanceFilter at highway speed](https://dl.dropboxusercontent.com/u/2319755/cordova-background-geolocaiton/distance-filter-highway.png)

Compare now background-geolocation in the scope of a city.  In this image, the left-hand track is from a cab-ride, while the right-hand track is walking speed.

![distanceFilter at city scale](https://dl.dropboxusercontent.com/u/2319755/cordova-background-geolocaiton/distance-filter-city.png)

####`@param {Integer} stopAfterElapsedMinutes`

The plugin can optionally auto-stop monitoring location when some number of minutes elapse after being the #start method was called.

## iOS Options

####`@param {Integer} stationaryRadius (meters)`

When stopped, the minimum distance the device must move beyond the stationary location for aggressive background-tracking to engage.  Note, since the plugin uses iOS significant-changes API, the plugin cannot detect the exact moment the device moves out of the stationary-radius.  In normal conditions, it can take as much as 3 city-blocks to 1/2 km before staionary-region exit is detected.

####`@param {Boolean} disableElasticity [false]`

Defaults to ```false```.  Set ```true``` to disable automatic speed-based ```#distanceFilter``` elasticity.  eg:  When device is moving at highway speeds, locations are returned at ~ 1 / km.

####`@param {String} activityType [AutomotiveNavigation, OtherNavigation, Fitness, Other]`

Presumably, this affects ios GPS algorithm.  See [Apple docs](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instp/CLLocationManager/activityType) for more information

####`@param {Boolean} useSignificantChangesOnly [false]`

Defaults to `false`.  Set `true` in order to disable constant background-tracking and use only the iOS [Significant Changes API](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CLLocationManager_Class/index.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges).  If Apple has denied your application due to background-tracking, this can be a solution.  **NOTE** The Significant Changes API will report a location only when a significant change from the last location has occurred.  Many of the configuration parameters **will be ignored**, such as `#distanceFilter`, `#stationaryRadius`, `#activityType`, etc.

####`@param {Boolean} disableMotionActivityUpdates [false]`

Set `true` to isable iOS `CMMotionActivity` updates (eg: "walking", "in_vehicle").  This feature requires a device having the **M7** co-processor (ie: iPhone 5s and up).  **NOTE** This feature will ask the user for "Health updates".  If you do not wish to ask the user for the "Health updates", set this option to `false`; However, you will no longer recieve activity data in the recorded locations.

## Android Options

####`@param {Integer millis} locationUpdateInterval`

Set the desired interval for active location updates, in milliseconds.

The location client will actively try to obtain location updates for your application at this interval, so it has a direct influence on the amount of power used by your application. Choose your interval wisely.

This interval is inexact. You may not receive updates at all (if no location sources are available), or you may receive them slower than requested. You may also receive them faster than requested (if other applications are requesting location at a faster interval). 

Applications with only the coarse location permission may have their interval silently throttled.

####`@param {Integer millis} fastestLocationUpdateInterval`

Explicitly set the fastest interval for location updates, in milliseconds.

This controls the fastest rate at which your application will receive location updates, which might be faster than ```#locationUpdateInterval``` in some situations (for example, if other applications are triggering location updates).

This allows your application to passively acquire locations at a rate faster than it actively acquires locations, saving power.

Unlike ```#locationUpdateInterval```, this parameter is exact. Your application will never receive updates faster than this value.

If you don't call this method, a fastest interval will be set to **30000 (30s)**. 

An interval of 0 is allowed, but not recommended, since location updates may be extremely fast on future implementations.

If ```#fastestLocationUpdateInterval``` is set slower than ```#locationUpdateInterval```, then your effective fastest interval is ```#locationUpdateInterval```.

========
An interval of 0 is allowed, but not recommended, since location updates may be extremely fast on future implementations.

####`@param {String} triggerActivities`

These are the comma-delimited list of [activity-names](https://developers.google.com/android/reference/com/google/android/gms/location/DetectedActivity) returned by the `ActivityRecognition` API which will trigger a state-change from **stationary** to **moving**.  By default, this list is set to all five **moving-states**:  `"in_vehicle, on_bicycle, on_foot, running, walking"`.  If you wish, you could configure the plugin to only engage **moving-mode** for vehicles by providing only `"in_vehicle"`.

# Activity Recognition Options

## Common Options

####`@param {Integer millis} [10000] activityRecognitionInterval`

Defaults to `10000` (10 seconds).  The desired time between activity detections. Larger values will result in fewer activity detections while improving battery life. A value of 0 will result in activity detections at the fastest possible rate.

####`@param {Integer millis} minimumActivityRecognitionConfidence` 

Each activity-recognition-result returned by the API is tagged with a "confidence" level expressed as a %.  You can set your desired confidence to trigger a state-change.  Defaults to `80`.

####`@param {Integer minutes} stopTimeout`

The number of miutes to wait before turning off the GPS after the ActivityRecognition System (ARS) detects the device is `STILL` (**Android:** defaults to 0, no timeout, **iOS:** defaults to 5min).  If you don't set a value, the plugin is eager to turn off the GPS ASAP.  An example use-case for this configuration is to delay GPS OFF while in a car waiting at a traffic light.  **WARNING** Setting a value > 15 min is **not** recommended, particularly for Android.

**iOS Stop-detection timing**.  
![](https://dl.dropboxusercontent.com/u/2319755/cordova-background-geolocaiton/ios-stop-detection-timing.png)

## iOS Options

####`@param {Integer minutes} stopDetectionDelay [0]` 

Allows the stop-detection system to be delayed from activating.  When the stop-detection system is engaged, the GPS is off and only the accelerometer is monitored.  Stop-detection will only engage if this timer expires.  The timer is cancelled if any movement is detected before expiration.  If a value of `0` is specified, the stop-detection system will engage as soon as the device is detected to be stationary.

####`@param {Boolan} disableMotionActivityUpdates [false]`

Prevent iOS Motion Activity updates.  If you're seeing your app request permission to see "Fitness Data", this is why.  The **iOS** plugin will no longer record `activity` data in the recorded locations.

# HTTP / Persistence Options

####`@param {String} url [undefined]`

Your server url where you wish to HTTP POST location data to.

####`@param {String} method [POST]`

The HTTP method to use when creating an HTTP request to your configured `#url`.  Defaults to `POST`.  Valid values are `POST`, `PUT` and `OPTIONS`.

####`@param {String} batchSync [false]`

Default is ```false```.  If you've enabled HTTP feature by configuring an ```#url```, ```batchSync: true``` will POST all the locations currently stored in native SQLite datbase to your server in a single HTTP POST request.  With ```batchSync: false```, an HTTP POST request will be initiated for **each** location in database.

####`@param {Integer} maxBatchSize [undefined]`

If you've enabled HTTP feature by configuring an `#url` with `batchSync: true`, this parameter will limit the number of records attached to **each** batch request.  If the current number of records exceeds the `maxBatchSize`, multiple HTTP requests will be generated until the location queue is empty.

####`@param {String} autoSync [true]`

Default is ```true```.  If you've enabeld HTTP feature by configuring an ```#url```, the plugin will attempt to HTTP POST each location to your server **as it is recorded**.  If you set ```autoSync: false```, it's up to you to **manually** execute the ```#sync``` method to initate the HTTP POST (**NOTE** The plugin will continue to persist **every** recorded location in the SQLite database until you execute ```#sync```).

####`@param {Object} params`

Optional HTTP params sent along in HTTP request to above ```#url```.

####`@param {Object} headers`

Optional HTTP params sent along in HTTP request to above ```#url```.

####`@param {Object} extras`

Optional arbitrary key/value `{}` to attach to each recorded location

Eg: Every recorded location will have the following `extras` appended:
```
bgGeo.configure(success, fail, {
  .
  .
  .
  extras: {route_id: 1234}
});
```

####`@param {Integer} maxDaysToPersist [1]`

Maximum number of days to store a geolocation in plugin's SQLite database when your server fails to respond with ```HTTP 200 OK```.  The plugin will continue attempting to sync with your server until ```maxDaysToPersist``` when it will give up and remove the location from the database.

####`@param {Integer} maxRecordsToPersist [-1]`

Maximum number of records to persist in plugin's SQLite database.

# Application Options

## Common Options

####`@param {Boolean} debug [false]`

When enabled, the plugin will emit sounds for life-cycle events of background-geolocation!  **NOTE iOS**:  In addition, you must manually enable the *Audio and Airplay* background mode in *Background Capabilities* to hear these [debugging sounds](../../../wiki/Debug-Sounds).  See the ../../../wiki [Debug Sounds](wiki/Debug-Sounds) for a detailed description of these sounds.

####`@param {Boolean} stopOnTerminate [true]`
Enable this in order to force a stop() when the application terminated (e.g. on iOS, double-tap home button, swipe away the app).  On Android, ```stopOnTerminate: false``` will cause the plugin to operate as a headless background-service (in this case, you should configure an #url in order for the background-service to send the location to your server)

####`@param {Boolean} startOnBoot [false]`

Set to `true` to enable background-tracking after the device reboots.

**iOS** 
iOS cannot immediately engage tracking after a device reboot since it requires either a "significant-change" event or geofence exit before iOS will awaken your app.  One can also use the [background-fetch plugin](https://github.com/christocracy/cordova-plugin-background-fetch) to *at least* awaken your app within 15 min of being rebooted.

## iOS Options

####`@param {Boolean} preventSuspend [false]`

Enable this to prevent **iOS** from suspending after location-services have been switch off.  Must be used in conjunction with a `heartbeatInterval`.  **WARNING**: `preventSuspend` should **only** be used in **very** specific use-cases and should typically **not** be used as it will have a **very serious impact on battery performance.**

####`@param {Integer} heartbeatInterval [60]`

Used in conjunction with `preventSuspend`, an **iOS** app can continue to monitor the accelerometer while in the **stationary-state** (ie: after location-services have been turned off).  If the *slightest* movement is detected during a `hearbeatInterval`, the plugin will request a high-accuracy location in order to determine if the device has begun moving.  If the device *is* moving, it will immediately switch state to **moving-state**.

# Events

####`onMotionChange(callbackFn, failureFn)`
Your ```callbackFn``` will be executed each time the device has changed-state between **MOVING** or **STATIONARY**.  The ```callbackFn``` will be provided with a ```Location``` object as the 1st param, with the usual params (```latitude, longitude, accuracy, speed, bearing, altitude```), in addition to a ```taskId``` used to signal that your callback is finished.

######@param {Boolean} isMoving `false` if entered **STATIONARY** mode; `true` if entered **MOVING** mode.
######@param {Object} location The location at the state-change.
######@param {Integer} taskId The taskId used to send to bgGeo.finish(taskId) in order to signal completion of your callbackFn

```
bgGeo.onMotionChange(function(isMoving, location, taskId) {
    if (isMoving) {
        console.log('Device has just started MOVING', location);
    } else {
        console.log('Device has just STOPPED', location);
    }
    bgGeo.finish(taskId);
})

```

####`onGeofence(callbackFn)`
Adds a geofence event-listener.  Your supplied callback will be called when any monitored geofence crossing occurs.  The `callbackFn` will be provided the following parameters:

######@param {Object} params.  This object contains 2 keys: `@param {String} identifier`, `@param {String} action [ENTER|EXIT]` and `@param {Object} location`.
######@param {Integer} taskId The background taskId which you must send back to the native plugin via `bgGeo.finish(taskId)` in order to signal that your callback is complete.

```
bgGeo.onGeofence(function(params, taskId) {
    try {
        var location = params.location;
        var identifier = params.identifier;
        var action = params.action;
        
        console.log('A geofence has been crossed: ', identifier);
        console.log('ENTER or EXIT?: ', action);
        console.log('location: ', JSON.stringify(location));
    } catch(e) {
        console.error('An error occurred in my application code', e);
    }
    // The plugin runs your callback in a background-thread:  
    // you MUST signal to the native plugin when your callback is finished so it can halt the thread.
    // IF YOU DON'T, iOS WILL KILL YOUR APP
    bgGeo.finish(taskId);
});
```

####`onHttp(successFn, failureFn)`

The `successFn` will be executed for each successful HTTP request.  `failureFn` will be executed on HTTP failure.  The `successFn` and `failureFn` will be provided a single `response {Object}` parameter with the following properties:

######@param {Integer} status.  The HTTP status
######@param {String} responseText The HTTP response as text.

Example:
```
bgGeo.onHttp(function(response) {
	var status = response.status;
	var responseText = response.responseText;
	var res = JSON.parse(responseText);  // <-- if your server returns JSON

	console.log("- HTTP success", status, res);

}, function(response) {
	var status = response.status;
	var responseText = response.responseText;
	console.log("- HTTP failure: ", status, responseText);
})
```

####`onHeartbeat(successFn, failureFn)`

The `successFn` will be executed for each `heartbeatInterval` while the **iOS** app is in **stationary** mode with `{preventSuspend: true}`.  **NOTE** The plugin will **not record any data** while in `preventSuspend` mode -- if you wish to record any data during, you have to do so manually using the `insertLocation` method.  The `successFn` will be provided a single `params {Object}` parameter with the following properties:

######@param {Integer} shakes.  A measure of the device movement.  Shakes is a measure of accelerometer data crossing over a threshold where the device is decided to be moving.  The higher the shakes, the more the device is moving.  When shakes is **0**, the device is completely still.
######@param {Object} location.  When the plugin detects `shakes > 0`, it will always request a new high-accuracy location in order to determine if the device has moved beyond `stationaryRadius` and if the location has `speed > 0`.  This fresh location will be provided to your `successFn`.  If `shakes == 0`, the current **stationary location** will be provided.

Example:
```
bgGeo.onHeartbeat(function(params) {
	console.log('- hearbeat');

	var shakes = params.shakes;
	var location = params.location;

	// Attach some arbitrary data to the location extras.
	location.extras = {
		foo: 'bar',
		shakes: shakes
	};

	// You can manually insert a location if you wish.
	bgGeo.insertLocation(location, function() {
    	console.log('- inserted location during heartbeat');
	});

}, function(response) {
	var status = response.status;
	var responseText = response.responseText;
	console.log("- HTTP failure: ", status, responseText);
})
```

# Methods

####`configure(locationCallback, failureCallback, config)`

Configures the plugin's parameters (@see following [Config](https://github.com/transistorsoft/cordova-background-geolocation/blob/edge/README.md#config) section for accepted ```config``` params.  The ```locationCallback``` will be executed each time a new Geolocation is recorded and provided with the following parameters:

######@param {Object} location The Location data
######@param {Integer} taskId The taskId used to send to bgGeo.finish(taskId) in order to signal completion of your callbackFn

```
bgGeo.configure(function(location, taskId) {
    try {
        var coords      = location.coords,
            timestamp   = location.timestamp
            latitude    = coords.latitude,
            longitude   = coords.longitude,
            speed       = coords.speed;

        console.log("A location has arrived:", timestamp, latitude, longitude, speed);
    } catch(e) {
        console.error("An error occurred in my application code", e);
    }
    // The plugin runs your callback in a background-thread:  
    // you MUST signal to the native plugin when your callback is finished so it can halt the thread.
    // IF YOU DON'T, iOS WILL KILL YOUR APP
    bgGeo.finish(taskId);
}, failureFn, {
    distanceFilter: 50,
    desiredAccuracy: 0,
    stationaryRadius: 25
});
```

If an error occurs while fetching the location, the `failureFn` will be executed with an `Integer` [Error Code](../../../wiki/Location-Error-Codes) as the first argument.

####`setConfig(successFn, failureFn, config)`
Reconfigure plugin's configuration (@see followign ##Config## section for accepted ```config``` params.  **NOTE** The plugin will continue to send recorded Geolocation to the ```locationCallback``` you provided to ```configure``` method -- use this method only to change configuration params (eg: ```distanceFilter```, ```stationaryRadius```, etc).

```
bgGeo.setConfig(function(){}, function(){}, {
    desiredAccuracy: 10,
    distanceFilter: 100
});
```

####`start(successFn, failureFn)`

Enable background geolocation tracking.

```
bgGeo.start()
```

####`stop(successFn, failureFn)`

Disable background geolocation tracking.

```
bgGeo.stop();
```

####`getState(successFn)`

Fetch the current-state of the plugin, including all configuration parameters.

```
bgGeo.getState(function(state) {
  console.log(JSON.stringify(state));
});

{
  "stopOnTerminate": true,
  "disableMotionActivityUpdates": false,
  "params": {
    "device": {
      "manufacturer": "Apple",
       "available": true,
       "platform": "iOS",
       "cordova": "3.9.1",
       "uuid": "61CA53C7-BC4B-44D3-991B-E9021AE7F8EE",
       "model": "iPhone8,1",
       "version": "9.0.2"
    }
  },
  "url": "http://192.168.11.120:8080/locations",
  "desiredAccuracy": 0,
  "stopDetectionDelay": 0,
  "activityRecognitionInterval": 10000,
  "distanceFilter": 50,
  "activityType": 2,
  "useSignificantChangesOnly": false,
  "autoSync": false,
  "isMoving": false,
  "maxDaysToPersist": 1,
  "stopTimeout": 2,
  "enabled": false,
  "debug": true,
  "batchSync": false,
  "headers": {},
  "disableElasticity": false,
  "stationaryRadius": 20
}
```

####`getCurrentPosition(successFn, failureFn, options)`
Retrieves the current position.  This method instructs the native code to fetch exactly one location using maximum power & accuracy.  The native code will persist the fetched location to its SQLite database just as any other location in addition to POSTing to your configured `#url` (if you've enabled the HTTP features).  In addition to your supplied `callbackFn`, the plugin will also execute the `callback` provided to `#configure`.

If an error occurs while fetching the location, the `failureFn` will be executed with an `Integer` [Error Code](../../../wiki/Location-Error-Codes) as the first argument.

#### Options

######@param {Integer} timeout [30]
An optional location-timeout.  If the timeout expires before a location is retrieved, the `failureFn` will be executed.

######@param {Integer millis} maximumAge [0]
Accept the last-recorded-location if no older than supplied value in milliseconds.

######@param {Integer} minimumAccuracy
Attempt to fetch a location with the supplied minimum accuracy

######@param {Object} extras
Optional extra-data to attach to the location.  These `extras {Object}` will be merged to the recorded `location` and persisted / POSTed to your server (if you've configured the HTTP Layer).

#### Callback

######@param {Object} location The Location data
######@param {Integer} taskId The taskId used to send to bgGeo.finish(taskId) in order to signal completion of your callbackFn

```
bgGeo.getCurrentPosition(function(location, taskId) {
    // This location is already persisted to plugin’s SQLite db.  
    // If you’ve configured #autoSync: true, the HTTP POST has already started.

    console.log(“- Current position received: “, location);
    bgGeo.finish(taskId);
}, function(errorCode) {
    alert('An location error occurred: ' + errorCode);
}, {
  timeout: 30,    // 30 second timeout to fetch location
  maximumAge: 5000,	// Accept the last-known-location if not older than 5000 ms.
  minimumAccuracy: 10,	// Fetch a location with a minimum accuracy of `10` meters.
  extras: {       // [Optional] Attach your own custom `metaData` to this location.  This metaData will be persisted to SQLite and POSTed to your server
    foo: "bar"  
  }
});

```

If a location failed to be retrieved, you `failureFn` will be executed with an error-code parameter

| Error | Reason | Code |
|---|---|---|
| kCLErrorLocationUnknown | Could not fetch location | 0 |
| kCLErrorDenied | The user disabled location-services in Settings | 1 |
| kCLErrorNetwork | Network error | 2 |
| kCLErrorHeadingFailure | - | 3 |
| kCLErrorRegionMonitoringDenied | User disabled region-monitoring in Settings | 4 |
| kCLErrorRegionMonitoringFailure | Installed in a device with no region-monitoring capability | 5 |
| kCLErrorRegionMonitoringSetupDelayed | - | 6 |
| kCLErrorRegionMonitoringResponseDelayed | - | 7 |
| kCLErrorDeferredFailed | - | 11 |
| kCLErrorDeferredNotUpdatingLocation | - | 12 |
| kCLErrorDeferredAccuracyTooLow | - | 13 |
| kCLErrorDeferredDistanceFiltered | - | 14 |
| kCLErrorDeferredCanceled | - | 15 |

Eg:

```
bgGeo.getLocation(succesFn, function(errorCode) {
	switch (errorCode) {
		case 0:
			alert('Failed to retrieve location');
			break;
		case 1:
			alert('You must enable location services in Settings');
			break;

	}
})
```

####`changePace(enabled, successFn, failureFn)`
Initiate or cancel immediate background tracking.  When set to ```true```, the plugin will begin aggressively tracking the devices Geolocation, bypassing stationary monitoring.  If you were making a "Jogging" application, this would be your [Start Workout] button to immediately begin GPS tracking.  Send ```false``` to disable aggressive GPS monitoring and return to stationary-monitoring mode.

```
bgGeo.changePace(true);  // <-- Aggressive GPS monitoring immediately engaged.
bgGeo.changePace(false); // <-- Disable aggressive GPS monitoring.  Engages stationary-mode.
```

####`addGeofence(config, callbackFn, failureFn)`
Adds a geofence to be monitored by the native plugin.  Monitoring of a geofence is halted after a crossing occurs.  The `config` object accepts the following params.

######@config {String} identifier The name of your geofence, eg: "Home", "Office"
######@config {Float} radius The radius (meters) of the geofence.  In practice, you should make this >= 100 meters.
######@config {Float} latitude Latitude of the center-point of the circular geofence.
######@config {Float} longitude Longitude of the center-point of the circular geofence.
######@config {Boolean} notifyOnExit Whether to listen to EXIT events
######@config {Boolean} notifyOnEntry Whether to listen to ENTER events
######@config {Boolean} notifyOnDwell (Android only) Whether to listen to DWELL events
######@config {Integer milliseconds} loiteringDelay (Android only) When `notifyOnDwell` is `true`, the delay before DWELL event is fired after entering a geofence.

```
bgGeo.addGeofence({
    identifier: "Home",
    radius: 150,
    latitude: 45.51921926,
    longitude: -73.61678581,
    notifyOnEntry: true,
    notifyOnExit: false,
    notifyOnDwell: true,
    loiteringDelay: 30000	// <-- 30 seconds
}, function() {
    console.log("Successfully added geofence");
}, function(error) {
    console.warn("Failed to add geofence", error);
});
```

####`addGeofences(geofences, callbackFn, failureFn)`
Adds a list of geofences to be monitored by the native plugin.  Monitoring of a geofence is halted after a crossing occurs.  The `geofences` param is an `Array` of geofence Objects `{}` with the following params:

######@config {String} identifier The name of your geofence, eg: "Home", "Office"
######@config {Float} radius The radius (meters) of the geofence.  In practice, you should make this >= 100 meters.
######@config {Float} latitude Latitude of the center-point of the circular geofence.
######@config {Float} longitude Longitude of the center-point of the circular geofence.
######@config {Boolean} notifyOnExit Whether to listen to EXIT events
######@config {Boolean} notifyOnEntry Whether to listen to ENTER events
######@config {Boolean} notifyOnDwell (Android only) Whether to listen to DWELL events
######@config {Integer milliseconds} loiteringDelay (Android only) When `notifyOnDwell` is `true`, the delay before DWELL event is fired after entering a geofence.

```
bgGeo.addGeofences([{
    identifier: "Home",
    radius: 150,
    latitude: 45.51921926,
    longitude: -73.61678581,
    notifyOnEntry: true,
    notifyOnExit: false,
    notifyOnDwell: true,
    loiteringDelay: 30000   // <-- 30 seconds
}], function() {
    console.log("Successfully added geofence");
}, function(error) {
    console.warn("Failed to add geofence", error);
});
```

####`removeGeofence(identifier, callbackFn, failureFn)`
Removes a geofence having the given `{String} identifier`.

######@config {String} identifier The name of your geofence, eg: "Home", "Office"
######@config {Function} callbackFn successfully removed geofence.
######@config {Function} failureFn failed to remove geofence

```
bgGeo.removeGeofence("Home", function() {
    console.log("Successfully removed geofence");
}, function(error) {
    console.warn("Failed to remove geofence", error);
});
```

####`removeGeofences(callbackFn, failureFn)`
Removes all geofences.

######@config {Function} callbackFn successfully removed geofences.
######@config {Function} failureFn failed to remove geofences

```
bgGeo.removeGeofences(function() {
    console.log("Successfully removed alll geofences");
}, function(error) {
    console.warn("Failed to remove geofence", error);
});
```

####`getGeofences(callbackFn, failureFn)`

Fetch the list of monitored geofences.  Your `callbackFn` will be provided with an `Array` of geofences.  If there are no geofences being monitored, you'll receive an empty Array `[]`.

```
bgGeo.getGeofences(function(geofences) {
    for (var n=0,len=geofences.length;n<len;n++) {
        console.log("Geofence: ", geofence.identifier, geofence.radius, geofence.latitude, geofence.longitude);
    }
}, function(error) {
    console.warn("Failed to fetch geofences from server");
});
```

####`getLocations(callbackFn, failureFn)`
Fetch all the locations currently stored in native plugin's SQLite database.  Your ```callbackFn`` will receive an ```Array``` of locations in the 1st parameter.  Eg:

The `callbackFn` will be executed with following params:

######@param {Array} locations.  The list of locations stored in SQLite database.
######@param {Integer} taskId The background taskId which you must send back to the native plugin via `bgGeo.finish(taskId)` in order to signal the end of your background thread.


```
    bgGeo.getLocations(function(locations, taskId) {
        try {
            console.log("locations: ", locations);
        } catch(e) {
            console.error("An error occurred in my application code");
        }
        bgGeo.finish(taskId);
    });
```

####`getCount(callbackFn, failureFn)`
Fetches count of SQLite locations table `SELECT count(*) from locations`.  The `callbackFn` will be executed with count as the only parameter.

######@param {Integer} count

```
    bgGeo.getCount(function(count) {
        console.log('- count: ', count);
    });
```

####`insertLocation(params, callbackFn, failureFn)`
Manually insert a location into the native plugin's SQLite database.  Your ```callbackFn`` will be executed if the operation was successful.  The inserted location's schema must match this plugin's published [Location Data Schema](wiki/Location-Data-Schema).  The plugin will have no problem inserting a location retrieved from the plugin itself.

######@param {Object} params.  The location params/object matching the [Location Data Schema](wiki/Location-Data-Schema).

```
    bgGeo.insertLocation({
		"uuid": "f8424926-ff3e-46f3-bd48-2ec788c9e761",	// <-- required
		"coords": {										// <-- required
			"latitude": 45.5192746,
			"longitude": -73.616909,
			"accuracy": 22.531999588012695,
			"speed": 0,
			"heading": 0,
			"altitude": 0
		},
		"timestamp": "2016-02-10T22:25:54.905Z"			// <-- required
    }, function() {
        console.log('- Inserted location success');
    }, function(error) {
    	console.warn('- Failed to insert location: ', error);
    });

    // insertLocation can easily consume any location which it returned.  Note that #getCurrentPosition ALWAYS persists so this example
    // will manually persist a 2nd version of the same location.  The purpose here is to show that the plugin can consume any location object which it generated.
    bgGeo.getCurrentPosition(function(location, taskId) {
    	location.extras = {foo: 'bar'};	// <-- add some arbitrary extras-data

    	// Insert it.
    	bgGeo.insertLocation(location, function() {
    		bgGeo.finish(taskId);
    	});
    });
```


####`clearDatabase(callbackFn, failureFn)`
Remove all records in plugin's SQLite database.

```
    bgGeo.clearDatabase(function() {
      console.log('- cleared database'); 
    });
```

####`sync(callbackFn, failureFn)`

If the plugin is configured for HTTP with an ```#url``` and ```#autoSync: false```, this method will initiate POSTing the locations currently stored in the native SQLite database to your configured ```#url```.  All records in the database will be DELETED.  If you configured ```batchSync: true```, all the locations will be sent to your server in a single HTTP POST request, otherwise the plugin will create execute an HTTP post for **each** location in the database (REST-style).  Your ```callbackFn``` will be executed and provided with an Array of all the locations from the SQLite database.  If you configured the plugin for HTTP (by configuring an `#url`, your `callbackFn` will be executed after the HTTP request(s) have completed.  If the plugin failed to sync to your server (possibly because of no network connection), the ```failureFn``` will be called with an ```errorMessage```.  If you are **not** using the HTTP features, ```sync``` is the only way to clear the native SQLite datbase.  Eg:

Your callback will be provided with the following params

######@param {Array} locations.  The list of locations stored in SQLite database.
######@param {Integer} taskId The background taskId which you must send back to the native plugin via `bgGeo.finish(taskId)` in order to signal the end of your background thread.

```
    bgGeo.sync(function(locations, taskId) {
        try {
        	// Here are all the locations from the database.  The database is now EMPTY.
        	console.log('synced locations: ', locations);
        } catch(e) {
            console.error('An error occurred in my application code', e);
        }

        // Be sure to call finish(taskId) in order to signal the end of the background-thread.
        bgGeo.finish(taskId);
    }, function(errorMessage) {
        console.warn('Sync FAILURE: ', errorMessage);
    });

```

####`getOdometer(callbackFn, failureFn)`

The plugin constantly tracks distance travelled.  To fetch the current **odometer** reading:

```
    bgGeo.getOdometer(function(distance) {
        console.log("Distance travelled: ", distance);
    });
```

####`resetOdometer(callbackFn, failureFn)`

Reset the **odometer** to zero.  The plugin never automatically resets the odometer so it's up to you to reset it as desired.

####`playSound(soundId)`

Here's a fun one.  The plugin can play a number of OS system sounds for each platform.  For [IOS](http://iphonedevwiki.net/index.php/AudioServices) and [Android](http://developer.android.com/reference/android/media/ToneGenerator.html).  I offer this API as-is, it's up to you to figure out how this works.

```
    // A soundId iOS recognizes
    bgGeo.playSound(1303);
    
    // An Android soundId
    bgGeo.playSound(90);
```

####`getLog(callbackFn)`

**Android ONLY**.  Fetches the entire contents of the current Android circular-log and return it as a String.

```
	bgGeo.getLog(function(log) {
		console.log(log);
	});
```

