---
title: plik dołączania
description: plik dołączania
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 02/20/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: f3cab4909937bbf5ccb0f72b194b08810c0487c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84792045"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Włączanie rejestrowania przy użyciu ustawień diagnostycznych

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do centrum IoT Hub.

2. Wybierz pozycję **Ustawienia diagnostyki**.

3. Wybierz pozycję **Włącz diagnostykę**.

   ![Włączanie diagnostyki](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Nadaj nazwę ustawieniom diagnostycznym.

5. Wybierz miejsce, do którego chcesz wysłać dzienniki. Można wybrać dowolną kombinację trzech opcji:

   * Zarchiwizuj na koncie magazynu
   * Przesyłaj strumieniowo do centrum zdarzeń
   * Wysyłanie do usługi Log Analytics

6. Wybierz operacje, które chcesz monitorować, i Włącz dzienniki dla tych operacji. Dla operacji, które mogą być zgłaszane przez ustawienia diagnostyczne, są następujące:

   * Połączenia
   * Dane telemetryczne urządzenia
   * Komunikaty z chmury do urządzenia
   * Operacje tożsamości urządzeń
   * Operacje przekazywania plików
   * Routing komunikatów
   * Operacje na sznurze z chmury do urządzenia
   * Operacje wieloosiowe między urządzeniami a chmurą
   * Operacje na przędzce
   * Operacje zadań
   * Metody bezpośrednie  
   * Śledzenie rozproszone (wersja zapoznawcza)
   * Konfiguracje
   * Strumienie urządzeń
   * Metryki urządzeń

6. Zapisz nowe ustawienia. 

Jeśli chcesz włączyć ustawienia diagnostyki przy użyciu programu PowerShell, użyj następującego kodu:

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Nowe ustawienia zaczną obowiązywać od około 10 minut. Następnie dzienniki są wyświetlane w skonfigurowanym miejscu docelowym w bloku **Ustawienia diagnostyki** . Aby uzyskać więcej informacji o konfigurowaniu diagnostyki, zobacz [zbieranie i korzystanie z danych dzienników z zasobów platformy Azure](../articles/azure-monitor/platform/platform-logs-overview.md).
