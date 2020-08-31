---
title: Integracja z usługą git dla Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób Azure Machine Learning integruje się z lokalnym repozytorium git. Podczas przesyłania szkolenia z katalogu lokalnego, który jest repozytorium git, informacje o repozytorium, rozgałęzieniu i bieżącym zatwierdzeniu są śledzone w ramach przebiegu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: 154d28e4df9bad68f3c5e93208ccf62ba2721663
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144825"
---
# <a name="git-integration-for-azure-machine-learning"></a>Integracja z usługą git dla Azure Machine Learning

[Git](https://git-scm.com/) to popularny system kontroli wersji, który umożliwia udostępnianie i współpracę nad projektami. 

Azure Machine Learning w pełni obsługuje repozytoria Git na potrzeby śledzenia pracy — można klonować repozytoria bezpośrednio do udostępnionego systemu plików obszaru roboczego, korzystać z narzędzia Git na lokalnej stacji roboczej lub używać narzędzia Git z potoku ciągłej integracji/ciągłego dostarczania.

Podczas przesyłania zadania do Azure Machine Learning, jeśli pliki źródłowe są przechowywane w lokalnym repozytorium git, informacje o repozytorium są śledzone w ramach procesu szkoleniowego.

Ponieważ Azure Machine Learning śledzi informacje z lokalnego repozytorium git, nie jest ono powiązane z żadnym określonym centralnym repozytorium. Repozytorium można sklonować z witryny GitHub, GitLab, BitBucket, Azure DevOps lub dowolnej innej usługi zgodnej z Git.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Klonowanie repozytoriów Git do systemu plików obszaru roboczego
Azure Machine Learning udostępnia współużytkowany system plików dla wszystkich użytkowników w obszarze roboczym.
Aby sklonować repozytorium git do tego udziału plików, zalecamy utworzenie wystąpienia obliczeniowego & otwarcie terminalu.
Po otwarciu terminalu masz dostęp do pełnego klienta git i można klonować i współdziałać z usługą Git za pośrednictwem interfejsu wiersza polecenia usługi git.

Zalecamy klonowanie repozytorium do katalogu użytkowników, tak aby inne osoby nie miały kolizji bezpośrednio w gałęzi roboczej.

Można sklonować dowolne repozytorium git, do których można się uwierzytelnić (GitHub, Azure Repos, BitBucket itp.).

Aby zapoznać się z przewodnikiem dotyczącym korzystania z interfejsu wiersza polecenia usługi git, Przeczytaj [tutaj.](https://guides.github.com/introduction/git-handbook/)

## <a name="track-code-that-comes-from-git-repositories"></a>Śledź kod pochodzący z repozytoriów Git

Po przesłaniu szkolenia z zestawu SDK języka Python lub interfejsu wiersza polecenia Machine Learning pliki potrzebne do uczenia modelu są przekazywane do obszaru roboczego. Jeśli `git` polecenie jest dostępne w środowisku deweloperskim, proces przekazywania używa go do sprawdzenia, czy pliki są przechowywane w repozytorium git. Jeśli tak, to w ramach przebiegu szkoleniowego zostanie również przekazane informacje z repozytorium git. Te informacje są przechowywane w następujących właściwościach przebiegu szkoleniowego:

| Właściwość | Polecenie git użyte do pobrania wartości | Opis |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | Identyfikator URI, z którego zostało Sklonowane repozytorium. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | Identyfikator URI, z którego zostało Sklonowane repozytorium. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | Aktywna gałąź podczas przesyłania przebiegu. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | Aktywna gałąź podczas przesyłania przebiegu. |
| `azureml.git.commit` | `git rev-parse HEAD` | Skrót zatwierdzenia kodu, który został przesłany dla uruchomienia. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | Skrót zatwierdzenia kodu, który został przesłany dla uruchomienia. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, jeśli gałąź/zatwierdzenie są zanieczyszczone; w przeciwnym razie `false` . |

Te informacje są wysyłane do przebiegów korzystających z szacowania, potoku uczenia maszynowego lub uruchamiania skryptów.

Jeśli pliki szkoleniowe nie znajdują się w repozytorium Git w środowisku deweloperskim lub `git` polecenie jest niedostępne, nie są śledzone żadne informacje dotyczące usługi git.

> [!TIP]
> Aby sprawdzić, czy polecenie git jest dostępne w środowisku deweloperskim, Otwórz sesję powłoki, wiersz polecenia, program PowerShell lub inny interfejs wiersza polecenia i wpisz następujące polecenie:
>
> ```
> git --version
> ```
>
> Jeśli jest zainstalowany, a w ścieżce otrzymujesz odpowiedź podobną do `git version 2.4.1` . Aby uzyskać więcej informacji na temat instalowania usługi Git w środowisku deweloperskim, zobacz [witrynę sieci Web usługi git](https://git-scm.com/).

## <a name="view-the-logged-information"></a>Wyświetlanie zarejestrowanych informacji

Informacje dotyczące usługi git są przechowywane we właściwościach przebiegu szkoleniowego. Te informacje można wyświetlić przy użyciu Azure Portal, zestawu Python SDK i interfejsu wiersza polecenia. 

### <a name="azure-portal"></a>Azure Portal

1. Na [Azure Portal](https://portal.azure.com)wybierz swój obszar roboczy.
1. Wybierz pozycję __eksperymenty__, a następnie wybierz jedno z eksperymentów.
1. Wybierz jeden z przebiegów z kolumny __numer uruchomienia__ .
1. Wybierz pozycję __dzienniki__, a następnie rozwiń pozycje __dzienniki__ i __Azure__ . Wybierz łącze zaczynające się od __ ### \_ platformy Azure__.

    ![Wpis # # #_azure w portalu](./media/concept-train-model-git-integration/azure-machine-learning-logs.png)

Zarejestrowane informacje zawierają tekst podobny do następującego:

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Zestaw SDK dla języka Python

Po przesłaniu przebiegu szkoleniowego zwracany jest obiekt [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) . `properties`Atrybut tego obiektu zawiera zarejestrowane informacje usługi git. Na przykład poniższy kod pobiera skrót zatwierdzenia:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>Interfejs wiersza polecenia

`az ml run`Polecenie CLI może służyć do pobierania właściwości z przebiegu. Na przykład następujące polecenie zwraca właściwości dla ostatniego uruchomienia w eksperymentie o nazwie `train-on-amlcompute` :

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Aby uzyskać więcej informacji, zapoznaj się z dokumentacją [AZ ml Run](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest) Reference.

## <a name="next-steps"></a>Następne kroki

* [Użyj obiektów docelowych obliczeń do szkolenia modelu](how-to-set-up-training-targets.md)
