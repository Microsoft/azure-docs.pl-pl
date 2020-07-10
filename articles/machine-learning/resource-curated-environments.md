---
title: Nadzorowane środowiska
titleSuffix: Azure Machine Learning
description: Zbieranie środowisk nadzorowanych dostępnych w Azure Machine Learning
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 9d0964fb28451abe0a77276d6f9d605fcaac2514
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156442"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning środowiska nadzorowane

W tym artykule wymieniono środowiska nadzorowane w Azure Machine Learning oraz pakiety i kanały, które są wstępnie zainstalowane w nich.

> [!NOTE]
> Ta lista jest aktualizowana od czerwca 2020. Użyj zestawu SDK języka Python, aby uzyskać najbardziej zaktualizowaną listę. Aby uzyskać więcej informacji, zobacz [artykuł](./how-to-use-environments.md#use-a-curated-environment)dotyczący środowisk.

## <a name="azure-automl"></a>Azure AutoML

- [AutoML Azure](#azureml-automl)
- [Procesor Azure AutoML GPU](#azureml-automl-gpu)
- [Azure AutoML DNN](#azureml-automl-dnn)
- [Procesor Azure AutoML DNN GPU](#azureml-automl-dnn-gpu)
- [Procesor GPU platformy Azure AutoML DNN](#azureml-automl-dnn-vision-gpu)

### <a name="azureml-automl"></a>AutoML Azure

kanały pakietów:
- Anaconda
- Conda — fałszowanie
- pytorch

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-Pipeline-Core = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure — Interpretuj = = 1.8.0
  - Azure-Wyjaśnij-model = = 1.8.0
  - Azure-automl-Core = = 1.8.0
  - Azure automl — środowisko uruchomieniowe = = 1.8.0
  - Azure-pociąg-automl-Client = = 1.8.0
  - Azure-pociąg-automl-Runtime = = 1.8.0. post1
  - Wnioskowanie — schemat
  - pyarrow = = 0.17.0
  - PR-cpuinfo = = 5.0.0
- numpy>= 1.16.0, <= 1.16.2
- Pandas>= 0.21.0, <= 0.23.4
- PR-xgboost<= 0,90
- fbprophet = = 0,5
- setuptools — git
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-gpu"></a>Procesor Azure AutoML GPU

kanały pakietów:
- Anaconda
- Conda — fałszowanie
- pytorch

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-Pipeline-Core = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure — Interpretuj = = 1.8.0
  - Azure-Wyjaśnij-model = = 1.8.0
  - Azure-automl-Core = = 1.8.0
  - Azure automl — środowisko uruchomieniowe = = 1.8.0
  - Azure-pociąg-automl-Client = = 1.8.0
  - Azure-pociąg-automl-Runtime = = 1.8.0. post1
  - Wnioskowanie — schemat
  - pyarrow = = 0.17.0
  - PR-cpuinfo = = 5.0.0
- numpy>= 1.16.0, <= 1.16.2
- Pandas>= 0.21.0, <= 0.23.4
- fbprophet = = 0,5
- setuptools — git
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-dnn"></a>Azure AutoML DNN

kanały pakietów:
- Anaconda
- Conda — fałszowanie
- pytorch

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-Pipeline-Core = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure — Interpretuj = = 1.8.0
  - Azure-Wyjaśnij-model = = 1.8.0
  - Azure-automl-Core = = 1.8.0
  - Azure automl — środowisko uruchomieniowe = = 1.8.0
  - Azure-pociąg-automl-Client = = 1.8.0
  - Azure-pociąg-automl-Runtime = = 1.8.0. post1
  - Wnioskowanie — schemat
  - pytorch-Transformatory = = 1.0.0
  - Spacy = = 2.1.8
  - https://aka.ms/automl-resources/packages/en_core_web_sm-2.1.0.tar.gz
  - pyarrow = = 0.17.0
  - PR-cpuinfo = = 5.0.0
- Pandas>= 0.21.0, <= 0.23.4
- numpy>= 1.16.0, <= 1.16.2
- PR-xgboost<= 0,90
- fbprophet = = 0,5
- setuptools — git
- pytorch = 1.4.0
- cudatoolkit = 10.0.130
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-dnn-gpu"></a>Procesor Azure AutoML DNN GPU

kanały pakietów:
- Anaconda
- Conda — fałszowanie
- pytorch

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-Pipeline-Core = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure — Interpretuj = = 1.8.0
  - Azure-Wyjaśnij-model = = 1.8.0
  - Azure-automl-Core = = 1.8.0
  - Azure automl — środowisko uruchomieniowe = = 1.8.0
  - Azure-pociąg-automl-Client = = 1.8.0
  - Azure-pociąg-automl-Runtime = = 1.8.0. post1
  - Wnioskowanie — schemat
  - horovod = = 0.19.4
  - pytorch-Transformatory = = 1.0.0
  - Spacy = = 2.1.8
  - https://aka.ms/automl-resources/packages/en_core_web_sm-2.1.0.tar.gz
  - pyarrow = = 0.17.0
  - PR-cpuinfo = = 5.0.0
- Pandas>= 0.21.0, <= 0.23.4
- numpy>= 1.16.0, <= 1.16.2
- fbprophet = = 0,5
- setuptools — git
- pytorch = 1.4.0
- cudatoolkit = 10.0.130
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-dnn-vision-gpu"></a>Procesor GPU AutoML DNN

tamten
- Python = 3.7
- graczy
  - Azure-automl-Core = = 1.8.0
  - Azure-Core = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Azure-pociąg-automl-Client = = 1.8.0
  - Azure automl — środowisko uruchomieniowe = = 1.8.0
  - Azure — Interpretuj = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Wyjaśnij-model = = 1.8.0
  - Azure-pociąg-automl-Runtime = = 1.8.0. post1
  - Azure-pociąg-automl = = 1.8.0
  - Azure-contrib-DataSet = = 1.8.0
  - Azure-Pipeline-Core = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - Azure-Pipeline-kroki = = 1.8.0
  - Azure-Pipeline = = 1.8.0
  - Azure — pociąg = = 1.8.0
  - Azure-SDK = = 1.8.0
  - Azure-contrib-automl-DNN-Vision = = 1.8.0

## <a name="azure-ml-designer"></a>Projektant Azure ML

- [Projektant Azure](#azureml-designer)
- [CV projektanta Azure](#azureml-designer-cv)
- [Transformacja CV projektanta Azure](#azureml-designer-cv-transform)
- [We/wy projektanta Azure](#azureml-designer-io)
- [NLP projektanta Azure](#azureml-designer-nlp)
- [PyTorch projektanta Azure](#azureml-designer-pytorch)
- [Uczenie PyTorch projektanta Azure](#azureml-designer-pytorch-train)
- [Projektant Azure — R](#azureml-designer-r)
- [Polecający projektanta Azure](#azureml-designer-recommender)
- [Ocena projektanta Azure](#azureml-designer-score)
- [Transformacja projektanta Azure](#azureml-designer-transform)

### <a name="azureml-designer"></a>Projektant Azure

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.8
- scikit-niespodziewane = 1.0.6
- graczy
  - Azure-Designer-Classic-module = = 0.0.124
  - https://github.com/explosion/spacy-models/releases/download/en_core_web_sm-2.1.0/en_core_web_sm-2.1.0.tar.gz#egg=en_core_web_sm
  - Spacy = = 2.1.7

### <a name="azureml-designer-cv"></a>CV projektanta Azure

kanały pakietów:
- Wartości domyślne

tamten
- Python = 3.6.8
- graczy
  - Azure-Designer-CV-modules = = 0.0.6

### <a name="azureml-designer-cv-transform"></a>Transformacja CV projektanta Azure

kanały pakietów:
- Wartości domyślne

tamten
- Python = 3.6.8
- graczy
  - Azure-Designer-CV-modules [pytorch] = = 0.0.6

### <a name="azureml-designer-io"></a>We/wy projektanta Azure

kanały pakietów:
- Wartości domyślne

tamten
- Python = 3.6.8
- graczy
  - Azure-dataprzygotowania>= 1,6
  - Azure-Designer-DataIO-modules = = 0.0.30

### <a name="azureml-designer-nlp"></a>NLP projektanta Azure

kanały pakietów:
- Wartości domyślne

tamten
- Python = 3.6.8
- graczy
  - Azure-Designer-Classic-module = = 0.0.121
  - https://github.com/explosion/spacy-models/releases/download/en_core_web_sm-2.1.0/en_core_web_sm-2.1.0.tar.gz#egg=en_core_web_sm
  - Spacy = = 2.1.7

### <a name="azureml-designer-pytorch"></a>PyTorch projektanta Azure

kanały pakietów:
- Wartości domyślne

tamten
- Python = 3.6.8
- graczy
  - Azure-Designer-pytorch-modules = = 0.0.8

### <a name="azureml-designer-pytorch-train"></a>Uczenie PyTorch projektanta Azure

kanały pakietów:
- Wartości domyślne

tamten
- Python = 3.6.8
- graczy
  - Azure-Designer-pytorch-modules = = 0.0.8

### <a name="azureml-designer-r"></a>Projektant Azure — R

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.8
- r-karetka = 6.0
- r-catools = 1.17.1
- r-Cluster = 2.1.0
- r-dplyr = 0.8.5
- r-e1071 = 1.7
- r-Forcats = 0.5.0
- r — Prognoza = 8.12
- r-glmnet = 2.0
- r-igraph = 1.2.4
- r-Matrix = 1.2
- r-mclust = 5.4.6
- r-mgcv = 1.8
- r-nlme = 3.1
- r-nnet = 7.3
- r-plyr = 1.8.6
- r-randomforest = 4.6
- r-reticulate = 1.12
- r-rocr = 1.0
- r-rodbc = 1.3
- r-rpart = 4.1
- r-String = 1.4.0
- r-tidyverse = 1.2.1
- r-timedate = 3043.102
- r-tseries = 0.10
- r = 3.5.1
- graczy
  - Azure-Designer-Classic-module = = 0.0.124

### <a name="azureml-designer-recommender"></a>Polecający projektanta Azure

kanały pakietów:
- Wartości domyślne

tamten
- Python = 3.6.8
- graczy
  - Azure-Designer-zalecamy-modules = = 0.0.5

### <a name="azureml-designer-score"></a>Ocena projektanta Azure

kanały pakietów:
- Wartości domyślne

tamten
- Conda
- Python = 3.6.8
- graczy
  - Azure-Designer-Score-modules = = 0.0.5

### <a name="azureml-designer-transform"></a>Transformacja projektanta Azure

kanały pakietów:
- Wartości domyślne

tamten
- Python = 3.6.8
- graczy
  - Azure-Designer-DataTransform-modules = = 0.0.49

## <a name="azureml-hyperdrive-forecastdnn"></a>ForecastDNN na dysku Azure

tamten
- Python = 3.7
- graczy
  - Azure-Core = = 1.8.0
  - Azure-Pipeline-Core = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-automl-Core = = 1.8.0
  - Azure automl — środowisko uruchomieniowe = = 1.8.0
  - Azure-pociąg-automl-Client = = 1.8.0
  - Azure-pociąg-automl-Runtime = = 1.8.0. post1
  - Azure-contrib-automl-DNN-prognozowanie = = 1.8.0

## <a name="azureml-minimal"></a>Minimum Azure

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0

## <a name="azureml-sidecar"></a>Przyczepka Azure

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2

## <a name="azureml-tutorial"></a>Samouczek Azure

kanały pakietów:
- Anaconda
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - Azure-widgets = = 1.8.0
  - Azure-Pipeline-Core = = 1.8.0
  - Azure-Pipeline-kroki = = 1.8.0
  - Azure-opendatasets = = 1.8.0
  - Azure-automl-Core = = 1.8.0
  - Azure automl — środowisko uruchomieniowe = = 1.8.0
  - Azure-pociąg-automl-Client = = 1.8.0
  - Azure-pociąg-automl-Runtime = = 1.8.0. post1  
  - Azure-pociąg-automl = = 1.8.0
  - Azure — pociąg = = 1.8.0
  - Azure-SDK = = 1.8.0
  - Azure — Interpretuj = = 1.8.0
  - Azure-tensorboard = = 1.8.0
  - Azure-mlflow = = 1.8.0
  - mlflow
  - skryptu sklearn — Pandas
- pandas
- numpy
- tqdm
- scikit-learn
- matplotlib

## <a name="azureml-vowpalwabbit-880"></a>Azure VowpalWabbit 8.8.0

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-[bezpiecznik, Pandas]

## <a name="dask"></a>Dask

- [Procesor Azure Dask CPU](#azureml-dask-cpu)
- [Procesor Azure Dask GPU](#azureml-dask-gpu)

### <a name="azureml-dask-cpu"></a>Procesor Azure Dask CPU

kanały pakietów:
- Conda — fałszowanie
- pytorch
- Wartości domyślne

tamten
- Python = 3.6.9
- graczy
  - adlfs
  - Azure-Core = = 1.8.0
  - azureml-dataprep
  - dask [kompletna]
  - dask-ml [kompletne]
  - podlega
  - fastparquet
  - fsspec
  - joblib
  - jupyterlab
  - lz4
  - mpi4py
  - notesu
  - pyarrow

### <a name="azureml-dask-gpu"></a>Procesor Azure Dask GPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.9
- graczy
  - Azure-wartości domyślne = = 1.8.0
  - adlfs
  - Azure-Core = = 1.8.0
  - azureml-dataprep
  - dask [kompletna]
  - dask-ml [kompletne]
  - podlega
  - fastparquet
  - fsspec
  - joblib
  - jupyterlab
  - lz4
  - mpi4py
  - notesu
  - pyarrow
- matplotlib

## <a name="chainer"></a>Chainer

- [Procesor Azure łańcucha 5.1.0 CPU](#azureml-chainer-510-cpu)
- [Procesor Azure 5.1.0 GPU](#azureml-chainer-510-gpu)

### <a name="azureml-chainer-510-cpu"></a>Procesor Azure łańcucha 5.1.0 CPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - łańcucha = = 5.1.0
  - mpi4py = = 3.0.0

### <a name="azureml-chainer-510-gpu"></a>Procesor Azure 5.1.0 GPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - łańcucha = = 5.1.0
  - cupy-cuda90 = = 5.1.0
  - mpi4py = = 3.0.0

## <a name="pyspark"></a>PySpark

### <a name="azureml-pyspark-mmlspark-015"></a>Azure PySpark MmlSpark 0,15

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0

## <a name="pytorch"></a>PyTorch

- [Azure PyTorch 1,0 — procesor CPU](#azureml-pytorch-10-cpu)
- [Procesor Azure PyTorch 1,0 GPU](#azureml-pytorch-10-gpu)
- [Azure PyTorch 1,1 — procesor CPU](#azureml-pytorch-11-cpu)
- [Procesor Azure PyTorch 1,1 GPU](#azureml-pytorch-11-gpu)
- [Azure PyTorch 1,2 — procesor CPU](#azureml-pytorch-12-cpu)
- [Procesor Azure PyTorch 1,2 GPU](#azureml-pytorch-12-gpu)
- [Azure PyTorch 1,3 — procesor CPU](#azureml-pytorch-13-cpu)
- [Procesor Azure PyTorch 1,3 GPU](#azureml-pytorch-13-gpu)
- [Azure PyTorch 1,4 — procesor CPU](#azureml-pytorch-14-cpu)
- [Procesor Azure PyTorch 1,4 GPU](#azureml-pytorch-14-gpu)
- [Azure PyTorch 1,5 — procesor CPU](#azureml-pytorch-15-cpu)
- [Procesor Azure PyTorch 1,5 GPU](#azureml-pytorch-15-gpu)

### <a name="azureml-pytorch-10-cpu"></a>Azure PyTorch 1,0 — procesor CPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - Torch = = 1.0
  - torchvision = = 0.2.1
  - MKL = = 2018.0.3
  - horovod = = 0.16.1

### <a name="azureml-pytorch-10-gpu"></a>Procesor Azure PyTorch 1,0 GPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - Torch = = 1.0
  - torchvision = = 0.2.1
  - MKL = = 2018.0.3
  - horovod = = 0.16.1

### <a name="azureml-pytorch-11-cpu"></a>Azure-PyTorch 1,1 — procesor CPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - Torch = = 1.1
  - torchvision = = 0.2.1
  - MKL = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard = = 1.14.0
  - przyszłość = = 0.17.1

### <a name="azureml-pytorch-11-gpu"></a>Procesor Azure PyTorch 1,1 GPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - Torch = = 1.1
  - torchvision = = 0.2.1
  - MKL = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard = = 1.14.0
  - przyszłość = = 0.17.1

### <a name="azureml-pytorch-12-cpu"></a>Azure PyTorch 1,2 — procesor CPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - Torch = = 1.2
  - torchvision = = 0.4.0
  - MKL = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard = = 1.14.0
  - przyszłość = = 0.17.1

### <a name="azureml-pytorch-12-gpu"></a>Procesor Azure PyTorch 1,2 GPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - Torch = = 1.2
  - torchvision = = 0.4.0
  - MKL = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard = = 1.14.0
  - przyszłość = = 0.17.1

### <a name="azureml-pytorch-13-cpu"></a>Azure PyTorch 1,3 — procesor CPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - Torch = = 1.3
  - torchvision = = 0.4.1
  - MKL = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard = = 1.14.0
  - przyszłość = = 0.17.1

### <a name="azureml-pytorch-13-gpu"></a>Procesor Azure PyTorch 1,3 GPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - Torch = = 1.3
  - torchvision = = 0.4.1
  - MKL = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard = = 1.14.0
  - przyszłość = = 0.17.1

### <a name="azureml-pytorch-14-cpu"></a>Azure PyTorch 1,4 — procesor CPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - Torch = = 1.4.0
  - torchvision = = 0.5.0
  - MKL = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard = = 1.14.0
  - przyszłość = = 0.17.1

### <a name="azureml-pytorch-14-gpu"></a>Procesor Azure PyTorch 1,4 GPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - Torch = = 1.4.0
  - torchvision = = 0.5.0
  - MKL = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard = = 1.14.0
  - przyszłość = = 0.17.1

### <a name="azureml-pytorch-15-cpu"></a>Azure PyTorch 1,5 — procesor CPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - Torch = = 1.5.0
  - torchvision = = 0.5.0
  - MKL = = 2018.0.3
  - horovod = = 0.19.1
  - tensorboard = = 1.14.0
  - przyszłość = = 0.17.1

### <a name="azureml-pytorch-15-gpu"></a>Procesor Azure PyTorch 1,5 GPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - Torch = = 1.5.0
  - torchvision = = 0.5.0
  - MKL = = 2018.0.3
  - horovod = = 0.19.1
  - tensorboard = = 1.14.0
  - przyszłość = = 0.17.1

## <a name="scikit-learn"></a>Scikit

### <a name="azureml-scikit-learn-0203"></a>Azure Scikit — uczenie 0.20.3

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - scikit-Dowiedz się = = 0.20.3
  - scipy = = 1.2.1
  - joblib = = 0.13.2

## <a name="tensorflow"></a>TensorFlow

- [Azure ML TensorFlow 1,10 — procesor CPU](#azureml-tensorflow-110-cpu)
- [Azure ML TensorFlow 1,10 GPU](#azureml-tensorflow-110-gpu)
- [Azure ML TensorFlow 1,12 — procesor CPU](#azureml-tensorflow-112-cpu)
- [Azure ML TensorFlow 1,12 GPU](#azureml-tensorflow-112-gpu)
- [Azure ML TensorFlow 1,13 — procesor CPU](#azureml-tensorflow-113-cpu)
- [Azure ML TensorFlow 1,13 GPU](#azureml-tensorflow-113-gpu)
- [Azure ML TensorFlow 2,0 — procesor CPU](#azureml-tensorflow-20-cpu)
- [Azure ML TensorFlow 2,0 GPU](#azureml-tensorflow-20-gpu)
- [Azure ML TensorFlow 2,1 — procesor CPU](#azureml-tensorflow-21-cpu)
- [Azure ML TensorFlow 2,1 GPU](#azureml-tensorflow-21-gpu)

### <a name="azureml-tensorflow-110-cpu"></a>Azure TensorFlow 1,10 — procesor CPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - tensorflow = = 1.10
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-110-gpu"></a>Procesor Azure TensorFlow 1,10 GPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - tensorflow-GPU = = 1.10.0
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-112-cpu"></a>Azure TensorFlow 1,12 — procesor CPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - tensorflow = = 1.12
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-112-gpu"></a>Procesor Azure TensorFlow 1,12 GPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - tensorflow-GPU = = 1.12.0
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-113-cpu"></a>Azure-TensorFlow 1,13 — procesor CPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - tensorflow = = 1.13.1
  - horovod = = 0.16.1

### <a name="azureml-tensorflow-113-gpu"></a>Procesor Azure TensorFlow 1,13 GPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - tensorflow-GPU = = 1.13.1
  - horovod = = 0.16.1

### <a name="azureml-tensorflow-20-cpu"></a>Azure TensorFlow 2,0 — procesor CPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - tensorflow = = 2.0
  - horovod = = 0.18.1

### <a name="azureml-tensorflow-20-gpu"></a>Procesor Azure TensorFlow 2,0 GPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - tensorflow-GPU = = 2.0.0
  - horovod = = 0.18.1

### <a name="azureml-tensorflow-21-cpu"></a>Azure TensorFlow 2,1 — procesor CPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - tensorflow = = 2.1.0
  - horovod = = 0.19.1

### <a name="azureml-tensorflow-21-gpu"></a>Procesor Azure TensorFlow 2,1 GPU

kanały pakietów:
- Conda — fałszowanie

tamten
- Python = 3.6.2
- graczy
  - Azure-Core = = 1.8.0
  - Azure-wartości domyślne = = 1.8.0
  - Azure-Telemetria = = 1.8.0
  - Uczenie maszynowe — restclients-dysk = = 1.8.0
  - Azure-pociąg-Core = = 1.8.0
  - tensorflow-GPU = = 2.1.0
  - horovod = = 0.19.1

