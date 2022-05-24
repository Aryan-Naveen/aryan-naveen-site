---
title: "Decentralized Correlation Agnostic Data Fusion Research"
date: 2019-06-17T23:53:00+01:00
draft: false
hideLastModified: true
summary: "Research in further optimizing data fusion techniques in environments where correlation is unknown between two data sources."
tags: ["Mathematics", "Algorithm", "Python", "Research"]
summaryImage: "summary.jpg"
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



## Research Motivation

In various applications in order to improve the accuracy of a certain measurement, multiple sensors are used and the measurements are combined. However, this process of combining measurements is not always as simple as taking averages/medians of the various sensors and frequently correlation between the various sources must be accounted for in the fusion. This is because correlation describes the amount of common information shared between data sources and using correlation we can avoid double counting the common information as well as accurately estimate the uncertainty. Both of these can have extreme consequences in various applications and must be appropriately handled.

However, not all circumstances are privy to making the correlation between sensors easily accesible and as a result correlation agnostic fusion techniques are implemented to "estimate" the correlation between data sources. In this research, I derive a novel fusion approach that yields more intuitive results.

## General Intuition of Novel Data Fusion Approach

In this research I further adapted Ellipsoidal Intersection, which is a popular correlation agnostic technique that uses a geometric intuition to estimate the common correlation. An issue that arises however, is that the resulting independent distributions are sometimes pushed extremely far apart, which is extremely unlikely based on the Z-score or mahalanobis distance. As a result, I further constrain EI by ensuring that the resulting independent distributions, defined in terms of the common correlation have a high possibility. Note that we evaluate data fusion algorithms based on how tight the estimated uncertainty is given that it is larger than the true uncertainty.

### Code
You can find the code that simulated these various data fusion algorithms, including the novel algorithm, on my github.

<div align = "center">
<button class="button button1" onclick="document.location='https://github.com/Aryan-Naveen/AFRL_dataFusion'">Github Repo</button>
</div>

<br>
{{< tabs
    file1="/static/content/research/afrl/tab_contents/one.md" title1="Citation & Publication PDF"
    file2="/static/content/research/afrl/tab_contents/two.md" title2="Poster"
    file3="/static/content/research/afrl/tab_contents/three.md" title3="Conference Presentation Video"
>}}
