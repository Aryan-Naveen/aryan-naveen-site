---
title: "HURC: PacBot Competition"
date: 2022-04-28T00:19:54-04:00
draft: false
summaryImage: "harvard_bot.jpg"
summary: "Competed in competition hosted among Boston schools to design, build, and program a robot to beat Pacman in real life."

tags: ["Robotics", "Python"]
---


Participated in Pacbot competition, where essentially the objective was to design a robot to score as many possible points on pacman as possible. This competition invoked numerous challenges from designing the chassis to the low level control of the robot as well as integrating it will high level decision making logic to beat Pacman.

My role on the team focused on further improving the low-level code from previous years, where I was able to succesfully integrate an IMU that uses a teensy to communicate with the raspberry pi. By using the heading of the raspberry pi in order to PID the direction of the robot to ensure that the bot travel straight I also added functionality for IR sensors for collision detection/avoidance.

Github Repository: [https://github.com/HarvardURC/Pacbot](https://github.com/HarvardURC/Pacbot)

<video width=100% controls autoplay>
    <source src="/static/pacbot/IMG_6392.mp4" type="video/mp4">
    Your browser does not support the video tag.  
</video>
