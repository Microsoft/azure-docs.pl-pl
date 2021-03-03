---
title: Wprowadzenie do tłumaczenia dokumentu
description: Jak utworzyć usługę tłumaczenia dokumentów przy użyciu języków programowania w języku C#, go, Java, Node.js lub Python
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: 886889ef9a42e358fca22a9d86955a23c5419dfa
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738161"
---
# <a name="get-started-with-document-translation-preview"></a>Wprowadzenie do tłumaczenia dokumentu (wersja zapoznawcza)

 W tym artykule przedstawiono sposób użycia tłumaczenia dokumentów z metodami interfejsu API REST protokołu HTTP. Tłumaczenie dokumentów to oparta na chmurze funkcja usługi [Azure translator](../translator-info-overview.md) .  Interfejs API tłumaczenia dokumentów umożliwia tłumaczenie całych dokumentów przy zachowaniu struktury dokumentu źródłowego i formatowania tekstu.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz:

* Aktywne [**konto platformy Azure**](https://azure.microsoft.com/free/cognitive-services/).  Jeśli go nie masz, możesz [**utworzyć bezpłatne konto**](https://azure.microsoft.com/free/).

* Zasób usługi [**translatora**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) (**nie** jest zasobem Cognitive Services). 

* [**Konto usługi Azure Blob Storage**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Cały dostęp do usługi Azure Storage odbywa się za pośrednictwem konta magazynu.

* [**Formularz tłumaczenie ukończonych dokumentów (wersja zapoznawcza)**](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-riVR3Xj0tOnIRdZOALbM9UOEE4UVdFQVBRQVBWWDBRQUM3WjYxUEpUTC4u) umożliwiający korzystanie z funkcji tłumaczenia nowych dokumentów na subskrypcję platformy Azure.

> [!NOTE]
> Tłumaczenie dokumentów jest obecnie obsługiwane tylko w ramach zasobu translatora (pojedynczej usługi), a **nie** do zasobu Cognitive Services (wiele usług).

## <a name="get-your-custom-domain-name-and-subscription-key"></a>Pobierz niestandardową nazwę domeny i klucz subskrypcji

> [!IMPORTANT]
>
> * Nie można użyć punktu końcowego znalezionego na stronie klucze zasobów Azure Portal _i punktu_ końcowego ani globalnego punktu końcowego usługi Translator — `api.cognitive.microsofttranslator.com` w celu żądania protokołu HTTP do tłumaczenia dokumentów.
> * **Wszystkie żądania interfejsu API do usługi tłumaczenia dokumentów wymagają niestandardowego punktu końcowego domeny**.

### <a name="what-is-the-custom-domain-endpoint"></a>Co to jest punkt końcowy domeny niestandardowej? 

Punkt końcowy domeny niestandardowej to adres URL sformatowany przy użyciu nazwy zasobu, nazwy hosta i podkatalogów translatora:

```http
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1
```

### <a name="find-your-custom-domain-name"></a>Znajdowanie niestandardowej nazwy domeny

Nazwa parametru- **zasobu** (nazywana również *niestandardową nazwą domeny*) jest wartością wprowadzoną w polu **Nazwa** podczas tworzenia zasobu usługi Translator.

:::image type="content" source="../media/instance-details.png" alt-text="Obraz Azure Portal, utwórz zasób, błyskawiczne szczegóły, nazwa pola.":::

### <a name="get-your-subscription-key"></a>Pobierz klucz subskrypcji

Żądania do usługi Translator wymagają klucza tylko do odczytu w celu uwierzytelniania dostępu.

1. Jeśli został utworzony nowy zasób, po jego wdrożeniu wybierz pozycję **Przejdź do zasobu**. Jeśli masz istniejący zasób tłumaczenia dokumentu, przejdź bezpośrednio do strony zasobów.
1. W lewej szynie w obszarze *Zarządzanie zasobami* wybierz pozycję **klucze i punkt końcowy**.
1. Skopiuj i Wklej klucz subskrypcji w wygodnej lokalizacji, na przykład w *notatniku firmy Microsoft*.
1. Spowoduje to wklejenie do poniższego kodu w celu uwierzytelnienia Twojego żądania do usługi tłumaczenia dokumentów.

:::image type="content" source="../media/translator-keys.png" alt-text="Obraz pola &quot;Pobierz klucz subskrypcji&quot; w Azure Portal.":::

## <a name="create-your-azure-blob-storage-containers"></a>Tworzenie kontenerów usługi Azure Blob Storage

Należy  [**utworzyć kontenery**](../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) na [**koncie usługi Azure Blob Storage**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) , aby uzyskać źródłowe, docelowe i opcjonalne pliki słownika.

* **Kontener źródłowy**. Ten kontener to miejsce, w którym można przekazać pliki do tłumaczenia (wymagane).
* **Kontener docelowy**. W tym kontenerze znajdują się pliki przetłumaczone (wymagane).  
* **Kontener słownika**. Ten kontener jest miejscem, w którym można przekazać pliki słownika (opcjonalnie).  

*Zobacz* **Tworzenie tokenów dostępu SAS na potrzeby tłumaczenia dokumentów**

`sourceUrl`Parametry, `targetUrl` i opcjonalne `glossaryUrl` muszą zawierać token sygnatury dostępu współdzielonego (SAS), dołączane jako ciąg zapytania. Token może być przypisany do kontenera lub określonych obiektów BLOB.

* Kontener **źródłowy** lub obiekt BLOB musi mieć wyznaczono dostęp do  **odczytu** i **listy** .
* **Docelowy** kontener lub obiekt BLOB muszą mieć wyznaczono dostęp do **zapisu** i **listy** .
* Kontener lub obiekt BLOB **słownika** musi mieć wyznaczono dostęp do  **odczytu** i **listy** .

> [!TIP]
>
> * W przypadku tłumaczenia **wielu** plików (obiektów BLOB) w operacji, należy **delegować dostęp do sygnatury dostępu współdzielonego na poziomie kontenera**.  
> * W przypadku tłumaczenia **pojedynczego** pliku (obiektu BLOB) w operacji, należy **delegować dostęp do sygnatury dostępu współdzielonego na poziomie obiektu BLOB**.  
>

## <a name="set-up-your-coding-platform"></a>Skonfiguruj platformę kodowania

### <a name="c"></a>[C#](#tab/csharp)

* Tworzenie nowego projektu.
* Zamień Program.cs na kod w języku C# przedstawiony poniżej.
* Ustaw punkt końcowy. klucz subskrypcji i wartości adresu URL kontenera w Program.cs.
* Aby przetworzyć dane JSON, Dodaj [Newtonsoft.Jsw pakiecie przy użyciu interfejsu wiersza polecenia platformy .NET](https://www.nuget.org/packages/Newtonsoft.Json/).
* Uruchom program z katalogu projektu.

### <a name="nodejs"></a>[Node.js](#tab/javascript)

* Utwórz nowy projekt Node.js.
* Zainstaluj bibliotekę Axios za pomocą programu `npm i axios` .
* Kopiuj wklej poniższy kod do projektu.
* Ustaw wartości w punktach końcowych, klucz subskrypcji i adres URL kontenera.
* Uruchomisz program.

### <a name="python"></a>[Python](#tab/python)  

* Tworzenie nowego projektu.
* Skopiuj i wklej kod z jednego z przykładów do projektu.
* Ustaw wartości w punktach końcowych, klucz subskrypcji i adres URL kontenera.
* Uruchomisz program. Na przykład: `python translate.py`.

### <a name="java"></a>[Java](#tab/java)

* Utwórz katalog roboczy dla projektu. Na przykład:

```powershell
mkdir sample-project
```

* W katalogu projektu Utwórz następującą strukturę podkatalogu:  

  src</br>
&emsp; └ Główne</br>
&emsp;&emsp;&emsp;└ Java

```powershell
mkdir -p src/main/java/
```

**Uwaga**: pliki źródłowe Java (na przykład przykład _. Java_) na żywo w src/Main/**Java**.

* W katalogu głównym (na przykład przykład *-Project*) zainicjuj projekt przy użyciu Gradle:

```powershell
gradle init --type basic
```

* Po wyświetleniu monitu wybierz pozycję **Język DSL**, a następnie **Kotlin**.

* Zaktualizuj `build.gradle.kts`  plik. Pamiętaj, że musisz zaktualizować w `mainClassName` zależności od przykładu:

  ```java
  plugins {
    java
    application
  }
  application {
    mainClassName = "{NAME OF YOUR CLASS}"
  }
  repositories {
    mavenCentral()
  }
  dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
  }
  ```

* Utwórz plik Java w katalogu **Java** i skopiuj/Wklej kod z podanego przykładu. Nie zapomnij dodać klucza subskrypcji i punktu końcowego.

* **Kompiluj i uruchamiaj przykład z katalogu głównego**:

```powershell
gradle build
gradle run
```

### <a name="go"></a>[Przejdź](#tab/go)  

* Utwórz nowy projekt przejdź do.
* Dodaj kod przedstawiony poniżej.
* Ustaw wartości w punktach końcowych, klucz subskrypcji i adres URL kontenera.
* Zapisz plik z rozszerzeniem „go”.
* Otwórz wiersz polecenia na komputerze, na którym zainstalowano środowisko języka Go.
* Skompiluj plik, na przykład: "przejdź do przykładu kompilacji — Code. go".
* Uruchom plik, na przykład: "przykładowy kod".

 ---

## <a name="make-document-translation-requests"></a>Utwórz żądania tłumaczenia dokumentu

Żądanie tłumaczenia dokumentu wsadowego jest przesyłane do punktu końcowego usługi translatora za pośrednictwem żądania POST. Jeśli to się powiedzie, Metoda POST zwraca `202 Accepted`  kod odpowiedzi, a żądanie wsadowe jest tworzone przez usługę.

### <a name="http-headers"></a>Nagłówki HTTP

Następujące nagłówki są dołączone do każdego żądania interfejsu API usługi tłumaczenia dokumentów:

|Nagłówek HTTP|Opis|
|---|--|
|Ocp-Apim-Subscription-Key|**Wymagane**: wartość jest kluczem subskrypcji platformy Azure dla translatora lub Cognitive Servicesgo zasobu.|
|Content-Type|**Wymagane**: określa typ zawartości ładunku. Akceptowane wartości to Application/JSON lub charset = UTF-8.|
|Długość zawartości|**Wymagane**: długość treści żądania.|

### <a name="post-request-body-properties"></a>Właściwości treści żądania POST

* Treść żądania POST jest obiektem JSON o nazwie `inputs` .
* `inputs`Obiekt zawiera zarówno `sourceURL` adres, jak i `targetURL` adresy kontenerów dla źródłowej i docelowej pary językowej i opcjonalnie może zawierać `glossaryURL` adres kontenera.
* `prefix`Pola i `suffix` (opcjonalne) są używane do filtrowania dokumentów w kontenerze, w tym folderów.
* Wartość  `glossaries`  pola (opcjonalnie) jest stosowana, gdy dokument jest tłumaczony.
* `targetUrl`Każdy język docelowy musi być unikatowy.

>[!NOTE]
> Jeśli plik o tej samej nazwie już istnieje w miejscu docelowym, zostanie zastąpiony.

## <a name="post-a-translation-request"></a>Opublikuj żądanie tłumaczenia

<!-- markdownlint-disable MD024 -->
### <a name="post-request-body-without-optional-glossaryurl"></a>Opublikuj treść żądania bez opcjonalnej glossaryURL

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "<https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS>",
                "storageSource": "AzureBlob",
                "filter": {
                    "prefix": "News",
                    "suffix": ".txt"
                },
                "language": "en"
            },
            "targets": [
                {
                    "targetUrl": "<https://YOUR-SOURCE-URL-WITH-WRITE-LIST-ACCESS-SAS>",
                    "storageSource": "AzureBlob",
                    "category": "general",
                    "language": "de"
                }
            ]
        }
    ]
}
```

### <a name="post-request-body-with-optional-glossaryurl"></a>Opublikuj treść żądania z opcjonalną glossaryURL

```json
{
  "inputs":[
    {
      "source":{
        "sourceUrl":"<https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS>",
        "storageSource":"AzureBlob",
        "filter":{
          "prefix":"News",
          "suffix":".txt"
        },
        "language":"en"
      },
      "targets":[
        {
          "targetUrl":"<https://YOUR-SOURCE-URL-WITH-WRITE-LIST-ACCESS-SAS>",
          "storageSource":"AzureBlob",
          "category":"general",
          "language":"de",
          "glossaries":[
            {
              "glossaryUrl":"<https://YOUR-GLOSSARY-URL-WITH-READ-LIST-ACCESS-SAS>",
              "format":"xliff",
              "version":"1.2"
            }
          ]
        }
      ]
    }
  ]
}
```

> [!IMPORTANT]
>
> W przypadku przykładów kodu poniżej może być konieczne zaktualizowanie następujących pól w zależności od operacji:
>>>
>> * `endpoint`
>> * `subscriptionKey`
>> * `sourceURL`
>> * `targetURL`
>> * `glossaryURL`
>> * `id`  (identyfikator zadania)
>>
> Gdzie można znaleźć `id` wartość:
> * Zadanie można znaleźć `id`  w adresie URL nagłówka odpowiedzi metody post `Operation-Location`  . Ostatni parametr adresu URL to zadanie operacji **`id`** .  
> * Możesz również użyć żądania GET Jobs, aby pobrać zadanie `id`  dla operacji tłumaczenia dokumentu.
>
> Poniżej przedstawiono kod i punkt końcowy w miejscu, w którym można znaleźć przykłady kodu. Pamiętaj, aby usunąć klucz z kodu, gdy skończysz, i nigdy nie Publikuj go publicznie.  
>
> Zapoznaj się z artykułem [Azure Cognitive Services Security](/azure/cognitive-services/cognitive-services-security?tabs=command-line%2Ccsharp) , aby poznać sposoby bezpiecznego przechowywania poświadczeń i uzyskiwania do nich dostępu.

## <a name="_post-document-translation_-request"></a>Żądanie _tłumaczenia po dokumencie_

Prześlij żądanie tłumaczenia dokumentu wsadowego do usługi tłumaczenia.

### <a name="c"></a>[C#](#tab/csharp)

```csharp

    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Text;
    

    class Program
    {

        static readonly string route = "/batches";

        private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

        private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

        static readonly string json = ("{\"inputs\": [{\"source\": {\"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",\"storageSource\": \"AzureBlob\",\"language\": \"en\",\"filter\":{\"prefix\": \"Demo_1/\"} }, \"targets\": [{\"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\"storageSource\": \"AzureBlob\",\"category\": \"general\",\"language\": \"es\"}]}]}");
        
        static async Task Main(string[] args)
        {
            using HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
            
                StringContent content = new StringContent(json, Encoding.UTF8, "application/json");

                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                request.Content = content;
                
                HttpResponseMessage  response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;
                if (response.IsSuccessStatusCode)
                {
                    Console.WriteLine($"Status code: {response.StatusCode}");
                    Console.WriteLine();
                    Console.WriteLine($"Response Headers:"); 
                    Console.WriteLine(response.Headers);
                }
                else
                    Console.Write("Error");

            }

        }

    }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios').default;

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let route = '/batches';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';

let data = JSON.stringify({"inputs": [
  {
      "source": {
          "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
          "storageSource": "AzureBlob",
          "language": "en",
          "filter":{
              "prefix": "Demo_1/"
          }
      },
      "targets": [
          {
              "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
              "storageSource": "AzureBlob",
              "category": "general",
              "language": "es"}]}]});

let config = {
  method: 'post',
  baseURL: endpoint,
  url: route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-Type': 'application/json'
  },
  data: data
};

axios(config)
.then(function (response) {
  let result = { statusText: response.statusText, statusCode: response.status, headers: response.headers };
  console.log()
  console.log(JSON.stringify(result));
})
.catch(function (error) {
  console.log(error);
});
```

### <a name="python"></a>[Python](#tab/python)

```python

import requests

endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
path = '/batches'
constructed_url = endpoint + path

payload= {
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
                "storageSource": "AzureBlob",
                "language": "en",
                "filter":{
                    "prefix": "Demo_1/"
                }
            },
            "targets": [
                {
                    "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
                    "storageSource": "AzureBlob",
                    "category": "general",
                    "language": "es"
                }
            ]
        }
    ]
}
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey,
  'Content-Type': 'application/json'
}

response = requests.post(constructed_url, headers=headers, json=payload)

print(f'response status code: {response.status_code}\nresponse status: {response.reason}\nresponse headers: {response.headers}')
```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DocumentTranslation {
    String subscriptionKey = "'<YOUR-SUBSCRIPTION-KEY>'";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String path = endpoint + "/batches";

    OkHttpClient client = new OkHttpClient();

    public void post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,  "{\n \"inputs\": [\n {\n \"source\": {\n \"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS\",\n \"filter\": {\n  \"prefix\": \"Demo_1\"\n  },\n  \"language\": \"en\",\n \"storageSource\": \"AzureBlob\"\n  },\n \"targets\": [\n {\n \"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\n \"category\": \"general\",\n\"language\": \"fr\",\n\"storageSource\": \"AzureBlob\"\n }\n ],\n \"storageType\": \"Folder\"\n }\n  ]\n}");
        Request request = new Request.Builder()
                .url(path).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        System.out.println(response.code());
        System.out.println(response.headers());
    }

  public static void main(String[] args) {
        try {
            DocumentTranslation sampleRequest = new DocumentTranslation();
            sampleRequest.post();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

### <a name="go"></a>[Przejdź](#tab/go)

```go

package main

import (
 "bytes"
 "fmt"
"net/http"
)

func main() {
endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
uri := endpoint + "/batches"
method := "POST"

var jsonStr = []byte(`{"inputs":[{"source":{"sourceUrl":"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS","storageSource":"AzureBlob","language":"en","filter":{"prefix":"Demo_1/"}},"targets":[{"targetUrl":"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS","storageSource":"AzureBlob","category":"general","language":"es"}]}]}`)

req, err := http.NewRequest(method, endpoint, bytes.NewBuffer(jsonStr))
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

client := &http.Client{}

if err != nil {
    fmt.Println(err)
    return
  }
  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()
  fmt.Println("response status:",  res.Status)
  fmt.Println("response headers",  res.Header)
}
```

---

## <a name="_get-file-formats_"></a>_Pobierz formaty plików_ 

Pobierz listę obsługiwanych formatów plików. Jeśli to się powiedzie, metoda zwraca `200 OK` kod odpowiedzi.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/documents/formats";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/documents/formats';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetFileFormats {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/documents/formats";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/documents/formats'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Przejdź](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/documents/formats"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_get-job-status_"></a>_Pobierz stan zadania_ 

Pobierz bieżący stan pojedynczego zadania i podsumowanie wszystkich zadań w żądaniu tłumaczenia dokumentu. Jeśli to się powiedzie, metoda zwraca `200 OK` kod odpowiedzi.
<!-- markdownlint-disable MD024 -->

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetJobStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Przejdź](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_get-document-status_"></a>_Pobierz stan dokumentu_

### <a name="brief-overview"></a>Krótkie omówienie

Pobierz stan określonego dokumentu w żądaniu tłumaczenia dokumentu. Jeśli to się powiedzie, metoda zwraca `200 OK` kod odpowiedzi.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/{id}/document/{documentId}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/{id}/document/{documentId}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetDocumentStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/{id}/document/{documentId}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/{id}/document/{documentId}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Przejdź](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/{id}/document/{documentId}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_delete-job_"></a>_Usuń zadanie_ 

### <a name="brief-overview"></a>Krótkie omówienie

Anuluj aktualnie przetwarzane lub w kolejce zadanie. Anulowane zostaną tylko dokumenty, dla których tłumaczenie nie zostało rozpoczęte.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Delete;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'delete',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DeleteJob {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("DELETE", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("DELETE", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Przejdź](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "DELETE"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="content-limits"></a>Limity zawartości

W poniższej tabeli wymieniono limity danych wysyłanych do tłumaczenia dokumentu.

|Atrybut | Limit|
|---|---|
|Rozmiar dokumentu| ≤ 40 MB |
|Łączna liczba plików.|≤ 1000 |
|Łączny rozmiar zawartości w partii | ≤ 250 MB|
|Liczba języków docelowych w partii| ≤ 10 |
|Rozmiar pliku pamięci translacji| ≤ 10 MB|

> [!NOTE]
> Powyższe limity zawartości mogą ulec zmianie przed wydaniem publicznym.

## <a name="learn-more"></a>Więcej tutaj

* [Dokumentacja interfejsu API usługi Translator v3](../reference/v3-0-reference.md)
* [Obsługa języków](../language-support.md)
* [Subskrypcje na platformie Azure API Management](../../../api-management/api-management-subscriptions.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie dostosowanego systemu języka przy użyciu translatora niestandardowego](../custom-translator/overview.md)
>
>