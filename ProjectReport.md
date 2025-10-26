# Project Report

I ran out of time to make a polished report :( so i'll just make a summary of all the things i did and the conclusions i reached.

## Task 1

- To make good plot, tried out these 4 pixel scaling methods, and picked the best looking one depending on needs.

* - Min-Max Scaling (Normalization)
* - Z-Score Scaling (Standardization)
* - Square Root Scaling
* - Log Scaling

- After that applied ZScaleInterval concept to create best visually appealing image.
- Carefully chose a good color map for the image
- Presented final output, and also created a "compare to original" pic to see the effect of our methods
- Used header information to locate the astronomical phenomenon and describe it

- Made the process really easy and streamlined by creating custom functions that do exactly what I need in my workflow, for example:

* - CompareScalings() automatically compares all scalings
* - Compare2Scalings() if i ever wanted to compare 2 specific scalings
* - CompareCmap() to compare a couple of color maps
* - Output() simple function to get the output i wanted
* - Compare2Original() function to get the comparision plot i wanted

- Sometimes needed to consider more extreme scalings than log scaling, which i dealt with
- Also in the last 2 questions, because of excess background noise, the final image was looking messy and pixelated, so manually changed vmin value to remove the noise

## Task 2

- This is very hard to summarize but ill try my best, so my main approach was columnwise cleaning and analysis

- First i started with PassengerId and 'Name':
- - Split up passengerId into groupId and groupSize after realising they were travelling in some sort of 'group'
- - Checked via code that the last name of the people in same group doesnt have to be same (ie they are not directly related), which meant that this group wasnt always as simple as 'family'
- - This meant that 'Name' as a column was
