# Recoding


## Mapping: change labels to numbers

Use household income as an example

```

# First make mapping

hhinc_map = {
    'Under 200.000 kroner': 1,
    '200.000 - 399.999 kroner': 2,
    '400.000 - 599.999 kroner': 3,
    '600.000 - 799.999 kroner': 4,
    '800.000 - 999.999 kroner': 5,
    '1.000.000 - 1.199.999 kroner': 6,
    '1.200.000 – 1.399.999 kroner': 7,
    '1.400.000 kroner eller mer': 8,
    'Ønsker ikke å svare': np.nan
}

# Now change the original to another new columns, 'hhinc':

df['hhinc'] = df['householdincome'].map(hhinc_map)


```

## Mapping and choose only some values and leave the others

Use household type as an example, where two of the original values are coded as having children, other as not having children

- first use `isin()`, than `map()`

```

df['child'] = df['hhtype'].isin([
    'Jeg bor sammen med ektefelle / samboer og barn',
    'Jeg bor sammen med mine barn'
]).map({
    True: 'Har barn',
    False: 'Ikke barn'
})    

```

