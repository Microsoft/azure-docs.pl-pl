---
title: Co to jest optyczne rozpoznawanie znaków?
titleSuffix: Azure Cognitive Services
description: Usługa optycznego rozpoznawania znaków (OCR) wyodrębnia widoczny tekst na obrazie i zwraca go jako ciągi strukturalne.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: da4ada8b505c747d24738e175a1701b5ea73b4e4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536744"
---
# <a name="what-is-optical-character-recognition"></a>Co to jest optyczne rozpoznawanie znaków?

Usługa optycznego rozpoznawania znaków (OCR, Optical Character Recognition) umożliwia wyodrębnianie z obrazów tekstu drukowanego lub odręcznego, takiego jak zdjęcia znaków i produktów na ulicy, a także z faktur dokumentów, faktur, raportów finansowych, artykułów &mdash; i innych. Używa modeli opartych na uczeniu głębokim i pracuje z tekstem na różnych powierzchniach i tłach.

Interfejsy API OCR obsługują wyodrębnianie tekstu drukowanego w [kilku językach.](./language-support.md) Postępuj zgodnie z [przewodnika Szybki start,](./quickstarts-sdk/client-library.md) aby rozpocząć pracę.

![Pokazy OCR](./Images/ocr-demo.gif)

Ta dokumentacja zawiera następujące typy artykułów:
* Przewodnik [Szybki start to](./quickstarts-sdk/client-library.md) instrukcje krok po kroku, które pozwalają na wywołania usługi i uzyskiwanie wyników w krótkim czasie. 
* Przewodniki [z instrukcjami zawierają](./Vision-API-How-to-Topics/call-read-api.md) instrukcje dotyczące korzystania z usługi w bardziej szczegółowe lub dostosowane sposoby.
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions. -->

## <a name="supported-languages"></a>Obsługiwane języki
Interfejsy API OCR obsługują łącznie 73 języki dla tekstu w stylu wydruku. Zapoznaj się z pełną listą języków obsługiwanych [przez OCR.](./language-support.md#optical-character-recognition-ocr) Rozpoznawanie znaków OCR w stylu odręcznym jest obsługiwane wyłącznie w języku angielskim.

## <a name="input-requirements"></a>Wymagania dotyczące danych wejściowych

Wywołanie **Read** przyjmuje obrazy i dokumenty jako dane wejściowe. Mają one następujące wymagania:

* Obsługiwane formaty plików: JPEG, PNG, BMP, PDF i TIFF
* W przypadku plików PDF i TIFF przetwarzanych jest maksymalnie 2000 stron (tylko dwie pierwsze strony w warstwie Bezpłatna).
* Rozmiar pliku musi być mniejszy niż 50 MB (4 MB dla warstwy Bezpłatna) i mieć wymiary co najmniej 50 x 50 pikseli i nie więcej niż 10000 x 10000 pikseli. 

## <a name="read-api"></a>Odczytywanie interfejsu API 

Interfejs [API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) przetwarzanie obrazów Read to najnowsza technologia OCR platformy Azure[(dowiedz](./whats-new.md)się, co nowego), która wyodrębnia tekst drukowany (w kilku językach), tekst odręczny (tylko w języku angielskim), cyfry i symbole walut z obrazów oraz wielostronicowych dokumentów PDF. Jest ona zoptymalizowana pod kątem wyodrębniania tekstu z obrazów z dużym obciążeniem tekstem i wielostronicowych dokumentów PDF w różnych językach. Obsługuje wykrywanie zarówno tekstu drukowanego, jak i odręcznego na tym samym obrazie lub dokumencie.

![Jak OCR konwertuje obrazy i dokumenty na ustrukturyzowane dane wyjściowe z wyodrębnianym tekstem](./Images/how-ocr-works.svg)

### <a name="key-features"></a>Najważniejsze funkcje

Interfejs API odczytu zawiera następujące funkcje. 

* Wyodrębnianie tekstu wydruku w 73 językach
* Wyodrębnianie tekstu odręcznego w języku angielskim
* Wiersze tekstowe i wyrazy z lokalizacją i wynikami ufności
* Nie jest wymagana identyfikacja języka
* Obsługa języków mieszanych, w trybie mieszanym (drukowanie i odręczne)
* Wybieranie stron i zakresów stron z dużych dokumentów wielostronicowych
* Naturalna kolejność odczytywania wierszy tekstu
* Klasyfikacja pisma ręcznego dla wierszy tekstowych
* Dostępny jako bezbłędny kontener platformy Docker do wdrażania lokalnego

Dowiedz [się, jak korzystać z funkcji OCR.](./vision-api-how-to-topics/call-read-api.md)

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>Korzystanie z interfejsu API w chmurze lub wdrażanie lokalne
Interfejsy API read 3.x w chmurze są preferowaną opcją dla większości klientów ze względu na łatwość integracji i wysoką produktywność. Platforma Azure i przetwarzanie obrazów obsługują potrzeby w zakresie skalowania, wydajności, bezpieczeństwa danych i zgodności, a ty koncentrujesz się na spełnianiu potrzeb klientów.

W przypadku wdrożenia lokalnego kontener [Read Docker (wersja zapoznawcza)](./computer-vision-how-to-install-containers.md) umożliwia wdrożenie nowych funkcji OCR we własnym środowisku lokalnym. Kontenery doskonale nadają się do określonych wymagań w zakresie zabezpieczeń i zarządzania danymi.

## <a name="ocr-api"></a>OCR API

Starszy interfejs [API OCR](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f20d) używa starszego modelu rozpoznawania, obsługuje tylko obrazy i wykonuje je synchronicznie, natychmiast po zwróceniu wykrytego tekstu. Listę obsługiwanych języków można znaleźć w kolumnie OCR [obsługiwanych](./language-support.md#optical-character-recognition-ocr) języków.

> [!WARNING]
> Operacje przetwarzanie obrazów 2.0 RecognizeText są w trakcie oznaczania jako przestarzałe na rzecz nowego interfejsu [API](#read-api) odczytu uwzględnionego w tym artykule. Istniejący klienci powinni [przejść na operacje odczytu.](upgrade-api-versions.md)

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Jak w przypadku wszystkich usług Cognitive Services, deweloperzy korzystający z usługi przetwarzania obrazów powinni znać zasady firmy Microsoft dotyczące danych klientów. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Następne kroki

- Wprowadzenie do interfejsu [API REST OCR (Read)](./quickstarts-sdk/client-library.md)lub biblioteki klienta szybki start .
- Dowiedz się więcej o [interfejsie API REST read 3.2.](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)
