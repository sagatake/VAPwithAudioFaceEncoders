# VAPwithAudioFaceEncoders

This repository is the official implementation of the voice activity projection (VAP) model, which is enhanced by audio and face encoders.

We extended the code from [Inoue's real-time VAP repository](https://github.com/inokoj/VAP-Realtime).

## Prerequisite

Please prepare the following environment beforehand
- Ubuntu 20.04
- Conda

## Installation

1. `conda env create py311_rvap.yml`
2. `pip install -r requirements.txt`
3. `pip install -r requirements_cu118 --index-url https://download.pytorch.org/whl/cu118`
4. copy files in `asset` directory from [Inoue's real-time VAP repository](https://github.com/inokoj/VAP-Realtime) into `asset` directory this repository

## Pretrained models

Please download pretrained models from [here](https://drive.google.com/drive/folders/1fX7USNGHYPzWhb9xzgylvshNwFW20-eZ?usp=sharing) and put it into `pretrained_models` directory

## Training

## Inference

## Citation
TBA
