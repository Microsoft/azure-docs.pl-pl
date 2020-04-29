---
title: 'Szybki Start: uzyskiwanie Aktualności przy użyciu wyszukiwanie wiadomości Bing interfejsu API REST i języka go'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik Szybki Start używa języka go do wywoływania interfejs API wyszukiwania wiadomości Bing. Wyniki obejmują nazwy i adresy URL źródeł wiadomości identyfikowanych przez ciąg zapytania.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: aaeb905c9cdc1e7b74e21d3c191f6a24a94fcd7d
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80053809"
---
# <a name="quickstart-get-news-results-using-the-bing-news-search-rest-api-and-go"></a>Szybki Start: uzyskiwanie Aktualności wyników przy użyciu interfejsu API REST wyszukiwanie wiadomości Bing i języka go

Ten przewodnik Szybki Start używa języka go do wywoływania interfejs API wyszukiwania wiadomości Bing. Wyniki obejmują nazwy i adresy URL źródeł wiadomości identyfikowanych przez ciąg zapytania.

## <a name="prerequisites"></a>Wymagania wstępne
* Instalowanie [plików binarnych języka go](https://golang.org/dl/)
* Zainstaluj bibliotekę przejdź do Spew, aby wyświetlić wyniki
    * Zainstaluj tę bibliotekę:`$ go get -u https://github.com/davecgh/go-spew`

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-a-project-and-import-libraries"></a>Tworzenie projektu i importowanie bibliotek

Utwórz nowy projekt przejdź do środowiska IDE lub edytora. Następnie należy `net/http` zaimportować żądania, `ioutil` odczytać odpowiedź i `encoding/json` obsłużyć tekst JSON wyników. Biblioteka go-Spew jest wymagana do analizowania kodu JSON. 

```go
package main

import (
    "fmt"
    "net/http"
    "io/ioutil"
    "encoding/json"
    "github.com/davecgh/go-spew/spew"
)

```

## <a name="create-a-struct-to-format-the-news-search-results"></a>Utwórz strukturę służącą do formatowania wyników wyszukiwania wiadomości

Struktura `NewsAnswer` formatuje dane zawarte w odpowiedzi. KOD JSON odpowiedzi jest wielopoziomowy i dość skomplikowany.  W poniższej implementacji przedstawiono podstawowe informacje dotyczące programu.

```go
// This struct formats the answer provided by the Bing News Search API.
type NewsAnswer struct {
    ReadLink       string `json: "readLink"` 
    QueryContext   struct {
        OriginalQuery   string   `json: "originalQuery"`
        AdultIntent     bool        `json: "adultIntent"`
    } `json: "queryContext"`
    TotalEstimatedMatches   int  `json: totalEstimatedMatches"` 
    Sort  []struct {
        Name    string  `json: "name"`
        ID       string    `json: "id"`
        IsSelected       bool  `json: "isSelected"`
        URL      string   `json: "url"`
    }  `json: "sort"` 
    Value   []struct   {
        Name     string   `json: "name"`
        URL   string    `json: "url"`
        Image   struct   {
            Thumbnail   struct  {
                ContentUrl  string  `json: "thumbnail"`
                Width   int  `json: "width"`
                Height  int   `json: "height"`
            } `json: "thumbnail"` 
            } `json: "image"` 
            Description  string  `json: "description"`
            Provider  []struct   {
                Type   string    `json: "_type"`
                Name  string     `json: "name"`
            } `json: "provider"` 
            DatePublished   string   `json: "datePublished"`
    } `json: "value"` 
}

```

## <a name="declare-the-main-function-and-define-variables"></a>Deklarowanie funkcji main i definiowanie zmiennych  

Poniższy kod deklaruje główną funkcję i przypisuje wymagane zmienne. Upewnij się, że punkt końcowy jest poprawny, i zamień wartość `token` na odpowiedni klucz subskrypcji ze swojego konta platformy Azure. Możesz użyć poniższego globalnego punktu końcowego lub niestandardowego punktu końcowego [poddomeny](../../cognitive-services/cognitive-services-custom-subdomains.md) , który jest wyświetlany w Azure Portal dla zasobu.

```go
func main() {
    // Verify the endpoint URI and replace the token string with a valid subscription key.  
    const endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
    token := "YOUR-ACCESS-KEY"
    searchTerm := "Microsoft Cognitive Services"

    // Declare a new GET request.
    req, err := http.NewRequest("GET", endpoint, nil)
    if err != nil {
        panic(err)
    }

    // The rest of the code in this example goes here in the main function.
}
```

## <a name="query-and-header"></a>Zapytanie i nagłówek

Dodawanie ciągu zapytania i nagłówka klucza dostępu

```go
// Add the query to the request.  
param := req.URL.Query()
param.Add("q", searchTerm)
req.URL.RawQuery = param.Encode()

// Insert the subscription-key header.  
req.Header.Add("Ocp-Apim-Subscription-Key", token)

```

## <a name="get-request"></a>Pobierz żądanie

Utwórz klienta i Wyślij żądanie Get. 

```go
// Instantiate a client.  
client := new(http.Client)

// Send the request to Bing.  
resp, err := client.Do(req)
if err != nil {
    panic(err)
}

```

## <a name="send-the-request"></a>Wysyłanie żądania

Wyślij żądanie i przeczytaj wyniki przy użyciu `ioutil`polecenia.

```go
resp, err := client.Do(req)
    if err != nil {
        panic(err)
}

// Close the connection.    
defer resp.Body.Close()

// Read the results
body, err := ioutil.ReadAll(resp.Body)
if err != nil {
    panic(err)
}

```

## <a name="handle-the-response"></a>Obsługa odpowiedzi

`Unmarshall` Funkcja wyodrębnia informacje z tekstu JSON zwróconego przez interfejs API wyszukiwanie wiadomości.  Następnie można wyświetlić węzły z wyników przy użyciu `go-spew` całkiem drukarki.

```go
// Create a new answer object 
ans := new(NewsAnswer)
err = json.Unmarshal(body, &ans)
if err != nil {
    fmt.Println(err)
}
    
fmt.Print("Output of BingAnswer: \r\n\r\n")
    
// Iterate over search results and print the result name and URL.
for _, result := range ans.Value{
spew.Dump(result.Name, result.URL)
}

```

## <a name="results"></a>Wyniki

Wyniki zawierają nazwę i adres URL każdego wyniku.

```
(string) (len=91) "Cognitive Services Market: Global Industry Analysis and Opportunity Assessment, 2019 - 2025"
(string) (len=142) "https://www.marketwatch.com/press-release/cognitive-services-market-global-industry-analysis-and-opportunity-assessment-2019---2025-2019-02-21"
(string) (len=104) "Microsoft calls for greater collaboration to harness the power of AI to empower people with disabilities"
(string) (len=115) "https://indiaeducationdiary.in/microsoft-calls-greater-collaboration-harness-power-ai-empower-people-disabilities-2/"
(string) (len=70) "Microsoft 'Intelligent Cloud Hub' to build AI-ready workforce in India"
(string) (len=139) "https://cio.economictimes.indiatimes.com/news/cloud-computing/microsoft-intelligent-cloud-hub-to-build-ai-ready-workforce-in-india/67187807"
(string) (len=81) "Skills shortage is stopping many Asian companies from embracing A.I., study shows"
(string) (len=106) "https://www.cnbc.com/2019/02/20/microsoft-idc-study-skills-shortages-stopping-companies-from-using-ai.html"
(string) (len=143) "Cognitive Computing in Healthcare Market Emerging Top Key Vendors- Apixio, MedWhat, Healthcare X.0, Apple, Google, Microsoft, and IBM 2017-2025"
(string) (len=40) "http://www.digitaljournal.com/pr/4163064"
(string) (len=49) "Microsoft launches AI skills initiative in Brazil"
(string) (len=80) "https://www.zdnet.com/article/microsoft-launches-ai-skills-initiative-in-brazil/"
(string) (len=45) "Kuwait's CITRA and Microsoft host AI OpenHack"
(string) (len=70) "http://www.itp.net/618639-kuwaits-citra-and-microsoft-host-ai-openhack"
(string) (len=51) "CITRA and Microsoft collaborate to host AI workshop"
(string) (len=123) "https://www.zawya.com/mena/en/press-releases/story/CITRA_and_Microsoft_collaborate_to_host_AI_workshop-ZAWYA20190212105751/"

```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Co to jest wyszukiwanie wiadomości Bing](search-the-web.md)
