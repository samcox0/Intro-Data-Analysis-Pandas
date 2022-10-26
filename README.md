# Intro-Data-Analysis-Pandas-Exercises

Exercises
Exercise 0: Create a new folder and notebook called Intro-Data-Analysis-Pandas-Exercises where you will perform all the exercises below. Make sure to copy any code you need from this notebook to that one. You will use that folder to create a new GitHub repo with the code, html, and slides as usual.
[1]:

import pandas as pd
Exercise 1: Create a new dataframe pop with population data downloaded from Wikipedia. Make sure to clean the data so it can be used further.
[2]:

url = 'https://en.wikipedia.org/wiki/List_of_countries_by_population_(United_Nations)'
pop = pd.read_html(url, encoding='utf-8')[0]
pop
[2]:
Country / Area	UN continentalregion[4]	UN statisticalsubregion[4]	Population(1 July 2018)	Population(1 July 2019)	Change
0	China[a]	Asia	Eastern Asia	1427647786	1433783686	+0.43%
1	India	Asia	Southern Asia	1352642280	1366417754	+1.02%
2	United States	Americas	Northern America	327096265	329064917	+0.60%
3	Indonesia	Asia	South-eastern Asia	267670543	270625568	+1.10%
4	Pakistan	Asia	Southern Asia	212228286	216565318	+2.04%
...	...	...	...	...	...	...
229	Falkland Islands (United Kingdom)	Americas	South America	3234	3377	+4.42%
230	Niue	Oceania	Polynesia	1620	1615	−0.31%
231	Tokelau (New Zealand)	Oceania	Polynesia	1319	1340	+1.59%
232	Vatican City[z]	Europe	Southern Europe	801	799	−0.25%
233	World	NaN	NaN	7631091040	7713468100	+1.08%
234 rows × 6 columns

[3]:

pop.columns = ['Country/Area','Continental_Region','Sub_Region','Population_July_2018','Population_July_2019','Change']
pop.head()
[3]:
Country/Area	Continental_Region	Sub_Region	Population_July_2018	Population_July_2019	Change
0	China[a]	Asia	Eastern Asia	1427647786	1433783686	+0.43%
1	India	Asia	Southern Asia	1352642280	1366417754	+1.02%
2	United States	Americas	Northern America	327096265	329064917	+0.60%
3	Indonesia	Asia	South-eastern Asia	267670543	270625568	+1.10%
4	Pakistan	Asia	Southern Asia	212228286	216565318	+2.04%
[4]:

pop.dtypes
[4]:
Country/Area            object
Continental_Region      object
Sub_Region              object
Population_July_2018     int64
Population_July_2019     int64
Change                  object
dtype: object
Exercise 2: Merge the isocodes and pop dataframes.
[12]:

from IPython.display import IFrame
url = 'https://en.wikipedia.org/wiki/List_of_ISO_3166_country_codes'
​
isocodes = pd.read_html(url, encoding='utf-8')[0]
isocodes
​
isocodes = isocodes.droplevel(0, axis=1)
isocodes.head()
​
mycols = isocodes.columns
mycols = [c[:c.find('[')] for c in mycols]
mycols
​
isocodes.columns = mycols
isocodes.head()
​
isocodes['Alpha-2 code original'] = isocodes['Alpha-2 code']
isocodes['Alpha-2 code'] = isocodes['Subdivision code links'].apply(lambda x: x[x.find(':')+1:])
isocodes.head()
​
isocodes = isocodes.drop(columns = (["Official state name", "Sovereignty", "Alpha-2 code", "Numeric code", "Subdivision code links", "Internet ccTLD"]))
​
merged = isocodes.merge(pop, left_on='Country name', right_on='Country/Area')
merged
[12]:
Country name	Alpha-3 code	Alpha-2 code original	Country/Area	Continental_Region	Sub_Region	Population_July_2018	Population_July_2019	Change
0	Afghanistan	AFG	.mw-parser-output .monospaced{font-family:mono...	Afghanistan	Asia	Southern Asia	37171921	38041754	+2.34%
1	Albania	ALB	AL	Albania	Europe	Southern Europe	2882740	2880917	−0.06%
2	Algeria	DZA	DZ	Algeria	Africa	Northern Africa	42228408	43053054	+1.95%
3	Andorra	AND	AD	Andorra	Europe	Southern Europe	77006	77142	+0.18%
4	Angola	AGO	AO	Angola	Africa	Middle Africa	30809787	31825295	+3.30%
...	...	...	...	...	...	...	...	...	...
139	Uzbekistan	UZB	UZ	Uzbekistan	Asia	Central Asia	32476244	32981716	+1.56%
140	Vanuatu	VUT	VU	Vanuatu	Oceania	Melanesia	292680	299882	+2.46%
141	Yemen	YEM	YE	Yemen	Asia	Western Asia	28498683	29161922	+2.33%
142	Zambia	ZMB	ZM	Zambia	Africa	Eastern Africa	17351708	17861030	+2.94%
143	Zimbabwe	ZWE	ZW	Zimbabwe	Africa	Eastern Africa	14438802	14645468	+1.43%
144 rows × 9 columns

Exercise 3: Merge the dataframes we have created so far to have a unique dataframe that has ISO codes, GDP per capita, and population data.
[13]:

url = 'https://en.wikipedia.org/wiki/List_of_countries_by_GDP_(PPP)_per_capita'
IFrame(url, width=800, height=400)
​
gdppc_wiki = pd.read_html(url, encoding='utf-8')[1]
gdppc_wiki
​
gdppc_wiki.columns = ['Country/Territory', 'UN Region', 'gdppc_IMF', 'year_IMF',
                      'gdppc_WB', 'year_WB', 'gdppc_CIA', 'year_CIA']
gdppc_wiki.head()
​
gdppc_wiki['country_name'] = gdppc_wiki['Country/Territory'].str.replace('*', '', regex=True).str.strip()
​
for c in gdppc_wiki.columns[2:-1]:
    if gdppc_wiki[c].dtype=='O':
        gdppc_wiki[c] = pd.to_numeric(gdppc_wiki[c].str.replace('Ã¢Â€Â”', 'nan'), errors='coerce')
        if c.startswith('year'):
            gdppc_wiki[c] = gdppc_wiki[c].astype('Int64')
gdppc_wiki.head()
[13]:
Country/Territory	UN Region	gdppc_IMF	year_IMF	gdppc_WB	year_WB	gdppc_CIA	year_CIA	country_name
0	Luxembourg *	Europe	141587.0	2022	134754.0	2021	110300	2020	Luxembourg
1	Liechtenstein *	Europe	NaN	<NA>	NaN	<NA>	139100	2009	Liechtenstein
2	Singapore *	Asia	131426.0	2022	116487.0	2021	93400	2020	Singapore
3	Ireland *	Europe	131034.0	2022	106456.0	2021	89700	2020	Ireland
4	Monaco *	Europe	NaN	<NA>	NaN	<NA>	115700	2015	Monaco
[14]:

merged = merged.merge(gdppc_wiki, left_on='Country name', right_on='country_name')
merged
[14]:
Country name	Alpha-3 code	Alpha-2 code original	Country/Area	Continental_Region	Sub_Region	Population_July_2018	Population_July_2019	Change	Country/Territory	UN Region	gdppc_IMF	year_IMF	gdppc_WB	year_WB	gdppc_CIA	year_CIA	country_name
0	Afghanistan	AFG	.mw-parser-output .monospaced{font-family:mono...	Afghanistan	Asia	Southern Asia	37171921	38041754	+2.34%	Afghanistan *	Asia	2456.0	2020	2079.0	2020	2000	2020	Afghanistan
1	Albania	ALB	AL	Albania	Europe	Southern Europe	2882740	2880917	−0.06%	Albania *	Europe	17858.0	2022	15646.0	2021	13300	2020	Albania
2	Algeria	DZA	DZ	Algeria	Africa	Northern Africa	42228408	43053054	+1.95%	Algeria *	Africa	13324.0	2022	12038.0	2021	10700	2020	Algeria
3	Andorra	AND	AD	Andorra	Europe	Southern Europe	77006	77142	+0.18%	Andorra *	Europe	65372.0	2022	NaN	<NA>	49900	2015	Andorra
4	Angola	AGO	AO	Angola	Africa	Middle Africa	30809787	31825295	+3.30%	Angola *	Africa	7455.0	2022	6581.0	2021	6200	2020	Angola
...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...
139	Uzbekistan	UZB	UZ	Uzbekistan	Asia	Central Asia	32476244	32981716	+1.56%	Uzbekistan *	Asia	9478.0	2022	8497.0	2021	7000	2020	Uzbekistan
140	Vanuatu	VUT	VU	Vanuatu	Oceania	Melanesia	292680	299882	+2.46%	Vanuatu *	Oceania	2858.0	2022	3105.0	2021	2800	2020	Vanuatu
141	Yemen	YEM	YE	Yemen	Asia	Western Asia	28498683	29161922	+2.33%	Yemen *	Asia	2136.0	2022	3689.0	2013	2500	2017	Yemen
142	Zambia	ZMB	ZM	Zambia	Africa	Eastern Africa	17351708	17861030	+2.94%	Zambia *	Africa	3808.0	2022	3624.0	2021	3300	2020	Zambia
143	Zimbabwe	ZWE	ZW	Zimbabwe	Africa	Eastern Africa	14438802	14645468	+1.43%	Zimbabwe *	Africa	2555.0	2022	2445.0	2021	2700	2020	Zimbabwe
144 rows × 18 columns

Exercise 4: Use the os package to create folders to export data and figures. Since you will be using the names of these folders a lot, save their names in variables called path, pathout, and pathgraphs, where path = './data/', pathout = './data/', and pathgraphs = './graphs/'
[15]:

import os
​
path = './data/'
pathout = './data/'
pathgraphs = './graphs/'
​
try: 
    os.mkdir(path) 
except OSError as error: 
    print("Folder already exists.")
​
try: 
    os.mkdir(pathgraphs) 
except OSError as error: 
    print("Folder already exists.")
Exercise 5: Save the dataframe created in Exercise 3 as a CSV, XLSX, and Stata file into the pathout folder. Use a variable called filename = 'Wiki_Data' so you can use similar code to save all file types. Notice only the filetype will change.
[16]:

merged.to_csv('./data/Wiki_Data', sep='\t')
Exercise 6: Create plots showing the relation between GDP per capita and Population. Create all 4 types of possible regression plots and save them as PNG, PDF, and JPG files. Make sure to save them in the folder you created for graphs
[26]:

merged.plot( x='Population_July_2019', y='gdppc_IMF', kind='hist')
plt.savefig("./graphs/output1.png")
---------------------------------------------------------------------------
NameError                                 Traceback (most recent call last)
Input In [26], in <cell line: 2>()
      1 merged.plot( x='Population_July_2019', y='gdppc_IMF', kind='hist')
----> 2 plt.savefig("./graphs/output1.png")

NameError: name 'plt' is not defined

[27]:

merged.plot( x='Population_July_2019', y='gdppc_IMF', kind='scatter')
plt.savefig("./graphs/output2.png")
---------------------------------------------------------------------------
NameError                                 Traceback (most recent call last)
Input In [27], in <cell line: 2>()
      1 merged.plot( x='Population_July_2019', y='gdppc_IMF', kind='scatter')
----> 2 plt.savefig("./graphs/output2.png")

NameError: name 'plt' is not defined

[28]:

merged.plot( x='Population_July_2019', y='gdppc_IMF', kind='line')
plt.savefig("./graphs/output3.png")
---------------------------------------------------------------------------
NameError                                 Traceback (most recent call last)
Input In [28], in <cell line: 2>()
      1 merged.plot( x='Population_July_2019', y='gdppc_IMF', kind='line')
----> 2 plt.savefig("./graphs/output3.png")

NameError: name 'plt' is not defined

[29]:

merged.plot( x='Population_July_2019', y='gdppc_IMF', kind='kde')
[29]:
<AxesSubplot:ylabel='Density'>

Exercise 7: Create plots showing the relation between GDP per capita and Population Growth. Create all 4 types of possible regression plots and save them as PNG, PDF, and JPG files. Make sure to save them in the folder you created for graphs
Exercise 8: Using the notebook create slides for presenting your work and results. Once you have your slides, create a new public repo, publish it, and make sure to create a READ.ME file that show links to the notebook, html, and slides. Also, create the gh-pages branch to have a working slides webpage.
Notebook written by Ömer Özak for his students in Economics at Southern Methodist University. Feel free to use, distribute, or contribute.

Image

