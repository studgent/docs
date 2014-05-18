
### App workflow


![alttext](http://f.cl.ly/items/02162I0i3T3a2S1g0a3K/MobieleGroepswerkFlowchart.png)

As can be seen on the flowchart, every activity can be reached with a minimum amount of clicks. Every activity also has a link to the login activity. When the authentication succeeds, the last visited form will be reactivated.

Some activities need a current GPS location (e.g. Check-in). These activities check  this information trough the locationUtil class. If the GPS is disabled, a Toast message appears. 
