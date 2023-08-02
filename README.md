# StrokeOrderPrediction

A transformer based self-supervised model for generating stroke-by-stroke images of Chinese characters given the original character. 

The approach is based on this paper: https://www.mdpi.com/2076-3417/13/3/1750

The data used for trainign is the same as the data located here: https://github.com/chanind/hanzi-writer-data

That data was originally in an SVG form that was embedding in JSON documents to power animated writing of Chinese characters to add to websites using Javascript. I preprocessed this data to create a dataaset of 256x256 PNGs, which were further processed by my attached Jupyter notebook. The set of 256x256 pngs is located on Kaggle: https://www.kaggle.com/datasets/maxwilcoxson/hanzi-writer-dataset-png

The data used is covered on the license https://github.com/skishore/makemeahanzi/blob/master/APL/english/ARPHICPL.TXT. 

The code written is made available under the MIT License. 

Some analysis of the project: 

In following the paper, we have results with and without the randomized resize cropping augmentation. 
In our experiement, the augmented data performed significantly worse on the test, training, and validation sets. We used the same learning rate and learning schedule as the original paper, but our loss plots suggests oscillation due to too high of a learning rate. We look to investigate this in the future. Notably, the model does not seem to ovefit, which suggests that we should be able to get better results with more fine tuning. 

Below is the loss for training and validation for the augmented data (with random resize cropping, across tens of epochs):

<img width="588" alt="Training and Validation Loss Over Time, Augmented" src="https://raw.githubusercontent.com/wilcoxsonm21/StrokeOrderPrediction/main/Training and Validation Loss Over Time, Augmented.png">

Below is the loss for training and validation across tens of epochs with no random resize cropping:
<img width="588" alt="Training and Validation Loss Over Time, Not Augmented" src="https://raw.githubusercontent.com/wilcoxsonm21/StrokeOrderPrediction/main/Training and Validation Loss Over Time, Not Augmented.png">

The test loss for the augmented test (260 epochs into training, selected by validation) was 0.0086, while the test loss for the version with no augmentation was 0.0056 (800 epochs). Both showed very good performance on simpler characters and radicals that were likely repeated many times in training, and showed room for improvement on more complicated characters. 

Suprisingly, the non-augmented data seems to get signficantly better test performance. This suggests that model overfitting is not the primary concern, but rather model bias to be able to compensate for additional variation added by augmentation. However, the fact that the paper got better results with augmentation suggests changes to the learning rate/additional fine tuning with hyperparameters can yield a better result with augmentation. The gap in performance between validation and test and this phenomena both warrent further investigation. 

An example of model predicting a charactercommon radical in test set almost perfect (top is prediction, middle is target, bottom is input)

<img width="143" alt="Radical, Augmented" src="https://raw.githubusercontent.com/wilcoxsonm21/StrokeOrderPrediction/main/Radical, Augmented.png">

Part of this accuracy may be attributed to the fact that while the character was not in the train set, this subsequence (or at least the first four strokes which compromise a common radical) was in the training set. 

We see that for a more complicated character, while the general order and strokes are captured, some details are lost

<img width="146" alt="Right Idea, Augmented" src="https://raw.githubusercontent.com/wilcoxsonm21/StrokeOrderPrediction/main/Right Idea, Augmented.png">

Finally, some characters become a bit blurry and inaccurate. 

<img width="152" alt="Blurry, Augmented" src="https://raw.githubusercontent.com/wilcoxsonm21/StrokeOrderPrediction/main/Blurry, Augmented.png">

An example of better performance on complicated characters without augmentation

<img width="139" alt="Screenshot 2023-07-31 at 11 16 22 PM" src="https://raw.githubusercontent.com/wilcoxsonm21/StrokeOrderPrediction/main/More Complicated Character, Not Augmented.png">

The paper seemed to only have crisp output images that while sometimes inaccurate did not have the same fuzzing issue. 

This futher supports the notion that more fine tuning can be used, although the paper did not mention using such enhancements.

We look to investigate these optimizations in the future, and also apply the pretrained model towards zero shot recognition of handwritten Chinese characters. 
