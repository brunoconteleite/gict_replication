# On the Geographical Implication of Carbon Taxes - Replication package

Authors: Bruno Conte, Klaus Desmet, and Esteban Rossi Hansberg

# Overview

## General structure

This replication package is organized as follows. The main (root) folder contains the following folders:

* ``codes/``: contains all Matlab/R codes used as inputs in the main replication codes.

* ``input/``: contains the needed inputs (data and more) used in the main replication codes.

* ``output/``: where all output is stored and processed.

Besides, it also contains the ``README`` files and the main replication codes (explained next).

## Replicating the results

Replicating the results consist of running a sequence of ``R`` and ``Matlab`` codes, followed by the paper ``.tex`` source. In what follows, we provide an overview of the sequential workflow of the replication and a brief description of each code. Sections 2 to 4 provide further details.

The sequence of codes are the following:

1. ``gict_initialize.m``: initializes the replication package by exporting some raw data to be further processed in ``R``.
2. ``gict_data.R``: processes and exports data from previous step and produces further inputs for the simulations.
3. ``gict_replicate.m``: runs all simulations sequentially.
4. ``gict_results.m``: processes all the results from the previous step, reproduces several plots, and exports inputs needed for producing the maps in ``R``.
5. ``gict_maps.R``: reproduces all the maps of the paper.
6. ``gict.tex``: reproduces the paper using the output of the previous steps.

Throughout, we use several inputs from previous papers, which we explain next.

## Inputs

* ``input/H0_areal.mat``: 180x360 grid with fraction of land per cell.
* ``input/C.csv``: 180x360 grid of the country indices per cell. From Desmet et al. (2018).
* ``input/desert.csv``: 180x360 grid of desert dummies. From Desmet et al. (2018).
* ``input/l.csv`` and ``input/pop1.csv``: 180x360 grids of the population level (number of people per cell) in the data for years 2000 and 20011, respectively. From Desmet et al. (2018).
* ``input/y.csv``: 180x360 grid of cell level GDP (in PPP) for 2000 divided by cell population. From Desmet et al. (2018).
* ``input/Y_A.csv``: 180x360 gird of cell level agricultural GDP (in PPP) for 2000 produced by Conte et al. (2021);
* ``input/ubar.csv``: 180x360 grid of subjective utility measure for each country, based on the Cantril ladder (Deaton and Stone, 2013);
* ``input/trmult reduced.mat``: matrix of bilateral trade costs across land cells to the power -theta (trade elasticity). From Desmet et al. (2018).
* ``input/T0.csv`` and * ``input/T.mat``: 180x360 grid of initial temperatures in 2000 and the evolution of local temperatures throughout the 21st from Conte et al. (2021).
* ``input/ksi.csv``: 180x360 grid of downscalling parameters (\ksi(s)) estimated in Conte et al. (2021).
* ``input/ne_50m_admin_0_countries.*``: files that compose the world's shapefile (used to render the maps).
* ``input/gaoi_with_data.rdata``: spatial grid in ``R``data format.
* ``input/grid_025_clipped_intersected.rdata``: spatial grid at .25 x .25 degree clipped and intersected with the country shapes (used to render the maps).
* ``codes/inversions/gict_functions.R``: loads several functions used in the main ``R`` replication codes.

# Pre-processing data

The codes ``gict_initialize.m`` and ``gict_data.R`` pre-process the some inputs. In particular, the former exports the raw inputs from Conte et al. (2021) - ``y.csv`` and ``Y_A.csv`` (as, for instance, ``Y_A_raw.csv``).

Then, ``gict_data.R`` imports the raw input and corrects the agricultural input in grid cells where it exceeds total GDP (measurement error from Conte et al. (2021)). In particular, it assigns to those cells the average of the relative agricultural GDP of the neighboring cells of the same country. Moreover, this code also exports **regional specific geographical indeces**: 180x360 grids of dummies for cells in a certain region (e.g. ``input/index_eu26.csv`` stands for dummies of the countries at the European Union).

# Simulations

All of the simulations are *sequentially* performed by the ``gict_replicate.m`` code. It calls the codes for each specific simulation, which are stored in the ``codes/`` and ``inversion/`` folders. Within the latter, the following codes are called:

* ``inversion.m``: does the inversion for sectoral productivities and amenities as in Desmet et al. (2018) and Conte et al. (2021).
* ``moving_costs.m``: does the inversion for mobility costs separates amenities from initial utility as in Desmet et al. (2018) and Conte et al. (2021).
* ``theta_inversion.m``: does the inversion for sectoral productivities and amenities as in Desmet et al. (2018) and Conte et al. (2021) *for different values of theta* (trade elasticity).
* ``theta_moving_costs.m``: does the inversion for mobility costs separates amenities from initial utility as in Desmet et al. (2018) and Conte et al. (2021) *for different values of theta* (trade elasticity).
* ``omega_moving_costs.m``: does the inversion for mobility costs separates amenities from initial utility as in Desmet et al. (2018) and Conte et al. (2021) *for different values of omega* (migration elasticity).

Besides, the main simulation codes (stored in ``codes/``) stand for:

* ``*reb/``: simulations for each rebating scheme (i.e. ``noreb`` = simulation without rebating) for 100 periods and CO2 tax = 40USD/tCO2 starting in 2021. Stores the outputs in the ``output/`` folder.
* ``*reb_HS/``: simulations for each rebating scheme and several CO2 tax values (0 to 65 USD/tCO2) for the *hump-shaped* (``_HS``) curves. It runs for 21 periods. Stores the outputs in the ``output/`` folder.
* ``locreb_HS_theta/``: simulations with local rebating of CO2 taxes of different values (0 to 65 USD/tCO2) for the *hump-shaped* (``_HS``) curves. It runs for 21 periods *for different values of theta* (the trade elasticity). Stores the outputs in the ``output/`` folder.
* ``locreb_HS_moega/``: simulations with local rebating of CO2 taxes of different values (0 to 65 USD/tCO2) for the *hump-shaped* (``_HS``) curves. It runs for 21 periods *for different values of omega* (the migration elasticity). Stores the outputs in the ``output/`` folder.
* ``locreb_HS_*/``: simulations with local rebating of CO2 taxes of different values (0 to 65 USD/tCO2) **in a different country/region** (e.g. ``locreb_HS_US/`` = CO2 taxes in the US). Used to produced the *hump-shaped* (``_HS``) curves for countries/regions different than the EU. Stores the outputs in the ``output/`` folder.

Note that ``gict_replicate.m`` runs all the simulations *sequentially*, which makes it a time-consuming task (each individual simulation can take half a day to run on a rather powerfull CPU).

To address that, each simulation folder in ``codes/`` contains also a ``.sh`` shell code that can be run individually in high-performance computers (e.g. be submitted as a *job* in a server-like super computer). By launching each of them individually in such a computer allows one to reproduce all results in *in parallel*. Importantly, before that one would need to run all of the codes contained in the ``codes/inversion/`` folder.

# Processing the results

The codes ``gict_results.m`` and ``gict_maps.R`` process all of the simulation results and report them as documented in the paper. In particular, ``gict_results.m``:

1. Exports all results in the ``R`` grid format to be used as inputs for the maps.
2. Plots the evolution of region-sectoral output over time with/out CO2 taxes for several rebating schemes (like Figure 2).
3. Reproduces the tables with the aggregate/regional effects of CO2 taxes for all rebating schemes (like Table 1).
4. Reproduces the plots of regional effects of CO2 taxes with respect to different taxe values (like Figure 4).
5. Calculates and plots the effects of CO2 taxes on emissions and temperature with different rebating schemes (Figures 6 and 18).

Then, ``gict_maps.R``:

1. Reproduces the maps of the paper using some of the outputs from ``gict_results.m``.
2. **Renders the final ``.pdf``** using the ``gict.tex`` latex source.

# References

* Conte, B., Desmet, K., Nagy, D.K. and Rossi-Hansberg, E., 2021. Local sectoral specialization in a warming world. *Journal of Economic Geography*, *21(4)*, pp.493-530.

* Deaton, A. and Stone, A.A., 2013. Two happiness puzzles. *American Economic Review*, *103(3)*, pp.591-97.

* Desmet, K., Nagy, D.K. and Rossi-Hansberg, E., 2018. The geography of development. *Journal of Political Economy*, *126(3)*, pp.903-983.

