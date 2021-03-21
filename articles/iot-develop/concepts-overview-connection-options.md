---
title: Dowiedz się więcej o opcjach połączenia dla deweloperów urządzeń usługi Azure IoT
description: Poznaj często używane opcje połączenia z urządzeniami i narzędzia dla deweloperów urządzeń usługi Azure IoT.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 6bbd7d37418af68065daa194d4ff4bd80f6fd09c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100654368"
---
# <a name="overview-connection-options-for-azure-iot-device-developers"></a>Przegląd: opcje połączenia dla deweloperów urządzeń usługi Azure IoT
Jako deweloper, który współpracuje z urządzeniami, masz kilka możliwości łączenia urządzeń z usługą Azure IoT i zarządzania nimi. W tym artykule opisano najczęściej używane opcje i narzędzia ułatwiające Łączenie urządzeń i zarządzanie nimi.

Podczas oceniania opcji połączenia usługi Azure IoT dla urządzeń warto porównać następujące elementy:
- Platformy aplikacji urządzeń Azure IoT
- Narzędzia do łączenia urządzeń i zarządzania nimi

## <a name="application-platforms-iot-central-and-iot-hub"></a>Platformy aplikacji: IoT Central i IoT Hub
Usługa Azure IoT obejmuje dwie usługi, które są platformami dla aplikacji w chmurze z obsługą urządzeń: Azure IoT Central i Azure IoT Hub. Możesz użyć jednej z nich do hostowania aplikacji IoT i połączenia urządzeń.
- [IoT Central](../iot-central/core/overview-iot-central.md) zaprojektowano w celu zredukowania złożoności i kosztów pracy z rozwiązaniami IoT. Jest to aplikacja typu oprogramowanie jako usługa (SaaS), która zapewnia kompletną platformę do hostowania aplikacji IoT. Korzystając z IoT Central internetowego interfejsu użytkownika, można usprawnić cały cykl życia tworzenia aplikacji IoT i zarządzania nimi. Interfejs użytkownika sieci Web upraszcza zadania tworzenia aplikacji oraz łączenie i zarządzanie z kilku do milionów urządzeń. IoT Central używa IoT Hub do tworzenia aplikacji i zarządzania nimi, ale utrzymuje szczegóły widoczne dla użytkownika. Ogólnie rzecz biorąc, IoT Central zapewnia zmniejszoną złożoność i programowanie oraz uproszczone zarządzanie urządzeniami za pomocą interfejsu użytkownika sieci Web.
- [IoT Hub](../iot-hub/about-iot-hub.md) pełni rolę centralnego centrum komunikatów na potrzeby komunikacji dwukierunkowej między aplikacjami IoT i połączonymi urządzeniami. Jest to aplikacja typu "platforma jako usługa" (PaaS, Platform-as-a-Service), która udostępnia również platformę do hostowania aplikacji IoT. Podobnie jak IoT Central, można skalować w celu obsługi milionów urządzeń. Ogólnie rzecz biorąc, IoT Hub zapewnia większą kontrolę i dostosowanie w projekcie aplikacji oraz więcej opcji narzędzia deweloperskiego do pracy z usługą, kosztem pewnego wzrostu złożoności projektowania i zarządzania.

## <a name="tools-to-connect-and-manage-devices"></a>Narzędzia do łączenia urządzeń i zarządzania nimi
Po wybraniu IoT Hub lub IoT Central do hostowania aplikacji IoT masz kilka opcji narzędzi programistycznych. Za pomocą tych narzędzi można nawiązywać połączenia z wybraną platformą aplikacji IoT oraz tworzyć aplikacje i urządzenia oraz zarządzać nimi. Poniższa tabela zawiera podsumowanie typowych opcji narzędzi. 

> [!NOTE]
> Oprócz poniższych narzędzi można programowo tworzyć aplikacje IoT i zarządzać nimi za pomocą szablonów interfejsu API REST, zestawów SDK platformy Azure lub Azure Resource Manager. Więcej informacji można znaleźć w dokumentacji usługi [IoT Hub](../iot-hub/about-iot-hub.md) i [IoT Central](../iot-central/core/overview-iot-central.md) .

|Narzędzie  |Obsługuje platformę &nbsp; &nbsp; &nbsp; IoT &nbsp; |Dokumentacja  |Opis  |
|---------|---------|---------|---------|
|Centralny interfejs użytkownika sieci Web     | Centrum | [Centralny Przewodnik Szybki Start](../iot-central/core/quick-deploy-iot-central.md) | Portal oparty na przeglądarce dla IoT Central. |
|Azure Portal     | Centrum, środkowe      | [Utwórz Centrum IoT Hub za pomocą Azure Portal](../iot-hub/iot-hub-create-through-portal.md), [zarządzaj IoT Central z Azure Portal](../iot-central/core/howto-manage-iot-central-from-portal.md)| Portal oparty na przeglądarce dla IoT Hub i urządzeń. Współpracuje również z innymi zasobami platformy Azure, w tym IoT Central. |
|Interfejs wiersza polecenia platformy Azure     | Centrum, środkowe          | [Tworzenie Centrum IoT Hub przy użyciu interfejsu wiersza polecenia](../iot-hub/iot-hub-create-using-cli.md), [Zarządzanie IoT Central z poziomu interfejsu wiersza polecenia platformy Azure](../iot-central/core/howto-manage-iot-central-from-cli.md) | Interfejs wiersza polecenia służący do tworzenia aplikacji IoT i zarządzania nimi. |
|Azure PowerShell     | Centrum, środkowe   | [Utwórz Centrum IoT Hub przy użyciu programu PowerShell](../iot-hub/iot-hub-create-using-powershell.md), [zarządzaj IoT Central z Azure PowerShell](../iot-central/core/howto-manage-iot-central-from-powershell.md) | Interfejs programu PowerShell służący do tworzenia aplikacji IoT i zarządzania nimi |
|Narzędzia Azure IoT Tools dla programu VS Code  | Koncentrator | [Tworzenie Centrum IoT Hub z narzędziami dla VS Code](../iot-hub/iot-hub-create-use-iot-toolkit.md) | VS Code rozszerzenie aplikacji IoT Hub. |
|Eksplorator IoT Azure     | Koncentrator | [Eksplorator IoT Azure](https://github.com/Azure/azure-iot-explorer) | Nie można utworzyć centrów IoT. Nawiązuje połączenie z istniejącym Centrum IoT Hub w celu zarządzania urządzeniami. Często używane z interfejsem wiersza polecenia lub portalem.|

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o opcjach dotyczących łączenia urządzeń z usługą Azure IoT, zapoznaj się z następującymi przewodnikami szybki start:
- IoT Central: [Tworzenie aplikacji IoT Central platformy Azure](../iot-central/core/quick-deploy-iot-central.md)
- IoT Hub: [wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub i monitorowanie go za pomocą interfejsu wiersza polecenia platformy Azure](../iot-hub/quickstart-send-telemetry-cli.md)