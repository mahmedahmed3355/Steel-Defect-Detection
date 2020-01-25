# Steel-Defect-Detection
this is a sematic segmentation task
uses images from high frequency cameras to power a defect detection algorithm.
In this competition,  improve the algorithm by localizing and classifying surface defects on a steel sheet.
valuated on the mean Dice coefficient. The Dice coefficient can be used to compare the pixel-wise agreement between a predicted segmentation and its corresponding ground truth. The formula is given by:

2∗|X∩Y|/|X|+|Y|
Classification is an important part of this competition
iltering out around half of images with no defects.
Augmentations:
Randomcrop, Hflip, Vflip, RandomBrightnessContrast (from albumentations) and a customized defect blackout
Batchsize: 8 for efficientnet-b1, 16 for resnet34 (both accumulate gradients for 32 samples)
Optimizer: SGD
Model Ensemble:
3 x efficientnet-b1+1 x resnet34
TTA: None, Hflip, Vflip
Threshold: 0.6,0.6,0.6,0.6
Train data: 256x512 crop images
Augmentations: Hflip, Vflip, RandomBrightnessContrast (from albumentations)
Batchsize: 12 or 24 (both accumulate gradients for 24 samples)
Optimizer: Rectified Adam
Models: Unet (efficientnet-b3), FPN (efficientnet-b3) from @pavel92 segmentationmodelspytorch
Loss:
BCE (with posweight = (2.0,2.0,1.0,1.5)) 0.75BCE+0.25DICE (with posweight = (2.0,2.0,1.0,1.5))
Model Ensemble:
1 x Unet(BCE loss) + 3 x FPN(first trained with BCE loss then finetuned with BCEDice loss) +2 x FPN(BCEloss)+ 3 x Unet from mlcomp+catalyst infer
TTA: None, Hflip, Vflip
Label Thresholds: 0.7, 0.7, 0.6, 0.6
Pixel Thresholds: 0.55,0.55,0.55,0.55
Postprocessing:
Remove whole mask if total pixel < threshold (600,600,900,2000) + remove small components with size <150

Pesudo Label
We did 2 rounds of pseudo labels in this competition. The first round is generated from a submission with 0.916 public LB, maybe it is too early? The second round was done several days before the end of this competition, generated from a submission with 0.91985 public LB. With pseudo label and public models, we finally improved from 0.91985 to 0.92124 on public LB and from 0.90663 to 0.90883 on private LB.
The pseudo labels are chosen if classifiers and segmentation networks make the same decisions. We got this idea from Heng. An image will only be chosen if the probabilities from classifiers are all over 0.95 or below 0.05 and it gets same result from segmentation part. According to this rule, 1135 images are chosen and added to trainset.
