This is a brief report about investigation and modification of indoor localization using Wi-Fi fingerprinting based on deep neural networks

## introduction
### Location Awareness
Location awareness is one of popular technologies in our lives. It plays an important role in many applications, such as navigation for drivers, charging tolls for crossing a bridge. At present, mature location-aware services are confined to outdoor scenes. It uses Global Positioning Systems (GPSs) to estimate location where there is an unobstructed line of sight to satellites. However, there is no line-of-sight signal from GPSs indoors. Compared with outdoor location, indoor location information is relative absent or rough. Therefore, indoor localization is a challenging problem and there exist no universal solutions.
### Wi-Fi fingerprinting
Nowadays, Wi-Fi networks exist everywhere in public buildings, schools, shopping malls. Almost every mobile robot is equipped with a Wi-Fi adapter used to connect with the Internet. Wi-Fi adapter can obtain the observed service set identifier (SSID) and received signal strengths (RSS) for access points (APs). Wi-Fi fingerprinting is defined as a vector of a pair of a SSID and an RSS for an AP measured at a location. Then a position can be estimated by finding the closet match between RSS and the fingerprints of known locations in a database. The procedure of indoor localization using Wi-Fi printing is shown below.

:![](shot\procedure.png)

## Project Objective

In this project, we investigate an indoor localization method using Wi-Fi fingerprinting based on deep learning. This method focuses on building/floor classification and floo-level location estimation. Besides, we adjust some parameters based on the original code including network layers, epochs, batch_size and CLASSIFIER_ACTIVATION. Some adjustments can improve the capability of this method.

## Methodology
The core of this method is classification DNN with stacked autoencoders.

### Stacked Antoencoders (SAE)
The objective of this part is to reduce feature dimensionality and take advantage of a large amount of gathered data. The basic structure of SAE is presented below. 
![](shot\autoencoder.png)

The input of SAE are received signal strengths (RSS) for APs. The output of decoder are the reconstructed RSSs of inputs from reduced representation. In the middle, there are several symmetrical hidden layers (HL) in the encoder and decoder parts dividedly. The SAE is learned during unsupervised training and the goal is to train the pair encoder-decoder to achieve the same information at the output as input. For example, here we enter 520 RSS inputs and create 520 feature layers, the network could generate smaller layers, such as 64 layers, these layers have enough information to reconstruct 520 input data. It makes use of information redundancy to compress data sets.

### Deep Neural Network (DNN) classifier with SAE
After finishing the unsupervised learning of weights of SAE, the decoder part is disconnected and the already pre-trained encoder part is connected to DNN classifier. The architecture is shown below. 
![](shot\DNN.PNG)

The inputs are the same as previous SAE, but the outputs are estimated location label, building, floor and location (B-F-L). The classifier part consists of several hidden layers, it depends on the complexity of the problem. Dropout is employed among the layers, which drops connections randomly between layers during training to force the network to learn redundant representation, achieve better generalization and avoid overfitting.
Before the supervised learning, the labeled data is divided into three parts, the training, validation and testing datasets. The whole DNN network is learned on training sets and then is tested on validation sets. The final performance of this network is obtained on the testing sets. During the training process, weights of SAE trained before and weights in DNN classifier are both modified to produce the final system.

## Experimental results and discussion
### Original final performance
The important parameters of the original code is presented in the table. 
![](shot\original.PNG)

We emply UJIIdoolrLoc datasets. We divide the training datasets into training, validation and testing parts, the ratio of them is 7:2:1. Each database contains 529 attributes including 520 RSSs for Aps, floor number, building ID, space ID, relative position and other information. Here, epochs is 20 and batch_size is 10. Epochs means the number of training the whole data sets. batch_size represents the number of samples per gradient update. These two parameters would influence the final performance a lot. The accuracy and loss of training data and validation data are presented below. The final result using testing data obtains 1.25 loss and about 60% accuracy.
![](shot\2010.PNG)

## Modification
After getting the original final accuracy and loss, we started to change the parameters. Although it is an individual coursework, we studied together. Because sometimes running program once requires lots of time. We can save much time if everyone run he program without duplicate parameters. 

At first, we change the number of network layers in SAE. The reuslts of accuray is presented in the table.
![](shot\SAElayer.PNG)

Comparing the results, we find that fewer layers can obtain the less loss and higher accuracy. However, it is meaningless modification to improve the performance. Because, the idea of SAE is to use less information to include the whole original information. Considering the performance and compression, we decide to use the original hidden layers.

Then, we change the value of epoch from 20 to 30, 40, 50. The final performance are shown. 
![](shot\epoch.PNG)

Comparing the results, loss is decreasing and accuracy is increasing as the number of epoch become larger. The reason is that in a certain range, with the larger number of training, the weights in network keep on being updated. Then, weights would be fitter for this training datasets. However, it may occur overfitting phenomenon, which means that network is too fit for training datasets and can not obtain inferior performance on testing data.

Therefore, we set epoch to 200, a larger number, and change the batch size to 200, 300, 400 and 500. The results are presented below. 
![](shot\batchsize.PNG)

When changing batch size from 200 to 300, the loss become smaller and accuracy is improved. However, from 300 to 500, the accuracy is decreasing. Therefore, we assume that when epoch equals to 200 and batch size equals to 300 can produce best performance. 

However, because of testing order conflict, we test different CLASSIFIER_ACTIVATION attributes with epoch 200 and batch size 200, not 300. You can see the whole results below.
![](shot\classifier.PNG)

There are some random variables on the code so the results are floating, but near a value. The original method uses relu with 73% accuray. Comparing the whole table, elu, selu, relu can obtain the best performance. 

## Conclusion

In conclusion, this project focuses on learning building/floor classification and location estimation using Wi-Fi fingerprinting based on deep neural networks. Two core parts of these method are Stacked autoencoder and Deep Neural Networks. The best performance we obtained is about 75% accuracy when epochs is 200 and batch size is 300.

## Reference
<sup><a id="fn.1" class="footnum" href=" ">1</a></sup> M. Nowicki and J. Wietrzykowski, "Low-effort place recognition with WiFi fingerprints using deep learning," arXiv:1611.02049v2 [cs.RO] [(arXiv)](https://arxiv.org/abs/1611.02049v2)

<sup><a id="fn.2" class="footnum" href="#fnr.2">2</a></sup> T. Yamashita et al., "Cost-alleviative learning for deep convolutional neural network-based facial part labeling," *IPSJ Transactions on Computer Vision and Applications*, vol. 7, pp. 99-103, 2015. [(DOI)](http://doi.org/10.2197/ipsjtcva.7.99)
