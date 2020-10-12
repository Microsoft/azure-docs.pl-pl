---
title: Używanie REST do zarządzania zasobami ML
titleSuffix: Azure Machine Learning
description: Jak używać interfejsów API REST do tworzenia, uruchamiania i usuwania zasobów usługi Azure ML
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 01/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: b733fbc44deefe46e3496e288ebad525346ef005
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322312"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>Tworzenie, uruchamianie i usuwanie zasobów usługi Azure ML przy użyciu interfejsu REST



Istnieje kilka sposobów zarządzania zasobami usługi Azure ML. Możesz użyć [portalu](https://portal.azure.com/), [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest&preserve-view=true)lub [zestawu SDK języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true). Można też wybrać interfejs API REST. Interfejs API REST używa zleceń HTTP w standardowym sposobie tworzenia, pobierania, aktualizowania i usuwania zasobów. Interfejs API REST działa z dowolnym językiem lub narzędziem, które może wykonywać żądania HTTP. Prosta struktura usługi REST często sprawia, że jest dobrym wyborem w środowiskach skryptów i automatyzacji MLOps. 

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Pobieranie tokenu autoryzacji
> * Utwórz poprawnie sformatowane żądanie REST przy użyciu uwierzytelniania nazwy głównej usługi
> * Użyj żądania GET, aby pobrać informacje o zasobach hierarchicznych platformy Azure ML
> * Używanie żądań PUT i POST do tworzenia i modyfikowania zasobów
> * Użyj żądań usuwania do oczyszczenia zasobów 
> * Korzystanie z uwierzytelniania opartego na kluczach w celu oceny wdrożonych modeli

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure** , dla której masz prawa administracyjne. Jeśli nie masz takiej subskrypcji, wypróbuj [bezpłatną lub płatną subskrypcję osobistą](https://aka.ms/AMLFree)
- [Obszar roboczy usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)
- Administracyjne żądania REST używają uwierzytelniania nazwy głównej usługi. Postępuj zgodnie z instrukcjami w temacie [Konfigurowanie uwierzytelniania dla Azure Machine Learning zasobów i przepływów pracy](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication) , aby utworzyć jednostkę usługi w obszarze roboczym
- Narzędzie **zwinięcie** . Program **zwinięcie** jest dostępny w [podsystemie Windows dla systemu Linux](https://aka.ms/wslinstall/) lub dowolnej dystrybucji systemu UNIX. W programie PowerShell **zwinięcie** jest aliasem dla elementu **Invoke-WebRequest** i `curl -d "key=val" -X POST uri` zmieni się `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri` . 

## <a name="retrieve-a-service-principal-authentication-token"></a>Pobieranie tokenu uwierzytelniania jednostki usługi

Administracyjne żądania REST są uwierzytelniane przy użyciu niejawnego przepływu OAuth2. Ten przepływ uwierzytelniania używa tokenu dostarczonego przez nazwę główną usługi subskrypcji. Aby można było pobrać ten token, potrzebne są:

- Identyfikator dzierżawy (identyfikujący organizację, do której należy Twoja subskrypcja)
- Identyfikator klienta (który zostanie skojarzony z utworzonym tokenem)
- Klucz tajny klienta (należy chronić)

Należy mieć te wartości z odpowiedzi na tworzenie nazwy głównej usługi. Te wartości zostały omówione w temacie [Konfigurowanie uwierzytelniania dla Azure Machine Learning zasobów i przepływów pracy](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication). Jeśli używasz subskrypcji firmy, możesz nie mieć uprawnień do tworzenia nazwy głównej usługi. W takim przypadku należy użyć [bezpłatnej lub płatnej subskrypcji osobistej](https://aka.ms/AMLFree).

Aby pobrać token:

1. Otwórz okno terminalu
1. Wprowadź następujący kod w wierszu polecenia
1. Podstaw własne wartości dla `{your-tenant-id}` , `{your-client-id}` i `{your-client-secret}` . W tym artykule ciągi ujęte w nawiasy klamrowe są zmiennymi, które należy zamienić na własne odpowiednie wartości.
1. Uruchamianie polecenia

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

Odpowiedź powinna zapewnić token dostępu dobry przez godzinę:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "your-access-token"
}
```

Zanotuj token, ponieważ będzie on używany do uwierzytelniania wszystkich kolejnych żądań administracyjnych. Należy to zrobić, ustawiając nagłówek autoryzacji we wszystkich żądaniach:

```bash
curl -h "Authentication: Bearer {your-access-token}" ...more args...
```

Należy pamiętać, że wartość zaczyna się od ciągu "Bearer", w tym jednego odstępu przed dodaniem tokenu.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>Pobierz listę grup zasobów skojarzonych z Twoją subskrypcją

Aby pobrać listę grup zasobów skojarzonych z Twoją subskrypcją, uruchom polecenie:

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

Na platformie Azure publikowane są wiele interfejsów API REST. Każdy dostawca usług aktualizuje swój interfejs API na własny erze, ale nie przerywa istniejących programów. Dostawca usług używa argumentu, `api-version` Aby zapewnić zgodność. `api-version`Argument jest różny od usługi do usługi. Dla usługi Machine Learning, na przykład bieżąca wersja interfejsu API to `2019-11-01` . W przypadku kont magazynu jest to `2019-06-01` . W przypadku magazynów kluczy jest to `2019-09-01` . Wszystkie wywołania REST powinny ustawiać `api-version` argument dla oczekiwanej wartości. Można polegać na składni i semantyki określonej wersji, nawet gdy interfejs API nadal się rozwija. W przypadku wysłania żądania do dostawcy bez `api-version` argumentu odpowiedź będzie zawierać listę obsługiwanych wartości przez człowieka. 

Powyższe wywołanie spowoduje powstanie kompaktowej odpowiedzi JSON w postaci: 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>Przechodzenie do szczegółów obszarów roboczych i ich zasobów

Aby pobrać zestaw obszarów roboczych w grupie zasobów, uruchom następujące polecenie, podstawiając, `{your-subscription-id}` `{your-resource-group}` i `{your-access-token}` : 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Ponownie otrzymasz listę JSON, która zawiera listę zawierającą szczegóły obszaru roboczego:

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

Aby można było korzystać z zasobów w obszarze roboczym, należy przełączyć się z ogólnego serwera **Management.Azure.com** na serwer interfejsu API REST specyficzny dla lokalizacji obszaru roboczego. Zanotuj wartość `discoveryUrl` klucza w powyższej odpowiedzi JSON. Jeśli OTRZYMAsz ten adres URL, otrzymasz odpowiedź podobną do:

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

Wartość `api` odpowiedzi to adres URL serwera, który będzie używany w przypadku dodatkowych żądań. Aby wyświetlić listę eksperymentów, na przykład Wyślij następujące polecenie. Zamień na `regional-api-server` wartość `api` odpowiedzi (na przykład `centralus.api.azureml.ms` ). Należy również zastąpić `your-subscription-id` , `your-resource-group` , `your-workspace-name` , i `your-access-token` w zwykły sposób:

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Podobnie, aby pobrać zarejestrowane modele w obszarze roboczym, Wyślij:

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Zwróć uwagę, że w celu wyświetlenia listy eksperymentów ścieżka zaczyna się od `history/v1.0` while, aby wyświetlić listę modeli, ścieżka zaczyna się od `modelmanagement/v1.0` . Interfejs API REST jest podzielony na kilka grup operacyjnych, z których każda ma odrębną ścieżkę. 

|Warstwowy|Ścieżka|
|-|-|
|Artifacts|/rest/api/azureml|
|Magazyny danych|/azure/machine-learning/how-to-access-data|
|Dostrajanie hiperparametrów|dysk/v 1.0/|
|Modele|Modelmanagement/v 1.0/|
|Historia uruchamiania|wykonanie/v 1.0/i historia/v 1.0/|

Interfejs API REST można eksplorować przy użyciu ogólnego wzorca:

|Składnik adresu URL|Przykład|
|-|-|
| https://| |
| regionalne-API-Server/ | centralus.api.azureml.ms/ |
| operacje — ścieżka/ | Historia/v 1.0/ |
| subskrypcje/{Identyfikator subskrypcji}/ | subscriptions/abcde123-abab-abab-1234-0123456789abc/ |
| resourceGroups/{Grupa zasobów}/ | resourceGroups/moja Resourceer/ |
| dostawcy/Operations-Provider/ | dostawcy/Microsoft. MachineLearningServices/ |
| Dostawca-zasób-ścieżka/ | obszary robocze/MLWorkspace/FirstExperiment/uruchomienia/1/ |
| operacje — punkt końcowy/ | artefakty/metadane/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>Tworzenie i modyfikowanie zasobów przy użyciu żądań PUT i POST

Oprócz pobierania zasobów przy użyciu zlecenia GET interfejs API REST obsługuje tworzenie wszystkich zasobów potrzebnych do uczenia, wdrażania i monitorowania rozwiązań ML. 

Szkolenia i uruchomione modele ML wymagają zasobów obliczeniowych. Zasoby obliczeniowe obszaru roboczego można wyświetlić przy użyciu: 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Aby utworzyć lub zastąpić nazwany zasób obliczeniowy, będzie używane żądanie PUT. W następujących tematach oprócz teraz znanych podstawiania `your-subscription-id` ,, `your-resource-group` `your-workspace-name` , i `your-access-token` , podstawiania `your-compute-name` i wartości dla,,, `location` , `vmSize` `vmPriority` `scaleSettings` `adminUserName` i `adminUserPassword` . Zgodnie z opisem w temacie Reference w [środowisko obliczeniowe usługi Machine Learning-Create lub Update SDK](https://docs.microsoft.com/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate), następujące polecenie tworzy dedykowany Standard_D1 z pojedynczym węzłem (podstawowy zasób obliczeniowy procesora), który będzie skalowany w dół po 30 minutach:

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes/{your-compute-name}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{your-azure-location}",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "{adminUserName}",
        "adminUserPassword": "{adminUserPassword}"
    }
}'
```

> [!Note]
> W terminalach systemu Windows może być konieczne wypróbowanie symboli podwójnych cudzysłowów podczas wysyłania danych JSON. Oznacza to, że tekst taki jak `"location"` staną się `\"location\"` . 

Pomyślne żądanie otrzyma `201 Created` odpowiedź, ale należy zauważyć, że ta odpowiedź po prostu oznacza, że proces aprowizacji został rozpoczęty. Aby potwierdzić pomyślne zakończenie, należy wykonać sondowanie (lub użyć portalu).

### <a name="create-an-experimental-run"></a>Utwórz przebieg eksperymentalny

Aby rozpocząć Uruchamianie w ramach eksperymentu, potrzebujesz folderu zip zawierającego skrypt szkoleniowy i powiązane pliki oraz plik JSON definicji uruchomienia. Folder zip musi mieć plik wpisu w języku Python w katalogu głównym. Przykładowo w folderze o nazwie **train.zip**należy umieścić w pliku zip prosty program Python, taki jak poniższy.

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

Zapisz ten Następny fragment kodu jako **definition.js**. Potwierdź, że wartość "skrypt" jest zgodna z nazwą pliku języka Python, który właśnie został spakowany. Potwierdź, że wartość "target" jest zgodna z nazwą dostępnego zasobu obliczeniowego. 

```json
{
    "Configuration":{  
       "Script":"hello.py",
       "Arguments":[  
          "234"
       ],
       "SourceDirectoryDataStore":null,
       "Framework":"Python",
       "Communicator":"None",
       "Target":"cpu-compute",
       "MaxRunDurationSeconds":1200,
       "NodeCount":1,
       "Environment":{  
          "Python":{  
             "InterpreterPath":"python",
             "UserManagedDependencies":false,
             "CondaDependencies":{  
                "name":"project_environment",
                "dependencies":[  
                   "python=3.6.2",
                   {  
                      "pip":[  
                         "azureml-defaults"
                      ]
                   }
                ]
             }
          },
          "Docker":{  
             "BaseImage":"mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
          }
      },
       "History":{  
          "OutputCollection":true
       }
    }
}
```

Opublikuj te pliki na serwerze przy użyciu `multipart/form-data` zawartości:

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

Pomyślne żądanie POST spowoduje wygenerowanie `200 OK` stanu z treścią odpowiedzi zawierającej identyfikator utworzonego przebiegu:

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

Można monitorować przebieg przy użyciu wzorca REST-pełna, który powinien być teraz znany:

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>Usuń zasoby, które nie są już potrzebne

Niektóre zasoby, ale nie wszystkie, obsługują zlecenie DELETE. Sprawdź [odwołanie do interfejsu API](https://docs.microsoft.com/rest/api/azureml/) przed zatwierdzeniem do interfejsu API REST w celu usunięcia przypadków użycia. Aby usunąć model, na przykład można użyć:

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>Użycie REST do oceny wdrożonego modelu

Chociaż istnieje możliwość wdrożenia modelu w taki sposób, że jest on uwierzytelniany przy użyciu nazwy głównej usługi, większość wdrożeń związanych z klientem korzysta z uwierzytelniania opartego na kluczach. Odpowiedni klucz można znaleźć na stronie wdrożenia na karcie **punkty końcowe** w programie Studio. Ta sama lokalizacja będzie zawierać identyfikator URI oceniania punktu końcowego. Dane wejściowe modelu muszą być modelowane jako tablica JSON o nazwie `data` :

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>Tworzenie obszaru roboczego przy użyciu REST 

Każdy obszar roboczy usługi Azure ML ma zależność od czterech innych zasobów platformy Azure: Rejestr kontenerów z włączoną administracją, Magazyn kluczy, zasób Application Insights i konto magazynu. Nie można utworzyć obszaru roboczego, dopóki te zasoby nie istnieją. Szczegółowe informacje na temat tworzenia poszczególnych zasobów można znaleźć w dokumentacji interfejsu API REST.

Aby utworzyć obszar roboczy, należy umieścić wywołanie podobne do poniższego `management.azure.com` . Chociaż to wywołanie wymaga ustawienia dużej liczby zmiennych, jest to strukturalnie identyczne z innymi wywołaniami opisanymi w tym artykule. 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.MachineLearningServices/workspaces/{your-new-workspace-name}?api-version=2019-11-01' \
  -H 'Authorization: Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{desired-region}",
    "properties": {
        "friendlyName" : "{your-workspace-friendly-name}",
        "description" : "{your-workspace-description}",
        "containerRegistry" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.ContainerRegistry/registries/{your-registry-name}",
        keyVault" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.Keyvault/vaults/{your-keyvault-name}",
        "applicationInsights" : "subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.insights/components/{your-application-insights-name}",
        "storageAccount" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.Storage/storageAccounts/{your-storage-account-name}"
    },
    "identity" : {
        "type" : "systemAssigned"
    }
}'
```

Należy odebrać `202 Accepted` odpowiedź i, w zwróconych nagłówkach, `Location` Identyfikator URI. Możesz uzyskać ten identyfikator URI, aby uzyskać informacje dotyczące wdrożenia, w tym przydatne informacje debugowania, jeśli wystąpi problem z jednym z zasobów zależnych (na przykład jeśli nie chcesz włączyć dostępu administratora w rejestrze kontenerów). 

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="resource-provider-errors"></a>Błędy dostawcy zasobów

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Przeniesienie obszaru roboczego

> [!WARNING]
> Przeniesienie obszaru roboczego Azure Machine Learning do innej subskrypcji lub przeniesienie subskrypcji będącej właścicielem do nowej dzierżawy nie jest obsługiwane. Wykonanie tej operacji może spowodować błędy.

### <a name="deleting-the-azure-container-registry"></a>Usuwanie Azure Container Registry

W przypadku niektórych operacji Azure Machine Learning obszar roboczy używa Azure Container Registry (ACR). Zostanie automatycznie utworzone wystąpienie ACR, gdy jest ono najpierw wymagane.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z kompletnym [odwołaniem interfejsu API REST usługi Azure](https://docs.microsoft.com/rest/api/azureml/).
- Dowiedz się, w jaki sposób używać projektanta do [przewidywania cen samochodów w programie Designer](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-train-score).
- Eksploruj [Azure Machine Learning z notesami Jupyter](https://docs.microsoft.com/azure//machine-learning/samples-notebooks).
