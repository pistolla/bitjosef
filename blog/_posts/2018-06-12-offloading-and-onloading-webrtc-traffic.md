---
layout: post
title: Offloading and onloading WebRTC traffic  
meta: WBF
source: http://www.ebook.com/about
category: blog
tags:
- WebRTC
- P2P
- Alljoyn
- MQTT
- Android
description: Seamless offloading WebRTC traffic to local network when user are in proximity and onloading back to internet when user moves out of proximity
---
Points

1. introduction (What is the problem, facts, statistics)
2. what is the nature of information and delivery(Real time, Delay)
3. what are the challenges and cost (assumptions)
4. What is the solution framework
5. what is performance tracking
6. what is the conclusion
### Real-Time streaming in large gatherings ###
This post could easily qualify to be a academic paper but it doesn't introduce any new concepts. It borrows from several papers that tries to solve a common issue with available solutions. Our major issue in the age of information is user experience while consuming  or indulging digital content. Social interaction tremendously improved with onset of Web 2.0. It became easy and new norm to interact with others via social platforms. The major percentage of data exchanged is multimedia data which is set to reach 75% of ISP's network capacity by 2020. Multimedia include pictures, audio, video, raw data files, map layers, and graphic vector data. Multimedia data takes up more capacity and require more bandwidth while is delivered in real-time. The user experience can be hampered due to bottleneck in network bandwidth. The throughput degrades where multiple users are sharing access point whether its Wifi AP or CDMA network access node.
Recently i saw a stadium lit with phone camera backlight and it was so beautiful even considering all these device had a video camera. The people near the front could caught clear images of the match but back stages was spectating from afar. This scenario made me think on how user could improve experience by sharing camera or the stadium cast a stream channel via access point. This would make users have a super vision and watch from all corners. This is difficult more than how it sounds when we deconstruct stepwise. First lets try to solve it, using today's on-demand real-time streaming.  


### WebRTC for streaming ###
WebRTC is fundamentally peer to peer framework that reduces if not eliminating the pain of capturing visual/audio/raw data and streaming from one user to another. It is just awesome even more considering you can now stream from a web browser and native mobile apps.  

### Offloading streams ###
Semantrix

### Onloading streams ###