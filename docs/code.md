## Repository

The [cffdrs-ng GitHub repository](https://github.com/nrcan-cfs-fire/cffdrs-ng/tree/main) contains the up-to-date code for the CFFDRS2025 modules in three programming languages: R, C, and Python. All three languages contain the same equations and perform the same tasks, the choice is yours depending on your system requirement.

### Fire Weather Index 2025 (FWI2025)
All code related to next generation CFFDRS development can be found on the [cffdrs-ng GitHub repository](https://github.com/nrcan-cfs-fire/cffdrs-ng/tree/main).

For each programming language, two scripts are required to generate FWI2025 outputs: 

1. NG_FWI : Holds the functions and equations for generating FWI2025 outputs. (**NG_FWI.r**, **NG_FWI.py**, **NG_FWI.c**) 

2. util : Includes basic functions that are not part of FWI2025 equations, but generate intermediate information for the calculation of the FWI2025 components (e.g., time of sunrise and sunset, number of sunlight hours).  This script also includes the process to generate the daily summary output. (**util.r**, **util.py**, **util.c**, **util.h**) 

For example, to use Python to generate FWI2025 outputs, you will need **NG_FWI.py** and **util.py**. 

NOTE:  Each language has library or package dependencies.  These must be installed prior to running NG_FWI.

R : lubridate and data.table 

Python : datetime, logging, math, numpy, pandas, os.path, sys 

C : stdlib.h, stdbool.h, string.h

## Documentation
The following resource describes the files and requirements to generate outputs for the next generation Fire Weather Index System (herein referred to as FWI2025). 

FWI2025 is part of a larger program to update the Canadian Forest Fire Danger Rating system (CFFDRS), and is available in three different programming languages: R, Python and C.  All three languages produce the same outputs, choose the language that suits your needs. 

Below describes the files, inputs, formats, and outputs in a general way for all three languages, any deviations for a specific language will be noted. 

FWI2025 code was originally written and tested using data tables and comma-separated value (.csv) files as inputs, as a result the output generates simple data tables that can then be exported.  Users can configure the input and output file types to fit their individual data streams.   

The [Tutorial page](/mock-up/tutorials/#how-to-calculate-hourly-fwi-the-new-fwi2025) includes steps to run the code with a publicly available test dataset.

Note on terms:

- The Fire Weather Index System calculations were originally designed to be calculated using data recorded at local *weather stations*.  As a result, descriptions below reference weather station data as input.  In reality, any weather data that provides a representative estimate of weather as per the standard parameters required for FWI System input can be used (e.g. gridded data, forecast data, etc.), see the [Weather Guide](https://ostrnrcan-dostrncan.canada.ca/handle/1845/219568) for a description of weather data input standards for the FWI System. 

### Input Parameters
FWI2025 inputs include hourly weather variables and an estimate of curing.  To read in detail on the inputs to the FWI System, [go here]().     

The column headers and data structure of the input data are as follows:

`id` : Weather station identifier, a unique number for the station, text or number.  
`lat` : Latitude. Location of weather station, decimal degrees (double precision).  
`long` : Longitude. Location of weather station, decimal degrees (double precision).  
`yr` : Year of weather station reading, number YYYY.  
`mon` : Month of weather station reading, number M or MM (must be consistent throughout the dataset).  
`day` : Day of weather station reading, number DD or D (must be consistent throughout the dataset).  
`hr` : Hour of weather station reading, number in military time (0-23).  
`temp` : Temperature in Celcius, number.  
`rh` : Relative humidity in percent, number (0-100).  
`ws` : Wind speed in km/hr., number.  
`prec` : Precipitation (rain) measured in millimeters (mm), number.  
`solrad` : Solar Radiation measured in kw/m2 (kilowatts per meter squared) for grassland codes, number.  OPTIONAL input, FWI2025 will generate based on default method. ([read more about solar radiation sensors and minimum requirements]())  
`percent_cured` : Percent Cured of grasses in open grassland, measured in percent for grassland codes, number (0-100). OPTIONAL input, FWI2025 will generate based on default method.   

In addition to the weather variables, the function to generate hourly FWI2025 outputs requires the timezone where the weather station is located and the start-up value for the moisture codes (e.g. FFMC of 85, DMC of 6 and DC of 15). See the next section below to see where they are used. 

The column headers can be lower case or upper case, the output format is set to upper case.

### Calculating FWI System components

The function in the NG_FWI file is built to generate FWI System outputs one station at a time but can accommodate a properly formatted input for multiple stations in the R and Python version. Except for the C version that requires input of a single year for a given station. 

The function to generate FWI2025 outputs can be found within the NG_FWI file look like this: 

R : `hFWI(df_wx, timezone, old ffmc, old dmc, old dc)`

- df_wx = data frame or formatted table (can have multiple years and stations, needs to be temporally sequential during a given year).
- timezone = timezone in which the weather station is located as the number of offset hours from UTC (e.g. for stations in Central Standard time the timezone is set to -6). 
- old dmc = the start-up value of the duff moisture code (e.g. 6, this is the default) 
- old dc = the start-up value of the drought code (e.g. 15, this is the default) 

Python : `hFWI(df_wx, ffmc_old=85, dmc_old=6, dc_old=15, silent=False)`

- df_wx = data frame or formatted table (can have multiple years and stations, needs to be      	                                temporally sequential during a given year) 
- timezone = timezone in which the weather station is located. 
- ffmc_old = the startup value of the fine fuel moisture code (e.g. 85, this is the default) 
- dmc_old = the start-up value of the duff moisture code (e.g. 6, this is the default) 
- dc_old = the start-up value of the drought code (e.g. 15, this is the default) 
- silent = print progress messages to monitor script. True or False, False by default.   

.C : ` `

The C version can be run from command line with the following arguments in order: 

local GMToffset: timezone in which the weather station is located. 

starting FFMC: the startup value of the fine fuel moisture code (e.g. 85) 

starting DMC: the start-up value of the duff moisture code (e.g. 6) 

starting DC: the start-up value of the drought code (e.g. 15) 

input file: name of .csv file containing data for a single station over the course of a year 

output file:  name of the file to output results into

### Output Parameters
Columns are added to the input data table, and a second summary data table is generated. The following output columns that are appended to the hourly weather data table: 

`timestamp` : date and time of weather and FWI variable. Timestamp YYYY-MM-DD HH:MM:SS. (not in C)  
`date` : YYYY-MM-DD Date type (without hh:mm:ss). (not in C)  
`sunrise` : Time of sunrise based on the latitude, longitude, and date (decimal time). (not in C)  
`sunset` : Time of sunset based on the latitude, longitude, and date (decimal time). (not in C)  
`sunlight_hours` : Number of hours between sunrise and sunset (decimal time). (not in C)  
`solrad` : Solar Radiation measured in kw/m2 (kilowatts per meter squared) for grassland codes, generated automatically if missing from input (number). (needs to be calculated using make_inputs.c if not given)  
`percent_cured` : percent of cured (dead) grass fuels in grasslands, generated automatically if missing from input.  Used exclusively in the Grassland calculations.  Percentage as number (0-100). (needs to be calculated using make_inputs.c if not given)  
`grass_fuel_load` : The standard grass fuel load is built into the Python and R code, 0.35kg/m2.  (needs to be calculated using make_inputs.c if not given, generates a column with the standard value)  
`ffmc` : Fine Fuel Moisture Code (number).  
`dmc` : Duff Moisture Code (number).  
`dc` : Drought Code (number).  
`isi` : Initial Spread Index (number).  
`bui` : Buildup Index (number).  
`fwi` : Fire Weather Index (number).  
`dsr` : Daily Severity Rating (number).  
`gfmc` : Grass Fuel Moisture Code, 0-101 similar to FFMC (number).  
`gsi` : Grassland Spread Index (number).  
`gfwi` : Grassland Fire Weather Index (number). 


The hourly FWI2025 outputs can be summarized in a variety of ways, and to inspire users to consider what summaries would be relevant to their situation, a function within the ‘util’ file summarizes the day into some basic metrics. 

The function to generate daily summaries : 

R (found within util.r) :  

```r
generate_daily_summaries <- function(hourly_data) {}
```

where : hourly_data = the output dataframe generated by NG_FWI 

Python (found within util.py) : 

```python
def generate_daily_summaries(hourly_data): 
```

where : hourly_data = the output data generated by NG_FWI 

C (found within util.h) : hourly_data = the output dataframe generated by NG_FWI

### Daily Summary Outputs
`wstnid` : weather station id, or unique identifier for the weather station.  
`year` : Year, YYYY, number (e.g. 2024).  
`mon` : month, M, number (e.g. 3).  
`day` : day, D number (e.g. 8)  
`sunrise` : Time of sunrise. HH:MM:SS timestamp format in military time (e.g. 06:30:55).  
`sunset` : Time of sunset. HH:MM:SS timestamp format in military time (e.g. 19:45:05). 
`peak_time` : The hour, H in military time (0-23),  number format. The hour where one might expect maximum fire behaviour as expressed by a modified ISI (see definitions of wind_speed_smoothed and peak_isi_smoothed below).  If the peak ISI (from peak_isi_smoothed field) is less than five, the peak_time is set to 17:00 hours.  
`duration` : number of hours, number format.  Can also be called duration of a burning window, number of hours in the day where one could expect an active fire (where a modified ISI is equal or greater than five).  If the ISI did not reach 5 or greater, the duration is zero.  
`wind_speed_smoothed` : Wind Speed (km/hr), double precision. Hourly wind speed recorded based on a 10 min average can be noisy hour-to-hour, this makes it difficult to estimate the duration of a burning window.  To estimate the duration of a burning window and peak time, the hourly windspeed is smoothed and the ISI is recalculated based on the smoothed wind speed and FFMC.  
`peak_isi_smoothed` : Maximum Initial Spread Index (ISI) recalculated based on FFMC and smoothed wind speed from ‘wind_speed_smoothed’, double precision number.  Number, double precision.  
`ffmc` : Fine Fuel Moisture Code corresponding to the FFMC at the hour of peak time. Number, double precision.  
`dmc` : Duff Moisture Code corresponding to the DMC at the hour of peak. Number, double precision.  
`dc` : Drought Code corresponding to the DC at the hour of peak time. Number, double precision.  
`isi` : Initial Spread Index corresponding to the ISI at the hour of peak time. Number, double precision.  
`bui` : Buildup Index corresponding to the BUI at the hour of peak time. Number, double precision.  
`fwi` : Fire Weather Index corresponding to the FWI at the hour of peak time. Number, double precision.  
`dsr` : Daily Severity Rating corresponding to the DSR at the hour of peak time. Number, double precision.  
`gfmc` : Grass Fuel Moisture Code corresponding to the GFMC at the hour of peak time. Number, double precision.  
`gsi` : Grassland Spread Index corresponding to the GSI at the hour of peak time. Number, double precision.  
`gfwi` : Grassland Fire Weather Index corresponding to the GFWI at the hour of peak time. Number, double precision.  

To dive into the code and learn about the functions, go to the Code Documentation pages. 

To get started, a test script and test data can be accessed on the ng-cffdrs repository.  See the [Tutorial page](../tutorials/#how-to-calculate-hourly-fwi-the-new-fwi2025) for step-by-step instructions.  

To learn more about Next Generation CFFDRS, including FWI2025, go to Documentation and Tech Transfer. 

 

### Functions
[R](r)  
[Python](python)  
[C](c)  

## License
