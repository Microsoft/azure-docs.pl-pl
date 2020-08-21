---
title: Omówienie agenta Azure Monitor
description: Przegląd Azure Monitor Agent (AMA), który gromadzi dane monitorowania z systemu operacyjnego gościa maszyn wirtualnych.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: ff70beef89f6db240db244de1e11e54193858be0
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705779"
---
# <a name="azure-monitor-agent-overview-preview"></a>Omówienie agenta Azure Monitor (wersja zapoznawcza)
Agent Azure Monitor (AMA) zbiera dane monitorowania z systemu operacyjnego gościa maszyn wirtualnych i dostarcza go do Azure Monitor. Ten artykuł zawiera omówienie agenta Azure Monitor, w tym sposobu instalowania go i konfigurowania zbierania danych.

## <a name="relationship-to-other-agents"></a>Relacja z innymi agentami
Agent Azure Monitor zastępuje następujących agentów, które są obecnie używane przez Azure Monitor do zbierania danych Gościa z maszyn wirtualnych:

- [Agent log Analytics](log-analytics-agent.md) — wysyła dane do log Analytics obszaru roboczego i obsługuje rozwiązania Azure monitor dla maszyn wirtualnych i monitorowania.
- [Rozszerzenie diagnostyczne](diagnostics-extension-overview.md) — wysyła dane do metryk Azure monitor (tylko system Windows), Azure Event Hubs i Azure Storage.
- [Telegraf Agent](collect-custom-metrics-linux-telegraf.md) — wysyła dane do metryk Azure monitor (tylko system Linux).

Oprócz konsolidacji tej funkcji w ramach jednego agenta Agent Azure Monitor zapewnia następujące korzyści w stosunku do istniejących agentów:

- Zakres monitorowania. Centralna konfiguracja kolekcji dla różnych zestawów danych z różnych zestawów maszyn wirtualnych.  
- Linux multihostingu: wysyłanie danych z maszyn wirtualnych systemu Linux do wielu obszarów roboczych.
- Filtrowanie zdarzeń systemu Windows: Użyj zapytań XPATH do filtrowania, które zdarzenia systemu Windows są zbierane.
- Ulepszone Zarządzanie rozszerzeniami: Agent Azure Monitor korzysta z nowej metody obsługi rozszerzalności, która jest bardziej przejrzysta i kontrolowanych niż pakiety administracyjne i wtyczki systemu Linux w bieżącym agencie Log Analytics.

### <a name="changes-in-data-collection"></a>Zmiany w zbieraniu danych
Metody definiowania zbierania danych dla istniejących agentów różnią się od siebie nawzajem, a każdy z nich ma wyzwania, które są rozłączone z agentem Azure Monitor.

- Agent Log Analytics pobiera swoją konfigurację z Log Analytics obszaru roboczego. Jest to łatwe do scentralizowanego konfigurowania, ale trudno definiować definicje niezależne dla różnych maszyn wirtualnych. Dane można wysyłać tylko do obszaru roboczego Log Analytics.

- Rozszerzenie diagnostyczne ma konfigurację dla każdej maszyny wirtualnej. Jest to łatwe definiowanie niezależnych definicji dla różnych maszyn wirtualnych, ale trudno jest centralnie zarządzać. Może on wysyłać tylko dane do Azure Monitor metryk, Azure Event Hubs lub Azure Storage. W przypadku agentów systemu Linux Agent telegraf Open Source jest wymagany do wysyłania danych do metryk Azure Monitor.

Agent Azure Monitor używa [reguł zbierania danych (DCR)](data-collection-rule-overview.md) do konfigurowania danych zbieranych z poszczególnych agentów. Reguły zbierania danych umożliwiają zarządzanie ustawieniami kolekcji na dużą skalę, jednocześnie umożliwiając jednocześnie korzystanie z unikatowych, w zakresie konfiguracji dla podzestawów maszyn. Są one niezależne od obszaru roboczego i niezależne od maszyny wirtualnej, dzięki czemu można je definiować raz i ponownie używać między maszynami i środowiskami. Zobacz [Konfigurowanie zbierania danych dla agenta Azure monitor (wersja zapoznawcza)](data-collection-rule-azure-monitor-agent.md).



## <a name="current-limitations"></a>Bieżące ograniczenia
W publicznej wersji zapoznawczej agenta Azure Monitor są stosowane następujące ograniczenia:

- Agent Azure Monitor nie obsługuje rozwiązań i szczegółowych informacji, takich jak Azure Monitor dla maszyn wirtualnych i Azure Security Center. Jedynym scenariuszem obsługiwanym obecnie jest zbieranie danych przy użyciu skonfigurowanych reguł zbierania danych. 
- Reguły zbierania danych muszą zostać utworzone w tym samym regionie, w którym znajduje się obszar roboczy Log Analytics używany jako miejsce docelowe.
- Obecnie są obsługiwane tylko maszyny wirtualne platformy Azure. Lokalne maszyny wirtualne, zestawy skalowania maszyn wirtualnych, usługi ARC dla serwerów, usługa Azure Kubernetes i inne typy zasobów obliczeniowych nie są obecnie obsługiwane.
- Maszyna wirtualna musi mieć dostęp do następujących punktów końcowych HTTPS:
  - *.ods.opinsights.azure.com
  - *. ingest.monitor.azure.com
  - *. control.monitor.azure.com


## <a name="coexistence-with-other-agents"></a>Współistnienie z innymi agentami
Agent Azure Monitor może współistnieć z istniejącymi agentami, aby można było nadal korzystać z istniejących funkcji podczas oceny lub migracji. Jest to szczególnie ważne ze względu na ograniczenia w publicznej wersji zapoznawczej w obsłudze istniejących rozwiązań. Należy zachować ostrożność podczas zbierania zduplikowanych danych, ponieważ może to spowodować pochylenie wyników zapytania i pozyskanie dodatkowych opłat za pozyskiwanie i przechowywanie danych.

Na przykład Azure Monitor dla maszyn wirtualnych używa agenta Log Analytics do wysyłania danych wydajności do obszaru roboczego Log Analytics. Możesz również skonfigurować obszar roboczy do zbierania zdarzeń systemu Windows i zdarzeń dziennika systemowego z agentów. W przypadku zainstalowania agenta Azure Monitor i utworzenia reguły zbierania danych dla tych samych zdarzeń i danych wydajności spowoduje to zduplikowanie danych.


## <a name="costs"></a>Koszty
Nie ma kosztu dla agenta Azure Monitor, ale opłaty za dane pozyskiwane mogą być naliczane. Zobacz [cennik Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) , aby uzyskać szczegółowe informacje na temat log Analytics zbierania i przechowywania danych oraz metryki klientów.

## <a name="data-sources-and-destinations"></a>Źródła danych i miejsca docelowe
Poniższa tabela zawiera listę typów danych, które można obecnie zbierać z agentem Azure Monitor przy użyciu reguł zbierania danych i skąd można wysłać te dane. Zobacz, [co jest monitorowane przez Azure monitor?](../monitor-reference.md) , aby zapoznać się z listą szczegółowych informacji, rozwiązań i innych rozwiązań, które używają agenta Azure monitor do zbierania innych rodzajów danych.


Agent Azure Monitor wysyła dane do metryk Azure Monitor lub obszaru roboczego Log Analytics obsługi dzienników Azure Monitor.

| Źródło danych | Miejsca docelowe | Opis |
|:---|:---|:---|
| Wydajność        | Metryki Azure Monitor<br>Obszar roboczy usługi Log Analytics | Wartości liczbowe mierzące wydajność różnych aspektów systemu operacyjnego i obciążeń. |
| Dzienniki zdarzeń systemu Windows | Obszar roboczy usługi Log Analytics | Informacje wysyłane do systemu rejestrowania zdarzeń systemu Windows. |
| Dziennik systemu             | Obszar roboczy usługi Log Analytics | Informacje wysyłane do systemu rejestrowania zdarzeń w systemie Linux. |


## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Następujące systemy operacyjne są obecnie obsługiwane przez agenta Azure Monitor.

### <a name="windows"></a>Windows 
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012
  - Windows Server 2012 z dodatkiem R2

### <a name="linux"></a>Linux
  - CentOS 6<sup>,</sup>7
  - Debian 9, 10
  - Oracle Linux 6<sup>1</sup>, 7
  - RHEL 6<sup>1</sup>, 7, 8
  - SLES 11, 12, 15
  - Ubuntu 14,04 LTS, 16,04 LTS, 18,04 LTS

> [!IMPORTANT]
> <sup>1</sup> W przypadku tych dystrybucji do wysyłania danych dziennika systemowego należy usunąć rsyslog i zainstalować dziennik systemowy — ng.


## <a name="security"></a>Zabezpieczenia
Agent Azure Monitor nie wymaga żadnych kluczy, ale wymaga [tożsamości zarządzanej przypisanej do systemu](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity). Przed wdrożeniem agenta na każdej maszynie wirtualnej musi być włączona tożsamość zarządzana przypisana przez system.


## <a name="install-the-azure-monitor-agent"></a>Zainstaluj agenta Azure Monitor
Agent Azure Monitor jest zaimplementowany jako [rozszerzenie maszyny wirtualnej platformy Azure](../../virtual-machines/extensions/overview.md) ze szczegółami podanymi w poniższej tabeli. 

| Właściwość | Windows | Linux |
|:---|:---|:---|
| Publisher | Microsoft. Azure. Monitor  | Microsoft. Azure. Monitor |
| Typ      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1.5 |

Zainstaluj agenta Azure Monitor przy użyciu dowolnej z metod instalacji agentów maszyny wirtualnej, w tym następujących elementów przy użyciu programu PowerShell lub interfejsu wiersza polecenia. Alternatywnie można zainstalować agenta i skonfigurować zbieranie danych na maszynach wirtualnych w ramach subskrypcji platformy Azure za pomocą portalu z procedurą opisaną w temacie [Konfigurowanie zbierania danych dla agenta Azure monitor (wersja zapoznawcza)](data-collection-rule-azure-monitor-agent.md#create-using-the-azure-portal).

### <a name="windows"></a>Windows

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI1)

```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids {resource ID of the VM}

```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell1)

```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---


### <a name="linux"></a>Linux

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI2)

```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids {resource ID of the VM}

```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell2)

```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---

## <a name="next-steps"></a>Następne kroki

- [Utwórz regułę zbierania danych](data-collection-rule-azure-monitor-agent.md) , aby zbierać dane z agenta i wysyłać je do Azure monitor.
