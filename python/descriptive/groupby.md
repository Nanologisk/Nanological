# Using of `groupby()`


## Calculate the meanscore based on groups

Example: Mean deprivation scores by income groups

```
grouped = df.groupby('hhinc, as_index=False)['dep_score'].mean()
```

Mean deprivation score by both income groups, and if people have or not have child

```
grouped_childinc = df.groupby(['child', 'hhinc'], observed=True)['dep_score'].mean().reset_index()
```

## First calcualte the meanscore across groups, then visualise this

```
# mean deprivation scores by group
grouped = df.grouppy('hhinc, as_index=False)['dep_score'].mean()
grouped_childinc = df
                    .groupby(['child', hhinc'], observed=True)['dep_score']
                    .mean()
                    .reset_index()

# Define color palette for child vs no child
palette = {
    'Har barn': 'Red',
    'Ikke barn': 'Blue'
}

# Line plot with scatters
fig, ax = plt.subplots()

# show plot generally not considering child vs no child
sns.scatterplot(data=grouped, x='hhinc', y='dep_score', s=80, color='black', legend=False)
sns.lineplot(data=grouped, x='hhinc', y='dep_score', color='black', label='Samlet')

# show plot for child vs nochild families
sns.scatterplot(data=grouped_childinc, x='hhinc', y='dep_score', hue='child',
                palette=palette, ax=ax, s=80, legend=False)
sns.lineplot(data=grouped_childinc, x='hhinc', y='dep_score', hue='child',
             palette=palette, ax=ax

# more configuration for the plot             
plt.title('Deprivasjonsskår og husholdningsinntket)
plt.xlable('')
plt.ylabel('Gjennomsnittlig deprivasjonsskår')
plt.xticks(rotation=90)
plt.legend(title='')
plt.tight_layout()
plt.savefig('path/path/name.png', dpi=300)
plt.show()
```
The codes above will show figure look like:

![Figure example here](/home/nan/OneDrive/SIFO/SIFO2025/familiefattigdom/out/dep23_inc_child.png)
