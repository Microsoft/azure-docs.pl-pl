---
title: Umiejętność OCR
titleSuffix: Azure Cognitive Search
description: Wyodrębnij tekst z plików obrazów przy użyciu funkcji optycznego rozpoznawania znaków (OCR) w potoku wzbogacania na platformie Azure Wyszukiwanie poznawcze.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 8b6a7c3e05b26cbda80ebf1a3fc0d4fed8255e6b
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950809"
---
# <a name="ocr-cognitive-skill"></a>Umiejętność OCR

Umiejętność **optycznego rozpoznawania znaków (OCR)** rozpoznaje drukowany i odręczny tekst w plikach obrazu. Ta umiejętność używa modeli uczenia maszynowego zapewnianych przez [Przetwarzanie obrazów](../cognitive-services/computer-vision/overview.md) API [v 3.0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) w Cognitive Services. Umiejętność **OCR** jest mapowana na następujące funkcje:

+ W języku angielskim, hiszpańskim, niemieckim, francuskim, włoskim, portugalskim i holenderskim jest używany nowy interfejs API [odczytu](../cognitive-services/computer-vision/concept-recognizing-text.md#read-api) .
+ W przypadku wszystkich innych języków używany jest interfejs API ["OCR"](../cognitive-services/computer-vision/concept-recognizing-text.md#ocr-api) .

Umiejętność **OCR** wyodrębnia tekst z plików obrazów. Obsługiwane formaty plików to:

+ . JPEG
+ . PLIKI
+ . Format
+ . BMP
+ . GIF
+ . TIFF

> [!NOTE]
> Podczas rozszerzania zakresu przez zwiększenie częstotliwości przetwarzania, Dodawanie większej liczby dokumentów lub Dodawanie algorytmów AI, należy [dołączyć Cognitive Services rozliczanego zasobu](cognitive-search-attach-cognitive-services.md). Opłaty naliczane podczas wywoływania interfejsów API w Cognitive Services oraz do wyodrębniania obrazów w ramach etapu łamania dokumentu w usłudze Azure Wyszukiwanie poznawcze. Nie są naliczane opłaty za Wyodrębnianie tekstu z dokumentów.
>
> Do wykonania wbudowanych umiejętności są naliczane opłaty za istniejące [Cognitive Services cena płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/cognitive-services/)użyciem. Cennik wyodrębniania obrazów został opisany na [stronie cennika usługi Azure wyszukiwanie poznawcze](https://azure.microsoft.com/pricing/details/search/).


## <a name="skill-parameters"></a>Parametry umiejętności

W nazwach parametrów jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| `detectOrientation`   | Włącza Autowykrywanie orientacji obrazu. <br/> Prawidłowe wartości: PRAWDA/FAŁSZ.|
| `defaultLanguageCode` | <p>   Kod języka tekstu wejściowego. Obsługiwane języki: <br/> zh-Hans (ChineseSimplified) <br/> zh-Hant (ChineseTraditional) <br/>cs (czeski) <br/>da (duński) <br/>NL (holenderski) <br/>pl (angielski) <br/>Fi (fiński)  <br/>fr (francuski) <br/>  Niemcy (niemiecki) <br/>El (grecki) <br/> hu (węgierski) <br/> IT (włoski) <br/>  ja (japoński) <br/> ko (koreański) <br/> NB (norweski) <br/>   pl (Polski) <br/> pt (portugalski) <br/>  ru (rosyjski) <br/>  es (hiszpański) <br/>  OHR (szwedzki) <br/>  TR (turecki) <br/> AR (arabski) <br/> ro (rumuński) <br/> Wirtualizacja SR-Cyrl (SerbianCyrillic) <br/> Wirtualizacja sr-latn (SerbianLatin) <br/>  SK (słowacki) <br/>  UNK (nieznany) <br/><br/> Jeśli kod języka jest nieokreślony lub ma wartość null, język zostanie ustawiony na język angielski. Jeśli język jest jawnie ustawiony na wartość "UNK", język zostanie wykryty. </p> |
| `lineEnding` | Wartość do użycia między wykrytymi wierszami. Możliwe wartości: "Space", "CarriageReturn", "wysuwu".  Wartość domyślna to "Space". |

Wcześniej był już parametr o nazwie "textExtractionAlgorithm", który określa, czy umiejętność powinna wyodrębnić tekst "drukowany" lub "odpisanych".  Ten parametr jest przestarzały i nie jest już potrzebny, ponieważ najnowszy algorytm interfejsu API odczytu jest w stanie wyodrębnić oba typy tekstu jednocześnie.  Jeśli Twoja definicja umiejętności zawiera już ten parametr, nie musisz jej usuwać, ale nie będzie można jej używać, a oba typy tekstu zostaną wyodrębnione w przód, niezależnie od tego, co jest ustawione na.

## <a name="skill-inputs"></a>Dane wejściowe kwalifikacji

| Nazwa wejściowa      | Opis                                          |
|---------------|------------------------------------------------------|
| `image`         | Typ złożony. Obecnie działa tylko z polem "/Document/normalized_images" tworzonym przez indeksator usługi Azure Blob, gdy ```imageAction``` jest ustawiony na wartość inną niż ```none``` . Zobacz [przykład](#sample-output) , aby uzyskać więcej informacji.|


## <a name="skill-outputs"></a>Wyniki umiejętności
| Nazwa wyjściowa     | Opis                   |
|---------------|-------------------------------|
| `text`            | Czysty tekst wyodrębniony z obrazu.   |
| `layoutText`    | Typ złożony, który opisuje wyodrębniony tekst i lokalizację, w której został znaleziony tekst.|


## <a name="sample-definition"></a>Definicja Przykładowa

```json
{
  "skills": [
    {
      "description": "Extracts text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": null,
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text",
          "targetName": "myText"
        },
        {
          "name": "layoutText",
          "targetName": "myLayoutText"
        }
      ]
    }
  ]
}
```
<a name="sample-output"></a>

## <a name="sample-text-and-layouttext-output"></a>Przykładowy tekst i layoutText danych wyjściowych

```json
{
  "text": "Hello World. -John",
  "layoutText":
  {
    "language" : "en",
    "text" : "Hello World. -John",
    "lines" : [
      {
        "boundingBox":
        [ {"x":10, "y":10}, {"x":50, "y":10}, {"x":50, "y":30},{"x":10, "y":30}],
        "text":"Hello World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ],
    "words": [
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"Hello"
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ]
  }
}
```

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Przykład: scalanie tekstu wyodrębnionego z obrazów osadzonych przy użyciu zawartości dokumentu.

Typowym przypadkiem użycia scalania tekstu jest możliwość scalania tekstowej reprezentacji obrazów (tekst z umiejętności OCR lub podpisanie obrazu) do pola zawartość dokumentu.

Poniższy przykład zestawu umiejętności tworzy pole *merged_text* . To pole zawiera zawartość tekstową dokumentu oraz tekst OCRed z każdego obrazu osadzonego w tym dokumencie.

#### <a name="request-body-syntax"></a>Składnia treści żądania
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
W powyższym przykładzie zestawu umiejętności założono, że istnieje pole Normal-images. Aby wygenerować to pole, Ustaw konfigurację *imageAction* w definicji indeksatora na *generateNormalizedImages* , jak pokazano poniżej:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters": {
    "configuration": {
      "dataToExtract":"contentAndMetadata",
      "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Zobacz też
+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Umiejętność scalania](cognitive-search-skill-textmerger.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Tworzenie indeksatora (REST)](/rest/api/searchservice/create-indexer)