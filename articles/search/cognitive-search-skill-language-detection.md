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
ms.openlocfilehash: 087989638193bb59001ed33c4ee253d61682d8bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935997"
---
#   <a name="language-detection-cognitive-skill"></a>Umiejętność wykrywania języka

Umiejętność **wykrywanie języka** wykrywa język tekstu wejściowego i raportuje jeden kod języka dla każdego dokumentu przesłanego w żądaniu. Kod języka jest sparowany z wynikiem wskazującym na siłę analizy. Ta umiejętność używa modeli uczenia maszynowego zapewnianych przez [Analiza tekstu](../cognitive-services/text-analytics/overview.md) w Cognitive Services.

Ta funkcja jest szczególnie przydatna, gdy trzeba dostarczyć język tekstu jako dane wejściowe do innych umiejętności (na przykład [umiejętności analiza tonacji](cognitive-search-skill-sentiment.md) lub [umiejętności podziału tekstu](cognitive-search-skill-textsplit.md)).

Wykrywanie języka korzysta z bibliotek przetwarzania języka naturalnego Bing, które przekraczają liczbę [obsługiwanych języków i regionów](../cognitive-services/text-analytics/language-support.md) wymienionych dla analiza tekstu. Dokładna lista języków nie jest publikowana, ale zawiera wszystkie języki szeroko wypowiadane oraz warianty, dialekty i niektóre języki regionalne i kulturowe. Jeśli masz zawartość wyrażoną w rzadziej używanym języku, możesz [wypróbować interfejs API wykrywanie języka](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) , aby zobaczyć, czy zwraca kod. Odpowiedź dla języków, których nie można wykryć, to `unknown` .

> [!NOTE]
> Podczas rozszerzania zakresu przez zwiększenie częstotliwości przetwarzania, Dodawanie większej liczby dokumentów lub Dodawanie algorytmów AI, należy [dołączyć Cognitive Services rozliczanego zasobu](cognitive-search-attach-cognitive-services.md). Opłaty naliczane podczas wywoływania interfejsów API w Cognitive Services oraz do wyodrębniania obrazów w ramach etapu łamania dokumentu w usłudze Azure Wyszukiwanie poznawcze. Nie są naliczane opłaty za Wyodrębnianie tekstu z dokumentów.
>
> Do wykonania wbudowanych umiejętności są naliczane opłaty za istniejące [Cognitive Services cena płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/cognitive-services/)użyciem. Cennik wyodrębniania obrazów został opisany na [stronie cennika usługi Azure wyszukiwanie poznawcze](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft. umiejętności. Text. LanguageDetectionSkill

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekordu powinien składać się z 50 000 znaków mierzonych przez [`String.Length`](/dotnet/api/system.string.length) . Jeśli musisz podzielić dane przed wysłaniem ich do umiejętności wykrywania języka, możesz użyć [umiejętności podziału tekstu](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Dane wejściowe kwalifikacji

W nazwach parametrów jest rozróżniana wielkość liter.

| Dane wejściowe     | Opis |
|--------------------|-------------|
| `text` | Tekst do analizy.|

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
      }
    ]
}
```


## <a name="error-cases"></a>Przypadki błędów
Jeśli tekst jest wyrażony w nieobsługiwanym języku, generowany jest błąd i nie jest zwracany żaden identyfikator języka.

## <a name="see-also"></a>Zobacz też

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)