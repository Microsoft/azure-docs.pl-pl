---
title: Rozwiązywanie problemów z wdrożeniem usługi sieci Web lokalnie
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak obejść, rozwiązywać i rozwiązywać problemy z typowymi błędami wdrożenia platformy Docker Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.reviewer: luquinta
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: devx-track-python, deploy, contperfq2
ms.openlocfilehash: 18de43cb170ccdede8f7fe13dc0c6c0c2fa4e7a3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188636"
---
# <a name="troubleshoot-model-deployment-locally"></a>Rozwiązywanie problemów z wdrożeniem modelu lokalnie

Dowiedz się, jak rozwiązywać problemy z Azure Machine Learning typowymi błędami wdrożenia usługi sieci Web platformy Docker i rozwiązać ten problem, a także obejść je w programie

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).
* [Zestaw SDK Azure Machine Learning](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
* [Rozszerzenie interfejsu wiersza polecenia dla Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Aby debugować lokalnie, musisz mieć działającą instalację platformy Docker w systemie lokalnym.

    Aby zweryfikować instalację platformy Docker, użyj polecenia `docker run hello-world` z terminalu lub wiersza polecenia. Informacje dotyczące instalowania platformy Docker lub rozwiązywania problemów z błędami platformy Docker znajdują się w [dokumentacji platformy Docker](https://docs.docker.com/).

## <a name="debug-locally"></a>Debuguj lokalnie

Jeśli masz problemy podczas wdrażania modelu w programie ACI lub AKS, wdróż go jako lokalną usługę sieci Web. Korzystanie z lokalnej usługi internetowej ułatwia rozwiązywanie problemów.

Przykładowy [Notes lokalnego wdrożenia](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb) można znaleźć w repozytorium  [MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) , aby poznać możliwy do uruchomienia przykład.

> [!WARNING]
> Lokalne wdrożenia usługi sieci Web nie są obsługiwane w scenariuszach produkcyjnych.

Aby wdrożyć lokalnie, zmodyfikuj swój kod w celu użycia `LocalWebservice.deploy_configuration()` w celu utworzenia konfiguracji wdrożenia. Następnie użyj `Model.deploy()` do wdrożenia usługi. W poniższym przykładzie jest wdrażany model (zawarty w zmiennej modelu) jako lokalna usługa sieci Web:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Jeśli definiujesz własną specyfikację Conda YAML, Wyświetl listę wartości domyślnych dla programu Azure >= 1.0.45 jako zależność PIP. Ten pakiet jest wymagany do hostowania modelu jako usługi sieci Web.

W tym momencie można korzystać z usługi w zwykły sposób. Poniższy kod ilustruje wysyłanie danych do usługi:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

Aby uzyskać więcej informacji na temat dostosowywania środowiska języka Python, zobacz [Tworzenie środowisk i zarządzanie nimi na potrzeby szkolenia i wdrażania](how-to-use-environments.md). 

### <a name="update-the-service"></a>Aktualizowanie usługi

Podczas testowania lokalnego może być konieczne zaktualizowanie `score.py` pliku w celu dodania rejestrowania lub podjęcia próby rozwiązania problemów, które zostały wykryte. Aby ponownie załadować zmiany do `score.py` pliku, użyj `reload()` . Na przykład poniższy kod ponownie ładuje skrypt dla usługi, a następnie wysyła do niej dane. Dane są oceniane przy użyciu zaktualizowanego `score.py` pliku:

> [!IMPORTANT]
> `reload`Metoda jest dostępna tylko dla wdrożeń lokalnych. Aby uzyskać informacje na temat aktualizowania wdrożenia do innego obiektu docelowego obliczeń, zobacz [jak zaktualizować usługę sieci Web](how-to-deploy-update-web-service.md).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Skrypt zostanie ponownie załadowany z lokalizacji określonej przez `InferenceConfig` obiekt używany przez usługę.

Aby zmienić model, Conda zależności lub konfigurację wdrożenia, użyj [Update ()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueupdate--args-). Poniższy przykład aktualizuje model używany przez usługę:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Usuwanie usługi

Aby usunąć usługę, użyj polecenie [delete ()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Inspekcja dziennika platformy Docker

Można wydrukować szczegółowe komunikaty dziennika aparatu Docker z obiektu usługi. Dziennik można wyświetlić w przypadku wdrożeń ACI, AKS i lokalnych. Poniższy przykład ilustruje sposób drukowania dzienników.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

Jeśli wiersz `Booting worker with pid: <pid>` występuje wielokrotnie w dziennikach, oznacza to, że nie ma wystarczającej ilości pamięci do uruchomienia procesu roboczego.
Możesz rozwiązać ten problem, zwiększając wartość `memory_gb` w `deployment_config`

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o wdrażaniu:

* [Jak rozwiązywać problemy z wdrożeniami zdalnymi](how-to-troubleshoot-deployment.md)
* [Jak wdrożyć i gdzie](how-to-deploy-and-where.md)
* [Samouczek: uczenie & Wdrażanie modeli](tutorial-train-models-with-aml.md)
* [Jak uruchamiać i debugować eksperymenty lokalnie](./how-to-debug-visual-studio-code.md)
