# depth-estimation

Setup and Dataset preparation: It first sets up the necessary dependencies and downloads the DIODE: A Dense Indoor and Outdoor Depth Dataset. This dataset is used for training the depth estimation model. The dataset is split into training and evaluation subsets. The dataset contains depth maps and corresponding RGB images. It only uses the indoor images to train the model.

Data Pipeline: The pipeline takes a dataframe containing the path for the RGB images, as well as the depth and depth mask files. It reads and resizes the RGB images and the depth maps. The pipeline returns a batch of RGB images and their corresponding depth maps.

Data visualization: It provides functions to visualize the depth maps and input RGB images. It also shows a 3D point cloud visualization using matplotlib, which is a scatter plot using the depth values and RGB values.

Model Architecture: The architecture is based on U-Net, which is a popular architecture for semantic segmentation tasks. U-Net is a type of Convolutional Neural Network that was developed for biomedical image segmentation at the Computer Science Department of the University of Freiburg, Germany. The architecture consists of a contracting path (encoder) to capture context and a symmetric expanding path (decoder) that allows precise localization. In this case, the model has been modified to include additive skip-connections in the downscaling (encoder) blocks.
DownscaleBlock: This block is designed to gradually reduce the spatial dimensions of the input image while increasing the depth (the number of feature maps). It does so by applying two sets of Convolution-BatchNormalization-LeakyReLU layers, and then applying a Max Pooling operation at the end. The Max Pooling operation effectively halves the height and width of the feature maps, increasing the level of abstraction in the data representation. The residual connection (shortcut connection) is also used in this block.

UpscaleBlock: This block is designed to increase the spatial dimensions of the feature maps. It does so by first applying an UpSampling operation, which doubles the height and width of the feature maps. Then it concatenates the upsampled feature maps with the corresponding feature maps from the downsampling path (the 'skip' connection in U-Net). After that, it applies two sets of Convolution-BatchNormalization-LeakyReLU layers.

BottleNeckBlock: This is the deepest part of the U-Net architecture, where the spatial dimensions are the smallest. This block applies two sets of Convolution-LeakyReLU layers to the input. This part of the network usually captures the most abstract features in the data.
The purpose of having a Bottleneck block is to compress the input data and then let the model learn to reconstruct the input from this compressed representation, making the model better at understanding the input data. It's the part of the model that forces it to learn the most important features.



It also has a custom DepthEstimationModel class that calculates three types of loss: Structural similarity index (SSIM), L1-loss, and Depth smoothness loss.

Training the Model: The model is trained using these losses. Structural similarity index (SSIM) is a method for comparing similarities between two images. The SSIM value can vary between -1 and 1, where 1 means that the test image is identical to the reference image. L1-loss is simply the absolute difference between the predicted and true values. Depth smoothness loss is a custom loss designed to ensure smooth transitions in depth between adjacent pixels.

Testing the Model: After training, the model can be used to predict depth maps from new RGB images.
