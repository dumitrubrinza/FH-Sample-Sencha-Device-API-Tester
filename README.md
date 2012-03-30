Overview
=============

This App allows the User to test various On-Device functionality available through the [config:provider] API. It's aim is to give developers a good example of how to use the [config:provider] API for On-Device functionality, while also showing one possible way to create an App. This App was built using a UI framework, Sencha Touch.

Where appropriate, code examples are given to help explain how the App works. Screen captures of the App are also used to help explain features of the App.

App Overview
============
UI Library
----------
The UI Library used to construct this app was [Sencha Touch](http://www.sencha.com/products/touch/). This is a javascript framework designed for building Apps with a native look and feel for Android and iOS Devices. The reason for choosing a framework was to demonstrate that the [config:provider] Platform doesn't restrict the way a developer can create their UI. There are many other frameworks and UI Libraries available, leaving the choice entirely up to the developer.

Using the App
----------
The Features of the App are explained in a section for each item below. The source code for each feature can be found in the corresponding 'view' and 'controller' files. e.g. For the Geolocation section, the 'view' is defined in /cloud/geolocation.js. The controller is defined in /client/default/script/controller/geolocation.js.

### Contacts

![Sencha Grouped Contacts list](FH-Sample-Sencha-Device-API-Tester/raw/master/docs/Sencha_device_api_contact_list.png)

![Device Contacts GUI](/raw/node/docs/Sencha_device_api_contact_gui.png)

The user can 'List Contacts' or create a new Contact. Listing the Device Contacts opens a grouped list showing all contacts on the device (see screen capture). The graphical component of the list is a Sencha Grouped List. To create this list, a Sencha Data Store is setup with the contacts, which then get passed into the List contstructor. The List creation process can be seen here:

```javascript
// Get contacts from device
$fh.contacts({act: 'list'}, function (data) {
        // get the contacts array from data returned
  var contacts = data.list;

        // create the data store, passing in the contacts list
  var store = new Ext.data.JsonStore({
      model: app.m.contact,  // the contact model that is registered in /client/default/script/models/contact.js
      sorters: 'last', // sort by 'last' field
      getGroupString: self.getGroupString, // the grouping callback for each item
      data: contacts // contacts array
  });

  // create the Sencha list by passing in the data store containing the contacts
  inst.v.cList = new Ext.List({
      itemTpl: '{first} {last}', // template for displaying each contact e.g. 'George Washington'
      grouped: true,
      indexBar: true,
      store: store
  });

        // Setup the container for our List
  inst.v.cCard = new Ext.Container({
      layout: 'card',
      items: [inst.v.cList]
  });

        // Swap in our List container so it's visible
  inst.v.mainPanel.prevCard.push(inst.v.mainPanel.getActiveItem());
  inst.v.mainPanel.setActiveItem(inst.v.cCard, {
      type: 'slide'
  });
  inst.v.cList.show();

}, function (e) {
  // problem listing contacts, show error
});
```

There are 2 ways to create a new contact. The first way is by entering the contact details directly into the App and pressing 'Create'. This method uses the [config:provider] Contacts API to interact with the Device's Contacts. Creating a contact this way is relatively easy for the Developer.

The second way is by pressing 'On-Device Contacts GUI'. This method tells the Device to show the built-in Contacts application. The screen capture shows what this looks like on an Android 2.2 device. Using this method would be rare as there's no control over what the User does in the Contacts application.

#### Log Message

![Log Message screen](raw/node/docs/Sencha_device_api_log.png)

This feature is to demonstrate debugging an App. It currently works on iOS if the App is being run from Xcode. The message entered in the box will be output to the Xcode Organiser console. If using the App in a desktop browser, the message will be logged to the console, if one is available e.g. Firebug Console in Firefox, Web Inspector Console in Chrome & Safari.

#### Data Storage

![Data Storage screen](raw/node/docs/Sencha_device_api_data.png)

Data Storage demonstrates On-Device persistent storage. It works by saving key-value entries. Any data stored for a specific key can be retrieved using that same key, even if the App is shut down and relaunched. One example of how this could be useful is by storing a Users preferences so that an App can configure itself according to how the User likes.

Another good example of Data Storage that this App uses is storing initialisation data retrieved from the server on startup. If during subsequent launches of the App the Device doesn't have an Internet connection, the most recent locally stored initialisation data will be used. More details on the Server Defined View/UI is available below.

#### Geolocation

![Geolocation values](raw/node/docs/Sencha_device_api_geolocation.png)

Pressing 'Get Current Location' populates the visible fields with data retrieved from the Device's GPS or other positioning system. Not all fields are supported by all devices, but the most important ones for Latitude and Longitude generally are. Geolocation data can be a very powerful addition to Apps where location based services are involved. Combined with the Map functionality, Geolocation can provide a basic graphic location to Users without much work at all.

#### Accelerometer

![Accelerometer values](raw/node/docs/Sencha_device_api_acclerometer.png)

For devices with an Accelerometer, this feature shows motion along the X,Y and Z axis whenever the 'Get Reading' button is pressed. Alternatively, the 'Monitor' button can be pressed to monitor the Axis readings at regular intervals. The most popular use for Accelerometer readings in Apps is game controlling. Other uses could include motion detection for step counters, or innovative ways of doing navigation or showing different content.

#### Notify

![Notification screen](raw/node/docs/Sencha_device_api_notify.png)

This feature is quite simple. Press 'Vibrate' to vibrate the device for a few seconds. Pressing 'Beep' will play a beep sound. More detailed information on Audio is detailed below.

#### Photos

![Photos screen](raw/node/docs/Sencha_device_api_photo.png)

This feature allows the User to display an image in the App. The image can be retrieved from the Device's Camera or Image Gallery. Pressing 'Use Camera' will start the On-Device Camera Application. Once a picture is taken and chosen, it will return to the App and the Image will be shown. If 'Use Photo Gallery' is selected, the On-Device Image Gallery is shown and the User can select an Image. Once an Image is chosen, it will return to the App and show the Image.

The image is returned as a base64 encoded represenation of the image. To display the image, the following code is used:

```javascript
// get the format of the image and base64 data from the returned picdata
var resultHtml = "<img src='data:image/" + picdata.format + ";base64," + picdata.b64 + "' width='300px' height='300px' ></img>";
// get the last item below the buttons, which should be a container,
// and update it with the image
var items = inst.v.mainPanel.getActiveItem().items;
items.get(items.length - 1).update({
    html: resultHtml
});
``` 

#### Messaging

![SMS and E-mail options](raw/node/docs/Sencha_device_api_messaging.png)

The messaging section demonstrates how to start the On-Device SMS or E-Mail Application with pre-populated fields. For SMS, the number entered in the 'To' field is set as the Recipient when the 'Send SMS' button is pressed. For E-Mail, the recipient, subject and message are passed to the E-Mail Application when 'Send E-mail' is pressed. The CC and BCC E-Mail fields could also be set by the developer.

#### Orientation

![Orientation value](raw/node/docs/Sencha_device_api_orientation.png)

When the App initialises, a callback is bound to the device orientation sensor. This is called from app.js. Whenever the device is rotated, this callback is run, which updates the Orientation field with the current orientation value. On iPhone the possible values are 0, 90, -90 and 180. On Android the possible values are 0 and 90.

#### Map

![Map loaded and initialised](raw/node/docs/Sencha_device_api_map.png)

This feature demonstrates the Mapping api. It works by passing a pair of latitude/longitude coordinates to the $fh.map function, which then initialises a Map at that location. In this App, the coordinates from doing a geolocation lookup are used for the Map initialisation. If the Geolocation lookup fails, a pair of default coordinates (hardcoded in the App) are used:

```javascript
// Get location using Geolocation API
$fh.geo({interval: 0}, function (res) {
  // Got geolocation details in response. Pass them into map api, initialising map at that point
  $fh.map({
      target: '#maps_div',
      lat: res.lat,
      lon: res.lon,
      zoom: 15
  }, function (res) {
      // Map is being shown, no need to do anything here
  }, function (error) {
      // something seriously wrong here. Show error
  });
}, function (error) {
  // Problem getting geolocation details, so fallback to hardcoded values
  $fh.map({
      target: '#maps_div',
      lat: 53.5, // Hardcoded values for lat & lon as fallbacks
      lon: -7.5,
      zoom: 15
  }, function (res) {
      // Map is being shown, no need to do anything here
  }, function (error) {
      // something seriously wrong here. Show error
  });
});
```

#### Audio

![Options before playing](raw/node/docs/Sencha_device_api_audio_opts.png)

![Audio successfully playing](raw/node/docs/Sencha_device_api_audio_playing.png)

![Options while playing](raw/node/docs/Sencha_device_api_audio_opts_while_playing.png)

The Audio section of the App demonstrates the streaming capabilites when using the [config:provider] API. This part of the API has undergone some changes and support for it across each platform is continuously improving. There are 2 stream of audio available to play in the App. The first is the Live stream for the Beat radio station. This MP3 stream uses http on port 8090, and works on iOS devices.

The second one is the Live stream for RTÉ Radio, which works on Android devices. This stream uses [RTSP](http://en.wikipedia.org/wiki/Real_Time_Streaming_Protocol). Whenever a stream is playing, the 'Pause' or 'Stop' buttons can be used. These buttons are disabled when a stream is not playing (See screenshots for how this should look). The audio control allows increasing or decreasing the media volume.

#### Webview

![Webview in action](raw/node/docs/Sencha_device_api_webview.png)

The Webview section demonstrates hows to send the User to an external site without the need to close the App. The 2 external sites here are the [whitelabel sitename] homepage and Google. The Webview slides in on top of the App, showing the site. The Webview has a toolbar at the top with some navigation otptions and the page title. The title and url are the only items need to be passed into the webview API:

```javascript
$fh.webview({
  url: "www.feedhenry.com",
  title: "FeedHenry"
}, function (result) {
  // no need to do anything as webview is displayed
}, function (err) {
  // Problem showing webview, show error instead
});
```

App Structure
-------------

### MVC Pattern
As much as possible, the app follows the MVC application pattern. There is one major difference though, and that is the View code is server-side (/cloud). When the App initialises, it makes an AJAX call to get the View structure, which is then translated (with the help of /client/default/script/view/parser.js) before being passed in the Sencha Framework. This allows for tweaking of the View layout post deployment of the App. More details on how this is done can be found in the Server Defined View/UI section below.

The application code, or business logic is in the controllers (/client/default/script/controller/). Each menu item has it's own controller e.g. /client/default/script/controller/contact.js. The view definition has handlers defined as callbacks to the controllers. For example, the 'List Contacts' button has the following handler defined in the view (/cloud/contact.js):

```javascript
exports.menuitem = {
  title: 'Contacts',
  items: [{
    ui_type: 'Ext.Button',
    text: 'List Contacts',
    margin: 5,
    ui: 'action',
    handler: 'contact.list' // Controller endpoint to callback to 
  }
  // additional contacts page config
  ]
};
```

This gets parsed during intialisation and sets up a callback to the list function in the contacts controller (/client/default/script/controller/contact.js):

```javascript
app.c.contact = function () {
    list: function (b, e) {
      // contact list code
    }
    // additional contact actions
})();
```

There are a couple of Models used by the App (/client/default/script/model/), one of which is the Contact model. This is used when displaying the grouped list of contacts. These models are defined using the Sencha model registration method (Ext.regModel). Here's how the Contact model is registered (/client/default/script/model/contact.js):

```javascript
// Define what a contact is, and its fields
app.m.contact = Ext.regModel('Contact', {
  fields: ['first', 'last']
});
```

Notice it is has 2 fields, first and last. These are used by the Sencha List template for displaying each contact (/client/default/script/controller/contact.js):

```javascript
  inst.v.cList = new Ext.List({
    itemTpl: '{first} {last}',
    grouped: true,
    indexBar: true,
    store: store
  });
```

### Files & Folders
Here is the complete App directory structure and an explanation of what's in each folder. The namespacing for files in each folder is also given e.g. Controllers stored in /client/default/script/controller/* are defined in the 'app.c' namespace:

* __/client/default__
** Default package for client code
* __/client/default/index.html__
** Main File of App
* __/client/default/resources/__
** Third Party Resources. This is where the Sencha Touch library is located
* __/client/default/script/__
** All of the developers client side javascript
* __/client/default/script/app.js__ (__app__ namespace)
** Our main App entry point/initialisation function
* __/client/default/script/controller/__ (__app.c__ namespace)
** Controllers i.e. business logic
* __/client/default/script/helper/__ (__app.h__ namespace)
** Helper functions (has a fix function for orientation when using Sencha and PhoneGap)
* __/client/default/script/model/__ (__app.m__ namespace)
** Required Models. Very light on models as the App is a Tester App
* __/client/default/script/utility/__ (__app.u__ namespace)
** Utility functions such as alerting the User or showing an error
* __/client/default/script/view/__ (__app.v__ and __inst.v__ namespaces for definitions and instances of views)
** View files. The view is defined server-side so this is quite light. Only has the 'shell' of the App and a View parser for parsing the server-side View definition
* __/client/default/script/setup/__
** App setup such as defining the App entry point and some Sencha defaults
* __/cloud/__
** Server side code and the View definition files. The only public server-side function, getUI(), returns the View definition
 
### Server Defined View/UI
The view definition is split across a number of files in /cloud/. Each file defines an menuitem, a definition of a Sencha Panel, which will be inserted into the Main Panel e.g. the Notify menu item is defined here in /cloud/notify.js:

```javascript
exports.menuitem = {
  title: 'Notify', // Title as it should appear in Menu
  
  // Array of items that should be in the Notify menu item
  items: [{
    ui_type: 'Ext.Button', // Simple button
    text: 'Vibrate', // Button text
    ui: 'action', // UI Type
    margin: 5,
    handler: 'notify.vibrate' // Controller endpoint to callback to
  }, {
    ui_type: 'Ext.Button',
    text: 'Beep (Android Only)',
    margin: 5,
    ui: 'action',
    handler: 'notify.beep'
  }]
};
```

The server-side function 'getUI' in /cloud/main.js returns this array of menuitems. At App startup, a $fh.act call to this server-side function is made. The App View is then saved to local device storage. This will be used as a fallback for the view definition in the event of no internet connectivity during future launches. The code for this fallback is shown here:

```javascript
// Get the latest view definition from server
$fh.act({act: 'getUI', req: {} }, function (res) {
  // Returned object is our menu definition, so we can use it to initialise the menu, but lets save it locally first

  $fh.data({
    act: 'save',
    key: 'menu_definition',
    val: JSON.stringify(res)
  });

  // Use the retrieved view to initialise the app

}, function (code, errorprops, params) {
  // If there's a problem getting defintion from server, fall back to a locally stored definition

  $fh.data({
      key: 'menu_definition'
  }, function (data) {
      // Use the retrieved view data to initialise the app
  }, function () {
      // No view available, drop back to view stored in client files
  });

});
```

The definition is then parsed out into local Sencha objects. This requires a little help from our custom parser for finding local object references for handlers and the proper Sencha objects. The parser file is /client/default/script/view/parser.js

The App View was defined server-side to demonstrate updating minor components of the App without the need for resubmitting to an App Store or Marketplace. For example, if the text for a particular button is wrong, this could easily be fixed by updating the View in the Server-side code, thereby updating all installed Apps the next time they start with an internet connection. The layout could also be changed, as long as the handler definitions were kept the same.

App interaction with server-side code has many possibilities. Storing the View definition is just one of them. Much more powerful Apps can be built by leveraging other server-side features such as Web Requests, or utilising the server-side Caching mechanism.