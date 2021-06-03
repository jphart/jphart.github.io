---
title: Weather Station
permalink: /weatherstation/
layout: page
visible: true
description: Using a Raspberry Pi and Arduino to track the weather
---


<div class="post_series">
<p>Posts in this series</p>
<ul>
    <li><b>Introduction</b></li>
    <li><a href="sensor">Sensor hardware</a></li>
    <li>Powering the sensor</li>
    <li>Final sensor hardware</li>
    <li>Sensor software</li>
    <li>Reciever hardware</li>
    <li>Reciever software</li>
    <li>Final build & installation</li>
</ul>
</div>

Back in March 2020 with lockdown in the UK looming I thought it would be a good time to pick up some new hobbies. By day I’m a software developer working mainly on backend technologies on large servers, using VMs or containers. I’ve not touched electronics since my uni days, but was interested in getting back into some coding closer to the metal. 

It seemed a good time to pick up a [Arduino starter kit](https://www.amazon.co.uk/Project-Starter-Tutorial-Prototype-Expansion/dp/B01D8KOZF4) & play around in the microcontroller world. A simple 8 bit chip with 2Kb RAM (Similar to a [NES](https://en.wikipedia.org/wiki/Nintendo_Entertainment_System#Technical_specifications)) is certainly quite a change from the day job!

After playing around with some of the starter projects and probably inspired by a unusually hot summer I thought I'd try to build an arduino powered weather station as a first proper project. 

This seems to be quite a popular idea, a quick search will show a [number](https://www.instructables.com/SOLAR-POWERED-ARDUINO-WEATHER-STATION/ ) of [guides](https://dev.drun.net/2017/03/19/reading-temperature-with-arduino-and-sending-it-via-hc-12/ ) out & [youtube videos](https://www.youtube.com/watch?v=ChQpD2gsC20) out there. 

Putting together a first quick prototype with a LCD display displaying the current temperature and humidity from a DH-11 sensor was a fun afternoon...

![Breadboard with Arduino, DH-11 and LCD display showing the temperature & humidity](/assets/images/weatherstation/first_prototype.jpg)

...but didn't quite scratch the itch enough for me. While it was fun watching the temperature climb over 30 degrees, 

I came up with the following list of requirements:
* I want the sensor to be robust enough to record the outside temperature
* I'd like to record and track the temperature over time
* As a minimum it should record temperature, humidity and pressure
* It should be extensible to add additional sensors in the future (Air quality? rain gauge? anemometer?)
* Ideally I'd like to be able to set an alert when the temperature drops below zero overnight
* I'd like to be able to view the current weather on my phone & display it on our kitchen TV
* The sensor should be battery powered, & should last weeks on the battery
* As there's a risk of this not working, keep the costs as low as possible to prove the concept

With these requirements in mind, a plan came together:

![Diagram showing the sensor, arduino and RaspberryPi as described below](/assets/images/weatherstation/weatherstation_overview.png)

* Use an arduino nano with a BME 280 sensor to record pressure, humidity and temperature
* This would be powered by a battery & housed outside, either in the garage or garden shed
* As WiFi doesn't reach outside the house, use a 433MHz radio module to transmit from the sensor to the Pi
* Write some custom code to push the measurements into [InfluxDB](https://www.influxdata.com) a time seriese database made for logging values over time.
* Use [Grafana](https://grafana.com) as a display to the measurments, display graphs & create alerts. 

I'd been looking for an excuse to try the cheap RaspberryPi Zero out & have used Grafana for monitoring at work so knew it's capabilities. I've got some experience with Prometheus for recording metrics, but it's designed to pull metrics from systems, that wouldn't work in this case. It seemed like a good time to learn InfluxDB, which you can push metrics into.

With a rough plan in place, it was time to start prototyping. 

Having never made anything like this before I had to do a lot of research and experimentation before coming up with a final design, so I'll be splitting up the build into a few parts.

* [Sensor hardware prototype](sensor)
* Powering the sensor
* Final sensor hardware
* Sensor software
* Reciever hardware
* Reciever software
* Final build & installation

<a href="sensor">Next time: building a prototype sensor</a>, looking at radio transmitters and sensor modules.