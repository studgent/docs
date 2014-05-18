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

