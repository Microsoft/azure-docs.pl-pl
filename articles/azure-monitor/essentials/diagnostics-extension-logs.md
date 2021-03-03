---
title: Korzystanie z usługi BLOB Storage na potrzeby usług IIS i magazynu tabel dla zdarzeń w Azure Monitor | Microsoft Docs
description: Azure Monitor może odczytać dzienniki usług platformy Azure, które zapisują dane diagnostyczne do magazynu tabel lub dzienników usług IIS zapisanych w usłudze BLOB Storage.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 2b7648155c28ec9b6cf2048246c17805b5f22404
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700732"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>Zbierz dane z rozszerzenia diagnostyki platformy Azure do dzienników Azure Monitor
Rozszerzenie diagnostyki Azure to [Agent w Azure monitor](../agents/agents-overview.md) , który zbiera dane monitorowania z systemu operacyjnego gościa zasobów obliczeniowych platformy Azure, w tym maszyn wirtualnych. W tym artykule opisano sposób zbierania danych zbieranych przez rozszerzenie diagnostyki z usługi Azure Storage do dzienników Azure Monitor.

> [!NOTE]
> Agent Log Analytics w Azure Monitor jest zazwyczaj preferowaną metodą zbierania danych z systemu operacyjnego gościa do dzienników Azure Monitor. Szczegółowe porównanie agentów można znaleźć w temacie [Omówienie agentów Azure monitor](../agents/agents-overview.md) .

## <a name="supported-data-types"></a>Obsługiwane typy danych
Rozszerzenie diagnostyki platformy Azure przechowuje dane na koncie usługi Azure Storage. Aby dzienniki Azure Monitor zebrać te dane, muszą one znajdować się w następujących lokalizacjach:

| Typ dziennika | Typ zasobu | Lokalizacja |
| --- | --- | --- |
| Dzienniki usług IIS |Virtual Machines <br> Role sieci Web <br> Role procesu roboczego |funkcji wad-IIS-LogFiles (Blob Storage) |
| Dziennik systemu |Virtual Machines |LinuxsyslogVer2v0 (Table Storage) |
| Service Fabric zdarzenia operacyjne |Service Fabric węzły |WADServiceFabricSystemEventTable |
| Service Fabric niezawodne zdarzenia aktora |Service Fabric węzły |WADServiceFabricReliableActorEventTable |
| Service Fabric niezawodne zdarzenia usługi |Service Fabric węzły |WADServiceFabricReliableServiceEventTable |
| Dzienniki zdarzeń systemu Windows |Service Fabric węzły <br> Virtual Machines <br> Role sieci Web <br> Role procesu roboczego |WADWindowsEventLogsTable (Table Storage) |
| Dzienniki funkcji ETW systemu Windows |Service Fabric węzły <br> Virtual Machines <br> Role sieci Web <br> Role procesu roboczego |WADETWEventTable (Table Storage) |

## <a name="data-types-not-supported"></a>Typy danych nie są obsługiwane

- Dane dotyczące wydajności z systemu operacyjnego gościa
- Dzienniki usług IIS z usługi Azure Websites


## <a name="enable-azure-diagnostics-extension"></a>Włącz rozszerzenie diagnostyki Azure
Zapoznaj się z tematem [Instalowanie i Konfigurowanie rozszerzenia Windows Azure Diagnostics (funkcji wad)](../agents/diagnostics-extension-windows-install.md) lub [rozszerzenia diagnostycznego systemu Linux w celu monitorowania metryk i dzienników](../../virtual-machines/extensions/diagnostics-linux.md) w celu uzyskania szczegółowych informacji na temat instalowania i konfigurowania rozszerzenia diagnostyki. Spowoduje to alow konta magazynu oraz skonfigurowanie kolekcji danych, które mają być przekazywane do dzienników Azure Monitor.


## <a name="collect-logs-from-azure-storage"></a>Zbieranie dzienników z usługi Azure Storage
Aby włączyć zbieranie danych rozszerzenia diagnostyki z konta usługi Azure Storage, wykonaj czynności opisane w poniższej procedurze:

1. W Azure Portal przejdź do **obszaru log Analytics obszary robocze** i wybierz swój obszar roboczy.
1. Kliknij pozycję **dzienniki kont magazynu** w sekcji **źródła danych obszaru roboczego** w menu.
2. Kliknij przycisk  **Dodaj**.
3. Wybierz **konto magazynu** zawierające dane do zebrania.
4. Wybierz **Typ danych** , który chcesz zebrać.
5. Wartość dla źródła jest automatycznie wypełniana na podstawie typu danych.
6. Kliknij przycisk **OK** , aby zapisać konfigurację.
7. Powtórz te czynności, aby uzyskać dodatkowe typy danych.

W ciągu około 30 minut można zobaczyć dane z konta magazynu w obszarze roboczym Log Analytics. Po zastosowaniu konfiguracji będą widoczne tylko dane, które są zapisywane w magazynie. Obszar roboczy nie odczytuje wcześniej istniejących danych z konta magazynu.

> [!NOTE]
> Portal nie sprawdza, czy źródło istnieje na koncie magazynu lub czy nowe dane są zapisywane.



## <a name="next-steps"></a>Następne kroki

* [Zbieraj dzienniki i metryki dla usług platformy Azure](./resource-logs.md#send-to-log-analytics-workspace) dla obsługiwanych usług platformy Azure.
* [Umożliwiaj rozwiązanie](../insights/solutions.md) w celu zapewnienia wglądu w dane.
* Przeanalizuj dane przy [użyciu zapytań wyszukiwania](../logs/log-query-overview.md) .