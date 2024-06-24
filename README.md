# General Collection Degradation Model

## Overview
This repository contains the implementation of a collection degradation model written in R. The model simulates the behavior of a population of agents (representing objects in a collection) over multiple years, taking into account continuous degradation and adverse events.

## Model Description

### Initialization
The model initializes the population of agents with their initial conditions. Each agent has a condition \( C \) that follows a normal distribution with a user-defined mean and standard deviation, capped between 0 and 100. Initial information about the current condition of the collection can be introduced at this step.

### Simulation Loop
The model iterates over multiple years, simulating the behavior of the population for each year.

### Continuous Degradation
For each year, the condition of each agent is decreased by a degradation rate, calculated from temperature (T) and relative humidity (RH) conditions. Initially, the Strlic damage function is used to represent continuous degradation over time. Other models can be easily added to apply to different subsets of the population of agents.

### Adverse Events
The model predicts the occurrence of adverse events using a Weibull distribution. For each year, a random mean time before an adverse event is generated, and a time until event occurrence is sampled from the Weibull distribution. If the sampled time is less than or equal to 1 year, an adverse event is simulated for a fraction of the population, reducing their condition accordingly.

### Percentage of Objects in Good Condition
After each year, the model calculates the percentage of objects in good condition (condition \( C > 0 \)) and records it.

### Repetition
The simulation is repeated multiple times to capture the variability of the system. Typically, 10 runs are enough to reveal a characteristic pattern of decay for the collection.

### Analysis
After all simulations are completed, the model analyzes the results, including histograms of initial and final conditions, time series of the percentage of objects in good condition, and an overall collection lifetime calculated as the average time to reach 1% of the agents in good condition.

## Implementation in R

### Main Inputs and Outputs

The table below describes the main inputs and outputs of the first implementation of the model. The code is mostly reproducible following the information given above, except for some non-trivial decisions. The `truncnorm` package generates random numbers from a truncated normal distribution to initialize the conditions of the agents, ensuring that the starting values are within a realistic and predefined range.

#### Inputs
- **num_agents**: Number of agents (population size)
- **num_years**: Number of years to simulate
- **num_simulations**: Number of simulation runs
- **lower_bound**: Lower bound of the initial condition distribution
- **upper_bound**: Upper bound of the initial condition distribution
- **mean**: Mean of the initial condition distribution
- **sd**: Standard deviation of the initial condition distribution
- **deg_processes**: List of degradation processes with parameters
- **T**: Temperature for degradation rate calculation
- **RH**: Relative humidity for degradation rate calculation
- **pH**: pH level for degradation rate calculation
- **DP0**: Initial degradation potential for degradation rate calculation

#### Outputs
- **all_conditions**: Final conditions of agents for each simulation
- **all_percentage_good**: Percentage of agents in good condition over time
- **time_to_1_percent**: Time taken for the percentage of objects in good condition to drop to 1%
- **average_time**: Average time to reach 1% good condition across all simulations
- **sd_time**: Standard deviation of the time to reach 1% good condition

#### Plots
- **hist**: Histogram of initial and final agent conditions
- **time_series_plot**: Time series plot of percentage of objects in good condition

### Adverse Events Simulation

For each year in the simulation, a time until the next event occurrence is sampled from a Weibull distribution with a shape parameter of 1 (indicating an exponential distribution) and a scale parameter equal to the mean time between adverse events specified by the user.

The following R code snippet illustrates the use of the `rweibull` function to sample the time until the next event occurrence:

```r
mean_time <- runif(1, process$mean_time[1], process$mean_time[2])
time_until_event <- rweibull(1, shape = 1, scale = mean_time)
```

In this snippet, `mean_time` is a random mean time before an adverse event, sampled from the specified range. The `rweibull` function then generates a random deviate from a Weibull distribution with shape parameter 1 and scale parameter equal to the sampled mean time. This sampled time until event occurrence determines whether an adverse event will happen in the current year.

## License
This project is licensed under the GPL License. See the LICENSE file for details.

## Contributing
Contributions are welcome! Please open an issue or submit a pull request for any improvements or bug fixes.
