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
ms.openlocfilehash: d1c642a660b24cfc54c9c4308b8956582e13d50a
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85954744"
---
# <a name="optical-character-recognition-ocr"></a>Optyczne rozpoznawanie znaków (OCR)

Przetwarzanie obrazów obejmuje nowe funkcje optycznego rozpoznawania znaków (OCR) oparte na głębokiej uczeniu, które wyodrębniają tekst drukowany lub odręczny z obrazów i dokumentów PDF. Przetwarzanie obrazów wyodrębnia tekst z dokumentów analogowych (obrazów, zeskanowanych dokumentów) i dokumentów z cyframi. Można wyodrębnić tekst ze obrazków dzika, takich jak zdjęcia z płyt licencyjnych lub kontenerów z numerami seryjnymi, a także dokumenty — faktury, weksle, raporty finansowe, artykuły i inne. Ta funkcja OCR jest dostępna jako część usługi zarządzanej w chmurze lub lokalnie (kontenery). Obsługuje ona także sieci wirtualne i prywatne punkty końcowe, aby zaspokoić wymagania dotyczące zgodności i prywatności klasy korporacyjnej.

## <a name="read-api"></a>Odczytaj interfejs API 

[Interfejs API odczytu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) przetwarzanie obrazów to najnowsza technologia OCR firmy Microsoft, która wyodrębnia drukowany tekst (siedem języków), tekst napisany ręcznie (tylko w języku angielskim), cyfry i symbole walut z obrazów i wielostronicowych dokumentów PDF. Jest zoptymalizowany pod kątem wyodrębniania tekstu z obrazów w formacie wieloznacznego i wielostronicowych dokumentów PDF z wielojęzycznymi językami. Obsługuje wykrywanie wydrukowanych i odpisanych tekstu (tylko w języku angielskim) w tym samym obrazie lub dokumencie. Pełną listę obsługiwanych języków można znaleźć na stronie [Obsługa języka dla przetwarzanie obrazów](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition) strony.

### <a name="how-it-works"></a>Jak to działa

[Interfejs API odczytu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) obsługuje dokumenty z dużą ilością tekstu do 2000 stron i w związku z tym wykonuje asynchronicznie. Pierwszym krokiem jest wywołanie operacji odczytu. Operacja odczytu pobiera obraz lub dokument PDF jako dane wejściowe i zwraca identyfikator operacji. 

Drugim krokiem jest wywołanie operacji [Pobierz wyniki](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) . Ta operacja przyjmuje Identyfikator operacji, który został utworzony przez operację odczytu. Następnie zwraca wyodrębnioną zawartość tekstową z obrazu lub dokumentu w postaci JSON. Odpowiedź JSON zachowuje pierwotną grupę wierszy rozpoznanych wyrazów. Zawiera wyodrębnione wiersze tekstu i ich współrzędne pola ograniczenia. Każdy wiersz tekstu zawiera wszystkie wyodrębnione wyrazy z ich współrzędnymi i wynikami pewności.

W razie potrzeby, odczyt koryguje obrót rozpoznanej strony, zwracając przesunięcie rotacyjne (w stopniach) o poziomą oś obrazu, jak pokazano na poniższej ilustracji.

![Obraz, który jest obracany, a jego tekst jest odczytywany i zakreślony](./Images/vision-overview-ocr-read.png)

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

Interfejs API odczytu obsługuje wyodrębnianie drukowanego tekstu w języku angielskim, hiszpańskim, niemieckim, francuskim, włoskim, portugalskim i holenderskim. Jeśli scenariusz wymaga obsługi większej liczby języków, zobacz Omówienie interfejsu API OCR w tym dokumencie. Zapoznaj się z listą wszystkich [obsługiwanych języków](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition)

![Obraz, który jest obracany, a jego tekst jest odczytywany i zakreślony](./Images/supported-languages-example.png)

### <a name="mixed-languages-support"></a>Obsługa języków mieszanych

Interfejs API odczytu obsługuje obrazy i dokumenty z wieloma językami, często znane jako dokumenty w języku mieszanym. Robi to przez klasyfikowanie każdego wiersza tekstu w dokumencie do wykrytego języka przed wyodrębnieniem zawartości tekstowej.

![Obraz, który jest obracany, a jego tekst jest odczytywany i zakreślony](./Images/mixed-language-example.png)

### <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Podobnie jak w przypadku wszystkich usług poznawczych, deweloperzy korzystający z usługi odczytu powinni mieć świadomość zasad firmy Microsoft dotyczących danych klientów. Aby dowiedzieć się więcej, zobacz stronę Cognitive Services w [Centrum zaufania firmy Microsoft](https://www.microsoft.com/en-us/trust-center/product-overview) .

### <a name="deploy-on-premises"></a>Wdrażanie lokalnie

Funkcja Read jest również dostępna jako kontener platformy Docker (wersja zapoznawcza) w celu umożliwienia wdrożenia nowych funkcji OCR w Twoim środowisku. Kontenery doskonale nadaje się do określonych wymagań w zakresie zabezpieczeń i zarządzania danymi. Zobacz [temat Instalowanie i uruchamianie kontenerów odczytu.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)


## <a name="ocr-api"></a>INTERFEJS API OCR

[Interfejs API OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) używa starszego modelu rozpoznawania, obsługuje tylko obrazy i wykonuje synchronicznie, zwracając natychmiast z wykrytym tekstem. Obsługuje [więcej języków](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition) niż interfejs API odczytu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [interfejsie API REST do odczytu 3,0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005).
- Postępuj zgodnie z przewodnikiem Szybki Start [tekstu](./QuickStarts/CSharp-hand-text.md) , aby zaimplementować OCR przy użyciu języka C#, Java, JavaScript lub Python wraz z interfejsem API REST.
