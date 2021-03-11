---
title: Umiejętność tonacji
titleSuffix: Azure Cognitive Search
description: Wyodrębnij pozytywnie negatywny wynik tonacji z tekstu w potoku wzbogacenia AI na platformie Azure Wyszukiwanie poznawcze.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 6519cd952bd1265b4daad3b77b29aabd47ea4cc5
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102547479"
---
# <a name="sentiment-cognitive-skill"></a>Umiejętność tonacji

Umiejętność **tonacji** ocenia niestrukturalny tekst wzdłuż zwracanej do dodatniej wartości składnika Continuum, a dla każdego rekordu zwraca wynik liczbowy z zakresu od 0 do 1. Wyniki zbliżone do 1 oznaczają pozytywne tonacji, a wyniki zbliżone do 0 wskazują na ujemne tonacji. Ta umiejętność używa modeli uczenia maszynowego zapewnianych przez [Analiza tekstu](../cognitive-services/text-analytics/overview.md) w Cognitive Services.

> [!NOTE]
> Podczas rozszerzania zakresu przez zwiększenie częstotliwości przetwarzania, Dodawanie większej liczby dokumentów lub Dodawanie algorytmów AI, należy [dołączyć Cognitive Services rozliczanego zasobu](cognitive-search-attach-cognitive-services.md). Opłaty naliczane podczas wywoływania interfejsów API w Cognitive Services oraz do wyodrębniania obrazów w ramach etapu łamania dokumentu w usłudze Azure Wyszukiwanie poznawcze. Nie są naliczane opłaty za Wyodrębnianie tekstu z dokumentów.
>
> Do wykonania wbudowanych umiejętności są naliczane opłaty za istniejące [Cognitive Services cena płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/cognitive-services/)użyciem. Cennik wyodrębniania obrazów został opisany na [stronie cennika usługi Azure wyszukiwanie poznawcze](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft. umiejętności. Text. SentimentSkill

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekordu powinien składać się z 5000 znaków mierzonych przez [`String.Length`](/dotnet/api/system.string.length) . Jeśli musisz podzielić dane przed wysłaniem ich do analizatora tonacji, użyj [umiejętności podziału tekstu](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Parametry umiejętności

W nazwach parametrów jest rozróżniana wielkość liter.

| Nazwa parametru | Opis |
|----------------|----------------------|
| `defaultLanguageCode` | obowiązkowe Kod języka, który ma zostać zastosowany do dokumentów, które nie określają jawnie języka. <br/> Zapoznaj się [z pełną listą obsługiwanych języków](../cognitive-services/text-analytics/language-support.md) |

## <a name="skill-inputs"></a>Dane wejściowe kwalifikacji 

| Nazwa wejściowa | Opis |
|--------------------|-------------|
| `text` | Tekst do analizy.|
| `languageCode`    |  Obowiązkowe Ciąg wskazujący język rekordów. Jeśli ten parametr nie jest określony, wartością domyślną jest "en". <br/>Zapoznaj się [z pełną listą obsługiwanych języków](../cognitive-services/text-analytics/language-support.md).|

## <a name="skill-outputs"></a>Wyniki umiejętności

| Nazwa wyjściowa | Opis |
|--------------------|-------------|
| `score` | Wartość z zakresu od 0 do 1 reprezentująca tonacji przeanalizowanego tekstu. Wartości zbliżone do 0 mają ujemną tonacji, blisko 0,5 mają neutralną tonacji, a wartości zbliżone do 1 mają dodatnie tonacji.|


##  <a name="sample-definition"></a>Definicja Przykładowa

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languagecode"
        }
    ],
    "outputs": [
        {
            "name": "score",
            "targetName": "mySentiment"
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
            "data": {
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
                "languageCode": "en"
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
            "data": {
                "score": 0.01
            }
        }
    ]
}
```

## <a name="warning-cases"></a>Przypadki ostrzegawcze
Jeśli tekst jest pusty, zostanie wygenerowane ostrzeżenie i nie zostanie zwrócony żaden wynik tonacji.
Jeśli język nie jest obsługiwany, zostanie wygenerowane ostrzeżenie i nie zostanie zwrócony żaden wynik tonacji.

## <a name="see-also"></a>Zobacz też

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)