---
title: 'Szybki Start: Tworzenie indeksu wyszukiwania w programie PowerShell przy użyciu interfejsów API REST'
titleSuffix: Azure Cognitive Search
description: W tym przewodniku szybki start interfejsu API REST dowiesz się, jak utworzyć indeks, załadować dane i uruchamiać zapytania przy użyciu Invoke-RestMethod programu PowerShell i interfejsu API REST platformy Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 08/17/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 476a2cf0d5886ffa677461a5a8156cdfa4a168a3
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701783"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-powershell-using-rest-apis"></a>Szybki Start: Tworzenie indeksu Wyszukiwanie poznawcze platformy Azure w programie PowerShell przy użyciu interfejsów API REST
> [!div class="op_single_selector"]
> * [PowerShell (REST)]()
> * [C#](./search-get-started-dotnet.md)
> * [REST](search-get-started-rest.md)
> * [Python](search-get-started-python.md)
> * [Portal](search-get-started-portal.md)
> 

W tym artykule omówiono proces tworzenia, ładowania i wykonywania zapytań dotyczących indeksu Wyszukiwanie poznawcze platformy Azure przy użyciu programu PowerShell i [interfejsów API REST platformy azure wyszukiwanie poznawcze](/rest/api/searchservice/). W tym artykule wyjaśniono, jak uruchomić polecenia programu PowerShell interaktywnie. Alternatywnie można [pobrać i uruchomić skrypt programu PowerShell](https://github.com/Azure-Samples/azure-search-powershell-samples/tree/master/Quickstart) , który wykonuje te same operacje.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start wymaga następujących usług i narzędzi. 

+ Program [PowerShell 5,1 lub nowszy](https://github.com/PowerShell/PowerShell)przy użyciu polecenia [Invoke-RestMethod](/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) dla kroków sekwencyjnych i interaktywnych.

+ [Utwórz usługę Azure wyszukiwanie poznawcze](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. Możesz użyć bezpłatnej usługi dla tego przewodnika Szybki Start. 

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona razem z usługą, więc jeśli do subskrypcji dodano Wyszukiwanie poznawcze platformy Azure, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Uzyskaj adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

2. W obszarze **Ustawienia**  >  **klucze** Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

![Pobieranie punktu końcowego HTTP i klucza dostępu](media/search-get-started-rest/get-url-key.png "Pobieranie punktu końcowego HTTP i klucza dostępu")

Wszystkie żądania wymagają klucza API dla każdego żądania wysyłanego do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="connect-to-azure-cognitive-search"></a>Nawiązywanie połączenia z usługą Azure Wyszukiwanie poznawcze

1. W programie PowerShell Utwórz obiekt **$Headers** , aby przechowywać typ zawartości i klucz interfejsu API. Zastąp klucz interfejsu API administratora (klucz-administrator-API-KEY) kluczem prawidłowym dla usługi wyszukiwania. Wystarczy ustawić ten nagłówek tylko raz na czas trwania sesji, ale zostanie on dodany do każdego żądania. 

    ```powershell
    $headers = @{
    'api-key' = '<YOUR-ADMIN-API-KEY>'
    'Content-Type' = 'application/json' 
    'Accept' = 'application/json' }
    ```

2. Utwórz obiekt **$URL** , który określa kolekcję indeksów usługi. Zastąp nazwę usługi (nazwa użytkownika-SEARCH-SERVICE-NAME) poprawną usługą wyszukiwania.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2020-06-30&$select=name"
    ```

3. Uruchom **Invoke-RestMethod** , aby wysłać żądanie Get do usługi i zweryfikować połączenie. Dodaj **ConvertTo-JSON** , aby wyświetlić odpowiedzi wysyłane z powrotem z usługi.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

   Jeśli usługa jest pusta i nie ma żadnych indeksów, wyniki są podobne do poniższego przykładu. W przeciwnym razie zobaczysz reprezentację definicji indeksu w formacie JSON.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
        "value":  [

                ]
    }
    ```

## <a name="1---create-an-index"></a>1 — Tworzenie indeksu

Jeśli nie korzystasz z portalu, musi istnieć indeks usługi, aby można było załadować dane. Ten krok umożliwia zdefiniowanie indeksu i wypchnięcie go do usługi. Dla tego kroku jest używany [interfejs API Rest tworzenia indeksu](/rest/api/searchservice/create-index) .

Wymagane elementy indeksu obejmują nazwę i kolekcję pól. Kolekcja Fields definiuje strukturę *dokumentu*. Każde pole ma nazwę, typ i atrybuty, które określają, w jaki sposób jest używany (na przykład czy umożliwia wyszukiwanie pełnotekstowe, filtrowanie lub pobieranie w wynikach wyszukiwania). W indeksie należy wyznaczyć jedno z pól typu `Edm.String` jako *klucz* dla tożsamości dokumentu.

Ten indeks ma nazwę "Hotele-Szybki Start" i zawiera definicje pól widoczne poniżej. Jest to podzestaw większego [indeksu hoteli](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) użyty w innych artykułach. Definicje pól zostały obcięte w tym przewodniku szybki start dla zwięzłości.

1. Wklej ten przykład do programu PowerShell, aby utworzyć obiekt **$Body** zawierający schemat indeksu.

    ```powershell
    $body = @"
    {
        "name": "hotels-quickstart",  
        "fields": [
            {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
            {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
            {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
            {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
            {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
            {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Address", "type": "Edm.ComplexType", 
            "fields": [
            {"name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true},
            {"name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true}
            ]
         }
      ]
    }
    "@
    ```

2. Ustaw identyfikator URI dla kolekcji indeksów w usłudze i w indeksie *hoteli — szybki start* .

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart?api-version=2020-06-30"
    ```

3. Aby utworzyć indeks usługi, uruchom polecenie z **$URL**, **$Headers** i **$Body** . 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    Wyniki powinny wyglądać podobnie do tego (obcięto do pierwszych dwóch pól dla zwięzłości):

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag":  "\"0x8D6EDE28CFEABDA\"",
        "name":  "hotels-quickstart",
        "defaultScoringProfile":  null,
        "fields":  [
                    {
                        "name":  "HotelId",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  true,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  true,
                        "key":  true,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
                    {
                        "name":  "HotelName",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  false,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  false,
                        "key":  false,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
    . . .
    ```

> [!Tip]
> Do weryfikacji można także sprawdzić listę indeksów w portalu.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 — ładowanie dokumentów

Aby wypchnąć dokumenty, użyj żądania HTTP POST do punktu końcowego adresu URL Twojego indeksu. Interfejs API REST dla tego zadania to [Dodawanie, aktualizowanie lub usuwanie dokumentów](/rest/api/searchservice/addupdate-or-delete-documents).

1. Wklej ten przykład do programu PowerShell, aby utworzyć obiekt **$Body** zawierający dokumenty, które chcesz przekazać. 

    To żądanie zawiera dwa pełne i jeden rekord częściowy. Rekord częściowy pokazuje, że można przekazać niekompletne dokumenty. `@search.action`Parametr określa sposób indeksowania. Prawidłowe wartości to upload, Merge, mergeOrUpload i DELETE. Zachowanie mergeOrUpload tworzy nowy dokument dla hotelId = 3 lub aktualizuje zawartość, jeśli już istnieje.

    ```powershell
    $body = @"
    {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": 
            {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": 
            {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    "@
    ```

1. Ustaw punkt końcowy w kolekcji *hoteli-szybkiego startu* i Uwzględnij operację indeksu (indeksy/Hotele — szybki start/docs/indeks).

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2020-06-30"
    ```

1. Uruchom polecenie z **$URL**, **$Headers** i **$Body** , aby załadować dokumenty do indeksu hoteli-szybkiego startu.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
    ```
    Wyniki powinny wyglądać podobnie do poniższego przykładu. Powinien zostać wyświetlony [kod stanu 201](/rest/api/searchservice/HTTP-status-codes).

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2019_05_06.IndexResult)",
        "value":  [
                    {
                        "key":  "1",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "2",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "3",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "4",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    }
                ]
    }
    ```

## <a name="3---search-an-index"></a>3 — Przeszukiwanie indeksu

W tym kroku przedstawiono sposób wykonywania zapytań względem indeksu przy użyciu [interfejsu API dokumentów wyszukiwania](/rest/api/searchservice/search-documents).

Upewnij się, że w $urls wyszukiwania są używane pojedyncze cudzysłowy. Ciągi zapytań zawierają **$** znaki i można pominąć ich wyprowadzanie, jeśli cały ciąg jest ujęty w apostrofy.

1. Ustaw punkt końcowy do kolekcji *hoteli-szybkiego startu* dokumentów i Dodaj parametr **wyszukiwania** do przekazania w ciągu zapytania. 
  
   Ten ciąg wykonuje puste wyszukiwanie (Search = *), zwracając niesklasyfikowaną listę (wynik wyszukiwania = 1,0) dowolnych dokumentów. Domyślnie usługa Azure Wyszukiwanie poznawcze zwraca 50 dopasowań w danym momencie. Zgodnie ze strukturą, to zapytanie zwraca całą strukturę dokumentu i jego wartości. Dodaj **$Count = true** , aby uzyskać liczbę wszystkich dokumentów w wynikach.

    ```powershell
    $url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=*&$count=true'
    ```

1. Uruchom polecenie, aby wysłać **$URL** do usługi.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

    Wyniki powinny wyglądać podobnie do poniższych danych wyjściowych.

    ```
    {
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#docs(*)",
    "@odata.count":  4,
    "value":  [
                  {
                      "@search.score":  0.1547872,
                      "HotelId":  "2",
                      "HotelName":  "Twin Dome Motel",
                      "Description":  "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                      "Category":  "Boutique",
                      "Tags":  "pool free wifi concierge",
                      "ParkingIncluded":  false,
                      "LastRenovationDate":  "1979-02-18T00:00:00Z",
                      "Rating":  3.6,
                      "Address":  "@{StreetAddress=140 University Town Center Dr; City=Sarasota; StateProvince=FL; PostalCode=34243; Country=USA}"
                  },
                  {
                      "@search.score":  0.009068266,
                      "HotelId":  "3",
                      "HotelName":  "Triple Landscape Hotel",
                      "Description":  "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel\u0027s restaurant services.",
                      "Category":  "Resort and Spa",
                      "Tags":  "air conditioning bar continental breakfast",
                      "ParkingIncluded":  true,
                      "LastRenovationDate":  "2015-09-20T00:00:00Z",
                      "Rating":  4.8,
                      "Address":  "@{StreetAddress=3393 Peachtree Rd; City=Atlanta; StateProvince=GA; PostalCode=30326; Country=USA}"
                  },
                . . . 
    ```

Wypróbuj kilka innych przykładów zapytania, aby uzyskać działanie dla składni. Możesz wykonać wyszukiwanie ciągów, Verbatim $filter zapytań, ograniczyć zestaw wyników, określić zakres wyszukiwania do określonych pól itd.

```powershell
# Query example 1
# Search the entire index for the terms 'restaurant' and 'wifi'
# Return only the HotelName, Description, and Tags fields
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=restaurant wifi&$count=true&$select=HotelName,Description,Tags'

# Query example 2 
# Apply a filter to the index to find hotels rated 4 or higher
# Returns the HotelName and Rating. Two documents match.
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=*&$filter=Rating gt 4&$select=HotelName,Rating'

# Query example 3
# Take the top two results, and show only HotelName and Category in the results
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=boutique&$top=2&$select=HotelName,Category'

# Query example 4
# Sort by a specific field (Address/City) in ascending order

$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating'
```
## <a name="clean-up-resources"></a>Czyszczenie zasobów

W przypadku pracy w ramach własnej subskrypcji warto sprawdzić po zakończeniu projektu, czy dalej potrzebuje się utworzonych zasobów. Uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit. 

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto programu PowerShell do przechodzenia przez podstawowy przepływ pracy na potrzeby tworzenia i uzyskiwania dostępu do zawartości na platformie Azure Wyszukiwanie poznawcze. Mając na uwadze koncepcje, zalecamy przechodzenie do bardziej zaawansowanych scenariuszy, takich jak indeksowanie ze źródeł danych platformy Azure;

> [!div class="nextstepaction"]
> [Samouczek REST: indeksowanie i wyszukiwanie danych częściowo strukturalnych (obiektów BLOB JSON) na platformie Azure Wyszukiwanie poznawcze](search-semi-structured-data.md)
