# Python-API-challenge
Challenge assignment for Pandas

In this challenge, I again utilized the formatting and sample code for the provided starter code throughout, adding sections and sub-sections for clarity.

Some sections inspired by/utilyzing code from elsewhere are noted below:

WeatherPY:

--------------------------------------------------
Setup
--------------------------------------------------

This setup section including the code to generate random geographic cooridinates and nearest city 
to teach latitude and longitude combination was provided.  
    Subsection "Starter Code to Generate Random Geographic Coordinates and a List of Cities"
    Subsection "Generate the Cities List by Using the `citipy` Library"

--------------------------------------------------
Requirement 1
--------------------------------------------------
This setup section's formatting to pull and store data from the API
    Subsection "Use the OpenWeatherMap API to retrieve weather data from the cities list generated"

This section's read/write CSV file code as it was provided in starter code.

This section's layout for plotting and saving figures was used.

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

    