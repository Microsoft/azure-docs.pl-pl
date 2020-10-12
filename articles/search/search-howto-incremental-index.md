---
title: Skonfiguruj pamięć podręczną i przyrostowe wzbogacanie (wersja zapoznawcza)
titleSuffix: Azure Cognitive Search
description: Włącz buforowanie i Zachowaj stan wzbogaconej zawartości dla kontrolowanego przetwarzania w zestawu umiejętności poznawczej. Ta funkcja jest obecnie w publicznej wersji zapoznawczej.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: a1b317b651b0e17c07eb17dbdb8a7c6657d39564
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90971620"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Jak skonfigurować buforowanie na potrzeby przyrostowego wzbogacania na platformie Azure Wyszukiwanie poznawcze

> [!IMPORTANT] 
> Wzbogacanie przyrostowe jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
> [Wersje zapoznawcze interfejsu API REST](search-api-preview.md) zapewniają tę funkcję. W tej chwili nie ma obsługi portalu lub zestawu SDK platformy .NET.

W tym artykule opisano sposób dodawania buforowania do potoku wzbogacania, dzięki czemu można stopniowo modyfikować czynności bez konieczności ponownego kompilowania za każdym razem. Domyślnie zestawu umiejętności jest bezstanowy i zmiana dowolnej części jej składu wymaga pełnego ponownego uruchomienia indeksatora. W przypadku przyrostowego wzbogacania indeksator może określić, które części drzewa dokumentów muszą być odświeżane na podstawie zmian wykrytych w definicjach zestawu umiejętności lub indeksatora. Istniejące przetworzone dane wyjściowe są zachowywane i ponownie używane wszędzie tam, gdzie jest to możliwe. 

Zawartość pamięci podręcznej jest umieszczana w usłudze Azure Storage przy użyciu podanych informacji o koncie. Kontener o nazwie `ms-az-search-indexercache-<alpha-numerc-string>` jest tworzony podczas uruchamiania indeksatora. Powinien być traktowany jako składnik wewnętrzny zarządzany przez usługę wyszukiwania i nie może być modyfikowany.

Jeśli nie masz doświadczenia w konfigurowaniu indeksatorów, Zacznij od [omówienia indeksatora](search-indexer-overview.md) , a następnie przejdź do [umiejętności](cognitive-search-working-with-skillsets.md) , aby dowiedzieć się więcej na temat potoków wzbogacania. Aby uzyskać więcej informacji na temat kluczowych pojęć, zobacz [wzbogacanie przyrostowe](cognitive-search-incremental-indexing-conceptual.md).

## <a name="enable-caching-on-an-existing-indexer"></a>Włącz buforowanie w istniejącym indeksatorze

Jeśli masz istniejący indeksator, który ma już zestawu umiejętności, wykonaj kroki opisane w tej sekcji, aby dodać buforowanie. W ramach jednorazowej operacji trzeba będzie zresetować i ponownie uruchomić indeksator, aby można było zastosować przetwarzanie przyrostowe.

> [!TIP]
> Jako Weryfikacja koncepcji możesz wykonać czynności opisane w tym [portalu szybki start](cognitive-search-quickstart-blob.md) , aby utworzyć niezbędne obiekty, a następnie użyć programu Poster lub portalu do wprowadzenia aktualizacji. Możesz chcieć dołączyć zasób Cognitive Services rozliczany. Wielokrotne uruchamianie indeksatora spowoduje wyczerpanie bezpłatnej alokacji dziennej przed ukończeniem wszystkich kroków.

### <a name="step-1-get-the-indexer-definition"></a>Krok 1. Uzyskiwanie definicji indeksatora

Rozpocznij z prawidłowym istniejącym indeksatorem, który ma te składniki: Data Source, zestawu umiejętności, index. Indeksator powinien być możliwy do uruchomienia. 

Za pomocą klienta interfejsu API Utwórz [żądanie pobrania indeksatora](/rest/api/searchservice/get-indexer) , aby uzyskać bieżącą konfigurację indeksatora. W przypadku korzystania z wersji zapoznawczej interfejsu API do pobrania indeksatora `cache` do definicji zostanie dodana właściwość o wartości null.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Skopiuj definicję indeksatora z odpowiedzi.

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>Krok 2. Modyfikowanie właściwości cache w definicji indeksatora

Domyślnie `cache` Właściwość ma wartość null. Użyj klienta interfejsu API, aby ustawić konfigurację pamięci podręcznej (Portal nie obsługuje tej aktualizacji pyłów). 

Zmodyfikuj Obiekt pamięci podręcznej, aby zawierał następujące wymagane i opcjonalne właściwości: 

+ `storageConnectionString`Jest to wymagane i musi być ustawiony na parametry połączenia usługi Azure Storage. 
+ `enableReprocessing`Właściwość Boolean jest opcjonalna ( `true` domyślnie) i wskazuje, że jest włączona funkcja wzbogacania przyrostowego. W razie potrzeby można ustawić, aby `false` zawiesić przetwarzanie przyrostowe, podczas gdy inne operacje intensywnie obciążające zasoby, takie jak indeksowanie nowych dokumentów, są w toku, a następnie przewracane do `true` nowszej wersji.

```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

### <a name="step-3-reset-the-indexer"></a>Krok 3. Resetowanie indeksatora

Podczas konfigurowania przyrostowego wzbogacania istniejących indeksatorów jest wymagane Resetowanie indeksatora, aby upewnić się, że wszystkie dokumenty są w spójnym stanie. Do tego zadania można użyć portalu lub klienta interfejsu API i [resetowania interfejsu API REST indeksatora](/rest/api/searchservice/reset-indexer) .

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>Krok 4. zapisywanie zaktualizowanej definicji

[Zaktualizuj indeksator](/rest/api/searchservice/preview-api/update-indexer) przy użyciu żądania Put, treść żądania powinna zawierać zaktualizowaną definicję indeksatora, która ma właściwość Cache. Jeśli otrzymujesz 400, sprawdź definicję indeksatora, aby upewnić się, że zostały spełnione wszystkie wymagania (Źródło danych, zestawu umiejętności, indeks).

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
{
    "name" : "<YOUR-INDEXER-NAME>",
    ...
    "cache": {
        "storageConnectionString": "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    }
}
```

Jeśli masz teraz inne żądanie GET w indeksatorze, odpowiedź z usługi będzie zawierać `ID` Właściwość w obiekcie pamięci podręcznej. Ciąg alfanumeryczny jest dołączany do nazwy kontenera zawierającego wszystkie buforowane wyniki i stan pośredni każdego dokumentu przetworzonego przez ten indeksator. Identyfikator będzie używany do jednoznacznej nazwy pamięci podręcznej w usłudze BLOB Storage.

```http
    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }
```

### <a name="step-5-run-the-indexer"></a>Krok 5. Uruchamianie indeksatora

Aby uruchomić indeksator, możesz użyć portalu lub interfejsu API. W portalu z listy indeksatorów wybierz indeksator, a następnie kliknij przycisk **Uruchom**. Jedną z zalet korzystania z portalu jest możliwość monitorowania stanu indeksatora, zanotowania czasu trwania zadania oraz liczby przetworzonych dokumentów. Strony portalu są odświeżane co kilka minut.

Alternatywnie możesz użyć REST do [uruchomienia indeksatora](/rest/api/searchservice/run-indexer):

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Po uruchomieniu indeksatora można znaleźć pamięć podręczną w usłudze Azure Blob Storage. Nazwa kontenera ma następujący format: `ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> Zresetowanie i ponowne uruchomienie indeksatora powoduje pełne Odbudowywanie, aby zawartość mogła być buforowana. Wszystkie, wzbogacanie poznawcze zostanie uruchomione ponownie dla wszystkich dokumentów.
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>Krok 6. Modyfikacja zestawu umiejętności i potwierdzenie przyrostowego wzbogacania

Aby zmodyfikować zestawu umiejętności, można użyć portalu lub interfejsu API. Na przykład, jeśli używasz tłumaczenia tekstu, prosta zmiana w tekście z `en` `es` lub do innego języka jest wystarczająca do testowania koncepcji w celu przetestowania przyrostowego.

Ponownie uruchom indeksator. Aktualizowane są tylko te części wzbogaconego drzewa dokumentów. Jeśli używasz [portalu szybkiego startu](cognitive-search-quickstart-blob.md) jako dowodu koncepcji, modyfikując umiejętność tłumaczenia tekstu na "es", zobaczysz, że zaktualizowane zostaną tylko 8 dokumentów zamiast oryginalnego 14. Pliki obrazów nieobjęte procesem tłumaczenia są ponownie używane z pamięci podręcznej.

## <a name="enable-caching-on-new-indexers"></a>Włącz buforowanie nowych indeksatorów

Aby skonfigurować przyrostowe wzbogacanie dla nowego indeksatora, wszystkie czynności, które należy wykonać, są uwzględniane `cache` w ładunku definicji indeksatora podczas wywoływania [Create indeksatora (2020-06-30-Preview)](/rest/api/searchservice/preview-api/create-indexer). 


```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
    }
}
```

## <a name="checking-for-cached-output"></a>Sprawdzanie danych wyjściowych w pamięci podręcznej

Pamięć podręczna jest tworzona, używana i zarządzana przez indeksator, a jej zawartość nie jest reprezentowana w formacie, który jest czytelny dla człowieka. Najlepszym sposobem na ustalenie, czy pamięć podręczna jest używana, jest uruchomienie indeksatora i porównanie metryk przed i-After dla czasu wykonywania i liczby dokumentów. 

Załóżmy na przykład, że zestawu umiejętności, który rozpoczyna się od analizy obrazów i optycznego rozpoznawania znaków (OCR) zeskanowanych dokumentów, a następnie przeanalizować tekst wyniku. Jeśli zmodyfikujesz umiejętność tekstu podrzędnego, indeksator może pobrać wszystkie wcześniej przetworzone obrazy i OCR zawartość z pamięci podręcznej, zaktualizować i przetworzyć tylko zmiany związane z tekstem wskazywane przez edycje. Możesz oczekiwać, że liczba dokumentów w liczbie dokumentów (na przykład 8/8 zamiast 14/14 w oryginalnym przebiegu), krótszym czasie wykonywania i mniejszej liczbie opłat na rachunku.

## <a name="working-with-the-cache"></a>Praca z pamięcią podręczną

Gdy pamięć podręczna działa, indeksatory sprawdzają pamięć podręczną przy każdym wywołaniu [indeksatora](/rest/api/searchservice/run-indexer) , aby zobaczyć, które części istniejących danych wyjściowych mogą być używane. 

W poniższej tabeli zestawiono różne interfejsy API powiązane z pamięcią podręczną:

| Interfejs API           | Wpływ na pamięć podręczną     |
|---------------|------------------|
| [Tworzenie indeksatora (2020-06-30 — wersja zapoznawcza)](/rest/api/searchservice/preview-api/create-indexer) | Tworzy i uruchamia indeksator przy pierwszym użyciu, w tym tworzenie pamięci podręcznej, jeśli określa ją definicja indeksatora. |
| [Uruchom indeksator](/rest/api/searchservice/run-indexer) | Wykonuje potok wzbogacania na żądanie. Ten interfejs API odczytuje z pamięci podręcznej, jeśli istnieje, lub tworzy pamięć podręczną, jeśli dodano buforowanie do zaktualizowanej definicji indeksatora. Gdy uruchamiasz indeksator, który ma włączone buforowanie, Indeksator pomija kroki, jeśli można użyć buforowanych danych wyjściowych. Można korzystać z ogólnie dostępnej lub wersji zapoznawczej interfejsu API tego interfejsu API.|
| [Zresetuj indeksator](/rest/api/searchservice/reset-indexer)| Czyści indeksator wszelkich przyrostowych informacji o indeksowaniu. Następny indeksator przebiega (na żądanie lub w harmonogramie) to pełne przetworzenie, w tym ponowne uruchamianie wszystkich umiejętności i odbudowanie pamięci podręcznej. Jest on funkcjonalnie równoważny do usunięcia indeksatora i ponownego utworzenia go. Można korzystać z ogólnie dostępnej lub wersji zapoznawczej interfejsu API tego interfejsu API.|
| [Resetuj umiejętności](/rest/api/searchservice/preview-api/reset-skills) | Określa, które umiejętności należy uruchomić ponownie przy następnym uruchomieniu indeksatora, nawet jeśli nie zmodyfikowano żadnych umiejętności. Pamięć podręczna jest odpowiednio aktualizowana. Dane wyjściowe, takie jak magazyn wiedzy lub indeks wyszukiwania, są odświeżane przy użyciu danych wielokrotnego użytku z pamięci podręcznej oraz nowej zawartości na zaktualizowane umiejętności. |

Aby uzyskać więcej informacji na temat kontrolowania, co się dzieje z pamięcią podręczną, zobacz [Zarządzanie pamięcią podręczną](cognitive-search-incremental-indexing-conceptual.md#cache-management).

## <a name="next-steps"></a>Następne kroki

Wzbogacanie przyrostowe dotyczy indeksatorów zawierających umiejętności. W następnym kroku zapoznaj się z dokumentacją zestawu umiejętności, aby poznać koncepcje i kompozycję. 

Ponadto po włączeniu pamięci podręcznej należy poznać parametry i interfejsy API, które są przeznaczone do buforowania, w tym sposób przesłonięcia lub wymuszania określonych zachowań. Aby uzyskać więcej informacji, zobacz następujące linki.

+ [Zestawu umiejętności koncepcje i kompozycje](cognitive-search-working-with-skillsets.md)
+ [Jak utworzyć zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Wprowadzenie do przyrostowego wzbogacania i buforowania](cognitive-search-incremental-indexing-conceptual.md)