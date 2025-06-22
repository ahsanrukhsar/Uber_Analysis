# Uber_Analysis

### 1. Initial Setup and Data Loading

The first part of the code imports necessary libraries and loads a sample Uber dataset.

* **`import pandas as pd`, `import numpy as np`, `import matplotlib.pyplot as plt`, `import seaborn as sns`**: These lines import fundamental libraries for data manipulation (`pandas`, `numpy`) and data visualization (`matplotlib.pyplot`, `seaborn`).
* **`import os`**: This imports the `os` module, which provides functions for interacting with the operating system, like listing directories.
* **`os.listdir(r"C:\Users\PMLS\Desktop\Project\Uber\Datasets")`**: This command lists all files and directories within the specified path, showing the various Uber datasets available.
* **`uber=pd.read_csv(r"C:\Users\PMLS\Desktop\Project\Uber\Datasets\uber-raw-data-janjune-15_sample.csv")`**: This loads a sample dataset named 'uber-raw-data-janjune-15\_sample.csv' into a pandas DataFrame called `uber`.
* **`uber.shape`**: Displays the number of rows and columns in the `uber` DataFrame.
* **`type(uber)`**: Confirms that `uber` is indeed a pandas DataFrame.

### 2. Data Cleaning and Preprocessing (Sample Data)

This section focuses on cleaning and preparing the `uber` sample dataset.

* **`uber.duplicated()`**: Checks for duplicate rows in the DataFrame.
* **`uber.duplicated().sum()`**: Counts the total number of duplicate rows.
* **`uber.drop_duplicates(inplace=True)`**: Removes the duplicate rows directly from the DataFrame (modifies in place).
* **`uber.duplicated().sum()`**: Re-checks to confirm that duplicates have been removed.
* **`uber.isnull().sum()`**: Counts the number of missing values for each column. We can see `Affiliated_base_num` has 1116 missing values.
* **`type(uber["Pickup_date"][0])`**: Checks the data type of the first element in the 'Pickup\_date' column, which is initially a string.
* **`uber['Pickup_date']=pd.to_datetime(uber['Pickup_date'])`**: Converts the 'Pickup\_date' column from string (object) to datetime objects, which is crucial for time-based analysis.
* **`uber['Pickup_date'].dtype`**: Verifies the data type conversion.
* **`uber.dtypes`**: Displays the data types of all columns in the DataFrame.
* **`uber['month']=uber['Pickup_date'].dt.month_name()`**: Extracts the full month name from the 'Pickup\_date' and creates a new 'month' column.
* **`uber['month'].value_counts().plot(kind='bar')`**: Generates a bar plot showing the distribution of rides across different months. This helps visualize which months have the highest activity.
* **`uber['weekday']=uber['Pickup_date'].dt.day_name()`**, **`uber['hours']=uber['Pickup_date'].dt.hour`**, **`uber['minutes']=uber['Pickup_date'].dt.minute`**: These lines extract the day of the week, hour, and minute from 'Pickup\_date' and create new respective columns.
* **`uber.head()`**: Displays the first few rows of the DataFrame with the newly added time-based columns.

### 3. Analyzing Monthly and Weekly Ride Patterns (Sample Data)

This part of the analysis uses pivot tables and plots to understand ride distribution.

* **`pivot=pd.crosstab(index=uber['month'],columns=uber['weekday'])`**: Creates a cross-tabulation (pivot table) to show the number of rides for each month and weekday combination.
* **`pivot.plot(kind='bar').legend(bbox_to_anchor=(1,1))`**: Generates a bar plot from the `pivot` table, visualizing monthly ride counts broken down by weekday. The `legend(bbox_to_anchor=(1,1))` places the legend outside the plot for better readability.
* **`summary=uber.groupby(['weekday','hours'], as_index=False).size()`**: Groups the data by 'weekday' and 'hours' and calculates the total number of rides (`size()`) for each combination. `as_index=False` prevents 'weekday' and 'hours' from becoming the index.
* **`sns.pointplot(x="hours",y="size",hue="weekday",data=summary).legend(bbox_to_anchor=(1,1))`**: Creates a point plot using Seaborn to visualize the average number of rides per hour, separated by weekday. This helps identify peak hours on different days.

### 4. Analyzing Active Vehicles by Base Number

This section shifts to a different dataset (`Uber-Jan-Feb-FOIL.csv`) to analyze active vehicles per dispatching base.

* **`uber_foil=pd.read_csv(r"C:\Users\PMLS\Desktop\Project\Uber\Datasets/Uber-Jan-Feb-FOIL.csv")`**: Loads the 'Uber-Jan-Feb-FOIL.csv' dataset.
* **`uber_foil.head(3)`**: Displays the first three rows of this new DataFrame.
* **`uber_foil.describe()`**: Provides descriptive statistics (count, mean, std, min, max, quartiles) for the numerical columns in `uber_foil`.
* **`!pip install chart_studio !pip install plotly`**: Installs the necessary `chart_studio` and `plotly` libraries if they aren't already installed. These are used for interactive visualizations.
* **`import chart_studio.plotly as py`, `import plotly.graph_objs as go`, `import plotly.express as px`, `from plotly.offline import download_plotlyjs, init_notebook_mode, plot, iplot`**: Imports specific modules from Plotly for creating interactive plots.
* **`init_notebook_mode(connected=True)`**: Initializes Plotly for offline use in Jupyter notebooks.
* **`px.box(x='dispatching_base_number',y='active_vehicles',data_frame=uber_foil)`**: Creates an interactive box plot to visualize the distribution of 'active\_vehicles' for each 'dispatching\_base\_number'. Box plots show the median, quartiles, and potential outliers.
* **`px.violin(x='dispatching_base_number',y='active_vehicles',data_frame=uber_foil)`**: Creates an interactive violin plot, which is similar to a box plot but also shows the probability density of the data at different values, providing a richer view of the distribution.

### 5. Concatenating Multiple Raw Data Files

This part combines several raw Uber data files from 2014 to 2015.

* **`files=os.listdir(r"C:\Users\PMLS\Desktop\Project\Uber\Datasets")[-8:]`**: Gets the last 8 filenames from the dataset directory listing. This assumes these are the raw data files.
* **`files.remove('uber-raw-data-janjune-15_sample.csv')`** and **`files.remove('uber-raw-data-janjune-15.csv')`**: Removes the already loaded sample file and the full 'janjune-15' file from the list, as we're aiming to concatenate the distinct 2014 monthly files.
* **`final=pd.DataFrame()`**: Initializes an empty DataFrame called `final`.
* **`path=r"C:\Users\PMLS\Desktop\Project\Uber\Datasets"`**: Stores the dataset directory path.
* **`for file in files: ...`**: This loop iterates through the selected raw data files:
    * **`current_df=pd.read_csv(path+'/'+file)`**: Reads each CSV file into a temporary DataFrame `current_df`.
    * **`final=pd.concat([current_df,final])`**: Concatenates `current_df` with the `final` DataFrame. This builds up a single DataFrame containing data from all specified files.
* **`final.shape`**: Displays the dimensions of the combined `final` DataFrame.
* **`final.duplicated().sum()`**: Counts duplicates in the concatenated DataFrame.
* **`final.drop_duplicates(inplace=True)`**: Removes duplicates from `final`.
* **`final.shape`**: Shows the shape after duplicate removal.
* **`final.head(3)`**: Displays the first three rows of the combined and cleaned `final` DataFrame.

### 6. Analyzing Rush Hour Locations with Heatmap

This section focuses on identifying high-demand areas using geographical coordinates.

* **`rush_uber = final.groupby(['Lat','Lon'], as_index=False).size()`**: Groups the `final` DataFrame by 'Lat' (latitude) and 'Lon' (longitude) and counts the number of rides at each unique geographical point.
* **`rush_uber.head(3)`**: Shows the first three entries of the `rush_uber` DataFrame.
* **`!pip install folium`**: Installs the `folium` library, which is used for creating interactive maps.
* **`import folium`**: Imports the `folium` library.
* **`basemap=folium.Map()`**: Creates a basic Folium map object.
* **`from folium.plugins import HeatMap`**: Imports the `HeatMap` plugin from Folium.
* **`HeatMap(rush_uber).add_to(basemap)`**: Creates a heatmap layer based on the latitude, longitude, and size (number of rides) from `rush_uber` and adds it to the `basemap`. Areas with a higher concentration of rides will appear "hotter" on the map, indicating rush locations.
* **`basemap`**: Displays the interactive map with the heatmap.

### 7. Pairwise Analysis of Rush on Hour and Weekday (Combined Data)

This final part analyzes ride patterns in the combined dataset based on the day of the month and hour of the day.

* **`final.columns`** and **`final.head(3)`** and **`final.dtypes`**: These commands inspect the columns, first few rows, and data types of the `final` DataFrame. Note that 'Date/Time' is still an `object` (string) type here.
* **`final["Date/Time"]=pd.to_datetime(final['Date/Time'],format="%m/%d/%Y %H:%M:%S")`**: Converts the 'Date/Time' column to datetime objects using a specified format, essential for extracting time components.
* **`final["Date/Time"].dtype`**: Confirms the data type conversion to datetime.
* **`final['Day']=final["Date/Time"].dt.day`** and **`final['hour']=final["Date/Time"].dt.hour`**: Extracts the day of the month and hour of the day into new columns.
* **`final.head(3)`**: Displays the updated `final` DataFrame with 'Day' and 'hour' columns.
* **`pivot=final.groupby(['Day','hour']).size().unstack()`**: Creates a pivot table that shows the count of rides for each combination of 'Day' and 'hour'. `unstack()` pivots the 'hour' column to become the new columns of the DataFrame.
* **`pivot.style.background_gradient()`**: Applies a color gradient to the pivot table, making it easier to visually identify patterns of high and low ride activity across days and hours.
* **`def gen_pivot_table(df,col1,col2): ... return pivot.style.background_gradient()`**: Defines a reusable function `gen_pivot_table` that takes a DataFrame and two column names, creates a pivot table by grouping and unstacking, and then applies a background gradient for visualization. This promotes code reusability.
* **`gen_pivot_table(final,"hour","Day")`**: Calls the `gen_pivot_table` function to create a pivot table showing ride counts with 'hour' as rows and 'Day' as columns, with a background gradient for easy interpretation.
