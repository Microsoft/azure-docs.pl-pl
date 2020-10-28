---
title: 'Szybki Start: Tworzenie indeksu wyszukiwania w języku JavaScript'
titleSuffix: Azure Cognitive Search
description: W tym przewodniku szybki start dla języka JavaScript dowiesz się, jak utworzyć indeks, załadować dane i uruchamiać zapytania na platformie Azure Wyszukiwanie poznawcze przy użyciu języka JavaScript
author: dereklegenzoff
manager: luisca
ms.author: delegenz
ms.devlang: javascript
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 10/26/2020
ms.custom: devx-track-js
ms.openlocfilehash: 5ccbe1035c5cc73993e069c7683d6b15ae18e21c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92795952"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-the-javascript-sdk"></a>Szybki Start: Tworzenie indeksu Wyszukiwanie poznawcze platformy Azure przy użyciu zestawu SDK języka JavaScript
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-javascript.md)
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [Program PowerShell](./search-get-started-powershell.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)


Użyj [zestawu SDK języka JavaScript/Typscript dla platformy wyszukiwanie poznawcze Azure](https://docs.microsoft.com/javascript/api/overview/azure/search-documents-readme?view=azure-node-latest) , aby utworzyć aplikację Node.js w języku JavaScript, która tworzy, ładuje i bada indeks wyszukiwania.

W tym artykule przedstawiono sposób tworzenia aplikacji krok po kroku. Alternatywnie możesz [pobrać kod źródłowy i dane](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) i uruchomić aplikację z wiersza polecenia.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy dysponować następującymi narzędziami i usługami:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/).

+ Usługa Wyszukiwanie poznawcze platformy Azure. [Utwórz usługę](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Możesz użyć bezpłatnej usługi dla tego przewodnika Szybki Start. 

+ [Node.js](https://nodejs.org) i [npm](https://www.npmjs.com)

+ [Visual Studio Code](https://code.visualstudio.com) lub inne środowisko IDE


## <a name="set-up-your-project"></a>konfigurowanie projektu

Zacznij od pobrania punktu końcowego i klucza dla usługi wyszukiwania. Następnie utwórz nowy projekt z NPM, jak opisano poniżej.

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>Kopiowanie klucza i punktu końcowego

Wywołania usługi wymagają punktu końcowego adresu URL i klucza dostępu dla każdego żądania. Najpierw znajdź klucz interfejsu API i adres URL, które mają zostać dodane do projektu. Podczas tworzenia klienta w późniejszym kroku należy określić obie wartości.

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Uzyskaj adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

2. W obszarze **Ustawienia**  >  **klucze** Uzyskaj klucz administratora dla pełnych praw do usługi, wymagany w przypadku tworzenia lub usuwania obiektów. Istnieją dwa wymienne klucze podstawowe i pomocnicze. Możesz użyć jednej z nich.

   ![Pobieranie punktu końcowego HTTP i klucza dostępu](media/search-get-started-postman/get-url-key.png "Pobieranie punktu końcowego HTTP i klucza dostępu")

Wszystkie żądania wymagają klucza API dla każdego żądania wysyłanego do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

### <a name="create-a-new-npm-project"></a>Utwórz nowy projekt NPM

Zacznij od otwarcia VS Code i jego [zintegrowanego terminalu](https://code.visualstudio.com/docs/editor/integrated-terminal) lub innego terminalu, takiego jak Node.js wiersza polecenia.

1. Utwórz katalog deweloperski, nadając mu nazwę `quickstart` :

    ```cmd
    mkdir quickstart
    cd quickstart
    ```

2. Zainicjuj pusty projekt z NPM przez uruchomienie 

    ```cmd
    npm init
    ```
     Zaakceptuj wartości domyślne, z wyjątkiem licencji, która powinna być ustawiona na wartość "MIT". 

3. Zainstaluj `@azure/search-documents` [zestaw SDK JavaScript/Typscript dla platformy wyszukiwanie poznawcze Azure](https://docs.microsoft.com/javascript/api/overview/azure/search-documents-readme?view=azure-node-latest).

    ```cmd
    npm install @azure/search-documents
    ```

4. Zainstaluj `dotenv` program, który służy do importowania zmiennych środowiskowych, takich jak nazwa usługi i klucz interfejsu API.
    ```cmd
    npm install dotenv
    ```

5. Upewnij się, że zostały skonfigurowane projekty i jego zależności, sprawdzając, czy  **package.jsw** pliku wygląda podobnie do następującego kodu JSON:

    ```json
    {
      "name": "quickstart",
      "version": "1.0.0",
      "description": "Azure Cognitive Search Quickstart",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "keywords": [
        "Azure",
        "Search"
      ],
      "author": "Your Name",
      "license": "MIT",
      "dependencies": {
        "@azure/search-documents": "^11.0.3",
        "dotenv": "^8.2.0"
      }
    }
    ```

6. Utwórz plik **. env** , aby przechowywać parametry usługi wyszukiwania:

    ```
    SEARCH_API_KEY=<search-admin-key>
    SEARCH_API_ENDPOINT=https://<search-service-name>.search.windows.net
    ```

Zastąp `<search-service-name>` wartość nazwą swojej usługi wyszukiwania. Zamień na `<search-admin-key>` zanotowaną wcześniej wartość klucza. 

### <a name="create-indexjs-file"></a>Utwórz plik index.js

Następnie tworzymy plik **index.js** , który jest głównym plikiem, który będzie hostować kod.

W górnej części tego pliku zaimportujemy `@azure/search-documents` bibliotekę:

```javascript
const { SearchIndexClient, SearchClient, AzureKeyCredential, odata } = require("@azure/search-documents");
```

Następnie musimy wymagać, `dotenv` Aby pakiet był odczytywany w parametrach z pliku **ENV** w następujący sposób:

```javascript
// Load the .env file if it exists
require("dotenv").config();

// Getting endpoint and apiKey from .env file
const endpoint = process.env.SEARCH_API_ENDPOINT || "";
const apiKey = process.env.SEARCH_API_KEY || "";
```

W przypadku naszych importów i zmiennych środowiskowych gotowe do zdefiniowania funkcji main.

Większość funkcji w zestawie SDK jest operacją asynchroniczną, więc oferujemy naszą główną funkcję `async` . Zawieramy również `main().catch()` poniżej główną funkcję do przechwytywania i rejestrowania napotkanych błędów:

```javascript
async function main() {
    console.log(`Running Azure Cognitive Search Javascript quickstart...`);
    if (!endpoint || !apiKey) {
        console.log("Make sure to set valid values for endpoint and apiKey with proper authorization.");
        return;
    }

    // remaining quickstart code will go here
}

main().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

W tym miejscu możemy utworzyć indeks.

## <a name="1---create-index"></a>1 — Tworzenie indeksu 

Utwórzhotels_quickstart_index.jspliku **na** .  Ten plik definiuje, w jaki sposób usługa Azure Wyszukiwanie poznawcze współpracuje z dokumentami, które zostaną załadowane w następnym kroku. Każde pole zostanie zidentyfikowane przez `name` i ma określony `type` . Każde pole ma także serię atrybutów indeksu, które określają, czy Wyszukiwanie poznawcze platformy Azure mogą przeszukiwać, filtrować, sortować i aspektować pola. Większość pól to proste typy danych, ale niektóre, takie jak `AddressType` złożone typy, które umożliwiają tworzenie rozbudowanych struktur danych w indeksie.  Więcej informacji o [obsługiwanych typach danych](/rest/api/searchservice/supported-data-types) i [atrybutach indeksu](./search-what-is-an-index.md#index-attributes)można znaleźć w części. 

Dodaj następujące elementy, aby **hotels_quickstart_index.jsna** lub [pobrać plik](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/v11/hotels_quickstart_index.json). 

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

W przypadku naszej definicji indeksu chcemy zaimportować  **hotels_quickstart_index.jsw** górnej części **index.js** , aby funkcja Main mogła uzyskać dostęp do definicji indeksu.

```javascript
const indexDefinition = require('./hotels_quickstart_index.json');
```

W ramach funkcji Main utworzymy `SearchIndexClient` , który jest używany do tworzenia indeksów dla wyszukiwanie poznawcze platformy Azure i zarządzania nimi. 

```javascript
const indexClient = new SearchIndexClient(endpoint, new AzureKeyCredential(apiKey));
```

Następnie chcemy usunąć indeks, jeśli już istnieje. Jest to typowa Metoda dla kodu testowego i demonstracyjnego.

W tym celu należy zdefiniować prostą funkcję, która próbuje usunąć indeks.

```javascript
async function deleteIndexIfExists(indexClient, indexName) {
    try {
        await indexClient.deleteIndex(indexName);
        console.log('Deleting index...');
    } catch {
        console.log('Index does not exist yet.');
    }
}
```

Aby uruchomić funkcję, wyodrębnimy nazwę indeksu z definicji indeksu i przekazujemy `indexName` wraz z `indexClient` do `deleteIndexIfExists()` funkcji.

```javascript
const indexName = indexDefinition["name"];

console.log('Checking if index exists...');
await deleteIndexIfExists(indexClient, indexName);
```

Następnie możemy utworzyć indeks za pomocą `createIndex()` metody.

```javascript
console.log('Creating index...');
let index = await indexClient.createIndex(indexDefinition);

console.log(`Index named ${index.name} has been created.`);
```

### <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Na tym etapie możesz uruchomić przykład. Użyj okna terminalu, aby uruchomić następujące polecenie:

```cmd
node index.js
```

Jeśli [pobrano kod źródłowy](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) i nie zainstalowano jeszcze wymaganych pakietów, najpierw należy uruchomić polecenie `npm install` .

Powinna zostać wyświetlona seria komunikatów opisujących akcje podejmowane przez program. 

Otwórz **Przegląd** usługi wyszukiwania w Azure Portal. Wybierz kartę **indeksy** . Powinien wyglądać podobnie do poniższego:

:::image type="content" source="media/search-get-started-javascript/create-index-no-data.png" alt-text="Zrzut ekranu przedstawiający Azure Portal, Omówienie usługi wyszukiwania, karta indeksy" border="false":::

W następnym kroku dodasz dane do indeksu. 

## <a name="2---load-documents"></a>2 — ładowanie dokumentów 


Na platformie Azure Wyszukiwanie poznawcze dokumenty są strukturami danych, które są danymi wejściowymi do indeksowania i wyjść z zapytań. Można wypchnąć takie dane do indeksu lub użyć [indeksatora](search-indexer-overview.md). W takim przypadku programowo wypchnięcie dokumentów do indeksu.

Dane wejściowe dokumentu mogą być wierszami w bazie danych, obiektami BLOB w usłudze BLOB Storage lub, jak w tym przykładzie, dokumentami JSON na dysku. Możesz pobrać [hotels.jsna](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/v11/hotels.json) lub utworzyć własny **hotels.jsna** pliku z następującą zawartością:

```json
{
    "value": [
        {
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
            "Category": "Boutique",
            "Tags": ["pool", "air conditioning", "concierge"],
            "ParkingIncluded": false,
            "LastRenovationDate": "1970-01-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "677 5th Ave",
                "City": "New York",
                "StateProvince": "NY",
                "PostalCode": "10022"
            }
        },
        {
            "HotelId": "2",
            "HotelName": "Twin Dome Motel",
            "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Boutique",
            "Tags": ["pool", "free wifi", "concierge"],
            "ParkingIncluded": "false",
            "LastRenovationDate": "1979-02-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "140 University Town Center Dr",
                "City": "Sarasota",
                "StateProvince": "FL",
                "PostalCode": "34243"
            }
        },
        {
            "HotelId": "3",
            "HotelName": "Triple Landscape Hotel",
            "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Resort and Spa",
            "Tags": ["air conditioning", "bar", "continental breakfast"],
            "ParkingIncluded": "true",
            "LastRenovationDate": "2015-09-20T00:00:00Z",
            "Rating": 4.8,
            "Address": {
                "StreetAddress": "3393 Peachtree Rd",
                "City": "Atlanta",
                "StateProvince": "GA",
                "PostalCode": "30326"
            }
        },
        {
            "HotelId": "4",
            "HotelName": "Sublime Cliff Hotel",
            "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
            "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
            "Category": "Boutique",
            "Tags": ["concierge", "view", "24-hour front desk service"],
            "ParkingIncluded": true,
            "LastRenovationDate": "1960-02-06T00:00:00Z",
            "Rating": 4.6,
            "Address": {
                "StreetAddress": "7400 San Pedro Ave",
                "City": "San Antonio",
                "StateProvince": "TX",
                "PostalCode": "78216"
            }
        }
    ]
}
```

Podobnie jak w przypadku indexDefinition, należy również zaimportować `hotels.json` je na początku **index.js** , aby można było uzyskać dostęp do danych w naszej funkcji main.

```javascript
const hotelData = require('./hotels.json');
```


Aby zindeksować dane do indeksu wyszukiwania, należy teraz utworzyć `SearchClient` . Gdy `SearchIndexClient` jest używany do tworzenia indeksu i zarządzania nim, służy `SearchClient` do przekazywania dokumentów i wykonywania zapytań względem indeksu.

Istnieją dwa sposoby tworzenia `SearchClient` . Pierwsza opcja to utworzenie `SearchClient` od podstaw:

```javascript
 const searchClient = new SearchClient(endpoint, indexName, new AzureKeyCredential(apiKey));
```

Alternatywnie można użyć `getSearchClient()` metody, `SearchIndexClient` Aby utworzyć `SearchClient` :

```javascript
const searchClient = indexClient.getSearchClient(indexName);
```

Teraz, gdy klient jest zdefiniowany, Przekaż dokumenty do indeksu wyszukiwania. W tym przypadku używamy `mergeOrUploadDocuments()` metody, która przekaże dokumenty lub scali je z istniejącym dokumentem, jeśli dokument z tym samym kluczem już istnieje.

```javascript
console.log('Uploading documents...');
let indexDocumentsResult = await searchClient.mergeOrUploadDocuments(hotelData['value']);

console.log(`Index operations succeeded: ${JSON.stringify(indexDocumentsResult.results[0].succeeded)}`);
```

Ponownie uruchom program z `node index.js` . Powinien pojawić się nieco inny zestaw komunikatów od tych, które zostały podane w kroku 1. Tym *razem indeks istnieje* i powinien zostać wyświetlony komunikat o usunięciu, zanim aplikacja utworzy nowy indeks i opublikuje do niego dane. 

Przed uruchomieniem zapytań w następnym kroku, zdefiniuj funkcję, aby program czekał na sekundę. Jest to wykonywane wyłącznie w celach testowych i demonstracyjnych, aby upewnić się, że indeksowanie kończy się i że dokumenty są dostępne w indeksie zapytań.

```javascript
function sleep(ms) {
    var d = new Date();
    var d2 = null;
    do {
        d2 = new Date();
    } while (d2 - d < ms);
}
```

Aby program czekał na sekundę, wywołaj `sleep` funkcję podobną do poniższej:

```javascript
sleep(1000);
```

## <a name="3---search-an-index"></a>3 — Przeszukiwanie indeksu

Po utworzeniu indeksu i przekazaniu dokumentów możesz wysyłać zapytania do indeksu. W tej sekcji wyślemy pięć różnych zapytań do indeksu wyszukiwania, aby przedstawić różne fragmenty funkcji zapytania.

Zapytania są zapisywane w `sendQueries()` funkcji, która będzie wywoływana w funkcji Main w następujący sposób:

```javascript
await sendQueries(searchClient);
```

Zapytania są wysyłane przy użyciu `search()` metody `searchClient` . Pierwszy parametr jest wyszukiwanym tekstem, a drugi parametr jest dodatkowymi opcjami wyszukiwania.

Pierwsze wyszukiwanie zapytań `*` , które jest równoważne z wyszukiwaniem wszystkiego i wybiera trzy pola w indeksie. Najlepszym rozwiązaniem jest tylko `select` potrzebne pola, ponieważ ściąganie niepotrzebnych danych może dodawać opóźnienia do zapytań.

`searchOptions`Dla tego zapytania `includeTotalCount` ustawiono również wartość `true` , która zwróci liczbę znalezionych pasujących wyników.

```javascript
async function sendQueries(searchClient) {
    console.log('Query #1 - search everything:');
    let searchOptions = {
        includeTotalCount: true,
        select: ["HotelId", "HotelName", "Rating"]
    };

    let searchResults = await searchClient.search("*", searchOptions);
    for await (const result of searchResults.results) {
        console.log(`${JSON.stringify(result.document)}`);
    }
    console.log(`Result count: ${searchResults.count}`);

    // remaining queries go here
}
```

Pozostałe zapytania opisane poniżej należy również dodać do `sendQueries()` funkcji. Są one oddzielone tutaj w celu zapewnienia czytelności.

W następnym zapytaniu określamy termin wyszukiwania, `"wifi"` a także filtr, aby zwracał tylko wyniki, w których stan jest równy `'FL'` . Wyniki są również uporządkowane według hotelu `Rating` .

```javascript
console.log('Query #2 - Search with filter, orderBy, and select:');
let state = 'FL';
searchOptions = {
    filter: odata`Address/StateProvince eq ${state}`,
    orderBy: ["Rating desc"],
    select: ["HotelId", "HotelName", "Rating"]
};

searchResults = await searchClient.search("wifi", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

Następnie wyszukiwanie jest ograniczone do pojedynczego pola wyszukiwania przy użyciu `searchFields` parametru. Jest to świetna opcja, aby zwiększyć efektywność wykonywania zapytania, jeśli wiadomo, że interesuje Cię tylko w przypadku niektórych pól. 

```javascript
console.log('Query #3 - Limit searchFields:');
searchOptions = {
    select: ["HotelId", "HotelName", "Rating"],
    searchFields: ["HotelName"]
};

searchResults = await searchClient.search("sublime cliff", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
console.log();
```

Inną wspólną opcją do uwzględnienia w zapytaniu jest `facets` . Zestawy reguł umożliwiają tworzenie filtrów w interfejsie użytkownika, aby ułatwić użytkownikom dowiedzieć się, jakie wartości można filtrować w dół.

```javascript
console.log('Query #4 - Use facets:');
searchOptions = {
    facets: ["Category"],
    select: ["HotelId", "HotelName", "Rating"],
    searchFields: ["HotelName"]
};

searchResults = await searchClient.search("*", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

Zapytanie końcowe używa `getDocument()` metody `searchClient` . Pozwala to efektywnie pobrać dokument według jego klucza. 

```javascript
console.log('Query #5 - Lookup document:');
let documentResult = await searchClient.getDocument(key='3')
console.log(`HotelId: ${documentResult.HotelId}; HotelName: ${documentResult.HotelName}`)
```

### <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Uruchom program za pomocą `node index.js` . Teraz, oprócz poprzednich kroków, zapytania będą wysyłane i wyniki zapisywane w konsoli.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W przypadku pracy w ramach własnej subskrypcji warto sprawdzić po zakończeniu projektu, czy dalej potrzebuje się utworzonych zasobów. Uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit. 

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start dla języka JavaScript przepracowałeś szereg zadań, aby utworzyć indeks, załadować go wraz z dokumentami i uruchamiać zapytania. 

Jeśli masz już tło na platformie Azure Wyszukiwanie poznawcze, możesz użyć tego przykładu jako Springboard do wykonywania sugestii (zapytań typu "z wyprzedzeniem lub autouzupełniania"), filtrów i nawigacji aspektowej. Jeśli dopiero zaczynasz korzystanie z usługi Azure Wyszukiwanie poznawcze, zalecamy podjęcie dalszych samouczków w celu opracowania informacji o tym, co można utworzyć. Aby znaleźć więcej zasobów, odwiedź naszą [stronę dokumentacji](https://azure.microsoft.com/documentation/services/search/). 

> [!div class="nextstepaction"]
> [Tworzenie frontonu w celu reagowania na platformę Azure Wyszukiwanie poznawcze](https://github.com/dereklegenzoff/azure-search-react-template)