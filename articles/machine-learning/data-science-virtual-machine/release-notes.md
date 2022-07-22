---
title: What's new on the Data Science Virtual Machine
titleSuffix: Azure Data Science Virtual Machine 
description: Release notes for the Azure Data Science Virtual Machine
author: jesscioffi
ms.service: data-science-vm

ms.author: jcioffi
ms.date: 12/14/2021
ms.topic: reference
---

# Azure Data Science Virtual Machine release notes

In this article, learn about Azure Data Science Virtual Machine releases. For a full list of tools included, along with version numbers, check out [this page](./tools-included.md).

Due to the rapidly evolving needs and packages updates, we target to release new Azure Data Science Virtual Machine for Windows and Ubuntu images every month.

Azure portal users will always find the latest image available for provisioning the Data Science Virtual Machine. For CLI or Azure Resource Manager (ARM) users, we keep images of individual versions available for 12 months. After that period, particular version of image is no longer available for provisioning.

See the [list of known issues](reference-known-issues.md) to learn about known bugs and workarounds.


## July 11, 2022
[Data Science VM – Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=overview) and [Data Science VM – Ubuntu 20.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-2004?tab=Overview)

Version `22.07.08`

Main changes:

- Minor bug fixes.

## June 28, 2022
[Data Science Virtual Machine - Windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview) and [Data Science VM – Ubuntu 20.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-2004?tab=Overview)

Version `22.06.10`

[Data Science VM – Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=overview)

Version `22.06.13`

Main changes:

- Remove `Rstudio` software tool from DSVM images.

## May 17, 2022
[Data Science VM – Ubuntu 20.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-2004?tab=Overview)

Version `22.05.11`

Main changes:

- Upgraded `log4j(v2)` to version `2.17.2`

## April 29, 2022
[Data Science VM – Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=overview) and [Data Science VM – Ubuntu 20.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-2004?tab=Overview)

Version `22.04.27`

Main changes:

- `Plotly` and `summarytools` R studio extensions runtime import fix.
- `Cudatoolkit` and `CUDNN` upgraded to 13.1 and 2.8.1 respectively.
- Fix Python 3.8 - AzureML notebook run, pinned `matplotlib` to 3.2.1 and cycler to 0.11.0 packages in `Azureml_py38` environment.

## April 26, 2022
[Data Science Virtual Machine - Windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview)

Version: `22.04.21`

Main changes:

- `Plotly` R studio extension patch.
- Update `Rscript` env path to support latest R studio version 4.1.3.

## April 14, 2022
New DSVM offering for [Data Science VM – Ubuntu 20.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-2004?tab=Overview) is currently live in the marketplace.

Version: `22.04.05`

## April 04, 2022
New Image for [Data Science VM – Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=overview)

Version: `22.04.01`

Main changes:

- Updated R environment - added libraries: Cluster, Devtools Factoextra, GlueHere, Ottr, Paletteer, Patchwork, Plotly, Rmd2jupyter, Scales, Statip, Summarytools, Tidyverse, Tidymodels and Testthat
- Further `Log4j` vulnerability mitigation - although not used, we moved all `log4j` to version v2, we have removed old log4j jars1.0 and moved log4j version 2.0 jars.
- Azure CLI to version 2.33.1
- Fixed jupyterhub access issue using public ip address
- Redesign of Conda environments - we're continuing with alignment and refining the Conda environments so we created:
  - `azureml_py38`: environment based on Python 3.8 with preinstalled [AzureML SDK](/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true) containing also [AutoML](../concept-automated-ml.md) environment
  - `azureml_py38_PT_TF`: additional azureml_py38 environment, preinstalled with latest TensorFlow and PyTorch
  - `py38_default`: default system environment based on Python 3.8
  - We have removed `azureml_py36_tensorflow`, `azureml_py36_pytorch`, `py38_tensorflow` and `py38_pytorch` environments.
 

## March 18, 2022
[Data Science Virtual Machine - Windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview)

Version: `22.03.09`

Main changes:

- Updated R environment - added libraries: Cluster, Devtools Factoextra, GlueHere, Ottr, Paletteer, Patchwork, Plotly, Rmd2jupyter, Scales, Statip, Summarytools, Tidyverse, Tidymodels and Testthat
- Further `Log4j` vulnerability mitigation - although not used, we moved all `log4j` to version v2, we have removed old log4j jars1.0 and moved `log4j` version 2.0 jars.
- Azure CLI to version 2.33.1
- Redesign of Conda environments - we're continuing with alignment and refining the Conda environments so we created:
  - `azureml_py38`: environment based on Python 3.8 with preinstalled [AzureML SDK](/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true) containing also [AutoML](../concept-automated-ml.md) environment
  - `azureml_py38_PT_TF`: complementary environment `azureml_py38` with preinstalled with latest TensorFlow and PyTorch
  - `py38_default`: default system environment based on Python 3.8
  - we removed `azureml_py36_tensorflow`, `azureml_py36_pytorch`, `py38_tensorflow` and `py38_pytorch` environments.




## March 9, 2022

[Data Science Virtual Machine - Windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview)

Version: `21.12.03`

Windows 2019 DSVM will now be supported under publisher: microsoft-dsvm, offer ID: dsvm-win-2019, plan ID/SKU ID: winserver-2019
 
Users using Azure Resource Manager (ARM) template / virtual machine scale set to deploy the Windows DSVM machines, should configure the SKU with `winserver-2019` instead of `server-2019`, since we'll continue to ship updates to Windows DSVM images on the new SKU from March, 2022.

## December 3, 2021

New image for [Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview).

Version: `21.12.03`

Main changes:

- Updated pytorch to version 1.10.0
- Updated tensorflow to version 2.7.0
- Fix for Azure Machine Learning SDK & AutoML environment
- Windows Security update
- Improvement of stability and minor bug fixes 




## November 4, 2021

New image for [Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview).

Version: `21.11.04`

Main changes:
* Changed .NET Framework to version 3.1.414
* Changed Azcopy to version 10.13.0
* Changed Azure CLI to version 2.30.0
* Changed CUDA to version 11.5
* Changed Docker to version 20.10.10
* Changed Intellijidea to version 2021.2.3
* Changed NVIDIA Drivers to version 470.103.01
* Changed NVIDIA SMI to version 470.103.01
* Changed Nodejs to version v16.13.0
* Changed Pycharm to version 2021.2.3
* Changed VS Code to version 1.61.2
* Conda
  * *azureml_py36_automl*
    * Changed azureml-core to version 1.35.0
  * *py38_default*
    * Changed Jupyter Lab / jupyterlab to version 3.2.1
    * Changed Jupyter Notebook / notebook to version 6.4.5
    * Changed Jupyter Server / jupyter_server to version 1.11.2
    * Changed PyTorch Profiler TensorBoard Plugin / torch-tb-profiler to version 0.3.1
    * Changed azure-core to version 1.19.1
    * Changed matplotlib to version 3.4.3
    * Changed mkl to version 2021.4.0
    * Changed onnx to version 1.10.2
    * Changed opencv-python to version 4.5.4.58
    * Changed pandas to version 1.3.4
    * Changed pytorch to version 1.10.0
    * Changed scikit-learn to version 1.0.1
    * Changed tensorflow-gpu to version 2.6.2


## October 7, 2021

New image for [Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview).

Version: `21.10.07`

Main changes:
 - Changed pytorch to version 1.9.1
 - Changed Docker to version 20.10.9
 - Changed Intellijidea to version 2021.2.2
 - Changed Nodejs to version v14.18.0
 - Changed Pycharm to version 2021.2.2
 - Changed VS Code to version 1.60.2
 - Fixed AutoML environment (azureml_py36_automl)
 - Fixed Azure Storage Explorer stability
 - Improvement of stability and minor bug fixes 


## August 11, 2021

New image for [Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview).

Version: `21.08.11`

Main changes:

- Windows Security update
- Update of Nvidia CuDNN to 8.1.0
- Update of Jupyter Lab -to 3.0.16
- Added MLFLow for experiment tracking
- Improvement of stability and minor bug fixes 



## July 12, 2021

New image for [Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview).

Main changes:

- Updated to PyTorch 1.9.0
- Updated Azure CLI to 2.26.1
- Updated Azure CLI Azure Machine Learning extension to 1.29.0
- Update VS Code version 1.58.1
- Improvement of stability and minor bug fixes 


## June 22, 2021

New image for [Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview).

Version: `21.06.22`

Main changes:

- Updated to PyTorch 1.9.0
- Fixed a bug where git wasn't available


## June 1, 2021

New image for [Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview).

Version: `21.06.01`

Main changes are:

- Docker is enabled by default
- JupyterHub is using JupyterLab by default
- Updated Python versions to fix [CVE-2020-15523](https://nvd.nist.gov/vuln/detail/CVE-2020-15523)
- Updated IntelliJ IDEA to version 2021.1 to fix [CVE-2021-25758](https://nvd.nist.gov/vuln/detail/CVE-2021-25758)
- Updated PyCharm Community to 2021.1
- Updated TensorFlow to version 2.5.0

<br/>
Removed several icons from desktop.

## May 22, 2021

New image for [Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview).

Version: `21.05.22`

Selected version updates are:
- CUDA 11.1
- Python 3.8
- PyTorch 1.8.1
- TensorFlow 2.5.0
- Spark 3.1.1
- Java 11
- R 4.1.0
- Julia 1.0.5
- NodeJS 16.2.0
- Visual Studio Code 1.56.2 incl. Azure ML extension
- PyCharm Community Edition 2021.1.1
- Jupyter Lab 2.2.6
- RStudio 1.4.1106
- Visual Studio Community Edition 2019 (version 16.9.6)
- Azure CLI 2.23.0
- Storage Explorer 1.19.1
- AzCopy 10.10.0
- Power BI Desktop 2.93.641.0 64-bit (May 2021)
- Azure Data Studio 1.28.0
- Microsoft Edge browser

<br/>
Removed Firefox, Apache Drill and Microsoft Integration Runtime.

<br/>
Dark mode, changed icons on desktop, wallpaper background change.

## May 12, 2021

New image for [Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview).

Selected version updates are:
- CUDA 11.3, cuDNN 8, NCCL2
- Python 3.8
- R 4.0.5
- Spark 3.1 incl. mmlspark, connectors to Blob Storage, Data Lake, Cosmos DB
- Java 11 (OpenJDK)
- Jupyter Lab 3.0.14
- PyTorch 1.8.1 incl. torchaudio torchtext torchvision, torch-tb-profiler
- TensorFlow 2.4.1 incl. TensorBoard
- dask 2021.01.0
- VS.Code 1.56
- Azure Data Studio 1.22.1
- Azure CLI 2.23.0
- Azure Storage Explorer 1.19.1
- azcopy 10.10
- Microsoft Edge browser (beta)

<br/>
Added docker. To save resources, the docker service isn't started by default. To start the docker service, run the
following command-line commands:

```
sudo systemctl start docker
```

> [!NOTE]
> If your machine has GPU(s), you can make use of the GPU(s) inside the containers by adding a `--gpus`
> parameter to your docker command.
>
> For example, running
>
> `sudo docker run --gpus all -it --rm -v local_dir:container_dir nvcr.io/nvidia/pytorch:18.04-py3`
>
> will run an Ubuntu 18.04 container with PyTorch pre-installed and all GPUs enabled. It will also make a local folder
> *local_dir* available in the container under *container_dir*.
>


## February 24, 2020

Data Science Virtual Machine images for [Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview) and [Windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview) images are now available.

## October 8, 2019

### Updates to software on the Windows DSVM

- Azure Storage Explorer 1.10.1
- Power BI Desktop 2.73.55xx
- Firefox 69.0.2
- PyCharm 19.2.3
- RStudio 1.2.50xx

### Default Browser for Windows updated

Earlier, the default browser was set to Internet Explorer. Users are now prompted to choose a default browser when they first sign in.
