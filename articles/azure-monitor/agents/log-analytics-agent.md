---
title: Omówienie agenta Log Analytics
description: Ten temat pomaga zrozumieć, jak zbierać dane i monitorować komputery hostowane na platformie Azure, lokalnie lub w innym środowisku chmury przy użyciu Log Analytics.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/12/2021
ms.openlocfilehash: 8b3b1d39766d556b820f21c1775210611b995d92
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199902"
---
# <a name="log-analytics-agent-overview"></a>Omówienie agenta Log Analytics

Agent Log Analytics platformy Azure zbiera dane telemetryczne z maszyn wirtualnych z systemami Windows i Linux w dowolnej chmurze, maszynach lokalnych i monitorowane przez [System Center Operations Manager](/system-center/scom/) i wysyła je do obszaru roboczego Log Analytics w Azure monitor. Agent Log Analytics obsługuje także szczegółowe informacje i inne usługi w Azure Monitor, takie jak usługa [VM Insights](../vm/vminsights-enable-overview.md), [Azure Security Center](../../security-center/index.yml)i [Azure Automation](../../automation/automation-intro.md). Ten artykuł zawiera szczegółowe omówienie wymagań dotyczących agenta, systemu i sieci oraz metod wdrażania.

> [!NOTE]
> Może być również widoczny Agent Log Analytics nazywany Microsoft Monitoring Agent (MMA).

## <a name="comparison-to-azure-diagnostics-extension"></a>Porównanie z rozszerzeniem usługi Diagnostyka Azure
[Rozszerzenia usługi Azure Diagnostics](./diagnostics-extension-overview.md) w Azure monitor można także użyć do zbierania danych monitorowania z systemu operacyjnego gościa maszyn wirtualnych platformy Azure. W zależności od potrzeb można użyć lub obu tych opcji. Szczegółowe porównanie agentów Azure Monitor można znaleźć w temacie [Omówienie agentów Azure monitor](../agents/agents-overview.md) . 

Kluczowe różnice, które należy wziąć pod uwagę:

- Rozszerzenia Diagnostyka Azure można używać tylko z maszynami wirtualnymi platformy Azure. Agent Log Analytics może być używany z maszynami wirtualnymi platformy Azure, innymi chmurami i lokalnymi.
- Diagnostyka Azure rozszerzenie wysyła dane do usługi Azure Storage, [Azure monitor metryk](../essentials/data-platform-metrics.md) (tylko system Windows) i Event Hubs. Agent Log Analytics wysyła dane do [dzienników Azure monitor](../logs/data-platform-logs.md).
- Agent Log Analytics jest wymagany w przypadku [rozwiązań](../monitor-reference.md#insights-and-core-solutions), [szczegółowych informacji o maszynach wirtualnych](../vm/vminsights-overview.md)i innych usług, takich jak [Azure Security Center](../../security-center/index.yml).

## <a name="costs"></a>Koszty

Nie ma kosztu dla agenta Log Analytics, ale opłaty za dane pozyskiwane mogą być naliczane. Aby uzyskać szczegółowe informacje na temat cen zebranych w obszarze roboczym Log Analytics, zobacz [temat Zarządzanie użyciem i kosztami za pomocą dzienników Azure monitor](../logs/manage-cost-storage.md) .

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

 Zobacz [obsługiwane systemy operacyjne](../agents/agents-overview.md#supported-operating-systems) , aby uzyskać listę wersji systemu operacyjnego Windows i Linux obsługiwanych przez agenta log Analytics. 

## <a name="data-collected"></a>Zbierane dane

Poniższa tabela zawiera listę typów danych, które można skonfigurować dla obszaru roboczego Log Analytics do zebrania ze wszystkich połączonych agentów. Zobacz, [co jest monitorowane przez Azure monitor?](../monitor-reference.md) , aby zapoznać się z listą szczegółowych informacji, rozwiązań i innych rozwiązań, które używają agenta log Analytics do zbierania innych rodzajów danych.

| Źródło danych | Opis |
| --- | --- |
| [Dzienniki zdarzeń systemu Windows](../agents/data-sources-windows-events.md) | Informacje wysyłane do systemu rejestrowania zdarzeń systemu Windows. |
| [Syslog](../agents/data-sources-syslog.md)                     | Informacje wysyłane do systemu rejestrowania zdarzeń w systemie Linux. |
| [Wydajność](../agents/data-sources-performance-counters.md)  | Wartości liczbowe mierzące wydajność różnych aspektów systemu operacyjnego i obciążeń. |
| [Dzienniki usług IIS](../agents/data-sources-iis-logs.md)                 | Informacje o użyciu witryn sieci Web usług IIS działających w systemie operacyjnym gościa. |
| [Niestandardowe dzienniki](../agents/data-sources-custom-logs.md)           | Zdarzenia z plików tekstowych na komputerach z systemem Windows i Linux. |

## <a name="data-destinations"></a>Miejsca docelowe danych

Agent Log Analytics wysyła dane do Log Analytics obszaru roboczego w Azure Monitor. Agent systemu Windows może być wieloadresowy w celu wysyłania danych do wielu obszarów roboczych i System Center Operations Manager grup zarządzania. Agent systemu Linux może wysyłać tylko do jednego miejsca docelowego, obszaru roboczego lub grupy zarządzania.

## <a name="other-services"></a>Inne usługi

Agent dla systemów Linux i Windows nie tylko do łączenia się z Azure Monitor. Inne usługi, takie jak Azure Security Center i Azure, są zależne od agenta i połączonego Log Analytics obszaru roboczego. Agent obsługuje również Azure Automation do hostowania roli hybrydowego procesu roboczego elementu Runbook i innych usług, takich jak [Change Tracking](../../automation/change-tracking/overview.md), [Update Management](../../automation/update-management/overview.md)i [Azure Security Center](../../security-center/security-center-introduction.md). Aby uzyskać więcej informacji na temat roli hybrydowego procesu roboczego elementu Runbook, zobacz [Azure Automation hybrydowego procesu roboczego elementu Runbook](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="workspace-and-management-group-limitations"></a>Ograniczenia obszarów roboczych i grup zarządzania

Aby uzyskać szczegółowe informacje na temat łączenia agenta z grupą zarządzania Operations Manager, zobacz [Konfigurowanie agenta do Operations Manager grupy zarządzania](../agents/agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group) .

* Agenci systemu Windows mogą łączyć się z maksymalnie czterema obszarami roboczymi, nawet jeśli są połączeni z System Center Operations Manager grupą zarządzania.
* Agent systemu Linux nie obsługuje multihostingu i może łączyć się tylko z jednym obszarem roboczym lub grupą zarządzania.

## <a name="security-limitations"></a>Ograniczenia zabezpieczeń

* Agenci systemów Windows i Linux obsługują [Standard FIPS 140](/windows/security/threat-protection/fips-140-validation), ale [Inne typy ograniczania funkcjonalności mogą nie być obsługiwane](../agents/agent-linux.md#supported-linux-hardening).

## <a name="installation-options"></a>Opcje instalacji

Istnieje wiele metod instalacji agenta Log Analytics i podłączenia maszyny do Azure Monitor w zależności od wymagań. W poniższych sekcjach wymieniono możliwe metody dla różnych typów maszyn wirtualnych.

> [!NOTE]
> Nie jest obsługiwane klonowanie maszyny z już skonfigurowanym agentem Log Analytics. Jeśli Agent został już skojarzony z obszarem roboczym, nie będzie on działał dla "Złotej obrazów".

### <a name="azure-virtual-machine"></a>Maszyna wirtualna platformy Azure

- [Szczegółowe informacje o maszynie wirtualnej](../vm/vminsights-enable-overview.md) udostępniają wiele metod włączania agentów na dużą skalę. Obejmuje to zainstalowanie agenta Log Analytics i agenta zależności. 
- [Azure Security Center może udostępnić agentowi log Analytics](../../security-center/security-center-enable-data-collection.md) na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i nowych, które są tworzone w przypadku włączenia ich do monitorowania luk w zabezpieczeniach i zagrożeń.
- Rozszerzenie maszyny wirtualnej Log Analytics dla [systemu Windows](../../virtual-machines/extensions/oms-windows.md) lub [Linux](../../virtual-machines/extensions/oms-linux.md) można zainstalować przy użyciu szablonu Azure Portal, interfejsu wiersza polecenia platformy Azure, Azure PowerShell lub Azure Resource Manager.
- Ręcznie zainstaluj poszczególne maszyny wirtualne platformy Azure [z poziomu Azure Portal](../vm/quick-collect-azurevm.md?toc=%2fazure%2fazure-monitor%2ftoc.json).

### <a name="windows-virtual-machine-on-premises-or-in-another-cloud"></a>Maszyna wirtualna z systemem Windows w środowisku lokalnym lub w innej chmurze

- Użyj [serwerów z obsługą usługi Azure Arc](../../azure-arc/servers/overview.md) do wdrożenia rozszerzenia maszyny wirtualnej log Analytics i zarządzania nim.
- [Ręcznie zainstaluj](../agents/agent-windows.md) agenta z wiersza polecenia.
- Automatyzowanie instalacji przy użyciu [Azure Automation DSC](../agents/agent-windows.md#install-agent-using-dsc-in-azure-automation).
- Użyj [szablonu Menedżer zasobów z Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win)

### <a name="linux-virtual-machine-on-premises-or-in-another-cloud"></a>Maszyna wirtualna z systemem Linux w środowisku lokalnym lub w innej chmurze

- Użyj [serwerów z obsługą usługi Azure Arc](../../azure-arc/servers/overview.md) do wdrożenia rozszerzenia maszyny wirtualnej log Analytics i zarządzania nim.
- [Ręcznie zainstaluj](../vm/quick-collect-linux-computer.md) agenta wywołującego skrypt otoki hostowany w witrynie GitHub.
- Integruj [System Center Operations Manager](./om-agents.md) z Azure monitor do przekazywania zebranych danych z komputerów z systemem Windows do grupy zarządzania.

## <a name="workspace-id-and-key"></a>Identyfikator i klucz obszaru roboczego

Niezależnie od używanej metody instalacji należy wymagać identyfikatora i klucza obszaru roboczego dla obszaru roboczego Log Analytics, z którym będzie się łączyć Agent. Wybierz obszar roboczy z menu **log Analytics obszary robocze** w Azure Portal. Następnie wybierz pozycję **Zarządzanie agentami** w sekcji **Ustawienia** . 

[![Szczegóły obszaru roboczego](media/log-analytics-agent/workspace-details.png)](media/log-analytics-agent/workspace-details.png#lightbox)

## <a name="tls-12-protocol"></a>Protokół TLS 1,2

Aby zapewnić bezpieczeństwo danych przesyłanych do dzienników Azure Monitor, zdecydowanie zachęcamy do skonfigurowania agenta do korzystania z co najmniej Transport Layer Security (TLS) 1,2. Starsze wersje protokołu TLS/Secure Sockets Layer (SSL) są zagrożone i chociaż nadal działają tak, aby umożliwić zgodność z poprzednimi wersjami, nie są **zalecane**.  Aby uzyskać dodatkowe informacje, zapoznaj się z [bezpiecznym przesyłaniem danych przy użyciu protokołu TLS 1,2](../logs/data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-requirements"></a>Wymagania dotyczące sieci

Agent dla systemów Linux i Windows komunikuje się z usługą Azure Monitor za pośrednictwem portu TCP 443. Jeśli komputer nawiązuje połączenie za pośrednictwem zapory lub serwera proxy w celu komunikowania się za pośrednictwem Internetu, przejrzyj poniższe wymagania, aby poznać wymaganą konfigurację sieci. Jeśli zasady zabezpieczeń IT nie zezwalają komputerom w sieci na łączenie się z Internetem, można skonfigurować [bramę log Analytics](gateway.md) , a następnie skonfigurować agenta do nawiązywania połączeń za pomocą bramy, aby Azure monitor. Agent może następnie odbierać informacje o konfiguracji i wysyłać zebrane dane.

![Diagram komunikacji agenta Log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

W poniższej tabeli wymieniono informacje o konfiguracji serwera proxy i zapory wymagane dla agentów systemu Linux i Windows w celu komunikowania się z dziennikami Azure Monitor.

### <a name="firewall-requirements"></a>Wymagania dotyczące zapory

|Zasób agenta|Porty |Kierunek |Obejście inspekcji HTTPS|
|------|---------|--------|--------|
|*.ods.opinsights.azure.com |port 443 |Wychodzący|Tak |  
|*.oms.opinsights.azure.com |port 443 |Wychodzący|Tak |  
|*.blob.core.windows.net |port 443 |Wychodzący|Tak |
|*.azure-automation.net |port 443 |Wychodzący|Tak |

Informacje dotyczące zapory wymagane do Azure Government można znaleźć w temacie [Azure Government Management](../../azure-government/compare-azure-government-global-azure.md#azure-monitor). 

Jeśli planujesz używać Azure Automation hybrydowego procesu roboczego elementu Runbook do nawiązywania połączenia z usługą Automation i rejestrowania się w niej w celu używania elementów Runbook lub funkcji zarządzania w danym środowisku, musi on mieć dostęp do numeru portu i adresów URL opisanych w temacie [Konfigurowanie sieci dla hybrydowego procesu roboczego elementu Runbook](../../automation/automation-hybrid-runbook-worker.md#network-planning).

### <a name="proxy-configuration"></a>Konfiguracja serwera proxy

Agent systemów Windows i Linux obsługuje komunikację za pośrednictwem serwera proxy lub bramy Log Analytics, aby Azure Monitor przy użyciu protokołu HTTPS.  Obsługiwane są zarówno uwierzytelnianie anonimowe, jak i podstawowe (nazwa użytkownika i hasło).  W przypadku agenta systemu Windows połączonego bezpośrednio z usługą konfiguracja serwera proxy jest określana podczas instalacji lub [po wdrożeniu](../agents/agent-manage.md#update-proxy-settings) z panelu sterowania lub za pomocą programu PowerShell.  

W przypadku agenta systemu Linux serwer proxy jest określony podczas instalacji lub [po instalacji](../agents/agent-manage.md#update-proxy-settings) , modyfikując plik konfiguracji proxy. conf. Wartość konfiguracji serwera proxy agenta systemu Linux ma następującą składnię:

`[protocol://][user:password@]proxyhost[:port]`

|Właściwość| Opis |
|--------|-------------|
|Protokół | https |
|użytkownik | Opcjonalna nazwa użytkownika dla uwierzytelniania serwera proxy |
|hasło | Opcjonalne hasło do uwierzytelniania serwera proxy |
|proxyhost | Adres lub nazwa FQDN serwera proxy/bramy Log Analytics |
|port | Opcjonalny numer portu dla bramy serwera proxy/Log Analytics |

Na przykład: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Jeśli używasz znaków specjalnych, takich jak " \@ " w haśle, pojawi się komunikat o błędzie połączenia z serwerem proxy, ponieważ wartość jest analizowana nieprawidłowo.  Aby obejść ten problem, zakoduj hasło w adresie URL przy użyciu narzędzia, takiego jak [UrlDecode](https://www.urldecoder.org/).  

## <a name="next-steps"></a>Następne kroki

* Przejrzyj [źródła danych](../agents/agent-data-sources.md) , aby zrozumieć źródła danych dostępne do zbierania danych z systemu Windows lub Linux. 
* Informacje na temat [zapytań dzienników](../logs/log-query-overview.md) w celu analizowania danych zebranych ze źródeł danych i rozwiązań. 
* Dowiedz się więcej o [rozwiązaniach do monitorowania](../insights/solutions.md) , które dodają funkcje do Azure monitor, a także Zbieraj dane w obszarze roboczym log Analytics.
