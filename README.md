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


# Test Equipment


# Test Procedures


# Test Results

| **Goal**   | Access the station database, filter for water quality measurement site names, and show unique location info |
|------------|------------------------------------------------------------------------------------------------------------|
| **Model**  | GPT-4 (ChatGPT-4, April 2025 version) |
| **Prompt** | Generate a Python function that accesses the database, filters for the names of water quality measurement sites, and displays the location information for all sites without repetition. |

---

| **Goal**   | Create an interactive map showing the location of all stations from the database |
|------------|----------------------------------------------------------------------------------|
| **Model**  | GPT-4 (ChatGPT-4, April 2025 version) |
| **Prompt** | Create a map that pinpoints the location of every station in the database. |

---

| **Goal**   | Request the full Python code used to generate the station map |
|------------|---------------------------------------------------------------|
| **Model**  | GPT-4 (ChatGPT-4, April 2025 version) |
| **Prompt** | Show Python code to generate the above map |

### Please generate a Python function that accesses the database, filters for the names of water quality measurement sites, and displays the location information for all sites without repetition.

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

### Create a map that pinpoints the location of every station in the database.
### Show python code to generate the map.

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
### Generate a python function that accesses Narrowresult, filters for a desired water quality characteristic, and plots the results with pH.
![image](https://github.com/user-attachments/assets/ed03d7e2-677a-46cb-8fb6-8b6a246e839d)
### Can you please generate with turbidity?
![image](https://github.com/user-attachments/assets/b4ccb42a-c5b9-4ffb-9bdd-26cc67d9e24f)
### Can you please show me the full python code you used to generate these graphs?
import pandas as pd
import matplotlib.pyplot as plt


def filter_and_plot_water_quality(dataframe, characteristic_name):
    """
    Filter *dataframe* for a specific water-quality characteristic
    and plot the result values over time.
    """
    filtered_df = dataframe[dataframe["CharacteristicName"] == characteristic_name].copy()
    filtered_df["ActivityStartDate"] = pd.to_datetime(
        filtered_df["ActivityStartDate"], errors="coerce"
    )
    filtered_df = filtered_df.dropna(subset=["ActivityStartDate", "ResultMeasureValue"])
    filtered_df["ResultMeasureValue"] = pd.to_numeric(
        filtered_df["ResultMeasureValue"], errors="coerce"
    )
    filtered_df = filtered_df.sort_values(by="ActivityStartDate")

    plt.figure(figsize=(12, 6))
    plt.plot(
        filtered_df["ActivityStartDate"],
        filtered_df["ResultMeasureValue"],
        marker="o",
        linestyle="-",
    )
    plt.title(f"{characteristic_name} Levels Over Time")
    plt.xlabel("Date")
    plt.ylabel("Result Measure Value")
    plt.grid(True)
    plt.tight_layout()
    plt.show()


def filter_and_plot_multiple_characteristics(dataframe, characteristic_names):
    """
    Filter *dataframe* for several characteristics and plot them
    together on one chart.
    """
    plt.figure(figsize=(14, 7))

    for characteristic in characteristic_names:
        filtered_df = dataframe[dataframe["CharacteristicName"] == characteristic].copy()
        filtered_df["ActivityStartDate"] = pd.to_datetime(
            filtered_df["ActivityStartDate"], errors="coerce"
        )
        filtered_df = filtered_df.dropna(subset=["ActivityStartDate", "ResultMeasureValue"])
        filtered_df["ResultMeasureValue"] = pd.to_numeric(
            filtered_df["ResultMeasureValue"], errors="coerce"
        )
        filtered_df = filtered_df.sort_values(by="ActivityStartDate")

        plt.plot(
            filtered_df["ActivityStartDate"],
            filtered_df["ResultMeasureValue"],
            marker="o",
            linestyle="-",
            label=characteristic,
        )

    plt.title("Water-Quality Characteristics Over Time")
    plt.xlabel("Date")
    plt.ylabel("Result Measure Value")
    plt.legend()
    plt.grid(True)
    plt.tight_layout()
    plt.show()


def filter_and_plot_dual_axis(dataframe, characteristic_1, characteristic_2):
    """
    Plot two characteristics on separate y-axes versus time.
    """
    df1 = dataframe[dataframe["CharacteristicName"] == characteristic_1].copy()
    df1["ActivityStartDate"] = pd.to_datetime(df1["ActivityStartDate"], errors="coerce")
    df1 = df1.dropna(subset=["ActivityStartDate", "ResultMeasureValue"])
    df1["ResultMeasureValue"] = pd.to_numeric(df1["ResultMeasureValue"], errors="coerce")
    df1 = df1.sort_values(by="ActivityStartDate")

    df2 = dataframe[dataframe["CharacteristicName"] == characteristic_2].copy()
    df2["ActivityStartDate"] = pd.to_datetime(df2["ActivityStartDate"], errors="coerce")
    df2 = df2.dropna(subset=["ActivityStartDate", "ResultMeasureValue"])
    df2["ResultMeasureValue"] = pd.to_numeric(df2["ResultMeasureValue"], errors="coerce")
    df2 = df2.sort_values(by="ActivityStartDate")

    fig, ax1 = plt.subplots(figsize=(14, 7))

    ax1.plot(df1["ActivityStartDate"], df1["ResultMeasureValue"], "b-", label=characteristic_1)
    ax1.set_xlabel("Date")
    ax1.set_ylabel(characteristic_1, color="b")
    ax1.tick_params(axis="y", labelcolor="b")

    ax2 = ax1.twinx()
    ax2.plot(df2["ActivityStartDate"], df2["ResultMeasureValue"], "r-", label=characteristic_2)
    ax2.set_ylabel(characteristic_2, color="r")
    ax2.tick_params(axis="y", labelcolor="r")

    plt.title(f"{characteristic_1} and {characteristic_2} Over Time")
    fig.tight_layout()
    plt.grid(True)
    plt.show()



# Discussion

# Conclusion

