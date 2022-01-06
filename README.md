# Big-Data-Bowl-2021-22

On the Fly: What Goes into Punt Return Decisions? 

Abstract:
	The purpose of this analysis is to create a logistic regression model that accurately predicts the result of a punt, specifically whether it is caught and returned or not. In the context of this project, I am focusing on the initial decision of the punt returner, essentially whether the returner decided to catch and return the punt (classified as a return) or if the punt is downed, fair caught, or bounced (classified as a non-return). Punts that bounce and are subsequently returned off the ground are considered as non-returns in this project, as I am focusing on the initial decision of the returner to catch and return the punt or not. Out of the four models I created, the logistic regression model with 18 predictors performed the best; the process of 5-fold cross validation led to an accuracy of 0.841 in predicting a test set of punt plays.

Research Question:
What variables best predict the catch and return frequency of a punt returner on a given play?

Data Manipulation / Variable Creations:
	The initial dataset contains 19979 total special teams plays, which I created by joining play and game data. Since I am only focused on punts, I filtered the data to get 5991 punt plays. Once I had this subset, I joined the play and game data for the punts with the tracking data and the PFF data across the 2018-2020 seasons in order to consolidate as much information as possible. Then, after further reducing the number of plays by omitting those with penalties, touchbacks, balls out of bounds, muffs, blocked punts, improper play event labels, and no returner identified, the number of punt plays decreased to 3370. 
	Next, I created a variable called "Response" that would say "yes" for punts that were caught and returned and "no" otherwise; this is variable that I would go on to predict with my model. As for new predictors, I manufactured several of them to help predict the response, some binary and some numerical. The reduced set of plays contains 1729 returns and 1641 non-returns, so about 51.3% of these plays are returned. Once I knew which plays I wanted to study, I chose one in particular to examine its structure, which was a 2019 Deonte Harris return against the Seahawks. This way, I could benefit two ways. For one, I could get a better understanding of the structure of the data for an individual play, and I could compare the data to a visual representation of the play on Youtube. After studying this play, I was able to come up with a "grand" function that would take in the arguments of gameId, playId, and a dataset of plays (these arguments represent a single play's data), and would subsequently return a list of 26 variables for this single play that would be turned into a row of a data frame of predictors (and the response variable). This resulting data frame has 3370 observations, one for each play; this is the data frame I would go on to analyze.
EDA:
	To get a better sense of how the return result related to some other variables, I used the ggplot2 library to create some visuals. My first idea was to examine how return results vary across quarters of a game, so I made a stacked bar plot of these results with the different colors representing the different quarters. As seen below (Figure 1), the distribution of quarters within each return result is fairly even, as there is no quarter that stands out in either category. Even more, by looking at the frequencies of quarters 1 through 4 in each return result, all quarters range from 20% to 28% within each return category, so it's clear no individual quarter dominates either return category. Moving on, I was curious about the relationship between return result and kick type (with the main two types being normal and Aussie-style), so I decided to make a stacked bar plot representing this idea. The visual below (Figure 2) demonstrates there is a clear relationship between return result and kick type, as it seems that normal punts are returned at a much higher rate than Aussie-style punts. Even more, when looking at the proportions of the kick types in each return category, I found that of the 897 Aussie-style punts I studied, only 224 were returned, good for a 25% clip. However, for the normal punts, 1505 out of the 2472 observations led to a return, which is a return rate just above 60%. So, even though the bar plot may be a tad misleading due to there being a lot more normal punts than Aussie-style punts, the fact remains that each type of punt is returned at significantly different rates. 
	As for my third visual (Figure 3), I created a scatterplot with score differential on the y-axis, the line of scrimmage on the x-axis, and the colors of the dots as the return result. I found that returners are less likely to return the ball if the punt is from closer to midfield. However, punt returners become more aggressive with their returns if the punter is closer to the end zone. As for the impact of score differential on the return result, there is no abundantly clear relationship present according to the visual. 

	


	The final visual I wanted to present for my exploratory data analysis is a correlation matrix of the numerical predictors. While most variables seem to have very little correlation, some variables do have significant correlations that need to be addressed. This visual tells me that not all of these variables can be used in the model, so some reduction is in order.
  
  
Model Comparison and ANOVA Table:
	First, I started with a full logistic regression model, where I used 22 predictors out of the 25 in the predictors data set; I took out gameId, playId, and returner Id because these were simply identifiers, not necessarily meaningful predictors, especially with a small subset of the original amount of plays. I split the data into train and test sets, with 66.6% of the data in the train set and 33.3% of the data in the test set. I chose this ratio because I had an intermediate-sized data set to work with, and this is one of the most common ratios to use in splitting data. After this, I set up the cross-validation process with 5 folds (a good k value empirically) using the train and test splits, and this method yielded a respectable accuracy of 0.838 in predicting the response variable across 1123 test observations. The corresponding table (Figure 5) is shown below. 

	Due to the high complexity of the model, I knew I had to reduce the predictors to help with predicting test data. So, for my next model, I omitted 4 predictors to get an 18-predictor model; the 5-fold cross-validation gave an even better accuracy of 0.841 across the test observations. The table below (Figure 6) shows the model's results. This is an encouraging result because not only did I improve accuracy, but I decreased model complexity at the same time.


	The next step is to try lasso regression with the variables from the reduced model described by Figure 6. After converting the training data into a matrix form and the response variable to a numerical variable, I set out to find the optimal lambda for this lasso regression. The plot below (Figure 7) shows the log lambdas plotted against mean squared error (MSE), and I identified the log lambda represented by the 2nd vertical line as an optimal choice for model simplicity and accuracy (known as lambda.1se). Using this lambda, the lasso regression yielded an accuracy of 0.839, lower than the reduced model. 
	Finally, I tried ridge regression with the variables from the reduced model as well. I went about the same process in identifying the optimal lambda, and in the visual below (Figure 8), I settled on the value represented by the 2nd vertical line (ridge.1se) for optimizing accuracy and simplicity. By incorporating this lambda into the ridge regression, the model returned an accuracy of 0.833, which was the worst accuracy of all. Regardless, all of these models performed very similarly, so it was still a respectable model.




Model Takeaways:
	All of the models had accuracies of at least 0.83, which is an exciting result, with the reduced model having the best accuracy at 0.841. In addition, all of these models had many of the same predictors be significant. Namely, the median speed of the coverage team at the time the ball reaches the returner (Med_cspeed), the closest defender's distance to the returner at the time of return (Closest_cdist), the returner's acceleration at the time the ball gets to him (Ret_acc), the returner's total distance traveled up until the ball gets to him (ret_dist_trav), and the median acceleration of the coverage team at the time the ball reaches the returner (Med_cacc). In terms of the relationships of these predictors with the response, below is the model summary of the reduced model for these five variables. According to the slopes, all of these variables have a positive relationship with the response except for ret_dist_trav, which implies that the less a returner moves before the punt, the more likely a return will happen. Med_cspeed has the strongest positive relationship with the response, as the coverage team will maintain speed through the return in order to stop the punt returner. 



Shortcomings / Recommendations:
	One big issue I had during this project was the long runtimes I had to deal with in R, especially since the data provided was so large. In future projects, using faster processors from C or other resources could be helpful. Also, I had a hard time discerning which plays to use because not all plays had the requisite events or returner identifications, which made filtering difficult.
	In the future, I would like to study how punt return frequencies change over the next few seasons. One thing to consider besides potential rule changes is that more teams will go for it to avoid punting; perhaps punt returners become more aggressive with fewer opportunities to return punts. It's only a matter of time until we find out.

Sources:
1. Deonte Harris Image:  (Otto Greule Jr / Getty Images)

2. Deonte Harris Video: Youtube (https://www.youtube.com/watch?v=-h34qUml52c)

3. NFL and PFF Datasets
4. Wisdom of my mentor


