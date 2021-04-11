---
title: Co to jest analiza obrazu?
titleSuffix: Azure Cognitive Services
description: Usługa Image Analysis używa wstępnie przeszkolonych modeli AI, aby wyodrębnić wiele różnych funkcji wizualnych z obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/30/2021
ms.author: pafarley
keywords: Obsługa komputerów, aplikacje komputerowe, usługa komputerowa
ms.openlocfilehash: f262fdb49cac4ab9abe7f3f6873160d3059968c6
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106287277"
---
# <a name="what-is-image-analysis"></a>Co to jest analiza obrazu?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Usługa przetwarzanie obrazów Image Analysis może wyodrębnić wiele różnych funkcji wizualnych z obrazów. Można na przykład określić, czy obraz zawiera zawartość dla dorosłych, znaleźć określone marki lub obiekty, czy też znaleźć ludzkie twarze.

Możesz użyć analizy obrazów za pomocą zestawu SDK biblioteki klienta lub bezpośrednio wywołując [interfejs API REST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) . Aby rozpocząć, postępuj zgodnie z [przewodnikiem Szybki Start](quickstarts-sdk/image-analysis-client-library.md) .

Ta dokumentacja zawiera następujące typy artykułów:
* [Przewodniki Szybki Start](./quickstarts-sdk/image-analysis-client-library.md) to instrukcje krok po kroku, które umożliwiają wykonywanie wywołań do usługi i uzyskiwanie wyników w krótkim czasie. 
* [Przewodniki z](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) instrukcjami zawiera instrukcje dotyczące korzystania z usługi w bardziej specyficzny lub dostosowany sposób.
* [Artykuły koncepcyjne](concept-tagging-images.md) zawierają szczegółowe omówienie funkcji i funkcji usługi.
* [Samouczki](./tutorials/storage-lab-tutorial.md) są więcej przewodnikami, które pokazują, jak używać tej usługi jako składnika w szerszym zakresie rozwiązań dla biznesu.

## <a name="image-analysis-features"></a>Funkcje analizy obrazów

Można analizować obrazy, aby zapewnić wgląd w ich funkcje i cechy wizualne. Wszystkie funkcje z poniższej listy są udostępniane przez interfejs API [analizy obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . Aby rozpocząć, Skorzystaj z [przewodnika Szybki Start](./quickstarts-sdk/image-analysis-client-library.md) .


### <a name="tag-visual-features"></a>Tagowanie funkcji wizualnych

Identyfikowanie i tagowanie elementów wizualnych na obrazie w oparciu o zestaw tysięcy rozpoznawalnych obiektów, istot żywych, scenerii i akcji. Gdy Tagi są niejednoznaczne lub nietypowej wiedzy, odpowiedź interfejsu API zawiera wskazówki wyjaśniające kontekst znacznika. Tagowanie nie jest ograniczone do głównego tematu, na przykład do osoby na pierwszym planie, ale uwzględnia także otoczenie (wewnątrz lub na zewnątrz), meble, narzędzia, rośliny, zwierzęta, akcesoria, gadżety itd. [Tagowanie funkcji wizualnych](concept-tagging-images.md)

### <a name="detect-objects"></a>Wykrywanie obiektów

Wykrywanie obiektów jest podobne do tagowania, ale interfejs API zwraca współrzędne pola ograniczenia dla każdego zastosowanego tagu. Na przykład jeśli na obrazie znajduje się pies, kot i osoba, operacja wykrywania utworzy listę tych obiektów wraz z ich współrzędnymi na obrazie. Ta funkcja umożliwia przetwarzanie dalszych relacji między obiektami na obrazie. Ponadto w odpowiednich przypadkach informuje, że obraz zawiera wiele wystąpień tego samego tagu. [Wykrywanie obiektów](concept-object-detection.md)

### <a name="detect-brands"></a>Wykrywanie marek

Identyfikuj marki handlowe na obrazach lub filmach na podstawie bazy danych z tysiącami znaków logo z całego świata. Za pomocą tej funkcji można na przykład ustalać, które marki są najpopularniejsze w mediach społecznościowych lub najpowszechniej promowane za pomocą lokowania produktów w mediach. [Wykrywanie marek](concept-brand-detection.md)

### <a name="categorize-an-image"></a>Klasyfikowanie obrazu

Identyfikowanie i kategoryzowanie całego obrazu za pomocą [taksonomii kategorii](Category-Taxonomy.md) z użyciem dziedzicznych hierarchii obiektów nadrzędnych i podrzędnych. Kategorii można używać oddzielnie lub z naszymi nowymi modelami tagowania.<br/>Obecnie jedynym obsługiwanym językiem tagowania i kategoryzowania obrazów jest angielski. [Klasyfikowanie obrazu](concept-categorizing-images.md)

### <a name="describe-an-image"></a>Opisywanie obrazu

Generowanie opisu całego obrazu w języku zrozumiałym dla człowieka przy użyciu pełnych zdań. Algorytmy przetwarzania obrazów generują różne opisy na podstawie obiektów zidentyfikowanych na obrazie. Poszczególne opisy są oceniane, po czym generowany jest współczynnik ufności. Następnie zwracana jest lista uporządkowana od najwyższego do najniższego współczynnika ufności. [Opisywanie obrazu](concept-describing-images.md)

### <a name="detect-faces"></a>Wykrywanie twarzy

Wykrywanie twarzy na obrazie i dostarczanie informacji o każdej wykrytej twarzy. Usługa przetwarzania obrazów zwraca współrzędne, prostokąt, płeć i wiek osoby dla każdej wykrytej twarzy.<br/>Przetwarzanie obrazów zapewnia podzestaw funkcji usługi [kroju](../face/index.yml) . Możesz użyć usługi twarzy, aby uzyskać bardziej szczegółową analizę, taką jak Identyfikacja twarzy i wykrywanie ułożenia. [Wykrywanie twarzy](concept-detecting-faces.md)

### <a name="detect-image-types"></a>Wykrywanie typów obrazu

Wykrywanie właściwości obrazu, na przykład czy obraz jest rysunkiem lub prawdopodobieństwa, że obraz jest obiektem clipart. [Wykrywanie typów obrazu](concept-detecting-image-types.md)

### <a name="detect-domain-specific-content"></a>Wykrywanie zawartości specyficznej dla domeny

Wykrywanie i identyfikowanie zawartości obrazu specyficznej dla domeny, takiej jak osobistości i charakterystyczne elementy krajobrazu, przy użyciu modeli domeny. Na przykład jeśli obraz zawiera inne osoby, przetwarzanie obrazów może użyć modelu domeny dla osobistości, aby określić, czy osoby wykryte w obrazie są znane osobistości. [Wykrywanie zawartości specyficznej dla domeny](concept-detecting-domain-content.md)

### <a name="detect-the-color-scheme"></a>Wykrywanie schematu kolorów

Analizowanie użycia kolorów na obrazie. Przetwarzanie obrazów może określić, czy obraz jest czarno-biały, czy kolorowy, a w przypadku obrazów kolorowych zidentyfikować kolory dominujące i kolory akcentu. [Wykrywanie schematu kolorów](concept-detecting-color-schemes.md)

### <a name="generate-a-thumbnail"></a>Generowanie miniatur

Analizowanie zawartości obrazu w celu wygenerowania dla niego odpowiedniej miniatury. Usługa przetwarzania obrazów najpierw generuje wysokiej jakości miniaturę, po czym analizuje obiekty na obrazie, aby określić *obszar zainteresowania*. Następnie przetwarzanie obrazów przycina obraz zgodnie z wymaganiami obszaru zainteresowania. W zależności od potrzeb wygenerowana miniatura może mieć współczynnik proporcji inny od współczynnika proporcji oryginalnego obrazu. [Generowanie miniatur](concept-generating-thumbnails.md)

### <a name="get-the-area-of-interest"></a>Określanie obszaru zainteresowania

Przeanalizuj zawartość obrazu w celu zwrócenia współrzędnych *obszaru zainteresowania*. Zamiast przycinania obrazu i generowania miniatur, przetwarzanie obrazów zwraca współrzędne pola ograniczenia regionu, dzięki czemu aplikacja wywołująca może zmodyfikować oryginalny obraz zgodnie z potrzebami. [Określanie obszaru zainteresowania](concept-generating-thumbnails.md#area-of-interest)

## <a name="moderate-content-in-images"></a>Moderowanie zawartości na obrazach

Za pomocą przetwarzanie obrazów można [wykrywać zawartość dla dorosłych](concept-detecting-adult-content.md) w obrazie i zwracać wyniki zaufania dla różnych klasyfikacji. Próg oflagowania zawartości można ustawić na ruchomej skali, aby dopasować swoje preferencje.

## <a name="image-requirements"></a>Wymagania obrazu

Analiza obrazów działa na obrazach, które spełniają następujące wymagania:

- Obraz musi być w formacie JPEG, PNG, GIF lub BMP
- Rozmiar pliku obrazu musi być mniejszy niż 4 megabajty (MB)
- Wymiary obrazu muszą być większe niż 50x50 pikseli

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Jak w przypadku wszystkich usług Cognitive Services, deweloperzy korzystający z usługi przetwarzania obrazów powinni znać zasady firmy Microsoft dotyczące danych klientów. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z analizą obrazu, postępując zgodnie z przewodnikiem Szybki Start w preferowanym języku deweloperskim:

- [Szybki Start: przetwarzanie obrazów interfejsów API REST lub bibliotek klienckich](./quickstarts-sdk/image-analysis-client-library.md)
