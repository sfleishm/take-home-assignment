# Python for Data Visualization Lesson Plan 

For this assignment, I thought it would be interesting to show off Data Visualizations in Python, regarding COVID-19.

In order to do that we need a few things first:

* A Dataset
* A way to manipulate the data. 
* A way to visualize what our data means, what is the story behind it all?

In order to solve these questions we'll look towards:

* Johns Hopkins Datasets that they've collected for COVID-19.
* Jupyter Notebooks and Pandas to show and manipulate our datasets easily.
* Matplotlib to make quick and easy graphs. 
 
| What Do We Need?          | Where or How Can We Solve the Question | 
| -------------             |:-------------:| 
| Dataset                   | [Johns Hopkins COVID-19 Datasets](https://github.com/CSSEGISandData/COVID-19) | 
| Way to Manipulate Data    | [Jupyter Notebooks](https://jupyter.org/) and [Pandas](https://pandas.pydata.org/pandas-docs/version/0.25.3/)      | 
| Visualizations            | [Matplotlib](https://matplotlib.org/3.2.0/contents.html)      | 

# Dataset

In order to get Johns Hopkins (JH) Dataset let's cloned their repository onto our Desktop.

1. On the JH github link above, click 'Clone or Download' then click the copy button above 'Download Zip'
2. With GitBash installed, go to your Desktop, right click, click 'Git Bash Here' and then type the following as well as paste the copied GitHub repository.
```bash
git clone https://github.com/CSSEGISandData/COVID-19.git 'NameOfFile'
```
3. Check to make sure everything cloned correctly.
4. Now we have some datasets that we can start looking at and manipulating!

# Way to Manipulate Data

For this step we will be using Jupyter Notebooks, Pandas, and some relatively basic Python code, which I am assuming is installed already. 

1. If you do not have Jupyter Notebooks or Pandas installed we can install them now.
```bash
pip install notebook
```
```bash
pip install pandas
```
2. Next we will open Jupyter notebooks by typing jupyter notebook into the command line.
```bash
jupyter notebook 
```
3. After a few seconds a Jupyter Notebook page should open up (mine opens in Chrome) and then navigate to the JH folder.
4. Here we can see a preview:
![](jupyterPreview.PNG?raw=true)
5. From here, click on 'New' next to 'Upload' and then click 'Python 3' which will create a new notebook in this folder.

## Working with our Dataset

1. Once we load into our Jupyter Notebook file, we first have to load the modules or dependencies we will be using. In this case, all we need is Pandas and Matplotlib. In the first cell we will just type the following:

```python
import pandas as pd
import matplotlib.pyplot as plt 
```
2. When we write 'as pd' after a module import, we are simplifying how we will call the module in the future. So instead of writing 'pandas' or 'matplotlib.pyplot', all we will have to write is 'pd' or 'plt' respectively.

3. In a new cell, we will then type:
```python
df_time_series_confirmed = pd.read_csv("csse_covid_19_data/csse_covid_19_time_series/time_series_19-covid-Confirmed.csv")
df_time_series_confirmed.head()
```
* 'df_time_series_confirmed' is telling us the name of a variable that we are assigning our csv to. 
* 'pd.read_csv' is reading our csv from the same file location as where we have our notebook. If we had the csv in a folder we would have to tell it the path to go to in order to read the csv. 
* Finally, 'df_time_series_confirmed.head()' will show us the first 5 rows of our csv.

![](load.PNG?raw=true)

## Filtering Data

* Now that we have our dataset loading properly into a dataframe we can start manipulating our data. 

* In this fictional case study, let's pretend that the company only wants to see data relating to China. 

1. Let's type:
```python
df_time_series_confirmed_china = df_time_series_confirmed.loc[df_time_series_confirmed['Country/Region'] == 'China']
df_time_series_confirmed_china.head()
```
* In this new block of code, we are creating a new variable, **_df_time_series_confirmed_china_**, then locating (.loc) a specific condition in our original dataframe, **_df_time_series_confirmed_**. 
* Since we only want to see cases in China, we will look at the 'Country/Region' column in our dataframe, and look at values with 'China' in them. 
* In doing so, we have effectively reduced our dataframe to only show instances in China which we can with the code **_df_time_series_confirmed_china.head()_**.

![](step_2.PNG?raw=true)

## Transposing Data

* In order easily read the data for ourselves and for the modules we will be using, we want to have the dates as one column together, instead of individuals. 

* To do this, we will transpose our rows and columns, essentially swapping their positions.

1. In a new code block, let's type:
```python
df_transposed = df_time_series_confirmed_china.T
df_transposed.head(10)
```

* Here we see that we are making a new shortened variable **_df_transposed_** that is assigned to the transposed (.T) dataframe, **_df_time_series_confirmed_china.T_**

* Then we'll preview the dataframe to make sure everything looks right, in this case I wanted to see 10 rows instead of the normal 5. 

![](step_3T.PNG?raw=true)

## Re-formatting our data

* Currently, we have data that looks a bit strange. However we can clean it up in no time.

---
1. First, let's reset our index, this will help us later down the road with further cleaning. 

```python
df_transposed.reset_index(inplace = True) 
df_transposed.head()
```
* **_df_transposed.reset_index(inplace = True)_** is an extremely easy way to reset our index back to 0. 

2. Next, we will replace the header names with the names of provinces: 

```python
df_transposed.columns = df_transposed.iloc[0]
df_transposed.head()
```
* Here, **_df_transposed.columns_** we are saying to rename the columns based on the first row of our dataframe, **_df_transposed.iloc[0]_**.

3. Next we want to rename the 'Province/State' column to what it should be, 'Date'.

```python
df_transposed = df_transposed.rename(columns={'Province/State': 'Date'})
```

* Here, we are our renaming column('Province/State') and changing it to read 'Date'.

4. At this point our data should look like this:

![](step_4Reformat.PNG?raw=true)

5. Finally, the first four rows aren't really helping us with anything. For the line plot we'll be making, we only need dates and corresponding cases. Therefore, the first four rows need to go. 

```python
df_transposed = df_transposed.drop([0,1,2,3])
df_transposed.head()
```

* Here is our solution to get around that:

* **_df_transposed.drop([0,1,2,3])_** will remove (.drop) rows specified by index number. This is why it was so important for us to reset our index count earlier, so that at this step we could simply remove the first four rows, (0,1,2,3). 

![](step_5Clean.PNG?raw=true)

## Creating Visualizations

Now we can make visualizations from all the data we worked so hard to clean. 

---
![](hubei_cases.png?raw=true)

```python
labels = ['Hubei']
fig = df_transposed.plot(x = 'Date', 
                   y=labels, 
                   title = f'Line Plot of COVID-19 Confirmed Cases in {labels}')
plt.legend(labels)
plt.ylabel('Number of Cases')
fig.get_figure().savefig("hubei_cases.png")
```

Let's go step by step through the lines of this code. 


```python
labels = ['Hubei']
```
* Here we are setting the labels we want to use in multiple different steps. In this case we want our labels to be the provinces that are affected. 
---

```python
fig = df_transposed.plot(x = 'Date', 
                   y=labels, 
                   title = f'Line Plot of COVID-19 Confirmed Cases in {labels}')
```
* Here, we are creating the variable **_fig_** that contains the plot we made. 

* In order to make our plot we are using the pandas data frame that we made earlier **_df_transposed_**. Following that we call the plot function (.plot) and then give it certain parameters for how we want to plot as well as titles. 

* Our first parameter corresponds to what we want our x to be, in this case 'Date'.

* Our second parameter corresponds to y, the values of confirmed cases over time which is in the 'Hubei' column. 

* Finally a title parameter that has some neat code inside. When we have f' ' we are saying that within the string we write, there's also some code that I want to include inside so that the string is easy to change. Here, we add {labels} to call the variable that we assigned earlier. This just makes it so that we only have the change the labels in one place.
---

```python
plt.legend(labels)
```

* Here we are calling the matplotlib library (plt) from the beginning of the lecture and assigning the legend to whatever is in the variable **_labels_**.

--- 
```python
plt.ylabel('Number of Cases')
```

* Here we are labelling our y-axis. 
---
```python
fig.get_figure().savefig("hubei_cases.png")
```
* Here we are saving our visualization as a png. 

## Additional Visualizations

![](four_cases.png?raw=true)


![](five_cases.png?raw=true)

**__**

