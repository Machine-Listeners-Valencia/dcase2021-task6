# Audio captioning DCASE baseline system - 2020

Welcome to the repository of the audio captioning baseline system
for the DCASE challenge of 2020. 

Here you can find the complete code of the baseline system, consisting
of:

  1. the caption evaluation part,
  2. the dataset pre-processing/feature extraction part,
  3. the data handling part for Pytorch library, and
  4. the deep neural network (DNN) method part
  
Parts 1, 2, and 3, also exist in separate repositories. Caption evaluation
tools for audio captioning can also be found
[here](https://github.com/audio-captioning/caption-evaluation-tools). 
Code for dataset pre-processing/feature extraction for Clotho dataset can
also be found [here](https://github.com/audio-captioning/clotho-dataloader).
Finally, code for handling the Clotho data (i.e. extracted features and 
one-hot encoded words) for PyTorch library (i.e. PyTorch DataLoader for
Clotho data) can also be found
[here](https://github.com/audio-captioning/clotho-dataloader).

This repository is maintained by [K. Drossos](https://github.com/dr-costas). 


## Table of contents

  1. [Setting up the code](#setting-up-the-code)
  2. [Preparing the data](#preparing-the-data)
  3. [Use the baseline system](#use-the-baseline-system)
  5. [Explanation of settings](#explanation-of-settings)
  
## Setting up the code

To start using the audio captioning DCASE 2020 baseline system, firstly you
have to set-up the code. Please **note bold** that the code in this repository
is tested with Python 3.7.

To set-up the code, you have to do the following: 

  1. Clone this repository.
  2. Use either pip or conda to install dependencies
  
Use the following command to clone this repository at your terminal:

````shell script
$ git clone git@github.com:audio-captioning/dacse-2020-baseline.git
````

The above command will create the directory `dacse-2020-baseline` and populate
it with the contents of this repository. The `dacse-2020-baseline` directory 
will be called root directory for the rest of this README file. 
  
For installing the dependencies, there are two ways. You can either use conda or
pip. 

### Using conda for installing dependencies

To use conda, you can issue the following command at your terminal (when you are 
in the root directory):

````shell script
$ conda create --name audio-captioning-baseline --file requirements_conda.yaml
````

The above command will create a new environment called `audio-captioning-baseline`, which 
will have set-up all the dependencies for the audio captioning DCASE 2020 baseline.  To 
activate the `audio-captioning-baseline` environment, you can issue the following command"

````shell script
$ conda activate audio-captioning-baseline
```` 

Now, you are ready to proceed to the following steps. 

### Using pip for installing dependencies

If you do not use anaconda/conda, you can use the default Python package manager to install
the dependencies of the audio captioning DCASE 2020 baseline. To do so, you have to issue
the following command at the terminal (when you are inside the root directory): 

````shell script
$ pip install -r requirements_pip.txt
````

The above command will install the required packages using pip. Now you are ready to go
to the following steps. 

## Preparing the data

After setting-up the code for the audio captioning DCASE 2020 baseline system, you have to
obtain the Clotho dataset, place it to the proper directory, and do the feature extraction.

### Getting the data from Zenodo

Clotho dataset is freely available online at the Zenodo platform. 
You can find Clotho at
<!--- [![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.3490684.svg)](https://doi.org/10.5281/zenodo.3490684)-->
 
You should download all `.7z` files and the `.csv` files with the captions. That is, you have
do download the following files from Zenodo: 

  1. `clotho_audio_development.7z`
  2. `clotho_audio_evaluation.7z`
  3. `clotho_captions_development.csv`
  4. `clotho_captions_evaluation.csv`
  
After downloading the files, you should place them in the `data` directory, in your root directory.

### Setting up the data

You should create two directories in your `data` directory:

  1. The first is called `clotho_audio_files`, and 
  2. second is called `clotho_csv_files`. 
  
Then, you have to expand the `7z` files. There are many options on how to do this. We do not want
to promote different software and/or packages, so you can just search on Google about how to expand
`7zip` files at your operating system. 

After you expand the `7z` files, you should have two directories created. The first is 
`development` and it will be created by teh `clotho_audio_development.7z` file. The second
is evaluation, and it will be created by the `clotho_audio_evaluation.7z` file. Finally, you should
have the following files and directories at your `root/data` directory: 

  1. `development` directory
  2. `evaluation` directory
  3. `clotho_captions_development.csv` file
  4. `clotho_captions_evaluation.csv` file
  
The `development` directory contains 2163 audio files and the `evaluation` directory 1045 audio
files. You should move the `development` and `evaluation` directories in the `data/clotho_audio_files`
directory, and the `clotho_captions_development.csv` and `clotho_captions_evaluation.csv` files in
the `data/clotho_csv_files` directory. Thus, there should be the following structure in your `data`
directory:


    data/
     | - clotho_audio_files/
     |   | - development/
     |   | - evaluation/
     | - clotho_csv_files/
     |   |- clotho_captions_development.csv
     |   |- clotho_captions_evaluation.csv 
 

Now, you can use the baseline system to extract the features and create the dataset. 


## Use the baseline system

This baseline system implements all the necessary processes in order to use the Clotho data,
optimize a deep neural network (DNN), and predict and evaluate captions. Each process has\
some corresponding settings that can be modified in order to fine tune the baseline system. 

In the following subsection, the default settings will be used.

### Create the dataset

To create the dataset, you can either run the script `processes/dataset.py` using
the command:

````shell script
$ python processes/dataset.py
````

or run the baseline system using the `main.py` script. In any case, the dataset creation will
start. 

The dataset creation is a lengthy process, mainly due to the checking of the data. That is,
the dataset creation has two steps: 

  1. Firstly a split is created (e.g. development or evaluation), and then
  2. the data for the split are validated. 
  
You can select if you want to have the validation of the data by altering the `validate_dataset`
parameter at the `settings/dataset_creation.yaml` file. 

The result of the dataset creation process will be the creation of the directories: 

  1. `data/data_splits`, 
  2. `data/data_splits/development`, 
  3. `data/data_splits/evaluation`, and
  2. `data/pickles`
  
The directories in `data/data_splits` have the input and output examples for the optimization
and assessment of the baseline DNN. The `data/pickles` directory holds the `pickle` files that
have the frequencies of the words and characters (so one can use weights in the objective function)
and the correspondence of words and characters with indices.

### Conduct an experiment

To conduct an experiment using the baseline DNN, you can use the `main.py` script. In case that you
do have previously created the input/output examples (using the above mentioned procedure), then you
can skip the dataset creation by altering the value of the `dataset_creation` in the
`settings/main_settings.yaml` file. To use the `main.py` script, you can issue the command:

````shell script
$ python main.py
```` 

Alternatively, you can use the `process/method.py` by: 

````shell script
$ python processes/method.py
````

The above commands will start the process of optimizing the baseline DNN, using the data
that were created in the [create the dataset](#create-the-dataset) section. 

### Use the pre-trained model

To use the pre-trained model, you have first to obtain the pre-trained weights. These will
be released on Zenodo, on the following days. Stay tuned!

### Evaluate predicted captions

### Modify the hyper-parameters

## Explanation of settings 

There are different settings for the baseline system, associated with the creation of the
dataset, the data, the outputs of the baseline system, and (of course) the optimization of
the baseline DNN. 

All these settings can be found in the `settings` directory. This directory has (by default)
the following files: 

  1. `main_settings.yaml`
  2. `dirs_and_files.yaml`
  3. `dataset_creation.yaml`
  4. `model_baseline.yaml`
  5. `method_baseline.yaml`
  
The parameters in the above `.yaml` files are explained in the following sections.

### Main settings

In the `settings/main_settings.yaml` file, you can find the following settings: 

    workflow:
      dataset_creation: Yes
      dnn_training: yes
      captions_evaluation: yes
    dataset_creation_settings: !include dataset_creation.yaml
    dnn_training_settings: !include method_baseline.yaml
    dirs_and_files: !include dirs_and_files.yaml

The settings at the `workflow` block, correspond to the different processes that the baseline
system can do; the creation of the dataset (`dataset_creation`), the optimization of the DNN
(`dnn_training`), and the evaluation of captions (`captions_evaluation`). By indicating
a `yes` or a `no`, you can switch on (with `yes`) or off (with `no`) the processes. For example,
the 

    workflow:
          dataset_creation: no
          dnn_training: yes
          captions_evaluation: no
          
means that the baseline system will **not** create the dataset and will **not** evaluate captions,
but it will do the optimization of the DNN. 

The rest settings serve to indicate the files that hold the settings for each of the processes.
For example, the `dataset_creation.yaml` file holds the settings for the dataset creation. An
exception is the `dirs_and_files` field, which indicates which file holds the settings for inputs
and outputs of the baseline system. 

### Settings for directories and files

The `settings/dirs_and_files.yaml` file, holds the following settings: 

    root_dirs:
      outputs: 'outputs'
      data: 'data'
    dataset:
      audio_dirs:
        downloaded: 'clotho_audio_files'
        output: 'data_splits'
        development: 'development'
        evaluation: 'evaluation'
      annotations_dir: 'clotho_csv_files'
      pickle_files_dir: 'pickles'
      files:
        np_file_name_template: 'clotho_file_{audio_file_name}_{caption_index}.npy'
        words_list_file_name: 'words_list.p'
        words_counter_file_name: 'words_frequencies.p'
        characters_list_file_name: 'characters_list.p'
        characters_frequencies_file_name: 'characters_frequencies.p'
    model:
      model_dir: 'models'
      checkpoint_model_name: 'dcase_model_baseline.pt'
      pre_trained_model_name: 'dcase_model_pre_trained.pytorch'
    logging:
      logger_dir: 'logging'
      caption_logger_file: 'captions_baseline.txt'
      
These are the necessary settings, to specify the input and output directories for the
baseline system. Specifically, the `root_dirs` has the root directories for the outputs
(`outputs`) and the data (`data`). The root directory of the outputs will be used in order
to output the checkpoints of the baseline DNN and the logging files. The root directory for
the data will be used as the root directory where the data are, e.g. the `data` (at `root/data`)
directory mentioned in section [setting up the data](#setting-up-the-data). 

The `dataset` has the directory and file names used when creating and accessing the dataset
files. The `dataset/audio` has the directory names that the:

  * downloaded audio will be - `downloaded`
  * the dataset files (i.e. the input/output examples) will be - `output`
  * the name of the directory that will hold the development data - `development`
  * the name of the directory that will hold the evaluation data - `evaluation`
  
The `annotations_dir` and `pickle_files_dir` hold the names of the directories where the
csv files will be (`annotations_dir`) and where the `pickle` files will be placed (`pickle_files_dir`).

The `files` entry at the `dataset` block, has:
 
  * the file names of the `pickle` files
    * `words_list_file_name`,
    * `words_counter_file_name`,
    * `characters_list_file_name`, and
    * `characters_frequencies_file_name`) 
  * and the template string of the file name of the input/output examples of the dataset
  (`np_file_name_template`). 

The `model` entry, has:

  * the name of the directory (in the `root_dirs/outputs`) where the checkpoints of the
  baseline DNN will be saved - `model_dir`,
  * the template file name of the checkpoint file - `checkpoint_model_name`, and
  * the file name that the baseline DNN will look as pre-trained model - `pre_trained_model_name`.
  
Finally, the `logging` entry has: 

  * the directory where the logging files will be placed - `logger_dir`, 
  * and the base file name of the logging - `caption_logger_file`. 
