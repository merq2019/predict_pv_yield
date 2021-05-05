| __Notebook__                                          | __Brief description of experiments run__                                                                                                                                                                                                                                                                                                                                                                                                                                | __Key results__                                                                                                                                                                                                                                                                                                                                             | __Notes__                                                                                                                                               |
|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
| - `001-base_experiment.ipynb`                         | Using basic min-max scaling of PV output from a single PV system. Nowcasting using satellite (in original reprojection) and using only the HRV channel, hour of day, and 3 irradiance measures (GHI, DNI, DHI). Using a simple CONV network with 2 conv layers and 3 fully connected layers.                                                                                                                                                                            | Trained for 240,000 (converged) batches and achieved test MAE of ~0.050 for the single system                                                                                                                                                                                                                                                               |                                                                                                                                                         |
| - `002-mulitple_PV_systems.ipynb`                     | Same as `001` but using PV output from all systems                                                                                                                                                                                                                                                                                                                                                                                                                      | Trained for 62,000 (converged) batches and achieved test MAE of ~0.088                                                                                                                                                                                                                                                                                      |                                                                                                                                                         |
| - `003-multiple_PV_systems_v2.ipynb`                  | TODO: difference between this and above                                                                                                                                                                                                                                                                                                                                                                                                                                 | Trained for 7,168 (converged) batches and achieved test MAE of ~0.088                                                                                                                                                                                                                                                                                       |                                                                                                                                                         |
| - `004-predict_fraction_of_irradiance_captured.ipynb` | Some analysis was done on anomalies in PV time series, measured max output vs metadata and system orientation. Express target as min-max scaled $PV(location, time)/GHI(location, time)$. Network trained is same structure as `001` and trained across all systems.                                                                                                                                                                                                    | Trained for 4,000 (converged) batches and achieved test MAE of ~ 0.010 on PV/GHI. Some plots show that some PV systems have anomalously high values in their time series (at least one system where anomalous value is 1000s of time greater than other values), ones that couldn't be physical. We make a more robust min-max scaling to deal with this. , | The robust min-max scaling developed here is used from this point forward. It is therefore invalid to compare metric scores to experiments before this. |
| - `005-library_test.ipynb`                            | A simple notebook to demo the loader functionality as developed at this stage. This simplifies and stops all the copying and pasting done in previous notebooks. Trained slightly larger network using hybrid of NWP and satellite data and 3 conv layers which go across both sources, 2 fc layers with batchnorm to predict robustly min-maxed scaled PV output. Also implemented stricter train-test splitting so testing is done on unseen systems at unseen times. | Demo purposes showing API                                                                                                                                                                                                                                                                                                                                   |                                                                                                                                                         |
| - `006-network_structure_tests.ipynb`                 | Train a number of nowcasting hybrid (satellite + NWP) networks as well as those with only NWP and only satellite data and with increasing neural network depth and width.                                                                                                                                                                                                                                                                                               | Trained for 33,000 (converged) batches and best test MAE achieved was 0.113. Best score was for hybrid model with the most amount of layers tested, but this only beat the equivalently sized satellite data only model by 0.001. The satellite data only models beat the NWP only models by ~0.02.                                                         |                                                                                                                                                         |
| - `007-more_network_structure_tests.ipynb`            | Very similar to `006` but using 2 more channels from NWP and slightly deeper convolutions. Also trained models using only HRV and models using time and location only as baselines.                                                                                                                                                                                                                                                                                     | Trained for 28,000 (converged) batches. Best HRV only model got test MAE of 0.165. Unexpectedly much worse than similar experiments `002` and `004`. Difference may be down to dropping GHI as feature? Best model overall was a hybrid model which got test MAE of 0.123.                                                                                  |                                                                                                                                                         |
| - `008-initial_forecast_experiment.ipynb`             | Forecast experiments at 1hr, 4hrs and 24hrs ahead. Similar model structure before. Satellite input data is most recent available image (so 1/4/24 hrs before prediction) and NWP input is most recent forecast for the prediction time.                                                                                                                                                                                                                                 | Trained for 17,500 (semi-converged) batches. Best scores were all hybrid models with test MAE of 0.132, 0.139, 0.135 at 1, 4, 24 hrs ahead. From 1 hr ahead the NWP only models were better than satellite and the difference between them grows with forecast time.                                                                                        |                                                                                                                                                         |
| - `009-learning_with_sequences_example.ipynb`         | Demo of how library can be used to load sequences of satellite and NWP data and also sequences of PV output data                                                                                                                                                                                                                                                                                                                                                        |                                                                                                                                                                                                                                                                                                                                                             |                                                                                                                                                         |
| - `010-predicting_pv_sequences.ipynb`                 | Nowcasting [0,5,10,15,...,55,60] minutes into future using single most recent satellite image and 3 NWP times centred around sequence. Hybrid only models of 3 different depths.                                                                                                                                                                                                                                                                                        | Trained for 2,200/9,500 (mostly converged) batches. Got a test MAE of 0.126.                                                                                                                                                                                                                                                                                |                                                                                                                                                         |
| - `011-persistence_baseline.ipynb`                    | Analysis on accuracy of persistence alone.                                                                                                                                                                                                                                                                                                                                                                                                                              | As a baseline, a MAE of 0.12 equates to the same accuracy of ~45 minutes persistence.                                                                                                                                                                                                                                                                       |                                                                                                                                                         |
| - `012-nwp_irradiance_basline.ipynb`                  | Baseline nowcasting using only NWP downwards shortwave radiation, time of year and time of day.                                                                                                                                                                                                                                                                                                                                                                         | Trained for 20,000 (converged) batches and achieved test MAE of 0.136.                                                                                                                                                                                                                                                                                      |                                                                                                                                                         |
| - `013-consolidating_previous_experiments.ipynb`      | Use best network structure found so far (from `006`) and train a sequence predicting network similar to `010`. Use time NWP centred on sequence and last sat image. Also retrain some climatology models to act as baseline.                                                                                                                                                                                                                                            | Trained for ~45,000 (converged) batches and achieved average test MAE of 0.129 (0.118 at start of sequence to 0.137 at end of sequence). Found that a smaller image patch of only 24km worked better than the bigger patches of 60 or 144km. New climatology baseline of 0.26 for lat-lon, datetime only or 0.22 if clearsky GHI is also included.          |                                                                                                                                                         |
| - `014-MetNet_inspired_network_structure.ipynb`       | Similar to `010`, nowcast 0-60 mins ahead. This time using both a coarse resolution and a fine resolution component from the NWP/sat data                                                                                                                                                                                                                                                                                                                               | Trained for ~40,000 (converged) batches and achieved average test MAE of 0.122 (0.117 - 0.133)                                                                                                                                                                                                                                                              |                                                                                                                                                         |      
| - `015-UNet_inspired.ipynb`                           | Nowcasting 0 minutes into future. This network structure uses fine and coarse grain resolution information by downsampling and concatenating at different spatial resolutions. This is essentially UNet applied to predict a single point rather than an image. Used sat + NWP only.                                                                                                                                                                                    | Trained for ~12,000 (converged) batched and matched previous MAE record of 0.113. Tried using input spatial scales of 16 and 32 and both reach approximately the same MAE.                                                                                                                                                                                  |                                                                                                                                                         |