---
title: "Environmental Mapping & Stability Prediction for Autonomous Wheelchair"
date: 2019-06-17T23:53:00+01:00
draft: false
hideLastModified: true
summary: "Developed novel pipeline that mapped surrounding terrain of autonomous wheelchair and predicted stability in order to avoid rollover."
tags: ["C++", "Python", "ROS", "Solidworks", "Research"]
summaryImage: "summary.png"
---

<style>
.button {
  border: none;
  color: white;
  padding: 4px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 4px 2px;
  transition-duration: 0.4s;
  cursor: pointer;
}

.button1 {
  background-color: white;
  color: black;
  border: 2px solid #4CAF50;
}

.button1:hover {
  background-color: #4CAF50;
  color: white;
}

.button2 {
  background-color: white;
  color: black;
  border: 2px solid #008CBA;
}

.button2:hover {
  background-color: #008CBA;
  color: white;
}

</style>


## Motivation

This research attempted to enable a greater degree of independence for an already handicapped population by addressing a fundamental barrier. Autonomous wheelchairs can serve as a bridge for individuals to be able to reclaim some of that independence, however, ensuring safe travel is important. Not only is effective path planning & following necessary but the wheelchair must ensure that it avoids terrains that can place the wheelchair in a compromising position such as rollover. Thus effectively mapping and evaluating various terramechanical properties to ensure the safety of the wheelchair is imperative and that is what this research attempts to resolve.

## General Intuition of Algorithm

I utilized 2 different Intelrealsense sensors through the different stages of this research: [IntelRealsense D435i](https://www.intelrealsense.com/depth-camera-d435i/) & [IntelRealsense L515](https://www.intelrealsense.com/lidar-camera-l515/). Both sensors offered visual data as well as depth which was used to perform SLAM for localization purposes & constructing a point cloud which was then converted into triangular mesh. From the mesh, we can compute the gradient of the terrain by taking the cross product of the vertices in the mesh and compare those slopes with the rollover angle computed by my dynamic model of the wheelchair.

Below you can see an example of the RTAB mapping with the L515 sensor where we are able to construct a 3D pointcloud of our lab as well as compute the odometry of the wheelchair over time.
<div align = "center">
<video width=80% controls muted = "false" autoplay>
    <source src="/static/cu-icar/mapping.mp4" type="video/mp4">
    Your browser does not support the video tag.  
</video>
</div>

<br>
{{< tabs
    file1="/static/content/research/cu-icar/tab_contents/one.md" title1="Citation & Publication PDF"
    file2="/static/content/research/cu-icar/tab_contents/two.md" title2="Poster"
    file3="/static/content/research/cu-icar/tab_contents/three.md" title3="Conference Presentation Video"
>}}
