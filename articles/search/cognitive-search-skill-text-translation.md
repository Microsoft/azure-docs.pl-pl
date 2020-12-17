---
title: Umiejętność tłumaczenia tekstu
titleSuffix: Azure Cognitive Search
description: Oblicza tekst i dla każdego rekordu zwraca tekst przetłumaczony na określony język docelowy w potoku wzbogacenia AI na platformie Azure Wyszukiwanie poznawcze.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0953d750ee8b59e9889512bb64cfd276a0bbeb53
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654868"
---
#   <a name="text-translation-cognitive-skill"></a>Umiejętność tłumaczenia tekstu

Umiejętność **tłumaczenia tekstu** oblicza tekst i dla każdego rekordu zwraca tekst przetłumaczony na określony język docelowy. Ta umiejętność używa [interfejs API tłumaczenia tekstu w usłudze translator v 3.0](../cognitive-services/translator/reference/v3-0-translate.md) dostępnych w Cognitive Services.

Ta funkcja jest przydatna, jeśli oczekujesz, że dokumenty mogą nie być w jednym języku, a w takim przypadku można znormalizować tekst do jednego języka przed indeksowaniem wyszukiwania, przetłumacząc go.  Jest on również przydatny w przypadku przypadków użycia lokalizacji, w których można chcieć mieć kopie tego samego tekstu w wielu językach.

[Interfejs API tłumaczenia tekstu w usłudze translator v 3.0](../cognitive-services/translator/reference/v3-0-reference.md) jest nieregionalną usługą poznawczej, co oznacza, że dane nie są gwarantowane w tym samym regionie co wyszukiwanie poznawcze platformy Azure lub dołączonego zasobu Cognitive Services.

> [!NOTE]
> Podczas rozszerzania zakresu przez zwiększenie częstotliwości przetwarzania, Dodawanie większej liczby dokumentów lub Dodawanie algorytmów AI, należy [dołączyć Cognitive Services rozliczanego zasobu](cognitive-search-attach-cognitive-services.md). Opłaty naliczane podczas wywoływania interfejsów API w Cognitive Services oraz do wyodrębniania obrazów w ramach etapu łamania dokumentu w usłudze Azure Wyszukiwanie poznawcze. Nie są naliczane opłaty za Wyodrębnianie tekstu z dokumentów.
>
> Do wykonania wbudowanych umiejętności są naliczane opłaty za istniejące [Cognitive Services cena płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/cognitive-services/)użyciem. Cennik wyodrębniania obrazów został opisany na [stronie cennika usługi Azure wyszukiwanie poznawcze](https://azure.microsoft.com/pricing/details/search/).

## <a name="odatatype"></a>@odata.type  
Microsoft. umiejętności. Text. TranslationSkill

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekordu powinien składać się z 50 000 znaków mierzonych przez [`String.Length`](/dotnet/api/system.string.length) . Jeśli musisz podzielić dane przed wysłaniem ich do umiejętności tłumaczenia tekstu, rozważ użycie [umiejętności podziału tekstu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry umiejętności

W nazwach parametrów jest rozróżniana wielkość liter.

| Dane wejściowe | Opis |
|---------------------|-------------|
| defaultToLanguageCode | Potrzeb Kod języka służący do tłumaczenia dokumentów na dokumenty, które nie określają jawnie języka w języku. <br/> Zapoznaj się [z pełną listą obsługiwanych języków](../cognitive-services/translator/language-support.md). |
| defaultFromLanguageCode | Obowiązkowe Kod języka służący do tłumaczenia dokumentów z dokumentów, które nie określają jawnie języka od.  Jeśli defaultFromLanguageCode nie zostanie określony, automatyczne wykrywanie języka zapewniane przez interfejs API tłumaczenia tekstu w usłudze Translator zostanie użyte do określenia języka z programu. <br/> Zapoznaj się [z pełną listą obsługiwanych języków](../cognitive-services/translator/language-support.md). |
| suggestedFrom | Obowiązkowe Kod języka służący do tłumaczenia dokumentów z, gdy nie są podane żadne dane wejściowe fromLanguageCode ani parametr defaultFromLanguageCode, a automatyczne wykrywanie języka nie powiedzie się.  Jeśli język suggestedFrom nie zostanie określony, jako język suggestedFrom będzie używany język angielski (EN). <br/> Zapoznaj się [z pełną listą obsługiwanych języków](../cognitive-services/translator/language-support.md). |

## <a name="skill-inputs"></a>Dane wejściowe kwalifikacji

| Nazwa wejściowa     | Opis |
|--------------------|-------------|
| tekst | Tekst do tłumaczenia.|
| toLanguageCode    | Ciąg wskazujący język, w którym powinien zostać przetłumaczony tekst. Jeśli nie określono tego parametru wejściowego, defaultToLanguageCode będzie służyć do tłumaczenia tekstu. <br/>Zapoznaj się [z pełną listą obsługiwanych języków](../cognitive-services/translator/language-support.md)|
| fromLanguageCode  | Ciąg wskazujący bieżący język tekstu. Jeśli ten parametr nie jest określony, defaultFromLanguageCode (lub automatyczne wykrywanie języka, jeśli nie podano defaultFromLanguageCode) będzie używany do tłumaczenia tekstu. <br/>Zapoznaj się [z pełną listą obsługiwanych języków](../cognitive-services/translator/language-support.md)|

## <a name="skill-outputs"></a>Wyniki umiejętności

| Nazwa wyjściowa    | Opis |
|--------------------|-------------|
| translatedText | Wynikowy ciąg tłumaczenia tekstu z translatedFromLanguageCode na translatedToLanguageCode.|
| translatedToLanguageCode  | Ciąg wskazujący kod języka, do którego tekst został przetłumaczony. Przydatne w przypadku tłumaczenia na wiele języków i chcesz, aby śledzenie tego, który tekst jest używany w języku.|
| translatedFromLanguageCode    | Ciąg wskazujący kod języka, z którego tekst został przetłumaczony. Przydatne, jeśli wybrano opcję automatycznego wykrywania języka, ponieważ dane wyjściowe będą dawać wynik tego wykrywania.|

##  <a name="sample-definition"></a>Definicja Przykładowa

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
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
          "text": "We hold these truths to be self-evident, that all men are created equal."
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
}
```


##  <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>Błędy i ostrzeżenia
Jeśli podano nieobsługiwany kod języka dla języka "from" lub "do", zostanie wygenerowany błąd, a tekst nie zostanie przetłumaczony.
Jeśli tekst jest pusty, zostanie wygenerowane ostrzeżenie.
Jeśli tekst jest większy niż 50 000 znaków, zostaną przetłumaczone tylko pierwsze 50 000 znaków i zostanie wygenerowane ostrzeżenie.

## <a name="see-also"></a>Zobacz także

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)