# Domain-adaptive-visual-object-detection-with-CycleGAN-and-AdaIN

In this repository it ha been uploaded the code that has been used for the different experiments made in domain adaptation related to object detection.
In this project we have used the code proposed [here](https://github.com/lufficc/SSD) , that we have modified in order to realize experiments with Cyclegan and AdaIN, two different pixel level domain adaptation algorithm.

# Step-by-step Installation
```bash
git clone https://github.com/gurciuoli95/Domain-adaptive-visual-object-detection-with-CycleGAN-and-AdaIN.git
pip install -r requirements.txt
```
# Configuration
In order to repeat the same experiments done, we give [here](https://drive.google.com/file/d/1-RUxFU92Qbh0AB2mODs2PSO1zBUiA0fb/view?usp=sharing) a pre trained model on SSD  and the config file that has to be put in SSD-master/configs.

## Datasets
The datasets that the code support are Clipart, PascalVOC2007, PascalVOC2012. The datasets folder has to be put in ssd/data/dataset and they need to have this configuration:
```
datasets
|__ VOC2007
    |_ JPEGImages
    |_ Annotations
    |_ ImageSets
    |_ SegmentationClass
|__ VOC2012
    |_ JPEGImages
    |_ Annotations
    |_ ImageSets
    |_ SegmentationClass
|__ ...
```
In order to add a new dataset it can be followed this [guide](./DEVELOP_GUIDE.md) given by the creator of the code: 


# Finetuning  

## Cyclegan
The experiments on CycleGAN need a new dataset of images that are domain shifted. We have used this [code](https://github.com/junyanz/pytorch-CycleGAN-and-pix2pix) , which is the official imlplementation of cyclegan, to train a new model on PascalVOC 2007 and 2012 on order to do a pixel level domain adaptation in Clipart1k domain.
The instruction to create this dataset are in the cycleGAN repository linked above.
After the creation of this new dataset that contains source images (voc2007 and voc2012) shifted in clipart domain, we need to import the dataset as we have explained before. After that the fine-tuning phase on SSD can start.
 In oredr to perform the finetuning we can use:
```bash
python train.py --config-file A --finetuning B 
```
Where:
* A is path of the specific config-file containing parameters of the finetuning
* B is the path containing the weights of the model on which the finetuning should be applied

In ./configs you will find the parameters that we used for the CycleGAN finetuning steps, they have the pattern "vgg_ssd300_voc2Clipart_FineTuning#.yaml" where # is relating to the specific experiment. They can be modified as you want.

## AdaIN
Starting from [this](https://github.com/naoto0804/pytorch-AdaIN) AdaIN repository  

We have implemented AdaIN style transfer internally in SSD starting from the code provided [here](https://github.com/naoto0804/pytorch-AdaIN)
From that repository, we also used the pre-trained model for the style transfer.
In /ssd/Adain you will find what we used for the AdaIN pixel level domain adaptation step.

In the "configs" folder you will find the parameters that we used for the AdaIN finetuning steps, they have
the pattern "vgg_ssd300_ADAIN_voc2Clipart_FineTuning#" where # is relating to the specific experiment.

In SSD-master/

To run the finetuning you should write in the console something like:
```bash
!python train.py --config-file A --finetuning B --adain C --probability D
```
where:
* A is the path to the specific config-file containing parameters of the finetuning.
* B is the path containing the weights of the model on which the finetuning should be applied.
* C is a boolean, it should be setted as True.
* D is an int, is the probability with which the style transfer will be applied to a content image of the source domain.

# Evaluation
A first evaluation is printed at the end of the finetuning phase or with th command:
```bash
!python test.py --config-file A 
```
where:
A is the path to the specific config-file containing parameters of the finetuning.
