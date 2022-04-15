<a href="https://explosion.ai"><img src="https://explosion.ai/assets/img/logo.svg" width="125" height="125" align="right" /></a>

# spacy-loggers: Logging utilities for spaCy

[![PyPi Version](https://img.shields.io/pypi/v/spacy-loggers.svg?style=flat-square&logo=pypi&logoColor=white)](https://pypi.python.org/pypi/spacy-loggers)

Starting with spaCy v3.2, alternate loggers are moved into a separate package
so that they can be added and updated independently from the core spaCy
library.

`spacy-loggers` currently provides loggers for:

- [Aim](https://aimstack.io/)
- [Weights & Biases](https://www.wandb.com)

If you'd like to add a new logger or logging option, please submit a PR to this
repo!

## Setup and installation

`spacy-loggers` should be installed automatically with spaCy v3.2+, so you
usually don't need to install it separately. You can install it with `pip` or
from the conda channel `conda-forge`:

```bash
pip install spacy-loggers
```

```bash
conda install -c conda-forge spacy-loggers
```

# Loggers

## AimLogger

In order to use aim for experimentation tracking, you must install it onto your system.

```
pip install aim
```

### Usage

`spacy.AimLogger.v1` allows the user to leverage [`Aim`](https://aimstack.io/) as the experiment tracker throughout the model development cycles. All of the training metrics will be tracked by the `Aim` through the training steps and can be easily accessed through Aim UI by simply running 

```
aim up
```

It is important to note that you can observe the changes in the metrics live throughout the training. Aim also supports tracking multiple experiments simultaneously. Aim will store all of the [training config]((https://spacy.io/usage/training#config) hyperparameters that are used during experimentation along with [system-related information](https://aimstack.readthedocs.io/en/latest/ui/pages/run_management.html#id7) ranging from GPU/CPU usability to Disk IO. This comes in with an added benefit that you can search/filter across the experimentation rather granularly, using our pythonic search [AimQL](https://aimstack.readthedocs.io/en/latest/using/search.html?highlight=AimQL#searching), live from the UI. To access the Aim Logger one can simply add a config akin to this


### Example config

```ini
[training.logger]
@loggers = "spacy.AimLogger.v1"
repo = "path/to/save/logs"
experiment_name = "your_experiment_name"
```


The complete overview of Aim Logger inputs looks like this.

| Name                   | Type | Description|
| ---------------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `repo`         | `str`           | The path for saving the logs        |
| `experiment_name` | `str`     | The name of the experiment to track(default: `None`, the experiment will be determined by hash).                                                                                                                                       |
| `run_hash`   | `str` | The hash of the run that you would like to resume. For a standalone run, this option is not needed (default: `None`).               |



## WandbLogger

### Installation

This logger requires `wandb` to be installed and configured:

```bash
pip install wandb
wandb login
```

### Usage

`spacy.WandbLogger.v4` is a logger that sends the results of each training step
to the dashboard of the [Weights & Biases](https://www.wandb.com/) tool. To use
this logger, Weights & Biases should be installed, and you should be logged in.
The logger will send the full config file to W&B, as well as various system
information such as memory utilization, network traffic, disk IO, GPU
statistics, etc. This will also include information such as your hostname and
operating system, as well as the location of your Python executable.

**Note** that by default, the full (interpolated)
[training config](https://spacy.io/usage/training#config) is sent over to the
W&B dashboard. If you prefer to **exclude certain information** such as path
names, you can list those fields in "dot notation" in the
`remove_config_values` parameter. These fields will then be removed from the
config before uploading, but will otherwise remain in the config file stored
on your local system.

### Example config

```ini
[training.logger]
@loggers = "spacy.WandbLogger.v4"
project_name = "monitor_spacy_training"
remove_config_values = ["paths.train", "paths.dev", "corpora.train.path", "corpora.dev.path"]
log_dataset_dir = "corpus"
model_log_interval = 1000
```

| Name                   | Type            | Description                                                                                                                                                                                                                     |
| ---------------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `project_name`         | `str`           | The name of the project in the Weights & Biases interface. The project will be created automatically if it doesn't exist yet.                                                                                                   |
| `remove_config_values` | `List[str]`     | A list of values to exclude from the config before it is uploaded to W&B (default: `[]`).                                                                                                                                       |
| `model_log_interval`   | `Optional[int]` | Steps to wait between logging model checkpoints to the W&B dasboard (default: `None`). Added in `spacy.WandbLogger.v2`.                                                                                                         |
| `log_dataset_dir`      | `Optional[str]` | Directory containing the dataset to be logged and versioned as a W&B artifact (default: `None`). Added in `spacy.WandbLogger.v2`.                                                                                               |
| `run_name`             | `Optional[str]` | The name of the run. If you don't specify a run name, the name will be created by the `wandb` library (default: `None`). Added in `spacy.WandbLogger.v3`.                                                                       |
| `entity`               | `Optional[str]` | An entity is a username or team name where you're sending runs. If you don't specify an entity, the run will be sent to your default entity, which is usually your username (default: `None`). Added in `spacy.WandbLogger.v3`. |
| `log_best_dir`      | `Optional[str]` | Directory containing the best trained model as saved by spaCy (by default in `training/model-best`), to be logged and versioned as a W&B artifact (default: `None`). Added in `spacy.WandbLogger.v4`.                                                                                               |
| `log_latest_dir`      | `Optional[str]` | Directory containing the latest trained model as saved by spaCy (by default in `training/model-latest`), to be logged and versioned as a W&B artifact (default: `None`). Added in `spacy.WandbLogger.v4`.                                                                                               |
