# iSeparate
This repository consists of an attempt to reimplement, 
reproduce and unify 
the various deep learning based methods for Music 
Source Separation. 

This project was started as part of the requirement for 
the course [Media Computing in Practice](https://media-comp.github.io/2022/) at the University of Tokyo, under the guidance
of [Yusuke Matsui](https://yusukematsui.me/) sensei.

## Currently implemented methods:
- D3Net
  - Densely connected multidilated convolutional networks for dense prediction tasks 
  - CVPR 2021, Takahashi et al., Sony
    - paper: [link](https://openaccess.thecvf.com/content/CVPR2021/papers/Takahashi_Densely_Connected_Multi-Dilated_Convolutional_Networks_for_Dense_Prediction_Tasks_CVPR_2021_paper.pdf)
    - official code: [link](https://github.com/sony/ai-research-code/tree/master/d3net/music-source-separation)
- Demucs (v2)
  - Music Source Separation in the Waveform Domain 
  - Arxiv 2021, Defossez et al., Facebook, INRIA
    - paper: [link](https://hal.archives-ouvertes.fr/hal-02379796/document)
    - official code: [link](https://github.com/facebookresearch/demucs/tree/v2)

## Getting Started
### For Linux users:
Install the [libsndfile](http://www.mega-nerd.com/libsndfile/) and 
[soundstretch](https://www.surina.net/soundtouch/soundstretch.html) libraries using your packagemanager, for example:

  ```shell
    sudo apt-get install libsndfile1 soundstretch
  ```

If you use anaconda or miniconda, you can quickly create an environment using the provided environment yaml files.

For GPU machines:

```shell
conda env create --name <envname> --file=environment-cuda.yml
```

For CPU only machines:

```shell
conda env create --name <envname> --file=environment-cpu.yml
```

After creating the environment you can activate it as below:

```shell
conda activate <envname>
```

### For Windows and Mac users:
  To do

## Separate using pre-trained model
  To do

## Dataset Preparation and Training
If you would like to train the models yourself, please follow the following procedure
### Dataset Preparation
iSeparate currently supports the [MUSDB18](https://zenodo.org/record/1117372#.Ymcqr9rP1PY) dataset.
This dataset is in the [Native Instruments STEMS](https://www.native-instruments.com/en/specials/stems/) format.
However, it is easier to deal with decded wav files. To do that you can run the `prepare_dataset.py` file.

If you would like to download a small 7s version of the dataset for testing the code, run

```shell
python prepare_dataset.py \
                        --root data/MUSDB18-sample \
                        --wav-root data/MUSDB18-sample-wav \
                        --filelists-dir filelists/musdb-sample \
                        --download-sample \
                        --keep-wav-only \
                        --make-symlink
```

If you would like to download the full dataset for training, run

```shell
python prepare_dataset.py \
                        --root data/MUSDB18 \
                        --wav-root data/MUSDB18-wav \
                        --filelists-dir filelists/musdb \
                        --keep-wav-only \
                        --make-symlink
```

The `prepare_dataset.py` downloads the data in STEMS format to the directory specified by `--root` and then extracts the 
wav files into the directory specified by `--wav-root`. If you want to delete the STEMS and keep only the wav files,
you can use the `--keep-wav-only` option. The `--make-symlink` option will create a symbolic link from the wav directory to the `data/MUSDB18-wav`
directory. If you wanted you could also edit the config files in `configs` directory to point to the dataset directory.

### Training
Nvidia GPU's are required for training. These models require quite a lot of VRAM, you can change the `batch_size`
parameter in the configs to suit your needs.

To train on a single GPU:

```shell
python train.py --config-file configs/<method>/<config-name.yaml>
```

To train on multiple GPU with DistributedDataParallel

```shell
python -m torch.distributed.run \
               --nproc_per_node=4 train.py \
               --config-file configs/<method>/<config-name.yaml>
```

