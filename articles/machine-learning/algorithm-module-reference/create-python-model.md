---
title: 'Tworzenie modelu języka Python: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu tworzenia modelu języka Python w Azure Machine Learning, aby utworzyć niestandardowy moduł modelowania lub przetwarzania danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/18/2020
ms.openlocfilehash: 03f80ebeb7b30d8c0fe14060335541d77b7523b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898617"
---
# <a name="create-python-model-module"></a>Utwórz moduł modelu języka Python

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Dowiedz się, jak za pomocą modułu tworzenia modelu języka Python utworzyć niepociąg model ze skryptu języka Python. Model można oprzeć na dowolnym zasobie, który znajduje się w pakiecie języka Python w środowisku projektanta Azure Machine Learning. 

Po utworzeniu modelu można użyć [modelu uczenia](train-model.md) do uczenia modelu w zestawie danych, podobnie jak w przypadku innych dowolnych informacji w Azure Machine Learning. Przeszkolony model można przesłać do [modelu oceny](score-model.md) , aby dokonać prognoz. Następnie można zapisać szkolony model i opublikować przepływ pracy oceniania jako usługę sieci Web.

> [!WARNING]
> Obecnie nie jest możliwe połączenie tego modułu w celu dostosowania modułu z **parametrami modelu** lub przekazanie wyników oceny modelu języka Python do [oceny modelu](evaluate-model.md). Jeśli musisz dostroić parametry lub oszacować model, możesz napisać niestandardowy skrypt języka Python za pomocą modułu [skryptu języka Python](execute-python-script.md) .


## <a name="configure-the-module"></a>Konfigurowanie modułu

Korzystanie z tego modułu wymaga pośredniej lub specjalistycznej wiedzy o języku Python. Moduł obsługuje korzystanie z dowolnych informacji, które znajdują się w pakietach języka Python zainstalowanych już w Azure Machine Learning. Zapoznaj się z wstępnie zainstalowaną listą pakietów języka Python w artykule [wykonywanie skryptu języka Python](execute-python-script.md).

> [!NOTE]
> Należy zachować ostrożność podczas pisania skryptu i upewnić się, że nie występuje błąd składniowy, taki jak użycie niezadeklarowanego obiektu lub zaimportowanego modułu.

> [!NOTE]
> Zwróć również uwagę na listę wstępnie zainstalowanych modułów w [skrypcie wykonaj skrypt języka Python](execute-python-script.md). Importuj tylko wstępnie zainstalowane moduły. Nie instaluj dodatkowych pakietów, takich jak "pip install xgboost" w tym skrypcie, w przeciwnym razie błędy zostaną zgłoszone podczas odczytywania modeli w modułach w usłudze Stream.
  
W tym artykule pokazano, jak używać metody **Create Python model** z prostym potokiem. Oto diagram potoku:

![Diagram tworzenia modelu języka Python](./media/module/create-python-model.png)

1. Wybierz pozycję **Utwórz model Python**i Edytuj skrypt, aby zaimplementować proces modelowania lub zarządzania danymi. Model można utworzyć na podstawie dowolnych informacji, które znajdują się w pakiecie Python w środowisku Azure Machine Learning.

> [!NOTE]
> Należy zwrócić szczególną uwagę na komentarze w przykładowym kodzie skryptu i upewnić się, że skrypt jest ściśle zgodny z wymaganiami, w tym nazwą klasy, metodami, a także sygnaturą metody. Naruszenie będzie prowadzić do wyjątków. 

   Następujący przykładowy kod klasyfikatora algorytm Bayesa Bayesa używa popularnego pakietu *skryptu sklearn* :

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

2. Połącz moduł **Create Python model** , który właśnie został utworzony, aby **szkolić model** i **model oceny**.

3. Jeśli musisz oszacować model, Dodaj moduł [skryptu języka Python](execute-python-script.md) i Edytuj skrypt języka Python.

   Następujący skrypt to przykładowy kod oceny:

   ```Python


   # The script MUST contain a function named azureml_main
   # which is the entry point for this module.

   # imports up here can be used to 
   import pandas as pd

   # The entry point function MUST have two input arguments:
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

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 