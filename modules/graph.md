<!--- Copyright (c) 2017 Gordon Williams, Pur3 Ltd. See the file LICENSE for copying permission. -->
Graph Library
==============

<span style="color:red">:warning: **Please view the correctly rendered version of this page at https://www.espruino.com/graph. Links, lists, videos, search, and other features will not work correctly when viewed on GitHub** :warning:</span>

* KEYWORDS: Module,Modules,Graphics,Graph,Graphs,Line Graph,Chart
* USES: Graphics,Pixl.js

This is a very simple, lightweight graph library ([[graph.js]]) for displays that use
Espruino's [[Graphics]] library.

In it's simplest form, you just call `drawLine` with the [[Graphics]] instance
as the first argument and the data as the second:

```
var data = [0,1,3,8,10,12,12,10,8,3,1,0];
require("graph").drawLine(g, data);
// Remember to call `g.flip()` to update your display if needed
```

By default the data is auto-scaled to fit the screen:

![graph](data:image/bmp;base64,Qk0gBAAAAAAAACAAAAAMAAAAgABAAAEAAQD///8AAABgAAAAAAAAAAAAAAAAAAAGGAAAAAAAAAAAAAAAAAAAGAYAAAAAAAAAAAAAAAAAAGABgAAAAAAAAAAAAAAAAAGAAGAAAAAAAAAAAAAAAAAGAAAQAAAAAAAAAAAAAAAACAAACAAAAAAAAAAAAAAAABAAAAYAAAAAAAAAAAAAAAAgAAABAAAAAAAAAAAAAAAAwAAAAIAAAAAAAAAAAAAAAQAAAABAAAAAAAAAAAAAAAIAAAAAIAAAAAAAAAAAAAAEAAAAABgAAAAAAAAAAAAACAAAAAAEAAAAAAAAAAAAADAAAAAAAgAAAAAAAAAAAABAAAAAAAEAAAAAAAAAAAAAgAAAAAABAAAAAAAAAAAAAIAAAAAAAIAAAAAAAAAAAAEAAAAAAACAAAAAAAAAAAABAAAAAAAAQAAAAAAAAAAAAgAAAAAAAEAAAAAAAAAAAAIAAAAAAABAAAAAAAAAAAACAAAAAAAAIAAAAAAAAAAABAAAAAAAACAAAAAAAAAAAAQAAAAAAAAQAAAAAAAAAAAIAAAAAAAAEAAAAAAAAAAACAAAAAAAAAgAAAAAAAAAAAgAAAAAAAAIAAAAAAAAAAAQAAAAAAAACAAAAAAAAAAAEAAAAAAAAAQAAAAAAAAAACAAAAAAAAAEAAAAAAAAAAAgAAAAAAAAAgAAAAAAAAAAQAAAAAAAAAIAAAAAAAAAAEAAAAAAAAACAAAAAAAAAABAAAAAAAAAAQAAAAAAAAAAgAAAAAAAAAEAAAAAAAAAAIAAAAAAAAAAgAAAAAAAAAEAAAAAAAAAAIAAAAAAAAABAAAAAAAAAACAAAAAAAAAAQAAAAAAAAAAQAAAAAAAAAIAAAAAAAAAAEAAAAAAAAACAAAAAAAAAAAgAAAAAAAABAAAAAAAAAAAIAAAAAAAAAQAAAAAAAAAABAAAAAAAAAIAAAAAAAAAAAIAAAAAAAAEAAAAAAAAAAABAAAAAAAACAAAAAAAAAAAAIAAAAAAABAAAAAAAAAAAABgAAAAAAAgAAAAAAAAAAAAEAAAAAAAwAAAAAAAAAAAAAgAAAAAAQAAAAAAAAAAAAAEAAAAAAIAAAAAAAAAAAAAAgAAAAAEAAAAAAAAAAAAAAEAAAAACAAAAAAAAAAAAAAAgAAAABAAAAAAAAAAAAAAAEAAAAAgAAAAAAAAAAAAAAAwAAAAQAAAAAAAAAAAAAAACAAAAYAAAAAAAAAAAAAAAAQAAAIAAAAAAAAAAAAAAAACAAAEAAAAAAAAAAAAAAAAAQAACAAAAAAAAAAAAAAAAADAABAAAAAAAAAAAAAAAAAAIABgAAAAAAAAAAAAAAAAABAAgAAAAAAAAAAAAAAAAAAP/wAAAAAAAAA)

But there are other options you can supply via an object as the third argument:

* `title` - the title of the graph
* `miny` - optional - minimum y value
* `maxy` - optional - maximum y value
* `gridy` - optional - grid value for y. Also enables labels on axis
* `x` - optional - pixel x offset on screen
* `y` - optional - pixel y offset on screen
* `width` - optional - pixel width on screen
* `height` - optional - pixel height on screen
* `ylabel` - optional - function(y_value) to format y axis labels, eg: `function(y) { return y.toFixed(1); }`

For example:

```
var data = [0,1,3,8,10,12,12,10,8,3,1,0];
require("graph").drawLine(g, data, {
  miny: 0,
  axes : true,
  gridy : 5
});
```

Draws a graph with axes, auto-scaled to the nearest `5` points and labelled:

![graph](data:image/bmp;base64,Qk0gBAAAAAAAACAAAAAMAAAAgABAAAEAAQD///8AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABwAAAAAAAAAAAAAAAAAAAAiAAAAAAAAAAAAAAAAAAAAHf///////////////////8C4AAAAAAAAAAAAAAAAAAOAhgAAAAAAAAAAAAAAAAAcAIHAAAAAAAAAAAAAAAAA4ACAMAAAAAAAAAAAAAAAAwAAgAwAAAAAAAAAAAAAAAwAAIACAAAAAAAAAAAAAAAQAACAAQAAAAAAAAAAAAAAIAAAgADAAAAAAAAAAAAAAMAAAIAAIAAAAAAAAAAAAAEAAACAABAAAAAAAAAAAAACAAAAgAAMAAAAAAAAAAAADAAAAIAAAgAAAAAAAAAAABAAAACAAAEAAAAAAAAAAAAgAAAAgAABAAAAAAAAAAAAIAAAAIAAAIAAAAAAAAAAAEAAAACAAACAAAAAAAAAAABAAAAAgAAAQAAAAAAAAAAAgAAAEoAAAEAAAAAAAAAAAIAAACqAAAAgAAAAAAAAAAEAAAAvwAAAEAAAAAAAAAACAAAAAIAAABAAAAAAAAAAAgAAAACAAAAIAAAAAAAAAAQAAAAAgAAACAAAAAAAAAAEAAAAAIAAAAQAAAAAAAAACAAAAACAAAACAAAAAAAAABAAAAAAgAAAAgAAAAAAAAAQAAAAAIAAAAEAAAAAAAAAIAAAAACAAAABAAAAAAAAACAAAAAAgAAAAIAAAAAAAABAAAAAAIAAAACAAAAAAAAAQAAAAACAAAAAQAAAAAAAAIAAAAAAgAAAADAAAAAAAAMAAAAAAIAAAAAIAAAAAAAEAAAAAACAAAAABAAAAAAACAAAAAAcgAAAAAMAAAAAADAAAAAAIoAAAAAAgAAAAABAAAAAAByAAAAAAEAAAAAAgAAAAAAAgAAAAAAwAAAAAwAAAAAAIcAAAAAACAAAAAQAAAAAAD6AAAAAAAYAAAAYAAAAAAAkgAAAAAABAAAAIAAAAAAAAIAAAAAAAIAAAEAAAAAAAACAAAAAAABgAAGAAAAAAAAAgAAAAAAAEAACAAAAAAAAAIAAAAAAAAgABAAAAAAAAACAAAAAAAAGABgAAAAAAAAAgAAAAAAAAf/gAAAAAAAAAIAAAAAAAAAAAAAAAAAAAACAAAAAAAAAAAAAAAAAAAAAgAAAAAAAAAAAAAAAAAAAAIAAAAAAAAAAAAAAAAAAAACAAAAAAAAAAAAAAAAAAAAAgAAAAAAAAAAAAAAAAAAAAIAAAAAAAAAAAAAAAAAAAACAAAAAAAAAAAAAAAAAAAAAgAAAAAAAAAAAAAAAAAAAAIAAAAAAAAAAAAAAAAAAAAHAAAAAAAAAAAAAAAAAAAA)

The `drawLine` function also returns an object, containing:

```
{
  x,    // top left corner of the graph x coordinate
  y,    // top left corner of the graph y coordinate
  w,    // graph width
  h,    // graph height
  getx, // function which given graph data x, returns a x coordinate
  gety, // function which given graph data y, returns a y coordinate
}
```


And to display a graph of the last 64 temperature readings from Espruino, taken
every 2 seconds you could do something like the following:

```
var history = new Float32Array(64);

function onTimer() {
  // quickly move all elements of history back one
  history.set(new Float32Array(history.buffer,4));
  // add new history element at the end
  var temp = E.getTemperature();
  history[history.length-1] = temp;
  //
  g.clear();
  // Draw Graph
  var r = require("graph").drawLine(g, history, {
    miny: 0,
    axes : true,
    gridy : 10,
    title: "Temperature"
  });
  // Label last reading
  g.setFontAlign(1,-1);
  g.drawString(Math.round(temp), r.x+r.w, r.gety(temp)+2);
  // Update the screen
  g.flip();
}

// Update temperature every 2 seconds
setInterval(onTimer,2000);
// Update temperature immediately
onTimer();
```

![graph](data:image/bmp;base64,Qk0gBAAAAAAAACAAAAAMAAAAgABAAAEAAQD///8AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABwAAAAAAAAAAAAAAAAAAAAiAAAAAAAAAAAAAAAAAAAAHf///////////////////8CAAAAAAAAAAAAAAAAAAAAAgAAAAAAAAAAAAAAAAAAAAIAAAAAAAAAAAAAAAAAAAACAAAAAAAAAAAAAAAAAAAAAgAAAAAAAAAAAAAAAAAAAAIAAAAAAAAAAAAAAAAAAAACAAAAAAAAAAAAAAAAAAAAAwAAAAAAAAAAAAAAAAAAAAMAAAAAAAAAAAAAAAAAAAADgAAAAAAAAAAAAAAAAAAAA4AAAAAAAAAAAAAAAAAAAAOAAAAAAAAAAAAAAAAAAAADQAAAAAAAACAAAAAAAAAAc0AAAAAAAAAgAAAAAAAAAIogAAAAAAAAIAAAAAAAAAByIAAAAAAAADAAAAAAAAAAAhAAAAAAAAAwAAAAAAAAAIcQAAAAAAAAMAAAAAAAAAD6CAAAAAAAAEgAAAAAAAAAkggAAAAAAABIAAAAAAAAAAIEAAAAAAAARAAAAAAAAAACBAAAAAAAAEIAAAAAAAAAAgIAAAAAAABCAAAAAAAAAAIBAAAAAAAAQQAAAAAAAAACAIAAAAAAAECAAAAAAAAAAgBgAAAAAABAYAAAAAAAAAIAEAAAAAAAQBgAAAAAAAACAAgAAAAAAEAEAAAAAAAAAgAGAAAAAABAAgAAAAAAAAIAAYAAAAAAQAGAAAAAAAByAAB4AAAAAIAAeAAAAAAAigAABgAAAACAAAYAAAAAAHIAAAHgAAAAgAAB4AAAAAACAAAAHAAAAIAAAB8AAAAAlwAAAAPAAACAAAAAgAABwKoAAAAAPwAAgAAAAH/AAjzKAAAAAAD8AQAAAAAAP8QDAgAAAAAAA/EAAAAAAAA0AAIAAAAAAAAOAAAAAAAACAACAAAAAAAAAAAAAAAAAADuAgAAAAAAAAAAAAAAAAAAhAIAAAAAAAAAAAAAAAAAAEQCAAAAAAAAAAAAAAAAAAAsAgAAAAAAAAAAAAAAAAAAxAIAAAAAAAAAAAAAAAAAAAACAAAAAAAAAAAAAAAAAAAAAgAAAAAAAAAAAAAAAAAAAHIAAAAAAAAAAAAAAAAAAACKAAAAAAAAAAAAAAAAAAAAcgAAAAAAAAAAAAAAAAAAAAIAAAAAAAAAAAAAAAAAAABXAAAAAAAAAAAAAAAAAAAAqAAAAAAAAEAAAAAAAAAAAIgAAAAAAjdjRxNDAAAAAAAAAAAAAAJHVEclRAAAAAAAAAAAAAACd1dhJWcAAAAAAAAAAAAAAiZiVnVSAAAAAAAAAAAAAAcAAAAgAAAAAAAA)

The code above will work as-is on [Pixl.js](/Pixl.js).