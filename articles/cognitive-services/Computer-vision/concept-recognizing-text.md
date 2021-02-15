---
title: Optyczne rozpoznawanie znaków (OCR) — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia dotyczące optycznego rozpoznawania znaków (OCR) obrazów i dokumentów z drukowanym i odręcznym tekstem przy użyciu interfejs API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 2833fd44b75f4bebf41b5100eb2350ca69436520
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362798"
---
# <a name="optical-character-recognition-ocr"></a>Optyczne rozpoznawanie znaków (OCR)

Interfejs API przetwarzania obrazów platformy Azure obejmuje funkcje optycznego rozpoznawania znaków (OCR), które wyodrębniają tekst drukowany lub odręczny z obrazów. Można wyodrębnić tekst z obrazów, takich jak zdjęcia z płyt licencyjnych lub kontenerów z numerami seryjnymi, a także dokumenty — faktury, weksle, raporty finansowe, artykuły i inne.

## <a name="read-api"></a>Odczytaj interfejs API 

[Interfejs API odczytu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) przetwarzanie obrazów to najnowsza technologia OCR platformy Azure ([Dowiedz się, co nowego](./whats-new.md)), która wyodrębnia drukowany tekst (w kilku językach), tekst odręczny (tylko w języku angielskim), cyfry i symbole walutowe z obrazów i wielostronicowych dokumentów PDF. Jest zoptymalizowany pod kątem wyodrębniania tekstu z obrazów z obrazami i wielostronicowych dokumentów PDF z wielojęzycznymi językami. Obsługuje ona wykrywanie wydrukowanych i odręcznych tekstu w tym samym obrazie lub dokumencie.

![Jak OCR konwertuje obrazy i dokumenty na strukturalne dane wyjściowe z wyodrębnionym tekstem](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>Wymagania wejściowe
Wywołanie **odczytu** pobiera obrazy i dokumenty jako dane wejściowe. Mają one następujące wymagania:

* Obsługiwane formaty plików: JPEG, PNG, BMP, PDF i TIFF
* W przypadku plików PDF i TIFF, do 2000 stron (tylko pierwsze dwie strony dla warstwy Bezpłatna) są przetwarzane.
* Rozmiar pliku musi być mniejszy niż 50 MB (4 MB dla warstwy Bezpłatna) i wymiary co najmniej 50 x 50 pikseli i maksymalnie 10000 x 10000 pikseli. 
* Wymiary PDF muszą mieć co najwyżej 17 x 17 cali, odpowiadające rozmiarowi papieru legalnego lub A3 i mniejszym.

> [!NOTE]
> **Dane wejściowe języka** 
>
> [Wywołanie odczytu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) ma opcjonalny parametr żądania dla języka. Odczyt obsługuje funkcję autoidentification języka i dokumenty wielojęzyczne, więc podaj tylko kod języka, jeśli chcesz wymusić przetwarzanie dokumentu jako tego konkretnego języka.

## <a name="ocr-demo-examples"></a>Demonstracja OCR (przykłady)

![Demonstracje OCR](./Images/ocr-demo.gif)

## <a name="step-1-the-read-operation"></a>Krok 1. operacja odczytu

[Wywołanie odczytu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) interfejsu API odczytu pobiera obraz lub dokument PDF jako dane wejściowe i asynchronicznie wyodrębnia tekst. Wywołanie zwraca z polem nagłówka odpowiedzi o nazwie `Operation-Location` . `Operation-Location`Wartość jest adresem URL, który zawiera identyfikator operacji, która ma zostać użyta w następnym kroku.

|Nagłówek odpowiedzi| Adres URL wyniku |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Rozliczenia** 
>
> Strona [cennika przetwarzanie obrazów](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) obejmuje warstwę cenową do odczytu. Każdy przeanalizowany obraz lub strona to jedna transakcja. Jeśli wywołasz operację z dokumentem PDF lub TIFF zawierającym 100 stron, operacja odczytu będzie liczyć ją z 100 transakcji. opłaty będą naliczane za 100 transakcji. Jeśli wykonano 50 wywołań operacji i każde wywołanie przesłało dokument z 100 strony, opłaty będą naliczane za 50 X 100 = 5000 transakcji.

## <a name="step-2-the-get-read-results-operation"></a>Krok 2. operacja pobrania wyników operacji odczytu

Drugim krokiem jest wywołanie operacji [Get Results wyniki](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750) . Ta operacja przyjmuje jako dane wejściowe Identyfikator operacji, który został utworzony przez operację odczytu. Zwraca odpowiedź JSON, która zawiera pole **stanu** z następującymi możliwymi wartościami. Tę operację można wywołać iteracyjnie, dopóki nie zwróci wartości z wartością **sukces** . Użyj interwału od 1 do 2 sekund, aby uniknąć przekroczenia liczby żądań na sekundę (RPS pliku).

|Pole| Typ | Możliwe wartości |
|:-----|:----:|:----|
|status | ciąg | notStarted: operacja nie została uruchomiona. |
| |  | uruchomiono: Trwa przetwarzanie operacji. |
| |  | Niepowodzenie: operacja nie powiodła się. |
| |  | sukces: operacja zakończyła się pomyślnie. |

> [!NOTE]
> Warstwa Bezpłatna ogranicza liczbę żądań do 20 wywołań na minutę. Warstwa płatna zezwala na 10 żądań na sekundę (RPS pliku), które można zwiększyć na żądanie. Użyj kanału pomocy technicznej systemu Azure lub zespołu kont, aby zażądać wyższego współczynnika żądań na sekundę (RPS pliku).

Gdy wartość w polu **stan** zostanie **zakończona pomyślnie** , odpowiedź JSON zawiera wyodrębnioną zawartość tekstową z obrazu lub dokumentu. Odpowiedź JSON zachowuje pierwotną grupę wierszy rozpoznanych wyrazów. Zawiera wyodrębnione wiersze tekstu i ich współrzędne pola ograniczenia. Każdy wiersz tekstu zawiera wszystkie wyodrębnione wyrazy ze swoimi współrzędnymi i wynikami pewności.

> [!NOTE]
> Dane przesłane do `Read` operacji są tymczasowo szyfrowane i przechowywane w spoczynku przez krótki czas, a następnie usuwane. Dzięki temu aplikacje mogą pobierać wyodrębniony tekst w ramach odpowiedzi usługi.

## <a name="sample-json-output"></a>Przykładowe dane wyjściowe JSON

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

## <a name="natural-reading-order-output-latin-only"></a>Dane wyjściowe z porządkiem naturalnym odczytywania (tylko łacińskie)
Za pomocą [interfejsu API odczytu 3,2 w wersji zapoznawczej](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005)Określ kolejność, w której wiersze tekstowe są wyprowadzane za pomocą `readingOrder` parametru zapytania. Użyj, `natural` Aby uzyskać bardziej przyjazny dla człowieka wynik z kolejnością odczytywania, jak pokazano w poniższym przykładzie. Ta funkcja jest obsługiwana tylko w przypadku języków łacińskich.

:::image border type="content" source="./Images/ocr-reading-order-example.png" alt-text="Przykład kolejności odczytywania OCR":::

## <a name="handwritten-classification-for-text-lines-latin-only"></a>Klasyfikacja odręczna dla linii tekstowych (tylko łaciński)
Odpowiedź w [interfejsie API Read 3,2 Preview](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005) obejmuje klasyfikowanie niezależnie od tego, czy każdy wiersz tekstu ma styl pisma ręcznego, czy nie, oraz ocenę ufności. Ta funkcja jest obsługiwana tylko w przypadku języków łacińskich. Poniższy przykład pokazuje klasyfikację odręczne dla tekstu w obrazie.

:::image border type="content" source="./Images/ocr-handwriting-classification.png" alt-text="Przykład klasyfikacji pisma ręcznego OCR":::

## <a name="select-pages-or-page-ranges-for-text-extraction"></a>Wybieranie stron lub zakresów stron do wyodrębniania tekstu
Za pomocą [interfejsu API odczytu 3,2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005)w przypadku dużych dokumentów wielostronicowych Użyj `pages` parametru zapytania, aby określić numery stron lub zakresy stron w celu wyodrębnienia tekstu z tylko tych stron. Poniższy przykład pokazuje dokument zawierający 10 stron z tekstem wyodrębnionym dla obu przypadków — wszystkie strony (1-10) i wybrane strony (3-6).

:::image border type="content" source="./Images/ocr-select-pages.png" alt-text="Wybrane strony wyjściowe":::

## <a name="supported-languages"></a>Obsługiwane języki
Interfejsy API odczytu obsługują łącznie 73 języków na potrzeby tekstu w stylu drukowania. Zapoznaj się z pełną listą [języków obsługiwanych przez aparat OCR](./language-support.md#optical-character-recognition-ocr). OCR w stylu odręcznym jest obsługiwany wyłącznie w języku angielskim.

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>Korzystanie z interfejsu API chmury lub Wdrażanie lokalne
Interfejs API chmury odczytywania 3. x jest preferowaną opcją dla większości klientów ze względu na łatwość integracji i szybką produktywność z usługi Box. Platforma Azure i usługa przetwarzanie obrazów obsługują skalowanie, wydajność, bezpieczeństwo danych i wymagania dotyczące zgodności podczas skoncentrowania się na potrzebach klientów.

W przypadku wdrożenia lokalnego [odczytywanie kontenera Docker (wersja zapoznawcza)](./computer-vision-how-to-install-containers.md) umożliwia wdrażanie nowych funkcji rozpoznawania w środowisku lokalnym. Kontenery doskonale nadają się do określonych wymagań w zakresie zabezpieczeń i zarządzania danymi.

## <a name="ocr-api"></a>INTERFEJS API OCR

[Interfejs API OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d) używa starszego modelu rozpoznawania, obsługuje tylko obrazy i wykonuje synchronicznie, zwracając natychmiast z wykrytym tekstem. Zobacz [obsługiwane języki OCR](./language-support.md#optical-character-recognition-ocr) , a następnie przeczytaj interfejs API.

> [!NOTE]
> 2,0 komputer, na którym są RecognizeText operacje, są w trakcie wycofywania na korzyść nowego interfejsu API odczytu omówionego w tym artykule. Istniejący klienci powinni [przejść do korzystania z operacji odczytu](upgrade-api-versions.md).

## <a name="next-steps"></a>Następne kroki

- Rozpocznij pracę z [interfejsem API REST przetwarzanie obrazów lub z przewodnikiem Szybki Start dla biblioteki klienta](./quickstarts-sdk/client-library.md).
- Dowiedz się więcej o [interfejsie API REST do odczytu 3,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005).
- Dowiedz się więcej o [interfejsie API REST usługi Read 3,2 Public Preview](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005) z obsługą łącznej liczby języków 73.
