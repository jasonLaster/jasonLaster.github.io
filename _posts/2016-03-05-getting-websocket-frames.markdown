---
layout: post
title:  "Chrome DevTools - Filtering / Formatting Websocket frames"
date:   2016-03-05
categories: DevTools JS
---


As of today, Chrome DevTools does not have great support for seeing web socket frames. Chrome DevTools uses it's standard `DataTable` component, which supports columns with sorting. It does not support filtering, formatting objects, or adding additional columns.

These limitations are ironically probably best shown by inspecting the websocket connection from DevTools and the browser. DevTools is a rich app that sends hundreds of messages back and forth with a standard `{id, method, params, result}` format. There's also a standard that DevTool requests are matched with responses with the same ID.

It's easy to imagine a better UI for visualizing the request method and params, with the following response result and timing information.

In the meantime, we can do a little hacking and build a half decent substitute.

#### Original

![](http://f.cl.ly/items/070s0S1b1p3g2b3r0q0L/Screen%20Shot%202016-03-05%20at%2012.13.26%20PM.png)

#### New View

![](http://f.cl.ly/items/1b0N1f1l3d0j2G1u3O41/Screen%20Shot%202016-03-05%20at%2012.13.19%20PM.png)


What you need to do is to open a second DevTools (DT) to inspect the original. You can do this when DT is undocked and you hit <cmd> shift I. At this point, you can create a new snippet in the sources tab and add the code below. Or if you're feeling lazy drop the whole thing into the console.

```js
// Get the Network Tabs
var networkTabs  = WebInspector.panels.network._networkItemView.tabViews();

// Get the Websocket View from the network tabs
var WebsocketView = networkTabs[1];

// get the frames from the websocket table
var frames = WebsocketView._dataGrid._flatNodesList();

var frameData = frames.map(frame => JSON.parse(frame._dataText))

// log each frame

// frameData.forEach(frame => {
//     console.log(frame.id, frame.method, frame.params, frame.result);
// })

// show the table
var framesData = frameData.map(frame =>  {
    var {id, method, params, result} = frame;
    var params = JSON.stringify(params);
    var result = JSON.stringify(result);
    return {id, method,params , result}
});

console.table(framesData)
```
