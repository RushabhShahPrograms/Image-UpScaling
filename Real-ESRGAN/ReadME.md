# How to installed on my system:
I tested on:
- Ubuntu 24.04 LTS
- 16GB RAM
- RTX 3040 GPU
- Ryzen 7 5800H
```python
git clone https://github.com/xinntao/Real-ESRGAN.git
cd Real-ESRGAN

#Download PreTrained Models
wget https://github.com/xinntao/Real-ESRGAN/releases/download/v0.2.2.4/RealESRGAN_x4plus_anime_6B.pth -P weights
wget https://github.com/xinntao/Real-ESRGAN/releases/download/v0.1.0/RealESRGAN_x4plus.pth -P weights
wget https://github.com/xinntao/Real-ESRGAN/releases/download/v0.2.5.0/realesr-animevideov3.pth -P weights

#Create and Activate Conda Environment with python==3.11
conda create -n realesrgan-env python==3.11
conda activate realesrgan-env

#Installation
pip install -r requirements.txt
python setup.py develop
```
By default as we are using new pytorch version so it will give one error which is related to this `ModuleNotFoundError: No module named 'torchvision.transforms.functional_tensor'`

To overcome this we can follow this step:
```
# First, let's locate the degradations.py file
file_path=$(find ~/anaconda3/envs/esrgan-env -name "degradations.py")

# Then use sed to replace the import statement
sed -i 's/from torchvision.transforms.functional_tensor import rgb_to_grayscale/from torchvision.transforms.functional import rgb_to_grayscale/' "$file_path"

# To verify the change was made correctly, you can check the file:
grep "rgb_to_grayscale" "$file_path"

# If you don't want to use the find command, you can directly specify the path if you know it:
sed -i 's/from torchvision.transforms.functional_tensor import rgb_to_grayscale/from torchvision.transforms.functional import rgb_to_grayscale/' "/home/lenovo-laptop/anaconda3/envs/esrgan-env/lib/python3.11/site-packages/basicsr/data/degradations.py"
```
Or Else install the old pytorch version:
```
conda activate esrgan-env
conda uninstall pytorch torchvision
conda install pytorch==1.7.1 torchvision==0.8.2 cudatoolkit=11.0 -c pytorch
```

### Inference
```
python inference_realesrgan.py -n RealESRGAN_x4plus -i inputs
```

![Screenshot from 2024-11-05 12-08-31](https://github.com/user-attachments/assets/62f26901-bef2-401f-a058-e884fa73dd1c)

### For anime
```
python inference_realesrgan.py -n RealESRGAN_x4plus_anime_6B -i inputs
```

![Screenshot from 2024-11-05 12-04-31](https://github.com/user-attachments/assets/a54e474d-e486-4942-8098-6299bee47355)

### Inference on Video
```
python inference_realesrgan_video.py -n realesr-animevideov3 -i inputs/video/onepiece_demo.mp4 -o results/video/onepiece_enhanced.mp4
```

# Run directly using executable file

### Portable executable files (NCNN)

You can download [Windows](https://github.com/xinntao/Real-ESRGAN/releases/download/v0.2.5.0/realesrgan-ncnn-vulkan-20220424-windows.zip) / [Linux](https://github.com/xinntao/Real-ESRGAN/releases/download/v0.2.5.0/realesrgan-ncnn-vulkan-20220424-ubuntu.zip) / [MacOS](https://github.com/xinntao/Real-ESRGAN/releases/download/v0.2.5.0/realesrgan-ncnn-vulkan-20220424-macos.zip) **executable files for Intel/AMD/Nvidia GPU**.

This executable file is **portable** and includes all the binaries and models required. No CUDA or PyTorch environment is needed.<br>

You can simply run the following command (the Windows example, more information is in the README.md of each executable files):

```bash
./realesrgan-ncnn-vulkan.exe -i input.jpg -o output.png -n model_name
```

We have provided five models:

1. realesrgan-x4plus  (default)
2. realesrnet-x4plus
3. realesrgan-x4plus-anime (optimized for anime images, small model size)
4. realesr-animevideov3 (animation video)

You can use the `-n` argument for other models, for example, `./realesrgan-ncnn-vulkan.exe -i input.jpg -o output.png -n realesrnet-x4plus`

#### Usage of portable executable files

1. Please refer to [Real-ESRGAN-ncnn-vulkan](https://github.com/xinntao/Real-ESRGAN-ncnn-vulkan#computer-usages) for more details.
1. Note that it does not support all the functions (such as `outscale`) as the python script `inference_realesrgan.py`.

```console
Usage: realesrgan-ncnn-vulkan.exe -i infile -o outfile [options]...

  -h                   show this help
  -i input-path        input image path (jpg/png/webp) or directory
  -o output-path       output image path (jpg/png/webp) or directory
  -s scale             upscale ratio (can be 2, 3, 4. default=4)
  -t tile-size         tile size (>=32/0=auto, default=0) can be 0,0,0 for multi-gpu
  -m model-path        folder path to the pre-trained models. default=models
  -n model-name        model name (default=realesr-animevideov3, can be realesr-animevideov3 | realesrgan-x4plus | realesrgan-x4plus-anime | realesrnet-x4plus)
  -g gpu-id            gpu device to use (default=auto) can be 0,1,2 for multi-gpu
  -j load:proc:save    thread count for load/proc/save (default=1:2:2) can be 1:2,2,2:2 for multi-gpu
  -x                   enable tta mode"
  -f format            output image format (jpg/png/webp, default=ext/png)
  -v                   verbose output
```

Note that it may introduce block inconsistency (and also generate slightly different results from the PyTorch implementation), because this executable file first crops the input image into several tiles, and then processes them separately, finally stitches together.


# Reference
For more info refer [original-readme.md](https://github.com/RushabhShahPrograms/Image-UpScaling/blob/master/Real-ESRGAN/Orginial-README.md) file

