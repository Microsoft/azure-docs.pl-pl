---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 11/09/2018
ms.author: dobett
ms.openlocfilehash: 0b478775aa572ce89f3dcdc6f28ee0418e818d99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91376290"
---
## <a name="automatic-device-management-in-azure-iot-hub"></a>Automatyczne zarządzanie urządzeniami w usłudze Azure IoT Hub
Automatyczne zarządzanie urządzeniami w systemie Azure IoT Hub automatyzuje wiele powtarzających się i złożonych zadań związanych z zarządzaniem dużymi flotami urządzeń w całym cyklu ich życia. Dzięki automatycznemu zarządzaniu urządzeniami można kierować zestawem urządzeń na podstawie ich właściwości, definiować żądaną konfigurację i zezwalać IoT Hub na aktualizowanie urządzeń, gdy zostaną one objęte zakresem.  Składa się z [automatycznych konfiguracji urządzeń](../articles/iot-hub/iot-hub-auto-device-config.md) i [IoT Edge wdrożeń automatycznych](../articles/iot-edge/how-to-deploy-at-scale.md).

## <a name="iot-edge"></a>IoT Edge
Azure IoT Edge umożliwia wdrożenie oparte na chmurze usług platformy Azure i kodu specyficznego dla rozwiązania na urządzeniach lokalnych. IoT Edge urządzenia mogą agregować dane z innych urządzeń, aby wykonywać obliczenia i analizy przed wysłaniem danych do chmury. Aby uzyskać więcej informacji, zobacz [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).

## <a name="iot-edge-agent"></a>Agent IoT Edge
Część środowiska uruchomieniowego IoT Edge odpowiedzialna za wdrażanie i monitorowanie modułów.

## <a name="iot-edge-device"></a>Urządzenie usługi IoT Edge
Na IoT Edge urządzeniach jest zainstalowane środowisko uruchomieniowe IoT Edge i są one oflagowane jako **IoT Edge urządzenia** w szczegółach urządzenia. Dowiedz się, jak [wdrażać Azure IoT Edge na symulowanym urządzeniu w systemie Linux — wersja zapoznawcza](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux).

## <a name="iot-edge-automatic-deployment"></a>IoT Edge Automatyczne wdrażanie
IoT Edge wdrożenie automatyczne konfiguruje docelowy zestaw IoT Edge urządzeń do uruchamiania zestawu modułów IoT Edge. Każde wdrożenie stale zapewnia, że wszystkie urządzenia, które pasują do warunku docelowego, uruchamiają określony zestaw modułów, nawet jeśli nowe urządzenia są tworzone lub są modyfikowane w celu dopasowania go do warunku docelowego. Każde urządzenie IoT Edge otrzymuje tylko wdrożenie o najwyższym priorytecie, którego warunek docelowy spełnia. Dowiedz się więcej o [IoT Edge wdrożeniu automatycznym](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

## <a name="iot-edge-deployment-manifest"></a>IoT Edge manifest wdrożenia
Dokument JSON zawierający informacje, które mają być skopiowane do co najmniej jednego wieloosiowego modułu IoT Edge urządzeń, aby wdrożyć zestaw modułów, tras i skojarzonych z nimi właściwości.

## <a name="iot-edge-gateway-device"></a>Urządzenie bramy IoT Edge
Urządzenie IoT Edge z urządzeniem podrzędnym. Urządzenie podrzędne może być IoT Edge lub nie IoT Edge urządzeniu.

## <a name="iot-edge-hub"></a>IoT Edge Hub
Część środowiska uruchomieniowego IoT Edge odpowiedzialna za komunikację między modułami, nadrzędną (w kierunku IoT Hub) i przejściem do trybu podrzędnego (w odniesieniu do IoT Hub). 

## <a name="iot-edge-leaf-device"></a>IoT Edge urządzenie liścia
Urządzenie IoT Edge bez urządzenia podrzędnego. 

## <a name="iot-edge-module"></a>Moduł IoT Edge
Moduł IoT Edge jest kontenerem platformy Docker, który można wdrożyć na IoT Edge urządzeniach. Wykonuje określone zadanie, takie jak pozyskiwanie komunikatu z urządzenia, przekształcanie komunikatu lub wysyłanie komunikatu do centrum IoT Hub. Komunikuje się z innymi modułami i wysyła dane do środowiska uruchomieniowego IoT Edge. [Poznaj wymagania i narzędzia służące do opracowywania modułów IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="iot-edge-module-identity"></a>IoT Edge tożsamość modułu
Rekord w rejestrze tożsamości modułu IoT Hub, w którym szczegółowo opisano istnienie i poświadczenia zabezpieczeń, które będą używane przez moduł do uwierzytelniania za pomocą koncentratora lub IoT Hub.

## <a name="iot-edge-module-image"></a>Obraz modułu IoT Edge
Obraz platformy Docker używany przez środowisko uruchomieniowe IoT Edge do tworzenia wystąpień modułów.

## <a name="iot-edge-module-twin"></a>IoT Edge dwuosiowy moduł
Dokument JSON utrwalony w IoT Hub, w którym przechowywane są informacje o stanie wystąpienia modułu.

## <a name="iot-edge-priority"></a>Priorytet IoT Edge
Gdy dwa IoT Edge wdrożenia są przeznaczone dla tego samego urządzenia, zostanie zastosowane wdrożenie z wyższym priorytetem. Jeśli dwa wdrożenia mają ten sam priorytet, zostanie zastosowane wdrożenie o późniejszej dacie utworzenia. Dowiedz się więcej o [priorytecie](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority).

## <a name="iot-edge-runtime"></a>Środowisko uruchomieniowe usługi IoT Edge
Środowisko uruchomieniowe IoT Edge obejmuje wszystkie elementy, które firma Microsoft dystrybuuje do zainstalowania na urządzeniu IoT Edge. Obejmuje ona agenta krawędzi, centrum brzegowe i demona zabezpieczeń IoT Edge.

## <a name="iot-edge-set-modules-to-a-single-device"></a>IoT Edge ustawić moduły na jedno urządzenie
Operacja, która kopiuje zawartość manifestu IoT Edge na jednej sznurze modułu urządzenia. Podstawowy interfejs API to ogólna wartość "Zastosuj konfigurację", która po prostu przyjmuje manifest IoT Edge jako dane wejściowe.

## <a name="iot-edge-target-condition"></a>Warunek docelowy IoT Edge
W przypadku wdrożenia IoT Edge warunek docelowy jest dowolnym warunkiem logicznym w tagach urządzenia bliźniaczych reprezentacji ', aby wybrać docelowe urządzenia wdrożenia, na przykład **tag. Environment = prod**. Warunek docelowy jest stale oceniany w celu uwzględnienia nowych urządzeń spełniających wymagania lub usuwania urządzeń, które nie są już wykonywane. Dowiedz się więcej o [warunku docelowym](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)