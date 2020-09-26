---
title: Analiza filmów wideo na żywo na IoT Edge informacji o wersji — Azure
description: Ten temat zawiera informacje o wersji analizy wideo na żywo na temat wydań IoT Edge, ulepszeń, poprawek błędów i znanych problemów.
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: e33a62891f9503a4f2ff907585316c3737c876e6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91250474"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Analiza filmów wideo na żywo na IoT Edge informacji o wersji

>Otrzymuj powiadomienia o tym, kiedy należy ponownie odwiedzić Tę stronę pod kątem aktualizacji przez skopiowanie i wklejenie tego adresu URL: `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` do czytnika źródła danych RSS.

Ten artykuł zawiera informacje na temat:

* Najnowsze wersje
* Znane problemy
* Poprawki błędów
* Funkcje uznane za przestarzałe

<hr width=100%>

## <a name="september-22-2020"></a>22 września, 2020

Ten tag wersji dla odświeżania z września 2020 dla modułu to:

```
mcr.microsoft.com/media/live-video-analytics:1.0.4
```

> [!NOTE]
> W ramach przewodników Szybki Start i samouczków w manifestach wdrożenia jest używany tag 1 (Live-Video-Analytics: 1). Należy więc po prostu ponownie wdrożyć takie manifesty należy zaktualizować moduł na urządzeniu brzegowym > urządzenia.

### <a name="module-updates"></a>Aktualizacje modułu

* Nowy węzeł rozszerzenia grafu, [MediaGraphCognitiveServicesVisionExtension](spatial-analysis-tutorial.md) jest dostępny do integracji z modułem [analizy przestrzennej](https://docs.microsoft.com/legal/cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview)(wersja zapoznawcza) z Cognitive Services.
* Dodano obsługę urządzeń z systemem Linux ARM64 — należy użyć [ręcznych kroków](deploy-iot-edge-device.md) do wdrożenia na takich urządzeniach.

### <a name="documentation-updates"></a>Aktualizacje dokumentacji

* Dostępne są [instrukcje](deploy-azure-stack-edge-how-to.md) dotyczące korzystania z usługi Analiza filmów wideo na żywo na IoT Edge na urządzeniach brzegowych Azure Stack.
* Nowy samouczek dotyczący projektowania specyficznych dla scenariusza modeli przetwarzania obrazów przy użyciu [usługi Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) i używania go do [analizowania wideo na żywo](custom-vision-tutorial.md) w czasie rzeczywistym.

<hr width=100%>

## <a name="august-19-2020"></a>19 sierpnia 2020

Ten tag wersji dla odświeżenia 2020 sierpnia modułu to:

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> W ramach przewodników Szybki Start i samouczków w manifestach wdrożenia jest używany tag 1 (Live-Video-Analytics: 1). Należy więc po prostu ponownie wdrożyć takie manifesty należy zaktualizować moduł na urządzeniu brzegowym > urządzenia.

### <a name="module-updates"></a>Aktualizacje modułu

* Teraz można uzyskać wysoką wydajność transferu zawartości danych między aktywną analizą filmów wideo na IoT Edge a niestandardowym rozszerzeniem przy użyciu platformy gRPC Framework. Zobacz [ten](analyze-live-video-use-your-grpc-model-quickstart.md) temat, aby rozpocząć pracę.
* Szersze wdrożenie regionalne analizy filmów wideo i aktualizowanie tylko usługi w chmurze.  
* Usługa Analiza filmów wideo na żywo jest teraz dostępna w 25 dodatkowych regionach na całym świecie. Poniżej znajduje się [Lista](https://azure.microsoft.com/global-infrastructure/services/?products=media-services) wszystkich dostępnych regionów.  
* [Konfiguracja](https://aka.ms/lva-edge/setup-resources-for-samples) przewodnika Szybki Start została zaktualizowana wraz z obsługą nowych regionów.
    * Brak wywołania akcji dla każdej osoby, która już skonfigurował zasoby

### <a name="bug-fixes"></a>Poprawki błędów 

* Usuń użycie przestarzałego rozszerzenia platformy Azure w skrypcie konfigurowania

<hr width=100%>

## <a name="july-13-2020"></a>13 lipca 2020

Ten tag wersji dla odświeżania 2020 modułu jest następujący:

```
mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> W ramach przewodników Szybki Start i samouczków w manifestach wdrożenia jest używany tag 1 (Live-Video-Analytics: 1). Należy więc po prostu ponownie wdrożyć takie manifesty należy zaktualizować moduł na urządzeniu brzegowym > urządzenia.

### <a name="module-updates"></a>Aktualizacje modułu

* Teraz można tworzyć topologie grafów, które mają węzeł ujścia zasobów, a także węzeł ujścia plików, który znajduje się w węźle procesora bramy sygnalizującej. Zapoznaj [się](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files) z przykładem.

### <a name="bug-fixes"></a>Poprawki błędów

* Ulepszenia weryfikacji żądanych właściwości

<hr width=100%>

## <a name="june-1-2020"></a>1 czerwca 2020 r.

Ta wersja to pierwsza publiczna wersja zapoznawcza usługi Live Video Analytics na IoT Edge. Tag wersji to

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Obsługiwane funkcje

* Analizuj strumienie wideo na żywo przy użyciu wybranych modułów AI i opcjonalnie nagrywaj wideo na urządzeniu brzegowym lub w chmurze
* Używany w systemach operacyjnych Linux AMD64 [obsługiwanych](../../iot-edge/support.md) przez IoT Edge
* Wdróż i skonfiguruj moduł za pośrednictwem IoT Hub przy użyciu Azure Portal lub Visual Studio Code
* Zdalne lub lokalne zarządzanie [topologiami grafu i wystąpieniami grafu](media-graph-concept.md#media-graph-topologies-and-instances) za pośrednictwem następujących wywołań metody bezpośredniej

    *   GraphTopologyGet
    *   GraphTopologySet
    *   GraphTopologyDelete
    *   GraphTopologyList
    *   GraphInstanceGet
    *   GraphInstanceSet
    *   GraphInstanceDelete
    *   GraphInstanceList

## <a name="next-steps"></a>Następne kroki

[Omówienie](overview.md)
