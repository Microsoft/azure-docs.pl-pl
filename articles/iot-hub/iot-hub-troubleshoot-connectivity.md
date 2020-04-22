---
title: Monitorowanie i rozwiązywanie problemów z rozłączaniami za pomocą usługi Azure IoT Hub
description: Dowiedz się, jak monitorować typowe błędy i rozwiązywać problemy z łącznością urządzenia dla usługi Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: 82139eef9708ff8d76e1087c71aa5445ba898385
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759611"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Monitorowanie, diagnozowanie i rozwiązywanie problemów z rozłączaniami za pomocą usługi Azure IoT Hub

Problemy z łącznością dla urządzeń IoT mogą być trudne do rozwiązania, ponieważ istnieje wiele możliwych punktów awarii. Logika aplikacji, sieci fizyczne, protokoły, sprzęt, Centrum IoT i inne usługi w chmurze mogą powodować problemy. Zdolność do wykrywania i wskazywanie źródła problemu jest krytyczna. Jednak rozwiązanie IoT na dużą skalę może mieć tysiące urządzeń, więc nie jest praktyczne, aby sprawdzić poszczególne urządzenia ręcznie. Aby ułatwić wykrywanie, diagnozowanie i rozwiązywanie tych problemów na dużą skalę, należy użyć możliwości monitorowania, które usługa IoT Hub zapewnia za pośrednictwem usługi Azure Monitor. Te możliwości są ograniczone do tego, co usługa IoT Hub może obserwować, dlatego zalecamy również przestrzeganie najlepszych rozwiązań dotyczących monitorowania urządzeń i innych usług platformy Azure.

## <a name="get-alerts-and-error-logs"></a>Otrzymuj alerty i dzienniki błędów

Użyj usługi Azure Monitor, aby otrzymywać alerty i dzienniki zapisu po odłączeniu urządzeń.

### <a name="turn-on-diagnostic-logs"></a>Włączanie dzienników diagnostycznych

Aby rejestrować zdarzenia i błędy połączenia urządzenia, włącz diagnostykę usługi IoT Hub. Zaleca się włączenie tych dzienników tak wcześnie, jak to możliwe, ponieważ jeśli dzienniki diagnostyczne nie są włączone, gdy wystąpią rozłączenia urządzenia, nie będziesz mieć żadnych informacji, aby rozwiązać problem z.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Przejdź do centrum IoT Hub.

3. Wybierz **ustawienia diagnostyki**.

4. Wybierz **włącz diagnostykę**.

5. Włącz **dzienniki połączeń** do zbierania.

6. Aby ułatwić analizę, włącz **funkcję Wyślij do usługi Log Analytics** [(patrz cennik).](https://azure.microsoft.com/pricing/details/log-analytics/) Zobacz przykład w obszarze [Rozwiązywanie błędów łączności](#resolve-connectivity-errors).

   ![Zalecane ustawienia](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Aby dowiedzieć się więcej, zobacz [Monitorowanie kondycji usługi Azure IoT Hub i szybkie diagnozowanie problemów.](iot-hub-monitor-resource-health.md)

### <a name="set-up-alerts-for-device-disconnect-at-scale"></a>Konfigurowanie alertów dotyczących rozłączania urządzenia na dużą skalę

Aby otrzymywać alerty po odłączeniu urządzeń, skonfiguruj alerty na **metryce Podłączone urządzenia (podgląd).**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Przejdź do centrum IoT Hub.

3. Wybierz **pozycję Alerty**.

4. Wybierz przycisk **Nowa reguła alertu**.

5. Wybierz **pozycję Dodaj warunek**, a następnie wybierz "Podłączone urządzenia (podgląd)".

6. Ustaw próg i alerty, wykonując monity.

Aby dowiedzieć się więcej, zobacz [Co to są alerty na platformie Microsoft Azure?](../azure-monitor/platform/alerts-overview.md).

#### <a name="detecting-individual-device-disconnects"></a>Wykrywanie odłączania poszczególnych urządzeń

Aby wykryć rozłączenia *na urządzenie,* na przykład gdy musisz wiedzieć, że fabryka po prostu przeszła w tryb offline, [skonfiguruj zdarzenia rozłączania urządzenia za pomocą usługi Event Grid](iot-hub-event-grid.md).

## <a name="resolve-connectivity-errors"></a>Rozwiązywanie problemów z łącznością

Po włączeniu dzienników diagnostycznych i alertów dla podłączonych urządzeń, są otrzymywać alerty, gdy wystąpią błędy. W tej sekcji opisano, jak szukać typowych problemów po otrzymaniu alertu. W poniższych krokach założono, że skonfigurowałeś dzienniki usługi Azure Monitor dla dzienników diagnostycznych.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Przejdź do centrum IoT Hub.

1. Wybierz **dzienniki**.

1. Aby wyizolować dzienniki błędów łączności dla Usługi IoT Hub, wprowadź następującą kwerendę, a następnie wybierz pozycję **Uruchom:**

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Jeśli są wyniki, `OperationName`poszukaj ,,(kod `ResultType` błędu) i `ResultDescription` (komunikat o błędzie), aby uzyskać więcej szczegółów na temat błędu.

   ![Przykład dziennika błędów](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

1. Postępuj zgodnie z przewodnikami rozwiązywania problemów, aby uzyskać najczęstsze błędy:

    - **[404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)**
    - **[401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)**
    - **[409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)**
    - **[500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**
    - **[500008 Ogólny Limit czasu](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Próbowałem kroki, ale nie działa

Jeśli poprzednie kroki nie pomogły, spróbuj:

* Jeśli masz dostęp do problematycznych urządzeń, fizycznie lub zdalnie (takich jak SSH), postępuj zgodnie z [przewodnikiem rozwiązywania problemów po stronie urządzenia,](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) aby kontynuować rozwiązywanie problemów.

* Sprawdź, czy twoje urządzenia są **włączone** w witrynie Azure portal > urządzeniach IoT hub > IoT.

* Jeśli urządzenie korzysta z protokołu MQTT, sprawdź, czy port 8883 jest otwarty. Aby uzyskać więcej informacji, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

* Uzyskaj pomoc z [forum usługi Azure IoT Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [przepełnienie stosu](https://stackoverflow.com/questions/tagged/azure-iot-hub)lub [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/).

Aby ulepszyć dokumentację dla wszystkich, zostaw komentarz w sekcji opinii poniżej, jeśli ten przewodnik ci nie pomógł.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat rozwiązywania problemów przejściowych, zobacz [Obsługa błędów przejściowych](/azure/architecture/best-practices/transient-faults).

* Aby dowiedzieć się więcej o zestawie SDK usługi Azure IoT i zarządzaniu ponownymi próbami, zobacz [Jak zarządzać łącznością i niezawodnymi wiadomościami przy użyciu zestawów SDK urządzeń usługi Azure IoT Hub.](iot-hub-reliability-features-in-sdks.md#connection-and-retry)
