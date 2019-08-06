# windowsearch
Window searching algorithm to be used to distinguish local irrigation-induced forcing from larger-scale climate forcings in a spatial dataset of a response variable (e.g., T2m, LST).

## Method
The method to reconstruct the irrigation imprint on temperature relies on three steps:

1. **Pixel selection:** Pixels exceeding a present-day irrigation threshold are selected for analysis if a window of 15x15 cells centered around the pixel of interest has a data coverage of at least 60%, and if at least 8% of the pixels are irrigated. The selection criteria match those by Lejeune et al. (2017), with the exception of the larger search window. Tests with a varying search window revealed that increasing the search window size increased the temperature contrast between irrigated cells and their surroundings (this is hypothesized to be due to the clustered spatial irrigation pattern), but leads to a more smooth irrigation signal in the temperature record compared to smaller windows.
2. **Multi-linear regression:** the analysis extracts an irrigation-induced temperature signal by performing a multi-linear regression on all pixels in the search window. The regressors are the change in irrigated fraction and, analogous to Lejeune et al. (2017), three spatial predictors which may confound the irrigation-derived signal (latitude, longitude and elevation). This results in the following linear regression model:

<a href="https://www.codecogs.com/eqnedit.php?latex=\Delta&space;T&space;=&space;\alpha_1&space;\times&space;\Delta&space;f_{irr}&space;&plus;&space;\alpha_2&space;\times&space;lat.&space;&plus;&space;\alpha_3&space;\times&space;lon.&space;&plus;&space;\alpha_4&space;\times&space;elev." target="_blank"><img src="https://latex.codecogs.com/gif.latex?\Delta&space;T&space;=&space;\alpha_1&space;\times&space;\Delta&space;f_{irr}&space;&plus;&space;\alpha_2&space;\times&space;lat.&space;&plus;&space;\alpha_3&space;\times&space;lon.&space;&plus;&space;\alpha_4&space;\times&space;elev." title="\Delta T = \alpha_1 \times \Delta f_{irr} + \alpha_2 \times lat. + \alpha_3 \times lon. + \alpha_4 \times elev." /></a>

3. **Irrigation impact reconstruction:**
