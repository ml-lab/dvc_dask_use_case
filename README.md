# DVC and Dask use case

This repository contains the code for setting up [DVC](https://dvc.org/) to use a remote computer server using [dask](https://docs.dask.org/en/latest/).

## How to set up the use case

The use case have the following prerequisites:

1. A remote server with:
    1. SSH installed.
    1. A unix user you have the username and password for.
    1. A folder for your remote shared DVC cache, my is at `/scratch/dvc_data_cache/`.
    1. A folder for your remote DVC data directories, my is at `/scratch/dvc_users/[REMOTE_USERNAME]/`.
    1. Dask scheduler installed and open at port 8786.
1. A local SSH keyfile (`ssh-keygen`), which have been copied to the remote server: `ssh-copy-id [REMOTE_USERNAME]@[REMOTE_IP]`.
1. An open SSH port-forward to the dask scheduler from our local to the remote machine: `ssh -L 8786:[REMOTE_USERNAME]@[REMOTE_IP]:8786 [REMOTE_USERNAME]@[REMOTE_IP]`.
1. Set up local DVC development repository (following https://dvc.org/doc/user-guide/contributing/) with a conda environment:
    1. Fork https://github.com/iterative/dvc on Github.
    1. `git clone git@github.com:<GITHUB_USERNAME>/dvc.git`
    1. `cd dvc`
    1. `conda create -n py36_open_source_dvc python=3.6`
    1. `conda activate py36_open_source_dvc`
    1. `pip install -r requirements.txt`
    1. `pip install -r tests/requirements.txt`
    1. `pip install -e .`
    1. `pip install pre-commit`
    1. `pre-commit install`
    1. `conda install -n py36_open_source_dvc dask`
    1. `which dvc` should say `[HOME]/anaconda3/envs/py36_open_source_dvc/bin/dvc` and `dvc --version` should say the exact version available in you local DVC development repository.
1. Configure you local DVC globally for you local machine, note that my I call my remote server "ahsoka":
    1. `conda activate py36_open_source_dvc`
    1. `dvc config core.analytics false --global`
    1. `dvc remote add ahsoka ssh://[REMOTE_IP]/scratch/dvc_users/[REMOTE_USERNAME]/ --global`
    1. `dvc remote modify ahsoka user [REMOTE_USERNAME] --global`
    1. `dvc remote modify ahsoka port 22 --global`
    1. `dvc remote modify ahsoka keyfile [PATH_TO_YOUR_PUBLIC_SSH_KEY] --global`
    1. `dvc remote add ahsoka_cache ssh://[REMOTE_IP]/scratch/dvc_data_cache --global`
    1. `dvc remote modify ahsoka_cache user [REMOTE_USERNAME] --global`
    1. `dvc remote modify ahsoka_cache port 22 --global`
    1. `dvc remote modify ahsoka_cache keyfile [PATH_TO_YOUR_PUBLIC_SSH_KEY] --global`
    1. `dvc config cache.ssh ahsoka_cache --global`

This use case of DVC and Dask has been set up as follow:

1. On the remote server do the following, to create the remote DVC data directory for this project (i.e. this use case):
    1. `cd scratch/dvc_users/[REMOTE_USERNAME]`
    1. `mkdir dvc_dask_use_case`
    1. `mkdir data`
    1. `wget -P data/ https://s3-us-west-2.amazonaws.com/dvc-share/so/100K/Posts.xml.tgz`
    1. `tar zxf data/Posts.xml.tgz -C data/`
1. Clone this test repository from my Github:
    `git clone git@github.com:PeterFogh/dvc_dask_use_case.git`