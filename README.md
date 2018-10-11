
# FiRE - Finder of Rare Entities
## Contents
   [Installation Guide](#install)<br />
     -[Prerequisites](#pre)<br />
     -[Installation Steps of FiRE software](#install-steps)<br />
   [Data Pre-processing](#data-pre-processing)<br />
   [python API](#python-api)<br/>
   [R API](#r-api)<br />
   [Usage of FiRE](#usage-of-fire-software)<br />

<a name="install"></a>
## Installation Guide

<a name="pre"></a>
### Prerequisites

Tested on Ubuntu 14.04 and Ubuntu 16.04.

All results in manuscript have been generated using `python` module.

FiRE is available for `python` and `R`. Required versions and modules for both are as mentioned below. `cpp` modules are necessary for both of them.

1. <h4>Required python modules</h4>
```python
    python 2.7
```

For FiRE module
```python
    cython >= 0.23.4
    distutils >= 2.7.12
```

For preprocessing
```python
    numpy >= 1.13.3
    pandas >= 0.20.3
    statsmodels >= 0.8.0
```

For demo
```python
    gzip >= 1.2.11 (zlib)
    scipy >= 1.1.0
    matplotlib >= 2.1.0
    cmocean >= 1.2
    sklearn >= 0.19.1
```

2. <h4>Required R modules</h4>
```R
    R >= 3.2.3
    Rcpp >= 0.12.19
```

3. <h4>Required cpp modules</h4>
```cpp
    g++ >= 4.8.4
    boost >= 1.54.0
```
FiRE only needs `<boost/random.hpp>` from boost. So, full installation is not necessary. It can be downloaded from [boost.org](https://www.boost.org/) and used as is.

<a name="install-steps"></a>
### Installation Steps of FiRE software

```bash
    [sudo] ./INSTALL [ --boost-path <boost-path> | --log-file <log-file> | --inplace | --py | --R | --help ]
    [sudo] ./UNINSTALL_python
    [sudo] ./UNINSTALL_R

    --boost-path <boost-path>  : python | R    : Path to boost-library, if boost is not installed at default location, this value needs to be provided.
    --inplace                  : python        : Required only for python, if set, inplace build will be run and resulting lib will be stored in python/FiRE.
    --log-file <log-file>      : python        : Required only for python, ignored with --inplace set.
    --py                       : python        : Install FiRE in python environment.
    --R                        : R             : Install FiRE in R environment.
    --help                     : python | R    : Display this help.

    Info:

    UNINSTALL_[python | R] files are generated upon installation.
```

[Only Python] Typically, FiRE module takes a few seconds to install. A snippet of installation time taken by FiRE (in seconds) on a machine with Intel® Core™ i5-7200U (CPU @ 2.50GHz × 4), with 4GB memory, and OS Ubuntu 16.04 LTS is as follows

```bash
real 3.06
user 2.90
sys 0.15
```

<h4>If boost is installed at default location</h4>

```bash
    sudo ./INSTALL [ --py | --R ]
```

<h4>If boost is installed at custom location</h4>

```bash
    sudo ./INSTALL --boost-path <full-path> [ --py | --R ]

```
Example:
```bash
    sudo ./INSTALL --boost-path $HOME/boost/boost_1_54_0 [ --py | --R ]
```
[Only Python] Above installation steps will generate `fireInstall.log` file. It is advisable to keep this file, since it will be needed for uninstallation. Name of the log file can be modified during installation.

```bash
    ./INSTALL --log-file <log-file-name> [--py]
```

Above steps will install `FiRE` at the default location.

<h4>[Only Python] For `inplace` installation</h4>

```bash
    ./INSTALL --inplace [--py]
```

<h4> Uninstallation of FiRE Software.</h4>

```bash
    [sudo] ./UNINSTALL_python
    [sudo] ./UNINSTALL_R
```

<a name="data-pre-processing"></a>
## Data Pre-processing
1. <h4>Load python libraries</h4>
```python
import numpy as np
```

2. <h4>Load Data in current environment.</h4>
```python
#Data matrix should only consist of values where rows represent cells and columns represent genes.
```

3. <h4>Copy preprocess.py in current working directory and load preprocess.</h4>
```python
import preprocess as pp
```

4. <h4> Call function ranger_preprocess for selecting thousand variable genes.</h4>
```python
preprocessedData, selGenes = pp.ranger_preprocess(data, genes, ngenes_keep=1000, dataSave='./', optionToSave=False, minLibSize=0, verbose=True)
```

|Parameter | Description | Required or Optional| Datatype | Default Value |
| -----:| -----:| -----:|-----:|-----:|
|data | Data for processing | Required | `np.array [nCells, nGenes]` | - |
|genes | Names of Genes | Required | `np.array [nGenes]` | - |
|ngenes_keep | Number of genes to keep | Optional | `integer` | 1000 |
|dataSave | Path to save results | Optional | `string` | Current working Directory (Used only when optionToSave is True) |
|optionToSave | Save processed output or not | Optional | `boolean` | False(Does not save) |
|minLibSize | Minimum number of expressed features | Optional | `integer` | 0 |
|verbose | Display progress | Optional | `boolean` | True(Prints intermediate results) |

```python
'''
Returned Value :
    preprocessedData : processed data matrix (log2 transformed) : np.array [nCells, nVariableGenes]
    selGenes         : Names of thousand variable genes selected : np.array [nVariableGenes]
'''
```

<a name="python-api"></a>
## python API
1. <h4>Load python module of FiRE software.</h4>
```python
import FiRE
```

2. <h4>Create model of FiRE.</h4>
```python
model = FiRE.FiRE(L=100, M=50, H=1017881, seed=5489, verbose=0)
```

|Parameter | Description | Required or Optional| Datatype | Default Value |
| -----:| -----:| -----:|-----:|-----:|
|L | Total number of estimators | Required | `int` | - |
|M | Number of features to be randomly sampled for each estimator | Required | `int` | - |
|H | Number of bins in hash table | Optional | `int` | 1017881|
|seed | Seed for random number generator | Optional | `unsigned int` | 5489|
|verbose | Controls verbosity of program at run time (0/1) | Optional | `int` | 0 (silent) |

3. <h4>Apply model to the above dataset.</h4>
```python
model.fit(preprocessedData)
```

4. <h4>Calculate FiRE score of every cell.</h4>
```python
score = np.array(model.score(preprocessedData))
```

5. <h4>Access to model parameters.</h4>
Sampled dimensions can be accessed via
```python
# type : 2d list
# shape : L x M
model.dims
```
Chosen thresholds can be accessed via
```python
# type : 2d list
# shape : L x M
model.thresholds
```

Weights can be accessed via
```python
# type : 2d list
# shape : L X M
model.weights
```

Hash tables can be accessed via
```python
# type : 3d list
# shape : L x H x <dynamic>
# <dynamic> : as per number of samples in a bin (H) for a given estimator (L).
model.bins
```

<a name="r-api"></a>
## R API
1. <h4>Load R module of FiRE software.</h4>
```R
library('FiRE')
```
2. <h4>Create model of FiRE.</h4>
```R
# model <- new(FiRE::FiRE, L, M, H, seed, verbose)
model <- new(FiRE::FiRE, 100, 50, 1017881, 5489, 0)
```

|Parameter | Description | Required or Optional| Datatype | Default Value |
| -----:| -----:| -----:|-----:|-----:|
|L | Total number of estimators | Required | `int` | - |
|M | Number of features to be randomly sampled for each estimator | Required | `int` | - |
|H | Number of bins in hash table | Optional | `int` | 1017881|
|seed | Seed for random number generator | Optional | `int` | 5489|
|verbose | Controls verbosity of program at run time (0/1) | Optional | `int` | 0 (silent) |

3. <h4>Apply model to the above dataset.</h4>
```R
model$fit(preprocessedData)
```
Acceptable datatype is of `matrix` class and of `type` `double` (`Numeric matrix`).

4. <h4>Calculate FiRE score of every cell.</h4>
```R
# Returns a numeric vector
score <- model$score(preprocessedData)
```

5. <h4>Access to model parameters.</h4>
Sampled dimensions can be accessed via
```R
# type : Integer matrix
# shape : L x M
model$d
```
Chosen thresholds can be accessed via
```R
# type : Numeric matrix
# shape : L x M
model$ths
```

Weights can be accessed via
```R
# type : Numeric matrix
# shape : 0 x 0
model$w

# Internally this vector is represented as unsigned int.
# Since R does not have unsigned data type. This function return a blank matrix.
# instead, it prints the matrix if number of elements is less than or equal to 100.
# Though FiRE provides API to save the weight matrix.
model$dump_w(filename)

```

Hash tables can be accessed via
```R
# type : List
# shape : L x H x <dynamic>
# <dynamic> : as per number of samples in a bin (H) for a given estimator (L).
model$b
```


<a name="usage-of-fire-software"></a>
## Usage of FiRE Software

1. <h4>Select cells with higher values of FiRE score, that satisfy IQR-based thresholding criteria.</h4>

```python

scoreFile = 'Results/score_hypothalamus.txt'
np.savetxt(scoreFile,score) #Save scores

from scipy import stats

q3 = np.percentile(score, 75)
iqr = stats.iqr(score)
th = q3 + 1.5*iqr

indIqr = np.where(score >= th)[0]

dataSel = preprocessedData[indSel,:] #Select subset of cells
dataSelFile = 'Results/dataSel_hypothalamus.txt'
np.savetxt(dataSelFile,dataSel)

```

2. <h4>Run [dropClust](https://github.com/debsin/dropClust) and predict rare clusters.</h4>
    <!--![Predicted 12 rare clusters using dropClust](image/dropClustClusters.png =250x250)-->
    <img src="image/dropClustClusters.png" width="500" height="350" />

3. <h4>Find genes up-regulated for predicted rare clusters.</h4>


<!--![violin plot of markers](image/secondRareHypoViolin.png)-->
<img src="image/secondRareHypoViolin.png" width="500" height="700" />
