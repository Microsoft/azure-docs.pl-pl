---
title: Tworzenie klienta dla modelu wdrożonego jako usługa internetowa
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wywołać punkt końcowy usługi internetowej, który został wygenerowany podczas wdrażania modelu z Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 10/12/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-csharp
ms.openlocfilehash: 8cab9331cdd4c15ab76b7c33f956be15ec2259ef
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861197"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Korzystanie z modelu usługi Azure Machine Learning wdrożonego jako usługa internetowa


Wdrożenie modelu usługi Azure Machine Learning jako usługi internetowej powoduje utworzenie punktu końcowego interfejsu API REST. Można wysłać dane do tego punktu końcowego i otrzymać prognozę zwróconą przez model. W tym dokumencie dowiesz się, jak tworzyć klientów dla usługi internetowej przy użyciu języków C#, Go, Java i Python.

Usługę sieci Web tworzy się podczas wdrażania modelu w środowisku lokalnym, Azure Container Instances, Azure Kubernetes Service lub programowalnych w terenie macierzy bramowych (FPGA). Możesz pobrać adres URI używany do uzyskiwania dostępu do usługi internetowej przy użyciu [zestawu AZURE MACHINE LEARNING SDK.](/python/api/overview/azure/ml/intro) Jeśli uwierzytelnianie jest włączone, można również pobrać klucze lub tokeny uwierzytelniania przy użyciu zestawu SDK.

Ogólny przepływ pracy tworzenia klienta, który korzysta z usługi internetowej uczenia maszynowego, to:

1. Użyj zestawu SDK, aby uzyskać informacje o połączeniu.
1. Określ typ danych żądania używanych przez model.
1. Utwórz aplikację, która wywołuje usługę internetową.

> [!TIP]
> Przykłady w tym dokumencie są tworzone ręcznie bez użycia specyfikacji OpenAPI (Swagger). Jeśli dla wdrożenia włączono specyfikację OpenAPI, możesz użyć narzędzi, takich jak [swagger-codegen,](https://github.com/swagger-api/swagger-codegen) aby utworzyć biblioteki klienckie dla swojej usługi.

## <a name="connection-information"></a>Informacje o połączeniu

> [!NOTE]
> Użyj zestawu AZURE MACHINE LEARNING SDK, aby uzyskać informacje o usłudze internetowej. Jest to zestaw SDK języka Python. Do utworzenia klienta dla usługi można użyć dowolnego języka.

Klasa [azureml.core.Webservice zawiera](/python/api/azureml-core/azureml.core.webservice%28class%29) informacje potrzebne do utworzenia klienta. Następujące właściwości `Webservice` są przydatne podczas tworzenia aplikacji klienckiej:

* `auth_enabled` - Jeśli jest włączone uwierzytelnianie klucza, `True` ; w przeciwnym razie `False` .
* `token_auth_enabled` - Jeśli uwierzytelnianie tokenu jest włączone; `True` w przeciwnym razie `False` .
* `scoring_uri` — adres interfejsu API REST.
* `swagger_uri` — adres specyfikacji openaPI. Ten URI jest dostępny, jeśli włączono automatyczne generowanie schematu. Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli za pomocą Azure Machine Learning](how-to-deploy-and-where.md).

Istnieje kilka sposobów pobierania tych informacji dla wdrożonych usług sieci Web:

# <a name="python"></a>[Python](#tab/python)

* Podczas wdrażania modelu jest zwracany obiekt z `Webservice` informacjami o usłudze:

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* Za pomocą funkcji można pobrać listę wdrożonych usług internetowych `Webservice.list` dla modeli w obszarze roboczym. Możesz dodać filtry, aby zawęzić listę zwracanych informacji. Aby uzyskać więcej informacji na temat elementów, od których można filtrować, zobacz [dokumentację referencyjną webservice.list.](/python/api/azureml-core/azureml.core.webservice.webservice.webservice)

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* Jeśli znasz nazwę wdrożonej usługi, możesz utworzyć nowe wystąpienie klasy i podać nazwę obszaru roboczego i usługi `Webservice` jako parametry. Nowy obiekt zawiera informacje o wdrożonej usłudze.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Jeśli znasz nazwę wdrożonej usługi, użyj [polecenia az ml service show:](/cli/azure/ml/service#az_ml_service_show)

```azurecli
az ml service show -n <service-name>
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

W Azure Machine Learning studio wybierz __pozycję Punkty końcowe__, Punkty końcowe __czasu rzeczywistego__, a następnie nazwę punktu końcowego. W szczegółach dotyczących punktu końcowego pole __punkt końcowy REST__ zawiera kod URI oceniania. Kod __URI swagger__ zawiera jego URI.

---

W poniższej tabeli przedstawiono, jak wyglądają te URI:

| Typ URI | Przykład |
| ----- | ----- |
| Scoring URI | `http://104.214.29.152:80/api/v1/service/<service-name>/score` |
| Swagger URI | `http://104.214.29.152/api/v1/service/<service-name>/swagger.json` |

> [!TIP]
> Adres IP będzie inny dla twojego wdrożenia. Każdy klaster usługi AKS będzie miał własny adres IP współużytkowany przez wdrożenia w tym klastrze.

### <a name="secured-web-service"></a>Zabezpieczona usługa internetowa

Jeśli wdrożona usługa sieci Web jest zabezpieczona przy użyciu certyfikatu TLS/SSL, możesz użyć protokołu [HTTPS](https://en.wikipedia.org/wiki/HTTPS) do nawiązania połączenia z usługą przy użyciu oceniania lub adresu URI programu Swagger. Protokół HTTPS pomaga zabezpieczyć komunikację między klientem a usługą internetową, szyfrując komunikację między nimi. Szyfrowanie używa [Transport Layer Security (TLS).](https://en.wikipedia.org/wiki/Transport_Layer_Security) Czasami protokół TLS jest nadal określany jako *Secure Sockets Layer* (SSL), który był poprzednikiem protokołu TLS.

> [!IMPORTANT]
> Usługi sieci Web wdrożone przez Azure Machine Learning obsługują tylko TLS w wersji 1.2. Podczas tworzenia aplikacji klienckiej upewnij się, że obsługuje ona tę wersję.

Aby uzyskać więcej informacji, zobacz [Zabezpieczanie](how-to-secure-web-service.md)usługi internetowej za pośrednictwem usługi internetowej przy użyciu Azure Machine Learning .

### <a name="authentication-for-services"></a>Uwierzytelnianie dla usług

Azure Machine Learning zapewnia dwa sposoby kontrolowania dostępu do usług internetowych.

|Metoda uwierzytelniania|Aci|AKS|
|---|---|---|
|Klucz|Domyślnie wyłączone| Domyślnie włączone|
|Token| Niedostępny| Domyślnie wyłączone |

W przypadku wysyłania żądania do usługi zabezpieczonej za pomocą klucza lub tokenu użyj nagłówka __Autoryzacja,__ aby przekazać klucz lub token. Klucz lub token musi być sformatowany jako `Bearer <key-or-token>` , gdzie to klucz lub wartość `<key-or-token>` tokenu.

Podstawowa różnica między kluczami i tokenami polega na **tym,** że klucze są statyczne i można je ponownie wygenerować ręcznie, a **tokeny** należy odświeżyć po wygaśnięciu . Uwierzytelnianie oparte na kluczach jest obsługiwane w przypadku usługi Azure Container Instance i Azure Kubernetes Service  usług sieci Web, a uwierzytelnianie oparte na tokenach jest dostępne tylko dla Azure Kubernetes Service wdrożeniach. Aby uzyskać więcej informacji na temat konfigurowania uwierzytelniania, zobacz [Konfigurowanie uwierzytelniania dla modeli wdrożonych jako usługi sieci Web.](how-to-authenticate-web-service.md)


#### <a name="authentication-with-keys"></a>Uwierzytelnianie przy użyciu kluczy

Po włączeniu uwierzytelniania dla wdrożenia klucze uwierzytelniania są tworzone automatycznie.

* Uwierzytelnianie jest domyślnie włączone podczas wdrażania w Azure Kubernetes Service.
* Uwierzytelnianie jest domyślnie wyłączone podczas wdrażania w Azure Container Instances.

Aby kontrolować uwierzytelnianie, `auth_enabled` użyj parametru podczas tworzenia lub aktualizowania wdrożenia.

Jeśli uwierzytelnianie jest włączone, możesz użyć metody `get_keys` , aby pobrać podstawowy i pomocniczy klucz uwierzytelniania:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Jeśli musisz ponownie wygenerować klucz, [`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29) użyj .

#### <a name="authentication-with-tokens"></a>Uwierzytelnianie za pomocą tokenów

Po włączeniu uwierzytelniania tokenu dla usługi sieci Web użytkownik musi podać token JWT Azure Machine Learning do usługi internetowej, aby uzyskać do niego dostęp. 

* Uwierzytelnianie tokenu jest domyślnie wyłączone podczas wdrażania w Azure Kubernetes Service.
* Uwierzytelnianie za pomocą tokenu nie jest obsługiwane w przypadku wdrażania w Azure Container Instances.

Aby kontrolować uwierzytelnianie tokenu, `token_auth_enabled` użyj parametru podczas tworzenia lub aktualizowania wdrożenia.

Jeśli jest włączone uwierzytelnianie tokenu, możesz użyć metody w celu pobrania tokenu okaziciela i czasu `get_token` wygaśnięcia tokenów:

```python
token, refresh_by = service.get_token()
print(token)
```

Jeśli masz interfejs wiersza polecenia platformy Azure i rozszerzenie [uczenia maszynowego](reference-azure-machine-learning-cli.md), możesz użyć następującego polecenia, aby uzyskać token:

```azurecli
az ml service get-access-token -n <service-name>
```

> [!IMPORTANT]
> Obecnie jedynym sposobem pobrania tokenu jest użycie zestawu SDK usługi Azure Machine Learning lub rozszerzenia uczenia maszynowego interfejsu wiersza polecenia platformy Azure.

Po upływie czasu tokenu należy zażądać nowego `refresh_by` tokenu. 

## <a name="request-data"></a>Żądanie danych

Interfejs API REST oczekuje, że treść żądania będzie dokumentem JSON o następującej strukturze:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> Struktura danych musi odpowiadać oczekiwaniom skryptu oceniania i modelu w usłudze. Skrypt oceniania może modyfikować dane przed przekazaniem ich do modelu.

### <a name="binary-data"></a>Dane binarne

Aby uzyskać informacje na temat włączania obsługi danych binarnych w usłudze, zobacz [Dane binarne](how-to-deploy-advanced-entry-script.md#binary-data).

> [!TIP]
> Włączenie obsługi danych binarnych odbywa się w score.py używanym przez wdrożony model. Z klienta użyj funkcji HTTP języka programowania. Na przykład poniższy fragment kodu wysyła zawartość pliku JPG do usługi internetowej:
>
> ```python
> import requests
> # Load image data
> data = open('example.jpg', 'rb').read()
> # Post raw data to scoring URI
> res = request.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/> octet-stream'})
> ```

### <a name="cross-origin-resource-sharing-cors"></a>Współużytkowanie zasobów między źródłami (CORS)

Aby uzyskać informacje na temat włączania obsługi cors w usłudze, zobacz [Współużytkowania zasobów między źródłami](how-to-deploy-advanced-entry-script.md#cors).

## <a name="call-the-service-c"></a>Wywołanie usługi (C#)

W tym przykładzie pokazano, jak używać języka C# do wywołania usługi internetowej utworzonej na podstawie [przykładu Train within notebook (Trenuj w notesie):](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb)

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MLWebServiceClient
{
    // The data structure expected by the service
    internal class InputData
    {
        [JsonProperty("data")]
        // The service used by this example expects an array containing
        //   one or more arrays of doubles
        internal double[,] data;
    }
    class Program
    {
        static void Main(string[] args)
        {
            // Set the scoring URI and authentication key or token
            string scoringUri = "<your web service URI>";
            string authKey = "<your key or token>";

            // Set the data to be sent to the service.
            // In this case, we are sending two sets of data to be scored.
            InputData payload = new InputData();
            payload.data = new double[,] {
                {
                    0.0199132141783263,
                    0.0506801187398187,
                    0.104808689473925,
                    0.0700725447072635,
                    -0.0359677812752396,
                    -0.0266789028311707,
                    -0.0249926566315915,
                    -0.00259226199818282,
                    0.00371173823343597,
                    0.0403433716478807
                },
                {
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303
                }
            };

            // Create the HTTP client
            HttpClient client = new HttpClient();
            // Set the auth header. Only needed if the web service requires authentication.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authKey);

            // Make the request
            try {
                var request = new HttpRequestMessage(HttpMethod.Post, new Uri(scoringUri));
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;
                // Display the response from the web service
                Console.WriteLine(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
    }
}
```

Zwrócone wyniki są podobne do następującego dokumentu JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>Wywołanie usługi (Go)

W tym przykładzie pokazano, jak używać go do wywołania usługi internetowej utworzonej na podstawie [przykładu Train within notebook (Trenuj w notesie):](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// Features for this model are an array of decimal values
type Features []float64

// The web service input can accept multiple sets of values for scoring
type InputData struct {
    Data []Features `json:"data",omitempty`
}

// Define some example data
var exampleData = []Features{
    []float64{
        0.0199132141783263, 
        0.0506801187398187, 
        0.104808689473925, 
        0.0700725447072635, 
        -0.0359677812752396, 
        -0.0266789028311707, 
        -0.0249926566315915, 
        -0.00259226199818282, 
        0.00371173823343597, 
        0.0403433716478807,
    },
    []float64{
        -0.0127796318808497, 
        -0.044641636506989, 
        0.0606183944448076, 
        0.0528581912385822, 
        0.0479653430750293, 
        0.0293746718291555, 
        -0.0176293810234174, 
        0.0343088588777263, 
        0.0702112981933102, 
        0.00720651632920303,
    },
}

// Set to the URI for your service
var serviceUri string = "<your web service URI>"
// Set to the authentication key or token (if any) for your service
var authKey string = "<your key or token>"

func main() {
    // Create the input data from example data
    jsonData := InputData{
        Data: exampleData,
    }
    // Create JSON from it and create the body for the HTTP request
    jsonValue, _ := json.Marshal(jsonData)
    body := bytes.NewBuffer(jsonValue)

    // Create the HTTP request
    client := &http.Client{}
    request, err := http.NewRequest("POST", serviceUri, body)
    request.Header.Add("Content-Type", "application/json")

    // These next two are only needed if using an authentication key
    bearer := fmt.Sprintf("Bearer %v", authKey)
    request.Header.Add("Authorization", bearer)

    // Send the request to the web service
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Failure: ", err)
    }

    // Display the response received
    respBody, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(respBody))
}
```

Zwrócone wyniki są podobne do następującego dokumentu JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>Wywołanie usługi (Java)

W tym przykładzie pokazano, jak używać języka Java do wywołania usługi internetowej utworzonej na podstawie [przykładu Train within notebook (Trenuj w notesie):](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb)

```java
import java.io.IOException;
import org.apache.http.client.fluent.*;
import org.apache.http.entity.ContentType;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class App {
    // Handle making the request
    public static void sendRequest(String data) {
        // Replace with the scoring_uri of your service
        String uri = "<your web service URI>";
        // If using authentication, replace with the auth key or token
        String key = "<your key or token>";
        try {
            // Create the request
            Content content = Request.Post(uri)
            .addHeader("Content-Type", "application/json")
            // Only needed if using authentication
            .addHeader("Authorization", "Bearer " + key)
            // Set the JSON data as the body
            .bodyString(data, ContentType.APPLICATION_JSON)
            // Make the request and display the response.
            .execute().returnContent();
            System.out.println(content);
        }
        catch (IOException e) {
            System.out.println(e);
        }
    }
    public static void main(String[] args) {
        // Create the data to send to the service
        JSONObject obj = new JSONObject();
        // In this case, it's an array of arrays
        JSONArray dataItems = new JSONArray();
        // Inner array has 10 elements
        JSONArray item1 = new JSONArray();
        item1.add(0.0199132141783263);
        item1.add(0.0506801187398187);
        item1.add(0.104808689473925);
        item1.add(0.0700725447072635);
        item1.add(-0.0359677812752396);
        item1.add(-0.0266789028311707);
        item1.add(-0.0249926566315915);
        item1.add(-0.00259226199818282);
        item1.add(0.00371173823343597);
        item1.add(0.0403433716478807);
        // Add the first set of data to be scored
        dataItems.add(item1);
        // Create and add the second set
        JSONArray item2 = new JSONArray();
        item2.add(-0.0127796318808497);
        item2.add(-0.044641636506989);
        item2.add(0.0606183944448076);
        item2.add(0.0528581912385822);
        item2.add(0.0479653430750293);
        item2.add(0.0293746718291555);
        item2.add(-0.0176293810234174);
        item2.add(0.0343088588777263);
        item2.add(0.0702112981933102);
        item2.add(0.00720651632920303);
        dataItems.add(item2);
        obj.put("data", dataItems);

        // Make the request using the JSON document string
        sendRequest(obj.toJSONString());
    }
}
```

Zwrócone wyniki są podobne do następującego dokumentu JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>Wywołanie usługi (Python)

W tym przykładzie pokazano, jak używać języka Python do wywołania usługi internetowej utworzonej na podstawie [przykładu Train within notebook (Trenuj w notesie):](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb)

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key or token
key = '<your key or token>'

# Two sets of data to score, so we get two results back
data = {"data":
        [
            [
                0.0199132141783263,
                0.0506801187398187,
                0.104808689473925,
                0.0700725447072635,
                -0.0359677812752396,
                -0.0266789028311707,
                -0.0249926566315915,
                -0.00259226199818282,
                0.00371173823343597,
                0.0403433716478807
            ],
            [
                -0.0127796318808497,
                -0.044641636506989,
                0.0606183944448076,
                0.0528581912385822,
                0.0479653430750293,
                0.0293746718291555,
                -0.0176293810234174,
                0.0343088588777263,
                0.0702112981933102,
                0.00720651632920303]
        ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = {'Content-Type': 'application/json'}
# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Zwrócone wyniki są podobne do następującego dokumentu JSON:

```JSON
[217.67978776218715, 224.78937091757172]
```


## <a name="web-service-schema-openapi-specification"></a>Schemat usługi sieci Web (specyfikacja interfejsu OpenAPI)

Jeśli podczas wdrażania używasz automatycznego generowania schematów, możesz uzyskać adres specyfikacji openaPI dla usługi przy użyciu [właściwości swagger_uri](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#swagger-uri). (Na przykład `print(service.swagger_uri)` ). Użyj żądania GET lub otwórz w przeglądarce dane URI, aby pobrać specyfikację.

Poniższy dokument JSON jest przykładem schematu (specyfikacji OpenAPI) wygenerowanego dla wdrożenia:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [Specyfikacja openAPI.](https://swagger.io/specification/)

Aby uzyskać narzędzie, które może tworzyć biblioteki klienckie na pomocą specyfikacji, zobacz [swagger-codegen](https://github.com/swagger-api/swagger-codegen).


> [!TIP]
> Dokument JSON schematu można pobrać po wdrożeniu usługi. Użyj [właściwości swagger_uri z](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#swagger-uri) wdrożonej usługi internetowej (na przykład ), aby pobrać adres URI do pliku `service.swagger_uri` swagger lokalnej usługi internetowej.

## <a name="consume-the-service-from-power-bi"></a>Wykorzystanie usługi z Power BI

Power BI obsługuje zużycie Azure Machine Learning internetowych w celu wzbogacenia danych Power BI o przewidywania. 

Aby wygenerować usługę internetową obsługiwaną do użycia w usłudze Power BI, schemat musi obsługiwać format wymagany przez program Power BI. [Dowiedz się, jak utworzyć Power BI obsługiwany schemat.](./how-to-deploy-advanced-entry-script.md#power-bi-compatible-endpoint)

Po wdrożeniu usługi internetowej może być ona używana przez przepływy danych usługi Power BI. [Dowiedz się, jak korzystać z usługi internetowej Azure Machine Learning za pomocą usługi Power BI](/power-bi/service-machine-learning-integration).

## <a name="next-steps"></a>Następne kroki

Aby wyświetlić architekturę referencyjną oceniania modeli języka Python i uczenia głębokiego w czasie rzeczywistym, przejdź do centrum [architektury platformy Azure.](/azure/architecture/reference-architectures/ai/realtime-scoring-python)
