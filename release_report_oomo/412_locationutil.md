
#### LocationUtil

The GPS sensor is frequently used in our app. Mostly to know the distance from the current location to a POI or quiz question. This means that every activity that shows a quiz question or a POI should use the same code to update the distance to the object. This resulted in repetitive code.

As a team we concluded to make a single class that can calculate the distances to every POI and every quiz question when the GPS coordinates are updated. The class follows a *singleton design pattern* and it implements the `LocationListener` interface (see: **Architectural design decisions → Location Updates **). With this class we can replace the repetitive code in the activities with only a few lines.

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

