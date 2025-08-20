# COVID-19-deaths

## Objective
The objective of this project is to quantitatively assess the global impact of COVID-19. One method of evaluating this impact is by analyzing the number of deaths per 100,000 people. Since the onset of the pandemic in 2020, COVID-19 has resulted in 27 million deaths worldwide by August 2024, placing immense strain on economies and healthcare systems. Throughout the pandemic, various political measures were implemented to control the spread and reduce the death toll, often supported by data-driven research. This project aims to provide a scientific foundation for future policy decisions that countries can adopt to better withstand future pandemics.

## Method 
Global COVID-19 total death data in May 2022, **_[worldometer_coronavirus_summary_data.csv](data-and-code/worldometer_coronavirus_summary_data.csv)_**, is analyzed using Python to identify the country most severely impacted by the pandemic, based on deaths per 100,000 population. Countries are ranked accordingly to highlight disparities in outcomes. A focused case study is then conducted on the most affected country to examine systemic weaknesses within its healthcare infrastructure and public health response. Based on these findings, policy recommendations are proposed to help other nations strengthen their preparedness and resilience, thereby mitigating the impact of future pandemics. 

```math
death\_per\_100,000\_population = \frac{total\_deaths}{population} \times 100,000
```

## The Code
### Libraries Used
![Pandas](https://img.shields.io/badge/Pandas-150458?style=for-the-badge&logo=pandas&logoColor=white)
![NumPy](https://img.shields.io/badge/NumPy-013243?style=for-the-badge&logo=numpy&logoColor=white)
![Matplotlib](https://img.shields.io/badge/Matplotlib-11557c?style=for-the-badge&logo=matplotlib&logoColor=white)
![Plotly](https://img.shields.io/badge/Plotly-3F4F75?style=for-the-badge&logo=plotly&logoColor=white)
 
Importing the libraries used in this project. 
```python
#import python modules
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np
import plotly.express as px
import plotly.io as pio
import plotly.graph_objects as go
```

Extract the columns, country name, total number of deaths, and population from the imported global corona virus data.  Make a new data frame with the extracted columns, then use Plotly to display it as a table.
```python
#import, sort and create new data frame
Data = pd.read_csv("worldometer_coronavirus_summary_data.csv")
countries = Data["country"].unique()
countryname = []
deaths = []
population = []

for country in countries: 
    currentcountry = Data[(Data['country'] == country)]
    countryname.append(country)
    deaths.append(currentcountry['total_deaths'].sum())
    population.append(currentcountry['population'].sum())

newData = pd.DataFrame({'Country': countryname,'Total_deaths': deaths, 'Population': population})

fig3 = go.Figure(data=[go.Table(
    header=dict(values=list(newData.columns),
                fill_color='paleturquoise',
                align='left'),
    cells=dict(values=[newData.Country, newData.Total_deaths, newData.Population],
               fill_color='lavender',
               align='left'))
])
pio.renderers.default = "browser"
fig3.show()
```
<p align="center">
<img src="images/world_data_table.png" alt="Plot" width="100%"/>

The total number of deaths per 100,000 persons in every country can be determined using this data. The data frame is then updated with this value, and a global map is produced.  The colour gradient shows the COVID-19 outbreak per country in terms of fatalities.

```python
#calculate total deaths per 100,000 in each country and display as colored map 
per = []
for i in range(0,226,1):
    result = (newData.iloc[i,1] / newData.iloc[i,2]) * 100000
    per.append(result)
perarray = np.array(per)

newData1 = pd.DataFrame({'Country': countryname,'Total deaths': deaths, 'Population': population, 'Death per 100,000': perarray})

fig = px.choropleth(
    newData1,
    locations="Country",
    locationmode="country names",         
    color="Death per 100,000",              
    hover_name="Country",           
    color_continuous_scale="Reds", 
    title="Covid 19 deaths per 100,000 by country 2020",
    scope="world",                
    projection="natural earth"
)

fig.update_layout(
    geo=dict(showframe=False, showcoastlines=True),
    margin=dict(l=0, r=0, t=50, b=0)
)

pio.renderers.default = "browser"
fig.show()
```
<p align="center">
<img src="images/newplot.png" alt="Plot" width="100%"/>
    
According to the map, South America had the highest number of COVID-19 deaths per 100,000 persons in 2022. A bar chart of COVID-19 deaths by South American countries is made by zoning into this area.

```python
#bar graph of COVID-19 fatality data in South America 
Data1 = Data[Data["continent"] == 'South America']

countries1 = Data1["country"].unique()
countryname1 = []
death = []
popu = []

for country in countries1:
    currentcountry = Data1[(Data1['country'] == country)]
    countryname1.append(country)
    death.append(currentcountry['total_deaths'].sum())
    popu.append(currentcountry['population'].sum())

newData2 = pd.DataFrame({'Country': countryname1, 'Total Deaths': death, 'Population': popu})

perr = []
for i in range(0,14,1):
    result = (newData2.iloc[i,1] / newData2.iloc[i,2]) * 100000
    perr.append(result)
perrarray = np.array(perr)

newData3 = pd.DataFrame({'Country': countryname1,'Death per 100,000': perrarray})

fig1 = px.bar(newData3, y='Death per 100,000', x='Country', text_auto='.2s',
            title="Total Deaths per 100,000 due to COVID 19 in South America")
fig1.update_traces(textfont_size=12, textangle=0, textposition="outside", cliponaxis=False)
pio.renderers.default = "browser"
fig1.show()
```
<p align="center">
<img src="images/south_america_bar.png" alt="Plot" width="100%"/>

The bar chart shows that Peru had the highest death toll per 100,000 population in 2022. Further insights into the impact of COVID-19 in Peru can be gained by analyzing the trend in death cases from the start of the pandemic in 2020. **_[worldometer_coronavirus_daily_data.csv](data-and-code/worldometer_coronavirus_daily_data.csv)_**
```python
# Daily data in Peru 
File = pd.read_csv("worldometer_coronavirus_daily_data.csv")
splice = File.loc[(File['date'] >= '2020-2-15') & (File['date'] <= '2022-5-14')]
PeruFiley = (((splice[splice['country'] == 'Peru'])[['daily_new_deaths']]).to_numpy()).flatten()
PeruFilex = (((splice[splice['country'] == 'Peru'])[['date']]).to_numpy()).flatten()
PeruFilex = pd.to_datetime(PeruFilex)
newFile = pd.DataFrame({'Date': PeruFilex, 'Daily New Death': PeruFiley })

fle = px.scatter(newFile, x="Date", y="Daily New Death", trendline="lowess", trendline_options=dict(frac=0.03),title="Daily New Deaths in Peru from 2020 to 2022")
fle.show()
```

<p align="center">
<img src="images/newplot (1).png" alt="Plot" width="100%"/>

## Conclusion: COVID-19 in Peru 2022
In 2022, Peru recorded more COVID-19 deaths per 100,000 inhabitants than any other country in the world. As of December 31, 2020—marking the end of the first year of the pandemic—there had been a total of 1,017,199 confirmed cases and 37,724 deaths. The country's healthcare system was overwhelmed, with fewer than 1,500 ICU beds available nationwide—an insufficient capacity for Peru’s population of over 32 million. The daily death data shows that fatalities peaked on July 16, 2020, with 808 reported deaths in a single day. This occurred despite Peru being one of the first countries in Latin America to implement a national lockdown. The government closed its borders until October 2020 and enforced a mandatory nationwide quarantine. All schools remained closed throughout the year, except for approximately 1,000 schools that reopened briefly between October and November as cases began to decline. Following the gradual lifting of restrictions after the first wave, Peru experienced a second surge in COVID-19 deaths, reaching a peak of 1,154 fatalities on April 18, 2021. 

Despite swift initial action, Peru was among the hardest-hit countries during the COVID-19 pandemic. This outcome highlights deeper systemic challenges, including weaknesses in healthcare infrastructure, economic inequality, and political instability—factors that severely limited the country’s ability to respond effectively to the crisis. One major issue was underinvestment in the healthcare sector. Only 4% of Peru’s GDP was allocated to healthcare, resulting in a critical shortage of ICU beds and inadequate access to primary care facilities. Economically, insufficient investment in impoverished communities left many vulnerable. Although the government attempted to provide financial aid to those living in extreme poverty, poor implementation meant that the funds often failed to reach the intended recipients. As a result, many citizens were forced to disregard lockdown measures to access food and essential goods in public markets and streets. Politically, the pandemic exposed instability in health sector leadership. Within the first four months of the crisis, three different health ministers were appointed, leading to inconsistent policy guidance and a fragmented national response. Coordination between regional and national governments was weak, with many acting independently, further complicating efforts to contain the virus.

This analysis underscores the critical importance of investing in healthcare infrastructure, ensuring effective financial aid distribution, and maintaining strong political coordination—factors that have proven to significantly influence mortality outcomes during pandemics and will remain essential in mitigating the impact of future health crises. 

### References
- https://ourworldindata.org/key-charts-understand-covid-pandemic
- https://pmc.ncbi.nlm.nih.gov/articles/PMC10986737/
- https://pmc.ncbi.nlm.nih.gov/articles/PMC8045664/
- https://www.unicef.org/media/92111/file/UNICEF-Peru-COVID-19-Situation-Report-No.-10-End-of-year-2020.pdf

[**_[View full python code](data-and-code/COVID.py)_**]
