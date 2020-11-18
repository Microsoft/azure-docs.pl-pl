---
title: Wyselekcjonowane środowiska
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej o Azure Machine Learning środowiskach nadzorowanych, zestawie wstępnie skonfigurowanych środowisk, które ułatwiają skrócenie czasu przygotowania eksperymentu i wdrożenia.
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.date: 11/16/2020
ms.openlocfilehash: 0e6817e03c5e5363ad925367b0632e26fc2da4a2
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701402"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning środowiska nadzorowane

W tym artykule wymieniono środowiska nadzorowane w Azure Machine Learning oraz pakiety i kanały, które są wstępnie zainstalowane w nich. Środowiska nadzorowane są udostępniane przez Azure Machine Learning i są domyślnie dostępne w obszarze roboczym. Są one obsługiwane przez buforowane obrazy platformy Docker, co zmniejsza koszt przygotowania i pozwala skrócić czas wdrażania. Korzystaj z tych środowisk, aby szybko rozpocząć pracę z różnymi platformami uczenia maszynowego.

> [!NOTE]
> Ta lista jest aktualizowana z dniem 2020 września. Użyj zestawu SDK języka Python, aby uzyskać najbardziej zaktualizowaną listę. Aby uzyskać więcej informacji, zobacz [artykuł](./how-to-use-environments.md#use-a-curated-environment)dotyczący środowisk.

## <a name="azureml-automl"></a>AzureML-AutoML

**Kanały pakietów:**

* Anaconda
* Conda — fałszowanie
* pytorch

**Pakiety Conda:**

* python
* numpy
* scikit-learn
* Pandas
* PR — xgboost
* fbprophet
* Świąteczny
* setuptools — git
* psutil

**Pakiety PIP:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* Azure — ustawienia domyślne
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* Wnioskowanie — schemat
* PR — cpuinfo

## <a name="azureml-automl-dnn"></a>Azure-AutoML-DNN

**Kanały pakietów:**

* Anaconda
* Conda — fałszowanie
* pytorch

**Pakiety Conda:**

* python
* numpy
* scikit-learn
* Pandas
* PR — xgboost
* fbprophet
* Świąteczny
* setuptools — git
* pytorch
* cudatoolkit
* psutil

**Pakiety PIP:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* Azure — ustawienia domyślne
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* Wnioskowanie — schemat
* pytorch — Transformatory
* spacy
* en_core_web_sm
* PR — cpuinfo

## <a name="azureml-automl-dnn-gpu"></a>Azure-AutoML-DNN-GPU

**Kanały pakietów:**

* Anaconda
* Conda — fałszowanie
* pytorch

**Pakiety Conda:**

* python
* numpy
* scikit-learn
* Pandas
* fbprophet
* Świąteczny
* setuptools — git
* pytorch
* cudatoolkit
* psutil

**Pakiety PIP:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* Azure — ustawienia domyślne
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* Wnioskowanie — schemat
* horovod
* pytorch — Transformatory
* spacy
* en_core_web_sm
* PR — cpuinfo

## <a name="azureml-automl-dnn-vision-gpu"></a>Azure-AutoML-DNN-Vision-GPU

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — zestaw danych — środowisko uruchomieniowe
* azureml-contrib-dataset
* azureml-telemetry
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* Azure — ustawienia domyślne
* azureml-interpret
* azureml-explain-model
* azureml-train-automl-runtime
* azureml-train-automl
* Azure-contrib-automl-DNN-Vision

## <a name="azureml-automl-gpu"></a>Azure-AutoML-GPU

**Kanały pakietów:**

* Anaconda
* Conda — fałszowanie
* pytorch

**Pakiety Conda:**

* python
* numpy
* scikit-learn
* Pandas
* fbprophet
* Świąteczny
* setuptools — git
* psutil

**Pakiety PIP:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* Azure — ustawienia domyślne
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* Wnioskowanie — schemat
* PR — cpuinfo

## <a name="azureml-chainer-510-cpu"></a>Azure — łańcuch — 5.1.0 — procesor CPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* Chainer
* mpi4py

## <a name="azureml-chainer-510-gpu"></a>Azure-łańcucha-5.1.0-GPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* Chainer
* cupy-cuda90
* mpi4py

## <a name="azureml-dask-cpu"></a>Azure Dask — procesor CPU

**Kanały pakietów:**

* Conda — fałszowanie
* pytorch
* Wartości domyślne

**Pakiety Conda:**

* python

**Pakiety PIP:**

* adlfs
* azureml-core
* Azure — zestaw danych — środowisko uruchomieniowe
* dask [kompletna]
* dask-ml [kompletne]
* podlega
* fastparquet
* fsspec
* joblib
* jupyterlab
* lz4
* mpi4py
* notebook
* pyarrow

## <a name="azureml-dask-gpu"></a>Azure-Dask-GPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python
* matplotlib

**Pakiety PIP:**

* Azure — ustawienia domyślne
* adlfs
* azureml-core
* dask [kompletna]
* dask-ml [kompletne]
* podlega
* fastparquet
* fsspec
* joblib
* jupyterlab
* lz4
* mpi4py
* notebook
* pyarrow

## <a name="azureml-hyperdrive-forecastdnn"></a>Uczenie maszynowe — ForecastDNN

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* Azure — ustawienia domyślne
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* Azure-contrib-automl-DNN — prognozowanie

## <a name="azureml-minimal"></a>AzureML-Minimal

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne

## <a name="azureml-pyspark-mmlspark-015"></a>Azure-PySpark-MmlSpark-0,15

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core

## <a name="azureml-pytorch-10-cpu"></a>Azure-PyTorch-1,0-procesor CPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* torch
* torchvision
* mkl
* horovod

## <a name="azureml-pytorch-10-gpu"></a>Azure-PyTorch-1,0-GPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* torch
* torchvision
* mkl
* horovod

## <a name="azureml-pytorch-11-cpu"></a>Azure-PyTorch-1,1-procesor CPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* kontrakt

## <a name="azureml-pytorch-11-gpu"></a>Azure-PyTorch-1,1-GPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* kontrakt

## <a name="azureml-pytorch-12-cpu"></a>Azure-PyTorch-1,2-procesor CPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* kontrakt

## <a name="azureml-pytorch-12-gpu"></a>Azure-PyTorch-1,2-GPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* kontrakt

## <a name="azureml-pytorch-13-cpu"></a>Azure-PyTorch-1,3-procesor CPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* kontrakt

## <a name="azureml-pytorch-13-gpu"></a>Azure-PyTorch-1,3-GPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* kontrakt

## <a name="azureml-pytorch-14-cpu"></a>Azure-PyTorch-1,4-procesor CPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* kontrakt

## <a name="azureml-pytorch-14-gpu"></a>Azure-PyTorch-1,4-GPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* kontrakt

## <a name="azureml-pytorch-15-cpu"></a>Azure-PyTorch-1,5-procesor CPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* kontrakt

## <a name="azureml-pytorch-15-gpu"></a>Azure-PyTorch-1,5-GPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* kontrakt

## <a name="azureml-pytorch-16-cpu"></a>Azure-PyTorch-1,6-procesor CPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* kontrakt

## <a name="azureml-pytorch-16-gpu"></a>Azure-PyTorch-1,6-GPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* kontrakt

## <a name="azureml-scikit-learn-0203"></a>Azure-Scikit-uczenie 0.20.3

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* scikit-learn
* scipy
* joblib

## <a name="azureml-tensorflow-110-cpu"></a>Azure-TensorFlow-1,10-procesor CPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-110-gpu"></a>Azure-TensorFlow-1,10-GPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* tensorflow — GPU
* horovod

## <a name="azureml-tensorflow-112-cpu"></a>Azure-TensorFlow-1,12-procesor CPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-112-gpu"></a>Azure-TensorFlow-1,12-GPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* tensorflow — GPU
* horovod

## <a name="azureml-tensorflow-113-cpu"></a>Azure-TensorFlow-1,13-procesor CPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-113-gpu"></a>Azure-TensorFlow-1,13-GPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* tensorflow — GPU
* horovod

## <a name="azureml-tensorflow-20-cpu"></a>Azure-TensorFlow-2,0-procesor CPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-20-gpu"></a>Azure-TensorFlow-2,0-GPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* tensorflow — GPU
* horovod

## <a name="azureml-tensorflow-21-cpu"></a>Azure-TensorFlow-2,1-procesor CPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-21-gpu"></a>Azure-TensorFlow-2,1-GPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* tensorflow — GPU
* horovod

## <a name="azureml-tensorflow-22-cpu"></a>Azure-TensorFlow-2,2-procesor CPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-22-gpu"></a>Azure-TensorFlow-2,2-GPU

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* tensorflow — GPU
* horovod

## <a name="azureml-tutorial"></a>AzureML-Tutorial

**Kanały pakietów:**

* Anaconda
* Conda — fałszowanie

**Pakiety Conda:**

* python
* Pandas
* numpy
* tqdm
* scikit-learn
* matplotlib

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* azureml-telemetry
* Uczenie maszynowe — restclients — dysk
* azureml-train-core
* azureml-widgets
* azureml-pipeline-core
* azureml-pipeline-steps
* azureml-opendatasets
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-train-automl
* Azure — szkolenie
* Azure-SDK
* azureml-interpret
* azureml-tensorboard
* azureml-mlflow
* mlflow
* skryptu sklearn — Pandas

## <a name="azureml-vowpalwabbit-880"></a>Azure-VowpalWabbit-8.8.0

**Kanały pakietów:**

* Conda — fałszowanie

**Pakiety Conda:**

* python

**Pakiety PIP:**

* azureml-core
* Azure — ustawienia domyślne
* Azure-DataSet-Runtime [bezpiecznik, Pandas]
