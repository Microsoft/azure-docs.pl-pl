---
title: Jak wywołać interfejs API odczytu
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wywołać interfejs API odczytu i skonfigurować jego zachowanie szczegółowo.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: pafarley
ms.openlocfilehash: c1f610a5fb3db41091ddc4d0d921bf1a0cd2cf34
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012232"
---
# <a name="call-the-read-api"></a>Wywoływanie interfejsu API odczytu

W tym przewodniku dowiesz się, jak wywołać interfejs API odczytu w celu wyodrębnienia tekstu z obrazów. Zapoznaj się z różnymi sposobami konfigurowania zachowania tego interfejsu API w celu spełnienia Twoich potrzeb.

W tym przewodniku przyjęto założenie, że <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" utworzono już zasób przetwarzanie obrazów "  target="_blank"> utwórz zasób przetwarzanie obrazów </a> i uzyskano klucz subskrypcji i adres URL punktu końcowego. Jeśli nie, Skorzystaj z [przewodnika Szybki Start](../quickstarts-sdk/client-library.md) , aby rozpocząć pracę.

## <a name="submit-data-to-the-service"></a>Przesyłanie danych do usługi

[Wywołanie odczytu](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) interfejsu API odczytu pobiera obraz lub dokument PDF jako dane wejściowe i asynchronicznie wyodrębnia tekst.

`https://{endpoint}/vision/v3.2-preview.3/read/analyze[?language][&pages][&readingOrder]`

Wywołanie zwraca z polem nagłówka odpowiedzi o nazwie `Operation-Location` . `Operation-Location`Wartość jest adresem URL, który zawiera identyfikator operacji, która ma zostać użyta w następnym kroku.

|Nagłówek odpowiedzi| Przykładowa wartość |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Rozliczenia** 
>
> Strona [cennika przetwarzanie obrazów](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) obejmuje warstwę cenową do odczytu. Każdy przeanalizowany obraz lub strona to jedna transakcja. Jeśli wywołasz operację z dokumentem PDF lub TIFF zawierającym 100 stron, operacja odczytu będzie liczyć ją z 100 transakcji. opłaty będą naliczane za 100 transakcji. Jeśli wykonano 50 wywołań operacji i każde wywołanie przesłało dokument z 100 strony, opłaty będą naliczane za 50 X 100 = 5000 transakcji.

## <a name="determine-how-to-process-the-data"></a>Określanie sposobu przetwarzania danych

### <a name="language-specification"></a>Specyfikacja języka

Wywołanie [odczytu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) ma opcjonalny parametr żądania dla języka. Odczyt obsługuje funkcję autoidentification języka i dokumenty wielojęzyczne, więc podaj tylko kod języka, jeśli chcesz wymusić przetwarzanie dokumentu jako tego konkretnego języka.

### <a name="natural-reading-order-output-latin-languages-only"></a>Naturalne odczytywanie danych wyjściowych z porządku (tylko języki łacińskie)
Za pomocą [interfejsu API odczytu 3,2 w wersji zapoznawczej](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)można określić kolejność, w której wiersze tekstu są wyprowadzane za pomocą `readingOrder` parametru zapytania. Użyj, `natural` Aby uzyskać bardziej przyjazny dla człowieka wynik z kolejnością odczytywania, jak pokazano w poniższym przykładzie. Ta funkcja jest obsługiwana tylko w przypadku języków łacińskich.

:::image border type="content" source="../Images/ocr-reading-order-example.png" alt-text="Przykład kolejności odczytywania OCR":::



### <a name="select-pages-or-page-ranges-for-text-extraction"></a>Wybieranie stron lub zakresów stron do wyodrębniania tekstu
Za pomocą [interfejsu API odczytu 3,2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)w przypadku dużych dokumentów wielostronicowych Użyj `pages` parametru zapytania, aby określić numery stron lub zakresy stron w celu wyodrębnienia tekstu z tylko tych stron. Poniższy przykład pokazuje dokument zawierający 10 stron z tekstem wyodrębnionym dla obu przypadków — wszystkie strony (1-10) i wybrane strony (3-6).

:::image border type="content" source="../Images/ocr-select-pages.png" alt-text="Wybrane strony wyjściowe":::

## <a name="get-results-from-the-service"></a>Uzyskiwanie wyników z usługi

Drugim krokiem jest wywołanie operacji [Get Results wyniki](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750) . Ta operacja przyjmuje jako dane wejściowe Identyfikator operacji, który został utworzony przez operację odczytu. 

`https://{endpoint}/vision/v3.2-preview.3/read/analyzeResults/{operationId}`

Zwraca odpowiedź JSON, która zawiera pole **stanu** z następującymi możliwymi wartościami. 

|Wartość | Znaczenie |
|:-----|:----|
| `notStarted`| Operacja nie została uruchomiona. |
| `running`| Trwa przetwarzanie operacji. |
| `failed`| Operacja nie powiodła się. |
| `succeeded`| Operacja zakończyła się pomyślnie. |

Tę operację można wywołać iteracyjnie, dopóki nie zwróci wartości z wartością **sukces** . Użyj interwału od 1 do 2 sekund, aby uniknąć przekroczenia liczby żądań na sekundę (RPS pliku).

> [!NOTE]
> Warstwa Bezpłatna ogranicza liczbę żądań do 20 wywołań na minutę. Warstwa płatna zezwala na 10 żądań na sekundę (RPS pliku), które można zwiększyć na żądanie. Użyj kanału pomocy technicznej systemu Azure lub zespołu kont, aby zażądać wyższego współczynnika żądań na sekundę (RPS pliku).

Gdy pole **stan** ma `succeeded` wartość, odpowiedź JSON zawiera wyodrębnioną zawartość tekstową z obrazu lub dokumentu. Odpowiedź JSON zachowuje pierwotną grupę wierszy rozpoznanych wyrazów. Zawiera wyodrębnione wiersze tekstu i ich współrzędne pola ograniczenia. Każdy wiersz tekstu zawiera wszystkie wyodrębnione wyrazy ze swoimi współrzędnymi i wynikami pewności.

> [!NOTE]
> Dane przesłane do `Read` operacji są tymczasowo szyfrowane i przechowywane w spoczynku przez krótki czas, a następnie usuwane. Dzięki temu aplikacje mogą pobierać wyodrębniony tekst w ramach odpowiedzi usługi.

### <a name="sample-json-output"></a>Przykładowe dane wyjściowe JSON

Zobacz następujący przykład pomyślnej odpowiedzi JSON:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              }
            ]
          }
        ]
      }
    ]
  }
}
```

### <a name="handwritten-classification-for-text-lines-latin-languages-only"></a>Klasyfikacja odręczna dla linii tekstowych (tylko języki łacińskie)
Odpowiedź w [interfejsie API Read 3,2 Preview](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) obejmuje klasyfikowanie niezależnie od tego, czy każdy wiersz tekstu ma styl pisma ręcznego, czy nie, oraz ocenę ufności. Ta funkcja jest obsługiwana tylko w przypadku języków łacińskich. Poniższy przykład pokazuje klasyfikację odręczne dla tekstu w obrazie.

:::image border type="content" source="../Images/ocr-handwriting-classification.png" alt-text="Przykład klasyfikacji pisma ręcznego OCR":::

## <a name="next-steps"></a>Następne kroki

Aby wypróbować interfejs API REST, przejdź do [odczytaj informacje o interfejsie API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005).