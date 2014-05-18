
#### Augmented Reality

One particular feature of the app is the ability to use a simple form of Augmented Reality to look around you for points of interest. At first we looked at implementing an external library called [Mixare](http://www.mixare.org/), but in the end, this proved more complex than originally estimated.

We decided to write an overlay for life camera footage instead, which would be a lot easier and better integrated with the application. All of this functionality is realized with a single Android Activity and 2 custom written View classes. The activity manages camera and sensor input, while the custom OverlayView class manages moving the custom POIView objects across the screen based on the sensor data it receives from the activity.

The AugmentedViewActivity is the activity containing an OverlayView object, which in turn contains different POIView objects. This activity also implements `SensorEventListener` to manage the magnetometer and accelerometer. For the live camera images, we use a `SurfaceHolder` object, on which the camera draws. For that purpose the activity implements the `SurfaceHolder.Callback` events.

The sensor data is also processed in the activity, but we quickly noticed a lot of jitter due to the high sensitivity of the sensors. To resolve this, a low-pass filter is used. After that, both the magnetometer and accelerometer data is used to calculate the device's azimuth (deviation to true north). This is passed on to the OverlayView, where it is used to determine how to draw the POIViews.

In the OverlayView, we keep track of the different POIViews we need to draw onscreen and we make sure they are positioned correctly in relation to the device's heading and each-other on the screen. This method is used:

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
				// Hackish way to force visibility with a SurfaceHolder beneath this view
				v.setVisibility(View.VISIBLE);
				v.requestLayout();
			}
		}
	}

We need to force a redraw of the entire View layout with `requestLayout()` due to a bug with a SurfaceView which does not like other Views being drawn on top of itself.

The POIView class is very basic: it contains a IPointOfInterest object and when it is created, it draws a circle and the point of interest's name onscreen. One particularity has to be noted though: in order to avoid a POIView to fill the entire screen, we had to overwrite the ``` onMeasure() ``` method.

	@Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
    	minWidth = 0;
    	int minHeight = 0;
    	int textWidth = (int) tpaint.measureText(poi.getName());
    	int iconWidth = (int) (circleRadius*2 + cpaint.getStrokeWidth()*2);
    	minWidth = Math.max(textWidth, iconWidth);
    	minHeight = screenHeight;
        setMeasuredDimension(MeasureSpec.makeMeasureSpec(minWidth, MeasureSpec.EXACTLY),
        		MeasureSpec.makeMeasureSpec(minHeight, MeasureSpec.EXACTLY));
    }

This method tells the parent View, in our case the OverlayView, the width and height of the custom View. This ensures the POIView has the specified width and height and it doesn't fill the entire screen.

