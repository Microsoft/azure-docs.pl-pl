---
title: Łączenie źródeł danych z usługą Azure Sentinel | Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć źródła danych z usługą Azure Sentinel.
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
ms.openlocfilehash: e59359d791729d2136003596d80dd1f31a9a2aac
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025156"
---
# <a name="connect-data-sources"></a>Łączenie ze źródłami danych

Aby urządzenie Azure Sentinel było wbudowane, musisz najpierw połączyć się ze źródłami danych. Usługa Azure Sentinel jest dostępna z wieloma łącznikami dla rozwiązań firmy Microsoft, dostępnymi po wyjęciu z pudełka i zapewniającymi integrację w czasie rzeczywistym, w tym rozwiązania microsoft threat protection i źródła usługi Microsoft 365, w tym usługi Office 365, azure ad, azure ATP i Microsoft Cloud App Security i inne. Ponadto istnieją wbudowane łączniki do szerszego ekosystemu zabezpieczeń dla rozwiązań innych firm. Można również użyć wspólnego formatu zdarzeń, Syslog lub REST-API, aby połączyć źródła danych z usługą Azure Sentinel.  

1. W menu wybierz polecenie **Łączniki danych**. Ta strona umożliwia wyświetlenie pełnej listy łączników udostępnianych przez usługę Azure Sentinel i ich stanu. Wybierz łącznik, który chcesz połączyć, i wybierz pozycję **Otwórz stronę łącznika**. 

   ![Moduły zbierające dane](./media/collect-data/collect-data-page.png)

1. Na konkretnej stronie łącznika upewnij się, że spełniłeś wszystkie wymagania wstępne i postępuj zgodnie z instrukcjami, aby połączyć dane z usługą Azure Sentinel. Może upłynąć trochę czasu, aby dzienniki rozpocząć synchronizację z usługi Azure Sentinel. Po nawiązaniu połączenia zobaczysz podsumowanie danych na wykresie **Dane odebrane** i stan łączności typów danych.

   ![Łączenie kolektorów](./media/collect-data/opened-connector-page.png)
  
1. Kliknij kartę **Następne kroki,** aby uzyskać listę nieuwzwiązanych treści, które usługa Azure Sentinel udostępnia dla określonego typu danych.

   ![Moduły zbierające dane](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Metody połączenia danych

Następujące metody połączenia danych są obsługiwane przez usługę Azure Sentinel:

- **Integracja usługi serwisowej:**<br> Niektóre usługi są połączone natywnie, takie jak usługi AWS i Microsoft, te usługi wykorzystują podstawę platformy Azure do integracji out-of-the-box, następujące rozwiązania mogą być połączone za pomocą kilku kliknięć:
    - [Amazon Web Services - CloudTrail](connect-aws.md)
    - [Działanie platformy Azure](connect-azure-activity.md)
    - [Dzienniki inspekcji usługi Azure AD i logowania](connect-azure-active-directory.md)
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

- **Rozwiązania zewnętrzne za pośrednictwem interfejsu API:** Niektóre źródła danych są połączone przy użyciu interfejsów API dostarczanych przez połączone źródło danych. Zazwyczaj większość technologii zabezpieczeń zapewniają zestaw interfejsów API, za pomocą których można pobrać dzienniki zdarzeń. Interfejsy API łączą się z usługą Azure Sentinel i zbierają określone typy danych i wysyłają je do usługi Azure Log Analytics. Urządzenia podłączone za pośrednictwem interfejsu API obejmują:
    - [Barracuda](connect-barracuda.md)
    - [Barracuda CloudGen Firewall](connect-barracuda-cloudgen-firewall.md)
    - [Citrix Analytics (Security)](connect-citrix-analytics.md)
    - [F5 BIG-IP](connect-f5-big-ip.md)
    - [Forcepoint DLP](connect-forcepoint-dlp.md)
    - [Squadra Technologies secRMM](connect-squadra-secrmm.md)
    - [Symantec ICDX](connect-symantec.md)
    - [Zimperium](connect-zimperium-mtd.md)


- **Rozwiązania zewnętrzne za pośrednictwem agenta:** Usługa Azure Sentinel może być połączona ze wszystkimi innymi źródłami danych, które mogą wykonywać przesyłanie strumieniowe dziennika w czasie rzeczywistym za pomocą protokołu Syslog za pośrednictwem agenta. <br>Większość urządzeń używa protokołu Syslog do wysyłania komunikatów o zdarzeniach, które zawierają sam dziennik i dane dotyczące dziennika. Format dzienników jest różny, ale większość urządzeń obsługuje formatowanie oparte na formacie common event format (CEF) dla danych dzienników. <br>Agent wartownika platformy Azure, który jest oparty na agencie usługi Log Analytics, konwertuje dzienniki sformatowane w ramach usługi CEF na format, który może być pozyskiwania przez usługę Log Analytics. W zależności od typu urządzenia agent jest instalowany bezpośrednio na urządzeniu lub na dedykowanym serwerze linux. Agent systemu Linux odbiera zdarzenia z demona Sysloga za pomocą protokołu UDP, ale jeśli oczekuje się, że maszyna z systemem Linux zbierze dużą liczbę zdarzeń Syslog, są one wysyłane za pomocą protokołu TCP z demona Syslog do agenta, a stamtąd do analizy dzienników.
    - Zapory sieciowe, serwery proxy i punkty końcowe:
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [ExtraHop Reveal(x)](connect-extrahop.md)
        - [F5](connect-f5.md)
        - [Produkty firmy Forcepoint](connect-forcepoint-casb-ngfw.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto Networks](connect-paloalto.md)
        - [Łączenie z rozwiązaniem One Identity Safeguard](connect-one-identity.md)
        - [Inne urządzenia CEF](connect-common-event-format.md)
        - [Inne urządzenia Syslog](connect-syslog.md)
        - [Trend Micro Deep Security](connect-trend-micro.md)
        - [Zscaler](connect-zscaler.md)
    - Rozwiązania DLP
    - [Dostawcy analizy zagrożeń](connect-threat-intelligence.md)
    - [Maszyny DNS](connect-dns.md) - agent zainstalowany bezpośrednio na komputerze DNS
    - Serwery Linuksa
    - Inne chmury
    
## <a name="agent-connection-options"></a>Opcje połączenia z agentem<a name="agent-options"></a>

Aby połączyć urządzenie zewnętrzne z usługą Azure Sentinel, agent musi zostać wdrożony na dedykowanym komputerze (maszynie wirtualnej lub lokalnie) w celu obsługi komunikacji między urządzeniem a usługą Azure Sentinel. Agenta można wdrożyć automatycznie lub ręcznie. Automatyczne wdrażanie jest dostępne tylko wtedy, gdy dedykowana maszyna jest nową maszyną wirtualną, którą tworzysz na platformie Azure. 


![CEF na platformie Azure](./media/connect-cef/cef-syslog-azure.png)

Alternatywnie można wdrożyć agenta ręcznie na istniejącej maszynie wirtualnej platformy Azure, na maszynie Wirtualnej w innej chmurze lub na komputerze lokalnym.

![CEF w lokalu](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>Mapowanie typów danych za pomocą opcji połączenia rozwiązania Azure Sentinel


| **Typ danych** | **Jak nawiązać połączenie** | **Łącznik danych?** | **Komentarze** |
|------|---------|-------------|------|
| AWSCloudTrail (AWSCloudTrail) | [Łączenie usługi AWS](connect-aws.md) | V | |
| AzureActivity | [Omówienie łączenia](connect-azure-activity.md) [dzienników](../azure-monitor/platform/platform-logs-overview.md) aktywności i aktywności platformy Azure| V | |
| Dzienniki inspekcji | [Łączenie z usługą Azure AD](connect-azure-active-directory.md)  | V | |
| Loga do oznaczeń | [Łączenie z usługą Azure AD](connect-azure-active-directory.md)  | V | |
| Ściana AzureFirewall |[Diagnostyka Azure](../firewall/tutorial-diagnostics.md) | V | |
| InformationProtectionLogs_CL  | [Raporty usługi Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip)<br>[Łączenie z usługą Azure Information Protection](connect-azure-information-protection.md)  | V | Zwykle używa **funkcji InformationProtectionEvents** oprócz typu danych. Aby uzyskać więcej informacji, zobacz [Jak zmodyfikować raporty i utworzyć kwerendy niestandardowe](https://docs.microsoft.com/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)|
| AzureNetworkAnalytics_CL  | [Schemat analityczny ruchu](../network-watcher/traffic-analytics.md) [Traffic analytics](../network-watcher/traffic-analytics.md)  | | |
| CommonSecurityLog (Dziennik commonsecuritylog)  | [Podłącz instrument CEF](connect-common-event-format.md)  | V | |
| Aktywności pakietu Office | [Łączenie z usługą Office 365](connect-office-365.md) | V | |
| BezpieczeństwoWzdowy | [Łączenie ze zdarzeniami zabezpieczeń systemu Windows](connect-windows-security-events.md)  | V | W przypadku skoroszytów niezabezpieczonych protokołów zobacz [Ustawienia skoroszytu niezabezpieczonych protokołów](/azure/sentinel/quickstart-get-visibility#use-built-in-workbooks)  |
| Dziennik systemu | [Łączenie ze źródłami Syslog](connect-syslog.md) | V | |
| Zapora aplikacji sieci Web firmy Microsoft (WAF) — (AzureDiagnostics) |[Łączenie Zapory aplikacji sieci Web firmy Microsoft](connect-microsoft-waf.md) | V | |
| SymantecICDx_CL | [Łączenie firmy Symantec](connect-symantec.md) | V | |
| ZagrożenieIntelligenceIndicator  | [Łączenie z funkcjami analizy zagrożeń](connect-threat-intelligence.md)  | V | |
| Połączenie VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Mapa usługi Usługi Azure Monitor](../azure-monitor/insights/service-map.md)<br>[Dołączanie do informacji o maszynach wirtualnych w usłudze Azure Monitor](../azure-monitor/insights/vminsights-onboard.md) <br> [Włączanie szczegółowych informacji na temat maszyn wirtualnych monitora Platformy Azure](../azure-monitor/insights/vminsights-enable-overview.md) <br> [Korzystanie z pojedynczej maszyny wirtualnej na pokładzie](../azure-monitor/insights/vminsights-enable-single-vm.md)<br>  [Korzystanie z zasad korzystania z pokładu za pośrednictwem](../azure-monitor/insights/vminsights-enable-at-scale-policy.md)| X | Skoroszyt aplikacji VM insights  |
| DnsEvents (DnsEvents) | [Łączenie systemu DNS](connect-dns.md) | V | |
| Dziennik W3CIIS | [Łączenie dzienników usługi IIS](../azure-monitor/platform/data-sources-iis-logs.md)  | X | |
| Dane wiredata | [Podłączanie danych przewodu](../azure-monitor/insights/wire-data.md) | X | |
| Ściana WindowsFirewall | [Łączenie Zapory systemu Windows](connect-windows-firewall.md) | V | |
| AADIP SecurityAlert  | [Łączenie z usługą Azure AD Identity Protection](connect-azure-ad-identity-protection.md)  | V | |
| AATP SecurityAlert  | [Łączenie z usługą Azure ATP](connect-azure-atp.md) | V | |
| ASC SecurityAlert  | [Łączenie z usługą Azure Security Center](connect-azure-security-center.md)  | V | |
| Narzędzie zabezpieczeń MCAS  | [Łączenie zabezpieczeń aplikacji w chmurze firmy Microsoft](connect-cloud-app-security.md)  | V | |
| SecurityAlert (Certyfikat zabezpieczeń) | | | |
| Sysmon (Wydarzenie) | [Łączenie sysmonu](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Łączenie zdarzeń systemu Windows](../azure-monitor/platform/data-sources-windows-events.md) <br> [Zdobądź Sysmon Parser](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/SysmonParser.txt)| X | Kolekcja Sysmon nie jest instalowana domyślnie na maszynach wirtualnych. Aby uzyskać więcej informacji na temat instalowania programu Sysmon Agent, zobacz [Sysmon](https://docs.microsoft.com/sysinternals/downloads/sysmon). |
| ConfigurationData (Dane konfiguracyjne)  | [Automatyzacja zapasów maszyn wirtualnych](../automation/automation-vm-inventory.md)| X | |
| KonfiguracjaZmija  | [Automatyzacja śledzenia maszyn wirtualnych](../automation/change-tracking.md) | X | |
| F5 BIG-IP | [Łączenie z rozwiązaniem F5 BIG-IP](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)  | X | |
| McasShadowItReporting (McasShadowItReporting)  |  | X | |
| Barracuda_CL | [Łączenie z rozwiązaniami Barracuda](connect-barracuda.md) | V | |


## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć korzystanie z usługi Azure Sentinel, potrzebujesz subskrypcji platformy Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
- Dowiedz się, jak [dołączać dane do usługi Azure Sentinel](quickstart-onboard.md)i uzyskać wgląd w dane i potencjalne [zagrożenia.](quickstart-get-visibility.md)
