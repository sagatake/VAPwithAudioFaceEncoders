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

You can download pretrained models from [here](https://drive.google.com/drive/folders/1fX7USNGHYPzWhb9xzgylvshNwFW20-eZ?usp=sharing) and put it into `pretrained_models` directory

## Training

You need to download the NoXi dataset (at least the Paris subset is required).
- mainly from [here](https://multimediate-challenge.org/datasets/Dataset_NoXi/).
- for preprocessed nonverbal features (AU, gaze, head pose, body joint), from [here](https://github.com/ahclab/turntaking)

Place the data in the following structure.
```
noxi_orig
- Paris_01
--- audio_expert.wav
--- audio_mix.wav
--- audio_novice.wav
--- non_verbal_expert.csv
--- non_verbal_novice.csv
--- vad_expert.txt
--- vad_novice.txt
```

Extract cropped face image sequences as follows.
```
conda activate py311_rvap
dadada
```

Run the following command to prepare for the training.
```
conda activate py311_rvap
python noxi_00_modify_format.py
python noxi_01_filter_csv_by_keyword.py
python noxi_02_add_face_image.py
```

Start the training.
```
conda activate py311_rvap
python finetune.py \
--data_train_path noxi/train_paris_ext.csv --data_val_path noxi/valid_paris_ext.csv --data_test_path noxi/test_paris_ext.csv \
--data_use_cache --data_exclude_av_cache --data_preload_av --data_cache_dir tmp_cache4 \
--data_global_batch_size 256 \
--data_batch_size 4 \
--vap_encoder_type cpc \
--vap_pretrained_cpc asset/cpc/60k_epoch4-d0f474de.pt \
--vap_pretrained_vap asset/vap/vap_state_dict_eng_20hz_2500msec.pt \
--vap_freeze_encoder 1 --vap_channel_layers 1 \
--vap_cross_layers 3 --vap_context_limit -1 --vap_context_limit_cpc_sec -1 --vap_frame_hz 25 \
--vap_multimodal \
--vap_use_face_encoder --vap_pretrained_face_encoder asset/FormerDFER/DFER_encoder_weight_only.pt \
--vap_mode 1 \
--event_frame_hz 25 \
--opt_early_stopping 1 --opt_patience 5 \
--opt_save_top_k 5 --opt_max_epochs 200 \
--opt_saved_dir trained_data_mode1_paris_256/ --opt_log_dir lightning_logs_mode1_paris_256 \
--devices 0,1 --seed 0
```

Depending on your GPU VRAM size, you might need to decrease the `data_batch_size` (we used two 12 GB VRAM GPUs for this setup). 

You can change `devices` if you use one GPU setting.

## Evaluation

You can evaluate the pretrained model with the following command.
```
python evaluation.py \
--data_train_path noxi/train_paris_ext.csv --data_val_path noxi/valid_paris_ext.csv --data_test_path noxi/test_paris_ext.csv \
--data_use_cache --data_exclude_av_cache --data_preload_av --data_cache_dir tmp_cache4 \
--vap_freeze_encoder 1 --vap_channel_layers 1 --vap_cross_layers 3 --vap_context_limit -1 --vap_context_limit_cpc_sec -1 --vap_frame_hz 25 \
--event_frame_hz 25 \
--devices 0 --seed 0 \
--vap_multimodal --vap_use_face_encoder --vap_pretrained_face_encoder asset/FormerDFER/DFER_encoder_weight_only.pt --vap_mode 1 --vap_load_pretrained 0 \
--vap_multimodal --vap_use_face_encoder --vap_mode 1 \
--checkpoint trained_data_mode1_paris_256
```

You can change the evaluation target model by specifying the target directory with the `checkpoint` option.

## Citation
TBA
