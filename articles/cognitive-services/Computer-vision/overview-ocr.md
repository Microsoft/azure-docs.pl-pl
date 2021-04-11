---
title: Co to jest rozpoznawanie znaków optycznych?
titleSuffix: Azure Cognitive Services
description: Usługa optycznego rozpoznawania znaków (OCR) wyodrębnia widoczny tekst w obrazie i zwraca go jako ciągi strukturalne.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 3fff9f4bd34fc1defdb50f2eefbc8ac1f39b46af
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106287274"
---
# <a name="what-is-optical-character-recognition"></a>Co to jest rozpoznawanie znaków optycznych?

Usługa optycznego rozpoznawania znaków (OCR) umożliwia wyodrębnianie wydrukowanych lub odręcznych tekstu z obrazów, takich jak zdjęcia z płyt licencyjnych lub kontenerów z numerami seryjnymi, a także dokumenty &mdash; faktury, weksle, raporty finansowe, artykuły i inne. Używa on modeli opartych na uczeniu głębokiego i współpracuje z tekstem na różnych powierzchniach i w tle.

Interfejsy API OCR obsługują wyodrębnianie drukowanego tekstu w [kilku językach](./language-support.md). Aby rozpocząć, Skorzystaj z [przewodnika Szybki Start](./quickstarts-sdk/client-library.md) .

![Demonstracje OCR](./Images/ocr-demo.gif)

Ta dokumentacja zawiera następujące typy artykułów:
* [Przewodniki Szybki Start](./quickstarts-sdk/client-library.md) to instrukcje krok po kroku, które umożliwiają wykonywanie wywołań do usługi i uzyskiwanie wyników w krótkim czasie. 
* [Przewodniki z](./Vision-API-How-to-Topics/call-read-api.md) instrukcjami zawiera instrukcje dotyczące korzystania z usługi w bardziej specyficzny lub dostosowany sposób.
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions. -->

## <a name="supported-languages"></a>Obsługiwane języki
Interfejsy API OCR obsługują łącznie 73 języków na potrzeby tekstu w stylu drukowania. Zapoznaj się z pełną listą [języków obsługiwanych przez aparat OCR](./language-support.md#optical-character-recognition-ocr). ROZPOZNAWANIE w stylu odręcznego jest obsługiwane wyłącznie w języku angielskim.

## <a name="input-requirements"></a>Wymagania wejściowe

Wywołanie **odczytu** pobiera obrazy i dokumenty jako dane wejściowe. Mają one następujące wymagania:

* Obsługiwane formaty plików: JPEG, PNG, BMP, PDF i TIFF
* W przypadku plików PDF i TIFF, do 2000 stron (tylko pierwsze dwie strony dla warstwy Bezpłatna) są przetwarzane.
* Rozmiar pliku musi być mniejszy niż 50 MB (4 MB dla warstwy Bezpłatna) i wymiary co najmniej 50 x 50 pikseli i maksymalnie 10000 x 10000 pikseli. 
* Wymiary PDF muszą mieć co najwyżej 17 x 17 cali, odpowiadające rozmiarowi papieru legalnego lub A3 i mniejszym.

## <a name="read-api"></a>Odczytaj interfejs API 

[Interfejs API odczytu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) przetwarzanie obrazów to najnowsza technologia OCR platformy Azure ([Dowiedz się, co nowego](./whats-new.md)), która wyodrębnia drukowany tekst (w kilku językach), tekst odręczny (tylko w języku angielskim), cyfry i symbole walutowe z obrazów i wielostronicowych dokumentów PDF. Jest zoptymalizowany pod kątem wyodrębniania tekstu z obrazów z obrazami i wielostronicowych dokumentów PDF z wielojęzycznymi językami. Obsługuje ona wykrywanie wydrukowanych i odręcznych tekstu w tym samym obrazie lub dokumencie.

![Jak OCR konwertuje obrazy i dokumenty na strukturalne dane wyjściowe z wyodrębnionym tekstem](./Images/how-ocr-works.svg)


## <a name="use-the-cloud-api-or-deploy-on-premise"></a>Korzystanie z interfejsu API chmury lub Wdrażanie lokalne
Interfejs API chmury odczytywania 3. x jest preferowaną opcją dla większości klientów ze względu na łatwość integracji i szybką produktywność z usługi Box. Platforma Azure i usługa przetwarzanie obrazów obsługują skalowanie, wydajność, bezpieczeństwo danych i wymagania dotyczące zgodności podczas skoncentrowania się na potrzebach klientów.

W przypadku wdrożenia lokalnego [odczytywanie kontenera Docker (wersja zapoznawcza)](./computer-vision-how-to-install-containers.md) umożliwia wdrażanie nowych funkcji rozpoznawania w środowisku lokalnym. Kontenery doskonale nadają się do określonych wymagań w zakresie zabezpieczeń i zarządzania danymi.

## <a name="ocr-api"></a>INTERFEJS API OCR

Starsza wersja [interfejsu API OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d) korzysta ze starszego modelu rozpoznawania, obsługuje tylko obrazy i wykonuje synchronicznie, zwracając natychmiast z wykrytym tekstem. Listę obsługiwanych języków można znaleźć w kolumnie OCR dla [obsługiwanych języków](./language-support.md#optical-character-recognition-ocr) .

## <a name="recognizetext-api"></a>Interfejs API RecognizeText

> [!WARNING]
> Operacje RecognizeText przetwarzanie obrazów 2,0 są w trakcie wycofywania na korzyść nowego [interfejsu API odczytu](#read-api) omówionego w tym artykule. Istniejący klienci powinni [przejść do korzystania z operacji odczytu](upgrade-api-versions.md).

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Jak w przypadku wszystkich usług Cognitive Services, deweloperzy korzystający z usługi przetwarzania obrazów powinni znać zasady firmy Microsoft dotyczące danych klientów. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Następne kroki

- Rozpocznij pracę z [interfejsem API REST OCR lub biblioteką klienta — szybki start](./quickstarts-sdk/client-library.md).
- Dowiedz się więcej o [interfejsie API REST do odczytu 3,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005).
- Dowiedz się więcej o [interfejsie API REST usługi Read 3,2 Public Preview](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) z obsługą łącznej liczby języków 73.
