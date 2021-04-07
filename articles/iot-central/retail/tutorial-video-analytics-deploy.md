---
title: 'Samouczek: Wdrażanie szablonu aplikacji IoT Central analiza filmów wideo — obiekt i ruch'
description: Samouczek — ten przewodnik zawiera podsumowanie kroków związanych z wdrażaniem aplikacji IoT Central platformy Azure przy użyciu szablonu wideo Analiza obiektów i funkcji wykrywania ruchu.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: c578da7e83a39f84e72b550038bd87dde3c61d28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727468"
---
# <a name="tutorial-how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>Samouczek: wdrażanie aplikacji IoT Central przy użyciu szablonu wideo Analiza obiektów i aplikacji wykrywanie ruchu

Aby zapoznać się z omówieniem składników aplikacji Key *Analytics — obiekty i wykrywanie ruchu* , zobacz [Architektura aplikacji Analiza wideo dotycząca obiektów i ruchu](architecture-video-analytics.md).

Poniższy film wideo zawiera Przewodnik dotyczący sposobu używania _szablonu aplikacji do analizy wideo i wykrywania ruchu_ w celu wdrożenia rozwiązania IoT Central:

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

W tym zestawie samouczków dowiesz się, jak:

> [!div class="checklist"]
> * Wdrażanie aplikacji
> * Wdróż wystąpienie IoT Edge, które nawiązuje połączenie z aplikacją
> * Monitorowanie aplikacji i zarządzanie nią

## <a name="prerequisites"></a>Wymagania wstępne

Zalecana jest subskrypcja platformy Azure. Alternatywnie możesz użyć bezpłatnej 7-dniowej wersji próbnej. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć na [stronie rejestracji na platformie Azure](https://aka.ms/createazuresubscription).

## <a name="deploy-the-application"></a>Wdrażanie aplikacji

Wykonaj następujące kroki, aby wdrożyć aplikację IoT Central przy użyciu szablonu aplikacji wideo Analytics:

1. Wypełnij instrukcje [tworzenia aplikacji do analizy filmów wideo w usłudze azure IoT Central (Yolo v3)](tutorial-video-analytics-create-app-yolo-v3.md) lub samouczka [Tworzenie wideo Analytics w usłudze Azure IoT Central (OpenVINO &trade; )](tutorial-video-analytics-create-app-openvino.md) , aby:
    - Tworzenie konta usługi Azure Media Services.
    - Utwórz aplikację IoT Central przy użyciu szablonu wideo Analiza obiektów i wykrywania ruchu.
    - Skonfiguruj urządzenie bramy w aplikacji IoT Central. Brama umożliwia urządzeniom kamer łączenie się z aplikacją.

1. Ukończ [Tworzenie wystąpienia IoT Edge na potrzeby analizy filmów wideo (maszyna wirtualna z systemem Linux)](tutorial-video-analytics-iot-edge-vm.md) lub [samouczka: Utwórz wystąpienie IoT Edge dla programu Video Analytics (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md) , aby:
    - Utwórz maszynę wirtualną platformy Azure z zainstalowanym środowiskiem uruchomieniowym Azure IoT Edge.-Przygotuj instalację IoT Edge do hostowania modułu wideo Analytics.
    - Podłącz urządzenie IoT Edge do aplikacji IoT Central.

1. Wykonaj czynności [monitorowane i Zarządzaj samouczkiem aplikacji wideo Analytics](tutorial-video-analytics-manage.md) , aby:
    - Dodaj aparaty wykrywania obiektów i ruchu do bramy w aplikacji IoT Central.
    - Rozpocznij przetwarzanie aparatu.
    - Zainstaluj lokalny odtwarzacz multimedialny, aby wyświetlić przechwycone wideo w AMS.
    - Wyświetl przechwycone wideo pokazujące wykryte obiekty.
    - Uporządkowanego.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z aplikacją można usunąć wszystkie utworzone zasoby w następujący sposób:

1. W aplikacji IoT Central przejdź do strony **aplikacji** w sekcji **Administracja** . Następnie wybierz pozycję **Usuń**.
1. W Azure Portal Usuń grupę zasobów **LVA-RG** .
1. Na maszynie lokalnej Zatrzymaj kontener platformy Docker **przeglądarki amp** .

## <a name="next-steps"></a>Następne kroki

Teraz można zapoznać się z instrukcjami wdrażania szablonu aplikacji wideo Analytics i korzystania z niego.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji do analizy wideo na platformie Azure IoT Central (Yolo v3)](tutorial-video-analytics-create-app-yolo-v3.md) lub

> [!div class="nextstepaction"]
> [Utwórz analizę wideo na platformie Azure IoT Central (OpenVINO &trade; )](tutorial-video-analytics-create-app-openvino.md) , aby rozpocząć pracę.