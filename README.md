
# Release Report OOMO

App Title: StudGent ([http://studgent.ahlun.be](http://studgent.ahlun.be))

Group Number: 12

Group Members: Benjamin Cottyn, Floris Devreese, Wouter Deryckere, Ah-Lun Tang



## App Functionality

Students who are new to the city where they do their studies, in this case Ghent, often do not know of the interesting places to visit, like restaurants or cafes. These so called points of interest take some time to be found and explored by people who are not from the area. Because of that, they often miss out on actions or events in which they could be interested.

It is our intention to prevent this situation. With our app, students can find these points of interest in the neighbourhood a lot quicker. In particular, we focus on known outings, restaurants and cafes, things that are worth visiting as a student. The app can also provide useful information about these places, for example opening hours, prices, upcoming actions and events.

There is also a social aspect, with a friends list and an achievement system. This is to encourage people to visit as much of Ghent as possible and include their friends in this experience. We want to provide the student an unforgettable time in this city.



## Related mobile, desktop and cloud applications 

Another app that uses GEO information is Foursquare. This app allows to check-in at various Point of interests. From somebodies coach to a public event. Our app also allows to check-in but only at public places who are interesting for all the users. The primary goal of studgent is to discover new places and events, not to know where your friends are. Foursquare is not usable to do this because the shown POI's are not useful for all students.
Both studgent and foursquare have one feature in common: the challenges. In foursquare you can 'challenge' your friends by earning badges. This is also possible in our app. Studgent has the advantage that points can also be earned by other challenges like a quiz or the 'Am I Drunk' game. 

See previous report for comparison with Visit Ghent, Toeristisch Gent WtC NL and Guido.
<hr>
## UX and Functional design 


### Intended audience & usage context


The intended users for our app are students who are new to the city in which they are studying and are not yet familiar with all the interesting places and wish to know a bit more about the place they are visiting. But this is not restricted to students, other people can easily use this app to explore Ghent and to look up useful info on points of interest.

Because most of the people don't carry their tablet with them, we choose to optimize our interface for smartphones. This is done by maximizing the navigation buttons and by reducing the visible information on the screen. This results in less errors while navigating throughout the app.

The style of the app is adapted to our audience. We have chosen to use a modern and stylish theme to attract more people to use our app.

Every activity focuses on the most useful information and we minimize uneccessary items to ensure a distraction-free focus on what the user wants. That way our app will also work on smaller screen smartphones. This is also the reason why we placed the 'login' button on a menu item in stead of a button on every activity. This reduces the used space and allows a user to login on every activity with only one click. Users who do not want to login are not bothered by this button.


<hr>
### App workflow

We've chosen for a crisp, clean and flat design with vivid colors. We have focused on usability rather than fancy layouts and tried to make a minimalist design, while still maintaining a sensible and obvious workflow.

![flowchart](http://f.cl.ly/items/02162I0i3T3a2S1g0a3K/MobieleGroepswerkFlowchart.png)

As can be seen on the flowchart, every activity can be reached with a minimum amount of user actions. Every activity also has the same Options Menu with an entry to the login activity (and with minimal user info if logged in). When the authentication succeeds, the last activity will be reactivated. 

Some activities need a current GPS location (e.g. Check-in). These activities check this information through the `LocationUtil` class. If the GPS is disabled, a Toast message appears.

We utilize graceful degradation with regards to the augmented reality functionality: if the current device is lacking the required sensors (accelerometer and/or magnetometer), we hide this option in menu. This is to prevent the experience of a "broken" app when a device lacks these sensors and the user tries to use the augmented reality feature.
<hr>
### Functional requirements for the system

Listed below are the system functional requirements we had envisioned at the start of the project. Not all requirements were met, but for the most part, we were able to implement everything that was needed for the app to function as intended.

List of the system functional requirements:

> In order to make use of the application, your device has to have an internet connection.

This was a pretty basic requirement and was implemented early in the development of the app.


> If you want to use the augmented reality, your device has to have a GPS signal.
> 
> To use the augmented reality you have to have a compass in your device.

Both this and the GPS requirement were added, with a check built in to allow graceful degradation to hide the augmented reality option if the device has no compass or GPS.

> The app stores you last GPS coordinates. This is to provide augmented reality when the device doesn’t have a valid GPS signal.

The app currently stores the last known GPS location in-memory if the signal drops, but this is not yet linked to the augmented reality. It is however working for the other parts of the app that use GPS.

> The points of interest of the last 5 times you used augmented reality, will be stored locally on your device. This is to reduce the internet traffic. These points of interest will have a maximum life-cycle of 10 days.

We cache all the points of interest and events with their detailed information for many different purposes, like the list and map overview, with a life-cycle of 1 day. That life-cycle duration can be tweaked in a XML file. The events are filtered at the backend based on the current date, only future events are served.

> For certain challenges, where the trophy points are dependent of the duration, the app will store data like duration.

This information is stored when specifically needed for a challenge. For example the check-in functionality uses GPS and a stored time-stamp to verify the validity.

> The application provides an easy way to befriend new people. This feature needs NFC.

This requirement is not met, mostly we did not have NFC capable devices to develop and test this on. It might be a feature considered for upcoming releases. The friend system has been implemented as follow users/friends system that doesn't require NFC.

> The app provides navigation to every point of interest. This will be accomplished by an intent.

Implemented for both POIs and events, using an intent to call Google Navigation with the necessary data.

> The app provides a calendar of upcoming events.

The user can view a list of upcoming events, ordered by date. There is no monthly, weekly or daily view, this would be a nice feature for a future update.
<hr>
## Code design

 

### Code



#### Datasources, CurlUtil and caching

We abstracted the retrieval of business/common objects from the dataprovider and put this logic in DataSources implementing the `IDataSource` interface (see: **Architectural design decisions → Business objects**). The datasources utilize `CurlUtil` to fetch or post data from/to a specific resource. The output from `CurlUtil` is parsed as JSON and mapped to their respective common object. For strings there is a small `JSONUtil` class that expands the `optString` method to correctly identify `null` objects.

##### CurlUtil

Because retrieving data from the web using GET and POST was used for many things, we decided to create this logic in a separate class. This class gives a generic way of posting/getting data to/from an online resource. 

##### Caching

The raw (JSON) output from `CurlUtil` is also saved as a file in the *Cache* directory with an expiration of 24h. This made sure once the app has loaded the required data, it could work offline and it also limits the data usage. 
Another positive effect was a speed improvement. The experience was so good there wasn't any strong reason to also put the common objects from the datasources in a SQLite database.

#### LocationUtil

The GPS sensor is frequently used in our app. Mostly to know the distance from the current location to a POI or quiz question. This means that every activity that shows a quiz question or a POI should use the same code to update the distance to the object. This resulted in repetitive code.

As a team we concluded to make a single class that can calculate the distances to every POI and every quiz question when the GPS coordinates are updated. The class follows a *singleton design pattern* and it implements the `LocationListener` interface (see: **Architectural design decisions → Location Updates**). With this class we can replace the repetitive code in the activities with only a few lines.

##### Implementing the LocationListener interface

With the LocationListener, the class can accept `LocationChanged` events from the Android `LocationManager`. A new `locationManager` is created in the constructor of the singleton.

We specifically implement the `onLocationChanged` method. When this event occurs, the distance field of the relevant POIs and quiz questions are updated.

The GPS must be disabled when the activity goes `onPause`. The `LocationUtil` also has `onPause` and `onResume` functions to unregister and reregister the `LocationListener` from/to the `locationManager`. These functions can be called from the respective `onPause` and `onResume` eventshandlers in the activity.

##### Integrating the Activities with the LocationUtil class

Because every activity is different, we needed a generic way to update the GUI when the distance is recalculated. Activities can implement the `IDistanceUpdatedListener` interface to allow registering the activity to `LocationUtil` to retrieve distance updates. 
Some activities need the current GPS location, these activities need to implement the `ILocationChangedListener` interface. This will send the last known GPS coordinate as argument to the function.
Everytime a new activity registers itself, the `LocationUtil` will immediately trigger the callback with the last known GPS coordinate and/or distances. This results in a non-blocking way of updating the activity with required info from the sensor with limited lines of code.


###### Example: `POIListAcitivy` implementing `IDistanceUpdateListener`

```
public class POIListActivity extends Activity implements IDistanceUpdatedListener
```

The `LocationUtil` can be initialised in the `onCreate` function:

```
LocationUtil.getInstance(this).registerDistanceUpdatedListener(this);
```

The `onPause` and `onResume` functions are overridden so they call the `onPause` and `onResume` functions from the `LocationUtil` class:

```
@Override
public void onPause() {
	LocationUtil.getInstance(this).onPause();
	super.onPause();
}

@Override
public void onResume() {
	LocationUtil.getInstance(this).onResume();
	super.onResume();
}
```

And finally, we can implement the `distanceIsUpdated` function from the `IDistanceUpdateListener` interface. `LocationUtil` will call this everytime a new GPS location is found.

```
@Override
public void distanceIsUpdated() {
        renewListGui();
}
```


#### Augmented Reality

One particular feature of the app is the ability to use a simple form of Augmented Reality to look around you for points of interest. At first we looked at implementing an external library called [Mixare](http://www.mixare.org/), but in the end, this proved more complex than originally estimated.

We decided to write an overlay for live camera footage instead, which would be a lot easier and better integrated with the application. All of this functionality is realized with a single Android `Activity` and 2 custom written `View` classes. The activity manages camera and sensor input, while the custom `OverlayView` class manages moving the custom `POIView` objects across the screen based on the sensor data it receives from the activity.

The `AugmentedViewActivity` is the activity containing an `OverlayView` object, which in turn contains different `POIView` objects. This activity also implements `SensorEventListener` to manage the magnetometer and accelerometer. For the live camera images, we use a `SurfaceHolder` object, on which the camera draws. For that purpose the activity implements the `SurfaceHolder.Callback` events.

The sensor data is also processed in the activity, but we quickly noticed a lot of jitter due to the high sensitivity of the sensors. To resolve this, a low-pass filter is used. After that, both the magnetometer and accelerometer data is used to calculate the device's azimuth (deviation to true north). This is passed on to the `OverlayView`, where it is used to determine how to draw the `POIView`s.

In the `OverlayView`, we keep track of the different `POIView`s we need to draw onscreen and we make sure they are positioned correctly in relation to the device's heading and each-other on the screen. This method is used:

```
public void updateOverlay(int az) {
    for (POIView v : pois) {
        float bearing = devLoc.bearingTo(LocationUtil.getLocationFromLatLng(v.getPoi().getLocation()));
        float t = ((az - bearing) + 180) % 360 - 180;
        if (t > fov) {
            // Do not draw, because poi is outside fov
            v.setVisibility(View.INVISIBLE);
        } else {
            float offset = ((az - bearing) + 180) % 360 - 180;
            offset = (offset / fov) * screenWidth;
            v.setTranslationX(-offset + (screenWidth / 2 - v.getMinWidth() / 2));
            // Force visibility with a SurfaceHolder beneath this view
            v.setVisibility(View.VISIBLE);
            v.requestLayout();
        }
    }
}
```

We need to force a redraw of the entire `View` layout with `requestLayout()` due to a bug with a `SurfaceView` which does not like other Views being drawn on top of itself.

The `POIView` class is very basic: it contains a `IPointOfInterest` object and when it is created, it draws a circle and the point of interest's name onscreen. One particularity has to be noted though: in order to avoid a `POIView` to fill the entire screen, we had to overwrite the `onMeasure()` method.

```
@Override
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
    minWidth = 0;
    int minHeight = 0;
    int textWidth = (int) tpaint.measureText(poi.getName());
    int iconWidth = (int) (circleRadius*2 + cpaint.getStrokeWidth()*2);
    minWidth = Math.max(textWidth, iconWidth);
    minHeight = screenHeight;
    setMeasuredDimension(MeasureSpec.makeMeasureSpec(minWidth, MeasureSpec.EXACTLY), MeasureSpec.makeMeasureSpec(minHeight, MeasureSpec.EXACTLY));
}
```
This method tells the parent `View`, in our case the `OverlayView`, the width and height of the custom `View`. This ensures the `POIView` has the specified width and height and it doesn't fill the entire screen.

<hr>
### Libraries

* Google Play Services: for displaying maps.
* Crashlytics: for analyzing crash logs



#### Crashlytics

![](http://f.cl.ly/items/2u410m2Y3k223p280l2n/3236_8ff9.jpg)

Because not everyone had a real device and Android emulators do not always measure up to the real thing, we decided to try Crashlytics. With Crashlytics, whenever there was an exception, the stacktrace and environment information was uploaded to the server and catalogued in a new or existing issue.

This free service from Twitter greatly improved our productivity to hunt down bugs and errors.

![](http://f.cl.ly/items/2E2F3h1J3G271B0O1q3X/Image%202014-05-17%20at%204.26.09%20pm.png)

<hr>
### Architectural design decisions

#### Package structure

* Main namespace: **be.ugent.oomo.groep12.studgent**
* Subpackages:
    * **activity** : Classes that extend from the Android Activity class
    * **adapter** : Classes that extend Adapters (for loading in ListViews)
    * **common** : Common business objects with their respective interfaces
    * **data** : DataSources to retrieve business objects from the dataprovider (backend).
    * **exception** : Some custom exception classes to improve handling and locating crashes.
    * **utilities** : Utility classes to abstract lower level functionality and often used functions and/or algorithms.
    * **view** : Views for augmented reality

#### Class diagram

##### Activities

An overview of how our activities are connected. `LoginActivity` and `AboutActivity` are separate and are called from the Options Menu. `POIListAcitivity` is reused for Checkins and can be called from `POIMapviewActivity` and `ChallengeActivity`, the `Intent` gets an extra parameter that can be used to alter the behaviour of the `POIListActivity`.

![Activity class diagram](http://f.cl.ly/items/2Q380K0h1H2y2x08221S/thumb_ab23993ae22b3d2ef62ce752462a86f0.png)
<hr>
##### Business objects

Example for a *Point of Interest* object

![Structure for using business object](http://f.cl.ly/items/473F3z1F1q0f1n470z3U/Image%202014-05-17%20at%2010.43.30%20pm.png)
<hr>
##### Location updates

`POIDetailActivity` requires both the distance and location and implements the required interfaces (`IDistanceUpdatedListener` and `ILocationChangedListener`) to listen to these events in the `LocationUtil`

![Structure of location updates](http://f.cl.ly/items/3S2I3Q2c39331l2m1t28/Image%202014-05-17%20at%2010.50.42%20pm.png)

<hr>
## Planning and work load distribution



### Comparison with original planning

The biggest deviation from the original planning was not implementing the NFC feature to make friends. This caused a few shifts in task assignments: Friends feature was modified to follow friends (implemented by Floris), the Quiz was reassigned to Wouter and the mini-game was assigned to Ah-Lun.

Release 3 was also used to implement the remaining small features in addition to testing and bugfixing.


<table cellpadding="0" cellspacing="0" style="margin:0 0 20px 0; width: 100%;">
    <tbody>
        <tr>
            <td>
                <p>
                    Release 1
                </p>
            </td>
            <td>
                <p>
                    Wouter
                </p>
            </td>
            <td>
                <p>
                    Ah-Lun
                </p>
            </td>
            <td>
                <p>
                    Benjamin
                </p>
            </td>
            <td>
                <p>
                    Floris
                </p>
            </td>
        </tr>
        <tr>
            <td>
                <p>
                    24/02-02/03
                </p>
            </td>
            <td>
                <p>
                    Setting up development environment
                </p>
            </td>
            <td>
                <p>
                    Setting up development
                    environment.
                </p>
            </td>
            <td>
                <p>
                    Setting up development
                    environment.
                </p>
            </td>
            <td>
                <p>
                    Setting up development environment.
                </p>
            </td>
        </tr>
        <tr>
            <td>
                <p>
                    03/03-09/03
                </p>
            </td>
            <td>
                <p>
                    Research and setting up environment.
                </p>
            </td>
            <td>
                <p>
                    Configuring git repositories (connecting with
                    continuous integration and issue tracker).
                </p>
                <p>
                    Structuring the basic project.
                </p>
                <p>
                    Creating common data objects &amp; interfaces.
                </p>
            </td>
            <td>
                <p>
                    Research and setting up
                    environment.
                </p>
            </td>
            <td>
                <p>
                    Research and setting up environment.
                </p>
            </td>
        </tr>
        <tr>
            <td>
                <p>
                    10/03-16/03
                </p>
            </td>
            <td>
                <p>
                    Mapview with markers (dummy data)
                </p>
            </td>
            <td>
                <p>
                    Backend: structure, seeding events &amp; POI.
                </p>
                <p>
                    CurlUtil: unified utility for retrieving (GET)
                    webcontent with basic caching.
                </p>
                <p>
                    Event Datasource.
                </p>
            </td>
            <td>
                <p>
                    Camera view for augmented reality.
                </p>
            </td>
            <td></td>
        </tr>
        <tr>
            <td>
                <p>
                    17/03-21/03
                </p>
            </td>
            <td></td>
            <td>
                <p>
                    Event list Activity.
                </p>
                <p>
                    Event detail Activity.
                </p>
            </td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <td>
                <p>
                    22/03-28/03
                </p>
            </td>
            <td>
                <p>
                    Receive POI markers from webservice
                </p>
                <p>
                    Marker click event.
                </p>
                <p>
                    POI Detail view
                </p>
            </td>
            <td>
                <p>
                    Parcelable objects.
                </p>
                <p>
                    Location Utility (convert address to latlng).
                </p>
                <p>
                    User login Activity.
                </p>
            </td>
            <td>
                <p>
                    Compass and Overlay on Camera view
                </p>
            </td>
            <td>
                <p>
                    Vriendenlijst
                </p>
                <p>
                    POI lijst (dummy)
                </p>
            </td>
        </tr>
    </tbody>
</table>



<table cellpadding="0" cellspacing="0" style="margin:0 0 20px 0; width: 100%;">
    <tbody>
        <tr>
            <td>
                <p>
                    Release 2
                </p>
            </td>
            <td>
                <p>
                    Wouter
                </p>
            </td>
            <td>
                <p>
                    Ah-Lun
                </p>
            </td>
            <td>
                <p>
                    Benjamin
                </p>
            </td>
            <td>
                <p>
                    Floris
                </p>
            </td>
        </tr>
        <tr>
            <td>
                <p>
                    29/03-04/04
                </p>
            </td>
            <td>
                <p>
                    Implementing Quiz activity with dummy data
                </p>
            </td>
            <td>
                <p>
                    Implement LocationListener in POIMapview
                    Activity.
                </p>
            </td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <td>
                <p>
                    05/04-11/04
                </p>
            </td>
            <td></td>
            <td>
                <p>
                    Change navigation from optionsmenu to ActionBar.
                </p>
            </td>
            <td></td>
            <td>
                <p>
                    Improve Friends activity
                </p>
            </td>
        </tr>
        <tr>
            <td>
                <p>
                    12/04-18/04
                </p>
            </td>
            <td>
                <p>
                    Implementing Quiz with Multiple choice and single choice question
                </p>
                <p> 
                    Abreviaton of questions in the listview
                </p>
                
            </td>
            <td>
                <p>
                    Initialising sensor for minigame.
                </p>
            </td>
            <td>
                <p>
                    Smoothing sensor data to reduce overlay stutter
                </p>
            </td>
            <td>
            </td>
        </tr>
        <tr>
            <td>
                <p>
                    19/04-25/04
                </p>
            </td>
            <td>
                <p>
                    Bugfix submit quiz
                </p>
            </td>
            <td>
                <p>
                    "Am I Drunk?" minigame.
                </p>
                <p>
                    Implement GPS background service (and check battery
                    usage).
                </p>
            </td>
            <td>
                <p>
                    Refactor code to implement tracking multiple
                    POIs
                </p>
                <p>
                    Show multiple POIs on overlay with dummy data
                </p>
                <p>
                    Making POIs on overlay clickable
                </p>
            </td>
            <td>
                <p>
                    Checkin activity
                </p>
                <p>
                    Search POI list
                </p>
                <p>
                    Uitbreiden friend
                </p>
                <p>
                    Throphy
                </p>
            </td>
        </tr>
    </tbody>
</table>


<table cellpadding="0" cellspacing="0" style="margin:0 0 20px 0; width: 100%;">
    <tbody>
        <tr>
            <td>
                <p>
                    Release 3
                </p>
            </td>
            <td>
                <p>
                    Wouter
                </p>
            </td>
            <td>
                <p>
                    Ah-Lun
                </p>
            </td>
            <td>
                <p>
                    Benjamin
                </p>
            </td>
            <td>
                <p>
                    Floris
                </p>
            </td>
        </tr>
        <tr>
            <td>
                <p>
                    26/04-02/05
                </p>
            </td>
            <td></td>
            <td></td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <td>
                <p>
                    03/05-9/05
                </p>
            </td>
            <td>
                <p>
                    Get Questions from backend and send answer
                </p>
                <p>
                    Bugfix Quiz
                </p>
                <p>
                    Navigation to POI
                </p>
            </td>
            <td>
                <p>
                    Quiz: QuestionDatasource + backend.
                </p>
                <p>
                    Autologin .<br />
                    Logout (+ remove user related data from cache).
                </p>
            </td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <td>
                <p>
                    10/05-16/05
                </p>
            </td>
            <td>
                <p>
                    Navigate to quiz
                </p>
                <p>
                    Calculate distances to POI and Questions
                </p>
                <p>
                    Implementing a general GPS class that can be used in all activities (LocationUtil)
                </p>
                <p>
                    'GPS Disable' bug
                </p>
            </td>
            <td>
                <p>
                    About activity.
                </p>
                <p>
                    Friend connect with backend.
                </p>
                <p>
                    Filter events.
                </p>
                <p>
                    Parsing textviews as HTML.
                </p>
                <p>
                    Fix/cleanup unicode/.. text bugs.
                </p>
                <p>
                    Fix Scrollview bugs.
                </p>
            </td>
            <td>
                <p>
                    Connected augmented with real data
                </p>
                <p>
                    Added GPS to sensors for augmented
                </p>
                <p>
                    Limit number of POIs on augmented
                </p>
                <p>
                    Improve stability and performance of augmented
                    view
                </p>
            </td>
            <td>
                <p>
                    Check-in
                </p>
                <p>
                    Friend connect with backend
                </p>
            </td>
        </tr>
    </tbody>
</table>
<hr>
## Conclusion and future work


### Final result

We feel like our final result matches our expectations very closely. The biggest feature that is lacking is NFC technology for making friends, mainly because we did not have access to devices with NFC. Other than that, there are only some very minor features (eg. registration, news-stream) not implemented because we set priorities on features that were more important.
Implementing these missing features would technically mean we would be repeating the same work (eg. fetching data and posting to backend was already handled with POI/Events, login system).


### Different approach

* Android Studio instead of Eclipse: for better integration with
    * gradle : for missing dependencies
    * external services (travis-ci, coveralls,...)
* Development workflow
    * more branching
    * more rebasing/squashing meaningless commits
* Testing on wider range of devices
* More cities, focusing only on Ghent required us to fake the GPS data when we were not in Ghent.


### Effort needed to release app

There is still some work to be done before the application is ready to be released for the general public. A few issues that would need to be resolved are listed below:

* Enabling user registration
* Adding more and accurate data to the database
* Host the backend service on a SSL protected infrastructure
* Polish the GUI layout and Augmented Reality functionality
* Disable debug code
* Test on a wider range of devices

<hr>
### Future releases

Future releases would see features implemented that were not finished now and quality of life tweaks to make the app more enjoyable to use. A few examples:

* *Newsstream*: view latest checkins, challenges, earned trophies from yourself, friends or your current location upon opening the app.
* *Create your own events/POI*
* *Integrate events with device's calendar*: allow the user to create reminders for events listed in the app.
* *Discussion boards/comments system*: discussing POI (in addition to add comments to your checkin), events, challenges, user profiles, trophies,... would be a nice social addition to engage the users with each other
* *RSVP*: allow users to RSVP to events
