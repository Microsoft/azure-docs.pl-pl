---
title: Łączenie źródeł danych z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak połączyć źródła danych, takie jak Microsoft 365 Defender (dawniej ochrona przed zagrożeniami firmy Microsoft), Microsoft 365 i pakietu Office 365, usługi Azure AD, ATP i Cloud App Security z platformą Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: 3df78d6b53f8e8739307e9b870aa03d76bfd6771
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101718611"
---
# <a name="connect-data-sources"></a>Łączenie ze źródłami danych

Po włączeniu usługi Azure wskaźnikowej należy najpierw połączyć źródła danych. Na platformie Azure jest dostępnych wiele łączników dla rozwiązań firmy Microsoft, które są dostępne na bieżąco i zapewniają integrację w czasie rzeczywistym, w tym Microsoft 365 Defender (dawniej chronione przed zagrożeniami firmy Microsoft), źródła Microsoft 365e (w tym Office 365), Azure AD, Microsoft Defender for Identity (dawniej Azure ATP), Microsoft Cloud App Security i inne. Ponadto istnieją wbudowane łączniki do szerszego ekosystemu zabezpieczeń dla rozwiązań firm innych niż Microsoft. Do łączenia źródeł danych z platformą Azure (CEF), dziennikiem systemu

1. Z menu wybierz pozycję **Łączniki danych**. Ta strona umożliwia wyświetlenie pełnej listy łączników udostępnianych przez wskaźnik platformy Azure oraz ich stan. Wybierz łącznik, który chcesz połączyć, a następnie wybierz pozycję **Otwórz stronę łącznika**. 

   ![Galeria łączników danych](./media/collect-data/collect-data-page.png)

1. Na określonej stronie łącznika upewnij się, że spełniono wszystkie wymagania wstępne, i postępuj zgodnie z instrukcjami, aby połączyć dane z platformą Azure wskaźnikiem. Synchronizowanie dzienników z platformą Azure jest możliwe dopiero po pewnym czasie. Po nawiązaniu połączenia zobaczysz podsumowanie danych w grafie **otrzymane dane** oraz stan łączności typów danych.

   ![Konfigurowanie łączników danych](./media/collect-data/opened-connector-page.png)
  
1. Kliknij kartę **następne kroki** , aby uzyskać listę zawartości wbudowanej platformy Azure, która zapewnia dla określonego typu danych.

   ![Następne kroki dla łączników](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Metody połączenia danych

Następujące metody łączenia danych są obsługiwane przez wskaźnik na platformie Azure:

- **Integracja między usługą a usługą**:<br> Niektóre usługi są połączone natywnie, takie jak AWS i usługi firmy Microsoft, te usługi wykorzystują platformę Azure Foundation do zintegrowanej integracji, ale następujące rozwiązania mogą być połączone za pomocą kilku kliknięć:
    - [Amazon Web Services — CloudTrail](connect-aws.md)
    - [Azure Active Directory](connect-azure-active-directory.md) — dzienniki inspekcji i dzienniki logowania
    - [Aktywność platformy Azure](connect-azure-activity.md)
    - [Usługa Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure DDoS Protection](connect-azure-ddos-protection.md)
    - [Azure Defender for IoT](connect-asc-iot.md) (dawniej Azure Security Center for IoT)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Firewall](connect-azure-firewall.md)
    - [Azure Security Center](connect-azure-security-center.md) — alerty z rozwiązań usługi Azure Defender
    - [Zapora aplikacji sieci Web platformy Azure (WAF)](connect-azure-waf.md) (dawniej Microsoft WAF)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Serwer nazw domen](connect-dns.md)
    - [Microsoft 365 Defender](connect-microsoft-365-defender.md) — obejmuje zdarzenia M365D i dane PIERWOTNe MDE
    - [Microsoft Defender for Endpoint](connect-microsoft-defender-advanced-threat-protection.md) (dawniej w przypadku zaawansowanej ochrony przed zagrożeniami w usłudze Microsoft Defender)
    - [Microsoft Defender for Identity](connect-azure-atp.md) (dawniej ochrona przed zagrożeniami na platformie Azure)
    - [Microsoft Defender for Office 365](connect-office-365-advanced-threat-protection.md) (dawniej pakiet Office 365 Advanced Threat Protection)
    - [Office 365](connect-office-365.md) (teraz z zespołami!)
    - [Zapora systemu Windows](connect-windows-firewall.md)
    - [Zdarzenia zabezpieczeń systemu Windows](connect-windows-security-events.md)

- **Rozwiązania zewnętrzne za pośrednictwem interfejsu API**: Niektóre źródła danych są połączone przy użyciu interfejsów API, które są udostępniane przez połączone źródło danych. Zazwyczaj większość technologii zabezpieczeń zapewnia zestaw interfejsów API, za pomocą których można pobrać dzienniki zdarzeń. Interfejsy API nawiązują połączenie z platformą Azure, a następnie zbierają określone typy danych i wysyłają je do Log Analytics platformy Azure. Urządzenia połączone za pośrednictwem interfejsu API obejmują:
    
    - [Agari phishing i ochrona przed markami](connect-agari-phishing-defense.md)
    - [Alcide kAudit](connect-alcide-kaudit.md)
    - [Zapora aplikacji internetowej Barracuda](connect-barracuda.md)
    - [Barracuda CloudGen Firewall](connect-barracuda-cloudgen-firewall.md)
    - [BETTER Mobile Threat Defense](connect-better-mtd.md)
    - [Beyond Security beSECURE](connect-besecure.md)
    - [Cisco Umbrella](connect-cisco-umbrella.md)
    - [Citrix Analytics (Security)](connect-citrix-analytics.md)
    - [F5 BIG-IP](connect-f5-big-ip.md)
    - [Forcepoint DLP](connect-forcepoint-dlp.md)
    - [Obszar roboczy Google (dawniej: zestaw G Suite)](connect-google-workspace.md)
    - [NXLog (system Windows) — dzienniki DNS](connect-nxlog-dns.md)
    - [NXLog LinuxAudit](connect-nxlog-linuxaudit.md)
    - [Okta SSO](connect-okta-single-sign-on.md)
    - [Orca Security](connect-orca-security-alerts.md)
    - [Dzienniki rozwiązania Perimeter 81](connect-perimeter-81-logs.md)
    - [Zabezpieczenia poczty E-mail Proofpoint na żądanie (POD)](connect-proofpoint-pod.md)
    - [Proofpoint TAP](connect-proofpoint-tap.md)
    - [Qualys VM](connect-qualys-vm.md)
    - [Salesforce Service Cloud](connect-salesforce-service-cloud.md)
    - [Sophos Cloud OPTIX](connect-sophos-cloud-optix.md)
    - [Squadra Technologies secRMM](connect-squadra-secrmm.md)
    - [Symantec ICDX](connect-symantec.md)
    - [VMware Carbon Black Cloud Endpoint Standard](connect-vmware-carbon-black.md)
    - [Zimperium](connect-zimperium-mtd.md)


- **Rozwiązania zewnętrzne przez agenta**: punkt kontrolny platformy Azure może być połączony przez agenta z innymi źródłami danych, które mogą wykonywać przesyłanie strumieniowe w czasie rzeczywistym przy użyciu protokołu dziennika systemowego.

    Większość urządzeń używa protokołu dziennika systemowego do wysyłania komunikatów zdarzeń, które obejmują sam dziennik i dane dotyczące dziennika. Format dzienników jest różny, ale większość urządzeń obsługuje CEF formatowanie danych dziennika. 

    Agent wskaźnikowy platformy Azure, który jest rzeczywiście agentem Log Analytics, konwertuje CEF dzienników w formacie, który może zostać pozyskany przez Log Analytics. W zależności od typu urządzenia Agent jest instalowany bezpośrednio na urządzeniu lub w dedykowanej usłudze przesyłania dalej dzienników opartej na systemie Linux. Agent dla systemu Linux odbiera zdarzenia z demona dziennika systemu za pośrednictwem protokołu UDP, ale jeśli oczekuje się, że maszyna z systemem Linux będzie zbierać duże ilości zdarzeń dziennika systemowego, są one wysyłane za pośrednictwem protokołu TCP z demona dziennika systemowego do agenta i z tego miejsca do Log Analytics.

    - **Zapory, proxy i punkty końcowe — CEF:**
        - [AI Vectra Detect](connect-ai-vectra-detect.md)
        - [Zdarzenia zabezpieczeń Akamai](connect-akamai-security-events.md)
        - [Aruba ClearPass](connect-aruba-clearpass.md)
        - [Funkcja DLP firmy Symantec](connect-broadcom-symantec-dlp.md)
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [Citrix WAF](connect-citrix-waf.md)
        - [CyberArk Enterprise Password Vault](connect-cyberark.md)
        - [ExtraHop Reveal(x)](connect-extrahop.md)
        - [F5 ASM](connect-f5.md)
        - [Produkty firmy Forcepoint](connect-forcepoint-casb-ngfw.md)
        - [Fortinet](connect-fortinet.md)
        - [Illusive Networks AMS](connect-illusive-attack-management-system.md)
        - [Imperva WAF Gateway](connect-imperva-waf-gateway.md)
        - [Łączenie z rozwiązaniem One Identity Safeguard](connect-one-identity.md)
        - [Palo Alto Networks](connect-paloalto.md)
        - [Thycotic Secret Server](connect-thycotic-secret-server.md)
        - [Trend Micro Deep Security](connect-trend-micro.md)
        - [Trend Micro TippingPoint](connect-trend-micro-tippingpoint.md)
        - [Platforma dowodowych Network WireX](connect-wirex-systems.md)
        - [Zscaler](connect-zscaler.md)
        - [Inne urządzenia z systemem CEF](connect-common-event-format.md)
    - **Zapory, proxy i punkty końcowe — dziennik systemowy:**
        - [Alsid Active Directory](connect-alsid-active-directory.md)
        - [Cisco Meraki](connect-cisco-meraki.md)
        - [Cisco Unified Computing System (UCS)](connect-cisco-ucs.md)
        - [Infoblox NIOS](connect-infoblox.md)
        - [Juniper SRX](connect-juniper-srx.md)
        - [Pulse Connect Secure](connect-pulse-connect-secure.md)
        - [Sophos XG](connect-sophos-xg-firewall.md)
        - [Squid Proxy](connect-squid-proxy.md)
        - [Symantec Proxy SG](connect-symantec-proxy-sg.md)
        - [Symantec VIP](connect-symantec-vip.md)
        - [VMware ESXi](connect-vmware-esxi.md)
        - [Inne urządzenia oparte na dzienniku systemu](connect-syslog.md)
    - [Serwer Apache HTTP](connect-apache-http-server.md)
    - Rozwiązania DLP
    - [Dostawcy analizy zagrożeń](connect-threat-intelligence.md)
    - [Maszyny DNS](connect-dns.md) — Agent zainstalowany bezpośrednio na komputerze DNS
    - [Azure Stack maszyny wirtualne](connect-azure-stack.md)
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
| AzureActivity | [Omówienie funkcji i dzienników aktywności](../azure-monitor/essentials/platform-logs-overview.md) [platformy Azure](connect-azure-activity.md)| &#10003; | |
| AuditLogs | [Łączenie z usługą Azure AD](connect-azure-active-directory.md)  | &#10003; | |
| SigninLogs | [Łączenie z usługą Azure AD](connect-azure-active-directory.md)  | &#10003; | |
| AzureFirewall |[Diagnostyka Azure](../firewall/firewall-diagnostics.md) | &#10003; | |
| InformationProtectionLogs_CL  | [Raporty Azure Information Protection](/azure/information-protection/reports-aip)<br>[Łączenie z usługą Azure Information Protection](connect-azure-information-protection.md)  | &#10003; | Zwykle używa funkcji **InformationProtectionEvents** oprócz typu danych. Aby uzyskać więcej informacji, zobacz [jak modyfikować raporty i tworzyć zapytania niestandardowe](/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)|
| AzureNetworkAnalytics_CL  | [Analiza ruchu](../network-watcher/traffic-analytics.md) [schematu analitycznego ruchu](../network-watcher/traffic-analytics.md)  | | |
| CommonSecurityLog  | [Połącz CEF](connect-common-event-format.md)  | &#10003; | |
| Pakiet Office | [Łączenie usługi Office 365](connect-office-365.md) | &#10003; | |
| SecurityEvents | [Łączenie ze zdarzeniami zabezpieczeń systemu Windows](connect-windows-security-events.md)  | &#10003; | W przypadku skoroszytów z niezabezpieczonymi protokołami zobacz [Ustawienia skoroszytu niezabezpieczonych protokołów](./quickstart-get-visibility.md#use-built-in-workbooks)  |
| Dziennik systemu | [Łączenie ze źródłami Syslog](connect-syslog.md) | &#10003; | |
| Zapora aplikacji sieci Web firmy Microsoft (WAF) — (AzureDiagnostics) |[Połącz zaporę aplikacji sieci Web firmy Microsoft](./connect-azure-waf.md) | &#10003; | |
| SymantecICDx_CL | [Połącz firmę Symantec](connect-symantec.md) | &#10003; | |
| ThreatIntelligenceIndicator  | [Łączenie z funkcjami analizy zagrożeń](connect-threat-intelligence.md)  | &#10003; | |
| VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Mapa usługi Azure Monitor](../azure-monitor/vm/service-map.md)<br>[Azure Monitor dołączania do usługi VM Insights](../azure-monitor/vm/vminsights-enable-overview.md) <br> [Włącz Azure Monitor szczegółowych informacji o maszynie wirtualnej](../azure-monitor/vm/vminsights-enable-overview.md) <br> [Korzystanie z jednej maszyny wirtualnej na płycie](../azure-monitor/vm/vminsights-enable-portal.md)<br>  [Korzystanie z funkcji dołączania za pośrednictwem zasad](../azure-monitor/vm/vminsights-enable-policy.md)| &#10007; | Skoroszyt usługi VM Insights  |
| DnsEvents | [Połącz system DNS](connect-dns.md) | &#10003; | |
| W3CIISLog | [Łączenie dzienników usług IIS](../azure-monitor/agents/data-sources-iis-logs.md)  | &#10007; | |
| Typowe | [Łączenie danych telekomunikacyjnych](../azure-monitor/insights/wire-data.md) | &#10007; | |
| WindowsFirewall | [Podłączanie zapory systemu Windows](connect-windows-firewall.md) | &#10003; | |
| AADIP SecurityAlert  | [Łączenie z usługą Azure AD Identity Protection](connect-azure-ad-identity-protection.md)  | &#10003; | |
| AATP SecurityAlert  | [Łączenie usługi Microsoft Defender for Identity](connect-azure-atp.md) (dawniej Azure ATP) | &#10003; | |
| SecurityAlert ASC  | [Połącz alerty usługi Azure Defender](connect-azure-security-center.md) z Azure Security Center  | &#10003; | |
| MCAS SecurityAlert  | [Połącz Microsoft Cloud App Security](connect-cloud-app-security.md)  | &#10003; | |
| SecurityAlert | | | |
| Sysmon (zdarzenie) | [Połącz Sysmon](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Połącz zdarzenia systemu Windows](../azure-monitor/agents/data-sources-windows-events.md) <br> [Pobierz Analizator Sysmon](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/Sysmon/Sysmon-v10.42-Parser.txt)| &#10007; | Kolekcja Sysmon nie jest instalowana domyślnie na maszynach wirtualnych. Aby uzyskać więcej informacji na temat sposobu instalowania agenta Sysmon, zobacz [Sysmon](/sysinternals/downloads/sysmon). |
| ConfigurationData  | [Automatyzowanie spisu maszyn wirtualnych](../automation/change-tracking/overview.md)| &#10007; | |
| Zmianakonfiguracji  | [Automatyzowanie śledzenia maszyn wirtualnych](../automation/change-tracking/overview.md) | &#10007; | |
| F5 BIG-IP | [Łączenie z rozwiązaniem F5 BIG-IP](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)  | &#10007; | |
| McasShadowItReporting  |  | &#10007; | |
| Barracuda_CL | [Łączenie z rozwiązaniami Barracuda](connect-barracuda.md) | &#10003; | |


## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć pracę z systemem Azure — wskaźnikiem, potrzebna jest subskrypcja do Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
- Dowiedz się, jak dołączać [dane do usługi Azure wskaźnikowej](quickstart-onboard.md)i [uzyskiwać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).