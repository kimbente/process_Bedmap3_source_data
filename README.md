# Processing the Bedmap3 source data collection (Antarctic ice thickness and bed topography)

This repository contains an end-to-end programmatic **preprocessing pipeline** for the **Bedmap3** source data collection, as used in [Pritchard et al. 2025](https://www.nature.com/articles/s41597-025-04672-y) (i.e. the Bedmap3 map), and as described in [Fremand et al. 2023](https://essd.copernicus.org/articles/15/2695/2023/).

## Preprocessing pipeline 

The following downloading and preprocessing pipeline is fully **reproducible**.

### Download Bedmap
- In `real_data_step1__download_bedmap123.py` replace `path_to_bedmap_data_folder` with your own local path. Run the python script with `python real_data_step1_download_bedmap123.py` from the terminal. This will automatically download, unzip, and organise all **Bedmap** data files. This script works on the os operating system. If you have trouble with this script or you are not on os, also see this [BAS resource from the Geophyscis Book by the UK Polar Centre](https://antarctica.github.io/PDC_GeophysicsBook/BEDMAP/Downloading_the_Bedmap_data.html) for useful information.
- <p style="color:red;"><strong>WARNING:</strong> This script downloads 11 GB of data!</p>
    - Bedmap1: 0.157 GB
    - Bedmap2: 3.2 GB
    - Bedmap3: 6.8 GB
- The script directly downloads all standardised .csv files from the Bedmap1, Bedmap2 and Bedmap3 collections from the [UK Polar Data Centre](https://www.bas.ac.uk/data/uk-pdc/). The lists of .csv files are visible on [this Bristish Antarctic Survey (BAS) webpage](https://www.bas.ac.uk/project/bedmap/#data).
- Also check out this [Github repository](https://github.com/kimbente/bedmap) for some additional analysis of Bedmap123 data.
- Bedmap3 references:
    - *Pritchard, Hamish D., et al. "Bedmap3 updated ice bed, surface and thickness gridded datasets for Antarctica." Scientific data 12.1 (2025): 414.*
    - *Frémand, Alice C., et al. "Antarctic Bedmap data: Findable, Accessible, Interoperable, and Reusable (FAIR) sharing of 60 years of ice bed, surface, and thickness data." Earth System Science Data 15.7 (2023): 2695-2710.*

### Preprocess Bedmap

- In `real_data_step2_preprocess_bedmap123.py`, specify your preference about which variable you care about by setting bool_remove_rows_without_ice_thickness and/or bool_remove_rows_without_bed_elevation. Also make sure you set `path_to_bedmap_data_folder` to the same path you used for the download script. We set `bool_remove_rows_without_ice_thickness = True` because we will be using ice thickness measurements.
    - For only `bool_remove_rows_without_ice_thickness = True` the resulting data set contains ~ 82 M points (i.e. rows.) and is 9.5 GB large.
    - For only `bool_remove_rows_without_bed_elevation = True` the resulting data set contains ~ 67 M points (i.e. rows.)
- Run the script with `python real_data_step2_preprocess_bedmap123.py` from the terminal.
- The script combines all csv files into a standardised pd.Dataframe (pandas) and performs a set of cleaning and preprocessing steps.
- Number of csv files to combine: 151 
    - Number of bedmap1 csv files: 1
    - Number of bedmap2 csv files: 66
    - Number of bedmap3 csv files: 84
- Next, the data is subsetted for the broader Byrd region. The subset of data for the 300 x 300 km Byrd area is more managable in size and only contains 750k data points, reducing the file size to 0.085 GB. These are the Antarctic Polar Stereographic coordinates (see [EPSG:3031](https://epsg.io/3031)) used to subset the data. (For a user-friendly, non-programmatic conversion between and Polar Stereographic Coordinates we recommend [this conversion webtool by the Polar Geospatial Center (University of Minnesota)](https://www.pgc.umn.edu/apps/convert/).)
    - x_min = 350_000
    - x_max = 650_000
    - y_min = -1_000_000
    - y_max = -700_000

### Generate train-test regions

Go through the IPython notebook `real_data_step3_generate_train_test_regions.ipynb` to generate the train and test tensors for the three regions, which are already provided in [real_data](data/real_data)
- Since the Bedmap data that we just downloaded is combined with ice velocity observations, these need to be downloaded too. Download **MEaSUREs InSAR-Based Antarctica Ice Velocity Map, Version 2** from (the NSIDC website)[https://nsidc.org/data/nsidc-0484/versions/2]. See [here for the documentation/user guide](https://nsidc.org/sites/default/files/nsidc-0484-v002-userguide.pdf).
    - MEaSUREs InSAR Antarctica reference: *Rignot, E., Mouginot, J. & Scheuchl, B. (2017). MEaSUREs InSAR-Based Antarctica Ice Velocity Map. (NSIDC-0484, Version 2). [Data Set]. Boulder, Colorado USA. NASA National Snow and Ice Data Center Distributed Active Archive Center. https://doi.org/10.5067/D7GK8F5J8M8R.*
- Again, change the path to the preprocessed Bedmap123 data to your local path and specify the path to the ice velocity observations. 
- The code handles firn corrections, performs some meta data analysis, and produces visualisations to get on overview over the data.
- The notebook subsets three regions within the wider Byrd glacier catchment for our experiments. Byrd Glacier drains a large part of the East Antarctic Ice Sheet (EAIS) and flows into the Ross Ice Shelf. Hence, the Byrd Subglacial Basin has fast flowing ice and an interesting and scientifically important bed topography. These boundries are given in Polar Stereographic coordinates:
    - Citation: **Rignot, E., Mouginot, J. & Scheuchl, B. (2017). MEaSUREs InSAR-Based Antarctica Ice Velocity Map. (NSIDC-0484, Version 2). [Data Set]. Boulder, Colorado USA. NASA National Snow and Ice Data Center Distributed Active Archive Center. https://doi.org/10.5067/D7GK8F5J8M8R.**
- Again, change the path to the preprocessed Bedmap123 data to your local path and specify the path to the ice velocity observations. 
- The code handles firn corrections, performs some meta data analysis, and produces visualisations to get on overview over the data.
- We use data from three regions within the wider Byrd glacier catchment. These are the Polar Stereographic coordinates of the respective regions:
    - Upper Byrd (70 x 70 km)
        - upper_byrd_x_min = 400_000
        - upper_byrd_x_max = 470_000
        - upper_byrd_y_min = -800_000
        - upper_byrd_y_max = -730_000
    - Mid Byrd (70 x 70 km)
        - mid_byrd_x_min = 395_000
        - mid_byrd_x_max = 465_000
        - mid_byrd_y_min = -870_000
        - mid_byrd_y_max = -800_000
    - Lower Byrd (30 x 30 km)
        - lower_byrd_x_min = 420_000
        - lower_byrd_x_max = 450_000
        - lower_byrd_y_min = -910_000
        - lower_byrd_y_max = -880_000
- All 6 train/test tensors have the following five columns:
    - x coordinate [original units: Polar Stereographic X, now: min-max normalised to (0,1)]
    - y coordinate [original units: Polar Stereographic Y, now: min-max normalised to (0,1)]
    - *surface elevation* [original units: m, now: km] (this is auxiliary information, not currently used)
    - ice flux in x-direction [original units: m^2 / year i.e. m^3 / m / year, now: scaled to reduce magnitude]
    - ice flux in y-direction [original units: m^2 / year i.e. m^3 / m / year, now: scaled to reduce magnitude]


## List of files with brief explanantions

- [real_data_step1_download_bedmap123.py](real_data_step1_download_bedmap123.py) downloads all standardised raw data from Bedmap1, Bedmap2, and Bedmap3.
- [real_data_step2_preprocess_bedmap123.py](real_data_step2_preprocess_bedmap123.py) preprocesses and cleans the downloaded raw datasets to then generate one pd.Dataframe.
- [real_data_step3_preprocess_regions.ipynb](real_data_step3_preprocess_regions.ipynb)
- [real_preprocess.py](preprocess/real_preprocess.py) contains functions needed in the step 3 preprocessing notebook.

## Links to research projects that use this preprocessing pipelineß

- [https://github.com/kimbente/FluxNet](https://github.com/kimbente/FluxNet): **Deep Learning Ice Shelf Basal Melt Rates via Differentiable Physics** (Kim Bente, Roman Marchant, Fabio Ramos), accepted at Climate Informatics 2026.
- [https://github.com/kimbente/mass_conservation_on_rails](https://github.com/kimbente/mass_conservation_on_rails): **Mass Conservation on Rails - Rethinking Physics-Informed Learning of Ice Flow Vector Fields** (Kim Bente, Roman Marchant, Fabio Ramos), accepted at the NeurIPS 2025 Workshop on Tackling Climate Change with Machine Learning.
- [https://github.com/kimbente/dfNGP](https://github.com/kimbente/dfNGP): **Frozen Constraints, Fluid Uncertainty: Physics-Informed Probabilistic Modelling of Mass-Conserving Flow Fields for Environmental Applications** (Kim Bente, Roman Marchant, Fabio Ramos), under review.
- [https://github.com/kimbente/BO4AIS](https://github.com/kimbente/BO4AIS): **Bayesian Optimisation (BO) for the Antarctic Ice Sheet (AIS)**, accepted at the EGU 2026 Machine Learning for Cryospheric Sciences session.

# Contact

For any comments, questions or otherwise, please contact kim.bente@utas.edu.au

# Timestamp

This processing pipeline was last updated on *25 March 2026*.

# Annotated Screenshots


