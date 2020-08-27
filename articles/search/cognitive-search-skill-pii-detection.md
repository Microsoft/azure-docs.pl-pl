---
title: Umiejętność wykrywania przez dane OSOBowe (wersja zapoznawcza)
titleSuffix: Azure Cognitive Search
description: Wyodrębnij i zamaskowanie danych osobowych z tekstu w potoku wzbogacania na platformie Azure Wyszukiwanie poznawcze. Ta umiejętność jest obecnie dostępna w publicznej wersji zapoznawczej.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: b2e35ba083e376f519ccbc32c71c1ac9b1e03a41
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935300"
---
#    <a name="pii-detection-cognitive-skill"></a>Umiejętność wykrywania przez dane OSOBowe

> [!IMPORTANT] 
> Ta umiejętność jest obecnie dostępna w publicznej wersji zapoznawczej. Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Obecnie nie ma obsługi portalu lub zestawu SDK platformy .NET.

Umiejętność **wykrywania** danych osobowych wyodrębnia dane osobowe z tekstu wejściowego i zapewnia możliwość zamaskowania tego tekstu na różne sposoby. Ta umiejętność używa modeli uczenia maszynowego zapewnianych przez [Analiza tekstu](../cognitive-services/text-analytics/overview.md) w Cognitive Services.

> [!NOTE]
> Podczas rozszerzania zakresu przez zwiększenie częstotliwości przetwarzania, Dodawanie większej liczby dokumentów lub Dodawanie algorytmów AI, należy [dołączyć Cognitive Services rozliczanego zasobu](cognitive-search-attach-cognitive-services.md). Opłaty naliczane podczas wywoływania interfejsów API w Cognitive Services oraz do wyodrębniania obrazów w ramach etapu łamania dokumentu w usłudze Azure Wyszukiwanie poznawcze. Nie są naliczane opłaty za Wyodrębnianie tekstu z dokumentów.
>
> Do wykonania wbudowanych umiejętności są naliczane opłaty za istniejące [Cognitive Services cena płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/cognitive-services/)użyciem. Cennik wyodrębniania obrazów został opisany na [stronie cennika usługi Azure wyszukiwanie poznawcze](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft. umiejętności. Text. PIIDetectionSkill

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekordu powinien składać się z 50 000 znaków mierzonych przez [`String.Length`](/dotnet/api/system.string.length) . Jeśli musisz podzielić dane przed wysłaniem ich do umiejętności, rozważ użycie [umiejętności podziału tekstu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry umiejętności

W parametrach jest rozróżniana wielkość liter i wszystkie są opcjonalne.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| `defaultLanguageCode` |    Kod języka tekstu wejściowego. Na razie tylko `en` jest obsługiwane. |
| `minimumPrecision` | Wartość z zakresu od 0,0 do 1,0. Jeśli wynik pewności (w `piiEntities` danych wyjściowych) jest mniejszy niż wartość ustawiona `minimumPrecision` , jednostka nie jest zwracana ani zamaskowane. Wartość domyślna to 0,0. |
| `maskingMode` | Parametr, który zapewnia różne sposoby maskowania wykryte dane OSOBowe w tekście wejściowym. Obsługiwane są następujące opcje: <ul><li>`none` (ustawienie domyślne): oznacza to, że maskowanie nie zostanie wykonane i `maskedText` wyjście nie zostanie zwrócone. </li><li> `redact`: Ta opcja spowoduje usunięcie wykrytych jednostek z tekstu wejściowego i nie zastępowanie ich żadnymi. Należy zauważyć, że w tym przypadku przesunięcie w `piiEntities` danych wyjściowych będzie w odniesieniu do oryginalnego tekstu, a nie do zamaskowanego tekstu. </li><li> `replace`: Ta opcja spowoduje zamianę wykrytych jednostek na znak określony w `maskingCharacter` parametrze.  Znak zostanie powtórzony do długości wykrytej jednostki, aby przesunięcia odpowiednio odpowiadały zarówno dane wejściowe, jak i dane wyjściowe `maskedText` .</li></ul> |
| `maskingCharacter` | Znak, który będzie używany do zamaskowania tekstu, jeśli `maskingMode` parametr jest ustawiony na `replace` . Obsługiwane są następujące opcje: `*` (domyślne), `#` , `X` . Ten parametr może mieć `null` wartość tylko wtedy `maskingMode` , gdy nie jest ustawiony na `replace` . |


## <a name="skill-inputs"></a>Dane wejściowe kwalifikacji

| Nazwa wejściowa      | Opis                   |
|---------------|-------------------------------|
| `languageCode`    | Opcjonalny. Wartość domyślna to `en`.  |
| `text`          | Tekst do analizy.          |

## <a name="skill-outputs"></a>Wyniki umiejętności

| Nazwa wyjściowa      | Opis                   |
|---------------|-------------------------------|
| `piiEntities` | Tablica typów złożonych, które zawierają następujące pola: <ul><li>tekst (rzeczywiste dane OSOBowe jako wyodrębnione)</li> <li>typ</li><li>Podtyp</li><li>wynik (wyższa wartość oznacza, że bardziej prawdopodobnie jest to rzeczywista jednostka)</li><li>Przesunięcie (do tekstu wejściowego)</li><li>length</li></ul> </br> [Możliwe typy i podtypy można znaleźć tutaj.](../cognitive-services/text-analytics/named-entity-types.md?tabs=personal) |
| `maskedText` | Jeśli `maskingMode` jest ustawiona na wartość inną niż `none` , dane wyjściowe będą wynikiem przeprowadzenia maskowania dla tekstu wejściowego, zgodnie z opisem wybranej opcji `maskingMode` .  Jeśli `maskingMode` jest ustawiona na `none` , dane wyjściowe nie będą obecne. |

##    <a name="sample-definition"></a>Definicja Przykładowa

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.PIIDetectionSkill",
    "defaultLanguageCode": "en",
    "minimumPrecision": 0.5,
    "maskingMode": "replace",
    "maskingCharacter": "*",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "piiEntities"
      },
      {
        "name": "maskedText"
      }
    ]
  }
```
##    <a name="sample-input"></a>Przykładowe dane wejściowe

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Microsoft employee with ssn 859-98-0987 is using our awesome API's."
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "piiEntities":[ 
           { 
              "text":"859-98-0987",
              "type":"U.S. Social Security Number (SSN)",
              "subtype":"",
              "offset":28,
              "length":11,
              "score":0.65
           }
        ],
        "maskedText": "Microsoft employee with ssn *********** is using our awesome API's."
      }
    }
  ]
}
```

Należy zauważyć, że przesunięcia zwrócone dla jednostek w danych wyjściowych tej umiejętności są zwracane bezpośrednio z [interfejs API analizy tekstu](../cognitive-services/text-analytics/overview.md), co oznacza, że są one używane do indeksowania w oryginalnym ciągu, należy użyć klasy [StringInfo](/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) w programie .NET w celu wyodrębnienia poprawnej zawartości.  [Więcej szczegółów można znaleźć tutaj.](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="error-and-warning-cases"></a>Przypadki błędów i ostrzeżeń
Jeśli kod języka dla dokumentu nie jest obsługiwany, zwracane jest ostrzeżenie i nie są wyodrębniane żadne jednostki.
Jeśli tekst jest pusty, zostanie wygenerowane ostrzeżenie.
Jeśli tekst jest większy niż 50 000 znaków, przeanalizowane zostaną tylko pierwsze 50 000 znaki i zostanie wygenerowane ostrzeżenie.

Jeśli umiejętność zwróci ostrzeżenie, dane wyjściowe `maskedText` mogą być puste.  Oznacza to, że Jeśli spodziewasz się, że dane wyjściowe mają być dostępne do późniejszej umiejętności, nie będą działały zgodnie z oczekiwaniami. Należy pamiętać o tym podczas pisania definicji zestawu umiejętności.

## <a name="see-also"></a>Zobacz też

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)