---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Trashware - IOT redefined"
subtitle: "Trashware is a project which aims at environment cleanliness through smart devices."
summary: "Trashware is a project which aims at environment cleanliness through smart devices."
authors: ["Prakhar Gurunani"]
tags: ["IOT", "Automation", "Clarifai", "API", "Python", "OpenCV", "Image Prediction", "AI", "Artificial Intelligence"]
categories: ["Tech"]
date: 2019-02-25
lastmod: 2019-02-25
featured: true
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: ["trashware"]
---

## Story

![](https://cdn-images-1.medium.com/max/2000/0*d4ADmvS5RHYbqSZ_)

![](https://cdn-images-1.medium.com/max/2000/0*fpQjhqIyuot9zLAk)

The device hacked in this project uses image recognition to identify recyclable and non-recyclable waste and seperate them in 2 different bins.
> *Hardware*

The following parts were used to build this project -

* Arduino UNO

* Ultrasonic Sensor — HCSR04 (Generic)

* HC-05 Bluetooth Module

* SG90 Micro-servo Motor
> *Software*

The following software packages were used to power this project -

* Arduino IDE

* OpenCV

* Python

* Clarifai REST API
> *How To Build*

* Connect the ultrasonic sensor and the bluetooth module according to the configurations in the ultrasonic_with_bluetooth/ultrasonic_with_bluetooth.ino file.

* Upload the .ino file to the Arduino and connect it to your computer.

* Now configure the software part. Inside the project directory, run the following commands -

* virtualenv Trashware

* For Windows — cd Trashware/Scripts && activate

* For Linux and Mac — source Trashware/bin/activate

* Now run the Main_Program.py in terminal

* If everything went well, then the program will display the ultrasonic sensor readings on the screen. You can also view the sensor readings on your Android or IOS device by downloading an arduino emulator.

* If an object is kept in the range of 30 cm in front of the ultrasonic sensor, then the Webcam_Capture.py captures the image through a webcam connected to computer, and the Image_Prediction.py predicts the captured image and classifies it as recyclable or non-recyclable.

* If the object is recyclable, then the servo motor turns 45 degree right, dumping it in the recyclable bin. And if the object is non-recyclable, then the servo motor turns 45 degree left dumping it in the non-recyclable bin.
> *Note — The code for the servo motor will be added in the future commits. Watch the repository for changes.*

*Share and fork the project if you would like to.*
*Feel free to pull requests and open issues.*

## Source Code

## [prakhargurunani](https://github.com/FirePing32) / [trashware](https://github.com/FirePing32/trashware)

Latest commit to the **master** branch on 4–11–2019

[**Download as zip**](https://github.com/FirePing32/trashware/archive/master.zip)