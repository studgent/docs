### Architectural design decisions

#### Package structures

* Main namespace: **be.ugent.oomo.groep12.studgent**
* Subpackages:
    * **activity** : Classes that extend from the Android Activity class
    * **adapter** : Classes that extend Adapters (for loading in ListViews)
    * **common** : Common business objects with their respective interfaces
    * **data** : DataSources to retrieve business objects from the dataprovider (backend).
    * **exception** : Some custom exception classes to improve handling and locating crashes.
    * **utilities** : Utility classes to abstract lower level functionality and often used functions and/or algorithms.
    * **view** : Views for augmented reality

