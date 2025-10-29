There will be a string like `/your/path/` in the file, which needs to be replaced by yourself.

# How to use

It is mainly divided into one `.cpp` file and three folders, namely `creat_trainset.cpp` and `TRAIN LIDAR_IVF LIDAR_HNSW` .

We use `creat_trainset.cpp` to create the training set, `TRAIN/` to train, and `LIDAR_IVF/` and `LIDAR_HNSW/` to run our algorithm.

## Compile

In `creat_trainset.cpp`, `openmp` parallel processing is used, so you need to link it before compiling the program.

```bash
g++ creat_trainset.cpp -o creat_trainset -Ofast -march=native -fopenmp
```

The remaining three folders need to enter the build folder in the corresponding directory and run the following commands to compile.

```bash
mkdir build
cd build
cmake ..
make
```

Note: libtorch is required for compilation, so you need to set your own environment path in CMakeLists.txt

Download link: https://pytorch.org/

## Run

### 1.generate trainset

```bash
./creat_trainset "dataid" "k"
```

### 2.train

First, you need to enter the build directory under the TRAIN folder and run the following command.

```bash
./main <data_dir> <dataid> <trainid> <in_dim> <out_dim> <count> <ratio> <device_cnt> <device_id_1> ... <device_id_n>
```

You can also modify our shell directly in the script.

| Parameter Name                        | Description                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| `data_dir`                    | The directory where the data is located (ending with `/`)    |
| `dataid`                      | Dataset name (file name prefix, default is `.data` suffix)   |
| `trainid`                     | Training set identifier (such as `trainset`, etc., default is `.csv` suffix) |
| `in_dim`                      | input dimension                                              |
| `out_dim`                     | output dimension                                             |
| `count`                       | **the number of local contrastive objects**                  |
| `ratio`                       | **the  $\lambda$  of loss function**                         |
| `device_cnt`                  | Number of GPUs used                                          |
| `device_id_1 ... device_id_n` | The ID of each GPU. For example, `0 1` means using GPUs 0 and 1. |

#### 3.Building Indexes & Querying

#### LIDAR_IVF

First, you need to enter the build directory under the LIDAR_IVF folder and run the program using the following command.

```bash
./main <data_dir> <dataid> <queryid> <modeldir> <modelname> <outtag> <r> <nlist>
```

| Parameter Name        | Description                                                         |
| ------------- | ------------------------------------------------------------ |
| `<data_dir>`  | The directory where the data is located (ending with `/`)    |
| `<dataid>`    | Dataset name (file name prefix, default is `.data` suffix)   |
| `<queryid>`   | Query set name (file name prefix, default is `_uniform1000.data` suffix) |
| `<modeldir>`  | The directory where the model is located                     |
| `<modelname>` | Model file name                                              |
| `<outtag>`    | Output Tags                                                  |
| `<r>`         | $\alpha$ in paper                                            |
| `<nlist>`     | Number of IVF cluster centers                                |

#### LIDAR_HNSW

First, you need to enter the build directory under the LIDAR_HNSW folder and run the program using the following command.

```bash
./main <data_dir> <dataid> <M> <ef> <tag_cnt> <model_dir> <model_file> <out_tag> <query_cnt> <query_id_1> ... <query_id_n>
```

| Parameter Name       | Description                                                       |
| :----------- | :--------------------------------------------------------- |
| `dataid`     | Dataset name (file name prefix, default is `.data` suffix) |
| `M`          | Parameter M in the HNSW algorithm                                        |
| `ef`         | Parameter ef in the HNSW algorithm                                       |
| `tag_cnt`    | k in knn of gt set                                         |
| `model_dir`  | The directory where the model is located                   |
| `model_file` | Model file name                                            |
| `outtag`     | Output Tags                                                |

Data Format
-----------

The data file ends with .data. 

The first eight bytes consist of two int, which are the number of points $n$ and the number of dimensions $d$ in the data set.

The rest consists of $n*dim*4 $ bytes, a total of $n*dim$ float types

```bash
n d(int)
e_1_1 e_1_2 ... e_1_d(float)
...
e_n_1 e_n_2 ... e_n_d(float)
```

# Model parameter and algorithm hyperparameter 

Model parameter weights and algorithm hyperparameter open source in Google Drive:  https://drive.google.com/drive/folders/1w3ZHH2N_U9_7MVunMvCRJoWkRmTWqzjF?usp=drive_link.

 

