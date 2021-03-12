---
title: 'Szybki Start: Tworzenie indeksu wyszukiwania w języku Python'
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak utworzyć indeks wyszukiwania, załadować dane i uruchamiać zapytania przy użyciu języka Python, Jupyter Notebook i Azure.Documents. Wyszukaj bibliotekę kliencką dla języka Python.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/12/2021
ms.custom: devx-track-python
ms.openlocfilehash: 8b9c4792fa6dbdc70f657ce3c5f1757473a22fda
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225221"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebook"></a>Szybki Start: Tworzenie indeksu Wyszukiwanie poznawcze platformy Azure w języku Python przy użyciu Jupyter Notebook

> [!div class="op_single_selector"]
> * [Python](search-get-started-python.md)
> * [PowerShell (REST)](search-get-started-powershell.md)
> * [C#](search-get-started-dotnet.md)
> * [REST](search-get-started-rest.md)
> * [Portal](search-get-started-portal.md)
>

Utwórz Notes służący do tworzenia, ładowania i wysyłania zapytań do indeksu Wyszukiwanie poznawcze platformy Azure przy użyciu języka Python oraz [biblioteki Azure-Search-Documents](/python/api/overview/azure/search-documents-readme) w zestawie Azure SDK dla języka Python. W tym artykule opisano sposób tworzenia notesu krok po kroku. Alternatywnie można [pobrać i uruchomić gotowy Notes Python Jupyter](https://github.com/Azure-Samples/azure-search-python-samples).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start wymaga następujących usług i narzędzi.

* [Anaconda 3. x](https://www.anaconda.com/distribution/#download-section), dostarczając Python 3. x i Jupyter Notebook.

* [Azure-Search — pakiet dokumentów](https://pypi.org/project/azure-search-documents/)

* [Utwórz usługę wyszukiwania](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. W tym przewodniku Szybki Start możesz skorzystać z warstwy Bezpłatna. 

## <a name="copy-a-key-and-url"></a>Kopiuj klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona razem z usługą, więc jeśli do subskrypcji dodano Wyszukiwanie poznawcze platformy Azure, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Uzyskaj adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W obszarze **Ustawienia**  >  **klucze** Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

   ![Pobieranie punktu końcowego HTTP i klucza dostępu](media/search-get-started-rest/get-url-key.png "Pobieranie punktu końcowego HTTP i klucza dostępu")

Wszystkie żądania wymagają klucza API dla każdego żądania wysyłanego do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="connect-to-azure-cognitive-search"></a>Nawiązywanie połączenia z usługą Azure Wyszukiwanie poznawcze

W tym zadaniu Rozpocznij Jupyter Notebook i sprawdź, czy możesz nawiązać połączenie z usługą Azure Wyszukiwanie poznawcze. W tym celu należy zażądać listy indeksów z usługi. W systemie Windows z Anaconda3 można użyć nawigatora Anaconda do uruchomienia notesu.

1. Utwórz nowy Notes python3.

1. W pierwszej komórce Załaduj biblioteki z zestawu Azure SDK dla języka Python, w tym [Azure-Search-Documents](/python/api/azure-search-documents).

   ```python
    !pip install azure-search-documents --pre
    !pip show azure-search-documents
    
    import os
    from azure.core.credentials import AzureKeyCredential
    from azure.search.documents.indexes import SearchIndexClient 
    from azure.search.documents import SearchClient
    from azure.search.documents.indexes.models import (
        ComplexField,
        CorsOptions,
        SearchIndex,
        ScoringProfile,
        SearchFieldDataType,
        SimpleField,
        SearchableField
    )
   ```

1. W drugiej komórce wprowadź elementy żądania, które będą stałe dla każdego żądania. Podaj nazwę usługi wyszukiwania, klucz interfejsu API administratora i klucz interfejsu API zapytania, skopiowane w poprzednim kroku. Ta komórka konfiguruje również klientów, którzy będą używani do określonych operacji: [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) do tworzenia indeksu i [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) do wykonywania zapytań względem indeksu.

   ```python
    service_name = "YOUR-SEARCH-SERIVCE-NAME"
    admin_key = "YOUR-SEARCH-SERVICE-ADMIN-API-KEY"
    
    index_name = "hotels-quickstart"
    
    # Create an SDK client
    endpoint = "https://{}.search.windows.net/".format(service_name)
    admin_client = SearchIndexClient(endpoint=endpoint,
                          index_name=index_name,
                          credential=AzureKeyCredential(admin_key))
    
    search_client = SearchClient(endpoint=endpoint,
                          index_name=index_name,
                          credential=AzureKeyCredential(admin_key))
   ```

1. W trzeciej komórce Uruchom delete_index operację, aby wyczyścić usługę wszelkich istniejących indeksów *szybkiego startu* . Usunięcie indeksu umożliwia utworzenie innego indeksu z *przewodnikiem Szybki Start* o tej samej nazwie.

   ```python
    try:
        result = admin_client.delete_index(index_name)
        print ('Index', index_name, 'Deleted')
    except Exception as ex:
        print (ex)
   ```

1. Uruchom każdy krok.

## <a name="1---create-an-index"></a>1 — Tworzenie indeksu

Wymagane elementy indeksu obejmują nazwę, kolekcję pól i klucz. Kolekcja Fields definiuje strukturę *dokumentu wyszukiwania* logicznego, używanego do ładowania danych i zwracania wyników. 

Każde pole ma nazwę, typ i atrybuty, które określają sposób użycia pola (na przykład czy jest to możliwość wyszukiwania pełnotekstowego, filtrowania lub pobierania w wynikach wyszukiwania). W indeksie należy wyznaczyć jedno z pól typu `Edm.String` jako *klucz* dla tożsamości dokumentu.

Ten indeks ma nazwę "Hotele-Szybki Start" i zawiera definicje pól widoczne poniżej. Jest to podzestaw większego [indeksu hoteli](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) używany w innych przewodnikach. Ten przewodnik Szybki Start został przez nas przycięty do zwięzłości.

1. W następnej komórce wklej poniższy przykład do komórki, aby udostępnić schemat.

    ```python
    # Specify the index schema
    name = index_name
    fields = [
            SimpleField(name="HotelId", type=SearchFieldDataType.String, key=True),
            SearchableField(name="HotelName", type=SearchFieldDataType.String, sortable=True),
            SearchableField(name="Description", type=SearchFieldDataType.String, analyzer_name="en.lucene"),
            SearchableField(name="Description_fr", type=SearchFieldDataType.String, analyzer_name="fr.lucene"),
            SearchableField(name="Category", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
        
            SearchableField(name="Tags", collection=True, type=SearchFieldDataType.String, facetable=True, filterable=True),
    
            SimpleField(name="ParkingIncluded", type=SearchFieldDataType.Boolean, facetable=True, filterable=True, sortable=True),
            SimpleField(name="LastRenovationDate", type=SearchFieldDataType.DateTimeOffset, facetable=True, filterable=True, sortable=True),
            SimpleField(name="Rating", type=SearchFieldDataType.Double, facetable=True, filterable=True, sortable=True),
    
            ComplexField(name="Address", fields=[
                SearchableField(name="StreetAddress", type=SearchFieldDataType.String),
                SearchableField(name="City", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="StateProvince", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="PostalCode", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="Country", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
            ])
        ]
    cors_options = CorsOptions(allowed_origins=["*"], max_age_in_seconds=60)
    scoring_profiles = []
    suggester = [{'name': 'sg', 'source_fields': ['Tags', 'Address/City', 'Address/Country']}]
    ```

1. W innej komórce należy sformułować żądanie. To żądanie create_index dotyczy kolekcji indeksów usługi wyszukiwania i tworzy [SearchIndex](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex) na podstawie schematu indeksu podanego w poprzedniej komórce.

    ```python
    index = SearchIndex(
        name=name,
        fields=fields,
        scoring_profiles=scoring_profiles,
        suggesters = suggester,
        cors_options=cors_options)
    
    try:
        result = admin_client.create_index(index)
        print ('Index', result.name, 'created')
    except Exception as ex:
        print (ex)
    ```

1. Uruchom każdy krok.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 — ładowanie dokumentów

Aby załadować dokumenty, Utwórz kolekcję dokumentów przy użyciu [akcji indeksu](/python/api/azure-search-documents/azure.search.documents.models.indexaction) dla typu operacji (przekazywanie, scalanie i przekazywanie itd.). Dokumenty pochodzą z [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) w serwisie GitHub.

1. W nowej komórce Podaj cztery dokumenty, które są zgodne ze schematem indeksu. Określ akcję przekazywania dla każdego dokumentu.

    ```python
    documents = [
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
    ```  

1. W innej komórce należy sformułować żądanie. To żądanie upload_documents odwołuje się do kolekcji docs tego indeksu hoteli-szybkiego startu i wypycha dokumenty podane w poprzednim kroku do indeksu Wyszukiwanie poznawcze.

    ```python
    try:
        result = search_client.upload_documents(documents=documents)
        print("Upload of new document succeeded: {}".format(result[0].succeeded))
    except Exception as ex:
        print (ex.message)
    ```

1. Uruchom każdy krok, aby wypchnąć dokumenty do indeksu w usłudze wyszukiwania.

## <a name="3---search-an-index"></a>3 — Przeszukiwanie indeksu

W tym kroku przedstawiono sposób wykonywania zapytań względem indeksu przy użyciu metody **Search** [klasy Search. Client](/python/api/azure-search-documents/azure.search.documents.searchclient).

1. Poniższy krok wykonuje puste wyszukiwanie ( `search=*` ), zwracając listę niesklasyfikowaną (wynik wyszukiwania = 1,0) dowolnych dokumentów. Ponieważ nie ma żadnych kryteriów, wszystkie dokumenty są zawarte w wynikach. To zapytanie drukuje tylko dwa pola w każdym dokumencie. Dodaje także `include_total_count=True` do wszystkich dokumentów (4) w wynikach.

    ```python
    results =  search_client.search(search_text="*", include_total_count=True)
    
    print ('Total Documents Matching Query:', results.get_count())
    for result in results:
        print("{}: {}".format(result["HotelId"], result["HotelName"]))
    ```

1. Następne zapytanie dodaje całe warunki do wyrażenia wyszukiwania ("Wi-Fi"). To zapytanie określa, że wyniki zawierają tylko te pola w `select` instrukcji. Ograniczenie pól, które powrócisz, minimalizuje ilość danych wysyłanych z powrotem przez sieć i zmniejsza czas oczekiwania na wyszukiwanie.

    ```python
    results =  search_client.search(search_text="wifi", include_total_count=True, select='HotelId,HotelName,Tags')
    
    print ('Total Documents Matching Query:', results.get_count())
    for result in results:
        print("{}: {}: {}".format(result["HotelId"], result["HotelName"], result["Tags"]))
    ```

1. Następnie Zastosuj wyrażenie filtru, zwracając tylko te Hotele z klasyfikacją większą niż 4, posortowaną w kolejności malejącej.

    ```python
    results =  search_client.search(search_text="hotels", select='HotelId,HotelName,Rating', filter='Rating gt 4', order_by='Rating desc')
    
    for result in results:
        print("{}: {} - {} rating".format(result["HotelId"], result["HotelName"], result["Rating"]))
    ```

1. Dodaj `search_fields` do zakresu zapytania dopasowywania do pojedynczego pola.

    ```python
    results =  search_client.search(search_text="sublime", search_fields='HotelName', select='HotelId,HotelName')
    
    for result in results:
        print("{}: {}".format(result["HotelId"], result["HotelName"]))
    ```

1. Zestawy reguł są etykietami, które mogą służyć do tworzenia struktury nawigacji aspektów. To zapytanie zwraca aspekty i liczniki dla kategorii.

    ```python
    results =  search_client.search(search_text="*", facets=["Category"])
    
    facets = results.get_facets()
    
    for facet in facets["Category"]:
        print("    {}".format(facet))
    ```

1. W tym przykładzie zapoznaj się z określonym dokumentem w oparciu o jego klucz. Zazwyczaj chcesz zwrócić dokument, gdy użytkownik kliknie dokument w wyniku wyszukiwania.

    ```python
    result = search_client.get_document(key="3")
    
    print("Details for hotel '3' are:")
    print("Name: {}".format(result["HotelName"]))
    print("Rating: {}".format(result["Rating"]))
    print("Category: {}".format(result["Category"]))
    ```

1. W tym przykładzie użyjemy funkcji Autouzupełnianie. Jest to zwykle używane w polu wyszukiwania, aby pomóc w autouzupełnianiu potencjalnych dopasowań jako typy użytkowników w polu wyszukiwania.

   Gdy indeks został utworzony, w ramach żądania utworzono również program sugerujący o nazwie "SG". Definicja programu sugerującego określa, które pola mogą być używane do znajdowania potencjalnych dopasowań żądań sugestii. W tym przykładzie pola te to "Tags", "Address/miasto", "Address/Country". Aby zasymulować funkcję autouzupełniania, Przekaż litery "sa" jako ciąg częściowy. Metoda autouzupełniania [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) wysyła zwroty potencjalnych warunków.

    ```python
    search_suggestion = 'sa'
    results = search_client.autocomplete(search_text=search_suggestion, suggester_name="sg", mode='twoTerms')
    
    print("Autocomplete for:", search_suggestion)
    for result in results:
        print (result['text'])
    ```

## <a name="clean-up"></a>Czyszczenie

Jeśli pracujesz w ramach własnej subskrypcji, dobrym pomysłem po zakończeniu projektu jest sprawdzenie, czy dalej potrzebujesz utworzonych zasobów. Uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit. 

## <a name="next-steps"></a>Następne kroki

Jako uproszczenie, ten przewodnik Szybki Start używa skróconej wersji indeksu hoteli. Możesz utworzyć pełną wersję, aby wypróbować bardziej interesujące zapytania. Aby uzyskać pełną wersję i wszystkie 50 dokumentów, uruchom kreatora **importowania danych** , wybierając *Hotele* z wbudowanych przykładowych źródeł danych.

> [!div class="nextstepaction"]
> [Szybki Start: Tworzenie indeksu w Azure Portal](search-get-started-portal.md)