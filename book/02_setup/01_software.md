---
site:
 outline_maxdepth: 1
---

<!-- setup course directories (accounting for the dual course and project structure) -->
<!-- seperate environment for course and projects? -->

# Software setup

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Preparing the tools you need for SDS320
</div>
<!-- markdownlint-enable MD033 -->

---

## Why this chapter matters

A stable software setup saves time during project work. Spatial analytics projects often combine geospatial libraries, machine learning packages, notebooks, scripts and external data. If your environment is unclear, errors can become difficult to diagnose.

The goal of this page is to help you set up a working SDS320 environment, check that it works and know what to do when something does not work.

```{admonition} Note
:class: note
The course environment file provided for SDS320 is the main reference for package installation. It uses the environment name `sds320` and Python 3.12.

```

## What you need

For SDS320, you should have access to:

* Python through Miniconda, Anaconda or another conda-compatible setup,
* a dedicated conda environment for the course,
* VS Code or another code editor,
* Git,
* GitHub or GitLab for your project repository,
* the packages listed in the course `environment.yml`.

:::{note} The provided environment includes:
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
  - pytorch
  - torchvision

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

This package list covers notebooks, spatial vector and raster workflows, GeoAI tools, machine learning, plotting and cloud/data access.

:::

```{warning}
Do not install many packages randomly into the same environment while troubleshooting. First check which environment is active, then install only what you need.
```
```{admonition} Hint
:class: hint
Do not install many packages randomly into the same environment while troubleshooting. First check which environment is active, then install only what you need.

```

---

## Recommended setup workflow

### 1. Install Miniconda or Anaconda

Conda is recommended because geospatial Python packages often depend on compiled libraries such as GDAL, PROJ and GEOS. Conda can install many of these dependencies together, which usually avoids compatibility problems.

Download Miniconda from the official documentation:

[https://www.anaconda.com/docs/getting-started/miniconda/install](https://www.anaconda.com/docs/getting-started/miniconda/install)

After installation, open a new terminal and check that conda is available:

```bash
conda --version
python --version
```

You should see version numbers rather than an error message.

### 2. Create the SDS320 environment

Save the course `environment.yml` file in a convenient folder. Then open a terminal in that folder and run:

```bash
conda env create -f environment.yml
```

This creates a conda environment called `sds320`.

Activate it with:

```bash
conda activate sds320
```

Your terminal prompt should now include something like:

```text
(sds320)
```

This means that packages you install or run are linked to the course environment.

### 3. Update the environment when needed

If the course environment file changes later, update your environment from the same folder:

```bash
conda env update -f environment.yml --prune
```

The `--prune` option removes packages that are no longer listed in the environment file.

```{tip}
Keep a copy of the `environment.yml` file in your project repository or link to the course version. This makes your project easier to reproduce.
```

### 4. Check VS Code

VS Code is useful for editing scripts, Markdown files, YAML files, README files and notebooks.

Download it from:

[https://code.visualstudio.com](https://code.visualstudio.com)

Recommended extensions:

```bash
code --install-extension ms-python.python
code --install-extension ms-toolsai.jupyter
code --install-extension charliermarsh.ruff
```

These extensions support Python, notebooks and code formatting/linting.

After opening your project folder in VS Code, select the `sds320` environment as the Python interpreter or notebook kernel.

### 5. Check Git

Check whether Git is installed:

```bash
git --version
```

You should see a version number.

If Git is not available, install it from the official Git website or through your operating-system package manager.

<!-- TODO: add course-preferred Git installation link if needed -->

---

## Checking your setup

After activating the environment, run the following checks.

### Check Python and the active environment

```bash
python --version
conda info --envs
```

The active environment should be marked with an asterisk.

### Check important packages

Create a new notebook or Python script and run:

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

If a package shows `NOT FOUND`, first check that the notebook is using the correct kernel.

### Check PyTorch and GPU availability

Most SDS320 work can run on a CPU, but some deep learning workflows are faster with a suitable GPU.

Run:

```{code-cell} python
import torch

print(f"PyTorch version: {torch.__version__}")
print(f"CUDA available: {torch.cuda.is_available()}")

if torch.cuda.is_available():
    print(f"GPU device: {torch.cuda.get_device_name(0)}")
else:
    print("Running in CPU-only mode.")
```

If you have an NVIDIA GPU and expected CUDA to be available, check your NVIDIA driver and PyTorch installation.

From the terminal, you can also run:

```bash
nvidia-smi
```

If the command displays your GPU model and driver version, the driver is available. If the command is not found, you may not have an NVIDIA GPU or the driver may not be installed.

```{note}
On macOS, NVIDIA CUDA is not supported. Apple Silicon Macs use a different acceleration framework. For this course, CPU-only mode is acceptable for many small examples, but larger deep learning tasks may require simplification or cloud resources.
```

### Check interactive maps

Run this in a notebook:

```{code-cell} python
import leafmap

m = leafmap.Map(center=[47.3769, 8.5417], zoom=10)
m
```

If an interactive map appears, your notebook and visualisation stack are working.

---

## Optional cloud alternatives

If your local computer struggles with larger models, cloud notebooks can be useful.

Possible options include:

* Google Colab,
* Kaggle notebooks.

Cloud platforms may provide GPU access, but they can have restrictions. Interactive mapping libraries may not behave exactly like they do locally.

<!-- TODO: decide whether SDS320 officially supports Google Colab or Kaggle workflows. -->

---

## Common setup problems

### Wrong Python environment

Symptom: a package is installed, but the notebook says `ModuleNotFoundError`.

First checks:

```bash
conda activate sds320
python -c "import sys; print(sys.executable)"
```

In VS Code, check that the selected kernel is `Python (sds320)` or equivalent.

### Missing packages

Symptom: importing a package fails.

First try updating the environment from the provided file:

```bash
conda env update -f environment.yml --prune
```

If only one package is missing, discuss with the course team before changing the shared environment.

### Kernel not visible

Register the environment manually:

```bash
conda activate sds320
python -m ipykernel install --user --name sds320 --display-name "Python (sds320)"
```

Then restart VS Code.

### Path issues

Symptom: Python cannot find a file that you can see in Finder, Explorer or VS Code.

Check your current working directory:

```{code-cell} python
from pathlib import Path

print(Path.cwd())
```

Use project-relative paths where possible.

### Git not found

Symptom: the terminal says `git: command not found`.

Install Git and restart your terminal.

<!-- TODO: add course-preferred Git installation instructions if needed -->

---

## Mini task

Before the first project work session, complete this checklist:

* Activate the `sds320` environment.
* Open VS Code and navigate to your course folder.
* Create a notebook called `setup_check.ipynb`.
* Run the package check code above.
* Create a small Leafmap map.
* Save the notebook.
* Check that Git is available with `git --version`.

---

## Key takeaways

* Use the provided `environment.yml` as the main setup reference.
* Work inside the `sds320` conda environment.
* Check the active environment before installing or debugging packages.
* JupyterLab is useful for interactive work; VS Code is useful for scripts and project files.
* A good setup reduces project friction and improves reproducibility.