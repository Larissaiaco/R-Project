# R-Project
Insurance Risk Evaluation and Premium Optimization Model
---
title: "ML Project"
author: "Larissa Iacobescu"
format:
  html:
    toc: true
    toc-title: Contents
embed-resources: true
date: "2023-12-10"
editor: visual
---

Objective

The objective of this project is to create a model using the risk factors given to enhance and transform how insurance companies evaluate risk, set premiums, and allocate resources.

age

gender

driving_experience

annual_mileage

speeding_violations

duis

past_accidents

vehicle_ownership

Step 1: Data Exploration

Explore the data using visualizations to gain more insight. I will be using key variables I believe best predict what insurance companies should use to set premiums and evaluate risk.

{r}
pacman::p_load(tidymodels, janitor, readr, dplyr, tidyverse, ggplot2)

# Read data
insurance_data <- read_csv("Insurance_data.csv")

# Remove missing values
insurance_data <- na.omit(insurance_data)
insurance_data

# Factor levels and labels for 'gender'
insurance_data$gender <- factor(insurance_data$gender, levels = c(0, 1), labels = c("Male", "Female"))

# Visualization: Speeding Violations by Gender
speeding_by_gender <- insurance_data %>%
  group_by(gender) %>%
  summarize(total_speeding_violations = sum(speeding_violations, na.rm = TRUE))

ggplot(speeding_by_gender, aes(x = gender, y = total_speeding_violations, fill = gender)) +
  geom_bar(stat = "identity") +
  scale_fill_manual(values = c("blue", "pink")) +
  labs(title = "Speeding Violations by Gender", x = "Gender", y = "Total Speeding Violations", caption = "Females are more likely to speed than males.") +
  theme_minimal()

# Factor levels and labels for 'age'
insurance_data$age <- factor(insurance_data$age, levels = c(0, 1, 2, 3), labels = c("Young drivers (under 25)", "Young adults (25 to 40)", "Middle-Aged (40 to 60)", "Senior Drivers (over 60)"))

# Visualization: Speeding Violations by Age
speeding_by_age <- insurance_data %>%
  group_by(age) %>%
  summarize(total_speeding_violations = sum(speeding_violations, na.rm = TRUE))

ggplot(speeding_by_age, aes(x = age, y = total_speeding_violations, fill = age)) +
  geom_bar(stat = "identity") +
  labs(title = "Speeding Violations by Age", x = "Age", y = "Total Speeding Violations", caption = "Seniors are more likely to speed than other age groups.") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 50, hjust = 1))

# Factor levels and labels for 'driving_experience'
insurance_data$driving_experience <- factor(insurance_data$driving_experience, levels = c(0, 1, 2), labels = c("New Drivers (0-5 years)", "Moderate (6-10 years)", "Experienced (11-20 years)"))
insurance_data

# Filter out "NA" values from 'driving_experience'
filtered_data <- filter(insurance_data, !is.na(driving_experience))

# Visualization: Speeding Violations by Driving Experience
speeding_by_experience <- filtered_data %>%
  group_by(driving_experience) %>%
  summarize(total_speeding_violations = sum(speeding_violations, na.rm = TRUE))

ggplot(
  data = speeding_by_experience,
  aes(x = driving_experience, y = total_speeding_violations, fill = driving_experience)) +
  geom_bar(stat = "identity") +
  labs(title = "Speeding Violations by Driving Experience",
       x = "Driving Experience",
       y = "Total Speeding Violations",
       caption = "Experienced drivers more likely to speed.") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 50, hjust = 1))

# Visualization: DUIS by Gender
duis_by_gender <- insurance_data %>%
  group_by(gender) %>%
  summarize(total_duis = sum(duis, na.rm = TRUE))

ggplot(duis_by_gender, aes(x = gender, y = total_duis, fill = gender)) +
  geom_bar(stat = "identity") +
  scale_fill_manual(values = c("blue", "pink")) +
  labs(title = "DUIS by Gender", x = "Gender", y = "Total DUIS", caption = "Females are more likely to drink under the influence.") +
  theme_minimal()

# Visualization: DUIS by Age
duis_by_age <- insurance_data %>%
  group_by(age) %>%
  summarize(total_duis = sum(duis, na.rm = TRUE))

ggplot(duis_by_age, aes(x = age, y = total_duis, fill = age)) +
  geom_bar(stat = "identity") +
  labs(title = "DUIS by Age", x = "Age", y = "Total DUIS", caption = "Middle-aged drivers are more likely to drink and drive.") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 50, hjust = 1))

# Continue from the previous code...

# Factor levels and labels for 'driving_experience'
insurance_data$driving_experience <- factor(insurance_data$driving_experience, levels = c(0,1,2), labels = c("New Drivers (0-5 years)", "Moderate (6-10 years)","Experienced (11-20 years)"))

# Convert 'duis' to numeric
insurance_data$duis <- as.numeric(as.character(insurance_data$duis))

# Use 'filtered_data' from before to filter out NA values
duis_by_driving_experience <- filtered_data %>%
  group_by(driving_experience) %>%
  summarize(total_duis = sum(duis, na.rm = TRUE))

ggplot(duis_by_driving_experience, aes(x = driving_experience, y = total_duis, fill = driving_experience)) +
  geom_bar(stat = "identity") +
  labs(title = "DUIS by Driving Experience", x = "Driving Experience", y = "Total DUIS", caption = "Experienced most likely to drink.") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 50, hjust = 1))

# ... (Repeat similar steps for other variables)

# Past Accidents by Gender
past_accidents_by_gender <- insurance_data %>%
  group_by(gender) %>%
  summarize(total_past_accidents = sum(past_accidents, na.rm = TRUE))

ggplot(past_accidents_by_gender, aes(x = gender, y = total_past_accidents, fill = gender)) +
  geom_bar(stat = "identity") +
  scale_fill_manual(values = c("blue", "pink")) +
  labs(title = "Past Accidents by Gender", x = "Gender", y = "Total Past Accidents") +
  theme_minimal()

# Past Accidents by Age
past_accidents_by_age <- insurance_data %>%
  group_by(age) %>%
  summarize(total_past_accidents = sum(past_accidents, na.rm = TRUE))

ggplot(past_accidents_by_age, aes(x = age, y = total_past_accidents, fill = age)) +
  geom_bar(stat = "identity") +
  labs(title = "Past Accidents by Age", x = "Age", y = "Total Past Accidents") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 50, hjust = 1))

# Factor levels and labels for 'driving_experience' 
insurance_data$driving_experience <- factor(insurance_data$driving_experience, levels = c(0,1,2), labels = c("New Drivers (0-5 years)", "Moderate (6-10 years)","Experienced (11-20 years)"))

# Outcome by Driving Experience
outcome_by_driving_experience <- filtered_data %>%
  group_by(driving_experience) %>%
  summarize(total_outcome = sum(outcome, na.rm = TRUE))

print(outcome_by_driving_experience)

ggplot(outcome_by_driving_experience, aes(x = driving_experience, y = total_outcome, fill = driving_experience)) +
  geom_bar(stat = "identity") +
  labs(title = "Outcome by Driving Experience", x = "Driving Experience", y = "Total Outcome") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 50, hjust = 1))

# Factor levels and labels for 'vehicle_ownership'
insurance_data$vehicle_ownership <- factor(insurance_data$vehicle_ownership, levels = c(0,1), labels = c("Not Own", "Own"))

# Outcome by Vehicle Ownership
outcome_by_vehicle_ownership <- insurance_data %>%
  group_by(vehicle_ownership) %>%
  summarize(total_outcome = sum(outcome, na.rm = TRUE))

print(outcome_by_vehicle_ownership)

ggplot(outcome_by_vehicle_ownership, aes(x = vehicle_ownership, y = total_outcome, fill = vehicle_ownership)) +
  geom_bar(stat = "identity") +
  labs(title = "Outcome by Vehicle Ownership", x = "Vehicle Ownership", y = "Total Outcome") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 50, hjust = 1))

insurance_data


These few key variables are effective in determining risk for car insurance because they are able to determine what age, gender, and experience level majority of drivers may get into an accident or make a claim on their car insurance.

Driving experience and annual mileage determine how long a driver has been on the road, the longer on the road, the more susceptible to an accident. Speeding violations, duis, and past accidents are self explanatory to if a driver may speed more when driving, may drink more when driving, or be involved with accidents often, are most likely to be riskier drivers toward insurance companies.

New drivers have the highest annual mileage so then tend to be on the road more often, which means they are more susceptible to risk on the road. Females, young drivers, and new drivers are most likely to file a claim on their car insurance. People who do not own their cars, are most likely to file a claim as well.

Step 2: Feature Scaling and Transformation

Normalization and Decision Tree

{r}
suppressWarnings(
## Load caret package
library(caret))

## Preprocess step where applying normalization method. Scales variables to fall withing 0 to 1.
normalized_data = preProcess(insurance_data, method = c("range"))

## Apply normalization to original insurance data.
normalized_data_df = predict(normalized_data, newdata = insurance_data)
normalized_data_df

## Modify variables to ensure we have consistent scales and distributions.

Feature Selection: Performing a decision tree model to make predictions about variables and their relationship to the outcome.

{r}
library(rpart)
library(rpart.plot)

pacman::p_load(tidymodels, janitor, readr, dplyr, tidyverse, ggplot2)

# Read data
insurance_data <- read_csv("Insurance_data.csv")

# Remove missing values
insurance_data <- na.omit(insurance_data)
insurance_data

## Select relevant columns and filter outcomes
insurance_data = insurance_data %>% 
  select(outcome, driving_experience, annual_mileage , duis , past_accidents , vehicle_ownership, age) %>% 
  filter(
    outcome %in% c("0", "1")
  ) %>% 
  na.omit()

## Convert class of outcome to a factor
class(insurance_data$outcome)
insurance_data$outcome <- as.factor(insurance_data$outcome)

any(is.na(insurance_data$outcome))

## Split data into training and testing
split = initial_split(insurance_data, prop = 0.7)
train_data = training(split)
test_data = testing(split)

## Cross validated folds for model training
tree_cv = vfold_cv(train_data, v = 5)
tree_cv

## Decision tree with parameters
decision_tree_spec = decision_tree(
  tree_depth = tune(),
  min_n = tune()
) %>%
  set_engine("rpart") %>%
  set_mode("classification")

tree_wf = workflow() %>% 
  add_model(decision_tree_spec) %>% 
  add_formula(outcome ~ driving_experience + annual_mileage + duis + past_accidents + vehicle_ownership + age)

## Define a grid of hyperparameters for tuning
tree_grid = grid_regular(
  min_n(range = c(1,50)),
  tree_depth(range = c(1, 30)),
  levels = 20
)
tree_grid

## Hyperparameter tuning using cross-validation
cv_results = tune_grid(
  tree_wf,
  resamples = tree_cv,
  grid = tree_grid
)

## Show tuning results
cv_results %>% collect_metrics()


show_notes(.Last.tune.result)


cv_results %>% show_best('accuracy')
best_tree = cv_results %>% select_best('accuracy')
best_tree

## Finalize with best tuned model
final_wf = finalize_workflow(
  tree_wf,
  best_tree
)

## Fit on training set
final_fit = last_fit(final_wf, split)
final_fit %>% collect_metrics

## Retrieve 'rpart' pacakage
fit_rpart = final_fit %>% extract_fit_engine()
rpart.plot(fit_rpart)

The three variables that were actually used in the tree construction were driving experience, vehicle ownership, and age. The response variable is the outcome with 0 for "No claim" and 1 for "Claim."

If driving experience is >= 0.5 (Moderate, Experienced, Highly experienced), it has an outcome of Not Claim. If not (New drivers), it will go to vehicle ownership. This means it is predicted new drivers are most likely claim insurance.

If the vehicle ownership is >=0.5 (Own), the outcome goes to age. If no, the outcome is claim. This means it is predicted clients are most likely to claim if they do not own the vehicle

If the age is >= 0.5 (Young adults, Middle aged, Seniors), the outcome is Not claim while no is claim. Young drivers are most likely to claim insurance.

Additional features that could enhance the predictive power of the model would be weather data or geographical information such that certain places may obligate drivers to be more susceptible to road risk (heavy snow areas, populated city areas, etc.).

Step 3: Model Training

Data Splitting and ElasticNet Regression

{r}
pacman::p_load(tidymodels, janitor, readr, dplyr, tidyverse, ggplot2)

# Read data
insurance_data <- read_csv("Insurance_data.csv")

## Prepare for analysis
insurance_data = insurance_data %>% 
  select(outcome, driving_experience, annual_mileage, , duis, past_accidents, speeding_violations, age, vehicle_ownership) %>% 
  filter(
    outcome %in% c("0", "1")
  ) %>% 
  na.omit()
insurance_data

{r}

## split the insurance data into training, validation, and test sets where 70% is used for training, 15% for validation, and 15% for test.
split = initial_validation_time_split(insurance_data, prop = c(0.7, 0.15)) 
train = training(split)
valid = validation_set(split)
test = testing(split)

## Feature Engineering and Preprocessing step where nominal predictors are converted to dummy varaibles (one hot encoding) then normalized.
regularization_recipe = 
  recipe(outcome ~ .,
         train) %>% 
  step_dummy(all_nominal_predictors()) %>% 
  step_normalize(all_predictors()) 

## Apply to training data
bake(regularization_recipe %>% prep(), train) %>% head() 

## Specify the elastic net model (chosen becasue it combines lasso and ridge) with 
enet_spec = linear_reg(penalty = tune(), mixture = tune()) %>% 
  set_engine('glmnet')

## Build workflow with preprocessing and modeling steps
enet_workflow = workflow() %>% 
  add_recipe(regularization_recipe) %>% 
  add_model(enet_spec)

## Define grid for hyperparameter tuning
enet_grid = grid_regular(penalty(range = c(0,3)), 
                         mixture(range = c(0,0.5)),
                         levels = list(penalty = 30, 
                                       mixture = 5))
## Hyperparameter tuning
enet_results = tune_grid(
  enet_workflow,
  resamples = valid,
  grid = enet_grid,
  metrics = metric_set(mae, rmse, rsq, mape)
)

# Generate metrics
enet_metrics = collect_metrics(enet_results)



Machines learning algorithms that are suitable for predicting insurance claims are elastic net regression and decision trees. Insurance datasets often contain numerous correlated features so elastic net ability to handle multicollinearity (lasso) and shrinking coefficients(ridge) is beneficial. Elastic net also incorporates feature selection which helps in identifying the most impactful variables for insurance claims. Decision trees are able to make decision making easy to interpret. Decision tree are also able to handle mixed data types of numerical and categorical which insurance datasets usually have. Both algorithms also aid in preventing overfitting of the models.

Step 4: Model Evaluation

{r}
## Create a line plot using enet_metrics data
enet_plot =  
  enet_metrics %>% 
  filter(.metric == "rmse") %>% 
  ggplot(aes(x = penalty, y = mean)) +
  geom_line() +
  ggtitle("Enet Regression")
enet_plot

## Top 3 models based on rmse metric
enet_top3 = show_best(enet_results, metric = 'rmse', n = 3)
enet_top3

## Select the best model based on rmse metric
best_model = select_best(enet_results, metric = 'rmse')
best_model_metrics <- enet_results %>%
  collect_metrics() %>%
  filter(.config == best_model$.config)
best_model_metrics

## Finalize workflow
final_workflow = finalize_workflow(
  enet_workflow,
  best_model
)
## Fit workflow to training set
final_fit = last_fit(
  final_workflow,
  split = split, 
  metrics = metric_set(mae, rmse, mape, rsq)
)
final_result = collect_metrics(final_fit)
final_result



pacman::p_load(vip)

## Generate a variable importance plot to show the top 50 important variables
variable_importance_df = 
  final_workflow %>% 
  fit(train) %>% 
  extract_fit_parsnip() %>% 
  vi(lambda = best_model$penalty) %>% 
  mutate(Importance = abs(Importance),
         Variable = fct_reorder(Variable, Importance))
variable_importance_df %>% head(5)


variable_importance_df %>% 
  filter(Importance > 0) %>% 
  nrow()

variable_importance_df %>% 
  slice(1:50) %>% 
  ggplot(aes(x = Importance, y = Variable, fill = Sign)) +
  geom_col()


Based on the elastic net regression model, variables such as driving experience, vehicle ownership, and age have the highest absolute importance score. This means they have the strongest impact on the response variable, outcome; these variables impact the most on whether a client will submit a claim. This benefits insurance companies by revealing which factors should be focused on when setting premiums, evaluating risk, etc. for insurance.

To compare the strengths and weaknesses of different algorithms, it is best to perform k-fold cross validation to evaluate algorithms on different subsets. Validation sets also are able to check unseen data.

There are some ethical concerns associated with using the data for making prediction such as using age, which is one of the highest variables that impact outcome. Insurance companies often use age as a risk factor where younger and older clients are perceived as higher risk based on these age groups having more insurance claims. This may raise a concern as charging higher premiums for certain age groups based on previous data or statistics is unfair. Using solely age as a determinant for insurance policies is unethical and discriminatory so it is important to consider other factors based on pure driving skills or situations such as how long someone has been driving or if they have had an accident before.

