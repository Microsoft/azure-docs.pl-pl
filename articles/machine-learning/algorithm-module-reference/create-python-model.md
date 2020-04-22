---
title: 'Tworzenie modelu Języka Python: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu Tworzenie modelu języka Python w usłudze Azure Machine Learning w celu utworzenia niestandardowego modułu modelowania lub przetwarzania danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c8be0882452dc120f538394a5481769e26e3fa15
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682803"
---
# <a name="create-python-model-module"></a>Tworzenie modułu modelu języka Python

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Dowiedz się, jak użyć modułu Tworzenie modelu języka Python, aby utworzyć nieprzeszkolony model ze skryptu Języka Python. Model można oprzeć na dowolnym uczniu, który jest zawarty w pakiecie języka Python w środowisku projektanta usługi Azure Machine Learning. 

Po utworzeniu modelu można użyć [modelu pociągu](train-model.md) do uczenia modelu na zestawie danych, jak każdy inny uczeń w usłudze Azure Machine Learning. Przeszkolony model można przekazać do [score model](score-model.md) do prognozowania. Następnie można zapisać przeszkolony model i opublikować przepływ pracy oceniania jako usługę sieci web.

> [!WARNING]
> Obecnie nie jest możliwe przekazanie wyników ocenianego modelu Języka Python w celu [oceny modelu.](evaluate-model.md) Jeśli chcesz ocenić model, możesz napisać niestandardowy skrypt języka Python i uruchomić go przy użyciu modułu [Wykonywanie skryptu Pythona.](execute-python-script.md)  


## <a name="configure-the-module"></a>Konfigurowanie modułu

Korzystanie z tego modułu wymaga pośredniej lub specjalistycznej wiedzy języka Python. Moduł obsługuje korzystanie z dowolnego ucznia, który jest zawarty w pakietach Pythona już zainstalowanych w usłudze Azure Machine Learning. Zobacz listę preinstalowanych pakietów Pythona w [języku Execute Python Script](execute-python-script.md).

> [!NOTE]
> Należy zachować szczególną ostrożność podczas pisania skryptu i upewnia się, że nie ma błędu składni, takich jak przy użyciu nieo zadeklarowanego obiektu lub niezaimportowanego modułu.

> [!NOTE]
Należy również zwrócić szczególną uwagę na listę wstępnie zainstalowanych modułów w [Execute Python Script](execute-python-script.md). Importuj tylko wstępnie zainstalowane moduły. Proszę nie instalować dodatkowych pakietów, takich jak "pip install xgboost" w tym skrypcie, w przeciwnym razie błędy zostaną podniesione podczas czytania modeli w modułach down-stream.
  
W tym artykule pokazano, jak używać **tworzenia modelu języka Python** za pomocą prostego potoku. Oto diagram potoku:

![Diagram tworzenia modelu języka Python](./media/module/create-python-model.png)

1. Wybierz **pozycję Utwórz model języka Python**i edytuj skrypt, aby zaimplementować proces modelowania lub zarządzania danymi. Model można oprzeć na dowolnym uczniu, który jest zawarty w pakiecie języka Python w środowisku usługi Azure Machine Learning.

> [!NOTE]
> Należy zwrócić szczególną uwagę na komentarze w przykładowym kodzie skryptu i upewnij się, że skrypt ściśle jest zgodny z wymaganiami, w tym nazwę klasy, metody, a także podpis metody. Naruszenie będzie prowadzić do wyjątków. 

   Poniższy przykładowy kod klasyfikatora Naive Bayes używa popularnego pakietu *sklearn:*

   ```Python

   # The script MUST define a class named AzureMLModel.
   # This class MUST at least define the following three methods:
       # __init__: in which self.model must be assigned,
       # train: which trains self.model, the two input arguments must be pandas DataFrame,
       # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
   # The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.

   # Please do not install extra packages such as "pip install xgboost" in this script,
   # otherwise errors will be raised when reading models in down-stream modules.
   
   import pandas as pd
   from sklearn.naive_bayes import GaussianNB


   class AzureMLModel:
       def __init__(self):
           self.model = GaussianNB()
           self.feature_column_names = list()

       def train(self, df_train, df_label):
           # self.feature_column_names records the column names used for training.
           # It is recommended to set this attribute before training so that the
           # feature columns used in predict and train methods have the same names.
           self.feature_column_names = df_train.columns.tolist()
           self.model.fit(df_train, df_label)

       def predict(self, df):
           # The feature columns used for prediction MUST have the same names as the ones for training.
           # The name of score column ("Scored Labels" in this case) MUST be different from any other columns in input data.
           return pd.DataFrame(
               {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
                'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
           )


   ```

1. Połącz moduł **Utwórz model Języka Python,** który został właśnie utworzony w celu **modelu pociągu** i **modelu wyników**.

1. Jeśli chcesz ocenić model, dodaj moduł [Wykonaj skrypt języka Python](execute-python-script.md) i edytuj skrypt Pythona.

   Poniższy skrypt to przykładowy kod oceny:

   ```Python


   # The script MUST contain a function named azureml_main
   # which is the entry point for this module.

   # imports up here can be used to 
   import pandas as pd

   # The entry point function can contain up to two input arguments:
   #   Param<dataframe1>: a pandas.DataFrame
   #   Param<dataframe2>: a pandas.DataFrame
   def azureml_main(dataframe1 = None, dataframe2 = None):
    
       from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
       import pandas as pd
       import numpy as np
    
       scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
       ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
       ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
       probabilities = scores["probabilities"]
    
       accuracy, precision, recall, auc = \
       accuracy_score(ytrue, ypred),\
       precision_score(ytrue, ypred),\
       recall_score(ytrue, ypred),\
       roc_auc_score(ytrue, probabilities)
    
       metrics = pd.DataFrame();
       metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
       metrics["Value"] = [accuracy, precision, recall, auc]
    
       return metrics,

   ```

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 