---
title: Linia bazowa zabezpieczeń platformy Azure
description: Podstawy zabezpieczeń platformy Azure dla Azure SQL Database i wystąpienia zarządzanego Azure SQL
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ce297b436ce79a4a0a14d0f6e9ad900de61a8dc9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90885214"
---
# <a name="azure-security-baseline-for-azure-sql-database--sql-managed-instance"></a>Podstawowa usługa Azure Security dla Azure SQL Database & wystąpienie zarządzane SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Podstawą zabezpieczeń platformy Azure dla Azure SQL Database są zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia.

Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](https://docs.microsoft.com/azure/security/benchmarks/overview), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure z naszymi najlepszymi wskazówkami.

Aby uzyskać więcej informacji, zobacz [podstawy zabezpieczeń platformy Azure — omówienie](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: zabezpieczenia sieci](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure na Virtual Network

**Wskazówki**: możesz włączyć link prywatny platformy Azure, aby umożliwić dostęp do usług Azure PaaS Services (na przykład SQL Database) oraz hostowanych usług klienta i partnerskich platformy Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu.

Aby zezwolić na dostęp do Azure SQL Database, użyj tagów usługi SQL, aby zezwolić na ruch wychodzący za pomocą sieciowych grup zabezpieczeń.

Reguły sieci wirtualnej umożliwiają Azure SQL Database tylko akceptowanie komunikacji wysyłanej z wybranych podsieci w sieci wirtualnej.

Jak skonfigurować link prywatny dla Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

Jak korzystać z punktów końcowych usługi sieci wirtualnej i reguł dla serwerów:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: Użyj Azure Security Center i skoryguj zalecenia dotyczące ochrony sieci dla podsieci, do której Azure SQL Database został wdrożony.

W przypadku usługi Azure Virtual Machines (VM), która będzie łączyć się z Azure SQL Database, Włącz dzienniki przepływu sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) dla sieciowych grup zabezpieczeń ochrony tych maszyn wirtualnych i Wyślij dzienniki do konta usługi Azure Storage w celu przeprowadzenia inspekcji ruchu.

Możesz również wysłać dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego Log Analytics i użyć Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Jak włączyć i używać Analiza ruchu:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla usługi Azure Apps lub zasobów obliczeniowych obsługujących aplikacje sieci Web.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: Włączanie standardu DDoS Protection w sieciach wirtualnych skojarzonych z Azure SQL Database w celu ochrony przed rozproszonymi atakami typu "odmowa usługi". Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi lub nieużywanymi adresami IP.

Jak skonfigurować ochronę DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Opis Azure Security Center zintegrowanej analizy zagrożeń:

https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: rejestrowanie pakietów sieciowych i dzienników przepływów

**Wskazówki**: dla usługi Azure Virtual Machines (maszyny wirtualne), które będą łączyć się z wystąpieniem Azure SQL Database, Włącz dzienniki przepływu sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) dla sieciowych grup zabezpieczeń ochrony tych maszyn wirtualnych i Wyślij dzienniki do konta usługi Azure Storage na potrzeby inspekcji ruchu. Jeśli jest to wymagane do badania nietypowego działania, Włącz funkcję przechwytywania pakietów Network Watcher.

Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak włączyć Network Watcher:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Wdróż systemy zapobiegania wykrywaniu dostępu do sieci/dostępu intruzów (identyfikatory/adresy IP)

**Wskazówki**: Włącz zaawansowaną ochronę przed zagrożeniami (ATP) dla Azure SQL Database.  Użytkownicy otrzymują alerty o podejrzanych działaniach związanych z bazą danych, potencjalnych luk w zabezpieczeniach i atakach iniekcji SQL, a także o nietypowych wzorcach dostępu do bazy danych i zapytań. Zaawansowana ochrona przed zagrożeniami integruje także alerty z Azure Security Center.

Zrozumienie i używanie zaawansowanej ochrony przed zagrożeniami dla Azure SQL Database: https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla usługi Azure Apps lub zasobów obliczeniowych obsługujących aplikacje sieci Web.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi sieci wirtualnej, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapory platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

W przypadku korzystania z punktów końcowych usługi dla Azure SQL Database jest wymagane wychodzące Azure SQL Database publicznych adresów IP: grupy zabezpieczeń sieci (sieciowych grup zabezpieczeń) muszą być otwarte, aby Azure SQL Database adresy IP w celu umożliwienia łączności. Można to zrobić za pomocą tagów usługi sieciowej grupy zabezpieczeń dla Azure SQL Database.

Informacje o tagach usługi z punktami końcowymi usługi dla Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations

Zrozumienie i używanie tagów usługi:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie konfiguracji zabezpieczeń sieci dla Azure SQL Database przy użyciu Azure Policy. Możesz użyć przestrzeni nazw "Microsoft. SQL" do definiowania niestandardowych definicji zasad lub użyć dowolnych wbudowanych definicji zasad przeznaczonych do ochrony sieci serwera. Przykładem odpowiednich wbudowanych zasad zabezpieczeń sieci dla serwera będzie: "SQL Database powinien korzystać z punktu końcowego usługi sieci wirtualnej".

Korzystając z planów platformy Azure, można uprościć duże wdrożenia platformy Azure przez pakowanie kluczowych artefaktów środowiska, takich jak szablony zarządzania zasobami platformy Azure, kontrola dostępu oparta na rolach (RBAC) i zasady, w ramach jednej definicji planu. Łatwo stosuj plan do nowych subskrypcji i środowisk, a następnie dostosuj kontrolę i zarządzanie przy użyciu wersji.

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak utworzyć Azure Blueprint: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: używanie tagów dla sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. W przypadku poszczególnych reguł sieciowej grupy zabezpieczeń Użyj pola "Description", aby określić potrzeby biznesowe i/lub czas trwania (itp.) dla reguł zezwalających na ruch do/z sieci.

Użyj dowolnych wbudowanych definicji Azure Policy związanych ze znakiem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o istniejących nieoznakowanych zasobach.

Możesz użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów.

Tworzenie i używanie tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z serwerem. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Jak utworzyć alerty w Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: rejestrowanie i monitorowanie](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu

**Wskazówki**: Firma Microsoft przechowuje źródła czasu dla zasobów platformy Azure. Możesz zaktualizować synchronizację czasu dla wdrożeń obliczeniowych.

Jak skonfigurować synchronizację czasu dla zasobów obliczeniowych platformy Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: Włączanie inspekcji dla Azure SQL Database do śledzenia zdarzeń bazy danych i zapisywania ich w dzienniku inspekcji na koncie usługi Azure Storage, log Analytics obszarze roboczym lub Event Hubs.

Ponadto można przesyłać dane telemetryczne diagnostyki usługi Azure SQL do Azure SQL Analytics, rozwiązanie w chmurze, które monitoruje wydajność Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL na dużą skalę i w wielu subskrypcjach. Może pomóc zbierać i wizualizować Azure SQL Database metryki wydajności i ma wbudowaną analizę na potrzeby rozwiązywania problemów z wydajnością.

Jak skonfigurować inspekcję Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

Jak zbierać dzienniki platformy i metryki z Azure Monitor:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging

Jak przesłać strumieniowo diagnostykę do Azure SQL Analytics:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Włącz inspekcję na serwerze i wybierz lokalizację przechowywania dzienników inspekcji (Azure storage, log Analytics lub centrum zdarzeń).

Jak włączyć inspekcję dla Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: nie dotyczy; Ten test porównawczy jest przeznaczony dla zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: podczas przechowywania dzienników Azure SQL Database w obszarze roboczym log Analytics Ustaw okres przechowywania dziennika zgodnie z regulacjami zgodności w organizacji.

Jak ustawić parametry przechowywania dziennika:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: analizowanie i monitorowanie dzienników pod kątem nietypowych zachowań i regularne przeglądy wyników. Użyj zaawansowanej ochrony przed zagrożeniami w usłudze Azure Security Center, aby otrzymywać alerty dotyczące nietypowej aktywności związanej z wystąpieniem Azure SQL Database. Alternatywnie możesz skonfigurować alerty na podstawie wartości metryk lub wpisów dziennika aktywności platformy Azure związanych z wystąpieniami Azure SQL Database.

Zapoznaj się z zaawansowaną ochroną przed zagrożeniami i alertami dla Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Jak skonfigurować alerty niestandardowe dla Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: Włączanie alertów dla nietypowego działania

**Wskazówki**: Użyj Azure Security Center zaawansowanej ochrony przed zagrożeniami dla Azure SQL Database do monitorowania i generowania alertów dotyczących nietypowej aktywności. Włącz usługę Azure Defender dla programu SQL dla baz danych SQL. Usługa Azure Defender dla programu SQL zawiera funkcje umożliwiające wykorzystanie i łagodzenie potencjalnych luk w zabezpieczeniach bazy danych oraz wykrywanie nietypowych działań, które mogą wskazywać na zagrożenie dla bazy danych.

Zapoznaj się z zaawansowaną ochroną przed zagrożeniami i alertami dla Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Jak włączyć usługę Azure Defender dla programu SQL dla Azure SQL Database:

https://docs.microsoft.com/azure/azure-sql/database/azure-defender-for-sql

Jak zarządzać alertami w Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie dotyczy; w przypadku Azure SQL Database rozwiązanie chroniące przed złośliwym kodem jest zarządzane przez firmę Microsoft na podstawowej platformie.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: nie dotyczy; Rejestrowanie DNS nie ma zastosowania do Azure SQL Database.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: nie dotyczy; Inspekcja w wierszu polecenia nie ma zastosowania do Azure SQL Database.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: tożsamość i Access Control](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: Azure Active Directory (Azure AD) ma wbudowane role, które muszą być jawnie przypisane i są queryable. Za pomocą modułu Azure AD PowerShell można wykonywać zapytania ad hoc w celu odnajdywania kont należących do grup administracyjnych.

Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: Azure Active Directory nie ma koncepcji domyślnych haseł. W przypadku inicjowania obsługi wystąpienia Azure SQL Database zaleca się integrację uwierzytelniania z Azure Active Directory.

Jak skonfigurować uwierzytelnianie Azure Active Directory i zarządzać nim przy użyciu usługi Azure SQL:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie zasad i procedur dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

Informacje o tożsamości i dostępie Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: nie dotyczy; Chociaż można skonfigurować uwierzytelnianie Azure Active Directory w celu integracji z usługą Azure SQL Database, logowanie jednokrotne nie jest obsługiwane.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: włączanie usługi Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA) i przestrzeganie Azure Security Center zaleceń dotyczących zarządzania tożsamościami i dostępem.

Jak włączyć usługę MFA na platformie Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak monitorować tożsamość i dostęp w Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj stacji roboczej dostępu uprzywilejowanego (dostępem uprzywilejowanym) z usługą Multi-Factor Authentication MFA skonfigurowaną w celu logowania się i konfigurowania zasobów platformy Azure.

Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak włączyć usługę MFA na platformie Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: dziennik i alert dotyczący podejrzanego działania z kont administracyjnych

**Wskazówki**: używanie raportów zabezpieczeń Azure Active Directory do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku.

Użyj zaawansowanej ochrony przed zagrożeniami Azure SQL Database, aby wykrywać anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

Identyfikowanie użytkowników usługi Azure AD oflagowanych w celu działania ryzykownego:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Jak monitorować działania związane z tożsamościami i dostępem użytkowników w Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

Przejrzyj zaawansowaną ochronę przed zagrożeniami i potencjalne alerty:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby zezwolić portalowi i dostępowi do usługi Azure Resource Management tylko z określonych logicznych grup zakresów adresów IP lub krajów/regionów.

Jak skonfigurować nazwane lokalizacje na platformie Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: utwórz administratora Azure Active Directory (Azure AD) dla serwera.

Jak skonfigurować uwierzytelnianie Azure Active Directory i zarządzać nim przy użyciu usługi Azure SQL:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

Jak utworzyć i skonfigurować wystąpienie usługi Azure AD:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Azure Active Directory (Azure AD) zawiera dzienniki ułatwiające wykrywanie starych kont. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają stały dostęp.

Jak korzystać z przeglądów dostępu do tożsamości platformy Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych kont

**Wskazówki**: konfigurowanie uwierzytelniania Azure Active Directory (Azure AD) przy użyciu usługi Azure SQL i tworzenie ustawień diagnostycznych dla Azure Active Directory kont użytkowników, wysyłanie dzienników inspekcji i dzienników logowania do obszaru roboczego log Analytics. Skonfiguruj żądane alerty w obszarze roboczym Log Analytics.

Jak skonfigurować uwierzytelnianie Azure Active Directory i zarządzać nim przy użyciu usługi Azure SQL:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

Jak zintegrować dzienniki aktywności platformy Azure z Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: usługa Azure Active Directory (Azure AD) umożliwia ochronę tożsamości i wykrywanie zagrożeń, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. Dodatkowo można pozyskać dane do usługi Azure wskaźnikowej na potrzeby dalszej analizy.

Jak wyświetlić informacje o ryzyku usługi Azure AD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: w scenariuszach pomocy technicznej, w których firma Microsoft musi uzyskać dostęp do danych klientów, usługa Azure skrytka klienta udostępnia interfejs umożliwiający klientom przeglądanie i zatwierdzanie lub odrzucanie żądań dostępu do danych klienta.

Opis Skrytka klienta:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.

Tworzenie i używanie tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie oddzielnych subskrypcji i/lub grup zarządzania na potrzeby tworzenia, testowania i produkcji. Zasoby powinny być oddzielone sieciami wirtualnymi/podsieć, odpowiednio oznakowane i zabezpieczone w ramach sieciowej grupy zabezpieczeń lub zapory platformy Azure. Zasoby przechowujące lub przetwarzające dane poufne powinny być izolowane. Użyj linku prywatnego; Wdróż Azure SQL Database wewnątrz sieci wirtualnej i łącz się prywatnie przy użyciu prywatnych punktów końcowych.

Jak utworzyć dodatkowe subskrypcje platformy Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć Grupy zarządzania:

https://docs.microsoft.com/azure/governance/management-groups/create

Tworzenie i używanie tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak skonfigurować link prywatny dla Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: dla baz danych w Azure SQL Database przechowywania lub przetwarzania informacji poufnych, Oznacz bazę danych i powiązane zasoby jako poufne przy użyciu tagów. Skonfiguruj prywatne łącze w połączeniu ze znacznikami usługi sieciowych grup zabezpieczeń w wystąpieniach Azure SQL Database, aby zapobiec eksfiltracji informacji poufnych.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

Jak skonfigurować link prywatny i sieciowych grup zabezpieczeń, aby zapobiec eksfiltracji danych w wystąpieniach Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview

Zrozumienie ochrony danych klientów na platformie Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: Azure SQL Database zabezpiecza dane przez szyfrowanie danych w ruchu z Transport Layer Security. SQL Database wymusza szyfrowanie (SSL/TLS) przez cały czas dla wszystkich połączeń. Gwarantuje to, że wszystkie dane są szyfrowane "podczas przesyłania" między klientem i serwerem niezależnie od ustawienia szyfrowania lub TrustServerCertificate w parametrach połączenia.

Informacje o szyfrowaniu usługi Azure SQL w tranzycie:

https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: Użyj funkcji odnajdywania i klasyfikowania danych Azure SQL Database. Funkcja odnajdywania i klasyfikowania danych zapewnia zaawansowane funkcje wbudowane w Azure SQL Database na potrzeby odnajdywania, klasyfikowania i etykietowania &amp; ochrony poufnych danych w bazach danych.

Jak używać funkcji odnajdywania i klasyfikowania danych Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów

**Wskazówki**: Użyj Azure Active Directory (Azure AD) do uwierzytelniania i kontrolowania dostępu do wystąpień Azure SQL Database.

Jak zintegrować Azure SQL Database z Azure Active Directory na potrzeby uwierzytelniania:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication

Jak kontrolować dostęp w Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-control-access

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: Firma Microsoft zarządza podstawową infrastrukturą dla Azure SQL Database i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

Zrozumienie ochrony danych klientów na platformie Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: przezroczyste szyfrowanie danych (TDE) pomaga chronić Azure SQL Database, wystąpienie zarządzane SQL Azure i magazyn danych Azure przed zagrożeniem złośliwego działania w trybie offline przez szyfrowanie danych przechowywanych w spoczynku. Ta technologia w czasie rzeczywistym szyfruje i odszyfrowuje magazynowaną bazę danych, skojarzone kopie zapasowe i pliki dzienników transakcji bez konieczności dokonywania jakichkolwiek zmian w aplikacji. Domyślnie TDE jest włączony dla wszystkich nowo wdrożonych baz danych w wystąpieniu zarządzanym SQL Database i SQL. Klucz szyfrowania TDE może być zarządzany przez firmę Microsoft lub klienta.

Jak zarządzać przezroczystym szyfrowaniem danych i korzystać z własnych kluczy szyfrowania:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dla sytuacji, w których zmiany są wprowadzane do wystąpień produkcyjnych Azure SQL Database i innych krytycznych lub powiązanych zasobów.

Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: Włącz usługę Azure Defender dla programu SQL dla Azure SQL Database i postępuj zgodnie z zaleceniami Azure Security Center na temat przeprowadzania oceny luk w zabezpieczeniach na serwerach.

Jak uruchomić oceny luk w zabezpieczeniach w Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment

Jak włączyć usługę Azure Defender dla programu SQL:

https://docs.microsoft.com/azure/azure-sql/database/azure-defender-for-sql

Jak zaimplementować zalecenia dotyczące oceny luk w zabezpieczeniach Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Wdróż zautomatyzowane rozwiązanie do zarządzania poprawkami oprogramowania innych firm

**Wskazówki**: nie dotyczy; Ten test porównawczy jest przeznaczony dla zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: Włącz okresowe skanowanie cykliczne dla wystąpień Azure SQL Database; spowoduje to skonfigurowanie oceny luk w zabezpieczeniach w celu automatycznego uruchamiania skanowania bazy danych na tydzień. Podsumowanie wyników skanowania zostanie wysłane na adresy e-mail, które zapewniasz. Porównaj wyniki, aby sprawdzić, czy luki zostały skorygowane.

Jak wyeksportować raport oceny luk w zabezpieczeniach w Azure Security Center:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: Użyj domyślnych ocen ryzyka ("Secure Score") dostarczonych przez Azure Security Center.

Informacje o usłudze Azure Security Center Secure Score:

https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Spis i zarządzanie zasobami](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Użyj odnajdywania zasobów platformy Azure

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań i odnajdywania wszystkich zasobów (w tym Azure SQL Database) w ramach subskrypcji.  Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i że można wyliczyć wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

Mimo że klasyczne zasoby platformy Azure mogą zostać odnalezione za pośrednictwem grafu zasobów, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów do przodu.

Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak wyświetlić subskrypcje platformy Azure: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Opis kontroli RBAC platformy Azure: https://docs.microsoft.com/azure/role-based-access-control/overview

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

Tworzenie i używanie tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: przechowywanie spisu zatwierdzonych zasobów platformy Azure i tytułów oprogramowania

**Wskazówki**: Definiowanie listy zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Użyj grafu zasobów platformy Azure, aby wykonywać zapytania/odnajdywać zasoby w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak tworzyć zapytania za pomocą usługi Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Użyj grafu zasobów platformy Azure, aby wykonywać zapytania/odnajdywać zasoby w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odmówić określonego typu zasobu Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="610-implement-approved-application-list"></a>6,10: Zaimplementuj listę zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji uruchamianych na zasobach obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z usługi Azure Resources za pośrednictwem skryptów

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla App Service lub zasobów obliczeniowych obsługujących aplikacje pulpitu lub sieci Web.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: bezpieczna konfiguracja](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy lub Azure Security Center zaleceń dotyczących Azure SQL Database, aby zachować konfiguracje zabezpieczeń dla wszystkich zasobów platformy Azure.

Jak skonfigurować Azure Policy i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.

Jak skonfigurować Azure Policy i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Opis efektów Azure Policy:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji Azure Policy, Użyj usługi Azure DevOps lub Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

Jak przechowywać kod w usłudze Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentacja Azure Repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją systemu

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. SQL", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

Jak skonfigurować Azure Policy i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla usług platformy Azure

**Wskazówki**: skorzystaj z Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla Azure SQL Database.

Jak skorygować zalecenia w Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: Użyj Azure Key Vault do przechowywania kluczy szyfrowania dla Azure SQL Database transparent Data Encryption (TDE).

Jak chronić poufne dane przechowywane w Azure SQL Database i przechowywać klucze szyfrowania w Azure Key Vault:

https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: Użyj tożsamości zarządzanych, aby zapewnić usługom platformy Azure automatyczną tożsamość zarządzaną w usłudze Azure Active Directory (Azure AD). Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Azure Key Vault, bez żadnych poświadczeń w kodzie.

Samouczek: używanie tożsamości zarządzanej przypisanej przez system Windows VM do uzyskiwania dostępu do usługi Azure SQL:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql

Jak skonfigurować tożsamości zarządzane:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń zachęca również do przemieszczania odnalezionych poświadczeń do bardziej bezpiecznych lokalizacji, takich jak Azure Key Vault.

Jak skonfigurować skaner poświadczeń: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Obrona złośliwego oprogramowania](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych. Firma Microsoft obsługuje oprogramowanie chroniące przed złośliwym kodem dla podstawowej platformy.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład Azure App Service), ale nie jest ono uruchamiane w treści klienta.

Przed przeskanowaniem zawartość przekazywana do zasobów platformy Azure, które nie są obliczeniowe, takich jak App Service, Data Lake Storage, Blob Storage, Azure SQL Database itp. Firma Microsoft nie może uzyskać dostępu do danych w tych wystąpieniach.

Informacje na temat ochrony przed złośliwym oprogramowaniem firmy Microsoft Cloud Services i Virtual Machines: https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych. Firma Microsoft obsługuje oprogramowanie chroniące przed złośliwym kodem dla podstawowej platformy.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odzyskiwanie danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: aby chronić swoją firmę przed utratą danych, Azure SQL Database automatycznie tworzy pełne kopie zapasowe bazy danych co tydzień, różnicowe kopie zapasowe bazy danych co 12 godzin oraz kopie zapasowe dziennika transakcji co 5-10 minut. Kopie zapasowe są przechowywane w magazynie RA-GRS przez co najmniej 7 dni dla wszystkich warstw usług. Wszystkie warstwy usług, z wyjątkiem pomoc techniczna Basic konfigurowalnego okresu przechowywania kopii zapasowych dla przywracania do punktu w czasie, do 35 dni.

Aby spełnić inne wymagania dotyczące zgodności, można wybrać różne okresy przechowywania dla cotygodniowych, comiesięcznych i/lub corocznych kopii zapasowych. Użycie magazynu zależy od wybranej częstotliwości wykonywania kopii zapasowych i okresów przechowywania.

Informacje o kopiach zapasowych i ciągłości działania Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-business-continuity

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: Azure SQL Database automatycznie tworzy kopie zapasowe bazy danych, które znajdują się w przedziale od 7 do 35 dni, i korzysta z magazynu geograficznie nadmiarowego platformy Azure do odczytu (RA-GRS), aby upewnić się, że są one zachowywane, nawet jeśli centrum danych jest niedostępne. Te kopie zapasowe są tworzone automatycznie. W razie potrzeby Włącz długoterminowe geograficznie nadmiarowe kopie zapasowe baz danych Azure SQL.

W przypadku korzystania z kluczy zarządzanych przez klienta do Transparent Data Encryption upewnij się, że tworzone są kopie zapasowe kluczy.

Informacje o kopiach zapasowych w Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database

Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: Zapewnij okresowe wykonywanie operacji przywracania danych w ramach Azure Backup. W razie potrzeby przetestuj odzyskiwanie zawartości do izolowanej sieci VLAN. Testowanie przywracania kopii zapasowych kluczy zarządzanych przez klienta.

Jak przywrócić klucze magazynu kluczy na platformie Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

Jak odzyskać Azure SQL Database kopie zapasowe przy użyciu przywracania do punktu w czasie:

https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Włącz usuwanie nietrwałe w Azure Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

Jak włączyć usuwanie nietrwałe w Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odpowiedź na zdarzenia](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Upewnij się, że istnieją plany odpowiedzi na zdarzenia, które definiują role pracowników, a także fazy obsługi zdarzeń/zarządzania.

Jak skonfigurować automatyzację przepływu pracy w ramach Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do alertów, ułatwiając określanie priorytetów kolejności, w której uczestniczy każdy alert, dzięki czemu w przypadku naruszenia zabezpieczeń zasobów można od razu uzyskać dostęp. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu.

Alerty zabezpieczeń w Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy i popraw plan zgodnie z wymaganiami.

Możesz zapoznać się z publikacją NIST: Przewodnik po testach, szkoleniu i wykonywaniu programów dla planów i możliwości IT:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę.

Jak ustawić Azure Security Center kontaktu zabezpieczeń:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do kontrolki wskaźnikowej.

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

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: testy penetracji i czerwone ćwiczenia zespołu](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń w ciągu 60 dni

**Wskazówki**: Postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Aby uzyskać więcej informacji na temat strategii firmy Microsoft i wykonywania testów z zakresu tworzenia zespołu Red-site i testowania aplikacji na żywo, należy wykonać następujące działania: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
