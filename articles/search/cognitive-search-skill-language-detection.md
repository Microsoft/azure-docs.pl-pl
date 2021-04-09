---
title: Umiejętność wykrywania języka
titleSuffix: Azure Cognitive Search
description: Oblicza niestrukturalny tekst, a dla każdego rekordu zwraca identyfikator języka z oceną wskazującą siłę analizy w potoku wzbogacenia AI na platformie Azure Wyszukiwanie poznawcze.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 078a9312a7ee1b3b0eafd000928ed74348a540c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548057"
---
#   <a name="language-detection-cognitive-skill"></a>Umiejętność wykrywania języka

Umiejętność **wykrywanie języka** wykrywa język tekstu wejściowego i raportuje jeden kod języka dla każdego dokumentu przesłanego w żądaniu. Kod języka jest sparowany z wynikiem wskazującym na siłę analizy. Ta umiejętność używa modeli uczenia maszynowego zapewnianych przez [Analiza tekstu](../cognitive-services/text-analytics/overview.md) w Cognitive Services.

Ta funkcja jest szczególnie przydatna, gdy trzeba dostarczyć język tekstu jako dane wejściowe do innych umiejętności (na przykład [umiejętności analiza tonacji](cognitive-search-skill-sentiment.md) lub [umiejętności podziału tekstu](cognitive-search-skill-textsplit.md)).

Wykrywanie języka korzysta z bibliotek przetwarzania języka naturalnego Bing, które przekraczają liczbę [obsługiwanych języków i regionów](../cognitive-services/text-analytics/language-support.md) wymienionych dla analiza tekstu. Dokładna lista języków nie jest publikowana, ale zawiera wszystkie języki szeroko wypowiadane oraz warianty, dialekty i niektóre języki regionalne i kulturowe. Jeśli masz zawartość wyrażoną w rzadziej używanym języku, możesz [wypróbować interfejs API wykrywanie języka](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) , aby zobaczyć, czy zwraca kod. Odpowiedź dla języków, których nie można wykryć, to `(Unknown)` .

> [!NOTE]
> Podczas rozszerzania zakresu przez zwiększenie częstotliwości przetwarzania, Dodawanie większej liczby dokumentów lub Dodawanie algorytmów AI, należy [dołączyć Cognitive Services rozliczanego zasobu](cognitive-search-attach-cognitive-services.md). Opłaty naliczane podczas wywoływania interfejsów API w Cognitive Services oraz do wyodrębniania obrazów w ramach etapu łamania dokumentu w usłudze Azure Wyszukiwanie poznawcze. Nie są naliczane opłaty za Wyodrębnianie tekstu z dokumentów.
>
> Do wykonania wbudowanych umiejętności są naliczane opłaty za istniejące [Cognitive Services cena płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/cognitive-services/)użyciem. Cennik wyodrębniania obrazów został opisany na [stronie cennika usługi Azure wyszukiwanie poznawcze](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft. umiejętności. Text. LanguageDetectionSkill

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekordu powinien składać się z 50 000 znaków mierzonych przez [`String.Length`](/dotnet/api/system.string.length) . Jeśli musisz podzielić dane przed wysłaniem ich do umiejętności wykrywania języka, możesz użyć [umiejętności podziału tekstu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry umiejętności

W nazwach parametrów jest rozróżniana wielkość liter.

| Dane wejściowe | Opis |
|---------------------|-------------|
| `defaultCountryHint` | Obowiązkowe Kod kraju w formacie ISO 3166-1 Alpha-2 2 można podać jako wskazówkę do modelu wykrywania języka, jeśli nie można odróżnić tego języka. Aby uzyskać więcej informacji [, zobacz dokumentację analiza tekstu](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content) w tym temacie. `defaultCountryHint`Parametr jest używany z dokumentami, które nie określają `countryHint` jawnie danych wejściowych.  |
| `modelVersion`   | Obowiązkowe Wersja modelu do użycia podczas wywoływania usługi analiza tekstu. Domyślnie będzie to najnowsza dostępna wartość, gdy nie zostanie określony. Zalecamy, aby nie określać tej wartości, chyba że jest to absolutnie konieczne. Aby uzyskać więcej informacji, zobacz artykuł [przechowywanie wersji modeli w interfejs API analizy tekstu](../cognitive-services/text-analytics/concepts/model-versioning.md) . |

## <a name="skill-inputs"></a>Dane wejściowe kwalifikacji

W nazwach parametrów jest rozróżniana wielkość liter.

| Dane wejściowe     | Opis |
|--------------------|-------------|
| `text` | Tekst do analizy.|
| `countryHint` | Kod ISO 3166-1 Alpha-2 2 Letter, który ma być używany jako Wskazówka do modelu wykrywania języka, jeśli nie może odróżnić języka. Aby uzyskać więcej informacji [, zobacz dokumentację analiza tekstu](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content) w tym temacie. |

## <a name="skill-outputs"></a>Wyniki umiejętności

| Nazwa wyjściowa    | Opis |
|--------------------|-------------|
| `languageCode` | Kod języka ISO 6391 dla zidentyfikowanego języka. Na przykład "en". |
| `languageName` | Nazwa języka. Na przykład "angielski". |
| `score` | Wartość z zakresu od 0 do 1. Prawdopodobieństwo poprawnego zidentyfikowania języka. Wynik może być mniejszy niż 1, jeśli zdanie ma Języki mieszane.  |

##  <a name="sample-definition"></a>Definicja Przykładowa

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "countryHint",
        "source": "/document/countryHint"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
      }

    ]
  }
```

##  <a name="sample-input"></a>Przykładowe dane wejściowe

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "impossible",
             "countryHint": "fr"
           }
      }
    ]
```


##  <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      },
      {
        "recordId": "3",
        "data":
            {
              "languageCode": "fr",
              "languageName": "French",
              "score": 1,
            }
      }
    ]
}
```

## <a name="see-also"></a>Zobacz też

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)