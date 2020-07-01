# RoutedFusion: Learning Real-time Depth Map Fusion

This is the official and improved implementation of the CVPR 2020 submission "RoutedFusion: Real-time Depth Map Fusion". 

RoutedFusion is a real-time capable depth map fusion method that leverages machine learning for fusing noisy and outlier-contaminated depth maps. It consists of two neural networks components: 1) the depth routing network that performs a 2D prepocessing of the depth maps estimating a de-noised depth map as well as corresponding confidence map. 2) a depth fusion network that predicts optimal updates to the scene representation given a canonical view of the current state of the scene representation as well as the new measurement and confidence map.

## Usage

Below you find instructions on how to use RoutedFusion as a standalone depth map fusion pipeline for training and evaluation.

### Prerequisites
For using RoutedFusion, you need to have the following installed on you machine:

1. docker (https://www.docker.com/)
2. nvidia-docker (https://github.com/NVIDIA/nvidia-docker)

### Data Preparation
The models are trained on the ShapeNet v1 dataset. Therefore, the data needs to be downloaded and perpared using mesh-fusion.

**Prerequisites**
1. Download ShapeNet v1 from www.shapenet.org and unzip files
2. Download binvox from https://www.patrickmin.com/binvox/, adjust permissions and move it to /usr/bin/

**Install Data Generation**

Run the installation script *scripts/install_data_generation.sh*

**Generate Shapenet Data**

Run the data generation script *scripts/generate_shapenet_data.sh*
<pre><code>bash scripts/generate_shapenet_data.sh $PATH_TO_SHAPENET $PATH_TO_GENERATE_TO
</code></pre>

### Installation

There are two possible ways of installing RoutedFusion. The recommended way is to use Docker. Alternatively, you can also use a conda environment.

**Clone the repo**

<pre><code>git clone https://github.com/weders/RoutedFusion.git
</code></pre>

**Build the docker image**
<pre><code>docker build . -t routed-fusion
</code></pre>

**Start and enter the container from the image**
<pre><code>docker run -v $PATH_TO_YOUR_PREPROCESSED_DATA:/data -v $PATH_TO_SAVE_EXPERIMENTS:/experiments --gpus all  -it routed-fusion:latest
</code></pre>

**Alternatively, create the Anaconda environment**
<pre><code>conda create env -f environment.yml
</code></pre>

### Training
Once you are in the docker container you can train RoutedFusion. First, you can train the routing network. Secondly, you can train the fusion network.

**Train Routing Network**
<pre><code>python train_routing.py --config configs/routing/shapenet.noise.005.yaml
</code></pre>

**Train Fusion Network**

***without routing***
<pre><code>python train_fusion.py --config configs/fusion/shapenet.noise.005.yaml
</code></pre>

***with routing***
<pre><code>python train_fusion.py --config configs/fusion/shapenet.noise.005.yaml --routing-model $PATH_TO_YOUR_ROUTING_MODEL
</code></pre>

### Testing
You can test RoutedFusion using either the pretrained models or your own model. Furthermore, you need to define a test config specifying the test data. 

<pre><code>python test_fusion.py --experiment $PATH_TO_YOUR_EXPERIMENT --test configs/tests/shapenet.routed.noise.005.yaml
</code></pre>

