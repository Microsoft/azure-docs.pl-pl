---
title: 'ML Studio (klasyczny): korzystanie z usług sieci Web — Azure'
description: Po wdrożeniu usługi Machine Learning z poziomu Azure Machine Learning Studio (klasycznego) usługa sieci Web RESTFul może być używana jako usługa żądania-odpowiedzi w czasie rzeczywistym lub jako usługa wykonywania wsadowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-python, devx-track-js, devx-track-csharp
ms.date: 05/29/2020
ms.openlocfilehash: eaf0131877e7a333fe2a6f157523da5ad7bcf07b
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95500003"
---
# <a name="how-to-consume-a-machine-learning-studio-classic-web-service"></a>Jak korzystać z usługi sieci Web Machine Learning Studio (klasycznej)

**dotyczy:** ![ Jest to znacznik wyboru, co oznacza, że ten artykuł ma zastosowanie do Machine Learning Studio (klasyczne).  ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny)   ![ to jest X, co oznacza, że ten artykuł ma zastosowanie do Azure Machine Learning](../../../includes/media/aml-applies-to-skus/no.png)[ . Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Po wdrożeniu modelu predykcyjnego Azure Machine Learning Studio (klasyczny) jako usługi sieci Web można użyć interfejsu API REST do wysyłania danych IT i uzyskiwania prognoz. Dane można wysyłać w czasie rzeczywistym lub w trybie wsadowym.

Więcej informacji na temat sposobu tworzenia i wdrażania usługi sieci Web Machine Learning przy użyciu Machine Learning Studio (klasyczny) można znaleźć tutaj:

* Aby zapoznać się z samouczkiem dotyczącym tworzenia eksperymentu w Machine Learning Studio (klasyczny), zobacz [Tworzenie pierwszego eksperymentu](create-experiment.md).
* Aby uzyskać szczegółowe informacje na temat wdrażania usługi sieci Web, zobacz [wdrażanie usługi sieci web Machine Learning](deploy-a-machine-learning-web-service.md).
* Aby uzyskać więcej informacji na temat ogólnie Machine Learning, odwiedź [centrum dokumentacji Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).



## <a name="overview"></a>Omówienie
W przypadku usługi sieci Web Azure Machine Learning zewnętrzna aplikacja komunikuje się z modelem oceniania Machine Learning przepływów pracy w czasie rzeczywistym. Wywołanie usługi sieci Web Machine Learning zwraca wyniki prognozowania do aplikacji zewnętrznej. Aby wykonać wywołanie usługi sieci Web Machine Learning, należy przekazać klucz interfejsu API, który jest tworzony podczas wdrażania przewidywania. Usługa sieci Web Machine Learning jest oparta na architekturze REST, popularnej architektury dla projektów programowania w sieci Web.

Azure Machine Learning Studio (klasyczny) ma dwa typy usług:

* Request-Response Service (RR) — małe opóźnienia i wysoce skalowalna usługa udostępniająca interfejs do modeli bezstanowych utworzonych i wdrożonych z Machine Learning Studio (klasyczny).
* Usługa wykonywania wsadowego (BES) — asynchroniczna usługa służąca do oceniania partii rekordów danych.

Aby uzyskać więcej informacji na temat Machine Learning usług sieci Web, zobacz [wdrażanie usługi sieci web Machine Learning](deploy-a-machine-learning-web-service.md).

## <a name="get-an-authorization-key"></a>Pobieranie klucza autoryzacji
Podczas wdrażania eksperymentu klucze interfejsu API są generowane dla usługi sieci Web. Klucze można pobrać z kilku lokalizacji.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>W portalu usług sieci Web Microsoft Azure Machine Learning
Zaloguj się do portalu [usług sieci Web Microsoft Azure Machine Learning](https://services.azureml.net) .

Aby pobrać klucz interfejsu API dla nowej usługi sieci Web Machine Learning:

1. W portalu usług sieci Web Azure Machine Learning kliknij pozycję **usługi sieci Web** w górnym menu.
2. Kliknij usługę sieci Web, dla której chcesz pobrać klucz.
3. W górnym **menu kliknij pozycję** Użyj.
4. Skopiuj i Zapisz **klucz podstawowy**.

Aby pobrać klucz interfejsu API dla klasycznej usługi sieci Web Machine Learning:

1. W portalu usług sieci Web Azure Machine Learning kliknij pozycję **klasyczne usługi sieci Web** w górnym menu.
2. Kliknij usługę sieci Web, z którą pracujesz.
3. Kliknij punkt końcowy, dla którego chcesz pobrać klucz.
4. W górnym **menu kliknij pozycję** Użyj.
5. Skopiuj i Zapisz **klucz podstawowy**.

### <a name="classic-web-service"></a>Klasyczna usługa sieci Web
 Możesz również pobrać klucz dla klasycznej usługi sieci Web z Machine Learning Studio (klasyczny).

#### <a name="machine-learning-studio-classic"></a>Usługa Machine Learning Studio (klasyczna)
1. W Machine Learning Studio (klasyczny) kliknij pozycję **usługi sieci Web** po lewej stronie.
2. Kliknij usługę sieci Web. **Klucz interfejsu API** znajduje się na karcie **pulpit nawigacyjny** .

## <a name="connect-to-a-machine-learning-web-service"></a><a id="connect"></a>Nawiązywanie połączenia z usługą sieci Web Machine Learning
Można nawiązać połączenie z usługą sieci Web Machine Learning przy użyciu dowolnego języka programowania, który obsługuje żądania HTTP i odpowiedzi. Przykłady w językach C#, Python i R można wyświetlić na stronie pomocy usługi sieci Web Machine Learning.

**Pomoc interfejsu API Machine Learning** Pomoc interfejsu API Machine Learning jest tworzona podczas wdrażania usługi sieci Web. Zobacz [samouczek 3: Wdrażanie modelu ryzyka kredytowego](tutorial-part3-credit-risk-deploy.md).
Pomoc interfejsu API Machine Learning zawiera szczegółowe informacje o usłudze sieci Web przewidywania.

1. Kliknij usługę sieci Web, z którą pracujesz.
2. Kliknij punkt końcowy, dla którego chcesz wyświetlić stronę pomocy interfejsu API.
3. W górnym **menu kliknij pozycję** Użyj.
4. Kliknij pozycję **Pomoc interfejsu API** w obszarze punkty końcowe Request-Response lub wykonywania wsadowego.

**Aby wyświetlić Pomoc interfejsu API Machine Learning dla nowej usługi sieci Web**

W [portalu Azure Machine Learning Web Services](https://services.azureml.net/):

1. Kliknij pozycję **usługi sieci Web** w górnym menu.
2. Kliknij usługę sieci Web, dla której chcesz pobrać klucz.

Kliknij pozycję **Użyj usługi sieci Web** , aby uzyskać identyfikatory URI Request-Response i usług wykonywania wsadowego oraz przykładowego kodu w językach C#, R i Python.

Kliknij pozycję **interfejs API struktury Swagger** , aby uzyskać dokumentację opartą na strukturze interfejsów API wywoływaną z dostarczonych identyfikatorów URI.

### <a name="c-sample"></a>Przykład w języku C#
Aby nawiązać połączenie z usługą sieci Web Machine Learning, użyj **HttpClient** Pass ScoreData. ScoreData zawiera FeatureVector, wektor n-wymiarowy funkcji liczbowych, które reprezentują ScoreData. Uwierzytelnianie w usłudze Machine Learning przy użyciu klucza interfejsu API.

Aby nawiązać połączenie z usługą sieci Web Machine Learning, należy zainstalować pakiet NuGet **Microsoft. ASPNET. WebApi. Client** .

**Zainstaluj pakiet NuGet Microsoft. AspNet. WebApi. Client w programie Visual Studio**

1. Opublikuj zestaw danych do pobrania z usługi sieci Web zestaw danych o treści 1.
2. Kliknij kolejno pozycje **Narzędzia** Menedżer  >  **pakietów NuGet**  >  **konsola Menedżera pakietów**.
3. Wybierz polecenie **install-package Microsoft. ASPNET. WebApi. Client**.

**Aby uruchomić przykładowy kod**

1. Publikowanie "przykład 1: pobieranie zestawu danych z/UCI: dorosły zestaw danych klasy" eksperymentowanie, część przykładowej kolekcji Machine Learning.
2. Przypisz apiKey z kluczem z usługi sieci Web. Zobacz **Pobieranie klucza autoryzacji** powyżej.
3. Przypisz serviceUri z identyfikatorem URI żądania.

**Oto, jak będzie wyglądać pełne żądanie.**
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Formatting;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

namespace CallRequestResponseService
{
    class Program
    {
        static void Main(string[] args)
        {
            InvokeRequestResponseService().Wait();
        }

        static async Task InvokeRequestResponseService()
        {
            using (var client = new HttpClient())
            {
                var scoreRequest = new
                {
                    Inputs = new Dictionary<string, List<Dictionary<string, string>>> () {
                        {
                            "input1",
                            // Replace columns labels with those used in your dataset
                            new List<Dictionary<string, string>>(){new Dictionary<string, string>(){
                                    {
                                        "column1", "value1"
                                    },
                                    {
                                        "column2", "value2"
                                    },
                                    {
                                        "column3", "value3"
                                    }
                                }
                            }
                        },
                    },
                    GlobalParameters = new Dictionary<string, string>() {}
                };

                // Replace these values with your API key and URI found on https://services.azureml.net/
                const string apiKey = "<your-api-key>"; 
                const string apiUri = "<your-api-uri>";
                
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
                client.BaseAddress = new Uri(apiUri);

                // WARNING: The 'await' statement below can result in a deadlock
                // if you are calling this code from the UI thread of an ASP.NET application.
                // One way to address this would be to call ConfigureAwait(false)
                // so that the execution does not attempt to resume on the original context.
                // For instance, replace code such as:
                //      result = await DoSomeTask()
                // with the following:
                //      result = await DoSomeTask().ConfigureAwait(false)

                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);

                if (response.IsSuccessStatusCode)
                {
                    string result = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("Result: {0}", result);
                }
                else
                {
                    Console.WriteLine(string.Format("The request failed with status code: {0}", response.StatusCode));

                    // Print the headers - they include the request ID and the timestamp,
                    // which are useful for debugging the failure
                    Console.WriteLine(response.Headers.ToString());

                    string responseContent = await response.Content.ReadAsStringAsync();
                    Console.WriteLine(responseContent);
                }
            }
        }
    }
}
```

### <a name="python-sample"></a>Przykład języka Python
Aby nawiązać połączenie z usługą sieci Web Machine Learning, użyj biblioteki **urllib2** dla języka Python 2. x i **urllib. Request** Library dla języka Python 3. x. Zostanie przekazany ScoreData, który zawiera FeatureVector, wektora n-wymiarowej funkcji liczbowych, które reprezentują ScoreData. Uwierzytelnianie w usłudze Machine Learning przy użyciu klucza interfejsu API.

**Aby uruchomić przykładowy kod**

1. Wdróż "przykład 1: Pobierz zestaw danych z//lub zestawu danych typu" Dorosła: dorosły 2 ", część przykładowej kolekcji Machine Learning.
2. Przypisz apiKey z kluczem z usługi sieci Web. Zobacz sekcję **Pobieranie klucza autoryzacji na** początku tego artykułu.
3. Przypisz serviceUri z identyfikatorem URI żądania.

**Oto, jak będzie wyglądać pełne żądanie.**
```python
import urllib2 # urllib.request and urllib.error for Python 3.X
import json

data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",   
                'column2': "value2",   
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters":  {}
}

body = str.encode(json.dumps(data))

# Replace this with the URI and API Key for your web service
url = '<your-api-uri>'
api_key = '<your-api-key>'
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

# "urllib.request.Request(url, body, headers)" for Python 3.X
req = urllib2.Request(url, body, headers)

try:
    # "urllib.request.urlopen(req)" for Python 3.X
    response = urllib2.urlopen(req)

    result = response.read()
    print(result)
# "urllib.error.HTTPError as error" for Python 3.X
except urllib2.HTTPError, error: 
    print("The request failed with status code: " + str(error.code))

    # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
    print(error.info())
    print(json.loads(error.read())) 
```

### <a name="r-sample"></a>Przykład R

Aby nawiązać połączenie z usługą sieci Web Machine Learning, użyj bibliotek **RCurl** i **rjson** , aby wykonać żądanie i przetworzyć zwróconą odpowiedź JSON. Zostanie przekazany ScoreData, który zawiera FeatureVector, wektora n-wymiarowej funkcji liczbowych, które reprezentują ScoreData. Uwierzytelnianie w usłudze Machine Learning przy użyciu klucza interfejsu API.

**Oto, jak będzie wyglądać pełne żądanie.**
```r
library("curl")
library("httr")
library("rjson")

requestFailed = function(response) {
    return (response$status_code >= 400)
}

printHttpResult = function(response, result) {
    if (requestFailed(response)) {
        print(paste("The request failed with status code:", response$status_code, sep=" "))
    
        # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
        print(response$headers)
    }
    
    print("Result:") 
    print(fromJSON(result))  
}

req = list(
        Inputs = list( 
            "input1" = list(
                "ColumnNames" = list("Col1", "Col2", "Col3"),
                "Values" = list( list( "0", "value", "0" ),  list( "0", "value", "0" )  )
            )                ),
        GlobalParameters = setNames(fromJSON('{}'), character(0))
)

body = enc2utf8(toJSON(req))
api_key = "abc123" # Replace this with the API key for the web service
authz_hdr = paste('Bearer', api_key, sep=' ')

response = POST(url= "<your-api-uri>",
        add_headers("Content-Type" = "application/json", "Authorization" = authz_hdr),
        body = body)

result = content(response, type="text", encoding="UTF-8")

printHttpResult(response, result)
```

### <a name="javascript-sample"></a>Przykładowy kod JavaScript

Aby nawiązać połączenie z usługą sieci Web Machine Learning, Użyj pakietu **Request** npm w projekcie. Możesz również użyć obiektu, `JSON` Aby sformatować dane wejściowe i przeanalizować wynik. Zainstaluj `npm install request --save` program lub Dodaj `"request": "*"` do package.jsw obszarze `dependencies` i uruchom `npm install` .

**Oto, jak będzie wyglądać pełne żądanie.**
```js
let req = require("request");

const uri = "<your-api-uri>";
const apiKey = "<your-api-key>";

let data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",
                'column2': "value2",
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters": {}
}

const options = {
    uri: uri,
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        "Authorization": "Bearer " + apiKey,
    },
    body: JSON.stringify(data)
}

req(options, (err, res, body) => {
    if (!err && res.statusCode == 200) {
        console.log(body);
    } else {
        console.log("The request failed with status code: " + res.statusCode);
    }
});
```