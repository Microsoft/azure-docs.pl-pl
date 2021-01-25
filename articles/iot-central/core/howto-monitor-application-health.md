---
title: Monitoruj kondycję aplikacji IoT Central platformy Azure | Microsoft Docs
description: Jako operator lub administrator Monitoruj ogólną kondycję urządzeń podłączonych do aplikacji IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 12485e07bc3cad9d420141ecc6ee66763ac010b7
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98760760"
---
# <a name="monitor-the-overall-health-of-the-devices-connected-to-an-iot-central-application"></a>Monitorowanie ogólnej kondycji urządzeń podłączonych do aplikacji IoT Central

> [!NOTE]
> Metryki są dostępne tylko dla aplikacji IoT Central w wersji 3. Aby dowiedzieć się, jak sprawdzić wersję aplikacji, zobacz [Informacje o aplikacji](./howto-get-app-info.md).

*Ten artykuł ma zastosowanie do operatorów i administratorów.*

W tym artykule dowiesz się, jak używać zestawu metryk dostarczonych przez IoT Central do oceny ogólnej kondycji urządzeń podłączonych do aplikacji IoT Central.

Metryki są domyślnie włączone dla aplikacji IoT Central i uzyskują do nich dostęp z [Azure Portal](https://portal.azure.com/). [Platforma danych Azure monitor udostępnia te metryki](../../azure-monitor/platform/data-platform-metrics.md) i oferuje kilka sposobów na korzystanie z nich. Na przykład można użyć wykresów w Azure Portal, interfejsie API REST lub kwerendach w programie PowerShell lub interfejsie wiersza polecenia platformy Azure.

### <a name="trial-applications"></a>Wersje próbne aplikacji

Aplikacje korzystające z planu bezpłatnej wersji próbnej nie mają skojarzonej subskrypcji platformy Azure i nie obsługują metryk Azure Monitor. Możesz [przekonwertować aplikację na standardowy plan cen](./howto-view-bill.md#move-from-free-to-standard-pricing-plan) i uzyskać dostęp do tych metryk.

## <a name="view-metrics-in-the-azure-portal"></a>Wyświetl metryki w Azure Portal

W poniższych krokach założono, że masz [aplikację IoT Central](./quick-deploy-iot-central.md) z niektórymi [połączonymi urządzeniami](./tutorial-connect-device.md).

Aby wyświetlić metryki IoT Central w portalu:

1. Przejdź do zasobu aplikacji IoT Central w portalu. Domyślnie zasoby IoT Central znajdują się w grupie zasobów o nazwie **IOTC**.
1. Aby utworzyć wykres na podstawie metryk aplikacji, wybierz pozycję **metryki** w sekcji **monitorowanie** .

![Metryki platformy Azure](media/howto-monitor-application-health/metrics.png)

### <a name="azure-portal-permissions"></a>Uprawnienia Azure Portal

Dostęp do metryk w Azure Portal jest zarządzany przez [kontrolę dostępu opartą na rolach platformy Azure](../../role-based-access-control/overview.md). Użyj Azure Portal, aby dodać użytkowników do IoT Central aplikacji/grupy zasobów/subskrypcji w celu udzielenia im dostępu. Musisz dodać użytkownika w portalu, nawet jeśli został on już dodany do aplikacji IoT Central. Użyj [wbudowanych ról platformy Azure](../../role-based-access-control/built-in-roles.md) , aby uzyskać dokładniejszą kontrolę dostępu.

## <a name="iot-central-metrics"></a>Metryki IoT Central

Listę metryk, które są obecnie dostępne dla IoT Central, można znaleźć w temacie [obsługiwane metryki z Azure monitor](../../azure-monitor/platform/metrics-supported.md#microsoftiotcentraliotapps).

### <a name="metrics-and-invoices"></a>Metryki i faktury

Metryki mogą różnić się od liczb widocznych na fakturze usługi Azure IoT Central. Ta sytuacja występuje z kilku powodów, takich jak:

- IoT Central [standardowe plany cenowe](https://azure.microsoft.com/pricing/details/iot-central/) obejmują dwa urządzenia i różne limity przydziału komunikatów za darmo. Gdy bezpłatne elementy są wykluczone z rozliczeń, są one nadal liczone w metrykach.

- IoT Central automatycznie generuje jeden identyfikator urządzenia testowego dla każdego szablonu urządzenia w aplikacji. Ten identyfikator urządzenia jest widoczny na stronie **Zarządzanie urządzeniem testowym** dla szablonu urządzenia. Konstruktorzy rozwiązań mogą zdecydować się na [zweryfikowanie swoich szablonów urządzeń](./overview-iot-central.md#create-device-templates) przed ich opublikowaniem, generując kod, który używa tych identyfikatorów urządzeń testowych. Mimo że te urządzenia są wykluczone z rozliczeń, są one nadal liczone w metrykach.

- Chociaż metryki mogą wyświetlać podzestaw komunikacji między urządzeniem i chmurą, cała komunikacja między urządzeniem a chmurą [jest traktowana jako komunikat do rozliczania](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak korzystać z szablonów aplikacji, sugerowanym następnym krokiem jest zapoznanie się z tematem jak [zarządzać IoT Central z Azure Portal](howto-manage-iot-central-from-portal.md).