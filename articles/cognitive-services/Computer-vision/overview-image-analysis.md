---
title: Co to jest analiza obrazów?
titleSuffix: Azure Cognitive Services
description: Usługa Analizy obrazów używa wstępnie wytrenowane modele AI do wyodrębniania wielu różnych cech wizualnych z obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/30/2021
ms.author: pafarley
keywords: computer vision, computer vision applications, computer vision service
ms.openlocfilehash: 0258eb7c57bc0734b5c0a67644cbaa4f62a34537
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766919"
---
# <a name="what-is-image-analysis"></a>Co to jest analiza obrazów?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Usługa przetwarzanie obrazów Analizy obrazów może wyodrębnić z obrazów szeroką gamę funkcji wizualnych. Może na przykład określić, czy obraz zawiera zawartość dla dorosłych, znaleźć konkretne marki lub obiekty albo znaleźć ludzkie twarze.

Analizy obrazów można używać za pośrednictwem zestawu SDK biblioteki klienta lub przez bezpośrednie wywołanie interfejsu [API REST.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v2-ga/operations/5d986960601faab4bf452005) Postępuj zgodnie z [przewodnika Szybki start,](quickstarts-sdk/image-analysis-client-library.md) aby rozpocząć pracę.

Ta dokumentacja zawiera następujące typy artykułów:
* Przewodnik [Szybki start to](./quickstarts-sdk/image-analysis-client-library.md) instrukcje krok po kroku, które pozwalają na wywołania usługi i uzyskiwanie wyników w krótkim czasie. 
* Przewodniki [z instrukcjami zawierają](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) instrukcje dotyczące korzystania z usługi w bardziej szczegółowe lub dostosowane sposoby.
* Artykuły [koncepcyjne](concept-tagging-images.md) zawierają szczegółowe wyjaśnienia dotyczące funkcjonalności i funkcji usługi.
* Samouczki [są](./tutorials/storage-lab-tutorial.md) dłuższymi przewodnikami, które pokazują, jak używać tej usługi jako składnika w szerszych rozwiązaniach biznesowych.

## <a name="image-analysis-features"></a>Funkcje analizy obrazów

Możesz analizować obrazy w celu zapewnienia szczegółowych informacji o ich cechach wizualnych i cechach. Wszystkie funkcje na poniższej liście są udostępniane przez interfejs API analizowanie obrazów [API.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) Skorzystaj z przewodnika [Szybki start,](./quickstarts-sdk/image-analysis-client-library.md) aby rozpocząć pracę.


### <a name="tag-visual-features"></a>Tagowanie funkcji wizualnych

Identyfikowanie i tagowanie elementów wizualnych na obrazie w oparciu o zestaw tysięcy rozpoznawalnych obiektów, istot żywych, scenerii i akcji. Gdy tagi są niejednoznaczne lub nie są powszechnie używane, odpowiedź interfejsu API zawiera wskazówki w celu wyjaśnienia kontekstu tagu. Tagowanie nie jest ograniczone do głównego tematu, na przykład do osoby na pierwszym planie, ale uwzględnia także otoczenie (wewnątrz lub na zewnątrz), meble, narzędzia, rośliny, zwierzęta, akcesoria, gadżety itd. [Tagowanie funkcji wizualnych](concept-tagging-images.md)

### <a name="detect-objects"></a>Wykrywanie obiektów

Wykrywanie obiektów jest podobne do tagowania, ale interfejs API zwraca współrzędne pola ograniczenia dla każdego zastosowanego tagu. Na przykład jeśli na obrazie znajduje się pies, kot i osoba, operacja wykrywania utworzy listę tych obiektów wraz z ich współrzędnymi na obrazie. Ta funkcja umożliwia przetwarzanie dalszych relacji między obiektami na obrazie. Ponadto w odpowiednich przypadkach informuje, że obraz zawiera wiele wystąpień tego samego tagu. [Wykrywanie obiektów](concept-object-detection.md)

### <a name="detect-brands"></a>Wykrywanie marek

Identyfikuj marki handlowe na obrazach lub filmach na podstawie bazy danych z tysiącami znaków logo z całego świata. Za pomocą tej funkcji można na przykład ustalać, które marki są najpopularniejsze w mediach społecznościowych lub najpowszechniej promowane za pomocą lokowania produktów w mediach. [Wykrywanie marek](concept-brand-detection.md)

### <a name="categorize-an-image"></a>Kategoryzowanie obrazu

Identyfikowanie i kategoryzowanie całego obrazu za pomocą [taksonomii kategorii](Category-Taxonomy.md) z użyciem dziedzicznych hierarchii obiektów nadrzędnych i podrzędnych. Kategorii można używać oddzielnie lub z naszymi nowymi modelami tagowania.<br/>Obecnie jedynym obsługiwanym językiem tagowania i kategoryzowania obrazów jest angielski. [Kategoryzowanie obrazu](concept-categorizing-images.md)

### <a name="describe-an-image"></a>Opisywanie obrazu

Generowanie opisu całego obrazu w języku zrozumiałym dla człowieka przy użyciu pełnych zdań. Algorytmy przetwarzania obrazów generują różne opisy na podstawie obiektów zidentyfikowanych na obrazie. Poszczególne opisy są oceniane, po czym generowany jest współczynnik ufności. Następnie zwracana jest lista uporządkowana od najwyższego do najniższego współczynnika ufności. [Opisywanie obrazu](concept-describing-images.md)

### <a name="detect-faces"></a>Wykrywanie twarzy

Wykrywanie twarzy na obrazie i dostarczanie informacji o każdej wykrytej twarzy. Usługa przetwarzania obrazów zwraca współrzędne, prostokąt, płeć i wiek osoby dla każdej wykrytej twarzy.<br/>przetwarzanie obrazów udostępnia podzestaw [funkcji](../face/index.yml) usługi rozpoznawania twarzy. Usługa rozpoznawania twarzy umożliwia bardziej szczegółową analizę, na przykład identyfikację twarzy i wykrywanie ułożenia. [Wykrywanie twarzy](concept-detecting-faces.md)

### <a name="detect-image-types"></a>Wykrywanie typów obrazu

Wykrywanie właściwości obrazu, na przykład czy obraz jest rysunkiem lub prawdopodobieństwa, że obraz jest obiektem clipart. [Wykrywanie typów obrazu](concept-detecting-image-types.md)

### <a name="detect-domain-specific-content"></a>Wykrywanie zawartości specyficznej dla domeny

Wykrywanie i identyfikowanie zawartości obrazu specyficznej dla domeny, takiej jak osobistości i charakterystyczne elementy krajobrazu, przy użyciu modeli domeny. Jeśli na przykład obraz zawiera osoby, przetwarzanie obrazów użyć modelu domeny dla osobistości, aby określić, czy osoby wykryte na obrazie są znanymi osobistościami. [Wykrywanie zawartości specyficznej dla domeny](concept-detecting-domain-content.md)

### <a name="detect-the-color-scheme"></a>Wykrywanie schematu kolorów

Analizowanie użycia kolorów na obrazie. Przetwarzanie obrazów może określić, czy obraz jest czarno-biały, czy kolorowy, a w przypadku obrazów kolorowych zidentyfikować kolory dominujące i kolory akcentu. [Wykrywanie schematu kolorów](concept-detecting-color-schemes.md)

### <a name="generate-a-thumbnail"></a>Generowanie miniatur

Analizowanie zawartości obrazu w celu wygenerowania dla niego odpowiedniej miniatury. Usługa przetwarzania obrazów najpierw generuje wysokiej jakości miniaturę, po czym analizuje obiekty na obrazie, aby określić *obszar zainteresowania*. Następnie przetwarzanie obrazów przycina obraz zgodnie z wymaganiami obszaru zainteresowania. W zależności od potrzeb wygenerowana miniatura może mieć współczynnik proporcji inny od współczynnika proporcji oryginalnego obrazu. [Generowanie miniatur](concept-generating-thumbnails.md)

### <a name="get-the-area-of-interest"></a>Określanie obszaru zainteresowania

Przeanalizuj zawartość obrazu w celu zwrócenia współrzędnych *obszaru zainteresowania*. Zamiast przycinania obrazu i generowania miniatury przetwarzanie obrazów zwraca współrzędne pola granicy regionu, dzięki czemu aplikacja wywołująca może zmodyfikować oryginalny obraz zgodnie z potrzebami. [Określanie obszaru zainteresowania](concept-generating-thumbnails.md#area-of-interest)

## <a name="moderate-content-in-images"></a>Moderowanie zawartości na obrazach

Możesz użyć przetwarzanie obrazów do [wykrywania zawartości dla](concept-detecting-adult-content.md) dorosłych na obrazie i zwracania ocen ufności dla różnych klasyfikacji. Próg flagowania zawartości można ustawić na przesuwanej skali w celu dostosowania do twoich preferencji.

## <a name="image-requirements"></a>Wymagania obrazu

Analiza obrazów działa na obrazach spełniających następujące wymagania:

- Obraz musi być w formacie JPEG, PNG, GIF lub BMP
- Rozmiar pliku obrazu musi być mniejszy niż 4 megabajty (MB)
- Wymiary obrazu muszą być większe niż 50x50 pikseli

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Jak w przypadku wszystkich usług Cognitive Services, deweloperzy korzystający z usługi przetwarzania obrazów powinni znać zasady firmy Microsoft dotyczące danych klientów. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Następne kroki

Rozpoczynanie pracy z analizą obrazów, korzystając z przewodnika Szybki start w preferowanym języku programowania:

- [Szybki start: przetwarzanie obrazów API REST lub bibliotek klienckich](./quickstarts-sdk/image-analysis-client-library.md)
