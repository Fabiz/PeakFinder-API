
# PeakFinder-API

This page contains information about embedding the [PeakFinder](https://www.peakfinder.com) mountain panorama module to your website.

PeakFinder supports 3 different methods to embed the Panorama Panel to a website:
- Url-Format: Create a link including latitude/longitude and some optional parameters to link to the [PeakFinder](https://www.peakfinder.com) website
- Embed with iFrame: Add an iFrame container to your website with latitude/longitude
- Embed with Canvas: Use Javascript for full control of the Panorama Panel

## Url-Format

PeakFinder provides a simple interface that allows you to create a link to a certain viewpoint or to embed PeakFinder directly on your homepage as an iFrame.

PeakFinder URL Format
The following link opens the PeakFinder website with the given viewpoint:
https://www.peakfinder.com/?lat=42.8612&lng=-72.109&name=Monadnock&ele=941


The following links opens the PeakFinder app with the given viewpoint on your iOS or Android device:
- [peakfinder://?lat=42.8612&lng=-72.109](peakfinder://?lat=42.8612&lng=-72.109)
- [peakfinder://?lat=42.8612&lng=-72.109&name=Monadnock](peakfinder://?lat=42.8612&lng=-72.109&name=Monadnock)
- [peakfinder://?lat=42.8612&lng=-72.109&name=Monadnock&ele=941](peakfinder://?lat=42.8612&lng=-72.109&name=Monadnock&ele=941)
- [peakfinder://?lat=42.8612&lng=-72.109&name=Monadnock&ele=941&off=500](peakfinder://?lat=42.8612&lng=-72.109&name=Monadnock&ele=941&off=500)

### Arguments

Additionally to the required parameters lat and lng you can add the following arguments:

| Key	| Argument | Description |
| -------------| -------- | ---- |
| lat |	Latitude (required)	| Float value, Wgs84 Format (lat=42.8612) |
| lng	| Longitude (required) | Float value, Wgs84 Format (lng=-72.1092) |
| name |	Name of the viewpoint (optional) | Text (name=Monadnock%20Mountain), replace spaces with %20 |
| ele | Elevation (optional) | Integer (ele=941) |
| off | Elevation offset (optional) | Integer (off=100) |
| azi | Azimuth (optional) | Float 0.0 .. 360.0 (azi=90.0) |
| alt | Altitude (optional) | Integer -25.0 .. 25.0 (alt=0.0) |
| fov | Field of view (optional) | Integer 8 .. 90.0 (fov=45.0) |
| teleazi & telealt | Azimuth and altitude for displaying the telescope (optional, but both values are required) | Floats 0..360 (teleazi=90.5&telealt=4.5) |

***

## Embed with iFrame

With the following code you can embed PeakFinder directly to your homepage. 
Check out this example page: [basicexample_iframe.html](https://fabiz.github.io/PeakFinder-API/basicexample_iframe.html).


```html
<iframe src="https://www.peakfinder.com/embed/?lat=42.8612&lng=-72.1092&name=Monadnock%20Mountain&ele=941&zoom=5&azi=255" 
    frameBorder="0" width="100%" height="570" name="peakfinder">
<p>Your Browser do not support iFrames.</p>
</iframe>
```
***

## Embed with canvas

This method gives you the most flexibility. You can use Javascript to control the PeakFinder module.

Check out this example page: [basicexample_canvas.html](https://fabiz.github.io/PeakFinder-API/basicexample_canvas.html).
\
\
You must do the following steps: 
- Include Javascript
- Create canvas tag
- Load script

### Include Javascript

Include https://www.peakfinder.com/script/peakfinder.1.0.min.js in your html header:

```html
<script async type="text/javascript" src="https://www.peakfinder.com/script/peakfinder.1.0.min.js"></script>
 ````

### Create Canvas Tag

Add the following canvas tag your your html body:

```html
<div class="content">
  <canvas id="pfcanvas" oncontextmenu="event.preventDefault()"> </canvas>

  <!-- you can also add an optional div that will be hidden when the PeakFinder modele has been loaded -->
  <div id="pfcanvasprogress">
    <div class="spinner" id="spinner">
    </div>
  </div>
</div>
```

 ### Load script
 
 Add the following script to load the module:

```javascript

if (PeakFinder.utils.caniuse()) {

  let panel = new PeakFinder.PanoramaPanel({
    canvasid: 'pfcanvas', 
    locale: 'en' // attach to canvas
  }) 
  
  panel.init(function() {
    // inside here its save to use the panel
    
    panel.settings.distanceUnit(1) // use imperial (miles, feet) format
            
    panel.loadViewpoint(46.53722, 8.12610, 'Finsteraarhorn') // loads a viewpoint

    // animate to view
    panel.azimut(209.0, 2.0)
    panel.altitude(1.0, 1.0)
    panel.fieldofview(45.0, 2.0)
  });
}
```


* * *

# Javascript API Reference

## Version 1.0


* * *

### Functions

<a name="module_PeakFinder..PeakFinder"></a>

### PeakFinder~PeakFinder : <code>object</code>
Constructor: Initialization of the PeakFinder PanoramaPanel. Pass the options in a Javascript dictionary:

**Properties**

| Name | Type | Description |
| --- | --- | --- |
| canvasid | <code>string</code> | The id of the html canvas element. Default: 'canvas' |
| locale | <code>string</code> | The language locale of the module. Default: 'en'. Supported locales: en,de,fr,it,es,pt,ja,ko,zh-Hans,zh-Hant |
| bgcolor | <code>string</code> | A custom color for the background/sky. Normally the sky is white. For another color use the format '#rrggbb' (e.g. #87CEEB for sky color). |

**Example**  
```js
let panel = new PeakFinder.PanoramaPanel({
  canvasid: 'pfcanvas', 
  locale: 'en'
}) // attach to canvas
```
<a name="module_PeakFinder..registerCommandsCallback"></a>

### PeakFinder~registerCommandsCallback(command)
Registers a callback that receives commands/messages from the PanoramaPanel.
The PanoramaPanel will send a message when a specific event occured. E.g. when a
new viewpoint was loaded the command: \
<code> viewpoint changed lat=46.53722&lng=8.12610 </code> \
will be sent. 
Normally register to this callback can be skipped.


| Param | Type | Description |
| --- | --- | --- |
| command | <code>function</code> | function must have the format functioname(command). |

**Example**  
```js
panel.registerCommandsCallback(function(cmd) {
  console.log(cmd)
})
```
<a name="module_PeakFinder..init"></a>

### PeakFinder~init(callback)
Loads all the needed stuff for displaying the panorama. Call this method only once.
The async callback will inform when the panorama panel is ready. After this call additional
commands like <code>loadViewpoint</code> may be called.


| Param | Type | Description |
| --- | --- | --- |
| callback | <code>function</code> | This function will be called when everything is ready |

**Example**  
```js
panel.init(function() {
  console.log('ready')
  // inside here you can use panel
  panel.loadViewpoint(46.53722, 8.12610, 'Finsteraarhorn')
  
});
```
<a name="module_PeakFinder..asyncinit"></a>

### PeakFinder~asyncinit()
Loads all the needed stuff for displaying the panorama. Call this method only once.
Same as the init function but with support for the Javascript async pattern. After this call additional
commands like <code>loadViewpoint</code> may be called.

**Example**  
```js
async panel.asyncinit()

console.log('ready')
panel.loadViewpoint(46.53722, 8.12610, 'Finsteraarhorn')
```
<a name="module_PeakFinder..loadViewpoint"></a>

### PeakFinder~loadViewpoint(latitude, longitude, the)
Loads a viewpoint with the given coordinates and an optional name


| Param | Type | Description |
| --- | --- | --- |
| latitude | <code>number</code> |  |
| longitude | <code>number</code> |  |
| the | <code>string</code> | viewpoint name. Optional |

<a name="module_PeakFinder..azimut"></a>

### PeakFinder~azimut(val, animationduration) ⇒ <code>number</code>
Get/set azimut.


| Param | Description |
| --- | --- |
| val | The azimut value in degrees |
| animationduration | The duration of the animation. If undefined no animation will be done. |

**Example**  
```js
await panel.azimut(120.0, 1.0) // set azimut with an animation time of 1 second

const azimut = panel.azimut() // gets azimut
```
<a name="module_PeakFinder..altitude"></a>

### PeakFinder~altitude(val, animationduration) ⇒ <code>number</code>
Get/set altitude.


| Param | Description |
| --- | --- |
| val | The altitude value in degrees |
| animationduration | The duration of the animation. If undefined no animation will be done. |

<a name="module_PeakFinder..fieldofview"></a>

### PeakFinder~fieldofview(val, animationduration) ⇒ <code>number</code>
Get/set field of view (zoom).


| Param | Description |
| --- | --- |
| val | The field of view (zoom) value in degrees |
| animationduration | The duration of the animation. If undefined no animation will be done. |

<a name="module_PeakFinder..elevationOffset"></a>

### PeakFinder~elevationOffset(val, animationduration) ⇒ <code>number</code>
Get/set elevation offset.


| Param | Description |
| --- | --- |
| val | The elevation offset in meters |
| animationduration | The duration of the animation. If undefined no animation will be done. |

**Example**  
```js
await panel.elevationOffset(200.0, 1.0) // set elevation offset to 200m animation time of 1 second

const elev = panel.elevationOffset() // gets elevation offset
```

* * *

## PeakFinder.settings

The following setters and getters manage the PeakFinder settings.

<a name="module_PeakFinder.Settings..theme"></a>

### PeakFinder.Settings~theme() ⇒ <code>number</code>
Get/set theme. \
0: light, 1: dark

**Example**  
```js
panel.settings.theme(1) // set to dark

const unit = panel.settings.theme() // gets dark
```
<a name="module_PeakFinder.Settings..distanceUnit"></a>

### PeakFinder.Settings~distanceUnit() ⇒ <code>number</code>
Get/set distance unit. \
0: metric, 1: imperial

**Example**  
```js
panel.settings.distanceUnit(1) // set to imperial

const unit = panel.settings.distanceUnit() // gets imperial
```
<a name="module_PeakFinder.Settings..coordsFormat"></a>

### PeakFinder.Settings~coordsFormat() ⇒ <code>number</code>
Get/set the coordinates format. \
0: degree (46°30'21"N 8°20'14"E), 1: decimal (46.2412°N 8.1342°E)

<a name="module_PeakFinder.Settings..projection"></a>

### PeakFinder.Settings~projection() ⇒ <code>number</code>
Get/set the projection. \
0: perspective, 1: cylindrical

<a name="module_PeakFinder.Settings..showSun"></a>

### PeakFinder.Settings~showSun() ⇒ <code>number</code>
Get/set display of the sun ecliptic. \
0: hide, 1: show

<a name="module_PeakFinder.Settings..showMoon"></a>

### PeakFinder.Settings~showMoon() ⇒ <code>number</code>
Get/set display of the moon ecliptic. \
0: hide, 1: show

<a name="module_PeakFinder.Settings..showGrid"></a>

### PeakFinder.Settings~showGrid() ⇒ <code>number</code>
Get/set display of the coordinate grid. \
0: hide, 1: show

<a name="module_PeakFinder.Settings..visibilityRange"></a>

### PeakFinder.Settings~visibilityRange() ⇒ <code>number</code>
Get/set the visiblitiy range in meters. \
valid range: 0..320000 (320km, 200mil)

<a name="module_PeakFinder.Settings..minimalElevation"></a>

### PeakFinder.Settings~minimalElevation() ⇒ <code>number</code>
Get/set the minimal elevation for the displayed peak names. \
valid range: 0..10000 (10000m, 32000feet)


* * *

## PeakFinder.viewpoint

These methods return information about the current viewpoint.

<a name="module_PeakFinder.Viewpoint..name"></a>

### PeakFinder.Viewpoint~name() ⇒ <code>String</code>
Gets the viewpoint name.

**Returns**: <code>String</code> - the viewpoint name  
<a name="module_PeakFinder.Viewpoint..coordsdecimal"></a>

### PeakFinder.Viewpoint~coordsdecimal() ⇒ <code>String</code>
Gets the viewpoint coordinates in decimal format.

**Returns**: <code>String</code> - the coordinates in decimal format (e.g. 46.53722°N, 8.12610°E)  
<a name="module_PeakFinder.Viewpoint..coordsdegree"></a>

### PeakFinder.Viewpoint~coordsdegree() ⇒ <code>String</code>
Gets the viewpoint coordinates in degree format.

**Returns**: <code>String</code> - the coordinates in degreee format (e.g. 46°32'13''N, 8°07'33''E)  
<a name="module_PeakFinder.Viewpoint..elevation"></a>

### PeakFinder.Viewpoint~elevation() ⇒ <code>number</code>
Gets the viewpoint elevation in meters.

**Returns**: <code>number</code> - the elevation in meters  

* * *

## PeakFinder.astro

These methods can be used to set the current date/time and to return sunrise/sunset, moonrise/moonset times.

<a name="module_PeakFinder.Astro..currentDateTime"></a>

### PeakFinder.Astro~currentDateTime(year, month, day, hour, minute)
Sets the date/time for the caluclation of sun and moon times


| Param | Type | Description |
| --- | --- | --- |
| year | <code>number</code> |  |
| month | <code>number</code> | (1..12) |
| day | <code>number</code> | (1..31) |
| hour | <code>number</code> |  |
| minute | <code>number</code> |  |

**Example**  
```js
panel.astro.currentDateTime(2022, 7, 12, 14, 30)
```
<a name="module_PeakFinder.Astro..currentDateTimeNow"></a>

### PeakFinder.Astro~currentDateTimeNow()
Sets the date/time to now

<a name="module_PeakFinder.Astro..sunTimes"></a>

### PeakFinder.Astro~sunTimes() ⇒ <code>String</code>
Gets the time of sunrise, sunset.

**Returns**: <code>String</code> - the times (e.g. '↑05:54, ↓21:17')  
<a name="module_PeakFinder.Astro..moonTimes"></a>

### PeakFinder.Astro~moonTimes() ⇒ <code>String</code>
Gets the time of moonrise, moonset.

**Returns**: <code>String</code> - the times (e.g. '↑07:13, ↓22:33, 3.4%')  

* * *

## PeakFinder.telescope

These methods can be used to show/hide telescope and get azimut, altitude, distance and elevation.

<a name="module_PeakFinder.Telescope..show"></a>

### PeakFinder.Telescope~show()
Shows the telescope

**Example**  
```js
panel.telescope.show()
```
<a name="module_PeakFinder.Telescope..hide"></a>

### PeakFinder.Telescope~hide()
Hide the telescope

<a name="module_PeakFinder.Telescope..centerAzimut"></a>

### PeakFinder.Telescope~centerAzimut() ⇒ <code>Number</code>
Get the azimut of the telecope center

**Returns**: <code>Number</code> - azimut  
<a name="module_PeakFinder.Telescope..centerAltitude"></a>

### PeakFinder.Telescope~centerAltitude() ⇒ <code>Number</code>
Get the altitude of the telecope center

**Returns**: <code>Number</code> - altitude  
<a name="module_PeakFinder.Telescope..centerDistance"></a>

### PeakFinder.Telescope~centerDistance() ⇒ <code>Number</code>
Get the distance of the telecope center

**Returns**: <code>Number</code> - distance  
<a name="module_PeakFinder.Telescope..centerElevation"></a>

### PeakFinder.Telescope~centerElevation() ⇒ <code>Number</code>
Get the elevation of the telecope center

**Returns**: <code>Number</code> - elevation  

* * *

## PeakFinder.utils

The following static util functions may be used for the initialization of the module.

<a name="module_PeakFinder.utils.caniuse"></a>

### PeakFinder.utils.caniuse() ⇒ <code>Boolean</code>
Checks if the browser supports the required technoligies to display the PeakFinder PanoramaPanel.

**Returns**: <code>Boolean</code> - True if showing PeakFinder module is supported  
<a name="module_PeakFinder.utils.isTouchDevice"></a>

### PeakFinder.utils.isTouchDevice() ⇒ <code>Boolean</code>
Checks if device has a touch screen.

**Returns**: <code>Boolean</code> - True if its a touch device  
<a name="module_PeakFinder.utils.sleep"></a>

### PeakFinder.utils.sleep(timeout)
Non-blocking sleep function. Use this function to wait for a result of an async call.


| Param | Type | Description |
| --- | --- | --- |
| timeout | <code>number</code> | in seconds |

**Example**  
```js
panel.astro.currentDateTime(2022, 7, 12, 14, 30)

// it takes a moment until the suntimes are evaluated. so sleep for a second.
await PeakFinder.utils.sleep(1.0)
console.log(panel.astro.sunTimes())
```

* * *


@ [https://www.peakfinder.com](www.peakfinder.com)