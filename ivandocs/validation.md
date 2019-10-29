# Adobe Experience Platform Location Service
## Recommendations for testing Location Service

As many customers and organizations will define points of interest across the globe, it becomes important to have a way to simulate and test how the Adobe Experience Platform Location Service will interact with your application. 

The guide below will assist in understanding ways to test and validate that Location Service entries and exits are being triggered properly based on points of interest defined and a user's current location. 

As environmental variables can be a factor in location signal and accuracy, it is recommended that you first establish baseline results working locally with developer tooling and simulated location entries. The goal here is to validate that all location events are correctly working. 

Once location events are correctly validated, solution integrations (i.e. Analytics, Target and Campaign) can be tested. Having Slack Webhooks setup with a postback and GPX files loaded into your individual development environment will help in testing.

This plan assumes that Points of Interest have been created in Places.adobe.com and, the latest versions of the Places and Places Monitor extension are installed and configured properly in the application. 

</br>


| ﻿Step 	| Description 	| Expected Result 	|
|------	|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|--------------------------------------------------------------------------------------------------------------------------------------	|
| 1 	| Confirm that the proper manifest keys have been entered for Android to grant access to track location: https://placesdocs.com/places-services-by-adobe-documentation/configure-places-in-the-sdk/places-monitor-extension/using-the-places-monitor-extension#add-permissions-to-the-manifest 	| Confirmed 	|
| 1a 	| Confirm location updates are configured in iOS. Also ensure that you have the appropriate plist keys setup in iOS to request user permission to track location: https://placesdocs.com/places-services-by-adobe-documentation/configure-places-in-the-sdk/places-monitor-extension/using-the-places-monitor-extension#enable-location-updates-in-background 	| Confirmed 	|
| 2 	| Confirm which monitoring mode is set for iOS. Note continuous mode allows for greater accuracy and persistence, but also more heavily drains battery life: https://placesdocs.com/places-services-by-adobe-documentation/configure-places-in-the-sdk/places-monitor-extension/places-monitor-api-reference#monitoring-mode-ios-only 	| Significant Changes or Continuous 	|
| 3 	| If using more than a single Library of POIs, confirm that the appropriate libraries have been selected in the Places extension for Launch 	| Confirmed |
| 4 	| Confirm that the latest versions of Mobile Core and Places / Places Monitor extensions have been bundled with the app via Gradle or CocoaPods	| Confirmed - for more information on recent updates see: https://placesdocs.com/places-services-by-adobe-documentation/release-notes 	|
| 5 	| Confirm that the correct environments are configured for testing. Launch environment ID should match your Launch development environment 	|  Confirmed |
| 6 	| Create GPX files for each POI that you want to test. GPX files can be used in local development environment to simulate a location entry. See links below for information on creating and using GPX files. <br>https://stackoverflow.com/questions/17292783/gpx-files-for-ios-simulator<br>https://mapstogpx.com/mobiledev.php<br>https://blackpixel.com/writing/2013/05/simulating-locations-with-xcode.html<br>https://qacumtester.wordpress.com/2014/02/27/location-testing-in-mobile-apps/ 	| GPX files created and loaded into app project 	|
| 7 	| Without doing anything else, you should be able to launch the application from Android Studio or XCode and see the appropriate alert to request access for tracking location. Grant "Always Allow" permission.<br>*Recommend using a real device connected to your computer over using device simulator.* 	| Location request prompt should display on application loaded through IDE 	|
| 8 	| Once Location permission has been accepted you should see in the application console messages that indicate ```"ACPExtensionEventName" : "requestgetnearbyplaces"```, has been called. Switching between different locations in XCode or Android studio should produce entry events for specific POIs  	| "ACPExtensionEventName" : "requestgetnearbyplaces" should be displayed as you simulate different locations.	|
| 9 	| If the Location you have selected is close to nearby POIs the Monitor extension will start monitoring the 20 closest POIs from the current location. Message in the console will be similar to:```[AdobeExperienceSDK DEBUG <com.adobe.placesMonitor>]: Received a new list of POIs from Places:```| Switching between different locations in XCode or Android studio should produce entry events for specific POIs 	|
| 10 	| Once you see the Places Monitor find nearby POIs - you will want to test sending location pings out. In Launch create a new rule with that uses the Places extension to trigger based on geo-fence entry. Then create a new action using Mobile Core to send a Postback. Creating a Slack Webhook app can be super helpful to see location entries and exits. For information on creating a Slack Webhook app see: https://api.slack.com/messaging/webhooks	|  	|
| 10a 	| In Launch make sure you have added data elements for the Places extension including: <br>Current POI name<br>Current POI lat<br>Current POI long<br>Last Entered name<br>Last Entered lat<br>Last Entered long<br>Last Exited name<br>Last Exited lat<br>Last Exited long<br>Timestamp 	|  	|
| 10b 	| Create a new rule with an Event = Places → Enter POI 	|  	|
| 10c 	| Create an an action = Mobile Core → Postback 	|  	|
| 10d 	| Use the Webhook URL for your Slack app i.e.:  https://hooks.slack.com/services/TKN5FKS68/BNFP7SVD….. 	|  	|
| 10e 	| The post body would be similar to: ```{"text":"User is in POI -  {%%Last Entered POI Name%%} in {%%Last Entered POI City%%} additional information: Radius:{%%Last Entered POI Radius%%} Timestamp: {%%timestamp%%}"}``` <br>Be sure to use the specific data elements you created here. 	|  	|
| 10f 	| Make sure to publish all of the new data element and rule changes in Launch(hint: select a working dev library in the upper right of the Launch interface)	|  	|
| 11 	| Re-launch and re-test your application, flipping between GPX locations in the developer IDE 	| You should now see Slack notifications showing entries for each POI as you select different locations in your development environment. 	|
|  	| **QUICK SUMMARY POINT**<br>All of this testing can be conducted locally without needing to go to a specific POI location. This validation testing helps to ensure that your application is correctly configured and has received the right permissions for location. This validation also helps to give you confidence that the points of interests defined are working correctly with the Places Monitor extension.  Beyond this step, we will begin to test messaging in Campaign to see if the proper messages appear based on POI entries and exits. 	|  	|
|  	| **Testing Adobe Campaign Standard In-App Messaging with Location Service.** 	|  	|
| 12 	| On the main Campaign dashboard configure a new In-App-Message (type = broadcast) 	|  	|
| 12a 	| In triggers choose Places event type → Entry as the trigger 	|  	|
| 12b	| Choose Places Custom metadata as an additional filter → use POI type = Last Entered POI<br>*We use Last Entered here as the POI type as in most cases Last Entered will be the same as "Current POI" Current POI should only be used in instances where there are overlapping POI geo-fences, in which cases these POIs need to be RANKED and then the Current POI will display the top ranked POI out of the 2 or 3 geo-fences that a user may currently be in.* 	|  	|
| 12c 	| Select a custom metadata key that will help you narrow which POIs will receive a message. 	|  	|
| 12d	| For frequency and duration - keep to only a single day or two, so that if you do not like the criteria you can expire the trigger in a quicker duration. 	|  	|
| 12e 	| For Always/Once or Until click-through use ALWAYS so that you can test across multiple locations 	| In-app message is displayed ALWAYS when you simulate a location change that meets the appropriate metadata criteria. 	|
| 12f 	| For the display - choose an option other than Local Notification (will be easier to see when testing with app in foreground) 	|  	|
| 12g 	| Prepare/confirm and deploy the in-app message 	|  	|
| 13 	| Back in your development environment quit and re-launch the application to ensure that new campaign rules are downloaded 	| Do not forget that the applications must be completely re-launched for the new Campaign rules file to be downloaded to device. 	|
| 14 	| In your development application, switch locations using the previously created GPX files.  	|  You should see the in-app message appear based on previous criteria set	|
| 15 	| For the next test, we will essentially copy the same steps as before, but this time we will test LOCAL NOTIFICATION 	| Expected result - Local notifications displayed each time matching criteria is met. 	|
| 16 	| Configure a new In-App-Message (type = broadcast) 	|  	|
| 16a 	| In triggers choose Places event type → Entry as the trigger 	|  	|
| 16b 	| Choose Places Custom metadata as an additional filter → use POI type = Last Entered POI 	|  	|
| 16c 	| Select a custom metadata key that will help you narrow which POIs will receive a message. 	|  	|
| 16d 	| For frequency and duration - keep to only a single day or two, so that if you do not like the criteria you can expire the trigger in a quicker duration. 	|  	|
| 16e 	| For Always/Once or Until click-through use ALWAYS 	|  	|
| 16f 	| For display type choose Local Notification 	|  	|
| 16g 	| Prepare/confirm and deploy the in-app message 	|  	|
| 17 	| In the developer environment you will want to connect your device and press play on the build. Once you have established that location is working, background the application and continue switching locations in Xcode or Android Studio. You should still see console read-outs indicating location change, you should also see local notifications displayed (may be a second or two delay) depending on the criteria set in your trigger. 	| Expected result - Local notifications displayed each time matching criteria is met. 	|
|  	| **SUMMARY POINT** <br>At this stage, we should be seeing POI entries in our local environment. We should be able to see messaging from Campaign based on POI work as well. If there are failures, first check to see if a Slack notification did not go out. If there is no Slack message, check the application console as it is likely that for some reason a new location entry was not recorded. If results are successful, then we can be fairly sure that the application is performing correctly and that the Location and Campaign messaging service is also working correctly.	|  	|
|  	| **ON-SITE TESTING** <br>Not much should change when testing on location. Keeping the slack post-back active should help with understanding if the device is getting an entry and exit for location.	|  	|
| 18 	| Conduct tests with devices starting out with wifi and cellular disabled and then enable once in POI region 	| If there is a failure make note if you are or are not getting a geo-fence entry and notification in Slack. What is the timestamp on the Slack notification? 	|
| 19 	| Conduct test with just cellular enabled with wifi turned off 	|  	|
| 20 	| Conduct test with both cellular and wifi turned on 	|  	|
|  	| **SUMMARY POINT** <br>On-site testing should match closely to development testing. Keep in mind that there are some environmental factors that can come into play in determining a users location, such as duration of time spent in a POI geo-fence, availability of cell signal and strength of nearby wifi access points.	|  	|
