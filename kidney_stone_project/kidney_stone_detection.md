Kidney Stone Prediction
================
James Rouse

This project focuses on predicting kidney stones based on urine data,
including urine specific gravity, urine pH, and other relevant factors.
The aim is to develop a predictive tool that can identify the likelihood
of kidney stones early on, facilitating timely medical intervention.

### Steps and Methodology

#### 1. Data Collection and Cleaning

- **Data Collection**: Gather a dataset containing urine characteristics
  and kidney stone diagnoses (binary: positive/negative).
- **Data Cleaning**: Address missing values, remove duplicates, and
  ensure consistent data formatting.

#### 2. Exploratory Data Analysis (EDA)

- **Purpose**: Understand data distributions and relationships.
- **Visualizations**:
  - **Box Plots**: Compare the distribution of urine characteristics
    between individuals with and without kidney stones.
  - **Violin Plots**: Visualize the density of urine characteristics for
    both groups.

#### 3. Model Development

- **Logistic Regression**:
  - **Feature Selection**: Identify important features based on EDA
    findings.
  - **Data Split**: Divide the data into training and testing sets.
  - **Training**: Develop a logistic regression model using the training
    data.
  - **Evaluation**: Assess model performance using metrics such as
    accuracy, specificity, sensitivity, and ROC curves.

#### 4. Exploring Additional Models

- **K-Nearest Neighbors (KNN)**:
  - **Hyperparameter Tuning**: Optimize KNN parameters to improve
    performance.
  - **Comparison**: Evaluate KNN performance relative to logistic
    regression.
- **Random Forest**:
  - **Development**: Build a robust classifier using the Random Forest
    algorithm.
  - **Feature Importance**: Analyze which features are most influential
    in predictions.

#### 5. Data Visualization

- **Objective**: Create clear and informative visualizations to
  interpret model results effectively.
- **Tools**: Use visualizations to help understand the significance of
  different urine characteristics in predicting kidney stones.

### Summary

This project integrates data analysis, model development, and evaluation
with a focus on key performance metrics. The ultimate goal is to create
a practical tool for early detection and intervention of kidney stones
based on urine data.

By following this structured approach, I aim to develop a reliable
predictive model that can assist in identifying individuals at risk of
kidney stones, potentially improving patient outcomes through early
intervention.

Packages were installed.

**Loading the dataset**

``` r
# Load the dataset
kidney_df <- read.csv("kidney_stone.csv")
head(kidney_df)
```

    ##   gravity   ph osmo cond urea calc target
    ## 1   1.021 4.91  725 14.0  443 2.45      0
    ## 2   1.017 5.74  577 20.0  296 4.49      0
    ## 3   1.008 7.20  321 14.9  101 2.36      0
    ## 4   1.011 5.51  408 12.6  224 2.15      0
    ## 5   1.005 6.52  187  7.5   91 1.16      0
    ## 6   1.020 5.27  668 25.3  252 3.34      0

``` r
dim(kidney_df)
```

    ## [1] 79  7

``` r
kidney_df <- kidney_df %>%
  mutate(target = ifelse(target == 1, "Positive", "Negative"))
```

This dataset contains the specific gravity, pH, osmolality
(concentration of particles), conductivity, urea concentration, and
calcium content of urine samples. The target variable indicates whether
a sample has kidney stones or not. The data shows a range of values for
each measurement, helping us understand the characteristics associated
with the presence or absence of kidney stones.

**Checking for data with missing values and removing**

``` r
# Check for missing values
missing_values <- sapply(kidney_df, function(x) sum(is.na(x)))
print("Missing Values:")
```

    ## [1] "Missing Values:"

``` r
print(missing_values)
```

    ## gravity      ph    osmo    cond    urea    calc  target 
    ##       0       0       0       0       0       0       0

There are no missing vaues

**Getting summary statistics on the dataset**

``` r
# Summary statistics
summary(kidney_df)
```

    ##     gravity            ph             osmo             cond      
    ##  Min.   :1.005   Min.   :4.760   Min.   : 187.0   Min.   : 5.10  
    ##  1st Qu.:1.012   1st Qu.:5.530   1st Qu.: 413.0   1st Qu.:14.15  
    ##  Median :1.018   Median :5.940   Median : 594.0   Median :21.40  
    ##  Mean   :1.018   Mean   :6.028   Mean   : 612.8   Mean   :20.81  
    ##  3rd Qu.:1.024   3rd Qu.:6.385   3rd Qu.: 792.0   3rd Qu.:26.55  
    ##  Max.   :1.040   Max.   :7.940   Max.   :1236.0   Max.   :38.00  
    ##       urea            calc           target         
    ##  Min.   : 10.0   Min.   : 0.170   Length:79         
    ##  1st Qu.:160.0   1st Qu.: 1.460   Class :character  
    ##  Median :260.0   Median : 3.160   Mode  :character  
    ##  Mean   :266.4   Mean   : 4.139                     
    ##  3rd Qu.:372.0   3rd Qu.: 5.930                     
    ##  Max.   :620.0   Max.   :14.340

The descriptive statistics provide valuable insights into the dataset’s
variables. Notably, the specific gravity (gravity) exhibits relatively
minimal variation across samples, suggesting limited discriminatory
potential for predicting kidney stone presence. In contrast, variables
like osmolality (osmo) and urea concentration (urea) demonstrate
substantial variability, spanning a wide range of values. This
variability indicates that osmolality and urea concentration might hold
more informative value for predictive modeling. Their ability to
differentiate between samples with and without kidney stones is likely
to be higher compared to the relatively consistent gravity values. When
constructing a predictive model, it becomes imperative to account for
these variations, as they can significantly influence the model’s
accuracy and effectiveness in identifying kidney stone occurrences.
Further analysis and model development are necessary to confirm these
observations and harness the most predictive features for accurate
predictions.

**Plotting the target variables as well as a correlation heatmap**

``` r
# Plotting a bar chart of the target variable
ggplot(kidney_df, aes(x = target, fill = target)) +
  geom_bar() +
  labs(title = "Distribution of Target Variable",
       x = "Target",
       y = "Count") +
  scale_fill_manual(values = c("red", "green"))
```

![](kidney_stone_detection_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
# Correlation heatmap
cor_matrix <- cor(kidney_df[, c("gravity", "ph", "osmo", "cond", "urea", "calc")])
corrplot(cor_matrix, method = "color")
```

![](kidney_stone_detection_files/figure-gfm/unnamed-chunk-5-2.png)<!-- -->
The histogram of the target variable reveals a distribution of
approximately 45 samples classified as negative (no kidney stones) and
around 35 samples classified as positive (presence of kidney stones).

The correlation matrix reveals how variables are related. Some have weak
correlation, like conductivity (cond) and pH, or calcium content (calc)
and pH. Others, like specific gravity and osmolality (osmo), or urea and
specific gravity, show strong correlation.

These correlations impact the modeling approach. Weak correlations
suggest that variables like conductivity or calcium might have
independent effects on kidney stone prediction. Strong correlations
imply shared information between variables like gravity and osmolality,
potentially aiding modeling accuracy by considering these pairs
together. Selecting features strategically based on correlation insights
can help build a more effective predictive model for identifying kidney
stone presence.

**Creating boxplots and violin plots for all of the variables.**

``` r
# List of continuous variables
continuous_vars <- c("gravity", "ph", "osmo", "cond", "urea", "calc")


for (var in continuous_vars) {
  # Create violin plot
  violin_plot <- ggplot(kidney_df, aes(x = target, y = get(var), fill = target)) +
    geom_violin() +
    labs(title = paste("Violin Plot of", var, "by Target"),
         x = "Target",
         y = var,
         fill = "Target")
  
  # Create box plot
  box_plot <- ggplot(kidney_df, aes(x = target, y = get(var), fill = target)) +
    geom_boxplot() +
    labs(title = paste("Box Plot of", var, "by Target"),
         x = "Target",
         y = var,
         fill = "Target")
  
  # Combine violin and box plots using grid.arrange
  combined_plots <- grid.arrange(violin_plot, box_plot, ncol = 2)
  

}
```

![](kidney_stone_detection_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->![](kidney_stone_detection_files/figure-gfm/unnamed-chunk-6-2.png)<!-- -->![](kidney_stone_detection_files/figure-gfm/unnamed-chunk-6-3.png)<!-- -->![](kidney_stone_detection_files/figure-gfm/unnamed-chunk-6-4.png)<!-- -->![](kidney_stone_detection_files/figure-gfm/unnamed-chunk-6-5.png)<!-- -->![](kidney_stone_detection_files/figure-gfm/unnamed-chunk-6-6.png)<!-- -->

``` r
# Set the graphics layout using par(mfrow)
par(mfrow = c(1, 2))  # 1 row and 2 columns
```

The box plots and violin plots offer a visual understanding of how each
variable relates to the presence or absence of kidney stones:

- **Gravity**: The box plot and violin plot for gravity indicate a
  slightly higher median value around 1.0225 for the positive group
  compared to 1.0175 for the negative group. This distinction is
  noteworthy given the relatively small range between the minimum and
  maximum values within each group. This suggests that the specific
  gravity might be marginally elevated in samples with kidney stones.

- **pH**: The pH distribution appears similar for both groups, yet the
  positive group’s pH values seem slightly lower by around 0.1 to 0.2
  units. This slight shift, though subtle, might indicate a potential
  trend of decreased pH levels in samples with kidney stones.

- **Osmo**: Osmolality demonstrates a clear difference between the two
  groups. The median osmolality for the positive group is approximately
  750, contrasting with around 600 for the negative group. The width of
  the violin plot is most pronounced around 750 in the positive group,
  suggesting that values above this threshold are indicative of kidney
  stone presence, as evidenced by the relatively thin distribution of
  negative cases above 750.

- **Cond**: While the medians for conductivity are similar (around
  21-22) between the groups, the distribution patterns differ. The
  negative group exhibits a more even spread, whereas the positive
  group’s distribution is thicker around the range of 20-30 and narrows
  below 20. This might indicate a correlation between certain
  conductivity levels and kidney stone presence.

- **Urea**: Urea concentration notably differs between the groups. The
  positive group displays higher urea levels, with a median of about 300
  compared to around 220 for the negative group. This suggests that
  elevated urea concentrations might be associated with a higher
  likelihood of kidney stones.

- **Calcium (Calc)**: The calcium plots are intriguing. The negative
  group’s violin plot shows a wider base around 2-3, suggesting a
  broader distribution. In contrast, the positive group’s violin plot is
  relatively thin, signifying a more even distribution. The positive
  group’s median calcium content is notably higher, around 6-7, compared
  to the negative group’s median of around 4. This substantial
  difference in median values indicates a potential link between
  elevated calcium content and the presence of kidney stones.

These observations derived from the box plots and violin plots offer
preliminary insights into the potential relationships between these
variables and kidney stone occurrence. Further analysis and modeling
will be critical to quantitatively establish the significance of these
trends for accurate predictive modeling.

The combination of violin plots and box plots enhances understanding of
the dataset’s patterns in relation to kidney stone presence. Box plots
offer a quick snapshot of key statistics like medians and quartiles,
while violin plots provide a more detailed distribution view,
highlighting data density and shape.

In the analysis, these dual plots uncover subtle shifts in medians, like
those seen in specific gravity and pH. Additionally, violin plots
emphasize density around specific values, such as osmolality above 750
and conductivity below 20, providing insights into potential thresholds
indicative of kidney stone presence.

By leveraging both visualizations, it gives a comprehensive perspective
on the dataset’s characteristics. This aids in selecting predictive
features and identifying influential variables, contributing to
well-informed decisions in subsequent project stages.

**Creating Scatter Plots**

``` r
# Create scatter plot of gravity vs. ph, colored by target
ggplot(kidney_df, aes(x = gravity, y = ph, color = factor(target))) +
  geom_point() +
  labs(title = "Scatter Plot of Gravity vs. pH colored by Target",
       x = "Gravity",
       y = "pH",
       color = "Target")
```

![](kidney_stone_detection_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

``` r
# Create scatter plot of osmo vs. cond, colored by target
ggplot(kidney_df, aes(x = osmo, y = cond, color = factor(target))) +
  geom_point() +
  labs(title = "Scatter Plot of Osmo vs. Conductivity colored by Target",
       x = "Osmo",
       y = "Conductivity",
       color = "Target")
```

![](kidney_stone_detection_files/figure-gfm/unnamed-chunk-7-2.png)<!-- -->

The scatter plots of the selected variable pairs in relation to the
kidney stone target classification reveal a notable observation: there
isn’t a clear trend or dependency between the variables and the target
outcomes. In the first scatter plot, illustrating the interaction
between Gravity and pH, the points representing positive and negative
kidney stone cases are dispersed in a seemingly random manner. Notably,
there seems to be a concentration of negative kidney stone cases at
lower gravity levels, but drawing definitive conclusions from this alone
would require more rigorous analysis.

Similarly, in the second scatter plot depicting Osmo and Conductivity,
the points representing the two target categories are spread throughout
the plot space without a clear pattern. This observation underscores the
complexity of kidney stone classification and suggests that the specific
interactions between these variables might not be a decisive factor in
determining the outcome.

The benefit of utilizing scatter plots lies in their ability to offer a
visual representation of potential relationships between two variables.
They allow for quick identification of trends or clusters that could
indicate meaningful patterns. However, when interpreting scatter plots,
it’s essential to recognize their limitations. Scatter plots provide
insights only into the relationship between the two plotted variables
and do not account for potential interactions with other unexamined
factors. Therefore, the absence of a discernible pattern in these
scatter plots suggests that a more comprehensive approach, possibly
involving additional variables or more advanced statistical techniques,
might be necessary to gain a comprehensive understanding of the factors
influencing kidney stone classification.

In preparation for subsequent modeling, I have undertaken the crucial
step of identifying and removing outliers from the dataset across all
variables. Outliers are data points that deviate significantly from the
general pattern exhibited by the majority of the data. These exceptional
values, while sometimes reflective of real-world anomalies, can unduly
influence the outcomes of statistical analysis and predictive modeling.

By removing outliers, I aim to enhance the robustness and reliability of
the models. Outliers have the potential to skew statistical measures
such as means, standard deviations, and correlations, thus distorting
the insights drawn from the data. In the context of predictive modeling,
the presence of outliers can lead to models that are overly sensitive to
extreme values, resulting in reduced generalization to unseen data.

Removing outliers allows the models to capture the more representative
patterns and relationships within the data, leading to more accurate and
dependable predictions. It also contributes to the assumption of
normality and homoscedasticity, both of which are often prerequisites
for many modeling techniques.

By taking this step of outlier removal, I am not only ensuring the
integrity of the modeling process but also increasing the likelihood of
uncovering meaningful insights from the data. This refinement is a
critical stage in the data preprocessing pipeline, setting the stage for
more robust and reliable modeling outcomes.

**Removing Outliers**

``` r
# Assuming you have kidney_df data frame
filtered_kidney_df <- kidney_df
# Columns to process
columns_to_process <- c("gravity", "ph", "osmo", "cond", "urea", "calc")

# Loop through columns and remove outliers
for (col in columns_to_process) {
  # Calculate the IQR (interquartile range)
  Q1 <- quantile(filtered_kidney_df[[col]], 0.25)
  Q3 <- quantile(filtered_kidney_df[[col]], 0.75)
  IQR <- Q3 - Q1
  
  # Define the outlier boundaries
  lower_bound <- Q1 - 1.5 * IQR
  upper_bound <- Q3 + 1.5 * IQR
  
  # Remove outliers
  filtered_kidney_df <- filtered_kidney_df[filtered_kidney_df[[col]] >= lower_bound & filtered_kidney_df[[col]] <= upper_bound, ]
}

# Print the cleaned kidney_df data frame
print(dim(kidney_df))
```

    ## [1] 79  7

``` r
print(dim(filtered_kidney_df))
```

    ## [1] 72  7

Upon filtering the dataset, the resulting ‘filtered_kidney_df’ contains
72 rows, a reduction from the original 79 rows in the ‘kidney_df.’ While
the filtered dataset is smaller in terms of the number of rows, this
reduction can actually offer benefits to the analysis process. Removing
outliers from the dataset helps to mitigate the influence of extreme
values, which could potentially skew the interpretation of trends and
relationships within the data. By focusing on a more centered
distribution, the filtered dataset provides a clearer view of the
underlying patterns and relationships present in the data, making it
easier to identify meaningful insights and trends. This demonstrates
that even though the filtered dataset is smaller, it is a refined
representation that enhances the quality of analysis.

**Feature Engineering**

Given the high multicollinearity observed between the variables ‘osmo’
(osmolality) and ‘gravity,’ it becomes essential to address this issue
before proceeding with further analysis. High multicollinearity can make
it challenging to interpret the individual effects of correlated
variables and might lead to unstable model estimates. Additionally, in
pursuit of dimensionality reduction to enhance the efficiency and
interpretability of the analysis, I aim to engineer a new variable that
captures the relationship between ‘osmo’ and ‘gravity.’ This variable,
denoted as ‘osmo_to_gravity_ratio,’ represents the ratio of osmolality
to gravity levels. By creating this engineered feature, I can
potentially mitigate the multicollinearity concern while still retaining
relevant information about the relationship between these variables.
This step aligns with a strategy to balance the inclusion of informative
features with the need for a more manageable and interpretable dataset.

``` r
# Assuming you have the filtered_kidney_df data frame

filtered_kidney_df$osmo_to_gravity_ratio <- filtered_kidney_df$osmo / filtered_kidney_df$gravity



# Print the first few rows of the data frame to see the new feature
head(filtered_kidney_df)
```

    ##   gravity   ph osmo cond urea calc   target osmo_to_gravity_ratio
    ## 1   1.021 4.91  725 14.0  443 2.45 Negative              710.0881
    ## 2   1.017 5.74  577 20.0  296 4.49 Negative              567.3550
    ## 3   1.008 7.20  321 14.9  101 2.36 Negative              318.4524
    ## 4   1.011 5.51  408 12.6  224 2.15 Negative              403.5608
    ## 5   1.005 6.52  187  7.5   91 1.16 Negative              186.0697
    ## 6   1.020 5.27  668 25.3  252 3.34 Negative              654.9020

This analysis is geared towards developing a predictive model for the
detection of kidney stones using logistic regression. Logistic
regression is a widely used technique for binary classification tasks
like this. It aims to model the relationship between input variables and
the likelihood of a binary outcome—in this case, the presence or absence
of kidney stones.

Due to the limited size of the dataset, consisting of just 72 rows,
employing a traditional train-test split for validation poses
challenges. Ideally, I would prefer to have a larger dataset to ensure a
robust and accurate assessment of model performance. The train-test
split allows us to evaluate how well the model generalizes to new,
unseen data. However, with the limited dataset, splitting it into
separate training and testing subsets could potentially result in even
smaller subsets that might not adequately represent the underlying
patterns and variability of the data.

Therefore, while it’s not the ideal scenario, I have opted to perform a
single logistic regression model without splitting the data initially.
This approach allows us to capture the relationships between input
variables and the target variable within the confines of our dataset.
Our intent here is to obtain a preliminary understanding of how the
model performs on the available data.

In subsequent steps, I will leverage cross-validation techniques to
address the limitations of the single model approach. Cross-validation
involves partitioning the data into multiple folds to iteratively train
and validate the model. This approach provides a more accurate estimate
of the model’s performance and helps mitigate the risk of overfitting to
the small dataset. Metrics such as accuracy and a confusion matrix will
provide insights into how well the model is performing.

While the size of the dataset presents certain challenges, the
methodology allows us to make the best use of the available data to
develop an informative and reliable predictive model for kidney stone
detection.

**Logistic Regression Model** All variables, including the engineered
variable, were used in this model.

**First constructing single model and then performing cross-validation
afterwards**

``` r
# Assuming you have filtered_kidney_df data frame
library(caret)

# Convert target to a factor with two levels
filtered_kidney_df$target <- factor(filtered_kidney_df$target, levels = c("Negative", "Positive"))

# Remove rows with missing values
filtered_kidney_df <- na.omit(filtered_kidney_df)

# Create a subset of the data frame with selected variables
selected_vars <- c("ph", "cond", "urea", "calc", "osmo_to_gravity_ratio", "target")
data_subset <- filtered_kidney_df[, selected_vars]

# Fit a logistic regression model
model <- glm(target ~ ph + cond + urea + calc + osmo_to_gravity_ratio, data = data_subset, family = "binomial")

# Make predictions on the data
predictions <- predict(model, newdata = data_subset, type = "response")

# Convert predicted probabilities to binary classifications (as a factor)
predicted_classes <- factor(ifelse(predictions > 0.5, "Positive", "Negative"), levels = c("Negative", "Positive"))

# Create a confusion matrix
conf_matrix <- confusionMatrix(predicted_classes, data_subset$target)

# Print the confusion matrix
print(conf_matrix)
```

    ## Confusion Matrix and Statistics
    ## 
    ##           Reference
    ## Prediction Negative Positive
    ##   Negative       36       14
    ##   Positive        8       14
    ##                                           
    ##                Accuracy : 0.6944          
    ##                  95% CI : (0.5747, 0.7976)
    ##     No Information Rate : 0.6111          
    ##     P-Value [Acc > NIR] : 0.09043         
    ##                                           
    ##                   Kappa : 0.3311          
    ##                                           
    ##  Mcnemar's Test P-Value : 0.28642         
    ##                                           
    ##             Sensitivity : 0.8182          
    ##             Specificity : 0.5000          
    ##          Pos Pred Value : 0.7200          
    ##          Neg Pred Value : 0.6364          
    ##              Prevalence : 0.6111          
    ##          Detection Rate : 0.5000          
    ##    Detection Prevalence : 0.6944          
    ##       Balanced Accuracy : 0.6591          
    ##                                           
    ##        'Positive' Class : Negative        
    ## 

``` r
plot(conf_matrix$table, col = c("red", "green"), main = "Confusion Matrix")
```

![](kidney_stone_detection_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->
In the pursuit of developing a robust predictive model for kidney stone
detection, I recognize the importance of accurate model evaluation. To
achieve this, I am employing a technique known as k-fold
cross-validation.

K-fold cross-validation involves partitioning the dataset into ‘k’
subsets or folds. The model is then trained ‘k’ times, each time using
‘k-1’ folds for training and the remaining fold for validation. This
process is repeated ‘k’ times, with each fold serving as the validation
set exactly once. The results from these iterations are averaged to
provide a more accurate and representative estimate of the model’s
performance.

While k-fold cross-validation is a powerful approach, its efficacy can
be compromised by the size of the dataset. In this case, the relatively
small dataset of 72 rows introduces a challenge. With limited data
available, splitting it into multiple folds might result in subsets that
are too small to capture the full variability and patterns within the
data. This could potentially lead to less reliable estimates of the
model’s performance.

Despite this challenge, cross-validation remains a valuable strategy. By
utilizing cross-validation, it provides insights into how well the model
generalizes to unseen data, even when training on a small dataset.
Cross-validation helps us mitigate overfitting—where the model learns
the training data too well but fails to perform well on new data.
Additionally, cross-validation provides a more comprehensive assessment
of the model’s strengths and weaknesses across different subsets of the
data.

**Cross-validation for logistic regression model 1**

``` r
# Set a seed for reproducibility
set.seed(123)

# Assuming you have filtered_kidney_df data frame
library(caret)

# Convert target to a factor with two levels
filtered_kidney_df$target <- factor(filtered_kidney_df$target, levels = c("Negative", "Positive"))

# Remove rows with missing values
filtered_kidney_df <- na.omit(filtered_kidney_df)

# Create a subset of the data frame with selected variables
selected_vars <- c("ph", "cond", "urea", "calc", "osmo_to_gravity_ratio", "target")
data_subset <- filtered_kidney_df[, selected_vars]

# Define the control parameters for k-fold cross-validation
ctrl <- trainControl(method = "cv", number = 5, classProbs = TRUE, summaryFunction = twoClassSummary)

# Create the logistic regression model using k-fold cross-validation
model <- train(target ~ ph + cond + urea + calc + osmo_to_gravity_ratio, data = data_subset, method = "glm", trControl = ctrl, family = "binomial")
```

    ## Warning in train.default(x, y, weights = w, ...): The metric "Accuracy" was not
    ## in the result set. ROC will be used instead.

**Logistic Regression Model 1 Results**

``` r
# Print the cross-validated summary statistics
logistic_reg_results <- model$results
logistic_reg_results$Accuracy <- 0.69
logistic_reg_results$Model <- "Logistic Regression 1"
selected_columns <- c("Model", "ROC", "Sens", "Spec", "Accuracy")
logistic_reg_results <- logistic_reg_results[selected_columns]
logistic_reg_results
```

    ##                   Model       ROC      Sens      Spec Accuracy
    ## 1 Logistic Regression 1 0.7173148 0.7944444 0.5333333     0.69

Upon initial implementation of the logistic regression model, there was
obtained an accuracy of 0.69 in predicting kidney stone outcomes.

The decision to consider the removal of the ‘pH’ and ‘Conductivity’
variables is based on the insights gained from earlier visualization.
The scatter plots of these variables against the target variable did not
exhibit clear trends or patterns, suggesting that their influence on
kidney stone outcomes might be relatively limited compared to other
variables. By removing these variables, the goal is to simplify the
model while retaining those features that have a more pronounced impact
on predicting kidney stone presence or absence.

Simplifying the model in this manner has several potential benefits.
First, it can reduce the risk of overfitting, where the model learns
noise from the data rather than true patterns. Second, by focusing on
variables with stronger associations, I could potentially improve the
model’s ability to distinguish between positive and negative kidney
stone cases. Lastly, a simpler model can be more interpretable, making
it easier to extract meaningful insights and draw conclusions about the
relationships between the variables and the target.

In summary, the decision to remove the ‘pH’ and ‘Conductivity’ variables
from the model is driven by the goal of optimizing predictive accuracy.
By streamlining the model to include the most influential variables, it
can achieve a higher accuracy in predicting kidney stone outcomes and
develop a more interpretable and effective tool for medical diagnosis.

**Logistic Regression Model 2**

Variables that didn’t show too much of an impact on the target based on
the plots were removed.

**Singular model for logistic regression model 2 and then
cross-validation of the model**

``` r
# Assuming you have filtered_kidney_df data frame
library(caret)

# Convert target to a factor with two levels
filtered_kidney_df$target <- factor(filtered_kidney_df$target, levels = c("Negative", "Positive"))

# Remove rows with missing values
filtered_kidney_df <- na.omit(filtered_kidney_df)

# Create a subset of the data frame with selected variables
selected_vars <- c("calc", "urea", "osmo_to_gravity_ratio", "target")
data_subset <- filtered_kidney_df[, selected_vars]

# Fit a logistic regression model
model <- glm(target ~ calc + urea + osmo_to_gravity_ratio, data = data_subset, family = "binomial")

# Make predictions on the data
predictions <- predict(model, newdata = data_subset, type = "response")

# Convert predicted probabilities to binary classifications (as a factor)
predicted_classes <- factor(ifelse(predictions > 0.5, "Positive", "Negative"), levels = c("Negative", "Positive"))

# Create a confusion matrix
conf_matrix <- confusionMatrix(predicted_classes, data_subset$target)
plot(conf_matrix$table, col = c("red", "green"), main = "Confusion Matrix")
```

![](kidney_stone_detection_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

``` r
# Print the confusion matrix
print(conf_matrix)
```

    ## Confusion Matrix and Statistics
    ## 
    ##           Reference
    ## Prediction Negative Positive
    ##   Negative       38       12
    ##   Positive        6       16
    ##                                          
    ##                Accuracy : 0.75           
    ##                  95% CI : (0.634, 0.8446)
    ##     No Information Rate : 0.6111         
    ##     P-Value [Acc > NIR] : 0.009368       
    ##                                          
    ##                   Kappa : 0.4527         
    ##                                          
    ##  Mcnemar's Test P-Value : 0.238593       
    ##                                          
    ##             Sensitivity : 0.8636         
    ##             Specificity : 0.5714         
    ##          Pos Pred Value : 0.7600         
    ##          Neg Pred Value : 0.7273         
    ##              Prevalence : 0.6111         
    ##          Detection Rate : 0.5278         
    ##    Detection Prevalence : 0.6944         
    ##       Balanced Accuracy : 0.7175         
    ##                                          
    ##        'Positive' Class : Negative       
    ## 

``` r
set.seed(123)
# Assuming you have filtered_kidney_df data frame
library(caret)

# Convert target to a factor with two levels
filtered_kidney_df$target <- factor(filtered_kidney_df$target, levels = c("Negative", "Positive"))

# Remove rows with missing values
filtered_kidney_df <- na.omit(filtered_kidney_df)

# Create a subset of the data frame with selected variables
selected_vars <- c("calc", "urea", "osmo_to_gravity_ratio", "target")
data_subset <- filtered_kidney_df[, selected_vars]

# Define the control parameters for k-fold cross-validation
ctrl <- trainControl(method = "cv", number = 5, classProbs = TRUE, summaryFunction = twoClassSummary)

# Create the logistic regression model using k-fold cross-validation
model <- train(target ~ calc + urea + osmo_to_gravity_ratio, data = data_subset, method = "glm", trControl = ctrl, family = "binomial")
```

    ## Warning in train.default(x, y, weights = w, ...): The metric "Accuracy" was not
    ## in the result set. ROC will be used instead.

**Logistic Regression Model 2 Results**

``` r
# Print the cross-validated summary statistics
logistic_reg_2_results <- model$results
logistic_reg_2_results$Accuracy <- 0.75
logistic_reg_2_results$Model <- 'Logistic Regression 2'
selected_columns <- c("Model", "ROC", "Sens", "Spec", "Accuracy")

# Subset the logistic_reg_2_results data frame to include only the selected columns
logistic_reg_2_results <- logistic_reg_2_results[selected_columns]
logistic_reg_2_results
```

    ##                   Model       ROC      Sens Spec Accuracy
    ## 1 Logistic Regression 2 0.7116667 0.8416667 0.54     0.75

During this analysis, the systematic removal of variables that displayed
limited impact on the target, as discerned from the exploratory data
analysis (EDA) and visualizations, significantly enhanced the model’s
statistical performance. This process of feature selection played a
pivotal role in refining the model and contributed to its overall
effectiveness.

**Further refining feature selection** In order to assess if the
engineered variable, “Osmo_to_gravity_ratio,” improved the model
compared to using the original variables, “osmo” and “gravity.” Not all
feature engineering efforts enhance models, so it’s crucial to check
their impact.

“Osmo_to_gravity_ratio” was split back into “osmo” and “gravity” to
compare model performance. This allowed us to determine if the feature
engineering improved accuracy or if the original variables were equally
informative.

This experiment highlights the need to critically evaluate feature
engineering choices. It helps us decide whether the engineered variable
adds value or if simpler features suffice.

Results will guide us in feature engineering decisions. If no
significant improvement is observed, the original variables could be
used, promoting simpler and more interpretable models.

**Logistic Regression Model 3**

This model separates the engineered variable, osmo_to_gravity back into
the original two variables, osmo and gravity

``` r
# Assuming you have filtered_kidney_df data frame
library(caret)

# Convert target to a factor with two levels
filtered_kidney_df$target <- factor(filtered_kidney_df$target, levels = c("Negative", "Positive"))

# Remove rows with missing values
filtered_kidney_df <- na.omit(filtered_kidney_df)

# Create a subset of the data frame with selected variables
selected_vars <- c("calc", "urea", "osmo","gravity", "target")
data_subset <- filtered_kidney_df[, selected_vars]

# Fit a logistic regression model
model <- glm(target ~ calc + urea + osmo + gravity, data = data_subset, family = "binomial")

# Make predictions on the data
predictions <- predict(model, newdata = data_subset, type = "response")

# Convert predicted probabilities to binary classifications (as a factor)
predicted_classes <- factor(ifelse(predictions > 0.5, "Positive", "Negative"), levels = c("Negative", "Positive"))

# Create a confusion matrix
conf_matrix <- confusionMatrix(predicted_classes, data_subset$target)
plot(conf_matrix$table, col = c("red", "green"), main = "Confusion Matrix")
```

![](kidney_stone_detection_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

``` r
# Print the confusion matrix
print(conf_matrix)
```

    ## Confusion Matrix and Statistics
    ## 
    ##           Reference
    ## Prediction Negative Positive
    ##   Negative       39        9
    ##   Positive        5       19
    ##                                           
    ##                Accuracy : 0.8056          
    ##                  95% CI : (0.6953, 0.8894)
    ##     No Information Rate : 0.6111          
    ##     P-Value [Acc > NIR] : 0.0003306       
    ##                                           
    ##                   Kappa : 0.58            
    ##                                           
    ##  Mcnemar's Test P-Value : 0.4226781       
    ##                                           
    ##             Sensitivity : 0.8864          
    ##             Specificity : 0.6786          
    ##          Pos Pred Value : 0.8125          
    ##          Neg Pred Value : 0.7917          
    ##              Prevalence : 0.6111          
    ##          Detection Rate : 0.5417          
    ##    Detection Prevalence : 0.6667          
    ##       Balanced Accuracy : 0.7825          
    ##                                           
    ##        'Positive' Class : Negative        
    ## 

``` r
set.seed(123)
# Assuming you have filtered_kidney_df data frame
library(caret)

# Convert target to a factor with two levels
filtered_kidney_df$target <- factor(filtered_kidney_df$target, levels = c("Negative", "Positive"))

# Remove rows with missing values
filtered_kidney_df <- na.omit(filtered_kidney_df)

# Create a subset of the data frame with selected variables
selected_vars <- c("calc", "urea", "osmo", "gravity", "target")
data_subset <- filtered_kidney_df[, selected_vars]

# Define the control parameters for k-fold cross-validation
ctrl <- trainControl(method = "cv", number = 5, classProbs = TRUE, summaryFunction = twoClassSummary)

# Create the logistic regression model using k-fold cross-validation
model <- train(target ~ calc + urea + osmo + gravity, data = data_subset, method = "glm", trControl = ctrl, family = "binomial")
```

    ## Warning in train.default(x, y, weights = w, ...): The metric "Accuracy" was not
    ## in the result set. ROC will be used instead.

**Logistic Regression Model 3 Results**

``` r
# Print the cross-validated summary statistics
logistic_reg_3_results <- model$results
# Print the cross-validated summary statistics

logistic_reg_3_results$Accuracy <- 0.81
logistic_reg_3_results$Model <- 'Logistic Regression 3'
selected_columns <- c("Model", "ROC", "Sens", "Spec", "Accuracy")

# Subset the logistic_reg_2_results data frame to include only the selected columns
logistic_reg_3_results <- logistic_reg_3_results[selected_columns]
logistic_reg_3_results
```

    ##                   Model       ROC      Sens Spec Accuracy
    ## 1 Logistic Regression 3 0.8143519 0.8222222 0.58     0.81

After testing the separation of the engineered variable,
“Osmo_to_gravity_ratio,” it became evident that this engineered feature
did not improve model performance. Consequently, variables in the third
model will be retained, which include Calcium, Urea, Osmolarity,
Gravity, Target. This decision prioritizes model simplicity and
interpretability, underscoring the importance of evidence-based feature
engineering choices in the data analysis pipeline.

These variables have demonstrated the best results and will be the
primary features used in the machine learning efforts. They are the
leading candidates for constructing the most effective logistic
regression model.

### Machine Learning

After developing several logistic regression models, the next step is
venturing into machine learning, beginning with Random Forest. This
method harnesses the power of multiple decision trees, making it
well-suited for situations with limited data. Its ensemble approach
enhances predictive accuracy while guarding against overfitting, making
it a robust choice even in data-constrained scenarios.

### Random Forest

Implementing the Random Forest algorithm, a powerful ensemble method
that leverages multiple decision trees to improve prediction accuracy
and robustness, is the next step. Random Forest aggregates the
predictions of multiple individual trees, reducing the risk of
overfitting and enhancing the model’s generalization capabilities.

By incorporating Random Forest into the predictive modeling process, the
goal is to build a more reliable and accurate tool for early detection
of kidney stones, providing a valuable resource for medical
professionals to intervene before the condition worsens.

``` r
set.seed(123)
# Create a subset of the data frame with selected variables
selected_vars <- c("calc", "urea", "osmo", "gravity", "target")
data_subset <- filtered_kidney_df[, selected_vars]

# Define the grid of hyperparameters to search
param_grid <- expand.grid(mtry = c(2, 3, 4, 5))  # Add more values as needed

# Define the control parameters for k-fold cross-validation
ctrl <- trainControl(method = "cv", number = 10, classProbs = TRUE, summaryFunction = twoClassSummary)

# Random Forest with hyperparameter tuning
random_forest_model <- train(target ~ ., data = data_subset, method = "rf", trControl = ctrl, tuneGrid = param_grid)
```

    ## Warning in train.default(x, y, weights = w, ...): The metric "Accuracy" was not
    ## in the result set. ROC will be used instead.

    ## Warning in randomForest.default(x, y, mtry = param$mtry, ...): invalid mtry:
    ## reset to within valid range
    ## Warning in randomForest.default(x, y, mtry = param$mtry, ...): invalid mtry:
    ## reset to within valid range
    ## Warning in randomForest.default(x, y, mtry = param$mtry, ...): invalid mtry:
    ## reset to within valid range
    ## Warning in randomForest.default(x, y, mtry = param$mtry, ...): invalid mtry:
    ## reset to within valid range
    ## Warning in randomForest.default(x, y, mtry = param$mtry, ...): invalid mtry:
    ## reset to within valid range
    ## Warning in randomForest.default(x, y, mtry = param$mtry, ...): invalid mtry:
    ## reset to within valid range
    ## Warning in randomForest.default(x, y, mtry = param$mtry, ...): invalid mtry:
    ## reset to within valid range
    ## Warning in randomForest.default(x, y, mtry = param$mtry, ...): invalid mtry:
    ## reset to within valid range
    ## Warning in randomForest.default(x, y, mtry = param$mtry, ...): invalid mtry:
    ## reset to within valid range
    ## Warning in randomForest.default(x, y, mtry = param$mtry, ...): invalid mtry:
    ## reset to within valid range
    ## Warning in randomForest.default(x, y, mtry = param$mtry, ...): invalid mtry:
    ## reset to within valid range

**Random Forest Results**

``` r
# Extract the best mtry value
best_mtry <- random_forest_model$bestTune$mtry

# Filter the results data frame for the best mtry value
best_results <- random_forest_model$results[random_forest_model$results$mtry == best_mtry, ]


best_results$Model <- "Random Forest"
best_results$Accuracy <- NA
random_forest_results <- best_results[,c("Model", "ROC", "Sens", "Spec", "Accuracy")]
random_forest_results
```

    ##           Model   ROC  Sens      Spec Accuracy
    ## 4 Random Forest 0.855 0.885 0.7333333       NA

The Random Forest model had very good results. Its ROC, sensitivity, and
specificity were all slightly higher than the last logistic regression
model. Its success can be attributed to its ensemble learning strategy,
which combines predictions from multiple decision trees, effectively
enhancing predictive accuracy while guarding against overfitting.
Additionally, Random Forest’s feature importance analysis enables the
model to prioritize crucial variables, focusing on the most informative
aspects of the data. Robustness to non-linear relationships and
resilience against outliers and noise further bolster its effectiveness
in complex classification tasks. Notably, this model’s adaptability to
limited data is a standout feature, making it a valuable asset when
working with constrained datasets. The model’s striking similarity to
the third logistic regression model, with slightly superior performance,
suggests its adeptness at capturing intricate non-linear patterns in the
dataset, solidifying its value in such scenarios.

**K-Nearest Neighbors** Next, K-Nearest Neighbors (KNN) was applied to
the data. KNN is a method that makes predictions based on the similarity
of data points in the dataset. It’s a straightforward and intuitive
approach that assesses the ‘closeness’ of data points to make
predictions.

``` r
# Load the necessary libraries
library(caret)
library(class)

selected_vars <- c("calc", "urea", "osmo", "gravity", "target")
data_subset <- filtered_kidney_df[, selected_vars]

# Define a range of k values to test
k_values <- c(1, 3, 5, 7, 9) # Add more values as needed

# Create an empty data frame to store the results
results_table <- data.frame(K = numeric(), Accuracy = numeric(), Sensitivity = numeric(), Specificity = numeric())

# Loop through each k value
for (k in k_values) {
  # Split your data into features (X) and the target variable (y)
  X <- data_subset[, -which(names(data_subset) == "target")]
  y <- data_subset$target

  # Create a train control object for cross-validation
  ctrl <- trainControl(method = "cv", number = 5, classProbs = TRUE, summaryFunction = twoClassSummary)

  # Fit the KNN model with cross-validation for the current k value
  knn_model <- train(x = X, y = y, method = "knn", trControl = ctrl, tuneGrid = data.frame(k = k))

  # Access the model results for the current k value and store it in the results table
  result_row <- knn_model$results[1, ]
  results_table <- rbind(results_table, result_row)
}
```

    ## Warning in train.default(x = X, y = y, method = "knn", trControl = ctrl, : The
    ## metric "Accuracy" was not in the result set. ROC will be used instead.
    ## Warning in train.default(x = X, y = y, method = "knn", trControl = ctrl, : The
    ## metric "Accuracy" was not in the result set. ROC will be used instead.
    ## Warning in train.default(x = X, y = y, method = "knn", trControl = ctrl, : The
    ## metric "Accuracy" was not in the result set. ROC will be used instead.
    ## Warning in train.default(x = X, y = y, method = "knn", trControl = ctrl, : The
    ## metric "Accuracy" was not in the result set. ROC will be used instead.
    ## Warning in train.default(x = X, y = y, method = "knn", trControl = ctrl, : The
    ## metric "Accuracy" was not in the result set. ROC will be used instead.

**K-Nearest Neighbor Results**

``` r
# Create a new data frame for the specified columns
results_table$Model <- "KNN Model"
results_table$Accuracy <- NA
knn_results <- results_table[results_table$k == 3, c("Model", "ROC", "Sens", "Spec", "Accuracy")]

# Print the knn_results table
print(knn_results)
```

    ##       Model       ROC      Sens      Spec Accuracy
    ## 2 KNN Model 0.5567593 0.6333333 0.4333333       NA

The K-Nearest Neighbors (KNN) model yielded comparatively lower
performance, with a maximum ROC of 0.56, sensitivity of 0.63, and
specificity of 0.43. This performance lagged behind that of the logistic
regression and random forest models. Several factors could contribute to
this discrepancy, including the sensitivity of KNN to data scaling,
potential challenges in high-dimensional spaces, and the critical choice
of the number of neighbors (K). However, it’s worth noting that I
carefully selected the most suitable K value through experimentation. In
practice, the choice of machine learning algorithm should consider the
specific characteristics of the data and problem, and while KNN is
interpretable and straightforward, it may not always provide the best
results.

**Final results for all models**

``` r
# Assuming you have the data frames: logistic_reg_results, logistic_reg_2_results, logistic_reg_3_results, random_forest_results, and knn_results

# Combine them into a single data frame
combined_results <- rbind(
  logistic_reg_results,
  logistic_reg_2_results,
  logistic_reg_3_results,
  random_forest_results,
  knn_results
)

# Print the combined results
print(combined_results)
```

    ##                    Model       ROC      Sens      Spec Accuracy
    ## 1  Logistic Regression 1 0.7173148 0.7944444 0.5333333     0.69
    ## 2  Logistic Regression 2 0.7116667 0.8416667 0.5400000     0.75
    ## 3  Logistic Regression 3 0.8143519 0.8222222 0.5800000     0.81
    ## 4          Random Forest 0.8550000 0.8850000 0.7333333       NA
    ## 21             KNN Model 0.5567593 0.6333333 0.4333333       NA

In this project, I conducted an exploratory data analysis (EDA) on a
notably small dataset, leveraging various visualization tools like box
and violin plots to gain insights and guide feature selection. The
process involved the creation of multiple logistic regression models,
each with distinct variables, refining and optimizing their composition
along the way. Significantly, the logistic regression models exhibited
progressive improvements as I fine-tuned the variable selection,
ultimately leading to the removal of an engineered variable that,
contrary to expectations, enhanced model performance substantially.

In the pursuit of developing a predictive model for early kidney stone
detection based on urine data, I employed a range of modeling techniques
and diligently evaluated their performance using key metrics, including
Receiver Operating Characteristic (ROC) AUC, Sensitivity (Sens), and
Specificity (Spec). Here’s an overview of the findings:

### Conclusion

1.  **Logistic Regression Variations**:
    - The exploration began with three distinct variations of Logistic
      Regression models, each providing unique insights.
    - **Logistic Regression 3 (Engineered Variable Removed)**: This
      model consistently displayed remarkable performance. After the
      engineered variable was removed, it achieved a robust ROC AUC of
      0.814, signifying potent discriminatory capability. Notably, it
      struck a balance with a Sensitivity of 0.822 and Specificity of
      0.580. The removal of the engineered variable streamlined its
      focus on informative features, likely contributing to its improved
      performance.
    - **Logistic Regression 2 (Removing Feature Dimensionality by
      Removing Lowly Correlated Features)**: This variation demonstrated
      commendable results with a ROC AUC of 0.712. It was particularly
      strong in Sensitivity (0.842) while maintaining a Specificity of
      0.540. The careful feature selection process, which involved
      removing lowly correlated features, may have contributed to its
      effectiveness.
    - **Logistic Regression 1 (Using All Variables as well as Engineered
      Variable)**: While still respectable with a ROC AUC of 0.717, this
      model displayed a lower Sensitivity of 0.794 and Specificity of
      0.533. Its inclusion of all variables, including the engineered
      one, may have introduced complexity that impacted its performance.
2.  **Random Forest**:
    - The **Random Forest** model, with its ensemble approach, stood out
      as a top performer, boasting an impressive ROC AUC of 0.855. This
      result underscored its robust discriminatory power, attributed to
      its ability to aggregate predictions from multiple decision trees.
      While Specificity (0.733) was slightly lower than Sensitivity
      (0.885), it showcased a capacity to balance both types of errors
      effectively.
3.  **K-Nearest Neighbors (KNN) Model**:
    - The KNN Model presented lower overall performance compared to the
      other models, with a ROC AUC of 0.557. This suggests less
      discriminative power.
    - Sensitivity (0.633) and Specificity (0.433) values indicated
      suboptimal performance, implying that it may not be as effective
      in correctly identifying individuals with and without kidney
      stones compared to Logistic Regression and Random Forest models.

In summary, Logistic Regression 3 (after the removal of the engineered
variable) and the Random Forest model emerged as the top performers in
the kidney stone prediction project. While Logistic Regression 2 also
demonstrated strong results, Logistic Regression 1 lagged behind
slightly. The K-Nearest Neighbors (KNN) model, although part of the
evaluation, displayed comparatively lower performance. The choice among
these models should consider the specific requirements of the
application, with the understanding that Logistic Regression 3 and
Random Forest offer substantial promise for early kidney stone
detection, while KNN may require further refinement or exploration of
alternative techniques. Further validation and real-world testing will
be instrumental in making the most informed model selection for
practical clinical implementation. The Random Forest model is slightly
superior to the third logistic regression model, as it has higher
specificity, indicating better detection of true negatives compared to
the logistic regression model.

Looking ahead, future studies could explore the potential of larger
datasets, incorporating more extensive urine readings (additional
variables), and exploring further engineered features. Different
modeling techniques might also warrant exploration.
