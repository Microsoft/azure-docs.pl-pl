---
title: Migrowanie monitorowania operacji IoT Hub platformy Azure do IoT Hub dzienników zasobów w Azure Monitor | Microsoft Docs
description: Jak aktualizować IoT Hub platformy Azure, aby używać Azure Monitor zamiast monitorowania operacji do monitorowania stanu operacji w centrum IoT w czasie rzeczywistym.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: 48b646881b12047b28490999a96326f6076af2c1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100591835"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-azure-monitor-resource-logs"></a>Migrowanie IoT Hub z monitorowania operacji do Azure Monitor dzienników zasobów

Klienci korzystający z [monitorowania operacji](iot-hub-operations-monitoring.md) do śledzenia stanu operacji w IoT Hub mogą migrować ten przepływ pracy do [Azure monitor dzienników zasobów](../azure-monitor/essentials/platform-logs-overview.md), funkcji Azure monitor. Dzienniki zasobów dostarczają informacje diagnostyczne na poziomie zasobów dla wielu usług platformy Azure.

**Funkcja monitorowania operacji IoT Hub jest przestarzała** i została usunięta z portalu. W tym artykule przedstawiono procedurę przenoszenia obciążeń z monitorowania operacji do Azure Monitor dzienników zasobów. Aby uzyskać więcej informacji na temat osi czasu wycofania, zobacz [monitorowanie rozwiązań usługi Azure IoT za pomocą Azure monitor i Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/).

## <a name="update-iot-hub"></a>IoT Hub aktualizacji

Aby zaktualizować IoT Hub w Azure Portal, najpierw Utwórz ustawienie diagnostyczne, a następnie wyłącz monitorowanie operacji.  

### <a name="create-a--diagnostic-setting"></a>Utwórz ustawienie diagnostyczne

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do centrum IoT Hub.

1. W okienku po lewej stronie w obszarze **monitorowanie** wybierz pozycję **Ustawienia diagnostyki**. Następnie wybierz pozycję **Dodaj ustawienie diagnostyczne**.

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/open-diagnostic-settings.png" alt-text="Zrzut ekranu, który wyróżnia ustawienia diagnostyczne w sekcji monitorowanie.":::

1. W okienku **Ustawienia diagnostyczne** Podaj nazwę ustawienia diagnostycznego.

1. W obszarze **szczegóły kategorii** wybierz kategorie dla operacji, które chcesz monitorować. Aby uzyskać więcej informacji na temat kategorii operacji dostępnych w IoT Hub, zobacz [dzienniki zasobów](monitor-iot-hub-reference.md#resource-logs).

1. W obszarze **szczegóły miejsca docelowego** wybierz miejsce, do którego chcesz wysłać dzienniki. Można wybrać dowolną kombinację tych miejsc docelowych:

   * Zarchiwizuj na koncie magazynu
   * Przesyłaj strumieniowo do centrum zdarzeń
   * Wysyłanie do dzienników Azure Monitor za pośrednictwem Log Analyticsego obszaru roboczego

   Poniższy zrzut ekranu przedstawia ustawienie diagnostyczne, które kieruje operacje w kategoriach połączenia i dane telemetryczne urządzenia do obszaru roboczego Log Analytics:

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/add-diagnostic-setting.png" alt-text="Zrzut ekranu przedstawiający zakończone ustawienie diagnostyczne.":::

1. Wybierz pozycję **Zapisz**, aby zapisać ustawienia.

Nowe ustawienia zaczną obowiązywać od około 10 minut. Następnie dzienniki są wyświetlane w skonfigurowanym miejscu docelowym. Aby uzyskać więcej informacji o konfigurowaniu diagnostyki, zobacz [zbieranie i korzystanie z danych dzienników z zasobów platformy Azure](../azure-monitor/essentials/platform-logs-overview.md).

Aby uzyskać szczegółowe informacje na temat sposobu tworzenia ustawień diagnostycznych, w tym programu PowerShell i interfejsu wiersza polecenia platformy Azure, zobacz [Ustawienia diagnostyczne](../azure-monitor/essentials/diagnostic-settings.md) w dokumentacji Azure monitor.

### <a name="turn-off-operations-monitoring"></a>Wyłącz monitorowanie operacji

> [!NOTE]
> Od 11 marca 2019 funkcja monitorowania operacji jest usuwana z Azure Portal interfejsu IoT Hub. Poniższe kroki nie mają już zastosowania. Aby przeprowadzić migrację, upewnij się, że odpowiednie kategorie są kierowane do lokalizacji docelowej z powyższym ustawieniem diagnostyki Azure Monitor.

Po przetestowaniu nowych ustawień diagnostycznych w przepływie pracy można wyłączyć funkcję monitorowania operacji. 

1. W menu IoT Hub wybierz pozycję **monitorowanie operacji**.

2. W obszarze każdej kategorii monitorowania zaznacz opcję **Brak**.

3. Zapisz zmiany monitorowania operacji.

## <a name="update-applications-that-use-operations-monitoring"></a>Aktualizowanie aplikacji korzystających z monitorowania operacji

Schematy monitorowania operacji i dzienników zasobów różnią się nieco. Ważne jest, aby w celu zamapowania na schemat używany przez dzienniki zasobów zaktualizować aplikacje korzystające z monitorowania operacji.

Ponadto dzienniki zasobów IoT Hub oferują pięć nowych kategorii do śledzenia. Po zaktualizowaniu aplikacji dla istniejącego schematu Dodaj również nowe kategorie:

* Operacje na sznurze z chmury do urządzenia
* Operacje wieloosiowe między urządzeniami a chmurą
* Zapytania bliźniaczye
* Operacje zadań
* Metody bezpośrednie

Aby uzyskać informacje o określonych strukturach schematu, zobacz [dzienniki zasobów](monitor-iot-hub-reference.md#resource-logs).

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Monitorowanie zdarzeń łączenia i rozłączania urządzeń z małymi opóźnieniami

Aby monitorować zdarzenia łączenia i rozłączania urządzeń w środowisku produkcyjnym, zalecamy subskrybowanie [zdarzenia **odłączenia urządzenia**](iot-hub-event-grid.md#event-types) w Event Grid w celu uzyskania alertów i monitorowania stanu połączenia urządzenia. Skorzystaj z tego [samouczka](iot-hub-how-to-order-connection-state-events.md) , aby dowiedzieć się, jak zintegrować urządzenia połączone i odłączone urządzenia z IoT Hub w rozwiązaniu IoT.

## <a name="next-steps"></a>Następne kroki

[Monitorowanie usługi IoT Hub](monitor-iot-hub.md)