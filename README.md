# Gliding vertex on the horizontal bounding box for multi-oriented object detection 

This is the repository of paper "[Gliding vertex on the horizontal bounding box for multi-oriented object detection](https://arxiv.org/abs/1911.09358)". 

![image-20191218130556646](https://tva1.sinaimg.cn/large/006tNbRwly1ga0sm5sjiuj30w60ek1kx.jpg)

## Install

The project is based on [Maskrcnn Benchmark](https://github.com/facebookresearch/maskrcnn-benchmark). **Please follow its instructions to prepare the environment**. For caution's sake, specific version [73ed879](https://github.com/facebookresearch/maskrcnn-benchmark/tree/73ed87954c971b4f6f38d9da442bcac51055353e) is suggested to use. We list the some details here:

```
Python: 3.6.7
PyTorch: 1.2.0.dev20190704
CUDA: 9.0
CUDNN: 7
```

Compile the `poly_nms`:

```shell
REPO_ROOT/maskrcnn_benchmark/utils/poly_nms$ python setup.py build_ext --inplace
```

Don't forget to add `maskrcnn_benchmark` into `$PYTHONPATH`:

```shell
REPO_ROOT/maskrcnn_benchmark$ export PYTHONPATH=$PYTHONPATH:`pwd`
```

## Run

Please edit the file `maskrcnn_benchmark/config/paths_catalog.py` to set the datasets.

Train:

```shell
REPO_ROOT$ python -m torch.distributed.launch --nproc_per_node=$NUM_GPUS tools/train_net.py --config-file $PATH_TO_CONFIG
```

Test:

```shell
REPO_ROOT$ python -m torch.distributed.launch --nproc_per_node=$NUM_GPUS tools/test_net.py --config-file $PATH_TO_CONFIG --ckpt=$PATH_TO_CKPT
```

### Reproduce the results on DOTA

Config: `configs/glide/dota.yaml`

#### 1. Prepare the data

Clone DOTA_Devkit as a sub-module:

```shell
REPO_ROOT$ git submodule update --init --recursive
REPO_ROOT/maskrcnn_benchmark/DOTA_devkit$ sudo apt-get install swig
REPO_ROOT/maskrcnn_benchmark/DOTA_devkit$ swig -c++ -python polyiou.i
REPO_ROOT/maskrcnn_benchmark/DOTA_devkit$ python setup.py build_ext --inplace
```

Edit the `config.json` and run:

```shell
REPO_ROOT$ python prepare.py
```

#### 2. Train

You can train the model by yourself or directly use the snapshot provided by us (our method with FPN on DOTA): from [Google Drive](https://drive.google.com/open?id=19j6PiHFdtWemu0TC6pURKCpVcjKawPso) or [Baidu NetDisk](https://pan.baidu.com/s/1FO2eNBP6J6HgiklGjxnxuw).

```shell
REPO_ROOT$ python -m torch.distributed.launch --nproc_per_node=3 tools/train_net.py --config-file configs/glide/dota.yaml
```

#### 3. Test

```shell
REPO_ROOT$ python -m torch.distributed.launch --nproc_per_node=3 tools/test_net.py --config-file configs/glide/dota.yaml
# Edit ResultMerge.py and run it.
# srcpath = "REPO_ROOT/exp_dota/dota/inference/dota_test_cut/results"
REPO_ROOT/maskrcnn_benchmark/DOTA_devkit$ python ResultMerge.py
```

BibTex:
```
@article{xu2019gliding,
  title={Gliding vertex on the horizontal bounding box for multi-oriented object detection},
  author={Xu, Yongchao and Fu, Mingtao and Wang, Qimeng and Wang, Yukang and Chen, Kai and Xia, Gui-Song and Bai, Xiang},
  journal={IEEE Transactions on Pattern Analysis and Machine Intelligence},
  year={2020}
}
```
