---
title: "The Relationship Between Former Soviet States’ Civil Liberties and Wealth"
date: 2022-05-12T00:04:05-04:00
draft: false
hideLastModified: true
summary: "We set out to determine how civil liberties in former Soviet Socialist
Republics developed either positively or negatively following the
collapse of the Soviet Union and to assess the relation of such
development with each country’s growth in GDP per capita."
summaryImage: "summary.jpg"
tags: ["Data Analytics", "R"]
---

We set out to determine how civil liberties in former Soviet Socialist
Republics developed either positively or negatively following the
collapse of the Soviet Union and to assess the relation of such
development with each country’s growth in GDP per capita.

## Motivation

We were interested in conducting this research because we believe that
the answer to this question could have wide-ranging implications upon
nation-building, constitution-drafting, and general legislative efforts
involving either bolstering or curtailing civil liberties. A conclusion
that liberalization is positively linked to economic growth could
incentivize such liberalization and inform policymakers and economic
stakeholders on how ostensibly political actions could have a financial
effect. This information, derived from countries that were forced to
undergo significant development following their independence, would be
especially useful for other developing nations and those that suffer
from the legacies and residual effects of colonization.

## Initial Hypothesis

Our initial hypothesis is multi-pronged: first, that most former Soviet
states will not have experienced much change in their level of civil
liberties, with authoritarian regimes staying authoritarian and hybrid
regimes staying hybrid; but second, that those countries who have
experienced expansion of civil liberties will have also experienced more
growth in GDP per capita than their peers. We base this hypothesis on
general observation of much of Eastern Europe, which with the notable
exception of the Baltic states has remained rather stagnant in most
surface-level metrics of growth. While before conducting our research we
were not aware of the exact quantifiable level of civil liberties in
each nation, we could broadly determine that there was a gap between
states like Belarus, Hungary, and Poland versus the Baltic states, which
is partially what inspired this particular set of countries as a
research group for our question.

# Methodology

## Dataset Overview

In order to evaluate the change in civil liberties for former USSR
nations, we utilized a dataset provided by the World Bank that contained
121 different indicators from 1975-2019 concerning various aspects of a
nation’s expressive environment. These indicators covered a lot of
ground, ranging from educational equality to censorship efforts to media
corruption. Each indicator was on a scale of 0 to 1, with 1
corresponding to the most progressive nations. For our particular
research question we selected the following 7 indicators which we
believe provide a comprehensive representation of the status of a
nation’s civil liberties:


{{< codeWide >}}
data <- read.csv("data/data.csv")
gdp <- read.csv("data/gdp.csv")
gdp <- gdp[, c(1:2, 5:64)]

pcaindicators <- c(55, 56, 59, 93, 94, 95, 106)
unique(data$Indicator)[pcaindicators]

## [1] "Freedom of discussion for men "     "Freedom of discussion for women "  
## [3] "Freedom of expression "             "Media freedom "                    
## [5] "Media integrity "                   "Media self-censorship "            
## [7] "Print/broadcast censorship effort "
{{< /codeWide >}}

## Data Analytics Approach

In order to properly represent all 7 of the indicators for the different
nations, we utilized Principal Component Analysis to reduce the
dimensionality to two as shown in the two graphs below.

We chose to use PCA because there is significant correlation and common
information between several of these indicators; thus, simply taking the
mean or some other metric would over-represent certain characteristics
and double-count some factors. PCA solves this problem. It takes the
data with interlocking correlations and represents it on a basis where
each dimension presents independent information. Due to the nature of
the selected indicators and the PCA process, the generated
two-dimensional representation was able to include 80% of the total
information; this coverage is a testament to the existing correlation in
the initial 7 indicators. In terms of graphical representation, it is
important to note that in the graph below, greater values for PCA
components 1 and 2 indicate countries that afford more civil liberties
to their citizens.

Having obtained the distilled civil liberty indicators from PCA, we then
ran K-means clustering on the PCA results for all 160 nations. We used
three centers for each year between 1992 (the year following the
collapse of the USSR) and 2019. Note that three clusters was selected
based on the general shape of the distribution of civil liberty
indicators for all countries. We universally define cluster 1 in this
report as the nations that afforded the most civil liberties, cluster 2
being nations that afforded the second most civil liberties, and cluster
3 being the least.

We then tracked the cluster that each nation fell into across every year
in our dataset. We were able to evaluate change over time in the level
of civil liberties countries afforded to their citizens as represented
by nations moving between clusters. As pertains to our specific research
question, note in the figure below that initially in 1992, all of the
former USSR states fell in either cluster 2 or cluster 3, however in
2019 we see that a few managed to progress to cluster 1.

{{< codeWide>}}
set.seed(37186942)
ussr <- c("Estonia", "Latvia", "Lithuania", "Georgia", "Ukraine", "Moldova", "Kyrgyz Republic", "Uzbekistan", "Tajikistan", "Armenia", "Azerbaijan", "Turkmenistan", "Belarus", "Russian Federation", "Kazakhstan")
pcaindicators <- c(55, 56, 59, 93, 94, 95, 106)
begYear <- 1975
endYear <- 2019
years = c(begYear:endYear)
timeclusters <- matrix(NA, length(unique(data$Country.Name)), endYear - begYear + 1)
rownames(timeclusters) <- unique(data$Country.Name)
medianGDP <- matrix(NA, nrow = 3, ncol = length(years))
for (year in years) {
    pcadata <- matrix(data = NA, length(unique(data$Country.Name)), 2 + length(pcaindicators))
    pcadata[, 1] <- unique(data$Country.ISO3)
    pcadata[, 2] <- unique(data$Country.Name)
    for (i in 1:length(pcaindicators)) {
    for(j in 1:nrow(pcadata)) {
        curcountry <- subset(data, Country.Name == pcadata[j, 2])[, c(1:5, year - 1969)]
        colnames(curcountry)[6] <- "curyear"
        curvalue <- curcountry$curyear[curcountry$Indicator == unique(curcountry$Indicator)[pcaindicators[i]]]
        pcadata[j, i + 2] <- ifelse(is.null(curvalue), NA, curvalue)
    }
    }
    colnames(pcadata) <- c("Country.ISO3", "country", unique(data$Indicator)[pcaindicators])
    pcadata <- na.omit(pcadata)
    pcadata <- data.frame(pcadata)
    for(i in 3:ncol(pcadata)){
        pcadata[, i] <- as.numeric(pcadata[, i])
    }
    pcadata$gdp <- rep(NA, nrow(pcadata))
    for(i in 1:nrow(pcadata)) {
    curgdp <- gdp[, year - 1958][gdp$Country.Code == pcadata$Country.ISO3[i]]
    if(length(curgdp > 0)) {
        pcadata$gdp[i] <- curgdp
    }
    }
    pcadata <- na.omit(pcadata)
    data.pca <- prcomp(pcadata[, 3:ncol(pcadata)], center = T, scale = T)
    km <- kmeans(data.pca$x[, 1:2], centers = 3)
    freedomOfSpeech <- c()
    for (i in 1:3) {
    freedomOfSpeech <- c(freedomOfSpeech, median(data.pca$x[, 1][which(km$cluster == i)]))
    }
    freedomOfSpeech <- order(freedomOfSpeech, decreasing = !(year %in% c(1992, 1993)))
    for (i in 1:3) {
    medianGDP[i, year - begYear + 1] <- mean(pcadata$gdp[which(km$cluster == freedomOfSpeech[i])])
    }
    for (i in 1:nrow(timeclusters)) {
    if (length(km$cluster[which(pcadata$country == rownames(timeclusters)[i])]) > 0) {
        clust <- km$cluster[which(pcadata$country == rownames(timeclusters)[i])]
        timeclusters[i, year - begYear + 1] <- which(freedomOfSpeech == clust)
    }
    }

    if(year == 1992){
    cols <- c(4, 2, 3)
    plot(-data.pca$x[which(!(pcadata$country %in% ussr)), 1], -data.pca$x[which(!(pcadata$country %in% ussr)), 2], col = cols[km$cluster[which(!(pcadata$country %in% ussr))]],  cex=1.5, xlab = "PCA component 1", ylab = "PCA component 2", main= "1992 Civil Liberties K-means clusters")
    legend("top", legend = c("Cluster 1", "Cluster 2", "Cluster 3", "Former Soviet States"), pch = c(1, 1, 1, 16), col = c(2, 3, 4, 1))
    points(-data.pca$x[which(pcadata$country %in% ussr), 1], -data.pca$x[which(pcadata$country %in% ussr), 2], col = cols[km$cluster[which(pcadata$country %in% ussr)]], pch = 16, cex=1.5)
    }

    if(year == 2019){
    cols <- c(2, 3, 4)
    plot(data.pca$x[which(!(pcadata$country %in% ussr)), 1], data.pca$x[which(!(pcadata$country %in% ussr)), 2], col = cols[km$cluster[which(!(pcadata$country %in% ussr))]],  cex=1.5, xlab = "PCA component 1", ylab = "PCA component 2", main= "2019 Civil Liberties K-means clusters")
    legend("top", legend = c("Cluster 1", "Cluster 2", "Cluster 3", "Former Soviet States"), pch = c(1, 1, 1, 16), col = c(2, 3, 4, 1))
    points(data.pca$x[which(pcadata$country %in% ussr), 1], data.pca$x[which(pcadata$country %in% ussr), 2], col = cols[km$cluster[which(pcadata$country %in% ussr)]], pch = 16, cex=1.5)
    }
}
{{< /codeWide >}}

{{% twoFigure src1="img/unnamed-chunk-2-1.png" width1="600" src2="img/unnamed-chunk-2-2.png" width2="600" %}}

## Limitations of Methodology

While our approach is extremely effective in creating a more
comprehensive representation of each country’s civil liberties by
utilizing PCA to avoid double-counting redundant information between the
indicators, there are a couple limitations that should be acknowledged.
1. Data was not available for every single country and year, and thus we
had to pick our indicators arbitarily to a certain extent to maximize
the number of countries that had data. However, it should be noted that
our data analytics approach is extremely scalable and given further data
in the future is extremely easy to incorporate more indicators without
much change. 2. The nature of the World Bank data is that intangible
qualities are quantified and standardized. Because they come from the
same source, we operate under the assumption that the quantification and
standardization is the same across indicators, but placing such trust in
the quality of the dataset could ignore potential issues in the
production of the data itself that would alter results.

# Results & Discussion

## Changes in Civil Liberties in FOrmer Soviet Socialist Republics

As discussed in the methodology section, in order to evaluate the shifts
in civil liberties that nations afforded citizens from year to year, we
tracked the cluster for each country in a given year. Interestingly, we
observe in the generated graphs below that since the collapse of the
USSR only 3 of the 15 nations were able to achieve civil liberties that
placed them in cluster 1 consistently: Estonia, Latvia, and Lithuania.
It should be noted that Georgia did briefly appear in cluster 1, however
due to the fact it was only for two years, we did not group Georgia with
the likes of the other three nations. In terms of our hypothesis, the
fact that only 3 nations progressed to cluster 1 supports it as it
indicates that majority of the nations civil liberties afforded remained
constant.

{{< codeWide >}}
par(mfrow = c(2, 2))
for (i in 1:3) {
    plot(1991:2019, timeclusters[which(rownames(timeclusters) == ussr[i]), 17:45],
        type = "l", col = i, ylim = c(1, 3), xlab = "Year", ylab = "Cluster",
        main = ussr[i])
}
plot(1991:2019, timeclusters[which(rownames(timeclusters) == "Georgia"), 17:45],
        type = "l", col = 4, ylim = c(1, 3), xlab = "Year", ylab = "Cluster",
        main = "All other former Soviet states")
for (i in 5:length(ussr)) {
    points(1991:2019, timeclusters[which(rownames(timeclusters) == ussr[i]), 17:45],
        type = "l", col = i, ylim = c(1, 3))
}
{{< /codeWide >}}
{{% figure src="img/unnamed-chunk-3-1.png" width="600" %}}

## Relationship between Wealth and Civil Liberties

We separated the former Soviet states into their 2019 clusters,
reflecting the most recent data as well as the clusters these countries
have spent the most time in. The graphs below shows us that the Baltic
states, which afforded the most freedoms to their citizens, had much
greater GDP per capita growth than other former Soviet states.
Interestingly, many of the Cluster 3 countries, which remained in the
same category as the former USSR in terms of freedom, are doing better
today than the Cluster 2 countries. We think this may be the case
because the countries in Cluster 2 tend to be emerging democracies where
the difficulties of adopting a new system of politics may be making it
harder for countries to grow their economies. While countries that
remained relatively authoritarian did not have these issues getting in
the way of economic progress, their restrictive nature also impeded
growth on the level of the Baltic democracies.

{{< codeWide >}}
rownames(timeclusters) <- unique(data$Country.ISO3)

ussrCodes <- unique(data$Country.ISO3[data$Country.Name == ussr])
plot(1992:2019, gdp[which(gdp$Country.Code == "EST"), 34:61], type = "l",
        col = timeclusters[which(rownames(timeclusters) == "EST"), 45] + 1,
        ylim = c(0, 25000), xlab = "Year", ylab = "GDP per capita (US$)",
        main = "Former Soviet states' GDP per capita over time")
legend("topleft", legend = c("Cluster 1", "Cluster 2", "Cluster 3"),
        col = c(2, 3, 4), lty = 1)

for (i in 2:length(ussr)) {
    lines(1992:2019, gdp[which(gdp$Country.Code == ussrCodes[i]), 34:61], type = "l",
        col = timeclusters[which(rownames(timeclusters) == ussrCodes[i]), 45] + 1)
}
{{< /codeWide >}}
{{% figure src="img/unnamed-chunk-4-1.png" width="600" %}}

{{< codeWide >}}
plot(years, medianGDP[1,], ylim = c(0, 50000), col = 2)
points(years, medianGDP[2,], col = 3)
points(years, medianGDP[3,], col = 4)
{{< /codeWide >}}

{{% figure src="img/unnamed-chunk-5-1.png" width="600" %}}

To take a closer look at whether different levels of civil liberties
truly affected GDP per capita over the period following the breakup of
the Soviet Union, we calculated 95% confidence intervals for the
difference-in-means between clusters for the years 1995 (the earliest
year we had cluster data for all former Soviet states) to 2019. Note
that we used the assumption that the mean GDP per capita in each cluster
is normally distributed even though we had fairly small sample sizes for
each mean, because this assumption gave us the best way to analyze the
presence of visible differences between the clusters. The cluster 1 vs 2
graph and cluster 1 vs 3 graph show us that there has been a conclusive
wealth advantage for the freest countries over those that are less free
in every year since 1995, since none of the confidence intervals for the
difference in mean GDP per capita include 0. Meanwhile, the cluster 2 vs
3 graph demonstrates that we cannot conclusively state that cluster 2
and cluster 3 countries had a difference in mean GDP per capita on
average, since the confidence intervals include zero in the majority of
the years. While some particular countries in cluster 2 may have done
better economically than some in cluster 3 (and vice versa), on average,
the difference is inconclusive.

{{< codeWide >}}
ussrgdp <- subset(gdp, Country.Code %in% ussrCodes)[, c(1, 38:61)]
ussrgdp$cluster <- c(2, 3, 3, 1, 2, 3, 2, 1, 1, 2, 3, 3, 3, 2, 3)
ussrclust1 <- subset(ussrgdp, cluster == 1)
ussrclust2 <- subset(ussrgdp, cluster == 2)
ussrclust3 <- subset(ussrgdp, cluster == 3)

difinmean <- matrix(NA, 2019 - 1995 + 1, 3, dimnames = list(c(1995:2019), c("estimate", "lower", "upper")))
for(i in 1995:2019) {
    difinmean[i - 1994, 1] <- mean(ussrclust1[, i - 1993]) - mean(ussrclust2[, i - 1993])
    difinmean[i - 1994, 2:3] <- mean(ussrclust1[, i - 1993]) - mean(ussrclust2[, i - 1993]) +
    c(-1, 1) * qnorm(0.975) * sqrt(var(ussrclust1[, i - 1993]) / nrow(ussrclust1) + var(ussrclust2[, i - 1993]) / nrow(ussrclust2))
}
difinmean <- as.data.frame(difinmean)
plot(1995:2019, difinmean$estimate, col = 2, pch = 16,
        xlab = "Year", ylab = "Difference in mean GDP ($)", ylim = c(-1000, 20000),
        main = "Cluster 1 vs. Cluster 2")
abline(h = 0)
segments(1995:2019, difinmean$lower, 1995:2019, difinmean$upper, lty = 2)
{{< /codeWide >}}
{{% figure src="img/unnamed-chunk-7-1.png" width="600" %}}

{{< codeWide >}}
for(i in 1995:2019) {
    difinmean[i - 1994, 1] <- mean(ussrclust1[, i - 1993]) - mean(ussrclust3[, i - 1993])
    difinmean[i - 1994, 2:3] <- mean(ussrclust1[, i - 1993]) - mean(ussrclust3[, i - 1993]) +
    c(-1, 1) * qnorm(0.975) * sqrt(var(ussrclust1[, i - 1993]) / nrow(ussrclust1) + var(ussrclust3[, i - 1993]) / nrow(ussrclust3))
}
plot(1995:2019, difinmean$estimate, col = 2, pch = 16,
        xlab = "Year", ylab = "Difference in mean GDP ($)", ylim = c(-1000, 20000),
        main = "Cluster 1 vs. Cluster 3")
abline(h = 0)
segments(1995:2019, difinmean$lower, 1995:2019, difinmean$upper, lty = 2)
{{< /codeWide >}}
{{% figure src="img/unnamed-chunk-8-1.png" width="600" %}}
{{< codeWide >}}
for(i in 1995:2019) {
    difinmean[i - 1994, 1] <- mean(ussrclust3[, i - 1993]) - mean(ussrclust2[, i - 1993])
    difinmean[i - 1994, 2:3] <- mean(ussrclust3[, i - 1993]) - mean(ussrclust2[, i - 1993]) +
    c(-1, 1) * qnorm(0.975) * sqrt(var(ussrclust3[, i - 1993]) / nrow(ussrclust3) + var(ussrclust2[, i - 1993]) / nrow(ussrclust2))
}
plot(1995:2019, difinmean$estimate, col = 2, pch = 16,
        xlab = "Year", ylab = "Difference in mean GDP ($)", ylim = c(-1000, 20000),
        main = "Cluster 3 vs. Cluster 2")
abline(h = 0)
segments(1995:2019, difinmean$lower, 1995:2019, difinmean$upper, lty = 2)
{{< /codeWide >}}
{{% figure src="img/unnamed-chunk-9-1.png" width="600" %}}

# Conclusion

## Hypothesis

We initially hypothesized that most former Soviet states would not
experience significant change in their level of civil liberties after
gaining independence, but that those who did increase their civil
liberties would see corresponding increases in GDP. This hypothesis,
generally, was proved correct. The three Baltic states were the only
nations to reach and sustain membership in cluster 1, and they
demonstrated substantially higher growth in GDP per capita than all
other former Soviet states. However, a factor that we did not predict
was that some cluster 3 countries would do better than cluster 2
countries. As previously mentioned, this may be due to the difficulties
associated with developing democracy and the efficiency benefits of
authoritarianism, but it does present an interesting counterpoint to the
broad hypothesis that better civil liberties means a better economy.

## Validity

Since randomization was not used in this study and there were likely
many confounders that were not adjusted for, there is not much internal
validity. Even though our results show that the countries that afforded
the most freedoms to their citizens were also the richest, and
increasingly so, other factors may be at play. For example, the Baltic
states are geographically closer to stronger economies such as Poland,
Sweden, and Finland, and they joined the European Union, enabling them
to attain greater economic success. Additionally, the countries that
have gone through relative economic stagnation over the past 20 years
have also dealt with issues such as a lack of wealthy neighbors as well
as the presence of natural resources that have increased the possibility
of corruption. However, it is reasonable to suggest that freedom is a
factor that can contribute to higher GDP per capita. The external
validity, meanwhile, is enhanced by the real-world nature of the study
and the fact that the former USSR states represent a significant variety
of countries in terms of their levels of freedom and economic situations
over the past 25 years.

## Potential Confounders

There are a number of potential external factors that could have
confounded our results. We only measured GDP per capita and civil
liberties. However, there are many elements that could have contributed
to the rise in GDP for the Baltic states other than civil liberties.
Some may include EU and NATO membership, Western trade cooperation, and
foreign investment. Similarly, for cluster 3 nations, it is possible
that relationships with Russia, China, and Turkey could contribute to
their achievement over cluster 2. That said, we believe that many of
these factors could be related to civil liberties and regime type. In a
future iteration of this project, we would like to expand the number of
variables included in the PCA process to account for potential economic
factors that exhibit correlation with civil liberties.
