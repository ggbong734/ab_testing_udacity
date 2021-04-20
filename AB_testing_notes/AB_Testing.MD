
# Lesson 1: Overview of A/B Testing

## A/B Testing
- Pick a metric, Review statistics, Design, Analyze
- Validate results using focus groups, surveys, human evaluation, user experience research
- A/B Testing tells us how to climb the mountain we are on, other techniques above tells us which mountain is the right one

## Choosing metric
- click through rate: clicks/pageviews
- click through probability: unique visitors to click/unique visitors to page

## Binomial Distribution
- Online experiments often have binary results (yes or no, user click or no click)
- Each event can be approximated as a Bernoulli trial, the distribution of the number of successes can be shown as a binomial distribution (y axis = fraction, x axis = number of trials). It looks like a bell curve and the center is the average number of successes from the experiment. 
- ASSUMES 1) two types of outcomes, 2) each event is independent, 3) success probability is the same for each event
- Can assume that the shape of distribution is normal (normal approximation) if Np > 5 and N(1-p) > 5
- Assuming normal, we can then calculate the margin of error using a formula m = z * SE, where z is z-score that depends on the confidence % level.

## Hypothesis test
- Null hypothesis = no difference between the probability of "success" in treatment and control groups (p_treat - p_control = 0)
- Calculate probability of (p_treat - p_control) assuming that the null hypothesis is true, or the p-value.
- if this probability is less than alpha (usually 0.05), then we can reject the null hypothesis 
- alpha is also known as the false positive rate OR the chance of rejecting the null hypothesis given that the null hypothesis is correct
- also possible to do a one-tailed test rather than two-tailed test, 
- one tailed-test only considers change in one direction and ignores change in the other direction but is more sensitive (has more power) at the same significance level (alpha)

## Confidence Interval
- To check if our result is statistically significant, we can calculate d = p_treat - p_control and if d > 1.96xSE_pool or d < -1.96xSE, then it is statistically significant 

## Statistical Power
- Want enough power to conclude with high probability that the result is statistically significant
- Power has an inverse trade off with size, the increased confidence or smaller the change to detect, the bigger sample size is required. 
- Use this calculator to calculate how many samples are needed: https://www.evanmiller.org/ab-testing/sample-size.html
- Higher confidence level (1- alpha), more sample needed
- Higher sensitivity (1- beta), more sample needed 

## Practical significance level vs. Substantive significance 
- Practical significance = from a business perspective at what change does it matter?
- Statistical significance = want to size experiment such that the statistical significance bar is lower than the practical significance bar

## Decision
- In some cases, where the change is not clear, let decision maker know the results are uncertain, decision makers will have to decide based on other factors such as business factors 

# Lesson 2: Policy and Ethics for Experiments 
- Risk, Benefits (what benefits result from the study), Alternatives (what alternatives do users have), Data Sensitivity (what data is being collected, what is expectation on privacy and confidentiality)

## Policy and Ethical Consideration
- important consideration for data collection are identifiability, privacy, and confidentiality / security
- Four questions need to be asked:
	- Are participants facing more than minimal risk?
	- Do participants understand what data is being gathered?
	- Is that data identifiable?
	- How is the data handled?

## Consent
- Are users getting informed? 
- What user identifiers are tied to the data?
- What type of data is being collected?
- What is the level of confidentiality and security?

## Getting approval
- Internet studies may need to get approval from IRB (independent review board) especially if it has higher risk than normal to the users

# Lesson 3: Choosing and Characterizing Metrics
- Define, Build intuition, Characterize

## High Level concept of metric
- Need to define how to use the metric: invariant metric and evaluation metric (goal (overarching company mission) and driver (more short term) metrics)
- High level business metric: revenue, market share, number of users
- User experience metric: how long they stay on a page, latency	
- If there are multiple metrics to use, consider using composite metric or OEC (Overall Evaluation Criterion)
- Have to get every team to agree on the metric

## Build intuition of Metric
- Think about user experience in terms of customer funnel (visit homepage, check out course, etc)
- If some metrics are too difficult or take too long to measure. Then consider using a proxy metric, or something that correlates well with the goal metric. For example, user return rate/happiness may be hard to measure, but may correlate with latency
- May want to build intuition using methods that are non-scalable to check if metric is right, methods include user survey, UX research. Then once we know which metric(s) are good, we can use more scalable methods such as click measurements. 
- We can use external data to validate our observation of the change in the metric (UX research, survey, focus groups, data from peer companies)
- Run restrospective analysis to review past data to validate that the metric will chan

## Define metric
- Choose which data to represent metric
- what will be numerator and denominator 
- Use average or median
- a good analyst knows what change in the data your system can or can't produce (say 10% change in a metric is impossible and can only be due to bugs)
- Filter traffic to look at the subset that matters for the metric (debias data to reduce dilution). E.g. look at year over year change or separate data by platform

## Robustness and Sensitivity of Metric
- Sensitivity: metric will move to the changes we want to see (retrospective analysis, external data, pre-experiment)
- Robust: metric won't change to the changes we don't care about 
- Run A/A test to check if the metric would change between groups, we expect no change (check if metric is too sensitive)

## Variability of Metric
- can calculate variability of metric empirically (based on observation) or analytically (assuming metric has normal distribution)
- mean often have formula for analytical variability while ratio and median do not (have to rely on )
- check Udacity lesson for formula for analytical variability
- or use nonparametric method to estimate variability (does not need to make assumption about the distribution of metric)
- an example is sign test, run binomial proportion test on whether data move up or down to check if difference in probability of control and treatment going up/down are significant  

## Empirical variability
- for more complicated metrics, distribution can be metric, so we may want to use empirical metrics
- analytical estimate usually end up underestimating variability 
- run a/a experiments to estimate empirical variability
- see example in lesson on how to calculate variability empirically (can also run bootstraps to get more data)

## Experience
- a lot of time analysts spend is to validating, evaluating, and choosing metric

# Lesson 4 Designing an Experiment

## Unit of Diversion
- Select what will be used to divert the traffic to the control or treatment groups
- Think about how the unit of diversion will change the metric
- Variability of metrics can change depending on the selected unit of diversion

## Population 
- Who will be affected
- Cohort can be selected as a subset of the population (e.g. device, region, language, user demographics)
- make sure to check the invariant metrics when running experiment too. For example checking if the ratio of control users to treatment users are the same

## Sizing 
- Iterative process to find out the number of users to expose to the control and treatment groups
- Depends on alpha (false positive rate), beta (false negative rate), practical significance level (d_min). Lower alpha, beta, d_min require more sample size.
- Also depends on the variability (standard error) of the metric. If the metric is on higher level than the unit of diversion, then the variability of the metric is likely higher than the analytical estimate of the variability, thus more sample size is needed. If the metric is the same as the unit of diversion (e.g. both pageviews), then the variability/SE will be small. Smaller variability means smaller sample size is needed.   

## Learning effects
- Due to primacy (used to old style, prefer older) and novelty (new feature, let's try!) effects, figuring out the impact of learning effects might take some time. The metric might be different initially and plateau off to the eventual number. 
- To reduce risk on losing users, we might want to use a small percentage of traffic and run the test for longer period of time.
- May want to use A/A test (uniformity period), where we expose two groups of users to the same treatment, we expect to see the same effect, to ensure no other factors (pre-existing conditions) are affecting the metrics.
- Google runs A/A tests before (pre-period) and after (post-period) the A/B test in order to ensure that any change is due to the treatment, not due to pre-existing or inherent different in the two groups.

# Lesson 5: Analyzing the Results
- what you can and what you cannot conclude
- A/B test is an ITERATIVE process

## Sanity checks
- Check the population ratio to make sure it is what we expect (# of events, # of pageviews)
- Also check other metrics that we don't expect to change 
- Only after checked our invariant metrics, then we can evaluate the outcome of the experiment

## Checking invariants
- use binomial proportion test to ensure the ratio of population is as expected
- find difference between total users for control and total users for treatment
- for example for a 0.5:0.5 split,
	- compute standard error of binomial with probability 0.5 of success, SE = sqrt(p(1-p)/N)
	- Multiply stdev with z score (assume normality if sample size is big) to get margin of error
	- Compute confidence interval around 0.5
	- check whether observed ratio is within confidence interval
	- see lesson 5, exercise 6
- if invariant is violated, check if infrastructure is causing the issue (talk to engineers)

## Single metric
- effect size test and sign test (see lesson 5, exercise 9) 
- if the two agree that there is statistically significance effect and is above the practical level, and the sign test shows that there is significant positive outcome. Then it's a sure launch
- However, if the conclusion is not clear, then maybe we can run more experiments to figure out the difference.

## Simpson's paradox
- the conclusion for the experiment is reversed from if the data is split into subsets
- be careful when analyzing data and try to check the effect for each subset
- can happen if the assignment of events are not consistent between control and treatment
- can't make conclusion until we figure out what is causing this

## Multiple metric
- when running evaluation with multiple metrics, there is higher chance of observing an effect that is significant (just by chance)
- one way to check is to run repeat experiments, and the metrics that show significant differences should not show that again. 
- use automatic analysis of differences
- Use formula to calculate overall alpha (Family wise error rate FWER, or chance of having at least one false positive metric) Overall alpha = 1- (1-alpha_i)^n 
where n is the number of metrics and assuming metrics are independent
- If metrics are not independent then the calculated overall alpha will be an overestimate
- To bring down overall alpha, we can use a smaller alpha for each metric (or higher confidence level) since alpha = 1 - confidence level
- Bonferroni correction - makes no assumption, alpha for each metric is equal to target overall alpha divided by number of metrics
- However, if metrics are correlated and not independent, then the Bonferronie method is too conservative, and lead to less launches
- Use Bonferroni correction to calculate a new alpha for each metric, instead of 0.05, we use 0.05/n where n is number of metrics
- Bonferroni method may result in overly conservative result as the margin of error will be larger due to smaller alpha = (bigger z score). Can also use other methods that are more sophisticated to consider metrics that are correlated.
- Other strategy includes using False Discovery Rate = Expected value (# false positive/ # rejection of null)
Suppose we use 200 metrics, we cap FDR at 0.05. This means that we are okay with 5 false positives and 95 true positives in each experiment.
- Can also use Holm-Bonferroni method, closed testing procedure, Boole-Bonferroni method (see links in lesson 5, exercise 15)
- ultimately even if we have an Overall evaluation criterion (OEC), decision makers will likely want to look at individual metrics to make decisions

## Making decisions
- Ask these three questions when deciding whether to deploy treatment operationally:
- Do i have a practically significant and statistically significant chance?
- Do I understand how it affects the user experience?
- Is it worth it?

## Gotcha
- always do ramp up when trying to deploy change to increase exposure to more users. Effects may also flatten out (disappearing launch effect) maybe due to novelty effect or change aversion. Cohort analysis can be helpful.
- If concerned about learning effects, we can use pre- and post- period experiments to understand how users change their behaviors (if any)
- remove filters in next iteration of ramping up to see how well it generalizes to the bigger population