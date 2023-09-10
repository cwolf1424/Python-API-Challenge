# Python-API-challenge
Challenge assignment for Pandas

In this challenge, I again utilized the formatting and sample code for the provided starter code extensively throughout, adding sections and sub-sections for clarity.

Some sections specifically inspired by/utilyzing code from elsewhere are noted below:

WeatherPY:

--------------------------------------------------
Setup
--------------------------------------------------

This setup section including the code to generate random geographic cooridinates and nearest city 
to teach latitude and longitude combination was provided.  
    Subsection "Starter Code to Generate Random Geographic Coordinates and a List of Cities"
    Subsection "Generate the Cities List by Using the `citipy` Library"

        # Empty list for holding the latitude and longitude combinations
        lat_lngs = []

        # Empty list for holding the cities names
        cities = []

        # Range of latitudes and longitudes
        lat_range = (-90, 90)
        lng_range = (-180, 180)

        # Create a set of random lat and lng combinations
        lats = np.random.uniform(lat_range[0], lat_range[1], size=1500)
        lngs = np.random.uniform(lng_range[0], lng_range[1], size=1500)
        lat_lngs = zip(lats, lngs)

        # Identify nearest city for each lat, lng combination
        for lat_lng in lat_lngs:
            city = citipy.nearest_city(lat_lng[0], lat_lng[1]).city_name
            
            # If the city is unique, then add it to a our cities list
            if city not in cities:
                cities.append(city)

        # Print the city count to confirm sufficient count
        print(f"Number of cities in the list: {len(cities)}")

--------------------------------------------------
Requirement 1
--------------------------------------------------

This section's read/write CSV file code as it was provided in starter code.

    # Export the City_Data into a csv
    city_data_df.to_csv("output_data/cities.csv", index_label="City_ID")

    This section's layout for plotting and saving figures was used.

    # Read saved data
    city_data_df = pd.read_csv("output_data/cities.csv", index_col="City_ID")

    # Display sample data
    city_data_df.head()

This setup section's formatting to pull and store data from the API was provided in starter code.
    Subsection "Use the OpenWeatherMap API to retrieve weather data from the cities list generated"

    # Set the API base URL
    url = "https://api.openweathermap.org/data/2.5/weather?"

    # Define an empty list to fetch the weather data for each city
    city_data = []

    # Print to logger
    print("Beginning Data Retrieval     ")
    print("-----------------------------")

    # Create counters
    record_count = 1
    set_count = 1

    # Loop through all the cities in our list to fetch weather data
    for i, city in enumerate(cities):
            
        # Group cities in sets of 50 for logging purposes
        if (i % 50 == 0 and i >= 50):
            set_count += 1
            record_count = 0

        # Create endpoint URL with each city
        city_url = url + "appid=" + weather_api_key + "&q=" + city
        
        # Log the url, record, and set numbers
        print("Processing Record %s of Set %s | %s" % (record_count, set_count, city))

        # Add 1 to the record count
        record_count += 1

        # Run an API request for each of the cities
        try:
            # Parse the JSON and retrieve data
            city_weather = requests.get(city_url).json()

            # Parse out latitude, longitude, max temp, humidity, cloudiness, wind speed, country, and date
            city_lat = city_weather["coord"]["lat"]
            city_lng = city_weather["coord"]["lon"]
            city_max_temp = city_weather["main"]["temp_max"]
            city_humidity = city_weather["main"]["humidity"]
            city_clouds = city_weather["clouds"]["all"]
            city_wind = city_weather["wind"]["speed"]
            city_country = city_weather["sys"]["country"]
            city_date = city_weather["dt"]

            # Append the City information into city_data list
            city_data.append({"City": city, 
                            "Lat": city_lat, 
                            "Lng": city_lng, 
                            "Max Temp": city_max_temp,
                            "Humidity": city_humidity,
                            "Cloudiness": city_clouds,
                            "Wind Speed": city_wind,
                            "Country": city_country,
                            "Date": city_date})

        # If an error is experienced, skip the city
        except:
            print("City not found. Skipping...")
            pass
                
    # Indicate that Data Loading is complete 
    print("-----------------------------")
    print("Data Retrieval Complete      ")
    print("-----------------------------")

The nameing of the files to export was provided in starter code.
    plt.savefig("output_data/Fig1.png")
--------------------------------------------------
Requirement 2
--------------------------------------------------

This section's linear regression section formatting was inspired by Week 5, Day 3, Activity 8
    # Linear regression
    (slope, intercept, rvalue, pvalue, stderr) = linregress(x_values, y_values)
    regress_values = x_values * slope + intercept
    plt.plot(x_values, regress_values, "r")

    # Annotate
    line_eq = "y = " + str(round(slope,2)) + "x + " + str(round(intercept,2))
    plt.annotate(line_eq,(-55,10),color="red")
    plt.annotate(f'r = {rvalue}',(-55,5),color="r")


VacationPY (Incomplete):

--------------------------------------------------
Setup
--------------------------------------------------

This setup section was provided in the starter code:

    # Load the CSV file created in Part 1 into a Pandas DataFrame
    city_data_df = pd.read_csv("output_data/cities.csv")

    #Convert Max Temp to Celcius
    city_data_df["Max Temp"] = (city_data_df["Max Temp"]-273.15)

    # Display sample data
    city_data_df.head()

--------------------------------------------------
Step 3
--------------------------------------------------

Professor Benjamin Alford helped with the following in class as .append is no longer supported:

    pd.concat([hotel_df,hotel_names_df],ignore_index=True)

--------------------------------------------------
Step 4
--------------------------------------------------

The outline and some content of this section was provided in the starter code and then I used Week 6, Day 3 Activity 04-Ins_Nearest-Resturaunts for the params section:

    # Set parameters to search for a hotel
    limit = 1
    radius = 10000 

    lat = 0
    lng = 0
    params ={}

    # Print a message to follow up the hotel search
    print("Starting hotel search")

    # Iterate through the hotel_df DataFrame
    for index, row in hotel_df.iterrows():
        # get latitude, longitude from the DataFrame
        lat = row ["Lat"]
        lng = row ["Lng"]

        filters = f"circle:{lng},{lat},{radius}"
        bias = f"proximity:{lng},{lat}"
        category ="accommodation.hotel"

        # Add filter and bias parameters with the current city's latitude and longitude to the params dictionary
        params ["filter"] = filters
        params ["bias"] = bias
        params ["limit"] = limit
        params ["categories"] = category
        params ["apiKey"] = geoapify_key
        
        # Set base URL
        base_url = "https://api.geoapify.com/v2/places"


        # Make and API request using the params dictionaty
        name_address = requests.get(base_url, params=params)

        # Convert the API response to JSON format
        name_address = name_address.json()
        
        # Grab the first hotel from the results and store the name in the hotel_df DataFrame
        try:
            hotel_df.loc[index, "Hotel Name"] = name_address["features"][0]["properties"]["name"]
        except:
            # If no hotel is found, set the hotel name as "No hotel found".
            hotel_df.loc[index, "Hotel Name"] = "No hotel found"
            
        # Log the search results
        print(f"{hotel_df.loc[index, 'City']} - nearest hotel: {hotel_df.loc[index, 'Hotel Name']}")

    # Display sample data
    hotel_df

