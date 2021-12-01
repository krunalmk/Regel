# REGEL: Regular Expression Generation from Examples and Language using Java

*Note: Since there were few files which were very large Git was unable to push it to GitHub as there is 100Mb limit for a single file in Git. So I have uploaded the complete project to [this link](https://drive.google.com/drive/folders/1sz302ahVrJ33pQm5uY2ol55BHysyLvTI?usp=sharing). Kindly download the project from the link mentioned.* 

## Prerequisites

Before runing the code for parsing your own language or reproducing experimental results, please first set up the [Sempre](https://github.com/percyliang/sempre) tool following the instructions below:

```shell
cd sempre
./pull-dependencies core
./pull-dependencies corenlp
./pull-dependencies freebase
./pull-dependencies tables
```

This repository also requires the following:

- [Z3](https://github.com/Z3Prover/z3). Make sure you have Z3 installed with the Java binding. 
- `ant` to compile the java files.
- `python3 ` 3.7
- `java` 1.8.0

Kindly run following command for building purposes-
```
cd sempre/fig && gradle build && cd .. && ant && ant compile && cd ..
```

##### `$trained_model`

I provided the pre-trained model for the two benchmarks datasets:

`so`: `pretrained_models/pretrained_so`

`deepregex`:`pretrained_models/pretrained_turk`

## Interactive Mode

We also provide a way to run Regel interactively (i.e. allowing users to interact with Regel by providing examples to refine the synthesis results).

### Run Interactive Mode with Arbituary Natural Language and Examples ("Customize" Mode)

```shell
python interactive.py --run_mode 0 --synth_mode $synthesis_mode --process_num $number_of_process_allowed --mem_max $max_memory_allowed --top $top_k_results_allowed --timeout $timeout_for_each_benchmark --max_iter $max_iter --skecth_num $number_of_sketch_per_benchmark --save_history $save_history
```

Example-

You may test the program with following command-
```
python interactive.py --run_mode 0 --synth_mode 1 --processnum 1 --mem_max 20 --top 5 --timeout 60 --max_iter 5 --sketch_num 25
```

##### `$top_k_results_allowed`

In interactive Regel, we only show user the first k finished sketch results. 

##### `$save_history`

The interactive Regel allows you to stop at any point working and continue from where you left last time. Set this to `True` if you wants to enable this functionality. 

##### `$max_iter`

The maximum of interaction allowed for each benchmark. 

#### The workflow of the interactive script (customize mode):

1. Enter a file name for your benchmark
2. Enter the natural language
3. Enter the examples
4. Indicating whether the example entered is a positive or negative example: use + to indicate it is a positive example, and - to indicate it is a negative example

The benchmark file created will be saved at `exp/customize/benchmark/$benchmark_file_name`

5. Regel will generate `$number_of_sketch_per_benchmark` number of sketches.

The sketch file created will be saved at `exp/customize/sketch/$benchmark_file_name`

6. Regel will run the synthesizer, and return either `$top_k_results_allowed` number of results or indicate it times out. 
7. Regel will ask you if there is any correct regex returned. Enter `y` if there is and enter the correct regex index.
8. If you enter `n` for the last question, Regel will ask you to enter two additional examples to disambiguate the regexes
9. Enter the examples in the same way as you enter the initial examples. After getting the additional examples, Regel will rerun the synthesizer and return the updated synthesis result. 

### Output

The output of execution is saved at `interactive/$your_benchmark_domain/logs/$synthesis_mode/raw_output.csv`.

If you uses `customize` mode, `$your_benchmark_domain = 'customize'`

## Train Sketch Parser

We have provided pretrained model that is ready to use in `sempre/pretrained`. We as well provide a examples procedure of how to train a model on **StackOverflow** dataset. 

**Prepare Train Set File**

We show a example train set file in `data/so.raw.txt`. It is a TSV file with three fields (`ID`, `NL`, and `Sketch`). Please prepare your train set file in this format, and put it in the `sempre/dataset/` directory with the name `*dataset-name*.raw.txt`.

**Train Parse Script**

To train a model, call

`python py_scripts/train.py *dataset-name* *model-dir*` 

E.g. `python py_scripts/train.py so models/so`

This command will preproces the training data (to fit the form required by `Sempre`. The processed data form will be stored in `regex/data/so`) and train a model that will be saved in the `*model-dir*` directory.

**Parse Script**

To parse a set of language descriptions using trained model, call

`python pyscripts/parse.py *dataset-name* *model-dir* *topk*`, where `*topk*` is the desired number of sketches.

The parsed sketches will be in `ouputs/*dataset-name*`, where each file contains the sketches for a single benchmark.


