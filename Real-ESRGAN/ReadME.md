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

# Inference
```
python inference_realesrgan.py -n RealESRGAN_x4plus -i inputs
```

![Screenshot from 2024-11-05 12-08-31](https://github.com/user-attachments/assets/62f26901-bef2-401f-a058-e884fa73dd1c)

# For anime
```
python inference_realesrgan.py -n RealESRGAN_x4plus_anime_6B -i inputs
```

![Screenshot from 2024-11-05 12-04-31](https://github.com/user-attachments/assets/a54e474d-e486-4942-8098-6299bee47355)

# Inference on Video
```
python inference_realesrgan_video.py -n realesr-animevideov3 -i inputs/video/onepiece_demo.mp4 -o results/video/onepiece_enhanced.mp4
```


# Reference
For more info refer [original-readme.md](https://github.com/RushabhShahPrograms/Image-UpScaling/blob/master/Real-ESRGAN/Orginial-README.md) file

