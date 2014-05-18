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

The app currently stores the last known GPS location if there is no signal, but this is not yet linked to the augmented reality. It is however working for the other parts of the app that use GPS.

> The points of interest of the last 5 times you used augmented reality, will be stored locally on your device. This is to reduce the internet traffic. These points of interest will have a maximum life-cycle of 10 days.

We cache all the points of interest and events with their detailed information for many different purposes, like the list and map overview, with a life-cycle of 1 day. That life-cycle duration can be tweaked in a XML file. If there are new items added, only those new items are fetched from the back-end, and not the entire list of data. This is to further reduce internet usage.

> For certain challenges, where the trophy points are dependent of the duration, the app will store data like duration.

This information is stored when specifically needed for a challenge. For example the check-in functionality uses GPS and a stored time-stamp to verify the validity.

> The application provides an easy way to befriend new people. This feature needs NFC.

This requirement is not met, mostly we did not have NFC capable devices to develop and test this on. It might be a feature considered for upcoming releases.

> The app provides navigation to every point of interest. This will be accomplished by an intent.

Implemented, using an intent to call Google Navigation with the necessary data.

> The app provides a calendar of upcoming events.

The app currently does not have a calendar per se, but the user can view a list of upcoming events, ordered by date. This would be a nice feature for a future update.
