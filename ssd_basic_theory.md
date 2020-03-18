# Single Shot Detection Algorithm

## Resources

[Undestand and Implement SSD](https://medium.com/@smallfishbigsea/understand-ssd-and-implement-your-own-caa3232cd6ad)

[SSD Paper](https://arxiv.org/abs/1512.02325)

## Receptive fields

[Receptive Fields in Detail](https://medium.com/mlreview/a-guide-to-receptive-field-arithmetic-for-convolutional-neural-networks-e0f514068807)

![Receptive Field](https://cdn-images-1.medium.com/max/1200/1*mModSYik9cD9XJNemdTraw.png)

The input image is 5x5. Here we apply a convolutional filter of kernel **size k=3x3**, padding size **p=1x1**, and stride **s=2x2**. We end up with a **3x3 feature map**. If we apply the same convolution on top of the feature map,  we end up with a **2x2 feature map**. The **receptive field** of a feature is defined as the area of the input image that is covered by that specific feature. If we use the fixed-size CNN visualization, we can see that the receptive filed of the second feature map is much larger than the receptive field of the first feature map.

## Single Shot Detection
![Architecture](https://cdn-images-1.medium.com/max/800/1*rtPAsnXglvZFWa57XkkT4w.jpeg)

In the SSD architecture we take advantage of receptive fields. Shallow layers look at small portions of the input image, while deeper layers look at larger portions. Therefore, we can use shallow layers to classify small objects and deeper layers to classify larger ones. 

### Output

The model produces two outputs for each point in each feature map: a classification output and a regression output. The first one tells us which class belongs to the feature point, the second one tells us the coordinates of the corresponding bounding box.Ge 

#### Generating Priors (Bounding Boxes)

![image-20190313192907978](/Users/alex/Library/Application Support/typora-user-images/image-20190313192907978.png)

For each feature map 4 bounding boxes is generated. The bounding boxes are sometimes referred to as priors or  anchors.

### Training

#### Match anchors with ground truth

During training each bounding box is matched with the ground truth. If the Intersection over Union (IoU) is more than 0.5 the bounding box becomes gets assigned to a class, otherwise it gets assigned to background.

#### Scale Default Boxes

Intuitively, it makes sense to scale the priors so that specific feature maps learn to be sensitive to particular scales of objects.

#### Hard Negative Mining

After matching, most default boxes are negative. This results in an imbalanced dataset. We can select only the background boxes with highest confidence loss to impose a fixed target/background equal to 1/3.

#### Loss Function

The loss function is a combination of a classification loss and a regression loss. 
![equation](https://render.githubusercontent.com/render/math?math=L%28x%2Cc%2Cl%2Cg%29%20%3D%20%5Cfrac%7B1%7D%7BN%7D%28L_%7Bconf%7D%28x%2Cc%29%2B%5Calpha%20L_%7Bloc%7D%28x%2Cl%2Cg%29%29)
N is the number of matched default boxes (or priors).

*The classification loss* is referred as confidence loss and is it a simple categorical cross entropy over the  ground truth labels.

The regression loss is a smoothL1 loss between parameters of the predicted box parameters and the ones of the ground truth box. The parameters of the ground truth box (cx, cy, w, h) are offset by the ones of the matching deafult box. 
![equation](https://render.githubusercontent.com/render/math?math=L_%7Bloc%7D%28x%2Cl%2Cg%29%20%3D%20%5Csum_%7Bi%7D%5E%7BN%7D%5Csum_%7Bm%7Dsmooth_%7BL1%7D%28l_%7Bi%7D%5E%7Bm%7D-%5Chat%7Bg%7D_%7Bi%7D%5E%7Bm%7D%29)

#### Data Augmentation

In order to make the model more robust towards object of various input sizes and shapes, the original dataset is augment by applying sample patching and photo-metric distortions. 

















