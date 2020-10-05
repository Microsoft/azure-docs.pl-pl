---
title: Wykrywanie ruchu & nagrywanie wideo na urządzeniach brzegowych — Azure
description: W tym przewodniku szybki start przedstawiono sposób korzystania z usługi Analiza filmów wideo na żywo na IoT Edge do analizowania strumieniowego wideo na żywo z (symulowanej) kamery IP, wykrywania ewentualnych ruchów i w razie potrzeby rejestrowania klipu wideo MP4 w lokalnym systemie plików na urządzeniu brzegowym.
ms.topic: quickstart
ms.date: 04/27/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: e456bb5b8d6d6658158af0c58f05ab38fe4693b8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88682342"
---
# <a name="quickstart-detect-motion-and-record-video-on-edge-devices"></a>Szybki Start: wykrywanie ruchu i nagrywanie filmów wideo na urządzeniach brzegowych
 
W tym przewodniku szybki start przedstawiono sposób korzystania z usługi Analiza filmów wideo na żywo na IoT Edge do analizowania strumieniowego kanału informacyjnego wideo z (symulowanej) kamery IP. Pokazano, jak wykryć, czy występują jakieś ruchy, a jeśli tak, Zapisz klip wideo MP4 w lokalnym systemie plików na urządzeniu brzegowym. Przewodnik Szybki Start używa maszyny wirtualnej platformy Azure jako urządzenia IoT Edge, a także używa symulowanego strumienia wideo na żywo. 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Wymagania wstępne

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Zapoznaj się z przykładowym wideo

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Omówienie

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/overview.md)]
::: zone-end

## <a name="examine-and-edit-the-sample-files"></a>Sprawdzanie i edytowanie plików przykładowych

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-edit-sample-files](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/examine-edit-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-edit-sample-files](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/examine-edit-sample-files.md)]
::: zone-end

## <a name="review---check-the-modules-status"></a>Przegląd — Sprawdź stan modułów

::: zone pivot="programming-language-csharp"
[!INCLUDE [check-modules-status](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/check-modules-status.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [check-modules-status](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/check-modules-status.md)]
::: zone-end

## <a name="review---prepare-for-monitoring-events"></a>Przegląd — przygotowanie do monitorowania zdarzeń

::: zone pivot="programming-language-csharp"
[!INCLUDE [prepare-monitoring-events](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/prepare-monitoring-events.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prepare-monitoring-events](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/prepare-monitoring-events.md)]
::: zone-end

## <a name="run-the-sample-program"></a>Uruchamianie przykładowego programu

::: zone pivot="programming-language-csharp"
[!INCLUDE [run-sample-program](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/run-sample-program.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [run-sample-program](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/run-sample-program.md)]
::: zone-end

## <a name="interpret-results"></a>Interpretowanie wyników 

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="play-the-mp4-clip"></a>Odtwórz klip MP4

::: zone pivot="programming-language-csharp"
[!INCLUDE [play-mp4-clip](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/play-mp4-clip.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [play-mp4-clip](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/play-mp4-clip.md)]
::: zone-end

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz wypróbować inne Przewodniki Szybki Start, Zachowaj utworzone zasoby. W przeciwnym razie w Azure Portal przejdź do grup zasobów, wybierz grupę zasobów, w której uruchomiono ten przewodnik Szybki Start, a następnie usuń wszystkie zasoby.

## <a name="next-steps"></a>Następne kroki

* Postępuj zgodnie z instrukcjami [uruchamiania usługi Live Video Analytics przy użyciu własnego modelu](use-your-model-quickstart.md) — Szybki Start, aby zastosować AI do kanałów wideo na żywo.
* Zapoznaj się z dodatkowymi wyzwaniami dla zaawansowanych użytkowników:

    * Użyj [kamery IP](https://en.wikipedia.org/wiki/IP_camera) , która obsługuje protokół RTSP zamiast korzystania z symulatora RTSP. Kamery IP obsługujące protokół RTSP można znaleźć na stronie [ONVIF zgodne produkty](https://www.onvif.org/conformant-products) . Wyszukaj urządzenia zgodne z profilami G, S lub T.
    * Użyj urządzenia z systemem AMD64 lub x64 zamiast korzystać z maszyny wirtualnej z systemem Linux na platformie Azure. To urządzenie musi znajdować się w tej samej sieci co kamera IP. Postępuj zgodnie z instrukcjami w temacie [Install Azure IoT Edge Runtime on Linux](../../iot-edge/how-to-install-iot-edge-linux.md). Następnie postępuj zgodnie z instrukcjami w temacie [wdrażanie pierwszego modułu IoT Edge na wirtualnym urządzeniu z systemem Linux](../../iot-edge/quickstart-linux.md) w celu zarejestrowania urządzenia w usłudze Azure IoT Hub.
