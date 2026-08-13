# Glossary

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Key terms used in SDS320
</div>
<!-- markdownlint-enable MD033 -->

## How to use the glossary

This glossary collects important terms used across SDS320, especially terms related to geospatial data, remote sensing, GeoAI workflows, training data, model evaluation and reproducible project work.

Throughout the Jupyter Book, selected terms are linked with MyST `{term}` roles. When you encounter a linked term, you can return here for a concise explanation.

The definitions are intentionally short. They are meant to help you understand and use the term in your own project, not to replace the lesson pages or official documentation.

<!-- TODO: Add SDS320-specific responsible AI and academic integrity terms from the course policy page if these terms are linked in the book. -->

## Glossary entries

```{glossary}

AI-Assisted Debugging
: The use of an AI tool to help explain errors, suggest checks or improve code during troubleshooting. AI-assisted debugging can be useful, but students remain responsible for testing and understanding any suggested fix.

Debugging
: The process of finding, understanding and fixing problems in code or workflows. In SDS320, debugging also includes checking spatial assumptions such as CRS, units, overlap and data quality.

Error Message
: Text produced by software when something fails. A useful error message often indicates the error type, the line that failed and the object or file involved.

External LLM
: A large language model accessed outside the course environment, often through a web interface or external application. External LLMs can help explain code and errors, but sensitive data and credentials should not be shared with them.

Internal Agent
: A course- or institution-provided AI assistant that can support specific workflows under defined access, privacy and usage rules. Internal agents may be preferable for course-specific or sensitive questions if provided.

Large Language Model (LLM)
: An AI model trained to process and generate text. LLMs can help explain code, draft debugging plans and suggest alternatives, but their outputs must be checked carefully.

Secret
: Sensitive information such as an API key, password or access token. Secrets should not be committed to repositories or pasted into external AI tools.

Small Reproducible Example
: A reduced version of a problem that still produces the same error. Small reproducible examples make debugging easier because they remove unrelated complexity.

Traceback
: The detailed Python error report that shows the sequence of calls leading to an error. The most useful information is often near the bottom, where the direct error type and failing line are shown.

Branch
: A separate line of development in a Git repository. Branches are useful for trying changes without immediately affecting the main version of a project.

Clone
: A local copy of a remote Git repository. Cloning downloads the repository so you can work on it on your own computer.

Commit
: A saved snapshot of selected changes in a Git repository. Good commits represent meaningful project steps and include clear messages.

GitHub
: An online platform for hosting Git repositories. In SDS320, GitHub can be used to share the final public project repository.

GitLab
: An online platform for hosting Git repositories. In SDS320, GitLab can be used to share the final public project repository if allowed by the course instructions.

Merge Conflict
: A Git situation where changes from different versions affect the same part of a file and Git cannot decide automatically which version to keep. Merge conflicts need to be resolved manually.

Pull
: A Git action that downloads changes from a remote repository into the local repository. Pulling before work helps keep local files up to date.

Push
: A Git action that uploads local commits to a remote repository. Pushing makes committed changes visible online.

Remote Repository
: The online version of a Git repository, usually hosted on a platform such as GitHub or GitLab. It is used for backup, sharing and submission.

Source Control
: A general term for tools and practices that track changes in files over time. Git is the source-control system used in SDS320.

Stage
: To select changed files or parts of files for the next Git commit. Staging helps keep commits focused and meaningful.

Version Control
: The practice of tracking changes to files over time. Version control makes it easier to review project history, recover earlier versions and collaborate.


Hidden Notebook State
: A situation where a notebook works only because cells were run in a particular previous order. Hidden notebook state makes a workflow difficult to reproduce and can often be detected by restarting the kernel and running all cells from top to bottom.

Markdown Cell
: A notebook cell that contains formatted text instead of executable code. Markdown cells are useful for explaining goals, decisions, outputs and interpretation in a reproducible notebook narrative.

Notebook Narrative
: The written explanation that connects code cells, outputs and decisions inside a notebook. A clear notebook narrative helps readers understand why each step was taken and what the results mean.

Python Script
: A `.py` file that contains Python code. Scripts are useful for reusable functions, repeated processing and organising stable parts of a project workflow.

Restart and Run All
: A notebook check in which the kernel is restarted and all cells are executed from top to bottom. It helps reveal missing imports, hidden variables, path problems and other reproducibility issues.


Absolute Path
: A file path that starts from the root of the file system or drive, such as `/Users/name/project/data/file.tif` or `C:\Users\name\project\data\file.tif`. Absolute paths often break when a project is moved to another computer.

Conditional
: A programming structure that runs code only when a condition is met. In Python, conditionals usually use `if`, `elif` and `else`.

DataFrame
: A table-like data structure commonly used in Pandas. DataFrames are useful for storing dataset inventories, attributes, model results and summary statistics.

Dictionary
: A Python data structure that stores key-value pairs. Dictionaries are useful for project settings because they keep related parameters together.

DRY Principle
: A programming principle meaning “Don’t Repeat Yourself”. In project code, repeated logic should often be turned into a function or reusable script.

Function
: A reusable block of code that performs a specific task. Functions make workflows easier to read, test and repeat.

Import
: A Python statement that loads a package, module or function so it can be used in the current script or notebook.

Intermediate Output
: A result created during a workflow before the final output. Checking intermediate outputs helps detect mistakes early.

List
: A Python data structure that stores multiple values in order. Lists are useful for processing several files, bands, years, classes or tiles.

Loop
: A programming structure that repeats code for several items. Loops are useful for applying the same operation to multiple files, years, bands or geometries.

Module
: A Python file or package that contains reusable code. Modules help organise functions and keep notebooks shorter.

Pathlib
: A Python module for working with file and folder paths. It helps create paths that are easier to read and more portable across operating systems.

Project Parameter
: A setting that controls part of a project workflow, such as study area, year range, CRS, input folder or output folder. Keeping project parameters visible makes code easier to adapt.

Relative Path
: A file path written relative to the current project or working directory. Relative paths usually make projects easier to move, share and reproduce.

Variable
: A named value that can be reused in code. Good variable names make notebooks and scripts easier to understand.

Anaconda
: A Python distribution that includes conda, Python and many scientific packages. It can be used for SDS320 if it is already installed and working, although Miniconda is often lighter.

Command Line
: A text-based interface for running commands such as creating folders, activating environments or starting JupyterLab. On Windows this may be PowerShell or Anaconda Prompt; on macOS and Linux it is usually Terminal.

Environment File
: A file, often named `environment.yml`, that lists the Python version, packages and channels needed to recreate a conda environment. It helps make a project easier to reproduce.

Git
: A version-control system used to track changes in files over time. In SDS320, Git supports project development and helps prepare the final public repository.

Gitignore File
: A file named `.gitignore` that tells Git which files or folders should not be tracked. It is useful for excluding large data, temporary files, credentials and local system files.

Jupyter Kernel
: The Python process that runs code inside a Jupyter notebook. Choosing the correct kernel ensures that the notebook uses the intended conda environment and packages.

JupyterLab
: A browser-based working environment for notebooks, code, terminals and files. It is useful for interactive exploration, setup checks and visual project work.

Miniconda
: A lightweight installer for Python and the conda package manager. It is a practical way to create isolated Python environments without installing many extra packages by default.

Package Manager
: A tool used to install, update and manage software packages. Conda is recommended in SDS320 because it can handle many geospatial dependencies together.

PowerShell
: A command-line shell commonly used on Windows. It can run setup commands, create folders and start Python tools.

README
: A Markdown file that explains what a project or folder contains and how it should be used. In SDS320, a clear README is important for project understanding and reproducibility.

Terminal
: A command-line application used to run shell commands. On macOS and Linux it is commonly called Terminal; on Windows, PowerShell or Anaconda Prompt serve a similar role.

VS Code
: A code editor that supports Python, notebooks, terminals, Git and extensions. It is useful for organising SDS320 scripts, Markdown files, notebooks and repositories.

Working Directory
: The folder from which Python or a shell command is currently running. Many file-path errors happen because the working directory is different from what the user expects.


Accuracy
: The proportion of predictions that match the reference labels. It is easy to understand, but it can be misleading when classes are strongly imbalanced.

Aerial Imagery
: Image data captured from aircraft or drones. It often has finer spatial resolution than satellite imagery and is useful for mapping small objects such as buildings, trees, vehicles or field boundaries.

AI Agent
: A system that combines models, tools and reasoning steps to carry out a multi-step task. In a GeoAI workflow, an agent could help search for imagery, run analysis and prepare maps or summaries.

Annotation
: A label attached to training data, such as a class name, polygon, mask or bounding box. Annotations define what the model should learn from each image or pixel.

Application Programming Interface (API)
: A structured way for software tools to communicate with each other. In SDS320 projects, APIs may be used to search, download or process spatial data programmatically.

Area of Interest (AOI)
: The spatial extent selected for analysis. A clear AOI helps keep data downloads, preprocessing, modelling and interpretation feasible.

Attribute
: A non-geometric property stored with a spatial feature, such as a building height, land-cover class or administrative name. Attributes are often used for filtering, grouping and labelling vector data.

Backbone
: The main feature-extraction part of a neural network architecture. In GeoAI, a backbone may be reused from a pre-trained model and adapted to a specific task.

Band
: One layer of values in raster imagery, often representing a specific wavelength range or derived measurement. Multispectral images contain several bands that can reveal information beyond visible colour.

Band Order
: The order in which image bands are stored or passed into a model. Incorrect band order can make an image look wrong and can reduce model performance.

Basemap
: A background map layer used for spatial context. Basemaps help readers understand where results are located, but they should not distract from the main data.

Batch Inference
: Running a trained model on many images, tiles or areas in one workflow. Batch inference is useful when applying a model across a larger study area.

Batch Processing
: Applying the same processing steps to multiple files or areas. It helps make repetitive spatial workflows more efficient and reproducible.

Bounding Box
: A rectangular annotation or prediction that marks the location of an object in an image. Bounding boxes are commonly used in object detection tasks.

Buffer Radius
: A distance used to expand or soften a geometry or label boundary. In training-data preparation, a buffer can sometimes reduce problems caused by small spatial misalignments.

Canopy Height
: The height of vegetation above the ground surface. GeoAI workflows may estimate canopy height from aerial, satellite or LiDAR-derived data.

Change Detection
: The task of identifying where and sometimes how a place has changed between two or more observation times. It is used for topics such as urban expansion, deforestation, disaster damage and seasonal dynamics.

Checkpoint
: A saved version of a model during or after training. Checkpoints make it possible to reuse a trained model, resume training or compare model versions.

Chip
: A smaller image tile cut from a larger raster scene. Chips make large satellite or aerial images manageable for neural networks.

Classification
: The task of assigning categories to observations. In GeoAI, classification may refer to image-level scene labels or pixel-level land-cover labels, depending on the task.

CLIP
: A vision-language model family that connects images and text in a shared representation space. CLIP-style models can support tasks such as image-text matching, zero-shot classification and text-guided search.

Cloud Optimized GeoTIFF (COG)
: A GeoTIFF structured so that parts of the raster can be read efficiently over the web. COGs are useful for cloud-based and large-scale geospatial workflows.

Cloud Removal
: An image-translation task that attempts to replace cloud-covered areas in optical imagery with plausible cloud-free content. The result should be interpreted carefully because the missing information is inferred.

COCO Format
: A common annotation format for computer vision datasets, especially object detection and segmentation. It stores information such as images, classes, bounding boxes and masks in a structured JSON file.

Colormap
: A mapping from data values to colours. Colormaps influence how patterns are perceived and should be chosen to match the type of data and message.

Conda Environment
: An isolated software environment containing a specific Python version and package set. It helps prevent package conflicts between projects.

Confidence Score
: A model output that indicates how confident the model is in a prediction. Confidence scores are useful for filtering detections, but they are not the same as proof that a prediction is correct.

Confusion Matrix
: A table comparing predicted classes with reference classes. It helps reveal which classes are confused with each other.

Convolutional Neural Network (CNN)
: A neural network architecture designed to learn spatial patterns from image-like data. CNNs are widely used for image classification, object detection and segmentation.

Coordinate Reference System (CRS)
: A system that defines how spatial coordinates relate to locations on Earth. Correct CRS handling is necessary when combining, measuring or mapping spatial datasets.

CUDA
: NVIDIA's computing platform for running code on compatible GPUs. In GeoAI, CUDA can accelerate deep learning training and inference when the hardware and software environment are configured correctly.

Data Leakage
: A situation where information from validation or test data unintentionally influences training. In spatial projects, leakage can happen when nearby or overlapping areas are split randomly across training and test sets.

Data Pipeline
: A connected sequence of steps that turns raw data into outputs such as model-ready datasets, predictions, figures or maps. A clear pipeline makes a project easier to debug, reproduce and explain.

Deep Learning
: A branch of machine learning that uses neural networks with many layers to learn patterns from data. In GeoAI, deep learning is often used for imagery tasks such as detection, segmentation and change detection.

Digital Elevation Model (DEM)
: A raster representation of elevation values. DEMs can support terrain analysis, hydrological modelling and contextual features for spatial prediction.

Downstream Task
: A specific task that uses outputs or representations from a model, such as classification, segmentation or similarity search. Foundation models are often adapted to downstream tasks.

Earth Observation (EO)
: The collection of information about Earth’s surface, atmosphere or oceans using sensors on satellites, aircraft or drones. EO data are a major input for spatial analytics and GeoAI.

Encoder-Decoder Architecture
: A neural network structure that first compresses an input into features and then reconstructs an output. It is common in segmentation, image translation and pixel-level regression.

Epoch
: One full pass through the training dataset during model training. Training for more epochs can improve learning, but can also increase overfitting.

Evaluation Metric
: A quantitative measure used to assess model performance. The choice of metric should match the task, data and project question.

F1-score
: A metric that combines precision and recall into a single value. It is useful when both missed detections and false alarms matter.

False Negative
: A case where the model misses something that is present in the reference data. In spatial applications, false negatives can mean missed buildings, missed changes or missed damaged areas.

False Positive
: A case where the model predicts something that is not present in the reference data. False positives can create misleading maps or overestimate the extent of a feature.

Feature Engineering
: The process of creating input variables that help a model learn. Traditional machine learning often relies more heavily on hand-crafted features than deep learning.

Feature Extraction
: The process of deriving useful patterns or representations from data. Neural networks learn features from imagery, while spatial workflows may also extract hand-crafted features such as indices, textures or geometry measures.

Feature Pyramid Network (FPN)
: A neural network component that combines features across multiple spatial scales. It is useful for detecting objects of different sizes in imagery.

Fine-Tuning
: Adapting a pre-trained model to a new dataset or task through additional training. Fine-tuning is useful when labelled project data are limited.

Foundation Model
: A large model pre-trained on broad datasets and adaptable to many downstream tasks. In GeoAI, foundation models can reduce the amount of task-specific labelled data needed.

GDAL/OGR
: A widely used geospatial library for reading, writing and transforming raster and vector data. Many Python geospatial tools rely on GDAL or OGR internally.

GeoAI
: The combination of geospatial data science and artificial intelligence to analyse, interpret or generate insights from spatial data. GeoAI connects geographic domain knowledge with machine learning and deep learning methods.

GeoDataFrame
: A GeoPandas table that includes a geometry column. It allows tabular operations and spatial operations to be used together.

GeoJSON
: A text-based vector data format for storing geographic features and their attributes. It is easy to read and useful for web and lightweight exchange workflows.

GeoPackage
: A file-based geospatial data format that can store vector layers, raster data and related tables. It is often more robust than older shapefile workflows.

GeoPandas
: A Python library that extends Pandas with support for vector geometries. It is commonly used for reading, analysing and writing vector data.

GeoParquet
: A columnar geospatial data format based on Parquet. It is useful for efficient storage and analysis of large vector datasets.

Georeferencing
: The process of connecting image pixels or geometries to real-world coordinates. Georeferencing is what makes a raster or vector dataset spatially meaningful.

GeoTIFF
: A raster file format that stores image values together with geospatial metadata such as CRS, transform and extent. It is one of the most common formats for satellite and aerial imagery.

GPU
: A graphics processing unit that can accelerate parallel computations. In deep learning, GPUs can greatly reduce training and inference time.

Ground Truth
: Reference information used to train or evaluate a model. Ground truth may come from field data, manual labels, trusted maps or carefully checked annotations.

Image Captioning
: A vision-language task where a model generates a text description of an image. In geospatial work, captions should be checked carefully because model descriptions may miss spatial context or uncertainty.

Image Chip
: A small image tile extracted from a larger image. Image chips are used to make large geospatial rasters compatible with model input sizes.

Image Recognition
: The task of assigning one or more labels to an entire image or chip. It is useful when the goal is to classify scenes rather than locate individual pixels or objects.

Image Translation
: The task of transforming an image from one representation to another while preserving spatial structure. Examples include super-resolution, cloud removal and sensor translation.

Inference
: The process of applying a trained model to new data to produce predictions. Inference outputs still need validation, interpretation and often post-processing.

Instance Identifier
: A unique label assigned to an individual object in instance segmentation. It allows adjacent objects of the same class to be counted or analysed separately.

Instance Segmentation
: A task that classifies pixels while also separating individual objects. It is useful when objects need distinct boundaries, counts or measurements.

Intersection over Union (IoU)
: A metric that compares the overlap between a predicted object or mask and the reference object or mask. Higher IoU means stronger spatial agreement.

Jupyter Notebook
: An interactive document that combines code, output, text and figures. Notebooks are useful for exploration, explanation and reproducible project narratives.

Land Cover Classification
: The task of assigning land-cover classes such as water, vegetation, built-up area or bare soil. It can be performed at scene level or pixel level depending on the project question.

Leafmap
: A Python package for interactive geospatial mapping in notebooks. It is useful for exploring imagery, vector data, model outputs and map comparisons.

Loss Function
: A function that measures how different model predictions are from reference labels during training. The model uses this signal to update its parameters.

Mask
: A raster layer that identifies pixels belonging to a class, object or region of interest. Masks are common labels and outputs in segmentation workflows.

Mask R-CNN
: A deep learning architecture for instance segmentation. It detects objects and produces a separate mask for each detected instance.

Mean Absolute Error (MAE)
: A regression metric that measures the average absolute difference between predicted and reference values. It is often easier to interpret than squared-error metrics because it uses the original units.

Mean Average Precision (mAP)
: A detection or segmentation evaluation metric that summarizes precision across confidence thresholds, classes or IoU thresholds. It is common in object detection benchmarks.

Mean Squared Error (MSE)
: A regression metric that averages squared prediction errors. It penalizes large errors more strongly than small ones.

Metadata
: Information that describes a dataset, such as CRS, resolution, acquisition date, band names, licence or sensor. Metadata is necessary for correct processing and documentation.

Model Architecture
: The structure of a model, including how layers and components are arranged. Architecture choices influence what patterns a model can learn and how it processes data.

Model Generalization
: A model’s ability to perform well on new data beyond the examples used during training. In GeoAI, generalization across regions, seasons, sensors and resolutions is often difficult.

Multiband Imagery
: Raster imagery with more than one band. Multiband data can store visible colours, infrared bands, thermal bands or derived variables.

Multispectral Imagery
: Imagery captured in several wavelength ranges, often including visible and infrared bands. It is central to many remote sensing workflows because different surfaces reflect wavelengths differently.

Natural Language Prompt
: A text instruction or query given to a model. Vision-language and segmentation models may use prompts to guide what they should identify or describe.

Near-Infrared (NIR)
: A spectral region just beyond visible red light. NIR is widely used in vegetation analysis because healthy vegetation strongly reflects near-infrared radiation.

NoData Value
: A value used in raster data to mark pixels with missing or invalid information. NoData values should usually be handled before analysis or model training.

Object Detection
: The task of locating and classifying discrete objects in an image, usually with bounding boxes. It is useful for counting and mapping objects such as vehicles, ships, trees or buildings.

OpenStreetMap (OSM)
: A collaborative global vector dataset containing features such as roads, buildings, land use and points of interest. OSM can provide useful context data, but quality and completeness vary by location.

Orthomosaic
: A spatially corrected image mosaic made from overlapping aerial or drone images. Orthomosaics can provide high-resolution base imagery for mapping and annotation.

Overfitting
: A situation where a model learns the training data too closely and performs poorly on new data. Overfitting is a risk when data are limited, imbalanced or spatially similar.

Pansharpening
: An image fusion technique that combines high-resolution panchromatic imagery with lower-resolution multispectral imagery. It aims to improve spatial detail while preserving spectral information.

Pascal VOC Format
: An annotation format commonly used for object detection and instance segmentation datasets. It typically stores object labels and bounding boxes in XML files.

Patch
: A small subset of an image used for model input or analysis. The term is often used similarly to chip or tile.

Pixel-Level Regression
: The task of predicting a continuous value for each pixel. It is useful for mapping variables such as canopy height, biomass, soil moisture or population density.

Post-Processing
: Processing applied after model prediction to clean, filter, vectorize or summarise outputs. Post-processing can improve usability but should be documented clearly.

Pre-Trained Model
: A model whose parameters were learned from a previous dataset or task. Pre-trained models can often be adapted to new GeoAI tasks through fine-tuning or prompting.

Precision
: The proportion of predicted positives that are correct. High precision means the model produces relatively few false positives.

Preprocessing
: Steps that prepare raw data for analysis or modelling. Examples include clipping, reprojection, resampling, masking, normalization and tiling.

Projection
: A mathematical transformation that represents the curved Earth on a flat surface. Projections introduce distortions that matter for area, distance, direction or shape.

PyTorch
: A Python deep learning framework used for building, training and running neural networks. It is a major foundation for many GeoAI tools.

QGIS Plugin
: An extension that adds functionality to the QGIS desktop GIS environment. GeoAI plugins can make AI-based workflows available through a graphical interface.

Random Forest
: A traditional machine learning method based on many decision trees. It remains useful for spatial classification and regression, especially with tabular features or limited training data.

Raster Data
: Spatial data stored as a grid of cells or pixels. Raster data are commonly used for satellite imagery, elevation, land cover and model output maps.

Raster Mask
: A raster layer used to mark classes, valid areas or labelled regions. Raster masks are often paired with image chips for segmentation training.

Rasterio
: A Python library for reading and writing raster data while preserving geospatial metadata. It is commonly used with NumPy arrays in spatial workflows.

Recall
: The proportion of reference positives that the model successfully finds. High recall means the model misses relatively few true objects or pixels.

Regression
: A modelling task that predicts continuous values rather than categories. In spatial analytics, regression can estimate variables such as temperature, elevation, biomass or risk.

Remote Sensing
: The collection of information about Earth without direct contact, often using satellites, aircraft or drones. Remote sensing provides many of the imagery datasets used in GeoAI.

Repository
: A version-controlled project folder, often hosted on GitHub or GitLab. In SDS320, a repository should make the project understandable and reproducible.

Reproducibility
: The ability for someone else to understand and rerun the main steps of a project. Reproducibility depends on clear code, documented data sources, environment information and organised outputs.

Reprojection
: Changing spatial data from one coordinate reference system to another. Reprojection is needed when datasets use different CRS and must be analysed together.

Resampling
: Changing the pixel size, grid alignment or resolution of raster data. Resampling is common when combining rasters with different resolutions or preparing model inputs.

Residual
: The difference between a predicted value and a reference value in a regression task. Mapping residuals can reveal spatial patterns in model errors.

Resolution
: The level of spatial, temporal or spectral detail in a dataset. In imagery, spatial resolution often refers to the ground size represented by one pixel.

RGB Composite
: A visual image made by assigning three bands to red, green and blue display channels. True-colour and false-colour composites help inspect remote sensing imagery.

Satellite Embedding
: A numerical representation of satellite imagery learned by a model. Embeddings can be used for similarity search, clustering, classification or change analysis.

Scene
: A spatial image acquisition covering a specific area and time. A scene may be too large for direct model input and often needs tiling.

Semantic Segmentation
: The task of assigning a class label to every pixel in an image. It produces thematic maps such as water, vegetation, buildings or roads.

Sensor
: An instrument that records data, such as an optical, radar, thermal or LiDAR sensor. Sensor properties influence resolution, bands, noise and suitable analysis methods.

Sentinel-2
: A multispectral satellite mission that provides repeated optical observations of Earth. Its visible and infrared bands are widely used in land cover, vegetation and change analysis.

Shapefile
: An older but still common vector data format. Shapefiles have practical limitations, so formats such as GeoPackage or GeoParquet are often preferable for new projects.

Shapely
: A Python library for geometric operations such as intersections, buffers and unions. It underpins many vector workflows in GeoPandas.

Siamese Network
: A neural network design that processes two inputs with related branches, often used to compare image pairs. It is common in change detection workflows.

Spatial Autocorrelation
: The tendency for nearby locations to be more similar than distant locations. It affects sampling, validation and interpretation in spatial machine learning.

Spatial Join
: An operation that combines vector features based on their spatial relationship. It can be used to attach attributes from one layer to another.

Spatial Resolution
: The ground area represented by one pixel or measurement. Spatial resolution influences which objects can be detected and how results should be interpreted.

SpatioTemporal Asset Catalog (STAC)
: A metadata standard and search interface for discovering geospatial assets such as satellite imagery. STAC helps users search by area, time, collection and cloud cover.

Spectral Band
: A wavelength range recorded by a sensor. Spectral bands help distinguish materials such as vegetation, water, soil and built surfaces.

Spectral Index
: A value calculated from two or more spectral bands to highlight a surface property. Examples include vegetation or water indices.

Stride
: The step size used when moving a window across an image during tiling or inference. Smaller stride creates more overlap but increases processing time.

Super-Resolution
: An image-translation task that estimates a higher-resolution image from a lower-resolution input. It can improve visual detail, but the added detail is inferred and should be interpreted carefully.

Test Set
: A held-out subset used to assess final model performance. In spatial projects, the test set should often be spatially separated from training data.

Tile
: A smaller spatial piece of a larger raster scene. Tiles help manage large images and allow models to process data in fixed-size inputs.

Tiled Inference
: Applying a model to many tiles from a larger raster and combining the predictions. It is necessary when the full image is too large for memory or model input limits.

TorchGeo
: A PyTorch domain library for geospatial data. It provides datasets, samplers, transforms and model components designed for remote sensing workflows.

Training Data
: Examples used by a model to learn the relationship between inputs and outputs. In GeoAI, training data often consist of image chips paired with labels, masks or bounding boxes.

Training Set
: The subset of data used to fit model parameters. It should be separate from validation and test data.

Transfer Learning
: Reusing knowledge from a model trained on another dataset or task. It can reduce the amount of labelled data needed for a project.

Transformer
: A neural network architecture based on attention mechanisms. Transformers are used in vision models, language models and many foundation models.

U-Net
: An encoder-decoder architecture widely used for image segmentation. Its skip connections help combine high-level context with fine spatial detail.

Uncertainty
: A measure or discussion of how confident and reliable results are. In SDS320 projects, uncertainty may come from data quality, model performance, spatial scale or interpretation choices.

Validation Set
: A subset used during model development to tune choices and monitor performance. It should remain separate from the training set.

Vector Data
: Spatial data represented by points, lines or polygons. Vector data are used for features such as roads, buildings, boundaries and training annotations.

Vectorization
: The conversion of raster outputs such as masks into vector geometries. Vectorization can make model outputs easier to measure, edit or use in GIS workflows.

Vision Transformer (ViT)
: A transformer architecture adapted for image analysis. ViTs split images into patches and use attention to learn relationships across the image.

Vision-Language Model (VLM)
: A model that connects visual information with text. In GeoAI, VLMs can support image captioning, visual question answering and text-guided analysis.

Visual Question Answering (VQA)
: A task where a model answers text questions about an image. For geospatial imagery, VQA outputs should be checked against spatial context and domain knowledge.

YOLO
: A family of object detection models designed for fast prediction. YOLO-style models are often used when many objects need to be detected efficiently.

Zarr
: A chunked array storage format useful for large multidimensional datasets. It supports workflows where data are too large to load all at once.

```
