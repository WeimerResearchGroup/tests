# Overview
Structure of the test_environment/ directory:
```
├── correctness
│   ├── test_correctness.py
│   ├── correctness_ignore
│   ├── test_easy1_correctness.py
|   |     ...
│   ├── test_medium6_correctness.py
│   ├── easy1.py
|   |     ...   
│   └── medium6.py
├── efficiency
│   ├── test_efficiency.py
│   ├── test_easy1.efficiency.py
|   |     ...
│   ├── test_medium6_efficiency.py
│   ├── optimal_easy1.py
|   |     ...
│   ├── optimal_medium6.py
│   ├── easy1.py
|   |     ...   
│   └── medium6.py
├── individual
│   ├── test_individual.py
│   ├── test_easy1.individual.py
|   |     ...
│   ├── test_medium6_individual.py
│   ├── easy1.py
|   |     ...   
│   └── medium6.py
├── scores
│   ├── correctness_all_files
│   │   ├── <participant_id>
│   │   ├── <participant_id>
│   │   ├── ...
│   ├── efficiency_scores
│   ├── get_highest_scores.py
│   ├── highest_and_last_scores
├── test_data
│   ├── <participant_id>-<problem set>
│   │   ├── ...
│   ├── <participant_id>-<problem set>
│   │   ├── ...
│   ├── ...
├── README.md
├── session_mapping.csv
└── get_test_data.py
└── requirements.txt
```
Before running tests, make sure the backup/ folder which contains all the raw session saved files is present.
<br>
The general steps are:
<br>
### 1. Generate the test_data/ folder from ~/backup/ using 
This is to transfer all the raw session folders from ~/backup/ to test_data/, and to ensure all the transferred folders follow the **\<participant_id\>-\<problem set\>** naming fashion. 
### 2. Run correctness tests on a time series problem
This step runs correctness tests on all timestamped programs, and generates `scores/correctness_all_files/<participant_id>` for each participant.
### 3. Get highest and latest scores
This step traverses all participants under`scores/correctness_all_files/` and generates `scores/highest_and_last_score` which contains the highest and last scores for each participant.
### 4. Run efficiency tests
This step runs efficiency tests on participants' highest-scored and last-saved programs. It generates `scores/efficiency_scores` which contains both correctness score and efficiency score for each participant's best and last program.
### 5. Run individual test on a program
This step runs individual tests on a single timestamped program or a known best/last program for one participant.

# Generate the test_data/ directory

```
test_environment$ python3 get_test_data.py
```
Note: 
1. Make sure `test_environment/session_mapping.csv` is up-to-date. It should be the same version as [zoom link + notes](https://docs.google.com/spreadsheets/d/1H-CLT6qM0k7WD4kvF_-pQHabqpj6KVvdTUdOdzgUJSc/edit#gid=0)
2. The program will skip sessions that have already been generated in test_data/.


# Correctness
##  scores/correctness_ignore
Each line in this file means that problem will be skipped when running `correctness/test_correctness.py`.
<br>
Sample lines:
```
abs33 A2
abs33 B1
oprtt A3 (ignore the problem A3 of oprtt)
huihu A (ignore all files of the session A of huihu)
iiooh (ignore all files of iiooh)
```
## Run Tests
### Run on all files
```
test_environment$ python3 correctness/test_correctness.py > /dev/null
```
Note: This will ignore problems in `scores/correctness_ignore`
### Run on one problem
```
test_environment$ python3 correctness/test_correctness.py <participant_id> <problemset> <problem number> > /dev/null
```
Note: This will still run tests on the problem even if it exists in `scores/correctness_ignore`
<br>
Sample command:
```
test_environment$ python3 correctness/test_correctness.py abs33 A 2 > /dev/null
```
### Run on one session
```
test_environment$ python3 correctness/test_correctness.py <participant_id> <problemset> > /dev/null
```
Note: This will still run tests on the problem even if it exists in `scores/correctness_ignore`
<br>
Sample command:
```
test_environment$ python3 correctness/test_correctness.py abs33 A > /dev/null
```
## Outputs
The outputs will be appended to corresponding files under `scores/correctness_all_files/`. *Run on all files* will not replace previous outputs in that file, but *Run on one problem* and *Run on one session* will replace the previously generated lines in the corresponding file.
# Get highest and latest scores
The `scores/get_highest_score.py` program reads files under `scores/correctness_all_files` and outputs the highest score and the last score of that participant. The output file is `scores/highest_and_last_scores`
```
test_environment$ cd scores
test_environment/scores$ python3 get_highest_score.py
```

# Efficiency
## Optimal Solutions
The optimal solutions are in files `efficiency/optimal_*.py`. Everytime you run `efficiency/test_efficiency.py`, it will first run the tests on the optimal solutions to obtain the optimal runtime at that time.
## Run Tests
### Run on all files
```
test_environment$ python3 efficiency/test_efficiency.py > /dev/null
```
Note: This runs all files documented in `scores/highest_and_last_scores`.
### Run on one problem
```
test_environment$ python3 efficiency/test_efficiency.py <participant_id> <problemset> <problem number> > /dev/null
```

Sample command:
```
test_environment$ python3 efficiency/test_efficiency.py abs33 B 1 > /dev/null
```
## Outputs
The outputs will be appended to `scores/efficiency_scores`. *Run on all files* will not update existing contents in this file, but *Run on one problem* will update the previous results in this file.
Each line in this file follows the format of:
```
<participant_id> <problem_name> <best_or_last> <timestamp> correctness_score: <correctness_score>  average_run_time:<average_time>  optimal_time: <optimal_time>
```


# Individual
## Run Tests
### Run on one problem with its timestamp
```
test_environment$ python3 individual/test_individual.py <participant_id> <problem_set> <problem_num> <timestamp>
```
Sample command:
```
test_environment$ python3 individual/test_individual.py dayta B 1 20230510172624
```
### Run on the best/last problem
```
test_environment$ python3 individual/test_individual.py <participant_id> <problem set> <problem num> best/last
```

Sample command:
```
test_environment$ python3 individual/test_individual.py abs33 B 1-lax best
```
## Outputs
The outputs will be displayed in the terminal. No output files will be generated.
