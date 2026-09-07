---
site:
  outline_maxdepth: 2
---

# Glossary

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Key terms used in SDS320
</div>
<!-- markdownlint-enable MD033 -->

---

## How to use the glossary

This glossary collects important terms used across SDS320, especially terms related to geospatial data, remote sensing, GeoAI workflows, training data, model evaluation and reproducible project work.

Throughout the Jupyter Book, selected terms are linked with MyST `{term}` roles. When you encounter a linked term, you can return here for a concise explanation.

The definitions are intentionally short. They are meant to help you understand and use the term in your own project, not to replace the lesson pages or official documentation.

<!-- TODO: Add SDS320-specific responsible AI and academic integrity terms from the course policy page if these terms are linked in the book. -->

---

## Glossary entries

```{glossary}

[Absolute Path](https://en.wikipedia.org/wiki/Path_%28computing%29)
: A file path that starts from the root of the file system or drive, such as `/Users/name/project/data/file.tif` or `C:\Users\name\project\data\file.tif`. Absolute paths often break when a project is moved to another computer.

Accuracy
: The proportion of predictions that match the reference labels. It is easy to understand, but it can be misleading when classes are strongly imbalanced.

[Aerial Imagery](https://en.wikipedia.org/wiki/Aerial_photography)
: Image data captured from aircraft or drones. It often has finer {term}`spatial resolution <Spatial Resolution>` than satellite imagery and is useful for mapping small objects such as buildings, trees, vehicles or field boundaries.

[AI Agent](https://en.wikipedia.org/wiki/AI_agent)
: A system that combines models, tools and reasoning steps to carry out a multi-step task. In a {term}`GeoAI` workflow, an agent could help search for imagery, run analysis and prepare maps or summaries.

AI-Assisted Debugging
: The use of an AI tool to help explain errors, suggest checks or improve code during troubleshooting. AI-assisted debugging can be useful, but students remain responsible for testing and understanding any suggested fix.

AlphaEarth
: A Google {term}`satellite embedding <Satellite Embedding>` product providing annual learned per-pixel representations through {term}`Google Earth Engine <Google Earth Engine>` for {term}`downstream tasks <Downstream Task>` such as {term}`classification <Classification>`, similarity analysis and temporal comparison.

[Anaconda](https://en.wikipedia.org/wiki/Anaconda_%28Python_distribution%29)
: A Python distribution that includes conda, Python and many scientific packages. It can be used for SDS320 if it is already installed and working, although {term}`Miniconda` is often lighter.

Anchor Box
: A predefined {term}`bounding-box <Bounding Box>` template used by some detection architectures as a starting point for predicting object location and size.

Annotation
: A label attached to {term}`training data <Training Data>`, such as a class name, polygon, {term}`mask <Mask>` or {term}`bounding box <Bounding Box>`. Annotations define what the model should learn from each image or pixel.

[Application Programming Interface](https://en.wikipedia.org/wiki/API)
: An application programming interface (API) is a structured way for software tools to communicate with each other. In SDS320 projects, APIs may be used to search, download or process spatial data programmatically.

Area of Interest
: An area of interest (AOI) is the spatial extent selected for analysis. A clear AOI helps keep data downloads, {term}`preprocessing <Preprocessing>`, modelling and interpretation feasible.

Attribute
: A non-geometric property stored with a spatial feature, such as a building height, land-cover class or administrative name. Attributes are often used for filtering, grouping and labelling vector data.

[Average Precision](https://en.wikipedia.org/wiki/Evaluation_measures_%28information_retrieval%29#Average_precision)
: Average precision (AP) is the area under a class's {term}`precision-recall curve <Precision-Recall Curve>`, summarizing that class's detection performance across all confidence thresholds into a single number. {term}`Mean Average Precision` averages AP across all classes.

Backbone
: The main {term}`feature-extraction <Feature Extraction>` part of a neural network architecture. In {term}`GeoAI`, a backbone may be reused from a {term}`pre-trained model <Pre-Trained Model>` and adapted to a specific task.

[Band](https://en.wikipedia.org/wiki/Spectral_band)
: One layer of values in raster imagery, often representing a specific wavelength range or derived measurement. Multispectral images contain several bands that can reveal information beyond visible colour.

Band Order
: The order in which image {term}`bands <Band>` are stored or passed into a model. Incorrect band order can make an image look wrong and can reduce model performance.

Basemap
: A background map layer used for spatial context. Basemaps help readers understand where results are located, but they should not distract from the main data.

Batch Inference
: Running a trained model on many images, {term}`tiles <Tile>` or areas in one workflow. Batch inference is useful when applying a model across a larger study area.

[Batch Processing](https://en.wikipedia.org/wiki/Batch_processing)
: Applying the same processing steps to multiple files or areas. It helps make repetitive spatial workflows more efficient and reproducible.

Binary Segmentation
: A {term}`semantic segmentation <Semantic Segmentation>` task with two classes, typically a target class and background.

Bitemporal Analysis
: {term}`Change detection <Change Detection>` based on comparing exactly two images from two different dates, as opposed to {term}`multitemporal analysis <Multitemporal Analysis>`, which uses a longer series of dates. Bitemporal comparison works well for abrupt change but can struggle with gradual, slow-moving change.

Bitemporal Change Detection
: {term}`Change detection <Change Detection>` based on two observations of the same area, commonly described as a before image and an after image.

[Bounding Box](https://en.wikipedia.org/wiki/Minimum_bounding_rectangle)
: A rectangular {term}`annotation <Annotation>` or prediction that marks the location of an object in an image. Bounding boxes are commonly used in {term}`object detection <Object Detection>` tasks.

[Branch](https://en.wikipedia.org/wiki/Branching_%28version_control%29)
: A separate line of development in a {term}`Git` {term}`repository <Repository>`. Branches are useful for trying changes without immediately affecting the main version of a project.

Buffer Radius
: A distance used to expand or soften a geometry or label boundary. In {term}`training-data <Training Data>` preparation, a buffer can sometimes reduce problems caused by small spatial misalignments.

Canopy Height
: The height of vegetation above the ground surface. {term}`GeoAI` workflows may estimate canopy height from aerial, satellite or LiDAR-derived data.

[Change Detection](https://en.wikipedia.org/wiki/Change_detection)
: The task of identifying where and sometimes how a place has changed between two or more observation times. It is used for topics such as urban expansion, deforestation, disaster damage and seasonal dynamics.

Change Vector Analysis
: Change vector analysis (CVA) is a multi-{term}`band <Band>` {term}`change detection <Change Detection>` technique that treats each pixel as a vector across multiple {term}`spectral bands <Spectral Band>`, computing a magnitude (how much a pixel changed) and a direction (what kind of change likely occurred) between two dates.

Changen2
: A pre-training approach for {term}`change detection <Change Detection>` that generates diverse synthetic change examples from single-temporal imagery, providing weights that can be transferred to downstream change-detection models.

ChangeStar
: A {term}`change detection <Change Detection>` architecture that jointly performs change detection and {term}`semantic segmentation <Semantic Segmentation>`, producing a change map plus per-date segmentation {term}`masks <Mask>`. It uses {term}`Changen2` pretraining, which generalizes across regions without requiring {term}`fine-tuning <Fine-Tuning>` on new imagery.

Checkpoint
: A saved version of a model during or after training. Checkpoints make it possible to reuse a trained model, resume training or compare model versions.

Chesapeake Land Cover
: A 13-class land-cover {term}`classification <Classification>` scheme for the Chesapeake Bay watershed, pairing {term}`NAIP` {term}`aerial imagery <Aerial Imagery>` with integer-valued land-cover label rasters. Used in `geoai` as a benchmark for {term}`multi-class segmentation <Multi-Class Segmentation>`.

Chip
: A smaller image {term}`tile <Tile>` cut from a larger raster {term}`scene <Scene>`. Chips make large satellite or aerial images manageable for neural networks.

Class Imbalance
: A situation where some classes occupy far more of a dataset than others. In segmentation, class imbalance can make aggregate metrics look strong while performance on rare classes remains poor.

[Classification](https://en.wikipedia.org/wiki/Statistical_classification)
: The task of assigning categories to observations. In {term}`GeoAI`, classification may refer to image-level {term}`scene <Scene>` labels or pixel-level land-cover labels, depending on the task.

Classification Head
: The final part of a {term}`classification <Classification>` model that maps learned features to class scores or probabilities.

Clay Foundation Model
: A geospatial {term}`foundation model <Foundation Model>` designed to learn reusable representations from {term}`Earth-observation <Earth Observation>` imagery. Precomputed Clay {term}`embeddings <Embedding>` can be used as features for downstream spatial analyses.

[CLIP](https://en.wikipedia.org/wiki/Contrastive_Language-Image_Pre-training)
: A {term}`vision-language model <Vision-Language Model>` family that connects images and text in a shared representation space. CLIP-style models can support tasks such as image-text matching, zero-shot {term}`classification <Classification>` and text-guided search.

Clone
: A local copy of a remote {term}`Git` {term}`repository <Repository>`. Cloning downloads the repository so you can work on it on your own computer.

[Cloud Optimized GeoTIFF](https://en.wikipedia.org/wiki/GeoTIFF#Cloud_Optimized_GeoTIFF)
: A Cloud Optimized GeoTIFF (COG) is a {term}`GeoTIFF` structured so that parts of the raster can be read efficiently over the web. COGs are useful for cloud-based and large-scale geospatial workflows.

Cloud Removal
: An {term}`image-translation <Image Translation>` task that attempts to replace cloud-covered areas in optical imagery with plausible cloud-free content. The result should be interpreted carefully because the missing information is inferred.

Co-registration
: The alignment of two or more images so that the same pixel location corresponds to the same real-world location across all of them. Poor co-registration is a common cause of false changes in {term}`change detection <Change Detection>`.

COCO Format
: A common {term}`annotation <Annotation>` format for computer vision datasets, especially {term}`object detection <Object Detection>` and segmentation. It stores information such as images, classes, {term}`bounding boxes <Bounding Box>` and {term}`masks <Mask>` in a structured JSON file.

Colormap
: A mapping from data values to colours. Colormaps influence how patterns are perceived and should be chosen to match the type of data and message.

[Command Line](https://en.wikipedia.org/wiki/Command-line_interface)
: A text-based interface for running commands such as creating folders, activating environments or starting {term}`JupyterLab`. On Windows this may be {term}`PowerShell` or {term}`Anaconda` {term}`Prompt`; on macOS and Linux it is usually {term}`Terminal`.

[Commit](https://en.wikipedia.org/wiki/Commit_%28version_control%29)
: A saved snapshot of selected changes in a {term}`Git` {term}`repository <Repository>`. Good commits represent meaningful project steps and include clear messages.

Conda Environment
: An isolated software environment containing a specific Python version and package set. It helps prevent package conflicts between projects.

[Conditional](https://en.wikipedia.org/wiki/Conditional_%28computer_programming%29)
: A programming structure that runs code only when a condition is met. In Python, conditionals usually use `if`, `elif` and `else`.

Confidence Score
: A model output that indicates how confident the model is in a prediction. Confidence scores are useful for filtering detections, but they are not the same as proof that a prediction is correct.

[Confusion Matrix](https://en.wikipedia.org/wiki/Confusion_matrix)
: A table comparing predicted classes with reference classes. It helps reveal which classes are confused with each other.

[Convolutional Neural Network](https://en.wikipedia.org/wiki/Convolutional_neural_network)
: A convolutional neural network (CNN) is a neural network architecture designed to learn spatial patterns from image-like data. CNNs are widely used for image {term}`classification <Classification>`, {term}`object detection <Object Detection>` and segmentation.

[Coordinate Reference System](https://en.wikipedia.org/wiki/Spatial_reference_system)
: A coordinate reference system (CRS) describes how spatial coordinates or geometries relate to real locations on Earth. Correct CRS handling is necessary when combining, measuring or mapping spatial datasets.

[Cosine Similarity](https://en.wikipedia.org/wiki/Cosine_similarity)
: A measure of how similar two vectors are, based on the angle between them rather than their magnitude. It ranges from -1 to 1, with 1 indicating identical direction (maximum similarity).

[CUDA](https://en.wikipedia.org/wiki/CUDA)
: NVIDIA's computing platform for running code on compatible GPUs. In {term}`GeoAI`, CUDA can accelerate {term}`deep learning <Deep Learning>` training and {term}`inference <Inference>` when the hardware and software environment are configured correctly.

[Data Leakage](https://en.wikipedia.org/wiki/Leakage_%28machine_learning%29)
: A situation where information from validation or test data unintentionally influences training. In spatial projects, leakage can happen when nearby or overlapping areas are split randomly across training and test sets.

Data Pipeline
: A connected sequence of steps that turns raw data into outputs such as model-ready datasets, predictions, figures or maps. A clear pipeline makes a project easier to debug, reproduce and explain.

[DataFrame](https://en.wikipedia.org/wiki/Pandas_%28software%29#Dataframes)
: A table-like data structure commonly used in Pandas. DataFrames are useful for storing dataset inventories, {term}`attributes <Attribute>`, model results and summary statistics.

[Debugging](https://en.wikipedia.org/wiki/Debugging)
: The process of finding, understanding and fixing problems in code or workflows. In SDS320, debugging also includes checking spatial assumptions such as {term}`CRS <Coordinate Reference System>`, units, overlap and data quality.

Decoder
: The part of an encoder-decoder segmentation network that reconstructs a compressed feature representation back to the input's original {term}`spatial resolution <Spatial Resolution>`, producing the final pixel-level prediction.

[Deep Learning](https://en.wikipedia.org/wiki/Deep_learning)
: A branch of machine learning that uses neural networks with many layers to learn patterns from data. In {term}`GeoAI`, deep learning is often used for imagery tasks such as detection, segmentation and {term}`change detection <Change Detection>`.

DeepLabV3+
: A segmentation architecture that uses dilated (atrous) convolutions to capture multi-scale context without reducing {term}`spatial resolution <Spatial Resolution>` as much as standard convolutions, combined with a {term}`decoder <Decoder>` for improved boundary detail.

[DETR](https://en.wikipedia.org/wiki/DETR-based_algorithms)
: A {term}`transformer <Transformer>`-based {term}`object detection <Object Detection>` architecture (DEtection TRansformer) that predicts a fixed-size set of detections directly, without {term}`anchor boxes <Anchor Box>` or {term}`non-maximum suppression <Non-Maximum Suppression>`.

[Dictionary](https://en.wikipedia.org/wiki/Associative_array)
: A Python data structure that stores key-value pairs. Dictionaries are useful for project settings because they keep related parameters together.

[Diffusion Model](https://en.wikipedia.org/wiki/Diffusion_model)
: A generative model that learns to produce images by reversing a gradual noise-corruption process, starting from random noise and progressively generating a plausible image, optionally conditioned on an input such as a lower-resolution image.

[Digital Elevation Model](https://en.wikipedia.org/wiki/Digital_elevation_model)
: A digital elevation model (DEM) is a raster representation of elevation values. DEMs can support terrain analysis, hydrological modelling and contextual features for spatial prediction.

Downstream Task
: A specific task that uses outputs or representations from a model, such as {term}`classification <Classification>`, segmentation or similarity search. {term}`Foundation models <Foundation Model>` are often adapted to downstream tasks.

[DRY Principle](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)
: A programming principle meaning “Don’t Repeat Yourself”. In project code, repeated logic should often be turned into a function or reusable script.

[Early Stopping](https://en.wikipedia.org/wiki/Early_stopping)
: A training strategy that halts optimization when a monitored validation metric has not improved for a defined number of epochs, reducing unnecessary training after validation performance has plateaued.

[Earth Observation](https://en.wikipedia.org/wiki/Earth_observation)
: Earth observation (EO) is the collection of information about Earth’s surface, atmosphere or oceans using {term}`sensors <Sensor>` on satellites, aircraft or drones. EO data are a major input for spatial analytics and {term}`GeoAI`.

[EfficientNet](https://en.wikipedia.org/wiki/EfficientNet)
: A {term}`convolutional neural network <Convolutional Neural Network>` family that scales network depth, width, and input resolution together using a single coefficient, aiming for strong {term}`accuracy <Accuracy>` with fewer parameters than comparably accurate architectures. EfficientNet-B0 is the smallest and fastest variant in the family.

Embedding
: A fixed-length numerical vector, produced by a {term}`foundation model <Foundation Model>`, that summarizes the content of an image {term}`patch <Patch>` or pixel location. Embeddings are feature representations rather than direct predictions; locations with similar characteristics have embeddings that are close together in the {term}`embedding space <Embedding Space>`.

Embedding Space
: The high-dimensional feature space in which each {term}`embedding <Embedding>` vector represents one image {term}`patch <Patch>`, pixel or other observation.

Encoder-Decoder Architecture
: A neural network structure that first compresses an input into features and then reconstructs an output. It is common in segmentation, {term}`image translation <Image Translation>` and {term}`pixel-level regression <Pixel-Level Regression>`.

Environment File
: A file, often named `environment.yml`, that lists the Python version, packages and channels needed to recreate a {term}`conda environment <Conda Environment>`. It helps make a project easier to reproduce.

Epoch
: One full pass through the training dataset during model training. Training for more epochs can improve learning, but can also increase {term}`overfitting <Overfitting>`.

[Error Message](https://en.wikipedia.org/wiki/Error_message)
: Text produced by software when something fails. A useful error message often indicates the error type, the line that failed and the object or file involved.

EuroSAT
: A land-use and land-cover {term}`classification <Classification>` dataset derived from {term}`Sentinel-2` imagery. In SDS320, it is useful as a compact benchmark for learning {term}`image-recognition <Image Recognition>` workflows.

Evaluation Metric
: A quantitative measure used to assess model performance. The choice of metric should match the task, data and project question.

External LLM
: A {term}`large language model <Large Language Model>` accessed outside the course environment, often through a web interface or external application. External LLMs can help explain code and errors, but sensitive data and credentials should not be shared with them.

[F1-score](https://en.wikipedia.org/wiki/F-score)
: A metric that combines {term}`precision <Precision>` and {term}`recall <Recall>` into a single value. It is useful when both missed detections and false alarms matter.

[False Negative](https://en.wikipedia.org/wiki/False_positives_and_false_negatives)
: A case where the model misses something that is present in the reference data. In spatial applications, false negatives can mean missed buildings, missed changes or missed damaged areas.

[False Positive](https://en.wikipedia.org/wiki/False_positives_and_false_negatives)
: A case where the model predicts something that is not present in the reference data. False positives can create misleading maps or overestimate the extent of a feature.

[Faster R-CNN](https://en.wikipedia.org/wiki/Region_Based_Convolutional_Neural_Networks#Faster_R-CNN)
: A two-stage {term}`object detection <Object Detection>` architecture that first proposes candidate regions with a {term}`Region Proposal Network <Region Proposal Network>`, then classifies and refines each proposed region. It is the default detection architecture in the `geoai` package.

[Feature Engineering](https://en.wikipedia.org/wiki/Feature_engineering)
: The process of creating input variables that help a model learn. Traditional machine learning often relies more heavily on hand-crafted features than {term}`deep learning <Deep Learning>`.

[Feature Extraction](https://en.wikipedia.org/wiki/Feature_extraction)
: The process of deriving useful patterns or representations from data. Neural networks learn features from imagery, while spatial workflows may also extract hand-crafted features such as indices, textures or geometry measures.

Feature Pyramid Network
: A feature pyramid network (FPN) is a neural network component that combines features across multiple spatial scales. FPNs are useful for detecting objects of different sizes in imagery.

Fields of The World
: Fields of The World (FTW) is a benchmark dataset for agricultural field-boundary mapping that pairs {term}`Sentinel-2` imagery with instance {term}`masks <Mask>` representing individual field parcels across multiple countries.

[Fine-Tuning](https://en.wikipedia.org/wiki/Fine-tuning_%28deep_learning%29)
: Adapting a {term}`pre-trained model <Pre-Trained Model>` to a new dataset or task through additional training. Fine-tuning is useful when labelled project data are limited.

[Foundation Model](https://en.wikipedia.org/wiki/Foundation_model)
: A large model pre-trained on broad datasets and adaptable to many {term}`downstream tasks <Downstream Task>`. In {term}`GeoAI`, foundation models can reduce the amount of task-specific labelled data needed.

Fully Convolutional One-Stage
: Fully Convolutional One-Stage (FCOS) is an anchor-free, single-stage {term}`object detection <Object Detection>` architecture that predicts {term}`bounding boxes <Bounding Box>` directly at each spatial location. It is supported in `geoai` as `fcos_resnet50_fpn`.

[Function](https://en.wikipedia.org/wiki/Function_%28computer_programming%29)
: A reusable block of code that performs a specific task. Functions make workflows easier to read, test and repeat.

[GDAL/OGR](https://en.wikipedia.org/wiki/GDAL)
: A widely used geospatial library for reading, writing and transforming raster and vector data. Many Python geospatial tools rely on GDAL or OGR internally.

[Generative Adversarial Network](https://en.wikipedia.org/wiki/Generative_adversarial_network)
: A generative adversarial network (GAN) is a {term}`model architecture <Model Architecture>` in which a generator network produces candidate outputs and a discriminator network tries to distinguish them from real examples. Training pushes the generator toward increasingly realistic output. Pix2Pix and CycleGAN are well-known GAN-based {term}`image translation <Image Translation>` frameworks.

GeoAI
: The combination of geospatial data science and artificial intelligence to analyse, interpret or generate insights from spatial data. GeoAI connects geographic domain knowledge with machine learning and {term}`deep learning <Deep Learning>` methods.

GeoDataFrame
: A {term}`GeoPandas` table that includes a geometry column. It allows tabular operations and spatial operations to be used together.

[GeoJSON](https://en.wikipedia.org/wiki/GeoJSON)
: A text-based vector data format for storing geographic features and their {term}`attributes <Attribute>`. It is easy to read and useful for web and lightweight exchange workflows.

[GeoPackage](https://en.wikipedia.org/wiki/GeoPackage)
: A file-based geospatial data format that can store vector layers, raster data and related tables. It is often more robust than older {term}`shapefile <Shapefile>` workflows.

GeoPandas
: A Python library that extends Pandas with support for vector geometries. It is commonly used for reading, analysing and writing vector data.

GeoParquet
: A columnar geospatial data format based on Parquet. It is useful for efficient storage and analysis of large vector datasets.

[Georeferencing](https://en.wikipedia.org/wiki/Georeferencing)
: The process of connecting image pixels or geometries to real-world coordinates. Georeferencing is what makes a raster or vector dataset spatially meaningful.

[GeoTIFF](https://en.wikipedia.org/wiki/GeoTIFF)
: A raster file format that stores image values together with geospatial {term}`metadata <Metadata>` such as {term}`CRS <Coordinate Reference System>`, transform and extent. It is one of the most common formats for satellite and {term}`aerial imagery <Aerial Imagery>`.

[Git](https://en.wikipedia.org/wiki/Git)
: A {term}`version-control <Version Control>` system used to track changes in files over time. In SDS320, Git supports project development and helps prepare the final public {term}`repository <Repository>`.

[GitHub](https://en.wikipedia.org/wiki/GitHub)
: An online platform for hosting {term}`Git` {term}`repositories <Repository>`. In SDS320, GitHub can be used to share the final public project repository.

Gitignore File
: A file named `.gitignore` that tells {term}`Git` which files or folders should not be tracked. It is useful for excluding large data, temporary files, credentials and local system files.

[GitLab](https://en.wikipedia.org/wiki/GitLab)
: An online platform for hosting {term}`Git` {term}`repositories <Repository>`. In SDS320, GitLab can be used to share the final public project repository if allowed by the course instructions.

[Google Earth Engine](https://en.wikipedia.org/wiki/Google_Earth#Google_Earth_Engine)
: Google Earth Engine (GEE) is a cloud-based platform for planetary-scale geospatial analysis, providing access to large public datasets, including {term}`AlphaEarth`, without requiring the data to be downloaded locally.

[GPU](https://en.wikipedia.org/wiki/Graphics_processing_unit)
: A graphics processing unit that can accelerate parallel computations. In {term}`deep learning <Deep Learning>`, GPUs can greatly reduce training and {term}`inference <Inference>` time.

[Ground Truth](https://en.wikipedia.org/wiki/Ground_truth)
: Reference information used to train or evaluate a model. Ground truth may come from field data, manual labels, trusted maps or carefully checked {term}`annotations <Annotation>`.

Hidden Notebook State
: A situation where a notebook works only because cells were run in a particular previous order. Hidden notebook state makes a workflow difficult to reproduce and can often be detected by restarting the kernel and running all cells from top to bottom.

[Huber Loss](https://en.wikipedia.org/wiki/Huber_loss)
: A {term}`loss function <Loss Function>` that behaves like {term}`MSE <Mean Squared Error>` for small errors and like {term}`MAE <Mean Absolute Error>` for large errors, combining smooth gradients near zero with reduced sensitivity to outliers. It is a common default for {term}`regression <Regression>` targets with noisy or imperfect reference data.

[Hugging Face Hub](https://en.wikipedia.org/wiki/Hugging_Face)
: An online platform for hosting and sharing trained model weights and configuration files. In `geoai`, `push_detector_to_hub()` and `predict_detector_from_hub()` support publishing and reusing trained detectors through the Hub.

[Hyperparameter](https://en.wikipedia.org/wiki/Hyperparameter_%28machine_learning%29)
: A setting chosen before or during training, such as learning rate, batch size, number of epochs or {term}`model architecture <Model Architecture>`. Hyperparameters affect model behaviour but are not learned directly from the data.

[Image Captioning](https://en.wikipedia.org/wiki/Natural_language_generation#Image_captioning)
: A vision-language task where a model generates a text description of an image. In geospatial work, captions should be checked carefully because model descriptions may miss spatial context or {term}`uncertainty <Uncertainty>`.

Image Chip
: A small image {term}`tile <Tile>` extracted from a larger image. Image {term}`chips <Chip>` are used to make large geospatial rasters compatible with model input sizes.

Image Differencing
: The simplest {term}`change detection <Change Detection>` technique: subtracting pixel values of one image from the corresponding pixel values of another, then thresholding the result to separate changed from unchanged areas.

Image Embedding
: A high-dimensional feature representation of an entire image, produced once by an image encoder and cached for reuse. In {term}`SAM <Segment Anything Model>`, the image embedding captures shapes, textures, and spatial relationships, and is combined with each new {term}`prompt <Prompt>` without needing to be recomputed.

[Image Recognition](https://en.wikipedia.org/wiki/Image_recognition)
: The task of assigning one or more labels to an entire image or {term}`chip <Chip>`. It is useful when the goal is to classify {term}`scenes <Scene>` rather than locate individual pixels or objects.

[Image Translation](https://en.wikipedia.org/wiki/Image-to-image_translation)
: The task of transforming an image from one representation to another while preserving spatial structure. Examples include {term}`super-resolution <Super-Resolution>`, {term}`cloud removal <Cloud Removal>` and {term}`sensor <Sensor>` translation.

ImageFolder
: A simple image-{term}`classification <Classification>` dataset layout where each class is represented by one folder, and all images inside that folder receive the folder name as their label.

Import
: A Python statement that loads a package, module or function so it can be used in the current script or notebook.

[Inference](https://en.wikipedia.org/wiki/Machine_learning#Inference)
: The process of applying a trained model to new data to produce predictions. Inference outputs still need validation, interpretation and often {term}`post-processing <Post-Processing>`.

Instance Identifier
: A unique label assigned to an individual object in {term}`instance segmentation <Instance Segmentation>`. It allows adjacent objects of the same class to be counted or analysed separately.

[Instance Segmentation](https://en.wikipedia.org/wiki/Image_segmentation#Instance_segmentation)
: A task that classifies pixels while also separating individual objects. It is useful when objects need distinct boundaries, counts or measurements.

Intermediate Output
: A result created during a workflow before the final output. Checking intermediate outputs helps detect mistakes early.

Internal Agent
: A course- or institution-provided AI assistant that can support specific workflows under defined access, privacy and usage rules. Internal agents may be preferable for course-specific or sensitive questions if provided.

[Intersection over Union](https://en.wikipedia.org/wiki/Jaccard_index)
: Intersection over Union (IoU) is a metric that compares the overlap between a predicted object or {term}`mask <Mask>` and the reference object or mask. Higher IoU means stronger spatial agreement.

Jupyter Kernel
: The Python process that runs code inside a {term}`Jupyter notebook <Jupyter Notebook>`. Choosing the correct kernel ensures that the notebook uses the intended {term}`conda environment <Conda Environment>` and packages.

[Jupyter Notebook](https://en.wikipedia.org/wiki/Project_Jupyter#Jupyter_Notebook)
: An interactive document that combines code, output, text and figures. Notebooks are useful for exploration, explanation and reproducible project narratives.

[JupyterLab](https://en.wikipedia.org/wiki/Project_Jupyter#JupyterLab)
: A browser-based working environment for notebooks, code, {term}`terminals <Terminal>` and files. It is useful for interactive exploration, setup checks and visual project work.

[K-Means Clustering](https://en.wikipedia.org/wiki/K-means_clustering)
: An unsupervised algorithm that partitions data into a fixed number of clusters by minimizing the variance within each cluster, without requiring any labels.

[k-Nearest Neighbors](https://en.wikipedia.org/wiki/K-nearest_neighbors_algorithm)
: k-nearest neighbors (k-NN) is a {term}`classification <Classification>` method that predicts a new point's class based on the majority class among its closest neighbors in feature space, here applied to {term}`embedding <Embedding>` vectors.

Land Cover Classification
: The task of assigning land-cover classes such as water, vegetation, built-up area or bare soil. It can be performed at {term}`scene <Scene>` level or pixel level depending on the project question.

[Landsat](https://en.wikipedia.org/wiki/Landsat_program)
: A long-running {term}`Earth observation <Earth Observation>` satellite programme that provides global imagery useful for long-term change analysis. In SDS320, Landsat is often useful when temporal depth matters more than fine spatial detail.

[Large Language Model](https://en.wikipedia.org/wiki/Large_language_model)
: A large language model (LLM) is an AI model trained to process and generate text. LLMs can help explain code, draft {term}`debugging <Debugging>` plans and suggest alternatives, but their outputs must be checked carefully.

Latent Diffusion Model
: A latent diffusion model (LDM) is a {term}`diffusion model <Diffusion Model>` that runs its denoising process in a compressed latent representation, produced by an encoder, rather than directly on full-resolution pixels, then decodes the result back to pixel space. This substantially reduces computational cost compared with running diffusion on raw pixels.

LDSR-S2
: A latent-diffusion {term}`super-resolution <Super-Resolution>` model designed for four-{term}`band <Band>` {term}`Sentinel-2` imagery, producing 4× finer output from Red, Green, Blue and {term}`near-infrared <Near-Infrared>` inputs.

Leafmap
: A Python package for interactive geospatial mapping in notebooks. It is useful for exploring imagery, vector data, model outputs and map comparisons.

List
: A Python data structure that stores multiple values in order. Lists are useful for processing several files, {term}`bands <Band>`, years, classes or {term}`tiles <Tile>`.

[Logistic Regression](https://en.wikipedia.org/wiki/Logistic_regression)
: A {term}`classification <Classification>` method that fits a linear decision boundary to separate classes, estimating the probability that a given input belongs to each class.

Loop
: A programming structure that repeats code for several items. Loops are useful for applying the same operation to multiple files, years, {term}`bands <Band>` or geometries.

[Loss Function](https://en.wikipedia.org/wiki/Loss_function)
: A function that measures how different model predictions are from reference labels during training. The model uses this signal to update its parameters.

Markdown Cell
: A notebook cell that contains formatted text instead of executable code. Markdown cells are useful for explaining goals, decisions, outputs and interpretation in a reproducible {term}`notebook narrative <Notebook Narrative>`.

Mask
: A raster layer that identifies pixels belonging to a class, object or region of interest. Masks are common labels and outputs in segmentation workflows.

Mask Decoder
: The component of {term}`SAM <Segment Anything Model>` that combines a cached {term}`image embedding <Image Embedding>` with an encoded {term}`prompt <Prompt>` to produce candidate segmentation {term}`masks <Mask>` and their {term}`confidence scores <Confidence Score>`.

Mask Head
: The branch of {term}`Mask R-CNN` that predicts a small, fixed-size binary {term}`mask <Mask>` for each detected object, run in parallel with the {term}`classification <Classification>` and {term}`bounding-box <Bounding Box>` heads. It is the component that distinguishes Mask R-CNN from {term}`Faster R-CNN`.

[Mask R-CNN](https://en.wikipedia.org/wiki/Region_Based_Convolutional_Neural_Networks#Mask_R-CNN)
: A {term}`deep learning <Deep Learning>` architecture for {term}`instance segmentation <Instance Segmentation>`. It detects objects and produces a separate {term}`mask <Mask>` for each detected instance.

[Mean Absolute Error](https://en.wikipedia.org/wiki/Mean_absolute_error)
: Mean absolute error (MAE) is a {term}`regression <Regression>` metric that measures the average absolute difference between predicted and reference values. MAE is often easier to interpret than squared-error metrics because it uses the original units.

Mean Average Precision
: Mean average precision (mAP) is a detection or segmentation {term}`evaluation metric <Evaluation Metric>` that summarizes {term}`precision <Precision>` across confidence thresholds, classes or {term}`IoU <Intersection over Union>` thresholds. It is common in {term}`object detection <Object Detection>` benchmarks.

[Mean Squared Error](https://en.wikipedia.org/wiki/Mean_squared_error)
: Mean squared error (MSE) is a {term}`loss function <Loss Function>` that averages the squared differences between predicted and actual values. MSE is sensitive to outliers because large errors are penalized disproportionately.

Merge Conflict
: A {term}`Git` situation where changes from different versions affect the same part of a file and Git cannot decide automatically which version to keep. Merge conflicts need to be resolved manually.

[Metadata](https://en.wikipedia.org/wiki/Metadata)
: Information that describes a dataset, such as {term}`CRS <Coordinate Reference System>`, resolution, acquisition date, {term}`band <Band>` names, licence or {term}`sensor <Sensor>`. Metadata is necessary for correct processing and documentation.

[Miniconda](https://en.wikipedia.org/wiki/Anaconda_%28Python_distribution%29#Conda)
: A lightweight installer for Python and the conda {term}`package manager <Package Manager>`. It is a practical way to create isolated Python environments without installing many extra packages by default.

Mixed Pixel
: A pixel whose footprint on the ground covers more than one land-cover type or feature, so its spectral value reflects a blend rather than any single class. Mixed pixels limit how small a change can be reliably detected at a given {term}`spatial resolution <Spatial Resolution>`.

Model Architecture
: The structure of a model, including how layers and components are arranged. Architecture choices influence what patterns a model can learn and how it processes data.

Model Generalization
: A model’s ability to perform well on new data beyond the examples used during training. In {term}`GeoAI`, generalization across regions, seasons, {term}`sensors <Sensor>` and resolutions is often difficult.

Module
: A Python file or package that contains reusable code. Modules help organise functions and keep notebooks shorter.

Multi-Class Segmentation
: A {term}`semantic segmentation <Semantic Segmentation>` task in which each pixel is assigned to one of three or more classes.

Multiband Imagery
: Raster imagery with more than one {term}`band <Band>`. Multiband data can store visible colours, infrared bands, thermal bands or derived variables.

[Multispectral Imagery](https://en.wikipedia.org/wiki/Multispectral_imaging)
: Imagery captured in several wavelength ranges, often including visible and infrared {term}`bands <Band>`. It is central to many {term}`remote sensing <Remote Sensing>` workflows because different surfaces reflect wavelengths differently.

Multitemporal Analysis
: Analysis using three or more observation times to study temporal patterns such as trends, repeated events, gradual change or recovery.

NAIP
: The National Agriculture Imagery Program, which provides high-resolution {term}`aerial imagery <Aerial Imagery>` for the United States. NAIP is useful for projects that need fine spatial detail within its coverage area.

Natural Language Prompt
: A text instruction or query given to a model. Vision-language and segmentation models may use {term}`prompts <Prompt>` to guide what they should identify or describe.

[Near-Infrared](https://en.wikipedia.org/wiki/Infrared#Near-infrared)
: Near-infrared (NIR) is a spectral region just beyond visible red light. NIR is widely used in vegetation analysis because healthy vegetation strongly reflects near-infrared radiation.

NoData Value
: A value used in raster data to mark pixels with missing or invalid information. NoData values should usually be handled before analysis or model training.

Non-Maximum Suppression
: A {term}`post-processing <Post-Processing>` method that removes lower-confidence {term}`bounding boxes <Bounding Box>` when they overlap strongly with a higher-confidence prediction of the same class.

[Normalized Difference Vegetation Index](https://en.wikipedia.org/wiki/Normalized_difference_vegetation_index)
: The normalized difference vegetation index (NDVI) is a {term}`spectral index <Spectral Index>` computed from red and {term}`near-infrared <Near-Infrared>` reflectance, ranging from about −1 to 1 and commonly used to measure vegetation greenness and density. Higher values generally indicate denser, healthier vegetation.

Notebook Narrative
: The written explanation that connects code cells, outputs and decisions inside a notebook. A clear notebook narrative helps readers understand why each step was taken and what the results mean.

NWPU-VHR-10
: A benchmark dataset of 800 very-high-resolution {term}`remote sensing <Remote Sensing>` images (650 annotated, 150 background-only) covering 10 object classes, commonly used to evaluate multi-class {term}`object detection <Object Detection>` models.

[Object Detection](https://en.wikipedia.org/wiki/Object_detection)
: The task of locating and classifying discrete objects in an image, usually with {term}`bounding boxes <Bounding Box>`. It is useful for counting and mapping objects such as vehicles, ships, trees or buildings.

OmniCloudMask
: A {term}`sensor <Sensor>`-agnostic, pre-trained cloud and cloud-shadow detection model that classifies pixels as clear, thick cloud, thin cloud, or cloud shadow using red, green, and {term}`near-infrared <Near-Infrared>` {term}`bands <Band>`. Accessible in `geoai` through `predict_cloud_mask_from_raster()`.

OmniWaterMask
: A {term}`sensor <Sensor>`-agnostic, pre-trained water segmentation model that combines {term}`deep learning <Deep Learning>` with a spectral water index and {term}`OpenStreetMap <OpenStreetMap>` reference data. Accessible in `geoai` through `segment_water()`.

[OpenStreetMap](https://en.wikipedia.org/wiki/OpenStreetMap)
: OpenStreetMap (OSM) is a collaborative global vector dataset containing features such as roads, buildings, land use and points of interest. OSM can provide useful context data, but quality and completeness vary by location.

Orthogonalization
: A {term}`post-processing <Post-Processing>` step that converts a raster segmentation {term}`mask <Mask>` into vector polygons and regularizes their edges into clean, right-angled shapes, better representing the geometry of features such as buildings.

[Orthomosaic](https://en.wikipedia.org/wiki/Orthophoto#Orthophotomosaic_and_orthophotomap)
: A spatially corrected image mosaic made from overlapping aerial or drone images. Orthomosaics can provide high-resolution base imagery for mapping and {term}`annotation <Annotation>`.

[Overfitting](https://en.wikipedia.org/wiki/Overfitting)
: A situation where a model learns the {term}`training data <Training Data>` too closely and performs poorly on new data. Overfitting is a risk when data are limited, imbalanced or spatially similar.

[Package Manager](https://en.wikipedia.org/wiki/Package_manager)
: A tool used to install, update and manage software packages. Conda is recommended in SDS320 because it can handle many geospatial dependencies together.

Paired Image Translation
: {term}`Image translation <Image Translation>` learned from corresponding input and target images that represent the same {term}`scene <Scene>` or content in two different domains.

[Panoptic Segmentation](https://en.wikipedia.org/wiki/Image_segmentation#Panoptic_segmentation)
: A segmentation task that combines semantic and instance information, assigning semantic classes to all pixels while also distinguishing individual countable objects with unique instance identities.

[Pansharpening](https://en.wikipedia.org/wiki/Pansharpened_image)
: An image fusion technique that combines high-resolution panchromatic imagery with lower-resolution {term}`multispectral imagery <Multispectral Imagery>`. It aims to improve spatial detail while preserving spectral information.

Pascal VOC Format
: An {term}`annotation <Annotation>` format commonly used for {term}`object detection <Object Detection>` and {term}`instance segmentation <Instance Segmentation>` datasets. It typically stores object labels and {term}`bounding boxes <Bounding Box>` in XML files.

Patch
: A small subset of an image used for model input or analysis. The term is often used similarly to {term}`chip <Chip>` or {term}`tile <Tile>`.

Patch-Based Embedding
: A fixed-length feature vector representing an entire image {term}`patch <Patch>` or {term}`tile <Tile>` as one observation.

Pathlib
: A Python module for working with file and folder paths. It helps create paths that are easier to read and more portable across operating systems.

[Pearson Correlation](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient)
: A measure of the strength and direction of a linear relationship between predicted and reference values. High correlation does not by itself imply unbiased agreement.

[Phenology](https://en.wikipedia.org/wiki/Phenology)
: The study of cyclical, seasonal biological changes, such as leaf-out and leaf-drop in vegetation. Phenological variation is a common source of spectral difference between images that is unrelated to genuine land-cover change.

Pixel-Based Embedding
: A feature vector assigned to each pixel or grid cell, preserving local spatial variation across a raster.

Pixel-Level Regression
: The task of predicting a continuous value for each pixel. It is useful for mapping variables such as {term}`canopy height <Canopy Height>`, biomass, soil moisture or population density.

Planetary Computer
: A cloud-based geospatial data platform by Microsoft that hosts many {term}`Earth observation <Earth Observation>` datasets and provides {term}`STAC <SpatioTemporal Asset Catalog>`-based search and data access tools.

Post-Processing
: Processing applied after model prediction to clean, filter, vectorize or summarise outputs. Post-processing can improve usability but should be documented clearly.

[PowerShell](https://en.wikipedia.org/wiki/PowerShell)
: A {term}`command-line <Command Line>` shell commonly used on Windows. It can run setup commands, create folders and start Python tools.

Pre-Trained Model
: A model whose parameters were learned from a previous dataset or task. Pre-trained models can often be adapted to new {term}`GeoAI` tasks through {term}`fine-tuning <Fine-Tuning>` or prompting.

[Precision](https://en.wikipedia.org/wiki/Precision_and_recall)
: The proportion of predicted positives that are correct. High precision means the model produces relatively few {term}`false positives <False Positive>`.

[Precision-Recall Curve](https://en.wikipedia.org/wiki/Precision_and_recall)
: A curve showing how {term}`precision <Precision>` and {term}`recall <Recall>` change as the confidence threshold used to retain predictions varies.

[Preprocessing](https://en.wikipedia.org/wiki/Data_preprocessing)
: Steps that prepare raw data for analysis or modelling. Examples include clipping, {term}`reprojection <Reprojection>`, {term}`resampling <Resampling>`, masking, normalization and tiling.

Preview Subset
: A small spatial subset of a larger raster dataset used for quick inspection, visualisation or teaching. Preview subsets are useful for checking data fit but may be resampled and should not automatically be treated as analysis-ready data.

[Principal Component Analysis](https://en.wikipedia.org/wiki/Principal_component_analysis)
: Principal component analysis (PCA) is a linear dimensionality-reduction method that projects high-dimensional data onto a smaller number of axes, often two for visualization, chosen to preserve as much of the original variance as possible.

Probability Map
: A multi-{term}`band <Band>` raster produced during segmentation {term}`inference <Inference>`, where each band corresponds to one class and pixel values from 0 to 1 represent the model's confidence that a pixel belongs to that class.

Probability Threshold
: A cutoff applied to a model probability or score to convert a continuous prediction into a discrete decision such as changed versus unchanged.

Project Parameter
: A setting that controls part of a project workflow, such as study area, year range, {term}`CRS <Coordinate Reference System>`, input folder or output folder. Keeping project parameters visible makes code easier to adapt.

[Projection](https://en.wikipedia.org/wiki/Map_projection)
: A mathematical transformation that represents the curved Earth on a flat surface. Projections introduce distortions that matter for area, distance, direction or shape.

Prompt
: A hint given to a {term}`foundation model <Foundation Model>` like {term}`SAM <Segment Anything Model>` to indicate what to segment. Prompts can take the form of text descriptions, labeled point coordinates, or {term}`bounding boxes <Bounding Box>`, all processed by a lightweight {term}`prompt encoder <Prompt Encoder>` against a cached {term}`image embedding <Image Embedding>`.

Prompt Encoder
: The {term}`SAM <Segment Anything Model>` component that converts text, points, boxes or other user guidance into a representation that can be combined with image features.

PSPNet
: A segmentation architecture (Pyramid {term}`Scene` Parsing Network) that pools features at multiple region sizes to capture global scene context, useful for scenes where broad context matters more than fine boundary {term}`precision <Precision>`.

Pull
: A {term}`Git` action that downloads changes from a {term}`remote repository <Remote Repository>` into the local {term}`repository <Repository>`. Pulling before work helps keep local files up to date.

Push
: A {term}`Git` action that uploads local {term}`commits <Commit>` to a {term}`remote repository <Remote Repository>`. Pushing makes committed changes visible online.

Python Script
: A `.py` file that contains Python code. Scripts are useful for reusable functions, repeated processing and organising stable parts of a project workflow.

[PyTorch](https://en.wikipedia.org/wiki/PyTorch)
: A Python {term}`deep learning <Deep Learning>` framework used for building, training and running neural networks. It is a major foundation for many {term}`GeoAI` tools.

QGIS Plugin
: An extension that adds functionality to the QGIS desktop GIS environment. {term}`GeoAI` plugins can make AI-based workflows available through a graphical interface.

[R-squared](https://en.wikipedia.org/wiki/Coefficient_of_determination)
: R-squared (R²), also called the coefficient of determination, is a {term}`regression <Regression>` {term}`evaluation metric <Evaluation Metric>` measuring the proportion of variance in the target that the model explains, ranging up to 1.0 for a perfect fit. It is commonly monitored alongside loss during training.

Radiometric Normalization
: Adjustment of image values to reduce systematic radiometric differences between acquisition dates so that temporal comparison focuses more strongly on surface change.

[Random Forest](https://en.wikipedia.org/wiki/Random_forest)
: A traditional machine learning method based on many decision trees. It remains useful for spatial {term}`classification <Classification>` and {term}`regression <Regression>`, especially with tabular features or limited {term}`training data <Training Data>`.

[Raster Data](https://en.wikipedia.org/wiki/Data_model_%28GIS%29#Raster_data_model)
: Spatial data stored as a grid of cells or pixels. Raster data are commonly used for satellite imagery, elevation, land cover and model output maps.

Raster Mask
: A raster layer used to mark classes, valid areas or labelled regions. Raster masks are often paired with image {term}`chips <Chip>` for segmentation training.

Rasterio
: A Python library for reading and writing raster data while preserving geospatial {term}`metadata <Metadata>`. It is commonly used with NumPy arrays in spatial workflows.

[README](https://en.wikipedia.org/wiki/README)
: A Markdown file that explains what a project or folder contains and how it should be used. In SDS320, a clear README is important for project understanding and {term}`reproducibility <Reproducibility>`.

[Recall](https://en.wikipedia.org/wiki/Precision_and_recall)
: The proportion of reference positives that the model successfully finds. High recall means the model misses relatively few true objects or pixels.

Region Proposal Network
: A region proposal network (RPN) is a small network, used in two-stage detectors such as {term}`Faster R-CNN`, that scans a {term}`backbone <Backbone>`'s feature map and proposes candidate regions likely to contain an object, regardless of class.

[Regression](https://en.wikipedia.org/wiki/Regression_analysis)
: A modelling task that predicts continuous values rather than categories. In spatial analytics, regression can estimate variables such as temperature, elevation, biomass or risk.

[Relative Path](https://en.wikipedia.org/wiki/Path_%28computing%29)
: A file path written relative to the current project or {term}`working directory <Working Directory>`. Relative paths usually make projects easier to move, share and reproduce.

Remote Repository
: The online version of a {term}`Git` {term}`repository <Repository>`, usually hosted on a platform such as {term}`GitHub` or {term}`GitLab`. It is used for backup, sharing and submission.

[Remote Sensing](https://en.wikipedia.org/wiki/Remote_sensing)
: The collection of information about Earth without direct contact, often using satellites, aircraft or drones. Remote sensing provides many of the imagery datasets used in {term}`GeoAI`.

[Repository](https://en.wikipedia.org/wiki/Repository_%28version_control%29)
: A version-controlled project folder, often hosted on {term}`GitHub` or {term}`GitLab`. In SDS320, a repository should make the project understandable and reproducible.

[Reproducibility](https://en.wikipedia.org/wiki/Reproducibility)
: The ability for someone else to understand and rerun the main steps of a project. Reproducibility depends on clear code, documented data sources, environment information and organised outputs.

Reprojection
: Changing spatial data from one {term}`coordinate reference system <Coordinate Reference System>` to another. Reprojection is needed when datasets use different CRS and must be analysed together.

Resampling
: Changing the pixel size, grid alignment or resolution of raster data. Resampling is common when combining rasters with different resolutions or preparing model inputs.

[Residual](https://en.wikipedia.org/wiki/Errors_and_residuals_in_statistics)
: The difference between a predicted value and a reference value in a {term}`regression <Regression>` task. Mapping residuals can reveal spatial patterns in model errors.

[ResNet](https://en.wikipedia.org/wiki/Residual_neural_network)
: A {term}`convolutional neural network <Convolutional Neural Network>` family built around {term}`residual <Residual>` (skip) connections, which let gradients flow directly through the network and make it possible to train much deeper models. ResNet-50 is a common default architecture for image {term}`classification <Classification>`, including satellite image {term}`tiles <Tile>`.

[Resolution](https://en.wikipedia.org/wiki/Image_resolution)
: The level of spatial, temporal or spectral detail in a dataset. In imagery, {term}`spatial resolution <Spatial Resolution>` often refers to the ground size represented by one pixel.

Restart and Run All
: A notebook check in which the kernel is restarted and all cells are executed from top to bottom. It helps reveal missing imports, hidden variables, path problems and other {term}`reproducibility <Reproducibility>` issues.

RetinaNet
: A single-stage {term}`object detection <Object Detection>` architecture that addresses {term}`class imbalance <Class Imbalance>` between background and object regions through a specialized {term}`loss function <Loss Function>`. It is supported in `geoai` as `retinanet_resnet50_fpn_v2`.

RGB Composite
: A visual image made by assigning three {term}`bands <Band>` to red, green and blue display channels. True-colour and false-colour composites help inspect {term}`remote sensing <Remote Sensing>` imagery.

RoI Align
: A sampling operation used in region-based neural networks to extract features for a proposed object region without coarse coordinate rounding, helping preserve spatial alignment for {term}`mask <Mask>` prediction.

[Root Mean Squared Error](https://en.wikipedia.org/wiki/Root_mean_square_deviation)
: Root mean squared error (RMSE) is the square root of {term}`mean squared error <Mean Squared Error>`. RMSE summarizes prediction error in the same units as the target while giving relatively high influence to large errors.

Satellite Embedding
: A numerical representation of satellite imagery learned by a model. {term}`Embeddings <Embedding>` can be used for similarity search, clustering, {term}`classification <Classification>` or change analysis.

Scene
: A spatial image acquisition covering a specific area and time. A scene may be too large for direct model input and often needs tiling.

Secret
: Sensitive information such as an {term}`API <Application Programming Interface>` key, password or access token. Secrets should not be committed to {term}`repositories <Repository>` or pasted into external AI tools.

[Segment Anything Model](https://en.wikipedia.org/wiki/Segment_Anything)
: The Segment Anything Model (SAM) is a general-purpose image segmentation {term}`foundation model <Foundation Model>` released by Meta, capable of segmenting objects in an image without being trained specifically for that object type. In {term}`GeoAI`, it is adapted for georeferenced imagery through packages such as `segment-geospatial`.

segment-geospatial
: The `segment-geospatial` package, commonly imported as `samgeo`, adapts the {term}`Segment Anything Model` for geospatial imagery, producing georeferenced vector outputs such as {term}`GeoJSON` or {term}`Shapefiles <Shapefile>` instead of plain, unreferenced image {term}`masks <Mask>`.

[Self-Supervised Learning](https://en.wikipedia.org/wiki/Self-supervised_learning)
: A training approach in which a model learns from unlabeled data by solving a task derived from the data itself, such as predicting a masked-out portion of an image. It lets {term}`foundation models <Foundation Model>` pre-train on large amounts of unlabeled imagery.

[Semantic Segmentation](https://en.wikipedia.org/wiki/Image_segmentation#Semantic_segmentation)
: The task of assigning a class label to every pixel in an image. It produces thematic maps such as water, vegetation, buildings or roads.

[Sensor](https://en.wikipedia.org/wiki/Sensor)
: An instrument that records data, such as an optical, radar, thermal or LiDAR sensor. Sensor properties influence resolution, {term}`bands <Band>`, noise and suitable analysis methods.

Sensor-Agnostic Model
: A model designed to support input from multiple {term}`sensor <Sensor>` types under specified input conventions, rather than being restricted to one sensor-specific training workflow.

[Sentinel-2](https://en.wikipedia.org/wiki/Sentinel-2)
: A multispectral satellite mission that provides repeated optical observations of Earth. Its visible and infrared {term}`bands <Band>` are widely used in land cover, vegetation and change analysis.

[Shapefile](https://en.wikipedia.org/wiki/Shapefile)
: An older but still common vector data format. Shapefiles have practical limitations, so formats such as {term}`GeoPackage` or {term}`GeoParquet` are often preferable for new projects.

Shapely
: A Python library for geometric operations such as intersections, buffers and unions. It underpins many vector workflows in {term}`GeoPandas`.

[Shortwave Infrared](https://en.wikipedia.org/wiki/Infrared#Short-wave_infrared)
: Shortwave infrared (SWIR) is a spectral region beyond {term}`near-infrared <Near-Infrared>` wavelengths. Water reflects very little in this range compared with vegetation and soil, which makes SWIR {term}`bands <Band>` especially useful for distinguishing water from other land cover.

[Siamese Network](https://en.wikipedia.org/wiki/Siamese_neural_network)
: A neural network design that processes two inputs with related branches, often used to compare image pairs. It is common in {term}`change detection <Change Detection>` workflows.

Skip Connection
: A direct pathway linking a corresponding encoder layer to a {term}`decoder <Decoder>` layer at the same spatial scale, bypassing the network's compressed bottleneck. Skip connections let a decoder combine fine spatial detail with the semantically rich features learned deeper in the network.

Small Reproducible Example
: A reduced version of a problem that still produces the same error. Small reproducible examples make {term}`debugging <Debugging>` easier because they remove unrelated complexity.

[Source Control](https://en.wikipedia.org/wiki/Version_control)
: A general term for tools and practices that track changes in files over time. {term}`Git` is the source-control system used in SDS320.

[Spatial Autocorrelation](https://en.wikipedia.org/wiki/Spatial_autocorrelation)
: The tendency for nearby locations to be more similar than distant locations. It affects sampling, validation and interpretation in spatial machine learning.

[Spatial Join](https://en.wikipedia.org/wiki/Spatial_join)
: An operation that combines vector features based on their spatial relationship. It can be used to attach {term}`attributes <Attribute>` from one layer to another.

[Spatial Resolution](https://en.wikipedia.org/wiki/Image_resolution)
: The ground area represented by one pixel or measurement. Spatial resolution influences which objects can be detected and how results should be interpreted.

SpatioTemporal Asset Catalog
: The SpatioTemporal Asset Catalog (STAC) specification is a {term}`metadata <Metadata>` standard and search interface for discovering geospatial assets such as satellite imagery. STAC helps users search by area, time, collection and cloud cover.

[Spectral Band](https://en.wikipedia.org/wiki/Spectral_band)
: A wavelength range recorded by a {term}`sensor <Sensor>`. Spectral bands help distinguish materials such as vegetation, water, soil and built surfaces.

Spectral Index
: A value calculated from two or more {term}`spectral bands <Spectral Band>` to highlight a surface property. Examples include vegetation or water indices.

Split-Panel Map
: An interactive map layout that places two datasets side by side with a draggable divider, so both sides always share the same geographic extent and zoom level. It is commonly used to compare imagery from different dates, compare a prediction against {term}`ground truth <Ground Truth>`, or check whether labels align with source imagery.

STAC Asset
: A file linked to a {term}`STAC <SpatioTemporal Asset Catalog>` item, such as an image {term}`band <Band>`, preview, {term}`metadata <Metadata>` document or quality layer. Asset keys need to be inspected because they differ between collections.

STAC Collection
: A group of related {term}`STAC <SpatioTemporal Asset Catalog>` items that share common {term}`metadata <Metadata>`, such as a satellite mission, product level or data provider collection.

STAC Item
: A single spatiotemporal observation in a {term}`STAC collection <STAC Collection>`, often one satellite {term}`scene <Scene>`, {term}`tile <Tile>` or image acquisition.

Stage
: To select changed files or parts of files for the next {term}`Git` {term}`commit <Commit>`. Staging helps keep commits focused and meaningful.

Stochastic Forward Pass
: One model {term}`inference <Inference>` run that includes a random sampling component, so repeated runs with the same input can produce slightly different outputs.

Streaming Memory
: A mechanism in {term}`SAM <Segment Anything Model>` 3's video architecture that maintains a memory bank of previously segmented frames, used to track objects coherently across a video even when they move, are partly occluded, or temporarily leave and reappear in the frame.

Stride
: The step size used when moving a window across an image during tiling or {term}`inference <Inference>`. Smaller stride creates more overlap but increases processing time.

[Super-Resolution](https://en.wikipedia.org/wiki/Super-resolution_imaging)
: An {term}`image-translation <Image Translation>` task that estimates a higher-resolution image from a lower-resolution input. It can improve visual detail, but the added detail is inferred and should be interpreted carefully.

SWISSIMAGE
: A high-resolution {term}`aerial imagery <Aerial Imagery>` product for Switzerland provided by swisstopo. It can be useful for detailed visual inspection and mapping in Swiss study areas.

[Terminal](https://en.wikipedia.org/wiki/Terminal_emulator)
: A {term}`command-line <Command Line>` application used to run shell commands. On macOS and Linux it is commonly called Terminal; on Windows, {term}`PowerShell` or {term}`Anaconda` {term}`Prompt` serve a similar role.

TESSERA
: A satellite {term}`foundation-model <Foundation Model>` {term}`embedding <Embedding>` product that represents annual Sentinel-1 and {term}`Sentinel-2` temporal information as dense per-pixel feature vectors.

[Test Set](https://en.wikipedia.org/wiki/Training,_validation,_and_test_data_sets)
: A held-out subset used to assess final model performance. In spatial projects, the test set should often be spatially separated from {term}`training data <Training Data>`.

Tile
: A smaller spatial piece of a larger raster {term}`scene <Scene>`. Tiles help manage large images and allow models to process data in fixed-size inputs.

Tiled Inference
: Applying a model to many {term}`tiles <Tile>` from a larger raster and combining the predictions. It is necessary when the full image is too large for memory or model input limits.

timm
: A Python library ({term}`PyTorch` Image Models) providing implementations and pre-trained weights for over a thousand image {term}`classification <Classification>` architectures, including {term}`ResNet`, {term}`EfficientNet`, {term}`Vision Transformer <Vision Transformer>`, and ConvNeXt families. Packages such as `geoai` build on `timm` to let you select an architecture with a single string parameter.

TorchGeo
: A {term}`PyTorch` domain library for geospatial data. It provides datasets, samplers, transforms and model components designed for {term}`remote sensing <Remote Sensing>` workflows.

[Traceback](https://en.wikipedia.org/wiki/Stack_trace)
: The detailed Python error report that shows the sequence of calls leading to an error. The most useful information is often near the bottom, where the direct error type and failing line are shown.

[Training Data](https://en.wikipedia.org/wiki/Training,_validation,_and_test_data_sets)
: Examples used by a model to learn the relationship between inputs and outputs. In {term}`GeoAI`, training data often consist of image {term}`chips <Chip>` paired with labels, {term}`masks <Mask>` or {term}`bounding boxes <Bounding Box>`.

[Training Set](https://en.wikipedia.org/wiki/Training,_validation,_and_test_data_sets)
: The subset of data used to fit model parameters. It should be separate from validation and test data.

[Transfer Learning](https://en.wikipedia.org/wiki/Transfer_learning)
: Reusing knowledge from a model trained on another dataset or task. It can reduce the amount of labelled data needed for a project.

[Transformer](https://en.wikipedia.org/wiki/Transformer_%28deep_learning_architecture%29)
: A neural network architecture based on attention mechanisms. Transformers are used in vision models, language models and many {term}`foundation models <Foundation Model>`.

[True Positive](https://en.wikipedia.org/wiki/Sensitivity_and_specificity)
: A true positive (TP) is a case where the model correctly predicts something that is present in the reference data. In validation, true positives contribute to metrics such as {term}`precision <Precision>`, {term}`recall <Recall>` and {term}`F1-score`, together with {term}`false positives <False Positive>` and {term}`false negatives <False Negative>`.

[U-Net](https://en.wikipedia.org/wiki/U-Net)
: An {term}`encoder-decoder architecture <Encoder-Decoder Architecture>` widely used for image segmentation. Its {term}`skip connections <Skip Connection>` help combine high-level context with fine spatial detail.

[Uncertainty](https://en.wikipedia.org/wiki/Uncertainty)
: A measure or discussion of how confident and reliable results are. In SDS320 projects, uncertainty may come from data quality, model performance, spatial scale or interpretation choices.

Uncertainty Map
: A per-pixel raster, computed from the standard deviation across multiple stochastic model runs on the same input, indicating where a generative model's output was consistent (low {term}`uncertainty <Uncertainty>`) or variable (high uncertainty) across repeated sampling.

[Unpaired Image Translation](https://en.wikipedia.org/wiki/Image-to-image_translation)
: {term}`Image translation <Image Translation>` learned from collections of images in two domains without requiring one-to-one aligned input-target pairs.

[Validation Set](https://en.wikipedia.org/wiki/Training,_validation,_and_test_data_sets)
: A subset used during model development to tune choices and monitor performance. It should remain separate from the {term}`training set <Training Set>`.

Vantor Open Data
: A collection of event-focused high-resolution imagery released for selected disasters and humanitarian contexts. Availability and licence conditions should be checked for each event collection.

[Variable](https://en.wikipedia.org/wiki/Variable_%28computer_science%29)
: A named value that can be reused in code. Good variable names make notebooks and scripts easier to understand.

Vector Data
: Spatial data represented by points, lines or polygons. Vector data are used for features such as roads, buildings, boundaries and training {term}`annotations <Annotation>`.

Vectorization
: The conversion of raster outputs such as {term}`masks <Mask>` into vector geometries. Vectorization can make model outputs easier to measure, edit or use in GIS workflows.

[Version Control](https://en.wikipedia.org/wiki/Version_control)
: The practice of tracking changes to files over time. Version control makes it easier to review project history, recover earlier versions and collaborate.

[Vision Transformer](https://en.wikipedia.org/wiki/Vision_transformer)
: A vision transformer (ViT) is a {term}`transformer <Transformer>` architecture adapted for image analysis. ViTs split images into {term}`patches <Patch>` and use attention to learn relationships across the image.

[Vision-Language Model](https://en.wikipedia.org/wiki/Vision-language_model)
: A vision-language model (VLM) connects visual information with text. In {term}`GeoAI`, VLMs can support {term}`image captioning <Image Captioning>`, {term}`visual question answering <Visual Question Answering>` and text-guided analysis.

[Visual Question Answering](https://en.wikipedia.org/wiki/Visual_question_answering)
: Visual question answering (VQA) is a task where a model answers text questions about an image. For geospatial imagery, VQA outputs should be checked against spatial context and domain knowledge.

[VS Code](https://en.wikipedia.org/wiki/Visual_Studio_Code)
: A code editor that supports Python, notebooks, {term}`terminals <Terminal>`, {term}`Git` and extensions. It is useful for organising SDS320 scripts, Markdown files, notebooks and {term}`repositories <Repository>`.

[Working Directory](https://en.wikipedia.org/wiki/Working_directory)
: The folder from which Python or a shell command is currently running. Many file-path errors happen because the working directory is different from what the user expects.

[YOLO](https://en.wikipedia.org/wiki/You_Only_Look_Once)
: A family of {term}`object detection <Object Detection>` models designed for fast prediction. YOLO-style models are often used when many objects need to be detected efficiently.

[Zarr](https://en.wikipedia.org/wiki/Zarr_%28data_format%29)
: A chunked array storage format useful for large multidimensional datasets. It supports workflows where data are too large to load all at once.

Zero-Shot Object Detection
: {term}`Object detection <Object Detection>` performed using a model, such as OWL-{term}`ViT <Vision Transformer>` or Grounding DINO, that can locate objects described by a text {term}`prompt <Prompt>` without training on labeled examples of that object class.

Zero-Shot Segmentation
: Applying a pre-trained segmentation model to a target task without first training it on labelled examples from that specific task.

```
