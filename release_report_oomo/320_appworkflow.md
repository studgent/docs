### App workflow

We've chosen for a crisp, clean and flat design with vivid colors. We have focused on usability rather than fancy layouts and tried to make a minimalist design, while still maintaining a sensible and obvious workflow.

![flowchart](http://f.cl.ly/items/02162I0i3T3a2S1g0a3K/MobieleGroepswerkFlowchart.png)

As can be seen on the flowchart, every activity can be reached with a minimum amount of user actions. Every activity also has the same Options Menu with an entry to the login activity (and with minimal user info if logged in). When the authentication succeeds, the last activity will be reactivated. 

Some activities need a current GPS location (e.g. Check-in). These activities check this information through the `LocationUtil` class. If the GPS is disabled, a Toast message appears.

We utilize graceful degradation with regards to the augmented reality functionality: if the current device is lacking the required sensors (accelerometer and/or magnetometer), we hide this option in menu. This is to prevent the experience of a "broken" app when a device lacks these sensors and the user tries to use the augmented reality feature.
