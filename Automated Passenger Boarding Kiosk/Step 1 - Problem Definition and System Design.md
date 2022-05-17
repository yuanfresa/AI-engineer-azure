# Problem Definition
Travelling  is an important part of our daily life however it is time-consuming and requires time management to plan the boarding.  What if there are automated boarding kiosk equipped with technology to make this whole experience faster?

## Goal (What)
We will build an automated passenger boarding kiosk to assist with pre-flight boarding procedures. The automated system will showcase the power of computer vision in executing a wide variety of business processes within the context of airline boarding operations, specifically identity verification to board the flight and automated customer feedback collection. 

## Business Considerations (Why)
* Automation
* Use of technology to improve the pre-flight boarding experience
* On-the-go lifestyle

## Technical Considerations (How)
Following are a few points to follow as technical considerations to start. 

Azure Computer Vision, Face, and Form Recognizer services, along with few other cloud services such as Blob Storage.

# Solution Strategy Walkthrough
The system will extract information from identification cards and boarding passes. Face information are extracted from digital ID and a 30-second video to match a given passenger's face from. The same video will also be used to extract the passenger's emotions. Also it can detect if a passenger's carry-on items include a lighter or not.
[![1.jpg](https://i.postimg.cc/8cq14qSV/1.jpg)](https://postimg.cc/H89DsvnS)

[![2.jpg](https://i.postimg.cc/KzWFcd4g/2.jpg)](https://postimg.cc/MM1gd37W)

[![3.jpg](https://i.postimg.cc/tJVbs2Vv/3.jpg)](https://postimg.cc/n9Zg5K2q)