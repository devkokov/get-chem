# get-chem

## Introduction

This piece of software is meant for numerical optimization for low or high-dimensionality problems. It generates test cases as inputs, calls WEKA predictive models (Java) to estimate outputs and then compares the mathematical closeness/distance of these outputs compared to benchmark(s). The user can specify the input variables (min/increment/max values), WEKA models need to be pre-made, and outputs are user-specified. Once outputs are estimated and benchmark(s) given, all is held in memory to normalise (optional) and perform comparison calculations. The software then seeks a MySQL database to store results and report.


The data can be normalised (optional) using one of the below methods prior to comparing results:

"Rescaling" normalisation method (formula below)

![Rescaling](https://wikimedia.org/api/rest_v1/media/math/render/svg/358923abc154221bb5022fc329061f6fc4dcc69f)

"Standardization" normalisation method (formula below)

![Standardization](https://wikimedia.org/api/rest_v1/media/math/render/svg/b0aa2e7d203db1526c577192f2d9102b718eafd5)


Results are compared using one of the below methods:

- Euclidean Distance
- Dissimilarity Index


## Requirements

- Linux OS (tested on CentOS)
- PHP >= 5.6
- MySQL server
- Java (OpenJDK is fine)
- WEKA 3.8.0
- Trained serialised WEKA models

## Configuration

### get-chem

All configuration of get-chem is done in `config/get-chem.ini`

1. Set correct file paths to temp folders and executables (e.g. WEKA install path etc.)
2. Set memory limit to as high as possible. Processing ~4 million test cases with 8 inputs and 8 outputs needs ~6GB of RAM on CentOS.
3. Set your database connection settings
4. Set a comparison method
5. Set a normalisation method (optional). Set to "none" to switch normalisation off
6. Set desired decimal precision for calculations
7. Add any number of input and output variables. There must be at least 1 input and 1 output variable.

### Input Values

get-chem generates the input values for each of the defined `input_variables`, based on a set of criteria defined in the `input_values_settings` section in the config file.

There are 4 parameters that need to be defined for each input variable:

1. `start`: this will be the variable's starting value
2. `end`: this will be the variable's ending value
3. `step`: the variable will be incremented by this value until it reaches `end`
4. `overflow`: (`true` or `false`) If `true`, it will allow the variable's ending value to be greater than `end` by up to half a `step`.

**Example 1**
```
Var1[start] = 1
Var1[end] = 3.3
Var1[step] = 0.5
Var1[overflow] = true
```
Generates Var1 values 1, 1.5, 2, 2.5, 3, 3.5

**Example 2**
```
Var1[start] = 1
Var1[end] = 3.3
Var1[step] = 0.5
Var1[overflow] = false
```
Generates Var1 values 1, 1.5, 2, 2.5, 3

### Benchmark data set

By default, the benchmark data set is in `config/benchmark.csv`. The format of the CSV should be as follows:
- 'Name' column
- A column for each of the `input_variables` as defined in `config/get-chem.ini`
- A column for each of the `output_variables` as defined in `config/get-chem.ini`

IMPORTANT: test cases are compared against the last row of the benchmark data set.

### WEKA Models

By default, the trained (and serialised!) WEKA models should be placed in `library/WEKA/`. Note that the file names of the models should match the `output_variables` defined in `get-chem.ini`. These are case-sensitive. There should be a WEKA model for each output variable.

Your WEKA models should be trained to accept a CSV input file that contains all `input_variables` as defined in `config/get-chem.ini`. The order of columns and letter-case DO matter! The last column in the input CSV will be different per model, as this is where WEKA will output its prediction. The label of this last column should match one of the defined `output_variables`.

## Usage

1. Open a command line terminal and navigate to the directory where get-chem is installed
2. Run command `sudo php get-chem`
3. Follow the on-screen instructions
