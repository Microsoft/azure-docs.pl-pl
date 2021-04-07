---
title: Przykładowe programy & podprzewodniki dotyczące ML
titleSuffix: Azure Data Science Virtual Machine
description: Za pomocą tych przykładów i przewodników dowiesz się, jak obsługiwać typowe zadania i scenariusze przy użyciu Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: cda5dfd936243602775e1f4f965032b9d746b0b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519766"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Przykłady dotyczące Virtual Machines analizy danych na platformie Azure

Usługa Azure Data Science Virtual Machines (DSVMs) zawiera kompleksowy zestaw przykładowego kodu. Te przykłady obejmują notesy Jupyter i skrypty w językach takich jak Python i R.
> [!NOTE]
> Więcej informacji o sposobach uruchamiania notesów Jupyter na maszynach wirtualnych do nauki o danych znajduje się w sekcji [dostęp do Jupyter](#access-jupyter) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było uruchomić te przykłady, trzeba mieć zainicjowaną [Ubuntu Data Science Virtual Machine](./dsvm-ubuntu-intro.md).

## <a name="available-samples"></a>Dostępne przykłady
| Kategoria przykładów | Opis | Lokalizacje |
| ------------- | ------------- | ------------- |
| Język R  | Przykłady ilustrują scenariusze, takie jak nawiązywanie połączenia z magazynami danych w chmurze opartymi na platformie Azure i porównywanie języka R i Microsoft Machine Learning Server typu open source. Wyjaśniono także, jak operacjonalizować modele na Microsoft Machine Learning Server i SQL Server. <br/> [Język R](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Język Python  | Przykłady wyjaśniają scenariusze, takie jak nawiązywanie połączenia z magazynami danych w chmurze opartymi na platformie Azure i jak korzystać z Azure Machine Learning.  <br/> [Język Python](#python-language) | <br/>`~notebooks` <br/><br/>|
| Język Julia  | Zawiera szczegółowy opis wykreślania i uczenia głębokiego w Julia. Wyjaśniono również, jak wywoływać C i Python z Julia. <br/> [Język Julia](#julia-language) |<br/> W systemie Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> W systemie Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Ilustruje sposób tworzenia modeli uczenia maszynowego i uczenia głębokiego przy użyciu Machine Learning. Wdrażaj modele w dowolnym miejscu. Użyj zautomatyzowanej uczenia maszynowego i inteligentnego dostrajania parametrów. Należy również użyć zarządzania modelami i szkolenia rozproszonego. <br/> [Usługa Machine Learning](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| Notesy PyTorch  | Przykłady głębokiej uczenia, które używają sieci neuronowych opartych na PyTorch. Notesy przedziały od początku do zaawansowanych scenariuszy.  <br/> [Notesy PyTorch](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Wiele przykładów i technik sieci neuronowych zaimplementowanych przy użyciu platformy TensorFlow. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Zestaw narzędzi usług Microsoft Cognitive <br/>   | Przykłady głębokiej uczenia opublikowane przez zespół Cognitive Toolkit w firmie Microsoft.  <br/> [Zestaw narzędzi Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> W systemie Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Przykłady głębokiej uczenia, które używają sieci neuronowych opartych na Caffe2. Kilka notesów umożliwia zapoznanie się z programem Caffe2 i korzystanie z niego efektywnie. Przykłady obejmują przetwarzanie wstępne obrazu i tworzenie zestawu danych. Obejmują one również regresję i sposób korzystania z modeli. <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Przykłady bazujące na języku Python, które wykorzystują program H2O dla rzeczywistych scenariuszy problemów. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Język SparkML  | Przykłady korzystające z funkcji zestawu narzędzi Apache Spark MLLib za pomocą pySpark i MMLSpark: Microsoft Machine Learning dla Apache Spark na Apache Spark 2. x.  <br/> [Język SparkML](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Standardowe przykłady uczenia maszynowego w XGBoost na potrzeby scenariuszy, takich jak klasyfikowanie i regresja. <br/> [XGBoost](#xgboost) | <br/>W systemie Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Dostęp do Jupyter 

Aby uzyskać dostęp do Jupyter, wybierz ikonę **Jupyter** w menu pulpitu lub aplikacji. Możesz również uzyskać dostęp do Jupyter w wersji systemu Linux DSVM. Aby uzyskać dostęp zdalnie z przeglądarki sieci Web, przejdź do strony `https://<Full Domain Name or IP Address of the DSVM>:8000` w witrynie Ubuntu.

Aby dodać wyjątki i udostępnić Jupyter dostęp za pośrednictwem przeglądarki, Skorzystaj z następujących wskazówek:


![Włącz wyjątek Jupyter](./media/ubuntu-jupyter-exception.png)


Zaloguj się przy użyciu tego samego hasła, którego używasz do logowania się do Data Science Virtual Machine.
<br/>

**Strona główna Jupyter**
<br/>![Strona główna Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Język R 
<br/>![Przykłady języka R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Język Python
<br/>![Przykłady w języku Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Język Julia 
<br/>![Przykłady Julia](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Azure Machine Learning 
<br/>![Przykłady Azure Machine Learning](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![Przykłady PyTorch](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Przykłady TensorFlow](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![Przykłady CNTK](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![Przykłady caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![H2O — przykłady](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Przykłady SparkML](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Przykłady XGBoost](./media/xgboost-samples.png)<br/>

