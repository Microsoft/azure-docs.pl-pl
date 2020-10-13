---
title: Wdrażanie modeli w wystąpieniach obliczeniowych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wdrażać modele Azure Machine Learning jako usługę sieci Web przy użyciu wystąpień obliczeniowych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: 7992283c1652199d665aad07c027b88e9489ddf0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90889826"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Wdrażanie modelu w celu Azure Machine Learning wystąpień obliczeniowych



Dowiedz się, jak za pomocą Azure Machine Learning wdrożyć model jako usługę sieci Web w wystąpieniu Azure Machine Learning COMPUTE. Użyj wystąpień obliczeniowych, jeśli spełniony jest jeden z następujących warunków:

- Należy szybko wdrożyć i zweryfikować model.
- Testujesz model, który jest w fazie projektowania.

> [!TIP]
> Wdrożenie modelu z Jupyter Notebook w wystąpieniu obliczeniowym w usłudze sieci Web na tej samej maszynie wirtualnej jest _wdrożeniem lokalnym_. W takim przypadku komputer "Local" jest wystąpieniem obliczeniowym. Aby uzyskać więcej informacji na temat wdrożeń, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy Azure Machine Learning z uruchomionym wystąpieniem obliczeniowym. Aby uzyskać więcej informacji, zobacz temat [Instalowanie środowiska i obszaru roboczego](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-compute-instances"></a>Wdrażanie w wystąpieniach obliczeniowych

Przykładowy Notes, który pokazuje lokalne wdrożenia, jest uwzględniony w wystąpieniu obliczeniowym. Wykonaj następujące kroki, aby załadować Notes i wdrożyć model jako usługę sieci Web na maszynie wirtualnej:

1. W programie [Azure Machine Learning Studio](https://ml.azure.com)wybierz Azure Machine Learning wystąpienia obliczeniowe.

1. Otwórz `samples-*` podkatalog, a następnie otwórz `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb` . Po otwarciu Uruchom Notes.

    ![Zrzut ekranu przedstawiający uruchomioną usługę lokalną w notesie](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)

1. Notes wyświetla adres URL i port, na którym uruchomiono usługę. Na przykład `https://localhost:6789`. Możesz również uruchomić komórkę zawierającą, `print('Local service port: {}'.format(local_service.port))` Aby wyświetlić port.

    ![Zrzut ekranu z uruchomionym portem usługi lokalnej](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. Aby przetestować usługę z wystąpienia obliczeniowego, użyj `https://localhost:<local_service.port>` adresu URL. Aby przetestować klienta zdalnego, uzyskaj publiczny adres URL usługi uruchomionej w wystąpieniu obliczeniowym. Publiczny adres URL można określić przy użyciu następującej formuły: 
    * Maszyna wirtualna notesu: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score` . 
    * Wystąpienie obliczeniowe: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score` . 

    Przykład: 
    * Maszyna wirtualna notesu: `https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Wystąpienie obliczeniowe: `https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Testowanie usługi

Aby przesłać przykładowe dane do uruchomionej usługi, użyj poniższego kodu. Zastąp wartość `service_url` adresem URL z poprzedniego kroku:

> [!NOTE]
> Podczas uwierzytelniania w wystąpieniu obliczeniowym uwierzytelnianie jest nawiązywane przy użyciu Azure Active Directory. Wywołanie `interactive_auth.get_authentication_header()` w ramach przykładowego kodu uwierzytelnia użytkownika za pomocą usługi AAD i zwraca nagłówek, którego można następnie użyć do uwierzytelnienia w usłudze w wystąpieniu obliczeniowym. Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania dla Azure Machine Learning zasobów i przepływów pracy](how-to-setup-authentication.md#interactive-authentication).
>
> W przypadku uwierzytelniania do wdrożenia w usłudze Azure Kubernetes Service lub Azure Container Instances jest używana inna metoda uwierzytelniania. Aby uzyskać więcej informacji na temat, zobacz [Konfigurowanie uwierzytelniania dla Azure Machine Learning zasobów i przepływów pracy](how-to-setup-authentication.md#web-service-authentication).

```python
import requests
import json
from azureml.core.authentication import InteractiveLoginAuthentication

# Get a token to authenticate to the compute instance from remote
interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()

# Create and submit a request using the auth header
headers = auth_header
# Add content type header
headers.update({'Content-Type':'application/json'})

# Sample data to send to the service
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

# Replace with the URL for your compute instance, as determined from the previous section
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Następne kroki

* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Rozwiązywanie problemów z wdrażaniem](how-to-troubleshoot-deployment.md)
* [Zabezpieczanie usługi internetowej za pomocą usługi Azure Machine Learning przy użyciu protokołu TLS](how-to-secure-web-service.md)
* [Korzystanie z modelu ML wdrożonego jako usługa sieci Web](how-to-consume-web-service.md)
* [Monitoruj modele Azure Machine Learning przy użyciu Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
