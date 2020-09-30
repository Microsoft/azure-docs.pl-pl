---
title: 'Samouczek: Python i AI za pośrednictwem obiektów blob platformy Azure'
titleSuffix: Azure Cognitive Search
description: Przechodzenie między przykładem wyodrębniania tekstu i przetwarzania języka naturalnego za pośrednictwem zawartości w usłudze BLOB Storage przy użyciu notesu Python Jupyter i interfejsów API REST platformy Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 09/25/2020
ms.custom: devx-track-python
ms.openlocfilehash: 34265552122c1f8d1bcbbcfe95948683a5750a71
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91531007"
---
# <a name="tutorial-use-python-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Samouczek: używanie języków Python i AI do generowania zawartości z możliwością wyszukiwania z obiektów blob platformy Azure

Jeśli w usłudze Azure Blob Storage znajduje się tekst lub obrazy bez struktury, [potok wzbogacenia AI](cognitive-search-concept-intro.md) może wyodrębnić informacje i utworzyć nową zawartość, która jest przydatna w przypadku wyszukiwania pełnotekstowego lub scenariuszy wyszukiwania w bazie wiedzy. Mimo że potok może przetwarzać obrazy, ten samouczek w języku Python koncentruje się na tekście, stosowaniu wykrywania języka i przetwarzania języka naturalnego w celu utworzenia nowych pól, których można użyć w zapytaniach, aspektach i filtrach.

Ten samouczek używa języka Python i [interfejsów API REST wyszukiwania](/rest/api/searchservice/) do wykonywania następujących zadań:

> [!div class="checklist"]
> * Zacznij od całego dokumentu (tekst bez struktury), takiego jak PDF, HTML, DOCX i PPTX, w usłudze Azure Blob Storage.
> * Zdefiniuj potok, który wyodrębnia tekst, wykrywa język, rozpoznaje jednostki i wykrywa kluczowe frazy.
> * Zdefiniuj indeks do przechowywania danych wyjściowych (nieprzetworzona zawartość oraz pary nazwa-wartość w postaci potoku).
> * Wykonaj potok, aby rozpocząć transformacje i analizę oraz utworzyć i załadować indeks.
> * Eksplorowanie wyników przy użyciu wyszukiwania pełnotekstowego i zaawansowanej składni zapytań.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Otwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Anaconda 3,7](https://www.anaconda.com/distribution/#download-section)
+ [Utwórz](search-create-service-portal.md) lub [Znajdź istniejącą usługę wyszukiwania](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Możesz skorzystać z bezpłatnej usługi dla tego samouczka. Bezpłatna usługa wyszukiwania ogranicza do trzech indeksów, trzech indeksatorów i trzech źródeł danych. W ramach tego samouczka tworzony jest jeden element każdego z tych typów. Przed rozpoczęciem upewnij się, że masz miejsce w usłudze, aby akceptować nowe zasoby.

## <a name="download-files"></a>Pobieranie plików

1. Otwórz ten [folder w usłudze OneDrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) i w lewym górnym rogu, kliknij pozycję **Pobierz** , aby skopiować pliki do komputera. 

1. Kliknij prawym przyciskiem myszy plik zip i wybierz polecenie **Wyodrębnij wszystko**. Istnieje 14 plików różnych typów. W tym ćwiczeniu należy użyć 7.

## <a name="1---create-services"></a>1 — Tworzenie usług

Ten samouczek używa usługi Azure Wyszukiwanie poznawcze do indeksowania i zapytań, Cognitive Services w zapleczu do wzbogacania AI i usługi Azure Blob Storage, aby zapewnić dane. Ten samouczek jest objęty bezpłatną alokacją 20 transakcji na indeksator dziennie na Cognitive Services, więc jedyne usługi, które należy utworzyć, to wyszukiwanie i magazynowanie.

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

1. Kliknij pozycję **+ kontener** , aby utworzyć kontener i nadaj mu nazwę *koło zębate-Search-demonstracyjn*.

1. Wybierz pozycję *koło zębate-Search-demonstracyjny* , a następnie kliknij pozycję **Przekaż** , aby otworzyć folder, w którym zapisano pliki do pobrania. Zaznacz wszystkie pliki niebędące obrazami. Należy mieć 7 plików. Kliknij przycisk **OK** , aby przekazać.

   :::image type="content" source="media/cognitive-search-tutorial-blob/sample-files.png" alt-text="Utwórz konto magazynu" border="false":::

1. Przed opuszczeniem usługi Azure Storage należy uzyskać parametry połączenia, aby można było sformułować połączenie w usłudze Azure Wyszukiwanie poznawcze. 

   1. Przejdź z powrotem do strony Przegląd Twojego konta magazynu (na przykład użyto *blobstragewestus* ). 
   
   1. W okienku nawigacji po lewej stronie wybierz pozycję **klucze dostępu** i skopiuj jeden z parametrów połączenia. 

   Ciąg połączenia jest adresem URL podobnym do poniższego przykładu:

      ```http
      DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Zapisz parametry połączenia w Notatniku. Będzie ona potrzebna później podczas konfigurowania połączenia ze źródłem danych.

### <a name="cognitive-services"></a>Cognitive Services

Wzbogacanie AI jest obsługiwane przez Cognitive Services, w tym analiza tekstu i przetwarzanie obrazów dla przetwarzania w języku naturalnym i obrazie. Jeśli celem było ukończenie rzeczywistego prototypu lub projektu, w tym momencie należy zastanowić się, Cognitive Services (w tym samym regionie co usługa Azure Wyszukiwanie poznawcze), aby można było dołączyć go do operacji indeksowania.

Ponieważ w tym samouczku są stosowane tylko 7 transakcji, można pominąć obsługę zasobów, ponieważ usługa Azure Wyszukiwanie poznawcze może połączyć się z Cognitive Services na potrzeby 20 bezpłatnych transakcji na indeksator. Dostępna jest bezpłatna alokacja. W przypadku większych projektów Zaplanuj obsługę Cognitive Services w warstwie płatność zgodnie z rzeczywistym użyciem. Aby uzyskać więcej informacji, zobacz [Attach Cognitive Services](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Trzeci składnik to Wyszukiwanie poznawcze platformy Azure, który można [utworzyć w portalu](search-create-service-portal.md). Możesz skorzystać z warstwy Bezpłatna, aby wykonać tę procedurę. 

Tak jak w przypadku usługi Azure Blob Storage, poświęć chwilę na zebranie klucza dostępu. Ponadto po rozpoczęciu tworzenia struktury żądań należy podać punkt końcowy i klucz interfejsu API administratora używany do uwierzytelniania każdego żądania.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Pobierz klucz API i adres URL administratora dla usługi Azure Wyszukiwanie poznawcze

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Pobierz nazwę usługi wyszukiwania. Nazwę usługi można potwierdzić, przeglądając adres URL punktu końcowego. Jeśli adres URL punktu końcowego to `https://mydemo.search.windows.net` , nazwa usługi to `mydemo` .

2. W obszarze **Ustawienia**  >  **klucze**Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

   Pobierz również klucz zapytania. Najlepszym rozwiązaniem jest wydawanie żądań zapytań z dostępem tylko do odczytu.

   :::image type="content" source="media/search-get-started-nodejs/service-name-and-keys.png" alt-text="Utwórz konto magazynu" border="false":::

Wszystkie żądania wymagają klucza API-Key w nagłówku każdego żądania wysyłanego do usługi. Prawidłowy klucz ustanawia zaufanie dla każdego żądania, między aplikacją wysyłającą żądanie a usługą, która go obsługuje.

## <a name="2---start-a-notebook"></a>2 — Uruchamianie notesu

Utwórz Notes, wykonując poniższe instrukcje, lub Pobierz gotowy Notes z [repozytorium Azure-Search-Python-Samples](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment).

Użyj nawigatora Anaconda, aby uruchomić Jupyter Notebook i utworzyć nowy Notes Python 3.

W Twoim notesie Uruchom ten skrypt, aby załadować biblioteki używane do pracy z JSON i formułowania żądań HTTP.

```python
import json
import requests
from pprint import pprint
```

W tym samym notesie Zdefiniuj nazwy dla źródła danych, indeksu, indeksatora i zestawu umiejętności. Uruchom ten skrypt, aby skonfigurować nazwy dla tego samouczka.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

W poniższym skrypcie Zastąp symbole zastępcze usługi wyszukiwania (nazwa użytkownika-SEARCH-SERVICE-NAME) i klucz interfejsu API administratora (klucz-ADMIN-API-KEY), a następnie uruchom go, aby skonfigurować punkt końcowy usługi wyszukiwania.

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2020-06-30'
}
```

## <a name="3---create-the-pipeline"></a>3 — Tworzenie potoku

W przypadku usługi Azure Wyszukiwanie poznawcze przetwarzanie AI odbywa się podczas indeksowania (lub pozyskiwania danych). Ta część instruktażu tworzy cztery obiekty: Data Source, index Definition, zestawu umiejętności, indeksator. 

### <a name="step-1-create-a-data-source"></a>Krok 1. Tworzenie źródła danych

[Obiekt źródła danych](/rest/api/searchservice/create-data-source) dostarcza parametry połączenia z kontenerem obiektów BLOB zawierającym pliki.

W poniższym skrypcie Zastąp symbol zastępczy obiektu-BLOB-RESOURCE-CONNECTION-String parametrami połączenia dla obiektu BLOB utworzonego w poprzednim kroku. Zamień tekst zastępczy dla kontenera. Następnie uruchom skrypt, aby utworzyć źródło danych o nazwie `cogsrch-py-datasource` .

```python
# Create a data source
datasourceConnectionString = "<YOUR-BLOB-RESOURCE-CONNECTION-STRING>"
datasource_payload = {
    "name": datasource_name,
    "description": "Demo files to demonstrate cognitive search capabilities.",
    "type": "azureblob",
    "credentials": {
        "connectionString": datasourceConnectionString
    },
    "container": {
        "name": "<YOUR-BLOB-CONTAINER-NAME>"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

Żądanie powinno zwrócić kod stanu 201 potwierdzenie sukcesu.

Na Azure Portal na stronie Pulpit nawigacyjny usługi wyszukiwania Sprawdź, czy na liście **źródła danych** jest wyświetlana wartość cogsrch-PR-DataSource. Kliknij przycisk **Odśwież** , aby zaktualizować stronę.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-data-source-tile.png" alt-text="Utwórz konto magazynu" border="false":::

### <a name="step-2-create-a-skillset"></a>Krok 2. Tworzenie elementu zestawu umiejętności

W tym kroku zdefiniujesz zestaw kroków wzbogacania, które zostaną zastosowane do danych. Każdy krok wzbogacania to *umiejętność*, a zestaw kroków wzbogacenia to *zestaw umiejętności*. W tym samouczku są stosowane [wbudowane umiejętności poznawcze](cognitive-search-predefined-skills.md) dla zestawu umiejętności:

+ [Rozpoznawanie jednostek](cognitive-search-skill-entity-recognition.md) do wyodrębniania nazw organizacji z zawartości kontenera obiektów BLOB.

+ [Wykrywanie języka](cognitive-search-skill-language-detection.md) — identyfikowanie języka zawartości.

+ [Dzielenie tekstu](cognitive-search-skill-textsplit.md) — dzielenie dużej zawartości na mniejsze fragmenty przed wywołaniem umiejętności wyodrębniania fraz kluczowych. Umiejętność wyodrębniania fraz kluczowych przyjmuje dane wejściowe składające się maksymalnie z 50 000 znaków. Kilka przykładowych plików należy podzielić, aby zmieścić się w tym limicie.

+ [Wyodrębnianie kluczowych fraz](cognitive-search-skill-keyphrases.md) — określanie najczęściej występujących fraz kluczowych. 

Uruchom następujący skrypt, aby utworzyć obiekt zestawu umiejętności o nazwie `cogsrch-py-skillset` .

```python
# Create a skillset
skillset_payload = {
    "name": skillset_name,
    "description":
    "Extract entities, detect language and extract key-phrases",
    "skills":
    [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": ["Organization"],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "organizations", 
                    "targetName": "organizations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "languageCode"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "textSplitMode": "pages",
            "maximumPageLength": 4000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/pages/*",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/pages/*"
                },
                {
                    "name": "languageCode", 
                    "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyPhrases"
                }
            ]
        }
    ]
}

r = requests.put(endpoint + "/skillsets/" + skillset_name,
                 data=json.dumps(skillset_payload), headers=headers, params=params)
print(r.status_code)
```

Żądanie powinno zwrócić kod stanu 201 potwierdzenie sukcesu.

Na każdej stronie jest stosowana umiejętność wyodrębniania frazy klucza. Ustawiając kontekst na `"document/pages/*"` , należy uruchomić ten program wzbogacający dla każdego elementu członkowskiego tablicy Document/Pages (dla każdej strony w dokumencie).

Każda umiejętność jest wykonywana dla zawartości dokumentu. Podczas przetwarzania platforma Azure Wyszukiwanie poznawcze pęka każdy dokument w celu odczytania zawartości z różnych formatów plików. Tekst znaleziony w pliku źródłowym jest umieszczany w `content` polu, po jednym dla każdego dokumentu. W związku z tym ustaw wartość wejściową jako `"/document/content"` .

Graficzna reprezentacja zestawu umiejętności jest przedstawiona poniżej.

:::image type="content" source="media/cognitive-search-tutorial-blob/skillset.png" alt-text="Utwórz konto magazynu" border="false":::

Wyniki mogą być mapowane na indeks, używany jako dane wejściowe do poziomu umiejętności podrzędnej, lub w obu przypadkach, podobnie jak w przypadku kodu języka. W indeksie kod języka jest przydatny do filtrowania. Kod języka jest używany jako dane wejściowe przez umiejętności analizy tekstu w celu określenia zasad podziału wyrazów przez reguły językowe.

Aby uzyskać więcej podstawowych informacji na temat zestawów umiejętności, zobacz [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>Krok 3. Tworzenie indeksu

W tej sekcji zdefiniujesz schemat indeksu, określając pola do uwzględnienia w indeksie wyszukiwania i ustawiając atrybuty wyszukiwania dla każdego pola. Pola mogą mieć typ i atrybuty, które określają sposób używania pola (z możliwością wyszukiwania, sortowania itp.). Nazwy pól w indeksie nie muszą dokładnie zgadzać się z nazwami pól w źródle. W kolejnym kroku są dodawane mapowania pól w indeksatorze w celu połączenia pól źródłowych z polami docelowymi. W tym kroku zdefiniuj indeks przy użyciu konwencji nazewnictwa pól właściwej dla aplikacji wyszukiwania.

W tym ćwiczeniu są używane następujące pola i typy pól:

| field-names: | ID         | zawartość   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| field-types: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Uruchom ten skrypt, aby utworzyć indeks o nazwie `cogsrch-py-index` .

```python
# Create an index
index_payload = {
    "name": index_name,
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": "true",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false",
            "sortable": "true"
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": "false",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "languageCode",
            "type": "Edm.String",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "keyPhrases",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "organizations",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "sortable": "false",
            "filterable": "false",
            "facetable": "false"
        }
    ]
}

r = requests.put(endpoint + "/indexes/" + index_name,
                 data=json.dumps(index_payload), headers=headers, params=params)
print(r.status_code)
```

Żądanie powinno zwrócić kod stanu 201 potwierdzenie sukcesu.

Aby dowiedzieć się więcej na temat definiowania indeksu, zobacz [create index (Azure wyszukiwanie POZNAWCZE REST API)](/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>Krok 4. Tworzenie i uruchamianie indeksatora

[Indeksator](/rest/api/searchservice/create-indexer) steruje potokiem. Trzy składniki, które zostały utworzone w tym przypadku (Źródło danych, zestawu umiejętności, indeks), są danymi wejściowymi indeksatora. Tworzenie indeksatora na platformie Azure Wyszukiwanie poznawcze to zdarzenie, które umieszcza cały potok w ruchu. 

Aby powiązać te obiekty ze sobą w indeksatorze, należy zdefiniować mapowania pól.

+ `"fieldMappings"`Są przetwarzane przed zestawu umiejętności, mapując pola źródłowe ze źródła danych do pól docelowych w indeksie. Jeśli nazwy pól i typy są takie same na obu końcach, mapowanie nie jest wymagane.

+ `"outputFieldMappings"`Są przetwarzane po zestawu umiejętności, odwołanie, `"sourceFieldNames"` które nie istnieje, dopóki nie zostaną utworzone pęknięcia lub wzbogacanie dokumentów. `"targetFieldName"`Jest polem w indeksie.

Oprócz przyłączania danych wejściowych do danych wyjściowych, można również używać mapowań pól do spłaszczania struktur. Aby uzyskać więcej informacji, zobacz [Jak mapować wzbogacone pola na indeks wyszukiwania](cognitive-search-output-field-mapping.md).

Uruchom ten skrypt, aby utworzyć indeksator o nazwie `cogsrch-py-indexer` .

```python
# Create an indexer
indexer_payload = {
    "name": indexer_name,
    "dataSourceName": datasource_name,
    "targetIndexName": index_name,
    "skillsetName": skillset_name,
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction":
            {"name": "base64Encode"}
        },
        {
            "sourceFieldName": "content",
            "targetFieldName": "content"
        }
    ],
    "outputFieldMappings":
    [
        {
            "sourceFieldName": "/document/organizations",
            "targetFieldName": "organizations"
        },
        {
            "sourceFieldName": "/document/pages/*/keyPhrases/*",
            "targetFieldName": "keyPhrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "languageCode"
        }
    ],
    "parameters":
    {
        "maxFailedItems": -1,
        "maxFailedItemsPerBatch": -1,
        "configuration":
        {
            "dataToExtract": "contentAndMetadata",
            "imageAction": "generateNormalizedImages"
        }
    }
}

r = requests.put(endpoint + "/indexers/" + indexer_name,
                 data=json.dumps(indexer_payload), headers=headers, params=params)
print(r.status_code)
```

Żądanie powinno zwrócić kod stanu 201 wkrótce, jednak przetwarzanie może potrwać kilka minut. Mimo że zestaw danych jest mały, umiejętności analityczne, takie jak analiza obrazu, są znacznie pracochłonne i czasochłonne.

Możesz [monitorować stan indeksatora](#check-indexer-status) , aby określić, kiedy indeksator jest uruchomiony lub zakończony.

> [!TIP]
> Utworzenie indeksatora powoduje wywołanie potoku. Jeśli wystąpi problem z uzyskaniem dostępu do danych, mapowanie wejść i wyjść lub z kolejnością operacji, pojawi się on na tym etapie. Aby ponownie uruchomić potok ze zmianami kodu lub skryptu, konieczne może być najpierw usunięcie obiektów. Aby uzyskać więcej informacji, zobacz [Resetowanie i ponowne uruchamianie](#reset).

#### <a name="about-the-request-body"></a>Informacje o treści żądania

Skrypt ustawia dla elementu `"maxFailedItems"` wartość -1, która powoduje, że aparat indeksowania ignoruje błędy podczas importowania danych. Jest to przydatne, ponieważ pokazowe źródło danych zawiera tak mało dokumentów. W przypadku większego źródła danych należy ustawić wartość większą od 0.

Ponadto zwróć uwagę na instrukcję `"dataToExtract":"contentAndMetadata"` w parametrach konfiguracji. Ta instrukcja nakazuje Indeksatorowi Wyodrębnienie zawartości z różnych formatów plików i metadanych związanych z każdym plikiem.

Gdy zawartość zostanie wyodrębniona, możesz ustawić element `imageAction`, aby wyodrębnić tekst z obrazów znalezionych w źródle danych. Konfiguracja `"imageAction":"generateNormalizedImages"`, w połączeniu z umiejętnością OCR i umiejętnością scalania tekstu, wskazuje indeksatorowi, aby wyodrębniał tekst z obrazów (np. wyraz „stop” ze znaku drogowego Stop) i osadzał go jako część pola zawartości. To zachowanie dotyczy obrazów osadzonych w dokumentach (obraz w pliku PDF) i obrazów znajdujących się w źródle danych na przykład pliku JPG.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 — monitorowanie indeksowania

Po zdefiniowaniu indeksatora jest on uruchamiany automatycznie przy przesyłaniu żądania. W zależności od tego, które umiejętności poznawcze zdefiniowano, indeksowanie może trwać dłużej, niż oczekujesz. Aby dowiedzieć się, czy przetwarzanie indeksatora zostało zakończone, uruchom następujący skrypt.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

W odpowiedzi Monitoruj `"lastResult"` dla nich `"status"` `"endTime"` wartości i. Należy okresowo uruchamiać skrypt, aby sprawdzić stan. Po zakończeniu indeksatora stan zostanie ustawiony na "powodzenie", zostanie określona wartość "endTime", a odpowiedź będzie zawierać wszelkie błędy i ostrzeżenia, które wystąpiły podczas wzbogacania.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png" alt-text="Utwórz konto magazynu" border="false":::

Ostrzeżenia często występują dla niektórych kombinacji plików źródłowych i umiejętności oraz nie zawsze wskazują istnienie problemu. Wiele ostrzeżeń jest niegroźnych. Na przykład w przypadku indeksowania pliku JPEG, który nie zawiera tekstu, na tym zrzucie ekranu zobaczysz ostrzeżenie.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png" alt-text="Utwórz konto magazynu" border="false":::

## <a name="5---search"></a>5 — wyszukiwanie

Po zakończeniu indeksowania uruchom zapytania zwracające zawartość poszczególnych pól. Domyślnie usługa Azure Wyszukiwanie poznawcze zwraca górne wyniki 50. Przykładowych danych jest mało, więc wartość domyślna działa dobrze. Jednak podczas pracy z większymi zestawami danych może być konieczne dołączenie do ciągu zapytania parametrów powodujących zwrócenie większej liczby wyników. Aby uzyskać instrukcje, zobacz [How to Page Results in Azure wyszukiwanie poznawcze](search-pagination-page-layout.md).

Jako krok weryfikacji Pobierz definicję indeksu pokazującą wszystkie pola.

```python
# Query the service for the index definition
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Wyniki powinny wyglądać podobnie do poniższego przykładu. Zrzut ekranu przedstawia tylko część odpowiedzi.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png" alt-text="Utwórz konto magazynu" border="false":::

Dane wyjściowe to schemat indeksu z nazwą, typem i atrybutami każdego pola.

Prześlij drugie zapytanie o element `"*"`, co spowoduje zwrócenie całej zawartości pojedynczego pola, takiego jak `organizations`.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Wyniki powinny wyglądać podobnie do poniższego przykładu. Zrzut ekranu przedstawia tylko część odpowiedzi.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png" alt-text="Utwórz konto magazynu" border="false":::

Powtórz dla dodatkowych pól: `content` , `languageCode` , `keyPhrases` i `organizations` w tym ćwiczeniu. Istnieje możliwość zwrócenia wielu pól za pomocą elementu `$select` używającego listy wartości rozdzielonych przecinkami.

W zależności od złożoności i długości ciągu zapytania można użyć metody GET lub POST. Aby uzyskać więcej informacji, zobacz [Odpytywanie przy użyciu interfejsu API REST](/rest/api/searchservice/search-documents).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetowanie i ponowne uruchamianie

W przypadku wczesnych eksperymentalnych etapów tworzenia najlepszym podejściem do iteracji projektu jest usunięcie obiektów z platformy Azure Wyszukiwanie poznawcze i umożliwienie kodowi odbudowania. Nazwy zasobów są unikatowe. Usunięcie obiektu umożliwia jego ponowne utworzenie przy użyciu tej samej nazwy.

Możesz użyć portalu, aby usunąć indeksy, indeksatory, źródła danych i umiejętności. Po usunięciu indeksatora można opcjonalnie selektywnie usunąć indeks, zestawu umiejętności i źródło danych.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png" alt-text="Utwórz konto magazynu" border="false":::

Można je również usunąć za pomocą skryptu. Poniższy skrypt pokazuje, jak usunąć element zestawu umiejętności. 

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

W przypadku pomyślnego usunięcia jest zwracany kod stanu 204.

## <a name="takeaways"></a>Wnioski

W tym samouczku przedstawiono podstawowe kroki tworzenia wzbogaconego potoku indeksowania przez utworzenie elementów będących jego składnikami: źródła danych, zestawu umiejętności, indeksu i indeksatora.

Wprowadzono [wbudowane umiejętności](cognitive-search-predefined-skills.md) , a także definicje zestawu umiejętności oraz sposób łańcucha umiejętności przy użyciu danych wejściowych i wyjściowych. Poznasz również, że `outputFieldMappings` w definicji indeksatora jest wymagany do routingu wzbogaconych wartości z potoku do indeksu wyszukiwania w usłudze Azure wyszukiwanie poznawcze.

Wreszcie zawiesz się, jak przetestować wyniki i zresetować system pod kątem dalszych iteracji. Omówiono proces, w ramach którego odpytanie indeksu powoduje zwrócenie danych wyjściowych utworzonych przez wzbogacony potok indeksowania. W tej wersji jest dostępny mechanizm wyświetlania wewnętrznych konstrukcji (wzbogaconych dokumentów utworzonych przez system). Zawarto również informacje na temat sprawdzania stanu indeksatora oraz obiektów, które należy usunąć przed ponownym uruchomieniem potoku.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy Pracujesz w ramach własnej subskrypcji, na końcu projektu warto usunąć zasoby, które nie są już potrzebne. Uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku wszystkie zasoby lub grupy zasobów w okienku nawigacji po lewej stronie.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz wszystkie obiekty w potoku wzbogacenia, przyjrzyjmy się dokładniej definicjom zestawu umiejętności i indywidualnym umiejętnościom.

> [!div class="nextstepaction"]
> [Jak utworzyć zestawu umiejętności](cognitive-search-defining-skillset.md)