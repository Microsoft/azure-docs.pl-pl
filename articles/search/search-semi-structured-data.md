---
title: 'Samouczek: indeksowanie danych z częściową strukturą w obiektach Blob JSON'
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak indeksować i przeszukiwać rozbudowane obiekty blob w formacie JSON platformy Azure przy użyciu usługi Azure Wyszukiwanie poznawcze API REST i programu Poster.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: 7c88aea6aff942cdcf5cbc022df8f07cfe0d4cce
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701283"
---
# <a name="tutorial-index-json-blobs-from-azure-storage-using-rest"></a>Samouczek: indeksowanie obiektów BLOB JSON z usługi Azure Storage przy użyciu interfejsu REST

Usługa Azure Wyszukiwanie poznawcze umożliwia indeksowanie dokumentów i tablic w formacie JSON w usłudze Azure Blob Storage za pomocą [indeksatora](search-indexer-overview.md) , który wie, jak odczytywać dane z częściową strukturą. Częściowo ustrukturyzowane dane zawierają tagi lub oznaczenia, które dzielą zawartość w ramach danych. Dzieli różnicę między danymi niestrukturalnymi, które muszą być w pełni indeksowane i formalnie uporządkowane dane, które są zgodne z modelem danych, takim jak schemat relacyjnej bazy danych, który można indeksować według poszczególnych pól.

W tym samouczku jest używany program Poster i [interfejsy API REST wyszukiwania](/rest/api/searchservice/) do wykonywania następujących zadań:

> [!div class="checklist"]
> * Konfigurowanie źródła danych Wyszukiwanie poznawcze platformy Azure dla kontenera obiektów blob platformy Azure
> * Utwórz indeks Wyszukiwanie poznawcze platformy Azure, aby zawierał zawartość z możliwością przeszukiwania
> * Konfigurowanie i uruchamianie indeksatora w celu odczytu kontenera i wyodrębnienia zawartości możliwej do przeszukania z usługi Azure Blob Storage
> * Przeszukać utworzony indeks

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

+ [Azure Storage](../storage/common/storage-account-create.md)
+ [Aplikacja klasyczna narzędzia Postman](https://www.getpostman.com/)
+ [Utwórz](search-create-service-portal.md) lub [Znajdź istniejącą usługę wyszukiwania](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Możesz skorzystać z bezpłatnej usługi dla tego samouczka. Bezpłatna usługa wyszukiwania ogranicza do trzech indeksów, trzech indeksatorów i trzech źródeł danych. W ramach tego samouczka tworzony jest jeden element każdego z tych typów. Przed rozpoczęciem upewnij się, że masz miejsce w usłudze, aby akceptować nowe zasoby.

## <a name="download-files"></a>Pobieranie plików

[Clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) zawiera dane używane w tym samouczku. Pobierz i rozpakuj ten plik do własnego folderu. Dane pochodzą z [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results), konwertowane na format JSON dla tego samouczka.

## <a name="1---create-services"></a>1 — Tworzenie usług

Ten samouczek używa platformy Azure Wyszukiwanie poznawcze do indeksowania i zapytań oraz do dostarczania danych przez usługę Azure Blob Storage. 

Jeśli to możliwe, Utwórz zarówno w tym samym regionie, jak i w grupie zasobów, co umożliwia bliskość i łatwość zarządzania. W tym przypadku konto usługi Azure Storage może znajdować się w dowolnym regionie.

### <a name="start-with-azure-storage"></a>Rozpoczynanie pracy z usługą Azure Storage

1. [Zaloguj się do Azure Portal](https://portal.azure.com/) i kliknij pozycję **+ Utwórz zasób**.

1. Wyszukaj *konto magazynu* i wybierz ofertę konta magazynu firmy Microsoft.

   :::image type="content" source="media/cognitive-search-tutorial-blob/storage-account.png" alt-text="Utwórz konto magazynu" border="false":::

1. Na karcie podstawowe wymagane są następujące elementy. Zaakceptuj wartości domyślne dla wszystkich innych elementów.

   + **Grupa zasobów**. Wybierz istniejący lub Utwórz nowy, ale Użyj tej samej grupy dla wszystkich usług, aby można było zarządzać nimi zbiorczo.

   + **Nazwa konta magazynu**. Jeśli uważasz, że może istnieć wiele zasobów tego samego typu, użyj nazwy, aby odróżnić według typu i regionu, na przykład *blobstoragewestus*. 

   + **Lokalizacja**. Jeśli to możliwe, wybierz tę samą lokalizację, która jest używana dla usługi Azure Wyszukiwanie poznawcze i Cognitive Services. Pojedyncza lokalizacja unieważnia opłaty za przepustowość.

   + **Rodzaj konta**. Wybierz wartość domyślną *StorageV2 (ogólnego przeznaczenia w wersji 2)*.

1. Kliknij przycisk **Przegląd + Utwórz** , aby utworzyć usługę.

1. Po jego utworzeniu kliknij pozycję **Przejdź do zasobu** , aby otworzyć stronę przegląd.

1. Kliknij pozycję **obiekty blob** usługa.

1. [Utwórz kontener obiektów BLOB](../storage/blobs/storage-quickstart-blobs-portal.md) , aby zawierał przykładowe dane. Można ustawić poziom dostępu publicznego na dowolną z jego prawidłowych wartości.

1. Po utworzeniu kontenera Otwórz go i wybierz pozycję **Przekaż** na pasku poleceń.

   :::image type="content" source="media/search-semi-structured-data/upload-command-bar.png" alt-text="Przekaż na pasku poleceń" border="false":::

1. Przejdź do folderu zawierającego pliki przykładowe. Zaznacz wszystkie z nich, a następnie kliknij przycisk **Przekaż**.

   :::image type="content" source="media/search-semi-structured-data/clinicalupload.png" alt-text="Przekazywanie plików" border="false":::

Po zakończeniu przekazywania pliki powinny pojawić się w podfolderze wewnątrz kontenera danych.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Następnym zasobem jest usługa Azure Wyszukiwanie poznawcze, którą można [utworzyć w portalu](search-create-service-portal.md). Aby ukończyć ten przewodnik, możesz skorzystać z warstwy Bezpłatna. 

Tak jak w przypadku usługi Azure Blob Storage, poświęć chwilę na zebranie klucza dostępu. Ponadto po rozpoczęciu tworzenia struktury żądań należy podać punkt końcowy i klucz interfejsu API administratora używany do uwierzytelniania każdego żądania.

### <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona razem z usługą, więc jeśli do subskrypcji dodano Wyszukiwanie poznawcze platformy Azure, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Uzyskaj adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W obszarze **Ustawienia**  >  **klucze** Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

:::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="Pobieranie punktu końcowego HTTP i klucza dostępu" border="false":::

Wszystkie żądania wymagają klucza API dla każdego żądania wysyłanego do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="2---set-up-postman"></a>2 — Konfigurowanie wpisu

Uruchom narzędzie Postman i skonfiguruj żądanie HTTP. Jeśli nie znasz tego narzędzia, zobacz [Eksplorowanie interfejsów API REST platformy Azure wyszukiwanie poznawcze](search-get-started-rest.md).

Metody żądań dla każdego wywołania w tym samouczku są **ogłaszane** i **odbierane**. Aby utworzyć źródło danych, indeks i indeksator, należy wykonać trzy wywołania interfejsu API do usługi wyszukiwania. Źródło danych zawiera wskaźnik do konta magazynu i danych JSON. Usługa wyszukiwania nawiązuje połączenie podczas ładowania danych.

W obszarze nagłówki ustaw wartość "Content-Type" na wartość `application/json` i ustaw `api-key` na klucz Admin API-Key usługi Azure wyszukiwanie poznawcze. Po ustawieniu nagłówków można używać ich dla każdego żądania w tym ćwiczeniu.

  :::image type="content" source="media/search-get-started-rest/postman-url.png" alt-text="Adres URL i nagłówek żądania post" border="false":::

Identyfikatory URI muszą określać wersję interfejsu API i każde wywołanie powinno zwrócić **201**. Ogólnie dostępna wersja interfejsu API do korzystania z tablic JSON to `2020-06-30` .

## <a name="3---create-a-data-source"></a>3 — Tworzenie źródła danych

[Interfejs API tworzenia źródła danych](/rest/api/searchservice/create-data-source) tworzy obiekt wyszukiwanie poznawcze platformy Azure, który określa, jakie dane mają być indeksowane.

1. Ustaw punkt końcowy tego wywołania `https://[service name].search.windows.net/datasources?api-version=2020-06-30` . Zastąp element `[service name]` nazwą usługi wyszukiwania. 

1. Skopiuj poniższy kod JSON do treści żądania.

    ```json
    {
        "name" : "clinical-trials-json-ds",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
        "container" : { "name" : "[blob container name]"}
    }
    ```

1. Zastąp ciąg połączenia prawidłowym ciągiem dla Twojego konta.

1. Zastąp ciąg "[nazwa kontenera obiektów BLOB]" kontenerem utworzonym dla przykładowych danych. 

1. Wyślij żądanie. Odpowiedź powinna wyglądać następująco:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
        "@odata.etag": "\"0x8D505FBC3856C9E\"",
        "name": "clinical-trials-json-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
        },
        "container": {
            "name": "[mycontainernamehere]",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

## <a name="4---create-an-index"></a>4 — Tworzenie indeksu
    
Drugie wywołanie to [Tworzenie interfejsu API indeksu](/rest/api/searchservice/create-index), tworzenie indeksu wyszukiwanie poznawcze platformy Azure, w którym są przechowywane wszystkie dane z możliwością wyszukiwania. Indeks określa wszystkie parametry i ich atrybuty.

1. Ustaw punkt końcowy tego wywołania `https://[service name].search.windows.net/indexes?api-version=2020-06-30` . Zastąp element `[service name]` nazwą usługi wyszukiwania.

1. Skopiuj poniższy kod JSON do treści żądania.

    ```json
    {
      "name": "clinical-trials-json-index",  
      "fields": [
      {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
      {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
      {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
      {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
      {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
      {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
      {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
      {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
      {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
      {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
      {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
      {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
      {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
      {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
      {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
      ]
    }
   ```

1. Wyślij żądanie. Odpowiedź powinna wyglądać następująco:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag": "\"0x8D505FC00EDD5FA\"",
        "name": "clinical-trials-json-index",
        "fields": [
            {
                "name": "FileName",
                "type": "Edm.String",
                "searchable": false,
                "filterable": false,
                "retrievable": true,
                "sortable": true,
                "facetable": false,
                "key": false,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            {
                "name": "Description",
                "type": "Edm.String",
                "searchable": true,
                "filterable": false,
                "retrievable": false,
                "sortable": false,
                "facetable": false,
                "key": false,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            ...
          }
    ```

## <a name="5---create-and-run-an-indexer"></a>5 — Tworzenie i uruchamianie indeksatora

Indeksator nawiązuje połączenie ze źródłem danych, importuje dane do docelowego indeksu wyszukiwania i opcjonalnie udostępnia harmonogram do automatyzowania odświeżania danych. Interfejs API REST służy do [tworzenia indeksatora](/rest/api/searchservice/create-indexer).

1. Ustaw identyfikator URI dla tego wywołania `https://[service name].search.windows.net/indexers?api-version=2020-06-30` . Zastąp element `[service name]` nazwą usługi wyszukiwania.

1. Skopiuj poniższy kod JSON do treści żądania.

    ```json
    {
      "name" : "clinical-trials-json-indexer",
      "dataSourceName" : "clinical-trials-json-ds",
      "targetIndexName" : "clinical-trials-json-index",
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
    ```

1. Wyślij żądanie. Żądanie jest przetwarzane od razu. Po powrocie odpowiedzi odpowiedź będzie miała indeks, który jest przeszukiwany w trybie pełnotekstowym. Odpowiedź powinna wyglądać następująco:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
        "@odata.etag": "\"0x8D505FDE143D164\"",
        "name": "clinical-trials-json-indexer",
        "description": null,
        "dataSourceName": "clinical-trials-json-ds",
        "targetIndexName": "clinical-trials-json-index",
        "schedule": null,
        "parameters": {
            "batchSize": null,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonArray"
            }
        },
        "fieldMappings": [],
        "enrichers": [],
        "disabled": null
    }
    ```

## <a name="6---search-your-json-files"></a>6 — wyszukiwanie plików JSON

Możesz rozpocząć wyszukiwanie zaraz po załadowaniu pierwszego dokumentu.

1. Zmień zlecenie, aby **uzyskać**.

1. Ustaw identyfikator URI dla tego wywołania `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&api-version=2020-06-30&$count=true` . Zastąp element `[service name]` nazwą usługi wyszukiwania.

1. Wyślij żądanie. Jest to nieokreślone zapytanie wyszukiwania pełnotekstowego, które zwraca wszystkie pola oznaczone jako możliwy do pobierania w indeksie oraz liczbę dokumentów. Odpowiedź powinna wyglądać następująco:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/indexes('clinical-trials-json-index')/$metadata#docs(*)",
        "@odata.count": 100,
        "value": [
            {
                "@search.score": 1.0,
                "FileName": "NCT00000102.txt",
                "MinimumAge": 14,
                "Title": "Congenital Adrenal Hyperplasia: Calcium Channels as Therapeutic Targets",
                "MyURL": "https://azure.storagedemos.com/clinical-trials/NCT00000102.txt",
                "Gender": "Both",
                "MaximumAge": 35,
                "Summary": "This study will test the ability of extended release nifedipine (Procardia XL), a blood pressure medication, to permit a decrease in the dose of glucocorticoid medication children take to treat congenital adrenal hyperplasia (CAH).",
                "NCTID": "NCT00000102",
                "Phase": "Phase 1/Phase 2",
                "Date": "ClinicalTrials.gov processed this data on October 25, 2016",
                "OverallStatus": "Completed",
                "OrgStudyId": "NCRR-M01RR01070-0506",
                "HealthyVolunteers": "No",
                "Keywords": [],
                "metadata_storage_last_modified": "2019-04-09T18:16:24Z",
                "metadata_storage_size": "33060",
                "metadata_content_type": null
            },
            . . . 
    ```

1. Dodaj `$select` parametr zapytania, aby ograniczyć wyniki do mniejszej liczby pól: `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&$select=Gender,metadata_storage_size&api-version=2020-06-30&$count=true` .  W przypadku tego zapytania 100 dokumenty są zgodne, ale domyślnie usługa Azure Wyszukiwanie poznawcze zwraca wartość 50 w wynikach.

   :::image type="content" source="media/search-semi-structured-data/lastquery.png" alt-text="Zapytanie sparametryzowane" border="false":::

1. Przykładem bardziej złożonej kwerendy może być `$filter=MinimumAge ge 30 and MaximumAge lt 75` Funkcja, która zwraca tylko wyniki, w przypadku których minimalny stopień ważności parametrów jest większy lub równy 30, a wartość maksymalna jest mniejsza niż 75. Zamień `$select` wyrażenie na `$filter` wyrażenie.

   :::image type="content" source="media/search-semi-structured-data/metadatashort.png" alt-text="Wyszukiwanie częściowo ustrukturyzowane" border="false":::

Można również użyć operatorów logicznych (i, not) i operatorów porównania (EQ, ne, gt, lt, GE, Le). W porównaniach ciągów jest rozróżniana wielkość liter. Aby uzyskać więcej informacji i przykładów, zobacz [Tworzenie prostego zapytania](search-query-simple-examples.md).

> [!NOTE]
> Parametr `$filter` działa wyłącznie z metadanymi, które podczas tworzenia indeksu zostały oznaczone jako możliwe do filtrowania.

## <a name="reset-and-rerun"></a>Resetowanie i ponowne uruchamianie

W przypadku wczesnych eksperymentalnych etapów tworzenia najlepszym podejściem do iteracji projektu jest usunięcie obiektów z platformy Azure Wyszukiwanie poznawcze i umożliwienie kodowi odbudowania. Nazwy zasobów są unikatowe. Usunięcie obiektu umożliwia jego ponowne utworzenie przy użyciu tej samej nazwy.

Możesz użyć portalu, aby usunąć indeksy, indeksatory i źródła danych. Lub Użyj **Usuń** i podaj adresy URL do każdego obiektu. Następujące polecenie usuwa indeksator.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/clinical-trials-json-indexer?api-version=2020-06-30
```

W przypadku pomyślnego usunięcia jest zwracany kod stanu 204.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy Pracujesz w ramach własnej subskrypcji, na końcu projektu warto usunąć zasoby, które nie są już potrzebne. Uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku wszystkie zasoby lub grupy zasobów w okienku nawigacji po lewej stronie.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już podstawy indeksowania obiektów blob platformy Azure, przyjrzyjmy się bliżej konfiguracji indeksatora dla obiektów BLOB JSON w usłudze Azure Storage.

> [!div class="nextstepaction"]
> [Konfigurowanie indeksowania obiektów BLOB JSON](search-howto-index-json-blobs.md)