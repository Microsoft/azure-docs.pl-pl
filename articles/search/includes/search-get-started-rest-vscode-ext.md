---
title: Plik dyrektywy include
description: Plik dyrektywy include
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: include
ms.custom: include file
ms.date: 11/17/2020
ms.openlocfilehash: c84a7291e342a1acc465732cfbc350c571bba74d
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94714187"
---
W tym artykule jest używane rozszerzenie Visual Studio Code (wersja zapoznawcza) dla interfejsów API REST platformy Azure Wyszukiwanie poznawcze.

> [!IMPORTANT] 
> Ta funkcja jest obecnie w publicznej wersji zapoznawczej. Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start wymaga następujących usług i narzędzi. 

+ [Visual Studio Code](https://code.visualstudio.com/download)

+ [Wyszukiwanie poznawcze platformy Azure dla Visual Studio Code (wersja zapoznawcza)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch)

+ [Utwórz usługę Azure wyszukiwanie poznawcze](../search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. Możesz użyć bezpłatnej usługi dla tego przewodnika Szybki Start. 

## <a name="copy-a-key-and-url"></a>Kopiuj klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona razem z usługą, więc jeśli do subskrypcji dodano Wyszukiwanie poznawcze platformy Azure, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Uzyskaj adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W obszarze **Ustawienia**  >  **klucze** Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

![Pobieranie punktu końcowego HTTP i klucza dostępu](../media/search-get-started-rest/get-url-key.png "Pobieranie punktu końcowego HTTP i klucza dostępu")

Wszystkie żądania wymagają klucza API dla każdego żądania wysyłanego do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="install-the-extension"></a>Instalowanie rozszerzenia

Zacznij od otwarcia [vs Code](https://code.visualstudio.com). Wybierz kartę **rozszerzenia** na pasku działania, a następnie wyszukaj pozycję *Azure wyszukiwanie poznawcze*. Znajdź rozszerzenie w wynikach wyszukiwania, a następnie wybierz pozycję **Zainstaluj**.

![Okienko rozszerzenia VS Code](../media/search-get-started-rest/download-extension.png "Pobieranie rozszerzenia VS Code")

Możesz również zainstalować [rozszerzenie Azure wyszukiwanie poznawcze](https://aka.ms/vscode-search) w witrynie vs Code Marketplace w przeglądarce internetowej.

Jeśli jeszcze tego nie zrobiono, na pasku działania powinna zostać wyświetlona nowa karta Azure.

![VS Code okienku platformy Azure](../media/search-get-started-rest/azure-pane.png "Okienko platformy Azure w VS Code")

## <a name="connect-to-your-subscription"></a>Nawiązywanie połączenia z subskrypcją

Wybierz pozycję **Zaloguj się do platformy Azure...** i zaloguj się do swojego konta platformy Azure.

Powinny pojawić się Twoje subskrypcje. Wybierz subskrypcję, aby wyświetlić listę usług wyszukiwania w subskrypcji.

![VS Code subskrypcje platformy Azure](../media/search-get-started-rest/subscriptions.png "Subskrypcje w VS Code")

Aby ograniczyć liczbę wyświetlanych subskrypcji, Otwórz paletę poleceń (Ctrl + Shift + P lub Cmd + Shift + P) i Wyszukaj *platformę Azure* lub *Wybierz pozycję subskrypcje*. Dostępne są również polecenia do logowania się i z konta platformy Azure.

Po rozszerzeniu usługi wyszukiwania zobaczysz elementy drzewa dla każdego z Wyszukiwanie poznawcze zasobów: indeksy, źródła danych, indeksatory, umiejętności i mapy synonimów.

![VS Code drzewo usługi Azure Search](../media/search-get-started-rest/search-tree.png "VS Code drzewo usługi Azure Search")

Te elementy drzewa można rozszerzyć, aby pokazać wszystkie zasoby, które znajdują się w usłudze wyszukiwania.

## <a name="1---create-an-index"></a>1 — Tworzenie indeksu

Aby rozpocząć pracę z usługą Azure Wyszukiwanie poznawcze, musisz najpierw utworzyć indeks wyszukiwania. Odbywa się to za pomocą [interfejsu API Rest tworzenia indeksu](/rest/api/searchservice/create-index). 

Za pomocą rozszerzenia VS Code musisz się martwić o treść żądania. W tym przewodniku szybki start udostępniamy przykładową definicję indeksu i odpowiednie dokumenty.

### <a name="index-definition"></a>Definicja indeksu

Poniższa Definicja indeksu to Przykładowy schemat dla fikcyjnych hoteli.

`fields`Kolekcja definiuje strukturę dokumentów w indeksie wyszukiwania. Każde pole ma typ danych i kilka dodatkowych atrybutów, które określają, jak można użyć pola.

```json
{
    "name": "hotels-quickstart",
    "fields": [
        {
            "name": "HotelId",
            "type": "Edm.String",
            "key": true,
            "filterable": true
        },
        {
            "name": "HotelName",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": true,
            "facetable": false
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "en.lucene"
        },
        {
            "name": "Description_fr",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "fr.lucene"
        },
        {
            "name": "Category",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Tags",
            "type": "Collection(Edm.String)",
            "searchable": true,
            "filterable": true,
            "sortable": false,
            "facetable": true
        },
        {
            "name": "ParkingIncluded",
            "type": "Edm.Boolean",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "LastRenovationDate",
            "type": "Edm.DateTimeOffset",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Rating",
            "type": "Edm.Double",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Address",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true
                },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "PostalCode",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "Country",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        }
    ],
    "suggesters": [
        {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields": [
                "HotelName"
            ]
        }
    ]
}
```

Aby utworzyć nowy indeks, kliknij prawym przyciskiem myszy pozycję **indeksy** , a następnie wybierz pozycję **Utwórz nowy indeks**. Edytor o nazwie podobnej do zostanie wyświetlona `indexes-new-28c972f661.azsindex` . 

Wklej definicję indeksu z powyżej do okna. Zapisz plik i wybierz opcję **Przekaż** po wyświetleniu monitu, jeśli chcesz zaktualizować indeks. Spowoduje to utworzenie indeksu i będzie dostępny w widoku drzewa.

![Plik GIF tworzenia indeksu](../media/search-get-started-rest/create-index.gif)

Jeśli wystąpi problem z definicją indeksu, powinien zostać wyświetlony komunikat o błędzie z informacją o błędzie.

![Komunikat o błędzie tworzenia indeksu](../media/search-get-started-rest/create-index-error.png)

W takiej sytuacji należy rozwiązać problem i ponownie zapisać plik.

## <a name="2---load-documents"></a>2 — ładowanie dokumentów

Tworzenie indeksu i wypełnianie indeksu to oddzielne kroki. Na platformie Azure Wyszukiwanie poznawcze indeks zawiera wszystkie dane z możliwością wyszukiwania. W tym scenariuszu dane są dostarczane jako dokumenty JSON. Dla tego zadania jest używany [interfejs API REST Dodawanie, aktualizowanie lub usuwanie dokumentów](/rest/api/searchservice/addupdate-or-delete-documents) . 

Aby dodać nowe dokumenty w VS Code:

1. Rozwiń `hotels-quickstart` utworzony indeks. Kliknij prawym przyciskiem myszy **dokumenty** i wybierz polecenie **Utwórz nowy dokument**.

    ![Tworzenie dokumentu](../media/search-get-started-rest/create-document.png)

2. Spowoduje to otwarcie edytora JSON, który zgłosił schemat indeksu.

    ![Utwórz kod JSON dokumentu](../media/search-get-started-rest/create-document-2.png)

3. Wklej poniższy kod JSON, a następnie Zapisz plik. Zostanie otwarty monit z prośbą o potwierdzenie zmian. Wybierz pozycję **Przekaż** , aby zapisać zmiany.

    ```json
    {
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
        } 
    }
    ```

4. Powtórz ten proces dla trzech pozostałych dokumentów

    Dokument 2:
    ```json
    {
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
        } 
    }
    ```

    Dokument 3:
    ```json
    {
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
        } 
    }
    ```

    Dokument 4:
    ```json
    {
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": true,
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
    ```

W tym momencie powinny być widoczne wszystkie cztery dokumenty dostępne w sekcji dokumenty.

![stan po przekazaniu wszystkich dokumentów](../media/search-get-started-rest/create-document-finish.png)

## <a name="3---search-an-index"></a>3 — Przeszukiwanie indeksu

Teraz, gdy indeks i zestaw dokumentów są ładowane, można wysyłać zapytania względem nich przy użyciu [interfejsu API REST dokumentów wyszukiwania](/rest/api/searchservice/search-documents).

W tym celu w VS Code:

1. Kliknij prawym przyciskiem myszy indeks, który chcesz wyszukać, i wybierz opcję **indeks wyszukiwania**. Spowoduje to otwarcie edytora o nazwie podobnej do `sandbox-b946dcda48.azs` .

    ![Widok wyszukiwania rozszerzenia](../media/search-get-started-rest/search-vscode.png)

2. Proste zapytanie jest wypełniane automatycznie. Naciśnij **kombinację klawiszy Ctrl + Alt + r** lub **cmd + Alt + R** , aby przesłać zapytanie. Wyniki pojawią się w oknie po lewej stronie.

    ![Wyniki wyszukiwania w rozszerzeniu](../media/search-get-started-rest/search-results.png)


### <a name="example-queries"></a>Przykładowe zapytania

Wypróbuj kilka innych przykładów zapytania, aby uzyskać działanie dla składni. Oto cztery dodatkowe zapytania, które można wypróbować. Można dodać wiele zapytań do tego samego edytora. Po naciśnięciu **klawiszy Ctrl + Alt + r** lub **cmd + Alt + r**, wiersz kursor określa, które zapytanie zostanie przesłane.

![zapytania i wyniki obok siebie](../media/search-get-started-rest/all-searches.png)

W pierwszym zapytaniu szukamy `boutique` `select` tylko niektórych pól. Najlepszym rozwiązaniem jest tylko `select` potrzebne pola, ponieważ ściąganie niepotrzebnych danych może dodawać opóźnienia do zapytań. Zapytanie jest również ustawiane tak, `$count=true` Aby zwracało łączną liczbę wyników z wynikami wyszukiwania.

```
// Query example 1 - Search `boutique` with select and return count
search=boutique&$count=true&$select=HotelId,HotelName,Rating,Category
```

W następnym zapytaniu określamy termin wyszukiwania, `wifi` a także filtr, aby zwracał tylko wyniki, w których stan jest równy `'FL'` . Wyniki są również uporządkowane według hotelu `Rating` .

```
// Query example 2 - Search with filter, orderBy, select, and count
search=wifi&$filter=Address/StateProvince eq 'FL'&$select=HotelId,HotelName,Rating&$orderby=Rating desc
```

Następnie wyszukiwanie jest ograniczone do pojedynczego pola wyszukiwania przy użyciu `searchFields` parametru. Jest to świetna opcja, aby zwiększyć efektywność wykonywania zapytania, jeśli wiadomo, że interesuje Cię tylko w przypadku niektórych pól.

```
// Query example 3 - Limit searchFields
search=submlime cliff&$select=HotelId,HotelName,Rating&searchFields=HotelName
```

Inną wspólną opcją do uwzględnienia w zapytaniu jest `facets` . Zestawy reguł umożliwiają tworzenie filtrów w interfejsie użytkownika, aby ułatwić użytkownikom dowiedzieć się, jakie wartości można filtrować w dół.

```
// Query example 4 - Take the top two results, and show only HotelName and Category in the results
search=*&$select=HotelId,HotelName,Rating&searchFields=HotelName&facet=Category
```

## <a name="open-index-in-the-portal"></a>Otwórz indeks w portalu

Jeśli chcesz wyświetlić usługę wyszukiwania w portalu, kliknij prawym przyciskiem myszy nazwę usługi wyszukiwania i wybierz polecenie **Otwórz w portalu**. Spowoduje to przejście do usługi wyszukiwania w Azure Portal.