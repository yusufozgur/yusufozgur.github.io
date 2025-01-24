---
layout: post
title: How to perform timescale analysis using COPASI.
---

Kinetical models of biological systems can have different reactions occuring at different timescales. For example, an isomerization reaction will be happening faster than the gene expression. You may consider them together in your model or you can also consider them seperately, assuming slow reactions are constant or approximating the effects of fast reactions. In this post, we will see example models where we can see reactions at different timescales in effect. We will see how to approximate reactions in fast timescales using rapid equilibrium assumption. We will also see how to use COPASI to build and inspect our model for these purposes.

Assume the following model:

$$
\xrightarrow{k_1=0.1}
A
\xleftrightarrow{k_2=1 \ k_{-2}=1}
B
\xrightarrow{k_4=0.1}
$$

$$B \xleftrightarrow{k_3=10\ k_{-3}=12}C$$


where, reactions are modelled with mass action, rate constants \\(k_{positive}\\) are for forward reactions and \\(k_{negative}\\) are for reverse reactions. Assume all starting concentrations of our species is 1 mM/L.

The time course results in copasi will be like:
![image of the time course]({{ site.baseurl }}/images/time_course.png)
You can see there are fast changes in concentrations initially, then there is a slow relaxation into a steady state. Although we have designed our reactions to have three different timescales, distingishing between the two timesclaes is not posible from this plot. We will perform time scale seperation analysis later to determine the different time scales in our model.

The phase plot for A vs B will be: 
![alt text]({{ site.baseurl }}/images/phase_plot_subset.png)
As a small explanation, this phase plot was created by plotting transient concentrations of A vs B.

But this is not representative of the whole phase space, to do that, we can try a bunch of random A,B,C values and plot the phase plot again.
![alt text]({{ site.baseurl }}/images/phase_plot_all.png)
Here, we can see the slow manifold in the middle, ranging the phase plot at a 45 degree angle. There are a bunch of branches reaching the slow manifold from the initial concentrations. We can also see the small bends at the start of the branches, I would speculate this bends are due to the reactions at the fastest time scale, namely due to \\(B \leftrightarrow C\\). But this is merely a projection of our phase space to A vs B after all, and we will later inspect if what we think is correct.

So, we can plot the phase space but still we have limited knowledge about our timescales. Hence, we will run a "Time Scale Seperation Analysis" with CSP method in copasi.

![alt text]({{ site.baseurl }}/images/time_0.png)
At t=0, copasi will label all timescales as 0, this means that at this timepoint, all the reactions matter and you cannot remove them for model simplification if you care about dynamics at this timepoint.

Now, lets move our time slider:
![alt text]({{ site.baseurl }}/images/time_0_5.png)
At t=0.5, we see that one out of the three timescales is labeleld as "Fast", means that this timescale is exhausted, and has minimal impact on the dynamics. We can also see which reactions contribute the most to this timescale by inspecting the absolute values in this window.

![alt text]({{ site.baseurl }}/images/time_4_7.png)
At t=4.7, second timescale is labelled as fast as well. This timescale is mostly influenced by \\(A\leftrightarrow B\\) The other timescale remains labelled slow, and it is mostly contributed by influx and outflux. This is most likely the slow manifold.

For more info regarding interpretation of CSP results, you can refer to https://doi.org/10.1186/1752-0509-6-14.

Our timescale seperation analysis shows there are three timescales, one exhausted after 0.5 seconds, one after 4.7 seconds, and another forming the slow manifold.

I would like to end this post by showing a visual representation of these timescales, for that, I have colored the phase space at different colors for our different timescales.

![alt text]({{ site.baseurl }}/images/phase_plot_colored.png)
Here, we can see all three of our timescales in action. We have speculated the bends at the end signals the C to B equilibrium, that seems to be partly true. Overall, we can observe the branches in the phase space start being dominated by \\(C \leftrightarrow B\\), these regions colored green, then in red colored regions \\(A\leftrightarrow B\\). Then, we see the slow manifold slowly moving to the middle of the plot where the system reaches steady state at B = 1 mM/L and A = 1 mM/L.