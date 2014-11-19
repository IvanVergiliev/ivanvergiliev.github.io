---
layout: post
title: "Simple Multi-touch Event Handling"
modified:
categories: 
excerpt: Detecting pinch-to-zoom style events in JavaScript
tags: []
image:
  feature:
date: 2014-11-16T18:36:32+02:00
comments: true
---

Suppose you've just released your shiny new mobile web app - let's say it's a photo album. Now you've decided to add multi-touch support to the picture details page - pinch-to-zoom, two-finger pan, etc. There is a somewhat standardised API for working with touch events [^1] [^2] and enough written about them [^3]  so the technical details won't be discussed here. The more interesting part is recognising the gesture each of these events represent. At first, it seems like a classification problem [^4], and, indeed, most of the Google results for “multi-touch gesture recognition” are Machine Learning papers.

Whenever you are about to start implementing research works for something seemingly simple like this, it is a good time to stop and reconsider if there is a cleaner solution. In this case, it turns out there is.

The key to arriving at the simple solution is to stop thinking about “classifying” the gestures and start thinking about characterizing them. So instead of receiving a series of events and checking if they represent a pinch-to-zoom gesture, try to conclude “these events show a 20% zoom level”. At the same time, they can also show “a move to the right by 20px” - imagine moving your fingers to the right and apart from each other at the same time.

There is a live demo of this idea [here](http://ivanvergiliev.github.io/simple-multitouch/)  (open it on a multi-touch-enabled device), and the source code is also [available on GitHub](https://github.com/IvanVergiliev/simple-multitouch). Here are some details about the implementation of each gesture:

* For pinch-to-zoom, say the initial distance between the two touches is **x**. Then, if the distance becomes **2x**, we simply increase the zoom level twice ([main.js: lines 106-108](https://github.com/IvanVergiliev/simple-multitouch/blob/45fb4c88888e9e1436b9250f3749a91d8bcad6a9/main.js#L106-L108)):
{% highlight js %}
var newDistance = Vector.distance(p1, p2);
var scaleFactor = newDistance / distance;
{% endhighlight %}
* For two finger pan, we can calculate the center of mass and track how it's moving with the gesture ([main.js: lines 102-104](https://github.com/IvanVergiliev/simple-multitouch/blob/45fb4c88888e9e1436b9250f3749a91d8bcad6a9/main.js#L102-L104)):
{% highlight js %}
var newCenter = Vector.findCenter(p1, p2);
var deltaVector = newCenter.subtract(center);
{% endhighlight %}
* Two finger rotation is only slightly more complicated. The idea is to analyze the vector beginning at one finger and ending at the other finger. We can track the movement - and most importantly, the rotation of this vector and thus determine the desired rotation of the object ([main.js: lines 110-123](https://github.com/IvanVergiliev/simple-multitouch/blob/45fb4c88888e9e1436b9250f3749a91d8bcad6a9/main.js#L110-L123)):
{% highlight js %}
var newDirection = p1.subtract(p2);
var crossProduct = Vector.crossProduct(direction, newDirection);
// We use the fact that cp(a, b) = ||a|| * ||b|| * sin(theta), where theta is the angle between
// vectors a and b, to find theta.
// This will not always give the correct result for any two vectors a and b, but
// should be enough for handling touch events.
var normalizedCrossProduct = crossProduct / (direction.norm() * newDirection.norm());
var rotationAngle = Math.asin(normalizedCrossProduct);
{% endhighlight %}

Apart from its simplicity, an advantage of this approach compared to a classification algorithm is that the latter will likely require the whole gesture to happen in order to start the recognition. The ideas described here are more reactive and responsive to user actions.

There are a few bugs that are left as an exercise for the reader. (Most glaringly - once you rotate the image, panning left and right don't move the photo left and right on the screen anymore, but instead move it in the direction in which it's oriented after the rotation.) Also, the movement doesn't accurately follow your fingers the way, say, Google Maps does - this requires a bit more complicated approach. But it's really simple, and it creates an overall feeling of responsiveness.

[^1]: <https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Touch_events>

[^2]: <https://developer.mozilla.org/en-US/docs/Web/API/TouchEvent>

[^3]: <http://www.html5rocks.com/en/mobile/touch/>

[^4]: <http://en.wikipedia.org/wiki/Statistical_classification>


