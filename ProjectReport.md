# Project Report

I ran out of time to make a polished report (and im inexperienced):( so i'll just make a summary of all the things i did and the conclusions i reached.

## Task 1

- To make good plots, tried out these 4 pixel scaling methods, and picked the best looking one depending on needs.

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

### Data cleaning

#### Passenger, Name Columns

- Split up passengerId into groupId and groupSize after realising they were travelling in some sort of 'group'
- Checked via code that the last name of the people in same group doesnt have to be same (ie they are not directly related), which meant that this group wasnt always as simple as 'family'
- This meant that 'Name' as a column was obsolete so made the decision to drop it at the end

#### HomePlanet, Destination Columns

- We found out that everyone travelling in same group comes from same HomePlanet
- We used above fact to smartly fill NaN values for people in group
- For the rest of the NaN values, we filled for the mode
- We filled with the mode for Destination as well

#### Cabin Column

- We split up cabin into 3 new columns, Cabin P1, Cabin P2 and Cabin P3
- After some careful analysis, made some conclusions about what each part meant, which i will copy paste from my analysis there:
- - the first letter part of cabin corresponds to some sort of 'block' (from A-G)
- - the second number in the cabin is some sort of cabin number (independent for each block)
- - there are 2 types of cabin rooms P or S (not sure what it means)
- - each group travelling might not be in the same cabin room BUT always has same type of cabin room (P or S)
- We found a weird 'T' block with only 5 people (out of 8k) and the 5 people had absolutely nothing special in their data whatsoever, so the most logical conclusion was that this 'T' block was some sort of corruption arising from the anomaly, so we changed 'T' to the mode 'F'
- Filled the rest of the Cabin P1 values with mode 'F'
- Cabin P2 was more interesting, we cant just fill with any one number, since its some sort of room number. To deal with this, i made a custom function **randomFill()** which was very useful throughout the project
- RandomFill() basically fills based on random values from the column itself (thus these new random values 'fit' the distribution of the values)
- Since each Cabin P1 has its independent set of Cabin P2 values, we first grouped by Cabin P1 and then used RandomFill function on the Cabin P2 values
- Since same group = same Cabin P3 value, we first smartly filled cabin P3 NaN values from groups
- For the rest of the cabin P3 values, we used our newly created randomFill function (it would provide a 50-50 split like we needed as per the distribution)

#### Cryosleep, VIP and spendings columns

- VIP is like the most useless column ever, we just filled it with mode 'False', couldnt find any meaningfull patterns there
- We found good correlations between TotalSpending and Cryosleep, obviously people in cryosleep cant spend
- We dealt with a few cases:
- - When the known spendings are 0, and there are some unknown NaNs (max was 1), and cryosleep is NaN -> make cryosleep True and all spendings 0
- - When all spendings are 0, and cryosleep is NaN, make it True
- - Wherever cryosleep = True, we made sure all spendings values were 0 (replaced NaNs)
- For the remaining cryosleep NaNs we replaced with mode (False)

#### Age and spendings columns

- For this whole section we only considered cases where cryosleep = False, since cryosleep = True wud skew results towards spendings = 0
- We found another rule in our data that no one under the age of 13 has any spendings
- If spendings = 0, and age = NaN, we randomly filled it with a value under 13 (it was statistically likely we checked)
- For the rest of the ages, we just filled it with our randomFill() function
- We randomly filled the rest of the spendings columns based on the distribution with randomFill() (I now realise that we cudve made this more efficient by maybe choosing a different filling algorithm as there are many outliers in our spendings data)
- We make sure everyone under 13 has spendings = 0 (we didnt accidentally fill those datapoints)

#### Final Cleaning steps

- Just dropped columns, added 'TotalSpendings' column n reordered columns to get final cleaned dataset

### Data exploration

- We used cleanData.corr() to see correlations and worked based on that
- There was an extremely high correlation between cryosleep = True and transportation (we visualized this too!)
- We made a bunch of interesting plots you can go see all that, i just wanted to highlight one

![InterestingPlot.jpeg](./Task2/InterestingPlots/4) StackedBarSpendings.jpeg)

- This beautiful plot itself tells us a lot:
- - There is a clear 'class' between cabin blocks, A-C blocks are 'rich', D block is 'mid', and E-G classes are 'poor/affordable'
- - Less total spending = more chances of transportation
- - Maybe Cabin D was especially dirty since they needed room service a lot more than the other cabins disproportionately
- - Spending money in food court or in shopping mall increased your chances of transportation (which is very interesting)

- Our final explanation we came up with after all data exploration was one of an anomaly that struck the ship mainly in certain locations

Here are my final anecdotes from data exploration copied onto here:

Data cleaning gives us _clean data_. Data exploration tells us a **story**.

This is the story that our data exploration has told us.

- The Cryo Chamber of the passenger ship that hosted all the cryo pods was the main location that the anomaly affected, causing a lot of the people in the cryopods to get transported.
- The housing in the passenger ship was divided into 7 blocks, A-G, and they are divided into the rich cabin category: A, B, C, the mid cabin: D, and the poor/affordable cabin category: E, F, G. There are lots more E, F, G cabins compared to A, B, C since A-C are more premium cabins.
- Cabin D rooms are especially dirty for some reason. A lot of people from cabin category E-F-G have almost no spendings.
- Most people that spend money in the passenger ship spend it in the food court (that may be attached to the shopping mall). The cryo chamber must be very close to the food court/shopping mall since a lot of people that spend a lot of money in the food court/shopping mall were transported, so the anomaly mustve affected them too.

- This overall tells us that the anomaly affected the ship location-based. The anomaly affected the ship somewhere near the cryo chambers, the food court, and the shopping mall. This anomaly was somewhat far from the spa, VRDeck.
- Cabin Blocks B and C were close to the anomaly (maybe near the food court/shopping mall, which is also why they spent so much money compared to block A) and had higher chance of getting transported.
- Meanwhile cabin blocks D, E and F were perhaps a bit further from the anomaly.

## Bonus Task

I will keep this very brief

- Used our entire data cleaning from task 2 to make one cleaning function that only takes 'distributions' from the final cleaned dataset (to avoid data leakage)
- Cleaned train and test using this cleaning function

- Dropped a few unnecessary columns, and used OneHotEncoder to convert our categorical data into numerical matrices that the machine can understand
- Also split our data into xtrain (everything else) and ytrain (transported (that we have to predict)) and stuff

The different classifiers we will try out to get the best model is

1. Logisitic Regression
2. SVC (Support Vector Classifier)
3. DecisionTreeClassifier
4. RandomForestClassifier
5. GradientBoostingClassifier
6. KNeighborsClassifier
7. GaussianNB (Naive Bayes) Classifier
8. LinearSVC

- Performed exhaustive GridSearchCV on all these classifiers using different parameters and even scaling models with a cv value of 10 to find the best average f1 score that these models can individually come up with

- Then we selected the best performing out of these models (SVC) and used it to actually create a prediction
- Fit our data into SVC with best performing parameters and made finalOutput array which takes prediction of SVC and creates finalOutput in the format that we want

And thats it!

This was my whole OlympusTech3.0 journey!

This was an amazing experience, one in which i learnt so much about different libraries in python, and also about how astronomical data works. From plotting really beautiful fits files, to trying to visualize our data in the best way, to trying to see patterns in the data that we can make conclusions out of, I really enjoyed every moment that i spent on this project and truly learnt a lot.

Thanks,
Vasudev Sajeev (AstroGoodBoy)
