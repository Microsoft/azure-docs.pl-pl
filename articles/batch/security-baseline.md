---
title: Podstawowa baza zabezpieczeń Azure dla usługi Batch
description: Podstawowa baza zabezpieczeń Azure dla usługi Batch
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1eb24871817f365efe58b8e687563727df74493c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400980"
---
# <a name="azure-security-baseline-for-batch"></a>Podstawowa baza zabezpieczeń Azure dla usługi Batch

Podstawowa baza danych zabezpieczeń Azure dla usługi Batch zawiera zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia.

Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](../security/benchmarks/overview.md), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure z naszymi najlepszymi wskazówkami.

Aby uzyskać więcej informacji, zobacz [podstawy zabezpieczeń platformy Azure — omówienie](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: zabezpieczenia sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure na Virtual Network

**Wskazówki**: wdrażanie pul Azure Batch w sieci wirtualnej. Aby umożliwić węzłom obliczeniowym puli bezpieczne komunikowanie się z innymi maszynami wirtualnymi lub z siecią lokalną, można udostępnić pulę w podsieci sieci wirtualnej platformy Azure. Ponadto wdrożenie puli w ramach sieci wirtualnej zapewnia kontrolę nad sieciową grupą zabezpieczeń (sieciowej grupy zabezpieczeń) służącą do zabezpieczania interfejsów sieciowych poszczególnych węzłów (NIC), a także podsieci. Skonfiguruj sieciowej grupy zabezpieczeń, aby zezwalać na ruch tylko z zaufanych adresów IP/Locations przez Internet.

Jak utworzyć pulę Azure Batch w ramach Virtual Network:

https://docs.microsoft.com/azure/batch/batch-virtual-network

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: Użyj Azure Security Center i skoryguj zalecenia dotyczące ochrony sieci dotyczące sieci wirtualnej/sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) skojarzonej z pulą zadań. Włącz dzienniki przepływu na sieciowej grupy zabezpieczeń używanym do ochrony puli usługi Batch i Wyślij dzienniki do konta magazynu platformy Azure na potrzeby inspekcji ruchu. Możesz również wysłać dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego usługi Azure Log Analytics i użyć usługi Azure Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety platformy Azure Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i konfigurowania nieprawidłowo skonfigurowanych sieci.

Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak włączyć i używać Analiza ruchu:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: Włącz usługę Azure DDoS (rozproszone odmowa usługi) w sieci wirtualnej chroniącą pulę Azure Batch w celu ochrony przed atakami DDoS. Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi lub nieużywanymi adresami IP.

Jak skonfigurować ochronę DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Opis Azure Security Center zintegrowanej analizy zagrożeń:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: rejestrowanie pakietów sieciowych i dzienników przepływów

**Wskazówki**: Włączanie dzienników przepływów w sieciowej grupie zabezpieczeń (sieciowej grupy zabezpieczeń) używanej do ochrony puli Azure Batch i wysyłania dzienników do konta usługi Azure Storage na potrzeby inspekcji ruchu.

Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1,6: Wdróż systemy zapobiegania wykrywaniu dostępu do sieci/intruzów

**Wskazówki**: Jeśli jest to wymagane do celów zgodności, wybierz sieciowe urządzenie wirtualne w portalu Azure Marketplace, które obsługuje systemy wykrywania wtargnięcia (identyfikatory) i systemy zapobiegania włamaniom (IP) z możliwościami inspekcji ładunku.

Jeśli wykrywanie i/lub zapobieganie włamaniu nie są wymagane w oparciu o inspekcję ładunku, można użyć zapory platformy Azure z funkcją analizy zagrożeń. Filtrowanie oparte na analizie zagrożeń na platformie Azure może wysyłać alerty i odrzucać ruch do i ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft.

Wdróż zaporę platformy Azure z publicznym adresem IP w tej samej sieci wirtualnej co węzły puli Azure Batch. Skonfiguruj reguły translacji adresów sieciowych (NAT) między zaufanymi lokalizacjami w Internecie i prywatnymi adresami IP węzłów poszczególnych pul. W obszarze Zapora systemu Azure w obszarze Analiza zagrożeń skonfiguruj opcję "Alert i Odmów", aby blokować alerty i blokować ruch do/z znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą ze źródła analizy zagrożeń firmy Microsoft i są uwzględniane tylko najwyższe rekordy zaufania. 

Jak utworzyć pulę Azure Batch w ramach Virtual Network:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Jak wdrożyć zaporę platformy Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Portal Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="17-manage-traffic-to-your-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: nie dotyczy, test porównawczy jest przeznaczony dla aplikacji sieci Web uruchamianych w wystąpieniach Azure App Service lub IaaS.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi sieci wirtualnej, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapór platformy Azure skojarzonych z pulami Azure Batch. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

Zrozumienie i używanie tagów usługi:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych skojarzonych z pulami Azure Batch z Azure Policy. Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft.Batch" i "Microsoft. Network", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci pul Azure Batch.

Jak skonfigurować Azure Policy i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: używanie tagów dla grup usług sieciowych (sieciowych grup zabezpieczeń) i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu, które są skojarzone z pulami usługi Azure Batch. W przypadku poszczególnych reguł sieciowej grupy zabezpieczeń Użyj pola "Description", aby określić potrzeby biznesowe i/lub czas trwania (itp.) dla reguł zezwalających na ruch do/z sieci.

Użyj dowolnych wbudowanych definicji Azure Policy związanych ze znakiem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o istniejących nieoznakowanych zasobach.

Możesz użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów.

Tworzenie i używanie tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak utworzyć sieć wirtualną:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Jak utworzyć sieciowej grupy zabezpieczeń:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z pulami Azure Batch. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view 

Jak utworzyć alerty w Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu

**Wskazówki**: dla Azure Batch domyślnie firma Microsoft zapewnia synchronizację czasu. Jednakże jeśli masz określone wymagania dotyczące synchronizacji czasu, można zaimplementować te zmiany.

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: dołączanie konta Azure Batch, aby Azure monitor do agregowania danych zabezpieczeń wygenerowanych przez urządzenia klastrowe. Wykorzystanie niestandardowych zapytań w celu wykrywania zagrożeń i reagowania na nie w środowisku.  W przypadku Azure Batch monitorowania na poziomie zasobów należy używać interfejsów API usługi Batch do monitorowania stanu zasobów lub wykonywania względem nich zapytań, takich jak zadania, zadania, węzły i pule.

Jak dołączyć konto Azure Batch do Azure Monitor:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: na potrzeby monitorowania na poziomie konta Azure Batch, Monitoruj każde konto usługi Batch przy użyciu funkcji usługi Azure monitor. Azure Monitor zbiera metryki i opcjonalne dzienniki diagnostyczne dla zasobów objętych zakresem na poziomie konta usługi Batch, takich jak pule, zadania i zadania. Zbieraj te dane i korzystaj z nich ręcznie lub programowo, aby monitorować działania na koncie w usłudze Batch i diagnozować problemy.

W przypadku Azure Batch monitorowania poziomu zasobów należy używać interfejsów API Azure Batch do monitorowania stanu zasobów, takich jak zadania, zadania, węzły i pule.

Jak skonfigurować Azure Batch monitorowania i rejestrowania na poziomie konta:

https://docs.microsoft.com/azure/batch/monitoring-overview

Informacje o monitorowaniu na poziomie zasobów w usłudze Batch:

https://docs.microsoft.com/azure/batch/monitoring-overview#batch-resource-monitoring

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="24-collect-security-logs-from-operating-system"></a>2,4: Zbierz dzienniki zabezpieczeń z systemu operacyjnego

**Wskazówki**: Azure monitor zbiera metryki i dzienniki diagnostyczne dla zasobów na koncie usługi Azure Batch. Zbieraj te dane i korzystaj z nich na różne sposoby monitorowania konta Azure Batch i diagnozowania problemów. Możesz również skonfigurować alerty metryk, aby otrzymywać powiadomienia, gdy Metryka osiągnie określoną wartość.

W razie potrzeby można połączyć się z węzłami poszczególnych pul za pośrednictwem protokołu Secure Shell (SSH) lub Remote Desktop Protocol (RDP), aby uzyskać dostęp do dzienników lokalnych systemów operacyjnych.

Jak zbierać dzienniki diagnostyczne z konta usługi Azure Batch:

https://docs.microsoft.com/azure/batch/batch-diagnostics#batch-diagnostics

Jak zdalnie nawiązać połączenie z węzłami puli Azure Batch:

https://docs.microsoft.com/azure/batch/batch-api-basics#error-handling

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: dołączanie Azure Batch konta do Azure monitor. Upewnij się, że używany obszar roboczy platformy Azure Log Analytics ma ustawiony okres przechowywania dziennika zgodnie z regulacjami zgodności organizacji

Jak skonfigurować Azure Batch monitorowania i rejestrowania:

https://docs.microsoft.com/azure/batch/monitoring-overview

Jak skonfigurować okres przechowywania obszaru roboczego usługi Azure Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: tworzenie alertów metryk Azure Batch, które są wyzwalane, gdy wartość określonej metryki przekroczy dany próg.

Jak skonfigurować alerty metryk Azure Batch:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: Włączanie alertów dla nietypowego działania

**Wskazówki**: tworzenie alertów metryk Azure Batch, które są wyzwalane, gdy wartość określonej metryki przekroczy dany próg.

Jak skonfigurować alerty metryk Azure Batch:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: Użyj usługi Windows Defender na poszczególnych węzłach usługi Batch w przypadku systemów operacyjnych Windows lub podaj własne rozwiązanie chroniące przed złośliwym oprogramowaniem, jeśli używasz systemu Linux.

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: Implementowanie rozwiązania innej firmy na potrzeby rejestrowania w systemie DNS

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: ręczne Konfigurowanie rejestrowania konsoli i transkrypcji programu PowerShell na poszczególnych węzłach.

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: tożsamość i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3,1: przechowywanie spisu kont administracyjnych

**Wskazówki**: zachowywanie rekordu lokalnego konta administracyjnego, które jest tworzone podczas aprowizacji puli Azure Batch, a także innych utworzonych kont. Ponadto jeśli jest używana integracja z usługą Azure Active Directory (AAD), w usłudze AAD znajdują się wbudowane role, które muszą być jawnie przypisane i dlatego queryable. Przy użyciu modułu programu PowerShell usługi AAD można wykonywać zapytania ad hoc w celu odnajdywania kont należących do grup administracyjnych.

Ponadto możesz użyć zaleceń dotyczących zarządzania tożsamościami i dostępem Azure Security Center.

Jak uzyskać rolę katalogu w usłudze AAD przy użyciu programu PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Jak uzyskać członków roli katalogu w usłudze AAD przy użyciu programu PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Jak monitorować tożsamość i uzyskać dostęp za pomocą Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: podczas aprowizacji puli Azure Batch można utworzyć konta komputera lokalnego. Nie ma domyślnych haseł do zmiany, ale można określić różne hasła dla dostępu Secure Shell (SSH) i Remote Desktop Protocol (RDP). Po skonfigurowaniu puli Azure Batch można wygenerować losowego użytkownika dla poszczególnych węzłów w Azure Portal lub za pośrednictwem interfejsu API Azure Resource Manager.

Jak dodać użytkownika do określonego węzła obliczeniowego:

https://docs.microsoft.com/rest/api/batchservice/computenode/adduser

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3,3: Upewnij się, że używasz dedykowanych kont administracyjnych

**Wskazówki**: Zintegruj uwierzytelnianie dla aplikacji Azure Batch z Azure Active Directory. Utwórz zasady i procedury dotyczące korzystania z dedykowanych kont administracyjnych.

Ponadto możesz użyć zaleceń dotyczących zarządzania tożsamościami i dostępem Azure Security Center.

Jak uwierzytelniać aplikacje wsadowe za pomocą Azure Active Directory:

https://docs.microsoft.com/azure/batch/batch-aad-auth

Jak monitorować tożsamość i uzyskać dostęp za pomocą Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3,4: Użyj jednego Sign-On (SSO) z Azure Active Directory

**Wskazówki**: nie dotyczy, podczas gdy Azure Batch obsługuje uwierzytelnianie w usłudze Azure AD, logowanie jednokrotne nie jest obsługiwane.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory.

**Wskazówki**: integracja uwierzytelniania dla aplikacji Azure Batch z usługą Azure Active Directory (AAD). Włącz uwierzytelnianie wieloskładnikowe usługi AAD (MFA) i postępuj zgodnie z zaleceniami Azure Security Center zarządzaniem tożsamościami i dostępem.

 

Jak włączyć usługę MFA na platformie Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak monitorować tożsamość i dostęp w Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj dostępem uprzywilejowanym (uprzywilejowanych stacji roboczych dostępu) z uwierzytelnianiem wieloskładnikowym (MFA) skonfigurowanym do logowania się i konfigurowania zasobów Azure Batch.

Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak włączyć usługę MFA na platformie Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: dziennik i alert dotyczący podejrzanego działania z kont administracyjnych

**Wskazówki**: Jeśli masz zintegrowane uwierzytelnianie dla Azure Batch aplikacji z usługą Azure Active Directory (AAD), użyj Azure Active Directory raportów zabezpieczeń, aby generować dzienniki i alerty w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku. Użyj Azure Security Center, aby monitorować działania związane z tożsamościami i dostępem.

Identyfikowanie użytkowników usługi Azure AD oflagowanych w celu działania ryzykownego:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Jak monitorować działania związane z tożsamościami i dostępem użytkowników w Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3,8: Zarządzaj zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Jeśli masz zintegrowane uwierzytelnianie dla Azure Batch aplikacji z Azure Active Directory, możesz użyć nazw lokalizacji dostępu warunkowego, aby zezwolić na dostęp tylko do określonych logicznych grup zakresów adresów IP lub krajów/regionów.

Jak skonfigurować nazwane lokalizacje na platformie Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (AAD) jako centralnego systemu uwierzytelniania i autoryzacji i Zintegruj uwierzytelnianie dla aplikacji Azure Batch za pomocą usługi AAD. W usłudze AAD dane są chronione przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. W usłudze AAD są również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

Jak utworzyć i skonfigurować wystąpienie usługi AAD:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Jak uwierzytelniać aplikacje usługi Batch za pomocą usługi AAD:

https://docs.microsoft.com/azure/batch/batch-aad-auth

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Azure Active Directory (AAD) zawiera dzienniki ułatwiające wykrywanie starych kont. Ponadto można korzystać z przeglądów dostępu do tożsamości platformy Azure w celu wydajnego zarządzania członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają stały dostęp.

Jak korzystać z przeglądów dostępu do tożsamości platformy Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych kont

**Wskazówki**: Tworzenie ustawień diagnostycznych dla Azure Active Directory kont użytkowników, wysyłanie dzienników inspekcji i dzienników logowania do obszaru roboczego usługi Azure log Analytics. Skonfiguruj żądane alerty w obszarze roboczym usługi Azure Log Analytics.

Jak zintegrować dzienniki aktywności platformy Azure z Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: korzystanie z funkcji wykrywania ryzyka Azure Active Directory (AAD) i ochrony tożsamości w celu skonfigurowania zautomatyzowanych odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. Dodatkowo można pozyskać dane do usługi Azure wskaźnikowej na potrzeby dalszej analizy.

Jak wyświetlić ryzykowne logowania w usłudze AAD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Jak dołączyć wskaźnik na platformie Azure:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="313-divprovide-microsoft-with-access-to-relevant-customer-data-during-support-scenariosbrdiv"></a>3,13: <div>Zapewnianie firmie Microsoft dostępu do odpowiednich danych klienta w trakcie scenariuszy pomocy technicznej<br></div>

**Wskazówki**: niedostępne; Azure Batch nie Skrytka klienta jeszcze obsługiwane.
 
Lista obsługiwanych usług Skrytka klienta: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.

Tworzenie i używanie tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie oddzielnych subskrypcji i/lub grup zarządzania na potrzeby tworzenia, testowania i produkcji. Pule Azure Batch powinny być oddzielone przez sieć wirtualną/podsieć, odpowiednio otagowane i zabezpieczone za pomocą sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń). Azure Batch dane powinny znajdować się w ramach zabezpieczonego konta usługi Azure Storage.

Jak utworzyć pulę Azure Batch w ramach Virtual Network:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Jak zabezpieczyć konta usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych.

**Wskazówki**: dla kont usługi Azure Storage skojarzonych z pulami Azure Batch, które zawierają informacje poufne, oznacz je jako poufne przy użyciu tagów i zabezpiecz je za pomocą najlepszych rozwiązań platformy Azure.

Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla usługi Azure Storage i zasobów obliczeniowych. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

Zrozumienie ochrony danych klientów na platformie Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Jak zabezpieczyć konta usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: udostępnione

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: Szyfruj wszystkie informacje poufne podczas przesyłania. Zasoby Microsoft Azure domyślnie negocjują TLS 1,2. Upewnij się, że wszyscy klienci łączący się z pulami Azure Batch lub magazynami danych (konta usługi Azure Storage) mogą negocjować protokół TLS 1,2 lub nowszy.

Upewnij się, że do uzyskiwania dostępu do konta magazynu zawierającego dane Azure Batch jest wymagany protokół HTTPS.

Informacje na temat szyfrowania konta usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: dla kont usługi Azure Storage skojarzonych z pulami Azure Batch, które zawierają informacje poufne, oznacz je jako poufne przy użyciu tagów i zabezpiecz je za pomocą najlepszych rozwiązań platformy Azure.

Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla usługi Azure Storage i zasobów obliczeniowych. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

Zrozumienie ochrony danych klientów na platformie Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Jak zabezpieczyć konta usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: udostępnione

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów

**Wskazówki**: Funkcja kontroli dostępu opartej na rolach (Azure RBAC) umożliwia kontrolowanie dostępu do płaszczyzny zarządzania zasobów platformy Azure, w tym konta usługi Batch, pul wsadowych i kont magazynu.

Opis kontroli RBAC platformy Azure:

https://docs.microsoft.com/azure/role-based-access-control/overview

Jak skonfigurować usługę Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla usługi Azure Storage i zasobów obliczeniowych. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

Zrozumienie ochrony danych klientów na platformie Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: udostępnione

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: dla kont magazynu skojarzonych z kontem Azure Batch zaleca się, aby firma Microsoft mogła zarządzać kluczami szyfrowania, jednak istnieje możliwość zarządzania własnymi kluczami w razie potrzeby.

Jak zarządzać kluczami szyfrowania dla kont usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dotyczące sytuacji, w których zmiany dotyczą najważniejszych zasobów platformy Azure związanych z kontami Azure Batch/pulami lub skojarzonych z nimi.

Skonfiguruj ustawienia diagnostyczne dla kont magazynu skojarzonych z pulą Azure Batch, aby monitorować i rejestrować wszystkie operacje CRUD na danych puli.

Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Jak włączyć dodatkowe rejestrowanie/inspekcję dla konta usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: dla węzłów puli Azure Batch użytkownik jest odpowiedzialny za zarządzanie rozwiązaniem do zarządzania lukami w zabezpieczeniach.

Opcjonalnie, jeśli istnieje Rapid7, Qualys lub jakakolwiek inna subskrypcja platformy do zarządzania lukami w zabezpieczeniach, można ręcznie zainstalować agentów oceny luk w zabezpieczeniach w węzłach puli usługi Batch i zarządzać węzłami za pomocą odpowiedniego portalu.

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: Firma Microsoft do obsługi i aktualizacji obrazów węzła podstawowego puli Azure Batch. Upewnij się, że system operacyjny węzłów puli Azure Batch jest zastosowany na czas trwania okresu istnienia klastra, co może wymagać włączenia aktualizacji automatycznych, monitorowania węzłów lub wykonywania okresowych ponownych uruchomień.


**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Wdróż rozwiązanie zautomatyzowanego zarządzania poprawkami Third Party Software

**Wskazówki**: Upewnij się, że węzły puli Azure Batch "aplikacje innych firm pozostają poprawione na czas istnienia klastra, co może wymagać włączenia aktualizacji automatycznych, monitorowania węzłów lub wykonywania okresowego ponownego rozruchu.


**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: Jeśli masz Rapid7, Qualys lub inną subskrypcję platformy do zarządzania lukami w zabezpieczeniach, możesz użyć portalu tego dostawcy, aby wyświetlić i porównać przeskanowane luki w zabezpieczeniach.

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach.

**Wskazówki**: Użyj typowego programu oceny ryzyka (np. typowego oceniania luk w zabezpieczeniach systemu) lub domyślnych ocen ryzyka udostępnianych przez narzędzie skanowania innej firmy.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Spis i zarządzanie zasobami](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Użyj odnajdywania zasobów platformy Azure

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania wszystkich zasobów (np. obliczeń, magazynu, sieci itp.) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i że można wyliczyć wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

Mimo że klasyczne zasoby platformy Azure mogą zostać odnalezione za pośrednictwem grafu zasobów, zdecydowanie zaleca się tworzenie i używanie zasobów Azure Resource Manager (ARM) do przodu.

Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak wyświetlić subskrypcje platformy Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Opis kontroli RBAC platformy Azure:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

Tworzenie i używanie tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie to konieczne, do organizowania i śledzenia zasobów. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

Jak utworzyć dodatkowe subskrypcje platformy Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć Grupy zarządzania:

https://docs.microsoft.com/azure/governance/management-groups/create

Jak tworzyć tagi użytkownika i:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: przechowywanie spisu zatwierdzonych zasobów platformy Azure i tytułów oprogramowania.

**Wskazówki**: Definiowanie listy zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Użyj grafu zasobów platformy Azure, aby wykonywać zapytania/odnajdywać zasoby w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak tworzyć zapytania za pomocą usługi Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: dla węzłów puli Azure Batch należy wdrożyć rozwiązanie innych firm do monitorowania węzłów klastra dla niezatwierdzonych aplikacji.


**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: dla węzłów puli Azure Batch należy wdrożyć rozwiązanie innych firm do monitorowania węzłów klastra dla niezatwierdzonych aplikacji.


**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: dla węzłów puli Azure Batch należy wdrożyć rozwiązanie innych firm, aby zapobiec wykonywaniu nieautoryzowanego oprogramowania.


**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odmówić określonego typu zasobu Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="610-implement-approved-application-list"></a>6,10: Zaimplementuj listę zatwierdzonych aplikacji

**Wskazówki**: dla węzłów puli Azure Batch należy wdrożyć rozwiązanie innych firm, aby zapobiec wykonywaniu nieautoryzowanych typów plików.


**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6,11: <div>Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager za pośrednictwem skryptów</div>

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: nie dotyczy,

Nie ma to zastosowania do Azure Batch, ponieważ użytkownicy (inni niż administratorzy) pul Azure Batch nie potrzebują dostępu do poszczególnych węzłów w celu uruchamiania zadań. Administrator klastra ma już dostęp do wszystkich węzłów.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: nie dotyczy, test porównawczy jest przeznaczony dla aplikacji sieci Web uruchamianych w wystąpieniach Azure App Service lub IaaS.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft.Batch", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji kont i pul Azure Batch.

Jak wyświetlić dostępne aliasy Azure Policy:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Jak skonfigurować Azure Policy i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7,2: Ustanów bezpieczne konfiguracje dla systemu operacyjnego

**Wskazówki**: Ustanów bezpieczne konfiguracje dla systemu operacyjnego węzłów puli usługi Batch.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7,3: obsługa bezpiecznych konfiguracji dla wszystkich zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia zasobów platformy Azure związanych z Twoim kontem i pulami usługi Batch (na przykład sieciami wirtualnymi, podsieciami, zaporami platformy Azure, kontami magazynu i magazynem). Do tworzenia zasad niestandardowych można użyć aliasów Azure Policy z następujących przestrzeni nazw:

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Opis efektów Azure Policy: https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7,4: Obsługa konfiguracji bezpiecznych dla systemów operacyjnych

**Wskazówki**: Azure Batch obrazów systemu operacyjnego puli zarządzanych i obsługiwanych przez firmę Microsoft. Użytkownik jest odpowiedzialny za implementację konfiguracji stanu na poziomie systemu operacyjnego.

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: udostępnione

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji Azure Policy dla kont Azure Batch, pul lub powiązanych zasobów, użyj Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

Jak przechowywać kod w usłudze Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentacja Azure Repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: Jeśli używasz niestandardowych obrazów dla pul Azure Batch, użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby zapewnić dostęp do obrazów tylko autoryzowanym użytkownikom.

Opis kontroli RBAC platformy Azure:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Jak skonfigurować usługę Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją systemu

**Wskazówki**: Użyj wbudowanych definicji Azure Policy do alertów, inspekcji i wymuszania konfiguracji zasobów związanych z Azure Batch.  Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft.Batch", aby utworzyć niestandardowe zasady dla kont Azure Batch i pul. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

Jak skonfigurować Azure Policy i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych

**Wskazówki**: Implementowanie rozwiązania innej firmy w celu utrzymania żądanego stanu dla systemów operacyjnych węzłów puli Azure Batch.


**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla usług platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft.Batch", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji wystąpienia Azure Batch. Można również używać wszelkich wbudowanych zasad utworzonych specjalnie dla Azure Batch lub zasobów używanych przez Azure Batch, takich jak:

- Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń

— Konta magazynu powinny używać punktu końcowego usługi sieci wirtualnej

- Należy włączyć dzienniki diagnostyczne na kontach wsadowych

Jak wyświetlić dostępne aliasy Azure Policy: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: Implementowanie rozwiązania innej firmy w celu monitorowania stanu systemów operacyjnych węzłów puli Azure Batch.


**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="711-securely-manage-azure-secrets"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: Azure Key Vault mogą być używane z wdrożeniami Azure Batch do zarządzania kluczami magazynu puli w ramach kont usługi Azure Storage.

Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Jak utworzyć Key Vault:

https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Jak przeprowadzić uwierzytelnianie w Key Vault:

https://docs.microsoft.com/azure/key-vault/general/authentication

Jak przypisać zasady dostępu Key Vault:

https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="712-securely-and-automatically-manage-identities"></a>7,12: bezpieczne i automatycznie Zarządzaj tożsamościami

**Wskazówki**: niedostępne, tożsamość usługi zarządzanej nieobsługiwane przez Azure Batch


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń zachęca również do przemieszczania odnalezionych poświadczeń do bardziej bezpiecznych lokalizacji, takich jak Azure Key Vault. 

Jak skonfigurować skaner poświadczeń: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: korzystanie z usługi Windows Defender na poszczególnych węzłach puli Azure Batch w przypadku systemów operacyjnych Windows lub w przypadku korzystania z systemu Linux przy użyciu własnego rozwiązania chroniącego przed złośliwym oprogramowaniem.


**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład Azure Batch), ale nie jest uruchamiane na zawartości klienta.

Skanuj wstępnie wszystkie pliki przekazywane do zasobów platformy Azure, które nie są obliczeniowe, takie jak App Service, Data Lake Storage, Blob Storage itd. Firma Microsoft nie może uzyskać dostępu do danych klienta w tych wystąpieniach.

Informacje na temat ochrony przed złośliwym oprogramowaniem firmy Microsoft Cloud Services i Virtual Machines:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: Użyj programu Windows Defender na poszczególnych węzłach puli Azure Batch w przypadku systemów operacyjnych Windows i upewnij się, że funkcja automatycznej aktualizacji jest włączona. Zapewniaj własne rozwiązanie chroniące przed złośliwym oprogramowaniem, jeśli używasz systemu Linux.


**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: w przypadku korzystania z konta usługi Azure Storage na potrzeby magazynu danych puli Azure Batch wybierz odpowiednią opcję NADMIAROWOŚCI (LRS, ZRS, GRS, RA-GRS). 

Jak skonfigurować nadmiarowość magazynu dla kont usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: w przypadku korzystania z konta usługi Azure Storage na potrzeby magazynu danych puli Azure Batch wybierz odpowiednią opcję NADMIAROWOŚCI (LRS, ZRS, GRS, RA-GRS).  W przypadku korzystania z Azure Key Vault dla dowolnej części wdrożenia Azure Batch upewnij się, że utworzono kopię zapasową kluczy.

Jak skonfigurować nadmiarowość magazynu dla kont usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: Jeśli zarządzasz własnymi kluczami dla kont usługi Azure Storage lub innych zasobów związanych z implementacją Azure Batch, okresowo Przetestuj przywracanie kluczy kopii zapasowej.

Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

Jak przywrócić klucz zarządzany przez klienta za pomocą programu PowerShell:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Jeśli Azure Key Vault są używane do przechowywania kluczy związanych z kontami magazynu puli Azure Batch, Włącz Soft-Delete w Azure Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

Jak włączyć usuwanie nietrwałe w Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odpowiedź na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Upewnij się, że istnieją plany odpowiedzi na zdarzenia, które definiują role pracowników, a także fazy obsługi zdarzeń/zarządzania.

Jak skonfigurować automatyzację przepływu pracy w ramach Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do alertów, ułatwiając określanie priorytetów kolejności, w której uczestniczy każdy alert, dzięki czemu w przypadku naruszenia zabezpieczeń zasobów można od razu uzyskać dostęp. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu.

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy i popraw plan zgodnie z wymaganiami.

Zapoznaj się z publikacją NIST: Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia &nbsp; dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe o zdarzeniach dotyczących zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli centrum Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną osobę.

Jak ustawić Azure Security Center kontaktu zabezpieczeń:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

Jak skonfigurować eksport ciągły:

https://docs.microsoft.com/azure/security-center/continuous-export

Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowej:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w programie Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.

Jak skonfigurować automatyzację przepływu pracy i Logic Apps:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i upewnij się, że korygujesz wszystkie krytyczne wyniki zabezpieczeń w ciągu 60 dni.

**Wskazówki**: Postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Aby uzyskać więcej informacji na temat strategii firmy Microsoft i wykonywania testów z zakresu tworzenia zespołu Red-site i testowania aplikacji na żywo, należy wykonać następujące działania: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)
