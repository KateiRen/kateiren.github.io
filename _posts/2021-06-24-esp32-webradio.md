---
layout: post
title:  "An ESP32 Webradio based on the VS10xx chipset"
author: karsten
categories: [ esp32, micropython ]
image: assets/images/2021-06/20210622-IMG_20210622_124444.jpg
beforetoc: "Can it really be that easy?"
---

# A webradio built on esp32 and VS1003B.

## Can it really be that easy?

Yes it can! But let's start with first things first.

## Background

While searching my Ali Express components box, I stumbled over a module that I previously had not used in any way. A VS1003/VS1053 MP3 Decoder board. Although it contains "VS1053" it is in fact a VS1003 board.

![A photograpgh of the VS1003 MP3 decoder board](/assets/images/2021-06/20210622-IMG_20210622_124010.jpg "A photograpgh of the VS1003 MP3 decoder board")

Looking for a python implementation, I found a VS1053 library by [Uri Shaked](https://github.com/urish) for CircuitPython [here](https://github.com/urish/vs1053-circuitpython).

That was rather easy to port to MicroPython ([here](https://github.com/KateiRen/vs10xx-micropython)), so we are good to go.

For whatever reason, I was not able to get the SD-Card expansion board running with my ESP32. So where to get mp3 data from to test the VS1003?

Well, radio streams come in different formats, one of them being mp3. How much work would be required to connect to a mp3 stream and contonupusly push data over to the VS1003?

## Putting it to work

It turns out, that it is rather easy to create a socket and connect to any webradio stream ressource.

The point is, more often then not, the stream is not directly being served from the initial addresses. Instead the initial connect responds with a 302 FOUND including a new url (including authorization token) to serve the request.

That means we need to extract the new ressource address and create a new connection and request.

If that is working, we can continuously fetch data and ship to the VS1003.

The code can be found [here](https://github.com/KateiRen/MicroPython-Webradio).

![A photograpgh of the breadboard with an ESP32 devkit connected to the VS1003 board and headphones](/assets/images/2021-06/20210622-IMG_20210622_124122.jpg "A photograpgh of the breadboard with an ESP32 devkit connected to the VS1003 board")

![A photograpgh of the breadboard with an ESP32 devkit connected to the VS1003 board](/assets/images/2021-06/20210622-IMG_20210622_124506.jpg "A photograpgh of the breadboard with an ESP32 devkit connected to the VS1003 board")

## Additional Ideas

### Optimizing operation

What is the perfect buffer size between receiving new stream data and sending to the VS10xx?

Initially, I intended to create a stress test systematically trying different buffer sizes with increasing interruptions (delays) in between. However, the radio is now playing for more than 12 hours continuously already - that means the buffer size is - if not ideal - at least good enough already.

### Rotary Encoder

With the radio stations stored in an array, it almost calls for a rotary encoder to be able to control volume and switch between the stored stations.

### MQTT Control

As always with these tiny WLAN enabled and connected devices, it would be quite easy to add a MQTT client subscribing to a remote control topic and make the whole thing controllable remotely.
