---
title: Użyj interfejsów API REST do wykonania ciągłej integracji/ciągłego wdrażania Azure Stream Analytics na urządzeniach IoT Edge
description: Dowiedz się, jak wdrożyć potok ciągłej integracji i wdrażania dla Azure Stream Analytics przy użyciu interfejsów API REST.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: ed11488f397704be782a092d6cdc6463449cc71e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86039079"
---
# <a name="implement-cicd-for-stream-analytics-on-iot-edge-using-apis"></a>Implementowanie ciągłej integracji/ciągłego wdrażania Stream Analytics na IoT Edge przy użyciu interfejsów API

Można włączyć ciągłą integrację i wdrażanie dla Azure Stream Analytics zadań przy użyciu interfejsów API REST. W tym artykule przedstawiono przykłady użycia interfejsów API i sposobu ich używania. Interfejsy API REST nie są obsługiwane w Azure Cloud Shell.

## <a name="call-apis-from-different-environments"></a>Wywoływanie interfejsów API z różnych środowisk

Interfejsy API REST można wywołać z systemów Linux i Windows. Następujące polecenia pokazują poprawną składnię wywołania interfejsu API. Określone użycie interfejsu API zostanie opisane w dalszej części tego artykułu.

### <a name="linux"></a>Linux

W przypadku systemu Linux można użyć `Curl` `Wget` poleceń lub:

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" { <url> }   
```

```bash
wget -q -O- --{ <method> } -data="<request body>" --header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a>Windows

W przypadku systemu Windows należy użyć programu PowerShell: 

```powershell 
$user = "<username>" 
$pass = "<password>" 
$encodedCreds = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $user,$pass))) 
$basicAuthValue = "Basic $encodedCreds" 
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("Content-Type", 'application/json') 
$headers.Add("Authorization", $basicAuthValue) 
$content = "<request body>" 
$response = Invoke-RestMethod <url> -Method <method> -Body $content -Headers $Headers 
echo $response 
```
 
## <a name="create-an-asa-job-on-edge"></a>Tworzenie zadania ASA na krawędzi 
 
Aby utworzyć zadanie Stream Analytics, wywołaj metodę PUT przy użyciu interfejsu API Stream Analytics.

|Metoda|Adres URL żądania|
|------|-----------|
|PUT|`https://management.azure.com/subscriptions/{\**subscription-id**}/resourcegroups/{**resource-group-name**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**job-name**}?api-version=2017-04-01-preview`|
 
Przykład polecenia korzystającego z **zwinięcie**:

```curl
curl -u { <username:password> } -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
Przykład treści żądania w formacie JSON:

```json
{ 
  "location": "West US", 
  "tags": { "key": "value", "ms-suppressjobstatusmetrics": "true" }, 
  "sku": {  
      "name": "Standard" 
    }, 
  "properties": { 
    "sku": {  
      "name": "standard" 
    }, 
       "eventsLateArrivalMaxDelayInSeconds": 1, 
       "jobType": "edge", 
    "inputs": [ 
      { 
        "name": "{inputname}", 
        "properties": { 
         "type": "stream", 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ], 
    "transformation": { 
      "name": "{queryName}", 
      "properties": { 
        "query": "{query}" 
      } 
    }, 
    "package": { 
      "storageAccount" : { 
        "accountName": "{blobstorageaccountname}", 
        "accountKey": "{blobstorageaccountkey}" 
      }, 
      "container": "{blobcontaine}]" 
    }, 
    "outputs": [ 
      { 
        "name": "{outputname}", 
        "properties": { 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ] 
  } 
} 
```
 
Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API](/rest/api/streamanalytics/stream-analytics-job).  
 
## <a name="publish-edge-package"></a>Publikowanie pakietu Edge 
 
Aby opublikować zadanie Stream Analytics na IoT Edge, wywołaj metodę POST przy użyciu interfejsu API publikowania pakietu Edge.

|Metoda|Adres URL żądania|
|------|-----------|
|POST|`https://management.azure.com/subscriptions/{\**subscriptionid**}/resourceGroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**jobname**}/publishedgepackage?api-version=2017-04-01-preview`|

Ta operacja asynchroniczna zwraca stan 202 do momentu pomyślnego opublikowania zadania. Nagłówek odpowiedzi lokalizacji zawiera identyfikator URI używany do uzyskiwania stanu procesu. Gdy proces jest uruchomiony, wywołanie identyfikatora URI w nagłówku lokalizacji zwraca stan 202. Po zakończeniu procesu identyfikator URI w nagłówku lokalizacji zwraca stan 200. 

Przykład wywołania publikowania pakietu brzegowego przy użyciu narzędzia **zwinięcie**: 

```bash
curl -d -X POST https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
Po wykonaniu wywołania POST powinna być oczekiwana odpowiedź z pustą treścią. Wyszukaj adres URL znajdujący się w NAGŁÓWKu odpowiedzi i Zapisz go do dalszej pracy.
 
Adres URL od SZEFa odpowiedzi:

```
https://management.azure.com/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
Przed uruchomieniem następującego polecenia poczekaj na dwie minuty, aby wykonać wywołanie interfejsu API z adresem URL znalezionym w NAGŁÓWKu odpowiedzi. Spróbuj ponownie wykonać polecenie, jeśli nie otrzymasz odpowiedzi 200.
 
Przykład tworzenia wywołania interfejsu API z zwróconym adresem URL z **zwinięciem**:

```bash
curl -d –X GET https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

Odpowiedź zawiera informacje, które należy dodać do skryptu wdrażania Edge. W poniższych przykładach pokazano, jakie informacje należy zebrać i gdzie dodać do manifestu wdrażania.
 
Przykładowa treść odpowiedzi po pomyślnym opublikowaniu:

```json
{ 
  edgePackageUrl : null 
  error : null 
  manifest : "{"supportedPlatforms":[{"os":"linux","arch":"amd64","features":[]},{"os":"linux","arch":"arm","features":[]},{"os":"windows","arch":"amd64","features":[]}],"schemaVersion":"2","name":"{jobname}","version":"1.0.0.0","type":"docker","settings":{"image":"{imageurl}","createOptions":null},"endpoints":{"inputs":["\],"outputs":["{outputnames}"]},"twin":{"contentType":"assignments","content":{"properties.desired":{"ASAJobInfo":"{asajobsasurl}","ASAJobResourceId":"{asajobresourceid}","ASAJobEtag":"{etag}","PublishTimeStamp":"{publishtimestamp}"}}}}" 
  status : "Succeeded" 
} 
```

Przykład manifestu wdrażania: 

```json
{ 
  "modulesContent": { 
    "$edgeAgent": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "runtime": { 
          "type": "docker", 
          "settings": { 
            "minDockerVersion": "v1.25", 
            "loggingOptions": "", 
            "registryCredentials": {} 
          } 
        }, 
        "systemModules": { 
          "edgeAgent": { 
            "type": "docker", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0", 
              "createOptions": "{}" 
            } 
          }, 
          "edgeHub": { 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0", 
              "createOptions": "{}" 
            } 
          } 
        }, 
        "modules": { 
          "<asajobname>": { 
            "version": "1.0", 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "<settings.image>", 
              "createOptions": "<settings.createOptions>" 
            } 
            "version": "<version>", 
             "env": { 
              "PlanId": { 
               "value": "stream-analytics-on-iot-edge" 
          } 
        } 
      } 
    }, 
    "$edgeHub": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "routes": { 
            "route": "FROM /* INTO $upstream" 
        }, 
        "storeAndForwardConfiguration": { 
          "timeToLiveSecs": 7200 
        } 
      } 
    }, 
    "<asajobname>": { 
      "properties.desired": {<twin.content.properties.desired>} 
    } 
  } 
} 
```

Po skonfigurowaniu manifestu wdrożenia zapoznaj się z tematem [wdrażanie modułów Azure IoT Edge za pomocą interfejsu wiersza polecenia platformy Azure](../iot-edge/how-to-deploy-modules-cli.md) w celu wdrożenia.


## <a name="next-steps"></a>Następne kroki 
 
* [Azure Stream Analytics na urządzeniach IoT Edge](stream-analytics-edge.md)
* [IoT Edge — samouczek](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Opracowywanie zadań Stream Analytics Edge przy użyciu narzędzi Visual Studio Tools](stream-analytics-tools-for-visual-studio-edge-jobs.md)
