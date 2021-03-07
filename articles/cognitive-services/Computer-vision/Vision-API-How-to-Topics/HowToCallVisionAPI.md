---
title: Wywoływanie interfejsu API przetwarzania obrazów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wywoływać interfejs API przetwarzania obrazów przy użyciu interfejsu API REST w usłudze Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 3a9ef3fb009cfb91b20ac7492be193286e2f0410
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102432540"
---
# <a name="call-the-computer-vision-api"></a>Wywoływanie interfejsu API przetwarzania obrazów

W tym artykule przedstawiono sposób wywoływania interfejs API przetwarzania obrazów przy użyciu interfejsu API REST. Przykłady są zapisywane zarówno w języku C#, przy użyciu biblioteki klienta interfejs API przetwarzania obrazów, jak i wywołania HTTP POST lub GET. Artykuł koncentruje się na:

- Pobieranie tagów, opisu i kategorii
- Pobieranie informacji specyficznych dla domeny lub "osobistości"

W przykładach w tym artykule przedstawiono następujące funkcje:

* Analizowanie obrazu w celu zwrócenia tablicy tagów i opisu
* Analizowanie obrazu z modelem specyficznym dla domeny (w konkretnym modelu "osobistości") w celu zwrócenia odpowiedniego wyniku w formacie JSON

Funkcje te oferują następujące opcje:

- **Opcja 1**: Analiza z zakresem — analizowanie tylko określonego modelu
- **Opcja 2**: rozszerzona analiza — analizowanie w celu zapewnienia dodatkowych informacji przy użyciu [taksonomii 86-kategorii](../Category-Taxonomy.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" Utwórz zasób przetwarzanie obrazów "  target="_blank"> utwórz zasób przetwarzanie obrazów </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który utworzysz, aby połączyć aplikację z usługą przetwarzanie obrazów. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.
* Adres URL obrazu lub ścieżka do przechowywanego lokalnie obrazu
* Obsługiwane metody wejściowe: plik binarny RAW obrazu w postaci aplikacji/strumienia oktetowego lub adresu URL obrazu
* Obsługiwane formaty plików obrazów: JPEG, PNG, GIF i BMP
* Rozmiar pliku obrazu: 4 MB lub mniej
* Wymiary obrazu: 50 &times; 50 pikseli lub więcej
  
## <a name="authorize-the-api-call"></a>Autoryzowanie wywołania interfejsu API

Każde wywołanie do interfejsu API przetwarzania obrazów wymaga klucza subskrypcji. Ten klucz musi być przesłany przez parametr ciągu zapytania lub określony w nagłówku żądania.

Klucz subskrypcji można przekazać, wykonując jedną z następujących czynności:

* Przekaż go za pomocą ciągu zapytania, jak w poniższym przykładzie interfejs API przetwarzania obrazów:

  ```
  https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
  ```

* Określ ją w nagłówku żądania HTTP:

  ```
  ocp-apim-subscription-key: <Your subscription key>
  ```

* W przypadku korzystania z biblioteki klienta należy przekazać klucz za pomocą konstruktora ComputerVisionClient i określić region we właściwości klienta:

    ```
    var visionClient = new ComputerVisionClient(new ApiKeyServiceClientCredentials("Your subscriptionKey"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com"
    }
    ```

## <a name="upload-an-image-to-the-computer-vision-api-service"></a>Przekazywanie obrazu do usługi interfejs API przetwarzania obrazów

Podstawowym sposobem wykonania wywołania interfejs API przetwarzania obrazów jest przekazanie obrazu bezpośrednio do znacznika powrotu, opisu i osobistości. W tym celu należy wysłać żądanie "POST" z obrazem binarnym w treści HTTP wraz z danymi odczytywanymi z obrazu. Metoda przekazywania jest taka sama dla wszystkich wywołań interfejs API przetwarzania obrazów. Jedyną różnicą są określone parametry zapytania. 

W przypadku określonego obrazu Pobierz Tagi i opis przy użyciu jednej z następujących opcji:

### <a name="option-1-get-a-list-of-tags-and-a-description"></a>Opcja 1: Pobieranie listy tagów i opis

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using System.IO;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

ImageAnalysis imageAnalysis;
var features = new VisualFeatureTypes[] { VisualFeatureTypes.Tags, VisualFeatureTypes.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageAnalysis = await visionClient.AnalyzeImageInStreamAsync(fs, features);
}
```

### <a name="option-2-get-a-list-of-tags-only-or-a-description-only"></a>Opcja 2: Pobieranie listy tylko tagów lub tylko opis

W przypadku tylko tagów Uruchom polecenie:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/tag?subscription-key=<Your subscription key>
var tagResults = await visionClient.TagImageAsync("http://contoso.com/example.jpg");
```

Aby uzyskać tylko opis, uruchom polecenie:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/describe?subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageDescription = await visionClient.DescribeImageInStreamAsync(fs);
}
```

## <a name="get-domain-specific-analysis-celebrities"></a>Pobierz analizę specyficzną dla domeny (osobistości)

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Opcja 1: Analiza z zakresem — analizowanie tylko określonego modelu
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

Dla tej opcji wszystkie pozostałe parametry zapytania {visualFeatures, details} są nieprawidłowe. Jeśli chcesz wyświetlić wszystkie obsługiwane modele, użyj kodu:

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.1/models 
var models = await visionClient.ListModelsAsync();
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-86-categories-taxonomy"></a>Opcja 2: rozszerzona analiza — analizowanie w celu zapewnienia dodatkowych informacji przy użyciu taksonomii 86-kategorii

W przypadku aplikacji, w których chcesz uzyskać ogólną analizę obrazu oprócz szczegółów z jednego lub kilku modeli specyficznych dla domeny, należy zwiększyć interfejs API w wersji 1 za pomocą parametru zapytania models.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?details=celebrities
```

Po wywołaniu tej metody należy najpierw wywołać klasyfikatora [kategorii 86](../Category-Taxonomy.md) . Jeśli którakolwiek z kategorii jest zgodna ze znanym lub zgodnym modelem, wystąpi drugie przejście do klasyfikatora. Na przykład jeśli "Szczegóły = wszystkie" lub "Szczegóły" zawierają "osobistości", wywoływany jest model osobistości po wywołaniu klasyfikatora kategorii 86. Wynik zawiera kategorię Category (kategoria). W przeciwieństwie do opcji 1, ta metoda zwiększa opóźnienie dla użytkowników, którzy interesują osobistości.

W takim przypadku wszystkie parametry zapytania V1 działają w ten sam sposób. Jeśli nie określisz kategorii visualFeatures =, jest on niejawnie włączony.

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>Pobierz i poznanie danych wyjściowych JSON na potrzeby analizy

Oto przykład:

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

Pole | Typ | Zawartość
------|------|------|
Tagi  | `object` | Obiekt najwyższego poziomu dla tablicy tagów.
tags[].Name | `string`    | Słowo kluczowe ze klasyfikatora tagów.
tags[].Score    | `number`    | Wynik pewności z zakresu od 0 do 1.
description (opis)     | `object`    | Obiekt najwyższego poziomu opisu.
description.tags[] |    `string`    | Lista tagów.  W przypadku niewystarczającego zaufania do tworzenia podpisów Tagi mogą być jedynymi informacjami dostępnymi dla obiektu wywołującego.
description.captions[].text    | `string`    | Fraza opisująca obraz.
description.captions[].confidence    | `number`    | Wynik pewności dla frazy.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>Pobieranie i poznawanie danych wyjściowych w formacie JSON dla modeli specyficznych dla domeny

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Opcja 1: Analiza z zakresem — analizowanie tylko określonego modelu

Wyjście jest tablicą tagów, jak pokazano w następującym przykładzie:

```json
{  
  "result":[  
    {  
      "name":"golden retriever",
      "score":0.98
    },
    {  
      "name":"Labrador retriever",
      "score":0.78
    }
  ]
}
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-the-86-categories-taxonomy"></a>Opcja 2: rozszerzona analiza — analizowanie w celu zapewnienia dodatkowych informacji przy użyciu taksonomii "86-Categories"

W przypadku modeli specyficznych dla domeny przy użyciu opcji 2 (rozszerzona analiza) typ zwracany kategorii jest rozszerzony, jak pokazano w następującym przykładzie:

```json
{  
  "requestId":"87e44580-925a-49c8-b661-d1c54d1b83b5",
  "metadata":{  
    "width":640,
    "height":430,
    "format":"Jpeg"
  },
  "result":{  
    "celebrities":[  
      {  
        "name":"Richard Nixon",
        "faceRectangle":{  
          "left":107,
          "top":98,
          "width":165,
          "height":165
        },
        "confidence":0.9999827
      }
    ]
  }
}
```

Pole kategorie jest listą co najmniej jednej [kategorii 86](../Category-Taxonomy.md) w oryginalnej taksonomii. Kategorie kończące się znakiem podkreślenia są zgodne z tą kategorią i jej elementami podrzędnymi (na przykład "people_" lub "people_group" dla modelu osobistości).

Pole    | Typ    | Zawartość
------|------|------|
categories | `object`    | Obiekt najwyższego poziomu.
categories[].name     | `string`    | Nazwa z listy Taksonomia kategorii 86.
categories[].score    | `number`    | Wynik pewności z zakresu od 0 do 1.
categories[].detail     | `object?`      | Obowiązkowe Obiekt szczegółowy.

Jeśli wiele kategorii jest zgodnych (na przykład klasyfikator kategorii 86 — zwraca ocenę dla obu "people_" i "people_young," gdy model = osobistości), szczegóły są dołączane do najbardziej ogólnego dopasowania poziomu ("people_" w tym przykładzie).

## <a name="error-responses"></a>Odpowiedzi na błędy

Te błędy są takie same jak w przypadku programu Vision. Analizuj, przy użyciu dodatkowego błędu NotSupportedModel (HTTP 400), który może być zwracany zarówno w scenariuszach opcji 1, jak i 2. W przypadku opcji 2 (Ulepszona analiza), jeśli którykolwiek z modeli określonych w szczegółach nie zostanie rozpoznany, interfejs API zwraca NotSupportedModel, nawet jeśli co najmniej jeden z nich jest prawidłowy. Aby dowiedzieć się, jakie modele są obsługiwane, możesz wywołać listModels.

## <a name="next-steps"></a>Następne kroki

Aby korzystać z interfejsu API REST, przejdź do [dokumentacji dotyczącej interfejsu API przetwarzania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b).
