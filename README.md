## WeGo Revisited

### Part 1: Modeling Lateness

Recall that a bus would be considered late if it is more than 6 minutes late (based on the adherence value). A bus is early if it is more than one minute early based on adherence.

Start by doing some simple overall aggregates. What percentage of the total stops are on time? How does this differ based on route? day of week? time of day?

Now, consider the operators. Which operators have the highest and lowest on-time percentages?

It's not really fair to compare operators with different number of observations with each other without taking into account the uncertainty due to small sample sizes. We could instead build a confidence interval around out estimates. Use the `prop.test` function to generate a confidence interval for the on-time percentage for each operator. Once you've done this, if we want to identify the best and worst operators in terms of on-time percentage you could look at those that have the highest lower bound and lowest upper bound, respectively.

Now, let's take a modeling approach. Fit a multilevel logistic regression model with random effects for driver, where the target is whether a stop is on-time. Compare the operator-level random effects to what you computed using the confidence interval approach. WARNING: Make sure that OPERATOR is being treated as a factor. 

We still haven't considered the fact that the route is likely going to influence the likelihood of being late. Fit a logistic regression model with a random term corresponding to the OPERATOR which account for the impact of route (and if you'd like, any other variables you want to control for) by including route as a fixed effect. WARNING: Make sure that you have converted OPERATOR and ROUTE_ABBR to factors before fitting this model.

Up to this point, we haven't considered the fact that observations within the same trip are likely going to be correlated. That is, if a driver is late at the first stop of a trip, they are probably going to be more likely to be late for other stops on the same route. As a result, our effective sample sizes are actually smaller for each operator. Determine for each operator the percentage of trips for which they are on-time, where on-time means that they were on time for all stops on that trip (or however you want to define an on-time trip). Does this change your evaluation of who the best and worst drivers are?

Try fitting a logistic regression model where for predicting whether a trip will be on time.

Another approach would be to go back to the full dataset (not aggregated by trip) and fit a model with trips nested within operators.

### Part 2: Modeling Variance

When it comes to headway deviation, we have seen that it tends to be close to zero on average. The more interesting aspect of headway deviation is the variance (or standard deviation).

Start by finding the variance of headway deviation overall, by route, by day of week or time of day, or by operator. 

If we want to model variance, we could make use of the fact that if a variable follows a normal distribution with zero mean, then the square of that variable will follow a [gamma distribution](https://en.wikipedia.org/wiki/Gamma_distribution). So, if we want to estimate the variance of such a variable, we can fit a generalized linear model to the square of that variable using the gamma family. Note that we have to use the [identity link function](https://en.wikipedia.org/wiki/Generalized_linear_model#Link_function), which is specified by using the argument `family = Gamma(link = "identity")` in the `glm` function.

Try it out to convince yourself that it works: simulate a normal variable with some variance value by using the `rnorm` function and then fit a glm to it (using just a constant for the predictors).

To estimate the impact of predictors on the variance of the target, you can first fit a linear model for the original target and then fit a gamma glm for the square of the residuals (recall that the residuals are assumed to follow a normal distribution with 0 mean). Try this on the headway deviation values. How much influence does route, day or week, time of day, or operator have on the variance of headway deviation? 
