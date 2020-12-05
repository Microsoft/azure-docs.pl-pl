---
title: Migrowanie z wersji 2 do V3 interfejsu API REST — usługa mowy
titleSuffix: Azure Cognitive Services
description: W porównaniu do wersji 2 nowy interfejs API w wersji v3 zawiera zestaw mniejszych istotnych zmian. Ten dokument ułatwia Migrowanie do nowej wersji głównej.
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: dd1dae963781cc0caacc25938e700a4c70a1f51a
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96738025"
---
# <a name="migration-from-v20-to-v30-of-speech-to-text-rest-api"></a>Migracja z wersji 2.0 do wersji 3.0 funkcji zamiany mowy na tekst interfejsu API REST

Wersja V3 interfejsu API REST usługi Speech jest ulepszona w porównaniu z poprzednią wersją interfejsu API w odniesieniu do niezawodności i łatwość użycia. Układ interfejsu API jest bardziej ściśle wyrównany z innymi platformami Azure lub interfejsy API usług Cognitive Services. Pozwala to na stosowanie istniejących umiejętności podczas korzystania z naszego interfejsu API mowy.

Przegląd interfejsu API jest dostępny jako [dokument struktury Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0). Jest to idealne rozwiązanie w celu udostępnienia interfejsu API i przetestowania nowego interfejsu API.

Udostępniamy przykłady dla języków C# i Python. W przypadku transkrypcji wsadowych można znaleźć przykłady w [repozytorium przykładowym GitHub](https://aka.ms/csspeech/samples) w `samples/batch` podkatalogu.

## <a name="forward-compatibility"></a>Zgodność dalej

Aby zapewnić bezproblemową migrację do wersji 3, w interfejsie API v3 można także znaleźć wszystkie jednostki z tego samego identyfikatora. W przypadku zmiany schematu wyniku (na przykład transkrypcji) odpowiedzi na wersję GET w wersji 3 interfejsu API będą znajdować się w schemacie v3. Jeśli zostanie wykonana wersja interfejsu API GET w wersji 2, schemat wynikowy będzie w formacie w wersji 2. Nowo utworzone jednostki na v3 **nie są** dostępne w wersji 2.

## <a name="breaking-changes"></a>Zmiany powodujące niezgodność

Lista istotnych zmian została posortowana według wielkości zmian wymaganych do dostosowania. Istnieje tylko kilka zmian, które wymagają nieuproszczonej zmiany kodu wywołującego. Większość zmian wymaga prostej zmiany nazwy. Czas, przez który zespoły przeprowadziły migrację z wersji 2 do v3, różnią się od kilku godzin do kilku dni. Jednak korzyści wynikające z zwiększonej stabilności, prostszego kodu, szybszej reakcji umożliwiają szybkie przesunięcie inwestycji. 

### <a name="host-name-changes"></a>Zmiany nazwy hosta

Nazwy hostów zostały zmienione z regionu {region}. CRI. AI do {region}. API. poznawcze. Microsoft. com. W tej zmianie ścieżki nie zawierają już interfejsu "API/", ponieważ jest częścią nazwy hosta. Zobacz [dokument Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) , aby uzyskać pełny opis regionów i ścieżek.

### <a name="identity-of-an-entity"></a>Tożsamość jednostki

Właściwość `id` została zamieniona na `self` . W wersji 2 użytkownik interfejsu API musiał wiedzieć, jak są tworzone ścieżki interfejsu API. Nie jest to rozszerzalne i wymagało niepotrzebnej pracy z użytkownikiem. Właściwość `id` (UUID) jest zastępowana przez `self` (ciąg), która jest lokalizacją jednostki (adres URL). Wartość jest wciąż unikatowa między wszystkimi jednostkami. Jeśli `id` jest przechowywany jako ciąg w kodzie, prosta zmiana nazwy jest wystarczająca do obsługi nowego schematu. Teraz można używać `self` zawartości jako adresu URL dla wszystkich wywołań REST dla jednostki (Get, patch, Delete).

Jeśli jednostka ma dodatkowe funkcje dostępne w innych ścieżkach, są one wymienione poniżej `links` . Dobrym przykładem jest Transkrypcja, która ma osobną metodę do `GET` zawartości transkrypcji.

Transkrypcja v2:

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

Transkrypcja v3:

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

W zależności od implementacji klienta może nie wystarczyć do zmiany nazwy właściwości. Zalecamy skorzystanie z używania zwracanych wartości `self` i `links` jako docelowych adresów URL wywołań REST, a nie do generowania ścieżek w kliencie. Korzystając z zwróconych adresów URL, można mieć pewność, że przyszłe zmiany w ścieżkach nie spowodują przerwania kodu klienta.

### <a name="working-with-collections-of-entities"></a>Praca z kolekcjami jednostek

Wcześniej interfejs API v2 zwrócił wszystkie dostępne jednostki w odpowiedzi. Aby umożliwić bardziej dokładniejszą kontrolę nad oczekiwanym rozmiarem odpowiedzi, w wersji 3 wszystkie odpowiedzi kolekcji są podzielone na strony. Masz kontrolę nad liczbą zwracanych jednostek i przesunięciem strony. Takie zachowanie ułatwia przewidywanie środowiska uruchomieniowego procesora odpowiedzi i jest spójne z innymi interfejsami API platformy Azure.

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

Właściwość `values` zawiera podzestaw dostępnych jednostek kolekcji. Liczby i przesunięcia można kontrolować za pomocą parametrów zapytania `skip` i `top` . Gdy `@nextLink` wartość nie jest równa null, dostępne są więcej danych, a następną partie danych można pobrać, wykonując polecenie Get on `$.@nextLink` .

Ta zmiana wymaga wywołania `GET` dla kolekcji w pętli do momentu zwrócenia wszystkich elementów.

### <a name="creating-transcriptions"></a>Tworzenie transkrypcji

Szczegółowy opis sposobu tworzenia transkrypcji można znaleźć w temacie [jak to zrobić](./batch-transcription.md).

Tworzenie transkrypcji zostało zmienione w wersji 3, aby jawnie włączyć ustawienie określonych opcji transkrypcji. Wszystkie (opcjonalne) właściwości konfiguracji można teraz ustawić we `properties` właściwości.
Ponadto wersja v3 obsługuje teraz wiele plików wejściowych, dlatego wymaga listy adresów URL, a nie jednego adresu URL wymaganego przez v2. Nazwa właściwości została zmieniona z `recordingsUrl` na `contentUrls` . Funkcja analizowania tonacji w transkrypcjach została usunięta w wersji 3. Zalecamy użycie [analizy tekstu](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) usługi poznawczej firmy Microsoft.

Nowa właściwość `timeToLive` w obszarze `properties` może pomóc w oczyszczaniu istniejących zakończonych jednostek. `timeToLive`Określa czas trwania, po upływie którego ukończona jednostka zostanie automatycznie usunięta. Ustaw dla niego wysoką wartość (na przykład `PT12H` ), gdy jednostki są stale śledzone, zużywane i usuwane, a więc zwykle przetwarzane długo przed upływem 12 godzin.

treść żądania POST dokumentu transkrypcji v2:

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

treść żądania POST z transkrypcją v3:

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

### <a name="format-of-v3-transcription-results"></a>Format wyników transkrypcji v3

Schemat wyników transkrypcji został nieco zmieniony, aby wyrównać transkrypcje utworzone przez punkty końcowe w czasie rzeczywistym. Szczegółowy opis nowego formatu można znaleźć w temacie [jak to zrobić](./batch-transcription.md). Schemat wyniku jest publikowany w naszym [repozytorium przykładowym GitHub](https://aka.ms/csspeech/samples) poniżej `samples/batch/transcriptionresult_v3.schema.json` .

Nazwy właściwości są teraz notacji CamelCase — wielkość liter, a wartości dla kanału i prelegenta używają typów całkowitych. Aby wyrównać format czasu trwania z innymi interfejsami API platformy Azure, jest teraz sformatowany zgodnie z opisem w artykule ISO 8601.

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

### <a name="getting-the-content-of-entities-and-the-results"></a>Pobieranie zawartości jednostek i wyników

W wersji 2 linki do plików wejściowych lub wynikowych zostały opisane w pozostałej części metadanych jednostki. Poprawa w wersji 3 oznacza wyraźne rozdzielenie metadanych jednostki, które są zwracane przez polecenie GET on `$.self` oraz szczegóły i poświadczenia, aby uzyskać dostęp do plików wynikowych. Ta separacja pomaga w ochronie danych klienta i umożliwia precyzyjne sterowanie okresem ważności poświadczeń.

W wersji 3 istnieje właściwość o nazwie w `files` obszarze linki na wypadek, gdy jednostka uwidacznia dane (zestawy danych, transkrypcje, punkty końcowe, obliczenia). GET on `$.links.files` zwróci listę plików i adres URL sygnatury dostępu współdzielonego, aby uzyskać dostęp do zawartości każdego pliku. Aby kontrolować okres ważności adresów URL sygnatury dostępu współdzielonego, `sasValidityInSeconds` można użyć parametru zapytania do określenia okresu istnienia.

Transkrypcja v2:

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

Transkrypcja v3:

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

Następnie uzyskasz `$.links.files` :

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

`kind`Wskazuje format zawartości pliku. W przypadku transkrypcji pliki typu `TranscriptionReport` są podsumowaniem zadania i plików rodzaju `Transcription` są wynikiem samego zadania.

### <a name="customizing-models"></a>Dostosowywanie modeli

Przed v3 wystąpił rozróżnienie między "modelem akustycznym" i "modelem języka" podczas uczenia modelu. Różnica ta spowodowała konieczność określenia wielu modeli podczas tworzenia punktów końcowych lub transkrypcji. Aby uprościć ten proces dla obiektu wywołującego, usunęliśmy różnice i wszystkie zależą od zawartości zestawów danych, które są używane do uczenia modelu. Dzięki tej zmianie Tworzenie modelu obsługuje teraz mieszane zestawy danych (dane języka i dane akustyczne). Punkty końcowe i transkrypcje wymagają teraz tylko jednego modelu.

W przypadku tej zmiany należy usunąć wymaganie elementu a `kind` w wpisie, a `datasets[]` teraz może zawierać wiele zestawów danych o takich samych lub mieszanych typach.

W celu poprawienia wyników przeszkolonego modelu dane akustyczne są automatycznie używane wewnętrznie do szkolenia w języku. Ogólnie rzecz biorąc, modele utworzone za pomocą interfejsu API v3 zapewniają dokładniejsze wyniki niż modele utworzone za pomocą interfejsu API v2.

### <a name="retrieving-base-and-custom-models"></a>Pobieranie modeli podstawowych i niestandardowych

Aby uprościć pobieranie dostępnych modeli, wersja 3 oddzielona kolekcje "modele podstawowe" od samego klienta "modele dostosowane". Te dwie trasy są teraz `GET /speechtotext/v3.0/models/base` i `GET /speechtotext/v3.0/models/` .

Poprzednio wszystkie modele zostały zwrócone razem w pojedynczej odpowiedzi.

### <a name="name-of-an-entity"></a>Nazwa jednostki

Nazwa właściwości `name` została zmieniona na `displayName` . Jest to wyrównane do innych interfejsów API platformy Azure, aby nie wskazywały właściwości tożsamości. Wartość tej właściwości nie może być unikatowa i można ją zmienić po utworzeniu jednostki za pomocą elementu `PATCH` .

Transkrypcja v2:

```json
{
    "name": "Transcription using locale en-US"
}
```

Transkrypcja v3:

```json
{
    "displayName": "Transcription using locale en-US"
}
```

### <a name="accessing-referenced-entities"></a>Uzyskiwanie dostępu do przywoływanych jednostek

W wersji 2 przywoływane jednostki zostały zawsze wbudowane, na przykład używane modele punktu końcowego. Zagnieżdżanie jednostek spowodowało duże odpowiedzi, a konsumenci rzadko zużywają zawartość zagnieżdżoną. Aby zmniejszyć rozmiar odpowiedzi i zwiększyć wydajność dla wszystkich użytkowników interfejsu API, jednostki, do których istnieją odwołania, nie są już wbudowane w odpowiedzi. Zamiast tego jest używane odwołanie do innej jednostki, które może być bezpośrednio używane. To odwołanie może być używane dla kolejnego `GET` (również adresu URL), następującego po tym samym wzorcu co `self` link.

Transkrypcja v2:

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

Transkrypcja v3:

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

Na wypadek, gdyby trzeba było korzystać ze szczegółowych informacji o modelu przywoływanym, jak pokazano w powyższym przykładzie, Uprość problem z poleceniem GET `$.model.self` .

### <a name="retrieving-endpoint-logs"></a>Pobieranie dzienników punktów końcowych

Wersja V2 usługi obsługiwała rejestrowanie odpowiedzi punktów końcowych. Aby można było pobrać wyniki punktu końcowego z v2, musiał on utworzyć "Eksport danych", który reprezentuje migawkę wyników zdefiniowanych przez zakres czasu. Proces eksportowania partii danych stał się elastyczny. Interfejs API v3 zapewnia dostęp do poszczególnych plików i umożliwia iterację.

Pomyślnie uruchomiono punkt końcowy v3:

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

Odpowiedź GET `$.links.logs` :

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

Stronicowanie dzienników punktów końcowych działa podobnie jak wszystkie inne kolekcje, z tą różnicą, że nie można określić przesunięcia. Ze względu na dużą ilość dostępnych danych należy zaimplementować stronicowanie oparte na serwerze.

W wersji 3 każdy dziennik punktu końcowego można usunąć pojedynczo, wydając usunięcie `self` pliku lub za pomocą polecenia Delete on `$.links.logs` . Aby określić dane końcowe, parametr zapytania `endDate` można dodać do żądania.

### <a name="using-custom-properties"></a>Korzystanie z właściwości "Custom"

Aby oddzielić właściwości niestandardowe od opcjonalnych właściwości konfiguracji, wszystkie jawnie nazwane właściwości znajdują się teraz we `properties` właściwości i wszystkie właściwości zdefiniowane przez wywołujących znajdują się teraz we `customProperties` właściwości.

Jednostka transkrypcji v2

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

Jednostka transkrypcji v3

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

Ta zmiana umożliwiła również użycie prawidłowych typów we wszystkich jawnie nazwanych właściwościach `properties` (na przykład bool zamiast String).

### <a name="response-headers"></a>Nagłówki odpowiedzi

V3 nie zwraca już nagłówka `Operation-Location` oprócz nagłówka `Location` w żądaniach post. Wartość obu nagłówków, która była dokładnie taka sama. Są teraz `Location` zwracane tylko.

Ponieważ nowa wersja interfejsu API jest teraz zarządzana przez usługę Azure API Management (APIM), nagłówki powiązane z ograniczeniami `X-RateLimit-Limit` `X-RateLimit-Remaining` i `X-RateLimit-Reset` nie znajdują się w nagłówkach odpowiedzi.

### <a name="accuracy-tests"></a>Testy dokładności

Zmieniono nazwy testów dokładności na oceny, ponieważ nowa nazwa opisuje lepiej to, co reprezentuje. Ścieżki wiadomości to na przykład "https://{region}. API. poznawcze. Microsoft. com/speechtotext/v 3.0/Evaluations".
