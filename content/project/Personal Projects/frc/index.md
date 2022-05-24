---
title: "FRC: Power Up Robot"
date: 2020-03-12T13:56:48-04:00
draft: true
hideLastModified: true
summary: "Program lead for team that built robot to compete in the 2020 FRC competition: Power Up."
summaryImage: "summary.png"
tags: ["Robotics", "Java", "Python", "Electronics", "OpenCV"]
---

I was the team captain for this season and help spear-head the development of an award winning vision system and autonomous control. In the videos below you will see the robot in action as it detects the distance from the target and automatically adjusts the shooter hood angle and RPM of the fly-wheel. Essentially the programming's responsibilities were:

1. Programming individual subsystems to perform desired functionality as well as ensure that the interfacing between multiple subsystems worked. E.g: Transition from intake to elevator to shooter required a lot of tuning in terms of timing and sequencing of commands.

<div align = "center">
<video width=50% controls autoplay muted = "false">
    <source src="/static/frc/intake.mp4" type="video/mp4">
    Your browser does not support the video tag.  
</video>
</div>

2. Determining desired paths for autonomous and implementing trajectory following algorithms (MotionMagic) to perform closed loop PID control of the robot's velocity to follow the desired path.
<div align = "center">
<video width=80% controls autoplay muted = "false">
    <source src="/static/frc/path.mp4" type="video/mp4">
    Your browser does not support the video tag.  
</video>
</div>


3. Determine the relationship between distance from target and shooter hood angle/fly wheel RPM and use vision system's reading to automatically adjust hood.

<div align = "center">
<video width=80% controls autoplay muted = "false">
    <source src="/static/frc/model.mp4" type="video/mp4">
    Your browser does not support the video tag.  
</video>
</div>

4. Allow for intuitive control of robot and manual overrides when necessary through operating panel.

Github Repository: [https://github.com/entech281/Robot2020](https://github.com/entech281/Robot2020)


<div align = "center">
<video width=50% controls autoplay muted = "false">
    <source src="/static/frc/everything.mp4" type="video/mp4">
    Your browser does not support the video tag.  
</video>
</div>