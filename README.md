
# PeakFinder-API

This page contains information about embedding the [PeakFinder](https://www.peakfinder.org) mountain panorama module to your website.

PeakFinder supports 3 different methods to embed the Panorama Panel to a website:
- Url-Format: Create a link including latitude/longitude and some optional parameters to link to the [PeakFinder](https://www.peakfinder.org) website
- Embed with iFrame: Add an iFrame container to your website with latitude/longitude
- Embed with Canvas: Use Javascript for full control of the Panorama Panel

## Url-Format

PeakFinder provides a simple interface that allows you to create a link to a certain viewpoint or to embed PeakFinder directly on your homepage as an iFrame.

PeakFinder URL Format
The following link opens the PeakFinder website with the given viewpoint:
https://www.peakfinder.org/?lat=42.8612&lng=-72.109&name=Monadnock&ele=941


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
<iframe src="https://www.peakfinder.org/embed/?lat=42.8612&lng=-72.1092&name=Monadnock%20Mountain&ele=941&zoom=5&azi=255" 
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

Include https://www.peakfinder.org/script/peakfinder.1.0.min.js in your html header:

```html
   <script async type="text/javascript" src="https://www.peakfinder.org/script/peakfinder.1.0.min.js"></script>
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
<a name="module_PeakFinder..loadViewpoint"></a>

### PeakFinder~loadViewpoint(Latitude, Longitude, The)
Loads a viewpoint with the given coordinates and an optional name


| Param | Type | Description |
| --- | --- | --- |
| Latitude | <code>number</code> |  |
| Longitude | <code>number</code> |  |
| The | <code>string</code> | viewpoint name. Optional |


* * *

## PeakFinder.settings

The following setters and getters manage the PeakFinder settings.

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

* * *


@ [https://www.peakfinder.org](www.peakfinder.org)