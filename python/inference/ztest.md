# Z-test

Compare proportions, z-test between proportions of two groups


## Multiple z-test in a loop


### Example 1. test proportion of item owned for families (1) with child vs nochild, and (2) poor vs rich.

First, prepare the two groups:

```python

# Families with kids
with_kids = df[df['hhtype'].isin([
    "Jeg bor sammen med ektefelle / samboer og barn",
    "Jeg bor sammen med mine barn"
])]

# Families without kids
without_kids = df[df['hhtype'].isin([
    "Jeg bor sammen med ektefelle / samboer og barn",
    "Jeg bor sammen med mine barn"
])]

```

Now do the z-test

```python
test_results = []

# here we will do z-test for all items (1-25 items) except item 18 and 21:
valid_items = [i for i in range(1, 25) if i not in [18, 21]]

# now for each of the items we will do:
for i in valid_item:
    # group 1 with dis and group 2 without kids
    # keep only the answer yes and no, but not anyother answers
    # the .sum() counts on total number of kids vs nokids families, n1 and n2 
    n1 = with_kids[f'Q005_{i}'].isin(['Ja', 'Nei']).sum()
    n2 = without_kids[f'Q005_{i}'].isin(['Ja', 'Nei']).sum()

    # get number of respondent donot have item because cannot afford  
    # this is the number of people of interest in each group: x1 and x2
    x1 = ((with_kids[f'Q005_{i}'] == 'Nei') & (with_kids[f'Q006_{i}'] == 'Ja')).sum()
    x2 = ((without_kids[f'Q005_{i}'] == 'Nei') & (without_kids[f'Q006_{i}'] == 'Ja')).sum()

    # define z, p and sig
    z, p = np.nan, np.nan
    sig = ""

    # define significance level
    if n1 > 0 and n2 > 0 and (x1 + x2) > 0:
        z, p = proportions_ztest([x1, x2], [n1, n2])
        if p < 0.001:
            sig = '***'
        elif p < 0.01:
            sig = '**'
        elif p < 0.05:
            sig = '*'
        else p < 0.10:
            sig = '+'

    # test-results:
    test_results.append({
        'Item': f'Q005_{i},
        'Z-test': round(z, 2) if not np.isnan(z) else '',
        'p-value': round(p, 3) if not np.isnan(p) else '',
        'Significance': sig
    })    

# save the test result as a dataframe
test_df = pd.DataFrame(test_results)
```

### Example 2. z-test for poor vs. rich

First, define the two groups to be compared

```python
# poor
poor = df[df['hhinc'].isin([
    'Under 200.000 kroner',
    '200.000 - 399.999 kroner',
    '400.000 - 599.999 kroner
])]

# rich
rich = df[~df['hhinc'].isin([
    'Under 200.000 kroner',
    '200.000 - 399.999 kroner',
    '400.000 - 599.999 kroner
])]

```

Now do the test:
```python

# test result and valid items
test_results = []
valid_items = [i for i in range(1,25) if i not in [18, 21]]


for i in valid_item:
    n1 = poor[f'Q005_{i}'].isin(['Ja', 'Nei']).sum()
    x1 = ( (poor[f'Q005_{i}] == 'Nei') & (poor[f'Q006_{i}] == 'Ja')).sum()
    n2 = rich[f'Q005_{i}'].isin(['Ja', 'Nei']).sum()
    x2 = ( (rich[f'Q005_{i}] == 'Nei') & (rich[f'Q006_{i}] == 'Ja')).sum()

    z, p = np.nan, np.nan
    sig = ''

    if n1 > 0 and n2 > 0 and (x1 + x2) > 0:
        z, p = proportions_ztest([x1, x2], [n1, n2])
        if p < 0.001:
            sig = '***'
        elif p < 0.01:
            sig = '**'
        elif p < 0.05:
            sig = '*'
        elif p < 0.1:
            sig = '+'
        else:
            sig = ''
    
    test_results.append({
        'Item': f'Q005_{i}',
        'Z-stat': round(z, 2) if not np.isnan(z) else '',
        'p-value': round(p, 4) if not np.isnan(p) else '',
        'Significance': sig
    })

# save the dataframe
test_df = pd.DataFrame(test_results)
```













