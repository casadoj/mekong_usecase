![header](images/header.png)

# Following steps

<br>
<br>
<br>

So far we have developed a LISFLOOD model than runs and it is correctly initialized. However, we are using default model parameters, so there's no confidence at all in the accuracy of the model. Before you can extract any conclusions from your model results, the model needs to be calibrated.

Hydrological models like LISFLOOD need to be calibrated, which means that the model parameters are tuned so that the outputs reproduced observed data. Calibration is usually perform on the river discharge timeseries at one or more gauging stations, but it is not the only way. 

To perform a calibration, LISFLOOD proposes a [calibration tool](https://github.com/ec-jrc/lisflood-calibration) based on DEAP (Distributed Evolutionary Algorithms in Python) (De Rainville et al., 2012). Please, feel free to use any other calibration procedure and optimization algorithm.