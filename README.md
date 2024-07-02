# dataviz-challenge

## Intro
You've just joined Pymaceuticals, Inc., a new pharmaceutical company that specializes in anti-cancer medications. Recently, it began screening for potential treatments for squamous cell carcinoma (SCC), a commonly occurring form of skin cancer. In this study, 249 mice who were identified with SCC tumors received treatment with a range of drug regimens. Over the course of 45 days, tumor development was observed and measured. The purpose of this study was to compare the performance of Pymaceuticals’ drug of interest, Capomulin, against the other treatment regimens.

## Prepare the Data
1. Run the provided package dependency and data imports, and then merge the mouse_metadata and study_results DataFrames into a single DataFrame.
```
mouse_metadata_path = "data/Mouse_metadata.csv"
study_results_path = "data/Study_results.csv"

mouse_metadata = pd.read_csv(mouse_metadata_path)
study_results = pd.read_csv(study_results_path)
```
2. Display the number of unique mice IDs in the data, and then check for any mouse ID with duplicate time points. Display the data associated with that mouse ID, and then create a new DataFrame where this data is removed. Use this cleaned DataFrame for the remaining steps.
```
ms_df = pd.merge(mouse_metadata, study_results, how='left', on=['Mouse ID'])
```
## Generate Summary Statistics
For this challenge I included two different ways to create a Dataframe that includes:
- A row for each drug regimen. These regimen names should be contained in the index column.
- A column for each of the following statistics: mean, median, variance, standard deviation, and SEM of the tumor volume.

## Create Bar Charts and Pie Charts
1. Generate two bar charts. Both charts should be identical and show the total total number of rows (Mouse ID/Timepoints) for each drug regimen throughout the study. Once chart is generated using Pandas Dataframe.plot() method and the other is using Matplotlib pyplot method.
2. Generate two pie charts. Both charts should be identical and show the distribution of female versus male mice in the study. Again using a Pandas method and then the other a matplotlib method.

## Calculate Quartiles, Find Outliers, and Create a Box Plot
1. Calculate the final tumor volume of each mouse across four of the most promising treatment regimens: Capomulin, Ramicane, Infubinol, and Ceftamin. Then, calculate the quartiles and IQR, and determine if there are any potential outliers across all four treatment regimens. Use the following substeps:
  - Create a grouped DataFrame that shows the last (greatest) time point for each mouse. Merge this grouped DataFrame with the original cleaned DataFrame.
```
mouse_ids = list(clean_df['Mouse ID'].unique())
target_drugs = ['Capomulin', 'Ramicane', 'Infubinol', 'Ceftamin']
max_vol = []
for ID in mouse_ids:
    max_mouse = clean_df.loc[clean_df['Mouse ID']==ID, :]
    max_time = max_mouse.loc[(max_mouse['Timepoint'].idxmax()), :]
    max_vol.append(max_time['Tumor Volume (mm3)'])

tumor_df = pd.DataFrame({
                        'Mouse ID': mouse_ids,
                        "Final Tumor Vol": max_vol
                        }
                       )


# Merge this group df with the original DataFrame to get the tumor volume at the last timepoint
merge_df = pd.merge(clean_df, tumor_df, on='Mouse ID', how='inner').reset_index()
```
  - Create a list that holds the treatment names as well as a second, empty list to hold the tumor volume data.
  - Loop through each drug in the treatment list, locating the rows in the merged DataFrame that correspond to each treatment. Append the resulting final tumor volumes for each drug to the empty list.
  - Determine outliers by using the upper and lower bounds, and then print the results.
```
treatments = ['Capomulin', 'Ramicane', 'Infubinol', 'Ceftamin']
# Create empty list to fill with tumor vol data (for plotting)
fin_tum_list = []
for treat in treatments:
    drug_df = merge_df.loc[merge_df['Drug Regimen']==treat, :]
    fin_tum_list.append(drug_df['Final Tumor Vol'])
    
    quartiles = drug_df['Final Tumor Vol'].quantile([.25, .5, .75])
    lowerq = quartiles[0.25]
    upperq = quartiles[0.75]
    iqr = upperq-lowerq
    lower_bound = lowerq - (1.5*iqr)
    upper_bound = upperq + (1.5*iqr)
    print(f'Values below {round(lower_bound,3)} for {treat} could be outliers')
    print(f'Values above {round(upper_bound, 3)} for {treat} could be outliers')
    outliers = drug_df.loc[(drug_df['Final Tumor Vol'] > upper_bound) |
                        (drug_df['Final Tumor Vol'] < lower_bound)]
    if len(outliers)==0:
        print(f'There are no outliers for {treat}')
            
    else:
        print(f'{treat} has {len(outliers)} outliers')
```
2. Using Matplotlib, generate a box plot that shows the distribution of the final tumor volume for all the mice in each treatment group. Highlight any potential outliers in the plot by changing their color and style.

## Create a Line and Scatter plot then Calculate Correlation and Regression
1. Select a single mouse that was treated with Capomulin, and generate a line plot of tumor volume versus time point for that mouse.
2. Generate a scatter plot of mouse weight versus average observed tumor volume for the entire Capomulin treatment regimen.
3. Calculate the correlation coefficient and linear regression model between mouse weight and average observed tumor volume for the entire Capomulin treatment regimen.
4. Plot the linear regression model on top of the previous scatter plot.




definitely used the pandas documentation page a lot and found out about the maxidx function from a similar project that was on github heres the link for that: https://mratx.github.io/Matplotlib_Challenge/

