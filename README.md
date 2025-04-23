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

# Please generate a Python function that accesses the database, filters for the names of water quality measurement sites, and displays the location information for all sites without repetition.

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

# Call the function to display the results
unique_sites = get_unique_water_quality_sites(data)


# Discussion


# Conclusion

