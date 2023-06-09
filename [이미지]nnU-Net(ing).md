[원본]([chrome-extension://hmigninkgibhdckiaphhmbgcghochdjc/pdfjs/web/viewer.html?file=https%3A%2F%2Farxiv.org%2Fpdf%2F1809.10486.pdf](https://arxiv.org/abs/1809.10486))
# nnU-Net: Self-adapting Framework for U-Net-Based Medical Image Segmentation
## Abstract
* The  present  paper  introduces  the  nnU-Net  (”no- new-Net”), which refers to a robust and self-adapting framework on the basis of 2D and 3D vanilla U-Nets. 
* We argue the strong case for taking away superfluous bells and whistles of many proposed network designs and  instead  focus  on  the  remaining  aspects  that  make  out  the  perfor- mance and generalizability of a method.

![image](https://github.com/joesiheon496/paper/assets/56191064/aeebc8e3-d1aa-4b20-96fd-b8c353ce2576)

Fig. 1.U-Net Cascade (on applicable datasets only). Stage 1 (left): a 3D U-Net pro- cesses downsampled data, the resulting segmentation maps are upsampled to the orig- inal resolution. Stage 2 (right): these segmentations are concatenated as one-hot en- codings to the full resolution data and refined by a second 3D U-Net.

## Method
* Medical images commonly encompass a third dimension, which is why we con- sider a pool of basic U-Net architectures consisting of a 2D U-Net, a 3D U-Net and  a  U-Net  Cascade.
* While  the  2D  and  3D  U-Nets  generate  segmentations at full resolution, the cascade first generates low resolution segmentations and subsequently refines them
* **Our architectural modifications as compared to the U-Net’s  original  formulation  are  close  to  negligible  and  instead  we  focus  our efforts on designing an automatic training pipeline for these models.**
* Its encoder part works similarly to a traditional classification CNN in that it successively aggregates semantic information at the expense of reduced spatial information.
* The U-Net does this through the  decoder,  which  receives  semantic  information  from  the  bottom  of  the  ’U’ and  recombines  it  with  higher  resolution  feature  maps  obtained  directly  from the encoder through skip connections.
* Just like the original U-Net, we use two plain convolutional layers between poolings in the encoder and transposed convolution operations in the decoder.
* We deviate from the original architecture in that we replace ReLU activation functions with leaky ReLUs (neg. slope 1e−2) and use instance normalization instead of the more popular batch normalization 

### 2D U-Net
Intuitively,  using  a  2D  U-Net  in  the  context  of  3D  medical  im- age segmentation appears to be suboptimal because valuable information along the  z-axis  cannot  be  aggregated  and  taken  into  consideration.  However,  there is evidence [13] that conventional 3D segmentation methods deteriorate in per- formance  if  the  dataset  is  anisotropic  (cf.  Prostate  dataset  of  the  Decathlon challenge).

### 3D U-Net
A  3D  U-Net  seems  like  the  appropriate  method  of  choice  for  3D image data. In an ideal world, we would train such an architecture on the entire patient’s image. In reality however, we are limited by the amount of available GPU memory which allows us to train this architecture only on image patches. While this is not a problem for datasets comprised of smaller images (in terms of number of voxels per patient) such as the Brain Tumour, Hippocampus and Prostate datasets of this challenge, patch-based training, as dictated by datasets with large images such as Liver, may impede training. This is due to the limited field of view of the architecture which thus cannot collect sufficient contextual information  to  e.g.  correctly  distinguish  parts  of  a  liver  from  parts  of  other organs.

### U-Net Cascade
To  address  this  practical  shortcoming  of  a  3D  U-Net  on datasets with large image sizes, we additionally propose a cascaded model. There- fore, a 3D U-Net is first trained on downsampled images (stage 1). The segmen- tation results of this U-Net are then upsampled to the original voxel spacing and passed as additional (one hot encoded) input channels to a second 3D U-Net, which is trained on patches at full resolution (stage 2). See Figure 1.
 
### Dynamic adaptation of network topologies
Due to the large differences in  image  size  (median  shape  482×512×512  for  Liver  vs.  36×50×35  for Hippocampus) the input patch size and number of pooling operations per axis (and thus implicitly the number of convolutional layers) must be automatically adapted for each dataset to allow for adequate aggregation of spatial information. Apart from adapting to the image geometries, there are technical constraints like the available memory to account for. Our guiding principle in this respect is to dynamically trade off the batch-size versus the network capacity, presented in detail below:

1. We start out with network configurations that we know to be working with our hardware setup. For the 2D U-Net this configuration is an input patch size of 256×256, a batch size of 42 and 30 feature maps in the highest layers (number of feature maps doubles with each downsampling).
2. We automatically adapt these parameters  to  the  median  plane  size  of  each  dataset  (where  we  use  the  plane with  the  lowest  in-plane  spacing,  corresponding  to  the  highest  resolution),  so that the network effectively trains on entire slices. 
> Just like the 2D U-Net, our 3D U-Net uses 30 feature maps at the highest resolution layers
> Here we start with a base configuration of input patch size 128×128×128, and a batch size of  2.  Due  to  memory  constraints,  we  do  not  increase  the  input  patch  volume beyond 1283voxels, but instead match the aspect ratio of the input patch size to that of the median size of the dataset in voxels.
3. If the median shape of the dataset is smaller than 1283then we use the median shape as input patch size and increase the batch size (so that the total number of voxels processed is the same as with 128×128×128 and a batch size of 2)
4. Just like for the 2D U-Net we pool (for a maximum of 5 times) along each axis until the feature maps have size 8.
5. For any network we limit the total number of voxels processed per optimizer step (defined as the input patch volume times the batch size) to a maximum of 5% of the dataset. For cases in excess, we reduce the batch size (with a lower- bound of 2).

## reprocessing
The  preprocessing  is  part  of  the  fully  automated  segmentation  pipeline  that our method consists of and, as such, the steps presented below are carried out without any user intervention.

### Cropping
All data is cropped to the region of nonzero values. This has no effect on most datasets such as liver CT, but will reduce the size (and therefore the computational burden) of skull stripped brain MRI.

### Resampling
* CNNs do not natively understand voxel spacings. In medical im- ages,  it  is  common  for  different  scanners  or  different  acquisition  protocols  to result  in  datasets  with  heterogeneous  voxel  spacings.  
* To  enable  our  networks to  properly  learn  spatial  semantics,  all  patients  are  resampled  to  the  median voxel spacing of their respective dataset, where third order spline interpolation is used for image data and nearest neighbor interpolation for the corresponding segmentation mask.
* Necessity for the U-Net Cascade is determined by the following heuristics: If  the  median  shape  of  the  resampled  data  has  more  than  4  times  the  voxels that  can  be  processed  as  input  patch  by  the  3D  U-Net  (with  a  batch  size  of 2), it qualifies for the U-Net Cascade and this dataset is additionally resampled to  a  lower  resolution.
* If  the dataset  is  anisotropic,  the  higher  resolution  axes  are  first  downsampled  until they match the low resolution axis/axes and only then all axes are downsampled simultaneously. 
* The following datasets of phase 1 fall within the set of described heuristics  and  hence  trigger  usage  of  the  U-Net  Cascade:  Heart,  Liver,  Lung, and Pancreas


### Normalization
* Because  the  intensity  scale  of  CT  scans  is  absolute,  all  CT images are automatically normalized based on statistics of the entire respective dataset: If the modality description in a dataset’s corresponding json desccriptor file indicates ‘ct’, all intensity values occurring within the segmentation masks of  the  training  dataset  are  collected  and  the  entire  dataset  is  normalized  by clipping to the [0.5, 99.5] percentiles of these intensity values, followed by a z- score normalization based on the mean and standard deviation of all collected intensity  values. 
* For  MRI  or  other  image  modalities  (i.e.  if  no  ‘ct’  string  is found in the modality), simple z-score normalization is applied to the patient individually.
* If cropping reduces the average size of patients in a dataset (in voxels) by 1/4 or more the normalization is carried out only within the mask of nonzero elements and all values outside the mask are set to 0

$$L_{total}=L_{dice}+L_{CE}$$

## Training Procedure
* All models are trained from scratch and evaluated using five-fold cross-validation on the training set. We train our networks with a combination of dice and cross- entropy loss
* For 3D U-Nets operating on nearly entire patients (first stage of the U-Net Cascade and 3D U-Net if no cascade is necessary) we compute the dice loss for each sample in the batch and average over the batch
* For all other networks we interpret the samples in the batch as a pseudo-volume and compute the dice loss over all voxels in the batch
* 

## Conclusion
* All design choices required to adapt to a new segmentation task are done  in  a  fully  automatic  manner  with  no  manual  interaction. 
* For  each  task the  nnU-Net  automatically  runs  a  five-fold  cross-validation  for  three  different automatically configures U-Net models and the model (or ensemble) with the highest mean foreground dice score is chosen for final submission
