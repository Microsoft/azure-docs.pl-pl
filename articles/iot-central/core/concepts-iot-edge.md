---
title: Azure IoT Edge i IoT Central platformy Azure | Microsoft Docs
description: Dowiedz się, jak używać Azure IoT Edge z aplikacją IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
- iot-edge
ms.openlocfilehash: 5c51649e742f6e314e0e324ae19c38f1a83b02ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90016864"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Łączenie urządzeń usługi Azure IoT Edge z aplikacją usługi Azure IoT Central

*Ten artykuł dotyczy konstruktorów rozwiązań i deweloperów urządzeń.*

IoT Edge składa się z trzech składników:

* **Moduły IoT Edge** są kontenerami, na których działają usługi platformy Azure, usługi partnerskie lub własny kod. Moduły są wdrażane na IoT Edge urządzeniach i uruchamiane lokalnie na tych urządzeniach.
* **Środowisko uruchomieniowe IoT Edge** działa na każdym urządzeniu IoT Edge i zarządza modułami wdrożonymi na poszczególnych urządzeniach.
* **Interfejs oparty na chmurze** umożliwia zdalne monitorowanie IoT Edge urządzeń i zarządzanie nimi. IoT Central jest interfejsem chmury.

Urządzenie **Azure IoT Edge** może być urządzeniem bramy z urządzeniami podrzędnymi łączącymi się z urządzeniem IoT Edge. Ten artykuł udostępnia więcej informacji na temat wzorców łączności urządzeń podrzędnych.

**Szablon urządzenia** definiuje możliwości urządzenia i moduły IoT Edge. Możliwości obejmują dane telemetryczne, które są wysyłane przez moduł, właściwości modułu i polecenia, na które odpowiada moduł.

## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Relacje urządzeń podrzędnych z bramą i modułami

Urządzenia podrzędne mogą łączyć się z urządzeniem bramy IoT Edge za pomocą `$edgeHub` modułu. To IoT Edge urządzenie zostanie w tym scenariuszu przezroczystą bramą.

![Diagram przezroczystej bramy](./media/concepts-iot-edge/gateway-transparent.png)

Urządzenia podrzędne mogą również łączyć się z urządzeniem bramy IoT Edge za pomocą modułu niestandardowego. W poniższym scenariuszu urządzenia podrzędne łączą się za pomocą modułu niestandardowego Modbus.

![Diagram połączenia modułu niestandardowego](./media/concepts-iot-edge/gateway-module.png)

Na poniższym diagramie przedstawiono połączenie z urządzeniem bramy IoT Edge za pomocą obu typów modułów (niestandardowych i `$edgeHub` ).  

![Diagram łączenia za pośrednictwem obu modułów połączeń](./media/concepts-iot-edge/gateway-module-transparent.png)

Na koniec urządzenia podrzędne mogą łączyć się z urządzeniem bramy IoT Edge przy użyciu wielu modułów niestandardowych. Na poniższym diagramie przedstawiono urządzenia podrzędne łączące się za pomocą modułu niestandardowego Modbus, modułu niestandardowego dla sieci i `$edgeHub` modułu. 

![Diagram łączenia za pośrednictwem wielu modułów niestandardowych](./media/concepts-iot-edge/gateway-module2-transparent.png)

## <a name="deployment-manifests-and-device-templates"></a>Manifesty wdrożenia i szablony urządzeń

W IoT Edge można wdrożyć logikę biznesową w formie modułów i zarządzać nią. Moduły IoT Edge są najmniejszą jednostką obliczeniową zarządzaną przez IoT Edge i mogą zawierać usługi platformy Azure (takie jak Azure Stream Analytics) lub własny kod specyficzny dla rozwiązania. Aby zrozumieć, jak moduły są opracowywane, wdrażane i utrzymywane, zobacz [IoT Edge modules](../../iot-edge/iot-edge-modules.md).

Na wysokim poziomie manifest wdrożenia jest listą bliźniaczych reprezentacji modułów, które są skonfigurowane przy użyciu odpowiednich właściwości. Manifest wdrożenia informuje urządzenie IoT Edge (lub grupę urządzeń), które moduły zainstalować, oraz sposób ich konfiguracji. Manifesty wdrożenia obejmują odpowiednie właściwości dla każdego sznurka modułu. IoT Edge urządzeń raportuje z powrotem raportowane właściwości dla każdego modułu.

Użyj Visual Studio Code, aby utworzyć manifest wdrożenia. Aby dowiedzieć się więcej, zobacz [Azure IoT Edge Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

Na platformie Azure IoT Central można zaimportować manifest wdrożenia w celu utworzenia szablonu urządzenia. Poniższy schemat blokowy przedstawia cykl życia manifestu wdrożenia w IoT Central.

![Schemat blokowy cyklu życia manifestu wdrożenia](./media/concepts-iot-edge/dmflow.png)

IoT Central modeluje urządzenie IoT Edge w następujący sposób:

* Każdy szablon urządzenia IoT Edge ma model możliwości urządzenia.
* Dla każdego modułu niestandardowego wymienionego w manifeście wdrożenia jest generowany model możliwości modułu.
* Między każdym modelem możliwości modułu a modelem możliwości urządzenia jest ustanowiona relacja.
* Model możliwości modułu implementuje interfejsy modułów.
* Każdy interfejs modułu zawiera dane telemetryczne, właściwości i polecenia.

![Diagram modelowania IoT Edge](./media/concepts-iot-edge/edgemodelling.png)

## <a name="iot-edge-gateway-devices"></a>Urządzenia bramy IoT Edge

Jeśli wybrano urządzenie IoT Edge jako urządzenie bramy, można dodać relacje podrzędne do modeli możliwości urządzeń, które mają być połączone z urządzeniem bramy.

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem urządzenia, sugerowanym następnym krokiem jest zapoznanie się z [typami urządzeń bramy w IoT Central](./tutorial-define-gateway-device-type.md).
