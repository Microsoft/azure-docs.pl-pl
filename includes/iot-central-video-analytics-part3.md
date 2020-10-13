---
title: plik dołączany
description: plik dołączany
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 60a62733a17d1a3dcc4ba80ed7ceb1c37c8ac5d6
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877206"
---
## <a name="create-the-azure-iot-edge-gateway-device"></a>Tworzenie urządzenia bramy Azure IoT Edge

Aplikacja do wykrywania filmów wideo — obiekt i ruch zawiera szablon urządzenia **LVA Edge** oraz szablon urządzenia do **wykrywania ruchu brzegowego** . W tej sekcji utworzysz szablon urządzenia bramy przy użyciu manifestu wdrażania i dodasz urządzenie bramy do aplikacji IoT Central.

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>Tworzenie szablonu urządzenia dla bramy LVA Edge

Aby zaimportować manifest wdrożenia i utworzyć szablon urządzenia **bramy LVA Edge** :

1. W aplikacji IoT Central przejdź do **szablonów urządzeń**i wybierz pozycję **+ Nowy**.

1. Na stronie **Wybieranie typu szablonu** wybierz kafelek **Azure IoT Edge** . Następnie wybierz pozycję **Dalej: Dostosuj**.

1. Na stronie **Przekaż manifest wdrożenia Azure IoT Edge** wprowadź *LVA Edge Gateway* jako nazwę szablonu i sprawdź **urządzenie bramy z urządzeniami podrzędnymi**.

    Nie wyszukuj jeszcze manifestu wdrożenia. W takim przypadku Kreator wdrażania oczekuje interfejsu dla każdego modułu, ale musisz tylko uwidocznić interfejs dla **LvaEdgeGatewayModule**. Manifest można przekazać w późniejszym kroku.

    :::image type="content" source="./media/iot-central-video-analytics-part3/upload-deployment-manifest.png" alt-text="Nie przekazuj manifestu wdrożenia":::

    Wybierz opcję **Dalej: Review** (Dalej: przegląd).

1. Na stronie **Przegląd** wybierz pozycję **Utwórz**.

### <a name="import-the-device-capability-model"></a>Importuj model możliwości urządzenia

Szablon urządzenia musi zawierać model możliwości urządzenia. Na stronie **LVA Edge Gateway** wybierz kafelek **model możliwości importowania** . Przejdź do utworzonego wcześniej folderu *LVA-Configuration* i wybierz *LvaEdgeGatewayDcm.jsw* pliku.

Szablon urządzenia **bramy LVA Edge** zawiera teraz **moduł LVA graniczny Gateway** oraz trzy interfejsy: **Informacje o urządzeniu**, **LVAe bramy graniczne**i **LVA**.
