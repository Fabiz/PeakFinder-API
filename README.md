# PeakFinder-API

This page contains information about embedding the [PeakFinder](https://www.peakfinder.com) mountain module to your website.

PeakFinder supports 3 different methods to embed a panel to a website:
- Url-Format: Create a link including latitude/longitude and some optional parameters to link to the [PeakFinder](https://www.peakfinder.com) website
- Embed with iFrame: Add an iFrame container to your website with latitude/longitude
- Embed with Canvas: Use Javascript for full control of the panel

With the canvas method you choose between two panel types: the **Panorama Panel**, which draws the
mountain panorama, and the **Map Panel**, which draws the map. Both are attached to an html canvas
and share the same setup.


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

More examples see here: [peakfinder.com/about/resources/apitest/](https://www.peakfinder.com/about/resources/apitest/)

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
| date | Current date (optional). Used for Sun and moon paths. | ISO 8601 date format: 2025-06-18T13:45:13Z |
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

Check out these example pages: [basicexample_canvas.html](https://fabiz.github.io/PeakFinder-API/basicexample_canvas.html) (panorama) and [basicexample_canvas_map.html](https://fabiz.github.io/PeakFinder-API/basicexample_canvas_map.html) (map).
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

For a map instead of a panorama create a `MapPanel`:

```javascript

if (PeakFinder.utils.caniuse()) {

  let panel = new PeakFinder.MapPanel({
    canvasid: 'pfcanvas',
    locale: 'en',
    mapstyle: 'toner',  // 'toner' | 'bright' | 'monochrome-light' | 'toner-flatwood'
    lat: 46.53722,      // the position the map opens at
    lng: 8.12610,
    zoom: 12
  })

  panel.init(async function() {
    // inside here its save to use the panel

    panel.addEventListener('camera changed', function(camera) {
      console.log(`camera changed ${JSON.stringify(camera)}`)
    })

    await panel.flyTo(45.97639, 7.65833, 13.0) // fly to the Matterhorn
  });
}
```


* * *

# Javascript API Reference

## Version 1.0

### Common panel functions

These functions are available on both the PanoramaPanel and the MapPanel.


<a name="module_PeakFinder..addEventListener"></a>

### PeakFinder~addEventListener(eventname, callback)
Registers an event listenster that receives events from the panel.
This method must be called after the init() resp. asycinit() methode.
The PanoramaPanel supports the following events:
- 'viewpointjourney finished' : all data for a new viewpoint has been loaded 
- 'viewpoint changed' : viewpoint has changed
- 'sun changed': sun times have beeen changed. 
- 'moon changed': moon times have beeen changed. 
- 'poiinfo show': user has clicked to a peak name or uses the telescope. 

The MapPanel supports these:
- 'map loaded': the style and the visible tiles have been loaded - the map has pixels.
- 'camera changed': the camera came to rest after a flight or a gesture. The event data holds
  the camera: {"lat":46.53722,"lng":8.12610,"zoom":12,"bearing":0,"pitch":0}
- 'map flight finished': a flyTo() animation has landed. The event data holds the camera.
- 'back pressed': the user pressed the map's back button (hidden by default, see
  settings.showBackButton).


| Param | Type | Description |
| --- | --- | --- |
| eventname | <code>string</code> | The name of the event (see list above) |
| callback | <code>function</code> | This function will be called when the requested event is dispached. 'args' will include event data. |

**Example**  
```js
panel.addEventListener('viewpointjourney finished', async function(args) {
  console.log(`viewpoint ready ${JSON.stringify(args)}`)
})
```
<a name="module_PeakFinder..registerCommandsCallback"></a>

### PeakFinder~registerCommandsCallback(command)
Registers a callback that receives commands/messages from the panel.
The panel will send a message when a specific event occured. E.g. when a
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
Loads all the needed stuff for displaying the panel. Call this method only once.
The async callback will inform when the panel is ready. After this call additional
commands like <code>loadViewpoint</code> (panorama) or <code>jumpTo</code> (map) may be called.


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
Loads all the needed stuff for displaying the panel. Call this method only once.
Same as the init function but with support for the Javascript async pattern. After this call additional
commands like <code>loadViewpoint</code> (panorama) or <code>jumpTo</code> (map) may be called.

**Example**  
```js
async panel.asyncinit()

console.log('ready')
panel.loadViewpoint(46.53722, 8.12610, 'Finsteraarhorn')
```

* * *

## PeakFinder.PanoramaPanel

The panorama panel: a canvas rendering the PeakFinder mountain panorama. In addition to the
functions below it carries the <code>settings</code>, <code>style</code>, <code>viewpoint</code>,
<code>astro</code> and <code>telescope</code> sub objects documented further down.

<a name="module_PeakFinder.PanoramaPanel..PeakFinder"></a>

### PeakFinder.PanoramaPanel~PeakFinder : <code>object</code>
Constructor: Initialization of the PeakFinder PanoramaPanel. Pass the options in a Javascript dictionary:

**Properties**

| Name | Type | Description |
| --- | --- | --- |
| canvasid | <code>string</code> | The id of the html canvas element. Default: 'canvas' |
| locale | <code>string</code> | The language locale of the module. Default: 'en'. Supported locales: en,de,fr,it,es,pt,ja,ko,zh-Hans,zh-Hant |
| bgcolor | <code>string</code> | A custom color for the background/sky. Normally the sky is white. For another color use the format '#rrggbb' (e.g. #87CEEB for sky color). |
| theme | <code>string</code> | 'dark' for dark-theme. otherwise 'light' theme will be shown |
| disableinfosheets | <code>boolean</code> | Disables showing the poi infosheet or the viewpoint infosheet when the users click on a peak label or the viewpoint |

**Example**  
```js
let panel = new PeakFinder.PanoramaPanel({
  canvasid: 'pfcanvas', 
  locale: 'en'
}) // attach to canvas
```
<a name="module_PeakFinder.PanoramaPanel..loadViewpoint"></a>

### PeakFinder.PanoramaPanel~loadViewpoint(latitude, longitude, name, options)
Loads a viewpoint with the given coordinates and an optional name


| Param | Type | Description |
| --- | --- | --- |
| latitude | <code>number</code> |  |
| longitude | <code>number</code> |  |
| name | <code>string</code> | The viewpoint name. Optional |
| options | <code>Object</code> | Additional settings. Optional |
| options.animation | <code>string</code> | How the panorama moves to the new viewpoint. 'fly' (the default) uses the PeakFinder journey animation - it walks, flies or teleports depending on the distance to the current viewpoint. 'teleport' skips the animation and shows the new viewpoint immediately. |

**Example**  
```js
panel.loadViewpoint(46.53722, 8.12610, 'Finsteraarhorn') // animated (default)

panel.loadViewpoint(46.53722, 8.12610, 'Finsteraarhorn', {
  animation: 'teleport', // no animation
})
```
<a name="module_PeakFinder.PanoramaPanel..viewpointJourneyFinished"></a>

### PeakFinder.PanoramaPanel~viewpointJourneyFinished() ⇒ <code>boolean</code>
Checks if the viewpoint journey has been finished.

<a name="module_PeakFinder.PanoramaPanel..azimut"></a>

### PeakFinder.PanoramaPanel~azimut(val, animationduration) ⇒ <code>number</code>
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
<a name="module_PeakFinder.PanoramaPanel..altitude"></a>

### PeakFinder.PanoramaPanel~altitude(val, animationduration) ⇒ <code>number</code>
Get/set altitude.


| Param | Description |
| --- | --- |
| val | The altitude value in degrees |
| animationduration | The duration of the animation. If undefined no animation will be done. |

<a name="module_PeakFinder.PanoramaPanel..fieldofview"></a>

### PeakFinder.PanoramaPanel~fieldofview(val, animationduration) ⇒ <code>number</code>
Get/set field of view (zoom).


| Param | Description |
| --- | --- |
| val | The field of view (zoom) value in degrees |
| animationduration | The duration of the animation. If undefined no animation will be done. |

<a name="module_PeakFinder.PanoramaPanel..elevationOffset"></a>

### PeakFinder.PanoramaPanel~elevationOffset(val, animationduration) ⇒ <code>number</code>
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

## PeakFinder.MapPanel

The map panel: a canvas rendering the PeakFinder map. It supports the common panel functions
(init, asyncinit, addEventListener, registerCommandsCallback) plus the camera functions below and
the <code>settings</code> sub object documented further down.

<a name="module_PeakFinder.MapPanel..PeakFinder"></a>

### PeakFinder.MapPanel~PeakFinder : <code>object</code>
Constructor: Initialization of the PeakFinder MapPanel. Pass the options in a Javascript dictionary:

**Properties**

| Name | Type | Description |
| --- | --- | --- |
| canvasid | <code>string</code> | The id of the html canvas element. Default: 'canvas' |
| locale | <code>string</code> | The language locale of the module. Default: 'en'. Supported locales: en,de,fr,it,es,pt,ja,ko,zh-Hans,zh-Hant |
| mapstyle | <code>string</code> | The style the map is drawn with: 'toner' (the default), 'bright', 'monochrome-light', 'toner-flatwood' or 'relief' (a diagnostic style painting the elevation model itself). An unknown name falls back to the default style. |
| lat | <code>number</code> | Latitude of the position the map opens at. Optional |
| lng | <code>number</code> | Longitude of the position the map opens at. Optional |
| zoom | <code>number</code> | Zoom level the map opens at. Optional, defaults to 12 when lat/lng are given. Without lat/lng the map opens on an overview of the Alps. |
| bearing | <code>number</code> | Bearing in degrees from true north the map opens at. Optional |
| pitch | <code>number</code> | Pitch in degrees the map opens at. 0 is a two-dimensional map. Optional |
| theme | <code>string</code> | 'dark' for dark-theme. otherwise 'light' theme will be shown |

**Example**  
```js
let panel = new PeakFinder.MapPanel({
  canvasid: 'pfcanvas',
  locale: 'en',
  mapstyle: 'toner',
  lat: 46.53722,
  lng: 8.12610,
  zoom: 12
}) // attach to canvas
```
<a name="module_PeakFinder.MapPanel..jumpTo"></a>

### PeakFinder.MapPanel~jumpTo(latitude, longitude, zoom, options)
Moves the camera to the given position without any animation.


| Param | Type | Description |
| --- | --- | --- |
| latitude | <code>number</code> |  |
| longitude | <code>number</code> |  |
| zoom | <code>number</code> | The zoom level. Optional - the current zoom is kept when it is omitted. |
| options | <code>Object</code> | Additional camera settings. Optional |
| options.bearing | <code>number</code> | Bearing in degrees from true north |
| options.pitch | <code>number</code> | Pitch in degrees. 0 is a two-dimensional map |

**Example**  
```js
panel.jumpTo(46.53722, 8.12610, 12)

panel.jumpTo(46.53722, 8.12610, 12, { bearing: 45.0, pitch: 30.0 })
```
<a name="module_PeakFinder.MapPanel..easeTo"></a>

### PeakFinder.MapPanel~easeTo(latitude, longitude, zoom, options)
Moves the camera to the given position with a transition of a fixed duration. Use this for short
moves, where <code>flyTo</code>'s zoom-out arc would look exaggerated.


| Param | Type | Description |
| --- | --- | --- |
| latitude | <code>number</code> |  |
| longitude | <code>number</code> |  |
| zoom | <code>number</code> | The zoom level. Optional - the current zoom is kept when it is omitted. |
| options | <code>Object</code> | Additional settings. Optional |
| options.duration | <code>number</code> | The duration of the animation in seconds. Default: 1.0 |
| options.bearing | <code>number</code> | Bearing in degrees from true north |
| options.pitch | <code>number</code> | Pitch in degrees. 0 is a two-dimensional map |

**Example**  
```js
await panel.easeTo(46.53722, 8.12610, 12, { duration: 1.0 })
```
<a name="module_PeakFinder.MapPanel..flyTo"></a>

### PeakFinder.MapPanel~flyTo(latitude, longitude, zoom, options)
Flies the camera to the given position: the map zooms out, travels and zooms back in. Without a
duration the flight takes as long as its distance warrants.
The returned promise resolves when the flight has landed - the same moment the
'map flight finished' event is dispatched.


| Param | Type | Description |
| --- | --- | --- |
| latitude | <code>number</code> |  |
| longitude | <code>number</code> |  |
| zoom | <code>number</code> | The zoom level. Optional - the current zoom is kept when it is omitted. |
| options | <code>Object</code> | Additional settings. Optional |
| options.duration | <code>number</code> | The duration of the flight in seconds. Optional |
| options.bearing | <code>number</code> | Bearing in degrees from true north |
| options.pitch | <code>number</code> | Pitch in degrees. 0 is a two-dimensional map |

**Example**  
```js
await panel.flyTo(45.97639, 7.65833, 13.0) // the engine picks the duration

await panel.flyTo(45.97639, 7.65833, 13.0, { duration: 2.0 })
```
<a name="module_PeakFinder.MapPanel..camera"></a>

### PeakFinder.MapPanel~camera() ⇒ <code>Object</code>
Gets the current camera.

**Returns**: <code>Object</code> - the camera (e.g. {"lat":46.53722,"lng":8.12610,"zoom":12,"bearing":0,"pitch":0})  
**Example**  
```js
const camera = panel.camera()
console.log(`${camera.lat}, ${camera.lng} @ ${camera.zoom}`)
```
<a name="module_PeakFinder.MapPanel..mapstyle"></a>

### PeakFinder.MapPanel~mapstyle(val) ⇒ <code>String</code>
Get/set the style the map is drawn with. In contrast to the <code>mapstyle</code> constructor
option this may be called at any time.


| Param | Description |
| --- | --- |
| val | The style name: 'toner', 'bright', 'monochrome-light', 'toner-flatwood' or 'relief'. An unknown name falls back to the default style. |

**Example**  
```js
panel.mapstyle('bright') // switch the style

const style = panel.mapstyle() // gets 'bright'
```
<a name="module_PeakFinder.MapPanel..addOverlay"></a>

### PeakFinder.MapPanel~addOverlay(id, geojson, layersjson, optionsjson)
Adds a GeoJSON overlay: one source holding the document plus the style layers drawn from it. The
overlay is identified by <code>id</code> for every later call, and adding an id that already exists
replaces it. Overlays are drawn above the map style, in the order they were added, and they survive
a <code>mapstyle</code> change.

<code>layersjson</code> is an array of MapLibre style layers, so the whole expression language is
available. Only what is specific to the layer needs to be given: the source is bound to this overlay
and a missing layer id is filled in. Leave it out for a plain 2px outline that takes its colour and
opacity from each feature's own <code>color</code> and <code>opacity</code> properties.


| Param | Type | Description |
| --- | --- | --- |
| id | <code>string</code> | The name of the overlay |
| geojson | <code>string</code> | The GeoJSON document, as text |
| layersjson | <code>string</code> | The style layers, as text. Optional |
| optionsjson | <code>string</code> | How the document is tiled, as text: a json object taking any of 'minzoom', 'maxzoom', 'buffer' and 'tolerance' - the same options a style's geojson source takes. Optional. These control simplification and tile extent, not memory: tiles are built on demand, so what a document costs is the document itself. |

**Example**  
```js
panel.addOverlay('vfpv3', geojsontext) // default outline, coloured per feature

panel.addOverlay('vfpv3', geojsontext, JSON.stringify([{
  type: 'line',
  minzoom: 5,
  paint: {
    'line-color': ['get', 'color'],
    'line-width': 2
  }
}]))

// coarse shapes: simplify harder and stop refining early
panel.addOverlay('vfpv3', geojsontext, undefined, '{"maxzoom":10,"tolerance":1}')
```
<a name="module_PeakFinder.MapPanel..setOverlayData"></a>

### PeakFinder.MapPanel~setOverlayData(id, geojson)
Replaces the overlay's document, keeping its layers and its visibility. Use this rather than
<code>addOverlay</code> when new data arrives for an overlay that is already on the map.


| Param | Type | Description |
| --- | --- | --- |
| id | <code>string</code> | The name of the overlay |
| geojson | <code>string</code> | The GeoJSON document, as text |

**Example**  
```js
const response = await fetch('/geojson/demorigins?source=vfpv3')
panel.setOverlayData('vfpv3', await response.text())
```
<a name="module_PeakFinder.MapPanel..showOverlay"></a>

### PeakFinder.MapPanel~showOverlay(id, show)
Shows or hides an overlay. The document stays loaded, so switching an overlay off and on again
costs nothing.


| Param | Type | Description |
| --- | --- | --- |
| id | <code>string</code> | The name of the overlay |
| show | <code>boolean</code> |  |

**Example**  
```js
panel.showOverlay('vfpv3', false)
```
<a name="module_PeakFinder.MapPanel..removeOverlay"></a>

### PeakFinder.MapPanel~removeOverlay(id)
Removes an overlay and frees its document.


| Param | Type | Description |
| --- | --- | --- |
| id | <code>string</code> | The name of the overlay |

<a name="module_PeakFinder.MapPanel..overlays"></a>

### PeakFinder.MapPanel~overlays() ⇒ <code>Array</code>
Gets the names of the overlays currently on the map, in the order they were added.

**Returns**: <code>Array</code> - the overlay ids (e.g. ['best', 'vfpv3'])  
<a name="module_PeakFinder.MapPanel..queryFeatures"></a>

### PeakFinder.MapPanel~queryFeatures(x, y) ⇒ <code>Array</code>
Gets the features drawn at a point on the canvas, topmost last. Use it to react to a click - the
panel draws no popups of its own, so the page decides what to show.

The coordinates are css pixels relative to the canvas, which is what a mouse event's
<code>offsetX</code>/<code>offsetY</code> give - on a high dpi display they are scaled to the
canvas' backing store here. <code>source</code> is the overlay the feature came from.

**Returns**: <code>Array</code> - the features (e.g. [{"source":"vfpv3","sourceLayer":"","id":42,"properties":{"name":"N46E008","color":"#89dbec"}}])  

| Param | Type |
| --- | --- |
| x | <code>number</code> | 
| y | <code>number</code> | 

**Example**  
```js
canvas.addEventListener('click', function (event) {
  const hits = panel.queryFeatures(event.offsetX, event.offsetY)
  if (hits.length) console.log(hits[hits.length - 1].properties.name)
})
```

* * *

## PeakFinder.PanoramaPanel.settings

The following setters and getters manage the settings of the panorama panel.

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

<a name="module_PeakFinder.Settings..showZoomButtons"></a>

### PeakFinder.Settings~showZoomButtons() ⇒ <code>number</code>
Get/set the visibility of the +/- zoom buttons in the upper left corner. \
0: hide, 1: show

**Example**  
```js
panel.settings.showZoomButtons(0) // hide the zoom buttons
```
<a name="module_PeakFinder.Settings..showElevationOffsetControl"></a>

### PeakFinder.Settings~showElevationOffsetControl() ⇒ <code>number</code>
Get/set the visibility of the elevation offset control on the left hand side. \
0: hide, 1: show

<a name="module_PeakFinder.Settings..showSliders"></a>

### PeakFinder.Settings~showSliders() ⇒ <code>number</code>
Get/set the visibility of the slider button in the lower left corner. Hiding it also closes
the sliders it opens (date, time, visibility range, minimal elevation). \
0: hide, 1: show


* * *

## PeakFinder.MapPanel.settings

The following setters and getters manage the settings of the map panel. The panorama settings that
have no meaning on a map (sun, moon, grid, projection, visibility range, ...) are not available here.

<a name="module_PeakFinder.MapSettings..theme"></a>

### PeakFinder.MapSettings~theme() ⇒ <code>number</code>
Get/set theme. \
0: light, 1: dark

**Example**  
```js
panel.settings.theme(1) // set to dark

const theme = panel.settings.theme() // gets dark
```
<a name="module_PeakFinder.MapSettings..distanceUnit"></a>

### PeakFinder.MapSettings~distanceUnit() ⇒ <code>number</code>
Get/set distance unit. Changing it reloads the map style, so the elevation labels are redrawn in
the matching unit. \
0: metric, 1: imperial

**Example**  
```js
panel.settings.distanceUnit(1) // set to imperial

const unit = panel.settings.distanceUnit() // gets imperial
```
<a name="module_PeakFinder.MapSettings..showBackButton"></a>

### PeakFinder.MapSettings~showBackButton() ⇒ <code>number</code>
Get/set the visibility of the back button in the upper left corner. It is hidden by default on an
embedded map, where there is nothing to go back to. When it is shown, pressing it dispatches the
'back pressed' event. \
0: hide, 1: show

**Example**  
```js
panel.settings.showBackButton(1) // show the back button

panel.addEventListener('back pressed', function () {
  history.back()
})
```
<a name="module_PeakFinder.MapSettings..showStartupMarker"></a>

### PeakFinder.MapSettings~showStartupMarker() ⇒ <code>number</code>
Get/set the visibility of the marker on the position the map opened at. \
0: hide, 1: show

**Example**  
```js
panel.settings.showStartupMarker(0) // hide the marker
```

* * *

## PeakFinder.style

These setters and getters manage the appearance of the panorama panel. In contrast to the
corresponding constructor options they may be used at any time. Panorama panel only.

<a name="module_PeakFinder.Style..backgroundColor"></a>

### PeakFinder.Style~backgroundColor(val) ⇒ <code>String</code>
Get/set the color of the background/sky. \
Normally the sky is white. Use the format '#rrggbb' (e.g. '#87ceeb' for sky color). \
In contrast to the <code>bgcolor</code> constructor option this may be called at any time.


| Param | Description |
| --- | --- |
| val | The background color in the format '#rrggbb'. Named css colors (e.g. 'skyblue') are supported as well. |

**Example**  
```js
panel.style.backgroundColor('#87ceeb') // set the sky to sky blue

const color = panel.style.backgroundColor() // gets '#87ceeb'
```

* * *

## PeakFinder.viewpoint

These methods return information about the current viewpoint. Panorama panel only.

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

These methods can be used to set the current date/time and to return sunrise/sunset, moonrise/moonset times. Panorama panel only.

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

### PeakFinder.Astro~sunTimes() ⇒ <code>Object</code>
Gets the time of sunrise, sunset.

**Returns**: <code>Object</code> - the sun times (e.g. {"sun":{"rise":"2025-04-07T06:50:59Z","set":"2025-04-07T20:11:59Z"}} )  
<a name="module_PeakFinder.Astro..sunTimes"></a>

### ~~PeakFinder.Astro~sunTimes() ⇒ <code>String</code>~~
***Use method sun instead***

Gets the time of sunrise, sunset.

**Returns**: <code>String</code> - the times (e.g. '↑05:54, ↓21:17')  
<a name="module_PeakFinder.Astro..moon"></a>

### PeakFinder.Astro~moon() ⇒ <code>Object</code>
Gets the time of moonrise, moonset.

**Returns**: <code>Object</code> - the sun times (e.g. {"moon":{"illum":"74.7%"},"sun":{"rise":"2025-04-07T14:11:59Z","set":"2025-04-08T05:32:59Z"}}  
<a name="module_PeakFinder.Astro..moonTimes"></a>

### ~~PeakFinder.Astro~moonTimes() ⇒ <code>String</code>~~
***Use method moon instead***

Gets the time of moonrise, moonset.

**Returns**: <code>String</code> - the times (e.g. '↑07:13, ↓22:33, 3.4%')  

* * *

## PeakFinder.telescope

These methods can be used to show/hide telescope and get azimut, altitude, distance and elevation. Panorama panel only.

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
<a name="module_PeakFinder.utils.hasMultiThreadingSupport"></a>

### PeakFinder.utils.hasMultiThreadingSupport() ⇒ <code>Boolean</code>
Checks if browser supports multithreading.

**Returns**: <code>Boolean</code> - True if multithreading is available  
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