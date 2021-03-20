---
title: Umiejętność scalania tekstu
titleSuffix: Azure Cognitive Search
description: Scalanie tekstu z kolekcji pól w jedno skonsolidowane pole. Korzystaj z tej umiejętności poznawczej w potoku wzbogacenia AI na platformie Azure Wyszukiwanie poznawcze.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: b364655f26c6ac29c14d387d69d7b4277d6aeb86
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88924643"
---
#   <a name="text-merge-cognitive-skill"></a>Umiejętność scalania tekstu

Umiejętność **scalania tekstu** konsoliduje tekst z kolekcji pól w jedno pole. 

> [!NOTE]
> Ta umiejętność nie jest powiązana z interfejsem API Cognitive Services i nie jest naliczana opłata za korzystanie z niego. Mimo to należy [dołączyć zasób Cognitive Services](cognitive-search-attach-cognitive-services.md), aby zastąpić opcję **bezpłatnych** zasobów, która ogranicza liczbę codziennych wzbogacań dziennie.

## <a name="odatatype"></a>@odata.type  
Microsoft. umiejętności. Text. MergeSkill

## <a name="skill-parameters"></a>Parametry umiejętności

W nazwach parametrów jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| `insertPreTag`    | Ciąg do uwzględnienia przed każdym wstawieniem. Wartość domyślna to `" "`. Aby pominąć miejsce, ustaw wartość na `""` .  |
| `insertPostTag`   | Ciąg do uwzględnienia po każdym wstawieniu. Wartość domyślna to `" "`. Aby pominąć miejsce, ustaw wartość na `""` .  |


##  <a name="sample-input"></a>Przykładowe dane wejściowe
Dokument JSON, który zapewnia użyteczne dane wejściowe dla tego umiejętności, może być następujący:

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "text": "The brown fox jumps over the dog",
        "itemsToInsert": ["quick", "lazy"],
        "offsets": [3, 28]
      }
    }
  ]
}
```

##  <a name="sample-output"></a>Przykładowe dane wyjściowe
Ten przykład pokazuje dane wyjściowe poprzedniej wartości wejściowej, przy założeniu, że *insertPreTag* jest ustawiona na `" "` , a *insertPostTag* jest ustawiona na `""` . 

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "mergedText": "The quick brown fox jumps over the lazy dog"
      }
    }
  ]
}
```

## <a name="extended-sample-skillset-definition"></a>Rozszerzona definicja zestawu umiejętności

Typowym scenariuszem korzystania z scalania tekstu jest scalanie tekstowej reprezentacji obrazów (tekst z umiejętności OCR lub podpisanie obrazu) do pola zawartość dokumentu. 

Poniższy przykład zestawu umiejętności używa umiejętności OCR do wyodrębniania tekstu z obrazów osadzonych w dokumencie. Następnie tworzy pole *merged_text* , aby zawierało oryginalny i OCRed tekst z każdego obrazu. Więcej informacji o umiejętnościach OCR można znaleźć [tutaj](./cognitive-search-skill-ocr.md).

```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
      "description": "Extract text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": "en",
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", 
          "source": "/document/content"
        },
        {
          "name": "itemsToInsert", 
          "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", 
          "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", 
          "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
W powyższym przykładzie przyjęto założenie, że istnieje znormalizowane pole obrazu. Aby uzyskać standardowe pole obrazu, Ustaw konfigurację *imageAction* w definicji indeksatora na *generateNormalizedImages* , jak pokazano poniżej:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":{
    "configuration":{
        "dataToExtract":"contentAndMetadata",
        "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Zobacz też

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Tworzenie indeksatora (REST)](/rest/api/searchservice/create-indexer)