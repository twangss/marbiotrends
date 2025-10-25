# Estimating total species abundance trends with spatial synchrony 
This tool is a Bayesian multivariate state-space (MARSS) model that incorporates spatial synchrony while estimating total species abundance over time. The model sums abundance trends across many populations within a species to calculate a single total abundance. Spatial synchrony can estimate trends during unsampled time periods for populations using information from nearby sampled populations. Spatial synchrony can also be toggled off. Observation error of survey methods are also estimated to inform biodiversity monitoring. This tool is intended to be generalizable for most vertebrates, which are usually counted at an annual resolution. We demonstrate the practicality this tool in several examples of marine vertebrates.

## File Descriptions
### Abundance Data
Global database of marine vertebrate time series for pinnipeds (LINK) and sea turtles (LINK). The data is a compilation from a literature review of marine vertebrate abundance time series by location. Data is structured in a wide format, in other words, each species location with counts is its own row. The first columns are metadata and characteristics of the data and the remaining columns are the abundance counts. 
#### Abundance Data Column Description
- TID: time series identification number assigned
- Author: Source author
- Report.Year: Source year
- Title: Source title
- Publication: Source publication
- Type: Publication type
- DOI: DOI of source
- Link: Url link to source
- Common.Name: Species common name
- Subspecies: Scientific name, identified to subspecies level when relevant
- Family: Taxonomic family
- Location.of.population: Location of abundance counts
- Indiv.pop.map: Location of abundance counts for model input, similar to Location.of.population
- Indiv.sum: Indicator if time series is included in total species abundance calculation = 1, necessary to avoid double counting -locations where there are multiple time series
- Latitude/Longitude: Geographic coordinates
- Use: For use in model
- Units_clean: Categories of abundance units 
- Scaling: Scalar life-stage conversion factors to standardize abundances of different units into a single unit for each species
- Sampling.method: Sampling method categories for estimation of observation error parameters
- yXXXX: Year XXXX, Years of abundance counts 

### Distance Data for Spatial Synchrony
Distances among locations for example taxa are found at DISTANCE_LINK. Distance are calculated as lengths of minimum contiguous ocean paths using CODE LINK prior to model fitting. 

### Model Files
Data pre-pocessing and model fitting code found at MODEL_LINK. Model is in Stan and found in the Stan folder. See Model description section below to get started. Model output processing files for reporting species abundance, overall abundance growth rates, observation error variability by sampling method and species, spatial synchrony relationship refer to model output files here.


## Model Description (Bayesian MARSS)
### Setting up the model
Download CmdStanR and follow the help guide:
https://mc-stan.org/cmdstanr/articles/cmdstanr.html

Stan files are located in the stan folder. Model without spatial syncrhony: marss_gomp_cmd.stan. Model with spatial syncrhony: marss_gomp_cmd.stan

### Multivariate State-space
SSMs differentiate process and observation error, which allows estimation of unobserved states while simultaneously accounting for sampling uncertainty. In the context of population dynamics, process error refers to true changes in abundance due to birth, deaths, immigration, emigrations, while observation error refers to randomness in surveys sampling abundance. This separation of process and observation error may reduce spurious interpretations of extinction risk, which should be based only on process variability

An in-depth description about the statistical underpinnings of the multivariate (multi-population) autoregressive state-space model:
https://nwfsc-timeseries.github.io/MARSS-Manual/chap-marss.html

We model abundances as random walks in log space. Process variance is independent and unique by location/populaton with an independent process hypothesis, or characterized as a Gaussian process covariance function of ocean distance with a spatial synchrony hypothesis. The observation covariance matrix is diagonal, with variances that differ by sampling method category. 

### Bayesian
Scarce ecological data, some parameters will be difficult to estimate in a likelihood framework. The priors in the Stan examples are based on pinnipeds.

### Framework
We designed this tool to be ran at the species level. This can be modified to other taxonomic levels. Model run times will exponentially increase with # of time series in the MARSS.

