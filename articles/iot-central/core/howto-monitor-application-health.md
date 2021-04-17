---
title: Monitorowanie kondycji aplikacji Azure IoT Central aplikacji | Microsoft Docs
description: Jako operator lub administrator monitoruj ogólną kondycję urządzeń podłączonych do IoT Central aplikacji.
author: dominicbetts
ms.author: dobett
ms.date: 01/27/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: a9abd00035ccd779fcbe5dcf29b90f47758ff403
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588653"
---
# <a name="monitor-the-overall-health-of-an-iot-central-application"></a>Monitorowanie ogólnej kondycji aplikacji IoT Central aplikacji

> [!NOTE]
> Metryki są dostępne tylko dla aplikacji IoT Central wersji 3. Aby dowiedzieć się, jak sprawdzić wersję aplikacji, zobacz [Informacje o aplikacji.](./howto-get-app-info.md)

*Ten artykuł dotyczy operatorów i administratorów.*

W tym artykule dowiesz się, jak używać zestawu metryk dostarczonych przez usługę IoT Central do oceny kondycji urządzeń połączonych z aplikacją IoT Central oraz kondycji uruchomionych eksportów danych.

Metryki są domyślnie włączone dla aplikacji IoT Central i uzyskujesz do nich dostęp z Azure Portal [.](https://portal.azure.com/) Platforma [Azure Monitor danych uwidacznia te](../../azure-monitor/essentials/data-platform-metrics.md) metryki i udostępnia kilka sposobów interakcji z nimi. Na przykład możesz użyć wykresów w interfejsie Azure Portal, interfejsie API REST lub zapytaniach w programie PowerShell lub interfejsie wiersza polecenia platformy Azure.

### <a name="trial-applications"></a>Aplikacje w wersji próbnej

Aplikacje, które korzystają z planu bezpłatnej wersji próbnej, nie mają skojarzonej subskrypcji platformy Azure, dlatego nie obsługują metryk Azure Monitor próbnej. Możesz [przekonwertować aplikację na](./howto-view-bill.md#move-from-free-to-standard-pricing-plan) standardowy plan cenowy i uzyskać dostęp do tych metryk.

## <a name="view-metrics-in-the-azure-portal"></a>Wyświetlanie metryk w Azure Portal

W poniższych krokach założono, że [](./tutorial-connect-device.md) masz [IoT Central z](./quick-deploy-iot-central.md) niektórymi połączonymi urządzeniami lub uruchomiony [eksport danych.](howto-export-data.md)

Aby wyświetlić IoT Central metryki w portalu:

1. Przejdź do IoT Central aplikacji w portalu. Domyślnie zasoby IoT Central znajdują się w grupie zasobów o nazwie **IOTC.**
1. Aby utworzyć wykres na podstawie metryk aplikacji, wybierz pozycję **Metryki** w **sekcji** Monitorowanie.

![Metryki platformy Azure](media/howto-monitor-application-health/metrics.png)

### <a name="azure-portal-permissions"></a>Azure Portal uprawnień

Dostęp do metryk w usłudze Azure Portal jest zarządzany przez kontrolę dostępu opartą na [rolach platformy Azure.](../../role-based-access-control/overview.md) Użyj Azure Portal, aby dodać użytkowników do IoT Central aplikacji/grupy zasobów/subskrypcji, aby udzielić im dostępu. Musisz dodać użytkownika w portalu, nawet jeśli został on już dodany do IoT Central aplikacji. Użyj [wbudowanych ról platformy Azure w](../../role-based-access-control/built-in-roles.md) celu uzyskania precyzyjnej kontroli dostępu.

## <a name="iot-central-metrics"></a>IoT Central metryki

Aby uzyskać listę metryk, które są obecnie dostępne dla IoT Central, zobacz [Obsługiwane metryki](../../azure-monitor/essentials/metrics-supported.md#microsoftiotcentraliotapps)z Azure Monitor .

### <a name="metrics-and-invoices"></a>Metryki i faktury

Metryki mogą różnić się od liczb wyświetlanych na Azure IoT Central faktury. Taka sytuacja występuje z kilku powodów, takich jak:

- IoT Central standardowe [plany cenowe](https://azure.microsoft.com/pricing/details/iot-central/) obejmują bezpłatnie dwa urządzenia i różne przydziały komunikatów. Chociaż bezpłatne elementy są wykluczone z rozliczeń, nadal są one uwzględniane w metrykach.

- IoT Central automatycznie wygenerowanie jednego identyfikatora urządzenia testowego dla każdego szablonu urządzenia w aplikacji. Ten identyfikator urządzenia jest widoczny na **stronie Zarządzanie urządzeniem testowym** szablonu urządzenia. Konstruktorzy rozwiązań mogą zdecydować się na [zweryfikowanie](./overview-iot-central.md#connect-devices) szablonów urządzeń przed ich opublikowaniem, generując kod, który używa tych identyfikatorów urządzeń testowych. Chociaż te urządzenia są wykluczone z rozliczeń, nadal są uwzględniane w metrykach.

- Metryki mogą pokazywać podzbiór komunikacji między urządzeniem a chmurą, jednak cała komunikacja między urządzeniem i chmurą jest liczony jako komunikat [dla rozliczeń.](https://azure.microsoft.com/pricing/details/iot-central/)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak używać szablonów aplikacji, sugerowanym następnym krokiem jest nauczenia się, jak zarządzać IoT Central [z Azure Portal](howto-manage-iot-central-from-portal.md).