<pre>
   ____   _       _                                          
  / __ \ (_) ___ | |__  _ __  _ __  _   ___      _____ _   _ 
 / / _` || |/ _ \| '_ \| '_ \| '_ \| | | \ \ /\ / / _ \ | | |
| | (_| || | (_) | | | | | | | | | | |_| |\ V  V /  __/ |_| |
 \ \__,_|/ |\___/|_| |_|_| |_|_| |_|\__, | \_/\_/ \___|\__, |
  \____/__/                         |___/              |___/ 

</pre>
# Optimizing Existing Web Apps for Mobile

## Intro
---
Hi, I'm Johnny Wey.

* [Twitter](https://twitter.com/johnnywey)
* [Github](https://github.com/johnnywey)

I'm going to talk about making legacy websites work better on mobile devices.

A "Mobile" device, at least for this talk, is a modern smart phone and / or tablet. The biggest differences between these devices today compared to a traditional PC are screen size, processing power and touch input.

Processing power is likely not as big an issue as it was when the iPhone was first released in 2007. The current crop of mobile devices have incredible capabilities and are quickly approaching desktop class performance from both a CPU and GPU perspective. While avoiding things like reflows are more of a concern for mobile devices, I consider that a temporary situation and, as such, I won't go into it too much.

## Outline
---
* Mobile viewport
	* CSS media queries
* Favicon
* Retina images
* Overflow: touch
* Click events
	* Fast button: https://developers.google.com/mobile/articles/fast_buttons
	* EnergizeJS: https://github.com/davidcalhoun/energize.js
	* FastClick: https://github.com/ftlabs/fastclick
* CSS native controls (tabs, accordions)
* Debugging on mobile devices

## Karl's Widgets
---
Let's take a look at a simple company website. Karl makes widgets. Karl's website doesn't do much aside from espousing the virtues of his company's widgets. That's really all Karl needs it to do.

Karl's sales team just gave every one of their reps an iPad and allowed them to use their personal smart phones for corporate use. The reps want to show off their site to customers.

What sorts of things could the company do to make their site look better on mobile?

## Does it need to be mobile?
---
The first question we should always ask is "is it good enough as is"?

During the original iPhone announcement, I remember Steve Jobs showing off the New York Times website and demonstrating pinch to zoom and other features that simply blew me away. He asserted that mobile versions of websites didn't need to be created anymore because Mobile Safari made the experience with full sites pretty good.

And, he wasn't totally wrong. Some sites still look fantastic in Mobile Safari and Android Chrome. They may not be completely ideal, but we should always ask if the user experience is good enough as is with no work at all.

## What devices matter?
---
Not every device is created the same. We should always explicitly decide what we want to optimize for and what we don't. Maybe you're comfortable with tablets getting a desktop experience and phones getting a more optimized one ala Facebook? 

In short, there exists no magic bullet in making a website look great across all devices and, even if your target is wide, you should always understand where your optimizations break down.

## Viewport
---
Ok, with that out of the way, let's pretend we want to target modern phones and tablets (Android and iOS). The first thing we probably want to do is alter the viewport.

On traditional PCs, the "viewport" is the part of the page you can see. Since you can easily use scroll bars and resize the browser window, not too much care is taken to worry about the __scale__ of the content.

This is different on mobile devices. They typically default to a much smaller scale and then allow zooming in with gestures or taps. Once zoomed in, we can typically move the viewport around to read items on the page.

Setting the viewport at a static setting overrides the default zooming behavior. The reason this is a good starting point for mobile is it automatically makes the page display at 100% scale. This is typically allows much easier reading of page content.

[Demo Viewports]

## Media Queries and Relative Widths
---
So, so we created a viewport. Sweet. But we lost something too: the ability to zoom content. This can be a big problem as fixed-width elements aren't scaled and are displayed at their native width come hell or high water.

One optimization we can make is to use media queries and relative widths to manually scale down elements.

[Demo Media Queries]

## Favicon
---
Many phones allow the saving of web pages to the application grid (e.g.: iOS's Spring Board) in order to easily launch later. Adding a high-resolution favicon enables us to create a nice looking icon rather than using the (probably) much smaller resolution favicon.

[Demo Favicon]

## Retina Images
---
With the release of the iPhone 4, Apple created coined the term "Retina Display" to describe screens with a much denser pixel display than previous devices. Since then, just about every mobile device, from tablets to phones, has a display with pixel density much larger than a traditional PC.

This results in images not looking great when viewed on these devices. We'll go over three ways to help mitigate this:

1. The easiest is to download a pixel-doubled version of the image and then set the image size explicitly. The benefit here is that it's simple to implement. [Demo Doubled Image] 

2. Another way to do this is to use a media query to detect the device screen using a media query. This method prevents everyone from downloading a pixel-doubled version of an image, which is probably twice as many bytes, unless they really need to.

3. The latest versions of Safari and Chrome support image sets which allow the browser to select the correct image based on its pixel density. This is likely the long-term solution but is not quite universally supported yet. [Demo Image Sets]

It's worth mentioning that "retina" displays are not just limited to mobile devices anymore. Many PC vendors are now making laptops with much higher density displays and it's only a matter of time before this "mobile optimization" becomes yet another thing every web developer needs to think about.

Also, there are more complexities to using retina images than putting them on the page. Often, scaling down images, either through Photoshop or using the browser's renderer, results in poorer image quality. If this is a consideration, there's really no getting around doing the hard work of creating images crafted specifically for a given resolution.

## Overflow: Touch
---
We've been using ```overflow: scroll``` for a really long time to constrain pieces of a webpage into a scrollable container. However, the default behavior for this on iOS is not the fun bouncy scrolling that everyone is used to. We can change that:

[Demo Overflow]

This is generally an easy fix but can become more complex if the containing element is nested in a complex manner. It can also negatively impact touch events so we need to make sure we thoroughly test this optimization!

## Click Events
---
If you're anything like me, you've gone crazy attaching click events to all sorts of elements. It's so easy!

Mobile browsers even transpile these to touch events for us!

Except, they need to make sure the user isn't scrolling the document. So, they introduce an noticeable delay (~300ms) where they listen for other touch events to make sure a 'click' is what the user really wants.

This can make a page appear sluggish. We can fix that by overriding the default behavior (see [Fast Button](https://developers.google.com/mobile/articles/fast_buttons)) or using a library like [EnergizeJS](https://github.com/davidcalhoun/energize.js). 

The concept of both is actually pretty simple:

1. Listen for a ```touchstart``` event and a ```touchstop``` event
2. If the two are just about right next to each other, immediately fire the click handler rather than waiting the extra time the browser would
3. Make sure you capture and swallow the click event (```preventDefault``` and ```stopPropagation```) otherwise the browser will continue to fire it

## CSS Native Controls
---
The final optimization we can make is to convert some of our simple controls from JavaScript to CSS. Using CSS3 transitions, we can create controls that are typically accelerated by the mobile device's GPU vs. altering DOM elements that aren't.

[Demo Native Controls]

## Debugging
