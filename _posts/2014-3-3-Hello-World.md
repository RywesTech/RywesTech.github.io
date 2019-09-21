---
layout: post
title: Roller Coaster Project with the Pandas Framework
---
As a first step in my Machine Learning class this semester, we were tasked with parsing large amounts of data regarding the world's top rollercoasters. To start out, the data regarding the rollercoasters was contained within a CSV file with hundreds of rows. My team and I cleaned the data to remove problemetic columns and rows using built-in pandas functions. I was absolutly blown away by how efficient it was! I'm used to itterating through each row of data and processing it individually. We then used the `.max()` command to find some interesting data. In particular, we found the the rollercoaster with the most number of inversions in the world is 14. 
We then plotted the data, and use NumPy `polyfit` function to create a line of best fit. Finally, we created our own rating system that takes into account user preference. 4 boolean values track wether the use likes height, speed, time length, and inversions. Depending on the user's preference, the rating system will change accordingly.
