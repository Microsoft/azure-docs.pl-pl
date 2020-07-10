---
title: Optyczne rozpoznawanie znaków (OCR) — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia dotyczące optycznego rozpoznawania znaków (OCR) z obrazów i dokumentów z drukowanym i odręcznym tekstem przy użyciu interfejs API przetwarzania obrazów.
services: cognitive-services
author: msbbonsu
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: t-bebon
ms.custom: seodec18
ms.openlocfilehash: 6bc118145bec30085c2d9fbf726c40a20b312430
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207050"
---
# <a name="optical-character-recognition-ocr"></a>Optyczne rozpoznawanie znaków (OCR)

Firma Microsoft interfejs API przetwarzania obrazów obejmuje funkcje optycznego rozpoznawania znaków (OCR), które wyodrębniają drukowany lub odręczny tekst z obrazów i dokumentów PDF. Interfejsy API OCR wyodrębniają tekst z obu dokumentów analogowych (obrazów, zeskanowanych dokumentów) i dokumentów z cyframi. Można wyodrębnić tekst ze obrazków dzika, takich jak zdjęcia z płyt licencyjnych lub kontenerów z numerami seryjnymi, a także dokumenty — faktury, weksle, raporty finansowe, artykuły i inne. Nowy interfejs API rozpoznawania OCR jest dostępny jako część usługi zarządzanej w chmurze lub lokalnie (kontenery). Obsługuje ona także sieci wirtualne i prywatne punkty końcowe, aby zaspokoić wymagania dotyczące zgodności i prywatności klasy korporacyjnej.

## <a name="read-api"></a>Odczytaj interfejs API 

[Interfejs API odczytu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) przetwarzanie obrazów to najnowsza technologia OCR firmy Microsoft, która wyodrębnia drukowany tekst (siedem języków), tekst napisany ręcznie (tylko w języku angielskim), cyfry i symbole walut z obrazów i wielostronicowych dokumentów PDF. Jest zoptymalizowany pod kątem wyodrębniania tekstu z obrazów w formacie wieloznacznego i wielostronicowych dokumentów PDF z wielojęzycznymi językami. Obsługuje wykrywanie wydrukowanych i odpisanych tekstu (tylko w języku angielskim) w tym samym obrazie lub dokumencie. Zapoznaj się z pełną listą stron [obsługiwanych przez OCR w języku](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) .

### <a name="how-ocr-works"></a>Jak działa OCR

[Interfejs API odczytu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) obsługuje dokumenty z dużą ilością tekstu do 2000 stron i w związku z tym wykonuje asynchronicznie. Pierwszym krokiem jest wywołanie operacji odczytu. Operacja odczytu pobiera obraz lub dokument PDF jako dane wejściowe i zwraca identyfikator operacji. 

Drugim krokiem jest wywołanie operacji [Pobierz wyniki](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) . Ta operacja przyjmuje Identyfikator operacji, który został utworzony przez operację odczytu. Następnie zwraca wyodrębnioną zawartość tekstową z obrazu lub dokumentu w postaci JSON. Odpowiedź JSON zachowuje pierwotną grupę wierszy rozpoznanych wyrazów. Zawiera wyodrębnione wiersze tekstu i ich współrzędne pola ograniczenia. Każdy wiersz tekstu zawiera wszystkie wyodrębnione wyrazy ze swoimi współrzędnymi i wynikami pewności.

W razie potrzeby, odczyt koryguje obrót rozpoznanej strony, zwracając przesunięcie rotacyjne (w stopniach) o poziomą oś obrazu, jak pokazano na poniższej ilustracji.

![Jak OCR konwertuje obrazy i dokumenty na strukturalne dane wyjściowe z wyodrębnionym tekstem](./Images/how-ocr-works.svg)

### <a name="sample-ocr-output"></a>Przykładowe dane wyjściowe OCR

W formacie JSON zostanie zwrócona pomyślna odpowiedź, jak pokazano w następującym przykładzie:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
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

Postępuj zgodnie z przewodnikiem Szybki Start [wydrukowanym i odręcznym](./QuickStarts/CSharp-hand-text.md) , aby zaimplementować OCR przy użyciu języka C# i interfejsu API REST.

### <a name="input-requirements"></a>Wymagania wejściowe

Interfejs API odczytu pobiera następujące dane wejściowe:
* Obsługiwane formaty plików: JPEG, PNG, BMP, PDF i TIFF
* W przypadku plików PDF i TIFF przetwarzane są do 2000 stron. W przypadku subskrybentów warstwy Bezpłatna są przetwarzane tylko dwie pierwsze strony.
* Rozmiar pliku musi być mniejszy niż 50 MB i wymiary co najmniej 50 x 50 pikseli i maksymalnie 10000 x 10000 pikseli.
* Wymiary PDF muszą mieć co najwyżej 17 x 17 cali, odpowiadające rozmiarowi papieru legalnego lub A3 i mniejszym.

### <a name="text-from-images"></a>Tekst z obrazów

Następujące dane wyjściowe interfejsu API odczytu pokazują wyodrębnione wiersze tekstu i wyrazy z obrazu z tekstem z różnymi kątami, kolorami i czcionkami

![Obraz, który jest obracany, a jego tekst jest odczytywany i zakreślony](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>Tekst z dokumentów

Oprócz obrazów, interfejs API odczytu pobiera dokument PDF jako dane wejściowe.

![Obraz, który jest obracany, a jego tekst jest odczytywany i zakreślony](./Images/text-from-documents-example.png)


### <a name="handwritten-text-in-english"></a>Tekst napisany ręcznie w języku angielskim

Teraz operacja odczytu obsługuje Wyodrębnianie tekstu odręcznego wyłącznie w języku angielskim.

![Obraz, który jest obracany, a jego tekst jest odczytywany i zakreślony](./Images/handwritten-example.png)

### <a name="printed-text-in-supported-languages"></a>Tekst drukowany w obsługiwanych językach

Interfejs API odczytu obsługuje wyodrębnianie drukowanego tekstu w języku angielskim, hiszpańskim, niemieckim, francuskim, włoskim, portugalskim i holenderskim. Jeśli scenariusz wymaga obsługi większej liczby języków, zobacz Omówienie interfejsu API OCR w tym dokumencie. Zapoznaj się z listą wszystkich [języków obsługiwanych przez OCR](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr)

![Obraz, który jest obracany, a jego tekst jest odczytywany i zakreślony](./Images/supported-languages-example.png)

### <a name="mixed-languages-support"></a>Obsługa języków mieszanych

Interfejs API odczytu obsługuje obrazy i dokumenty z wieloma językami, często znane jako dokumenty w języku mieszanym. Robi to przez klasyfikowanie każdego wiersza tekstu w dokumencie do wykrytego języka przed wyodrębnieniem zawartości tekstowej.

![Obraz, który jest obracany, a jego tekst jest odczytywany i zakreślony](./Images/mixed-language-example.png)

### <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Podobnie jak w przypadku wszystkich usług poznawczych, deweloperzy korzystający z usługi odczytu powinni mieć świadomość zasad firmy Microsoft dotyczących danych klientów. Aby dowiedzieć się więcej, zobacz stronę Cognitive Services w [Centrum zaufania firmy Microsoft](https://www.microsoft.com/en-us/trust-center/product-overview) .

### <a name="deploy-on-premises"></a>Wdrażanie lokalnie

Funkcja Read jest również dostępna jako kontener platformy Docker (wersja zapoznawcza) w celu umożliwienia wdrożenia nowych funkcji OCR w Twoim środowisku. Kontenery doskonale nadaje się do określonych wymagań w zakresie zabezpieczeń i zarządzania danymi. Zobacz [temat Instalowanie i uruchamianie kontenerów odczytu.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)


## <a name="ocr-api"></a>INTERFEJS API OCR

[Interfejs API OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) używa starszego modelu rozpoznawania, obsługuje tylko obrazy i wykonuje synchronicznie, zwracając natychmiast z wykrytym tekstem. Zobacz [obsługiwane języki OCR](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) niż interfejs API odczytu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [interfejsie API REST do odczytu 3,0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005).
- Postępuj zgodnie z przewodnikiem Szybki Start [tekstu](./QuickStarts/CSharp-hand-text.md) , aby zaimplementować OCR przy użyciu języka C#, Java, JavaScript lub Python wraz z interfejsem API REST.
