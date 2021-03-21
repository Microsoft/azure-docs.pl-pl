---
title: Wpisy tajne uwierzytelniania w szkoleniu
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak przekazywać wpisy tajne do przebiegów szkoleniowych w sposób bezpieczny przy użyciu Azure Key Vault dla obszaru roboczego.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: b93da1e252357830578783c8f3ab5ca02f5a3e5b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520748"
---
# <a name="use-authentication-credential-secrets-in-azure-machine-learning-training-runs"></a>Użyj wpisów tajnych poświadczeń uwierzytelniania w Azure Machine Learningych przebiegach szkoleniowych


W tym artykule dowiesz się, jak korzystać z wpisów tajnych w programie szkoleniowym w sposób bezpieczny. Informacje o uwierzytelnianiu, takie jak nazwa użytkownika i hasło, są tajne. Na przykład, jeśli łączysz się z zewnętrzną bazą danych w celu wykonywania zapytań dotyczących danych szkoleniowych, musisz przekazać nazwę użytkownika i hasło do zdalnego kontekstu uruchomienia. Kodowanie takich wartości do skryptów szkoleniowych w postaci zwykłego tekstu jest niebezpieczne, ponieważ uwidacznia wpis tajny. 

Zamiast tego obszar roboczy Azure Machine Learning ma skojarzony zasób o nazwie [Azure Key Vault](../key-vault/general/overview.md). Użyj tej Key Vault, aby bezpiecznie przekazywać klucze tajne do zdalnego uruchamiania za pomocą zestawu interfejsów API w Azure Machine Learning SDK języka Python.

Standardowy przepływ dla korzystania z kluczy tajnych to:
 1. Na komputerze lokalnym Zaloguj się do platformy Azure i Połącz się z obszarem roboczym.
 2. Na komputerze lokalnym Ustaw klucz tajny w obszarze roboczym Key Vault.
 3. Prześlij zdalne uruchomienie.
 4. W ramach przebiegu zdalnego Pobierz klucz tajny z Key Vault i użyj go.

## <a name="set-secrets"></a>Ustaw wpisy tajne

W Azure Machine Learning Klasa [magazynu](/python/api/azureml-core/azureml.core.keyvault.keyvault) kluczy zawiera metody ustawiania wpisów tajnych. W lokalnej sesji języka Python najpierw Uzyskaj odwołanie do obszaru roboczego Key Vault, a następnie użyj [`set_secret()`](/python/api/azureml-core/azureml.core.keyvault.keyvault#set-secret-name--value-) metody, aby ustawić wpis tajny według nazwy i wartości. Metoda __set_secret__ aktualizuje wartość klucza tajnego, jeśli nazwa już istnieje.

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

Można wyświetlić listę wpisów tajnych za pomocą [`list_secrets()`](/python/api/azureml-core/azureml.core.keyvault.keyvault#list-secrets--) metody, a także wersję usługi Batch,[set_secrets ()](/python/api/azureml-core/azureml.core.keyvault.keyvault#set-secrets-secrets-batch-) , która umożliwia ustawienie wielu wpisów tajnych w danym momencie.

## <a name="get-secrets"></a>Pobierz wpisy tajne

W kodzie lokalnym możesz użyć [`get_secret()`](/python/api/azureml-core/azureml.core.keyvault.keyvault#get-secret-name-) metody, aby uzyskać wartość klucza tajnego według nazwy.

W przypadku przebiegów przesłanych [`Experiment.submit`](/python/api/azureml-core/azureml.core.experiment.experiment#submit-config--tags-none----kwargs-) [`get_secret()`](/python/api/azureml-core/azureml.core.run.run#get-secret-name-) Metoda Użyj metody z [`Run`](/python/api/azureml-core/azureml.core.run%28class%29) klasą. Ze względu na to, że przesłany przebieg ma świadomość swojego obszaru roboczego, ta metoda skrótów tworzy Tworzenie wystąpienia obszaru roboczego i zwraca wartość tajną bezpośrednio.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Należy zachować ostrożność, aby nie ujawniać wartości klucza tajnego przez zapisanie lub wydrukowanie.

Dostępna jest również wersja usługi Batch, [get_secrets ()](/python/api/azureml-core/azureml.core.run.run#get-secrets-secrets-) w celu uzyskania dostępu do wielu wpisów tajnych jednocześnie.

## <a name="next-steps"></a>Następne kroki

 * [Wyświetl przykładowy Notes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Dowiedz się więcej o zabezpieczeniach przedsiębiorstwa z Azure Machine Learning](concept-enterprise-security.md)