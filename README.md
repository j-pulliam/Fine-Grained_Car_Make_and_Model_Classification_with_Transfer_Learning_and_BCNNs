# Fine-Grained Car Make and Model Classification with Transfer Learning and BCNNs
In this project we explore fine-grained car make and model classification on the Stanford Cars Dataset. We first experiment with fine-tuning some of the more famous CNN architectures such as VGG, Resnet, and Densenet. After doing this analysis we build various structured ensembles of these fine-tuned models and analyze how they are able to support each other during classification. Finally we explore the concept of bilinear convolutional neural networks (BCNNs) which take into consideration not only spatial locality within images but also feature location.

This project was created as the final for [10-707 Topics in Deep Learning](https://deeplearning-cmu-10707.github.io/) at [Carnegie Mellon University](https://www.cmu.edu/) under the instruction of [Ruslan Salakhutdinov](https://www.cs.cmu.edu/~rsalakhu/) in collaboration with Hashim Saeed.  

## Dataset Download
Due to space constraints the dataset has not been included in this repository as it is ~2GB in size. The Stanford Cars Dataset can be downloaded at the following [link](https://ai.stanford.edu/~jkrause/cars/car_dataset.html). To download this data please scroll to the "Download" section and click on the following series of links:
* Link at the end of "Training images can be downloaded here" (Line 1)
* Link at the end of "Testing images can be downloaded here" (Line 2)
* Link at the end of "A devkit, including class labels for training images..." (Line 3)

The three links above should download the following files:
* cars_train.tgz
* cars_test.tgz
* car_devkit.tgz

After downloading these files please create a 'data' folder within this repository and move the three files above to it. Extract the folder contained within each compressed download. The code is setup to work over the data in this way. The result of performing the actions above should result in the following being contained in this repo:
* data/car_devkit
* data/cars_test
* data/cars_train

For testing purposes download the test annotations file with labels. This can be downloaded by selecting the final link in the "Update" subsection underneath "Download" where it reads "you can use test annotations here" (Line 3). After downloading this file (named "cars_test_annos_withlabels.mat") please move it to the following folder:
* data/car_devkit/devkit

The Stanford Cars Dataset should now be ready for processing with the code contained in this repository.


## Virtual environment creation:
Assuming [Anaconda](https://www.anaconda.com/) is installed the following steps can be taken to create a conda virtual environment for this project. Note that the local system used for development had access to a GeForce GTX 1060 GPU with CUDA version 10.2, thus the PyTorch install command may vary based on CUDA version. Please see [PyTorch installation](https://pytorch.org/) for more details.
```
conda create -n car_classification python=3.7
conda activate car_classification
conda install pytorch torchvision cudatoolkit=10.2 -c pytorch
conda install scipy
conda install opencv
conda install matplotlib
```

## Repo breakdown
This repository consists of 9 files and 1 folder. The 9 files are as follows:
* bcnnMain.py:
* bcnnModel.py:
* dataset.py: PyTorch dataset class definition.
* display_training_stats.py: Displays the stats from training in a graph.
* label_names.py: Prints all car labels in the dataset.
* main_cnn.py: Trains a CNN over the dataset for a set number of epochs evaluating performance over the test set after each.
* test_cnn.py: Evaluates the final performance of a trained CNN over both the training and test sets.
* test_ensemble.py: Evaluates the performance of an ensemble of three trained CNNs over both the training and test sets.
* utils.py: Utility functions called by numerous files.
* view_image.py: Views a random image from the training dataset and its corresponding label.

The 1 folder is:
* report: contains the paper written covering the project, "Fine Grained Car Make and Model Classification with Transfer Learning and BCNNs"  

## Viewing all labels in the dataset
To view all dataset labels please run the following command:
```
python label_names.py
```

## Viewing a random image from the training dataset
To view a random image from the training dataset run the following command:
```
python view_image.py
```

## Training a CNN over the dataset
To train (finetune / feature extraction) a CNN over the dataset and evaluate the performance over the test set after each epoch run the following command:
```
python main_cnn.py --model <int> --resize_shape <int> --feature_extract --batch_size <int> --epochs <int> --name <string>
```

The "model" parameter must be an integer between 1 and 9 and specifies the type of pre-trained CNN architecture to use. This directly makes use of [PyTorch's torchvision models](https://pytorch.org/docs/stable/torchvision/models.html) with options being as follows:
* [VGG11](https://arxiv.org/abs/1409.1556)
* [VGG11_BN](https://arxiv.org/abs/1409.1556)
* [VGG16](https://arxiv.org/abs/1409.1556)
* [VGG16_BN](https://arxiv.org/abs/1409.1556)
* [Resnet18](https://arxiv.org/abs/1512.03385)
* [Resnet50](https://arxiv.org/abs/1512.03385)
* [Resnet152](https://arxiv.org/abs/1512.03385)
* [Densenet161](https://arxiv.org/abs/1608.06993)
* [Alexnet](https://arxiv.org/abs/1404.5997)

Additional parameters are "resize_shape", "feature_extract", "batch_size", "epochs", and "name". These parameters serve the following purpose:
* resize_shape: Specifies the size to reshape dataset images to for classification. Note that this should be a positive integer and not all values will work; it is recommended to keep this at the default value (default=224).
* feature_extract: If this parameter is used feature extraction will be performed where only the top classification layer's weights are modified. Otherwise all network weights will be adjusted with fine-tuning. [For more information on the differences between feature extraction and fine-tuning](https://stats.stackexchange.com/questions/255364/fine-tuning-vs-joint-training-vs-feature-extraction#:~:text=You%20train%20a%20model%20on,it%20on%20a%20new%20dataset.).
* batch_size: Batch size to use while training. Must be a positive integer and it is recommended to use a power of 2 as this will scale the best to GPU hardware. If a "CUDA out of memory" error occurs reduce this value. To check GPU resource consumption run "watch nvidia-smi" at the command line (default=32).  
* epochs: Number of epochs to train for. In the attached report models were trained for 100 epochs or until over-fitting occurred (default=5)
* name: Filename to save the model and training stats to. The model will be saved to a file named "name.pt" while the training stats will be saved to "name_stats.pt". This parameter is required.   
