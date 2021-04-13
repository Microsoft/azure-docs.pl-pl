---
title: Co nowego w programie przetwarzanie obrazów?
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wiadomości dotyczące przetwarzanie obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/13/2021
ms.author: pafarley
ms.openlocfilehash: e42096fc32a504ae329d3b179004b6a123de4469
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365641"
---
# <a name="whats-new-in-computer-vision"></a>Co nowego w programie przetwarzanie obrazów

Dowiedz się, co nowego w usłudze. Mogą to być informacje o wersji, filmy wideo, wpisy w blogu i inne typy informacji. Dodaj tę stronę do zakładek, aby być na bieżąco z usługą.

## <a name="april-2021"></a>Kwiecień 2021 r.

### <a name="computer-vision-v32-ga"></a>przetwarzanie obrazów 3.2 (wersja 3.2)

Interfejs PRZETWARZANIE OBRAZÓW API w wersji 3.2 jest teraz ogólnie dostępny z następującymi aktualizacjami:
* Ulepszony model tagowania obrazów: analizuje zawartość wizualizacji i generuje odpowiednie tagi na podstawie obiektów, akcji i zawartości wyświetlanych na obrazie. Ta usługa jest dostępna za pośrednictwem [interfejsu API tagów obrazu.](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f200) Aby dowiedzieć się więcej, zobacz Przewodnik po [analizie](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtocallvisionapi) obrazów [i](https://docs.microsoft.com/azure/cognitive-services/computer-vision/overview-image-analysis) omówienie.
* Zaktualizowany model moderowania zawartości: wykrywa obecność treści dla dorosłych i udostępnia flagi do filtrowania obrazów zawierających zawartość wizualną dla dorosłych, erudycyjną i cytną. Jest to dostępne za pośrednictwem interfejsu [API analizowania](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b). Aby dowiedzieć się więcej, zobacz Przewodnik po [analizie](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtocallvisionapi) obrazów [i](https://docs.microsoft.com/azure/cognitive-services/computer-vision/overview-image-analysis) omówienie.
* [Funkcja OCR (Read) jest dostępna dla 73](./language-support.md#optical-character-recognition-ocr) języków, w tym uproszczonych i tradycyjnych, japońskich, koreańskich i łacińskich.
* [OCR (Read)](./overview-ocr.md) jest również dostępny jako kontener [bez](./computer-vision-how-to-install-containers.md?tabs=version-3-2) dystrybucji do wdrażania lokalnego.

> [!div class="nextstepaction"]
> [Zobacz przetwarzanie obrazów wersji 3.2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)

## <a name="march-2021"></a>Marzec 2021 r.

### <a name="computer-vision-32-public-preview-update"></a>przetwarzanie obrazów publicznej wersji zapoznawczej 3.2

Publiczna wersja zapoznawcza interfejsu PRZETWARZANIE OBRAZÓW API w wersji 3.2 została zaktualizowana. Wersja zapoznawcza zawiera wszystkie przetwarzanie obrazów oraz zaktualizowane interfejsy API odczytu i analizy.

> [!div class="nextstepaction"]
> [Zobacz przetwarzanie obrazów 3.2 publicznej wersji zapoznawczej 3](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)

## <a name="february-2021"></a>Luty 2021 r.

### <a name="read-api-v32-public-preview-with-ocr-support-for-73-languages"></a>Odczytywanie publicznej wersji zapoznawczej interfejsu API w wersji 3.2 z obsługą OCR dla 73 języków
przetwarzanie obrazów w publicznej wersji zapoznawczej interfejsu API odczytu w wersji 3.2, który jest dostępny jako usługa w chmurze i kontener platformy Docker, zawiera następujące aktualizacje:
* [OCR dla 73 języków, w](./language-support.md#optical-character-recognition-ocr) tym uproszczony i chiński tradycyjny, japoński, koreański i łaciński.
* Naturalna kolejność odczytywania danych wyjściowych wiersza tekstu (tylko w językach łacińskich)
* Klasyfikacja stylu pisma ręcznego dla wierszy tekstowych wraz ze ocenami ufności (tylko w językach łacińskich).
* Wyodrębnianie tekstu tylko dla wybranych stron dla dokumentu wielostronicowego.
* Dostępny jako [kontener bez dystrybucji](./computer-vision-how-to-install-containers.md?tabs=version-3-2) do wdrożenia lokalnego.

Aby dowiedzieć się więcej, zobacz Przewodnik po przewodniku po [interfejsie API](Vision-API-How-to-Topics/call-read-api.md) odczytywania.

> [!div class="nextstepaction"]
> [Korzystanie z publicznej wersji zapoznawczej interfejsu API odczytu w wersji 3.2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)


## <a name="january-2021"></a>Styczeń 2021 r.

### <a name="spatial-analysis-container-update"></a>Aktualizacja kontenera analizy przestrzennej

Wraz z nowym zestawem funkcji [została wydana nowa](spatial-analysis-container.md) wersja kontenera analizy przestrzennej. Ten kontener platformy Docker umożliwia analizowanie wideo przesyłanego strumieniowo w czasie rzeczywistym w celu zrozumienia relacji przestrzennych między osobami i ich ruchem w środowiskach fizycznych. 

* [Operacje analizy przestrzennej](spatial-analysis-operations.md) można teraz skonfigurować do wykrywania, czy osoba ma w sobie twarze ochronne, na przykład maskę. 
    * Klasyfikator maski można włączyć dla `personcount` operacji i `personcrossingline` , `personcrossingpolygon` konfigurując `ENABLE_FACE_MASK_CLASSIFIER` parametr .
    * Atrybuty i zostaną zwrócone jako metadane z wynikiem ufności dla `face_mask` `face_noMask` każdej osoby wykrytej w strumieniu wideo
* Operacja *personcrossingpolygon* została rozszerzona, aby umożliwić obliczenie czasu dosyć spędzanego przez osobę w strefie. Możesz ustawić parametr w konfiguracji strefy dla operacji na , a nowe zdarzenie typu `type` `zonedwelltime` *personZoneDwellTimeEvent* będzie zawierać pole wypełnione liczbą milisekund, które osoba spędza w `durationMs` strefie.
* **Zmiana przełomowa:** nazwa *zdarzenia personZoneEvent* została zmieniona na *personZoneEnterExitEvent.* To zdarzenie jest wywoływane przez operację *personcrossingpolygon,* gdy osoba wchodzi do strefy lub znika z tej strefy, i udostępnia informacje kierunkowe z numerową stroną strefy, która została skrzyżowana.
* We wszystkich operacjach adres URL wideo może być podany jako "Parametr prywatny/zaciemniany". Zaciemnianie jest teraz opcjonalne i będzie działać tylko wtedy, gdy zmienne i `KEY` są dostarczane jako zmienne `IV` środowiskowe.
* Domyślnie wszystkie operacje są włączone. Ustaw wartość `do_calibration: false` , aby ją wyłączyć.
* Dodano obsługę automatycznej recalibracji (domyślnie wyłączonej) za pośrednictwem parametru. Aby uzyskać szczegółowe informacje, zobacz `enable_recalibration` [Operacje analizy przestrzennej](./spatial-analysis-operations.md)
* Parametry odwzorowania kamery na `DETECTOR_NODE_CONFIG` . Aby uzyskać szczegółowe [informacje, zobacz Operacje analizy przestrzennej.](./spatial-analysis-operations.md)


## <a name="october-2020"></a>Październik 2020 r.

### <a name="computer-vision-api-v31-ga"></a>przetwarzanie obrazów API 3.1 (wersja 3.1 ga)

Ogólnie przetwarzanie obrazów API został uaktualniony do wersji 3.1.

## <a name="september-2020"></a>Wrzesień 2020

### <a name="spatial-analysis-container-preview"></a>Wersja zapoznawcza kontenera analizy przestrzennej

Kontener [analizy przestrzennej jest](spatial-analysis-container.md) teraz w wersji zapoznawczej. Funkcja analizy przestrzennej usługi przetwarzanie obrazów umożliwia analizowanie wideo przesyłanego strumieniowo w czasie rzeczywistym w celu zrozumienia relacji przestrzennych między osobami i ich ruchem w środowiskach fizycznych. Analiza przestrzenna to kontener platformy Docker, z których można korzystać lokalnie. 

### <a name="read-api-v31-public-preview-adds-ocr-for-japanese"></a>Interfejs API odczytu w wersji 3.1 w publicznej wersji zapoznawczej dodaje OCR dla języka japońskiego
przetwarzanie obrazów interfejsu API odczytu w wersji 3.1 w publicznej wersji zapoznawczej dodaje następujące możliwości:
* OCR dla języka japońskiego
* Dla każdego wiersza tekstu wskaż, czy wygląd to Styl pisma ręcznego, czy Styl drukowania, oraz wynik ufności (tylko w językach łacińskich).
* W przypadku dokumentu wielostronicowego wyodrębnij tekst tylko dla wybranych stron lub zakresu stron.

* Ta wersja zapoznawcza interfejsu API odczytywania obsługuje języki angielski, niderlandzki, francuski, niemiecki, włoski, japoński, portugalski, chiński uproszczony i hiszpański.

Aby dowiedzieć się więcej, zobacz Przewodnik z [how-to-guide (Odczytywanie](Vision-API-How-to-Topics/call-read-api.md) interfejsu API).

> [!div class="nextstepaction"]
> [Dowiedz się więcej o interfejsie API odczytywania w wersji 3.1 w publicznej wersji zapoznawczej 2](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)

## <a name="july-2020"></a>Lipiec 2020 r.

### <a name="read-api-v31-public-preview-with-ocr-for-simplified-chinese"></a>Przeczytaj temat Interfejs API w wersji 3.1 w publicznej wersji zapoznawczej z OCR dla języka chińskiego uproszczonego
przetwarzanie obrazów w publicznej wersji zapoznawczej interfejsu API odczytywania w wersji 3.1 dodano obsługę języka chińskiego uproszczonego.

* Ta wersja zapoznawcza interfejsu API odczytywania obsługuje języki angielski, niderlandzki, francuski, niemiecki, włoski, portugalski, chiński uproszczony i hiszpański.

Aby dowiedzieć się więcej, zobacz Przewodnik po przewodniku po [interfejsie API](Vision-API-How-to-Topics/call-read-api.md) odczytywania.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o interfejsie API odczytywania w wersji 3.1 w publicznej wersji zapoznawczej 1](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)

## <a name="may-2020"></a>Maj 2020 r.
przetwarzanie obrazów API w wersji 3.0 wprowadzono ogólnie dostępną z aktualizacjami interfejsu API odczytu:

* Obsługa języka angielskiego, holenderskiego, francuskiego, niemieckiego, włoskich, portugalskich i hiszpańskich
* Ulepszona dokładność
* Ocena ufności dla każdego wyodrębnionego słowa
* Nowy format danych wyjściowych

Zobacz omówienie [OCR,](overview-ocr.md) aby dowiedzieć się więcej.

## <a name="march-2020"></a>Marzec 2020 r.

* Protokół TLS 1.2 jest teraz wymuszany dla wszystkich żądań HTTP do tej usługi. Aby uzyskać więcej informacji, zobacz [Azure Cognitive Services zabezpieczeń](../cognitive-services-security.md).

## <a name="january-2020"></a>Styczeń 2020 r.

### <a name="read-api-30-public-preview"></a>Odczytywanie publicznej wersji zapoznawczej interfejsu API 3.0

Teraz możesz użyć wersji 3.0 interfejsu API odczytywania, aby wyodrębnić z obrazów tekst drukowany lub odręczny. W porównaniu do wcześniejszych wersji wersja 3.0 zapewnia:
* Ulepszona dokładność
* Nowy format danych wyjściowych
* Ocena ufności dla każdego wyodrębnienia słowa
* Obsługa języków hiszpańskiego i angielskiego z dodatkowym parametrem języka

Postępuj zgodnie z [przewodnika Szybki start wyodrębnianie](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-hand-text.md?tabs=version-3) tekstu, aby rozpocząć korzystanie z interfejsu API w wersji 3.0.

## <a name="cognitive-service-updates"></a>Aktualizacje usługi Cognitive Service

[Ogłoszenia o aktualizacji platformy Azure dla Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
