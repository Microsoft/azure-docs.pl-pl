---
title: Usługa Azure IoT Edge i usługa Azure IoT Central | Dokumenty firmy Microsoft
description: Dowiedz się, jak korzystać z usługi Azure IoT Edge za pomocą aplikacji IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 89d068fbd835a5b4f8886a2ed77691f94a701bb0
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759102"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Łączenie urządzeń usługi Azure IoT Edge z aplikacją Azure IoT Central

*Ten artykuł dotyczy konstruktorów rozwiązań i deweloperów urządzeń.*

IoT Edge składa się z trzech komponentów:

* **Moduły usługi IoT Edge to kontenery,** które uruchamiają usługi platformy Azure, usługi partnerskie lub własny kod. Moduły są wdrażane na urządzeniach usługi IoT Edge i uruchamiane lokalnie na tych urządzeniach.
* **Środowisko uruchomieniowe IoT Edge** działa na każdym urządzeniu IoT Edge i zarządza modułami wdrożonymi na każdym urządzeniu.
* **Interfejs oparty na chmurze** umożliwia zdalne monitorowanie urządzeń usługi IoT Edge i zarządzanie nimi. IoT Central to interfejs chmury.

Urządzenie **usługi Azure IoT Edge** może być urządzeniem bramy, z urządzeniami podrzędnymi łączącymi się z urządzeniem usługi IoT Edge. W tym artykule udostępnia więcej informacji na temat wzorców łączności urządzenia podrzędnego.

**Szablon urządzenia** definiuje możliwości urządzenia i modułów IoT Edge. Możliwości obejmują dane telemetryczne, które wysyła moduł, właściwości modułu i polecenia, na które odpowiada moduł.

## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Relacje urządzeń podrzędnych z bramą i modułami

Urządzenia podrzędne mogą łączyć się z urządzeniem bramy usługi IoT Edge za pośrednictwem modułu. `$edgeHub` To urządzenie usługi IoT Edge staje się przezroczystą bramą w tym scenariuszu.

![Diagram przezroczystej bramy](./media/concepts-iot-edge/gateway-transparent.png)

Urządzenia podrzędne mogą również łączyć się z urządzeniem bramy usługi IoT Edge za pośrednictwem modułu niestandardowego. W poniższym scenariuszu urządzenia podrzędne łączą się za pośrednictwem modułu niestandardowego Modbus.

![Diagram niestandardowego połączenia modułu](./media/concepts-iot-edge/gateway-module.png)

Na poniższym diagramie przedstawiono połączenie z urządzeniem bramy usługi `$edgeHub`IoT Edge za pośrednictwem obu typów modułów (niestandardowych i ).  

![Schemat łączenia za pośrednictwem obu modułów połączeń](./media/concepts-iot-edge/gateway-module-transparent.png)

Na koniec urządzenia podrzędne mogą łączyć się z urządzeniem bramy usługi IoT Edge za pośrednictwem wielu modułów niestandardowych. Na poniższym diagramie przedstawiono urządzenia podrzędne łączące się za `$edgeHub` pośrednictwem modułu niestandardowego Modbus, modułu niestandardowego BLE i modułu. 

![Diagram łączenia za pomocą wielu modułów niestandardowych](./media/concepts-iot-edge/gateway-module2-transparent.png)

## <a name="deployment-manifests-and-device-templates"></a>Manifesty wdrażania i szablony urządzeń

W umyw ciągłej rzeczywistości można wdrażać logikę biznesową i zarządzać nią w postaci modułów. Moduły usługi IoT Edge są najmniejszą jednostką obliczeń zarządzaną przez usługę IoT Edge i mogą zawierać usługi platformy Azure (takie jak usługa Azure Stream Analytics) lub własny kod specyficzny dla rozwiązania. Aby zrozumieć, w jaki sposób moduły są opracowywane, wdrażane i utrzymywane, zobacz [moduły IoT Edge](../../iot-edge/iot-edge-modules.md).

Na wysokim poziomie manifest wdrożenia jest lista bliźniacze modułu, które są skonfigurowane z ich żądanych właściwości. Manifest wdrażania informuje urządzenie usługi IoT Edge (lub grupę urządzeń), które moduły do zainstalowania i jak je skonfigurować. Manifesty wdrażania zawierają żądane właściwości dla każdej bliźniaczej reprezentacji modułu. Urządzenia IoT Edge zgłaszają zgłoszone właściwości dla każdego modułu.

Użyj programu Visual Studio Code, aby utworzyć manifest wdrożenia. Aby dowiedzieć się więcej, zobacz [Usługa Azure IoT Edge for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

W usłudze Azure IoT Central można zaimportować manifest wdrożenia w celu utworzenia szablonu urządzenia. Poniższy schemat blokowy przedstawia cykl życia manifestu wdrażania w centrum IoT.

![Schemat blokowy cyklu życia manifestu wdrażania](./media/concepts-iot-edge/dmflow.png)

IoT Plug and Play (wersja zapoznawcza) modeluje urządzenie IoT Edge w następujący sposób:

* Każdy szablon urządzenia IoT Edge ma model możliwości urządzenia.
* Dla każdego modułu niestandardowego wymienionego w manifeście wdrażania generowany jest model możliwości modułu.
* Nawiązywany jest związek między każdym modelem możliwości modułu a modelem możliwości urządzenia.
* Model możliwości modułu implementuje interfejsy modułów.
* Każdy interfejs modułu zawiera dane telemetryczne, właściwości i polecenia.

![Diagram modelowania IoT Edge](./media/concepts-iot-edge/edgemodelling.png)

## <a name="iot-edge-gateway-devices"></a>Urządzenia bramy usługi IoT Edge

Jeśli wybrano urządzenie usługi IoT Edge jako urządzenie bramy, można dodać relacje podrzędne do modeli możliwości urządzenia dla urządzeń, które chcesz połączyć się z urządzeniem bramy.

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem urządzeń, sugerowanym następnym krokiem jest zapoznanie się z [typami urządzeń bramy w aplikacji IoT Central.](./tutorial-define-gateway-device-type.md)
