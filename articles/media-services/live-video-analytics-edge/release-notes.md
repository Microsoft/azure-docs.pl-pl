---
title: Analiza filmów wideo na żywo na IoT Edge informacji o wersji — Azure
description: Ten temat zawiera informacje o wersji analizy wideo na żywo na temat wydań IoT Edge, ulepszeń, poprawek błędów i znanych problemów.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: fd607ee027b46cf386180338440e6530da911302
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261037"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Analiza filmów wideo na żywo na IoT Edge informacji o wersji

>Otrzymuj powiadomienia o tym, kiedy należy ponownie odwiedzić Tę stronę pod kątem aktualizacji przez skopiowanie i wklejenie tego adresu URL: `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` do czytnika źródła danych RSS.

Ten artykuł zawiera informacje na temat:

* Najnowsze wersje
* Znane problemy
* Poprawki błędów
* Funkcje uznane za przestarzałe

## <a name="june-1-2020"></a>1 czerwca 2020

Ta wersja to pierwsza publiczna wersja zapoznawcza usługi Live Video Analytics na IoT Edge. Tag wersji to

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Obsługiwane funkcje
* Analizuj strumienie wideo na żywo przy użyciu wybranych modułów AI i opcjonalnie nagrywaj wideo na urządzeniu brzegowym lub w chmurze
* Używany w systemach operacyjnych Linux AMD64 [obsługiwanych](https://docs.microsoft.com/azure/iot-edge/support) przez IoT Edge
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
