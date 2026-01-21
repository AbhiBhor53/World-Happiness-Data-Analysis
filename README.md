<h1>World Happiness Data Analysis</h1>
<h2>Importing Required Libraries and Loading dataset</h2>

```python
import pandas as pd
import matplotlib.pyplot as plt
df_2015=pd.read_csv("Happiness_Data_2015.csv")
df_2015
```
<img width="1743" height="747" alt="image" src="https://github.com/user-attachments/assets/2c0a9bad-47fd-4180-a249-d78976cdfe87" />

```python
df_2016=pd.read_csv("Happiness_Data_2016.csv")
df_2016
df_2017=pd.read_csv("Happiness_Data_2017.csv")
df_2017
df_2018=pd.read_csv("Happiness_Data_2018.csv")
df_2018
df_2019=pd.read_csv("Happiness_Data_2019.csv")
df_2019
```
During column profiling and quality check it is found that all values are within range no outlier. Also Column standardization and zero values have been handled in SQL and Excel

Exploratory Data Analysis
Which countries are happiest and why (data reasons)?

```Python
top_10 = df_2015.sort_values(
    by='Happiness_Score',
    ascending=False
).head(10)

bar= plt.barh(top_10["Country"],top_10["Happiness_Score"])
plt.bar_label(bar)
plt.xlabel('Happiness rank')
plt.ylabel('Countries')
plt.show()

```

<img width="815" height="539" alt="image" src="https://github.com/user-attachments/assets/494daf0d-83c0-47df-b924-fdeb4b026c37" />

```python

top_10 = df_2016.sort_values(
    by='Happiness_Score',
    ascending=False
).head(10)

bar= plt.barh(top_10["Country"],top_10["Happiness_Score"])
plt.bar_label(bar)
plt.xlabel('Happiness rank')
plt.ylabel('Countries')
plt.show()

```

<img width="835" height="548" alt="image" src="https://github.com/user-attachments/assets/9273c784-6663-49e7-8708-f2bf577dcab0" />

```python

top_10 = df_2019.sort_values(
    by='Happiness_Score',
    ascending=False
).head(10)

bar= plt.barh(top_10["Country"],top_10["Happiness_Score"])
plt.bar_label(bar)
plt.xlabel('Happiness rank')
plt.ylabel('Countries')
plt.show()

```

<img width="807" height="505" alt="image" src="https://github.com/user-attachments/assets/03a6060e-6706-4cbc-b6a8-2e0d95a30ac6" />


Nordic countries consistently rank high due to strong social support, high trust in government, and stable economic conditions.

Combining data of 5 years

```python

All_df=pd.concat([df_2015,df_2016,df_2017,df_2018,df_2019],ignore_index=True)
Avg_happiness=(All_df.groupby('Country')['Happiness_Score'].mean().reset_index())
Top10=Avg_happiness.sort_values(by='Happiness_Score', ascending=False).head(10)
bar= plt.barh(Top10["Country"],Top10["Happiness_Score"])
plt.bar_label(bar)
plt.xlabel('Happiness_Score')
plt.ylabel('Countries')
plt.show()

```
<img width="849" height="546" alt="image" src="https://github.com/user-attachments/assets/10eb5f40-6110-4879-a82a-c58c39eb02ad" />

Denmark,Norway,Finland,Switzerland,Iceland,Netherlands,Canada,Sweden,New Zealand & Australia are top 10 countries which have been consistently performing well on multiple metrics,cumulative score of which is 'Happiness Score'.

How does GDP per capita relate to happiness score?

```python

Avg_gdp_happiness =(All_df.groupby('Country')[['GDP_Per_Capita', 'Happiness_Score']].mean().reset_index())
#print(Avg_gdp_happiness)
#Ploting Scatter Plot
plt.figure(figsize=(8,6))
plt.scatter(
    Avg_gdp_happiness['GDP_Per_Capita'],
    Avg_gdp_happiness['Happiness_Score'],
    alpha=0.9
)

plt.xlabel('Average GDP Per Capita')
plt.ylabel('Average Happiness Score')
plt.title('Relationship Between GDP and Happiness')
plt.tight_layout()
plt.show()

```

<img width="1009" height="741" alt="image" src="https://github.com/user-attachments/assets/03065879-fe3b-4b4d-b965-eca60184cfd4" />

```python

sorted_df = Avg_gdp_happiness.sort_values('GDP_Per_Capita')

# Rolling mean (smooth trend)
sorted_df['Happiness_MA'] = (
    sorted_df['Happiness_Score']
    .rolling(window=10, center=True)
    .mean()
)

plt.figure(figsize=(8,6))

plt.scatter(
    sorted_df['GDP_Per_Capita'],
    sorted_df['Happiness_Score'],
    alpha=0.4
)

plt.plot(
    sorted_df['GDP_Per_Capita'],
    sorted_df['Happiness_MA']
)

plt.xlabel('Average GDP Per Capita')
plt.ylabel('Average Happiness Score')
plt.title('GDP vs Happiness (Smoothed Trend)')
plt.tight_layout()
plt.show()

```

<img width="1003" height="744" alt="image" src="https://github.com/user-attachments/assets/0de12627-cb72-4cb4-a10f-88c66bcc6674" />

The relation with GDP per capita income and happiness is almost linear. More the per capita income better the happiness score of a country

Is life expectancy strongly linked to happiness?

```python

Avg_Life_Expectancy=(All_df.groupby('Country')[['Healthy_Life_Expectancy','Happiness_Score']].mean().reset_index())
Sorted_df= Avg_Life_Expectancy.sort_values('Healthy_Life_Expectancy')
# Rolling Mean (smooth trend)

Sorted_df['Happiness_Score_MA']=(
    Sorted_df['Happiness_Score']
    .rolling(window=30,center=True)
    .mean())
plt.figure(figsize=(8,6))

plt.scatter(
    Sorted_df['Healthy_Life_Expectancy'], 
    Sorted_df['Happiness_Score'],
    alpha=0.6
)

plt.plot(
   Sorted_df['Healthy_Life_Expectancy'], 
   Sorted_df['Happiness_Score_MA']
)

plt.xlabel('Average Life Expectancy')
plt.ylabel('Average Happiness Score')
plt.title('Life Expectancy vs Happiness (Smoothed Trend)')
plt.tight_layout()
plt.show()

```

<img width="998" height="749" alt="image" src="https://github.com/user-attachments/assets/957a9073-8a8b-4fc9-9724-b74eda5b7ae8" />

As above scatter plot dipicts the relation between happiness score and life expectancy is almost linear therefore life expectancy is strongly related to Happiness

Does social support matter more than economic strength?

```python

plt.figure(figsize=(10,6))

# ---------- GDP vs Happiness ----------
sorted_gdp = Avg_gdp_happiness.sort_values('GDP_Per_Capita')
sorted_gdp['Happiness_MA_GDP'] = (
    sorted_gdp['Happiness_Score']
    .rolling(window=10, center=True)
    .mean()
)

ax1 = plt.gca()

# GDP scatter (blue)
ax1.scatter(
    sorted_gdp['GDP_Per_Capita'],
    sorted_gdp['Happiness_Score'],
    alpha=0.4,
    color='tab:blue',
    label='GDP vs Happiness (Points)'
)

# GDP trend line (dark blue)
ax1.plot(
    sorted_gdp['GDP_Per_Capita'],
    sorted_gdp['Happiness_MA_GDP'],
    color='navy',
    linewidth=2,
    label='GDP vs Happiness (Trend)'
)

ax1.set_xlabel('GDP Per Capita')
ax1.set_ylabel('Happiness Score')

# ---------- Social Support vs Happiness ----------
Avg_Social_Support = (
    All_df
    .groupby('Country')[['Social_support', 'Happiness_Score']]
    .mean()
    .reset_index()
)

sorted_social = Avg_Social_Support.sort_values('Social_support')
sorted_social['Happiness_MA_Social'] = (
    sorted_social['Happiness_Score']
    .rolling(window=20, center=True)
    .mean()
)

ax2 = ax1.twiny()

# Social support scatter (green)
ax2.scatter(
    sorted_social['Social_support'],
    sorted_social['Happiness_Score'],
    alpha=0.4,
    color='tab:green',
    label='Social Support vs Happiness (Points)'
)

# Social support trend line (dark green)
ax2.plot(
    sorted_social['Social_support'],
    sorted_social['Happiness_MA_Social'],
    color='darkgreen',
    linewidth=2,
    label='Social Support vs Happiness (Trend)'
)

ax2.set_xlabel('Social Support')

# ---------- Combined Legend ----------
lines1, labels1 = ax1.get_legend_handles_labels()
lines2, labels2 = ax2.get_legend_handles_labels()

plt.legend(
    lines1 + lines2,
    labels1 + labels2,
    loc='best'
)

plt.title('Happiness vs GDP and Social Support (Smoothed Trends)')
plt.tight_layout()
plt.show()

```

<img width="1241" height="742" alt="image" src="https://github.com/user-attachments/assets/f2d62761-bf69-4662-bb6e-465c11470d89" />

the behaviour of Happiness and Social support trends shows social support does not matter more than economic strengh. rather both are equally important

```python

All_df[['GDP_Per_Capita','Social_support','Happiness_Score']].corr()

```

<img width="575" height="173" alt="image" src="https://github.com/user-attachments/assets/864adcb5-2d74-4ef4-841f-6f95741e478c" />

```python

Avg_happiness = (
    All_df
    .groupby('Country')['Happiness_Score']
    .mean()
    .reset_index()
)

# Create happiness clusters
Avg_happiness['Happiness_Level'] = pd.qcut(
    Avg_happiness['Happiness_Score'],
    q=3,
    labels=['Low', 'Medium', 'High']
)
Avg_happiness

```

<img width="459" height="521" alt="image" src="https://github.com/user-attachments/assets/8074484d-0231-4496-8fc5-93823d315379" />

```python

High_happiness_countries = (
    Avg_happiness
    [Avg_happiness['Happiness_Level'] == 'High']
    .sort_values('Happiness_Score', ascending=False)
    .reset_index()
)

High_happiness_countries

```

<img width="598" height="888" alt="image" src="https://github.com/user-attachments/assets/02bc8bcb-25f6-4e7d-99e7-8fe57c2e0460" />

```python

Medium_happiness_countries = (
    Avg_happiness
    [Avg_happiness['Happiness_Level'] == 'Medium']
    .sort_values('Happiness_Score', ascending=False)
    .reset_index()
)

Medium_happiness_countries

```

<img width="592" height="810" alt="image" src="https://github.com/user-attachments/assets/ec67ec36-3fdf-4501-944f-89075cc40ad5" />

```python

low_happiness_countries = (
    Avg_happiness
    [Avg_happiness['Happiness_Level'] == 'Low']
    .sort_values('Happiness_Score', ascending=False)
    .reset_index()
)

low_happiness_countries

```

<img width="590" height="914" alt="image" src="https://github.com/user-attachments/assets/77e75bb8-cbce-42cd-831f-ea8e610edf2f" />

Reccomendations

World Happiness Data Analysis clearly demonstrate following points:

Countries with good per capita income, healthy life expectancy and Social support have been consistently doing well in this list for 5 subsequent years. citizens of the country off worries about attaining basic living standards, healthcare are more likely to be generous and help others wholeheartedly citizens who have been given old age allowances, un-employment allowances, disability support and other social support benefits to underprivileged class more likely trust government and because of this social support schemes people are more free to make life choices. e.g. Denmark with happiness Score=7.5460 has also performed in per capita income=1.383(Apr),healthy life expectancy=0.996(Apr.) and Social support=1.573(Apr)

For countries to perform well in this index must work on uplifting GPD per capita income, Healthcare facilities and enhancing social support for weaker section of the society.

GDP per capita income, Healthcare facilities, Social support and trust on government are 4 metrics largely responsible for position in the list all of these metrics are equally important. because there are some countries who have performed exceedingly well in one of these fronts but failed to secure rank in high happiness level cluster. e.g. China, India despite being into club of 10 largest economies in the world couldn’t secure high level happiness cluster.
