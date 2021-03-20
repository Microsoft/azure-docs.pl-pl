---
title: Migrowanie z wersji 2 do V3 interfejsu API REST — usługa mowy
titleSuffix: Azure Cognitive Services
description: Ten dokument ułatwia deweloperom Migrowanie kodu z wersji 2 do V3 programu w interfejsie API REST zamiany mowy na tekst w usłudze Speech Services.
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c8016b566db8be1b7f5c5ddb8d92123d6673db5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98569848"
---
# <a name="migrate-code-from-v20-to-v30-of-the-rest-api"></a>Migrowanie kodu z wersji v 2.0 do wersji 3.0 interfejsu API REST

W porównaniu do wersji 2 wersja v3 interfejsu API REST usługi Speech Services dla zamiany mowy na tekst jest bardziej niezawodna, łatwiejsza w użyciu i bardziej spójna z interfejsami API dla podobnych usług. Większość zespołów może migrować z wersji 2 do V3 w ciągu dnia lub dwóch.

## <a name="forward-compatibility"></a>Zgodność dalej

Wszystkie jednostki z wersji 2 można również znaleźć w interfejsie API V3 w ramach tej samej tożsamości. W przypadku zmiany schematu wyniku (na przykład transkrypcji) wynik GET w wersji v3 interfejsu API używa schematu v3. Wynik pobrania w wersji 2 interfejsu API używa tego samego schematu v2. Nowo utworzone jednostki w wersji 3 **nie** są   dostępne w odpowiedziach z interfejsów API v2. 

## <a name="migration-steps"></a>Kroki migracji

Ta lista zawiera podsumowanie elementów, o których należy pamiętać podczas przygotowywania do migracji. Szczegółowe informacje znajdują się w poszczególnych łączach. W zależności od bieżącego użycia interfejsu API nie wszystkie kroki wymienione w tym miejscu mogą być stosowane. Tylko niektóre zmiany wymagają nieuproszczonych zmian w kodzie wywołującym. Większość zmian wymaga tylko zmiany nazw elementów. 

Ogólne zmiany: 

1. [Zmień nazwę hosta](#host-name-changes)

1. [Zmień nazwę identyfikatora właściwości na własny w kodzie klienta](#identity-of-an-entity) 

1. [Zmień kod, aby wykonać iterację kolekcji jednostek](#working-with-collections-of-entities)

1. [Zmień nazwę właściwości na displayName w kodzie klienta](#name-of-an-entity)

1. [Dostosuj Pobieranie metadanych jednostek, do których istnieją odwołania](#accessing-referenced-entities)

1. Jeśli używasz transkrypcji partii: 

    * [Dostosuj kod na potrzeby tworzenia transkrypcji partii](#creating-transcriptions) 

    * [Dostosowanie kodu do nowego schematu wyników transkrypcji](#format-of-v3-transcription-results)

    * [Dostosuj kod na potrzeby pobierania wyników](#getting-the-content-of-entities-and-the-results)

1. Jeśli używasz niestandardowych interfejsów API szkolenia/testowania: 

    * [Stosowanie modyfikacji do szkolenia modelu niestandardowego](#customizing-models)

    * [Zmień sposób pobierania modeli podstawowych i niestandardowych](#retrieving-base-and-custom-models)

    * [Zmień nazwę segmentu ścieżki accuracytests na oceny w kodzie klienta](#accuracy-tests)

1. Jeśli używasz interfejsów API punktów końcowych:

    * [Zmień sposób pobierania dzienników punktów końcowych](#retrieving-endpoint-logs)

1. Inne drobne zmiany: 

    * [Przekazuj wszystkie właściwości niestandardowe jako customProperties zamiast właściwości w żądaniach POST](#using-custom-properties)

    * [Odczytaj lokalizację z lokalizacji nagłówka odpowiedzi zamiast z lokalizacji operacji](#response-headers)

## <a name="breaking-changes"></a>Fundamentalne zmiany

### <a name="host-name-changes"></a>Zmiany nazwy hosta

Nazwy hostów punktów końcowych zostały zmienione z `{region}.cris.ai` na `{region}.api.cognitive.microsoft.com` . Ścieżki do nowych punktów końcowych nie są już zawarte `api/` , ponieważ jest częścią nazwy hosta. [Dokument struktury Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) zawiera prawidłowe regiony i ścieżki.
>[!IMPORTANT]
>Zmień nazwę hosta z `{region}.cris.ai` na, `{region}.api.cognitive.microsoft.com` gdzie region jest regionem Twojej subskrypcji mowy. Usuń również `api/` z dowolnej ścieżki w kodzie klienta.

### <a name="identity-of-an-entity"></a>Tożsamość jednostki

Właściwość `id` jest teraz `self` . W wersji 2 użytkownik interfejsu API musiał wiedzieć, jak są tworzone ścieżki interfejsu API. Nie jest to rozszerzalne i wymagało niepotrzebnej pracy z użytkownikiem. Właściwość `id` (UUID) jest zastępowana przez `self` (ciąg), która jest lokalizacją jednostki (adres URL). Wartość jest wciąż unikatowa między wszystkimi jednostkami. Jeśli `id` jest przechowywany jako ciąg w kodzie, zmiana nazwy jest wystarczająca do obsługi nowego schematu. Teraz można używać `self` zawartości jako adresu URL dla `GET` `PATCH` wywołań, i `DELETE` REST dla jednostki.

Jeśli jednostka ma dodatkowe funkcje dostępne za pomocą innych ścieżek, są one wymienione w sekcji `links` . Poniższy przykład dla transkrypcji przedstawia oddzielną metodę do `GET` zawartości transkrypcji:
>[!IMPORTANT]
>Zmień nazwę właściwości `id` na `self` w kodzie klienta. Zmień typ z `uuid` na, `string` Jeśli jest to konieczne. 

**Transkrypcja v2:**

```json
{
    "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Transcription using locale en-US"
}
```

**Transkrypcja v3:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US", 
    "displayName": "Transcription using locale en-US",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    }
}
```

W zależności od implementacji kodu może nie wystarczyć do zmiany nazwy właściwości. Zalecamy używanie zwracanych `self` i `links` wartości jako docelowych adresów URL wywołań REST zamiast generowania ścieżek na kliencie. Korzystając z zwróconych adresów URL, można mieć pewność, że przyszłe zmiany w ścieżkach nie spowodują przerwania kodu klienta.

### <a name="working-with-collections-of-entities"></a>Praca z kolekcjami jednostek

Wcześniej interfejs API v2 zwrócił wszystkie dostępne jednostki w wyniku. Aby umożliwić dokładniejszą kontrolę nad oczekiwanym rozmiarem odpowiedzi w wersji 3, wszystkie wyniki kolekcji są podzielone na strony. Masz kontrolę nad liczbą zwracanych jednostek i początkową przesunięciem strony. To zachowanie ułatwia przewidywanie środowiska uruchomieniowego procesora odpowiedzi.

Podstawowy kształt odpowiedzi jest taki sam dla wszystkich kolekcji:

```json
{
    "values": [
        {     
        }
    ],
    "@nextLink": "https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/{collection}?skip=100&top=100"
}
```

`values`Właściwość zawiera podzestaw dostępnych jednostek kolekcji. Liczby i przesunięcia można kontrolować przy użyciu `skip` parametrów i `top` zapytania. Gdy `@nextLink` nie jest `null` , dostępne są więcej danych i można pobrać następną partię danych, WYKONUJĄC polecenie Get on `$.@nextLink` .

Ta zmiana wymaga wywołania `GET` dla kolekcji w pętli do momentu zwrócenia wszystkich elementów.

>[!IMPORTANT]
>Gdy odpowiedź elementu GET na `speechtotext/v3.0/{collection}` zawiera wartość w `$.@nextLink` , Kontynuuj wydawanie do `GETs` `$.@nextLink` do do, `$.@nextLink` aby nie była ustawiona na pobieranie wszystkich elementów tej kolekcji.

### <a name="creating-transcriptions"></a>Tworzenie transkrypcji

Szczegółowy opis sposobu tworzenia partii transkrypcji można znaleźć w temacie [jak to zrobić](./batch-transcription.md).

Interfejs API transkrypcji v3 umożliwia jawne Ustawianie określonych opcji transkrypcji. Wszystkie (opcjonalne) właściwości konfiguracji można teraz ustawić we `properties` właściwości.
Wersja V3 obsługuje również wiele plików wejściowych, dlatego wymaga listy adresów URL zamiast pojedynczego adresu URL jako v2. Nazwa właściwości v2 `recordingsUrl` jest teraz `contentUrls` w wersji 3. Funkcja analizowania tonacji w transkrypcjach została usunięta w wersji 3. Zobacz [Analiza tekstu](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) usługi poznawczej firmy Microsoft dla opcji analizy tonacji.

Nowa właściwość `timeToLive` w obszarze `properties` może pomóc w oczyszczaniu istniejących zakończonych jednostek. `timeToLive`Określa czas trwania, po upływie którego ukończona jednostka zostanie automatycznie usunięta. Ustaw dla niego wysoką wartość (na przykład `PT12H` ), gdy jednostki są stale śledzone, zużywane i usuwane, a przez to zwykle przetwarzane długo przed upływem 12 godzin.

**treść żądania POST dokumentu transkrypcji v2:**

```json
{
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "properties": {
    "AddDiarization": "False",
    "AddWordLevelTimestamps": "False",
    "PunctuationMode": "DictatedAndAutomatic",
    "ProfanityFilterMode": "Masked"
  }
}
```

**treść żądania POST z transkrypcją v3:**

```json
{
  "locale": "en-US",
  "displayName": "Transcription using locale en-US",
  "contentUrls": [
    "https://contoso.com/mystoragelocation",
    "https://contoso.com/myotherstoragelocation"
  ],
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  }
}
```
>[!IMPORTANT]
>Zmień nazwę właściwości `recordingsUrl` na `contentUrls` i przekaż tablicę adresów URL zamiast pojedynczego adresu URL. Przekazanie ustawień dla `diarizationEnabled` lub `wordLevelTimestampsEnabled` jako `bool` zamiast `string` .

### <a name="format-of-v3-transcription-results"></a>Format wyników transkrypcji v3

Schemat wyników transkrypcji został nieco zmieniony do dopasowania z transkrypcjami utworzonymi przez punkty końcowe w czasie rzeczywistym. Szczegółowe opisy nowych formatów można znaleźć w temacie [jak to zrobić](./batch-transcription.md). Schemat wyniku jest publikowany w naszym [repozytorium przykładowym GitHub](https://aka.ms/csspeech/samples) poniżej `samples/batch/transcriptionresult_v3.schema.json` .

Nazwy właściwości są teraz notacji CamelCase — wielkość liter i wartości dla `channel` i `speaker` teraz używają typów całkowitych. Format dla czasów trwania używa teraz struktury opisanej w ISO 8601, która jest zgodna z formatowaniem czasu trwania używanym w innych interfejsach API platformy Azure.

Przykład wyniku transkrypcji v3. Różnice są opisane w komentarzach.

```json
{
  "source": "...",                      // (new in v3) was AudioFileName / AudioFileUrl
  "timestamp": "2020-06-16T09:30:21Z",  // (new in v3)
  "durationInTicks": 41200000,          // (new in v3) was AudioLengthInSeconds
  "duration": "PT4.12S",                // (new in v3)
  "combinedRecognizedPhrases": [        // (new in v3) was CombinedResults
    {
      "channel": 0,                     // (new in v3) was ChannelNumber
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // (new in v3) was SegmentResults
    {
      "recognitionStatus": "Success",   // 
      "channel": 0,                     // (new in v3) was ChannelNumber
      "offset": "PT0.07S",              // (new in v3) new format, was OffsetInSeconds
      "duration": "PT1.59S",            // (new in v3) new format, was DurationInSeconds
      "offsetInTicks": 700000.0,        // (new in v3) was Offset
      "durationInTicks": 15900000.0,    // (new in v3) was Duration

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,phrase
          "speaker": 1,
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
            }
          ]
        }
      ]
    }
  ]
}
```
>[!IMPORTANT]
>Deserializacja wyniku transkrypcji do nowego typu, jak pokazano powyżej. Zamiast pojedynczego pliku na kanał audio, należy odróżnić kanały, sprawdzając wartość właściwości `channel` dla każdego elementu w `recognizedPhrases` . Dla każdego pliku wejściowego istnieje teraz pojedynczy plik wynikowy.


### <a name="getting-the-content-of-entities-and-the-results"></a>Pobieranie zawartości jednostek i wyników

W wersji 2 linki do plików wejściowych lub wynikowych zostały opisane w pozostałej części metadanych jednostki. Poprawa w wersji 3 oznacza wyraźne rozdzielenie metadanych jednostki (które są zwracane przez polecenie GET on `$.self` ) oraz szczegóły i poświadczenia, aby uzyskać dostęp do plików wynikowych. Ta separacja pomaga chronić dane klientów i umożliwia precyzyjne sterowanie okresem ważności poświadczeń.

W wersji 3 należy `links` uwzględnić Właściwość podrzędną o nazwie `files` w przypadku, gdy jednostka uwidacznia dane (zestawy danych, transkrypcje, punkty końcowe lub obliczenia). GET on `$.links.files` zwróci listę plików i adres URL sygnatury dostępu współdzielonego, aby uzyskać dostęp do zawartości każdego pliku. Aby kontrolować okres ważności adresów URL sygnatury dostępu współdzielonego, `sasValidityInSeconds` można użyć parametru zapytania do określenia okresu istnienia.

**Transkrypcja v2:**

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "status": "Succeeded",
  "reportFileUrl": "https://contoso.com/report.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f728327c53b5",
  "resultsUrls": {
    "channel_0": "https://contoso.com/audiofile1.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f72832e6600c",
    "channel_1": "https://contoso.com/audiofile2.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=3e0163f1-0029-4d4a-988d-3fba7d7c53b5"
  }
}
```

**Transkrypcja v3:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

**W efekcie uzyskasz `$.links.files` :**

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/f23e54f5-ed74-4c31-9730-2f1a3ef83ce8",
      "name": "Name",
      "kind": "Transcription",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/mywavefile1.wav.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/28bc946b-c251-4a86-84f6-ea0f0a2373ef",
      "name": "Name",
      "kind": "TranscriptionReport",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/report.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files?skip=2&top=2"
}
```

`kind`Właściwość wskazuje format zawartości pliku. W przypadku transkrypcji pliki typu `TranscriptionReport` są podsumowaniem zadania i plików rodzaju `Transcription` są wynikiem samego zadania.

>[!IMPORTANT]
>Aby uzyskać wyniki operacji, użyj wartości `GET` on `/speechtotext/v3.0/{collection}/{id}/files` , nie są już zawarte w odpowiedziach `GET` `/speechtotext/v3.0/{collection}/{id}` lub `/speechtotext/v3.0/{collection}` .

### <a name="customizing-models"></a>Dostosowywanie modeli

Przed v3 nastąpiło rozróżnienie między _modelem akustycznym_ i _modelem języka_ podczas uczenia modelu. Różnica ta spowodowała konieczność określenia wielu modeli podczas tworzenia punktów końcowych lub transkrypcji. Aby uprościć ten proces dla obiektu wywołującego, usunęliśmy różnice i wszystko to zależało od zawartości zestawów danych, które są używane do uczenia modelu. Dzięki tej zmianie Tworzenie modelu obsługuje teraz mieszane zestawy danych (dane języka i dane akustyczne). Punkty końcowe i transkrypcje wymagają teraz tylko jednego modelu.

W przypadku tej zmiany należy usunąć potrzebę wykonania `kind` `POST` operacji, a `datasets[]` Tablica może teraz zawierać wiele zestawów danych o takich samych lub mieszanych typach.

W celu poprawienia wyników przeszkolonego modelu dane akustyczne są automatycznie używane wewnętrznie podczas szkolenia języka. Ogólnie rzecz biorąc, modele utworzone za pomocą interfejsu API v3 zapewniają dokładniejsze wyniki niż modele utworzone za pomocą interfejsu API v2.

>[!IMPORTANT]
>Aby dostosować zarówno składnik akustyczny, jak i model języka, należy przekazać wszystkie wymagane Języki i akustyczne zestawy danych w `datasets[]` wpisie do `/speechtotext/v3.0/models` . Spowoduje to utworzenie jednego modelu z dostosowanymi obydwoma częściami.

### <a name="retrieving-base-and-custom-models"></a>Pobieranie modeli podstawowych i niestandardowych

Aby uprościć pobieranie dostępnych modeli, wersja 3 oddzielona kolekcje "modele podstawowe" od należącego do klienta "modeli dostosowanych". Te dwie trasy są teraz `GET /speechtotext/v3.0/models/base` i `GET /speechtotext/v3.0/models/` .

W wersji 2 wszystkie modele zostały zwrócone razem w pojedynczej odpowiedzi.

>[!IMPORTANT]
>Aby uzyskać listę podanych modeli bazowych do dostosowania, użyj `GET` na `/speechtotext/v3.0/models/base` . Własne dostosowane modele można znaleźć `GET` w usłudze `/speechtotext/v3.0/models` .

### <a name="name-of-an-entity"></a>Nazwa jednostki

`name`Właściwość jest teraz `displayName` . Jest to zgodne z innymi interfejsami API platformy Azure, aby nie wskazywały właściwości tożsamości. Wartość tej właściwości nie może być unikatowa i można ją zmienić po utworzeniu jednostki przy użyciu `PATCH` operacji.

**Transkrypcja v2:**

```json
{
    "name": "Transcription using locale en-US"
}
```

**Transkrypcja v3:**

```json
{
    "displayName": "Transcription using locale en-US"
}
```

>[!IMPORTANT]
>Zmień nazwę właściwości `name` na `displayName` w kodzie klienta.

### <a name="accessing-referenced-entities"></a>Uzyskiwanie dostępu do przywoływanych jednostek

W wersji 2 przywoływane jednostki były zawsze wbudowane, na przykład używane modele punktu końcowego. Zagnieżdżanie jednostek spowodowało duże odpowiedzi, a konsumenci rzadko zużywają zawartość zagnieżdżoną. Aby zmniejszyć rozmiar odpowiedzi i zwiększyć wydajność, jednostki, do których istnieją odwołania, nie są już wbudowane w odpowiedzi. Zamiast tego zostanie wyświetlone odwołanie do innej jednostki i można je bezpośrednio użyć dla kolejnego `GET` (jest to adres URL), wykonując ten sam wzorzec co `self` link.

**Transkrypcja v2:**

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "models": [
    {
      "id": "827712a5-f942-4997-91c3-7c6cde35600b",
      "modelKind": "Language",
      "lastActionDateTime": "2019-01-07T11:36:07Z",
      "status": "Running",
      "createdDateTime": "2019-01-07T11:34:12Z",
      "locale": "en-US",
      "name": "Acoustic model",
      "description": "Example for an acoustic model",
      "datasets": [
        {
          "id": "702d913a-8ba6-4f66-ad5c-897400b081fb",
          "dataImportKind": "Language",
          "lastActionDateTime": "2019-01-07T11:36:07Z",
          "status": "Succeeded",
          "createdDateTime": "2019-01-07T11:34:12Z",
          "locale": "en-US",
          "name": "Language dataset",
        }
      ]
    },
  ]
}
```

**Transkrypcja v3:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

Jeśli potrzebujesz użyć szczegółowych informacji o modelu, do którego istnieje odwołanie, jak pokazano w powyższym przykładzie, wystarczy wydać polecenie GET on `$.model.self` .

>[!IMPORTANT]
>Aby pobrać metadane przywoływanych jednostek, należy wydać polecenie GET on `$.{referencedEntity}.self` , aby na przykład pobrać model transkrypcji `GET` `$.model.self` .


### <a name="retrieving-endpoint-logs"></a>Pobieranie dzienników punktów końcowych

Wersja V2 obsługiwanej przez usługę wyników rejestrowania punktów końcowych. Aby pobrać wyniki punktu końcowego z v2, należy utworzyć "Eksport danych", który reprezentuje migawkę wyników zdefiniowanych przez zakres czasu. Proces eksportowania partii danych był nieelastyczny. Interfejs API v3 zapewnia dostęp do poszczególnych plików i umożliwia iterację.

**Pomyślnie uruchomiono punkt końcowy v3:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

**Odpowiedź GET `$.links.logs` :**

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/6d72ad7e-f286-4a6f-b81b-a0532ca6bcaa/files/logs/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "name": "2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "kind": "Audio",
      "properties": {
        "size": 12345
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }    
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs?top=2&SkipToken=2!188!MDAwMDk1ITZhMjhiMDllLTg0MDYtNDViMi1hMGRkLWFlNzRlOGRhZWJkNi8yMDIwLTA0LTAxLzEyNDY0M182MzI5NGRkMi1mZGYzLTRhZmEtOTA0NC1mODU5ZTcxOWJiYzYud2F2ITAwMDAyOCE5OTk5LTEyLTMxVDIzOjU5OjU5Ljk5OTk5OTlaIQ--"
}
```

Stronicowanie dzienników punktów końcowych działa podobnie jak wszystkie inne kolekcje, z tą różnicą, że nie można określić przesunięcia. Ze względu na dużą ilość dostępnych danych podział na strony jest określany przez serwer.

W wersji 3 każdy dziennik punktu końcowego można usunąć pojedynczo przez wystawienie `DELETE` operacji na `self` pliku lub przy użyciu polecenia `DELETE` on `$.links.logs` . Aby określić datę końcową, parametr zapytania `endDate` można dodać do żądania.

>[!IMPORTANT]
>Zamiast tworzyć eksporty dzienników przy `/api/speechtotext/v2.0/endpoints/{id}/data` użyciu w `/v3.0/endpoints/{id}/files/logs/` celu uzyskiwania dostępu do plików dziennika pojedynczo. 

### <a name="using-custom-properties"></a>Używanie właściwości niestandardowych

Aby oddzielić właściwości niestandardowe od opcjonalnych właściwości konfiguracji, wszystkie jawnie nazwane właściwości znajdują się teraz we `properties` właściwości i wszystkie właściwości zdefiniowane przez wywołujących znajdują się w `customProperties` właściwości.

**Jednostka transkrypcji v2:**

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

**Jednostka transkrypcji v3:**

```json
{
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false 
  },
  "customProperties": {
    "customerDefinedKey": "value"
  }
}
```

Ta zmiana pozwala także użyć prawidłowych typów dla wszystkich jawnie nazwanych właściwości w `properties` (na przykład wartość logiczna zamiast ciągu).

>[!IMPORTANT]
>Przekazanie wszystkich właściwości niestandardowych jako `customProperties` zamiast `properties` w `POST` żądaniach.

### <a name="response-headers"></a>Nagłówki odpowiedzi

V3 nie zwraca już `Operation-Location` nagłówka oprócz `Location` nagłówka w `POST` żądaniach. Wartość obu nagłówków w wersji 2 była taka sama. Jest teraz tylko `Location` zwracana.

Ponieważ nowa wersja interfejsu API jest teraz zarządzana przez usługę Azure API Management (APIM), nagłówki powiązane z ograniczeniami `X-RateLimit-Limit` `X-RateLimit-Remaining` i `X-RateLimit-Reset` nie znajdują się w nagłówkach odpowiedzi.

>[!IMPORTANT]
>Odczytaj lokalizację z nagłówka odpowiedzi `Location` zamiast `Operation-Location` . W przypadku kodu odpowiedzi 429, Odczytaj `Retry-After` wartość nagłówka zamiast `X-RateLimit-Limit` , `X-RateLimit-Remaining` lub `X-RateLimit-Reset` .


### <a name="accuracy-tests"></a>Testy dokładności

Zmieniono nazwy testów dokładności na oceny, ponieważ nowa nazwa opisuje lepiej to, co reprezentuje. Nowe ścieżki: `https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/evaluations` .

>[!IMPORTANT]
>Zmień nazwę segmentu ścieżki `accuracytests` na `evaluations` w kodzie klienta.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się ze wszystkimi funkcjami tych najczęściej używanych interfejsów API REST udostępnianych przez usługi mowy:

* [Interfejs API REST zamiany mowy na tekst](rest-speech-to-text.md)
* [Dokument struktury Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) dla wersji 3 interfejsu API REST
* Aby zapoznać się z przykładowym kodem do wykonania transkrypcji partii, przejrzyj [przykładowe repozytorium GitHub](https://aka.ms/csspeech/samples) w `samples/batch` podkatalogu.
