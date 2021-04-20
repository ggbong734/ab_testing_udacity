# Udacity A/B Testing [Final Project](https://classroom.udacity.com/courses/ud257/lessons/4126079196/concepts/42072285530923)
_completed on April 19, 2021_ 

In this project, we establish an experiment idea into a fully defined design, analyze the results, and propose a high-level follow-on experiment.

## Experiment Overview: Free Trial Screener

In the experiment, Udacity tested a change where if the student clicked "start free trial" in the course overview page, they were asked how much time they can devote to the course (a pop up questionnaire appears). If students say they can allocate more than 5 hours, then it would be process as usual. If less than 5 hours, then Udacity will suggest the student take the free course audit instead. See [project description](https://classroom.udacity.com/courses/ud257/lessons/4126079196/concepts/42072285530923)

Students will have the option to continue enrolling in free trial or access course material for free. 

The unit of diversion is a cookie. If the student enrolls in the free trial, they are tracked by user-id from that point forward.

**Hypothesis: The new question will reduce number of students who left the free trial (didn't have enough time). If this is true, this helps free up the coaches and improve student experience.**

## Metric Choice 

Questions: Single or multiple metrics? Evaluation or invariant metric?

**Number of cookies**: That is, number of unique cookies to view the course overview page. (dmin=3000)
This serves as the invariant metric to ensure the traffic for control is same as treatment. 

**Number of user-ids**: That is, number of users who enroll in the free trial. (dmin=50)
This can serve as one of the evaluation metrics, the number of users is expected to decrease as more students select free course due to the time commitment warning.  

**Number of clicks**: That is, number of unique cookies to click the "Start free trial" button (which happens before the free trial screener is trigger). (dmin=240) 
This is an invariant metric as there is no change until the "Start free trial" button is clicked. 

**Click-through-probability**: That is, number of unique cookies to click the "Start free trial" button divided by number of unique cookies to view the course overview page. (dmin=0.01)
This is also an invariant metric as there should be no change in the number of unique cookies visiting the page and the number of unique cookies that click the "Start free trial" button.

**Gross conversion**: That is, number of user-ids to complete checkout and enroll in the free trial divided by number of unique cookies to click the "Start free trial" button. (dmin= 0.01)
This can be an evaluation metric, though I am not entirely sure which direction the metric will change. The denominator or the number of unique cookies to click the "Start free trial" button will decrease but the numerator will also decrease.

**Retention**: That is, number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by number of user-ids to complete checkout. (dmin=0.01)
This can be an evaluation metric, the retention rate should increase as users who select free trial are aware of the time commitment. The denominator will decrease while numerator increase. 

**Net conversion**: That is, number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by the number of unique cookies to click the "Start free trial" button. (dmin= 0.0075)
This can also be an evaluation metric, the net conversion should increase as the numerator increases while denominator remains the same. 

The **invariant metrics are: number of cookies, number of clicks, click through probability**
The **evaluation metrics are: **number of user ids (down), gross conversion (?), retention (up), net conversion (up)**

## Measuring Variability

Using this spreadsheet of [data](https://docs.google.com/spreadsheets/d/1MYNUtC47Pg8hdoCjOXaHqF-thheGpUshrFA21BAJnNc/edit#gid=0) provided by Udacity, we can calculate the empirical standard deviation for each evaluation metric:
- number of user ids (use empirical estimate)
- gross conversion (stdev = 0.0057)
- retention (stdev = 0.0071)
- net conversion (stdev = 0.0044)

We can assume that gross conversion, retention, and net conversion follow the binomial distribution since it is based on probability and the standard deviation is calculated as sqrt(p(1-p)/N). The analytical estimate of standard error for gross conversion and net conversion should be similar to the empirical estimate since the unit of diversion (cookie) is the same as the unit of evaluation (cookie with click). However, the analytical estimate of standard error for retention will be an underestimate of the empirical estimate since the unit of diversion (cookie) is one level lower than the unit of evaluation (user id who enrolled). It's still good to check the empirical estimate to ensure we are on the right track. 

The number of user ids (enrollments per day) may be modeled with the Poisson distribution with difficult to calculate analytical estimate of standard deviation, thus it might be better to also calculate the empirical (observed) standard deviation of this metric.   

## Sizing

I will use the Bonferroni correction because the evaluation metrics are expected to be correlated. For example net conversion and user id would be correlated. Retentio nand net conversion are likely to be correlated as the . Thus in order to get the overall alpha value of 0.05, the individual metric alpha should be 0.05/4=0.0125

Selecting alpha = 0.0125 (significance level = 98.75%) and beta = 0.2 (sensitivity = 80%), we can determine the number of samples (pageviews) we would need for the experiment using the calculator on [Evan Miller's website](https://www.evanmiller.org/ab-testing/sample-size.html) 

Based on practical significance level and the baseline probability for each of the evaluation metric, we can calculate the minimum number of samples for each metric:
- gross conversion = 38,405
- retention = 58,199
- net conversion = 40,721

**We pick the highest number needed among the metrics or 58,199 as our number of samples.**

For the first iteration, we can choose a conservative percentage of the traffic to the website to minimize risk in case the treatment effect is detrimental to conversion rate. As a start 15% might be a good starting point. We can run more iteration with higher fraction of traffic diverted to the experiment in subsequent ramp-up experiments to generalize the finding to the broader population. Since the daily number of pageviews is 40,000, 10% gives 4,000 per day and running the experiment for 14 days will five us 64,000 samples which is sufficient. 

**The duration of the experiment can be set to two - three weeks to account for variation between weekdays and weekends** and we should be careful to select a timeframe that is not during the holiday season or the start of the year as people might have more free time to enroll in classes and have new resolutions to complete which could bias the results. 

## Sanity Checks

We can calculate the observed from the experimental [data](https://docs.google.com/spreadsheets/d/1Mu5u9GrybDdska-ljPXyBjTpdZIUev_6i7t4LRDfXM8/edit#gid=0) 

The invariants are number of cookies, number of clicks, click through probability, 

For number of cookies and number of clicks, we expect that the ratio of control /(control + treatment) = 0.5
We can calculate the standard error associated with the mean by using the formula SE = sqrt(p * (1-p)/(N_cont+N_treat))
Then we can calculate the margin of error by the formula SE * 1.96 by assuming normal approximation since sample size is large. Then we compare if the observed ratio is within the confidence interval of the expected value

For click through probability, we calculate the pooled probability of success and pooled standard error with the formula shown in lesson 1, exercise 19. Then the margin of error can be computed and we can check if the observed difference in proportion between control and experiment are within the margin of error assuming that the expected value is supposed to be 0 (p_cont - p_treat) (i.e. the number of clicks per pageview is the same for control and treatment since the effect is not until the user click the "Free Trial" button)

For **all three invariant metrics, we find that the observed value is within the margin of error of the expected value thus they all passed the sanity check**.

## Analysis 

### Effect size test

An effect size test is performed for each evaluation metric by finding the 95% confidence interval around the difference between the control and experiment groups. If the interval crosses zero than the result for that metric is not statistically significant. We then compare the mean observed difference with the practical significance level (d_min) to see which metrics have sufficiently large change that makes business sense. 

Here are the [Results](https://docs.google.com/spreadsheets/d/1WwGc7lxj_TPtoTfOjhDj6D4xF1B8qinrv3Q9HyGEMJ0/edit#gid=1077804739)

**All metrics are statistically significant (none of the confidence interval crosses zero) but only retention rate shows change above the practical significance level**. This implies that the experiment group only has a net positive impact on retention rate but could possibly have detrimental effect on gross conversion and net conversion (the conclusion for net conversion could be neutral as the mean difference is close to zero).

### Sign test

A sign test is performed using the day-by-day data for each of the evaluation metrics. We first checked if the metric had an increase or a decrease for each day out of the 23 days total duration. Then we count the number of days with increases. We then use the [online calculator](https://www.graphpad.com/quickcalcs/binomial1.cfm) to calculate the p-value assuming that the control and experiment treatments are the same (null hypothesis) which means probability to get an increase is 50%. Here are the results: 
- user id: p-value = 0.0026
- gross conversion: p-value = 0.026
- retention: p-value = 0.6776
- net conversion: p-value = 0.6776

Based on the p-value, **only user-id and gross conversion has p-values less than the alpha of 0.05 which means that the observed reduction in metric per day is statistically significant and is likely not due to chance. On the other hand, retention and net conversion has a high p-value which means that the increase or decrease per day is not statistically significant and likely due to chance.**

### Summary

The Bonferroni correction was used in the above analysis when determining the statistical significance of each metric because the metrics are correlated and we need to counteract the problem of multiple comparisons. 

The effect size analysis shows that **only retention rate has a practically significant and statistically significant increase**. On the other hand, **gross conversion and net conversion shows statistically significant decrease which are not favorable**. 

## Recommendation

Because of this, my recommendation is **not to launch the treatment on the website as the results may be counterproductive**. Even though the retention increased as expected, it seems that the reduction in the number of users that completed checkout was quite large and counteracted the positive increase in retention. The result of the large decrease in user ids that checked out is evident in the decrease of gross conversion and net conversion rate. I would dig deeper to understand the tradeoff between user id decrease and the increase in retention rate and if the tradeoff is favorable and what other knobs can be turned to lower the user id decrease but still raise retention rate.

In the future, as a follow-up experiment, I would try other prompts to kindly let the user know about the time commitment needed, perhaps not immediately when they click the free trial but during the checkout step prior to enrolling. The unit of diversion in this test would also be cookies/user-ids once enrolled to make sure that the user experience is consistent between users. The hypothesis would be that delaying the time commitment warning might have a less intimidating effect on the user (perhaps we can also ask questions about whether they can spare 3 hours on Sat or Sun) . The metrics measured would be similar. The unit of diversion would be also be the pageviews per unique cookies.
