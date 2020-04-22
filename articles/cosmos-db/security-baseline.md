---
title: Azure Security Baseline for Cosmos DB
description: Azure Security Baseline for Cosmos DB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 7e927b398bd95160e02ee915eb98ea3cb78d68fe
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758694"
---
# <a name="azure-security-baseline-for-cosmos-db"></a>Azure Security Baseline for Cosmos DB

Usługa Azure Security Baseline for Cosmos DB zawiera zalecenia, które pomogą Ci poprawić poziom zabezpieczeń wdrożenia.

Punkt odniesienia dla tej usługi jest pobierany z [usługi Azure Security Benchmark w wersji 1.0,](https://docs.microsoft.com/azure/security/benchmarks/overview)która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure za pomocą naszych najlepszych wskazówek dotyczących rozwiązań.

Aby uzyskać więcej informacji, zobacz [omówienie planów bazowych zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Bezpieczeństwo sieci](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure w sieci wirtualnej

**Wskazówki:** Korzystając z usługi Azure Private Link, można połączyć się z kontem usługi Azure Cosmos za pośrednictwem prywatnego punktu końcowego. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Można również zmniejszyć ryzyko eksfiltracji danych, konfigurując ścisły zestaw reguł ruchu wychodzącego w sieciowej grupie zabezpieczeń (NSG) i kojarząc tę grupę zabezpieczeń z podsiecią.

Można również użyć punktów końcowych usługi do zabezpieczenia konta usługi Azure Cosmos. Włączając punkt końcowy usługi, można skonfigurować konta usługi Azure Cosmos, aby zezwolić na dostęp tylko z określonej podsieci sieci wirtualnej platformy Azure. Po włączeniu punktu końcowego usługi usługi Usługi Usługi Azure Cosmos DB można ograniczyć dostęp do konta usługi Azure Cosmos z połączeniami z podsieci w sieci wirtualnej.

Można również zabezpieczyć dane przechowywane na koncie usługi Azure Cosmos przy użyciu zapór IP. Usługa Azure Cosmos DB obsługuje formanty dostępu oparte na protokãołowych adresach IP dla obsługi zapory przychodzącej. Zaporę IP można ustawić na koncie usługi Azure Cosmos przy użyciu witryny Azure portal, szablonów usługi Azure Resource Manager lub za pośrednictwem interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell.

Omówienie łącza prywatnego platformy Azure:https://docs.microsoft.com/azure/private-link/private-link-overview

Jak skonfigurować prywatny punkt końcowy dla usługi Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints 

Jak utworzyć grupę zabezpieczeń sieci z configiem zabezpieczeń:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Jak skonfigurować zaporę IP w usłudze Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-firewall

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki:** Użyj usługi Azure Security Center i postępuj zgodnie z zaleceniami dotyczącymi ochrony sieci, aby zabezpieczyć zasoby sieciowe związane z kontem usługi Azure Cosmos.

Gdy maszyny wirtualne są wdrażane w tej samej sieci wirtualnej co konto usługi Azure Cosmos, można użyć sieciowej grupy zabezpieczeń (NSG), aby zmniejszyć ryzyko eksfiltracji danych. Włącz dzienniki przepływu nsg i wysyłaj dzienniki do konta usługi Azure Storage dla inspekcji ruchu. Można również wysłać dzienniki przepływu nsg do obszaru roboczego usługi Log Analytics i użyć usługi Traffic Analytics, aby zapewnić wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety usługi Traffic Analytics to możliwość wizualizacji aktywności sieciowej i identyfikowania punktów zapalnych, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i określania błędnych konfiguracji sieci.

Poznaj zabezpieczenia sieci udostępniane przez usługę Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Jak włączyć dzienniki przepływu nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak włączyć i korzystać z analizy ruchu:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrona krytycznych aplikacji internetowych

**Wskazówki:** Użyj funkcji udostępniania zasobów między źródłami (CORS), aby umożliwić aplikacji sieci web działającej w jednej domenie dostęp do zasobów w innej domenie. Przeglądarki sieci Web implementują ograniczenie zabezpieczeń znane jako zasady tego samego źródła, które uniemożliwia stronie sieci Web wywoływanie interfejsów API w innej domenie. Jednak cors zapewnia bezpieczny sposób, aby umożliwić domeny pochodzenia do wywoływania interfejsów API w innej domenie. Po włączeniu obsługi CORS dla konta usługi Azure Cosmos tylko uwierzytelnione żądania są oceniane w celu ustalenia, czy są one dozwolone zgodnie z określonymi regułami.

Konfigurowanie udostępniania zasobów między źródłami:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** Użyj zaawansowanej ochrony przed zagrożeniami (ATP) dla usługi Azure Cosmos DB . Narzędzie ATP dla usługi Azure Cosmos DB zapewnia dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont usługi Azure Cosmos lub ich wykorzystania. Ta warstwa ochrony pozwala na rozwiązywanie zagrożeń i integrowanie ich z centralnymi systemami monitorowania bezpieczeństwa.

Włącz standard ochrony przed atakami DDoS w sieciach wirtualnych skojarzonych z wystąpieniami usługi Azure Cosmos DB, aby chronić się przed atakami DDoS. Usługa Azure Security Center Integrated Threat Intelligence umożliwia odmawianie komunikacji ze znanymi złośliwymi lub nieużywanym internetowymi adresami IP.

Jak skonfigurowaćzadukazautrza cosmos DB zaawansowana ochrona przed zagrożeniami:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Jak skonfigurować ochronę przed atakami DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Poznaj zintegrowaną analizę zagrożeń w centrum zabezpieczeń platformy Azure:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Rejestrowanie pakietów sieciowych i dzienników przepływu

**Wskazówki:** Włącz dzienniki przepływu sieciowej grupy zabezpieczeń (NSG) i wyślij dzienniki do konta magazynu w celu inspekcji ruchu. Dzienniki przepływu grup nsg można wysyłać do obszaru roboczego usługi Log Analytics i korzystać z usługi Traffic Analytics, aby zapewnić wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety usługi Traffic Analytics to możliwość wizualizacji aktywności sieciowej i identyfikowania punktów zapalnych, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i określania błędnych konfiguracji sieci.

Jak włączyć dzienniki przepływu nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak włączyć i korzystać z analizy ruchu:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Wdrażanie opartych na sieci systemów wykrywania włamań/włamań (IDS/IPS)

**Wskazówki:** Użyj zaawansowanej ochrony przed zagrożeniami (ATP) dla usługi Azure Cosmos DB. Narzędzie ATP dla usługi Azure Cosmos DB zapewnia dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont usługi Azure Cosmos lub ich wykorzystania. Ta warstwa ochrony pozwala na rozwiązywanie zagrożeń i integrowanie ich z centralnymi systemami monitorowania bezpieczeństwa. 

Jak skonfigurować zaawansowaną ochronę przed zagrożeniami usługi Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Zarządzanie ruchem do aplikacji internetowych

**Wskazówki**: Nie dotyczy; zalecenie jest przeznaczony dla aplikacji sieci web działających w usłudze Azure App Service lub zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizowanie złożoności i administracyjnych narzutów administracyjnych dotyczących zasad bezpieczeństwa sieci

**Wskazówki:** W przypadku zasobów, które wymagają dostępu do konta usługi Azure Cosmos, użyj tagów usługi sieci wirtualnej, aby zdefiniować kontrolki dostępu do sieci w grupach zabezpieczeń sieciowych lub zaporze platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. AzureCosmosDB) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić lub odmówić ruchu dla odpowiedniej usługi. Firma Microsoft zarządza prefiksami adresów objętymi tagiem usługi i automatycznie aktualizuje tag usługi w miarę zmiany adresów.

Aby uzyskać więcej informacji na temat używania tagów usługi:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych za pomocą usługi Azure Policy. Użyj aliasów zasad platformy Azure w obszarach nazw "Microsoft.DocumentDB" i "Microsoft.Network", aby utworzyć niestandardowe zasady do inspekcji lub wymuszenia konfiguracji sieciowej wystąpień usługi Azure Cosmos DB. Można również korzystać z wbudowanych definicji zasad dla usługi Azure Cosmos DB, takich jak:

- Wdrażanie zaawansowanej ochrony przed zagrożeniami dla kont usługi Cosmos DB

- Usługa Cosmos DB powinna używać punktu końcowego usługi sieci wirtualnej

Można również użyć planów platformy Azure, aby uprościć wdrożenia platformy Azure na dużą skalę przez artefakty kluczowe środowisko pakowania, takie jak szablony usługi Azure Resource Manager, kontrola dostępu oparta na rolach (RBAC) i zasady w definicji jednego planu. Plan można łatwo zastosować do nowych subskrypcji, środowisk i dostroić kontrolę i zarządzanie za pomocą wersji.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak utworzyć plan platformy Azure:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="110-document-traffic-configuration-rules"></a>1.10: Przepisy konfiguracji ruchu dokumentów

**Wskazówki:** Użyj tagów dla zasobów sieciowych skojarzonych z wdrożeniem usługi Azure Cosmos DB, aby logicznie zorganizować je w taksonomię.

Jak tworzyć i używać tagów:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki:** Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z wystąpieniami usługi Azure Cosmos DB. Tworzenie alertów w usłudze Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych. 

Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Jak tworzyć alerty w usłudze Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Rejestrowanie i monitorowanie](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Korzystanie z zatwierdzonych źródeł synchronizacji czasu

**Wskazówki**: Firma Microsoft przechowuje źródło czasu używane dla zasobów platformy Azure, takich jak Usługa Azure Cosmos DB dla sygnatur czasowych w dziennikach.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** Pozyskiwania dzienników za pośrednictwem usługi Azure Monitor do agregowania danych zabezpieczeń generowanych przez usługę Azure Cosmos DB. W ramach usługi Azure Monitor użyj obszarów roboczych usługi Log Analytics do wykonywania zapytań i wykonywania analiz oraz używania kont magazynu długoterminowego/archiwizacji. Alternatywnie można włączyć i danych wbudowanych do usługi Azure Sentinel lub innej firmy Security Incident and Event Management (SIEM). 

Jak włączyć dzienniki diagnostyczne dla usługi Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/logging

Jak ować wobec pokład Azure Wartownik:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Włączanie rejestrowania inspekcji zasobów platformy Azure

**Wskazówki:** Włącz ustawienia diagnostyczne dla usługi Azure Cosmos DB i wyślij dzienniki do obszaru roboczego usługi Log Analytics lub konta magazynu. Ustawienia diagnostyczne w usłudze Azure Cosmos DB są używane do zbierania dzienników zasobów. Te dzienniki są przechwytywane na żądanie i są również określane jako "dzienniki płaszczyzny danych". Niektóre przykłady operacji na płaszczyźnie danych obejmują usuwanie, wstawianie i odczytanie. Można również włączyć ustawienia diagnostyczne dziennika aktywności platformy Azure i wysłać je do tego samego obszaru roboczego usługi Log Analytics.

Jak włączyć ustawienia diagnostyczne dla usługi Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/logging

Jak włączyć ustawienia diagnostyczne dziennika aktywności platformy Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Zbieranie dzienników zabezpieczeń z systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** W usłudze Azure Monitor ustaw okres przechowywania dzienników dla obszarów roboczych usługi Log Analytics skojarzonych z wystąpieniami usługi Azure Cosmos DB zgodnie z przepisami dotyczącymi zgodności obowiązującymi w organizacji.

Jak ustawić parametry przechowywania dziennika:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorowanie i przeglądanie dzienników

**Wskazówki:** Można wykonywać kwerendy w usłudze Log Analytics obszaru roboczego do wyszukiwania terminów, identyfikować trendy, analizować wzorce i dostarczać wiele innych szczegółowych informacji na podstawie dzienników usługi Azure Cosmos DB, które zostały zebrane.

Jak wykonywać kwerendy dotyczące usługi Azure Cosmos DB w obszarach roboczych analizy dzienników:https://docs.microsoft.com/azure/cosmos-db/monitor-cosmos-db

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Włączanie alertów o nietypowej aktywności

**Wskazówki:** W usłudze Azure Security Center włącz zaawansowaną ochronę przed zagrożeniami dla usługi Azure Cosmos DB, aby monitorować nietypowe działania w dziennikach zabezpieczeń i zdarzeniach. Włącz ustawienia diagnostyczne w usłudze Azure Cosmos DB i wysyłaj dzienniki do obszaru roboczego usługi Log Analytics.

 

Można również dołączać obszar roboczy usługi Log Analytics do usługi Azure Sentinel, ponieważ zapewnia rozwiązanie automatycznej odpowiedzi aranżacji zabezpieczeń (SOAR). Pozwala to na tworzenie podręczników (zautomatyzowanych rozwiązań) i ich wykorzystanie do rozwiązywania problemów z zabezpieczeniami. Ponadto można utworzyć alerty dziennika niestandardowego w obszarze roboczym usługi Log Analytics przy użyciu usługi Azure Monitor.

Lista alertów ochrony przed zagrożeniami dla usługi Azure Cosmos DB:https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos

Jak ować wobec pokład Azure Wartownik:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Tworzenie, wyświetlanie i zarządzanie alertami dziennika przy użyciu usługi Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="28-centralize-anti-malware-logging"></a>2.8: Scentralizuj rejestrowanie przed złośliwym oprogramowaniem

**Wskazówki**: Nie dotyczy; Usługa Azure Cosmos DB nie przetwarza ani nie generuje dzienników związanych ze złośliwym oprogramowaniem.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="29-enable-dns-query-logging"></a>2.9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: Nie dotyczy; Usługa Azure Cosmos DB nie przetwarza ani nie tworzy dzienników związanych z systemem DNS.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="210-enable-command-line-audit-logging"></a>2.10: Włącz rejestrowanie inspekcji wiersza polecenia

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Kontrola tożsamości i dostępu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Prowadzenie spisu rachunków administracyjnych

**Wskazówki: Okienko**kontrola tożsamości i dostępu (IAM) w witrynie Azure Portal służy do konfigurowania kontroli dostępu opartej na rolach (RBAC) i obsługi zasobów usługi Azure Cosmos DB. Role są stosowane do użytkowników, grup, podmiotów usługi i tożsamości zarządzanych w usłudze Active Directory. Dla osób i grup można używać wbudowanych ról lub ról niestandardowych.

Usługa Azure Cosmos DB udostępnia wbudowany pakiet RBAC dla typowych scenariuszy zarządzania w usłudze Azure Cosmos DB. Osoba, która ma profil w usłudze Azure Active Directory (AD), może przypisać te role RBAC do użytkowników, grup, podmiotów zabezpieczeń usługi lub tożsamości zarządzanych w celu udzielenia lub odmowy dostępu do zasobów i operacji w zasobach usługi Azure Cosmos DB.

Moduł programu Azure AD PowerShell służy również do wykonywania kwerend adhoc w celu odnajdowania kont, które są członkami grup administracyjnych. 

Ponadto niektóre akcje w usłudze Azure Cosmos DB można kontrolować za pomocą usługi Azure Active Directory i kluczy głównych specyficznych dla konta.  Użyj ustawienia konta "disableKeyBasedMetadataWriteAccess", aby kontrolować dostęp do kluczy.

Opis kontroli dostępu opartej na rolach w usłudze Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

Tworzenie własnych ról niestandardowych przy użyciu akcji usługi Azure Cosmos DB (obszar nazw microsoft.documentdb):https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb

Utwórz nową rolę w usłudze Azure Active Directory:https://docs.microsoft.com/azure/role-based-access-control/custom-roles

Jak uzyskać rolę katalogu w usłudze Azure Active Directory za pomocą programu PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Jak uzyskać członków roli katalogu w usłudze Azure Active Directory z programem PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Ogranicz dostęp użytkownika tylko do operacji danych:https://docs.microsoft.com/azure/cosmos-db/how-to-restrict-user-data

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="32-change-default-passwords-where-applicable"></a>3.2: W stosownych przypadkach zmieniaj domyślne hasła

**Wskazówki:** Pojęcie domyślnych lub pustych haseł nie istnieje w odniesieniu do usługi Azure AD lub usługi Azure Cosmos DB. Zamiast tego usługa Azure Cosmos DB używa dwóch typów kluczy do uwierzytelniania użytkowników i zapewnienia dostępu do swoich danych i zasobów; klucze główne i tokeny zasobów. Klucze można regenerować w dowolnym momencie.

Opis bezpiecznego dostępu do danych w usłudze Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data

Jak ponownie wygenerować klucze bazy danych usługi Azure Cosmos:https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys

Jak programowo uzyskiwać dostęp do kluczy przy użyciu usługi Azure Active Directory:https://docs.microsoft.com/azure/cosmos-db/certificate-based-authentication

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Współdzielone

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Korzystanie z dedykowanych kont administracyjnych

**Wskazówki**: Nie dotyczy; Usługa Azure Cosmos DB nie obsługuje kont administratorów.  Cały dostęp jest zintegrowany z usługą Azure Active Directory i kontrolą dostępu opartą na rolach platformy Azure (RBAC).



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Używanie logowania jednokrotnego (Logowanie jednokrotne) w usłudze Azure Active Directory

**Wskazówki:** Usługa Azure Cosmos DB używa dwóch typów kluczy do autoryzowania użytkowników i nie obsługuje logowania jednokrotnego (Logowanie jednokrotne) na poziomie płaszczyzny danych. Dostęp do płaszczyzny sterowania dla usługi Cosmos DB jest dostępny za pośrednictwem interfejsu API REST i obsługuje funkcję SSO. Aby uwierzytelnić, ustaw nagłówek autoryzacji żądań na token sieci Web JSON, który można uzyskać z usługi Azure Active Directory.

Poznaj interfejs API rest usługi Azure Database for Cosmos DB:https://docs.microsoft.com/rest/api/cosmos-db/

Poznaj ujedniaka wstępna w usłudze Azure Active Directory:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Użyj uwierzytelniania wieloskładnikowego dla całego dostępu opartego na usłudze Azure Active Directory

**Wskazówki:** Włącz uwierzytelnianie wieloskładnikowe usługi Azure Active Directory i postępuj zgodnie z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem usługi Azure Security Center.

Jak włączyć usługę MFA na platformie Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak monitorować tożsamość i dostęp w usłudze Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Używanie dedykowanych maszyn (stacje robocze z uprzywilejowanym dostępem) do wszystkich zadań administracyjnych

**Wskazówki:** Użyj stacji roboczych dostępu uprzywilejowanego (PAW) z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania zasobów platformy Azure.

Dowiedz się więcej o stacjach roboczych z dostępem uprzywilejowanym:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak włączyć usługę MFA na platformie Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Rejestrowanie i ostrzeganie o podejrzanych działaniach z kont administracyjnych

**Wskazówki:** Użyj zaawansowanej ochrony przed zagrożeniami (ATP) dla usługi Azure Cosmos DB. Narzędzie ATP dla usługi Azure Cosmos DB zapewnia dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont usługi Azure Cosmos lub ich wykorzystania. Ta warstwa ochrony pozwala na rozwiązywanie zagrożeń i integrowanie ich z centralnymi systemami monitorowania bezpieczeństwa. 

Ponadto można użyć usługi Azure Active Directory (AD) Uprzywilejowane zarządzanie tożsamościami (PIM) do generowania dzienników i alertów, gdy podejrzane lub niebezpieczne działania występuje w środowisku.

Użyj wykrywania ryzyka usługi Azure AD, aby wyświetlić alerty i raporty dotyczące ryzykownych zachowań użytkowników.

Jak wdrożyć zarządzanie tożsamościami uprzywilejowanymi (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Poznaj wykrywanie ryzyka usługi Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** Skonfiguruj stan lokalizacji zasad dostępu warunkowego i zarządzaj nazwanymi lokalizacjami. W nazwanych lokalizacjach można tworzyć logiczne grupy zakresów adresów IP lub krajów i regionów. Można ograniczyć dostęp do poufnych zasobów, takich jak wystąpienia usługi Azure Cosmos DB, do skonfigurowanych nazwanych lokalizacji.

Jak skonfigurować nazwane lokalizacje na platformie Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="39-use-azure-active-directory"></a>3.9: Korzystanie z usługi Azure Active Directory

**Wskazówki:** Użyj usługi Azure Active Directory (AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych w spoczynku i podczas przesyłania. Usługa Azure AD również sole, skróty i bezpiecznie przechowuje poświadczenia użytkownika.

Jak utworzyć i skonfigurować wystąpienie usługi Azure Active Directory:https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Jak skonfigurować uwierzytelnianie usługi Azure Active Directory i zarządzać nim za pomocą usługi Azure SQL:https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** Usługa Azure Active Directory udostępnia dzienniki ułatwiające odnajdowanie starych kont. Ponadto można użyć przeglądów dostępu tożsamości platformy Azure do efektywnego zarządzania członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkownika można regularnie przeglądać, aby upewnić się, że tylko prawo Użytkownicy mają stały dostęp.

Jak korzystać z przeglądów dostępu tożsamości platformy Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych kont

**Wskazówki:** Można utworzyć ustawienia diagnostyczne dla kont użytkowników usługi Azure Active Directory, wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego usługi Log Analytics, w którym można skonfigurować żądane alerty.

Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alert o odchyleniu zachowania logowania do konta

**Wskazówki:** Użyj zaawansowanej ochrony przed zagrożeniami (ATP) dla usługi Azure Cosmos DB. Narzędzie ATP dla usługi Azure Cosmos DB zapewnia dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont usługi Azure Cosmos lub ich wykorzystania. Ta warstwa ochrony pozwala na rozwiązywanie zagrożeń i integrowanie ich z centralnymi systemami monitorowania bezpieczeństwa. 

Za pomocą usługi Azure AD Identity Protection i funkcji wykrywania ryzyka można również skonfigurować automatyczne odpowiedzi na wykryte podejrzane akcje związane z tożsamościami użytkowników. Ponadto można pozyskiwania dzienników do usługi Azure Sentinel do dalszego badania.

Jak wyświetlić ryzykowne logowania usługi Azure Active Directory:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak skonfigurować i włączyć zasady ryzyka ochrony tożsamości:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Jak ować wobec pokład Azure Wartownik:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Zapewnienie firmie Microsoft dostępu do odpowiednich danych klientów podczas scenariuszy pomocy technicznej

**Wskazówki**: Obecnie niedostępne; Skrytka klienta nie jest jeszcze obsługiwana dla usługi Azure Database for Cosmos DB.

Lista obsługiwanych usług skrytki klienta:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Nie dotyczy

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Prowadzenie wykazu informacji poufnych

**Wskazówki:** Użyj tagów, aby pomóc w śledzeniu wystąpień usługi Azure Cosmos DB, które przechowują lub przetwarzają poufne informacje.

Jak tworzyć i używać tagów:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemy izolowania przechowujące lub przetwarzające informacje poufne

**Wskazówki**: Implementowanie oddzielnych subskrypcji i/lub grup zarządzania do programowania, testowania i produkcji. Wystąpienia usługi Azure Cosmos DB są oddzielone przez sieć wirtualną/podsieć, odpowiednio oznakowane i zabezpieczone w sieciowej grupie zabezpieczeń (NSG) lub zaporze platformy Azure. Wystąpienia usługi Azure Cosmos DB przechowujące poufne dane powinny być izolowane. Za pomocą usługi Azure Private Link, można połączyć się z kontem wystąpienia usługi Azure Cosmos DB za pośrednictwem prywatnego punktu końcowego. Prywatny punkt końcowy to zestaw prywatnych adresów IP w podsieci w sieci wirtualnej. Następnie można ograniczyć dostęp do wybranych prywatnych adresów IP. 

Jak utworzyć dodatkowe subskrypcje platformy Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć grupy zarządzania:https://docs.microsoft.com/azure/governance/management-groups/create

Jak tworzyć i używać tagów:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak skonfigurować prywatny punkt końcowy dla usługi Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints

Jak utworzyć grupę zabezpieczeń sieci z configiem zabezpieczeń:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego przesyłania poufnych informacji

**Wskazówki:** Można wdrożyć zaawansowaną ochronę przed zagrożeniami dla usługi Azure Cosmos DB, która wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. Obecnie może wyzwolić następujące alerty:

- Dostęp z nietypowych miejsc

- Nietypowe wyodrębnianie danych

Ponadto podczas korzystania z maszyn wirtualnych w celu uzyskania dostępu do wystąpień usługi Azure Cosmos DB, użyj łącza prywatnego, zapory, grup zabezpieczeń sieciowych i tagów usług, aby ograniczyć możliwość eksfiltracji danych. Firma Microsoft zarządza podstawową infrastrukturą usługi Azure Cosmos DB i wdrożyła ścisłe mechanizmy kontroli, aby zapobiec utracie lub ekspozycji danych klientów.

Jak skonfigurować zaawansowaną ochronę przed zagrożeniami usługi Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Poznaj ochronę danych klientów na platformie Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Współdzielone

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Szyfrowanie wszystkich poufnych informacji podczas przesyłania

**Wskazówki:** Wszystkie połączenia z usługą Azure Cosmos DB obsługują protokół HTTPS. Usługa Azure Cosmos DB obsługuje również protokół TLS1.2. Istnieje możliwość wymuszenia minimalnej wersji TLS po stronie serwera. W tym celu [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com)należy skontaktować się z firmą .

Omówienie zabezpieczeń bazy danych usługi Cosmos:https://docs.microsoft.com/azure/cosmos-db/database-security

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Identyfikowanie poufnych danych za pomocą aktywnego narzędzia do wykrywania

**Wskazówki:** Funkcje automatycznej identyfikacji danych, klasyfikacji i zapobiegania stratom nie są jeszcze dostępne dla usługi Azure Cosmos DB. Jednak można użyć integracji usługi Azure Cognitive Search do klasyfikacji i analizy danych. Można również zaimplementować rozwiązanie innej firmy, jeśli jest to wymagane do celów zgodności.

W przypadku podstawowej platformy, która jest zarządzana przez firmę Microsoft, firma Microsoft traktuje całą zawartość klienta jako wrażliwą i dokłga mierze chroni przed utratą i ekspozycją danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje zestaw niezawodnych mechanizmów kontroli i możliwości ochrony danych.

Indeksowanie danych usługi Azure Cosmos https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&ampDB za pomocą usługi Azure Cognitive Search: ;bc=/azure/cosmos-db/breadcrumb/toc.json

Poznaj ochronę danych klientów na platformie Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współdzielone

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Sterowanie dostępem do zasobów za pomocą narzędzia Azure RBAC

**Wskazówki:** Usługa Azure Cosmos DB zapewnia wbudowaną kontrolę dostępu opartą na rolach (RBAC) dla typowych scenariuszy zarządzania w usłudze Azure Cosmos DB. Osoba, która ma profil w usłudze Azure Active Directory, może przypisać te role RBAC do użytkowników, grup, podmiotów zabezpieczeń usługi lub tożsamości zarządzanych w celu udzielenia lub odmowy dostępu do zasobów i operacji w zasobach usługi Azure Cosmos DB. Przypisania ról są ograniczone tylko do dostępu do płaszczyzny kontroli, która obejmuje dostęp do kont usługi Azure Cosmos, baz danych, kontenerów i ofert (przepływność).

Jak zaimplementować rbac w usłudze Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Wymuszanie kontroli dostępu za pomocą funkcji zapobiegania utracie danych opartych na hoście

**Wskazówki**: Nie dotyczy; niniejsza wytyczna jest przeznaczona dla zasobów obliczeniowych.

Firma Microsoft zarządza podstawową infrastrukturą usługi Cosmos DB i wdrożyła ścisłe mechanizmy kontroli, aby zapobiec utracie lub ekspozycji danych klientów.

Poznaj ochronę danych klientów na platformie Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Szyfrowanie poufnych informacji w spoczynku

**Wskazówki:** Wszystkie dane użytkownika przechowywane w usłudze Cosmos DB są domyślnie szyfrowane w stanie spoczynku. Nie ma żadnych formantów, aby go wyłączyć. Usługa Azure Cosmos DB używa szyfrowania AES-256 we wszystkich regionach, w których konto jest uruchomione.

Domyślnie firma Microsoft zarządza kluczami używanymi do szyfrowania danych na koncie usługi Azure Cosmos. Opcjonalnie można dodać drugą warstwę szyfrowania za pomocą własnych kluczy.

Opis szyfrowania w pozostałej części za pomocą usługi Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Opis zarządzania kluczami szyfrowania w pozostałej części usługi Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-security-controls

Jak skonfigurować klucze zarządzane przez klienta dla konta usługi Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Współdzielone

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Rejestrowanie i ostrzeganie o zmianach w krytycznych zasobach platformy Azure

**Wskazówki:** Użyj usługi Azure Monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dotyczące tego, kiedy zmiany mają miejsce w wystąpieniach produkcyjnych usługi Azure Cosmos DB.

Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Uruchamianie zautomatyzowanych narzędzi do skanowania luk w zabezpieczeniach

**Wskazówki:** Postępuj zgodnie z zaleceniami z usługi Azure Security Center dla wystąpień usługi Azure Cosmos DB. 

Firma Microsoft wykonuje łatanie systemu i zarządzanie lukami w zabezpieczeniach na podstawowych hostach, które obsługują wystąpienia usługi Azure Cosmos DB. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje zestaw niezawodnych mechanizmów kontroli i możliwości ochrony danych.

Obsługiwane funkcje dostępne w usłudze Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Współdzielone

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: Nie dotyczy; niniejsza wytyczna jest przeznaczona dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami oprogramowania innych firm

**Wskazówki**: Nie dotyczy; niniejsza wytyczna jest przeznaczona dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porównanie skanów luk w zabezpieczeniach typu back-to-back

**Wskazówki**: Nie dotyczy; niniejsza wytyczna jest przeznaczona dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Użyj procesu oceny ryzyka, aby nadać priorytet usuwaniu wykrytych luk w zabezpieczeniach

**Wskazówki**: Nie dotyczy; niniejsza wytyczna jest przeznaczona dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie zapasami i zasobami](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Korzystanie z odnajdowania zasobów platformy Azure

**Wskazówki:** Użyj witryny Azure portal lub usługi Azure Resource Graph, aby odkryć wszystkie zasoby (nie tylko usługi Azure Cosmos DB, ale także zasoby, takie jak zasoby, takie jak zasoby obliczeniowe, inne magazyny, sieć, porty i protokoły itp.) w ramach subskrypcji.  Upewnij się, że masz odpowiednie uprawnienia w dzierżawie i są w stanie wyliczyć wszystkie subskrypcje platformy Azure, a także zasoby w ramach subskrypcji.

Chociaż klasyczne zasoby platformy Azure mogą zostać wykryte za pomocą wykresu zasobów, zdecydowanie zaleca się tworzenie i używanie zasobów usługi Azure Resource Manager w przyszłości.

Jak tworzyć zapytania za pomocą programu Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak wyświetlić subskrypcje platformy Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Opis kontroli dostępu opartej na rolach platformy Azure:https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="62-maintain-asset-metadata"></a>6.2: Obsługa metadanych zasobów

**Wskazówki:** Stosowanie tagów do wystąpień usługi Azure Cosmos DB i powiązanych zasobów z metadanymi, aby logicznie zorganizować je w taksonomię.

Jak tworzyć i używać tagów:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Które zasoby usługi Azure Cosmos DB obsługują tagi:https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** Użyj tagowania, grup zarządzania i oddzielnych subskrypcji, w stosownych przypadkach, do organizowania i śledzenia zasobów, w tym między innymi zasobów usługi Azure Cosmos DB. Regularnie uzgadniaj zapasy i upewnij się, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

Jak utworzyć dodatkowe subskrypcje platformy Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć grupy zarządzania:https://docs.microsoft.com/azure/governance/management-groups/create

Jak tworzyć i używać tagów:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Obsługa spisu zatwierdzonych zasobów i tytułów oprogramowania platformy Azure

**Wskazówki**: Nie dotyczy; niniejsza wytyczna jest przeznaczona dla zasobów obliczeniowych i platformy Azure jako całości.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki:** Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Ponadto użyj programu Azure Resource Graph do wykonywania zapytań/odnajdywanie zasobów w ramach subskrypcji.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak tworzyć zapytania za pomocą programu Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorowanie niezatwierdzonych aplikacji w zasobach obliczeniowych

**Wskazówki**: Nie dotyczy; ta linia bazowa jest przeznaczona dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Usuwanie niezatwierdzonych zasobów platformy Azure i aplikacji

**Wskazówki**: Nie dotyczy; niniejsza wytyczna jest przeznaczona dla zasobów obliczeniowych i platformy Azure jako całości.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="68-use-only-approved-applications"></a>6.8: Używaj wyłącznie zatwierdzonych aplikacji

**Wskazówki**: Nie dotyczy; niniejsza wytyczna jest przeznaczona dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="69-use-only-approved-azure-services"></a>6.9: Korzystaj tylko z zatwierdzonych usług platformy Azure

**Wskazówki:** Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów 

- Dozwolone typy zasobów

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odmówić określonego typu zasobu za pomocą usługi Azure Policy:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="610-implement-approved-application-list"></a>6.10: Wdrożenie listy zatwierdzonych wniosków

**Wskazówki**: Nie dotyczy; niniejsza wytyczna jest przeznaczona dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Ogranicz możliwość interakcji użytkowników z usługą AzureResources Manager za pomocą skryptów

**Wskazówki:** Użyj dostępu warunkowego platformy Azure, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resource Manager, konfigurując "Blokuj dostęp" dla aplikacji "Microsoft Azure Management". Może to zapobiec tworzeniu i zmian zasobów w środowisku wysokiego bezpieczeństwa.

Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do usługi Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Ograniczenie możliwości wykonywania skryptów przez użytkowników w zasobach obliczeniowych

**Wskazówki**: Nie dotyczy; niniejsza wytyczna jest przeznaczona dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizycznie lub logicznie segregować aplikacje wysokiego ryzyka

**Wskazówki**: Nie dotyczy; ta wytyczna jest przeznaczona dla aplikacji sieci web działających w usłudze Azure App Service lub zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Bezpieczna konfiguracja](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Ustanawianie bezpiecznych konfiguracji dla wszystkich zasobów platformy Azure

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla wystąpień usługi Cosmos DB za pomocą zasad platformy Azure. Użyj aliasów zasad platformy Azure w obszarze nazw "Microsoft.DocumentDB", aby utworzyć niestandardowe zasady do inspekcji lub wymuszenia konfiguracji wystąpień usługi Cosmos DB. Można również korzystać z wbudowanych definicji zasad dla usługi Azure Cosmos DB, takich jak:

- Wdrażanie zaawansowanej ochrony przed zagrożeniami dla kont usługi Cosmos DB

- Usługa Cosmos DB powinna używać punktu końcowego usługi sieci wirtualnej

Jak wyświetlić dostępne aliasy zasad platformy Azure:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Ustanawianie bezpiecznych konfiguracji systemów operacyjnych

**Wskazówki**: Nie dotyczy; niniejsza wytyczna jest przeznaczona dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** Użyj zasad platformy Azure [odmów] i [wdrożyć, jeśli nie istnieje], aby wymusić bezpieczne ustawienia w zasobach platformy Azure.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Poznaj efekty zasad platformy Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Obsługa bezpiecznych konfiguracji systemów operacyjnych

**Wskazówki**: Nie dotyczy; niniejsza wytyczna jest przeznaczona dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** Jeśli używasz niestandardowych definicji zasad platformy Azure dla usługi Cosmos DB lub powiązanych zasobów, użyj usługi Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

Dokumentacja repozytorium platformy Azure: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devopshttps://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpieczne przechowywanie obrazów niestandardowych systemów operacyjnych

**Wskazówki**: Nie dotyczy; niniejsza wytyczna jest przeznaczona dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Wdrażanie narzędzi do zarządzania konfiguracją systemu

**Wskazówki:** Użyj aliasów zasad platformy Azure w obszarze nazw "Microsoft.DocumentDB", aby utworzyć niestandardowe zasady do ostrzegania, inspekcji i wymuszania konfiguracji systemu. Ponadto opracuj proces i potok do zarządzania wyjątkami zasad.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych

**Wskazówki**: Nie dotyczy; niniejsza wytyczna jest przeznaczona dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Wdrażanie automatycznego monitorowania konfiguracji usług platformy Azure

**Wskazówki:** Użyj aliasów zasad platformy Azure w obszarze nazw "Microsoft.DocumentDB", aby utworzyć niestandardowe zasady do ostrzegania, inspekcji i wymuszania konfiguracji systemu. Użyj zasad platformy Azure [inspekcja], [odmówić] i [wdrożyć, jeśli nie istnieje], aby automatycznie wymusić konfiguracje dla wystąpień usługi Azure Cosmos DB i powiązanych zasobów. 

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Wdrożenie automatycznego monitorowania konfiguracji systemów operacyjnych

**Wskazówki**: Nie dotyczy; niniejsza wytyczna jest przeznaczona dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** W przypadku maszyn wirtualnych platformy Azure lub aplikacji sieci web uruchomionych w usłudze Azure App Service używanych do uzyskiwania dostępu do wystąpień usługi Azure Cosmos DB należy używać tożsamości usługi zarządzanej w połączeniu z usługą Azure Key Vault, aby uprościć i zabezpieczyć zarządzanie tajne usługi Azure Cosmos DB. Upewnij się, że włączona jest funkcja usuwania nietrwałego przechowalni kluczy.

Jak zintegrować się z tożsamościami zarządzanymi platformy Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Jak utworzyć przechowalnię kluczy:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Jak zapewnić uwierzytelnianie usługi Key Vault z tożsamością zarządzaną:https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** W przypadku maszyn wirtualnych platformy Azure lub aplikacji sieci web uruchomionych w usłudze Azure App Service używanych do uzyskiwania dostępu do wystąpień usługi Azure Cosmos DB należy używać tożsamości usługi zarządzanej w połączeniu z usługą Azure Key Vault, aby uprościć i zabezpieczyć zarządzanie tajne usługi Azure Cosmos DB.

Użyj tożsamości zarządzanych, aby zapewnić usługi platformy Azure z automatycznie zarządzaną tożsamością w usłudze Azure Active Directory (AD). Tożsamości zarządzane umożliwia uwierzytelnianie do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD, w tym Usługi Key Vault, bez żadnych poświadczeń w kodzie.

Jak skonfigurować tożsamości zarządzane:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Jak zintegrować się z tożsamościami zarządzanymi platformy Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Wyeliminuj niezamierzone narażenie na poświadczenia

**Wskazówki:** Implementowanie skanera poświadczeń w celu zidentyfikowania poświadczeń w kodzie. Skaner poświadczeń będzie również zachęcać do przenoszenia wykrytych poświadczeń do bezpieczniejszych lokalizacji, takich jak Usługa Azure Key Vault.

Jak skonfigurować Skaner poświadczeń:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona przed złośliwym oprogramowaniem](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Korzystanie z centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: Nie dotyczy; niniejsza wytyczna jest przeznaczona dla zasobów obliczeniowych. Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład usługa Azure App Service), jednak nie jest uruchamiana na zawartości klienta.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Przed skanowaniem plików, które mają zostać przesłane do zasobów platformy Azure nieoliczonych

**Wskazówki:** Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład usługa Azure App Service), jednak nie działa na zawartości klienta.

Obowiązkiem jest wstępne skanowanie wszystkich plików przekazywanych do nieoliczających zasobów platformy Azure, w tym usługi Azure Cosmos DB. Firma Microsoft nie może uzyskać dostępu do danych klientów i w związku z tym nie może przeprowadzać skanowania zawartości klienta w celu ochrony przed złośliwym oprogramowaniem.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Upewnij się, że oprogramowanie i podpisy chroniące przed złośliwym oprogramowaniem są aktualizowane

**Wskazówki**: Nie dotyczy; jest przeznaczony dla zasobów obliczeniowych. Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście obsługującym usługi platformy Azure, jednak nie działa na zawartości klienta.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Odzyskiwanie danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnienie regularnych automatycznych kopii zapasowych

**Wskazówki:** Usługa Azure Cosmos DB wykonuje migawki danych co cztery godziny. Wszystkie kopie zapasowe są przechowywane oddzielnie w usłudze magazynu, a te kopie zapasowe są replikowane globalnie w celu zapewnienia odporności na awarie regionalne. W danym momencie tylko dwa ostatnie migawki są zachowywane. Jeśli jednak kontener lub baza danych zostanie usunięta, usługa Azure Cosmos DB zachowuje istniejące migawki danego kontenera lub bazy danych przez 30 dni. Skontaktuj się z pomocą techniczną platformy Azure, aby przywrócić z kopii zapasowej.

Opis automatycznych kopii zapasowych usługi Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Wykonywanie kompletnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki:** Usługa Azure Cosmos DB automatycznie wykonuje kopie zapasowe danych w regularnych odstępach czasu. Jeśli baza danych lub kontener zostanie usunięty, można zgłosić bilet pomocy technicznej lub wywołać pomoc techniczną platformy Azure, aby przywrócić dane z automatycznych kopii zapasowych online. Pomoc techniczna platformy Azure jest dostępna tylko dla wybranych planów, takich jak Standard, Developer i plany wyższe od nich. Aby przywrócić określoną migawkę kopii zapasowej, usługa Azure Cosmos DB wymaga, aby dane były dostępne przez cały cykl tworzenia kopii zapasowej dla tej migawki. 

Jeśli usługa Key Vault jest używany do przechowywania poświadczeń dla wystąpień usługi Cosmos DB, upewnij się, że regularne automatyczne kopie zapasowe kluczy.

Poznaj automatyczne kopie zapasowe usługi Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Jak przywrócić dane w usłudze Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Jak wykonać kopię zapasową kluczy magazynu kluczy:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współdzielone

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Sprawdzanie poprawności wszystkich kopii zapasowych, w tym kluczy zarządzanych przez klienta

**Wskazówki:** Jeśli baza danych lub kontener zostanie usunięty, można złożyć bilet pomocy technicznej lub zadzwonić do pomocy technicznej platformy Azure, aby przywrócić dane z automatycznych kopii zapasowych online. Pomoc techniczna platformy Azure jest dostępna tylko dla wybranych planów, takich jak Standard, Developer i plany wyższe od nich. Aby przywrócić określoną migawkę kopii zapasowej, usługa Azure Cosmos DB wymaga, aby dane były dostępne przez cały cykl tworzenia kopii zapasowej dla tej migawki.

Przetestuj przywracanie wpisów tajnych przechowywanych w usłudze Azure Key Vault przy użyciu programu PowerShell. Polecenie cmdlet Restore-AzureKeyVaultKey tworzy klucz w określonym magazynie kluczy. Ten klucz jest repliką klucza kopii zapasowej w pliku wejściowym i ma taką samą nazwę jak oryginalny klucz.

Poznaj automatyczne kopie zapasowe usługi Azure Cosmos DB:

https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Jak przywrócić dane w usłudze Azure Cosmos DB:

https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Jak przywrócić wpisy tajne usługi Azure Key Vault:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Współdzielone

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zapewnienie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** Ponieważ wszystkie dane użytkownika przechowywane w usłudze Cosmos DB są szyfrowane w spoczynku i w transporcie, nie trzeba podejmować żadnych działań. Innym sposobem, aby umieścić to jest to, że szyfrowanie w spoczynku jest "na" domyślnie. Nie ma żadnych formantów, aby go wyłączyć lub włączyć. Usługa Azure Cosmos DB używa szyfrowania AES-256 we wszystkich regionach, w których konto jest uruchomione.

Włącz usuwanie programowe w magazynie kluczy, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

Poznaj szyfrowanie danych w usłudze Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Jak włączyć usuwanie programowe w przechowalni kluczy:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Współdzielone

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Reagowanie na incydenty](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Tworzenie przewodnika po zdarzeniach

**Wskazówki:** Tworzenie przewodnika po zdarzeniach dla twojej organizacji. Upewnij się, że istnieją pisemne plany reagowania na incydenty, które definiują wszystkie role personelu, a także fazy obsługi incydentów/zarządzania od wykrywania do przeglądu po incydencie.

Możesz również wykorzystać przewodnik obsługi incydentów związanych z bezpieczeństwem komputerowym NIST, aby pomóc w stworzeniu własnego planu reagowania na incydenty:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

Jak skonfigurować automatyzacje przepływu pracy w usłudze Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Wskazówki dotyczące tworzenia własnego procesu reagowania na incydenty bezpieczeństwa:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia incydentu w centrum reagowania na zabezpieczenia firmy Microsoft:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Tworzenie procedury oceniania incydentów i ustalania priorytetów

**Wskazówki:** Centrum zabezpieczeń przypisuje ważność do każdego alertu, aby ułatwić ustalanie priorytetów alertów, które powinny być najpierw zbadane. Ważność jest oparta na tym, jak pewny siebie jest usługa Security Center w znalezieniu lub analizach użytych do wydania alertu, a także poziom zaufania, że za działaniem, które doprowadziło do alertu, było złośliwe intencje.

Dodatkowo wyraźnie zaznacz subskrypcje (np. nieprod) i utworzyć system nazewnictwa, aby jasno identyfikować i kategoryzować zasoby platformy Azure.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki**: Przeprowadzaj ćwiczenia, aby przetestować możliwości reagowania na incydenty w systemach na regularnym rytmie. Zidentyfikować słabe punkty i luki oraz w razie potrzeby dokonać przeglądu planu.

Zapoznaj się z publikacją NIST: Przewodnik po testach, szkoleniach i programach ćwiczeń dla planów i możliwości IT:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Podaj dane kontaktowe zdarzeń bezpieczeństwa i konfiguruj powiadomienia o alertach dla incydentów bezpieczeństwa

**Wskazówki:** Informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do kontaktu się z Użytkownikiem, jeśli Centrum Microsoft Security Response Center (MSRC) odkryje, że dane klienta zostały uzyskiane przez osobę niezgodną z prawem lub nieautoryzowaną.  Przejrzyj incydenty po fakcie, aby upewnić się, że problemy zostały rozwiązane.

Jak ustawić kontakt zabezpieczeń usługi Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Włącz alerty bezpieczeństwa do systemu reagowania na incydenty

**Wskazówki:** Eksportuj alerty i zalecenia usługi Azure Security Center przy użyciu funkcji ciągłego eksportowania. Ciągły eksport umożliwia eksportowanie alertów i zaleceń ręcznie lub w sposób ciągły. Możesz użyć łącznika danych usługi Azure Security Center do przesyłania strumieniowego alertów Sentinel.

Jak skonfigurować eksport ciągły:https://docs.microsoft.com/azure/security-center/continuous-export

Jak przesyłać strumieniowo alerty do usługi Azure Sentinel:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzacja reakcji na alerty bezpieczeństwa

**Wskazówki:** Użyj funkcji automatyzacji przepływu pracy w usłudze Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Aplikacji logiki" w alertach i zaleceniach dotyczących zabezpieczeń.

Jak skonfigurować automatyzację przepływu pracy i aplikacje logiki:https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola bezpieczeństwa: Testy penetracyjne i Ćwiczenia zespołu Czerwonego](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Przeprowadzaj regularne testy penetracyjne zasobów platformy Azure i zapewnij korygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń w ciągu 60 dni

**Wskazówki:** Postępuj zgodnie z regułami zaangażowania firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszają zasad firmy Microsoft:https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Więcej informacji na temat strategii firmy Microsoft i wykonywania testów red teaming i testów penetracji witryn na żywo w infrastrukturze, usługach i aplikacjach zarządzanych przez firmę Microsoft można znaleźć tutaj:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Współdzielone

## <a name="next-steps"></a>Następne kroki

- Zobacz [wzorzec zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Dowiedz się więcej o [planach bazowych zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
