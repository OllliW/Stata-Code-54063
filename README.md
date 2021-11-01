# Stata-Code-54063
Sample code for artefact using Stata17
* 1. set up the data from the Our World in Data co2 repository:

import delimited "Desktop/owid-co2-data.csv",clear

/*The advantage of declaring a data to be a panel is that it allows us to generate time series variables like 3-day or 7-day moving averages that we often see being used in online graphs especially for daily cases and deaths which tend to be very spiky:

2. Generate 3-year smoothing averages of Co2 per capita and global share of Co2
*/
tssmooth ma Co2pc_ma3  = co2_per_capita , w(2 1 0) 
/* The smoother applied was
    by id : (1/3)*[x(t-2) + x(t-1) + 1*x(t)]; x(t)= co2_per_capita
*/	
tssmooth ma share_global_co2_ma3 = share_global_co2, w(2 1 0)     // try help tssmooth
/*
The smoother applied was
     by id : (1/3)*[x(t-2) + x(t-1) + 1*x(t)]; x(t)= share_global_co2
	 It says use this observation and the prior two observations
	
3. label the variables for completeness

lab var id      "Country"
lab var year    "Year"
lab var Co2pc_ma3    "Co2 percap (3-year moving average)"
lab var share_global_co2_ma3   "Global share CO2 (3-year moving average)"
lab var Methanepc_ma3   "Methanepc (3-year moving average)"

4. Two graphs and combine 

xtline Co2pc_ma3, overlay xlabel(1750 (20) 2020) ylabel (0 (5) 25) name(Co2pc, replace)
graph export Co2pc.png, replace wid(1000)  


xtline share_global_co2_ma3, overlay xlabel(1750 (30) 2030) name(GlobShareCO2, replace)
graph export GlobShareCO2.png, replace wid(1000) 


graph combine Co2pc GlobShareCO2, ///
name("TrendsCo2Share", replace)  cols(1) title("TrendsCo2ShareAllYears")
![image](https://user-images.githubusercontent.com/92966779/139611701-c2149d75-a4d3-4104-a32e-2951efb44b51.png)
