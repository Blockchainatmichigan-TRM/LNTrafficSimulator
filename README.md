﻿# LNTrafficSimulator

This repository contains the Lightning Network (LN) traffic simulator used in the cryptoeconomic research of [Ferenc Béres](https://github.com/ferencberes), [István András Seres](https://github.com/seresistvanandras) and András A. Benczúr.

# Introduction

In our work, we designed a traffic simulator to empirically study LN’s transaction fees and privacy provisions. The simulator relies only on publicly available data of the network structure and capacities, and generates transactions under assumptions that we validated based on information spread by  [blog posts](https://www.trustnodes.com/2019/08/20/guy-makes-20-a-month-for-locking-5-million-worth-of-bitcoin-on-the-lightning-network?fbclid=IwAR2-p8nWdg0ayO9S0Uz7qg3wmh_A8Wy6ueX8r3dLQvDTyJaj1ReSbYalnWI) of LN node owners.

## Cite

**A link for our pre-print paper will be published here in a few days.**

# What's in it for me?
We think that our simulator can be of interest mainly for two types of people: LN node owners and researchers. Hence, the simulator can answer the following questions of interest for these people:

## i.) LN node owners, routers:
- What is the optimal fee I could charge for transactions going through my node in order to maximise my routing profits?
- What is my expected income from routing with respect to certain parameters (topology, traffic, transacted amounts)?
- How various parameters (topology, traffic, transacted amounts) affect the profitability of my nodes?

## ii.) Researchers:
- What is the optimal fee nodes can charge? How far is it (if at all) from on-chain fees?  
- What is the path length distribution of transactions on the LN graph? How much anonymity do they provide? 
- How profitable is it to run a router node? Who are the most profitable ones?
- Is everyone altruistic on the LN transaction fee market?
- How various parameters (topology, traffic, transacted amounts) affect the profitability of each node?

# Requirements

- UNIX or macOS environment
    - For macOS users: you need to have wget (brew install wget) 
- **Python 3.5** conda environment with the following packages installed:
    - **data processing:** pandas, numpy, networkx, matplotlib
    - **general:** sys, os, json, copy, tqdm, collections, functools, concurrent

# Data

By providing daily LN snapshots as input **(you can bring and use your own!)**, our simulator models the flow of daily transactions. 

## i.) Download

You can download the data files, that we used in our research by executing the following command:

#### a.) Linux
```bash
bash ./scripts/download_data.sh
ls ln_data
```
#### b.) macOS
```bash
sh ./scripts/download_data.sh
ls ln_data
```

## ii.) Content

After running the *download_data.sh* script 4 data files can be observed in the *ln_data* folder:

| File | Simulator input? | Content |
|     :---      |   :---:   |   :---   |
| **sample.json** | Yes | sample JSON file containing a daily LN snapshot |
| **ln_edges.csv** | Yes | preprocessed LN snapshots in the form of a directed graph |
| **1ml_meta_data.csv** | Yes | merchant meta data that we downloaded from [1ml.com](https://1ml.com/) |
| **ln.tsv** | No | edge stream data about LN channels |

You can also download the compressed data file with this [link](https://dms.sztaki.hu/~fberes/ln/ln_data_2019-10-29.zip).


# Usage

**You must download the data as described in the Data section to use our simulator!**

## i.) Parameters:

| Parameter | Description |
|     :---      |   :---   |
| `amount` |  value of each simulated transaction in satoshis  |
| `count`  | number of random transactions to sample  |
| `epsilon` |  ratio of merchants among transactions endpoints  |
| `drop_disabled` | drop temporarily disabled channels |
| `drop_low_cap` | drop channels with capacity less than `amount` |
| `with_depletion` | the available channel capacity is maintained for both endpoints |

You can set the value of these parameters in a JSON [configuration file](scripts/params.json).

## ii.) Execution

You can run our LN traffic simulator with two different settings. 

### a.) Load data from preprocessed file

The default input format of the simulator is a directed graph representation of LN snapshots. In this case you must provide the *snapshot_id* (e.g. 0) and the *output folder* as parameters.

```bash
cd scripts
python run_simulator.py preprocessed 0 params.json YOUR_OUTPUT_DIR
```

### b.) Load data from (custom) JSON file

You can execute the simulator on custom data as well, by providing daily LN snapshot as a JSON file (e.g. [lnd](https://graph.lndexplorer.com/api/graph)). See the example below:

```bash
cd scripts
python run_simulator.py raw ../ln_data/sample.json params.json YOUR_OUTPUT_DIR
```

**Note:** If you have **multiple CPUs** at your disposal then we recommend setting a higher value for the `max_threads` parameter in the simulator [script](scripts/run_simulator.py).

**Note:** runtime decreases significantly if you set `find_alternative_paths=False` in the simulator [script](scripts/run_simulator.py). In this case base fee optimization is not executed.

## iii.) Output

After execution you will find the output files in the provided YOUR_OUTPUT_DIR folder. The content of these files are as follows:

| File | Content |
|     :---      |   :---   |
| params.json | Traffic simulator parameter values |
| lengths_distrib.csv | Length distribution of simulated transactions |
| router_incomes.csv | Contains the total routing income (satoshi) and the  number of routed payments for LN nodes in a simulation |
| source_fees.csv | Contains the mean transaction costs (satoshi) and the  number of sent payments for transaction initiator nodes |
| opt_fees.csv | For each router the estimated optimal increase in base fee  and gain in daily routing income is presented |

We provide detailed information on the output files [here](scripts/README.md).

**In order to get stable daily LN node statistics, we recommend to run the simulator for multiple times over several consecutive snapshots!**


# Acknowledgements

We are grateful to Antoine Le Calvez (Coinmetrics) and Altangent Labs for kindly providing us their edge stream data and daily graph snapshots. We are thankful to [Domokos M. Kelen](https://github.com/proto-n) and [Rene Pickhardt](https://github.com/renepickhardt) for insightful discussions.
