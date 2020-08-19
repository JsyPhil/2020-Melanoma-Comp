## BrainTangle - Kaggle 2020 Melanoma Competition Silver Medal

This repository contains the code used by the BrainTangle Team in the [Kaggle 2020 SIIM-ISIC Melanoma Classification Competition](https://www.kaggle.com/c/siim-isic-melanoma-classification).

#### Summary

Our submission file 'ENSEMBLE_FINAL_1351v4.csv' was given an Area Under Curve (AUC) score of 0.9403 on the Private Leaderboard. This provided our team with a Silver Medal position finishing in position 149 out of 3,319 teams.

We also submitted two other entries into the competition which scored 0.9194 and 0.8395.

Our team used a two-prong approach to the problem. We were allowed a maximum of three submissions and so one submission was made using each of the approaches as well as a 50:50 ensemble of both approaches.

We discussed the progress of each approach, reviewed code, created ideas and agreed promising next steps on a weekly basis throughout the course of the competition.

###### Approach One

This focused on using an approach used in the [2019 ISIC Challenge](https://challenge2019.isic-archive.com/leaderboard.html) by the Hamburg University of Technology using state of the art models and techniques.

These models were built using [Tensorflow](https://www.tensorflow.org/) and [Keras](https://keras.io/) in Python. They were trained on a local environment using NVIDIA Geforce RTX 2080ti and NVIDIA Titan RTX GPUs as well as the cloud resources provided by Kaggle and Google Colab benefitting from their provision of limited TPU resources.

###### Approach Two

This focused on an alternative and innovative approach utilising bespoke image manipulation techniques and models built using [Pytorch](https://pytorch.org/) in Python. They were all trained on a local environment using an NVIDIA Geforce GTX 1080ti GPU.

Results were

- Approach One - 0.9403
- Aproach Two - 0.8395
- 50:50 Combination - 0.9194

#### Common Challenges

###### Class Imbalance

With only 33,126 samples across 2,056 patients in the Kaggle Dataset* and 10,015 images in the external HAM10000 Dataset, we were restricted by both the total number of samples and a large class imbalance with only 1.77% of malignant samples in the Kaggle Dataset to use as the positive class.

###### Resources

Whilst the Efficientnet models have made huge progress in reducing the number of parameters required for training models with highly accurate predictions in image recognition, training beyond Efficientnet B4 with image sizes greater than 384 x 384 pixels in a local environment using the latest consumer and prosumer graphics cards becomes restrictive in terms of both the timescale it takes to train models (days rather than hours) and memory limitations impacting the ability to experiment with different batch sizes which was determined to be an important hyper-parameter.

One solution to this is to use state of the art TPUs available in the cloud made available by Google and Kaggle, however Kaggle restricts training to three hours per session and Google similarly restricts availability depending on demand which provides uncertainty of success when training larger models.

#### Approach One

We used the state of the art Efficientnet models B0 to B7 feeding in images using TFRecords with image sizes as follows:

| Efficientnet Model | Image Size (pixels) |
| ------------------ | ------------------- |
| Efficientnet B0    | 192 x 192           |
| Efficientnet B1    | 256 x 256           |
| Efficientnet B2    | 256 x 256           |
| Efficientnet B3    | 384 x 384           |
| Efficientnet B4    | 384 x 384           |
| Efficientnet B5    | 512 x 512           |
| Efficientnet B6    | 512 x 512           |
| Efficientnet B7    | 768 x 768           |

###### Experiments

Many experiments were carried out utilising a train/validation split of the dataset which was a combination of the Kaggle Dataset* and the HAM10000 Datset*. These included:

- Image size and model combinations
- Number of epochs with and without early stopping
- Batch sizes
- Learning rate schedules including ramp up and decay parameters
- With and without external data (HAM 10000 Dataset*)
- Upsampling all and/or just positive samples
- Calculations and implementations of class weights during fitting
- Image augmentation including rotation, shifting, cropping and colour alterations.
- Cutout of image pixels
- Searching for meta-data mix optimisation
- Test time augmentation (TTA)
- Model ensemble methods

###### Final Selection

Experiments were carried out on all the models and image sizes. Results for each experiment were logged. The most promising were then used in a 5 fold cross validation process to review training loss and AUC scores from each cross validation fold together with standard deviation scores. These Out of Fold (OOF) scores were analysed to determine stability and success in the models. These scores were also logged and occasionally compared against Kaggle public leaderboard scores to further help determine cross validation stability.

Once the best cross validation scores were observed based on AUC score and stability, these models were fully trained using all the data for each of the Efficientnet and Image Size pairs.

Each of these models included a specific mix of meta-data predictions based on a simple probability model with a search technique testing all possible iterations between 0.1% and 30% mix on every cross validation fold. The mix which provided the best mean AUC score across the cross validation process was determined.

A final ensemble of all eight predictions using a weighted average method was used to calculate the final predictions for submission in the competition. This included a 10% mix of predictions using an XGBoost model to make predictions using patient meta-data including age, sex and area of the body in which the legion exists.

#### Approach Two

We used innovative image manipulation techniques across both RGB and HSL image spectrums for each sample.

Models were built using image-only data in Pytorch. OOF predictions were used in the final enseble using models Efficientnet B0 to Efficientnet B4.

#### Using This Repo

The prediction files used in the final ensemble together with notebooks with the models used to generate these predictions are included.

The notebooks to build the cross validation OOF predictions are included.

The notebooks to build out experiments are included. 

Logs and summaries of experiments and cross val scores are included.

Datasets will required to be downloaded from Kaggle.

##### *Kaggle Dataset

The ISIC 2020 Challenge Dataset https://doi.org/10.34970/2020-ds01 (c) by ISDIS, 2020

Creative Commons Attribution-Non Commercial 4.0 International License.

The dataset was generated by the International Skin Imaging Collaboration (ISIC) and images are from the following sources: Hospital Clínic de Barcelona, Medical University of Vienna, Memorial Sloan Kettering Cancer Center, Melanoma Institute Australia, The University of Queensland, and the University of Athens Medical School.

You should have received a copy of the license along with this work.

If not, see https://creativecommons.org/licenses/by-nc/4.0/legalcode.txt.

##### *HAM10000 Dataset

[The HAM10000 dataset, a large collection of multi-source dermatoscopic images of common pigmented skin lesions](https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/DBW86T)