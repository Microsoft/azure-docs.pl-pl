---
title: Wywoływanie interfejsu API analizy obrazów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wywołać interfejs API analizy obrazów i skonfigurować jego zachowanie.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 3f9a6afe3202df40e26332c3a8c91b8c3eca8a32
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012272"
---
# <a name="call-the-image-analysis-api"></a>Wywoływanie interfejsu API analizy obrazów

W tym artykule pokazano, jak wywołać interfejs API analizy obrazów, aby zwrócić informacje o funkcjach wizualnych obrazu.

W tym przewodniku przyjęto założenie, że <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" utworzono już zasób przetwarzanie obrazów "  target="_blank"> utwórz zasób przetwarzanie obrazów </a> i uzyskano klucz subskrypcji i adres URL punktu końcowego. Jeśli nie, Skorzystaj z [przewodnika Szybki Start](../quickstarts-sdk/image-analysis-client-library.md) , aby rozpocząć pracę.
  
## <a name="submit-data-to-the-service"></a>Przesyłanie danych do usługi

Przesyłasz obraz lokalny lub zdalny obraz do interfejsu API analizy. W przypadku lokalnego należy umieścić dane obrazu binarnego w treści żądania HTTP. W przypadku elementu zdalnego należy określić adres URL obrazu przez sformatowanie treści żądania podobnej do następującej: `{"url":"http://example.com/images/test.jpg"}` .

## <a name="determine-how-to-process-the-data"></a>Określanie sposobu przetwarzania danych

###  <a name="select-visual-features"></a>Wybieranie funkcji wizualnych

[Interfejs API analizy](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b) zapewnia dostęp do wszystkich funkcji analizy obrazów usługi. Należy określić, które funkcje mają być używane przez ustawienie parametrów zapytania adresu URL. Parametr może zawierać wiele wartości rozdzielonych przecinkami. Każda określona funkcja będzie wymagała dodatkowego czasu obliczeniowego, więc należy określić tylko to, czego potrzebujesz.

|Parametr adresu URL | Wartość | Opis|
|---|---|--|
|`visualFeatures`|`Adult` | wykrywa, czy obraz ma charakter pornograficznej (przedstawia nagość lub akt płci), czy też jest gorii (przedstawia skrajną przemoc lub krew). Wykryto również zawartość z sugestią seksualną (alias erotycznej Content).|
||`Brands` | wykrywa różne marki w obrazie, w tym przybliżoną lokalizację. Argument marek jest dostępny tylko w języku angielskim.|
||`Categories` | klasyfikuje zawartość obrazu zgodnie z taksonomią zdefiniowaną w dokumentacji. Jest to wartość domyślna `visualFeatures` .|
||`Color` | Określa kolor akcentu, kolor dominujący oraz to, czy obraz jest czarny&biały.|
||`Description` | opisuje zawartość obrazu z kompletnymi zdaniami w obsługiwanych językach.|
||`Faces` | wykrywa, czy twarze są obecne. Jeśli jest obecny, Generuj współrzędne, płeć i wiek.|
||`ImageType` | wykrywa, czy obraz jest obiektem clipart czy rysowaniem linii.|
||`Objects` | wykrywa różne obiekty w obrazie, w tym przybliżoną lokalizację. Argument obiektów jest dostępny tylko w języku angielskim.|
||`Tags` | tworzy tag obrazu ze szczegółową listą wyrazów związanych z zawartością obrazu.|
|`details`| `Celebrities` | Identyfikuje osobistości w przypadku wykrycia w obrazie.|
||`Landmarks` |określa punkty orientacyjne, jeśli zostały wykryte na obrazie.|

Wypełniony adres URL może wyglądać następująco:

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities`

### <a name="specify-languages"></a>Określ Języki

Możesz również określić język zwracanych danych. Następujący parametr zapytania URL określa język. Wartość domyślna to `en`.

|Parametr adresu URL | Wartość | Opis|
|---|---|--|
|`language`|`en` | Angielski|
||`es` | Hiszpański|
||`ja` | japoński|
||`pt` | Portugalski|
||`zh` | Chiński uproszczony|

Wypełniony adres URL może wyglądać następująco:

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities&language=en`

> [!NOTE]
> **Wywołania interfejsu API w zakresie**
>
> Niektóre funkcje analizy obrazu można wywołać bezpośrednio, a także za pomocą analizy wywołania interfejsu API. Na przykład można przeanalizować zakresem tylko tagów obrazu, wysyłając żądanie do `https://{endpoint}/vision/v3.2-preview.3/tag` . Zobacz [dokumentację referencyjną](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b) , aby poznać inne funkcje, które można wywoływać osobno.

## <a name="get-results-from-the-service"></a>Uzyskiwanie wyników z usługi

Usługa zwraca `200` odpowiedź HTTP, a treść zawiera zwrócone dane w postaci ciągu JSON. Poniżej przedstawiono przykład odpowiedzi JSON.

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

W poniższej tabeli znajdują się wyjaśnienia dotyczące pól w tym przykładzie:

Pole | Typ | Zawartość
------|------|------|
Tagi  | `object` | Obiekt najwyższego poziomu dla tablicy tagów.
tags[].Name | `string`    | Słowo kluczowe ze klasyfikatora tagów.
tags[].Score    | `number`    | Wynik pewności z zakresu od 0 do 1.
description (opis)     | `object`    | Obiekt najwyższego poziomu dla opisu obrazu.
description.tags[] |    `string`    | Lista tagów. W przypadku niewystarczającego zaufania do tworzenia podpisów Tagi mogą być jedynymi informacjami dostępnymi dla obiektu wywołującego.
description.captions[].text    | `string`    | Fraza opisująca obraz.
description.captions[].confidence    | `number`    | Wynik pewności dla frazy.

### <a name="error-codes"></a>Kody błędów

Zobacz następującą listę możliwych błędów i ich przyczyny:

* 400
    * InvalidImageUrl — adres URL obrazu jest nieprawidłowo sformatowany lub nie jest dostępny.
    * InvalidImageFormat — dane wejściowe nie są prawidłowym obrazem.
    * InvalidImageSize — obraz wejściowy jest zbyt duży.
    * NotSupportedVisualFeature — określony typ funkcji jest nieprawidłowy.
    * NotSupportedImage — nieobsługiwany obraz, np. pornografia podrzędna.
    * InvalidDetails — nieobsługiwana `detail` wartość parametru.
    * NotSupportedLanguage — żądana operacja nie jest obsługiwana w określonym języku.
    * BadArgument — dodatkowe szczegóły znajdują się w komunikacie o błędzie.
* 415 — błąd nieobsługiwanego typu nośnika. Typ zawartości nie ma dozwolonych typów:
    * Dla adresu URL obrazu: Content-Type powinna być Application/JSON
    * W przypadku danych obrazu binarnego: typ zawartości powinien mieć wartość application/octet-stream lub wieloczęściowy/formularz-Data
* 500
    * FailedToProcess
    * Limit czasu przetwarzania obrazu upłynął limit czasu.
    * InternalServerError

## <a name="next-steps"></a>Następne kroki

Aby wypróbować interfejs API REST, przejdź do [dokumentacji interfejsu API analizy obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b).
