---
title: Spis rozwiązań monitorowania na platformie Azure | Microsoft Docs
description: Monitorowanie rozwiązań w Azure Monitor to zbiór reguł logiki, wizualizacji i pozyskiwania danych, które zapewniają metryki przestawiane wokół określonego obszaru problemu.  Ten artykuł zawiera listę rozwiązań monitorowania dostępnych od firmy Microsoft i szczegółowych informacji o ich metodzie i częstotliwości zbierania danych.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/26/2018
ms.openlocfilehash: b87179c79489bf781619b70b19ca8982f2e38dff
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/24/2020
ms.locfileid: "83816815"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Szczegóły dotyczące spisu i zbierania danych na potrzeby monitorowania rozwiązań na platformie Azure
[Rozwiązania do monitorowania](solutions.md) wykorzystują usługi na platformie Azure do uzyskiwania informacji o działaniu konkretnej aplikacji lub usługi. Monitorowanie rozwiązań zazwyczaj zbiera dane dzienników i udostępnia zapytania i widoki umożliwiające analizowanie zebranych danych. Możesz dodać rozwiązania do monitorowania do Azure Monitor dla wszystkich używanych aplikacji i usług. Są one zazwyczaj dostępne bezpłatnie, ale zbierają dane, które mogą wywołać opłaty za użycie.

Ten artykuł zawiera listę [rozwiązań monitorowania](solutions.md) dostępnych od firmy Microsoft z linkami do ich szczegółowej dokumentacji.  Zawiera również informacje na temat metody i częstotliwości zbierania danych w Azure Monitor.  Korzystając z informacji w tym artykule, można zidentyfikować różne dostępne rozwiązania oraz poznać wymagania dotyczące przepływu danych i połączenia dla różnych rozwiązań monitorowania.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>Lista rozwiązań do monitorowania

Poniższa tabela zawiera listę [rozwiązań monitorowania](solutions.md) na platformie Azure udostępnianych przez firmę Microsoft. Wpis w kolumnie oznacza, że rozwiązanie zbiera dane do Azure Monitor za pomocą tej metody.  Jeśli rozwiązanie nie ma wybranych kolumn, zapisuje je bezpośrednio do Azure Monitor z innej usługi platformy Azure. Aby uzyskać więcej informacji, Skorzystaj z linku dla każdej z nich do szczegółowej dokumentacji.

Wyjaśnienia kolumn są następujące:

- **Program Microsoft Monitoring Agent** — Agent używany w systemach Windows i Linux do uruchamiania pakietu zarządzania z programu Microsoft System Center — Operations Manager (om) i monitorowania rozwiązań z platformy Azure. W tej konfiguracji Agent jest połączony bezpośrednio z Azure Monitor bez połączenia z Operations Manager grupą zarządzania. 
- **Operations Manager** — identyczny Agent jako program Microsoft Monitoring Agent. W tej konfiguracji jest ona [połączona z Operations Manager grupą zarządzania](../platform/om-agents.md) , która jest połączona z Azure monitor. 
-  **Azure Storage** — rozwiązanie zbiera dane z konta usługi Azure Storage. 
- **Operations Manager jest wymagane?** -Połączona Grupa zarządzania Operations Manager jest wymagana do zbierania danych przez rozwiązanie do monitorowania. 
- **Operations Manager dane agenta wysyłane za pośrednictwem grupy zarządzania** — Jeśli Agent jest [połączony z grupą zarządzania OM](../platform/om-agents.md), dane są wysyłane do Azure monitor z serwera zarządzania. W takim przypadku Agent nie musi łączyć się bezpośrednio z Azure Monitor. Jeśli to pole nie zostanie zaznaczone, dane są wysyłane bezpośrednio z agenta do Azure Monitor nawet wtedy, gdy Agent jest połączony z grupą zarządzania OM. Musi mieć możliwość komunikowania się z Azure Monitor za pomocą [bramy log Analytics](../platform/gateway.md).
- **Częstotliwość zbierania** — określa częstotliwość, z jaką dane są zbierane przez rozwiązanie do monitorowania. 



| **Rozwiązanie do monitorowania** | **Platforma** | **Program Microsoft Monitoring Agent** | **Agent programu Operations Manager** | **Azure Storage** | **Operations Manager jest wymagane?** | **Dane agenta Operations Manager wysyłane przez grupę zarządzania** | **Częstotliwość zbierania** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Analiza dzienników aktywności](../platform/activity-log-collect.md) | Azure | | | | | | przy powiadomień |
| [Ocena usługi AD](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dni |
| [Stan replikacji usługi AD](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 dni |
| [Kondycja agenta](solution-agenthealth.md) | System Windows i Linux | &#8226; | &#8226; | | | &#8226; | 1 minuta |
| [Alert Management](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |przy nadejściu |
| [Alert Management](../platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 minuta |
| [Alert Management](../platform/alert-management-solution.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 minuty |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | nie dotyczy |
| [Application Insights Connector (przestarzałe)](../platform/app-insights-connector.md) | Azure | | | |  |  | przy powiadomień |
| [Automation Hybrid Worker](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | nie dotyczy |
| [Azure Application Gateway Analytics](azure-networking-analytics.md) | Azure |  |  |  |  |  | przy powiadomień |
| **Rozwiązanie do monitorowania** | **Platforma** | **Program Microsoft Monitoring Agent** | **Agent programu Operations Manager** | **Azure Storage** | **Operations Manager jest wymagane?** | **Dane agenta Operations Manager wysyłane przez grupę zarządzania** | **Częstotliwość zbierania** |
| [Azure Network Security Group Analytics (przestarzałe)](azure-networking-analytics.md) | Azure |  |  |  |  |  | przy powiadomień |
| [Azure SQL Analytics (wersja zapoznawcza)](azure-sql.md) | Windows | | | | | | 1 minuta |
| [Backup](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | przy powiadomień |
| [Capacity and Performance (wersja zapoznawcza)](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |przy nadejściu |
| [Change Tracking](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[różni się](../../automation/change-tracking.md#change-tracking-and-inventory-data-collection) |
| [Change Tracking](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[różni się](../../automation/change-tracking.md#change-tracking-and-inventory-data-collection) |
| [Containers](containers.md) | System Windows i Linux | &#8226; | &#8226; |  |  |  | 3 minuty |
| [Key Vault Analytics](azure-key-vault.md) |Windows | | | | | |przy powiadomień |
| [Ocena złośliwego oprogramowania](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |co godzinę |
| [Monitor wydajności sieci](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | Uzgadnianie TCP co 5 sekund, dane wysyłane co 3 minuty |
| [Office 365 Analytics (wersja zapoznawcza)](solution-office-365.md) |Windows | | | | | |przy powiadomień |
| **Rozwiązanie do monitorowania** | **Platforma** | **Program Microsoft Monitoring Agent** | **Agent programu Operations Manager** | **Azure Storage** | **Operations Manager jest wymagane?** | **Dane agenta Operations Manager wysyłane przez grupę zarządzania** | **Częstotliwość zbierania** |
| [Analiza usługi Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 minut |
| [Mapa usługi](service-map.md) | System Windows i Linux | &#8226; | &#8226; |  |  |  | 15 sekund |
| [Ocena serwera SQL](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dni |
| [SurfaceHub](surface-hubs.md) |Windows |&#8226; | | | | |przy nadejściu |
| [System Center Operations Manager Assessment (wersja zapoznawcza)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | siedem dni |
| [Zarządzanie aktualizacjami](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |co najmniej dwa razy dziennie i 15 minut po zainstalowaniu aktualizacji |
| [Gotowość do uaktualnienia](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 dni |
| [VMware Monitoring (przestarzałe)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 minuty |
| [Wire Data 2.0 (wersja zapoznawcza)](wire-data.md) |Windows (2012 R2/8,1 lub nowszy) |&#8226; |&#8226; | | | | 1 minuta |




## <a name="next-steps"></a>Następne kroki
* Dowiedz się [, jak instalować i używać rozwiązań do monitorowania](solutions.md).
* Dowiedz się, jak [tworzyć zapytania](../log-query/log-query-overview.md) do analizowania danych zbieranych przez rozwiązania monitorujące.
