---
title: Analiza filmów wideo na żywo na IoT Edge informacji o wersji — Azure
description: Ten temat zawiera informacje o wersji analizy wideo na żywo na temat wydań IoT Edge, ulepszeń, poprawek błędów i znanych problemów.
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 328fe97c4e03f039a1224d13ce6712ccff06b3b7
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629780"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Analiza filmów wideo na żywo na IoT Edge informacji o wersji

>Otrzymuj powiadomienia o tym, kiedy należy ponownie odwiedzić Tę stronę pod kątem aktualizacji przez skopiowanie i wklejenie tego adresu URL: `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` do czytnika źródła danych RSS.

Ten artykuł zawiera informacje na temat:

* Najnowsze wersje
* Znane problemy
* Poprawki błędów
* Funkcje uznane za przestarzałe

<hr width=100%>

## <a name="january-12-2021"></a>12 stycznia 2021 r.

Ten tag wersji jest przeznaczony do odświeżania w styczniu 2021 dla modułu:

```
mcr.microsoft.com/media/live-video-analytics:2.0.1
```

> [!NOTE]
> W ramach przewodników Szybki Start i samouczków manifesty wdrożenia używają znacznika 2 (Live-Video-Analytics: 2). Należy więc po prostu ponownie wdrożyć takie manifesty należy zaktualizować moduł na urządzeniu brzegowym > urządzenia.
### <a name="bug-fixes"></a>Poprawki błędów 

* Pola `ActivationSignalOffset` `MinimumActivationTime` i `MaximumActivationTime` w procesorach bramy sygnałów zostały nieprawidłowo ustawione jako wymagane właściwości. Są teraz właściwościami **opcjonalnymi** .
* Naprawiono usterkę dotyczącą użycia, która powoduje awarię modułu IoT Edge analizy wideo na żywo w przypadku wdrożenia w określonych regionach.

<hr width=100%>

## <a name="december-14-2020"></a>14 grudnia 2020
Ta wersja jest publiczną wersją zapoznawczą w wersji zapoznawczej analizy wideo na żywo na IoT Edge. Tag wersji to

```
     mcr.microsoft.com/media/live-video-analytics:2.0.0
```
### <a name="module-updates"></a>Aktualizacje modułu
* Dodano obsługę używania więcej niż jednego procesora rozszerzeń HTTP i procesora rozszerzenia gRPC na topologię grafu.
* Dodano obsługę zarządzania miejscem na dysku dla węzłów ujścia.
* `MediaGraphGrpcExtension` węzeł obsługuje teraz Właściwość [extensionConfiguration](grpc-extension-protocol.md) na potrzeby używania wielu modeli AI w ramach jednego serwera gRPC.
* Dodano obsługę zbierania metryk modułu analizy wideo na żywo w [formacie Prometheus](https://prometheus.io/docs/practices/naming/). Dowiedz się więcej o tym [, jak zbierać metryki i wyświetlać dane w Azure monitor.](monitoring-logging.md#azure-monitor-collection-via-telegraf) 
* Dodano możliwość filtrowania wyboru danych wyjściowych. Można przekazać **tylko dźwięk** lub **tylko wideo** lub **audio i wideo** , korzystając z pomocy programu `outputSelectors` do dowolnego węzła grafu. 
* Procesor filtru szybkości klatek jest **przestarzały**.  
    * Zarządzanie szybkością klatek jest teraz dostępne w ramach węzłów procesora rozszerzenia grafu.

### <a name="visual-studio-code-extension"></a>Rozszerzenie programu Visual Studio Code
* Udostępnienie usługi [Live Video Analytics na IoT Edge](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge) — Visual Studio Code rozszerzenie, które ułatwia zarządzanie wykresami multimediów LVA. To rozszerzenie współdziała z **modułem LVA 2,0** i oferuje możliwość edytowania wykresów multimedialnych i zarządzania nimi za pomocą bardzo eleganckiego i łatwego w użyciu interfejsu graficznego.
## <a name="september-22-2020"></a>22 września, 2020

Ten tag wersji jest przeznaczony do odświeżania 2020 września modułu:

```
mcr.microsoft.com/media/live-video-analytics:1.0.4
```

> [!NOTE]
> W ramach przewodników Szybki Start i samouczków w manifestach wdrożenia jest używany tag 1 (Live-Video-Analytics: 1). Należy więc po prostu ponownie wdrożyć takie manifesty należy zaktualizować moduł na urządzeniu brzegowym > urządzenia.

### <a name="module-updates"></a>Aktualizacje modułu

* Nowy węzeł rozszerzenia grafu, [MediaGraphCognitiveServicesVisionExtension](spatial-analysis-tutorial.md) jest dostępny do integracji z modułem [analizy przestrzennej](/legal/cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview)(wersja zapoznawcza) z Cognitive Services.
* Dodano obsługę urządzeń z systemem Linux ARM64 — należy użyć [ręcznych kroków](deploy-iot-edge-device.md) do wdrożenia na takich urządzeniach.

### <a name="documentation-updates"></a>Aktualizacje dokumentacji

* Dostępne są [instrukcje](deploy-azure-stack-edge-how-to.md) dotyczące korzystania z usługi Analiza filmów wideo na żywo na IoT Edge na urządzeniach brzegowych Azure Stack.
* Nowy samouczek dotyczący projektowania specyficznych dla scenariusza modeli przetwarzania obrazów przy użyciu [usługi Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) i używania go do [analizowania wideo na żywo](custom-vision-tutorial.md) w czasie rzeczywistym.

<hr width=100%>

## <a name="august-19-2020"></a>19 sierpnia 2020

Ten tag wersji jest przeznaczony do odświeżania 2020 sierpnia modułu:

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

Ten tag wersji jest przeznaczony do odświeżania 2020 lipca modułu:

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
