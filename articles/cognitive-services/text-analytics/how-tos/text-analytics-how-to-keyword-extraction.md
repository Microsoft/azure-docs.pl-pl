---
title: Wyodrębnianie kluczowych fraz przy użyciu interfejsu API REST analiza tekstu
titleSuffix: Azure Cognitive Services
description: Jak wyodrębnić kluczowe frazy za pomocą interfejsu API REST analiza tekstu z usługi Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/29/2021
ms.author: aahi
ms.openlocfilehash: fdf24fdc56d39d93fed0009e2fadbafd7f97db6c
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280500"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Przykład: Jak wyodrębnić kluczowe frazy przy użyciu analiza tekstu

[Interfejs API wyodrębniania kluczowych fraz](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) ocenia tekst bez struktury i dla każdego dokumentu JSON zwraca listę kluczowych fraz.

Ta możliwość jest przydatna, jeśli chcesz szybko zidentyfikować główne tematy w kolekcji dokumentów. Na przykład dla tekstu wejściowego „Jedzenie było pyszne, a serwowała je doskonała obsługa” usługa zwraca główne tematy wypowiedzi: „jedzenie” i „doskonała obsługa”.

Aby uzyskać więcej informacji, zobacz listę [obsługiwanych języków](../language-support.md).

> [!TIP]
> * Analiza tekstu udostępnia również obraz kontenera platformy Docker oparty na systemie Linux na potrzeby wyodrębniania kluczowych fraz, można więc [zainstalować i uruchomić kontener analizy tekstu](text-analytics-how-to-install-containers.md) blisko danych.
> * Możesz również użyć tej funkcji [asynchronicznie](text-analytics-how-to-call-api.md) za pomocą `/analyze` punktu końcowego.

## <a name="preparation"></a>Przygotowanie

Wyodrębnianie kluczowych fraz działa najlepiej na większej ilości tekstu. Jest to przeciwieństwo analizy tonacji, która działa lepiej na mniejszej ilości tekstu. Aby uzyskać najlepsze wyniki obydwu operacji, warto rozważyć odpowiednią zmianę struktury danych wejściowych.

Musisz mieć dokumenty JSON w tym formacie: ID, text, language

Rozmiar dokumentu nie może być mniejszy niż 5 120 znaków i może zawierać do 1 000 elementów (identyfikatorów) na kolekcję. Kolekcja jest przesyłana w treści żądania. Poniższy przykład przedstawia zawartość, którą możesz przesłać w celu wyodrębnienia kluczowych fraz. 

Zobacz [, jak wywoływać interfejs API analizy tekstu,](text-analytics-how-to-call-api.md) Aby uzyskać więcej informacji na temat obiektów żądania i odpowiedzi.  

### <a name="example-synchronous-request-object"></a>Przykład obiektu żądania synchronicznego


```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

### <a name="example-asynchronous-request-object"></a>Przykładowy obiekt żądania asynchronicznego

Począwszy od programu `v3.1-preview.3` , żądania ner można wysyłać asynchronicznie przy użyciu `/analyze` punktu końcowego.


```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
    }
}
```

## <a name="step-1-structure-the-request"></a>Krok 1: Określenie struktury żądania

Aby uzyskać informacje na temat definicji żądania, zobacz [jak wywołać interfejs API analizy tekstu](text-analytics-how-to-call-api.md). Dla wygody poniżej ponownie podano odpowiednie kroki:

+ Utwórz żądanie **POST**. Zapoznaj się z dokumentacją interfejsu API dla tego żądania: [interfejs API fraz kluczy](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases).

+ Ustaw punkt końcowy HTTP na potrzeby wyodrębniania fraz kluczowych przy użyciu zasobu analiza tekstu na platformie Azure lub [kontenera analiza tekstu](text-analytics-how-to-install-containers.md)wystąpienia. Jeśli używasz interfejsu API synchronicznie, musisz uwzględnić `/text/analytics/v3.0/keyPhrases` w adresie URL. Na przykład: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`.

+ Ustaw nagłówek żądania w taki sposób, aby zawierał [klucz dostępu](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) dla operacji analiza tekstu.

+ W treści żądania podaj kolekcję dokumentów JSON przygotowaną na potrzeby tej analizy.

> [!Tip]
> Użyj programu [Postman](text-analytics-how-to-call-api.md) lub otwórz **konsolę testowania interfejsu API** w [dokumentacji](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases), aby określić strukturę żądania i przesłać je do usługi za pomocą operacji POST.

## <a name="step-2-post-the-request"></a>Krok 2: Wysłanie żądania

Analiza jest wykonywana po odebraniu żądania. Aby uzyskać informacje o rozmiarze i liczbie żądań wysyłanych na minutę lub na sekundę, zobacz artykuł dotyczący [limitów danych](../concepts/data-limits.md) .

Pamiętaj, że usługa jest bezstanowa. Żadne dane nie są przechowywane na koncie. Wyniki są zwracane natychmiast w odpowiedzi.

## <a name="step-3-view-results"></a>Krok 3: Wyświetlenie wyników

Wszystkie żądania POST zwracają odpowiedź w formacie JSON z identyfikatorami i wykrytymi właściwościami. Kolejność zwróconych zwrotów kluczy jest określana wewnętrznie przez model.

Dane wyjściowe są zwracane natychmiast. Wyniki można przesłać strumieniowo do aplikacji, która akceptuje kod JSON, lub zapisać do pliku w systemie lokalnym, a następnie zaimportować do aplikacji, która umożliwia sortowanie i wyszukiwanie danych oraz manipulowanie nimi.

Przykład danych wyjściowych dotyczących wyodrębniania fraz kluczowych z punktu końcowego v 3.1 — Preview. 2 jest przedstawiony tutaj:

### <a name="synchronous-result"></a>Wynik synchroniczny

```json
    {
       "documents":[
          {
             "id":"1",
             "keyPhrases":[
                "year",
                "trail",
                "trip",
                "views",
                "hike"
             ],
             "warnings":[]
          },
          {
             "id":"2",
             "keyPhrases":[
                "marked trails",
                "Worst hike",
                "goners"
             ],
             "warnings":[]
          },
          {
             "id":"3",
             "keyPhrases":[
                "trail",
                "small children",
                "family"
             ],
             "warnings":[]
          },
          {
             "id":"4",
             "keyPhrases":[
                "spectacular views",
                "trail",
                "Worth",
                "area"
             ],
             "warnings":[]
          },
          {
             "id":"5",
             "keyPhrases":[
                "places",
                "beautiful views",
                "favorite trail",
                "rest"
             ],
             "warnings":[]
          }
       ],
       "errors":[],
       "modelVersion":"2020-07-01"
    }

```
Jak wspomniano, Analizator znajdzie i odrzuca nieważne słowa i utrzymuje pojedyncze terminy lub frazy, które wyglądają jako temat lub obiekt zdania.

### <a name="asynchronous-result"></a>Wynik asynchroniczny

Jeśli używasz `/analyze` punktu końcowego dla operacji asynchronicznej, otrzymasz odpowiedź zawierającą zadania wysłane do interfejsu API.

```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```


## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono koncepcje i przepływ pracy służące do wyodrębniania fraz kluczowych przy użyciu analiza tekstu w Cognitive Services. Podsumowanie:

+ [Interfejs API wyodrębniania kluczowych fraz](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) jest dostępny dla wybranych języków.
+ Dokumenty JSON w treści żądania obejmują identyfikator, tekst i kod języka.
+ Żądanie POST jest `/keyphrases` `/analyze` punktem końcowym lub, przy użyciu spersonalizowanego [klucza dostępu i punktu końcowego](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , który jest prawidłowy dla Twojej subskrypcji.
+ Dane wyjściowe odpowiedzi, które składają się z kluczowych słów i fraz dla każdego identyfikatora dokumentu, mogą być przesyłane strumieniowo do dowolnej aplikacji, która akceptuje kod JSON, w tym Microsoft Office Excel i Power BI, aby nazwać kilka.

## <a name="see-also"></a>Zobacz też

 [Omówienie analizy tekstu](../overview.md) [Frequently asked questions (FAQ) (Często zadawane pytania (FAQ))](../text-analytics-resource-faq.md)</br>
 [Strona produktu analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Następne kroki

* [Przegląd analizy tekstu](../overview.md)
* [Korzystanie z biblioteki klienta analiza tekstu](../quickstarts/client-libraries-rest-api.md)
* [Co nowego](../whats-new.md)
