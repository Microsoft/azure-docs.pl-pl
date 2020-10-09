---
title: Wykrywanie języka za pomocą interfejsu API REST analiza tekstu
titleSuffix: Azure Cognitive Services
description: Wykryj język przy użyciu interfejsu API REST analiza tekstu z usługi Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: b5936d68ba98054eb6d98860c6edfaf12d6736d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710129"
---
# <a name="example-detect-language-with-text-analytics"></a>Przykład: wykrywanie języka za pomocą analiza tekstu

Funkcja [wykrywanie języka](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) interfejsu API REST platformy Azure analiza tekstu szacuje dane wejściowe tekstu dla każdego dokumentu i zwraca identyfikatory języka z wynikiem, który wskazuje na siłę analizy.

Ta możliwość jest przydatna w przypadku magazynów zawartości przechowujących dowolne teksty, których język nie jest znany. Wyniki analizy możesz przeanalizować w celu ustalenia, który język jest używany w wejściowym dokumencie. Odpowiedź zwraca również wynik, który odzwierciedla zaufanie modelu. Wartość wyniku jest z przedziału od 0 do 1.

Funkcja wykrywanie języka może wykryć szeroką gamę języków, wariantów, dialektów i niektórych języków regionalnych lub kulturowych. Dokładna lista języków dla tej funkcji nie jest opublikowana.

Jeśli masz zawartość wyrażoną w rzadziej używanym języku, możesz wypróbować funkcję wykrywanie języka, aby zobaczyć, czy zwraca kod. Odpowiedzi dla języków, których nie można wykryć, to `unknown` .

> [!TIP]
> Analiza tekstu udostępnia również obraz kontenera platformy Docker oparty na systemie Linux na potrzeby wykrywania języka, można więc [zainstalować i uruchomić kontener analizy tekstu](text-analytics-how-to-install-containers.md) blisko danych.

## <a name="preparation"></a>Przygotowanie

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

Musisz mieć dokumenty JSON w tym formacie: ID i Text.

Rozmiar dokumentu musi zawierać 5 120 znaków na dokument. Możesz mieć do 1 000 elementów (identyfikatorów) na kolekcję. Kolekcja jest przesyłana w treści żądania. Poniższy przykład jest przykładem zawartości, którą można przesłać do wykrywania języka:

```json
    {
        "documents": [
            {
                "id": "1",
                "text": "This document is in English."
            },
            {
                "id": "2",
                "text": "Este documento está en inglés."
            },
            {
                "id": "3",
                "text": "Ce document est en anglais."
            },
            {
                "id": "4",
                "text": "本文件为英文"
            },
            {
                "id": "5",
                "text": "Этот документ на английском языке."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Krok 1: Określenie struktury żądania

Aby uzyskać więcej informacji na temat definicji żądania, zobacz [wywoływanie interfejs API analizy tekstu](text-analytics-how-to-call-api.md). Dla wygody poniżej ponownie podano odpowiednie kroki:

+ Utwórz żądanie POST. Aby zapoznać się z dokumentacją interfejsu API dla tego żądania, zobacz [interfejs api wykrywanie języka](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages).

+ Ustaw punkt końcowy HTTP dla wykrywania języka. Użyj zasobu analiza tekstu na platformie Azure lub w [kontenerze analiza tekstu](text-analytics-how-to-install-containers.md)wystąpienia. Musisz uwzględnić `/text/analytics/v3.0/languages` w adresie URL. Przykład: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`.

+ Ustaw nagłówek żądania w taki sposób, aby zawierał [klucz dostępu](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) dla operacji analiza tekstu.

+ W treści żądania podaj kolekcję dokumentów JSON przygotowaną na potrzeby tej analizy.

> [!Tip]
> Użyj programu [Postman](text-analytics-how-to-call-api.md) lub otwórz **konsolę testowania interfejsu API** w [dokumentacji](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages), aby określić strukturę żądania i przesłać je do usługi za pomocą operacji POST.

## <a name="step-2-post-the-request"></a>Krok 2. Publikowanie żądania

Analiza jest wykonywana po odebraniu żądania. Aby uzyskać informacje na temat rozmiaru i liczby żądań wysyłanych na minutę i sekundę, zobacz sekcję [limity danych](../overview.md#data-limits) w przeglądzie.

Pamiętaj, że usługa jest bezstanowa. Żadne dane nie są przechowywane na koncie. Wyniki są zwracane natychmiast w odpowiedzi.


## <a name="step-3-view-the-results"></a>Krok 3. Wyświetlanie wyników

Wszystkie żądania POST zwracają odpowiedź sformatowaną w formacie JSON z identyfikatorami i wykrytymi właściwościami.

Dane wyjściowe są zwracane natychmiast. Można przesyłać strumieniowo wyniki do aplikacji, która akceptuje kod JSON lub zapisuje dane wyjściowe do pliku w systemie lokalnym. Następnie zaimportuj dane wyjściowe do aplikacji, która może być używana do sortowania, wyszukiwania i manipulowania danymi.

Wyniki dla przykładowego żądania powinny wyglądać podobnie do następującego kodu JSON. Zwróć uwagę, że jest to jeden dokument z wieloma elementami. Dane wyjściowe są w języku angielskim. Identyfikatory języka obejmują przyjazną nazwę i kod języka zgodne ze standardem [ISO 639-1](https://www.iso.org/standard/22109.html).

Wynik dodatni 1,0 wyraża najwyższy możliwy poziom ufności analizy.

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "English",
                "iso6391Name": "en",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "2",
            "detectedLanguage": {
                "name": "Spanish",
                "iso6391Name": "es",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "3",
            "detectedLanguage": {
                "name": "French",
                "iso6391Name": "fr",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "4",
            "detectedLanguage": {
                "name": "Chinese_Simplified",
                "iso6391Name": "zh_chs",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "5",
            "detectedLanguage": {
                "name": "Russian",
                "iso6391Name": "ru",
                "confidenceScore": 1.0
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

### <a name="ambiguous-content"></a>Zawartość niejednoznaczna

W niektórych przypadkach może być trudno odróżnić Języki w oparciu o dane wejściowe. Możesz użyć parametru, `countryHint` Aby określić 2-literowy kod kraju/regionu. Domyślnie interfejs API używa "US" jako domyślnego countryHint, aby usunąć to zachowanie, można zresetować ten parametr, ustawiając tę wartość na pusty ciąg `countryHint = ""` .

Na przykład "niemożliwe" jest wspólne dla języka angielskiego i francuskiego, a jeśli jest określony z ograniczonym kontekstem, odpowiedź zostanie wyświetlona na podstawie wskazówki kraju/regionu "US". Jeśli wiadomo, że tekst pochodzi z Francji, można podać to we wskazówce.

**Dane wejściowe**

```json
    {
        "documents": [
            {
                "id": "1",
                "text": "impossible"
            },
            {
                "id": "2",
                "text": "impossible",
                "countryHint": "fr"
            }
        ]
    }
```

Usługa ma teraz dodatkowy kontekst, aby zapewnić lepszą opinię: 

**Dane wyjściowe**

```json
    {
        "documents": [
            {
                "id": "1",
                "detectedLanguages": [
                    {
                        "name": "English",
                        "iso6391Name": "en",
                        "confidenceScore": 1
                    }
                ]
            },
            {
                "id": "2",
                "detectedLanguages": [
                    {
                        "name": "French",
                        "iso6391Name": "fr",
                        "confidenceScore": 1
                    }
                ]
            }
        ],
        "errors": []
    }
```

Jeśli analizator nie może przeanalizować danych wejściowych, zwraca `(Unknown)` . Przykładem jest przesłanie bloku tekstu, który składa się wyłącznie z cyfr arabskich.

```json
    {
        "id": "5",
        "detectedLanguages": [
            {
                "name": "(Unknown)",
                "iso6391Name": "(Unknown)",
                "confidenceScore": "NaN"
            }
        ]
    }
```

### <a name="mixed-language-content"></a>Zawartość języka mieszanego

Zawartość w języku mieszanym w tym samym dokumencie zwraca język z największą reprezentacją zawartości, ale z niższą dodatnią klasyfikacją. Klasyfikacja odzwierciedla krańcową moc oceny. W następującym przykładzie dane wejściowe stanowią mieszankę języków angielskiego, hiszpańskiego i francuskiego. Analizator zlicza znaki w każdym segmencie w celu ustalenia dominującego języka.

**Dane wejściowe**

```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
        }
      ]
    }
```

**Dane wyjściowe**

Wynikowe dane wyjściowe składają się z języka dominującego, z wynikiem niższym niż 1,0, który wskazuje słaby poziom zaufania.

```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "confidencescore": 0.94
            }
          ]
        }
      ],
      "errors": []
    }
```

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono koncepcje i przepływ pracy wykrywania języka przy użyciu analiza tekstu w usłudze Azure Cognitive Services. Wyjaśniono i przedstawiono następujące punkty:

+ [Wykrywanie języka](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) jest dostępne dla szerokiego zakresu języków, wariantów, dialektów i niektórych języków regionalnych lub kulturowych.
+ Dokumenty JSON w treści żądania zawierają identyfikator i tekst.
+ Żądanie POST jest `/languages` punktem końcowym przy użyciu spersonalizowanego [klucza dostępu i punktu końcowego](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , który jest prawidłowy dla Twojej subskrypcji.
+ Dane wyjściowe odpowiedzi składają się z identyfikatorów języka dla każdego identyfikatora dokumentu. Dane wyjściowe mogą być przesyłane strumieniowo do dowolnej aplikacji, która akceptuje kod JSON. Przykładowe aplikacje to programy Excel i Power BI, aby podać kilka nazw.

## <a name="see-also"></a>Zobacz też

* [Przegląd analizy tekstu](../overview.md)
* [Korzystanie z biblioteki klienta analiza tekstu](../quickstarts/text-analytics-sdk.md)
* [Co nowego](../whats-new.md)
