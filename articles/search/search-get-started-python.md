---
title: 'Szybki Start: Tworzenie indeksu wyszukiwania w języku Python przy użyciu interfejsów API REST'
titleSuffix: Azure Cognitive Search
description: Wyjaśnia, jak utworzyć indeks, załadować dane i uruchamiać zapytania przy użyciu języka Python, notesów Jupyter oraz interfejsu API REST platformy Azure Wyszukiwanie poznawcze.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 08/20/2020
ms.custom: devx-track-python
ms.openlocfilehash: e4141bc4887a166876d1fc4590b73f382abd0b95
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88936677"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebooks"></a>Szybki Start: Tworzenie indeksu Wyszukiwanie poznawcze platformy Azure w języku Python przy użyciu notesów Jupyter

> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](./search-get-started-powershell.md)
> * [C#](./search-get-started-dotnet.md)
> * [Poster (REST)](search-get-started-postman.md)
> * [Portal](search-get-started-portal.md)
> 

Tworzenie notesu Jupyter, który tworzy, ładuje i bada indeks Wyszukiwanie poznawcze platformy Azure przy użyciu języka Python i [interfejsów API REST platformy azure wyszukiwanie poznawcze](/rest/api/searchservice/). W tym artykule opisano sposób tworzenia notesu krok po kroku. Alternatywnie można [pobrać i uruchomić gotowy Notes Python Jupyter](https://github.com/Azure-Samples/azure-search-python-samples).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start wymaga następujących usług i narzędzi. 

+ [Anaconda 3. x](https://www.anaconda.com/distribution/#download-section), dostarczając notesy języka Python 3. x i Jupyter.

+ [Utwórz usługę Azure wyszukiwanie poznawcze](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. W tym przewodniku Szybki Start możesz skorzystać z warstwy Bezpłatna. 

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona razem z usługą, więc jeśli do subskrypcji dodano Wyszukiwanie poznawcze platformy Azure, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Uzyskaj adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W obszarze **Ustawienia**  >  **klucze**Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

![Pobieranie punktu końcowego HTTP i klucza dostępu](media/search-get-started-postman/get-url-key.png "Pobieranie punktu końcowego HTTP i klucza dostępu")

Wszystkie żądania wymagają klucza API dla każdego żądania wysyłanego do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="connect-to-azure-cognitive-search"></a>Nawiązywanie połączenia z usługą Azure Wyszukiwanie poznawcze

W tym zadaniu Uruchom Notes Jupyter i sprawdź, czy możesz nawiązać połączenie z usługą Azure Wyszukiwanie poznawcze. W tym celu należy zażądać listy indeksów z usługi. W systemie Windows z Anaconda3 można użyć nawigatora Anaconda do uruchomienia notesu.

1. Utwórz nowy Notes python3.

1. W pierwszej komórce Załaduj biblioteki używane do pracy z formatem JSON i formułowania żądań HTTP.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. W drugiej komórce wprowadź elementy żądania, które będą stałe dla każdego żądania. Zastąp wartość w polu Nazwa usługi wyszukiwania (nazwa usługi-SEARCH-SERVICE-NAME) i klucz interfejsu API administratora (administrator-administrator-klucz) z prawidłowymi wartościami. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2020-06-30'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

   W przypadku uzyskania ConnectionError `"Failed to establish a new connection"` upewnij się, że klucz API-Key jest podstawowym lub pomocniczym kluczem administratora i że wszystkie znaki wiodące i końcowe `?` `/` są używane.

1. W trzeciej komórce należy sformułować żądanie. To żądanie GET odwołuje się do kolekcji indeksów usługi wyszukiwania i wybiera Właściwość Name istniejących indeksów.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Uruchom każdy krok. Jeśli istnieją indeksy, odpowiedź zawiera listę nazw indeksów. Na poniższym zrzucie ekranu usługa ma już indeks azureblob i realestate-US-Sample.

   ![Skrypt języka Python w notesie Jupyter z żądaniami HTTP do usługi Azure Wyszukiwanie poznawcze](media/search-get-started-python/connect-azure-search.png "Skrypt języka Python w notesie Jupyter z żądaniami HTTP do usługi Azure Wyszukiwanie poznawcze")

   W przeciwieństwie do pustej kolekcji indeksów zwracana jest odpowiedź: `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 — Tworzenie indeksu

Jeśli nie korzystasz z portalu, musi istnieć indeks usługi, aby można było załadować dane. Ten krok powoduje użycie [interfejsu API Rest tworzenia indeksu](/rest/api/searchservice/create-index) w celu wypchnięcia schematu indeksu do usługi.

Wymagane elementy indeksu obejmują nazwę, kolekcję pól i klucz. Kolekcja Fields definiuje strukturę *dokumentu*. Każde pole ma nazwę, typ i atrybuty, które określają sposób użycia pola (na przykład czy jest to możliwość wyszukiwania pełnotekstowego, filtrowania lub pobierania w wynikach wyszukiwania). W indeksie należy wyznaczyć jedno z pól typu `Edm.String` jako *klucz* dla tożsamości dokumentu.

Ten indeks ma nazwę "Hotele-Szybki Start" i zawiera definicje pól widoczne poniżej. Jest to podzestaw większego [indeksu hoteli](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) używany w innych przewodnikach. Ten przewodnik Szybki Start został przez nas przycięty do zwięzłości.

1. W następnej komórce wklej poniższy przykład do komórki, aby udostępnić schemat. 

    ```python
    index_schema = {
       "name": "hotels-quickstart",  
       "fields": [
         {"name": "HotelId", "type": "Edm.String", "key": "true", "filterable": "true"},
         {"name": "HotelName", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "true", "facetable": "false"},
         {"name": "Description", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "en.lucene"},
         {"name": "Description_fr", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "fr.lucene"},
         {"name": "Category", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Tags", "type": "Collection(Edm.String)", "searchable": "true", "filterable": "true", "sortable": "false", "facetable": "true"},
         {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Rating", "type": "Edm.Double", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Address", "type": "Edm.ComplexType", 
         "fields": [
         {"name": "StreetAddress", "type": "Edm.String", "filterable": "false", "sortable": "false", "facetable": "false", "searchable": "true"},
         {"name": "City", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "StateProvince", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "PostalCode", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Country", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"}
        ]
       }
      ]
    }
    ```

2. W innej komórce należy sformułować żądanie. To żądanie POST odwołuje się do kolekcji indeksów usługi wyszukiwania i tworzy indeks na podstawie schematu indeksu podanego w poprzedniej komórce.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Uruchom każdy krok.

   Odpowiedź obejmuje reprezentację schematu w formacie JSON. Poniższy zrzut ekranu przedstawia tylko część odpowiedzi.

    ![Żądanie utworzenia indeksu](media/search-get-started-python/create-index.png "Żądanie utworzenia indeksu")

> [!Tip]
> Innym sposobem sprawdzenia tworzenia indeksu jest sprawdzenie listy indeksów w portalu.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 — ładowanie dokumentów

Aby wypchnąć dokumenty, użyj żądania HTTP POST do punktu końcowego adresu URL Twojego indeksu. Interfejs API REST to [Dodawanie, aktualizowanie lub usuwanie dokumentów](/rest/api/searchservice/addupdate-or-delete-documents). Dokumenty pochodzą z [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) w serwisie GitHub.

1. W nowej komórce Podaj cztery dokumenty, które są zgodne ze schematem indeksu. Określ akcję przekazywania dla każdego dokumentu.

    ```python
    documents = {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel's restaurant services.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
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
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    ```   

2. W innej komórce należy sformułować żądanie. To żądanie POST odwołuje się do kolekcji docs na indeksie z przewodnikiem Szybki Start i wypycha dokumenty podane w poprzednim kroku.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Uruchom każdy krok, aby wypchnąć dokumenty do indeksu w usłudze wyszukiwania. Wyniki powinny wyglądać podobnie do poniższego przykładu. 

    ![Wysyłanie dokumentów do indeksu](media/search-get-started-python/load-index.png "Wysyłanie dokumentów do indeksu")

## <a name="3---search-an-index"></a>3 — Przeszukiwanie indeksu

W tym kroku przedstawiono sposób wykonywania zapytań względem indeksu przy użyciu [interfejsu API REST dokumentów do przeszukiwania](/rest/api/searchservice/search-documents).

1. W komórce podaj wyrażenie zapytania, które wykonuje puste wyszukiwanie (Search = *), zwracając niesklasyfikowaną listę (wynik wyszukiwania = 1,0) dowolnych dokumentów. Domyślnie usługa Azure Wyszukiwanie poznawcze zwraca 50 dopasowań w danym momencie. Zgodnie ze strukturą, to zapytanie zwraca całą strukturę dokumentu i jego wartości. Dodaj $count = true, aby uzyskać liczbę wszystkich dokumentów w wynikach.

   ```python
   searchstring = '&search=*&$count=true'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. W nowej komórce podaj następujący przykład, aby wyszukać warunki "Hotele" i "Wi-Fi". Dodaj $select, aby określić pola do uwzględnienia w wynikach wyszukiwania.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)   
   ```

   Wyniki powinny wyglądać podobnie do poniższych danych wyjściowych. 

    ![Przeszukiwanie indeksu](media/search-get-started-python/search-index.png "Przeszukiwanie indeksu")

1. Następnie Zastosuj wyrażenie $filter, które wybiera tylko Hotele z klasyfikacją większą niż 4. 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)     
   ```

1. Domyślnie aparat wyszukiwania zwraca pierwsze 50 dokumentów, ale można użyć funkcji Top i Skip, aby dodać podział na strony i wybrać liczbę dokumentów w każdym wyniku. To zapytanie zwraca dwa dokumenty w każdym zestawie wyników.

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. W tym ostatnim przykładzie Użyj $orderby, aby sortować wyniki według miejscowości. Ten przykład zawiera pola z kolekcji adresów.

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

## <a name="clean-up"></a>Czyszczenie

Jeśli pracujesz w ramach własnej subskrypcji, dobrym pomysłem po zakończeniu projektu jest sprawdzenie, czy dalej potrzebujesz utworzonych zasobów. Uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub jako grupę zasobów, usuwając cały zestaw zasobów.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit. 

## <a name="next-steps"></a>Następne kroki

Jako uproszczenie, ten przewodnik Szybki Start używa skróconej wersji indeksu hoteli. Możesz utworzyć pełną wersję, aby wypróbować bardziej interesujące zapytania. Aby uzyskać pełną wersję i wszystkie 50 dokumentów, uruchom kreatora **importowania danych** , wybierając *Hotele* z wbudowanych przykładowych źródeł danych.

> [!div class="nextstepaction"]
> [Szybki Start: Tworzenie indeksu w Azure Portal](search-get-started-portal.md)