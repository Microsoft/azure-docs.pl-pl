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
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: f007ddf2625bf9e8b9d4365902ac1511ea95fa81
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90904092"
---
# <a name="whats-new-in-computer-vision"></a>Co nowego w przetwarzanie obrazów

Dowiedz się, co nowego w usłudze. Te elementy mogą być informacjami o wersji, klipami wideo, wpisami w blogu i innymi rodzajami informacji. Oznacz Tę stronę zakładką, aby zachować aktualność za pomocą usługi.

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

Postępuj zgodnie z [przewodnikiem Szybki Start dla tekstu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text?tabs=version-3) , aby rozpocząć korzystanie z interfejsu API 3,0.

## <a name="cognitive-service-updates"></a>Aktualizacje usługi poznawczej

[Anonse aktualizacji platformy Azure dla Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
