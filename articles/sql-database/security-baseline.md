---
title: Usługa Azure Security Baseline dla bazy danych SQL usługi Azure
description: Usługa Azure Security Baseline dla bazy danych SQL usługi Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8c0e7c6dfb1275e1fafbab1dd9e89cb2fe4376ad
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759158"
---
# <a name="azure-security-baseline-for-azure-sql-database"></a>Usługa Azure Security Baseline dla bazy danych SQL usługi Azure

Usługa Azure Security Baseline for Azure SQL Database zawiera zalecenia, które pomogą Ci poprawić poziom zabezpieczeń wdrożenia.

Punkt odniesienia dla tej usługi jest pobierany z [usługi Azure Security Benchmark w wersji 1.0,](https://docs.microsoft.com/azure/security/benchmarks/overview)która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure za pomocą naszych najlepszych wskazówek dotyczących rozwiązań.

Aby uzyskać więcej informacji, zobacz [omówienie planów bazowych zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Bezpieczeństwo sieci](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure w sieci wirtualnej

**Wskazówki:** Można włączyć usługę Azure Private Link, aby zezwolić na dostęp do usług PaaS platformy Azure (na przykład bazy danych SQL) i usług hostowanych przez platformę Azure dla klientów/partnerów za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. 

Aby umożliwić ruchowi dostęp do usługi Azure SQL Database, użyj tagów usługi SQL, aby zezwolić na ruch wychodzący za pośrednictwem sieciowych grup zabezpieczeń.

Reguły sieci wirtualnej umożliwiają usłudze Azure SQL Database akceptowanie tylko komunikacji, które są wysyłane z wybranych podsieci wewnątrz sieci wirtualnej.

Jak skonfigurować łącze prywatne dla bazy danych SQL usługi Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

Jak używać punktów końcowych usługi sieci wirtualnej i reguł dla serwerów bazy danych:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki:** Użyj usługi Azure Security Center i korygować zalecenia dotyczące ochrony sieci dla podsieci, w której wdrożono serwer bazy danych SQL platformy Azure. 

W przypadku maszyn wirtualnych platformy Azure (VM), które będą łączyć się z wystąpieniem serwera bazy danych SQL azure, włącz dzienniki przepływu sieciowej grupy zabezpieczeń (NSG) dla sieciowych grup zabezpieczeń chroniących te maszyny wirtualne i wysyłaj dzienniki do konta usługi Azure Storage w celu inspekcji ruchu. 

Można również wysłać dzienniki przepływu nsg do obszaru roboczego usługi Log Analytics i użyć usługi Traffic Analytics, aby zapewnić wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety usługi Traffic Analytics to możliwość wizualizacji aktywności sieciowej i identyfikowania punktów zapalnych, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i określania błędnych konfiguracji sieci.

Jak włączyć dzienniki przepływu nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Poznaj zabezpieczenia sieci udostępniane przez usługę Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Jak włączyć i korzystać z analizy ruchu:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Poznaj zabezpieczenia sieci udostępniane przez usługę Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrona krytycznych aplikacji internetowych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla usługi Azure Apps Service lub zasobów obliczeniowych hosting aplikacji sieci web.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** Włącz standard ochrony przed atakami DDoS w sieciach wirtualnych skojarzonych z wystąpieniami programu SQL Server w celu ochrony przed rozproszonymi atakami typu "odmowa usługi". Usługa Azure Security Center Integrated Threat Intelligence umożliwia odmawianie komunikacji ze znanymi złośliwymi lub nieużywanym internetowymi adresami IP.

Jak skonfigurować ochronę przed atakami DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Poznaj zintegrowaną analizę zagrożeń w centrum zabezpieczeń platformy Azure:

https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Rejestrowanie pakietów sieciowych i dzienników przepływu

**Wskazówki:** Dla maszyn wirtualnych platformy Azure (VM), które będą łączyć się z wystąpieniem bazy danych SQL platformy Azure, włącz dzienniki przepływu sieciowej grupy zabezpieczeń (NSG) dla sieciowych grup zabezpieczeń chroniących te maszyny wirtualne i wysyłaj dzienniki do konta usługi Azure Storage w celu inspekcji ruchu. Jeśli jest to wymagane do badania nietypowej aktywności, włącz przechwytywanie pakietów Obserwatora sieci.

Jak włączyć dzienniki przepływu nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak włączyć funkcję Obserwatora sieciowego:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Wdrażanie opartych na sieci systemów wykrywania włamań/włamań (IDS/IPS)

**Wskazówki:** Włącz zaawansowaną ochronę przed zagrożeniami (ATP) dla usługi Azure SQL Database.  Użytkownicy otrzymują alert o podejrzanych działaniach bazy danych, potencjalnych lukach i atakach iniekcji SQL, a także o nietypowych wzorcach dostępu do bazy danych i kwerend. Zaawansowana ochrona przed zagrożeniami integruje również alerty z usługą Azure Security Center. 

Zrozumienie i używanie zaawansowanej ochrony przed zagrożeniami dla bazy danych SQL usługi Azure:https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Zarządzanie ruchem do aplikacji internetowych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla usługi Azure Apps Service lub zasobów obliczeniowych hosting aplikacji sieci web.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizowanie złożoności i administracyjnych narzutów administracyjnych dotyczących zasad bezpieczeństwa sieci

**Wskazówki:** Użyj tagów usługi sieci wirtualnej do definiowania kontroli dostępu do sieci w sieciowych grupach zabezpieczeń lub Zaporze platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić lub odmówić ruchu dla odpowiedniej usługi. Firma Microsoft zarządza prefiksami adresów objętymi tagiem usługi i automatycznie aktualizuje tag usługi w miarę zmiany adresów.

Podczas korzystania z punktów końcowych usługi dla usługi Azure SQL Database, wychodzących do usługi Azure SQL Database publicznych adresów IP jest wymagane: sieciowe grupy zabezpieczeń (NSG) muszą być otwarte do usługi Azure SQL Database adresy IP, aby umożliwić łączność. Można to zrobić przy użyciu tagów usługi NSG dla usługi Azure SQL Database.

Poznaj tagi usług z punktami końcowymi usługi dla bazy danych SQL azure:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations

Zrozumienie tagów usług i używanie ich:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Definiowanie i implementowanie konfiguracji zabezpieczeń sieci dla wystąpień serwera usługi Azure SQL Database za pomocą zasad platformy Azure. Do definiowania definicji niestandardowych definicji zasad można użyć obszaru nazw "Microsoft.Sql", w tym definicji niestandardowych zasad lub użyć dowolnej z wbudowanych definicji zasad przeznaczonych do ochrony sieci serwera usługi Azure SQL Database. Przykładem odpowiednich wbudowanych zasad zabezpieczeń sieci dla serwera usługi Azure SQL Database jest: "SQL Server powinien używać punktu końcowego usługi sieci wirtualnej".

 

Użyj planów platformy Azure, aby uprościć wdrożenia platformy Azure na dużą skalę przez artefakty kluczowe środowisko pakowania, takie jak szablony usługi Azure Resource Management, kontrola dostępu oparta na rolach (RBAC) i zasady w jednej definicji planu. Łatwo zastosować plan do nowych subskrypcji i środowisk i dostosować kontroli i zarządzania poprzez przechowywanie wersji.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak utworzyć plan platformy Azure:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="110-document-traffic-configuration-rules"></a>1.10: Przepisy konfiguracji ruchu dokumentów

**Wskazówki:** Używaj tagów dla sieciowych grup zabezpieczeń (NSG) i innych zasobów związanych z bezpieczeństwem sieci i przepływem ruchu. W przypadku poszczególnych reguł sieciowej grupy danych sieciowych należy użyć pola "Opis", aby określić potrzeby biznesowe i/lub czas trwania (itp.) dla reguł umożliwiających ruch do/z sieci.

Użyj dowolnej z wbudowanych definicji zasad platformy Azure związanych z tagowaniem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone za pomocą tagów i powiadamiać o istniejących nieoznakowanych zasobach.

Można użyć narzędzia Azure PowerShell lub interfejsu wiersza polecenia platformy Azure do wyszukiwania lub wykonywania akcji na podstawie zasobów na podstawie ich tagów.

Jak tworzyć i używać tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki:** Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z wystąpieniami serwera bazy danych SQL platformy Azure. Tworzenie alertów w usłudze Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Jak tworzyć alerty w usłudze Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Rejestrowanie i monitorowanie](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Korzystanie z zatwierdzonych źródeł synchronizacji czasu

**Wskazówki:** Firma Microsoft przechowuje źródła czasu dla zasobów platformy Azure. Synchronizację czasu można zaktualizować dla wdrożeń obliczeniowych.

Jak skonfigurować synchronizację czasu dla zasobów obliczeniowych platformy Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** Włącz inspekcję dla usługi Azure SQL Database, aby śledzić zdarzenia bazy danych i zapisywać je w dzienniku inspekcji w swoim koncie usługi Azure Storage, obszarze roboczym usługi Log Analytics lub centrum zdarzeń.

Ponadto można przesyłać strumieniowo dane telemetryczne diagnostyki SQL platformy Azure do usługi Azure SQL Analytics, rozwiązania w chmurze, które monitoruje wydajność baz danych SQL platformy Azure, pul elastycznych i wystąpień zarządzanych na dużą skalę i w wielu subskrypcjach. Może pomóc w zbieraniu i wizualizowaniu metryk wydajności usługi Azure SQL Database i ma wbudowaną inteligencję do rozwiązywania problemów z wydajnością.

Jak skonfigurować inspekcję bazy danych SQL usługi Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

Jak zbierać dzienniki platformy i metryki za pomocą usługi Azure Monitor:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging

Jak przesyłać strumieniowo diagnostykę do usługi Azure SQL Analytics:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Włączanie rejestrowania inspekcji zasobów platformy Azure

**Wskazówki:** Włącz inspekcję wystąpienia serwera usługi Azure SQL Database i wybierz lokalizację magazynu dla dzienników inspekcji (usługa Azure Storage, usługa Log Analytics lub Centrum zdarzeń).

Jak włączyć inspekcję dla programu Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Zbieranie dzienników zabezpieczeń z systemów operacyjnych

**Wskazówki**: Nie dotyczy; ten wskaźnik jest przeznaczony dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** Podczas przechowywania dzienników bazy danych SQL usługi Azure w obszarze roboczym usługi Log Analytics ustaw okres przechowywania dzienników zgodnie z przepisami dotyczącymi zgodności organizacji.

Jak ustawić parametry przechowywania dziennika:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorowanie i przeglądanie dzienników

**Wskazówki:** Analizuj i monitoruj dzienniki pod kątem nietypowych zachowań i regularnie przeglądaj wyniki. Użyj zaawansowanej ochrony przed zagrożeniami usługi Azure Security Center, aby ostrzegać o nietypowej aktywności związanej z wystąpieniem bazy danych SQL platformy Azure. Alternatywnie należy skonfigurować alerty na podstawie wartości metryk lub wpisów dziennika aktywności platformy Azure związanych z wystąpieniami usługi Azure SQL Database.

Poznaj zaawansowaną ochronę przed zagrożeniami i alerty dla programu Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Jak skonfigurować niestandardowe alerty dla usługi Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Włączanie alertów o nietypowej aktywności

**Wskazówki:** Użyj usługi Azure Security Center Advanced Threat Protection for Azure SQL Databases do monitorowania i ostrzegania o nietypowej aktywności. Włącz zaawansowane zabezpieczenia danych dla baz danych SQL. Zaawansowane zabezpieczenia danych obejmują funkcje wykrywania i klasyfikowania poufnych danych, wykrywania i łagodzenia potencjalnych luk w zabezpieczeniach bazy danych oraz wykrywania nietypowych działań, które mogą wskazywać na zagrożenie dla bazy danych.

Poznaj zaawansowaną ochronę przed zagrożeniami i alerty dla bazy danych SQL usługi Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Jak włączyć zaawansowane zabezpieczenia danych dla bazy danych SQL usługi Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security

Jak zarządzać alertami w usłudze Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="28-centralize-anti-malware-logging"></a>2.8: Scentralizuj rejestrowanie przed złośliwym oprogramowaniem

**Wskazówki**: Nie dotyczy; dla usługi Azure SQL Server rozwiązanie chroniące przed złośliwym oprogramowaniem jest zarządzane przez firmę Microsoft na podstawowej platformie.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="29-enable-dns-query-logging"></a>2.9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: Nie dotyczy; Rejestrowanie DNS nie ma zastosowania do programu Azure SQL Server.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="210-enable-command-line-audit-logging"></a>2.10: Włącz rejestrowanie inspekcji wiersza polecenia

**Wskazówki**: Nie dotyczy; inspekcja wiersza polecenia nie ma zastosowania do programu Azure SQL Server.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Kontrola tożsamości i dostępu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Prowadzenie spisu rachunków administracyjnych

**Wskazówki:** Usługa Azure Active Directory (AAD) ma wbudowane role, które muszą być jawnie przypisane i są możliwe do queryable. Moduł AAD PowerShell służy do wykonywania zapytań ad hoc w celu odnajdowania kont, które są członkami grup administracyjnych.

Jak uzyskać rolę katalogu w usłudze Azure AD za pomocą programu PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Jak uzyskać członków roli katalogu w usłudze Azure AD za pomocą programu PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="32-change-default-passwords-where-applicable"></a>3.2: W stosownych przypadkach zmieniaj domyślne hasła

**Wskazówki:** Usługa Azure Active Directory nie ma pojęcia haseł domyślnych. Podczas inicjowania obsługi administracyjnej wystąpienia usługi Azure SQL Database zaleca się, aby zintegrować uwierzytelnianie z usługą Azure Active Directory.

Jak skonfigurować uwierzytelnianie usługi Azure Active Directory i zarządzać nim za pomocą usługi Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Korzystanie z dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie zasad i procedur dotyczących korzystania z dedykowanych kont administracyjnych. Usługa Azure Security Center Identity and Access Management służy do monitorowania liczby kont administracyjnych.

Poznaj tożsamość i dostęp usługi Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Używanie logowania jednokrotnego (Logowanie jednokrotne) w usłudze Azure Active Directory

**Wskazówki**: Nie dotyczy; podczas konfigurowania uwierzytelniania usługi Azure Active Directory do integracji z usługą Azure SQL Server, logowanie jednokrotne nie jest obsługiwane.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Użyj uwierzytelniania wieloskładnikowego dla całego dostępu opartego na usłudze Azure Active Directory

**Wskazówki:** Włącz uwierzytelnianie wieloskładnikowe usługi Azure Active Directory (AAD) i postępuj zgodnie z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem usługi Azure Security Center.

Jak włączyć usługę MFA na platformie Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak monitorować tożsamość i dostęp w usłudze Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Używanie dedykowanych maszyn (stacje robocze z uprzywilejowanym dostępem) do wszystkich zadań administracyjnych

**Wskazówki:** Użyj stacji roboczej dostępu uprzywilejowanego (PAW) z uwierzytelnianiem wieloskładnikowym uwierzytelnianym skonfigurowanym do logowania się i konfigurowania zasobów platformy Azure.

Dowiedz się więcej o stacjach roboczych z dostępem uprzywilejowanym:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak włączyć usługę MFA na platformie Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Rejestrowanie i ostrzeganie o podejrzanych działaniach z kont administracyjnych

**Wskazówki:** Użyj raportów zabezpieczeń usługi Azure Active Directory do generowania dzienników i alertów, gdy w środowisku występuje podejrzana lub niebezpieczna aktywność.

Za pomocą zaawansowanej ochrony przed zagrożeniami dla usługi Azure SQL Database można wykryć nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

Jak zidentyfikować użytkowników usługi Azure AD oflagowanych pod kątem ryzykownych działań:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Jak monitorować tożsamość użytkowników i dostęp do aktywności w usłudze Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

Przejrzyj zaawansowaną ochronę przed zagrożeniami i potencjalne alerty:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** Użyj lokalizacji nazwanych dostępu warunkowego, aby zezwolić portalowi i platformie Azure Resource Management na dostęp tylko z określonych grup logicznych zakresów adresów IP lub krajów/regionów.

Jak skonfigurować nazwane lokalizacje na platformie Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="39-use-azure-active-directory"></a>3.9: Korzystanie z usługi Azure Active Directory

**Wskazówki:** Utwórz administratora usługi Azure Active Directory (AAD) dla wystąpień serwera bazy danych SQL platformy Azure.

Jak skonfigurować uwierzytelnianie usługi Azure Active Directory i zarządzać nim za pomocą usługi Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

Jak utworzyć i skonfigurować wystąpienie AAD:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** Usługa Azure Active Directory (AAD) udostępnia dzienniki ułatwiające odnajdowanie starych kont. Ponadto użyj przeglądów dostępu usługi Azure Identity, aby skutecznie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników można regularnie przeglądać, aby upewnić się, że tylko właściwi użytkownicy mają stały dostęp.

Jak korzystać z przeglądów dostępu tożsamości platformy Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych kont

**Wskazówki:** Konfigurowanie uwierzytelniania usługi Azure Active Directory (AAD) za pomocą usługi Azure SQL i tworzenie ustawień diagnostycznych dla kont użytkowników usługi Azure Active Directory, wysyłanie dzienników inspekcji i logowań logowania do obszaru roboczego usługi Log Analytics. Konfigurowanie żądanych alertów w obszarze roboczym usługi Log Analytics.

Jak skonfigurować uwierzytelnianie usługi Azure Active Directory i zarządzać nim za pomocą usługi Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alert o odchyleniu zachowania logowania do konta

**Wskazówki:** Użyj ochrony tożsamości usługi Azure Active Directory (AAD) i wykrywania ryzyka, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane akcje związane z tożsamościami użytkowników. Ponadto można pozyskiwania danych do usługi Azure Sentinel do dalszego badania.

Jak wyświetlić logowania ryzyka usługi Azure AD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak skonfigurować i włączyć zasady ryzyka ochrony tożsamości:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Zapewnienie firmie Microsoft dostępu do odpowiednich danych klientów podczas scenariuszy pomocy technicznej

**Wskazówki:** W scenariuszach pomocy technicznej, w których firma Microsoft musi uzyskać dostęp do danych klienta, skrytka klienta platformy Azure udostępnia interfejs dla klientów do przeglądania i zatwierdzania lub odrzucania żądań dostępu do danych klienta.

Zrozumieć skrytę klienta:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Prowadzenie wykazu informacji poufnych

**Wskazówki:** Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają poufne informacje.

Jak tworzyć i używać tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemy izolowania przechowujące lub przetwarzające informacje poufne

**Wskazówki**: Implementowanie oddzielnych subskrypcji i/lub grup zarządzania do programowania, testowania i produkcji. Zasoby powinny być oddzielone przez sieci wirtualnej/podsieci, odpowiednio oznakowane i zabezpieczone w sieciowej sieciowej sieciowej lub zaporze platformy Azure. Zasoby przechowujące lub przetwarzające poufne dane powinny być izolowane. Użyj łącza prywatnego; wdrażanie programu Azure SQL Server wewnątrz sieci wirtualnej i łączenia się prywatnie przy użyciu prywatnych punktów końcowych.

Jak utworzyć dodatkowe subskrypcje platformy Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć grupy zarządzania:

https://docs.microsoft.com/azure/governance/management-groups/create

Jak tworzyć i używać tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak skonfigurować łącze prywatne dla bazy danych SQL usługi Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego przesyłania poufnych informacji

**Wskazówki:** Dla bazy danych SQL platformy Azure przechowywania lub przetwarzania poufnych informacji, oznacz bazę danych i powiązanych zasobów jako poufne przy użyciu tagów. Skonfiguruj łącze prywatne w połączeniu z tagami usługi sieciowej grupy zabezpieczeń w wystąpieniach bazy danych SQL platformy Azure, aby zapobiec eksfiltracji poufnych informacji.

W przypadku podstawowej platformy, która jest zarządzana przez firmę Microsoft, firma Microsoft traktuje całą zawartość klienta jako wrażliwą i dokłga mierze chroni przed utratą i ekspozycją danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje zestaw niezawodnych mechanizmów kontroli i możliwości ochrony danych.

Jak skonfigurować private link i sieciowe sieciowe, aby zapobiec eksfiltracji danych w wystąpieniach usługi Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview

Poznaj ochronę danych klientów na platformie Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Szyfrowanie wszystkich poufnych informacji podczas przesyłania

**Wskazówki:** Usługa Azure SQL Database zabezpiecza dane, szyfrując dane w ruchu za pomocą zabezpieczeń warstwy transportu. SQL Server wymusza szyfrowanie (SSL/TLS) przez cały czas dla wszystkich połączeń. Dzięki temu wszystkie dane są szyfrowane "w tranzycie" między klientem a serwerem, niezależnie od ustawienia Encrypt lub TrustServerCertificate w ciągu połączenia.

Poznaj szyfrowanie SQL platformy Azure w tranzycie:

https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Identyfikowanie poufnych danych za pomocą aktywnego narzędzia do wykrywania

**Wskazówki:** Użyj funkcji odnajdowania i klasyfikacji danych usługi Azure SQL Database. Odnajdowanie i klasyfikacja danych zapewnia zaawansowane funkcje wbudowane &amp; w usługę Azure SQL Database do odnajdowania, klasyfikowania, etykietowania chroniącego poufne dane w bazach danych.

Jak używać odnajdowania i klasyfikacji danych dla programu Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Sterowanie dostępem do zasobów za pomocą narzędzia Azure RBAC

**Wskazówki:** Użyj usługi Azure Active Directory (AAD) do uwierzytelniania i kontrolowania dostępu do wystąpień usługi Azure SQL Database.

Jak zintegrować program Azure SQL Server z usługą Azure Active Directory w celu uwierzytelniania:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication

Jak kontrolować dostęp w programie Sql Server platformy Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-control-access

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Wymuszanie kontroli dostępu za pomocą funkcji zapobiegania utracie danych opartych na hoście

**Wskazówki:** Firma Microsoft zarządza podstawową infrastrukturą usługi Azure SQL Database i wdrożyła ścisłe kontrole, aby zapobiec utracie lub ekspozycji danych klientów.

Poznaj ochronę danych klientów na platformie Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Szyfrowanie poufnych informacji w spoczynku

**Wskazówki:** Przezroczyste szyfrowanie danych (TDE) pomaga chronić usługę Azure SQL Database, wystąpienie zarządzanego sql platformy Azure i usługę Azure Data Warehouse przed zagrożeniem złośliwą aktywnością w trybie offline przez szyfrowanie danych w spoczynku. Ta technologia w czasie rzeczywistym szyfruje i odszyfrowuje magazynowaną bazę danych, skojarzone kopie zapasowe i pliki dzienników transakcji bez konieczności wprowadzania jakichkolwiek zmian w aplikacji. Technologia TDE jest domyślnie włączona dla wszystkich nowo wdrożonych baz danych usługi Azure SQL Database. Kluczem szyfrowania TDE może zarządzać firma Microsoft lub klient.

Jak zarządzać przezroczystym szyfrowaniem danych i używać własnych kluczy szyfrowania:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Rejestrowanie i ostrzeganie o zmianach w krytycznych zasobach platformy Azure

**Wskazówki:** Użyj usługi Azure Monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dotyczące tego, kiedy zmiany mają miejsce w wystąpieniach produkcyjnych bazy danych SQL platformy Azure i innych krytycznych lub powiązanych zasobów.

Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Uruchamianie zautomatyzowanych narzędzi do skanowania luk w zabezpieczeniach

**Wskazówki:** Włącz zaawansowane zabezpieczenia danych dla bazy danych SQL platformy Azure i postępuj zgodnie z zaleceniami usługi Azure Security Center dotyczącymi przeprowadzania ocen luk w zabezpieczeniach na serwerach SQL platformy Azure.

Jak uruchomić oceny luk w zabezpieczeniach w bazach danych SQL azure:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment

Jak włączyć zaawansowane zabezpieczenia danych:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security

Jak zaimplementować zalecenia dotyczące oceny luk w zabezpieczeniach usługi Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami oprogramowania innych firm

**Wskazówki**: Nie dotyczy; ten wskaźnik jest przeznaczony dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porównanie skanów luk w zabezpieczeniach typu back-to-back

**Wskazówki:** Włącz okresowe skanowanie cykliczne dla wystąpień usługi Azure SQL Database; spowoduje to skonfigurowanie oceny luk w zabezpieczeniach w celu automatycznego uruchamiania skanowania w bazie danych raz w tygodniu. Podsumowanie wyników skanowania zostanie wysłane na adres e-mail, który podasz. Porównaj wyniki, aby sprawdzić, czy luki zostały naprawione.

Jak wyeksportować raport oceny luk w zabezpieczeniach w usłudze Azure Security Center:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Użyj procesu oceny ryzyka, aby nadać priorytet usuwaniu wykrytych luk w zabezpieczeniach

**Wskazówki:** Użyj domyślnych ocen ryzyka (Secure Score) dostarczonych przez usługę Azure Security Center.

Poznaj bezpieczny wynik usługi Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie zapasami i zasobami](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Korzystanie z odnajdowania zasobów platformy Azure

**Wskazówki:** Użyj usługi Azure Resource Graph do wykonywania zapytań i odnajdywania wszystkich zasobów (w tym wystąpień programu Azure SQL Server) w ramach subskrypcji.  Upewnij się, że masz odpowiednie (odczytu) uprawnienia w dzierżawie i są w stanie wyliczyć wszystkie subskrypcje platformy Azure, a także zasoby w ramach subskrypcji.

Chociaż klasyczne zasoby platformy Azure mogą zostać wykryte za pomocą wykresu zasobów, zdecydowanie zaleca się tworzenie i używanie zasobów usługi Azure Resource Manager w przyszłości.

Jak tworzyć zapytania za pomocą programu Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak wyświetlić subskrypcje platformy Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Poznaj usługę Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="62-maintain-asset-metadata"></a>6.2: Obsługa metadanych zasobów

**Wskazówki:** Stosowanie tagów do zasobów platformy Azure, podając metadane logicznie zorganizować je w taksonomię.

Jak tworzyć i używać tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** W stosownych przypadkach należy używać tagowania, grup zarządzania i oddzielnych subskrypcji, aby organizować i śledzić zasoby. Regularnie uzgadniaj zapasy i upewnij się, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

Jak utworzyć dodatkowe subskrypcje platformy Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć grupy zarządzania:

https://docs.microsoft.com/azure/governance/management-groups/create

Jak tworzyć i używać tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Obsługa spisu zatwierdzonych zasobów i tytułów oprogramowania platformy Azure

**Wskazówki:** Definiowanie listy zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki:** Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Użyj usługi Azure Resource Graph do wykonywania zapytań/odnajdywanie zasobów w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak tworzyć zapytania za pomocą programu Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorowanie niezatwierdzonych aplikacji w zasobach obliczeniowych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Usuwanie niezatwierdzonych zasobów platformy Azure i aplikacji

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="68-use-only-approved-applications"></a>6.8: Używaj wyłącznie zatwierdzonych aplikacji

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="69-use-only-approved-azure-services"></a>6.9: Korzystaj tylko z zatwierdzonych usług platformy Azure

**Wskazówki:** Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Użyj usługi Azure Resource Graph do wykonywania zapytań/odnajdywanie zasobów w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odmówić określonego typu zasobu za pomocą usługi Azure Policy:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="610-implement-approved-application-list"></a>6.10: Wdrożenie listy zatwierdzonych wniosków

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla aplikacji działających na zasobach obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Ograniczanie użytkownikom możliwości interakcji z usługą Azure Resources Manager za pomocą skryptów

**Wskazówki:** Użyj dostępu warunkowego platformy Azure, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resource Manager, konfigurując "Blokuj dostęp" dla aplikacji "Microsoft Azure Management".

Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do usługi Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Ograniczenie możliwości wykonywania skryptów przez użytkowników w zasobach obliczeniowych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizycznie lub logicznie segregować aplikacje wysokiego ryzyka

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla usługi App Service lub zasobów obliczeniowych hosting aplikacji klasycznych lub internetowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Bezpieczna konfiguracja](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Ustanawianie bezpiecznych konfiguracji dla wszystkich zasobów platformy Azure

**Wskazówki:** Użyj zasad platformy Azure lub zaleceń usługi Azure Security Center dla serwerów/baz danych SQL platformy Azure, aby zachować konfiguracje zabezpieczeń dla wszystkich zasobów platformy Azure.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Ustanawianie bezpiecznych konfiguracji systemów operacyjnych

**Wskazówki**: Nie dotyczy; zalecenia przeznaczonego dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** Użyj zasad platformy Azure [odmów] i [wdrożyć, jeśli nie istnieje], aby wymusić bezpieczne ustawienia w zasobach platformy Azure.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Poznaj efekty zasad platformy Azure:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Obsługa bezpiecznych konfiguracji systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** Jeśli używasz niestandardowych definicji zasad platformy Azure, użyj usługi Azure DevOps lub Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

Jak przechowywać kod w usłudze Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentacja repozytorium platformy Azure:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpieczne przechowywanie obrazów niestandardowych systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Wdrażanie narzędzi do zarządzania konfiguracją systemu

**Wskazówki:** Użyj aliasów zasad platformy Azure w obszarze nazw "Microsoft.SQL", aby utworzyć niestandardowe zasady do ostrzegania, inspekcji i wymuszania konfiguracji systemu. Ponadto opracuj proces i potok do zarządzania wyjątkami zasad.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Wdrażanie automatycznego monitorowania konfiguracji usług platformy Azure

**Wskazówki:** Skorzystaj z usługi Azure Security Center do wykonywania skanowania według planu bazowego dla serwerów i baz danych SQL platformy Azure.

Jak skorygować zalecenia w usłudze Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Wdrożenie automatycznego monitorowania konfiguracji systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** Użyj usługi Azure Key Vault do przechowywania kluczy szyfrowania dla usługi Azure SQL Database Transparent Data Encryption (TDE).

Jak chronić poufne dane przechowywane w programie Sql Server platformy Azure i przechowywać klucze szyfrowania w usłudze Azure Key Vault:

https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** Użyj tożsamości zarządzanych, aby zapewnić usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory (AAD). Tożsamości zarządzane umożliwia uwierzytelnianie do dowolnej usługi, która obsługuje uwierzytelnianie usługi AAD, w tym usługi Azure Key Vault, bez żadnych poświadczeń w kodzie.

Samouczek: Aby uzyskać dostęp do usługi Azure SQL, użyj tożsamości zarządzanej przypisanej przez system Windows VM:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql

Jak skonfigurować tożsamości zarządzane:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Wyeliminuj niezamierzone narażenie na poświadczenia

**Wskazówki:** Zaimplementuj skaner poświadczeń w celu zidentyfikowania poświadczeń w kodzie. Skaner poświadczeń będzie również zachęcać do przenoszenia wykrytych poświadczeń do bezpieczniejszych lokalizacji, takich jak Usługa Azure Key Vault. 

Jak skonfigurować Skaner poświadczeń:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona przed złośliwym oprogramowaniem](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Korzystanie z centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych. Firma Microsoft obsługuje oprogramowanie chroniące przed złośliwym oprogramowaniem dla podstawowej platformy.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Przed skanowaniem plików, które mają zostać przesłane do zasobów platformy Azure nieoliczonych

**Wskazówki:** Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład usługa Azure App Service), jednak nie działa na zawartości klienta.

Wstępnie skanuj dowolną zawartość przekazywanych do nie obliczających zasobów platformy Azure, takich jak usługa App Service, magazyn usługi Data Lake, magazyn obiektów Blob, program Azure SQL Server itp. Firma Microsoft nie może uzyskać dostępu do danych użytkownika w takich przypadkach.

Poznaj oprogramowanie antywirusowe firmy Microsoft dla usług w chmurze i maszyn wirtualnych platformy Azure:https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Upewnij się, że oprogramowanie i podpisy chroniące przed złośliwym oprogramowaniem są aktualizowane

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych. Firma Microsoft obsługuje oprogramowanie chroniące przed złośliwym oprogramowaniem dla podstawowej platformy.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Odzyskiwanie danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnienie regularnych automatycznych kopii zapasowych

**Wskazówki:** Aby chronić firmę przed utratą danych, usługa Azure SQL Database automatycznie tworzy pełne kopie zapasowe bazy danych co tydzień, różnicowe kopie zapasowe bazy danych co 12 godzin i kopie zapasowe dziennika transakcji co 5 - 10 minut. Kopie zapasowe są przechowywane w magazynie RA-GRS przez co najmniej 7 dni dla wszystkich warstw usług. Wszystkie warstwy usług z wyjątkiem podstawowych pomocy technicznej konfigurowalny okres przechowywania kopii zapasowych dla przywracania punktu w czasie, do 35 dni.

Aby spełnić różne wymagania dotyczące zgodności, można wybrać różne okresy przechowywania dla cotygodniowych, miesięcznych i/lub rocznych kopii zapasowych. Zużycie magazynu zależy od wybranej częstotliwości tworzenia kopii zapasowych i okresu przechowywania.The storage consumption depends on the selected frequency of backups and the retention period(s).

Poznaj kopie zapasowe i ciągłość działania za pomocą programu Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-business-continuity

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Współdzielone

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Wykonywanie kompletnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki:** Usługa Azure SQL Database automatycznie tworzy kopie zapasowe bazy danych, które są przechowywane między 7 i 35 dni i używa usługi Azure dostęp do odczytu magazynu geograficznego (RA-GRS), aby upewnić się, że są one zachowywane, nawet jeśli centrum danych jest niedostępne. Te kopie zapasowe są tworzone automatycznie. W razie potrzeby włącz długoterminowe geob nadmiarowe kopie zapasowe dla baz danych SQL platformy Azure.

Jeśli używasz kluczy zarządzanych przez klienta do szyfrowania danych przezroczystych, upewnij się, że klucze są archiwizowane.

Opis kopii zapasowych w programie Sql Server platformy Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database

Jak wykonać kopię zapasową kluczy magazynu kluczy na platformie Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Sprawdzanie poprawności wszystkich kopii zapasowych, w tym kluczy zarządzanych przez klienta

**Wskazówki:** Zapewnij możliwość okresowego przywracania zawartości w ramach usługi Azure Backup. Jeśli to konieczne, przetestuj przywracanie zawartości do izolowanej sieci VLAN. Przetestuj przywracanie kopii zapasowych kluczy zarządzanych przez klienta.

Jak przywrócić klucze magazynu kluczy na platformie Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

Jak odzyskać kopie zapasowe usługi Azure SQL Database przy użyciu przywracania punktu w czasie:

https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zapewnienie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** Włącz usuwanie nietrwałe w usłudze Azure Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

Jak włączyć usuwanie nietrwałe w przechowalni kluczy:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Reagowanie na incydenty](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Tworzenie przewodnika po zdarzeniach

**Wskazówki**: Upewnij się, że istnieją pisemne plany reagowania na incydenty, które określają role personelu, a także fazy obsługi incydentów/zarządzania.

Jak skonfigurować automatyzacje przepływu pracy w usłudze Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Tworzenie procedury oceniania incydentów i ustalania priorytetów

**Wskazówki:** Centrum zabezpieczeń przypisuje ważność alertów, aby ułatwić ustalanie priorytetów kolejności, w jakiej uczestniczysz w każdym alertie, dzięki czemu gdy zasób zostanie naruszony, możesz uzyskać do niego od razu. Ważność opiera się na tym, jak pewny jest usługa Security Center w znalezieniu lub analityczna używana do wystawiania alertu, a także poziom zaufania, że za działaniem, które doprowadziło do alertu, było złośliwe intencje.

Alerty zabezpieczeń w usłudze Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-alerts-overview



**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki**: Przeprowadzaj ćwiczenia, aby przetestować możliwości reagowania na incydenty w systemach na regularnym rytmie. Zidentyfikować słabe punkty i luki oraz w razie potrzeby dokonać przeglądu planu.

Można zapoznać się z publikacją NIST: Przewodnik po testach, szkoleniach i programach ćwiczeń dla planów i możliwości IT:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Podaj dane kontaktowe zdarzeń bezpieczeństwa i konfiguruj powiadomienia o alertach dla incydentów bezpieczeństwa

**Wskazówki:** Informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do kontaktu się z Użytkownikiem, jeśli Centrum Microsoft Security Response Center (MSRC) odkryje, że dane użytkownika zostały uzyskiane przez osobę niezgodną z prawem lub nieautoryzowaną.

Jak ustawić kontakt zabezpieczeń usługi Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Włącz alerty bezpieczeństwa do systemu reagowania na incydenty

**Wskazówki:** Eksportuj alerty i zalecenia usługi Azure Security Center przy użyciu funkcji ciągłego eksportowania. Ciągły eksport umożliwia eksportowanie alertów i zaleceń ręcznie lub w sposób ciągły. Możesz użyć łącznika danych usługi Azure Security Center do przesyłania strumieniowego alertów do usługi Sentinel.

Jak skonfigurować eksport ciągły:

https://docs.microsoft.com/azure/security-center/continuous-export

Jak przesyłać strumieniowo alerty do usługi Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzacja reakcji na alerty bezpieczeństwa

**Wskazówki:** Użyj funkcji automatyzacji przepływu pracy w usłudze Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Aplikacji logiki" w alertach i zaleceniach dotyczących zabezpieczeń.

Jak skonfigurować automatyzację przepływu pracy i aplikacje logiki:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola bezpieczeństwa: Testy penetracyjne i Ćwiczenia zespołu Czerwonego](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Przeprowadzaj regularne testy penetracyjne zasobów platformy Azure i zapewnij korygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń w ciągu 60 dni

**Wskazówki:** Postępuj zgodnie z regułami zaangażowania firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszają zasad firmy Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Więcej informacji na temat strategii firmy Microsoft i wykonywania testów red teaming i testów penetracji witryn na żywo w oparciu o zarządzał infrastrukturą, usługami i aplikacjami firmy Microsoft w chmurze:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Współdzielone

## <a name="next-steps"></a>Następne kroki

- Zobacz [wzorzec zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Dowiedz się więcej o [planach bazowych zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
