---
title: Łączenie źródeł danych z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie ze źródłami danych, takimi jak Microsoft Threat Protection, Microsoft 365 i Office 365, Azure AD, ATP i Cloud App Security do platformy Azure.
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
ms.openlocfilehash: a2b9c1602ead56b35c46508ef4d414145eb07432
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85555519"
---
# <a name="connect-data-sources"></a>Łączenie ze źródłami danych

Po włączeniu usługi Azure wskaźnikowej należy najpierw połączyć źródła danych. Wskaźnik platformy Azure obejmuje wiele łączników dla rozwiązań firmy Microsoft, dostępnych poza platformą i zapewniania integracji w czasie rzeczywistym, w tym rozwiązań ochrony przed zagrożeniami firmy Microsoft, źródeł Microsoft 365 (w tym pakietu Office 365), usługi Azure AD i usługi Azure ATP, Microsoft Cloud App Security i innych. Ponadto istnieją wbudowane łączniki do szerszego ekosystemu zabezpieczeń dla rozwiązań firm innych niż Microsoft. Do łączenia źródeł danych z platformą Azure (CEF), dziennikiem systemu

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
    - [Azure Active Directory](connect-azure-active-directory.md) — dzienniki inspekcji i dzienniki logowania
    - [Usługa Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure Advanced Threat Protection](connect-azure-atp.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Security Center](connect-azure-security-center.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Serwer nazw domen](connect-dns.md)
    - [Office 365](connect-office-365.md)
    - [Microsoft Defender ATP](connect-microsoft-defender-advanced-threat-protection.md)
    - [Zapora aplikacji internetowej firmy Microsoft](connect-microsoft-waf.md)
    - [Zapora systemu Windows](connect-windows-firewall.md)
    - [Zdarzenia zabezpieczeń systemu Windows](connect-windows-security-events.md)

- **Rozwiązania zewnętrzne za pośrednictwem interfejsu API**: Niektóre źródła danych są połączone przy użyciu interfejsów API, które są udostępniane przez połączone źródło danych. Zazwyczaj większość technologii zabezpieczeń zapewnia zestaw interfejsów API, za pomocą których można pobrać dzienniki zdarzeń. Interfejsy API nawiązują połączenie z platformą Azure, a następnie zbierają określone typy danych i wysyłają je do Log Analytics platformy Azure. Urządzenia połączone za pośrednictwem interfejsu API obejmują:
    
    - [Alcide kAudit](connect-alcide-kaudit.md)
    - [Zapora aplikacji internetowej Barracuda](connect-barracuda.md)
    - [Barracuda CloudGen Firewall](connect-barracuda-cloudgen-firewall.md)
    - [Citrix Analytics (Security)](connect-citrix-analytics.md)
    - [F5 BIG-IP](connect-f5-big-ip.md)
    - [Forcepoint DLP](connect-forcepoint-dlp.md)
    - [Dzienniki rozwiązania Perimeter 81](connect-perimeter-81-logs.md)
    - [Squadra Technologies secRMM](connect-squadra-secrmm.md)
    - [Symantec ICDX](connect-symantec.md)
    - [Zimperium](connect-zimperium-mtd.md)


- **Rozwiązania zewnętrzne przez agenta**: punkt kontrolny platformy Azure może być połączony przez agenta z innymi źródłami danych, które mogą wykonywać przesyłanie strumieniowe w czasie rzeczywistym przy użyciu protokołu dziennika systemowego.

    Większość urządzeń używa protokołu dziennika systemowego do wysyłania komunikatów zdarzeń, które obejmują sam dziennik i dane dotyczące dziennika. Format dzienników jest różny, ale większość urządzeń obsługuje CEF formatowanie danych dziennika. 

    Agent wskaźnikowy platformy Azure, który jest rzeczywiście agentem Log Analytics, konwertuje CEF dzienników w formacie, który może zostać pozyskany przez Log Analytics. W zależności od typu urządzenia Agent jest instalowany bezpośrednio na urządzeniu lub w dedykowanej usłudze przesyłania dalej dzienników opartej na systemie Linux. Agent dla systemu Linux odbiera zdarzenia z demona dziennika systemu za pośrednictwem protokołu UDP, ale jeśli oczekuje się, że maszyna z systemem Linux będzie zbierać duże ilości zdarzeń dziennika systemowego, są one wysyłane za pośrednictwem protokołu TCP z demona dziennika systemowego do agenta i z tego miejsca do Log Analytics.

    - **Zapory, proxy i punkty końcowe:**
        - [AI Vectra Detect](connect-ai-vectra-detect.md)
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [ExtraHop Reveal(x)](connect-extrahop.md)
        - [F5 ASM](connect-f5.md)
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
| AWSCloudTrail | [Łączenie usługi AWS](connect-aws.md) | &#10003; | |
| AzureActivity | [Omówienie funkcji i dzienników aktywności](../azure-monitor/platform/platform-logs-overview.md) [platformy Azure](connect-azure-activity.md)| &#10003; | |
| AuditLogs | [Łączenie z usługą Azure AD](connect-azure-active-directory.md)  | &#10003; | |
| SigninLogs | [Łączenie z usługą Azure AD](connect-azure-active-directory.md)  | &#10003; | |
| AzureFirewall |[Diagnostyka Azure](../firewall/tutorial-diagnostics.md) | &#10003; | |
| InformationProtectionLogs_CL  | [Raporty Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip)<br>[Łączenie z usługą Azure Information Protection](connect-azure-information-protection.md)  | &#10003; | Zwykle używa funkcji **InformationProtectionEvents** oprócz typu danych. Aby uzyskać więcej informacji, zobacz [jak modyfikować raporty i tworzyć zapytania niestandardowe](https://docs.microsoft.com/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)|
| AzureNetworkAnalytics_CL  | [Analiza ruchu](../network-watcher/traffic-analytics.md) [schematu analitycznego ruchu](../network-watcher/traffic-analytics.md)  | | |
| CommonSecurityLog  | [Połącz CEF](connect-common-event-format.md)  | &#10003; | |
| Pakiet Office | [Łączenie usługi Office 365](connect-office-365.md) | &#10003; | |
| SecurityEvents | [Łączenie ze zdarzeniami zabezpieczeń systemu Windows](connect-windows-security-events.md)  | &#10003; | W przypadku skoroszytów z niezabezpieczonymi protokołami zobacz [Ustawienia skoroszytu niezabezpieczonych protokołów](/azure/sentinel/quickstart-get-visibility#use-built-in-workbooks)  |
| Dziennik systemu | [Łączenie ze źródłami Syslog](connect-syslog.md) | &#10003; | |
| Zapora aplikacji sieci Web firmy Microsoft (WAF) — (AzureDiagnostics) |[Połącz zaporę aplikacji sieci Web firmy Microsoft](connect-microsoft-waf.md) | &#10003; | |
| SymantecICDx_CL | [Połącz firmę Symantec](connect-symantec.md) | &#10003; | |
| ThreatIntelligenceIndicator  | [Łączenie z funkcjami analizy zagrożeń](connect-threat-intelligence.md)  | &#10003; | |
| VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Mapa usługi Azure Monitor](../azure-monitor/insights/service-map.md)<br>[Azure Monitor dołączania do usługi VM Insights](../azure-monitor/insights/vminsights-onboard.md) <br> [Włącz Azure Monitor szczegółowych informacji o maszynie wirtualnej](../azure-monitor/insights/vminsights-enable-overview.md) <br> [Korzystanie z jednej maszyny wirtualnej na płycie](../azure-monitor/insights/vminsights-enable-single-vm.md)<br>  [Korzystanie z funkcji dołączania za pośrednictwem zasad](../azure-monitor/insights/vminsights-enable-at-scale-policy.md)| &#10007; | Skoroszyt usługi VM Insights  |
| DnsEvents | [Połącz system DNS](connect-dns.md) | &#10003; | |
| W3CIISLog | [Łączenie dzienników usług IIS](../azure-monitor/platform/data-sources-iis-logs.md)  | &#10007; | |
| Typowe | [Łączenie danych telekomunikacyjnych](../azure-monitor/insights/wire-data.md) | &#10007; | |
| WindowsFirewall | [Podłączanie zapory systemu Windows](connect-windows-firewall.md) | &#10003; | |
| AADIP SecurityAlert  | [Łączenie z usługą Azure AD Identity Protection](connect-azure-ad-identity-protection.md)  | &#10003; | |
| AATP SecurityAlert  | [Łączenie z usługą Azure ATP](connect-azure-atp.md) | &#10003; | |
| SecurityAlert ASC  | [Łączenie z usługą Azure Security Center](connect-azure-security-center.md)  | &#10003; | |
| MCAS SecurityAlert  | [Połącz Microsoft Cloud App Security](connect-cloud-app-security.md)  | &#10003; | |
| SecurityAlert | | | |
| Sysmon (zdarzenie) | [Połącz Sysmon](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Połącz zdarzenia systemu Windows](../azure-monitor/platform/data-sources-windows-events.md) <br> [Pobierz Analizator Sysmon](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/Sysmon/Sysmon-v10.42-Parser.txt)| &#10007; | Kolekcja Sysmon nie jest instalowana domyślnie na maszynach wirtualnych. Aby uzyskać więcej informacji na temat sposobu instalowania agenta Sysmon, zobacz [Sysmon](https://docs.microsoft.com/sysinternals/downloads/sysmon). |
| ConfigurationData  | [Automatyzowanie spisu maszyn wirtualnych](../automation/automation-vm-inventory.md)| &#10007; | |
| Zmianakonfiguracji  | [Automatyzowanie śledzenia maszyn wirtualnych](../automation/change-tracking.md) | &#10007; | |
| F5 BIG-IP | [Łączenie z rozwiązaniem F5 BIG-IP](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)  | &#10007; | |
| McasShadowItReporting  |  | &#10007; | |
| Barracuda_CL | [Łączenie z rozwiązaniami Barracuda](connect-barracuda.md) | &#10003; | |


## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć pracę z systemem Azure — wskaźnikiem, potrzebna jest subskrypcja do Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
- Dowiedz się, jak dołączać [dane do usługi Azure wskaźnikowej](quickstart-onboard.md)i [uzyskiwać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
