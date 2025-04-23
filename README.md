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



# Discussion

# Conclusion

