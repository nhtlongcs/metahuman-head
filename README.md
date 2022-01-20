# metahuman-head
Meta Human Head Creation 

## Face reconstruction

### Requirement

- Linux
- python >= 3.6
- tensorflow 1.15
- g++

### Installation

```bash
cd hifi3dface
bash install.sh
```

The script in **install.sh** installs all packages required to run our code, and compiles the c++ kernel of the differentiable renderer provided by [tf_mesh_renderer](https://github.com/google/tf_mesh_renderer).
Pay attention to finding the correct path to **TF_INC** and **TF_LIB**. The path specified in **install.sh** might not suit your case. If it does not work, please try to fix them manually. If your g++ version is greater than 4, please delete "-D_GLIBCXX_USE_CXX11_ABI=0" ( Line 9 ) in **install.sh**.
You can also compile the code using the method provided by [**tf_mesh_renderer**](https://github.com/google/tf_mesh_renderer).

### Download

You will need to download the following files to run the code:

- Download **3DMM** (5.4GB) and unzip it to the **./3DMM** folder: [Google Drive](https://drive.google.com/file/d/1vSb2EpduRJuIEUOc_aRsbjASSOUAk7tG/view?usp=sharing)
- Download the **resources.zip** (2GB) into the repo root directory **./** and unzip it: [Google Drive](https://drive.google.com/file/d/1FoBbj1qWdf2LyJkxLYnMJ4h4woTQ6QL_/view?usp=sharing)
- Download the **test_data.zip** (80MB) into the repo root directory **./** and unzip it: [Google Drive](https://drive.google.com/file/d/1f5imMCO0ug_ozj0cDcNYtizom5lBu2PH/view?usp=sharing)

**1.1 Data acquisition:**

You may use [RGBD data capture code](https://github.com/lxk121lalala/RGBD_data_capture) (need an iPhone with TrueDepth camera) to capture RGB-D selfies. The color and depth images are in JPG and PNG formats respectively. The resolution of the color image is 1504 x 1128, and the depth image is of size 640 x 480. If you want to use your own capturing system, please modify the code to suit your case.

**1.2 Configuration:**

Modify **run_rgbd.sh** as following:

1. Line 6: define the path to **3DMM** as where you download the 3DMM model. 
    ```bash
    basic_path=$(pwd)/3DMM/files/;
    ```
2. Line 7: define the path to **resources** as where you download the data. 
    ```bash
    resources_path=$(pwd)/resources/;
    ```
3. Line 24: define the path to **test data**. If you capture your own data, please specify the path to the video sequence or 4 pairs of (color, depth) images with different poses (mid-left-right-up).
    ```bash
    ROOT_DIR=$(pwd)/test_data/RGBD/test1/;
    ```
4. Line 23: define whether testing data is a video sequence. 
    ```bash
    is_only_four_frame="False" #  True for four frames, False for a video sequence
    ```
5. Line 121-136: configure optimization parameters.
    ```bash
    log_step=10
    learning_rate=0.05
    lr_decay_step=10
    lr_decay_rate=0.9
    photo_weight=100
    gray_photo_weight=80
    reg_shape_weight=0.4
    reg_tex_weight=0.0001
    depth_weight=1000
    id_weight=1.8
    real_86pt_lmk3d_weight=0.01
    lmk_struct_weight=0
    train_step=100
    is_fixed_pose="False"
    is_add_head_mirrow="False" #  True: output symmetrical head completion result
    is_add_head_male="True" #  True: complete head with a male template
    ```

**1.3 Run:**

Please run the follow command to generate the results.
```bash
bash run_opt_rgbd.sh
```

The RGBD data will be used for shape optimization and HD texture/normal maps generation. We highly recommend that you use our 3DMM version **AI-NExT-Shape.mat** in order to achieve the same results as reported in our paper. You can also run our code with **BFM**, but the texture map and normal map will not be generated if you use BFM, as BFM does not provide UV maps.

**1.4 Results:**

The produced results will be saved in the **results** floder in data path, including: 

    - head.obj: the final mesh file
    - albedo.png: the final albedo map file
    - normal.png: the final normal map file
    - test.mtl: a material description file for simple rendering in meshlab