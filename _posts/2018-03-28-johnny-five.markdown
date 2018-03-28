---
title: "Johnny-Five in Production"
layout: post
image: /assets/images/mariana-b-604752-unsplash.jpg
headerImage: true
tag:
- JavaScript
- Electronics
category: blog
author: pete
description: Adventures with Johnny-five, Raspberry Pi and a compass. Photo by Mariana B. on Unsplash
---

*I found this writeup that I never published. Oops! Here it is at long last*

[Photo by Mariana B. on Unsplash](https://unsplash.com/photos/MCKz5KGAmdQ)

Back in the halcyon days of 2015, I was involved with a project to build a virtual periscope. The brief was that motion of a periscope, sensed via a digital compass, would control a panorama of a cold-war bunker. Said bunker is actually there under your feet, but it's not the sort of thing you want to be drilling a big hole in to install a real periscope in - you have to lower your equipment down on a bit of rope to get in. It's certainly not the sort of place you expect to deploy software.

We required a relatively simple way to read from our magnetometer - an [HMC5883L][1] if you're interested - and feed this heading data to our panorama running in the browser to position and interact with the virtual tour.

We chose a Raspberry Pi since it can run a large part of our application and interface with the magnetometer over its GPIO pins. This also opened up the choice of technologies we could use to prototype and trial the idea before committing to a solution.

The decision was neatly narrowed down by a quick glance around the office. We didn't all have the Ruby or Go experience to use something like [Artoo][2] or [Gobot][3], but we all have plenty of JavaScript experience.

I was aware of two robotics libraries for JavaScript: [CylonJS][4], and [johnny-five][5]. In the end familiarity won out as I'd used johnny-five more than cylonjs while toying about with my own Raspberry Pi.

As an aside, all of the linked libraries rely on the [firmata protocol][8] in order to communicate with the host device. In the case of an arduino a program is flashed to the arduino that bootstraps and runs firmata, accepting instructions over a serial connection. With the Raspberry Pi it uses [raspi-io][9], which uses a firmata-compatible API. It has the downside of requiring a serial connection to run your code.

Getting started with johnny-five is about as straightforward as it gets:

```javascript
'use strict';

var RasPi = require('raspi-io'),
    five  = require('johnny-five');

var board = new five.Board({io: new RasPi()});
```

with the `board` object, we can now make connections over the Raspberry Pi's GPIO and use asynchronous features of the language to wait for and respond to events from the hardware.

Firstly we need to tell johnny-five to expect a magnetometer to be connected- luckily there is a preset Magnetometer contructor (it's actually a compass object with some presets for the HMC5888L sensor, see [the example code][6] for more details.) The default read rate is 50ms, which can be adjusted up or down depending on the datasheet of the sensor.

```javascript
board.on('ready', function() {
  var compass = new five.Magnetometer();

  /* ... */
});
```

Now we have our `compass` object, which is an [`EventEmitter`][7], we can listen to a set of events by binding callbacks and respond to them in our application.

The event we want to listen out for is called `headingchange`, this is fired when the magnetometer reading has changed since the last reading and contains a heading object bound to `this`. There is also a `data` event which fires on each read of the heading regardless of all previous. Since we don't need the data if the reading has not changed, we can safely use `headingchange`.

```javascript
compass.on('headingchange', function() {
  // Send the new heading to the waiting browser
  // to tell it to pan the virtual tour appropriately.
});
```

The best part about the johnny-five code is that it stays out of the way. A colleague with very little johnny-five experience was able to read and work on the code in short order since it introduces no new syntax and keeps the simple event/callback logic that is familiar.

I didn't find many articles about this so I think it's safe to say that most johnny-five (and really, most firmata-based) code is being used in a hobbyist or setting. Since we had an unusual environment to run our application and didn't need to run our hardware in an environment where firmata would be impractical it was the perfect chance to use it in a production setting.

[1]: https://upload.wikimedia.org/wikipedia/commons/2/27/Digital_Compass_sensor.jpg "By Ctg4Rahat (Own work) [CC BY 3.0 (http://creativecommons.org/licenses/by/3.0)], via Wikimedia Commons"

[2]: http://artoo.io/ "Ruby on Robots"

[3]: http://gobot.io/ "Golang framework for robotics"

[4]: http://cylonjs.com/ "JavaScript framework for robotics"

[5]: http://johnny-five.io/ "The JavaScript robotics framework"

[6]: https://github.com/rwaldron/johnny-five/blob/80107fd66fd671425893e6803e77f5d30fb11074/docs/magnetometer.md "Magnetometer Example"

[7]: https://nodejs.org/api/events.html#events_class_events_eventemitter "nodejs EventEmitter documentation"

[8]: https://github.com/firmata/protocol/

[9]: https://github.com/nebrius/raspi-io/
