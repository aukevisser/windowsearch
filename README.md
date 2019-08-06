# windowsearch
Window searching algorithm to be used to distinguish local irrigation-induced forcing from larger-scale climate forcings in a spatial dataset of a response variable (e.g., T2m, LST).

## Method
The method to reconstruct the irrigation imprint on temperature relies on three steps:

1. **Pixel selection:** Pixels exceeding a present-day irrigation threshold are selected for analysis if a window of 15x15 cells centered around the pixel of interest has a data coverage of at least 60%, and if at least 8% of the pixels are irrigated. The selection criteria match those by [Lejeune et al. (2017)](https://www.nature.com/articles/s41558-018-0131-z), with the exception of the larger search window. Tests with a varying search window revealed that increasing the search window size increased the temperature contrast between irrigated cells and their surroundings (this is hypothesized to be due to the clustered spatial irrigation pattern), but leads to a more smooth irrigation signal in the temperature record compared to smaller windows.
2. **Multi-linear regression:** the analysis extracts an irrigation-induced temperature signal by performing a multi-linear regression on all pixels in the search window. The regressors are the change in irrigated fraction and, analogous to Lejeune et al. (2017), three spatial predictors which may confound the irrigation-derived signal (latitude, longitude and elevation). This results in the following linear regression model:

  <a href="https://www.codecogs.com/eqnedit.php?latex=\Delta&space;T&space;=&space;\alpha_1&space;\times&space;\Delta&space;f_{irr}&space;&plus;&space;\alpha_2&space;\times&space;lat.&space;&plus;&space;\alpha_3&space;\times&space;lon.&space;&plus;&space;\alpha_4&space;\times&space;elev." target="_blank"><img src="https://latex.codecogs.com/gif.latex?\Delta&space;T&space;=&space;\alpha_1&space;\times&space;\Delta&space;f_{irr}&space;&plus;&space;\alpha_2&space;\times&space;lat.&space;&plus;&space;\alpha_3&space;\times&space;lon.&space;&plus;&space;\alpha_4&space;\times&space;elev." title="\Delta T = \alpha_1 \times \Delta f_{irr} + \alpha_2 \times lat. + \alpha_3 \times lon. + \alpha_4 \times elev." /></a>

3. **Irrigation impact reconstruction:** the multi-linear regression coefficient for irrigation obtained from the above equation is multiplied by the change in irrigated fraction in the cell of interest:

  <a href="https://www.codecogs.com/eqnedit.php?latex=\Delta&space;T_{irr}&space;=&space;\alpha_1&space;\times&space;\Delta&space;f_{irr}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\Delta&space;T_{irr}&space;=&space;\alpha_1&space;\times&space;\Delta&space;f_{irr}" title="\Delta T_{irr} = \alpha_1 \times \Delta f_{irr}" /></a>

The algorithm is desinged to capture the direct cooling effect of irrigation due to an enhanced surface latente energy flux. The secondary effects of irrigation such as enhanced atmospheric moisture content, potentially leading to downwind precipitation and altered monsoon circulation (Puma & Cook, 2010; Guimberteau et al., 2012; Thiery et al., 2017) are potential confounding factors because they could act more or less heterogeneously in the search window. Additionally, this method does not correct for possible dependence between spatial predictors. For example, most irrigation occurs in low-lying areas. This spatial dependence could reduce the regression coefficient for irrigation and thus <a href="https://www.codecogs.com/eqnedit.php?latex=\Delta&space;T_{irr}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\Delta&space;T_{irr}" title="\Delta T_{irr}" /></a>.

## Code structure
The algorithm files all contain one function, which can be called from other algorithm- or plotting scripts. The algorithm is written in the following three files:

- *extract_T_irr.py* extracts the temperature signal for the reference period and the present-day period, and stores it in a three-dimensional (2 x n_lat x n_lon) array. When applying the search window algorithm to determine spatial irrigation effects (e.g. in the case of MODIS), the reference period consists only of zeros. 
- *calc_irr_diff.py* generates a three-dimensional array of irrigation maps for the reference and present-day periods. 
- *calc_irr_impact_regression.py* contains a function that calculates irrigation-induced temperature change (<a href="https://www.codecogs.com/eqnedit.php?latex=\Delta&space;T_{irr}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\Delta&space;T_{irr}" title="\Delta T_{irr}" /></a>) using the regression-based method based on a range of user-specified parameter choices.

The parameters that are used in the function call of *calc_irr_impact_regression* are:

- *yr_start1, yr_end1, yr_start2, yr_end2* can be used to determine the reference and present-day period (for CRU, these parameters do not have a function for MODIS).
- *method*: chosen option should be 'regression'
- *thres_irr* is the irrigated fraction threshold used for pixel selection
- *response* can be 'PC/PD' or 'PD', depending on whether pixel selection should occur by changes in the irrigated fraction, or based on the present-day irrigated fraction.

## Data pre-processing

### Historical Irrigation Dataset
This dataset contains spatial information on the Area Equipped for Irrigation (AEI) at a resolution of 5 arcmin [Siebert et al., 2015](https://www.hydrol-earth-syst-sci.net/19/1521/2015/). The window searching analysis on the irrigated fraction, which is calculated from AEI using a bash script containing CDO commands (*HID_regrid.sh*).

### Temperature datasets
Temperature datasets can in principle be used as long as they are can be accessed via Python. 
