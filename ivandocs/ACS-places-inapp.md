# Adobe Campaign Standard
## In-App Messaging with Adobe Experience Platform Location Service

In this guide, we’ll show how you can use Adobe Experience Platform Location Service information to send In-App Messages or Local Notifications. 

Before we begin, this guide assumes that you have a mobile application already configured with the Adobe Experience Platform Mobile SDK, including the Adobe Campaign Standard extension. 

### Prerequisites
1.  Integration the [Adobe Experience Platform Mobile SDK](https://aep-sdks.gitbook.io/docs/getting-started/get-the-sdk) into your app.
2. Add the [Adobe Campaign Standard Extension](https://aep-sdks.gitbook.io/docs/using-mobile-extensions/adobe-campaign-standard) to your mobile app configuration.
3. [Create one or more POIs](https://placesdocs.com/places-services-by-adobe-documentation/places-database-management-1/managing-pois-in-the-places-ui#create-a-poi) in the Places POI management interface.
4. Install and configure the [Places]() and [Places Monitor Extensions]() in your mobile application

### Sending In-App Messages based on Geo-fence entry or exit
1. In your Adobe Campaign Standard instance, click **Create In-App message**.
2.  For the message type select **Target all users of a Mobile application**.
3. Click **Next** and type the general details on the next screen.
4. You’ll see on the left sidebar that you can now use a variety triggers related to Places.
5. You can choose to have the in-app message display if the user has entered a POI geo-fence.
6. You can also use metadata that defined in the Location Services UI to filter audience.
 
	 In this example I want to trigger an in-app message shown only to users that have entered one of my vacation resorts that are participating in a free drink program. I want to send my users a coupon when they arrive. 
 ![In-App Message Places Metadata](images/last-entered-vacation.png)
8. Click the **Next** to finish creating the in-app message for delivery.
![](images/prepare-ACS.png)

To test the in-app message delivery, launch the application in Xcode or Android studio and use the location simulator to select a point of interest that will fit the messaging criteria.
![](images/drink-coupon-on-app.PNG)


Using Places with Adobe Campaign Standard gives you a powerful tool to segment and target your messaging to users based on geo-fence entries and extis. This simple integration opens the door for building out more personalized and contextual use cases.

### Video Example
https://youtu.be/ikiTTQw9c-o
