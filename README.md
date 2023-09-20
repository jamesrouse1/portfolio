# James Rouse's Data Science Portfolio

Welcome to my data science portfolio! This repository showcases two of my data science projects, each addressing unique and interesting challenges. Below, you'll find a brief overview of each project along with the libraries used in Python and R.


## Project 1: Predicting Player Performance in Baseball (Python)

### Overview
**Description:** In this project, I focused on predicting player performance in baseball, particularly their ability to drive in runs (RBIs). I collected hitting data for individual players and conducted exploratory data analysis (EDA) to understand the relationships between player statistics and RBIs.

### Libraries Used
- **Python:**
  - `pandas`: For data manipulation and analysis.
  - `numpy`: For numerical operations and array handling.
  - `matplotlib` and `seaborn`: For data visualization.
  - `scikit-learn`: For machine learning models, including Random Forest and Linear Regression.

### Key Highlights
- Identified the dominant role of home runs in predicting RBIs using machine learning and regression models.
- Explored the complexity of on-base percentage (OBP) and its impact on RBIs.
- Encouraged future research by suggesting avenues like incorporating additional variables and exploring new dimensions of player performance.

### Project Goal
The project aims to provide insights into the factors influencing a player's ability to contribute to offensive success in baseball, specifically in terms of RBIs.

## Data
The data for this project is located in the batting folder of this repository. The module pybaseball was also used to obtain data.


## How to View Project

### IPython Notebook (ipynb)

To view the IPython Notebook and its rendered content, simply click on the file:

- [predicting_rbis.ipynb](rbi_project/predicting_rbis.ipynb)

GitHub will automatically render the IPython Notebook, allowing you to see the code, visualizations, and markdown cells directly in your browser.

## Real-World Applications
## Project 2: Kidney Stone Prediction (R)

### Overview
**Description:** This project focuses on predicting kidney stone presence based on urine data. Kidney stones are a painful health issue, and early detection can be crucial. I collected and cleaned a dataset containing urine characteristics and kidney stone diagnoses. After data cleaning, I conducted exploratory data analysis (EDA) to gain insights into the data's characteristics.

### Libraries Used
- **R:**
  - `dplyr`: For data manipulation.
  - `tidyverse`: Includes various packages (e.g., `ggplot2`, `dplyr`, `tidyr`) for data manipulation and visualization.
  - `caret`: For machine learning model training and evaluation.

### Key Highlights
- Utilized data visualization techniques such as box plots and violin plots to visualize variations in urine characteristics.
- Employed logistic regression for binary classification, emphasizing model evaluation metrics like accuracy, specificity, sensitivity, and ROC curves.
- Introduced K-Nearest Neighbors (KNN) for predictive modeling.
- Emphasized clear and informative data visualization throughout the project.

## Data
The data for this project is in the file kidney_stone.csv, which is located in this repository. 

### Project Goal
The primary goal of this project is to create a practical tool for early kidney stone detection and intervention.

### R Markdown (Rmd)

To view the R Markdown project and its output, you'll need to download the HTML file:

1. Navigate to the html file:
   - [kidney_stone_detection.html](kidney_stone_project/kidney_stone_detection.html)

2. Click on the file name to open it.

3. In the top right corner of the R Markdown file view, you'll find a "Download" button. Click it to download the HTML file.

4. Once downloaded, open the HTML file in your web browser to view the project's content and visualizations.


# Real-World Application
- Kidney Stone Prediction: Early detection and intervention for kidney stones.
- Baseball Player Performance: Informing player acquisition decisions for baseball teams seeking offensive firepower.

Feel free to explore each project folder for more details, code, and results.

Thank you for visiting my data science portfolio!
