---
site:
 outline_maxdepth: 2
---

# Software setup

<!-- markdownlint-disable MD033-->

<div class="page-subtitle">
Preparing the tools you need for SDS320
</div>
<!-- markdownlint-enable MD033 -->

---

## Why this setup matters

A stable software setup saves time during project work. Spatial analytics projects often combine {term}`GeoAI` tools, geospatial libraries, machine learning packages, notebooks, scripts, model outputs and external data. If your environment and folder structure are unclear, small technical problems can become difficult to diagnose.

The goal of this page is to guide you through a coherent setup pathway:

1. create a clear SDS320 folder structure,
2. install Miniconda or Anaconda,
3. create the course {term}`Conda Environment`,
4. connect the environment to VS Code,
5. check that the core packages work,
6. know what to check first when something breaks.

```{note}
The SDS320 environment file is the main reference for package installation. It uses the environment name `sds320` and Python 3.12.
```

---

## Organise your SDS320 folders

Before you download the environment file, create one clear working area for SDS320. This helps separate course exercises from your own project and makes it easier to use relative paths later.

The suggested structure has two main parts:

* `course/` for course material, exercises, tests and shared examples,
* `project/` for your own assessed SDS320 project and final {term}`Repository`.

Within both parts, data are separated into:

* `data/raw/` for original downloaded files that should not be modified,
* `data/processed/` for clipped, reprojected, cleaned or merged files,
* `data/training/` for tiles, labels, masks or tables prepared as {term}`Training Data`.

```{tip}
Treat `data/raw/` as read-only. If you change a dataset, save the changed version under `data/processed/` or `data/training/`.
```

### Recommended folder structure

```text
sds320/
├── README.md
├── course/
│   ├── README.md
│   ├── environment.yml
│   ├── data/
│   │   ├── README.md
│   │   ├── raw/
│   │   │   ├── swissimage/
│   │   │   ├── sentinel2/
│   │   │   ├── landsat/
│   │   │   ├── naip/
│   │   │   └── vectors/
│   │   ├── processed/
│   │   └── training/
│   ├── models/
│   ├── notebooks/
│   ├── scripts/
│   └── results/
│       ├── figures/
│       ├── maps/
│       ├── predictions/
│       └── evaluation/
└── project/
    ├── README.md
    ├── environment.yml
    ├── .gitignore
    ├── data/
    │   ├── README.md
    │   ├── raw/
    │   │   ├── raster/
    │   │   └── vectors/
    │   ├── processed/
    │   └── training/
    ├── models/
    ├── notebooks/
    ├── scripts/
    └── results/
        ├── figures/
        ├── maps/
        ├── predictions/
        └── evaluation/
```

The additional files are useful for reproducibility:

* `README.md` explains what a folder contains and how it should be used.
* `environment.yml` records the software environment.
* `.gitignore` helps prevent large data files, temporary files or private files from being committed to Git.
* `data/README.md` documents where data came from, how they were downloaded and whether they can be shared.

### Create the folders

Copy the command for your platform into a terminal. Run it in the location where you want to store your SDS320 work, for example your Documents folder.

::::::{tab-set}

:::::{tab-item} Windows PowerShell

Open **PowerShell** and navigate to the folder where you want to create your SDS320 working area.

```powershell
$folders = @(
  "sds320\course\data\raw\swissimage",
  "sds320\course\data\raw\sentinel2",
  "sds320\course\data\raw\landsat",
  "sds320\course\data\raw\naip",
  "sds320\course\data\raw\vectors",
  "sds320\course\data\processed",
  "sds320\course\data\training",
  "sds320\course\models",
  "sds320\course\notebooks",
  "sds320\course\scripts",
  "sds320\course\results\figures",
  "sds320\course\results\maps",
  "sds320\course\results\predictions",
  "sds320\course\results\evaluation",
  "sds320\project\data\raw\raster",
  "sds320\project\data\raw\vectors",
  "sds320\project\data\processed",
  "sds320\project\data\training",
  "sds320\project\models",
  "sds320\project\notebooks",
  "sds320\project\scripts",
  "sds320\project\results\figures",
  "sds320\project\results\maps",
  "sds320\project\results\predictions",
  "sds320\project\results\evaluation"
)

foreach ($folder in $folders) {
  New-Item -ItemType Directory -Force -Path $folder | Out-Null
}

$files = @(
  "sds320\README.md",
  "sds320\course\README.md",
  "sds320\course\environment.yml",
  "sds320\course\data\README.md",
  "sds320\project\README.md",
  "sds320\project\environment.yml",
  "sds320\project\.gitignore",
  "sds320\project\data\README.md"
)

foreach ($file in $files) {
  New-Item -ItemType File -Force -Path $file | Out-Null
}
```

:::::

:::::{tab-item} macOS / Linux

Open **Terminal** and navigate to the folder where you want to create your SDS320 working area.

```bash
cd path/to/sds320/course
```

```bash
mkdir -p sds320/course/data/raw/{swissimage,sentinel2,landsat,naip,vectors}
mkdir -p sds320/course/data/{processed,training}
mkdir -p sds320/course/{models,notebooks,scripts}
mkdir -p sds320/course/results/{figures,maps,predictions,evaluation}

mkdir -p sds320/project/data/raw/{raster,vectors}
mkdir -p sds320/project/data/{processed,training}
mkdir -p sds320/project/{models,notebooks,scripts}
mkdir -p sds320/project/results/{figures,maps,predictions,evaluation}

touch sds320/README.md
touch sds320/course/README.md
touch sds320/course/environment.yml
touch sds320/course/data/README.md
touch sds320/project/README.md
touch sds320/project/environment.yml
touch sds320/project/.gitignore
touch sds320/project/data/README.md
```

:::::

::::::

### What to put where

| Location                  | Use for                                                                                        |
| ------------------------- | ---------------------------------------------------------------------------------------------- |
| `course/environment.yml`  | The official SDS320 course environment file.                                                   |
| `course/notebooks/`       | Course notebooks, setup checks and small experiments.                                          |
| `course/data/raw/`        | Original datasets used in course examples.                                                     |
| `course/results/`         | Outputs from course examples and setup checks.                                                 |
| `project/environment.yml` | The environment used for your own project. This can start as a copy of the course environment. |
| `project/README.md`       | The main explanation of your final project repository.                                         |
| `project/data/raw/`       | Original project data. Do not modify these files directly.                                     |
| `project/data/processed/` | Data changed by preprocessing, such as clipping, reprojection or merging.                      |
| `project/data/training/`  | Image chips, labels, masks or other model-ready training data.                                 |
| `project/scripts/`        | Reusable Python scripts and helper functions.                                                  |
| `project/notebooks/`      | Exploratory notebooks and project analysis notebooks.                                          |
| `project/results/`        | Final or intermediate outputs such as maps, predictions, figures and evaluation results.       |

```{warning}
Large raw data files (>100 MB) should usually not be committed to Git. Use `data/README.md` to document where the data came from and how they can be downloaded again.
```

---

## Software recommendations

For SDS320, you should have access to:

* Python through Miniconda (or another conda-compatible setup),
* a dedicated conda environment for the course,
* VS Code or comparable code editor,
* Git,
* GitHub or GitLab for your project repository,
* the packages listed in the course `environment.yml`.

:::{note} The provided SDS320 course environment includes:
:class: dropdown

```yaml
name: sds320
channels:
  - conda-forge
  - nodefaults
dependencies:
  - python=3.12
  - setuptools<81

  # Notebook environment
  - jupyterlab
  - notebook
  - ipykernel

  # GeoAI and ML stack
  - geoai
  - samgeo
  - pytorch
  - torchgeo
  - torchvision
  - segment-geospatial

  # Geospatial stack
  - gdal
  - proj
  - pyproj
  - rasterio
  - rioxarray
  - xarray
  - geopandas
  - shapely
  - fiona
  - pyogrio
  - folium
  - leafmap
  - localtileserver

  # General scientific Python
  - numpy
  - pandas
  - matplotlib
  - scikit-learn
  - scikit-image
  - tqdm
  - requests

  # Cloud/data access
  - boto3
  - s3fs
  - awscli
```

This package list covers notebooks, {term}`Raster Data`, {term}`Vector Data`, machine learning, plotting, cloud access and interactive mapping.
:::

> If there is a good overlap between the packages you intend to use, you can use the same environment file for your SDS320 project to create a seperate `sds320-project` environment later on. Otherwise, I would recommend either selecting a subset or starting from scratch.

---

## Hardware and {abbr}`OS (operating-system)` recommendations

Most SDS320 work can be done on a normal laptop, especially when working with small examples, pre-trained models or limited study areas.

For heavier model training, larger image collections or repeated inference, hardware becomes more important.

| Component        | Minimum for small examples                              | Recommended for heavier GeoAI work                                                              |
| ---------------- | ------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| Processor        | Modern multi-core {abbr}`CPU (Central Processing Unit)` | Recent multi-core CPU                                                                           |
| Memory           | 8 GB {abbr}`RAM (Random Access Memory)`                 | 16–32 GB RAM                                                                                    |
| Storage          | 20 GB free disk space                                   | {abbr}`SSD (Solid-State Drive)` with 50 GB or more free disk space                              |
| Operating system | Windows 10/11, macOS 14+, or recent Linux               | Same                                                                                            |
| GPU              | Not required for small examples                         | NVIDIA {term}`GPU` with sufficient {abbr}`VRAM (Video Random Access Memory)` for model training |

```{note}
A CPU-only setup is sufficient for many SDS320 tasks. If your project depends on larger deep learning workflows, you may need to reduce the scope, use pre-trained models, work with smaller areas or consider a cloud-based option (e.g. Colab).
```

---

## Setup pathway

Follow the steps below in order.

```text
Create folders
→ install Miniconda or Anaconda
→ create the sds320 environment
→ activate the environment
→ connect it to VS Code
→ verify packages, GPU/CPU and maps
→ document the setup in your project
```

---

### 1. Install Miniconda or Anaconda

Conda is recommended because geospatial Python packages often depend on compiled libraries such as {term}`GDAL/OGR`, PROJ and GEOS. Conda can install many of these dependencies together, which usually avoids compatibility problems.

Use **Miniconda** as a lightweight installation. For a more detailed setup description follow the [SDS210 Conda setup](https://hendrikwulf.github.io/sds210-jb/book/setup/conda/).

::::::{tab-set}

:::::{tab-item} Windows

Open **PowerShell**. The GeoAI setup chapter recommends running PowerShell as administrator for the command-line installer.

```powershell
curl https://repo.anaconda.com/miniconda/Miniconda3-latest-Windows-x86_64.exe -o .\miniconda.exe
start /wait "" .\miniconda.exe /S
del .\miniconda.exe
```

Then close PowerShell. Open **Anaconda Prompt (Miniconda3)** from the Start menu and check the installation:

```bash
conda --version
python --version
```

You should see version numbers rather than an error message.

:::::

:::::{tab-item} macOS Apple Silicon

Use this pathway for M-series Macs, such as M1, M2, M3 or newer.

```bash
mkdir -p ~/miniconda3
curl https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-arm64.sh -o ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm ~/miniconda3/miniconda.sh
```

Initialise conda:

```bash
source ~/miniconda3/bin/activate
conda init --all
```

Close and reopen the terminal, then check the installation:

```bash
conda --version
python --version
```

:::::

:::::{tab-item} macOS Intel

Use this pathway for older Intel-based Macs.

```bash
mkdir -p ~/miniconda3
curl https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-x86_64.sh -o ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm ~/miniconda3/miniconda.sh
```

Initialise conda:

```bash
source ~/miniconda3/bin/activate
conda init --all
```

Close and reopen the terminal, then check the installation:

```bash
conda --version
python --version
```

:::::

:::::{tab-item} Linux

For many Linux distributions, the Miniconda installer can be installed from the terminal.

```bash
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm ~/miniconda3/miniconda.sh
```

Initialise conda:

```bash
source ~/miniconda3/bin/activate
conda init --all
```

Close and reopen the terminal, then check the installation:

```bash
conda --version
python --version
```

:::::

::::::

```{tip}
You can also use the graphical Miniconda installer from the official website if you prefer. The important outcome is that `conda --version` works in a new terminal.
```

---

### 2. Save the SDS320 environment file

[Download](https://data.source.coop/giuz/sds320/environment.yml) the course `environment.yml` file and save it here here:

```text
sds320/course/environment.yml
```

If your own project uses the same environment, copy the file later to:

```text
sds320/project/environment.yml
```

This makes the project easier to reproduce because the software requirements are documented inside the project folder.

---

### 3. Create the SDS320 environment

Open a terminal in the `sds320/course/` folder, where the course `environment.yml` file is saved.

Check that the file is there:

::::::{tab-set}

:::::{tab-item} Windows Anaconda Prompt

```powershell
cd path\to\sds320\course
dir
```

:::::

:::::{tab-item} macOS / Linux

```bash
cd path/to/sds320/course
ls
```

:::::

::::::

Then create the environment:

```bash
conda env create -f environment.yml
```

This creates an environment called `sds320`.

Activate it with:

```bash
conda activate sds320
```

Your terminal prompt should now include:

```text
(sds320)
```

This means that Python commands run from this terminal use the SDS320 environment.

To deactivate the environment later, use:

```bash
conda deactivate
```

```{tip}
Activate the `sds320` environment each time you open a new terminal for course or project work.
```

---

### 4. Optional: update the environment

If the course environment file changes later, update your environment from the folder that contains `environment.yml`:

```bash
conda env update -f environment.yml --prune
```

The `--prune` option removes packages that are no longer listed in the file.

```{warning}
Update the environment only from the intended `environment.yml` file. Running the command from the wrong folder may use the wrong file or fail because no environment file is found.
```

---

### 5. Optional: GPU and CUDA setup

Some GeoAI and deep learning workflows are faster with {term}`CUDA` support. CUDA is relevant only for compatible NVIDIA GPUs.

You can skip this section if:

* you do not have an NVIDIA GPU,
* you use a Mac,
* your project does not require heavy model training,
* or you plan to work with small examples and pre-trained models.

:::{note} GPU vs. CPU
:class: dropdown

A {term}`GPU` and a {abbr}`CPU (Central Processing Unit)` are both processors, but they are designed for different kinds of work.

A **CPU** is the general-purpose processor of your computer. It is good at handling many different tasks: running the operating system, opening files, executing Python code, managing notebooks, reading tables, working with file paths and coordinating programs. Most normal Python and spatial data tasks run on the CPU.

A **GPU** is designed for many small calculations at the same time. This makes it useful for graphics, image processing and deep learning. In a GeoAI context, a GPU can speed up tasks such as training neural networks, running {term}`Inference` on many image {term}`tiles <Tile>`, processing large batches of image chips or working with large segmentation models.

The difference matters because GeoAI workflows often work with large arrays of pixel values. A satellite image can contain millions of pixels, and a neural network may need to apply many mathematical operations to each pixel, band or image chip. GPUs are well suited to this kind of parallel computation.

On Windows and Linux, GPU acceleration for deep learning usually means using an NVIDIA GPU with {term}`CUDA`. On macOS, CUDA is not supported. Apple Silicon Macs may use Apple’s `mps` backend for some PyTorch workflows, but support depends on the package and task.

For this course, the practical rule is:

> We will figure it out. If you are uncertain, start small on your own machine. If the workflow is too slow or too large, reduce the spatial extent, use fewer images, test with smaller tiles, use a pre-trained model, or discuss whether a cloud or GPU option is needed.

:::

#### Check your pathway

::::::{tab-set}

:::::{tab-item} Windows with NVIDIA GPU

Install or update the NVIDIA driver using one of these options:

* download the driver from the NVIDIA driver website,
* or use Windows Update under optional updates.

After installation, restart your computer and run:

```bash
nvidia-smi
```

If the command displays your GPU model and driver version, the driver is available.

Then check PyTorch from inside the `sds320` environment:

```bash
conda activate sds320
python -c "import torch; print(torch.cuda.is_available())"
```

If this prints `True`, PyTorch can access CUDA. If it prints `False`, the environment may be using a CPU-only PyTorch build, or the NVIDIA driver may not be available.

:::::

:::::{tab-item} macOS Apple Silicon

macOS does not support NVIDIA CUDA. Apple Silicon Macs use Apple’s Metal framework instead.

Check whether PyTorch can use Apple’s `mps` backend:

```bash
conda activate sds320
python -c "import torch; print(torch.backends.mps.is_available())"
```

A result of `True` means that PyTorch can see the Apple Silicon acceleration backend. A result of `False` does not necessarily mean your setup is broken; CPU-only mode is still acceptable for many SDS320 tasks.

:::::

:::::{tab-item} macOS Intel

Recent macOS systems do not support NVIDIA CUDA.

Use CPU-only mode for local work, or consider a cloud alternative for heavier model training.

Check that PyTorch works:

```bash
conda activate sds320
python -c "import torch; print(torch.__version__)"
```

:::::

:::::{tab-item} Linux

On Ubuntu/Debian, install the recommended NVIDIA driver:

```bash
sudo apt update
sudo ubuntu-drivers install
sudo reboot
```

After rebooting, check the driver:

```bash
nvidia-smi
```

Then check PyTorch from inside the `sds320` environment:

```bash
conda activate sds320
python -c "import torch; print(torch.cuda.is_available())"
```

For other Linux distributions, driver installation differs.

````{dropdown} Other Linux distributions
On Fedora/RHEL, the GeoAI setup chapter uses RPM Fusion and `akmod-nvidia`.

On Arch/Manjaro, it uses:

```bash
sudo pacman -S nvidia nvidia-utils
```

Restart after driver installation and verify with:

```bash
nvidia-smi
```

````

:::::

::::::

```{note}
The CUDA version shown by `nvidia-smi` indicates what your driver can support. It does not mean that a separate CUDA toolkit is installed. PyTorch often ships with its own CUDA runtime, so a compatible driver is usually the key requirement.
```

---

### 6. Optional: cloud alternatives

If your local computer struggles with larger models, cloud notebooks can provide GPU access.

Possible options include:

* Google Colab,
* Kaggle notebooks.

In this course we will use Colab, as Kaggle has restrictions for the {term}`Leafmap` interactive mapping library.

---

### 7. Install and configure VS Code

VS Code is useful for editing scripts, Markdown files, YAML files, README files and notebooks.

Download VS Code from: [https://code.visualstudio.com](https://code.visualstudio.com)

For a detailed setup description follow the [SDS210 VS Code setup](https://hendrikwulf.github.io/sds210-jb/book/setup/vs-code/).

After opening your SDS320 folder in VS Code:

1. open the Command Palette (Win: `Ctrl` + `Shift` + `P`, Mac: `Cmd` + `Shift` + `P`),
2. choose the Python interpreter or notebook kernel,
3. select the `sds320` environment,

```{tip}
Open the whole `sds320/` folder in VS Code, not only a single notebook. This makes paths, scripts and project files easier to manage.
```

---

### 8. Check Git

Check whether Git is installed:

```bash
git --version
```

You should see a version number.

If Git is not available, install it from the [official Git website](https://git-scm.com/install/mac) or through your operating-system package manager.

For a detailed Git setup description follow the instructions at [SDS210](https://hendrikwulf.github.io/sds210-jb/book/setup/git/)

You will use Git more fully in [Git basics](04_git-basics.md). For now, the goal is only to confirm that the command is available.

---

### 9. Verify your setup

After activating the environment, run the following checks.

#### Check Python and the active environment

```bash
conda activate sds320
python --version
conda info --envs
```

The active environment should be marked with an asterisk.

#### Check important packages

Create a notebook called:

```text
sds320/course/notebooks/setup_check.ipynb
```

Run this code cell:

```{code-cell} python
import importlib
import sys

print(f"Python version: {sys.version}\n")

packages = {
    "numpy": "NumPy",
    "pandas": "Pandas",
    "geopandas": "GeoPandas",
    "rasterio": "Rasterio",
    "rioxarray": "Rioxarray",
    "xarray": "Xarray",
    "matplotlib": "Matplotlib",
    "sklearn": "scikit-learn",
    "torch": "PyTorch",
    "torchvision": "TorchVision",
    "geoai": "GeoAI",
    "leafmap": "Leafmap",
    "folium": "Folium",
}

for module_name, display_name in packages.items():
    try:
        module = importlib.import_module(module_name)
        version = getattr(module, "__version__", "installed")
        print(f"{display_name}: {version}")
    except ImportError:
        print(f"{display_name}: NOT FOUND")
```

If a package shows `NOT FOUND`, first check that the notebook is using the correct `sds320` kernel.

#### Check PyTorch and acceleration

Run:

```{code-cell} python
import torch

print(f"PyTorch version: {torch.__version__}")
print(f"CUDA available: {torch.cuda.is_available()}")

if torch.cuda.is_available():
    print(f"GPU device: {torch.cuda.get_device_name(0)}")
elif hasattr(torch.backends, "mps") and torch.backends.mps.is_available():
    print("Apple MPS available")
else:
    print("Running in CPU-only mode")
```

A CPU-only result is acceptable for many course tasks. For larger model training, discuss whether your project scope needs to be adjusted.

#### Check interactive maps

Run this in the same notebook:

```{code-cell} python
import leafmap

m = leafmap.Map(center=[47.3769, 8.5417], zoom=10)
m
```

If an interactive map appears, your notebook and visualisation stack are working.

---

## Flags & checks

Use this table before reinstalling packages.

| Red flag                                      | First check                                                                                         |
| --------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| `ModuleNotFoundError` in a notebook           | Check whether the notebook kernel is `sds320`.                                                      |
| Package works in terminal but not in notebook | The terminal and notebook probably use different Python environments.                               |
| `conda activate sds320` fails                 | Check whether the environment was created successfully with `conda info --envs`.                    |
| `environment.yml` cannot be found             | Check that your terminal is in `sds320/course/`.                                                    |
| `nvidia-smi` is not found                     | You may not have an NVIDIA GPU, or the NVIDIA driver is not installed.                              |
| CUDA is `False` but you expected GPU support  | Check NVIDIA driver, PyTorch build and active environment.                                          |
| Leafmap does not display                      | Try restarting the kernel, check browser output and confirm that the correct environment is active. |
| Python cannot find a data file                | Print your working directory and check relative paths.                                              |
| Git is not found                              | Install Git and restart the terminal.                                                               |
| Repository contains large raw data            | Update `.gitignore` and document data downloads in `data/README.md`.                                |

For more detail, see [Troubleshooting](05_troubleshooting.md).

---

## Mini task

Before the first project work session, complete this checklist:

* [ ] Create the `sds320/` folder structure.
* [ ] Save the official `environment.yml` file in `sds320/course/`.
* [ ] Create the `sds320` conda environment.
* [ ] Activate the environment.
* [ ] Open the environment in VS Code.
* [ ] Create `sds320/course/notebooks/setup_check.ipynb`.
* [ ] Run the package check.
* [ ] Run the PyTorch acceleration check.
* [ ] Create a small Leafmap map.
* [ ] Check that Git works with `git --version`.
* [ ] Add a short note to `sds320/course/README.md` describing when and how you created the setup.

```{tip}
Keep the setup-check notebook. It gives you a quick way to test whether your environment still works later in the semester.
```

---

## Key takeaways

* Create the SDS320 folder structure before installing or downloading data.
* Keep course exercises and your own project separate.
* Use `data/raw/` for original files and avoid modifying them directly.
* Use the provided `environment.yml` as the main setup reference.
* Work inside the `sds320` conda environment.
* Check the active environment before debugging packages.
* Use VS Code with the correct `sds320` kernel.
* Document setup choices because they affect {term}`Reproducibility`.

---

### What to do next

Once your setup works, continue with:

* [Python reactivation](02_python-reactivation.md) if you need to refresh basic Python patterns,
* [Notebooks and scripts](03_notebooks-scripts.md) to plan how your project code should be organised,
* [Git basics](04_git-basics.md) to start tracking your project,
* [Repository](../03_project/07_repository.md) when you begin preparing your assessed project repository,
* [Reproducibility](../03_project/06_reproducibility.md) when you document how your project can be rerun.
