---
title: Usługa Azure Security Baseline dla pamięci podręcznej platformy Azure dla redis
description: Usługa Azure Security Baseline dla pamięci podręcznej platformy Azure dla redis
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 172ac4e13201457f62d722236dff130a312cfdeb
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81755514"
---
# <a name="azure-security-baseline-for-azure-cache-for-redis"></a>Usługa Azure Security Baseline dla pamięci podręcznej platformy Azure dla redis

Usługa Azure Security Baseline for Azure Cache for Redis zawiera zalecenia, które pomogą Ci poprawić poziom zabezpieczeń wdrożenia.

Punkt odniesienia dla tej usługi jest pobierany z [usługi Azure Security Benchmark w wersji 1.0,](https://docs.microsoft.com/azure/security/benchmarks/overview)która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure za pomocą naszych najlepszych wskazówek dotyczących rozwiązań.

Aby uzyskać więcej informacji, zobacz [omówienie planów bazowych zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Bezpieczeństwo sieci](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure w sieci wirtualnej

**Wskazówki:** Wdrażanie pamięci podręcznej platformy Azure dla wystąpienia Redis w sieci wirtualnej (VNet). Sieć wirtualna to sieć prywatna w chmurze. Gdy usługa Azure Cache for Redis wystąpienie jest skonfigurowany za pomocą sieci wirtualnej, nie jest publicznie adresowalna i można uzyskać do nich dostęp tylko z maszyn wirtualnych i aplikacji w sieci wirtualnej.

Można również określić reguły zapory z początkowym i końcowym zakresem adresów IP. Gdy skonfigurowano reguły zapory, tylko połączenia klientów z określonych zakresów adresów IP mogą łączyć się z pamięcią podręczną.

Jak skonfigurować obsługę sieci wirtualnej dla pamięci podręcznej platformy Azure premium dla redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Jak skonfigurować reguły zapory usługi Azure Cache dla redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki:** Gdy maszyny wirtualne są wdrażane w tej samej sieci wirtualnej co usługa Azure Cache dla wystąpienia Redis, można użyć sieciowych grup zabezpieczeń (NSG), aby zmniejszyć ryzyko eksfiltracji danych. Włącz dzienniki przepływu nsg i wysyłaj dzienniki do konta usługi Azure Storage w celu inspekcji ruchu. Można również wysłać dzienniki przepływu nsg do obszaru roboczego usługi Log Analytics i użyć usługi Traffic Analytics, aby zapewnić wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety usługi Traffic Analytics to możliwość wizualizacji aktywności sieciowej i identyfikowania punktów zapalnych, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i określania błędnych konfiguracji sieci.

Jak włączyć dzienniki przepływu nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak włączyć i korzystać z analizy ruchu:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrona krytycznych aplikacji internetowych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci web działających w usłudze Azure App Service lub zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** Wdrożenie sieci wirtualnej platformy Azure zapewnia zwiększone zabezpieczenia i izolację pamięci podręcznej platformy Azure dla programu Redis, a także podsieci, zasad kontroli dostępu i innych funkcji w celu dalszego ograniczenia dostępu. Po wdrożeniu w sieci wirtualnej usługa Azure Cache for Redis nie jest publicznie adresowalna i można uzyskać do niej dostęp tylko z maszyn wirtualnych i aplikacji w sieci wirtualnej.

Włącz standard ochrony przed atakami DDoS w sieciach wirtualnych skojarzonych z pamięcią podręczną platformy Azure dla wystąpień Redis, aby chronić przed rozproszonymi atakami typu "odmowa usługi" (DDoS). Usługa Azure Security Center Integrated Threat Intelligence umożliwia odmawianie komunikacji ze znanymi złośliwymi lub nieużywanym internetowymi adresami IP.

Jak skonfigurować obsługę sieci wirtualnej dla pamięci podręcznej platformy Azure premium dla redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Zarządzanie standardem ochrony przed atakami DDoS platformy Azure przy użyciu portalu Azure:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Rejestrowanie pakietów sieciowych i dzienników przepływu

**Wskazówki:** Gdy maszyny wirtualne są wdrażane w tej samej sieci wirtualnej co usługa Azure Cache dla wystąpienia Redis, można użyć sieciowych grup zabezpieczeń (NSG), aby zmniejszyć ryzyko eksfiltracji danych. Włącz dzienniki przepływu nsg i wysyłaj dzienniki do konta usługi Azure Storage w celu inspekcji ruchu. Można również wysłać dzienniki przepływu nsg do obszaru roboczego usługi Log Analytics i użyć usługi Traffic Analytics, aby zapewnić wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety usługi Traffic Analytics to możliwość wizualizacji aktywności sieciowej i identyfikowania punktów zapalnych, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i określania błędnych konfiguracji sieci.

Jak włączyć dzienniki przepływu nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak włączyć i korzystać z analizy ruchu:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Wdrażanie opartych na sieci systemów wykrywania włamań/włamań (IDS/IPS)

**Wskazówki:** Podczas korzystania z usługi Azure Cache for Redis z aplikacjami sieci web uruchomionymi w usłudze Azure App Service lub wystąpieniami obliczeniowymi należy wdrożyć wszystkie zasoby w sieci wirtualnej platformy Azure (VNet) i zabezpieczyć za pomocą zapory aplikacji sieci Web azure (WAF) w bramie aplikacji sieci Web. Skonfiguruj WAF do działania w trybie zapobiegania. Tryb zapobiegania blokuje włamania i ataki, które wykrywają reguły. Osoba atakująca otrzymuje wyjątek "403 nieautoryzowany dostęp", a połączenie zostanie zamknięte. Tryb zapobiegania rejestruje takie ataki w dziennikach WAF.

Alternatywnie można wybrać ofertę z portalu Azure Marketplace, która obsługuje funkcje IDS/IPS z możliwością inspekcji ładunku i/lub wykrywania anomalii.

Poznaj możliwości usługi Azure WAF:

https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview

Jak wdrożyć usługę Azure WAF:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Zarządzanie ruchem do aplikacji internetowych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci web działających w usłudze Azure App Service lub zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizowanie złożoności i administracyjnych narzutów administracyjnych dotyczących zasad bezpieczeństwa sieci

**Wskazówki:** Użyj tagów usługi sieci wirtualnej do definiowania kontroli dostępu do sieci w sieciowych grupach zabezpieczeń (NSG) lub Zaporze platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić lub odmówić ruchu dla odpowiedniej usługi. Firma Microsoft zarządza prefiksami adresów objętymi tagiem usługi i automatycznie aktualizuje tag usługi w miarę zmiany adresów.

Można również użyć grup zabezpieczeń aplikacji (ASG), aby uprościć złożoną konfigurację zabezpieczeń. Sieci ASG umożliwiają konfigurowanie zabezpieczeń sieci jako naturalnego rozszerzenia struktury aplikacji, umożliwiając grupowanie maszyn wirtualnych i definiowanie zasad zabezpieczeń sieci na podstawie tych grup.

Tagi usługi sieci wirtualnej:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Grupy zabezpieczeń aplikacji:

https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych związanych z pamięcią podręczną azure dla wystąpień Redis za pomocą zasad platformy Azure. Użyj aliasów zasad platformy Azure w obszarach nazw "Microsoft.Cache" i "Microsoft.Network", aby utworzyć niestandardowe zasady do inspekcji lub wymuszenia konfiguracji sieciowej pamięci podręcznej azure dla wystąpień Redis. Można również korzystać z wbudowanych definicji zasad, takich jak:

Należy włączyć tylko bezpieczne połączenia z pamięcią podręczną Redis

Standard ochrony przed atakami DDoS powinien być włączony

Można również użyć planów platformy Azure, aby uprościć wdrożenia platformy Azure na dużą skalę przez artefakty kluczowe środowisko pakowania, takie jak szablony usługi Azure Resource Manager (ARM), kontrola dostępu oparta na rolach (RBAC) i zasady, w jednej definicji planu. Łatwo zastosować plan do nowych subskrypcji i środowisk i dostosować kontroli i zarządzania poprzez przechowywanie wersji.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak utworzyć plan platformy Azure:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="110-document-traffic-configuration-rules"></a>1.10: Przepisy konfiguracji ruchu dokumentów

**Wskazówki:** Użyj tagów dla zasobów sieciowych skojarzonych z pamięcią podręczną platformy Azure dla wdrożenia Redis, aby logicznie zorganizować je w taksonomię.

Jak tworzyć i używać tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki:** Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z pamięcią podręczną platformy Azure dla wystąpień Redis. Tworzenie alertów w usłudze Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Jak tworzyć alerty w usłudze Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Rejestrowanie i monitorowanie](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Korzystanie z zatwierdzonych źródeł synchronizacji czasu

**Wskazówki**: Firma Microsoft przechowuje źródło czasu używane dla zasobów platformy Azure, takich jak Usługa Azure Cache dla redis dla znaczników czasu w dziennikach.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** Włącz ustawienia diagnostyczne dziennika aktywności platformy Azure i wyślij dzienniki do obszaru roboczego usługi Log Analytics, centrum zdarzeń platformy Azure lub konta magazynu platformy Azure w celu ich archiwum. Dzienniki aktywności zapewniają wgląd w operacje, które zostały wykonane w pamięci podręcznej platformy Azure dla wystąpień Redis na poziomie płaszczyzny sterowania. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" dla wszelkich operacji zapisu (PUT, POST, DELETE) wykonywanych na poziomie płaszczyzny sterowania dla pamięci podręcznej azure dla wystąpień Redis.

Jak włączyć ustawienia diagnostyczne dziennika aktywności platformy Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Włączanie rejestrowania inspekcji zasobów platformy Azure

**Wskazówki:** Włącz ustawienia diagnostyczne dziennika aktywności platformy Azure i wyślij dzienniki do obszaru roboczego usługi Log Analytics, centrum zdarzeń platformy Azure lub konta magazynu platformy Azure w celu ich archiwum. Dzienniki aktywności zapewniają wgląd w operacje, które zostały wykonane w pamięci podręcznej platformy Azure dla wystąpień Redis na poziomie płaszczyzny sterowania. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" dla wszelkich operacji zapisu (PUT, POST, DELETE) wykonywanych na poziomie płaszczyzny sterowania dla pamięci podręcznej azure dla wystąpień Redis.

Gdy metryki są dostępne po włączeniu ustawień diagnostycznych, rejestrowanie inspekcji na płaszczyźnie danych nie jest jeszcze dostępne dla usługi Azure Cache for Redis.

Jak włączyć ustawienia diagnostyczne dziennika aktywności platformy Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Zbieranie dzienników zabezpieczeń z systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** W usłudze Azure Monitor ustaw okres przechowywania dzienników dla obszarów roboczych usługi Log Analytics skojarzonych z pamięcią podręczną platformy Azure dla wystąpień Redis zgodnie z przepisami dotyczącymi zgodności obowiązującymi w organizacji.

Należy zauważyć, że rejestrowanie inspekcji na płaszczyźnie danych nie jest jeszcze dostępne dla usługi Azure Cache for Redis.

Jak ustawić parametry przechowywania dziennika:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorowanie i przeglądanie dzienników

**Wskazówki:** Włącz ustawienia diagnostyczne dziennika aktywności platformy Azure i wyślij dzienniki do obszaru roboczego usługi Log Analytics. Wykonywanie zapytań w usłudze Log Analytics w celu wyszukiwania terminów, identyfikowania trendów, analizowania wzorców i dostarczania wielu innych szczegółowych informacji na podstawie danych dziennika aktywności, które mogły zostać zebrane dla usługi Azure Cache for Redis.

Należy zauważyć, że rejestrowanie inspekcji na płaszczyźnie danych nie jest jeszcze dostępne dla usługi Azure Cache for Redis.

Jak włączyć ustawienia diagnostyczne dziennika aktywności platformy Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Jak zbierać i analizować dzienniki aktywności platformy Azure w obszarze roboczym usługi Log Analytics w usłudze Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Włączanie alertów o nietypowej aktywności

**Wskazówki:** Można skonfigurować do odbierania alertów na podstawie metryk i dzienników aktywności związanych z pamięcią podręczną platformy Azure dla wystąpień Redis. Usługa Azure Monitor umożliwia skonfigurowanie alertu do wysyłania powiadomień e-mail, wywoływania elementu webhook lub wywoływania aplikacji logiki azure.

Gdy metryki są dostępne po włączeniu ustawień diagnostycznych, rejestrowanie inspekcji na płaszczyźnie danych nie jest jeszcze dostępne dla usługi Azure Cache for Redis.

Jak skonfigurować alerty dla usługi Azure Cache for Redis:https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#alerts

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="28-centralize-anti-malware-logging"></a>2.8: Scentralizuj rejestrowanie przed złośliwym oprogramowaniem

**Wskazówki**: Nie dotyczy; Usługa Azure Cache for Redis nie przetwarza ani nie generuje dzienników związanych ze złośliwym oprogramowaniem.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="29-enable-dns-query-logging"></a>2.9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: Nie dotyczy; Usługa Azure Cache for Redis nie przetwarza ani nie tworzy dzienników związanych z usługą DNS.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="210-enable-command-line-audit-logging"></a>2.10: Włącz rejestrowanie inspekcji wiersza polecenia

**Wskazówki**: Nie dotyczy; niniejsza wytyczna jest przeznaczona dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Kontrola tożsamości i dostępu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Prowadzenie spisu rachunków administracyjnych

**Wskazówki:** Usługa Azure Active Directory (AD) ma wbudowane role, które muszą być jawnie przypisane i są możliwe do queryable. Użyj modułu programu Azure AD PowerShell do wykonywania zapytań ad hoc w celu odnajdowania kont, które są członkami grup administracyjnych.

Jak uzyskać rolę katalogu w usłudze Azure AD za pomocą programu PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Jak uzyskać członków roli katalogu w usłudze Azure AD za pomocą programu PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="32-change-default-passwords-where-applicable"></a>3.2: W stosownych przypadkach zmieniaj domyślne hasła

**Wskazówki:** Kontrolowanie dostępu płaszczyzny do pamięci podręcznej azure dla programu Redis jest kontrolowane za pośrednictwem usługi Azure Active Directory (AD). Usługa Azure AD nie ma pojęcia haseł domyślnych. 

Dostęp płaszczyzny danych do pamięci podręcznej Azure Cache for Redis jest kontrolowany za pomocą kluczy dostępu. Klucze te są używane przez klientów łączących się z pamięci podręcznej i mogą być generowane ponownie w dowolnym momencie.

Nie zaleca się tworzenia domyślnych haseł do aplikacji. Zamiast tego można przechowywać hasła w usłudze Azure Key Vault, a następnie użyć usługi Azure Active Directory, aby je pobrać.

Jak ponownie wygenerować pamięć podręczną usługi Azure dla kluczy dostępu Redis:https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#settings

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Współdzielone

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Korzystanie z dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych rachunków administracyjnych. Usługa Azure Security Center Identity and Access Management służy do monitorowania liczby kont administracyjnych.

Ponadto, aby ułatwić śledzenie dedykowanych kont administracyjnych, można użyć zaleceń z usługi Azure Security Center lub wbudowanych zasad platformy Azure, takich jak:

- Do subskrypcji powinien być przypisany więcej niż jeden właściciel

- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

Jak używać usługi Azure Security Center do monitorowania tożsamości i dostępu (Wersja zapoznawcza):https://docs.microsoft.com/azure/security-center/security-center-identity-access

Jak korzystać z zasad platformy Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Używanie logowania jednokrotnego (Logowanie jednokrotne) w usłudze Azure Active Directory

**Wskazówki:** Usługa Azure Cache for Redis używa kluczy dostępu do uwierzytelniania użytkowników i nie obsługuje logowania jednokrotnego (Logowania jednokrotnego) na poziomie płaszczyzny danych. Dostęp do płaszczyzny sterowania dla usługi Azure Cache for Redis jest dostępny za pośrednictwem interfejsu API REST i obsługuje funkcję SSO. Aby uwierzytelnić, ustaw nagłówek autoryzacji żądań na token sieci Web JSON, który można uzyskać z usługi Azure Active Directory.

Poznaj interfejs API usługi Azure Cache for Redis REST:https://docs.microsoft.com/rest/api/redis/

Poznaj ujedniaka ujednawcze za pomocą usługi Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Użyj uwierzytelniania wieloskładnikowego dla całego dostępu opartego na usłudze Azure Active Directory

**Wskazówki:** Włącz uwierzytelnianie wieloskładnikowe usługi Azure Active Directory (AD) i postępuj zgodnie z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem usługi Azure Security Center.

Jak włączyć usługę MFA na platformie Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak monitorować tożsamość i dostęp w usłudze Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Używanie dedykowanych maszyn (stacje robocze z uprzywilejowanym dostępem) do wszystkich zadań administracyjnych

**Wskazówki:** Użyj stacji roboczych dostępu uprzywilejowanego (PAW) z uwierzytelnianiem wieloskładnikowym (MFA) skonfigurowanym do logowania się i konfigurowania zasobów platformy Azure.

Dowiedz się więcej o stacjach roboczych z dostępem uprzywilejowanym:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak włączyć usługę MFA na platformie Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Rejestrowanie i ostrzeganie o podejrzanych działaniach z kont administracyjnych

**Wskazówki:** Użyj usługi Azure Active Directory (AD) Uprzywilejowane zarządzanie tożsamościami (PIM) do generowania dzienników i alertów, gdy podejrzane lub niebezpieczne działania występuje w środowisku.

Ponadto użyj wykrywania ryzyka usługi Azure AD, aby wyświetlić alerty i raporty dotyczące ryzykownych zachowań użytkowników.

Jak wdrożyć zarządzanie tożsamościami uprzywilejowanymi (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Poznaj wykrywanie ryzyka usługi Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** Konfigurowanie nazwanych lokalizacji w usłudze Azure Active Directory (AD) Dostęp warunkowy, aby zezwolić na dostęp tylko z określonych grup logicznych zakresów adresów IP lub krajów/regionów.

Jak skonfigurować nazwane lokalizacje na platformie Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="39-use-azure-active-directory"></a>3.9: Korzystanie z usługi Azure Active Directory

**Wskazówki:** Użyj usługi Azure Active Directory (AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych w spoczynku i podczas przesyłania. Usługa Azure AD również sole, skróty i bezpiecznie przechowuje poświadczenia użytkownika.

Nie można użyć uwierzytelniania usługi Azure AD dla bezpośredniego dostępu do usługi Azure Cache dla płaszczyzny danych usługi Redis, jednak poświadczenia usługi Azure AD mogą być używane do administrowania na poziomie płaszczyzny sterowania (tj. w witrynie Azure portal) do kontrolowania pamięci podręcznej Azure dla kluczy dostępu Redis.


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** Usługa Azure Active Directory (AD) udostępnia dzienniki ułatwiające odnajdowanie starych kont. Ponadto użyj przeglądów dostępu do tożsamości platformy Azure, aby skutecznie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkownika można regularnie przeglądać, aby upewnić się, że tylko prawo Użytkownicy mają stały dostęp. 

Opis raportowania usługi Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Jak korzystać z przeglądów dostępu tożsamości platformy Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych kont

**Wskazówki:** Masz dostęp do działania logowania usługi Azure Active Directory (AD), inspekcji i źródeł dziennika zdarzeń ryzyka, które umożliwiają integrację z usługą Azure Sentinel lub siem innej firmy.

Możesz usprawnić ten proces, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego usługi Log Analytics. Można skonfigurować żądane alerty dziennika w usłudze Log Analytics.

Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Jak na pokładzie usługi Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alert o odchyleniu zachowania logowania do konta

**Wskazówki:** W przypadku odchylenia zachowania logowania do konta na płaszczyźnie kontrolnej należy użyć funkcji ochrony tożsamości usługi Azure Active Directory (AD) i wykrywania ryzyka, aby skonfigurować automatyczne odpowiedzi do wykrytych podejrzanych akcji związanych z tożsamościami użytkowników. Można również pozyskiwania danych do usługi Azure Sentinel do dalszego badania.

Jak wyświetlić ryzykowne logowania usługi Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak skonfigurować i włączyć zasady ryzyka ochrony tożsamości:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Jak ować wobec pokład Azure Wartownik:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Zapewnienie firmie Microsoft dostępu do odpowiednich danych klientów podczas scenariuszy pomocy technicznej

**Wskazówki**: Jeszcze niedostępne; Skrytka klienta nie jest jeszcze obsługiwana w pamięci podręcznej Azure dla firmy Redis.

Lista usług obsługiwanych przez skrytki klienta:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Nie dotyczy

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Prowadzenie wykazu informacji poufnych

**Wskazówki:** Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają poufne informacje.

Jak tworzyć i używać tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemy izolowania przechowujące lub przetwarzające informacje poufne

**Wskazówki**: Implementowanie oddzielnych subskrypcji i/lub grup zarządzania do programowania, testowania i produkcji. Usługa Azure Cache dla wystąpień Redis powinny być oddzielone przez sieć wirtualną/podsieć i odpowiednio oznakowane. Opcjonalnie użyj zapory Usługi Azure Cache for Redis, aby zdefiniować reguły, aby tylko połączenia klientów z określonych zakresów adresów IP mogły łączyć się z pamięcią podręczną.

Jak utworzyć dodatkowe subskrypcje platformy Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć grupy zarządzania:

https://docs.microsoft.com/azure/governance/management-groups/create

Jak wdrożyć pamięć podręczną Azure Cache for Redis w sieci wirtualnej:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Jak skonfigurować reguły zapory usługi Azure Cache dla redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

Jak tworzyć i używać tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego przesyłania poufnych informacji

**Wskazówki**: Jeszcze niedostępne; funkcje identyfikacji danych, klasyfikacji i zapobiegania utracie nie są jeszcze dostępne dla usługi Azure Cache for Redis.

Firma Microsoft zarządza podstawową infrastrukturą usługi Azure Cache for Redis i wdrożyła ścisłe mechanizmy kontroli, aby zapobiec utracie lub ekspozycji danych klientów.

Poznaj ochronę danych klientów na platformie Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współdzielone

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Szyfrowanie wszystkich poufnych informacji podczas przesyłania

**Wskazówki:** Usługa Azure Cache for Redis domyślnie wymaga komunikacji szyfrowanej przez TLS. TLS wersje 1.0, 1.1 i 1.2 są obecnie obsługiwane. Jednak TLS 1.0 i 1.1 są na ścieżce do deprecation całej branży, więc należy użyć TLS 1.2, jeśli w ogóle możliwe. Jeśli biblioteka klienta lub narzędzie nie obsługuje protokołu TLS, włączenie połączeń niezaszyfrowanych można wykonać za pośrednictwem witryny Azure portal lub interfejsów API zarządzania. W takich przypadkach, gdy szyfrowane połączenia nie są możliwe, zaleca się umieszczenie pamięci podręcznej i aplikacji klienckiej w sieci wirtualnej.

Poznaj szyfrowanie podczas przesyłania dla usługi Azure Cache for Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-best-practices

Opis wymaganych portów używanych w scenariuszach pamięci podręcznej sieci wirtualnej:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet#outbound-port-requirements

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Współdzielone

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Identyfikowanie poufnych danych za pomocą aktywnego narzędzia do wykrywania

**Wskazówki:** Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla usługi Azure Cache for Redis. Oznaczanie wystąpień zawierających poufne informacje jako takie i implementowanie rozwiązania innych firm, jeśli jest to wymagane do celów zgodności.

W przypadku podstawowej platformy, która jest zarządzana przez firmę Microsoft, firma Microsoft traktuje całą zawartość klienta jako wrażliwą i dokłga mierze chroni przed utratą i ekspozycją danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje zestaw niezawodnych mechanizmów kontroli i możliwości ochrony danych.

Poznaj ochronę danych klientów na platformie Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Sterowanie dostępem do zasobów za pomocą narzędzia Azure RBAC

**Wskazówki:** Użyj kontroli dostępu opartej na rolach usługi Azure Active Directory (AAD), aby kontrolować dostęp do warstwy kontrolnej usługi Azure Cache for Redis (tj. 

Jak skonfigurować rbac na platformie Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Wymuszanie kontroli dostępu za pomocą funkcji zapobiegania utracie danych opartych na hoście

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

Firma Microsoft zarządza podstawową infrastrukturą usługi Azure Cache for Redis i wdrożyła ścisłe mechanizmy kontroli, aby zapobiec utracie lub ekspozycji danych klientów.

Poznaj ochronę danych klientów na platformie Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Szyfrowanie poufnych informacji w spoczynku

**Wskazówki:** Usługa Azure Cache for Redis przechowuje dane klientów w pamięci i chociaż jest silnie chroniona przez wiele formantów zaimplementowanych przez firmę Microsoft, pamięć nie jest domyślnie szyfrowana. Jeśli jest to wymagane przez organizację, zaszyfruj zawartość przed zapisaniem w pamięci podręcznej platformy Azure dla firmy Redis.

Jeśli używasz funkcji Azure Cache for Redis "Redis Data Persistence", dane są wysyłane do konta usługi Azure Storage, którego jesteś właścicielem i którymi zarządzasz. Trwałość można skonfigurować z bloku "Nowa pamięć podręczna platformy Azure dla redis" podczas tworzenia pamięci podręcznej i w menu Zasobów dla istniejących pamięci podręcznych premium.

Dane w usłudze Azure Storage są szyfrowane i odszyfrowywane w sposób przezroczysty przy użyciu 256-bitowego szyfrowania AES, jednego z najsilniejszych dostępnych szyfrów blokowych i zgodnego ze standardem FIPS 140-2. Nie można wyłączyć szyfrowania usługi Azure Storage. Szyfrowanie można polegać na kluczach zarządzanych przez firmę Microsoft do szyfrowania konta magazynu lub zarządzać szyfrowaniem za pomocą własnych kluczy.

Jak skonfigurować trwałość w pamięci podręcznej platformy Azure dla redis:https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Poznaj szyfrowanie kont usługi Azure Storage:https://docs.microsoft.com/azure/storage/common/storage-service-encryption

Poznaj ochronę danych klientów platformy Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Współdzielone

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Rejestrowanie i ostrzeganie o zmianach w krytycznych zasobach platformy Azure

**Wskazówki:** Użyj usługi Azure Monitor z dziennika aktywności platformy Azure do tworzenia alertów, gdy zmiany mają miejsce w wystąpieniach produkcyjnych usługi Azure Cache dla redis i innych krytycznych lub powiązanych zasobów.

Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Uruchamianie zautomatyzowanych narzędzi do skanowania luk w zabezpieczeniach

**Wskazówki:** Postępuj zgodnie z zaleceniami usługi Azure Security Center dotyczących zabezpieczania pamięci podręcznej platformy Azure dla wystąpień Redis i powiązanych zasobów.

Firma Microsoft wykonuje zarządzanie lukami w zabezpieczeniach w podstawowych systemach obsługujących usługę Azure Cache for Redis.

Poznaj zalecenia usługi Azure Security Center:https://docs.microsoft.com/azure/security-center/recommendations-reference

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Współdzielone

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami oprogramowania innych firm

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porównanie skanów luk w zabezpieczeniach typu back-to-back

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Użyj procesu oceny ryzyka, aby nadać priorytet usuwaniu wykrytych luk w zabezpieczeniach

**Wskazówki:** Firma Microsoft wykonuje zarządzanie lukami w zabezpieczeniach w podstawowych systemach obsługujących pamięć podręczną Azure cache dla programu Redis.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie zapasami i zasobami](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Korzystanie z odnajdowania zasobów platformy Azure

**Wskazówki:** Użyj usługi Azure Resource Graph do wykonywania zapytań/odnajdywać wszystkie zasoby (takie jak zasoby obliczeniowe, magazyn, sieć, porty i protokoły itp.) w ramach subskrypcji.  Upewnij się, odpowiednie (odczytu) uprawnienia w dzierżawie i wyliczyć wszystkie subskrypcje platformy Azure, a także zasoby w ramach subskrypcji.

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

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** Użyj tagowania, grup zarządzania i oddzielnych subskrypcji, w stosownych przypadkach, do organizowania i śledzenia pamięci podręcznej platformy Azure dla wystąpień Redis i powiązanych zasobów. Regularnie uzgadniaj zapasy i upewnij się, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

Ponadto użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Jak utworzyć dodatkowe subskrypcje platformy Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć grupy zarządzania:https://docs.microsoft.com/azure/governance/management-groups/create

Jak tworzyć i używać tagów:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Obsługa spisu zatwierdzonych zasobów i tytułów oprogramowania platformy Azure

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych i platformy Azure jako całości.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki:** Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

Niedozwolone typy zasobów

Dozwolone typy zasobów

Ponadto użyj usługi Azure Resource Graph do wykonywania zapytań/odnajdywanie zasobów w ramach subskrypcji.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak tworzyć zapytania za pomocą programu Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorowanie niezatwierdzonych aplikacji w zasobach obliczeniowych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Usuwanie niezatwierdzonych zasobów platformy Azure i aplikacji

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych i platformy Azure jako całości.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="68-use-only-approved-applications"></a>6.8: Używaj wyłącznie zatwierdzonych aplikacji

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="69-use-only-approved-azure-services"></a>6.9: Korzystaj tylko z zatwierdzonych usług platformy Azure

**Wskazówki:** Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

Niedozwolone typy zasobów

Dozwolone typy zasobów

Jak skonfigurować zasady platformy Azure i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odmówić określonego typu zasobu za pomocą usługi Azure Policy:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="610-implement-approved-application-list"></a>6.10: Wdrożenie listy zatwierdzonych wniosków

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Ograniczanie użytkownikom możliwości interakcji z usługą Azure Resources Manager za pomocą skryptów

**Wskazówki:** Skonfiguruj dostęp warunkowy platformy Azure, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resource Manager (ARM), konfigurując "Blokuj dostęp" dla aplikacji "Microsoft Azure Management".

Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do ARM:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Ograniczenie możliwości wykonywania skryptów przez użytkowników w zasobach obliczeniowych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizycznie lub logicznie segregować aplikacje wysokiego ryzyka

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci web działających w usłudze Azure App Service lub zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Bezpieczna konfiguracja](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Ustanawianie bezpiecznych konfiguracji dla wszystkich zasobów platformy Azure

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla pamięci podręcznej azure dla wystąpień Redis za pomocą zasad platformy Azure. Użyj aliasów zasad platformy Azure w obszarze nazw "Microsoft.Cache", aby utworzyć niestandardowe zasady do inspekcji lub wymuszenia konfiguracji pamięci podręcznej azure dla wystąpień Redis. Można również korzystać z wbudowanych definicji zasad związanych z pamięcią podręczną Azure dla wystąpień Redis, takich jak:

Należy włączyć tylko bezpieczne połączenia z pamięcią podręczną Redis

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

**Wskazówki:** Jeśli używasz niestandardowych definicji zasad platformy Azure lub szablonów usługi Azure Resource Manager dla pamięci podręcznej azure dla wystąpień Redis i powiązanych zasobów, użyj repozytorium platformy Azure, aby bezpiecznie przechowywać kod i zarządzać nim.

Jak przechowywać kod w usłudze Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentacja repozytorium platformy Azure:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpieczne przechowywanie obrazów niestandardowych systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Wdrażanie narzędzi do zarządzania konfiguracją systemu

**Wskazówki:** Użyj aliasów zasad platformy Azure w obszarze nazw "Microsoft.Cache", aby utworzyć niestandardowe zasady do ostrzegania, inspekcji i wymuszania konfiguracji systemu. Ponadto opracuj proces i potok do zarządzania wyjątkami zasad.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Wdrażanie automatycznego monitorowania konfiguracji usług platformy Azure

**Wskazówki:** Użyj aliasów zasad platformy Azure w obszarze nazw "Microsoft.Cache", aby utworzyć niestandardowe zasady do ostrzegania, inspekcji i wymuszania konfiguracji systemu. Użyj zasad platformy Azure [inspekcja], [odmówić] i [wdrożyć, jeśli nie istnieje], aby automatycznie wymusić konfiguracje dla pamięci podręcznej azure dla wystąpień Redis i powiązanych zasobów.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Wdrożenie automatycznego monitorowania konfiguracji systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** Dla maszyn wirtualnych platformy Azure lub aplikacji sieci web uruchomionych w usłudze Azure App Service używanych do uzyskiwania dostępu do pamięci podręcznej platformy Azure dla wystąpień Redis, użyj tożsamości usługi zarządzanej w połączeniu z usługą Azure Key Vault, aby uprościć i zabezpieczyć pamięć podręczną Azure Cache dla zarządzania tajnym usługi Redis. Upewnij się, że nietrwałe usuwanie magazynu kluczy jest włączone.

Jak zintegrować się z tożsamościami zarządzanymi platformy Azure:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Jak utworzyć przechowalnię kluczy:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Jak zapewnić uwierzytelnianie usługi Key Vault z tożsamością zarządzaną:

https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** Dla maszyn wirtualnych platformy Azure lub aplikacji sieci web uruchomionych w usłudze Azure App Service używanych do uzyskiwania dostępu do pamięci podręcznej platformy Azure dla wystąpień Redis, użyj tożsamości usługi zarządzanej w połączeniu z usługą Azure Key Vault, aby uprościć i zabezpieczyć pamięć podręczną Azure Cache dla zarządzania tajnym usługi Redis. Upewnij się, że włączona jest funkcja usuwania nietrwałego przechowalni kluczy.

Użyj tożsamości zarządzanych, aby zapewnić usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory. Tożsamości zarządzane umożliwia uwierzytelnianie do dowolnej usługi, która obsługuje uwierzytelnianie usługi AAD, w tym usługi Azure Key Vault, bez żadnych poświadczeń w kodzie.

Jak skonfigurować tożsamości zarządzane:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Jak zintegrować się z tożsamościami zarządzanymi platformy Azure:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

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

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład usługa Azure App Service), jednak nie jest uruchamiana na zawartości klienta.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Przed skanowaniem plików, które mają zostać przesłane do zasobów platformy Azure nieoliczonych

**Wskazówki:** Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Usługa Azure Cache for Redis), jednak nie działa na zawartości klienta.

Wstępnie skanuj dowolną zawartość przekazywanych do nie obliczających zasobów platformy Azure, takich jak usługa App Service, magazyn usługi Data Lake, magazyn obiektów Blob, usługa Azure Database for PostgreSQL itp. Firma Microsoft nie może uzyskać dostępu do danych użytkownika w takich przypadkach.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Upewnij się, że oprogramowanie i podpisy chroniące przed złośliwym oprogramowaniem są aktualizowane

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Usługa Azure Cache for Redis), jednak nie działa na zawartości klienta.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Odzyskiwanie danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnienie regularnych automatycznych kopii zapasowych

**Wskazówki**: Włącz trwałość Redis. Trwałość Redis umożliwia utrwalanie danych przechowywanych w redis. Można również robić migawki i tworzenie kopii zapasowych danych, które można załadować w przypadku awarii sprzętu. Jest to ogromna przewaga nad warstwą Podstawowa lub Standardowa, w której wszystkie dane są przechowywane w pamięci i może dopaść się potencjalna utrata danych w przypadku awarii, gdy węzły pamięci podręcznej są w dół.

Można również użyć usługi Azure Cache do eksportu redis. Eksport umożliwia eksportowanie danych przechowywanych w pamięci podręcznej platformy Azure dla redis do plików RDB zgodnych z redis. Za pomocą tej funkcji można przenieść dane z jednej pamięci podręcznej platformy Azure dla wystąpienia Redis do innego lub do innego serwera Redis. Podczas procesu eksportowania plik tymczasowy jest tworzony na maszynie wirtualnej, która obsługuje wystąpienie serwera Usługi Azure Cache for Redis, a plik jest przekazywalny do wyznaczonego konta magazynu. Po zakończeniu operacji eksportowania ze stanem sukcesu lub niepowodzenia plik tymczasowy jest usuwany.

Jak włączyć trwałość Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Jak używać usługi Azure Cache do eksportu redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Wykonywanie kompletnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki**: Włącz trwałość Redis. Trwałość Redis umożliwia utrwalanie danych przechowywanych w redis. Można również robić migawki i tworzenie kopii zapasowych danych, które można załadować w przypadku awarii sprzętu. Jest to ogromna przewaga nad warstwą Podstawowa lub Standardowa, w której wszystkie dane są przechowywane w pamięci i może dopaść się potencjalna utrata danych w przypadku awarii, gdy węzły pamięci podręcznej są w dół.

Można również użyć usługi Azure Cache do eksportu redis. Eksport umożliwia eksportowanie danych przechowywanych w pamięci podręcznej platformy Azure dla redis do plików RDB zgodnych z redis. Za pomocą tej funkcji można przenieść dane z jednej pamięci podręcznej platformy Azure dla wystąpienia Redis do innego lub do innego serwera Redis. Podczas procesu eksportowania plik tymczasowy jest tworzony na maszynie wirtualnej, która obsługuje wystąpienie serwera Usługi Azure Cache for Redis, a plik jest przekazywalny do wyznaczonego konta magazynu. Po zakończeniu operacji eksportowania ze stanem sukcesu lub niepowodzenia plik tymczasowy jest usuwany.

Jeśli używasz usługi Azure Key Vault do przechowywania poświadczeń dla pamięci podręcznej platformy Azure dla wystąpień Redis, upewnij się, że regularne automatyczne kopie zapasowe kluczy.

Jak włączyć trwałość Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Jak używać usługi Azure Cache do eksportu redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

Jak wykonać kopię zapasową kluczy magazynu kluczy:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Sprawdzanie poprawności wszystkich kopii zapasowych, w tym kluczy zarządzanych przez klienta

**Wskazówki:** Użyj pamięci podręcznej Azure dla importu Redis. Import może służyć do przynoszenia plików RDB zgodnych z Redis z dowolnego serwera Redis działającego w dowolnej chmurze lub środowisku, w tym redis działającego w systemie Linux, Windows lub dowolnym dostawcy chmury, takiego jak Amazon Web Services i innych. Importowanie danych to prosty sposób na utworzenie pamięci podręcznej z wstępnie wypełnionymi danymi. Podczas procesu importowania usługa Azure Cache for Redis ładuje pliki RDB z magazynu platformy Azure do pamięci, a następnie wstawia klucze do pamięci podręcznej.

Okresowo przetestuj przywracanie danych wpisów tajnych usługi Azure Key Vault.

Jak używać pamięci podręcznej Azure cache do importowania redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

Jak przywrócić klucze vault secrets:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zapewnienie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** Usługa Azure Cache dla kopii zapasowych Redis z redis export i trwałość Redis są przechowywane na wybranym koncie usługi Azure Storage. Dane w usłudze Azure Storage są szyfrowane i odszyfrowywane w sposób przezroczysty przy użyciu 256-bitowego szyfrowania AES, jednego z najsilniejszych dostępnych szyfrów blokowych i zgodnego ze standardem FIPS 140-2. Nie można wyłączyć szyfrowania usługi Azure Storage. Szyfrowanie można polegać na kluczach zarządzanych przez firmę Microsoft do szyfrowania konta magazynu lub zarządzać szyfrowaniem za pomocą własnych kluczy.

Poznaj szyfrowanie kont usługi Azure Storage:https://docs.microsoft.com/azure/storage/common/storage-service-encryption

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Microsoft

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Reagowanie na incydenty](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Tworzenie przewodnika po zdarzeniach

**Wskazówki:** Tworzenie przewodnika po zdarzeniach dla twojej organizacji. Upewnij się, że istnieją pisemne plany reagowania na incydenty, które definiują wszystkie role personelu, a także fazy obsługi incydentów/zarządzania od wykrywania do przeglądu po incydencie.

Jak skonfigurować automatyzacje przepływu pracy w usłudze Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Wskazówki dotyczące tworzenia własnego procesu reagowania na incydenty bezpieczeństwa:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia incydentu w centrum reagowania na zabezpieczenia firmy Microsoft:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Klient może również wykorzystać przewodnik obsługi incydentów związanych z bezpieczeństwem komputerowym NIST, aby pomóc w stworzeniu własnego planu reagowania na incydenty:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Tworzenie procedury oceniania incydentów i ustalania priorytetów

**Wskazówki:** Centrum zabezpieczeń przypisuje ważność do każdego alertu, aby ułatwić ustalanie priorytetów alertów, które powinny być najpierw zbadane. Ważność opiera się na tym, jak pewny jest usługa Security Center w znalezieniu lub analityczna używana do wystawiania alertu, a także poziom zaufania, że za działaniem, które doprowadziło do alertu, było złośliwe intencje.

Dodatkowo wyraźnie zaznacz subskrypcje (np. nieprod) i utworzyć system nazewnictwa, aby jasno identyfikować i kategoryzować zasoby platformy Azure.

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki**: Przeprowadzaj ćwiczenia, aby przetestować możliwości reagowania na incydenty w systemach na regularnym rytmie. Zidentyfikować słabe punkty i luki oraz w razie potrzeby dokonać przeglądu planu.

Zapoznaj się z publikacją NIST: Przewodnik po testach, szkoleniach i programach ćwiczeń dla planów i możliwości IT:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Podaj dane kontaktowe zdarzeń bezpieczeństwa i konfiguruj powiadomienia o alertach dla incydentów bezpieczeństwa

**Wskazówki:** Informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do kontaktu się z Użytkownikiem, jeśli Centrum Microsoft Security Response Center (MSRC) odkryje, że dane klienta zostały uzyskiane przez osobę niezgodną z prawem lub nieautoryzowaną.  Przejrzyj incydenty po fakcie, aby upewnić się, że problemy zostały rozwiązane.

Jak ustawić kontakt zabezpieczeń usługi Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Włącz alerty bezpieczeństwa do systemu reagowania na incydenty

**Wskazówki:** Eksportuj alerty i zalecenia usługi Azure Security Center przy użyciu funkcji ciągłego eksportowania. Ciągły eksport umożliwia eksportowanie alertów i zaleceń ręcznie lub w sposób ciągły. Możesz użyć łącznika danych usługi Azure Security Center do przesyłania strumieniowego alertów Sentinel.

Jak skonfigurować eksport ciągły:

https://docs.microsoft.com/azure/security-center/continuous-export

Jak przesyłać strumieniowo alerty do usługi Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzacja reakcji na alerty bezpieczeństwa

**Wskazówki:** Użyj funkcji automatyzacji przepływu pracy w usłudze Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Aplikacji logiki" w alertach i zaleceniach dotyczących zabezpieczeń.

Jak skonfigurować automatyzację przepływu pracy i aplikacje logiki:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola bezpieczeństwa: Testy penetracyjne i Ćwiczenia zespołu Czerwonego](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Przeprowadzaj regularne testy penetracyjne zasobów platformy Azure i zapewnij korygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń w ciągu 60 dni

**Wskazówki:** Postępuj zgodnie z regułami zaangażowania firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszają zasad firmy Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Więcej informacji na temat strategii firmy Microsoft i wykonywania testów red teaming i testów penetracji witryn na żywo w infrastrukturze, usługach i aplikacjach zarządzanych przez firmę Microsoft można znaleźć tutaj: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Współdzielone

## <a name="next-steps"></a>Następne kroki

- Zobacz [wzorzec zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Dowiedz się więcej o [planach bazowych zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
