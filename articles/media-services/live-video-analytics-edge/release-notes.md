---
title: Analiza filmów wideo na żywo na IoT Edge informacji o wersji — Azure
description: Ten temat zawiera informacje o wersji analizy wideo na żywo na temat wydań IoT Edge, ulepszeń, poprawek błędów i znanych problemów.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 28260728532d9db52b8d36488c2e456bd11803ea
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091783"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Analiza filmów wideo na żywo na IoT Edge informacji o wersji

>Otrzymuj powiadomienia o tym, kiedy należy ponownie odwiedzić Tę stronę pod kątem aktualizacji przez skopiowanie i wklejenie tego adresu URL: `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` do czytnika źródła danych RSS.

Ten artykuł zawiera informacje na temat:

* Najnowsze wersje
* Znane problemy
* Poprawki błędów
* Funkcje uznane za przestarzałe

## <a name="july-13-2020"></a>13 lipca 2020

Ten tag wersji dla odświeżania 2020 modułu jest następujący:

```
     mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> W ramach przewodników Szybki Start i samouczków w manifestach wdrożenia jest używany tag 1 (Live-Video-Analytics: 1). Należy więc po prostu ponownie wdrożyć takie manifesty należy zaktualizować moduł na urządzeniu brzegowym > urządzenia.

### <a name="new-features"></a>Nowe funkcje
* Teraz można tworzyć topologie grafów, które mają węzeł ujścia zasobów, a także węzeł ujścia plików, który znajduje się w węźle procesora bramy sygnalizującej. Zapoznaj [się](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files) z przykładem.

### <a name="bug-fixes"></a>Poprawki błędów
* Ulepszenia weryfikacji żądanych właściwości

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
