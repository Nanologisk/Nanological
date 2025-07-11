# Coefficient plot


Make function to get results from regressions, making coefficient plots
- Need to specify `name_map`, in which the names of the variables on y-labels will be shown
- Need to specify `blocks`, the name of catetories grouped together, such as demographcis, SES, education, householdtype, etc.
- Need to run regressions to get the parameters/CIs/p-values, etc.
- Need to apply the function and use with concrete model names


## I. A general function for coefplot

### 1. First, define function

```python

def regression_plot(model, blocks, name_map, title=None, save_path=None):
    
    # Prepare dataframe of coefficients, CIs and p-values
    summary_df = pd.DataFrame({
        'coef': model.params,
        'lower_ci': model.conf_int().iloc[: 0],
        'upper_ci': model.conf_int().iloc[: 1],
        'pval': model.pvalues
    }).drop(Intercept, errors = 'ignore')

    # Empty lists to collect plotting data
    labels = []         # y-axis labels (variables names and block titles)
    coefs = []          
    lower_cis = []     
    upper_cis = []      
    pvals = []
    y_positions = []    # vertical positions for each label on y-axis
    y_pos = 0           # counter to track current y position

    # loop throught each block (group of variables)
    for block_name, coefs_list in blocks.items():
        # Add the block title as a fake row with non values
        labels.append(f'**{block_name}**') # add block name with ** to identify a title
        coefs.append(None)
        lower_cis.append(None)
        upper_cis.append(None)
        pvals.append(None)
        y_positions.append(y_pos)           # assign current y position to block title
        y_pos += 1                          # increment y position for next row

        # Add each actual coef from this block of present in the model
        for coef_name in coefs_list:
            if coef_name in summary_df.index:
                # add the name for the variable if availabel, else raw name
                labels.append(name_map.get(coef_name, coef_name))
                coefs.append(summary_df.loc[coef_name, 'coef'])
                lower_cis.append(summary_df.loc[coef_names, 'lower_ci'])
                upper_cis.append(summary_df.loc[coef_names, 'upper_ci'])
                pvals.append(summary_df.loc[coef_names, 'pval'])
                y_positions.append(y_pos)   # vertical position
                y_pos.append(y_pos)         # move to next row

    # Create the figure and axis for plotting
    # Height depends on number of labels (so plot is not cramped)
    fig, ax = plt.subplots(figsize=(8, max(5, len(labels) * 0.45)))

    # Choose colors based on p-values significance
    def get_color(p):
        if pd.isna(p):
            return 'none'       # no colour if p is missing
        elif p < 0.05:
            return 'red'
        elif p < 0.10:
            return 'orange'
        else:
            return 'dimgrey'

    # determine colours for each point/CI line
    colors = [get_color(p) for p in pvals]

    # plot CI as horizontal lines (hlines)
    for y, coef, low, high, color in zip(y_positions, coefs, lower_cis, upper_cis, colors):
        if coef is not None:        # skip block titles (which have None coef)
            ax.hlines(y, xmin=low, xmax=high, color=color, 
                      alpha=0.7, linewidth=1, zorder=1)

    # plot coefficient estimates as points
    for y, coef, low, high, color in zip(y_positions, coefs, lower_cis, upper_cis, colors):
        ax.scatter(coef, y, color = color, s=50, zorder=2)

    # set y labels 
    ax.set_yticks(y_positions) # set y-axis ticks to match the number of lables
    ax.set_yticklabels([label.replace('**', '') for label in labels]) #remove ** from block titles
    ax.invert_yaxis() # reverse y axis, top block title statys on top

    # style bock headers
    for i, label in enumerate(labels): 
        if label.startswith('**'):
            ax.get_yticklabels()[i].set_fontweight('bold')
            ax.get_yticklabels()[i].set_color('black')

    # draw a vertical line at zero for reference
    ax.axvline(0, color='gray', linestyle='--', linewidth=1)
    ax.set_xlabel('Coefficient estimate') # label for x-axis

    # Add plot title
    if title:
        ax.set_title(title)

    # remove top and right plot border 
    ax.spines['top'].set_visible(False)
    ax.spines['right'].set_visible(False)

    plt.tight_layout()      # adjust layout to labels and titles fit without overlap

    if save_path:
        plt.savefig(save_path, bbox_inches='tight', dpi=300)
    
    plt.show()


```

### 2. Run the models

```python

# 1. total household deprivation
model_total = smf.ols(
    'deprivation ~ age + gender + '
    'C(educ, Treatment(reference="Elementary")) + '
    'C(hhtype, Treatment(reference="Couple with children")) + '
    'hhsize  + hhinc',
    data=df
).fit(cov_type='HC3')
model_total.summary()

# 1a. deprivation1
model_total1 = smf.ols(
    'deprivation1 ~ age + gender + '
    'C(educ, Treatment(reference="Elementary")) + '
    'C(hhtype, Treatment(reference="Couple with children")) + '
    'hhsize  + hhinc',
    data=df
).fit(cov_type='HC3')
model_total1.summary()

# 1b. deprivation2
model_total2 = smf.ols(
    'deprivation2 ~ age + gender + '
    'C(educ, Treatment(reference="Elementary")) + '
    'C(hhtype, Treatment(reference="Couple with children")) + '
    'hhsize  + hhinc',
    data=df
).fit(cov_type='HC3')
model_total2.summary()

```

### 3. Define `name_map` and `blocks`

```python

name_map = {
    # demographics
    'age': 'Alder',
    'gender': 'Kvinne',
    'hhinc': 'Husholdningsinntekt',
    # education : grunnskole
    'C(educ, Treatment(reference="Elementary"))[T.Master+]': 'Master+',
    'C(educ, Treatment(reference="Elementary"))[T.Bachelor]': 'Bachelor',
    'C(educ, Treatment(reference="Elementary"))[T.High School]': 'Videregående',
    'C(educ, Treatment(reference="Elementary"))[T.Vocational]': 'Fagskole',
    'C(educ, Treatment(reference="Elementary"))[T.Other]': 'Annet',
    # household types: Par med barn
    'C(hhtype, Treatment(reference="Couple with children"))[T.Alone with children]': 'Enslige forsørgere',
    'C(hhtype, Treatment(reference="Couple with children"))[T.Couple no children]': 'Par uten barn',
    'C(hhtype, Treatment(reference="Couple with children"))[T.Alone]': 'Bor alene',
    'C(hhtype, Treatment(reference="Couple with children"))[T.Shared apartment]': 'Bor i kollektiv',
    'C(hhtype, Treatment(reference="Couple with children"))[T.With parents]': 'Bor med foreldre',
    'C(hhtype, Treatment(reference="Couple with children"))[T.Other]': 'Annet',
    # household information
    'hhsize': 'Antall i husholdning',
    'num_children': 'Antall barn hjemme',
    'age_oldest_child': 'Eldste barn (alder)',
    'age_youngest_child': 'Yngste barn (alder)',
    'age_mean_children': 'Snittsalder barn',
    'deprivation': 'Udekket behov i familie'
}

```

Define blocks, which are the names/titles of the categories/groups

```python

blocks = {
    'Demografi': ['age', 'gender', 'hhinc'],
    'Utdanning \n(ref. grunnskole)': [
        'C(educ, Treatment(reference="Elementary"))[T.Master+]',
        'C(educ, Treatment(reference="Elementary"))[T.Bachelor]',
        'C(educ, Treatment(reference="Elementary"))[T.High School]',
        'C(educ, Treatment(reference="Elementary"))[T.Vocational]',
        'C(educ, Treatment(reference="Elementary"))[T.Other]',
    ],
    'Husholdningstyper \n(ref. par med barn)': [
        'C(hhtype, Treatment(reference="Couple with children"))[T.Alone with children]',
        'C(hhtype, Treatment(reference="Couple with children"))[T.Couple no children]',
        'C(hhtype, Treatment(reference="Couple with children"))[T.Alone]',
        'C(hhtype, Treatment(reference="Couple with children"))[T.Shared apartment]',
        'C(hhtype, Treatment(reference="Couple with children"))[T.With parents]',
        'C(hhtype, Treatment(reference="Couple with children"))[T.Other]',
    ],
    'Husholdnings': [
        'deprivation', 'hhsize', 'num_children', 'age_oldest_child',
        'age_youngest_child', 'age_mean_children'
    ],
}

```

### 4. apply the functions

Make three separated graphs based on the three models, saved as three files:

```python

regression_plot(model_total, blocks, name_map, title="Materielle deprivasjon (samlet)")
regression_plot(model_total, blocks, name_map, title="Materielle deprivasjon (samlet)",
                save_path='out/reg_depall.png')

regression_plot(model_total1, blocks, name_map, title="Materielle deprivasjon (grunnleggende)")
regression_plot(model_total1, blocks, name_map, title="Materielle deprivasjon (grunnleggende)",
                save_path='out/reg_depall1.png')

regression_plot(model_total2, blocks, name_map, title="Materielle deprivasjon (alvorlig)")
regression_plot(model_total2, blocks, name_map, title="Materielle deprivasjon (alvorlig)",
                save_path='out/reg_depall2.png')


```

One of the graphs would look like
![](/home/nan/OneDrive/SIFO/SIFO2025/familiefattigdom/out/reg_depall.png)




---


## II. Two side-by-side subplots of two regressions

### 1. Define new function

```python

def regression_plot_subplots(model, blocks, name_map, ax, title=None):
    
    # Creat dataframe summaries of the results
    summary_df = pd.DataFrame({
        'coef': model.params,
        'lower_ci': model.conf_int().iloc[:, 0]
        'upper_ci': model.conf_int().iloc[:, 1]
        'pval': model.pvalues
    }).drop('Intercept', errors = 'ignore')

    # Initialise lists to store plot data and positions
    labels = []             # labels on y-axis (group title + variables)
    coefs = []
    lower_cis = []
    upper_cis = []
    pvals = []
    y_positions = []        # vertical position on y-axis for each label/point
    y_pos = 0               # counter for y position
    group_positions = []    # track y positions of group titles for special treatment

    # Loop throught each block (group of variables)
    for block_name, coefs_list in blocks.items():
        if labels:
            y_pos += 1# if not the first group, add a black line before
        
        group_positions.append(y_pos)   # record y-position of this group title
        labels.append(block_name)       # add group title as a label
        coefs.append(0)
        lower_cis.append(0)
        upper_cis.append(0)
        pvals.append(1)                 # p=1 means no significance, color=dimgrey
        y_positions.append(y_pos)       # position on y axis
        y_pos += 1                      # move to next y position for variables

        # Now add actual variables from this group
        for coef_name in coefs_list:
            if coef_name in summary_df.index:
                # use name if availabel, else raw coefficient name
                labels.append(name_map.get(coef_name, coef_name))
                coefs.append(summary_df.loc[coef_name, 'coef'])
                lower_cis.append(summary_df.loc[coef_name, 'lower_ci'])
                upper_cis.append(summary_df.loc[coef_name, 'upper_ci'])
                pvals.append(summary_df.loc[coef_name, 'pval'])
                y_positions.append(y_pos)   # add position on y axis
                y_pos += 1                  # increment y position for next variable 

    # define colours based on p-value
    def get_color(p):
        if p < 0.05:
            return 'red'
        elif p < 0.1:
            return 'orange'
        else:
            return 'dimgrey'

    # create colour list matching each p-value
    colors = [get_color(p) for p in pvals]

    # plot horizontal lines for CIs
    for y, coef, low, high, color in zip(y_positions, coefs, lower_cis, upper_cis, colors):
        if y in group_positions:
            continue        # skip plotting lines for group titles
        ax.hlines(y, xmin=low, xmax=high, color=color, alpha=0.7, linewidth=2, zorder=1)
    # plot points only for variables, not group titles
    ax.scatter([c for i, c in enumerate(coefs) if y_positions[i] not in group_positions],
        [y for y in y_positions if y not in group_positions],
        color=[c for i, c in enumerate(colors) if y_positions[i] not in group_positions],
        s=50, zorder=2)

    # set y ticks and labels
    ax.set_yticks(y_positions)
    ax.set_yticklabels(labels)

    # style group titles
    for label in ax.get_yticklabels():
        if label.get_text() in blocks.keys():
            label.set_fontweight('bold')
            label.set_fontstyle('italic')

    # reverse y-axis 
    ax.invert_yaxis()

    # draw reference line
    ax.axvline(0, color='gray', linestyle='--', linewidth=1)

    ax.set_title(title or '', fontsize=10)
    ax.set_xlabel('')
    ax.spines['top'].set_visible(False)
    ax.spines['right'].set_visible(False)


```

### 2. Run regression models, and define `name_map` and `blocks`. 
As shown earlier.

### 3. Define which models to plug in and give them titles

```python
model_list = [model_total1, model_total2]

title = [
    'Grunnleggende deprivasjon',
    'Kritisk deprivasjon'
]

```

### 4. Draw figures 

One alternative is to have two side-by-side plots not share yaxis:

```python

# Create the figure (might need to adjust figsize height)
fig, axis = plt.subplots(1, 2, figsize=(8, 8), sharex = True)  #nrow=1, ncols=2

for model, subplot_title, ax in zip(models_list, titles, axis.flatter()):
    regression_plot_subplots(model, blocks, name_map, ax, title=subplot_title)

# add ocmmon x-axis label
fig.text(0.57, 0.005, 'Koeffisienter og 95% KI', ha='center', fontsize=12)

# Adjust layout
plt.tight_layout()
plt.subplots_adjust(top=0.92) # adjust top margin
plt.show()

```


Another option is to share y-labels 

```python

fig, axes = plt.subplots(1, 2, figsize=(8, 6), sharex=True)

# First plot (left) - keep everything
regression_plot_subplot(models_list[0], blocks, name_map, axes[0], title=titles[0])

# Second plot (right) - remove all y-tick labels including group titles
regression_plot_subplot(models_list[1], blocks, name_map, axes[1], title=titles[1])

axes[1].set_yticklabels(['']*len(axes[1].get_yticklabels()))  # Remove all y-tick labels

# to ensure both plots have the same y-axis limits
y_min, y_max = axes[0].get_ylim()

axes[1].set_ylim(y_min, y_max)  # Match y-axis limits

fig.text(0.57, 0.005, 'Koeffisienter og 95% KI', ha='center', fontsize=12)
plt.tight_layout()
#plt.savefig('out/reg_depall_2compact.png', dpi=300)
plt.show()

```

### 5. This function is more effective then previous
* Better for reuse and composition. It takes `ax` parameter so it can 
    * (1) create multi-panel figures (like 2x2 grids), 
    * (2) share axes, and 
    * (3) control layout, spacing and titles more flexibly.
* Cleaner handling of group headers. 
    * It uses `group_position` to separate data rows from group head rows. 
    * It avoids confusing magic values line `None`. so more precise and robust logic.
* More explicit logic for what gets plotted. 
    * The code checks `if y in group_position` 
    * instead of `coef is not None`.
* Can modify more to include save_path.


The graph looks like:

![example](/home/nan/OneDrive/SIFO/SIFO2025/familiefattigdom/out/reg_depall_2compact.png)




----------------

## 3. Flat coefplot without block titles

### 1. define new function


```python

def regression_plot_subplot(model, blocks, name_map, ax, title=None):
    summary_df = pd.DataFrame({
        'coef': model.params,
        'lower_ci': model.conf_int().iloc[:, 0],
        'upper_ci': model.conf_int().iloc[:, 1],
        'pval': model.pvalues
    }).drop('Intercept', errors='ignore')

    labels = []
    coefs = []
    lower_cis = []
    upper_cis = []
    pvals = []
    y_positions = []
    y_pos = 0

    for block_name, coefs_list in blocks.items():
        if labels:
            y_pos += 1
        for coef_name in coefs_list:
            if coef_name in summary_df.index:
                labels.append(name_map.get(coef_name, coef_name))
                coefs.append(summary_df.loc[coef_name, 'coef'])
                lower_cis.append(summary_df.loc[coef_name, 'lower_ci'])
                upper_cis.append(summary_df.loc[coef_name, 'upper_ci'])
                pvals.append(summary_df.loc[coef_name, 'pval'])
                y_positions.append(y_pos)
                y_pos += 1

    def get_color(p):
        if p < 0.05:
            return 'red'
        elif p < 0.1:
            return 'orange'
        else:
            return 'dimgrey'

    colors = [get_color(p) for p in pvals]

    for y, coef, low, high, color in zip(y_positions, coefs, lower_cis, upper_cis, colors):
        ax.hlines(y, xmin=low, xmax=high, color=color, alpha=0.7, linewidth=2, zorder=1)
    ax.scatter(coefs, y_positions, color=colors, s=50, zorder=2)

    ax.set_yticks(y_positions)
    ax.set_yticklabels(labels)
    ax.invert_yaxis()
    ax.axvline(0, color='gray', linestyle='--', linewidth=1)
    ax.set_title(title or '', fontsize=10)
    ax.set_xlabel('')  # Remove xlabel for compactness
    ax.spines['top'].set_visible(False)
    ax.spines['right'].set_visible(False)



```

### 2. Run regressions

Here is an example of running multiple regressions, in which the outcome variables have similar name.

```python
# run regressions
food_dep = ['QH_1r1', 'QH_1r2', 'QH_1r3',
            'QH_2r1', 'QH_2r2', 'QH_2r8', 'QH_2r9', 'QH_2r11']

# dictionary to store models
models_food = {}
for i, col in enumerate(food_dep):
    formula = f'{col} ~ age + gender + ' \
              'C(educ, Treatment(reference="Elementary")) + ' \
              'C(hhtype, Treatment(reference="Couple with children")) + ' \
              'num_children  + age_youngest_child + hhinc'
              #'hhsize + hhinc'
    model = smf.ols(formula, data=df).fit(cov_type='HC3')
    models_food[col] = model

models_food['QH_1r1'].summary()


```

### 3. Define `name_map` and `blocks` as before.
Note that this function will take blocks as groups and make spaces between each group, but it will not show headings for each group

### 4. Define list of models that need to be shown

```python
# list of models
models_list = [models_food[col] for col in food_dep]

titles = [
    'Varmt måltid annenhver dag',
    'Mat i tård med kostholdsråd',
    'Spise seg mett hver dag',
    'Droppet nødvendige matinnkjøp',
    'Hoppet over måltid / spise mindre',
    'Oppsøkt matutdeling',
    'Bedt om hjelp til mat',
    'Droppet frukt og grønt'
]

```

### 5. Plot 8 models in 4*2 grid

```python
fig, axes = plt.subplots(4, 2, figsize=(8, 12), sharex=True)

for model, subplot_title, ax in zip(models_list, titles, axes.flatten()):
    regression_plot_subplot(model, blocks, name_map, ax, title=subplot_title)

fig.text(0.57, 0.005, 'Koeffisienter og 95% KI', ha='center', fontsize=12)
plt.tight_layout()
plt.savefig('out/reg_depmat_8items2.png', dpi=300)
#plt.savefig('out/reg_depmat_8items.png', dpi=300)
plt.show()

```


This gives a graph that looks like:

![](/home/nan/OneDrive/SIFO/SIFO2025/familiefattigdom/out/reg_depmat_8items2.png)
