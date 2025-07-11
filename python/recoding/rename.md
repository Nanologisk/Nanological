# Rename 

## Rename columns

Example:
```python
df.rename(columns = {
    'kjonnx': 'gender',
    'alder': 'age',
    'persinnt12': 'indinc',
    'antpers': 'hhsize',
    'bolig11': 'hhtype',
    'utd6delt': 'educ'
}, inplace = True)

```

## Select columns and save them

Example:
```python

# some columns to keep
key_columns = ['alder', 'kjonnx', 'livsopph', 'persinnt12', 'utd6delt', 'antpers',
               'husinnt', 'bolig11', 'SRH', 'boligtype', 'boligeieleie', 'bransje',
               'dep_score', 'dep_score_weighted', 'dep_level', 'dep_level_weighted']

# some columns started with the same name
valid_items = [i for i in range(1, 25) if i not in [18, 21]]
q004_cols = [f'Q004_{i}' for i in valid_items]
q005_cols = [f'Q005_{i}' for i in valid_items]
q006_cols = [f'Q006_{i}' for i in valid_items]

# Now keep these columns:
keep_columns = key_columns + q004_cols + q005_cols + q006_cols

# make a new dataframe
df_new = df[keep_columns].copy()

```




