---
title: Wpisy tajne uwierzytelniania w szkoleniu
titleSuffix: Azure Machine Learning
description: Przekaż wpisy tajne do przebiegów szkoleniowych w sposób bezpieczny przy użyciu Key Vault obszaru roboczego
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 89934470dc3bf86bb2843137a2129bff13323ca0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91302081"
---
# <a name="use-authentication-credential-secrets-in-azure-machine-learning-training-runs"></a>Użyj wpisów tajnych poświadczeń uwierzytelniania w Azure Machine Learningych przebiegach szkoleniowych


W tym artykule dowiesz się, jak korzystać z wpisów tajnych w programie szkoleniowym w sposób bezpieczny. Informacje o uwierzytelnianiu, takie jak nazwa użytkownika i hasło, są tajne. Na przykład, jeśli łączysz się z zewnętrzną bazą danych w celu wykonywania zapytań dotyczących danych szkoleniowych, musisz przekazać nazwę użytkownika i hasło do zdalnego kontekstu uruchomienia. Kodowanie takich wartości do skryptów szkoleniowych w postaci zwykłego tekstu jest niebezpieczne, ponieważ uwidacznia wpis tajny. 

Zamiast tego obszar roboczy Azure Machine Learning ma skojarzony zasób o nazwie [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Użyj tej Key Vault, aby bezpiecznie przekazywać klucze tajne do zdalnego uruchamiania za pomocą zestawu interfejsów API w Azure Machine Learning SDK języka Python.

Standardowy przepływ dla korzystania z kluczy tajnych to:
 1. Na komputerze lokalnym Zaloguj się do platformy Azure i Połącz się z obszarem roboczym.
 2. Na komputerze lokalnym Ustaw klucz tajny w obszarze roboczym Key Vault.
 3. Prześlij zdalne uruchomienie.
 4. W ramach przebiegu zdalnego Pobierz klucz tajny z Key Vault i użyj go.

## <a name="set-secrets"></a>Ustaw wpisy tajne

W Azure Machine Learning Klasa [magazynu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true) kluczy zawiera metody ustawiania wpisów tajnych. W lokalnej sesji języka Python najpierw Uzyskaj odwołanie do obszaru roboczego Key Vault, a następnie użyj [`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true#&preserve-view=trueset-secret-name--value-) metody, aby ustawić wpis tajny według nazwy i wartości. Metoda __set_secret__ aktualizuje wartość klucza tajnego, jeśli nazwa już istnieje.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Nie należy umieszczać wartości tajnych w kodzie języka Python, ponieważ jest ona niezabezpieczona, aby można było ją zapisać w pliku jako zwykły tekst. Zamiast tego należy uzyskać wartość klucza tajnego ze zmiennej środowiskowej, na przykład Azure DevOps Build Secret lub z interakcyjnych danych wejściowych użytkownika.

Można wyświetlić listę wpisów tajnych za pomocą [`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true#&preserve-view=truelist-secrets--) metody, a także wersję usługi Batch,[set_secrets ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true#&preserve-view=trueset-secrets-secrets-batch-) , która umożliwia ustawienie wielu wpisów tajnych w danym momencie.

## <a name="get-secrets"></a>Pobierz wpisy tajne

W kodzie lokalnym możesz użyć [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-secret-name-) metody, aby uzyskać wartość klucza tajnego według nazwy.

W przypadku przebiegów przesłanych [`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py&preserve-view=true#&preserve-view=truesubmit-config--tags-none----kwargs-) [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-secret-name-) Metoda Użyj metody z [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true) klasą. Ze względu na to, że przesłany przebieg ma świadomość swojego obszaru roboczego, ta metoda skrótów tworzy Tworzenie wystąpienia obszaru roboczego i zwraca wartość tajną bezpośrednio.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Należy zachować ostrożność, aby nie ujawniać wartości klucza tajnego przez zapisanie lub wydrukowanie.

Dostępna jest również wersja usługi Batch, [get_secrets ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-secrets-secrets-) w celu uzyskania dostępu do wielu wpisów tajnych jednocześnie.

## <a name="next-steps"></a>Następne kroki

 * [Wyświetl przykładowy Notes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Dowiedz się więcej o zabezpieczeniach przedsiębiorstwa z Azure Machine Learning](concept-enterprise-security.md)
