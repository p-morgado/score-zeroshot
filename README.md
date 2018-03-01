## Semantically Consistent Regularizer (SCoRe)
By [Pedro Morgado](http://www.svcl.ucsd.edu/~morgado) and [Nuno Vasconcelos](http://www.svcl.ucsd.edu/~nuno).

Statistical Visual Computing Lab (SVCL)

University of California, San Diego

### Introduction
This repository contains the source code for 
["Semantically Consistent Regularization for Zero-Shot Recognition"](http://www.svcl.ucsd.edu/~morgado/score/score-cvpr17.pdf), CVPR, 2017.

Implementation was written by Pedro Morgado.
If you encounter any issue when using our code/models, let me know.
 
##### Citation
Please cite our paper if it helps your research:
```
@inproceedings{MorgadoCVPR17,
    author={Pedro Morgado and Nuno Vasconcelos},
    title={Semantically Consistent Regularization for Zero-Shot Recognition},
    booktitle={Computer Vision and Pattern Recognition (CVPR), IEEE Conf.~on},
    year={2017},
    organization={IEEE}
}
```

```
Pedro Morgado and Nuno Vasconcelos. 
Semantically consistent regularization for zero-shot recognition.
Computer Vision and Pattern Recognition (CVPR), IEEE Conf. on, 2017.
```

### Prerequisites
* [python 2.7.](https://www.python.org/download/releases/2.7/) (with scikit-image and lmdb)
* [caffe](http://caffe.berkeleyvision.org/installation.html) (with pycaffe)

### Tour
##### Code
1. `score_model.py`: Defines the SCoRe model.
1. `score_train.py`: Training script.
1. `score_eval.py`: Evaluation script. 
1. `tools/prepare_LMDBs.py`: Script for preparing LMDBs used in `score_train.py` and `score_test.py`.

Type `python xxx.py --help` for usage options.

For a better understanding of the workflow of our code, a script 
`train_eval_CUB.sh` is provided for training a SCoRe model on the CUB dataset.
This script 1) downloads all required data (CUB and caffemodels for standard 
CNNs), 2) prepares LMDBs for both images and semantics, 3) trains the 
classifier and 4) evaluates on source and target (Zero-Shot) classes.

##### Data
1. Semantic codewords: Pre-extracted for all classes in both AwA and 
CUB datasets (see `data/`).
1. Partition into source and target classes: see 
`classes.txt`, `train_classes.txt` and `test_classes.txt` at `data/${DB}`.
1. Training sets: `data/${DB}/train_images.txt`
1. Test sets: `data/${DB}/testRecg_images.txt` (source classes) and 
`data/${DB}/testZS_images.txt` (target classes).
 

### Results
Mean class accuracy for source and target classes using three architectures: 
AlexNet, GoogLeNet and VGG19.

***Note:*** 
Lagrangian coefficients were tuned for Zero-Shot MCA 
on a set of validation classes. Obtained coefficients are shown below.
 
##### [Animals with Attributes](http://attributes.kyb.tuebingen.mpg.de/)
Semantics | Source Classes | Target Classes | Semantic Coeff | Codeword Coeff
:---:|:---:|:---:|:---:|:---:
Attributes | 72.5 / 85.1 / 84.6 | 66.7 / 78.3 / 82.8 | 0.01 | 10.0
Hierarchy  | 74.4 / 84.2 / 84.5 | 52.3 / 61.2 / 60.7 | 0.05 | 1.0
Word2Vec   | 76.7 / 86.7 / 85.8 | 51.9 / 60.9 / 57.9 | 0.01 | 0.5 

Key: AlexNet / GoogLeNet / VGG19

##### [Caltech-UCSD Birds-200-2011](http://www.vision.caltech.edu/visipedia/CUB-200-2011.html)
Semantics |Source Classes | Target Classes | Semantic Coeff | Codeword Coeff
:---:|:---:|:---:|:---:|:---:
Attributes | 61.7 / 71.6 / 70.9 | 48.5 / 58.4 / 59.5 | 0.01 | 1.0
Hierarchy  | 60.2 / 73.1 / 69.6 | 24.2 / 31.8 / 31.3 | 0.05 | 5.0
Word2Vec   | 61.4 / 73.6 / 71.9 | 26.0 / 31.5 / 30.1 | 0.01 | 1.0




### (↓ Reproduced results in Caltech-UCSD Birds-200-2011 dataset) 
##### 
Semantics |Source Classes | Target Classes | Semantic Coeff | Codeword Coeff
:---:|:---:|:---:|:---:|:---:
Attributes | 62.2 / 73.4 / 69.6 | 46.7 / 58.5 / 56.3 | 0.01 | 1.0
Hierarchy  | 60.6 / 71.9 / 68.1 | 24.3 / 25.0 / 27.7 | 0.05 | 5.0
Word2Vec   | 60.7 / 73.4 / 73.9 | 26.9 / 30.5 / 29.2 | 0.01 | 1.0

### (↓ Difference between reproduced results (R) and paper ones (P): (R) - (P)) 
#####
Semantics |Source Classes | Target Classes 
:---:|:---:|:---:
Attributes | 0.5 / 1.8 / -1.3 | -1.8 / 0.1 / -3.2 
Hierarchy  | 0.4 / -1.2 / -1.5 | 0.1 / **-6.8** / **-3.6** 
Word2Vec   | -0.7 / -0.2 / 2.0 | **0.9** / -1.0 / -0.9 

Key: AlexNet / GoogLeNet / VGG19

### Commands to train each model
#####
Model |**Attributes**
:---:|:---:
AlexNet | python score_train.py A_Attr CUB Attributes AlexNet -g 0.01 -c 1 --gpu 0 --iters 1000 --init_lr 0.0005
GoogLeNet | python score_train.py G_Attr CUB Attributes GoogLeNet -g 0.01 -c 1 --gpu 1 --iters 1000 --init_lr 0.0005
VGG19 | python score_train.py V_Attr CUB Attributes VGG19 -g 0.01 -c 1 --gpu 0 --iters 1000 --init_lr 0.0005 --batch_size 30 
#####
Model |**Hierarchy**
:---:|:---:
AlexNet | python score_train.py A_Hier CUB Hierarchy AlexNet -g 0.05 -c 5 --gpu 1 --iters 1000 --init_lr 0.0005
GoogLeNet | python score_train.py G_Hier CUB Hierarchy GoogLeNet -g 0.05 -c 5 --gpu 1 --iters 1000 --init_lr 0.001
VGG19 | python score_train.py V_Hier CUB Hierarchy VGG19 -g 0.05 -c 5 --gpu 0 --iters 1000 --init_lr 0.0005 --batch_size 30  
#####
Model |**Word2Vec**
:---:|:---:
AlexNet | python score_train.py A_Word CUB Word2Vec AlexNet -g 0.01 -c 1 --gpu 1 --iters 1000 --init_lr 0.0005 
GoogLeNet | python score_train.py G_Word CUB Word2Vec GoogLeNet -g 0.01 -c 1 --gpu 1 --iters 1000 --init_lr 0.001 
VGG19 | python score_train.py V_Word CUB Word2Vec VGG19 -g 0.01 -c 1 --gpu 0 --iters 2000 --init_lr 0.0005 --batch_size 30

### System environments
* Ubuntu 16.04
* GTX 1080 Ti x2
* CUDA 8.0
* cuDNN v7

### Trained models

These models are compatible with the provided code. Simply download and uncompress the `.tar.gz` files, and use 
 `score_eval.py` to evaluate them.

-/- |AwA | CUB
:---:|:---:|:---:
Attributes | [AlexNet](http://www.svcl.ucsd.edu/~morgado/score/models/AwA/AlexNet/SCoRe_Attributes_P0.0005_G0.01_C10.0.tar.gz) / [GoogLeNet](http://www.svcl.ucsd.edu/~morgado/score/models/AwA/GoogLeNet/SCoRe_Attributes_P0.0005_G0.01_C10.0.tar.gz) / [VGG19](http://www.svcl.ucsd.edu/~morgado/score/models/AwA/VGG19/SCoRe_Attributes_P0.0005_G0.01_C10.0.tar.gz) | [AlexNet](http://www.svcl.ucsd.edu/~morgado/score/models/CUB/AlexNet/SCoRe_Attributes_P0.0005_G0.01_C1.0.tar.gz) / [GoogLeNet](http://www.svcl.ucsd.edu/~morgado/score/models/CUB/GoogLeNet/SCoRe_Attributes_P0.0005_G0.01_C1.0.tar.gz) / [VGG19](http://www.svcl.ucsd.edu/~morgado/score/models/CUB/VGG19/SCoRe_Attributes_P0.0005_G0.01_C1.0.tar.gz) 
Hierarchy  | [AlexNet](http://www.svcl.ucsd.edu/~morgado/score/models/AwA/AlexNet/SCoRe_Hierarchy_P0.0005_G0.05_C1.0.tar.gz) / [GoogLeNet](http://www.svcl.ucsd.edu/~morgado/score/models/AwA/GoogLeNet/SCoRe_Hierarchy_P0.0005_G0.05_C1.0.tar.gz) / [VGG19](http://www.svcl.ucsd.edu/~morgado/score/models/AwA/VGG19/SCoRe_Hierarchy_P0.0005_G0.05_C1.0.tar.gz) | [AlexNet](http://www.svcl.ucsd.edu/~morgado/score/models/CUB/AlexNet/SCoRe_Hierarchy_P0.0005_G0.05_C5.0.tar.gz) / [GoogLeNet](http://www.svcl.ucsd.edu/~morgado/score/models/CUB/GoogLeNet/SCoRe_Hierarchy_P0.0005_G0.05_C5.0.tar.gz) / [VGG19](http://www.svcl.ucsd.edu/~morgado/score/models/CUB/VGG19/SCoRe_Hierarchy_P0.0005_G0.05_C5.0.tar.gz) | 
Word2Vec   | [AlexNet](http://www.svcl.ucsd.edu/~morgado/score/models/AwA/AlexNet/SCoRe_Word2Vec_P0.0005_G0.01_C0.5.tar.gz) / [GoogLeNet](http://www.svcl.ucsd.edu/~morgado/score/models/AwA/GoogLeNet/SCoRe_Word2Vec_P0.0005_G0.01_C0.5.tar.gz) / [VGG19](http://www.svcl.ucsd.edu/~morgado/score/models/AwA/VGG19/SCoRe_Word2Vec_P0.0005_G0.01_C0.5.tar.gz) | [AlexNet](http://www.svcl.ucsd.edu/~morgado/score/models/CUB/AlexNet/SCoRe_Word2Vec_P0.0005_G0.01_C1.0.tar.gz) / [GoogLeNet](http://www.svcl.ucsd.edu/~morgado/score/models/CUB/GoogLeNet/SCoRe_Word2Vec_P0.0005_G0.01_C1.0.tar.gz) / [VGG19](http://www.svcl.ucsd.edu/~morgado/score/models/CUB/VGG19/SCoRe_Word2Vec_P0.0005_G0.01_C1.0.tar.gz) | 


