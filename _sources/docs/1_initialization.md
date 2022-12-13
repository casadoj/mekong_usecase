< [General setup](0_general_setup.ipynb) | [Warmup run](2_warmup.ipynb) >

![header](images/header.png)

# Initialization run

A thorough explanation on the importance of the model initialization can be found in this section of the [User Guide](https://ec-jrc.github.io/lisflood-code/3_step5_model-initialisation/). To summarize, the purpose of this run is to estimate two rate variables required for the model initialization:

* `avgdis.nc`:  a map of the average discharge in the river network.
* `lzavin.nc`: a map of the average inflow into the lower groundwater zone.

We will save these outputs in a specific subfolder (_initial_) within the project folder.


```python
import os
import numpy as np
import pandas as pd
import xarray as xr
import matplotlib.pyplot as plt
from datetime import datetime, timedelta
from matplotlib.gridspec import GridSpec
from lisflood_read_plot import *

os.chdir('../../')
```

## 1 Settings file

In the following lines, a snippet of the settings file (_settings_initialization.xml_) shows the most relevant lines set in this file for the initializatin run.

```xml
<lfoptions>
    
    # [...]
    
    <setoption choice="1" name="InitLisflood"/>
    <setoption choice="0" name="InitLisfloodwithoutsplit"/>
    
    # [...]
    
</lfoptions>

<lfuser>
    
    # GENERAL SETUP
    
    # [...]
    
    # time-related variables
    <textvar name="CalendarDayStart" value="02-01-1979 00:00"/>
    <textvar name="StepStart" value="02-01-1979 00:00"/>
    <textvar name="StepEnd" value="01-01-2020 00:00"/>
    <textvar name="timestepInit" value="1"/>
    <textvar name="DtSec" value="86400"/>
    <textvar name="DtSecChannel" value="14400"/>
    
    # PATHS
    <textvar name="PathInit" value="$(PathRoot)/initial"/>
    <textvar name="LZAvInflowMap" value="$(PathInit)/lzavin"/>
    <textvar name="AvgDis" value="$(PathInit)/avgdis"/>
    
    # [...]
    
    # INITIAL CONDITIONS
    
    # water balance
    <textvar name="OFDirectInitValue" value="0"/>
    <textvar name="OFOtherInitValue" value="0"/>
    [...]
    
    # channels
    <textvar name="TotalCrossSectionAreaInitValue" value="-9999"/>
    <textvar name="CrossSection2AreaInitValue" value="-9999"/>
    <textvar name="PrevSideflowInitValue" value="-9999"/>
    
    # reservoirs
    <textvar name="ReservoirInitialLevelValue" value="-9999"/>
    <textvar name="LakePrevInflowValue" value="-9999"/>??
    <textvar name="LakePrevOutflowValue" value="-9999"/>??
    <textvar name="PrevDischarge" value="-9999"/>
    
    # soils
    <textvar name="ThetaInit1Value" value="-9999"/>
    <textvar name="ThetaInit2Value" value="-9999"/>
    <textvar name="ThetaInit3Value" value="-9999"/>
    <textvar name="LZInitValue" value="-9999"/>
    [...]
    
</lfuser>
```

* In the element `<lfoptions>`, the option `InitLisflood` tells LISFLOOD that this run is an initialization. Since we are using as a routing module the split kinematic wave, we must deactivate the option `InitLisfloodwithoutsplit`; otherwise, the initialization run will not produce the file _avgdis.nc_ and we will not be able to initialize the routing module in suceeding runs. 
* In the element `<lfuser>` we must define the simulation period, the location of the output files, and the initial conditions.
    * The initialization run extends from 01-01-1979 to 31-12-2019. Following the [end of timestep naming convention](https://ec-jrc.github.io/lisflood-code/2_ESSENTIAL_time-management/) in LISFLOOD, the previous dates will be shifted forward by 1 day; that's why in the settings file the `StepStart` and `StepEnd` are 02-01-1979 and 01-01-2020, respectively. 
    * We will save the two ouput files (_lzavin_ and _avgdis_) in a folder named _initial_.
    * Regarding the initial conditions, those in the section water balance must be initialized with a value or a map (we define default values of 0 or 1), whereas the rest of variables can be internally initialized by setting the value -9999.
    

## 2 Run the simulation

To run the simulation, open a terminal, activate the Conda environment where you installed LISFLOOD and execute the `lisflood` function pointing at the appropriate settings file. For instance:

```shell
conda activate your_lisflood_environment
lisflood /home/user/your_path/settings_initialization.xml
```

## 3 Outputs

The outputs are the two maps (NetCDF format) mentioned at the top of this notebook. In the settings file, we set that these file must be saved in the _initial_ subfolder. Let's load them and inspect them:


```python
# load average inflow into the lower groundware zone
lzavin = xr.open_dataarray('initial/lzavin.nc')
lzavin.close()

# load average discharge
avgdis = xr.open_dataarray('initial/avgdis.nc')
avgdis.close()

# plot the maps
fig, axes = plt.subplots(ncols=2, figsize=(12, 4.5))
for ax, da in zip(axes, [lzavin, avgdis]):
    da.plot(ax=ax, cmap='Blues')
    ax.axis('off')
```


    
![png](images/1_1.png)
    


***Figure 1**. Output maps of the initialization run.*

Both outputs represent an average flow rate, therefore, they have no temporal dimension.

< [General setup](0_general_setup.ipynb) | [Warmup run](2_warmup.ipynb) >