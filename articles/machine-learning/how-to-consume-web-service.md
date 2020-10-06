---
title: Utwórz klienta dla modelu wdrożonego jako usługa sieci Web
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wywołać punkt końcowy usługi sieci Web wygenerowany, gdy model został wdrożony z Azure Machine Learning. Punkt końcowy uwidacznia interfejs API REST, który można wywołać w celu wykonania wnioskowania z modelem. Tworzenie klientów dla tego interfejsu API przy użyciu wybranego języka programowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-csharp
ms.openlocfilehash: 6aacc2778e02b96f31c633671da014ced30778fd
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91756674"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Korzystanie z modelu usługi Azure Machine Learning wdrożonego jako usługa internetowa


Wdrożenie modelu Azure Machine Learning jako usługi sieci Web powoduje utworzenie punktu końcowego interfejsu API REST. Dane można wysyłać do tego punktu końcowego i odbierać prognozę zwracaną przez model. W tym dokumencie dowiesz się, jak tworzyć klientów dla usługi sieci Web przy użyciu języków C#, go, Java i Python.

Usługa sieci Web jest tworzona podczas wdrażania modelu w środowisku lokalnym, Azure Container Instances, usługi Azure Kubernetes lub tablic bram opartych na polach (FPGA). Użytkownik pobiera identyfikator URI służący do uzyskiwania dostępu do usługi sieci Web przy użyciu [zestawu SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true). Jeśli jest włączone uwierzytelnianie, można również pobrać klucze uwierzytelniania lub tokeny przy użyciu zestawu SDK.

Ogólny przepływ pracy służący do tworzenia klienta korzystającego z usługi sieci Web Machine Learning to:

1. Użyj zestawu SDK, aby uzyskać informacje o połączeniu.
1. Określ typ danych żądania używanych przez model.
1. Utwórz aplikację, która wywołuje usługę sieci Web.

> [!TIP]
> Przykłady w tym dokumencie są tworzone ręcznie bez użycia specyfikacji OpenAPI (Swagger). Jeśli została włączona Specyfikacja OpenAPI dla danego wdrożenia, można użyć narzędzi, takich jak [Swagger-codegen](https://github.com/swagger-api/swagger-codegen) , aby utworzyć biblioteki klienckie dla usługi.

## <a name="connection-information"></a>Informacje o połączeniu

> [!NOTE]
> Użyj zestawu SDK Azure Machine Learning, aby uzyskać informacje o usłudze sieci Web. To jest zestaw SDK języka Python. Możesz użyć dowolnego języka, aby utworzyć klienta dla usługi.

Klasa [Azure. Core. WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true) zawiera informacje potrzebne do utworzenia klienta. Następujące `Webservice` właściwości są przydatne podczas tworzenia aplikacji klienckiej:

* `auth_enabled` -Jeśli jest włączone uwierzytelnianie klucza `True` ; w przeciwnym razie `False` .
* `token_auth_enabled` -Jeśli uwierzytelnianie tokenu jest włączone, `True` w przeciwnym razie, `False` .
* `scoring_uri` — Adres interfejsu API REST.
* `swagger_uri` -Adres specyfikacji OpenAPI. Ten identyfikator URI jest dostępny po włączeniu automatycznego generowania schematu. Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](how-to-deploy-and-where.md).

Istnieją trzy sposoby pobierania tych informacji dla wdrożonych usług sieci Web:

* Podczas wdrażania modelu `Webservice` obiekt jest zwracany z informacjami o usłudze:

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* Możesz użyć, `Webservice.list` Aby pobrać listę wdrożonych usług sieci Web dla modeli w obszarze roboczym. Filtry można dodać, aby zawęzić listę zwracanych informacji. Aby uzyskać więcej informacji o tym, co można filtrować, zobacz dokumentację dotyczącą usługi [WebService. list](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py&preserve-view=true) .

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* Jeśli znasz nazwę wdrożonej usługi, możesz utworzyć nowe wystąpienie programu `Webservice` i podać nazwę obszaru roboczego i usługi jako parametry. Nowy obiekt zawiera informacje o wdrożonej usłudze.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

### <a name="secured-web-service"></a>Zabezpieczona usługa sieci Web

Jeśli wdrożona usługa sieci Web została zabezpieczona przy użyciu certyfikatu TLS/SSL, możesz użyć [protokołu HTTPS](https://en.wikipedia.org/wiki/HTTPS) , aby nawiązać połączenie z usługą przy użyciu identyfikatora URI tworzenia oceny lub struktury Swagger. Protokół HTTPS pomaga w zabezpieczeniu komunikacji między klientem a usługą sieci Web przez szyfrowanie komunikacji między nimi. Szyfrowanie używa [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Protokół TLS jest czasami nazywany *SSL* (SSL), który był POPRZEDNIKIEM protokołu TLS.

> [!IMPORTANT]
> Usługi sieci Web wdrożone przez Azure Machine Learning obsługują tylko protokół TLS w wersji 1,2. Podczas tworzenia aplikacji klienckiej upewnij się, że jest ona obsługiwana w tej wersji.

Aby uzyskać więcej informacji, zobacz [Korzystanie z protokołu TLS w celu zabezpieczenia usługi sieci Web za pomocą Azure Machine Learning](how-to-secure-web-service.md).

### <a name="authentication-for-services"></a>Uwierzytelnianie dla usług

Azure Machine Learning zapewnia dwa sposoby kontroli dostępu do usług sieci Web.

|Metoda uwierzytelniania|ACI|AKS|
|---|---|---|
|Klucz|Domyślnie wyłączone| Domyślnie włączone|
|Token| Niedostępny| Domyślnie wyłączone |

Podczas wysyłania żądania do usługi, która jest zabezpieczona za pomocą klucza lub tokenu, użyj nagłówka __autoryzacji__ w celu przekazania klucza lub tokenu. Klucz lub token musi być sformatowany jako `Bearer <key-or-token>` , gdzie `<key-or-token>` jest wartością klucza lub tokenu.

Podstawowa różnica między kluczami i tokenami polega na tym, że **klucze są statyczne i mogą być ponownie generowane ręcznie**, a **tokeny muszą być odświeżane po wygaśnięciu**. Uwierzytelnianie oparte na kluczach jest obsługiwane dla wystąpień kontenerów platformy Azure i wdrożonych usług sieci Web usługi Azure Kubernetes, a uwierzytelnianie oparte na tokenach jest dostępne **tylko** dla wdrożeń usługi Azure Kubernetes. Zobacz instrukcje [dotyczące](how-to-setup-authentication.md#web-service-authentication) uwierzytelniania, aby uzyskać więcej informacji i przykłady kodu.


#### <a name="authentication-with-keys"></a>Uwierzytelnianie przy użyciu kluczy

Po włączeniu uwierzytelniania dla wdrożenia automatycznie tworzone są klucze uwierzytelniania.

* Uwierzytelnianie jest domyślnie włączone podczas wdrażania w usłudze Azure Kubernetes Service.
* Podczas wdrażania programu w celu Azure Container Instances uwierzytelnianie jest domyślnie wyłączone.

Aby kontrolować uwierzytelnianie, użyj `auth_enabled` parametru podczas tworzenia lub aktualizowania wdrożenia.

Jeśli jest włączone uwierzytelnianie, można użyć metody, `get_keys` Aby pobrać podstawowy i pomocniczy klucz uwierzytelniania:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Jeśli musisz ponownie wygenerować klucz, użyj [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true) .

#### <a name="authentication-with-tokens"></a>Uwierzytelnianie przy użyciu tokenów

Po włączeniu uwierzytelniania tokenów dla usługi sieci Web użytkownik musi podać Azure Machine Learning token JWT do usługi sieci Web, aby uzyskać do niej dostęp. 

* Uwierzytelnianie tokenu jest domyślnie wyłączone podczas wdrażania w usłudze Azure Kubernetes Service.
* Uwierzytelnianie tokenu nie jest obsługiwane w przypadku wdrażania do Azure Container Instances.

Aby kontrolować uwierzytelnianie tokenu, użyj `token_auth_enabled` parametru podczas tworzenia lub aktualizowania wdrożenia.

Jeśli jest włączone uwierzytelnianie tokenu, można użyć metody, `get_token` Aby pobrać token okaziciela i czas wygaśnięcia tokenów:

```python
token, refresh_by = service.get_token()
print(token)
```

Jeśli masz [interfejs wiersza polecenia platformy Azure i rozszerzenie uczenia maszynowego](reference-azure-machine-learning-cli.md), możesz użyć następującego polecenia, aby uzyskać token:

```azurecli
az ml service get-access-token -n <service-name>
```

> [!IMPORTANT]
> Obecnie jedynym sposobem na pobranie tokenu jest użycie zestawu SDK Azure Machine Learning lub rozszerzenia Azure Machine Learning.

Po upływie czasu tokenu trzeba będzie zażądać nowego tokenu `refresh_by` . 

## <a name="request-data"></a>Dane żądania

Interfejs API REST oczekuje treści żądania jako dokumentu JSON o następującej strukturze:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> Struktura danych musi być zgodna z oczekiwanym skryptem oceniania i modelem w usłudze. Skrypt oceniania może zmodyfikować dane przed przekazaniem ich do modelu.

### <a name="binary-data"></a>Dane binarne

Aby uzyskać informacje na temat włączania obsługi danych binarnych w usłudze, zobacz [dane binarne](how-to-deploy-advanced-entry-script.md#binary-data).

> [!TIP]
> Włączenie obsługi danych binarnych odbywa się w pliku score.py używanym przez wdrożony model. Z poziomu klienta Użyj funkcji HTTP języka programowania. Na przykład poniższy fragment kodu wysyła zawartość pliku JPG do usługi sieci Web:
>
> ```python
> import requests
> # Load image data
> data = open('example.jpg', 'rb').read()
> # Post raw data to scoring URI
> res = request.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/> octet-stream'})
> ```

### <a name="cross-origin-resource-sharing-cors"></a>Współużytkowanie zasobów między źródłami (CORS)

Aby uzyskać informacje na temat włączania obsługi mechanizmu CORS w usłudze, zobacz [udostępnianie zasobów między źródłami](how-to-deploy-advanced-entry-script.md#cors).

## <a name="call-the-service-c"></a>Wywoływanie usługi (C#)

W tym przykładzie pokazano, jak używać języka C# do wywoływania usługi sieci Web utworzonej na podstawie [szkolenia w ramach przykładu notesu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb) :

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

## <a name="call-the-service-go"></a>Wywoływanie usługi (go)

W tym przykładzie pokazano, jak używać języka go do wywoływania usługi sieci Web utworzonej na podstawie [szkolenia w ramach przykładu notesu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/notebook_example.ipynb) :

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

## <a name="call-the-service-java"></a>Wywoływanie usługi (Java)

W tym przykładzie pokazano, jak używać języka Java do wywoływania usługi sieci Web utworzonej na podstawie [szkolenia w ramach przykładu notesu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/notebook_example.ipynb) :

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

## <a name="call-the-service-python"></a>Wywoływanie usługi (Python)

W tym przykładzie pokazano, jak używać języka Python do wywoływania usługi sieci Web utworzonej na podstawie [szkolenia w ramach przykładu notesu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/notebook_example.ipynb) :

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


## <a name="web-service-schema-openapi-specification"></a>Schemat usługi sieci Web (Specyfikacja OpenAPI)

Jeśli używasz automatycznej generacji schematu w ramach wdrożenia, możesz uzyskać adres specyfikacji OpenAPI dla usługi przy użyciu [właściwości swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py&preserve-view=true#&preserve-view=trueswagger-uri). (Na przykład `print(service.swagger_uri)` .) Użyj żądania GET lub Otwórz identyfikator URI w przeglądarce, aby pobrać specyfikację.

Poniższy dokument JSON jest przykładem schematu (Specyfikacja OpenAPI) wygenerowanego dla wdrożenia:

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

Aby uzyskać więcej informacji, zobacz [Specyfikacja openapi](https://swagger.io/specification/).

Aby uzyskać narzędzie, które może tworzyć biblioteki klienckie ze specyfikacją, zobacz [Swagger-codegen](https://github.com/swagger-api/swagger-codegen).


> [!TIP]
> Po wdrożeniu usługi można pobrać dokument JSON schematu. Użyj [właściwości swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py&preserve-view=true#&preserve-view=trueswagger-uri) ze wdrożonej usługi sieci Web (na przykład `service.swagger_uri` ), aby uzyskać identyfikator URI do pliku struktury Swagger lokalnej usługi sieci Web.

## <a name="consume-the-service-from-power-bi"></a>Korzystanie z usługi z poziomu usługi Power BI

Power BI obsługuje użycie usług sieci Web Azure Machine Learning do wzbogacania danych w Power BI z przewidywaniami. 

Aby wygenerować usługę sieci Web obsługiwaną do użycia w Power BI, schemat musi obsługiwać format wymagany przez Power BI. [Dowiedz się, jak utworzyć schemat obsługiwany przez Power BI](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#example-entry-script).

Po wdrożeniu usługi sieci Web jest ona zużywana z Power BI przepływów danych. [Dowiedz się, jak korzystać z usługi sieci web Azure Machine Learning z Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="next-steps"></a>Następne kroki

Aby wyświetlić architekturę referencyjną dla oceny w czasie rzeczywistym dla modeli Python i głębokiego uczenia, przejdź do [centrum architektury platformy Azure](/azure/architecture/reference-architectures/ai/realtime-scoring-python).
