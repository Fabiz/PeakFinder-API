
# PeakFinder-API

This page contains information about embedding the [PeakFinder](https://wwwpeakfinder.org) mountain panorama module to your website.

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

Check out this example page: [basicexample_iframe.html](https://fabiz.github.io/PeakFinder-API/basicexample_canvas.html).
\
\
You must do the following steps: 
- Include Javascript
- Create canvas tag
- Load script

### Include Javascript

Include https://www.peakfinder.org/script/peakfinder.min.js in your html header:

```html
   <script async type="text/javascript" src="https://www.peakfinder.org/script/peakfinder.min.js"></script>
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
 
 Add the following script to load the module

```javascript
function wasmcallback(cmd) {
  console.log('command: ' + cmd)
}

window.onload = () => {
  if (PFWasm.hasWasmSupport()) {
    let wasm = new PFWasm('pfcanvas') // attach to canvas
    wasm.registercallback(wasmcallback)
    wasm.load('en')
  } else {
    console.log('This browser does not support webassembly')
  }
}
```




