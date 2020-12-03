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
ms.openlocfilehash: 50cce15d14561bb1c8ebfdbbeeedd99dc8efcda1
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532996"
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

### <a name="read-32-preview-allows-selecting-pages"></a>Wersja zapoznawcza 3,2 umożliwia wybieranie stron
Za pomocą [interfejsu API programu Read 3,2 Preview](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-1/operations/5d986960601faab4bf452005)dla dużych dokumentów wielostronicowych można podać określone numery stron lub zakresy stron jako parametr wejściowy, aby wyodrębnić tekst z tylko tych stron. Jest to nowy parametr wejściowy oprócz opcjonalnego parametru języka.

> [!NOTE]
> **Dane wejściowe języka** 
>
> [Wywołanie odczytu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) ma opcjonalny parametr żądania dla języka. Jest to kod języka BCP-47 tekstu w dokumencie. Odczyt obsługuje funkcję autoidentification języka i dokumenty wielojęzyczne, więc podaj tylko kod języka, jeśli chcesz wymusić przetwarzanie dokumentu jako tego konkretnego języka.

## <a name="the-read-call"></a>Wywołanie odczytu

[Wywołanie odczytu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) interfejsu API odczytu pobiera obraz lub dokument PDF jako dane wejściowe i asynchronicznie wyodrębnia tekst. Wywołanie zwraca z polem nagłówka odpowiedzi o nazwie `Operation-Location` . `Operation-Location`Wartość jest adresem URL, który zawiera identyfikator operacji, która ma zostać użyta w następnym kroku.

|Nagłówek odpowiedzi| Adres URL wyniku |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Rozliczenia** 
>
> Strona [cennika przetwarzanie obrazów](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) obejmuje warstwę cenową do odczytu. Każdy przeanalizowany obraz lub strona to jedna transakcja. Jeśli wywołasz operację z dokumentem PDF lub TIFF zawierającym 100 stron, operacja odczytu będzie liczyć ją z 100 transakcji. opłaty będą naliczane za 100 transakcji. Jeśli wykonano 50 wywołań operacji i każde wywołanie przesłało dokument z 100 strony, opłaty będą naliczane za 50 X 100 = 5000 transakcji.

## <a name="the-get-read-results-call"></a>Wywołanie metody get Read Results

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
> Dane przesłane do `Read` operacji są tymczasowo szyfrowane i przechowywane w spoczynku i usuwane w ciągu 48 godzin. Dzięki temu aplikacje mogą pobierać wyodrębniony tekst w ramach odpowiedzi usługi.

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
### <a name="read-32-preview-adds-text-line-style-latin-languages-only"></a>Odczyt 3,2 w wersji zapoznawczej dodaje styl linii tekstu (tylko języki łacińskie)
[Interfejs API odczytu 3,2 w wersji zapoznawczej](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-1/operations/5d986960601faab4bf452005) wyświetla obiekt **wyglądu** klasyfikowanie niezależnie od tego, czy każdy wiersz tekstu jest stylem drukowania, czy też z wynikiem ufności. Ta funkcja jest obsługiwana tylko dla języków łacińskich.

Rozpocznij pracę z [interfejsem API REST przetwarzanie obrazów lub z biblioteką klienta — szybki](./quickstarts-sdk/client-library.md) Start, aby rozpocząć integrację funkcji OCR z aplikacjami.

## <a name="supported-languages-for-print-text"></a>Obsługiwane języki na potrzeby drukowania tekstu
[Interfejs API odczytu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) obsługuje wyodrębnianie drukowanego tekstu w języku angielskim, hiszpańskim, niemieckim, francuskim, włoskim, portugalskim i holenderskim.

Pełną listę języków obsługiwanych przez OCR można znaleźć w [obsługiwanych językach](./language-support.md#optical-character-recognition-ocr) .

### <a name="read-32-preview-adds-simplified-chinese-and-japanese"></a>Read 3,2 Preview dodaje uproszczony chiński i japoński
W [publicznej wersji zapoznawczej interfejsu API Read 3,2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-1/operations/5d986960601faab4bf452005) dodano obsługę języka chińskiego uproszczonego i japońskiego. Jeśli scenariusz wymaga obsługi większej liczby języków, zobacz sekcję dotyczącą [interfejsu API OCR](#ocr-api) . 

## <a name="supported-languages-for-handwritten-text"></a>Obsługiwane języki dla tekstu odręcznego
Operacja odczytu obsługuje obecnie Wyodrębnianie tekstu odręcznego wyłącznie w języku angielskim.

## <a name="use-the-rest-api-and-sdk"></a>Korzystanie z interfejsu API REST i zestawu SDK
[Interfejs API REST do odczytu 3. x](./QuickStarts/CSharp-hand-text.md) jest preferowaną opcją dla większości klientów ze względu na łatwość integracji i szybką produktywność z usługi Box. Platforma Azure i usługa przetwarzanie obrazów obsługują skalowanie, wydajność, bezpieczeństwo danych i wymagania dotyczące zgodności podczas skoncentrowania się na potrzebach klientów.

## <a name="deploy-on-premise-with-docker-containers"></a>Wdrażanie lokalnie przy użyciu kontenerów platformy Docker
[Odczytaj kontener platformy Docker (wersja zapoznawcza)](./computer-vision-how-to-install-containers.md) umożliwia wdrożenie nowych funkcji OCR w środowisku lokalnym. Kontenery doskonale nadają się do określonych wymagań w zakresie zabezpieczeń i zarządzania danymi.

## <a name="example-outputs"></a>Przykładowe dane wyjściowe

### <a name="text-from-images"></a>Tekst z obrazów

Następujące dane wyjściowe interfejsu API odczytu pokazują wyodrębniony tekst z obrazu z różnymi kątami, kolorami i czcionkami tekstu.

![Obraz kilku wyrazów o różnych kolorach i kątami z wyodrębnionym tekstem wymienionym na liście](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>Tekst z dokumentów

Interfejs API odczytu może również przyjmować dokumenty PDF jako dane wejściowe.

![Dokument faktury, z wyodrębnionym tekstem wymienionym na liście](./Images/text-from-pdf-example.png)

### <a name="handwritten-text"></a>Tekst odręczny

Operacja odczytu wyodrębnia Tekst odręczny z obrazów (obecnie tylko w języku angielskim).

![Obraz notatki napisanej ręcznie z wyodrębnionym tekstem wymienionym na liście](./Images/handwritten-example.png)

### <a name="printed-text"></a>Tekst drukowany

Operacja odczytu może wyodrębnić drukowany tekst w kilku różnych językach.

![Obraz hiszpańskiej Textbook z wyodrębnionym tekstem wymienionym na liście](./Images/supported-languages-example.png)

### <a name="mixed-language-documents"></a>Dokumenty języka mieszanego

Interfejs API odczytu obsługuje obrazy i dokumenty zawierające wiele różnych języków, często znane jako dokumenty w języku mieszanym. Działa przez klasyfikowanie każdego wiersza tekstu w dokumencie do wykrytego języka przed wyodrębnieniem jego zawartości tekstowej.

![Obraz wyrażeń w kilku językach z wyodrębnionym tekstem wymienionym na liście](./Images/mixed-language-example.png)

## <a name="ocr-api"></a>INTERFEJS API OCR

[Interfejs API OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d) używa starszego modelu rozpoznawania, obsługuje tylko obrazy i wykonuje synchronicznie, zwracając natychmiast z wykrytym tekstem. Zobacz [obsługiwane języki OCR](./language-support.md#optical-character-recognition-ocr) , a następnie przeczytaj interfejs API.

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Podobnie jak w przypadku wszystkich usług poznawczych, deweloperzy korzystający z usług odczytu/OCR powinni mieć świadomość zasad firmy Microsoft dotyczących danych klientów. Aby dowiedzieć się więcej, zobacz stronę Cognitive Services w [Centrum zaufania firmy Microsoft](https://www.microsoft.com/trust-center/product-overview) .

> [!NOTE]
> 2,0 komputer, na którym są RecognizeText operacje, są w trakcie wycofywania na korzyść nowego interfejsu API odczytu omówionego w tym artykule. Istniejący klienci powinni [przejść do korzystania z operacji odczytu](upgrade-api-versions.md).

## <a name="next-steps"></a>Następne kroki

- Rozpocznij pracę z [interfejsem API REST przetwarzanie obrazów lub z przewodnikiem Szybki Start dla biblioteki klienta](./quickstarts-sdk/client-library.md).
- Dowiedz się więcej na temat [interfejsu API REST odczytu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005).
- Dowiedz się więcej o [interfejsie API REST usługi Read 3,2 Public Preview](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-1/operations/5d986960601faab4bf452005) z dodaną obsługą języka chińskiego uproszczonego i japońskiego.