---
title: "Mathematical Derivation & Applications of Principal Component Analysis"
date: 2021-12-12T23:39:49+05:30
tags: ["Mathematics"]
draft: true
hideLastModified: true
summary: "Many current scientific and technological advancements depend on incredibly large datasets. 
These datasets often contain an incredibly large number of data points, each of which in turn contains multiple values.
Take for example the ImageNet dataset, which contains 14 million images used to train computer vision models. Each of these images can be viewed as an array of roughly 200,000 pixel values. This means the entire dataset takes up an incredibly large amount of space in computer memory and is often slow and unwieldy to analyse. What if however, we did not need to record all 200,000 pixel values for every image, and we could instead store a smaller number of values for each image whilst retaining as much information as possible. PCA offers us a way of doing this!"
summaryImage: "summary.png"
---

<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.13.16/dist/katex.min.css" integrity="sha384-6LkG2wmY8FK9E0vU9OOr8UvLwsaqUg9SETfpq4uTCN1agNe8HRdE9ABlk+fVx6gZ" crossorigin="anonymous">
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.13.16/dist/katex.min.js" integrity="sha384-31El76TwmbHj4rF9DyLsygbq6xoIobG0W+jqXim+a3dU9W53tdH3A/ngRPxOzzaB" crossorigin="anonymous"></script>
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.13.16/dist/contrib/auto-render.min.js" integrity="sha384-vZTG03m+2yp6N6BNi5iM4rW4oIwk5DfcNdFfxkk9ZWpDriOkXX8voJBFrAO7MpVl" crossorigin="anonymous"
    onload="renderMathInElement(document.body);"></script>

<h1 id="introduction">Introduction</h1>
<p>Many current scientific and technological advancements depend on incredibly large datasets. These datasets often contain an incredibly large number of data points, each of which in turn contains multiple values. Take for example the ImageNet dataset, which contains 14 million images used to train computer vision models. Each of these images can be viewed as an array of roughly 200,000 pixel values. This means the entire dataset takes up an incredibly large amount of space in computer memory and is often slow and unwieldy to analyse. What if however, we did not need to record all 200,000 pixel values for every image, and we could instead store a smaller number of values for each image whilst retaining as much information as possible. PCA offers us a way of doing this!</p>
<p>More concretely, Principal Component Analysis (PCA) is an algorithm that takes in a dataset and puts the information in terms of a basis defined by <em>principal components</em>. This enables dimensionality reduction with minimal information loss, which we cover more rigorously in section [sec:dim reduction].</p>
<p>In the ImageNet example, reducing dimensionality means that instead of each data point (image) being a collection of 200,000 values (one for each pixel), it is a collection of a fewer number of values. A natural question now arises: why would we ever want to do this? There are three simple reasons:</p>
<ol>
<li><p>PCA can be used to compress datasets.</p></li>
<li><p>The new lower-dimension dataset that is produced is less computationally intensive to run analyses on. Once again, consider the ImageNet dataset. Training machine learning models on this dataset is very slow because there is an extremely large number of variables. However, if we halve the dimensionality of the dataset, we can roughly halve the time it takes to train models on it.</p></li>
<li><p>Data visualization. PCA can be used to lower the dimensionality of data to 2D or 3D, thus allowing the data to be plotted and visualized.</p></li>
</ol>
<p>These cases make PCA an incredibly useful algorithm that can be applied to a variety of fields in science and industry. Additionally, as we shall soon see, the linear algebra theory that underpins how PCA works is incredibly interesting. In the following work, we present:</p>
<ol>
<li><p>A step-by-step walkthrough of PCA</p></li>
<li><p>A mathematical derivation of the PCA algorithm with example data</p></li>
<li><p>An example application</p></li>
<li><p>Potential limitations of the PCA algorithm</p></li>
</ol>
<p>Before moving on, however, we will introduce the notation and some basic results we will use throughout the rest of this paper.</p>
<h1 id="linear-algebra-notation">Linear Algebra Notation</h1>
<p>Given an <span class="math inline">\(m \times n\)</span> matrix <span class="math inline">\(A\)</span>, we denote the <span class="math inline">\(i\)</span>th row of <span class="math inline">\(A\)</span> as <span class="math inline">\(A_{(i)}\)</span> and the <span class="math inline">\(i\)</span>th column of <span class="math inline">\(A\)</span> as <span class="math inline">\(A_{[i]}\)</span>. Furthermore, the element in the <span class="math inline">\(i\)</span>th row and <span class="math inline">\(j\)</span>th column of <span class="math inline">\(A\)</span> is denoted by <span class="math inline">\(A_{ij}\)</span>.</p>
<h2 id="data-matrix">Data matrix</h2>
<p>Consider an <span class="math inline">\(m \times n\)</span> data matrix <span class="math inline">\(X\)</span>. Each row in the data matrix is a feature vector <span class="math inline">\(f_i\)</span> (Definition [feature]). Furthermore, each column in <span class="math inline">\(X\)</span> is a data point <span class="math inline">\(d_i\)</span> (Definition [data]). Thus we can mathematically define <span class="math inline">\(X\)</span> as follows: <span class="math display">\[\begin{aligned}
    X = \begin{bmatrix}
       f_1 \\
       f_2 \\
       \vdots \\
       f_m
    \end{bmatrix} 
    = 
    \begin{bmatrix}
       d_1 &amp; d_2 &amp; \cdots &amp; d_n
    \end{bmatrix}\end{aligned}\]</span></p>
<p style="border:3px; border-style:solid; border-color:#FF0000; padding: 1em;"><b>Definition 2.1 (Feature vector): </b> A row vector <span class="math inline">\(f \in R^{n}\)</span>, where each element is an individual measurable property (scalar) of the same specific phenomenon.</p>
<p style="border:3px; border-style:solid; border-color:#FF0000; padding: 1em;"> <b>Definition 2.2 (Data Point): </b> A column vector <span class="math inline">\(d \in R^{m}\)</span>, a collection of <span class="math inline">\(m\)</span> different observable features at a given time.</p>
<p>For example, we could collect the weight and height of two people. Person 1 could be 6 feet tall and weigh 150 pounds, and Person 2 could be 5 feet tall and weight 100 pounds. Let <span class="math inline">\(X_e\)</span> be the data matrix for this example. We would have:</p>
<p><span class="math display">\[X_e
=
\begin{bmatrix}
6 &amp; 5\\
150 &amp; 100
\end{bmatrix}\]</span></p>
<p>We note that the first row corresponds to the height feature and the second row corresponds to the weight feature. Thus we can say that <span class="math inline">\(X_{(1)}\)</span> is the row corresponding to the height feature and <span class="math inline">\(X_{(2)}\)</span> is the row corresponding to the weight feature.</p>
<p>Having presented our linear algebra notion, we now introduce the statistical background required to understand the PCA algorithm.</p>
<h1 id="sec:stat background">Statistical Background</h1>
<p>Considering PCA is heavily applied in data analytics and data representation, many statistical definitions and theorems are used in its derivation. As reviewed in Section 2.1, each row of an arbitrary <span class="math inline">\(m \times n\)</span> data matrix <span class="math inline">\(X\)</span> corresponds to a feature <span class="math inline">\(f\)</span>. We let <span class="math inline">\(f_i\)</span> be the <span class="math inline">\(i\)</span>th value in <span class="math inline">\(f\)</span>. Each feature has certain statistical characteristics that are fundamental to PCA (Jauregui 3):</p>
<p style="border:3px; border-style:solid; border-color:#FF0000; padding: 1em;">The <strong>sample mean</strong> of <span class="math inline">\(n\)</span> measurements of a feature vector <span class="math inline">\(f \in \mathbb{R}^{n}\)</span> can be defined as: <span class="math display">\[\begin{aligned}
        \mu_{f} = \frac{1}{n}(f_1 + \cdots + f_n)
    \end{aligned}\]</span> where <span class="math inline">\(f_1, \cdots, f_n\)</span> are the specific observed values.</p>
<p style="border:3px; border-style:solid; border-color:#FF0000; padding: 1em;"> The <strong>sample variance</strong> is a measurement of the spread of the observed measurements in a particular feature, where <span class="math inline">\(f \in \mathbb{R}^{n}\)</span>. It is defined as: <span class="math display">\[\begin{aligned}
           Var(f) &amp;= \frac{1}{n-1}\sum_{i=1}^{n}(f_i - \mu_f)^2
       \end{aligned}\]</span></p>
<p style="border:3px; border-style:solid; border-color:#FF0000; padding: 1em;"> The sample covariance is a measurement that describes the relationship between two features. The sample covariance between two features <span class="math inline">\(f, g \in \mathbb{R}^n\)</span> is defined as: <span class="math display">\[\begin{aligned}
           Cov(f, g) = \frac{1}{n-1}\sum_{i=1}^{m}(f_i - \mu_f)(g_i - \mu_g)
       \end{aligned}\]</span> It is important to note that a useful property of covariance is <span class="math inline">\(Cov(f,f)= Var(f)\)</span>.</p>
<p>By solely looking at its mathematical description, the concept of covariance can seem quite opaque. Luckily, it has a simple interpretation (Blitzstein and Hwang 326). A positive covariance between two features means as one increases, we expect the other to increase as well. Similarly, a negative covariance means as one increases, we expect the other to decrease. Finally if two feature vectors have covariance equal to 0, the values of one feature vector convey no information about the values of the other feature vector.</p>
<p>This concept can clearly be seen if we take two feature vectors and plot their values against each other; that is, we take the first value from each feature vector (which corresponds to the first data point) and plot it as one point, then the second value from each vector, etc. This can be seen in Figure 1.</p>
<p><img src="/static/math22/figure1.svg" title="fig:" alt="Plots of feature vectors f and g with different covariances" /></p>
<p>Furthermore, for an <span class="math inline">\(m \times n\)</span> data matrix <span class="math inline">\(X\)</span>, the covariance between each features can be represented in a covariance matrix (Shlens 5).</p>
<p style="border:3px; border-style:solid; border-color:#FF0000; padding: 1em;">A <strong>covariance matrix</strong> <span class="math inline">\(C\)</span> for a data matrix <span class="math inline">\(X\)</span> is one where <span class="math display">\[C_{ij} = \text{Cov}(X_{(i)}, X_{(j)})\]</span> Note that <span class="math inline">\(X_{(i)}, X_{(j)}\)</span> are the feature vectors of the <span class="math inline">\(i\)</span>th and <span class="math inline">\(j\)</span>th row respectively. An interesting property is that the elements along the diagonal can be simplified as follows: <span class="math display">\[C_{ii} = \text{Cov}(X_{(i)}, X_{(i)}) = \text{Var}(X_{(i)})\]</span></p>
<p>The definition of covariance can be leveraged to derive a general mathematical formula to compute a covariance matrix for a data matrix.</p>
<p><b>Thereom 3.1: </b> Let <span class="math inline">\(Z\)</span> be an <span class="math inline">\(m \times n\)</span> data matrix such that each row has mean 0, and let <span class="math inline">\(C\)</span> be the covariance matrix of <span class="math inline">\(Z\)</span>. Then, <span class="math inline">\(C = \frac{1}{n-1}ZZ^T\)</span>, where <span class="math inline">\(C\)</span> is an <span class="math inline">\(m \times m\)</span> matrix.</p>
<ol>
<p>Consider some arbitrary <span class="math inline">\(m \times n\)</span> data matrix <span class="math inline">\(Z\)</span> where each row has mean 0. We have:</p>
<p><span class="math display">\[Z =
     \begin{bmatrix}
        Z_{11} &amp; \dots &amp; Z_{1n} \\
        \vdots &amp; \ddots &amp; \vdots \\
        Z_{m1} &amp; \dots &amp; Z_{mn}
     \end{bmatrix}\]</span></p>
<p><span class="math display">\[Z^T =
     \begin{bmatrix}
        Z_{11} &amp; \dots &amp; Z_{m1} \\
        \vdots &amp; \ddots &amp; \vdots \\
        Z_{1n} &amp; \dots &amp; Z_{mn}
     \end{bmatrix}\]</span></p>
<p>From Definition [cov], for any <span class="math inline">\(i, j\)</span> where <span class="math inline">\(1 \leq i, j \leq m\)</span>, we get <span class="math display">\[\begin{aligned}
    \text{Cov}(Z_{(i)}, Z_{(j)}) &amp;= \frac{1}{n - 1} ((Z_{i1} - \mu_{Z_{(i)}})(Z_{j1} - \mu_{Z_{(j)}}) + \cdots + (Z_{in} - \mu_{Z_{(i)}})(Z_{jn} - \mu_{Z_{(j)}}))\\
    &amp;= \frac{1}{n - 1} (Z_{i1}Z_{j1} + \cdots + Z_{in}Z_{jn})\\
    &amp;= \frac{1}{n - 1} Z_{(i)} \cdot Z_{(j)}\\
    &amp;= \frac{1}{n - 1} Z_{(i)} {Z_{(j)}}^T
    \end{aligned}\]</span></p>
<p>Therefore, by the definition of the covariance matrix <span class="math inline">\(C\)</span>, we get <span class="math display">\[\begin{aligned}
        C = \frac{1}{n - 1} ZZ^T
    \end{aligned}\]</span></p>
</ol>
<h1 id="sec:principal intro">What is a Principal Component?</h1>
<p>The fundamental goal of PCA is to represent a given dataset in terms of a basis defined by its principal components. At a high level, a basis defined in terms of principal components is one wherein each feature of a data matrix has <em>unique information</em> (Definition [def:unique]).</p>
<p>We can leverage the statistical definitions introduced in Section [sec:stat background] to both intuitively and mathematically define principal components of a data matrix. We will then use these definitions to gain intuition behind and explain how the PCA algorithm works.</p>
<p style="border:3px; border-style:solid; border-color:#FF0000; padding: 1em;">A feature presents <strong>unique information</strong> if the covariance between that feature and all other features in the data matrix is 0.</p>
<p>We can apply the definition of unique information to a data matrix <span class="math inline">\(X\)</span> to formally define the set of principal components of <span class="math inline">\(X\)</span>:</p>
<p style="border:3px; border-style:solid; border-color:#FF0000; padding: 1em;"> The set of principal components is a orthonormal basis <span class="math inline">\(\mathcal{B}\)</span> of a data matrix <span class="math inline">\(X\)</span> such that when <span class="math inline">\(X\)</span> is written in the coordinate system of <span class="math inline">\(\mathcal{B}\)</span>, each feature presents unique information. In other words, using the notation from class, <span class="math inline">\([X]_{\mathcal{B}}\)</span> is a data matrix with features that all present unique information. A principal component is simply an individual vector in the set of principal components.</p>
<p>We can now nicely frame what exctly the PCA algorithm does. In simple terms, PCA computes the set of principal components for a dataset. As per Definition [def:principal], the set of principal components form an orthonormal basis for the input data matrix. We use this basis to form a new data matrix written with respect to the coordinates of the new basis. The features of this new dataset have 0 covariance with all other features. This means that every feature presents unique information. In PCA, we define the amount of information that a feature conveys as the variance of the feature vector (more on this in section [sec:dim reduction]). To reduce dimensionality, we simply take the new data matrix and remove the features with the lowest variance until we have reached our desired dimension. We remove the lowest variance features because these convey the least amount of information. This is how PCA allows us to reduce the dimensionality of a dataset with minimal information loss. We discuss more about the specifics of dimensionality reduction in section [sec:dim reduction].</p>
<h2 id="sec:motive">A Motivating Example</h2>
<p>Consider a scenario in which you are collecting the heights, weights, and shoe sizes of a group of people. Intuitively, we can reason these three quantities are heavily positively correlated, that is in general the taller someone is, the heavier they are and the larger their shoe size. We can see this in the following plot of some example data:</p>
<div class="figure">
<img src="/static/math22/figure2.svg" alt="Simulated distribution for sample data" style="width:80.0%" />
<p class="caption">Simulated distribution for sample data<span data-label="fig:motive"></span></p>
</div>
<p>Although this dataset seems scattered, we will prove in Section [sec:int] that with minimal information loss we can represent the data in terms of 2 dimensions. We will now use this motivating example to fully introduce and explain the PCA algorithm.</p>
<h1 id="sec:alg">PCA Algorithm: Overview and Implementation</h1>
<p>As explained in Section [sec:principal intro], the goal of PCA is to produce a set of principal componants for an input data matrix. In order to do this, PCA performs 4 main steps: (1) standardizing the data matrix, (2) computing the covariance matrix for the standardized data matrix, (3) diagonalizing the covariance matrix, and (4) changing the coordinate basis of the data matrix. The pseudocode for each step is discussed in Algorithm [alg:main]. We introduce the algorithm here to frame the next section in which we delve into the mathematical derivation of PCA. Having the algorithm to reference back to will be useful when trying to understand the underlying theory behind PCA.</p>
<p><strong>Principal Component Analysis Algorithm Psuedocode</strong></p>
<div style="border:3px; border-style:solid; border-color:#FF0000; padding: 1em;">
<p ><em>Input:</em> <span class="math inline">\(X\)</span>, an <span class="math inline">\(m \times n\)</span> data matrix with <span class="math inline">\(m\)</span> features, each with <span class="math inline">\(n\)</span> data points.<br />
<em>Standardize Data Matrix:</em></p>
<ul>
<li><p>Make the means of every variable zero: <span class="math inline">\(Z = X - 
        \begin{bmatrix}
           \mu_{X_{(1)}} &amp; \dots &amp; \mu_{X_{(1)}}\\
           \vdots &amp; \ddots &amp; \vdots\\
           \mu_{X_{(m)}} &amp; \dots &amp; \mu_{X_{(m)}}\\
        \end{bmatrix}\)</span></p></li>
</ul>
<p><em>Compute Covariance Matrix</em>:</p>
<ul>
<li><p><span class="math inline">\(C_Z = \frac{1}{n - 1} ZZ^T\)</span></p></li>
</ul>
<p><em>Diagonalize Covariance Matrix:</em></p>
<ul>
<li><p>Ordered eigenvalues of <span class="math inline">\(C_Z\)</span>, from greatest to least: <span class="math inline">\(\{\lambda_1, \cdots, \lambda_n\}\)</span></p></li>
<li><p>Normalized eigenvectors of <span class="math inline">\(C_Z\)</span>: <span class="math inline">\(\{u_1, \cdots, u_n\}\)</span></p></li>
<li><p>Eigenbasis of <span class="math inline">\(C_Z\)</span>: <span class="math inline">\(P_Z = [u_1, \cdots, u_n]\)</span></p></li>
<li><p>Diagonalized covariance matrix: <span class="math inline">\(\begin{bmatrix}
            \lambda_{1} &amp;  &amp; 0\\
             &amp; \ddots &amp;  \\
             0&amp;  &amp; \lambda_{n}
        \end{bmatrix}\)</span></p></li>
<li><p>By the Diagonalization Theorem, <span class="math inline">\(C_Z = P_Z D_Z {P_Z}^T\)</span>.</p></li>
</ul>
<p><em>Calculate New Features:</em></p>
<ul>
<li><p><span class="math inline">\(\widehat{Z} = {P_Z}^T Z\)</span></p></li>
<li><p><span class="math inline">\(\widehat{C}_Z = D_Z\)</span></p></li>
</ul>
<p><em>Output:</em> <span class="math inline">\(\widehat{Z}\)</span>, the <span class="math inline">\(m \times n\)</span> data matrix in terms of the principal component basis of <span class="math inline">\(X\)</span>; and <span class="math inline">\(\widehat{C}_Z\)</span>, the covariance matrix of <span class="math inline">\(\widehat{Z}\)</span>. [alg:main]</p>
</div>
<p>Essentially, this algorithm is able to represent our <span class="math inline">\(m \times n\)</span> data matrix <span class="math inline">\(X\)</span> in terms of its principal components. As explained in the introduction, the data matrix defined in this basis has numerous computational advantages that further emphasize the motivation of Algorithm [alg:main].</p>
<h1 id="sec:int">PCA Algorithm: Intuition and Derivation</h1>
<p>Consider the example data matrix described in section [sec:motive] and plotted in Figure 2 <span class="math inline">\(X\)</span>. We will use this data matrix in the following section to explain the steps of PCA. <span class="math display">
\[
    X = \begin{bmatrix}
        171  &amp; 195 &amp; 157 &amp; 160 &amp; 178 &amp; 168 &amp; 171 &amp; 165 &amp; 175 &amp; 163 &amp; 158 &amp; 159 \\
        39  &amp; 46 &amp; 37 &amp; 38 &amp; 39 &amp; 39 &amp; 38 &amp; 41 &amp; 39 &amp; 44 &amp; 38 &amp; 37   \\
        152  &amp; 98 &amp; 56 &amp; 131 &amp; 107 &amp; 59 &amp; 141 &amp; 57 &amp; 135 &amp; 57 &amp; 149 &amp; 80 
    \end{bmatrix}\]</span></p>
<h2 id="standardizing-the-dataset">Standardizing the Dataset</h2>
<p>Prior to computing the principal components of the dataset, standardizing the input data matrix is crucial in order to leverage the certain definitions outlined above. This can be achieved by taking every element of the data matrix and subtracting the mean of the values in that element’s row from itself. Let <span class="math inline">\(Z\)</span> be the resulting data matrix.</p>
<p>We can perform such an operation on the data matrix <span class="math inline">\(X\)</span> above as follows: <span class="math display">\[Z = X -  \begin{bmatrix}
           168.33 &amp; \cdots &amp; 168.33\\
            39.58  &amp; \cdots &amp; 39.58 \\ 
          101.83 &amp; \cdots &amp; 101.83
        \end{bmatrix}\]</span> <span class="math display">\[\begin{aligned}
    Z = \begin{bmatrix}
2.7 &amp; 26.7 &amp; -11.3 &amp; -8.3 &amp; 9.7 &amp; -0.3 &amp; 2.7 &amp; -3.3 &amp; 6.7 &amp; -5.3 &amp; -10.3 &amp; -9.3 \\ 
-0.6 &amp; 6.4 &amp; -2.6 &amp; -1.6 &amp; -0.6 &amp; -0.6 &amp; -1.6 &amp; 1.4 &amp; -0.6 &amp; 4.4 &amp; -1.6 &amp; -2.6 \\
50.2 &amp; -3.8 &amp; -45.8 &amp; 29.2 &amp; 5.2 &amp; -42.8 &amp; 39.2 &amp; -44.8 &amp; 33.2 &amp; -44.8 &amp; 47.2 &amp; -21.8 
        \end{bmatrix}\end{aligned}\]</span></p>
<p>Now, we find the covariance matrix of <span class="math inline">\(Z\)</span>:</p>
<p><span class="math display">\[\begin{aligned}
    C_Z = \frac{1}{n - 1} ZZ^T = 
    \begin{bmatrix}
       117.697 &amp; 19.152 &amp; 72.970\\
       19.152 &amp; 7.720 &amp; -29.167 \\
       72.970 &amp; -29.167 &amp; 1518.150
    \end{bmatrix}\end{aligned}\]</span></p>
<h2 id="computing-the-principal-component-basis">Computing the Principal Component Basis</h2>
<p>Given that we have standardized the dataset into <span class="math inline">\(Z\)</span> where each row has mean 0, we now wish to compute the principal components and define the new basis.</p>
<p>We know <span class="math inline">\(Z_{[i]} \in \mathbb{R}^{m}\)</span>, so each data point is in <span class="math inline">\(\mathbb{R}^{m}\)</span>. Let <span class="math inline">\(\mathcal{E}\)</span> be the standard basis of <span class="math inline">\(\mathbb{R}^{m}\)</span>. We wish to compute the basis <span class="math inline">\(\mathcal{B} = \{b_1, \dots, b_n\}\)</span> such that writing <span class="math inline">\(Z\)</span> in the coordinate system of <span class="math inline">\(\mathcal{B}\)</span> results in each transformed feature presenting unique information, thus making the correlation between each row 0. Let <span class="math inline">\(\underset{\mathcal{B}\leftarrow\mathcal{E}}{P}\)</span> be the change of basis matrix from <span class="math inline">\(\mathcal{E}\)</span> to <span class="math inline">\(\mathcal{B}\)</span>. We know the following:</p>
<p><span class="math display">\[\underset{\mathcal{B}\leftarrow\mathcal{E}}{P} = \underset{\mathcal{E}\leftarrow\mathcal{B}}{P}^{-1} = [b_1 \dots b_n]^{-1}\]</span></p>
<p>Once we find this change of basis matrix we can use it to transform our normalized data matrix <span class="math inline">\(Z\)</span> into some new data matrix <span class="math inline">\(\widehat{Z}\)</span> that has the properties we desire, as follows:</p>
<p><span class="math display">\[\widehat{Z} = {P_Z}^T Z = [b_1 \dots b_n]^{-1}Z\]</span></p>
<p>To find the basis <span class="math inline">\(\mathcal{B}\)</span>, we must formalize the exact properties that we want our data matrix <span class="math inline">\(\widehat{Z}\)</span> to have.</p>
<h3 id="desired-properties-of-principal-component-basis">Desired Properties of Principal Component Basis</h3>
<p>With the objective of PCA in mind, as explained by Definition [def:principal], we want the covariance of any two distinct features in <span class="math inline">\(\widehat{Z}\)</span> to be 0. To achieve this, we want <span class="math inline">\(\widehat{C}_Z\)</span>, the covariance matrix of <span class="math inline">\(\widehat{Z}\)</span>, to be a diagonal matrix. This is achieved through setting the principal component basis <span class="math inline">\(\mathcal{B}\)</span> to the set of normalized eigenvectors of <span class="math inline">\(C_Z\)</span>. The following theorem proves that the eigenvectors correspond to the principal components:</p>
<p>[cov diagonalizable] For an arbitrary covariance matrix <span class="math inline">\(C\)</span>, we have <span class="math inline">\(C = \mathcal{P}D\mathcal{P}^T\)</span> where <span class="math inline">\(D\)</span> is a diagonal matrix and <span class="math inline">\(\mathcal{P}\)</span> is an orthogonal matrix of the eigenvectors of <span class="math inline">\(C\)</span>.</p>
<ol>
<li><p>By the definition of the covariance matrix, we know that <span class="math inline">\(C\)</span> is symmetric because <span class="math inline">\(C_{ij} = \text{Cov}(X_{(i)}, X_{(j)})\)</span>, and by the definition of covariance this equals <span class="math inline">\(\text{Cov}(X_{(j)}, X_{(i)}) = C_{ji}\)</span>, which by proves covariance matrices are symmetric. Therefore, suppose for the sake of contradiction that there exists a symmetric <span class="math inline">\(n \times n\)</span> covariance matrix <span class="math inline">\(C\)</span> such that <span class="math inline">\(C\)</span> is not orthogonally diagonalizable, where <span class="math inline">\(n\)</span> is as small as possible. It is known that symmetric matrices have all real eigenvalues, so let <span class="math inline">\(\lambda_1 \in \mathbb{R}\)</span> be an eigenvalue with corresponding unit eigenvector <span class="math inline">\(u_1\)</span>, where <span class="math inline">\(Cu_1 = \lambda_1 u_1\)</span>.<br />
Now, let <span class="math inline">\(W = \{w \in \mathbb{R}^n: u_1 \cdot w = 0\} = \text{Span}\{u_1\}^\bot = N({u_1})^\bot\)</span>, where dim<span class="math inline">\((W) = n - 1\)</span>. Then, <span class="math inline">\(\mathcal{B} = \{u_2, \cdots, u_n\}\)</span>, representing all of the other unit eigenvectors of <span class="math inline">\(C\)</span>, is an orthonormal basis for <span class="math inline">\(W\)</span>. Note that if <span class="math inline">\(w \in W\)</span>, then <span class="math inline">\(Cw \in W\)</span> since <span class="math inline">\(u_1 \cdot (Cw) = (Cu_1)^T w = (\lambda_1 u_1)^T w = \lambda_1 (u_1 \cdot w) = 0\)</span>.<br />
Now, let <span class="math inline">\(\mathcal{P} = [u_2 \cdots u_n]\)</span>. Any <span class="math inline">\(x \in W\)</span> can be written <span class="math inline">\(x = c_2 u_2 + \cdots + c_n u_n\)</span>, so <span class="math inline">\(x = \mathcal{P}{[x]}_\mathcal{B}\)</span>. Then, since <span class="math inline">\(\mathcal{P}\)</span> has orthonormal columns, we have <span class="math inline">\(\mathcal{P}^T x = {[x]}_\mathcal{B}\)</span>. Hence <span class="math inline">\(M = \mathcal{P}^T CP\)</span>, where <span class="math inline">\(M\)</span> is a symmetric <span class="math inline">\(n - 1 \times n - 1\)</span> matrix. Therefore, <span class="math inline">\(M\)</span> must be orthogonally diagonalizable by our initial assumption.<br />
Now, let <span class="math inline">\(\{{[x_2]}_\mathcal{B}, \cdots, {[x_n]}_\mathcal{B}\}\)</span> be an orthonormal basis that diagonalizes <span class="math inline">\(M\)</span>. Since we have an eigenbasis, <span class="math inline">\(M{[x_k]}_\mathcal{B} = \mu_k {[x_k]}_\mathcal{B}\)</span> for each <span class="math inline">\(k \in \{2, \cdots, n\}\)</span>. Then, <span class="math display">\[\begin{aligned}
        \mathcal{P}^T C\mathcal{P}\mathcal{P}^T x_k &amp;= \mu_k \mathcal{P}^T x_k\\
        \mathcal{P}^T Cx_k &amp;= \mu_k \mathcal{P}^T x_k\\
        Cx_k &amp;= \mu_k x_k
     \end{aligned}\]</span> Therefore, <span class="math inline">\(x_k\)</span> is an eigenvector for covariance matrix <span class="math inline">\(C\)</span> for each <span class="math inline">\(k \in \{2, \cdots, n\}\)</span>, and thus<br />
<span class="math inline">\(\{u_1, x_2, \cdots, x_n\}\)</span> is an orthonormal eigenbasis for <span class="math inline">\(C\)</span>. Hence, <span class="math inline">\(C\)</span> is orthogonally diagonalizable and we have a contradiction.</p></li>
</ol>
<p>As outlined in section [sec:stat background], we know that <span class="math inline">\(C_Z\)</span> is a symmetric matrix, and from Theorem [cov diagonalizable], we know that <span class="math inline">\(C_Z\)</span> has <span class="math inline">\(n\)</span> orthogonal eigenvectors. Let the normalized versions of these orthogonal eigenvectors be <span class="math inline">\(u_1, \dots, u_n\)</span>. We thus have:</p>
<p><span class="math display">\[\underset{\mathcal{B}\leftarrow\mathcal{E}}{\mathcal{P}} = \underset{\mathcal{E}\leftarrow\mathcal{B}}{\mathcal{P}}^{-1} = [u_1 \dots u_n]^{-1}\]</span></p>
<p>As <span class="math inline">\(u_1, \dots, u_n\)</span> are all orthonormal vectors, we know that <span class="math inline">\(\underset{\mathcal{E}\leftarrow\mathcal{B}}{\mathcal{P}}\)</span> is an orthogonal matrix and thus <span class="math inline">\(\underset{\mathcal{E}\leftarrow\mathcal{B}}{\mathcal{P}}^{-1} = \underset{\mathcal{E}\leftarrow\mathcal{B}}{\mathcal{P}}^T\)</span>. We thus have:</p>
<p><span class="math display">\[\underset{\mathcal{B}\leftarrow\mathcal{E}}{\mathcal{P}} = \underset{\mathcal{E}\leftarrow\mathcal{B}}{\mathcal{P}}^T = 
\begin{bmatrix}
   u_1^T \\
   \vdots \\
   u_n^T 
\end{bmatrix}\]</span></p>
<p>Before proving our selection of <span class="math inline">\(\underset{\mathcal{B}\leftarrow\mathcal{E}}{\mathcal{P}}\)</span> yields a diagonal covariance matrix of <span class="math inline">\(\widehat{Z}\)</span>, <span class="math inline">\(\widehat{C}_Z\)</span>, as required, we need to prove the following theorem:</p>
<p>[the: zero mean] With <span class="math inline">\(\underset{\mathcal{B}\leftarrow\mathcal{E}}{\mathcal{P}}\)</span> as defined above and <span class="math inline">\(Z\)</span> having <span class="math inline">\(m\)</span> rows with mean 0 and <span class="math inline">\(n\)</span> columns, the transformed data matrix <span class="math inline">\(\widehat{Z}\)</span> also has all rows with mean 0.</p>
<ol>
<li><p>We have from our definition of <span class="math inline">\(\widehat{Z}\)</span> that: <span class="math display">\[\begin{aligned}
         \widehat{Z} = \underset{\mathcal{B}\leftarrow \mathcal{E}}{\mathcal{P}}Z 
     \end{aligned}\]</span></p>
<p>Furthermore, using the definition of matrix multiplication, we can select an arbitrary row <span class="math inline">\(\widehat{Z}_{\{i\}}\)</span> and define it as follows: <span class="math display">\[\begin{aligned}
        \widehat{Z}_{(i)} &amp;= \begin{bmatrix} \underset{\mathcal{B}\leftarrow\mathcal{E}}{\mathcal{P}}_{(i)} Z_{[0]} &amp; \underset{\mathcal{B}\leftarrow\mathcal{E}}{\mathcal{P}}_{(i)} Z_{[1]} &amp; \cdots &amp; \underset{\mathcal{B}\leftarrow\mathcal{E}}{\mathcal{P}}_{(i)} Z_{[n]}  \end{bmatrix} \\
        \mu_{\widehat{Z}_{(i)}} &amp;= \frac{\underset{\mathcal{B}\leftarrow\mathcal{E}}{\mathcal{P}}_{(i)} Z_{[0]} + \underset{\mathcal{B}\leftarrow\mathcal{E}}{\mathcal{P}}_{(i)} Z_{[1]} + \cdots + \underset{\mathcal{B}\leftarrow\mathcal{E}}{\mathcal{P}}_{(i)} Z_{[n]}}{n} \\
        &amp;= \frac{\underset{\mathcal{B}\leftarrow\mathcal{E}}{\mathcal{P}}_{(i)} (Z_{[0]} + Z_{[1]} + \cdots + Z_{[n]})}{n}
    \end{aligned}\]</span> And since we know that the average of each row is 0, the sum of each column must be the 0 vector. Thus we can further simplify. <span class="math display">\[\begin{aligned}
        \mu_{\widehat{Z}_{\{i\}}} &amp;= \frac{\mathcal{P}_{\{i\}}(\Vec{0})}{n} \\ &amp;= 0
    \end{aligned}\]</span> Thus, we have shown that given a data matrix <span class="math inline">\(X\)</span>, even after changing the basis, the average of each row remains 0.</p></li>
</ol>
<p>We can now show that this selection of <span class="math inline">\(\underset{\mathcal{B}\leftarrow\mathcal{E}}{\mathcal{P}}\)</span> leads to a diagonal <span class="math inline">\(\widehat{C}_Z\)</span> as we wanted.</p>
<p>The selection of <span class="math inline">\(\underset{\mathcal{B}\leftarrow\mathcal{E}}{\mathcal{P}} = [u_1 \dots u_n]^{-1}\)</span>, where <span class="math inline">\(\{u_1 \dots u_n\}\)</span> are normalized eigenvectors of <span class="math inline">\(C_Z\)</span>, yields a transformed data matrix <span class="math inline">\(\widehat{Z}=\underset{\mathcal{B}\leftarrow\mathcal{E}}{\mathcal{P}}Z\)</span> with a diagonal covariance matrix.</p>
<ol>
<li><p>Theorem [the: zero mean] proved that the mean of every row of <span class="math inline">\(\widehat{Z}\)</span> is 0. Thus, we can write the covariance matrix of <span class="math inline">\(\widehat{Z}\)</span> as:</p>
<p><span class="math display">\[\begin{aligned}
    \widehat{C}_Z = \frac{1}{n+1}\widehat{Z}\widehat{Z}^T
    \end{aligned}\]</span></p>
<p>For convenience of writing let <span class="math inline">\(\mathcal{P} = \underset{\mathcal{B}\leftarrow\mathcal{E}}{\mathcal{P}}\)</span>. Then, we know <span class="math inline">\(\widehat{Z} = {\mathcal{P}_Z}^TZ\)</span> and thus we can rewrite the above as:</p>
<p><span class="math display">\[\begin{aligned}
        \widehat{C}_Z &amp;= \frac{1}{n+1}({\mathcal{P}_Z}^T Z)({\mathcal{P}_Z}^T Z)^T \\
        &amp;= {\mathcal{P}_Z}^T \left(\frac{1}{n+1}Z Z^T\right) \mathcal{P}_Z = {\mathcal{P}_Z}^T C_Z \mathcal{P}_Z
    \end{aligned}\]</span></p>
<p>However, recall above that we previously applied Theorem [cov diagonalizable] to <span class="math inline">\(C_Z\)</span> to find that <span class="math inline">\(C_Z = \mathcal{P}_Z D_Z {\mathcal{P}_Z}^T\)</span>. Thus, we can further simplify our expression for the covariance of <span class="math inline">\(\widehat{C}_Z\)</span> as follows: <span class="math display">\[\begin{aligned}
        \widehat{C}_Z &amp;=  ({\mathcal{P}_Z}^T \mathcal{P}_Z) D_Z ({\mathcal{P}_Z}^T \mathcal{P}_Z) \\
        &amp;= D_Z
    \end{aligned}\]</span></p>
<p>Thus we have <span class="math inline">\(\widehat{C}_Z\)</span>, a diagonal matrix.</p></li>
</ol>
<p>Therefore, the aforementioned selection of normalized eigenvectors of <span class="math inline">\(C_Z\)</span> as the basis vectors of our new basis <span class="math inline">\(\mathcal{B}\)</span> will result in the transformed <span class="math inline">\(\widehat{Z}\)</span> data matrix with features that present unique information.<br />
Now, we can apply this process to our example standardized data matrix <span class="math inline">\(Z\)</span>, as follows: <span class="math display">\[\begin{aligned}
\{\lambda_1, \lambda_2, \lambda_3\} &amp;= \{1522.460, 117.757, 3.346\}\\
\{u_1, u_2, u_3\} &amp;= \left\{\begin{bmatrix}0.052 \\ -0.019 \\ 0.998\end{bmatrix}, \begin{bmatrix}-0.982 \\ -0.183 \\ 0.047\end{bmatrix}, \begin{bmatrix}-0.182 \\ 0.983 \\ 0.028\end{bmatrix}\right\}\\
\mathcal{P} &amp;= \begin{bmatrix} 0.052 &amp; -0.982 &amp; -0.182\\ -0.019 &amp; -0.183 &amp; 0.983\\ 0.998 &amp; 0.047 &amp; 0.028 \end{bmatrix}\\
\widehat{Z} &amp;= {\mathcal{P}_Z}^T Z\end{aligned}\]</span> <span class="math display">\[\begin{aligned}
\widehat{Z} &amp;= \begin{bmatrix} 50.3 &amp; -2.5 &amp; -46.3 &amp; 28.7 &amp; 4.7 &amp; -42.7 &amp; 39.3 &amp; -44.9 &amp; 33.5 &amp; -45.1 &amp; 46.6 &amp; -22.2\\ -0.2 &amp; -27.6 &amp; 9.4 &amp; 9.8 &amp; 9.9 &amp; -1.6 &amp; -0.5 &amp; 0.9 &amp; -4.9 &amp; 2.3 &amp; 12.6 &amp; 8.6\\ 0.3 &amp; 1.3 &amp; -1.8 &amp; 0.8 &amp; 1.3 &amp; -1.7 &amp; -1.9 &amp; 0.7 &amp; -0.9 &amp; 4.0 &amp; 1.6 &amp; -1.5\end{bmatrix}\end{aligned}\]</span> <span class="math display">\[\begin{aligned}
\widehat{C}_Z &amp;= D_Z = \begin{bmatrix} 1522.460 &amp; 0 &amp; 0\\0 &amp; 117.757 &amp; 0\\ 0 &amp; 0 &amp; 3.346\end{bmatrix}\end{aligned}\]</span></p>
<h2 id="sec:dim reduction">Dimensionality Reduction</h2>
<p>We now consider how we reduce the dimension of <span class="math inline">\(\widehat{Z}\)</span> with minimal information loss. For a data matrix with 0 covariance between features, we define the following:</p>
<p>[Total Variance] The total variance is the sum of the variances of each feature. This is simply the sum of the diagonal entries of the transformed covariance matrix <span class="math inline">\(\widehat{C}_Z\)</span>.</p>
<p>[Quantity of Information Conveyed by a Feature] Let <span class="math inline">\(f\)</span> be some feature of a data matrix <span class="math inline">\(X\)</span>, and let <span class="math inline">\(T\)</span> be the total variance of <span class="math inline">\(X\)</span>. We denote the quantity of information that a feature <span class="math inline">\(f\)</span> conveys by <span class="math inline">\(Q(f)\)</span>. It is calculated as follows:</p>
<p><span class="math display">\[Q(f) = \frac{Var(f)}{T}\]</span></p>
<p><span class="math inline">\(Q(f)\)</span> is simply the proportion of the total variance that the variance of <span class="math inline">\(f\)</span> makes up. This definition stems from the fact that in PCA, we assume that the more variance a feature has, the more information it conveys about the data.</p>
<p>We note from the above definition that the information that a feature conveys is directly proportional to the variance of that feature. We also note that this definition only holds for data matrices that have 0 covariance between features, and thus all features present unique information. This is because if the features present unique information, then variance is a better metric for the amount of information they convey compared to when features do not present unique information. If a feature has non-zero covariance with another feature, then we can in some way deduce each of the feature’s values from the other, and thus the information they convey overlaps in some way.</p>
<p>With this concept of information formally introduced, we can now tackle the problem of reducing the dimensionality of <span class="math inline">\(\widehat{Z}\)</span> with minimum information loss. Remember, each feature in <span class="math inline">\(\widehat{Z}\)</span> presents unique information. If we want to reduce the dimensionality of <span class="math inline">\(\widehat{Z}\)</span> to some lower dimension <span class="math inline">\(k\)</span> such that <span class="math inline">\(0 &lt; k &lt; m\)</span>, we can simply remove the <span class="math inline">\(n-k\)</span> rows of <span class="math inline">\(\widehat{Z}\)</span> that have the lowest variances. This removes the <span class="math inline">\(n-k\)</span> features that have the lowest variance, and from our assumption that the lower the variance of a feature, the less information it conveys, this reduces the dimension of <span class="math inline">\(\widehat{Z}\)</span> to <span class="math inline">\(k\)</span> with minimum information loss.</p>
<p>We note that removing these features is identical to removing the corresponding principal components and projecting <span class="math inline">\(Z\)</span> onto the subspace spanned by the remaining principal components. The reason these two are the same is because the principal components form an orthogonal basis. Thus, removing the coordinates corresponding to the basis vectors that are removed is the same as simply projecting the original data onto the subspace of the smaller set of basis vectors.</p>
<p>Now let’s take our <span class="math inline">\(\widehat{Z}\)</span> data and actually reduce the dimensionality. Let <span class="math inline">\(f_1\)</span> be the feature corresponding to the first row of <span class="math inline">\(\widehat{Z}\)</span>. The first thing we note is that the quantity of information conveyed by <span class="math inline">\(f_1\)</span> is incredibly large. From the covariance matrix we have <span class="math inline">\(Var(f_1) = 1552.460 \)</span> and the total variance is <span class="math inline">\(1643.56\)</span>. Thus we have <span class="math inline">\(Q(f_1) = 0.926\)</span>. This is incredible. It means we can represent almost 93% of the information of the dataset with only a single principal component and thus only one coordinate for each data point! If we include the feature with the second highest variance, we can convey <span class="math inline">\(99.8\%\)</span> of the information. Having found this out, we decide to reduce the dimensionality of <span class="math inline">\(\widehat{Z}\)</span> to 2. Let this new reduced dimension dataset be <span class="math inline">\(\widehat{Z}_2\)</span>. By removing the feature with the lowest variance we get:</p>
<p><span class="math display">\[\widehat{Z}_2 =
\begin{bmatrix} 50.3 &amp; -2.5 &amp; -46.3 &amp; 28.7 &amp; 4.7 &amp; -42.7 &amp; 39.3 &amp; -44.9 &amp; 33.5 &amp; -45.1 &amp; 46.6 &amp; -22.2\\ -0.2 &amp; -27.6 &amp; 9.4 &amp; 9.8 &amp; 9.9 &amp; -1.6 &amp; -0.5 &amp; 0.9 &amp; -4.9 &amp; 2.3 &amp; 12.6 &amp; 8.6 \end{bmatrix}\]</span></p>
<p>Furthermore, in Figure 3 we visualize <span class="math inline">\(\widehat{Z}_2\)</span> in the principal component basis as a 2-dimensional plot. Notice how the data varies most along the principle component #1 axis. This supports what we described above when we found <span class="math inline">\(Q(f_1) = 0.926\)</span>.</p>
<div class="figure">
<img src="/static/math22/figure3.svg" alt="Two-Dimensional Data Matrix in Principal Component Basis" style="width:80.0%" />
</div>
<h1 id="facial-image-compression-application">Facial Image Compression Application</h1>
<p>An interesting application of PCA is image compression, and the ability to reduce the dimensions of an image with minimal information loss. We use a web scraped Kaggle dataset of human faces to demonstrate how PCA can be used in this manner.</p>
<ol>
<li><p><strong>Preprocessing:</strong> Note that prior to performing PCA, all images were grayscaled such that a single value represented each pixel in the image. Furthermore, all images were resized in order to ensure that each data point had the same number of features.</p></li>
</ol>
<p>After computing the principal component set and arranging them as explained in Section [sec:dim reduction], we can plot the cumulative percent of total information represented by the first <span class="math inline">\(n\)</span> number of principle components ([fig: graph]).</p>
<div class="figure">
<img src="/static/math22/figure4.svg" alt="Relation between number of dimensions and cumulative explained information" style="width:80.0%" />
</div>
<p>To see how effective this compression technique is, we take one of the images and express it with 0.1% and 2% of the total principle components (Figure [fig:visual]).</p>
<div class="figure">
<img src="/static/math22/figure5.svg" alt="Relation between number of dimensions and cumulative explained information" style="width:90.0%" />
</div>
<p>Figure [fig:visual] clearly demonstrates that a lot of the information is encoded in merely 2<span class="math inline">\(\%\)</span> of all 10,000 components because the image is still recognisably a face. Referring to Figure [fig: graph], mathematically we can say that <span class="math inline">\(94.5\%\)</span> of the total information is included. This application demonstrates the fundamental idea of PCA, which is that data can be encoded in fewer dimensions without losing much information from the original data.</p>
<h1 id="limitations">Limitations</h1>
<p>Now that we know how the PCA algorithm works and have an understanding of its derivation, it is important to be aware of the limitations of the algorithm. More precisely, we will note occasions where using PCA to reduce dimensionality would not be suitable.</p>
<p>Firstly, PCA finds some change of basis matrix and uses this to construct a new dataset with respect to this new basis. Because this is a simple matrix multiplication, the entire transformation is linear. This means that PCA can never find some non-linear mapping to a new coordinate system. Sometimes, the best way to better understand a dataset or reduce its dimensionality is to use a non-linear transformation. Take for example cases where we may want to change from Cartesian to polar coordinates to gain more insight into our dataset. PCA will never be able to do this as this change of coordinates is non-linear (Shlens ,12).</p>
<p>Secondly, it is important to note that sometimes a dataset is complex enough that each of its axes provide novel information and any form of dimensionality reduction may result in losing a lot of important information. We could see this if after we ran PCA, the diagonal entries of the resulting covariance matrix were all large similar values. This would tell us that the dataset varies in similar amounts along each of the principal components and thus it would be impossible to reduce the dimensionality of the dataset using PCA and not lose considerable amounts of information. Figure [fig:limitation] is a good example of this. The red arrow is the principal component and the blue arrow is the second principal component. We can see that the data varies by similar amounts in both directions and thus projecting onto either of the principal components would appear to lead to significant information loss. This heavily contrasts with our example dataset shown in Figure 2.</p>
<div class="figure">
<img src="/static/math22/figure6.svg" alt="Dataset that PCA cannot work on" style="width:60.0%" />
<p class="caption">Dataset that PCA cannot work on<span data-label="fig:limitation"></span></p>
</div>
<h1 id="conclusion">Conclusion</h1>
<p>In this paper, we have presented how the PCA algorithm works and an interesting application of PCA. Our explanation of PCA focused both on building an intuition behind why the algorithm works and a rigorous treatment of the underlying mathematics that dictate how and why the algorithm works. We also discussed the limitations of PCA and situations in which it would fail to be able to reduce the dimension of a dataset without leading to large amounts of information loss.</p>
<p>If you are still interested in PCA, there are many more interesting applications! PCA is often used for pre-processing datasets before applying machine learning to them (Goonewardana).</p>
<p>Furthermore, research has investigated optimizing PCA, which has yielded further improved algorithms such as Independent Component Analysis (Hyvärinen 1). Essentially, these algorithms relax certain assumptions and are applicable to even more distributions and data matrices as a result.</p>
<h1 id="references" class="unnumbered">References</h1>
<ol>
<li><p>Blitzstein, J. and Hwang, J. (2019) <em>Introduction to Probability: Second Edition.</em> Taylor &amp; Francis Group, LLC. Retrieved from<br />
<a href="https://drive.google.com/file/d/1VmkAAGOYCTORq1wxSQqy255qLJjTNvBI/view" class="uri">https://drive.google.com/file/d/1VmkAAGOYCTORq1wxSQqy255qLJjTNvBI/view</a>.</p></li>
<li><p>Goonewardana, H. (2019, Feb.) <em>PCA: Application in Machine Learning</em> Apprentice Journal. Retrieved from <a href="https://medium.com/apprentice-journal/pca-application-in-machine-learning-4827c07a61db" class="uri">https://medium.com/apprentice-journal/pca-application-in-machine-learning-4827c07a61db</a>.</p></li>
<li><p>Gupta, A. (2020) “Human Faces.” Kaggle. Retrieved from<br />
<a href="https://www.kaggle.com/ashwingupta3012/human-faces" class="uri">https://www.kaggle.com/ashwingupta3012/human-faces</a>.</p></li>
<li><p>Hyvärinen, A. and Oja, E. (2000) <em>Independent Component Analysis: Algorithms and Applications</em> Helsinki University of Technology. Retrieved from <a href="https://www.cs.helsinki.fi/u/ahyvarin/papers/NN00new.pdf" class="uri">https://www.cs.helsinki.fi/u/ahyvarin/papers/NN00new.pdf</a>.</p></li>
<li><p>Jauregui, J. (2012, Aug.) <em>Principal component analysis with linear algebra.</em> Union College. Retrieved from <a href="http://www.math.union.edu/~jaureguj/PCA.pdf" class="uri">http://www.math.union.edu/~jaureguj/PCA.pdf</a>.</p></li>
<li><p>Shlens, J. (2003, Mar.) <em>A tutorial on principal component analysis: derivation, discussion, and singular value decomposition.</em> Princeton University. Retrieved from <a href="https://www.cs.princeton.edu/picasso/mats/PCA-Tutorial-Intuition_jp.pdf" class="uri">https://www.cs.princeton.edu/picasso/mats/PCA-Tutorial-Intuition_jp.pdf</a>.</p></li>
</ol>
