# Descriptive statistics


## Frequency table

Making frequency tables for a certain column, `var` in python

```python
df['var'].describe()
df['var'].value_counts()

# count 
var_summary = (
    df['var']
    .value_counts(normalize = False, dropna = False)
    .rename('Count')
    .to_frame()
)

# percentage
var_summary['pct'] = (
    df['var']
    .value_counts(normalize = True, dropna = False)*100
).round(2)

# cumulative percentage
var_summary['cum'] = var_summary['pct'].cumsum()

# print data
print(var_summary.head())
```

## Descriptive table
We have now questions such as
- Q005_1-Q005_25: Has the item or not (Ja/Nei)
- Q006_1-Q006_25: If not, cannot afford it (Ja/Nei)
- Make descriptive table based on these questions, show N and % of people have (not have) item, and N and % people can't afford them.



```python

# Items, N% Have/Not have, and N% can't afford

valid_items = [i for i in range(1, 25) if i not in [18, 12]]

# Make two types of tables
summary_row = []  # first table show all columns
summary_row_compact = [] # second table more compact

# define the table
for i in valid_items:
    q005 = f"Q005_{i}"
    q006 = f"Q006_{i}"

    # count
    valid_mask = df[q005].isin(['Ja', 'Nei']) # only keep Ja and Nei, drop other answers.

    n_total = valid_mask.sum() # Total number respondents 
    n_have = (df.loc[valid_mask, q005] == 'Ja').sum() # total number have the item
    n_not_have = (df.loc[valid_mask, q005] == 'Nei').sum() # total number not have the item
    n_cant_afford = ( (df[q005] == 'nei') & (df[q006] == 'Ja') ).sum() # who dont have it because they can't afford it

    # percentage
    pct_have = n_have / n_total * 100 if n_total > 0 else np.nan
    pct_not_have = n_not_have / n_total * 100 if n_total > 0 else np.nan
    pct_cant_afford = n_cant_afford / n_total * 100 if n_total > 0 else np.nan

    # now add names for the rows
    summary_row.append({
        'Item': f'Q005_{i}',
        'N Have': n_have,
        '% Have': pct_have,
        "N Don't have": n_not_have,
        "% Don't have": pct_not_have,
        "N Can't afford": n_cant_afford,
        "% Can't afford": pct_cant_afford,
        'N total': n_total
    })

    summary_row_compact.append({
        'Item ': f"Q005_{i}",
        "Have (N, %)": f"{n_have} ({pct_have:.2f}%)",
        "Don't have (N, %)": f"{n_not_have} ({pct_not_have:.2f}%)",
        "Can't afford (N, %)": f"{n_cant_afford} ({pct_cant_afford:.2f}%)",
        "N Total": n_total
    })

# Now make them to dataframes
summary_df = pd.DataFrame(summary_row)
summary_compact_df = pd.DataFrame(summary_row_compact)
```

### The same table, but for sub-data

If condition on some columns, or select part of the data and make the same table, we can make a function instead of running everything over and over again.

Here is a function:

```python

# define a function
def item_stat(sub_df):
    rows = []
    rows_compact = []

    for i in valid_items:
        q005 = f'Q005_{i}'
        q006 = f'Q006_{i}'

        valid_mask = sub_df[q005].isin(['Ja', 'Nei'])
        n_total = valid_mask.sum()

        n_have = (sub_df.loc[valid_mask, q005] == 'Ja').sum()
        n_not_have = (sub_df.loc[valid_mask, q005] == 'Nei').sum()
        n_cant_afford = ((sub_df[q005] == 'Nei') & (sub_df[q006] == 'Ja')).sum()

        pct_have = n_have / n_total * 100 if n_total > 0 else np.nan
        pct_not_have = n_not_have / n_total * 100 if n_total > 0 else np.nan
        pct_cant_afford = n_cant_afford / n_total * 100 if n_total > 0 else np.nan

        rows.append({
            'Item': f"Q005_{i}",
            "N Have": n_have,
            "% Have": pct_have,
            "N Don't have": n_not_have,
            "% Don't have": pct_not_have,
            "N Can't afford": n_cant_afford,
            "% Can't afford": pct_cant_afford,
            "N total": n_total
         })

        rows_compact.append({
            'Item': f'Q005_{i}',
            'Have (N, %)': f'{n_have} ({pct_have:.2f}%)',
            "Don't have (N, %)": f'{n_not_have} ({pct_not_have:.2f}%)',
             "Can't afford (N, %)": f"{n_cant_afford} ({pct_cant_afford:.2f}%)",
            "N Total": n_total
        })

    return pd.DataFrame(rows), pd.DataFrame(rows_compact) 

```

#### Use the function to look at subdata: child vs nochild

Now, define the sub-dataset. 

For example, we define families with and without children:

```python

# for child familie:
child_mask = df['hhtype'].isin({
    "Jeg bor sammen med ektefelle / samboer og barn",
    "Jeg bor sammen med mine barn"
])

# for families without kids:
no_child_mask = ~child_mask
```

Apply the sub-data into the function:
```python
# summary table and compact summary table for families with kids:
summary_child, summary_child_compact = item_stat(df[child_mask])

# summary table and compact summary table for families without kids:
summary_nochild, summary_nochild_compact = item_stat(df[no_child_mask])

# Can merge data (child vs nochild)
summary_hhtype = summary_child.merge(
    summary_nochild,
    on = 'Item',
    suffixes =(", children", ", nochildren")
    )

# Merge compact data (child vs no child)
summary_hhtype_compact = summary_child_compact.merge(
    summary_nochild_compact,
    on = 'Item',
    suffixeds = (", children", ", nochildren")
```

#### use the function of poor vs rich

First, split by rich vs poor families

```python

# order the income categories:
income_order = [
    'Under 200.000 kroner',
    '200.000 - 399.999 kroner',
    '400.000 - 599.999 kroner',
    '600.000 - 799.999 kroner',
    '800.000 - 999.999 kroner',
    '1.000.000 - 1.199.999 kroner',
    '1.200.000 – 1.399.999 kroner',
    '1.400.000 kroner eller mer'
]

# Create a mapping from category to rank (starting from 1)
income_rank_map = {category: rank +1 for rank, category in enumerate(income_order)}

# Create new numeric column based on this mapping
df['hhinc_num'] = df['hhinc'].map(income_rank_map)

# decide where to cut in this continuous variable
df['hhinc_num'].quantile([.25, .5, .75])

# define the first three as low income
poor_mask = df['hhinc_num].isin([
    'Under 200.000 kroner',
    '200.000 - 399.999 kroner',
    '400.000 - 599.999 kroner'
])
rich_mask = ~poor_mask
```

After this subdata criteria is created, we can now apply the function
```python

# apply function
summary_poor, summary_poor_compact = item_stat(df[poor_mask])
summary_rich, summary_rich_compact = item_stat(df[rich_mask])

# merge the compact dataset poor vs rich
summary_hhinc_compact = summary_poor_compact.merge(
    summary_rich_compact,
    on = 'Item',
    suffixes = (", poor", ", rich")
```


















