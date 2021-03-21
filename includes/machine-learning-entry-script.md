---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 95acb028867caf6f497dd99ad3082efcaab09c7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93325300"
---
Skrypt wejściowy odbiera dane przesyłane do wdrożonej usługi internetowej i przekazuje je do modelu. Następnie pobiera odpowiedź zwróconą przez model i zwraca ją do klienta. *Skrypt jest specyficzny dla modelu*. Musi on zrozumieć dane, które są oczekiwane i zwracane przez model.

Poniżej przedstawiono dwie rzeczy, które należy wykonać w skrypcie wprowadzania:

1. Ładowanie modelu (przy użyciu funkcji o nazwie `init()` )
1. Uruchamianie modelu na danych wejściowych (przy użyciu funkcji o nazwie `run()` )

Przechodźmy tu szczegółowo.

### <a name="writing-init"></a>Pisanie init () 

#### <a name="loading-a-registered-model"></a>Ładowanie zarejestrowanego modelu

Zarejestrowane modele są przechowywane w ścieżce wskazywanej przez zmienną środowiskową o nazwie `AZUREML_MODEL_DIR` . Aby uzyskać więcej informacji na temat dokładnej struktury katalogów, zobacz [Lokalizowanie plików modelu w skrypcie wpisu](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models)

#### <a name="loading-a-local-model"></a>Ładowanie modelu lokalnego

Jeśli wybrano opcję rejestrowania modelu i przeszedł model jako część katalogu źródłowego, można odczytać go w taki sam sposób, jak w przypadku lokalnego, przekazując ścieżkę do modelu względem Twojego skryptu oceniania. Na przykład, jeśli katalog ma strukturę:

```bash

- source_dir
    - score.py
    - models
        - model1.onnx

```

możesz ładować modele przy użyciu następującego kodu w języku Python:

```python
import os

model = open(os.path.join('.', 'models', 'model1.onnx'))
```

### <a name="writing-run"></a>Zapis przebiegu ()

`run()` jest wykonywane za każdym razem, gdy model otrzymuje żądanie oceniania i oczekuje treści żądania jako dokumentu JSON o następującej strukturze:

```json
{
    "data": <model-specific-data-structure>
}

```

Dane wejściowe `run()` to ciąg języka Python, który jest zgodny z kluczem "Data".

Poniższy przykład ilustruje sposób ładowania zarejestrowanego modelu scikit-Dowiedz się i oceny z danymi numpy:

```python
import json
import numpy as np
import os
from sklearn.externals import joblib


def init():
    global model
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(data):
    try:
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Aby uzyskać bardziej zaawansowane przykłady, w tym automatyczne generowanie schematów Swagger i dane binarne (tj. obraz), zapoznaj [się z artykułem na temat tworzenia skryptów zaawansowanego wprowadzania](../articles/machine-learning/how-to-deploy-advanced-entry-script.md)