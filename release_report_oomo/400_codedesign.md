## Code design

##Location util

The GPS device is frequently used in our app. Mostly to know the distance from the current location to a POI or quiz question. This means that every activity that shows a quiz question or a POI should use the same code to update the distance to the object. This results in repetitive code.

As a team we concluded to make a singleton class that would recalculate the distances to every POI and every quiz question when the GPS coordinates are updated. We also found a way to report to the current activity that the values has been changed. 

First of all should our class (locationUtil) inherits the LocationListener interface so it can accept LocationChanged events from the android LocationManager. A new locationManager is created in the constructor of the singleton. If you want to create a locationManager you need access to the current loaded context. Therefor we adapted the getInstance function of the singleton to pass the current context. That way we can create a LocationManger in the constructor.

When the onLocationChanged event occurs, the distance field of all the POIs and quiz questions are updated.

The GPS must be disabled when the activity goes on pause. So we made two special functions in the locationUtil class: onPause and onResume. These functions must be called when an activity uses the locationUtil. OnPause disables the update event. The onResume function reregistrates the onLocationUpdate event.

Because every activity is different, we need a generic way to update the gui when the distance is recalculate. That is why the IdistanceUpdatedListener interface is made. Every acitivity who wants to use the locationUtil singleton has to inherit this interface and register itself. That way you can simply send a callback from the locationUtil to the current activity.
Some activities need the current GPS location. That is why we also made the  IlocationChangedListener interface. This works the same way as the IDistanceUpdatedListener and sends the last known GPS coordinates as arguments to the function.
Evertime a new activity registers itself, a callback will be generated automaticly with the last known GPS coordinates and distances.

As an axample we use the POIListActivity. As mentioned above, this activity implements the IDistanceUpdatedListener interface.
 
public class POIListActivity extends Activity implements …, IDistanceUpdatedListener

In the onCreate function, the locationUtil is initialised:
LocationUtil.getInstance(this).registerDistanceUpdatedListener(this);

The onPause and onResume functions are overwriten so they execute the onPause and onResume functions from the locationUtil class:
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

And as last we had to override the distanceIsUpdated function that is called everytime a new GPS location is found.
 @Override
       public void distanceIsUpdated() {
               renewListGui();
 }


 
