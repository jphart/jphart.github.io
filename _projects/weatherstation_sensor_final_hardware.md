---
title: Weather Station
permalink: /weatherstation/final_hardware
layout: page
visible: false
description: Using a Raspberry Pi and Arduino to monitor the weather
---

<div class="post_series">
<p>Posts in this series</p>
<ul>
    <li><a href="/weatherstation">Introduction</a></li>
    <li><a href="/weatherstation/sensor">Sensor hardware prototype</a></li>
    <li><a href="/weatherstation/power">Powering the sensor</a></li>
    <li><b>Final sensor hardware</b></li>
    <li>Sensor software</li>
    <li>Receiver hardware</li>
    <li>Receiver software</li>
    <li>Final build & installation</li>
</ul>
</div>


# Final design 

After the [power experiments](/weatherstation/power) we have a final circuit design for our solar powered sensor & a breadboard prototype.  

![Final circuit design for the weather sensor](/assets/images/weatherstation/weather_sensor.png)

Now its time to create something a bit more rugged that can survive outside. 

## Circuit board
As this is a one off, I went with a simple [perfboard](https://en.wikipedia.org/wiki/Perfboard) solution for the circuit board. I decided to socket all the components with pin header connectors. This would make it easier to replace any components in the future & as heat from a soldering iron can damage components, it would make it a bit safer.

To layout the board, I measured the size of each component in terms of pin holes in the perfboard, then used (of all things) Excel to layout the components on the board.

![Excel perfboard layout](/assets/images/weatherstation/perf_layout.png)

The BME-280 sensor would be on a seperate board, to allow it to be placed outside in its own housing. I repurposed an old Ethernet cable to connect the two boards together. This gave plenty of wires for connecting the two together, but wasn't the best choice. Each individual wire was made up of multiple strands rather than a single core, making it difficult to achieve a good connection. It works well enough, but I wouldn't recommend reusing an ethernet cable this way. 

![Photo of the created perfboard](/assets/images/weatherstation/perf_image.jpg)

## Casing

The receiver will be housed inside a garden shed, to give it an element of weatherproofing I repurposed a tupperware box to mount the components in. Its not at all waterproof, but should provide a bit of protection.

![Casing for receiver](/assets/images/weatherstation/casing.jpeg)

The sensor itself will be housed outside, to house the sensor I built a small wood box & painted it to match the exterior of the shed. I'm by no means an expert at woodworking, but its held together so far. 

![Casing for sensor](/assets/images/weatherstation/casing2.jpeg)

Finally I created a platform to mount the solar panel to & angle it towards the sun. 

![Mounted solar panel](/assets/images/weatherstation/solar_panel.jpeg)

With that done, the sensor is complete, on to the Receiver. 