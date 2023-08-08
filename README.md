# SRDTrans: Spatial redundancy transformer for self-supervised fluorescence image denoising

<p align="center">
  <img src="assets/SRDTrans.gif" width='600'>
</p> 

<div align="center">

✨ [**Method**](#-Method) **|** 🚩 [**Paper**](#-Paper) **|** 🔧 [**Install**](#-Install)  **|** 🎨 [**Datasets**](#-Datasets)  **|** 💻 [**Training**](#-Training) **|** 🏰 [**Model Zoo**](#-Model-Zoo)**|** ⚡ [**Inference**](#-Inference)**|** &#x1F308; [**Results**](#-Results)

</div>

---


## ✨ Method overview

<p align="center">
<img src="assets/self_super.png" width='800'>
</p>

We provide a spatial redundancy denoising transformer (SRDTrans) to remove noise from fluorescence images in a self-supervised manner. **First**, a sampling strategy based on spatial redundancy is proposed to extract adjacent orthogonal training pairs, which eliminates the dependence on high imaging speed. SRDTrans is complementary to our previously proposed [DeepCAD](https://www.nature.com/articles/s41592-021-01225-0) and [DeepCAD-RT](https://www.nature.com/articles/s41587-022-01450-8). **Second**, to break the performance bottleneck of convolutional neural networks (CNNs), we designed a lightweight spatiotemporal transformer architecture to capture long-range dependencies and high-resolution features at a low computational cost. SRDTrans can overcome the inherent spectral bias of CNNs and restore high-frequency information without producing over-smoothed structures and distorted fluorescence traces. **Finally**, we demonstrate the state-of-the-art denoising performance of SRDTrans on single-molecule localization microscopy (SMLM) and two-photon volumetric calcium imaging. SRDTrans does not contain any assumptions about the imaging process and the sample, thus can be easily extended to a wide range of imaging modalities and biological applications.


## 🚩 Paper

This repository is for SRDTrans introduced in the following paper:

[Xinyang Li, Xiaowan Hu, Xingye Chen, et al. "Spatial redundancy transformer for self-supervised fluorescence image denoising." bioRxiv (2023)](https://www.biorxiv.org/content/10.1101/2023.06.01.543361v1) 


## 🔧 Install

### Dependencies 
  - Python >= 3.6 
  - PyTorch >= 1.7 
    
### Installation

1. Clone the repository and delete placeholder files.

    ```bash
    git clone https://github.com/cabooster/SRDTrans.git
    cd SRDTrans
    rm datasets/noisy/__init__.py pth/__init__.py results/__init__.py
    ```

2. Create a virtual environment and install PyTorch and other dependencies. **In the 3rd step**, please select the correct Pytorch version that matches your CUDA version from [https://pytorch.org/get-started/previous-versions/](https://pytorch.org/get-started/previous-versions/). 

    ```bash
    $ conda create -n srdtrans python=3.6
    $ conda activate srdtrans
    $ pip install torch==1.8.0+cu111 torchvision==0.9.0+cu111 torchaudio==0.8.0 -f https://download.pytorch.org/whl/torch_stable.html
    $ pip install tifffile einops timm tqdm scikit-image
    ```


## 💻 Training 

### 1. Prepare the data  

You can use your own data or download one of the demo data (.tif file), and put the low-SNR data into datasets/noisy/.

### 🎨 Datasets

| Data&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Pixel&nbsp;size&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Frame rate&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Size&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   |Download |         Description                       |
| ---------------------------------------------- |:--------- | :---- | :---- | :---- | :------------------------------------------- |
|Calcium imaging |1.02 μm|30 Hz| 29.2 G   |  [Zenodo](https://doi.org/10.5281/zenodo.7812603)     |   Simulated calcium imaging data under different SNR      |
|Calcium imaging| 1.02 μm|0.1 Hz, 0.3Hz, 1 Hz, 3 Hz, 10 Hz, and 30 Hz|5.8 G   |  [Zenodo](https://doi.org/10.5281/zenodo.7812545)     |    SRDTrans dataset: simulated calcium imaging data at different imaging speeds|
|SMLM                    |30 nm |200 Hz|  48.0 G     |    [Zenodo](https://doi.org/10.5281/zenodo.7812590)    |    SRDTrans dataset: simulated SMLM data under different SNR|
|SMLM                    | 43 nm|200 Hz|  23.6 G     |    [Zenodo](https://doi.org/10.5281/zenodo.7813185)    |      SRDTrans dataset: experimental imaging SMLM data|


### 2. Start training

  ```bash
    # Simulated SMLM & Simulated Calcium imaging data at 30hz
    python -u train.py --datasets_folder noisy --n_epochs 20 --GPU 0,1 --train_datasets_size 6000  --patch_x 128 --patch_t 128
    
    # Key parameters:
    --datasets_folder: the folder containing your training data (one or more *.tif stacks)
    --n_epochs: the number of training epochs
    --GPU: specify the GPU(s) used for training. (e.g., '0', '0,1', '0,1,2')
    --train_datasets_size: how many patches will be extracted for training
    --patch_x, --patch_t: patch size in three dimensions (xy and t)
  ```

In the vast majority of cases, good denosing models can be trained with these default parameters. **If not necessary, you do not need to modify these parameters**. You just need to change `--datasets_folder` and `--GPU`. 

The folders containing `datasets` and `models` are in `./datasets` and `./pth`, respectively.


## 🏰 Model Zoo
| Models                            | Modality  |Download                                  |
| --------------------------------- |:--------- | :------------------------------------------- |
| SRDTrans                 | Two-photon calcium imaging  |  [Zenodo](https://doi.org/10.5281/zenodo.7818031)                                              |
| SRDTrans                 | Single-molecule localization microscopy (SMLM)     |    [Zenodo](https://doi.org/10.5281/zenodo.7817710)   

## ⚡ Inference
### 1. Prepare models

    Before inference, you should have trained your own model or downloaded our pre-trained model.

### 2. Test models

  ```bash
    # Simulated calcium imaging data sampled at 0.3 Hz
    python test.py --datasets_folder noisy --denoise_model cad_03hz --GPU 0,1 --patch_x 128 --patch_t 128
  ```

---
## &#x1F308; Results

  ### 1. SMLM denoising
  <p align="center">
  <img src="assets/storm_vis.png" width='800'>
  </p>

  ### 2. Localization and reconstruction of SMLM
  <p align="center">
  <img src="assets/storm_rec.png" width='800'>
  </p>

  ### 3. Calcium imaging denoising at 30 Hz
  <p align="center">
  <img src="assets/cad_30hz.png" width='800'>
  </p>

  ### 4. Calcium imaging denoising at 0.3 Hz
  <p align="center">
  <img src="assets/cad_0.3hz.png" width='800'>
  </p>

  ### 4. Videos
  You can click on the pictures to play the videos.
  1. SRDTrans enhances large-scale volumetric calcium imaging in the mouse cortex.

  [![IMAGE ALT TEXT](assets/v1_png.png)](https://youtu.be/0IUB8IRyIAM "Please click")

  2. The denoising performance of different methods on calcium imaging data sampled at 0.3 Hz.

  [![IMAGE ALT TEXT](assets/v2_png.png)](https://youtu.be/4p7y57EMY14 "Please click")
  3. Comparing the performance of DeepCAD and SRDTrans on fast-moving objects. 

  [![IMAGE ALT TEXT](assets/v3_png.png)](https://youtu.be/UpTXmd35Xxs "Please click")

[def]: #-demos-videos
