---
title: Kontrola zabezpieczeń platformy Azure — rejestrowanie i monitorowanie
description: Rejestrowanie i monitorowanie kontroli zabezpieczeń platformy Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e58df0997c62a131a3c9987369f8e076a62d6654
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408342"
---
# <a name="security-control-logging-and-monitoring"></a>Kontrola bezpieczeństwa: rejestrowanie i monitorowanie

Rejestrowanie i monitorowanie zabezpieczeń koncentruje się na działaniach związanych z włączaniem, nabywaniem i przechowywaniem dzienników inspekcji dla usług platformy Azure.

## <a name="21-use-approved-time-synchronization-sources"></a>2.1: Korzystanie z zatwierdzonych źródeł synchronizacji czasu

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

Firma Microsoft przechowuje źródła czasu dla zasobów platformy Azure, jednak masz możliwość zarządzania ustawieniami synchronizacji czasu dla zasobów obliczeniowych.

- [Jak skonfigurować synchronizację czasu dla zasobów obliczeniowych systemu Azure Windows](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)

- [Jak skonfigurować synchronizację czasu dla zasobów obliczeniowych systemu Azure Linux](https://docs.microsoft.com/azure/virtual-machines/linux/time-sync)

## <a name="22-configure-central-security-log-management"></a>2.2: Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 2.2 | 6.5, 6.6 | Klient |

Pozyskiwania dzienników za pośrednictwem usługi Azure Monitor do agregowania danych zabezpieczeń generowanych przez urządzenia końcowego, zasoby sieciowe i inne systemy zabezpieczeń. W usłudze Azure Monitor użyj obszaru roboczego analizy dzienników do wykonywania zapytań i wykonywania analiz oraz użyj kont usługi Azure Storage do długoterminowego/archiwizacji magazynu.

Alternatywnie można włączyć i na pokładzie danych do usługi Azure Sentinel lub innej firmy SIEM. 

- [Jak ować na pokładzie usługi Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Jak zbierać dzienniki i metryki platformy za pomocą usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [Jak zbierać wewnętrzne dzienniki hostów platformy Azure Virtual Machine za pomocą usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

- [Jak rozpocząć pracę z usługą Azure Monitor i integracją siem innych firm](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Włączanie rejestrowania inspekcji zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 2.3 | 6.2, 6.3 | Klient |

Włącz ustawienia diagnostyczne w zasobach platformy Azure, aby uzyskać dostęp do dzienników inspekcji, zabezpieczeń i diagnostyki. Dzienniki aktywności, które są automatycznie dostępne, obejmują źródło zdarzeń, datę, użytkownika, sygnaturę czasową, adresy źródłowe, adresy docelowe i inne przydatne elementy.

- [Jak zbierać dzienniki i metryki platformy za pomocą usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [Opis rejestrowania i różnych typów dzienników na platformie Azure](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview)

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Zbieranie dzienników zabezpieczeń z systemów operacyjnych

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 2.4 | 6.2, 6.3 | Klient |

Jeśli zasób obliczeniowy jest własnością firmy Microsoft, firma Microsoft jest odpowiedzialna za jego monitorowanie. Jeśli zasób obliczeniowy jest własnością organizacji, jest odpowiedzialny za jego monitorowanie. Za pomocą usługi Azure Security Center można monitorować system operacyjny. Dane zbierane przez Centrum zabezpieczeń z systemu operacyjnego obejmują typ i wersję systemu operacyjnego, system operacyjny (dzienniki zdarzeń systemu Windows), uruchomione procesy, nazwę komputera, adresy IP i zalogowanego użytkownika. Agent analizy dzienników zbiera również pliki zrzutu awaryjnego.

- [Jak zbierać wewnętrzne dzienniki hostów platformy Azure Virtual Machine za pomocą usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

- [Opis zbierania danych usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection)

## <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 2.5 | 6.4 | Klient |

W usłudze Azure Monitor ustaw okres przechowywania obszaru roboczego usługi Log Analytics zgodnie z przepisami dotyczącymi zgodności obowiązującymi w organizacji. Użyj kont usługi Azure Storage dla magazynu długoterminowego/archiwizacji.

- [Zmienianie okresu przechowywania danych w usłudze Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Jak skonfigurować zasady przechowywania dla dzienników kont usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

## <a name="26-monitor-and-review-logs"></a>2.6: Monitorowanie i przeglądanie dzienników

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 2,6 | 6.7 | Klient |

Analizuj i monitoruj dzienniki pod kątem nietypowego zachowania i regularnie przeglądaj wyniki. Użyj obszaru roboczego analizy dzienników usługi Azure Monitor, aby przeglądać dzienniki i wykonywać kwerendy dotyczące danych dziennika.

Alternatywnie można włączyć i na pokładzie danych do usługi Azure Sentinel lub innej firmy SIEM. 

- [Jak ować na pokładzie usługi Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Opis obszaru roboczego analizy dzienników](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

- [Jak wykonywać zapytania niestandardowe w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

## <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Włączanie alertów o nietypowych działaniach

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 2.7 | 6.8 | Klient |

Użyj usługi Azure Security Center z obszarem roboczym usługi Log Analytics do monitorowania i ostrzegania o nietypowej aktywności znalezionej w dziennikach zabezpieczeń i zdarzeniach.

Alternatywnie można włączyć i na pokładzie danych do usługi Azure Sentinel.

- [Jak ować na pokładzie usługi Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Jak zarządzać alertami w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

- [Jak ostrzegać o danych dziennika analizy dziennika](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

## <a name="28-centralize-anti-malware-logging"></a>2.8: Scentralizuj rejestrowanie przed złośliwym oprogramowaniem

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 2,8 | 8.6 | Klient |

Włącz zbieranie zdarzeń ochrony przed złośliwym oprogramowaniem dla maszyn wirtualnych platformy Azure i usług w chmurze.

- [Jak skonfigurować oprogramowanie antywirusowe Microsoft Antimalware for Virtual Machines](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0)

- [Jak skonfigurować oprogramowanie Antywirusowe dla usług w chmurze](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0)

- [Opis ochrony przed złośliwym oprogramowaniem firmy Microsoft](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

## <a name="29-enable-dns-query-logging"></a>2.9: Włączanie rejestrowania zapytań DNS

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 2.9 | 8.7 | Klient |

Zaimplementuj rozwiązanie innej firmy z usługi Azure Marketplace dla rozwiązania do rejestrowania DNS zgodnie z potrzebami organizacji.  

## <a name="210-enable-command-line-audit-logging"></a>2.10: Włącz rejestrowanie inspekcji wiersza polecenia

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 2,10 | 8.8 | Klient |

Użyj programu Microsoft Monitoring Agent na wszystkich obsługiwanych maszynach wirtualnych systemu Azure Windows, aby zarejestrować zdarzenie tworzenia procesu i pole CommandLine.   W przypadku obsługiwanych maszyn wirtualnych systemu Azure Linux można ręcznie skonfigurować rejestrowanie konsoli na podstawie węzła i używać sysloga do przechowywania danych.  Ponadto użyj obszaru roboczego analizy dzienników usługi Azure Monitor, aby przeglądać dzienniki i wykonywać kwerendy na zarejestrowanych danych z maszyn wirtualnych platformy Azure. 

- [Zbieranie danych w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

- [Jak wykonywać zapytania niestandardowe w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

- [Syslog data sources in Azure Monitor (Źródła danych usługi Syslog w usłudze Azure Monitor)](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog)


## <a name="next-steps"></a>Następne kroki

- Zobacz następną kontrolę zabezpieczeń: [kontrola tożsamości i dostępu](security-control-identity-access-control.md)