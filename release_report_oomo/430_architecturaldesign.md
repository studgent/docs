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

![Activity class diagram](http://f.cl.ly/items/2Q380K0h1H2y2x08221S/thumb_ab23993ae22b3d2ef62ce752462a86f0.png)

##### Business objects

![Structure for using business object](http://f.cl.ly/items/473F3z1F1q0f1n470z3U/Image%202014-05-17%20at%2010.43.30%20pm.png)

##### Location updates

![Structure of location updates](http://f.cl.ly/items/3S2I3Q2c39331l2m1t28/Image%202014-05-17%20at%2010.50.42%20pm.png)
