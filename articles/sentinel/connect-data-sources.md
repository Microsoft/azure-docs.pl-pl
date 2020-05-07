---
title: Łączenie źródeł danych z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak łączyć źródła danych z danymi wskaźnikowymi platformy Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: angrobe
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: yelevin
ms.openlocfilehash: 5cb1f699f070bbb0c37d4b6adf5e919c7ec5d6cd
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82591979"
---
# <a name="connect-data-sources"></a>Łączenie ze źródłami danych

Aby przejść do tablicy wskaźnikowej platformy Azure, musisz najpierw nawiązać połączenie ze źródłami danych. Wskaźnik platformy Azure obejmuje wiele łączników dla rozwiązań firmy Microsoft, dostępnych poza platformą i zapewniania integracji w czasie rzeczywistym, w tym rozwiązań firmy Microsoft do ochrony przed zagrożeniami, a także źródeł Microsoft 365, takich jak Office 365, Azure AD, Azure ATP i Microsoft Cloud App Security. Ponadto istnieją wbudowane łączniki do szerszego ekosystemu zabezpieczeń dla rozwiązań firm innych niż Microsoft. Możesz również użyć typowego formatu zdarzeń, dziennika systemowego lub REST-API, aby połączyć źródła danych z systemem Azure.  

1. Z menu wybierz pozycję **Łączniki danych**. Ta strona umożliwia wyświetlenie pełnej listy łączników udostępnianych przez wskaźnik platformy Azure oraz ich stan. Wybierz łącznik, który chcesz połączyć, a następnie wybierz pozycję **Otwórz stronę łącznika**. 

   ![Moduły zbierające dane](./media/collect-data/collect-data-page.png)

1. Na określonej stronie łącznika upewnij się, że spełniono wszystkie wymagania wstępne, i postępuj zgodnie z instrukcjami, aby połączyć dane z platformą Azure wskaźnikiem. Synchronizowanie dzienników z platformą Azure jest możliwe dopiero po pewnym czasie. Po nawiązaniu połączenia zobaczysz podsumowanie danych w grafie **otrzymane dane** oraz stan łączności typów danych.

   ![Połącz moduły zbierające](./media/collect-data/opened-connector-page.png)
  
1. Kliknij kartę **następne kroki** , aby uzyskać listę zawartości wbudowanej platformy Azure, która zapewnia dla określonego typu danych.

   ![Moduły zbierające dane](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Metody połączenia danych

Następujące metody łączenia danych są obsługiwane przez wskaźnik na platformie Azure:

- **Integracja między usługą a usługą**:<br> Niektóre usługi są połączone natywnie, takie jak AWS i usługi firmy Microsoft, te usługi wykorzystują platformę Azure Foundation do zintegrowanej integracji, ale następujące rozwiązania mogą być połączone za pomocą kilku kliknięć:
    - [Amazon Web Services — CloudTrail](connect-aws.md)
    - [Aktywność platformy Azure](connect-azure-activity.md)
    - [Dzienniki inspekcji usługi Azure AD i logowania](connect-azure-active-directory.md)
    - [Usługa Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure Advanced Threat Protection](connect-azure-atp.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Security Center](connect-azure-security-center.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Serwer nazw domen](connect-dns.md)
    - [Office 365](connect-office-365.md)
    - [Usługa Microsoft Defender ATP](connect-microsoft-defender-advanced-threat-protection.md)
    - [Zapora aplikacji internetowej firmy Microsoft](connect-microsoft-waf.md)
    - [Zapora systemu Windows](connect-windows-firewall.md)
    - [Zdarzenia zabezpieczeń systemu Windows](connect-windows-security-events.md)

- **Rozwiązania zewnętrzne za pośrednictwem interfejsu API**: Niektóre źródła danych są połączone przy użyciu interfejsów API, które są udostępniane przez połączone źródło danych. Zazwyczaj większość technologii zabezpieczeń zapewnia zestaw interfejsów API, za pomocą których można pobrać dzienniki zdarzeń. Interfejsy API nawiązują połączenie z platformą Azure, a następnie zbierają określone typy danych i wysyłają je do Log Analytics platformy Azure. Urządzenia połączone za pośrednictwem interfejsu API obejmują:
    - [Barracuda](connect-barracuda.md)
    - [Barracuda CloudGen Firewall](connect-barracuda-cloudgen-firewall.md)
    - [Citrix Analytics (Security)](connect-citrix-analytics.md)
    - [F5 BIG-IP](connect-f5-big-ip.md)
    - [Forcepoint DLP](connect-forcepoint-dlp.md)
    - [Squadra Technologies secRMM](connect-squadra-secrmm.md)
    - [Symantec ICDX](connect-symantec.md)
    - [Zimperium](connect-zimperium-mtd.md)


- **Rozwiązania zewnętrzne przez agenta**: wskaźnik na platformie Azure może być połączony ze wszystkimi innymi źródłami danych, które mogą wykonywać przesyłanie strumieniowe w czasie rzeczywistym przy użyciu protokołu dziennika systemowego za pośrednictwem agenta. <br>Większość urządzeń używa protokołu dziennika systemowego do wysyłania komunikatów zdarzeń, które obejmują sam dziennik i dane dotyczące dziennika. Format dzienników jest różny, ale większość urządzeń obsługuje formatowanie oparte na formacie Common Event format (CEF) dla danych dzienników. <br>Agent wskaźnikowy platformy Azure, który jest oparty na agencie Log Analytics, konwertuje CEF sformatowane dzienniki do formatu, który może zostać pozyskany przez Log Analytics. W zależności od typu urządzenia Agent jest instalowany bezpośrednio na urządzeniu lub na dedykowanym serwerze z systemem Linux. Agent dla systemu Linux odbiera zdarzenia z demona dziennika systemu za pośrednictwem protokołu UDP, ale jeśli oczekuje się, że maszyna z systemem Linux będzie zbierać duże ilości zdarzeń dziennika systemowego, są one wysyłane za pośrednictwem protokołu TCP z demona dziennika systemowego do agenta i z tego miejsca do Log Analytics.
    - Zapory, proxy i punkty końcowe:
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [ExtraHop Reveal(x)](connect-extrahop.md)
        - [F5](connect-f5.md)
        - [Produkty firmy Forcepoint](connect-forcepoint-casb-ngfw.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto Networks](connect-paloalto.md)
        - [Łączenie z rozwiązaniem One Identity Safeguard](connect-one-identity.md)
        - [Inne urządzenia CEF](connect-common-event-format.md)
        - [Inne urządzenia dziennika systemu](connect-syslog.md)
        - [Trend Micro Deep Security](connect-trend-micro.md)
        - [Zscaler](connect-zscaler.md)
    - Rozwiązania DLP
    - [Dostawcy analizy zagrożeń](connect-threat-intelligence.md)
    - [Maszyny DNS](connect-dns.md) — Agent zainstalowany bezpośrednio na komputerze DNS
    - Serwery z systemem Linux
    - Inne chmury
    
## <a name="agent-connection-options"></a>Opcje połączenia z agentem<a name="agent-options"></a>

Aby połączyć urządzenie zewnętrzne z centrum danych platformy Azure, należy wdrożyć agenta na dedykowanym komputerze (maszynie wirtualnej lub lokalnie), aby umożliwić obsługę komunikacji między urządzeniem i wskaźnikiem kontrolnym platformy Azure. Agenta można wdrożyć automatycznie lub ręcznie. Automatyczne wdrażanie jest dostępne tylko wtedy, gdy dedykowany komputer jest nową maszyną wirtualną, którą tworzysz na platformie Azure. 


![CEF na platformie Azure](./media/connect-cef/cef-syslog-azure.png)

Alternatywnie można wdrożyć agenta ręcznie na istniejącej maszynie wirtualnej platformy Azure, na maszynie wirtualnej w innej chmurze lub na maszynie lokalnej.

![CEF lokalnie](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>Mapowanie typów danych za pomocą opcji połączenia rozwiązania Azure Sentinel


| **Typ danych** | **Jak nawiązać połączenie** | **Łącznik danych?** | **Komentarze** |
|------|---------|-------------|------|
| AWSCloudTrail | [Łączenie usługi AWS](connect-aws.md) | V | |
| AzureActivity | [Omówienie funkcji i dzienników aktywności](../azure-monitor/platform/platform-logs-overview.md) [platformy Azure](connect-azure-activity.md)| V | |
| AuditLogs | [Łączenie z usługą Azure AD](connect-azure-active-directory.md)  | V | |
| SigninLogs | [Łączenie z usługą Azure AD](connect-azure-active-directory.md)  | V | |
| AzureFirewall |[Diagnostyka Azure](../firewall/tutorial-diagnostics.md) | V | |
| InformationProtectionLogs_CL  | [Raporty Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip)<br>[Łączenie z usługą Azure Information Protection](connect-azure-information-protection.md)  | V | Zwykle używa funkcji **InformationProtectionEvents** oprócz typu danych. Aby uzyskać więcej informacji, zobacz [jak modyfikować raporty i tworzyć zapytania niestandardowe](https://docs.microsoft.com/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)|
| AzureNetworkAnalytics_CL  | [Analiza ruchu](../network-watcher/traffic-analytics.md) [schematu analitycznego ruchu](../network-watcher/traffic-analytics.md)  | | |
| CommonSecurityLog  | [Połącz CEF](connect-common-event-format.md)  | V | |
| Pakiet Office | [Łączenie z usługą Office 365](connect-office-365.md) | V | |
| SecurityEvents | [Łączenie ze zdarzeniami zabezpieczeń systemu Windows](connect-windows-security-events.md)  | V | W przypadku skoroszytów z niezabezpieczonymi protokołami zobacz [Ustawienia skoroszytu niezabezpieczonych protokołów](/azure/sentinel/quickstart-get-visibility#use-built-in-workbooks)  |
| Dziennik systemu | [Łączenie ze źródłami Syslog](connect-syslog.md) | V | |
| Zapora aplikacji sieci Web firmy Microsoft (WAF) — (AzureDiagnostics) |[Połącz zaporę aplikacji sieci Web firmy Microsoft](connect-microsoft-waf.md) | V | |
| SymantecICDx_CL | [Połącz firmę Symantec](connect-symantec.md) | V | |
| ThreatIntelligenceIndicator  | [Łączenie z funkcjami analizy zagrożeń](connect-threat-intelligence.md)  | V | |
| VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Mapa usługi Azure Monitor](../azure-monitor/insights/service-map.md)<br>[Azure Monitor dołączania do usługi VM Insights](../azure-monitor/insights/vminsights-onboard.md) <br> [Włącz Azure Monitor szczegółowych informacji o maszynie wirtualnej](../azure-monitor/insights/vminsights-enable-overview.md) <br> [Korzystanie z jednej maszyny wirtualnej na płycie](../azure-monitor/insights/vminsights-enable-single-vm.md)<br>  [Korzystanie z funkcji dołączania za pośrednictwem zasad](../azure-monitor/insights/vminsights-enable-at-scale-policy.md)| X | Skoroszyt usługi VM Insights  |
| DnsEvents | [Połącz system DNS](connect-dns.md) | V | |
| W3CIISLog | [Łączenie dzienników usług IIS](../azure-monitor/platform/data-sources-iis-logs.md)  | X | |
| Typowe | [Łączenie danych telekomunikacyjnych](../azure-monitor/insights/wire-data.md) | X | |
| WindowsFirewall | [Podłączanie zapory systemu Windows](connect-windows-firewall.md) | V | |
| AADIP SecurityAlert  | [Łączenie z usługą Azure AD Identity Protection](connect-azure-ad-identity-protection.md)  | V | |
| AATP SecurityAlert  | [Łączenie z usługą Azure ATP](connect-azure-atp.md) | V | |
| SecurityAlert ASC  | [Łączenie z usługą Azure Security Center](connect-azure-security-center.md)  | V | |
| MCAS SecurityAlert  | [Połącz Microsoft Cloud App Security](connect-cloud-app-security.md)  | V | |
| SecurityAlert | | | |
| Sysmon (zdarzenie) | [Połącz Sysmon](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Połącz zdarzenia systemu Windows](../azure-monitor/platform/data-sources-windows-events.md) <br> [Pobierz Analizator Sysmon](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/Sysmon-v10.42-Parser.txt)| X | Kolekcja Sysmon nie jest instalowana domyślnie na maszynach wirtualnych. Aby uzyskać więcej informacji na temat sposobu instalowania agenta Sysmon, zobacz [Sysmon](https://docs.microsoft.com/sysinternals/downloads/sysmon). |
| ConfigurationData  | [Automatyzowanie spisu maszyn wirtualnych](../automation/automation-vm-inventory.md)| X | |
| Zmianakonfiguracji  | [Automatyzowanie śledzenia maszyn wirtualnych](../automation/change-tracking.md) | X | |
| F5 BIG-IP | [Łączenie z rozwiązaniem F5 BIG-IP](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)  | X | |
| McasShadowItReporting  |  | X | |
| Barracuda_CL | [Łączenie z rozwiązaniami Barracuda](connect-barracuda.md) | V | |


## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć pracę z systemem Azure — wskaźnikiem, potrzebna jest subskrypcja do Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
- Dowiedz się, jak dołączać [dane do usługi Azure wskaźnikowej](quickstart-onboard.md)i [uzyskiwać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
