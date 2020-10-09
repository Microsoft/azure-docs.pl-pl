---
title: Tworzenie sklepu z bazami danych przy użyciu usługi REST
titleSuffix: Azure Cognitive Search
description: Użyj interfejsu API REST i programu Poster, aby utworzyć magazyn wiedzy platformy Azure Wyszukiwanie poznawcze na potrzeby utrwalania wzbogacania z potoku wzbogacania AI.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 08/02/2020
ms.openlocfilehash: 1745a2bf83cb704c8cc73e9d3bf0eba8245329b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88924269"
---
# <a name="create-a-knowledge-store-using-rest-and-postman"></a>Tworzenie sklepu z bazami danych przy użyciu REST i programu Poster

Magazyn wiedzy zawiera dane wyjściowe z potoku wzbogacenia Wyszukiwanie poznawcze platformy Azure na potrzeby późniejszej analizy lub innego przetwarzania podrzędnego. Potok wzbogacony AI akceptuje pliki obrazów lub pliki tekstowe bez struktury, indeksuje je za pomocą usługi Azure Wyszukiwanie poznawcze, stosuje wzbogacenia AI z Cognitive Services (takich jak analiza obrazów i przetwarzanie języka naturalnego), a następnie zapisuje wyniki w sklepie z bazami danych w usłudze Azure Storage. Aby eksplorować informacje o sklepie z bazami danych, można użyć narzędzi takich jak Power BI lub Eksplorator usługi Storage w Azure Portal.

W tym artykule opisano korzystanie z interfejsu API REST w celu pozyskiwania, indeksowania i stosowania wzbogacania systemu AI do zestawu przeglądów hotelowych. Przeglądy hotelu są importowane do usługi Azure Blob Storage. Wyniki są zapisywane jako magazyn wiedzy w usłudze Azure Table Storage.

Po utworzeniu sklepu z informacjami można dowiedzieć się, jak uzyskać dostęp do sklepu z bazami danych przy użyciu [Eksplorator usługi Storage](knowledge-store-view-storage-explorer.md) lub [Power BI](knowledge-store-connect-power-bi.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!TIP]
> Zalecamy [opublikowanie aplikacji klasycznych](https://www.getpostman.com/) w tym artykule. [Kod źródłowy](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) tego artykułu zawiera kolekcję programu Poster zawierającą wszystkie żądania. 

## <a name="create-services-and-load-data"></a>Tworzenie usług i ładowanie danych

Ten przewodnik Szybki Start używa platformy Azure Wyszukiwanie poznawcze, usługi Azure Blob Storage i [usługi azure Cognitive Services dla systemu](https://azure.microsoft.com/services/cognitive-services/) AI. 

Ponieważ obciążenie jest tak małe, Cognitive Services jest wybierane w tle, aby zapewnić bezpłatne przetwarzanie do 20 transakcji codziennie. Ze względu na to, że zestaw danych jest mały, możesz pominąć tworzenie i dołączanie zasobów Cognitive Services.

1. [Pobierz HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Te dane to dane z przeglądu hotelu zapisane w pliku CSV (pochodzące z Kaggle.com) i zawierają 19 opinii klientów na temat pojedynczego hotelu. 

1. [Utwórz konto usługi Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal) lub [Znajdź istniejące konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) w ramach bieżącej subskrypcji. Będziesz używać usługi Azure Storage do importowania nieprzetworzonej zawartości oraz magazynu wiedzy, który jest wynikiem końcowym.

   Wybierz typ konta **StorageV2 (ogólnego przeznaczenia w wersji 2)** .

1. Otwórz strony usługi BLOB Services i Utwórz kontener o nazwie *"przeglądy hotelowe"*.

1. Kliknij pozycję **Przekaż**.

    ![Przekaż dane](media/knowledge-store-create-portal/upload-command-bar.png "Przekaż przeglądy hotelu")

1. Wybierz plik **HotelReviews-Free.csv** pobrany w pierwszym kroku.

    ![Tworzenie kontenera obiektów blob platformy Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Tworzenie kontenera obiektów blob platformy Azure")

1. Prawie zakończysz korzystanie z tego zasobu, ale zanim opuścisz te strony, Użyj linku w okienku nawigacji po lewej stronie, aby otworzyć stronę **klawisze dostępu** . Pobierz parametry połączenia, aby pobrać dane z magazynu obiektów BLOB. Parametry połączenia wyglądają podobnie do poniższego przykładu: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. W portalu przejdź do usługi Azure Wyszukiwanie poznawcze. [Utwórz nową usługę](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). W tym ćwiczeniu możesz użyć bezpłatnej usługi.

## <a name="configure-postman"></a>Konfigurowanie programu Postman

Zainstaluj i skonfiguruj program ogłaszający.

### <a name="download-and-install-postman"></a>Pobierz i zainstaluj program Poster

1. Pobierz [kod źródłowy kolekcji Poster](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json).
1. Wybierz **File**pozycję  >  **Importuj** plik, aby zaimportować kod źródłowy do programu Poster.
1. Wybierz kartę **kolekcje** , a następnie wybierz przycisk **...** (wielokropek).
1. Kliknij pozycję **Edytuj**. 
   
   ![Aplikacja do publikowania po wyświetleniu nawigacji](media/knowledge-store-create-rest/postman-edit-menu.png "Przejdź do menu Edycja w programie Poster")
1. W oknie dialogowym **Edycja** wybierz kartę **zmienne** . 

Na karcie **zmienne** można dodawać wartości, które zaswapki są księgowane w każdym momencie napotkania określonej zmiennej wewnątrz podwójnych nawiasów klamrowych. Na przykład, program Poster zastępuje symbol `{{admin-key}}` wartością bieżącą ustawioną przez użytkownika `admin-key` . Program ogłaszający tworzy podstawienie w adresach URL, nagłówkach, treści żądania i tak dalej. 

Aby uzyskać wartość dla `admin-key` , przejdź do usługi Azure wyszukiwanie poznawcze i wybierz kartę **klucze** . Zmień `search-service-name` i `storage-account-name` na wartości wybrane w obszarze [Tworzenie usług](#create-services-and-load-data). Ustaw `storage-connection-string` wartość przy użyciu wartości na karcie **klucze dostępu** konta magazynu. Można pozostawić wartości domyślne dla innych wartości.

![Karta zmiennych aplikacji po wprowadzeniu](media/knowledge-store-create-rest/postman-variables-window.png "Okno zmiennych autora")


| Zmienna    | Skąd uzyskać |
|-------------|-----------------|
| `admin-key` | Na stronie **klucze** usługi Azure wyszukiwanie poznawcze.  |
| `api-version` | Pozostaw jako **2020-06-30**. |
| `datasource-name` | Pozostaw jako **Hotel-Recenzje — ds**. | 
| `indexer-name` | Pozostaw jako **Hotel-Recenzje — IXR**. | 
| `index-name` | Pozostaw jako **Hotel-Recenzje-IX**. | 
| `search-service-name` | Nazwa usługi Azure Wyszukiwanie poznawcze. Adres URL to `https://{{search-service-name}}.search.windows.net` . | 
| `skillset-name` | Pozostaw jako **Hotel-Recenzje — SS**. | 
| `storage-account-name` | Nazwa konta magazynu. | 
| `storage-connection-string` | Na koncie magazynu na karcie **klucze dostępu** wybierz pozycję **Klucz1**  >  **Parametry połączenia**. | 
| `storage-container-name` | Pozostaw jako **hotelowe przeglądy**. | 

### <a name="review-the-request-collection-in-postman"></a>Przeglądanie kolekcji żądań w programie Poster

Podczas tworzenia sklepu z bazami danych należy wydać cztery żądania HTTP: 

- **Umieść żądanie utworzenia indeksu**: ten indeks zawiera dane używane przez usługę Azure wyszukiwanie poznawcze i zwraca.
- **Opublikuj żądanie utworzenia źródła danych**: to źródło danych łączy zachowanie wyszukiwanie poznawcze platformy Azure z kontem magazynu danych i magazynu informacji. 
- **Umieść żądanie utworzenia zestawu umiejętności**: zestawu umiejętności określa wzbogacenia, które są stosowane do danych i struktury sklepu wiedzy.
- **Umieść żądanie utworzenia indeksatora**: uruchomienie indeksatora odczytuje dane, stosuje zestawu umiejętności i przechowuje wyniki. Należy uruchomić to żądanie jako ostatnie.

[Kod źródłowy](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) zawiera kolekcję programu Poster, która ma cztery żądania. Aby wystawić żądania, w programie Poster wybierz kartę dla żądania. Następnie Dodaj `api-key` i `Content-Type` Żądaj nagłówków. Ustaw wartość `api-key` na `{{admin-key}}` . Ustaw wartość `Content-type` na `application/json` . 

![Zrzut ekranu przedstawiający interfejs autora dla nagłówków](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> Należy ustawić `api-key` i `Content-type` nagłówki we wszystkich żądaniach. Jeśli program Poster rozpoznaje zmienną, zmienna pojawia się w kolorze pomarańczowym, tak jak `{{admin-key}}` w przypadku poprzedniego zrzutu ekranu. Jeśli zmienna jest błędna, pojawia się na czerwono.
>

## <a name="create-an-azure-cognitive-search-index"></a>Tworzenie indeksu usługi Azure Cognitive Search

Utwórz indeks Wyszukiwanie poznawcze platformy Azure, aby reprezentować dane, które interesują się wyszukiwaniem, filtrowaniem i stosowaniem ulepszeń. Utwórz indeks, wydając żądanie PUT `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}` . Program pocztowy zastępuje symbole, które są ujęte w podwójne nawiasy klamrowe (takie jak `{{search-service-name}}` , `{{index-name}}` i `{{api-version}}` ) wartościami ustawionymi w [konfiguracji](#configure-postman). Jeśli używasz innego narzędzia do wystawiania poleceń REST, musisz samodzielnie zastąpić te zmienne.

Ustaw strukturę indeksu Wyszukiwanie poznawcze platformy Azure w treści żądania. W programie Poster po ustawieniu `api-key` `Content-type` nagłówków i przejdź do okienka **Body** żądania. Powinien zostać wyświetlony poniższy kod JSON. Jeśli nie, wybierz pozycję **RAW**  >  **JSON (Application/JSON)**, a następnie wklej następujący kod jako treść:

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "address", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "categories", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "city", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "country", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "latitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "longitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "postalCode", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "province", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_dateAdded", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "metadata_storage_content_type", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_size", "type": "Edm.Int64", "searchable": false, "filterable": false, "sortable": false, "facetable": false},
        { "name": "metadata_storage_last_modified", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_name", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_path", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "Sentiment", "type": "Collection(Edm.Double)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "Language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "Keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}

```

Ta definicja indeksu jest kombinacją danych, które chcesz przedstawić użytkownikowi (nazwa hotelu, zawartość do przeglądu, Data), metadane wyszukiwania i dane ulepszeń AI (tonacji, frazy kluczowe i język).

Wybierz pozycję **Wyślij** , aby wydać żądanie Put. Powinien zostać wyświetlony stan `201 - Created` . Jeśli zobaczysz inny stan, w okienku **treść** Wyszukaj odpowiedź JSON, która zawiera komunikat o błędzie. 

## <a name="create-the-datasource"></a>Tworzenie źródła danych

Następnie połącz usługę Azure Wyszukiwanie poznawcze z danymi hotelu zapisanymi w usłudze BLOB Storage. Aby utworzyć źródło danych, Wyślij żądanie POST do `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}` . Należy ustawić `api-key` i `Content-Type` nagłówki. 

W programie Poster przejdź do żądania **Create DataSource** , a następnie do okienka **Body (treść** ). Powinien zostać wyświetlony następujący kod:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Wybierz pozycję **Wyślij** , aby wydać żądanie post. 

## <a name="create-the-skillset"></a>Tworzenie zestawu umiejętności 

Następnym krokiem jest określenie zestawu umiejętności, który określa ulepszenia do zastosowania oraz magazyn wiedzy, w którym będą przechowywane wyniki. W programie Poster wybierz kartę **Tworzenie zestawu umiejętności** . To żądanie wysyła do `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}` . Ustaw `api-key` nagłówki i `Content-type` tak jak wcześniej. 

Istnieją dwa duże obiekty najwyższego poziomu: `skills` i `knowledgeStore` . Każdy obiekt wewnątrz `skills` obiektu jest usługą wzbogacania. Każda usługa wzbogacania ma `inputs` i `outputs` . `LanguageDetectionSkill`Ma wynik `targetName` `Language` . Wartość tego węzła jest używana przez większość innych umiejętności jako dane wejściowe. Źródło to `document/Language` . Możliwość używania danych wyjściowych z jednego węzła jako dane wejściowe do innego jest jeszcze bardziej oczywista w `ShaperSkill` , co określa sposób, w jaki dane są przepływane do tabel w sklepie z bazami danych.

`knowledge_store`Obiekt nawiązuje połączenie z kontem magazynu za pośrednictwem `{{storage-connection-string}}` zmiennej programu Poster. `knowledge_store` zawiera zestaw mapowań między rozszerzonym dokumentem i tabelami oraz kolumnami w sklepie z bazami danych. 

Aby wygenerować zestawu umiejętności, wybierz przycisk **Wyślij** w ogłoszeniu w celu umieszczenia żądania:

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, extract key phrases, and detect sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "score", "targetName": "Sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "Language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "Keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                { 
                    "name": "pages",
                    "sourceContext": "/document/reviews_text/pages/*",
                    "inputs": [
                        { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment" },
                        { "name": "LanguageCode", "source": "/document/Language" },
                        { "name": "Page", "source": "/document/reviews_text/pages/*" },
                        { 
                            "name": "keyphrase", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                            "inputs": [
                                { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                            ]
                        }
                    ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviewsDocument", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviewsPages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviewsKeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyphrase/*" },
                    { "tableName": "hotelReviewsSentiment", "generatedKeyName": "Sentimentid", "source": "/document/tableprojection/pages/*/sentiment/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviewsInlineDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlinePages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment"},
                            { "name": "LanguageCode", "source": "/document/Language"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlineKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="create-the-indexer"></a>Tworzenie indeksatora

Ostatnim krokiem jest utworzenie indeksatora. Indeksator odczytuje dane i aktywuje zestawu umiejętności. W obszarze Ogłoś wybierz żądanie **utworzenia indeksatora** , a następnie przejrzyj treść. Definicja indeksatora odwołuje się do kilku innych już utworzonych zasobów: źródła danych, indeksu i zestawu umiejętności. 

`parameters/configuration`Obiekt steruje sposobem pozyskiwania danych przez indeksator. W takim przypadku dane wejściowe znajdują się w jednym dokumencie zawierającym wiersz nagłówka i wartości rozdzielane przecinkami. Klucz dokumentu jest unikatowym identyfikatorem dla dokumentu. Przed kodowaniem klucz dokumentu jest adresem URL dokumentu źródłowego. Na koniec zestawu umiejętności wartości wyjściowe, takie jak kod języka, tonacji i kluczowe frazy, są mapowane na ich lokalizacje w dokumencie. Chociaż istnieje pojedyncza wartość dla `Language` , `Sentiment` jest stosowana do każdego elementu w tablicy `pages` . `Keyphrases` jest tablicą, która również jest stosowana do każdego elementu w `pages` tablicy.

Po ustawieniu `api-key` i `Content-type` nagłówku i upewnieniu się, że treść żądania jest podobna do poniższego kodu źródłowego, wybierz pozycję **Wyślij** w ogłoszeniu. Po wysłaniu żądania PUT do `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}` . Usługa Azure Wyszukiwanie poznawcze tworzy i uruchamia indeksator. 

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="run-the-indexer"></a>Uruchamianie indeksatora 

W Azure Portal przejdź do strony **Przegląd** usługi Azure wyszukiwanie poznawcze. Wybierz kartę **indeksatory** , a następnie wybierz pozycję **Hotele-Recenzje-IXR**. Jeśli indeksator nie został jeszcze uruchomiony, wybierz pozycję **Uruchom**. Zadanie indeksowania może zgłosić pewne ostrzeżenia dotyczące rozpoznawania języka. Dane obejmują pewne przeglądy, które są zapisywane w językach, które nie są jeszcze obsługiwane przez umiejętności poznawcze. 

## <a name="next-steps"></a>Następne kroki

Po dodaniu danych przy użyciu Cognitive Services i zaprezentowania wyników w sklepie z bazami informacji można użyć Eksplorator usługi Storage lub Power BI do eksplorowania wzbogaconego zestawu danych.

Aby dowiedzieć się, jak eksplorować ten magazyn wiedzy przy użyciu Eksplorator usługi Storage, zobacz ten przewodnik:

> [!div class="nextstepaction"]
> [Wyświetlanie przy użyciu Eksploratora usługi Storage](knowledge-store-view-storage-explorer.md)

Aby dowiedzieć się, jak połączyć ten magazyn wiedzy z Power BI, zobacz ten przewodnik:

> [!div class="nextstepaction"]
> [Łączenie z usługą Power BI](knowledge-store-connect-power-bi.md)

Jeśli chcesz powtórzyć to ćwiczenie lub spróbować użyć innego instruktażu do wzbogacania, Usuń usługę **Hotel-Recenzje-idxr** indeksator. Usunięcie indeksatora resetuje bezpłatny dzienny licznik transakcji do zera.