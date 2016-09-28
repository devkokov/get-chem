# get-chem

## Introduction

This is a piece of software that I wrote in a weekend for a friend of mine who's doing his PhD in Biology/Chemistry. I asked him to write the scientific description below, as he can explain it far better than I can, but the basic idea is that we use brute-force to generate A LOT of cell chemistry designs. Then we run a set of WEKA models (not included here) to calculate various predictions for each chemistry design. Then we load all predictions in memory and compare them to a set of known chemistry designs by calculating the Euclidean distances between them. Everything gets stored in a MySQL database to allow for reporting.

The Feature Scaling method (formula below) is used for normalising the data prior to calculating Euclidean distances.

![Feature scaling](https://wikimedia.org/api/rest_v1/media/math/render/svg/358923abc154221bb5022fc329061f6fc4dcc69f)

#### Scientist's description


## Requirements

- Linux OS (tested on CentOS)
- PHP >= 5.6
- MySQL server
- Java (OpenJDK is fine)
- WEKA 3.9.0
- Trained serialised WEKA models

## Configuration

### get-chem

All configuration of get-chem is done in `config/get-chem.ini`

1. Set correct file paths to temp folders and executables (e.g. WEKA install path etc.)
2. Set memory limit to as high as possible. Processing ~3.5 million chemistry designs with 8 inputs and 8 outputs needs roughly 6GB of RAM.
3. Set your database connection settings
4. Add any number of input and output variables. There must be at least 1 input and 1 output variable.

### Input Values

get-chem generates the input values for each of the defined `input_variables`, based on a set of criteria defined in the `input_values_settings` section in the config file.

There are 4 parameters that need to be defined for each input variable:

1. `start`: this will be the variable's starting value
2. `end`: this will be the variable's ending value
3. `step`: the variable will be incremented by this value until it reaches `end`
4. `overflow`: (`true` or `false`) If `true`, it will allow the variable's ending value to be greater than `end` by up to half a `step`.

**Example 1**
```
LogP1[start] = 1
LogP1[end] = 3.3
LogP1[step] = 0.5
LogP1[overflow] = true
```
Generates LogP1 values 1, 1.5, 2, 2.5, 3, 3.5

**Example 2**
```
LogP1[start] = 1
LogP1[end] = 3.3
LogP1[step] = 0.5
LogP1[overflow] = false
```
Generates LogP1 values 1, 1.5, 2, 2.5, 3

### Benchmark dataset

By default, the benchmark dataset is in `config/benchmark.csv`. The format of the CSV should be as follows:
- 'Name' column
- A column for each of the `input_variables` as defined in `config/get-chem.ini`
- A column for each of the `output_variables` as defined in `config/get-chem.ini`

### WEKA Models

By default, the trained (and serialised!) WEKA models should be placed in `library/WEKA/`. Note that the filenames of the models should match the `output_variables` defined in `get-chem.ini`. These are case-sensitive. There should be a WEKA model for each output variable.

Your WEKA models should be trained to accept a CSV input file that contains all `input_variables` as defined in `config/get-chem.ini`. The order of columns and letter-case DO matter! The last column in the input CSV will different per model, as this is where WEKA will output its prediction. The label of this last column should match one of the defined `output_variables`.

## Usage

1. Open a command line terminal and navigate to the directory where get-chem is isntalled
2. Run command `sudo php get-chem`
3. Follow the on-screen instructions
