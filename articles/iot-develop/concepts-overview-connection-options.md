---
title: Dowiedz się więcej o opcjach połączenia dla deweloperów urządzeń Azure IoT
description: Dowiedz się więcej o najczęściej używanych opcjach i narzędziach połączenia urządzenia dla deweloperów urządzeń Usługi Azure IoT.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 8669919192b1e6394043842d7d23f8829ec7c71e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589554"
---
# <a name="overview-connection-options-for-azure-iot-device-developers"></a>Omówienie: opcje połączenia dla deweloperów urządzeń Azure IoT
Jako deweloper, który pracuje z urządzeniami, masz kilka opcji łączenia urządzeń Azure IoT i zarządzania nimi. Ten artykuł zawiera omówienie najczęściej używanych opcji i narzędzi, które ułatwiają łączenie urządzeń i zarządzanie nimi.

Podczas oceniania opcji połączenia usługi Azure IoT dla urządzeń warto porównać następujące elementy:
- Platformy aplikacji urządzeń Azure IoT
- Narzędzia do łączenia urządzeń i zarządzania nimi

## <a name="application-platforms-iot-central-and-iot-hub"></a>Platformy aplikacji: IoT Central i IoT Hub
Usługa Azure IoT zawiera dwie usługi, które są platformami dla aplikacji w chmurze z obsługą urządzeń: Azure IoT Central i Azure IoT Hub. Możesz użyć jednej z nich do hostować aplikację IoT i łączyć urządzenia.
- [IoT Central](../iot-central/core/overview-iot-central.md) została zaprojektowana w celu zmniejszenia złożoności i kosztów pracy z rozwiązaniami IoT. Jest to aplikacja typu oprogramowanie jako usługa (SaaS), która zapewnia kompletną platformę do hostowania aplikacji IoT. Za pomocą interfejsu IoT Central można usprawnić cały cykl życia tworzenia aplikacji IoT i zarządzania nimi. Internetowy interfejs użytkownika upraszcza zadania tworzenia aplikacji oraz łączenia się i zarządzania od kilku do milionów urządzeń. IoT Central używa IoT Hub do tworzenia aplikacji i zarządzania nimi, ale zapewnia, że szczegóły są niewidoczne dla użytkownika. Ogólnie rzecz biorąc, IoT Central złożoność i nakład pracy programistyki oraz uproszczone zarządzanie urządzeniami za pośrednictwem internetowego interfejsu użytkownika.
- [IoT Hub](../iot-hub/about-iot-hub.md) pełni rolę centrum komunikatów dla dwukierunkowej komunikacji między aplikacjami IoT i połączonymi urządzeniami. Jest to aplikacja typu platforma jako usługa (PaaS), która zapewnia również platformę do hostowania aplikacji IoT. Podobnie IoT Central może być skalowana w celu obsługi milionów urządzeń. Ogólnie rzecz biorąc, IoT Hub zapewnia większą kontrolę i dostosowywanie projektu aplikacji oraz więcej opcji narzędzi dewelopera do pracy z usługą, kosztem pewnego wzrostu złożoności tworzenia i zarządzania.

## <a name="tools-to-connect-and-manage-devices"></a>Narzędzia do łączenia urządzeń i zarządzania nimi
Po wybraniu IoT Hub lub IoT Central do hostowania aplikacji IoT dostępnych jest kilka opcji narzędzi deweloperskie. Za pomocą tych narzędzi można łączyć się z wybraną platformą aplikacji IoT oraz tworzyć aplikacje i urządzenia oraz zarządzać nimi. W poniższej tabeli przedstawiono podsumowanie typowych opcji narzędzi. 

> [!NOTE]
> Oprócz następujących narzędzi można programowo tworzyć aplikacje IoT i zarządzać nimi przy użyciu interfejsu API REST, zestawów Azure SDK lub szablonów Azure Resource Manager REST. Więcej informacji można znaleźć w [dokumentacji IoT Hub](../iot-hub/about-iot-hub.md) [i IoT Central](../iot-central/core/overview-iot-central.md) service.

|Narzędzie  |Obsługuje platformę IoT &nbsp; &nbsp; &nbsp;&nbsp; |Dokumentacja  |Opis  |
|---------|---------|---------|---------|
|Centralny internetowy interfejs użytkownika     | Centrum | [Centralny przewodnik Szybki start](../iot-central/core/quick-deploy-iot-central.md) | Portal oparty na przeglądarce dla IoT Central. |
|Azure Portal     | Centrum, Środkowe      | [Tworzenie centrum IoT hub za pomocą Azure Portal](../iot-hub/iot-hub-create-through-portal.md), zarządzanie IoT Central z [Azure Portal](../iot-central/core/howto-manage-iot-central-from-portal.md)| Portal oparty na przeglądarce dla IoT Hub i urządzeń. Współpracuje również z innymi zasobami platformy Azure, w tym IoT Central. |
|Azure IoT Explorer     | Koncentrator | [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer#azure-iot-explorer-preview) | Nie można utworzyć centrów IoT. Nawiązuje połączenie z istniejącym centrum IoT w celu zarządzania urządzeniami. Często używane z interfejsem wiersza polecenia lub portalem.|
|Interfejs wiersza polecenia platformy Azure     | Hub, Środkowe          | [Tworzenie centrum IoT hub przy użyciu interfejsu wiersza polecenia,](../iot-hub/iot-hub-create-using-cli.md) [zarządzanie IoT Central z interfejsu wiersza polecenia platformy Azure](../iot-central/core/howto-manage-iot-central-from-cli.md) | Interfejs wiersza polecenia do tworzenia aplikacji IoT i zarządzania nimi. |
|Azure PowerShell     | Hub, Środkowe   | [Tworzenie centrum IoT Hub przy użyciu programu PowerShell,](../iot-hub/iot-hub-create-using-powershell.md) [zarządzanie IoT Central z Azure PowerShell](../iot-central/core/howto-manage-iot-central-from-powershell.md) | Interfejs programu PowerShell do tworzenia aplikacji IoT i zarządzania nimi |
|Narzędzia Azure IoT Tools dla programu VS Code  | Koncentrator | [Tworzenie centrum IoT Hub za pomocą narzędzi Tools for VS Code](../iot-hub/iot-hub-create-use-iot-toolkit.md) | VS Code dla IoT Hub aplikacji. |

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o opcjach łączenia urządzeń z usługą Azure IoT, zapoznaj się z następującymi przewodnikami Szybki start:
- IoT Central: Tworzenie [Azure IoT Central aplikacji](../iot-central/core/quick-deploy-iot-central.md)
- IoT Hub: wysyłanie [danych telemetrycznych z urządzenia do centrum IoT Hub](../iot-hub/quickstart-send-telemetry-cli.md) i monitorowanie ich za pomocą interfejsu wiersza polecenia platformy Azure