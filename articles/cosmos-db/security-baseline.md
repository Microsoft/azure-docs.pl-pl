---
title: Podstawa zabezpieczeń platformy Azure dla Cosmos DB
description: Podstawa zabezpieczeń platformy Azure dla Cosmos DB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 961dae4cd35c7529a5661d1ddc7d0d1f6e4db634
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029119"
---
# <a name="azure-security-baseline-for-cosmos-db"></a>Podstawa zabezpieczeń platformy Azure dla Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Podstawą zabezpieczeń platformy Azure dla Cosmos DB są zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia.

Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](../security/benchmarks/overview.md), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure z naszymi najlepszymi wskazówkami.

Aby uzyskać więcej informacji, zobacz [podstawy zabezpieczeń platformy Azure — omówienie](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: zabezpieczenia sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure na Virtual Network

**Wskazówki**: korzystając z linku prywatnego platformy Azure, można nawiązać połączenie z kontem usługi Azure Cosmos za pośrednictwem prywatnego punktu końcowego. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Istnieje również możliwość zmniejszenia ryzyka związanego z eksfiltracji danych przez skonfigurowanie ścisłego zestawu reguł ruchu wychodzącego w sieciowej grupie zabezpieczeń (sieciowej grupy zabezpieczeń) i skojarzenie tego sieciowej grupy zabezpieczeń z podsiecią.

Możesz również użyć punktów końcowych usługi do zabezpieczenia konta usługi Azure Cosmos. Po włączeniu punktu końcowego usługi można skonfigurować konta usługi Azure Cosmos, aby zezwolić na dostęp tylko z określonej podsieci sieci wirtualnej platformy Azure. Po włączeniu punktu końcowego usługi Azure Cosmos DB można ograniczyć dostęp do konta usługi Azure Cosmos z połączeniami z podsieci w sieci wirtualnej.

Możesz również zabezpieczyć dane przechowywane na koncie usługi Azure Cosmos za pomocą zapór IP. Azure Cosmos DB obsługuje kontrolę dostępu opartą na protokole IP na potrzeby obsługi zapory przychodzącej. Zaporę IP można ustawić na koncie usługi Azure Cosmos za pomocą Azure Portal, Azure Resource Manager szablonów lub za pośrednictwem interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.

Omówienie linku prywatnego platformy Azure: https://docs.microsoft.com/azure/private-link/private-link-overview

Jak skonfigurować prywatny punkt końcowy dla Azure Cosmos DB:  https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints 

Jak utworzyć sieciową grupę zabezpieczeń z konfiguracją zabezpieczeń:  https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Jak skonfigurować zaporę IP w Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-firewall

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: Użyj Azure Security Center i postępuj zgodnie z zaleceniami dotyczącymi ochrony sieci, aby pomóc w zabezpieczeniu zasobów sieciowych związanych z kontem usługi Azure Cosmos.

Gdy maszyny wirtualne są wdrażane w tej samej sieci wirtualnej co konto usługi Azure Cosmos, można użyć sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) w celu zmniejszenia ryzyka eksfiltracji danych. Włącz dzienniki przepływu sieciowej grupy zabezpieczeń i Wyślij dzienniki do konta usługi Azure Storage na potrzeby inspekcji ruchu. Możesz również wysłać dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego Log Analytics i użyć Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak włączyć i używać Analiza ruchu: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: Użyj funkcji udostępniania zasobów między źródłami (CORS), aby umożliwić aplikacji sieci Web działającej w ramach jednej domeny dostęp do zasobów w innej domenie. Przeglądarki sieci Web implementują ograniczenie zabezpieczeń nazywane zasadami tego samego źródła, które uniemożliwiają stronie sieci Web wywoływanie interfejsów API w innej domenie. Jednak mechanizm CORS zapewnia bezpieczny sposób, aby umożliwić domenie pochodzenia wywoływanie interfejsów API w innej domenie. Po włączeniu obsługi mechanizmu CORS dla konta usługi Azure Cosmos są oceniane tylko uwierzytelnione żądania, aby określić, czy są dozwolone zgodnie z określonymi regułami.

Skonfiguruj współużytkowanie zasobów między źródłami: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: Użyj zaawansowanej ochrony przed zagrożeniami (ATP) dla Azure Cosmos DB. Usługa ATP dla Azure Cosmos DB stanowi dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont usługi Azure Cosmos lub korzystania z nich. Ta warstwa ochrony pozwala na rozwiązywanie zagrożeń i integrowanie ich z systemami monitorowania zabezpieczeń centralnych.

Włącz DDoS Protection standard w sieciach wirtualnych skojarzonych z wystąpieniami Azure Cosmos DB, aby chronić przed atakami DDoS. Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi lub nieużywanymi adresami IP.

Jak skonfigurować Azure Cosmos DB zaawansowanej ochrony przed zagrożeniami: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Jak skonfigurować ochronę DDoS: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Opis Azure Security Center zintegrowanej analizy zagrożeń: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: rejestrowanie pakietów sieciowych i dzienników przepływów

**Wskazówki**: Włączanie dzienników przepływu sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) i wysyłanie dzienników do konta magazynu na potrzeby inspekcji ruchu. Dzienniki przepływu sieciowej grupy zabezpieczeń można wysyłać do obszaru roboczego Log Analytics i używać Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak włączyć i używać Analiza ruchu: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Wdróż systemy zapobiegania wykrywaniu dostępu do sieci/dostępu intruzów (identyfikatory/adresy IP)

**Wskazówki**: Użyj zaawansowanej ochrony przed zagrożeniami (ATP) dla Azure Cosmos DB. Usługa ATP dla Azure Cosmos DB stanowi dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont usługi Azure Cosmos lub korzystania z nich. Ta warstwa ochrony pozwala na rozwiązywanie zagrożeń i integrowanie ich z systemami monitorowania zabezpieczeń centralnych. 

Jak skonfigurować Cosmos DB zaawansowanej ochrony przed zagrożeniami: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: nie dotyczy; zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: dla zasobów, które wymagają dostępu do konta usługi Azure Cosmos, użyj tagów usługi Virtual Network, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapory platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. AzureCosmosDB) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

Aby uzyskać więcej informacji na temat używania tagów usługi: https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych za pomocą Azure Policy. Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft.DocumentDB" i "Microsoft. Network", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci wystąpień Azure Cosmos DB. Możesz również używać wbudowanych definicji zasad dla Azure Cosmos DB, takich jak:

- Wdróż zaawansowaną ochronę przed zagrożeniami dla kont Cosmos DB

- Cosmos DB powinien używać punktu końcowego usługi sieci wirtualnej

Możesz również użyć planów platformy Azure, aby uprościć wdrożenia platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów środowiska, takich jak szablony Azure Resource Manager, kontrola dostępu oparta na rolach (RBAC) na platformie Azure i zasady w ramach jednej definicji planu. Możesz łatwo zastosować plan do nowych subskrypcji, środowisk i dostrajania kontroli i zarządzania przy użyciu wersji.

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak utworzyć Azure Blueprint: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Użyj tagów dla zasobów sieciowych skojarzonych z wdrożeniem Azure Cosmos DB, aby logicznie zorganizować je w taksonomię.

Tworzenie i używanie tagów: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z wystąpieniami Azure Cosmos DB. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych. 

Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Jak utworzyć alerty w Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu

**Wskazówki**: Firma Microsoft utrzymuje źródło czasu używane dla zasobów platformy Azure, takie jak Azure Cosmos DB sygnatury czasowe w dziennikach.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: pobieranie dzienników przy użyciu Azure monitor do agregowania danych zabezpieczeń wygenerowanych przez Azure Cosmos DB. W ramach Azure Monitor należy używać Log Analytics obszarów roboczych do wykonywania zapytań i wykonywania analiz oraz używania kont magazynu do przechowywania długoterminowego/archiwizowania. Alternatywnie możesz włączyć i zarejestrować dane na platformie Azure wskaźnikowej lub zdarzeniu zabezpieczeń innych firm (SIEM). 

Jak włączyć dzienniki diagnostyczne dla Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/logging

Jak dołączyć wskaźnik na platformie Azure: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Włączanie ustawień diagnostycznych dla Azure Cosmos DB i wysyłanie dzienników do log Analytics obszaru roboczego lub konta magazynu. Ustawienia diagnostyczne w Azure Cosmos DB są używane do zbierania dzienników zasobów. Te dzienniki są przechwytywane na żądanie i są również określane jako "dzienniki płaszczyzny danych". Niektóre przykłady operacji na płaszczyźnie danych obejmują usuwanie, wstawianie i odczytywanie. Możesz również włączyć ustawienia diagnostyki dziennika aktywności platformy Azure i wysłać je do tego samego obszaru roboczego Log Analytics.

Jak włączyć ustawienia diagnostyczne dla Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/logging

Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: w Azure monitor ustawić okres przechowywania dziennika dla log Analytics obszarów roboczych skojarzonych z wystąpieniami Azure Cosmos DB zgodnie z regulacjami zgodności w organizacji.

Jak ustawić parametry przechowywania dziennika: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: możesz wykonywać zapytania w log Analytics obszarze roboczym, aby wyszukiwać terminy, identyfikować trendy, analizować wzorce i udostępniać wiele innych szczegółowych informacji na podstawie zebranych dzienników Azure Cosmos DB.

Jak wykonywać zapytania dotyczące Azure Cosmos DB w Log Analytics obszarach roboczych: https://docs.microsoft.com/azure/cosmos-db/monitor-cosmos-db

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: Włączanie alertów dla nietypowego działania

**Wskazówki**: w Azure Security Center Włącz zaawansowaną ochronę przed zagrożeniami dla Azure Cosmos DB do monitorowania nietypowych działań w dziennikach i zdarzeniach zabezpieczeń. Włącz ustawienia diagnostyczne w Azure Cosmos DB i Wyślij dzienniki do Log Analytics obszaru roboczego.

 

Możesz również dołączyć obszar roboczy Log Analytics do funkcji wskaźnikowej platformy Azure, ponieważ zapewnia ona rozwiązanie do automatycznej reakcji aranżacji zabezpieczeń (o). Pozwala to na tworzenie i używanie automatycznych rozwiązań elementy PlayBook w celu korygowania problemów z zabezpieczeniami. Ponadto można utworzyć niestandardowe alerty dzienników w obszarze roboczym Log Analytics przy użyciu Azure Monitor.

Lista alertów dotyczących ochrony przed zagrożeniami dla Azure Cosmos DB: https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos

Jak dołączyć wskaźnik na platformie Azure: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Tworzenie i wyświetlanie alertów dzienników oraz zarządzanie nimi za pomocą Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie dotyczy; Azure Cosmos DB nie przetwarza ani nie tworzy dzienników związanych z oprogramowaniem chroniącym przed złośliwym kodem.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: nie dotyczy; Azure Cosmos DB nie przetwarza ani nie tworzy dzienników związanych z usługą DNS.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: tożsamość i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: możesz użyć okienka kontrola tożsamości i dostępu (IAM) w Azure Portal, aby skonfigurować kontrolę dostępu opartą na rolach (Azure RBAC) na platformie Azure i zachować spis zasobów Azure Cosmos DB. Role są stosowane do użytkowników, grup, podmiotów usługi i tożsamości zarządzanych w Active Directory. Można używać wbudowanych ról lub ról niestandardowych dla użytkowników indywidualnych i grup.

Azure Cosmos DB zapewnia wbudowaną rolę RBAC platformy Azure dla typowych scenariuszy zarządzania w programie Azure Cosmos DB. Osoba mająca profil w usłudze Azure Active Directory (AD) może przypisywać te role platformy Azure do użytkowników, grup, podmiotów usługi lub tożsamości zarządzanych, aby udzielać lub odmawiać dostępu do zasobów i operacji na zasobach Azure Cosmos DB.

Przy użyciu modułu Azure AD PowerShell można również wykonywać zapytania ad hoc w celu odnajdywania kont, które są członkami grup administracyjnych. 

Ponadto niektóre akcje w Azure Cosmos DB mogą być kontrolowane przy użyciu kluczy podstawowych Azure Active Directory i dla konta.  Użyj ustawienia konta "disableKeyBasedMetadataWriteAccess", aby kontrolować dostęp do klucza.

Informacje na temat kontroli dostępu opartej na rolach na platformie Azure w Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

Tworzenie własnych ról niestandardowych przy użyciu akcji Azure Cosmos DB (przestrzeń nazw Microsoft.DocumentDB): https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb

Utwórz nową rolę w Azure Active Directory: https://docs.microsoft.com/azure/role-based-access-control/custom-roles

Jak uzyskać rolę katalogu w Azure Active Directory przy użyciu programu PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&preserve-view=true

Jak uzyskać członków roli katalogu w Azure Active Directory przy użyciu programu PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&preserve-view=true

Ogranicz dostęp użytkowników tylko do operacji związanych z danymi: https://docs.microsoft.com/azure/cosmos-db/how-to-restrict-user-data

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: pojęcie domyślnego lub pustego hasła nie istnieje w odniesieniu do usługi Azure AD lub Azure Cosmos DB. Zamiast tego Azure Cosmos DB używa dwóch typów kluczy do uwierzytelniania użytkowników i zapewniania dostępu do danych i zasobów; klucze podstawowe i tokeny zasobów. Klucze można generować w dowolnym momencie.

Informacje o bezpiecznym dostępie do danych w Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data

Jak ponownie wygenerować klucze Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys

Jak programowo uzyskać dostęp do klawiszy przy użyciu Azure Active Directory: https://docs.microsoft.com/azure/cosmos-db/certificate-based-authentication

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: nie dotyczy; Azure Cosmos DB nie obsługuje kont administratorów.  Cały dostęp jest zintegrowany z usługami Azure Active Directory i kontroli dostępu opartej na rolach (RBAC) na platformie Azure.



**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: Azure Cosmos DB używa dwóch typów kluczy do autoryzowania użytkowników i nie obsługuje pojedynczych Sign-On (SSO) na poziomie płaszczyzny danych. Dostęp do płaszczyzny kontroli dla Cosmos DB jest dostępny za pośrednictwem interfejsu API REST i obsługuje logowanie jednokrotne. Aby przeprowadzić uwierzytelnianie, należy ustawić nagłówek autoryzacji dla żądań na token sieci Web JSON uzyskany z Azure Active Directory.

Informacje na temat interfejsu API REST usługi Azure Database for Cosmos DB: https://docs.microsoft.com/rest/api/cosmos-db/

Opis logowania jednokrotnego za pomocą Azure Active Directory: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: włączanie Azure Active Directory Multi-Factor Authentication i postępuj zgodnie z zaleceniami związanymi z zarządzaniem tożsamościami i dostępem Azure Security Center.

Jak włączyć usługę MFA na platformie Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak monitorować tożsamość i dostęp w Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Używanie stacji roboczych dostępu uprzywilejowanego (dostępem uprzywilejowanym) z Multi-Factor Authentication skonfigurowany do logowania się i konfigurowania zasobów platformy Azure.

Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak włączyć usługę MFA na platformie Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: dziennik i alert dotyczący podejrzanego działania z kont administracyjnych

**Wskazówki**: Użyj zaawansowanej ochrony przed zagrożeniami (ATP) dla Azure Cosmos DB. Usługa ATP dla Azure Cosmos DB stanowi dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont usługi Azure Cosmos lub korzystania z nich. Ta warstwa ochrony pozwala na rozwiązywanie zagrożeń i integrowanie ich z systemami monitorowania zabezpieczeń centralnych. 

Ponadto można użyć Azure Active Directory (AD) Privileged Identity Management (PIM) do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku.

Użyj funkcji wykrywania ryzyka usługi Azure AD, aby wyświetlać alerty i raporty na temat ryzykownego zachowania użytkowników.

Jak wdrożyć Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Informacje o wykrywaniu ryzyka usługi Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Skonfiguruj warunek lokalizacji zasad dostępu warunkowego i Zarządzaj nazwanymi lokalizacjami. Przy użyciu nazwanych lokalizacji można tworzyć logiczne grupowania zakresów adresów IP lub krajów i regionów. Można ograniczyć dostęp do poufnych zasobów, takich jak wystąpienia Azure Cosmos DB, do skonfigurowanych lokalizacji nazwanych.

Jak skonfigurować nazwane lokalizacje na platformie Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

Jak utworzyć i skonfigurować wystąpienie Azure Active Directory: https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Jak skonfigurować uwierzytelnianie Azure Active Directory i zarządzać nim przy użyciu usługi Azure SQL: https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Azure Active Directory zawiera dzienniki ułatwiające wykrywanie starych kont. Ponadto można korzystać z przeglądów dostępu do tożsamości platformy Azure w celu wydajnego zarządzania członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkownika może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają stały dostęp.

Jak korzystać z przeglądów dostępu do tożsamości platformy Azure: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych kont

**Wskazówki**: można utworzyć ustawienia diagnostyczne dla Azure Active Directory kont użytkowników, wysłać dzienniki inspekcji i dzienniki logowania do log Analytics obszaru roboczego, w którym można skonfigurować żądane alerty.

Jak zintegrować dzienniki aktywności platformy Azure z Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: Użyj zaawansowanej ochrony przed zagrożeniami (ATP) dla Azure Cosmos DB. Usługa ATP dla Azure Cosmos DB stanowi dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont usługi Azure Cosmos lub korzystania z nich. Ta warstwa ochrony pozwala na rozwiązywanie zagrożeń i integrowanie ich z systemami monitorowania zabezpieczeń centralnych. 

Możesz również użyć funkcji Azure AD Identity Protection i wykrywania ryzyka, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. Ponadto można pozyskiwanie dzienników na platformie Azure — badanie wskaźnikowe.

Jak wyświetlić Azure Active Directory ryzykowne logowania: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Jak dołączyć wskaźnik na platformie Azure: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: skrytka klienta nie ma zastosowania do Cosmos DB.  Pracownicy firmy Microsoft nie mogą uzyskać dostępu do zasobów danych nawet z uprawnieniami klienta.

Lista obsługiwanych usług Skrytka klienta: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu wystąpień Azure Cosmos DB, które przechowują lub przetwarzają informacje poufne.

Tworzenie i używanie tagów: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie oddzielnych subskrypcji i/lub grup zarządzania na potrzeby tworzenia, testowania i produkcji. Wystąpienia Azure Cosmos DB są rozdzielone przez sieć wirtualną/podsieć, odpowiednio otagowane i zabezpieczone w sieciowej grupie zabezpieczeń (sieciowej grupy zabezpieczeń) lub na zaporze platformy Azure. Wystąpienia Azure Cosmos DB, w których są przechowywane poufne dane, powinny być izolowane. Za pomocą linku prywatnego platformy Azure można nawiązać połączenie z kontem wystąpienia Azure Cosmos DB za pośrednictwem prywatnego punktu końcowego. Prywatny punkt końcowy to zestaw prywatnych adresów IP w podsieci w sieci wirtualnej. Następnie możesz ograniczyć dostęp do wybranych prywatnych adresów IP. 

Jak utworzyć dodatkowe subskrypcje platformy Azure: https://docs.microsoft.com/azure/billing/billing-create-subscription

Tworzenie grup zarządzania: https://docs.microsoft.com/azure/governance/management-groups/create

Tworzenie i używanie tagów: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak skonfigurować prywatny punkt końcowy dla Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints

Jak utworzyć sieciową grupę zabezpieczeń z konfiguracją zabezpieczeń: https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: można wdrożyć zaawansowaną ochronę przed zagrożeniami dla Azure Cosmos DB, która będzie wykrywać anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. Obecnie można wyzwolić następujące alerty:

- Dostęp z nietypowych lokalizacji

- Nietypowa wyodrębnianie danych

Ponadto w przypadku korzystania z maszyn wirtualnych do uzyskiwania dostępu do wystąpień Azure Cosmos DB należy używać prywatnych linków, zapór, sieciowych grup zabezpieczeń i tagów usług, aby ograniczyć możliwość eksfiltracji danych. Firma Microsoft zarządza podstawową infrastrukturą dla Azure Cosmos DB i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

Jak skonfigurować Cosmos DB zaawansowanej ochrony przed zagrożeniami: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Zrozumienie ochrony danych klientów na platformie Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: wszystkie połączenia do Azure Cosmos DB obsługują protokół https. Azure Cosmos DB obsługuje również protokół TLS 1.2. Istnieje możliwość wymuszenia minimalnej wersji protokołu TLS po stronie serwera. Aby to zrobić, skontaktuj się z firmą [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com) .

Omówienie zabezpieczeń Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/database-security

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: automatyczne Identyfikowanie danych, klasyfikacja i funkcje zapobiegania utracie nie są jeszcze dostępne dla Azure Cosmos DB. Można jednak użyć integracji Wyszukiwanie poznawcze platformy Azure na potrzeby klasyfikacji i analizy danych. Rozwiązanie innych firm można wdrożyć również w razie potrzeby w celu zapewnienia zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

Indeksuj Azure Cosmos DB dane przy użyciu platformy Azure Wyszukiwanie poznawcze: https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&amp ; BC =/azure/cosmos-db/breadcrumb/toc.jsna

Zrozumienie ochrony danych klientów na platformie Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: udostępnione

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów

**Wskazówki**: Azure Cosmos DB zapewnia wbudowaną kontrolę dostępu opartą na rolach (Azure RBAC) na potrzeby typowych scenariuszy zarządzania w programie Azure Cosmos DB. Osoba mająca profil w Azure Active Directory może przypisywać te role platformy Azure do użytkowników, grup, nazw podmiotów usług lub tożsamości zarządzanych w celu udzielania lub odmawiania dostępu do zasobów i operacji na zasobach Azure Cosmos DB. Przypisania ról są objęte zakresem tylko dostępu do płaszczyzny kontroli, który obejmuje dostęp do kont usługi Azure Cosmos, baz danych, kontenerów i ofert (przepływności).

Jak zaimplementować usługę Azure RBAC w Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

Firma Microsoft zarządza podstawową infrastrukturą dla Cosmos DB i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

Zrozumienie ochrony danych klientów na platformie Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: domyślnie są szyfrowane wszystkie dane użytkownika przechowywane w Cosmos DB. Brak kontrolek, aby ją wyłączyć. Azure Cosmos DB używa szyfrowania AES-256 we wszystkich regionach, w których konto jest uruchomione.

Domyślnie firma Microsoft zarządza kluczami, które są używane do szyfrowania danych na koncie usługi Azure Cosmos. Opcjonalnie możesz dodać drugą warstwę szyfrowania przy użyciu własnych kluczy.

Informacje na temat szyfrowania Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Informacje na temat zarządzania Kluczami na potrzeby szyfrowania Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-security-controls

Jak skonfigurować klucze zarządzane przez klienta dla konta Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor w dzienniku aktywności platformy Azure, aby utworzyć alerty, kiedy zmiany mają miejsce w wystąpieniach produkcyjnych Azure Cosmos DB.

Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: Postępuj zgodnie z zaleceniami Azure Security Center wystąpienia Azure Cosmos DB. 

Firma Microsoft wykonuje stosowanie poprawek i luk w zabezpieczeniach na podstawowych hostach, które obsługują wystąpienia Azure Cosmos DB. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

Obsługiwane funkcje dostępne w Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Wdróż zautomatyzowane rozwiązanie do zarządzania poprawkami oprogramowania innych firm

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Spis i zarządzanie zasobami](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Użyj odnajdywania zasobów platformy Azure

**Wskazówki**: Użyj Azure Portal lub grafu zasobów platformy Azure, aby odnaleźć wszystkie zasoby (nieograniczone do Azure Cosmos DB, ale także takie jak zasoby obliczeniowe, inne magazyny, Sieć, porty i protokoły itp.) w ramach subskrypcji.  Upewnij się, że masz odpowiednie uprawnienia w dzierżawie i że można wyliczyć wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

Mimo że klasyczne zasoby platformy Azure mogą zostać odnalezione za pośrednictwem grafu zasobów, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów do przodu.

Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak wyświetlić subskrypcje platformy Azure: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0&preserve-view=true

Informacje na temat kontroli dostępu opartej na rolach na platformie Azure: https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: Zastosuj znaczniki do wystąpień Azure Cosmos DB i powiązanych zasobów przy użyciu metadanych, aby logicznie zorganizować je w taksonomię.

Tworzenie i używanie tagów: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Które zasoby Azure Cosmos DB obsługują Tagi: https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie jest to konieczne, do organizowania i śledzenia zasobów, w tym między innymi zasobów Azure Cosmos DB. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

Jak utworzyć dodatkowe subskrypcje platformy Azure: https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć Grupy zarządzania: https://docs.microsoft.com/azure/governance/management-groups/create

Tworzenie i używanie tagów: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: przechowywanie spisu zatwierdzonych zasobów platformy Azure i tytułów oprogramowania

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych i platformy Azure jako całości.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Ponadto Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania zasobów w ramach subskrypcji.

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak tworzyć zapytania za pomocą usługi Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: nie dotyczy; Ta linia bazowa jest przeznaczona dla zasobów obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych i platformy Azure jako całości.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów 

- Dozwolone typy zasobów

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odmówić określonego typu zasobu Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="610-implement-approved-application-list"></a>6,10: Zaimplementuj listę zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z programu AzureResources Manager za pośrednictwem skryptów

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość współpracy użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management". Może to uniemożliwić tworzenie i wprowadzanie zmian w zasobach w środowisku o wysokim poziomie zabezpieczeń.

Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla wystąpień Cosmos dB przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft.DocumentDB", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji wystąpień Cosmos DB. Możesz również używać wbudowanych definicji zasad dla Azure Cosmos DB, takich jak:

- Wdróż zaawansowaną ochronę przed zagrożeniami dla kont Cosmos DB

- Cosmos DB powinien używać punktu końcowego usługi sieci wirtualnej

Jak wyświetlić dostępne aliasy Azure Policy: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0&preserve-view=true

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Opis efektów Azure Policy: https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji Azure Policy dla Cosmos DB lub powiązanych zasobów, użyj Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

Dokumentacja Azure Repos: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops&preserve-view=truehttps://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją systemu

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft.DocumentDB", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla usług platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft.DocumentDB", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Użyj Azure Policy [Audit], [Odmów] i [Wdróż, jeśli nie istnieje], aby automatycznie wymuszać konfiguracje dla wystąpień Azure Cosmos DB i powiązanych zasobów. 

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: w przypadku maszyn wirtualnych platformy Azure lub aplikacji sieci Web działających na Azure App Service używanym do uzyskiwania dostępu do wystąpień Azure Cosmos DB Użyj tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć Azure Cosmos DB zarządzaniem kluczami tajnymi. Upewnij się, że Key Vault usuwanie trwałe jest włączone.

Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Jak utworzyć Key Vault: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Jak przeprowadzić uwierzytelnianie w Key Vault: https://docs.microsoft.com/azure/key-vault/general/authentication

Jak przypisać zasady dostępu Key Vault: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: w przypadku maszyn wirtualnych platformy Azure lub aplikacji sieci Web działających na Azure App Service używanym do uzyskiwania dostępu do wystąpień Azure Cosmos DB Użyj tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć Azure Cosmos DB zarządzaniem kluczami tajnymi.

Użyj tożsamości zarządzanych, aby zapewnić usługom platformy Azure automatyczną tożsamość zarządzaną w usłudze Azure Active Directory (AD). Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie.

Jak skonfigurować tożsamości zarządzane: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń zachęca również do przemieszczania odnalezionych poświadczeń do bardziej bezpiecznych lokalizacji, takich jak Azure Key Vault.

Jak skonfigurować skaner poświadczeń: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych. Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Azure App Service), ale nie jest uruchamiane na treści klienta.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład Azure App Service), ale nie jest uruchamiane na zawartości klienta.

Ponosisz odpowiedzialność za wstępne skanowanie wszystkich plików przekazywanych do zasobów platformy Azure, które nie są obliczeniowe, w tym Azure Cosmos DB. Firma Microsoft nie może uzyskać dostępu do danych klienta i w związku z tym nie może przeprowadzić skanowania zawartości klienta przez oprogramowanie chroniące przed złośliwym kodem.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: nie dotyczy; testy porównawcze są przeznaczone do zasobów obliczeniowych. Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure, ale nie jest uruchamiane w treści klienta.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: Azure Cosmos DB wykonuje migawki danych co cztery godziny. Wszystkie kopie zapasowe są przechowywane oddzielnie w usłudze magazynu, a te kopie zapasowe są replikowane globalnie w celu odporności na awarie regionalne. W danym momencie tylko ostatnie dwie migawki są zachowywane. Jeśli jednak kontener lub baza danych zostanie usunięta, usługa Azure Cosmos DB zachowuje istniejące migawki danego kontenera lub bazy danych przez 30 dni. Aby przywrócić kopię zapasową, skontaktuj się z pomocą techniczną platformy Azure.

Informacje o Azure Cosmos DB zautomatyzowanych kopii zapasowych: https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: Azure Cosmos DB automatycznie pobiera kopie zapasowe danych w regularnych odstępach czasu. Jeśli usunięto bazę danych lub kontener, możesz utworzyć bilet pomocy technicznej lub wywoływać pomoc techniczną platformy Azure w celu przywrócenia danych z automatycznych kopii zapasowych online. Pomoc techniczna systemu Azure jest dostępna dla wybranych planów, takich jak standard, Developer i plany wyższe niż. Aby przywrócić określoną migawkę kopii zapasowej, Azure Cosmos DB wymaga, aby dane były dostępne na czas trwania cyklu tworzenia kopii zapasowej dla tej migawki. 

W przypadku używania Key Vault do przechowywania poświadczeń dla wystąpień Cosmos DB należy zapewnić regularne automatyczne tworzenie kopii zapasowych kluczy.

Informacje o Azure Cosmos DB zautomatyzowanych kopii zapasowych: https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Jak przywrócić dane w Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Jak utworzyć kopię zapasową kluczy Key Vault: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: udostępnione

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: Jeśli usunięto bazę danych lub kontener, możesz utworzyć bilet pomocy technicznej lub skontaktować się z pomocą techniczną platformy Azure w celu przywrócenia danych z automatycznych kopii zapasowych online. Pomoc techniczna systemu Azure jest dostępna dla wybranych planów, takich jak standard, Developer i plany wyższe niż. Aby przywrócić określoną migawkę kopii zapasowej, Azure Cosmos DB wymaga, aby dane były dostępne na czas trwania cyklu tworzenia kopii zapasowej dla tej migawki.

Przetestuj przywracanie wpisów tajnych przechowywanych w Azure Key Vault przy użyciu programu PowerShell. Restore-AzureKeyVaultKey polecenie cmdlet tworzy klucz w określonym magazynie kluczy. Ten klucz jest repliką klucza kopii zapasowej w pliku wejściowym i ma taką samą nazwę jak klucz oryginalny.

Informacje o Azure Cosmos DB zautomatyzowanych kopii zapasowych:

https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Jak przywrócić dane w Azure Cosmos DB:

https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Jak przywrócić Azure Key Vault wpisy tajne:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0&preserve-view=true

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: ze względu na to, że wszystkie dane użytkownika przechowywane w Cosmos DB są szyfrowane w stanie spoczynku i transport, nie trzeba podejmować żadnych działań. Innym sposobem na umieszczenie tego ustawienia jest to, że szyfrowanie w stanie spoczynku jest domyślnie włączone. Brak kontrolek do wyłączenia lub włączenia. Azure Cosmos DB używa szyfrowania AES-256 we wszystkich regionach, w których konto jest uruchomione.

Włącz Soft-Delete w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

Poznaj szyfrowanie danych w Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Jak włączyć Soft-Delete w Key Vault: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odpowiedź na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Tworzenie przewodnika odpowiedzi na zdarzenia dla organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania z wykrywania do oceny po zdarzeniu.

Możesz również wykorzystać Przewodnik obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

Jak skonfigurować automatyzację przepływu pracy w ramach Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia w centrum Microsoft Security Response: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Waga jest zależna od tego, jak dobrze jest znajdowanie Security Center lub analiza używana do wystawiania alertu, a także poziom pewności, że istniało złośliwe zamiar w odniesieniu do działania, które prowadziło do alertu.

Dodatkowo jasno Oznacz subskrypcje (na przykład produkcyjny, nieprodukcyjny) i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy i popraw plan zgodnie z wymaganiami.

Zapoznaj się z publikacją NIST: Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych klienta został uzyskany przez nielegalną lub nieautoryzowaną osobę.  Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

Jak ustawić Azure Security Center kontaktu zabezpieczeń: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center, aby przesłać strumieniowo wskaźnik do alertów.

Jak skonfigurować eksport ciągły: https://docs.microsoft.com/azure/security-center/continuous-export

Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowej: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w programie Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.

Jak skonfigurować automatyzację przepływu pracy i Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń w ciągu 60 dni

**Wskazówki**: Postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Aby uzyskać więcej informacji na temat strategii i sposobu działania tworzenia zespołu Red-and-testowego na żywo w oparciu o infrastrukturę, usługi i aplikacje chmurowe zarządzane przez firmę Microsoft, należy tutaj:  https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)
