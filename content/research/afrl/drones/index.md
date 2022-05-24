---
title: "Drone Localization in Challenging Environments Research"
date: 2018-10-09T14:30:07-04:00
draft: true
summary: "Applied novel data fusion algorithm to enable autonomous drone flights in unstructured environments."

tags: ["Robotics", "Electronics", "Solidworks", "Python"]
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


## Motivation
In an autonomous navigation system, both the pose and uncertainty associated with that estimate must be estimated. When estimating the uncertainty of a navigation solution, some techniques assume that measurements from different sensors are independent (uncorrelated) with each other, and the measurements from the same sensor are uncorrelated over time. When this assumption is violated, however, the estimated uncertainty can be significantly over-optimistic.

To overcome the problem of unknown correlation among measurements, one possibility is to use fusion techniques that are correlation agnostic. While these techniques are guaranteed to not be over-optimistic, they often either degrade the estimate quality or yield covariance matrices that are significantly more conservative. In this research, I applied a new correlation-agnostic fusion technique, Probabilisticly Conservative (PC) fusion. As an example, we use a velocity estimator that uses measurements based on the Doppler Effect of radio frequency signals, such as GNSS and SOOP. Because SOOP emitters from the same system may have their clocks driven by the same mechanism, sometimes synchronized with GNSS time, the degree of correlation among GNSS and SOOP measurements is both (a) unknown and (b) may be significant. I found that utilizing PC fusion leads to fusion results with a minimal decrease in accuracy, and less conservative (but not over-optimistic) covariance estimates than other techniques.

### Evaluation Technique

More information can be found in the pdf below, however data was collected from car rides that simulated drone flights in various environments and different fusion techniques were applied to evaluate accuracy in both the pose estimate & the accuracy in uncertainty estimate. The code and data can be found on my github.

<div align = "center">
<button class="button button2" onclick="document.location='https://github.com/Aryan-Naveen/dronePoseEstimation'">Github Repo</button>
</div>

<br>

## Report
The attached report was submitted to Regeneron STS where I was recognized as a finalist and awarded 4000 dollars.
<div class = "container" align = "center">
<embed src="paper.pdf" type="application/pdf" style="width:80%; height:620px" />
</div>

## Research presentation

<div align = "center">
<iframe style="width:80%; height:500px;" src="https://www.youtube.com/embed/HVeyBeDWtNA" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>
