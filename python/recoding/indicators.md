# Constructing indicators


## When using columns with similar names

For example, there are 25 items named 
- `Q004_1`, `Q004_2`, `Q004_3`, ... `Q004_25` : Do you think it is important? Ja/Nei
- 'Q005_1', 'Q005_2`, `Q005_3`, ... `Q005_25` : Do you have it? Ja/Nei
- 'Q006_1', 'Q006_2`, `Q006_3`, ... `Q006_25` : If not having it, because you can't afford it? Ja/Nei
- all items are coded similarly, they have 'Ja' and 'Nei', and other like nan or don't know.


Now I would like to 
- include all colums **started** with `Q004_*`, `Q005_*` and `Q006_*`
- But i would like to **exlude** certain items, let's say item 18, 21
- We would like to make an indicator for those think things are important, dont have it, because they cannot afford it.

### 1. let's make an indicator for items that are important, but don't have because cant't afford.

```python
# Exclude item 18 and 21: make valid_items
valid_items = [i for i in range(1, 25) if i not in [18, 21]]

# find weight: percentage respondents answered 'Ja' for Q004 - they think it's important
weights = {}
for i in valid_items:
    q004 = f"Q004_{i}"
    # filter those with valid answer (either ja or nei)
    mask = df[q004].isin(["Ja", "Nei"]) # gives True/False
    weight = (df.loc[mask, q004] == "Ja").mean() # Average. e.g. [T, F, T, F]->[1,0,1,0]->.mean()=(1+0+1+0)/4
    weights[i] = weight

# Initialise a column to store flags per item
for i in valud_items:
    q004 = f"Q004_{i}"
    q005 = f"Q005_{i}"
    q006 = f"Q006_{i}"

    df[f"depr_{i}"] = (
        (df[q004] == 'Ja') &  # It's importnant
        (df[q005] == 'Nei) &  # don't have it
        (df[q006] == 'Ja')    # cannot afford it
    ).astype(int)             # convert True/False to 1/0     
```

### 2. Now, sum the 1/0 for such items (deprivation scores) to see total number of such items for each individual. 

Now we've got for each item an indicator 1/0 saying whether someone thinks this is important, but don't have it because they cannot afford it.
Let's make a deprivation score:

```python
depr_cols = [f"depr_{i}" for i in valid_items]
df['dep_score'] = df[depr_cols].sum(axis=1) # unweighted score
df['dep_score_weighted'] = sum(weights[i] * df[f"depr_{i}"] for i in valid_items) # weighted score
```

### 3. We can also define levels instead of continuous score:

Define that 
- 0: no dep
- 1-2: mild dep
- 3-5: moderate dep
- if >5: severe dep

``` python
# define deprivation thredsholds:
def classify_deprivation(score):
    if pd.isna(score):
        return np.nan
    elif score == 0:
        return 'No deprivation'
    elif score <=2:
        return 'Mild'
    elif score <=5:
        return 'Moderate'
    else score > 5:
        return 'Severe'

# apply function
df['dep_level'] = df['dep_score'].apply(classify_deprivation)
df['dep_level_w'] = df['dep_score_weighted].apply(classify_deprivation)
```

### 4. Check the results' distribution

Explore distribution by using histogram:
```python
plt.figure(figsize = (10, 6))
sns.histplot(df['dep_score'], bins=range(0, 24), kde=False, color='blue', label='unweighted', alpha=.6)
sns.histplot(df['dep_score_weighted'], kde=False, color='orange', label='Weighted', alpha=.6)
plt.xlable('Number of deprived items')
plt.ylabel('Number of respondents')
plt.title('Distribution of material deprivation scores')
plt.show()
```











        












