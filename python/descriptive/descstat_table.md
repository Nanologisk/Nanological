# Make descriptive statistics table

## Read in data and import packages

```python
import pandas as pd
import numpy as np

# read in data
dat = pd.read_pickle('data/sosialhjelp_clean.pkl')

# keep useful columns:
desc_col = ['deprivation', 'deprivation1', 'deprivation2', 'deprivation_child', 'deprivation_food',
            'age', 'gender',
            'educ', 'hhinc', 'hhtype', 'hhsize',
            'num_children', 'age_youngest_child',
            'Q17B'] # Q17B: hhinc

# keep data
df = dat[desc_col]
```


## Get dummies from categorical variables

Three categorical variables: education, household types and household income:

```python

dummies = pd.get_dummies(df[['educ', 'hhtype', 'Q17B']]).astype(int)
df_dummies = pd.concat([df, dummies], axis=1)
df_dummies = df_dummies.drop(['Q17B'], axis=1)

```


## Make a list of colums to include in descriptive statistics

Make a list of variables to income 
- list of exclusion variables (missings for `hhinc` colums, etc.)
- list of household income variables (get from dummies)
- list of household type variables (get from dummies)
- list of education variables (get from dummies)
- list of more specified variables 

```python

# lists
exclude_cols = ['Q17B_Vet ikke', 'Q17B_Vil ikke oppgi', 'educ']
hhinc_cols = [col for col in df_dummies.columns if col.startswith('Q17B_') if col not in exclude_cols]
hhtype_cols = [col for col in df_dummies.columns if col.startswith('hhtype_')]
educ_cols = [col for col in df_dummies.columns if col.startswith('educ')]

cols = (['deprivation', 'deprivation1', 'deprivation2', 'deprivation_child', 'deprivation_food',
        'age', 'gender'] + educ_cols + ['hhinc']
        + hhinc_cols +  hhtype_cols +
        ['hhsize', 'num_children', 'age_youngest_child'])

cols = [col for col in cols if col not in exclude_cols]

# check types of columns - are they are numeric?
df_dummies[cols].dtype

```


## Create dataframe of descriptive statistics

To create a dataframe:
* 1. define measures that would be included in empty lists
    * Here i use `max_` and `min_` to avoid conflicts with python built-in functions `max()` and `min()`
* 2. use `.append()` to avoid overwriting the measures in each loop iteration
* 3. use `pd.DataFrame()` to make a dataframe.


```python
mean, std, max_, min_, n = [], [], [], [], []

for col in cols:
    mean.append(round(df_dummies[col].mean(), 2))
    std.append(round(df_dummies[col].std(), 2))
    max_.append(int(round(df_dummies[col].max(), 0)))
    min_.append(int(round(df_dummies[col].min(), 0)))
    n.append(int(round(df_dummies[col].count(), 0)))

df_desc = pd.DataFrame({
    'var': cols,
    'mean': mean,
    'std': std,
    'max_': max_,
    'min_': min_,
    'n': n
})

# check dataframe 
df_desc.head()
```

## A much easier way!!

Use `.describe()` and get the statistics by `df_dummies[col].descirbe().T`.
- Index: column names (`cols`)
- Columns: `count`, `mean`, `std`, `min`, `25%`, `50%`, `75%`, `max`

```python
df_dummies[cols].describe().T[['mean', 'std', 'max', 'min', 'count']]

```

And this gives:
``` yaml

# a easier way!
df_dummies[cols].describe().T[['mean', 'std', 'max', 'min', 'count']]

# this igves:
                                   mean        std   max   min   count
deprivation                    0.475559   0.330773   1.0   0.0  1496.0
deprivation1                   0.486821   0.349809   1.0   0.0  1494.0
deprivation2                   0.463735   0.340255   1.0   0.0  1489.0
deprivation_child              0.333942   0.329887   1.0   0.0   801.0
deprivation_food               0.387082   0.354517   1.0   0.0  1334.0
age                           41.755791  11.873753  79.0  18.0  1511.0
gender                         0.480027   0.499767   1.0   0.0  1502.0
educ_Other                     0.010589   0.102391   1.0   0.0  1511.0
educ_Vocational                0.233620   0.423273   1.0   0.0  1511.0
educ_Elementary                0.062210   0.241617   1.0   0.0  1511.0
educ_Master+                   0.186631   0.389744   1.0   0.0  1511.0
educ_Bachelor                  0.258107   0.437738   1.0   0.0  1511.0
educ_High School               0.248842   0.432485   1.0   0.0  1511.0
hhinc                          7.315869   3.693685  14.0   1.0  1317.0
Q17B_1.000.001-1.100.000 kr.   0.052945   0.223998   1.0   0.0  1511.0
Q17B_1.100.001-1.200.000 kr    0.046989   0.211685   1.0   0.0  1511.0
Q17B_1.200.001-1.300.000 kr.   0.046989   0.211685   1.0   0.0  1511.0
Q17B_100.000-200.000 kr.       0.027134   0.162529   1.0   0.0  1511.0
Q17B_200.001-300.000 kr.       0.098610   0.298237   1.0   0.0  1511.0
Q17B_300.001-400.000 kr.       0.107214   0.309487   1.0   0.0  1511.0
Q17B_400.001-500.000 kr.       0.093977   0.291894   1.0   0.0  1511.0
Q17B_500.001-600.000 kr.       0.086698   0.281484   1.0   0.0  1511.0
Q17B_600.001-700.000 kr.       0.062872   0.242813   1.0   0.0  1511.0
Q17B_700.001-800.000 kr.       0.056254   0.230488   1.0   0.0  1511.0
Q17B_800.001-900.000 kr.       0.057578   0.233020   1.0   0.0  1511.0
Q17B_900.001-1.000.000 kr.     0.051621   0.221335   1.0   0.0  1511.0
Q17B_Over 1.300.000 kr         0.064858   0.246356   1.0   0.0  1511.0
Q17B_Under 100.000 kr.         0.017869   0.132519   1.0   0.0  1511.0
hhtype_Alone                   0.228987   0.420320   1.0   0.0  1511.0
hhtype_Alone with children     0.123759   0.329416   1.0   0.0  1511.0
hhtype_Couple no children      0.154203   0.361262   1.0   0.0  1511.0
hhtype_Couple with children    0.428855   0.495076   1.0   0.0  1511.0
hhtype_Other                   0.007280   0.085040   1.0   0.0  1511.0
hhtype_Shared apartment        0.017869   0.132519   1.0   0.0  1511.0
hhtype_With parents            0.034414   0.182351   1.0   0.0  1511.0
hhsize                         3.200516   1.013381   5.0   1.0  1162.0
num_children                   0.926959   0.985599   4.0   0.0  1506.0
age_youngest_child             8.720189   5.198277  19.0   1.0   847.0

```


