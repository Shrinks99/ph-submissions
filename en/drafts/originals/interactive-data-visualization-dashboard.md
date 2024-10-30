---
title: "Creating a Dashboard for Interactive Data Visualization with Dash in Python"
slug: interactive-data-visualization-dashboard
layout: lesson
collection: lessons
date: YYYY-MM-DD
authors:
- Luling Huang
reviewers:
- Diego Alves
- Johannes Breuer
editors:
- Caio Mello
review-ticket: https://github.com/programminghistorian/ph-submissions/issues/609
difficulty: 3
activity: presentation
topics: data visualization, web development
abstract: Short abstract of this lesson
avatar_alt: Visual description of lesson image
doi: XX.XXXXX/phen0000
---

{% include toc.html %}

To advance open scholarship in the humanities, it is important to make research outputs more accessible to other scholars and the general public. Creating a web-based interactive dashboard to visualize data results has become a popular method to achieve this goal. There are a wide range of examples, such as [this project that tracks social media data](https://portal.research.lu.se/en/publications/stancexplore-visualization-for-the-interactive-exploration-of-sta), [a study that recreates W. E. B. Du Bois' study of black residents in Philadelphia](http://digitalhumanities.org/dhq/vol/16/2/000609/000609.html), and [a project that visualizes the narrative structure in William Faulkner's work](http://digitalhumanities.org/dhq/vol/15/2/000548/000548.html). 

Unlike static graphs, interactive dashboards allow readers to explore patterns in the data based on their specific interests by filtering, sorting, or changing data views. Features like hover-over tooltips can also provide additional information without cluttering the main display. This lesson will walk you through the process of creating interactive dashboards based on publicly available datasets using the open-source [Dash library in Python](https://dash.plotly.com/introduction). Here is an example of the kind of data visualization dashboard that can be created by Dash[^1]:

{% include figure.html filename="en-or-interactive-data-visualization-dashboard-01.png" alt="A screenshot showing what kind of dashboard can be created by Dash." caption="Figure 1. Screenshot of an example of interactive data visualization dashboard created by Dash." %}

Figure 1 shows a dashboard that visualizes the gender pay gaps in the businesses and organizations in Ireland. On the left, the interactive features include a radio button to switch between Year 2023 and Year 2022, and a dropdown menu to select a company. Depending on what year and what company a user chooses, the data and the bar graph in the main panel on the right change. This lesson will show how to use Dash to create an interactive data dashboard.

For demonstration, this lesson is guided by a contemporary case study in the field of media and communication studies. This case study asks: How do U.S. television stations cover the war in Ukraine? The dataset used is a publicly available dataset of transcription texts from television news. To demonstrate more of the range of applications available with web-based dashboards, this lesson also discusses how to extend the case study by looking at another example with a historical focus.

This lesson contributes to the existing Programming Historian lessons by adding a tutorial focused on creating an interactive web-based dashboard in Python ([see a similar English lesson focused on using Shiny in R](https://programminghistorian.org/en/lessons/shiny-leaflet-newspaper-map-tutorial)). The approach taken by this lesson can be applied to a wide range of digital humanities projects where there is a need to retrieve data from a publicly available source, process and analyze the data, and visualize the research outputs in an interactive manner. In addition, this lesson also shows how to deploy a dashboard via a free (freemium) web service, which helps to make similar dashboards widely and easily accessible.

# Lesson Goals
In this lesson you will learn how to use Python to:
  * Retrieve data using an [Application Programming Interface (API)](https://en.wikipedia.org/wiki/API)
  * Create the dashboard frontend that determines how it looks
  * Create the dashboard backend that determines how users interact with it
  * Deploy the dashboard onto the web for free

Other essential steps such as installing necessary libraries, setting up a [virtual environment](https://docs.python.org/3/library/venv.html#venv-def), and manipulating the downloaded data will be included when appropriate as well. The code to be executed in the command line will start with the symbol `$`.

# Case Study
The case study concerns how U.S. television stations have covered the current war between Russia and Ukraine. One can compare whether the stations have mentioned the keywords related to Ukraine as frequently as the keywords related to Russia. Further, we can also compare the coverage frequency among some major stations. 

Quantitative methods for content analysis (CA) have long been a tradition in mass communication studies, and the method of algorithmic text analysis (ATA) has become popular given the rising availability of large amounts of textual data.[^2] Both methods aim to infer meanings from text through classification or measurement. Whereas CA relies heavily on a carefully crafted codebook based on research questions and multiple human coders to ensure the reliability and validity of a systematic analysis,[^3] [^4] ATA relies on algorithms and models (a more general term for this method is [text mining](https://en.wikipedia.org/wiki/Text_mining) or [natural language processing](https://en.wikipedia.org/wiki/Natural_language_processing)).[^5] 

The approach used in the case study situates somewhere in between CA and ATA. On the one hand, this approach only conducts a distant reading, relying less on human coders often required in CA. On the other hand, this approach only measures the manifest features of text (i.e., frequency) and does not involve the types of algorithmic classification that is often seen in ATA. This approach of distant reading aims to discover patterns from large amount of data.[^6] 

## Dataset
This lesson uses a free and open database from the Internet Archive's [Television Explorer](https://blog.archive.org/2016/12/20/new-research-tool-for-visualizing-two-million-hours-of-television-news/). This database tracks the amount of airtime television stations give to certain keywords, with a resolution of 15 seconds. The keyword searches are based on the text of closed captioning. The data-retrieval tool is the [2.0 TV API](https://blog.gdeltproject.org/gdelt-2-0-television-api-debuts/) made available by the Global Database of Events, Language and Tone (GDELT).

Our goal is to retrieve the data for the dashboard via the 2.0 TV API. Regarding keyword, some appropriate Ukraine-related terms can include "Ukrainian" and "Zelenskyy," and the Russia-related terms can include "Russian" and "Putin." With the 2.0 TV API, you can also specify the TV geographic market to be "National;" the output mode is the normalized percentage of airtime (the y-axis of the line graph that you will create later); and the time range covers the last 365 days, including today. 

After data retrieval, you will prepare a dataset like this for visualization:

{% include figure.html filename="en-or-interactive-data-visualization-dashboard-02.png" alt="A screenshot showing what the processed dataset looks like. There are three columns: date collected, Series, and Value." caption="Figure 2. Screenshot of the processed dataset." %}

In Figure 2, the Value column represents the daily percentage of airtime that mentions certain keywords for a given station (e.g., "CNN"). This dataset is the one that the dashboard will be based on.

## Why Dash in Python?
Several alternative tools to create interactive dashboards are well discussed in [this lesson on Shiny in R](https://programminghistorian.org/en/lessons/shiny-leaflet-newspaper-map-tutorial). Options that do not require coding include such proprietary software as Tableau or [ArcGIS](https://www.arcgis.com/index.html).

The case for Python is that it is a widely used programming language. Python is flexible and powerful to process a dataset in its full life cycle (i.e., from data collection, to data analysis, and to data visualization). 

If you have already been using Python heavily, Dash is a good option, as it is developed by [plotly](https://plotly.com/), the go-to tool for data visualization in various programming languages including Python, R, and JavaScript. This makes the workflow of publishing an interactive visualization more efficient. 

As an alternative, you could use both plotly and [Flask](https://flask.palletsprojects.com) (the web application framework underlying Dash) directly, but this requires deep knowledge of JavaScript and HTML. If you want to focus on data visualization rather than the technical details of web development, Dash is highly recommended.

# Prepare for the Lesson

In this lesson, you will write code in a `.py` file stored in a folder on your local machine. You will then run this `.py` file in the command line to test your application (e.g., running `$python YourFileName.py`). Lastly, you will need to use GitHub to deploy your application.

## Prerequisites
  * Python 3 (3.7.13 or later). See [Mac Installation](https://programminghistorian.org/lessons/mac-installation), [Windows Installation](https://programminghistorian.org/lessons/windows-installation), or [Linux Installation](https://programminghistorian.org/lessons/linux-installation)
  * Command line. For introductions, see [Windows here](https://programminghistorian.org/en/lessons/intro-to-powershell) and [macOS/Linux here](https://programminghistorian.org/en/lessons/intro-to-bash)
  * A text editor (e.g., [Atom](https://atom.io/), [Notepad++](https://notepad-plus-plus.org/), [Visual Studio Code](https://code.visualstudio.com/)) to write Python code
  * A web browser
  * A [GitHub](https://github.com) account
  * Have [git](https://git-scm.com/doc) ready to use in command line. You could also use either of the following (not covered in this lesson):
    * [GitHub Desktop](https://desktop.github.com/)
    * [GitHub CLI](https://cli.github.com/)
    * [GitHub Codespaces](https://github.com/features/codespaces) ([costs may be incurred after you exceed the free quota](https://docs.github.com/en/billing/managing-billing-for-github-codespaces/about-billing-for-github-codespaces))

Optional: [Jupyter Notebook](https://jupyter.org/). If you prefer to run the code in Jupyter Notebook, you'll need to install it (see [this lesson for instructions](https://programminghistorian.org/en/lessons/jupyter-notebooks#installing-jupyter-notebooks)).

## Create a Virtual Environment
To avoid conflicts in library versions among multiple Python projects, it is a good practice to create a virtual environment for each project.

A virtual environment in Python is a self-contained directory that contains a specific version of Python and a set of libraries. It allows you to manage dependencies for different projects separately, ensuring that changes in one project do not affect others. This is especially useful for maintaining consistent development environments and avoiding conflicts between package versions.

There are several ways to create a virtual environment. One way is to use `conda` ([see this lesson for more details](https://programminghistorian.org/en/lessons/visualizing-with-bokeh#prerequisites)). This is a good option if you are already using [Anaconda](https://docs.conda.io/projects/conda/en/latest/glossary.html?highlight=anaconda#anaconda) for more data-science-oriented projects. Assuming that you are starting fresh, it would be more appropriate to go for a more lightweight method by using [virtualenv](https://virtualenv.pypa.io/en/latest/). To install, open a command line window and run `$pip install virtualenv`.

Next, create a folder at your preferred location for the current lesson and name it *ph-dash*. In your command line, navigate to the *ph-dash* directory. To create a virtual environment called *venv*, run `$virtualenv venv`. Then, you need to activate the virtual environment by running:

```
$venv\Scripts\activate # For Windows
$source venv/bin/activate # For macOS/Linux
```

If properly executed, you will see a pair of parentheses around *venv*, the name of the created virtual environment, at the start of the current line in your command line window. 

Now, you are in an isolated development environment with a specific version of Python and a specific list of libraries with their specific versions. When you are done writing code for a project, to exit the virtual environment, just run `$deactivate`.

## Install Libraries
Once a virtual environment is set up, you are ready to install several third-party libraries needed for the current lesson. With the virtual environment still in the activated mode, run `$pip install requests pandas dash dash_bootstrap_components`.

  * [requests](https://requests.readthedocs.io/en/latest/): Used in data retrieval [for sending and receiving API queries](https://en.wikipedia.org/wiki/Requests_(software))
  * [pandas](https://pandas.pydata.org/docs/index.html): Used in data preparation [for manipulating data in tabular forms](https://en.wikipedia.org/wiki/Pandas_(software))
  * [dash](https://dash.plotly.com/introduction): Used for creating dashboards
  * [dash_bootstrap_components](https://dash-bootstrap-components.opensource.faculty.ai/): Used for frontend templates for dashboards

Alternatively, you can also download the file called `requirements.txt` from [here](https://github.com/programminghistorian/ph-submissions/blob/gh-pages/assets/interactive-data-visualization-dashboard/requirements.txt) to the same folder and run `$pip install -r requirements.txt`. This will also install the required packages.
 
# Coding the Dashboards

The next section will walk you through the major steps in coding. If you want to execute the code blocks as you follow along, I have provided [the Jupyter Notebook version of the code here](https://github.com/programminghistorian/ph-submissions/blob/gh-pages/assets/interactive-data-visualization-dashboard/interactive-data-visualization-dashboard.ipynb). There is a [Colab](https://colab.research.google.com/) button at the top of the notebook, so that you can go there to execute the code in a Colab environment.

In the planning stage, we can envision a dashboard where there are two line graphs, one showing the trend of Russia-related terms and the other for the trend of Ukraine-related terms mentioned by television networks. 

More specifically, in either of the line graph, the y-axis represents the percentage of airtime mentioning certain keywords by a certain national station, and the x-axis represents dates. 

In addition, there are multiple lines, each representing one television network. A basic interactive component is a date-range selector where users can specify a range of dates, and the line graphs will be updated upon selection.

## Import Libraries
```
import datetime
import requests
import pandas as pd
from io import StringIO
from datetime import date
import dash
from dash import dcc
from dash import html
from dash.dependencies import Input, Output
import dash_bootstrap_components as dbc
import plotly.express as px
```
* The `datetime` library is needed to manipulate date and time objects in python.
* The `StringIO` library is needed to treat a string object as a file-like object. This will be useful when you need to convert the output from the an HTML request to a pandas dataframe.
* The `plotly.express` library is needed to draw basic graphs like a line graph.
* `dcc`, `html`, `Input`, and `Output` are the specific modules within the `dash` library that are needed in various parts of the code below. `dcc` (Dash Core Components) provides a set of popular components like text-input boxes, sliders, and dropdowns; `html` contains components that represent standard HTML elements, allowing you to structure a layout using HTML tags; `Input` and `Output`: These are used for defining the interactivity in a Dash app. They allow you to specify how changes in one component (Input) should affect another component (Output).

## Retrieve Data Using API

First, define a range of dates for the complete dataset to be retrieved using the API. The goal here is to create two string objects: `today_str` and `start_day_str`. 

```
today = date.today()
today_str = today.strftime("%Y%m%d")
start_day = today - datetime.timedelta(365)
start_day_str = start_day.strftime("%Y%m%d")
```

Here you restrict the range to be 365 days for demonstration purpose only.

Two string objects are then created for query: one for Ukraine-related terms and one for Russia-related terms.

```
query_url_ukr = f"https://api.gdeltproject.org/api/v2/tv/tv?query=(ukraine%20OR%20ukrainian%20OR%20zelenskyy%20OR%20zelensky%20OR%20kiev%20OR%20kyiv)%20market:%22National%22&mode=timelinevol&format=html&datanorm=perc&format=csv&timelinesmooth=5&datacomb=sep&timezoom=yes&STARTDATETIME={start_day_str}120000&ENDDATETIME={today_str}120000"
```

```
query_url_rus = f"https://api.gdeltproject.org/api/v2/tv/tv?query=(kremlin%20OR%20russia%20OR%20putin%20OR%20moscow%20OR%20russian)%20market:%22National%22&mode=timelinevol&format=html&datanorm=perc&format=csv&timelinesmooth=5&datacomb=sep&timezoom=yes&STARTDATETIME={start_day_str}120000&ENDDATETIME={today_str}120000"
```

 The parameters to be specified include keywords, geographic market, output mode, output format, range of dates, etc. 

For the purpose of this lesson, the Ukraine-related keywords are "Ukraine," "Ukrainian," "Zelenskyy," "Kyiv," or "Kiev;" the Russia-related keywords are "Russia," "Russian," "Putin," "Kremlin," or "Moscow;" the geographic market is "National;" the output mode is the normalized percentage of airtime (the y-axis of the line graph that you will create later); the output format is set to [CSV (comma-separated values)](https://en.wikipedia.org/wiki/Comma-separated_values); the start date and the end date are specified with the corresponding object names (`start_day_str` and `today_str`). 

See [this documentation](https://blog.gdeltproject.org/gdelt-2-0-television-api-debuts/) for a complete description of query parameters. The encoding characters `%20` and `%22` represent space and double quotation mark ("), respectively.

Next, once you have retrieved the data, you can prepare the data in a way that is ready for visualization. Our goal is to transform the data into the shape shown in Figure 2, above.

```
def to_df(queryurl):
  response = requests.get(queryurl)
  content_text = StringIO(response.content.decode('utf-8'))
  df = pd.read_csv(content_text)
  return df
```

The `requests` library is used to execute the queries and transform the results into a `pandas` dataframe. To do this, you create a function called `to_df()` to streamline the workflow.

Once you have created the function, you can put it to work:

```
df_ukr = to_df(query_url_ukr)
df_rus = to_df(query_url_rus)
```

Optional: You can use the `df.head()` function to take a look at the first five rows of the output dataframe from the above action.

If you are in Jupyter Notebook, take a look at the first five rows of the retrieved dataframe for Ukraine:

```  
df_ukr.head()
```

If you execute a .py file in the command line, e.g., `$python filename.py`, add the print() function to see the first five rows:
```
print(df_ukr.head())
```

You can also use the shape() function to see how many columns and rows there are in the dataframe. Give it a try!

Now there are two dataframes: one for Ukraine and one for Russia. In either, there are three columns: date, station, and relative frequency of keyword mentions (from left to right).

## Clean Data for Further Use
Although not strictly required, rename the first column to something shorter for convenience:
```
df_ukr = df_ukr.rename(columns={df_ukr.columns[0]: "date_col"})
df_rus = df_rus.rename(columns={df_rus.columns[0]: "date_col"})
```
Next, because the date and time in the first column is string, you want Python to actually treat the data as date and time:
```
df_ukr['date_col'] = pd.to_datetime(df_ukr['date_col'])
df_rus['date_col'] = pd.to_datetime(df_rus['date_col'])
```
The following code will Select three stations for comparison. These three stations are CNN, Foxnews, and MSNBC. 

```
df_rus = df_rus[[x in ['CNN', 'FOXNEWS', 'MSNBC'] for x in df_rus.Series]]
df_ukr = df_ukr[[x in ['CNN', 'FOXNEWS', 'MSNBC'] for x in df_ukr.Series]]
```

For the purposes of this lesson, these three news channels provide a range of ideological perspectives. CNN is generally presumed to represent an ideological middle ground, FOX News presumed to represent the ideological conservative, and MSNBC presumed to represent the ideological liberal perspective.

## Initiate a Dashboard Instance

The following code will initiate a dashboard instance. 

```
app = dash.Dash(__name__, external_stylesheets=[dbc.themes.LITERA])
server = app.server
```

To use a template that controls how our dashboard will look, we use the LITERA theme from [Dash Bootstrap Components](https://dash-bootstrap-components.opensource.faculty.ai/) (`dbc`). 

You can choose any theme you prefer from [this list](https://dash-bootstrap-components.opensource.faculty.ai/docs/themes/).  

## Coding the Frontend
```
app.layout = dbc.Container(
    [   dbc.Row([ # row 1
        dbc.Col([html.H1('US National Television News Coverage of the War in Ukraine')],
        className="text-center mt-3 mb-1")
    ]
    ),
        dbc.Row([ # row 2
            dbc.Label("Select a date range:", className="fw-bold")
    ]),
    
     dbc.Row([ # row 3
              dcc.DatePickerRange(
                id='date-range',
                min_date_allowed=df_ukr['date_col'].min().date(),
                max_date_allowed=df_ukr['date_col'].max().date(),
                initial_visible_month=df_ukr['date_col'].min().date(),
                start_date=df_ukr['date_col'].min().date(),
                end_date=df_ukr['date_col'].max().date()
              )
    ]),

     dbc.Row([ # row 4
              dbc.Col(dcc.Graph(id='line-graph-ukr'), 
                      )
     ]),

    dbc.Row([ # row 5
              dbc.Col(dcc.Graph(id='line-graph-rus'), 
                      )
     ])

    ])
```

Here, you need to think about the dashboard layout as a grid with rows and columns. In our dashboard, we have five rows from top to bottom: title, instruction text for the date-range selector, data-range selector, the first line graph, and the second line graph.

To break down the above long code block: For Row 1, we have:
```
dbc.Col([html.H1('US National Television News Coverage of the War in Ukraine')], className="text-center mt-3 mb-1")
```
This just mean that you place only one column in Row 1 and use the `h1` HTML element to enclose the title of the dashboard. `className` sets the [CSS](https://en.wikipedia.org/wiki/CSS) styling for the `h1` HTML element: The title is centered with margins set on top and bottom.

In Row 2, you create a text box to direct users to select a date range:

```
dbc.Row([ # row 2
            dbc.Label("Select a date range:", className="fw-bold")])
```
The text has a font weight set to bold.

In Row 3, here comes the key interactive feature of our dashboard: a date range picker where a user can choose a start date and an end date (Figure 3).

{% include figure.html filename="en-or-interactive-data-visualization-dashboard-03.png" alt="A screenshot showing what the date range picker looks like" caption="Figure 3. Interactive feature: The date range picker of the dashboard." %}

```
dbc.Row([ # row 3
          dcc.DatePickerRange(
              id='date-range',
              min_date_allowed=df_ukr['date_col'].min().date(),
              max_date_allowed=df_ukr['date_col'].max().date(),
              initial_visible_month=df_ukr['date_col'].min().date(),
              start_date=df_ukr['date_col'].min().date(),
              end_date=df_ukr['date_col'].max().date()
          )
    ])
```
By default (i.e., when the dashboard is first loaded), we set the start date and the end date (regardless of whether a date is selected) to be the earliest date and the latest date in the `date_col` column of the dataframe. Remember that those two dates are 365 days apart (the actual difference shown in the date picker could be shorter due to the fact that the most recent data may not be available yet).

You are now ready to put the two line graphs in place in Row 4 and Row 5, respectively:

```
dbc.Row([ # row 4
        dbc.Col(dcc.Graph(id='line-graph-ukr'), )
     ]),

dbc.Row([ # row 5
        dbc.Col(dcc.Graph(id='line-graph-rus'), )
     ])
```
The line graph for Ukraine is on Row 4, and the one for Russia is on Row 5.

If you want to add columns within a row, you can easily do so by nesting two `dbc.Col` components under the same `dbc.Row` component. Below is an example of placing the two line graphs side by side on the same row:


```
dbc.Row([
          dbc.Col(dcc.Graph(id='line-graph-ukr'), 
                  ),
          dbc.Col(dcc.Graph(id='line-graph-rus'), 
                  )
  ])
```

Also important to note in the frontend code above is that you explicitly give names to those components that are involved in user interaction. 

In this case, you have three such components: the data-range picker as input and the two line graphs as output (i.e., reacting to any update in the date-range picker triggered by a user). The names of these components are created using the `id` parameter. These names are very important when you code the backend later.

### Coding the Backend

In the backend, the core concepts are *callback decorator* and *callback function*. 

In the following code, `@app.callback`, the callback decorator, defines which output variables and input variables are included in a user interaction. For example, remember that when you code the frontend, you name the line graph for Ukraine as 'line-graph-ukr'. Now you refer this name in one of the Output variables. The parameter 'figure' specifies which property of the referred component is updated when needed.

```
# callback decorator
@app.callback(
    Output('line-graph-ukr', 'figure'),
    Output('line-graph-rus', 'figure'),
    Input('date-range', 'start_date'),
    Input('date-range', 'end_date')   
)

# callback function
def update_output(start_date, end_date):
    # filter dataframes based on updated data range
    mask_ukr = (df_ukr['date_col'] >= start_date) & (df_ukr['date_col'] <= end_date)
    mask_rus = (df_rus['date_col'] >= start_date) & (df_rus['date_col'] <= end_date)
    df_ukr_filtered = df_ukr.loc[mask_ukr]
    df_rus_filtered = df_rus.loc[mask_rus]
    
    # create line graphs based on filtered dataframes
    line_fig_ukr = px.line(df_ukr_filtered, x="date_col", y="Value", 
                     color='Series', title="Coverage of Ukrainian Keywords")
    line_fig_rus = px.line(df_rus_filtered, x='date_col', y='Value', 
                     color='Series', title="Coverage of Russian Keywords")

    # set x-axis title and y-axis title in line graphs 
    line_fig_ukr.update_layout(
                   xaxis_title='Date',
                   yaxis_title='Percentage of Airtime')
    line_fig_rus.update_layout(
                   xaxis_title='Date',
                   yaxis_title='Percentage of Airtime')
    
    # set label format on x-axis in line graphs 
    line_fig_ukr.update_xaxes(tickformat="%b %d<br>%Y")
    line_fig_rus.update_xaxes(tickformat="%b %d<br>%Y")
    
    return line_fig_ukr, line_fig_rus
```

The callback function, `update_output()`, defines how the interaction occurs: The two line graphs are updated whenever the start date or the end date in the date-range picker is changed by a user. This is called *reactive programming*, similar to [the server logic used in R Shiny](https://programminghistorian.org/en/lessons/shiny-leaflet-newspaper-map-tutorial#shiny-and-reactive-programming). 

The callback functions determine the dynamic nature of the created dashboard. Here is the breakdown of the `update_output()` function above:

First, there are two arguments: `start_date` and `end_date`. These come from the dates that a user chooses and become the input of the `update_output()` function. The order matters: It should be the same as the order of the Input variables specified in `@app.callback`. That is, `start_date` comes before `end_date`.

For the actual task that the `update_output()` function performs, it first filters the two dataframes based on updated data range:

```
mask_ukr = (df_ukr['date_col'] >= start_date) & (df_ukr['date_col'] <= end_date)

mask_rus = (df_rus['date_col'] >= start_date) & (df_rus['date_col'] <= end_date)

df_ukr_filtered = df_ukr.loc[mask_ukr]
df_rus_filtered = df_rus.loc[mask_rus]
```
The mask (e.g., `mask_ukr`) can be understood as the condition(s) under which a row in a dataframe is selected. `df_ukr_filtered` and `df_rus_filtered` are the filtered dataframes based on the date range selected by a user.

The next step is to create the two line graphs based on the filtered dataframes:
```
line_fig_ukr = px.line(df_ukr_filtered, x="date_col", y="Value", color='Series', title="Coverage of Ukrainian Keywords")

line_fig_rus = px.line(df_rus_filtered, x='date_col', y='Value', color='Series', title="Coverage of Russian Keywords")
```
In each of the line graphs, the x-axis represents the dates from the `date_col` column; the y-axis represents the percentage of airtime in the `Value` column; the lines are color-coded by channels based on the `Series` variable; the last argument sets the title of the graph.

The rest of the code is just applying cosmetic changes to how the line graphs look. You set the x-axis title and the y-axis title like so:
```
line_fig_ukr.update_layout(
                   xaxis_title='Date',
                   yaxis_title='Percentage of Airtime')

line_fig_rus.update_layout(
                   xaxis_title='Date',
                   yaxis_title='Percentage of Airtime')
```

Because you have limited space horizontally, it would look cleaner if year can be put on a new line. Here is how you set the label format on the x-axis.
```
line_fig_ukr.update_xaxes(tickformat="%b %d<br>%Y")
line_fig_rus.update_xaxes(tickformat="%b %d<br>%Y")
```
`%b` means the short version of month (e.g., Dec); `%d` means the two-digit day of month (01-31); `<br>` is the newline element in HTML; `%Y` means the full version of year (e.g., 2024).
 
Finally, note that the two returned objects (`line_fig_ukr` and `line_fig_rus`) should be ordered in the same way as how the output variables are ordered in the callback decorator (i.e., Ukraine's line graph goes first).

### Testing the Dashboard

Now you can add the following line to actually see and test the created dashboard.

```
app.run_server(debug=True)
```

It is recommended to turn on the debug mode so that any errors can be looked into when needed.

You need to put all the code you have written so far into a single `.py` file and name it such as `app.py`. The complete code [is provided here](https://github.com/programminghistorian/ph-submissions/blob/gh-pages/assets/interactive-data-visualization-dashboard/app.py) for convenience. 

In the command line, execute `$python app.py`. Then, a server address will appear, and you will need to copy and paste this address into a web browser to launch the dashboard. Do not close the command line program when the server is running. 

When you are done, in the command line, press `ctrl`+`c` on keyboard to stop the server. 

In a Jupyter Notebook, you can also choose to review the dashboard as a cell output (again, please refer to [the notebook version of the code](https://github.com/programminghistorian/ph-submissions/blob/gh-pages/assets/interactive-data-visualization-dashboard/interactive-data-visualization-dashboard.ipynb)).

The dashboard looks like this:

{% include figure.html filename="en-or-interactive-data-visualization-dashboard-04.png" alt="A screenshot showing what the dashboard looks like. There are two line graphs: one shows how media attention to Ukraine-related words in TV stations changes over time; the other shows the same but for Russia-related words" caption="Figure 4. Screenshot of the dashboard." %}

### Deploying the Dashboard
After the dashboard code is ready, in most cases, it is desirable to share your dashboards with the public using a URL. This means that you need to deploy your dashboard as a web application. 

In this section, you will achieve this goal by using a free service that allows us to host a dynamic web application: the free-tier web service provided by [Render](https://render.com/docs/web-services). In Render's free plan, the [RAM](https://en.wikipedia.org/wiki/Random-access_memory) limit is 512 MB at the time of writing. Our demo app takes about 90 MB, so the allocated RAM should be sufficient.

If you need more computing power and greater RAM, especially for a heavily used web application that is based on a large dataset, you may need to pay Render a certain fee. At the time of writing, other options that can be used to host dynamic web applications (instead of static sites) include [PythonAnywhere](https://www.pythonanywhere.com/), [Dash Enterprise](https://dash.plotly.com/dash-enterprise), [Heroku](https://devcenter.heroku.com/), [Amazon Web Services](https://aws.amazon.com/), and [Google App Engine](https://cloud.google.com/appengine). 

If you want to host your own server, or you have someone at your institution who can help you set up a dedicated server, the general approach to take is to find ways to deploy Flask apps (e.g., via [Apache2](https://ubuntu.com/server/docs/web-servers-apache)).


## Setting up in GitHub
You will need to upload the code folder, `ph-dash`, as a repository onto GitHub. This can be done in command line or in GitHub Desktop (see [this lesson if you are new to Git or GitHub](https://programminghistorian.org/en/lessons/building-static-sites-with-jekyll-github-pages#github--github-pages-)).

Then, install one more library for deployment: `$pip install gunicorn`. This library, [`gunicorn`](https://gunicorn.org/), is needed when Render sets up a web server for you.

In the repository, you need two essential files: A `.py` file that contains all of your Python code, and a file called `requirements.txt` that lists all the required Python libraries for the dashboard. Later, Render will read this file to install the needed Python libraries when you deploy the app. You can easily create this requirements file in command line using `$pip freeze > requirements.txt`. I have [provided a sample repository in this link for your reference](https://github.com/hluling/ph-dash).

### Setting up in Render
You can sign up for free using an email address. 

Then, navigate to the appropriate place to create a new "Web Service." 

If your GitHub repository is public, you can copy and paste the HTTPS address of the repository into the address of "Public Git Repository." 

Otherwise, you can also link your GitHub account with Render so that Render has access to your private repository.

Then, you will enter several pieces of information on the next screen. In addition to giving your dashboard a name, you need to configure two more settings (assuming all the populated default settings are correct). 

First, in "Start Command," change the input to `gunicorn app:server`. That is, the name after the colon must be the same as the object name of the server you set in your Python script. The name before the colon must be the same as the `.py` file in the repository.

Second, scroll down and find the section called "Environment Variables." Click "Add Environment Variable" and input `PYTHON_VERSION` as the key and the Python version that you use on your machine as the value (use `$python -V` in command line to check your Python version). If you don't explicitly specify the Python version this way, Render will use Python 3.7 as default, which may cause conflicts between this old Python version and the libraries' versions specified in `requirements.txt`.

Third, click "Create Web Service" and wait for several minutes for the application to build. When finished, you can see your dashboard via a URL like this: https://ph-dash-demo.onrender.com/

# Extending the Case Study
To demonstrate the wide applicability of the approach used in the case study, this lesson also shows how to create another dashboard using a different dataset. This extended case explores a different research question: How has the ranking of top non-English languages of American newspapers changed from the 1690s to the present? Specifically, a dashboard will be designed to show the top ten languages for each decade dating back to the 1690s, highlighting any shifts in their rankings and the emergence or decline of different languages over time. 

Whereas non-English Native American newspapers serve as a crucial medium for preserving cultural values, educating about Euro-American society, and negotiating tribal sovereignty,[^7] [^8] non-English immigrant newspapers help newcomers track the latest events in their home countries, provide ways to learn about the new country, and facilitates transition.[^9] Examining the top non-English American newspapers helps further investigation into the history of Native Americans, immigration history, the sociolinguistics and ideological landscapes in the U.S.,[^10] and various functions of ethnic media.[^11] 

## Dataset
The dashboard for the extended case relies on a publicly available dataset from [the Chronicling America project](https://chroniclingamerica.loc.gov/). Specifically, the data come from [the U.S. Newspaper Directory, 1690-Present](https://chroniclingamerica.loc.gov/search/titles/). This dataset tracks the metadata of historic American newspapers including the language of a newspaper. 

The data-retrieval tool is [Chronicling America's API](https://chroniclingamerica.loc.gov/about/api/). You can use this API to retrieve the needed data and prepare it for visualization in a tabular structure like this:

{% include figure.html filename="en-or-interactive-data-visualization-dashboard-05.png" alt="A screenshot showing what the dataset for the extended case looks like. The rows represent languages, the columns represent decades, and the cells represent count of newspapers." caption="Figure 5. Screenshot of the dataset for the extended case." %}

In Figure 4, the rows represent languages, the columns represent decades (from the 1690s to the 2020s), and the cells represent counts of newspaper. 

You can use the cell values to calculate the percentage for a given newspaper language in a certain decade. The percentage is calculated by dividing the number of newspapers for a given language in a certain decade by the total number of non-English newspapers in that decade, and then multiplying by 100. This gives the proportion of newspapers for that language relative to all non-English newspapers in the same decade. 

Then, you can visualize what the top 10 non-English newspapers are in a certain decade.

## Planning the Dashboard for the Extended Case
Let's create two pie charts side by side. The pie chart will show the top 10 non-English languages in percentage. Both charts will allow users to specify a decade, so the results from any two decades can be compared.

Regarding workflow, the following steps will be the same as described in the TV airtime case above: the same prerequisites will be needed; follow the same steps to create a new virtual environment; the same Python libraries will be needed; and you can follow the same steps to deploy the dashboard on Render. 

The data downloading procedure and the specific code used for the non-English-newspaper dashboard will be different from the TV airtime case. However, the underlying logic is the same: you start with data retrieval, prepare the data for visualization, code the dashboard frontend, then code the dashboard backend.

## Download Data

Because the download can take a long time, for the purpose of this lesson, it may be more helpful to focus on the dashboard-coding part directly. Thus, I provide the downloaded data in CSV [here](https://github.com/programminghistorian/ph-submissions/blob/gh-pages/assets/interactive-data-visualization-dashboard/data_lang_asrow.csv). Feel free to download this dataset directly and move on to the next section.

If you wonder how to download the data, I have provided [the script here](https://github.com/programminghistorian/ph-submissions/blob/gh-pages/assets/interactive-data-visualization-dashboard/rq2-download.py). The key step is to retry a query if there is an error returned by the server. This is probably due to the restriction that Chronicling America sets on how many requests in a given period can be sent to the server for downloads. No matter what your data demand is, always follow the rule set by the server and respect other users.

## Coding the non-English-newspaper Dashboard

The dashboard has two pie charts placed side by side, each of which has a dropdown menu for selecting decades. 

Both charts show the top-10 non-English languages in percentage. [The script for coding the dashboard can be found here](https://github.com/programminghistorian/ph-submissions/blob/gh-pages/assets/interactive-data-visualization-dashboard/app-rq2.py). 

If you have downloaded the data in CSV (see the previous section, above), you can run the script (`app-rq2.py`) directly without retrieving the data from Chronicling America. The final product looks like this:

{% include figure.html filename="en-or-interactive-data-visualization-dashboard-06.png" alt="A screenshot showing what the dashboard for the extended case looks like. There are two pie graphs: one shows the top 10 non-English newspapers in the U.S. in the 1690s; the other shows the same but for 2020s" caption="Figure 6. Screenshot of the non-English-newspaper dashboard. Each chart shows the top-10 non-English newspapers in a given decade. The percentage is the count of newspaper titles in a given non-English language divided by the sum of non-English newspaper titles." %}

The deployment procedure is the same as the TV airtime case. I would encourage you to refer to the same procedure outlined above to deploy the non-English-newspaper dashboard by yourself.

# Conclusion
Interactive visualization contributes to digital humanities by facilitating knowledge discovery and making the research output more accessible to the public. In this lesson, the key steps of creating and deploying an interactive dashboard using an open-source tool, Dash in Python, are demonstrated with two examples in media studies. Like [Shiny in R](https://doi.org/10.46430/phen0105), this is an approach that can be applied in a wide range of applications in digital humanities.

You have learned:
* How to retrieve publicly available data using an API using the `requests` library

  The data owner may have a restriction on the amount of data to be requested. You need to respect such a policy. As shown in the non-English newspaper case, something you could do is to time your requests (e.g., to resend a request after a certain time). 

* How to create the frontend of a dashboard
  
  The key is to understand the page layout as a grid that has a certain number of rows and columns. We have used a CSS framework called bootstrap that is user-friendly to non-web-developers. This can help you get off the ground quickly. We did not discuss the use of [wireframe](https://en.wikipedia.org/wiki/Website_wireframe) in the planning stage. But that is something useful to consider if you work in a team or your dashboard has more features and outputs.

* How to create the backend of a dashboard
  
  One takeaway regarding the backend: Any changes in the user input will trigger changes in the output. There are two things to keep in mind: The `id` you specified in the frontend component helps you to define the input and output variables in the callback decorator; the dataframe needs to be updated based on the changes in the input variables.

* How to deploy a dashboard for free

  Your dashboard needs to be seen and accessed by others to make great impacts. We have used Render for the deployment, but that is just one of the options at the time of writing. There are even more options if you are willing to pay a certain amount of money. The key is to find a platform that is reliable and sustainable for long term.

For the final message of this lesson, you are encouraged to adapt the provided code for your own purposes. There are also numerous free online resources for further study. To get started, all you need to do is just opening a command line tool, opening a text editor, and trying out the Python code yourself.

# References
[^1]: Ann Marie Ward. *Ireland Gender Pay Gap Analysis* (), https://genderpaygap.pythonanywhere.com/.

[^2]: Stephen Lacy et al., “Issues and Best Practices in Content Analysis,” *Journalism &amp; Mass Communication Quarterly* 92, no. 4 (September 28, 2015): 791–802, https://doi.org/10.1177/1077699015607338.

[^3]: Matthew Lombard, Jennifer Snyder‐Duch, and Cheryl Campanella Bracken. "Content Analysis in Mass Communication: Assessment and Reporting of Intercoder Reliability," *Human Communication Research* 28, no. 4 (2002): 587-604, https://doi.org/10.1111/j.1468-2958.2002.tb00826.x.

[^4]: Kimberly A. Neuendorf, *The Content Analysis Guidebook* (Thousand Oaks: Sage, 2017).

[^5]: Gross, Justin, and Dana Nestor. "Algorithmic Text Analysis: Toward More Careful Consideration of Qualitative Distinctions," in *Oxford Handbook of Engaged Methodological Pluralism in Political Science*, eds. Janet M. Box-Steffensmeier, Dino P. Christenson, and Valeria Sinclair-Chapman (Oxford Academic, 2023).

[^6]: André Baltz, “What’s so Social about Facebook? Distant Reading of Swedish Local Government Facebook Pages, 2010-2017,” *International Journal of Strategic Communication* 17, no. 2 (February 15, 2023): 118, https://doi.org/10.1080/1553118x.2022.2144324.

[^7]: Sharon Murphy, “Neglected Pioneers: 19th Century Native American Newspapers,” *Journalism History* 4, no. 3 (1977): 79, https://doi.org/10.1080/00947679.1977.12066850.

[^8]: Patty Loew and Kelly Mella, “Black Ink and the New Red Power: Native American Newspapers and Tribal Sovereignty,” *Journalism & Communication Monographs* 7, no. 3 (2005): 101, https://doi.org/10.1080/00947679.1977.12066850.

[^9]: Hermann W. Haller, “Ethnic-Language Mass Media and Language Loyalty in the United States Today: The Case of French, German and Italian,” *WORD* 39, no. 3 (December 1988): 187, https://doi.org/10.1080/00437956.1988.11435789.

[^10]: Andrew Hartman, “Language as Oppression: The English‐only Movement in the United States,” *Socialism and Democracy* 17, no. 1 (January 2003): 187–208, https://doi.org/10.1080/08854300308428349.

[^11]: K. Viswanath and Pamela Arora, “Ethnic Media in the United States: An Essay on Their Role in Integration, Assimilation, and Social Control,” *Mass Communication and Society* 3, no. 1 (February 2000): 39–56, https://doi.org/10.1207/s15327825mcs0301_03.
