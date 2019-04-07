---
layout: post
title: Exploring capabilities of Android 8 new WiFi Aware feature 
meta: IEEE WiFi Aware
source: http://www.ebook.com/about
category: blog
tags:
- Android
- P2P
- WiFi Share
description: IEEE new WiFi Aware standard is now mature and has been integrated in new versions of Android. This feature is super awesome for device to device communication and holds many possibilities previously tackled by projects such as Alljoyn and . 
---

### Introduction ###
The wireless technologies are widely adopted since they require less infrastructure and know-how to use. They provide a sense of freedom, aesthetic, sleek and futuristic technology. In recent past I have witnessed how wireless technologies has become part of our every day life. It is part of Smartphone, laptops, fridge, doors, and many more gadgets. The most notable technology is WiFi which surpassed Bluetooth technologies. Nowadays young generation will allows makes fun and ask 'who sends a data via Bluetooth when you have WiFi enabled Smartphone?'. It is amazing how Wireless technologies have increased  in adoption all mobile and computing technologies. All appreciation and thanks to be awarded to IEEE group that seeks to formalize and harmonize these technologies globally. They ensure interoperability between wireless technologies developed from different companies. It would be awkward to have two WiFi chipset that could not communicate. Another question that is mostly asked is why can't I connect all my friends to my WiFi. Of course I know there are apps that have stacked this functionality on top of the existing WiFi standard but why doesn't it come out of the box in all WiFi enabled devices?

### Device to device communication ###
This is a good question and its my favorite since I champion the idea of intercommunication between devices in proximity. It is not my sole idea but its the next evolution in computing technologies. The increasing number of computer devices used by users, means we now have an extended Internet which is normally known as Internet at the peripherally. It is closely related to Internet of Things that seeks to connect billion of devices to the Internet. This devices have minimal functionality and that suggest the users may not require the device always connected to the Internet. They may only need to use them locally and have a single device as the Internet gateway. The interesting part is connecting user devices locally without connecting to the whole Internet. The most common device is our Smartphones and thanks to Android which is incrementally developed to accommodate new features. We would like to seamlessly connect our Android Smartphone in a wireless local network and communicate like Internet. Before we make a decision on which is the most appropriate technology, lets outline functionality we need. It connects devices in proximity, discovers devices nearby, connects to Internet, shows devices we are connected, secures our communication, does not drain all battery juice, connects many devices, bidirectional communication with all connected devices, authenticate users and disconnect at will. I prefer the WiFi standard over other wireless standard because of wide adoption and implementation of the functionality we need.

### IEEE WiFi Standards ###
When buying Android devices I guess everyone first checks the specifications to determine what is implemented in the device. The interested must have noticed the WLAN specs which are indicated as a/b/g/ax which represent Wifi a, Wifi Direct, WiFi Aware which you can learn more [here](http://ieee.org). This are the various standards of WiFi that have reached adoption stage for the general public. They are mostly implemented at hardware or OS level and that's why older device might not have the functionality. Android device are criticized for not adopting them as fast as they are released by IEEE, compared to Iphone Smartphone flagship. But since the Android OS has the largest users base compared to other Smartphone devices, it means new feature reaches many users on every new Android version. 

### Android Implementation of WiFi Aware standard ###
New technology needs to have a positive impact on its users and that's why will examine how Android implements the WiFi Aware IEEE standard. I am very excited of this new feature since I implemented similar functionality using AllJoyn platform on this [article]({{baseUrl}}). You can check the official Android [documentation](http://android.com) on the specifics of Android WiFi Aware API. We will instead implement a application that allows users to live stream from their camera to proximity device similar to Video calling. We will mostly ignore WebRTC([learn from here]({{baseUrl}})) streaming technology and concentrate on the code that uses the Android WiFi Aware API.

### Face Time with nearby devices ###
Its everybody's dream of a future which we will communicate face to face with everybody no matter where they are in the universe. We also believe in Kotlin philosophy of pragmatic, concise, safe-language and interoperability and that is why we use it in coding our FaceTime app.

### Other Uses ###


### Criticism and raising the bar ###