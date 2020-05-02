---
title: Wydrukowane, rozpoznawanie tekstu odręcznego — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Koncepcje dotyczące rozpoznawania wydrukowanych i odręcznych tekstu w obrazach przy użyciu interfejs API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: c829579f3ad9922c8c621b6dbfa966b69a2497d1
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690815"
---
# <a name="recognize-printed-and-handwritten-text"></a>Rozpoznawanie tekstu drukowanego i pisma odręcznego

Przetwarzanie obrazów udostępnia wiele usług, które wykrywają i wyodrębniają tekst drukowany lub odręczny, który pojawia się w obrazach. Jest to przydatne w wielu scenariuszach, takich jak notowanie, rejestry medyczne, zabezpieczenia i bankowość. W poniższych trzech sekcjach szczegółowo opisano trzy różne interfejsy API rozpoznawania tekstu, które są zoptymalizowane pod kątem różnych przypadków użycia.

## <a name="read-api"></a>Odczytaj interfejs API

Interfejs API odczytu wykrywa zawartość tekstową w obrazie przy użyciu najnowszych modeli rozpoznawania i konwertuje zidentyfikowany tekst na strumień znaków do odczytu maszynowego. Są one zoptymalizowane pod kątem obrazów z dużym użyciem tekstu (takich jak dokumenty, które zostały przeskanowane cyfrowo) i obrazów z dużą ilością szumów wizualnych. Określi model rozpoznawania, który ma być używany dla każdego wiersza tekstu, obsługując obrazy zarówno w postaci tekstu, jak i w postaci odręcznej. Interfejs API odczytu jest wykonywany asynchronicznie, ponieważ w większych dokumentach można zwrócić wynik.

Operacja odczytu zachowuje pierwotne Grupowanie wierszy w danych wyjściowych. Każdy wiersz zawiera współrzędne pola ograniczenia, a każdy wyraz w wierszu ma także własne współrzędne. Jeśli wyraz został rozpoznany z niskim poziomem pewności, te informacje są również przekazywane. Aby dowiedzieć się więcej, zobacz [Przeczytaj dokumenty referencyjne interfejsu API v 2.0](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) lub [Przeczytaj dokumentację referencyjną interfejsu API v 3.0 w wersji zapoznawczej](https://go.microsoft.com/fwlink/?linkid=2118322) .

> [!NOTE]
> Ta funkcja jest dostępna tylko dla tekstu w języku angielskim i hiszpańskim (wersja zapoznawcza 3.0).

### <a name="image-requirements"></a>Wymagania obrazu

Interfejs API odczytu działa z obrazami spełniającymi następujące wymagania:

- Obraz musi być przedstawiony w formacie JPEG, PNG, BMP, PDF lub TIFF.
- Wymiary obrazu muszą mieścić się w przedziale od 50 x 50 do 10000 x 10000 pikseli. Strony PDF muszą mieć 17 x 17 cali lub mniej.
- Rozmiar pliku obrazu musi być mniejszy niż 20 megabajtów (MB).

### <a name="limitations"></a>Ograniczenia

Jeśli używasz subskrypcji warstwy Bezpłatna, interfejs API odczytu będzie przetwarzać tylko pierwsze dwie strony dokumentu PDF lub TIFF. Dzięki płatnej subskrypcji proces będzie przetwarzać do 200 stron. Należy również zauważyć, że interfejs API wykryje maksymalnie 300 wierszy na stronie.

## <a name="ocr-optical-character-recognition-api"></a>Interfejs API OCR (optyczne rozpoznawanie znaków)

Interfejs API optycznego rozpoznawania znaków (OCR) przetwarzanie obrazów jest podobny do interfejsu API odczytu, ale jest wykonywany synchronicznie i nie jest zoptymalizowany pod kątem dużych dokumentów. Używa wcześniejszego modelu rozpoznawania, ale współpracuje z większą liczbą języków; Zobacz [obsługę języka](language-support.md#text-recognition) , aby uzyskać pełną listę obsługiwanych języków.

W razie potrzeby OCR koryguje obrót rozpoznanego tekstu, zwracając przesunięcie rotacyjne (w stopniach) o poziomą oś obrazu. OCR udostępnia również współrzędne ramki każdego wyrazu, jak pokazano na poniższej ilustracji.

![Obraz, który jest obracany, a jego tekst jest odczytywany i zakreślony](./Images/vision-overview-ocr.png)

Zobacz [dokumenty referencyjne OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) , aby dowiedzieć się więcej.

### <a name="image-requirements"></a>Wymagania obrazu

Interfejs API OCR działa na obrazach, które spełniają następujące wymagania:

* Obraz musi być przedstawiony w formacie JPEG, PNG, GIF lub BMP.
* Rozmiar obrazu wejściowego musi mieścić się w przedziale od 50 x 50 do 4200 x 4200 pikseli.
* Tekst w obrazie może być obrócony przez dowolną wielokrotność 90 stopni i niewielki kąt do 40 stopni.

### <a name="limitations"></a>Ograniczenia

Na fotografiach, w których tekst jest dominujący, fałszywe dodatnie mogą pochodzić z częściowo rozpoznanych wyrazów. W przypadku niektórych fotografii, zwłaszcza zdjęć bez żadnego tekstu, precyzja może się różnić w zależności od typu obrazu.

## <a name="recognize-text-api"></a>Interfejs API Rozpoznawanie tekstu

> [!NOTE]
> Interfejs API Rozpoznawanie tekstu jest przestarzały na korzyść interfejsu API odczytu. Interfejs API odczytu ma podobne możliwości i jest aktualizowany do obsługi plików PDF, TIFF i wielostronicowych.

Interfejs API Rozpoznawanie tekstu jest podobny do OCR, ale jest wykonywany asynchronicznie i używa zaktualizowanych modeli rozpoznawania. Więcej informacji można znaleźć w [dokumentacji dotyczącej interfejsów API rozpoznawanie tekstu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) .

### <a name="image-requirements"></a>Wymagania obrazu

Interfejs API Rozpoznawanie tekstu współpracuje z obrazami spełniającymi następujące wymagania:

- Obraz musi być przedstawiony w formacie JPEG, PNG lub BMP.
- Wymiary obrazu muszą mieścić się w przedziale od 50 x 50 do 4200 x 4200 pikseli.
- Rozmiar pliku obrazu musi być mniejszy niż 4 megabajty (MB).

## <a name="limitations"></a>Ograniczenia

Dokładność operacji rozpoznawania tekstu zależy od jakości obrazów. Następujące czynniki mogą spowodować niedokładne odczytywanie:

* Rozmyte obrazy.
* Tekst odręczny lub napisany kursywą.
* Artystyczne style czcionki.
* Mały rozmiar tekstu.
* Złożone tła, cienie, oślepiające światło nad tekstem lub zniekształcenie perspektywy.
* Na początku wyrazów lub brakuje wielkich liter.
* Indeks dolny, indeks górny lub tekst przekreślony.

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z przewodnikiem Szybki Start dotyczącym [wyodrębniania tekstu (odczyt)](./QuickStarts/CSharp-hand-text.md) , aby zaimplementować rozpoznawanie tekstu w prostej aplikacji języka C#.
