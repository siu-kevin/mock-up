## How to calculate hourly FWI – the new FWI2025
This tutorial is designed to be an example of how to calculate FWI2025 with tabular data.  The data has been formatted for correct column names and is in a sequential hourly format. The data has been pre-formatted for correct column names, and is in a sequential hourly format.  

\*R version is available

### What you'll need:

Go to the
<a href="https://github.com/nrcan-cfs-fire/cffdrs-ng/tree/main" target="_blank">CFFDRS-NG repo</a>
and fork/clone/download the following in the language of your choice: 

- **NG_FWI.r**, OR **NG_FWI.py**, OR **NG_FWI.c** 
- **util.r**, OR **util.py**, OR **util.c** and **util.h**
- <a href="https://github.com/nrcan-cfs-fire/cffdrs-ng/blob/main/data/wx_prf.csv" target="_blank"> **/data/wx_prf.csv** </a> (the data file we will work with)
- **Tutorial_NGFWI.r**, OR **Tutorial_NGFWI.py**

Prior to running this tutorial, the user should ensure they have the necessary packages and libraries installed.  Each programming language references different packages or libraries, see [Code#Documentation](/mock-up/code/#documentation) for more details, or look in the code files.
#### Step 1:  
Open the data file:
<a href="https://github.com/nrcan-cfs-fire/cffdrs-ng/blob/main/data/wx_prf.csv" target="_blank"> wx_prf.csv </a>

About the file: This file contains hourly weather recorded from weather station at the Petawawa Research Forest, during a field season in 2007. The data is sorted into ascending order and has not gaps. The column headers are formatted with the expected inputs, details on the input requirements can be found in [Code#Documentation](/mock-up/code/#documentation). Notice there is no input for solar radiation (*sol_rad*) or curing faction (*percent_cure*), these are optional inputs and if they are not in the input file, they will be automatically estimated.

Open the **Tutorial_NGFWI.xx** code file.  This file contains the step by step process for running NG_FWI.

#### Step 2: 
Change the file path and load your data file. 
