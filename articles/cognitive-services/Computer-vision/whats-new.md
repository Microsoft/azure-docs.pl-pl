---
title: Co nowego w przetwarzanie obrazów?
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera informacje o przetwarzanie obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/13/2021
ms.author: pafarley
ms.openlocfilehash: 31ca65099d645b7fdfee70275515f0a864cd905f
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007401"
---
# <a name="whats-new-in-computer-vision"></a>Co nowego w przetwarzanie obrazów

Dowiedz się, co nowego w usłudze. Te elementy mogą być informacjami o wersji, klipami wideo, wpisami w blogu i innymi rodzajami informacji. Oznacz Tę stronę zakładką, aby zachować aktualność za pomocą usługi.

## <a name="february-2021"></a>Luty 2021 r.

### <a name="read-api-v32-public-preview-with-ocr-support-for-73-languages"></a>Przeczytaj publiczną wersję zapoznawczą interfejsu API v 3.2 z obsługą OCR dla języków 73
Informacje o wersji zapoznawczej interfejsu API v 3.2 przetwarzanie obrazów dostępne jako usługa w chmurze i kontener Docker obejmują następujące aktualizacje:
* Program [OCR dla języków 73, w](./language-support.md#optical-character-recognition-ocr) tym uproszczony i tradycyjny język chiński, japoński, koreański i łaciński.
* Naturalna kolejność odczytywania danych wyjściowych wiersza tekstu.
* Klasyfikacja stylu pisma ręcznego dla linii tekstowych wraz z oceną pewności (tylko języki łacińskie).
* Wyodrębnij tekst tylko dla wybranych stron dla dokumentu wielostronicowego.
* Dostępne jako [kontener Distroless](./computer-vision-how-to-install-containers.md?tabs=version-3-2) dla wdrożenia lokalnego.

[Dowiedz się więcej](concept-recognizing-text.md) o interfejsie API odczytu.

> [!div class="nextstepaction"]
> [Korzystanie z publicznej wersji zapoznawczej interfejsu API Read v 3.2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005)


## <a name="january-2021"></a>Styczeń 2021 r.

### <a name="spatial-analysis-container-update"></a>Aktualizacja kontenera analizy przestrzennej

Nowa wersja [kontenera analizy przestrzennej](spatial-analysis-container.md) została wydana z nowym zestawem funkcji. Ten kontener platformy Docker pozwala analizować wideo strumieniowe w czasie rzeczywistym, aby zrozumieć relacje przestrzenne między osobami i ich przenoszeniem przez środowiska fizyczne. 

* Można teraz skonfigurować [operacje analizy przestrzennej](spatial-analysis-operations.md) , aby wykrywać, czy osoba jest w tej samej ochronie, na przykład jako maskę. 
    * Klasyfikator masek można włączyć dla `personcount` `personcrossingline` operacji i, `personcrossingpolygon` konfigurując `ENABLE_FACE_MASK_CLASSIFIER` parametr.
    * Atrybuty `face_mask` i `face_noMask` zostaną zwrócone jako metadane z wynikiem dopasowania dla każdej osoby wykrytej w strumieniu wideo
* Operacja *personcrossingpolygon* została rozszerzona, aby umożliwić Obliczanie czasu mieszkania przez osobę w strefie. Można ustawić `type` parametr w konfiguracji strefy dla operacji do `zonedwelltime` i nowe zdarzenie typu *personZoneDwellTimeEvent* będzie zawierać `durationMs` pole wypełnione liczbą milisekund, jaką osoba spędziła w strefie.
* **Zmiana podziału**: zmieniono nazwę zdarzenia *personZoneEvent* na *personZoneEnterExitEvent*. To zdarzenie jest zgłaszane przez operację *personcrossingpolygon* , gdy osoba wejdzie w stronę lub opuszcza ją i zawiera informacje kierunkowe z numerowaną stroną przekreślonej strefy.
* Adres URL wideo można podać jako "prywatny parametr/zasłonięty" we wszystkich operacjach. Mieszanie jest teraz opcjonalne i działa tylko wtedy, gdy `KEY` i `IV` są dostarczane jako zmienne środowiskowe.
* Kalibracja jest domyślnie włączona dla wszystkich operacji. Ustaw, `do_calibration: false` Aby go wyłączyć.
* Dodano obsługę autokalibracji (domyślnie wyłączone) za pośrednictwem `enable_recalibration` parametru, zapoznaj się ze szczegółami [operacji analizy przestrzennej](https://docs.microsoft.com/azure/cognitive-services/computer-vision/spatial-analysis-operations) , aby uzyskać szczegółowe informacje
* Parametry kalibracji aparatu fotograficznego do `DETECTOR_NODE_CONFIG` . Aby uzyskać szczegółowe informacje, zobacz operacje związane z [analizą przestrzenną](https://docs.microsoft.com/azure/cognitive-services/computer-vision/spatial-analysis-operations) .


## <a name="october-2020"></a>Październik 2020 r.

### <a name="computer-vision-api-v31-ga"></a>Interfejs API przetwarzania obrazów v 3.1

Interfejs API przetwarzania obrazów ogólnie dostępna wersja została uaktualniona do wersji 3.1.

## <a name="september-2020"></a>Wrzesień 2020

### <a name="spatial-analysis-container-preview"></a>Podgląd kontenera analizy przestrzennej

[Kontener analizy przestrzennej](spatial-analysis-container.md) jest teraz w wersji zapoznawczej. Funkcja analizy przestrzennej przetwarzanie obrazów umożliwia analizowanie wideo przesyłania strumieniowego w czasie rzeczywistym w celu zrozumienia relacji przestrzennych między osobami i ich przenoszeniem za pomocą środowisk fizycznych. Analiza przestrzenna jest kontenerem platformy Docker, którego można używać lokalnie. 

### <a name="read-api-v31-public-preview-adds-ocr-for-japanese"></a>Read API v 3.1 Public Preview dodaje OCR dla języka japońskiego
W publicznej wersji zapoznawczej interfejsu API w przetwarzanie obrazów v 3.1 dodano następujące funkcje:
* OCR dla języka japońskiego
* Dla każdego wiersza tekstu wskaż, czy wygląd jest stylem pisma ręcznego, czy drukowania, wraz z oceną pewności (tylko języki łacińskie).
* Dla dokumentu wielostronicowego Wyodrębnij tekst tylko dla wybranych stron lub zakresu stron.

* Ta wersja zapoznawcza interfejsu API odczytu obsługuje język angielski, holenderski, francuski, niemiecki, włoski, japoński, portugalski, chiński (uproszczony) i hiszpański.

Zobacz [Omówienie interfejsu API odczytu](concept-recognizing-text.md) , aby dowiedzieć się więcej.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o funkcji Read API v 3.1 w publicznej wersji zapoznawczej 2](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)

## <a name="july-2020"></a>Lipiec 2020 r.

### <a name="read-api-v31-public-preview-with-ocr-for-simplified-chinese"></a>Przeczytaj publiczną wersję zapoznawczą interfejsu API v 3.1 z OCR for chiński uproszczony
W publicznej wersji zapoznawczej interfejsu API usługi przetwarzanie obrazów Read 3.1 Dodano obsługę języka chińskiego uproszczonego.

* Ta wersja zapoznawcza interfejsu API odczytu obsługuje język angielski, holenderski, francuski, niemiecki, włoski, portugalski, chiński (uproszczony) i hiszpański.

Zobacz [Omówienie interfejsu API odczytu](concept-recognizing-text.md) , aby dowiedzieć się więcej.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wersji Read API v 3.1 Public Preview 1](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)

## <a name="may-2020"></a>Maj 2020 r.
W interfejs API przetwarzania obrazów v 3.0 wprowadzono ogólną dostępność z aktualizacjami [interfejsu API odczytu](concept-recognizing-text.md):

* Obsługa języków angielskim, holenderskim, francuskim, niemieckim, włoskim, portugalskim i hiszpańskim
* Ulepszona dokładność
* Wynik pewności dla każdego wyodrębnionego wyrazu
* Nowy format danych wyjściowych

## <a name="march-2020"></a>Marzec 2020 r.

* Protokół TLS 1,2 jest teraz wymuszany dla wszystkich żądań HTTP do tej usługi. Aby uzyskać więcej informacji, zobacz [Azure Cognitive Services Security](../cognitive-services-security.md).

## <a name="january-2020"></a>Styczeń 2020 r.

### <a name="read-api-30-public-preview"></a>Przeczytaj informacje o publicznej wersji zapoznawczej interfejsu API 3,0

Masz teraz możliwość używania wersji 3,0 interfejsu API odczytu do wyodrębniania wydrukowanych lub odręcznych tekstu z obrazów. W porównaniu z wcześniejszymi wersjami 3,0 zapewnia:
* Ulepszona dokładność
* Nowy format danych wyjściowych
* Wynik pewności dla każdego wyodrębnionego wyrazu
* Obsługa języków hiszpańskich i angielskich przy użyciu dodatkowego parametru języka

Postępuj zgodnie z [przewodnikiem Szybki Start dla tekstu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-hand-text.md?tabs=version-3) , aby rozpocząć korzystanie z interfejsu API 3,0.

## <a name="cognitive-service-updates"></a>Aktualizacje usługi poznawczej

[Anonse aktualizacji platformy Azure dla Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
