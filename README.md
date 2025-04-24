# BAE305-SP25-Lab10
# Laboratory Report for Lab 10 of BAE305 Spring 2025
# Lab 10 - Python and AI
* By: Abby Phillips and Audrey Suit
* Submitted: April 23, 2025


# Summary  


# Materials
- Computer running
- 
# Assembly Procedures  

## Assembly Procedures

### Part 1: Map It

#### 1. Data Exploration
- Downloaded **station.csv** from Canvas and opened in Excel.
- Examined key fields: `MonitoringLocationName`, `MonitoringLocationTypeName`, `LatitudeMeasure`, `LongitudeMeasure`.

#### 2. Environment Setup
- Launched a Jupyter Notebook (Anaconda).
- Imported required libraries:
  ```python
  import pandas as pd
  import folium
  ```

#### 3. AI-Assisted Code Generation
- Prompted GPT-4 to generate a function that:
  1. Reads `station.csv`.
  2. Filters for water-quality site types (“Stream” and “River”).
  3. Drops duplicates or irrelevant rows.
  4. Returns a cleaned DataFrame of unique stations.

#### 4. Mapping with Folium
- Centered the map on the mean latitude/longitude of valid stations.
- Added a `folium.Marker` for each site (with popup & tooltip).
- Saved the interactive map as `station_map.html`.

---

### Part 2: What’s Normal

#### 1. Understanding the Dataset
- Downloaded **narrowresult.csv** and opened in Excel.
- Confirmed columns:
  - `CharacteristicName` (e.g., pH, Turbidity)
  - `ActivityStartDate` (timestamp of measurement)
  - `ResultMeasureValue` (numeric result)

#### 2. Single-Axis Plotting
- Uploaded `narrowresult.csv` to GitHub Codespaces.
- Prompted GPT-4 to write a script that:
  - Filters by a chosen characteristic.
  - Plots `ResultMeasureValue` vs. `ActivityStartDate` using Matplotlib.

#### 3. Dual-Axis Overlay
- Prompted GPT-4 again to plot two characteristics (pH & Turbidity) on one timeline with separate left/right y-axes.
- Iteratively refined:
  - Axis labels
  - Legend placement
  - Grid lines
  - Marker styles
  - Figure dimensions

---

### Part 3: Streamlit App Development

#### 1. Environment Setup & Repo
- Linked Streamlit.io ↔ GitHub via OAuth.
- Created public repo **BAE305-SP25-Lab10** in Codespaces.
- Added:
  - `streamlit_app.py`
  - `requirements.txt` (pins: `pandas`, `matplotlib`, `folium`, `streamlit`)

#### 2. User Interface
- **File uploaders** for `station.csv` & `narrowresult.csv`.
- **Sidebar controls** for:
  - Contaminant (`CharacteristicName`)
  - Date range
  - Value range

#### 3. Backend Logic
- Loaded uploads into pandas.
- Filtered by selected contaminant + date/value bounds.
- Merged to identify stations with measurements in that window.

#### 4. Visual Outputs
- **Interactive Folium map** showing filtered station locations.
- **Matplotlib time-series plot** of the contaminant trend.

#### 5. Development Workflow
- Edited code in GitHub Codespaces.
- On session restart, reinstalled dependencies with:
  ```bash
  pip install -r requirements.txt
  ```
- Ensured `requirements.txt` resides alongside `streamlit_app.py` in the repo.


# Test Equipment


# Test Procedures


# Test Results
### Part 1: Map It
| **Goal**   | Access the station database, filter for water quality measurement site names, and show unique location info |
|------------|------------------------------------------------------------------------------------------------------------|
| **Model**  | GPT-4 (ChatGPT-4, April 2025 version) |
| **Prompt** | Generate a Python function that accesses the database, filters for the names of water quality measurement sites, and displays the location information for all sites without repetition. |

```python
# paste here
import pandas as pd

def get_unique_water_quality_sites(data):
    # Filter for rows where the MonitoringLocationTypeName contains water quality-related sites (e.g., stream, river, etc.)
    water_quality_sites = data[data['MonitoringLocationTypeName'].str.contains('Stream|River', na=False)]

    # Select only the relevant columns to display location information
    location_info = water_quality_sites[['MonitoringLocationName', 'MonitoringLocationIdentifier', 
                                         'MonitoringLocationTypeName', 'MonitoringLocationDescriptionText']]
    
    # Drop duplicate rows to ensure no repetition
    unique_location_info = location_info.drop_duplicates()

    # Display the filtered and unique location information
    import ace_tools as tools; tools.display_dataframe_to_user(name="Unique Water Quality Sites", dataframe=unique_location_info)
    return unique_location_info
    
    Call the function to display the results 
    unique_sites = get_unique_water_quality_sites(data)
```
---

| **Goal**   | Create an interactive map showing the location of all stations from the database |
|------------|----------------------------------------------------------------------------------|
| **Model**  | GPT-4 (ChatGPT-4, April 2025 version) |
| **Prompt** | Create a map that pinpoints the location of every station in the database. |

```python

import pandas as pd
import folium

# Load the station data
station_df = pd.read_csv("station.csv")  # Replace with your file path if needed

# Drop rows with missing latitude or longitude
valid_locations = station_df.dropna(subset=["LatitudeMeasure", "LongitudeMeasure"])

# Create a base map centered around the mean coordinates
m = folium.Map(
    location=[valid_locations["LatitudeMeasure"].mean(), 
              valid_locations["LongitudeMeasure"].mean()],
    zoom_start=7
)

# Add a marker for each station
for _, row in valid_locations.iterrows():
    folium.Marker(
        location=[row["LatitudeMeasure"], row["LongitudeMeasure"]],
        popup=row["MonitoringLocationName"],
        tooltip=row["MonitoringLocationIdentifier"]
    ).add_to(m)

# Save the map to an HTML file
m.save("station_map.html")
```
![image](https://github.com/user-attachments/assets/5c9f58fc-b2d8-4671-9f29-d477e814ca42)

---

| **Goal**   | Request the full Python code used to generate the station map |
|------------|---------------------------------------------------------------|
| **Model**  | GPT-4 (ChatGPT-4, April 2025 version) |
| **Prompt** | Show Python code to generate the above map |

```python

import pandas as pd
import matplotlib.pyplot as plt

def filter_and_plot_water_quality(dataframe, characteristic_name):
    """
    Filters the dataframe for a specific water quality characteristic
    and plots the result values over time.
    """
    filtered_df = dataframe[dataframe['CharacteristicName'] == characteristic_name].copy()
    filtered_df['ActivityStartDate'] = pd.to_datetime(filtered_df['ActivityStartDate'], errors='coerce')
    filtered_df = filtered_df.dropna(subset=['ActivityStartDate', 'ResultMeasureValue'])
    filtered_df['ResultMeasureValue'] = pd.to_numeric(filtered_df['ResultMeasureValue'], errors='coerce')
    filtered_df = filtered_df.sort_values(by='ActivityStartDate')

    plt.figure(figsize=(12, 6))
    plt.plot(filtered_df['ActivityStartDate'], filtered_df['ResultMeasureValue'], marker='o', linestyle='-')
    plt.title(f"{characteristic_name} Levels Over Time")
    plt.xlabel("Date")
    plt.ylabel("Result Measure Value")
    plt.grid(True)
    plt.tight_layout()
    plt.show()


def filter_and_plot_multiple_characteristics(dataframe, characteristic_names):
    """
    Filters the dataframe for multiple water quality characteristics
    and plots each characteristic's result values over time.
    """
    plt.figure(figsize=(14, 7))

    for characteristic in characteristic_names:
        filtered_df = dataframe[dataframe['CharacteristicName'] == characteristic].copy()
        filtered_df['ActivityStartDate'] = pd.to_datetime(filtered_df['ActivityStartDate'], errors='coerce')
        filtered_df = filtered_df.dropna(subset=['ActivityStartDate', 'ResultMeasureValue'])
        filtered_df['ResultMeasureValue'] = pd.to_numeric(filtered_df['ResultMeasureValue'], errors='coerce')
        filtered_df = filtered_df.sort_values(by='ActivityStartDate')

        plt.plot(filtered_df['ActivityStartDate'], filtered_df['ResultMeasureValue'],
                 marker='o', linestyle='-', label=characteristic)

    plt.title("Water Quality Characteristics Over Time")
    plt.xlabel("Date")
    plt.ylabel("Result Measure Value")
    plt.legend()
    plt.grid(True)
    plt.tight_layout()
    plt.show()


def filter_and_plot_dual_axis(dataframe, characteristic_1, characteristic_2):
    """
    Plots two water quality characteristics over time with separate y-axes.
    """
    df1 = dataframe[dataframe['CharacteristicName'] == characteristic_1].copy()
    df1['ActivityStartDate'] = pd.to_datetime(df1['ActivityStartDate'], errors='coerce')
    df1 = df1.dropna(subset=['ActivityStartDate', 'ResultMeasureValue'])
    df1['ResultMeasureValue'] = pd.to_numeric(df1['ResultMeasureValue'], errors='coerce')
    df1 = df1.sort_values(by='ActivityStartDate')

    df2 = dataframe[dataframe['CharacteristicName'] == characteristic_2].copy()
    df2['ActivityStartDate'] = pd.to_datetime(df2['ActivityStartDate'], errors='coerce')
    df2 = df2.dropna(subset=['ActivityStartDate', 'ResultMeasureValue'])
    df2['ResultMeasureValue'] = pd.to_numeric(df2['ResultMeasureValue'], errors='coerce')
    df2 = df2.sort_values(by='ActivityStartDate')

    fig, ax1 = plt.subplots(figsize=(14, 7))

    ax1.plot(df1['ActivityStartDate'], df1['ResultMeasureValue'], 'b-', label=characteristic_1)
    ax1.set_xlabel('Date')
    ax1.set_ylabel(f'{characteristic_1}', color='b')
    ax1.tick_params(axis='y', labelcolor='b')

    ax2 = ax1.twinx()
    ax2.plot(df2['ActivityStartDate'], df2['ResultMeasureValue'], 'r-', label=characteristic_2)
    ax2.set_ylabel(f'{characteristic_2}', color='r')
    ax2.tick_params(axis='y', labelcolor='r')

    plt.title(f"{characteristic_1} and {characteristic_2} Over Time")
    fig.tight_layout()
    plt.grid(True)
    plt.show()
```
### Part 2: What's Normal?

| **Goal**   | Generate a Python function that accesses the Narrowresult dataset, filters for a desired water quality characteristic, and plots the results |
|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| **Model**  | GPT-4 (ChatGPT-4, April 2025 version) |
| **Prompt** | Generate a Python function that accesses Narrowresult, filters for a desired water quality characteristic, and plots the results with pH. Can you please generate with turbidity? |
![image](https://github.com/user-attachments/assets/150dd0f6-17db-42b0-be2f-1afc5321ae9c)
![image](https://github.com/user-attachments/assets/e83d219f-5658-4a07-8291-ccbfa99d5a06)

| **Goal** | Generate a Python function that accesses the Narrowresult dataset and plots multiple datasets on the same plot |
|----------|----------------------------------------------------------------------------------------------------------------|
| **Model** | GPT-4 (ChatGPT-4, April 2025 version) |
| **Prompt** | Can you please modify the python code so I can ask for two characteristics at the same time with 2 y axis? |
![image](https://github.com/user-attachments/assets/7fcbb5c8-5d0e-44b1-af68-3c7579efe99d)
| **Goal** | Request the full Python code used to generate the turbidity and pH comparison graphs |
|----------|-------------------------------------------------------------------------------------|
| **Model** | GPT-4 (ChatGPT-4, April 2025 version) |
| **Prompt** | Can you please show me the full Python code you used to generate these graphs? |

```python
import pandas as pd
import matplotlib.pyplot as plt

def filter_and_plot_water_quality(dataframe, characteristic_name):
    """
    Filters the dataframe for a specific water quality characteristic
    and plots the result values over time.
    """
    filtered_df = dataframe[dataframe['CharacteristicName'] == characteristic_name].copy()
    filtered_df['ActivityStartDate'] = pd.to_datetime(filtered_df['ActivityStartDate'], errors='coerce')
    filtered_df = filtered_df.dropna(subset=['ActivityStartDate', 'ResultMeasureValue'])
    filtered_df['ResultMeasureValue'] = pd.to_numeric(filtered_df['ResultMeasureValue'], errors='coerce')
    filtered_df = filtered_df.sort_values(by='ActivityStartDate')

    plt.figure(figsize=(12, 6))
    plt.plot(filtered_df['ActivityStartDate'], filtered_df['ResultMeasureValue'], marker='o', linestyle='-')
    plt.title(f"{characteristic_name} Levels Over Time")
    plt.xlabel("Date")
    plt.ylabel("Result Measure Value")
    plt.grid(True)
    plt.tight_layout()
    plt.show()


def filter_and_plot_multiple_characteristics(dataframe, characteristic_names):
    """
    Filters the dataframe for multiple water quality characteristics
    and plots each characteristic's result values over time.
    """
    plt.figure(figsize=(14, 7))

    for characteristic in characteristic_names:
        filtered_df = dataframe[dataframe['CharacteristicName'] == characteristic].copy()
        filtered_df['ActivityStartDate'] = pd.to_datetime(filtered_df['ActivityStartDate'], errors='coerce')
        filtered_df = filtered_df.dropna(subset=['ActivityStartDate', 'ResultMeasureValue'])
        filtered_df['ResultMeasureValue'] = pd.to_numeric(filtered_df['ResultMeasureValue'], errors='coerce')
        filtered_df = filtered_df.sort_values(by='ActivityStartDate')

        plt.plot(filtered_df['ActivityStartDate'], filtered_df['ResultMeasureValue'],
                 marker='o', linestyle='-', label=characteristic)

    plt.title("Water Quality Characteristics Over Time")
    plt.xlabel("Date")
    plt.ylabel("Result Measure Value")
    plt.legend()
    plt.grid(True)
    plt.tight_layout()
    plt.show()


def filter_and_plot_dual_axis(dataframe, characteristic_1, characteristic_2):
    """
    Plots two water quality characteristics over time with separate y-axes.
    """
    df1 = dataframe[dataframe['CharacteristicName'] == characteristic_1].copy()
    df1['ActivityStartDate'] = pd.to_datetime(df1['ActivityStartDate'], errors='coerce')
    df1 = df1.dropna(subset=['ActivityStartDate', 'ResultMeasureValue'])
    df1['ResultMeasureValue'] = pd.to_numeric(df1['ResultMeasureValue'], errors='coerce')
    df1 = df1.sort_values(by='ActivityStartDate')

    df2 = dataframe[dataframe['CharacteristicName'] == characteristic_2].copy()
    df2['ActivityStartDate'] = pd.to_datetime(df2['ActivityStartDate'], errors='coerce')
    df2 = df2.dropna(subset=['ActivityStartDate', 'ResultMeasureValue'])
    df2['ResultMeasureValue'] = pd.to_numeric(df2['ResultMeasureValue'], errors='coerce')
    df2 = df2.sort_values(by='ActivityStartDate')

    fig, ax1 = plt.subplots(figsize=(14, 7))

    ax1.plot(df1['ActivityStartDate'], df1['ResultMeasureValue'], 'b-', label=characteristic_1)
    ax1.set_xlabel('Date')
    ax1.set_ylabel(f'{characteristic_1}', color='b')
    ax1.tick_params(axis='y', labelcolor='b')

    ax2 = ax1.twinx()
    ax2.plot(df2['ActivityStartDate'], df2['ResultMeasureValue'], 'r-', label=characteristic_2)
    ax2.set_ylabel(f'{characteristic_2}', color='r')
    ax2.tick_params(axis='y', labelcolor='r')

    plt.title(f"{characteristic_1} and {characteristic_2} Over Time")
    fig.tight_layout()
    plt.grid(True)
    plt.show()
```
### Part 3: Streamlit

| **Goal** | Develop a Streamlit app that allows uploading station and result databases, filtering for a contaminant and range, and displaying a map and trend graph |
|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Model** | GPT-4 (ChatGPT-4, April 2025 version) |
| **Prompt** | Develop a Streamlit app that allows the user to upload both databases used in Part 1 and 2, to search for a contaminant in the databases. Once a contaminant has been selected you should be able to define the range of values and dates that you want to show. After modifying the ranges, update the map showing the location of the stations with the contaminant within that range and measured |

```python

import streamlit as st
import pandas as pd
import folium
from folium.plugins import MarkerCluster
from streamlit_folium import st_folium
import matplotlib.pyplot as plt

st.set_page_config(layout="wide")
st.title("Water Quality Monitoring Explorer")

# Upload CSV files
station_file = st.file_uploader("Upload station.csv (Part 1)", type=["csv"])
result_file = st.file_uploader("Upload narrowresult.csv (Part 2)", type=["csv"])

if station_file and result_file:
    # Load data
    station_df = pd.read_csv(station_file)
    result_df = pd.read_csv(result_file)

    # Ensure correct data types
    result_df['ActivityStartDate'] = pd.to_datetime(result_df['ActivityStartDate'], errors='coerce')
    result_df['ResultMeasureValue'] = pd.to_numeric(result_df['ResultMeasureValue'], errors='coerce')

    # Sidebar filters
    st.sidebar.header("Filters")

    # Filter by contaminant
    contaminants = result_df['CharacteristicName'].dropna().unique()
    selected_contaminant = st.sidebar.selectbox("Select a Contaminant", sorted(contaminants))

    filtered_results = result_df[result_df['CharacteristicName'] == selected_contaminant]

    # Filter by date range
    min_date = filtered_results['ActivityStartDate'].min()
    max_date = filtered_results['ActivityStartDate'].max()

    start_date, end_date = st.sidebar.date_input("Select Date Range", [min_date, max_date])

    # Filter by value range
    min_val = float(filtered_results['ResultMeasureValue'].min())
    max_val = float(filtered_results['ResultMeasureValue'].max())
    val_range = st.sidebar.slider("Select Value Range", min_val, max_val, (min_val, max_val))

    # Apply all filters
    mask = (
        (filtered_results['ActivityStartDate'] >= pd.to_datetime(start_date)) &
        (filtered_results['ActivityStartDate'] <= pd.to_datetime(end_date)) &
        (filtered_results['ResultMeasureValue'] >= val_range[0]) &
        (filtered_results['ResultMeasureValue'] <= val_range[1])
    )
    filtered_results = filtered_results[mask]

    # Join with station data
    merged = pd.merge(
        filtered_results,
        station_df,
        how='left',
        left_on='MonitoringLocationIdentifier',
        right_on='MonitoringLocationIdentifier'
    )
    merged = merged.dropna(subset=["LatitudeMeasure", "LongitudeMeasure"])

    # Map of stations
    st.subheader("Map of Monitoring Stations")
    map_center = [merged["LatitudeMeasure"].mean(), merged["LongitudeMeasure"].mean()]
    m = folium.Map(location=map_center, zoom_start=7)
    marker_cluster = MarkerCluster().add_to(m)

    for _, row in merged.iterrows():
        popup_text = f"""
        <b>Station:</b> {row['MonitoringLocationName']}<br>
        <b>Value:</b> {row['ResultMeasureValue']}<br>
        <b>Date:</b> {row['ActivityStartDate'].date()}
        """
        folium.Marker(
            location=[row["LatitudeMeasure"], row["LongitudeMeasure"]],
            popup=popup_text,
            tooltip=row["MonitoringLocationIdentifier"]
        ).add_to(marker_cluster)

    st_data = st_folium(m, width=900, height=500)

    # Trend chart
    st.subheader(f"{selected_contaminant} Trend Over Time")
    time_series = merged[['ActivityStartDate', 'ResultMeasureValue']].sort_values(by='ActivityStartDate')

    plt.figure(figsize=(12, 6))
    plt.plot(time_series['ActivityStartDate'], time_series['ResultMeasureValue'], marker='o', linestyle='-')
    plt.xlabel("Date")
    plt.ylabel("Result Measure Value")
    plt.title(f"{selected_contaminant} Levels Across All Stations")
    plt.grid(True)
    st.pyplot(plt)

else:
    st.info("Please upload both `station.csv` and `narrowresult.csv` to begin.")
```

https://bae305-sp25-lab10-abbyphillips.streamlit.app/


# Discussion

# Conclusion

