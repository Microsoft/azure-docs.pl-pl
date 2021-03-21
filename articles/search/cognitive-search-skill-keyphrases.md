---
title: wyodrębnianie kluczowych fraz umiejętności poznawcze
titleSuffix: Azure Cognitive Search
description: Oblicza tekst niestrukturalny, a dla każdego rekordu zwraca listę kluczowych fraz w potoku wzbogacenia AI na platformie Azure Wyszukiwanie poznawcze.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8aafb08ff0ccc9391071f796450e69f87de279ba
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547836"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>wyodrębnianie kluczowych fraz umiejętności poznawcze

Umiejętność **wyodrębnianie kluczowych fraz** oblicza niestrukturalny tekst, a dla każdego rekordu zwraca listę fraz kluczowych. Ta umiejętność używa modeli uczenia maszynowego zapewnianych przez [Analiza tekstu](../cognitive-services/text-analytics/overview.md) w Cognitive Services.

Ta funkcja jest przydatna, jeśli trzeba szybko identyfikować główne punkty rozmowy w rekordzie. Na przykład w przypadku tekstu wejściowego "żywność została deliciousa i wystąpiła wspaniałe zatrudnienie" usługa zwraca "żywność" i "wspaniałe personel".

> [!NOTE]
> Podczas rozszerzania zakresu przez zwiększenie częstotliwości przetwarzania, Dodawanie większej liczby dokumentów lub Dodawanie algorytmów AI, należy [dołączyć Cognitive Services rozliczanego zasobu](cognitive-search-attach-cognitive-services.md). Opłaty naliczane podczas wywoływania interfejsów API w Cognitive Services oraz do wyodrębniania obrazów w ramach etapu łamania dokumentu w usłudze Azure Wyszukiwanie poznawcze. Nie są naliczane opłaty za Wyodrębnianie tekstu z dokumentów.
>
> Do wykonania wbudowanych umiejętności są naliczane opłaty za istniejące [Cognitive Services cena płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/cognitive-services/)użyciem. Cennik wyodrębniania obrazów został opisany na [stronie cennika usługi Azure wyszukiwanie poznawcze](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft. umiejętności. Text. KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekordu powinien składać się z 50 000 znaków mierzonych przez [`String.Length`](/dotnet/api/system.string.length) . Jeśli konieczne jest rozbicie danych przed wysłaniem ich do wyodrębniania kluczowych fraz, rozważ użycie [umiejętności podziału tekstu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry umiejętności

W nazwach parametrów jest rozróżniana wielkość liter.

| Dane wejściowe | Opis |
|---------------------|-------------|
| `defaultLanguageCode` | Obowiązkowe Kod języka, który ma zostać zastosowany do dokumentów, które nie określają jawnie języka.  Jeśli kod języka domyślnego nie zostanie określony, jako domyślny kod języka zostanie użyty język angielski (EN). <br/> Zapoznaj się [z pełną listą obsługiwanych języków](../cognitive-services/text-analytics/language-support.md). |
| `maxKeyPhraseCount`   | Obowiązkowe Maksymalna liczba kluczowych fraz do wygenerowania. |
| `modelVersion`   | Obowiązkowe Wersja modelu do użycia podczas wywoływania usługi analiza tekstu. Domyślnie będzie to najnowsza dostępna wartość, gdy nie zostanie określony. Zalecamy, aby nie określać tej wartości, chyba że jest to absolutnie konieczne. Aby uzyskać więcej informacji, zobacz artykuł [przechowywanie wersji modeli w interfejs API analizy tekstu](../cognitive-services/text-analytics/concepts/model-versioning.md) . |

## <a name="skill-inputs"></a>Dane wejściowe kwalifikacji

| Dane wejściowe  | Opis |
|--------------------|-------------|
| `text` | Tekst do analizy.|
| `languageCode`    |  Ciąg wskazujący język rekordów. Jeśli ten parametr nie jest określony, kod języka domyślnego będzie używany do analizowania rekordów. <br/>Zapoznaj się [z pełną listą obsługiwanych języków](../cognitive-services/text-analytics/language-support.md)|

## <a name="skill-outputs"></a>Wyniki umiejętności

| Dane wyjściowe     | Opis |
|--------------------|-------------|
| `keyPhrases` | Lista kluczowych fraz wyodrębnionych z tekstu wejściowego. Kluczowe frazy są zwracane w kolejności ważności. |


##  <a name="sample-definition"></a>Definicja Przykładowa

Rozważmy rekord SQL zawierający następujące pola:

```json
{
    "content": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
    "language": "en"
}
```

Następnie definicja umiejętności może wyglądać następująco:

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      },
      {
        "name": "languageCode",
        "source": "/document/language" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
      }
    ]
  }
```

##  <a name="sample-output"></a>Przykładowe dane wyjściowe

W powyższym przykładzie dane wyjściowe Twojej umiejętności będą zapisywane w nowym węźle w wzbogaconym drzewie o nazwie "Document/myKeyPhrases", ponieważ jest to `targetName` wskazane. Jeśli nie określisz elementu `targetName` , będzie to "dokument/frazy kluczowe".

#### <a name="documentmykeyphrases"></a>dokument/myKeyPhrases 
```json
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
```

Możesz użyć "Document/myKeyPhrases" jako danych wejściowych w innych umiejętnościach lub jako źródło [mapowania pól wyjściowych](cognitive-search-output-field-mapping.md).

## <a name="warnings"></a>Ostrzeżenia
Jeśli podano nieobsługiwany kod języka, generowane jest ostrzeżenie, a frazy kluczy nie są wyodrębniane.
Jeśli tekst jest pusty, zostanie wygenerowane ostrzeżenie.
Jeśli tekst jest większy niż 50 000 znaków, przeanalizowane zostaną tylko pierwsze 50 000 znaki i zostanie wygenerowane ostrzeżenie.

## <a name="see-also"></a>Zobacz też

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Jak definiować mapowania pól wyjściowych](cognitive-search-output-field-mapping.md)