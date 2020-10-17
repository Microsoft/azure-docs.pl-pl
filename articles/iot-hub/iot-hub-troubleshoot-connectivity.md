---
title: Monitorowanie i rozwiązywanie problemów z połączeniami przy użyciu usługi Azure IoT Hub
description: Dowiedz się, jak monitorować typowe błędy i rozwiązywać problemy z łącznością z urządzeniami dla platformy Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
ms.openlocfilehash: 17fb1bf8aebe1bd114f970aed997e77ce8a07af1
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150767"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Monitorowanie, diagnozowanie i rozwiązywanie problemów z połączeniami z platformą Azure IoT Hub

Problemy z łącznością dla urządzeń IoT mogą być trudne do rozwiązania, ponieważ istnieje wiele możliwych punktów awarii. Aplikacje logiki aplikacji, sieci fizycznych, protokoły, sprzęt, IoT Hub i inne usługi w chmurze mogą spowodować problemy. Możliwość wykrywania i lokalizowania źródła problemu jest krytyczna. Jednak rozwiązanie IoT na dużą skalę może mieć tysiące urządzeń, więc nie jest praktyczne sprawdzanie poszczególnych urządzeń ręcznie. Aby ułatwić wykrywanie, diagnozowanie i rozwiązywanie problemów w odpowiedniej skali, użyj możliwości monitorowania IoT Hub zapewnia Azure Monitor. Te możliwości są ograniczone do tego, co IoT Hub może obserwować, dlatego zalecamy przestrzeganie najlepszych rozwiązań dotyczących monitorowania dla urządzeń i innych usług platformy Azure.

## <a name="get-alerts-and-error-logs"></a>Uzyskiwanie alertów i dzienników błędów

Użyj Azure Monitor, aby otrzymywać alerty i zapisywać dzienniki podczas odłączania urządzeń.

### <a name="turn-on-diagnostic-logs"></a>Włączanie dzienników diagnostycznych

Aby rejestrować zdarzenia i błędy połączeń urządzeń, Włącz diagnostykę dla IoT Hub. Zalecamy włączenie tych dzienników tak szybko, jak to możliwe, ponieważ dzienniki diagnostyczne nie są włączone, gdy nastąpi odłączenie urządzenia, nie będą dostępne żadne informacje umożliwiające rozwiązanie problemu z usługą.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Przejdź do centrum IoT Hub.

3. Wybierz pozycję **Ustawienia diagnostyki**.

4. Wybierz pozycję **Włącz diagnostykę**.

5. Włącz zbieranie dzienników **połączeń** .

6. Aby ułatwić analizę, Włącz opcję **Wyślij do log Analytics** ([Zobacz cennik](https://azure.microsoft.com/pricing/details/log-analytics/)). Zobacz przykład w obszarze [Rozwiązywanie problemów z łącznością](#resolve-connectivity-errors).

   ![Zalecane ustawienia](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Aby dowiedzieć się więcej, zobacz [monitorowanie kondycji usługi Azure IoT Hub i Szybkie diagnozowanie problemów](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-device-disconnect-at-scale"></a>Konfigurowanie alertów dotyczących rozłączenia urządzenia w dużej skali

Aby otrzymywać alerty w przypadku rozłączenia urządzeń, skonfiguruj alerty na metrykach **podłączonych urządzeń (wersja zapoznawcza)** .

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Przejdź do centrum IoT Hub.

3. Wybierz pozycję **alerty**.

4. Wybierz przycisk **Nowa reguła alertu**.

5. Wybierz pozycję **Dodaj warunek**, a następnie wybierz pozycję "połączone urządzenia (wersja zapoznawcza)".

6. Skonfiguruj próg i alerty, postępując zgodnie z poniższymi instrukcjami.

Aby dowiedzieć się więcej, zobacz [co to są alerty w Microsoft Azure?](../azure-monitor/platform/alerts-overview.md).

#### <a name="detecting-individual-device-disconnects"></a>Wykrywanie odłączeń poszczególnych urządzeń

Aby wykryć rozłączenia *dla poszczególnych urządzeń* , na przykład gdy trzeba wiedzieć, że fabryka właśnie przeszedł w tryb offline, [Skonfiguruj zdarzenia rozłączenia urządzeń za pomocą Event Grid](iot-hub-event-grid.md).

## <a name="resolve-connectivity-errors"></a>Rozwiązywanie problemów z łącznością

Po włączeniu dzienników diagnostycznych i alertów dla podłączonych urządzeń otrzymujesz alerty w przypadku wystąpienia błędów. W tej sekcji opisano, jak wyszukiwać typowe problemy występujące podczas otrzymywania alertu. W poniższych krokach przyjęto założenie, że skonfigurowano dzienniki Azure Monitor dla dzienników diagnostycznych.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Przejdź do centrum IoT Hub.

1. Wybierz pozycję **dzienniki**.

1. Aby wyizolować dzienniki błędów łączności dla IoT Hub, wprowadź następujące zapytanie, a następnie wybierz polecenie **Uruchom**:

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Jeśli istnieją wyniki, Wyszukaj `OperationName` , `ResultType` (kod błędu) i `ResultDescription` (komunikat o błędzie), aby uzyskać więcej szczegółów dotyczących błędu.

   ![Przykładowy dziennik błędów](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

1. Postępuj zgodnie z przewodnikami dotyczącymi rozwiązywania problemów w przypadku najczęstszych błędów:

    - **[404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)**
    - **[401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)**
    - **[409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)**
    - **[500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**
    - **[500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Podjęto próbę wykonania czynności, ale nie zadziałały

Jeśli poprzednie kroki nie były pomocne, spróbuj wykonać następujące czynności:

* Jeśli masz dostęp do problematycznych urządzeń, fizycznie lub zdalnie (na przykład SSH), postępuj zgodnie z [przewodnikiem rozwiązywania problemów po stronie urządzenia](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) , aby kontynuować rozwiązywanie problemów.

* Sprawdź, czy urządzenia są **włączone** w Azure Portal > urządzeniu iot Hub > IoT.

* Jeśli urządzenie korzysta z protokołu MQTT, sprawdź, czy port 8883 jest otwarty. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

* Uzyskaj pomoc od [firmy Microsoft pytań&stronie pytań dotyczących usługi azure IoT Hub](/answers/topics/azure-iot-hub.html), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)lub [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/).

Aby pomóc w ulepszaniu dokumentacji dla wszystkich użytkowników, należy pozostawić komentarz w sekcji opinii poniżej, jeśli ten przewodnik nie pomoże.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o rozwiązywaniu problemów przejściowych, zobacz [Obsługa błędów przejściowych](/azure/architecture/best-practices/transient-faults).

* Aby dowiedzieć się więcej o zestawie SDK usługi Azure IoT i zarządzaniu ponownymi próbami, zobacz [jak zarządzać łącznością i niezawodną obsługą przy użyciu zestawów SDK urządzeń IoT Hub Azure](iot-hub-reliability-features-in-sdks.md#connection-and-retry).