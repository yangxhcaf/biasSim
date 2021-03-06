# About biasSim
#
# This code was used to perform the analysis and produce the results described in:
#
# Peel, S.L., Hill, N.A., Foster, S.D., Wotherspoon, S.J., Ghiglione, C. & Schiaparelli, S. (2019) Reliable species distributions are obtainable with sparse, patchy and biased data by leveraging over species and data types. Methods in Ecology and Evolution [accepted]
#
# and is provided here as a record of this work.  

# Script descriptions ...
#
# cellsObj.r
# Script for the cells object.  This object contains information specified at the cell level.  For example, the species and sampling bias covariate values per cell, or the true number of species per cell for the current simulation.  Functions within this script initialise the storage space for the object and fill in the values.  Some are called once (i.e. for values such as the covariates that don’t change per simulation) and some are called per simulation (i.e. the function that simulates the number of individuals per cell).
# 
# dataObj.r
# Script for the data objects.  This script contains functions to get the environmental data (that are raster files in long-lat format and cover the full 360 degrees of longitude), project the data, crop the data to the required extent, and save results to a list.
# 
# domainObj.r
# Script for the domain object (referred to as region within the paper).  Creates and stores domain information in several formats (owin as required by spatstat package, ext as required by raster package and poly as required by sp package).
# 
# estimateCoeffsNew.R
# Script to estimate the “true” species distribution and sampling bias coefficients from real data (i.e. section 2.2 in MEE paper).
# 
# paperPlots.r
# Script to generate the plots from the results for the MEE paper that reports this work.  Purely for information as cannot be run without results from experiments.
# 
# plotLayers.r
# Script to make plotting different layers together easier (well for me anyway) and reduce repeated code.
# 
# plottingObj.r
# Script to make the plotting object.  This script contains functions related to plotting the results and also check plots along the way.
# resultsObject.r
# Script to set up and save the results coming out of the repeat simulations.  This deals with getting the results from a single simulation run and saving it to the correct run number, scenario and SDM (as runs performed in parallel do not necessarily come back in the order you sent them).  It also stores any error messages so that they can be checked at the end of the experiment. 
# 
# scenariosObject.r
# Script to control scenarios in an experiment.  Functions to set up the scenarios required in the experiment from the given parameters as well as plot the results.
# 
# settingsObj.r
# Script to set up the settings within the simulation.  These are set from other functions, do not change values in list here.
# 
# simFuncs.r
# This script runs the simulations.  It has hard-wired numbers in function “runScenario” that will need to be changed to suit the research problem.  This function also sets up the rest of the scenario run and repeats the simulations via the function “runSim”.  Note that the simulations are setup to be run in parallel using the function mclapply (this may not be the appropriate parallelisation function for your machine, see package “parallel” for more details).  Please test that the settings are working by uncommenting and running the single run: 
# #par1Res <- runSim(1, simObj, domainObj, cellObj, envirObj, biasObj, surveyObj, plotObj, BG)
# before progressing to a parallel run (cores that fail just drop out and whilst there is some error catching, it is easier to correct setting errors through a single run).  Another hint for testing the setup is to set argument doCheckPlots to TRUE in function runScenario and evaluate the plots that are produced (are they what you expected given your problem).  
# 
# startHereNew.r
# This script is where the experiments are set up, run and results evaluated.  Mostly doesn’t contain functions just lines of codes for each experiment.  Note that running an experiment assumes that the data has been collected and processed into appropriate objects, true coefficients have been estimated, and the settings for the problem have been specified (hard-wired in script simFuncs.r).  A good place to start (despite the name of this script) would be to get the function “runScenario” (in simFuncs.r) working for one lot of parameter settings.  
# 
# surveysObj.r
# Script for the surveys object.  Initialises, simulates and saves the information about surveys (as these are not performed in every cell so are not contained in the cells object).
# 
# utils.r
# Script of functions that are not necessarily just for this work.
#
# Included data ...
#
# Data files included are BiasCovariates.RData, EnvirCovariates.RData, and TrueCoefficients.RData.  These were created from raw data during the setup phase.  The covariates (Bias and Envir) are cropped, projected, have missing values replaced, the domain mask created, and are stored in a data object ready for use by the simulation phase.  The true coefficients are those created by using the functions in 'estimateCoeffsNew.r' with the 21 species of Ross Sea PA and PO data.  Having these files already prepared allows the user to skip some of the setup phase.  To do this change the hardwired variables 'inputDir' and 'useSavedData' in function runScenario (in file 'simFuncs.r') to the directory where these .RData files are stored and to TRUE for useSavedData. You can then run the function 'runScenarios' using this prepared data.  
# Any run of the simulation will create a 'LastSimSettings.RData' in this same input directory.  It uses this data when the useSavedData = TRUE to check that the covariate data and true coefficients data was from a simulation with the same 'isCentred' setting (a different 'isCentred' setting in the last simulation will cause the data setup process to be performed).  If you change the data files that you are using, make sure that you also set useSavedData = FALSE in order to perform the data setup process on your new data (only need to do this once as this process will create the above .RData files so that you can set useSavedData = TRUE for the next run).
#
# Not included data ...
#
# The antarctic coast line polygon data and research base location and name data are not included here.  These are only used to make mapping more readable to the user (i.e. do not affect the experiments or the results).  They are useful for some of the check plots but not necessary. 
#
# Packages required ...
# 
# To run everything you will need the following R packages installed: raster, spatstat, mutlispeciesPP (https://sampeel/multispeciesPP version), sp, rgeos, parallel, vioplot.

