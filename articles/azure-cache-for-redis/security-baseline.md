---
title: Punkt odniesienia zabezpieczeń platformy Azure dla Azure Cache for Redis
description: Punkt Azure Cache for Redis zabezpieczeń zawiera wskazówki proceduralne i zasoby dotyczące wdrażania zaleceń dotyczących zabezpieczeń określonych w te testach porównawczych zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: cache
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 0397f53c54f488f6840e35212de3e51624f360d7
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830002"
---
# <a name="azure-security-baseline-for-azure-cache-for-redis"></a>Punkt odniesienia zabezpieczeń platformy Azure dla Azure Cache for Redis

Ten punkt odniesienia zabezpieczeń stosuje wskazówki z testu porównawczego zabezpieczeń platformy [Azure w wersji 1.0](../security/benchmarks/overview-v1.md) do Azure Cache for Redis. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontrolek zabezpieczeń zdefiniowanych** przez test porównawczy zabezpieczeń platformy Azure i powiązanych wskazówek dotyczących Azure Cache for Redis. **Kontrolki,** które nie Azure Cache for Redis zostały wykluczone.

 
Aby zobaczyć, Azure Cache for Redis mapuje całkowicie na test porównawczy zabezpieczeń platformy Azure, zobacz pełny [Azure Cache for Redis mapowania punktów odniesienia zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1. Ochrona zasobów platformy Azure w sieciach wirtualnych

**Wskazówki:** wdrażanie Azure Cache for Redis w sieci wirtualnej. Sieć wirtualna to sieć prywatna w chmurze. Jeśli wystąpienie Azure Cache for Redis jest skonfigurowane przy użyciu sieci wirtualnej, nie jest publicznie adresowalne i można uzyskać do niego dostęp tylko z poziomu maszyn wirtualnych i aplikacji w sieci wirtualnej.

Można również określić reguły zapory z zakresem adresów IP rozpoczęcia i zakończenia. Po skonfigurowaniu reguł zapory tylko połączenia klientów z określonych zakresów adresów IP mogą łączyć się z pamięcią podręczną.

- [Jak skonfigurować Virtual Network pomocy technicznej dla usługi Premium Azure Cache for Redis](cache-how-to-premium-vnet.md)

- [Jak skonfigurować reguły Azure Cache for Redis zapory](./cache-configure.md#firewall)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki:** w przypadku Virtual Machines w tej samej sieci wirtualnej co wystąpienie usługi Azure Cache for Redis można użyć sieciowych grup zabezpieczeń w celu zmniejszenia ryzyka eksfiltracji danych. Włącz dzienniki przepływu sieciowej organizacji zabezpieczeń i wysyłaj dzienniki na konto usługi Azure Storage w celu inspekcji ruchu. Możesz również wysyłać dzienniki przepływu sieciowych grup zabezpieczeń do obszaru roboczego usługi Log Analytics i używać Analiza ruchu, aby zapewnić wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety usługi Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania punktów aktywności, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i identyfikowania błędnych konfiguracji sieci.

- [Jak włączyć dzienniki przepływu sieciowej organizacji zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrona krytycznych aplikacji internetowych

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla aplikacji internetowych działających w Azure App Service zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** Wdrożenie usługi Azure Virtual Network (VNet) zapewnia większe bezpieczeństwo i izolację dla usługi Azure Cache for Redis, a także podsieci, zasad kontroli dostępu i innych funkcji w celu dalszego ograniczenia dostępu. W przypadku wdrożenia w sieci wirtualnej Azure Cache for Redis adresowany publicznie i jest dostępny tylko z poziomu maszyn wirtualnych i aplikacji w sieci wirtualnej.

Włącz DDoS Protection Standardowa w sieciach wirtualnych skojarzonych z wystąpieniami usługi Azure Cache for Redis, aby chronić się przed rozproszonymi atakami typu "odmowa usługi" (DDoS). Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby uniemożliwić komunikację ze znanymi złośliwymi lub nieużywanymi internetowymi adresami IP.

- [Jak skonfigurować obsługę Virtual Network dla usługi Premium Azure Cache for Redis](cache-how-to-premium-vnet.md)

- [Zarządzanie Azure DDoS Protection Standardowa przy użyciu Azure Portal](../ddos-protection/manage-ddos-protection.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="15-record-network-packets"></a>1.5. Rejestrowanie pakietów sieciowych

**Wskazówki:** Gdy maszyny wirtualne są wdrażane w tej samej sieci wirtualnej co wystąpienie usługi Azure Cache for Redis, można użyć sieciowych grup zabezpieczeń w celu zmniejszenia ryzyka eksfiltracji danych. Włącz dzienniki przepływu sieciowej organizacji zabezpieczeń i wysyłaj dzienniki na konto usługi Azure Storage w celu inspekcji ruchu. Możesz również wysyłać dzienniki przepływu sieciowych grup zabezpieczeń do obszaru roboczego usługi Log Analytics i używać usługi Analiza ruchu do zapewnienia wglądu w przepływ ruchu w chmurze platformy Azure. Niektóre zalety usługi Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania punktów aktywności, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i identyfikowania błędnych konfiguracji sieci.

- [How to Enable NSG Flow Logs](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6. Wdrażanie sieciowych systemów wykrywania/zapobiegania włamań (IDS/IPS)

**Wskazówki:** W przypadku korzystania z usługi Azure Cache for Redis z aplikacjami internetowymi uruchomionymi w usłudze Azure App Service lub wystąpieniach obliczeniowych należy wdrożyć wszystkie zasoby w usłudze Azure Virtual Network (VNet) i zabezpieczyć je przy użyciu usługi Azure Web Application Firewall (WAF) w sieci Web Application Gateway. Skonfiguruj uruchamianie aplikacji WAF w trybie zapobiegania. Tryb zapobiegania blokuje włamania i ataki wykrywane przez reguły. Osoba atakująca otrzymuje wyjątek "403 brak autoryzacji dostępu" i połączenie jest zamykane. Tryb zapobiegania rejestruje takie ataki w dziennikach WAF.

Alternatywnie możesz wybrać ofertę z usługi Azure Marketplace która obsługuje funkcje identyfikatorów/adresów IPS z możliwościami inspekcji ładunku i/lub wykrywania anomalii.

- [Opis możliwości usługi Azure WAF](../web-application-firewall/ag/ag-overview.md)

- [Jak wdrożyć usługę Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Zarządzanie ruchem do aplikacji internetowych

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla aplikacji internetowych działających w Azure App Service zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8. Zminimalizuj złożoność i koszty administracyjne związane z regułami zabezpieczeń sieci

**Wskazówki:** użyj tagów usługi dla sieci wirtualnej do zdefiniowania kontroli dostępu do sieci w sieciowych grupach zabezpieczeń lub grupach Azure Firewall. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródła lub miejsca docelowego reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odmówić. Firma Microsoft zarządza prefiksami adresów obejmującymi tag usługi i automatycznie aktualizuje tag usługi w przypadku zmiany adresów.

Możesz również użyć grup zabezpieczeń aplikacji (ASG), aby uprościć złożoną konfigurację zabezpieczeń. Grupy zabezpieczeń aplikacji umożliwiają skonfigurowanie zabezpieczeń sieci jako naturalnego rozszerzenia struktury aplikacji, co umożliwia grupowanie maszyn wirtualnych i definiowanie zasad zabezpieczeń sieci na podstawie tych grup.

- [Tagi usługi dla sieci wirtualnej](../virtual-network/service-tags-overview.md)

- [Grupy zabezpieczeń aplikacji](../virtual-network/network-security-groups-overview.md#application-security-groups)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych powiązanych z wystąpieniami Azure Cache for Redis za pomocą Azure Policy. Użyj Azure Policy aliasów w przestrzeniach nazw "Microsoft.Cache" i "Microsoft.Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci Azure Cache for Redis wystąpień. Możesz również korzystać z wbudowanych definicji zasad, takich jak:
- Należy włączyć tylko bezpieczne Redis Cache połączenia z siecią

- DDoS Protection Standardowa powinna być włączona

Usługi Azure Blueprints można również użyć do uproszczenia wdrożeń platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów środowiska, takich jak szablony usługi Azure Resource Manager, kontrola dostępu oparta na rolach (RBAC) platformy Azure i zasady, w pojedynczej definicji strategii. Łatwe stosowanie strategii do nowych subskrypcji i środowisk oraz dostosowywanie kontroli i zarządzania za pomocą zarządzania wersjami.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak utworzyć usługę Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentuj reguły konfiguracji ruchu

**Wskazówki:** użyj tagów dla zasobów sieciowych skojarzonych Azure Cache for Redis wdrożenia w celu logicznego zorganizowania ich w taksonomię.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Używanie zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki:** Użyj dziennika aktywności platformy Azure, aby monitorować konfiguracje zasobów sieciowych i wykrywać zmiany zasobów sieciowych związanych Azure Cache for Redis wystąpieniami sieci. Utwórz alerty w Azure Monitor, które będą wyzwalane w przypadku zmiany krytycznych zasobów sieciowych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Jak tworzyć alerty w Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: rejestrowanie i monitorowanie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2. Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** Włącz ustawienia diagnostyczne dziennika aktywności platformy Azure i wysyłaj dzienniki do obszaru roboczego usługi Log Analytics, centrum zdarzeń platformy Azure lub konta usługi Azure Storage w celu archiwizacji. Dzienniki aktywności zapewniają wgląd w operacje, które zostały wykonane Azure Cache for Redis wystąpieniach na poziomie płaszczyzny sterowania. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" dla wszystkich operacji zapisu (PUT, POST, DELETE) wykonywanych na poziomie płaszczyzny sterowania dla wystąpień Azure Cache for Redis danych.

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki:** Włącz ustawienia diagnostyczne dziennika aktywności platformy Azure i wysyłaj dzienniki do obszaru roboczego usługi Log Analytics, centrum zdarzeń platformy Azure lub konta usługi Azure Storage w celu archiwizacji. Dzienniki aktywności zapewniają wgląd w operacje, które zostały wykonane Azure Cache for Redis wystąpieniach na poziomie płaszczyzny sterowania. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" dla wszystkich operacji zapisu (PUT, POST, DELETE) wykonywanych na poziomie płaszczyzny sterowania dla wystąpień Azure Cache for Redis danych.

Mimo że metryki są dostępne po włączeniu ustawień diagnostycznych, rejestrowanie inspekcji na płaszczyźnie danych nie jest jeszcze dostępne dla Azure Cache for Redis.

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="25-configure-security-log-storage-retention"></a>2.5. Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** w Azure Monitor ustaw okres przechowywania dzienników dla obszarów roboczych usługi Log Analytics skojarzonych z wystąpieniami usługi Azure Cache for Redis zgodnie z przepisami dotyczącymi zgodności w organizacji.

Należy pamiętać, że rejestrowanie inspekcji na płaszczyźnie danych nie jest jeszcze dostępne dla Azure Cache for Redis.

- [Jak ustawić parametry przechowywania dzienników](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="26-monitor-and-review-logs"></a>2.6. Monitorowanie i przeglądanie dzienników

**Wskazówki:** włączanie ustawień diagnostycznych dziennika aktywności platformy Azure i wysyłanie dzienników do obszaru roboczego usługi Log Analytics. Wykonywanie zapytań w uciekierach usługi Log Analytics w celu wyszukiwania terminów, identyfikowania trendów, analizowania wzorców i zapewnienia wielu innych szczegółowych informacji na podstawie danych dziennika aktywności, które mogą zostać zebrane dla Azure Cache for Redis.

Należy pamiętać, że rejestrowanie inspekcji na płaszczyźnie danych nie jest jeszcze dostępne dla Azure Cache for Redis.

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md)

- [Jak zbierać i analizować dzienniki aktywności platformy Azure w obszarze roboczym usługi Log Analytics w Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7. Włączanie alertów dla niesłychanych działań

**Wskazówki:** można skonfigurować do odbierania alertów na podstawie metryk i dzienników aktywności związanych z Azure Cache for Redis wystąpieniami. Azure Monitor umożliwia skonfigurowanie alertu w celu wysyłania powiadomienia e-mail, wywoływania element webhook lub wywoływania aplikacji logiki platformy Azure.

Mimo że metryki są dostępne po włączeniu ustawień diagnostycznych, rejestrowanie inspekcji na płaszczyźnie danych nie jest jeszcze dostępne dla Azure Cache for Redis.

- [Jak skonfigurować alerty dla Azure Cache for Redis](./cache-how-to-monitor.md#alerts)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz Test porównawczy zabezpieczeń platformy [Azure: tożsamość i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Utrzymywanie spisu kont administracyjnych

**Wskazówki:** Azure Active Directory (Azure AD) ma wbudowane role, które muszą być jawnie przypisane i które mogą być odpytywalne. Moduł Azure AD PowerShell umożliwia wykonywanie zapytań ad hoc w celu odnajdywania kont, które są członkami grup administracyjnych.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Zmień domyślne hasła tam, gdzie ma to zastosowanie

**Wskazówki:** Dostęp do płaszczyzny sterowania Azure Cache for Redis jest kontrolowany za pośrednictwem Azure Active Directory (Azure AD). Usługa Azure AD nie ma koncepcji haseł domyślnych.

Dostęp do płaszczyzny danych Azure Cache for Redis jest kontrolowany za pomocą kluczy dostępu. Te klucze są używane przez klientów łączących się z pamięcią podręczną i mogą być ponownie wygenerowane w dowolnym momencie.

Nie zaleca się kompilowania domyślnych haseł w aplikacji. Zamiast tego możesz przechowywać hasła w usłudze Azure Key Vault a następnie pobrać je za pomocą usługi Azure AD.

- [Jak ponownie wygenerować Azure Cache for Redis dostępu](./cache-configure.md#settings)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Użyj dedykowanych kont administracyjnych

**Wskazówki:** Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center zarządzania tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

Ponadto, aby ułatwić śledzenie dedykowanych kont administracyjnych, możesz skorzystać z zaleceń Azure Security Center lub wbudowanych zasad platformy Azure, takich jak:

- Do subskrypcji powinien być przypisany więcej niż jeden właściciel

- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Jak używać usługi Azure Security Center do monitorowania tożsamości i dostępu (wersja zapoznawcza)](../security-center/security-center-identity-access.md)

- [Jak używać Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Używanie Azure Active Directory jednokrotnego (SSO)

**Wskazówki:** Azure Cache for Redis używa kluczy dostępu do uwierzytelniania użytkowników i nie obsługuje logowania jednokrotnego na poziomie płaszczyzny danych. Dostęp do płaszczyzny sterowania dla aplikacji Azure Cache for Redis za pośrednictwem interfejsu API REST i obsługuje logowanie jednokrotne. Aby się uwierzytelnić, ustaw nagłówek Autoryzacja dla żądań na wartość JSON Web Token uzyskaną z usługi Azure Active Directory (Azure AD).

- [Opis Azure Cache for Redis API REST](/rest/api/redis/)

- [Opis logowania jednokrotnego w usłudze Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Używaj uwierzytelniania wieloskładnikowego dla Azure Active Directory dostępu opartego na danych

**Wskazówki:** włączanie Azure Active Directory wieloskładnikowego (Azure AD) i postępuj zgodnie z Azure Security Center tożsamości i zarządzania dostępem.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6. Użyj dedykowanych maszyn (stacji roboczych z dostępem uprzywilejowanym) do wykonywania wszystkich zadań administracyjnych

**Wskazówki:** Używaj stacji roboczych z dostępem uprzywilejowanym (PAW) z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania zasobów platformy Azure. 

- [Dowiedz się więcej o stacjach roboczych z dostępem uprzywilejowanym](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Rejestrowanie i alerty dotyczące podejrzanych działań z kont administracyjnych

**Wskazówki:** Generowanie dzienników i alertów przy użyciu usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) w przypadku wystąpienia podejrzanej lub niebezpiecznej aktywności w środowisku.

Ponadto wykrywanie ryzyka w usłudze Azure AD umożliwia wyświetlanie alertów i raportów dotyczących ryzykownych zachowań użytkowników.

- [Jak wdrożyć Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Opis wykrywania ryzyka w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** Skonfiguruj nazwane lokalizacje w dostępie warunkowym usługi Azure Active Directory (Azure AD), aby zezwolić na dostęp tylko z określonych grup logicznych zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="39-use-azure-active-directory"></a>3.9. Użyj Azure Active Directory

**Wskazówki:** Używanie Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych magazynowych i podczas przesyłania. Usługa Azure AD dodaje również iniekty, skróty i bezpiecznie przechowuje poświadczenia użytkownika.

Uwierzytelniania usługi Azure AD nie można używać do bezpośredniego dostępu do płaszczyzny danych usługi Azure Cache for Redis, jednak poświadczeń usługi Azure AD można używać do administrowania na poziomie płaszczyzny sterowania (tj. Azure Portal) do kontrolowania kluczy dostępu Azure Cache for Redis.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** Azure Active Directory (Azure AD) zawiera dzienniki, które ułatwiają odnajdywanie nieaktywnego konta. Ponadto używaj przeglądów dostępu do tożsamości platformy Azure, aby efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisań ról. Dostęp użytkowników można regularnie przeglądać, aby upewnić się, że tylko odpowiedni użytkownicy mają dalszy dostęp.

- [Informacje o raportowaniu w usłudze Azure AD](../active-directory/reports-monitoring/index.yml)

- [Jak używać przeglądów dostępu tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych poświadczeń

**Wskazówki:** masz dostęp do źródeł dzienników Azure Active Directory (Azure AD), inspekcji i ryzyka, które umożliwiają integrację z rozwiązaniami Azure Sentinel lub SIEM innej firmy.

Ten proces można usprawnić, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD oraz wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego usługi Log Analytics. Żądane alerty dzienników można skonfigurować w ramach usługi Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Jak Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Odchylenie zachowania alertu podczas logowania na koncie

**Wskazówki:** W przypadku odchylenia zachowania logowania do konta na płaszczyźnie sterowania użyj usługi Azure Active Directory (Azure AD) Identity Protection i funkcji wykrywania ryzyka, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane akcje związane z tożsamościami użytkowników. Możesz również pozysać dane do Azure Sentinel celu dalszego badania.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady ryzyka usługi Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Utrzymywanie spisu informacji poufnych

**Wskazówki:** używanie tagów do śledzenia zasobów platformy Azure, które przechowują lub przetwarzają poufne informacje.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Odizoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki:** Zaim implementuj oddzielne subskrypcje i/lub grupy zarządzania na rzecz testowania, testowania i tworzenia aplikacji. Azure Cache for Redis powinny być oddzielone siecią wirtualną/podsiecią i odpowiednio otagowane. Opcjonalnie możesz użyć zapory Azure Cache for Redis, aby zdefiniować reguły tak, aby tylko połączenia klientów z określonych zakresów adresów IP mogą łączyć się z pamięcią podręczną.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak wdrożyć Azure Cache for Redis w sieci wirtualnej](cache-how-to-premium-vnet.md)

- [Jak skonfigurować reguły Azure Cache for Redis zapory](./cache-configure.md#firewall)

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego transferu poufnych informacji

**Wskazówki:** jeszcze niedostępne; Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla Azure Cache for Redis.

Firma Microsoft zarządza podstawową infrastrukturą Azure Cache for Redis i wdrożyła ścisłe mechanizmy kontroli, aby zapobiec utracie lub narażeniu danych klientów.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki:** Azure Cache for Redis wymaga domyślnie komunikacji zaszyfrowanej za pomocą szyfrowania TLS. Obecnie są obsługiwane wersje TLS 1.0, 1.1 i 1.2. Jednak w przypadku użycia WLS 1.0 i 1.1 istnieje ścieżka do cofania korzystania z tej funkcji w całej branży, dlatego należy w ogóle używać standardu TLS 1.2. Jeśli biblioteka klienta lub narzędzie nie obsługuje szyfrowania TLS, włączanie nieszyfrowanych połączeń można wykonać za pośrednictwem interfejsów API Azure Portal lub zarządzania. W takich przypadkach, gdy szyfrowane połączenia nie są możliwe, zaleca się umieszczenie pamięci podręcznej i aplikacji klienckiej w sieci wirtualnej.

- [Opis szyfrowania podczas przesyłania Azure Cache for Redis](cache-best-practices.md)

- [Opis wymaganych portów używanych w scenariuszach pamięci podręcznej sieci wirtualnej](./cache-how-to-premium-vnet.md#outbound-port-requirements)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Cache:**

[!INCLUDE [Resource Policy for Microsoft.Cache 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.cache-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5. Identyfikowanie poufnych danych za pomocą aktywnego narzędzia odnajdywania

**Wskazówki:** Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla Azure Cache for Redis. Oznaczanie wystąpień zawierających poufne informacje jako takie i implementowanie rozwiązań innych firm, jeśli jest to wymagane w celu zapewnienia zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft firma Microsoft traktuje całą zawartość klientów jako poufne i bardzo długo chroni przed utratą i utratą danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych funkcji i kontroli ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Kontrola dostępu do zasobów przy użyciu kontroli dostępu opartej na rolach

**Wskazówki:** Kontrola dostępu oparta na rolach (RBAC) na platformie Azure umożliwia kontrolowanie dostępu do Azure Cache for Redis kontroli dostępu (tj. Azure Portal).

- [Jak skonfigurować RBAC platformy Azure](../role-based-access-control/role-assignments-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8. Szyfrowanie poufnych informacji w spoczynku

**Wskazówki:** Azure Cache for Redis dane klientów w pamięci i mimo że są one silnie chronione przez wiele kontrolek implementowanych przez firmę Microsoft, pamięć nie jest domyślnie szyfrowana. Jeśli jest to wymagane przez organizację, zaszyfruj zawartość przed zapisaniem jej w Azure Cache for Redis.

Jeśli używasz funkcji Azure Cache for Redis "Trwałość danych Redis", dane są wysyłane na konto usługi Azure Storage, które należy do Ciebie i zarządzasz nimi. Trwałość można skonfigurować w bloku "Nowe Azure Cache for Redis" podczas tworzenia pamięci podręcznej i w menu Zasób dla istniejących pamięci podręcznych w premium.

Dane w usłudze Azure Storage są szyfrowane i odszyfrowywać w sposób przezroczysty przy użyciu 256-bitowego szyfrowania AES, jednego z najsilniejszych dostępnych szyfrów blokowych, i są zgodne ze standardem FIPS 140-2. Nie można wyłączyć szyfrowania usługi Azure Storage. W celu szyfrowania konta magazynu możesz polegać na kluczach zarządzanych przez firmę Microsoft lub zarządzać szyfrowaniem przy użyciu własnych kluczy.

- [Jak skonfigurować trwałość w Azure Cache for Redis](cache-how-to-premium-persistence.md)

- [Opis szyfrowania kont usługi Azure Storage](../storage/common/storage-service-encryption.md)

- [Informacje o ochronie danych klientów platformy Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Rejestrowanie i alerty dotyczące zmian w krytycznych zasobach platformy Azure

**Wskazówki:** użyj Azure Monitor dziennika aktywności platformy Azure, aby tworzyć alerty dotyczące sytuacji, w których zmiany zostaną wprowadzone w wystąpieniach produkcyjnych Azure Cache for Redis i innych krytycznych lub powiązanych zasobów.

- [Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: zarządzanie lukami w zabezpieczeniach.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1. Uruchamianie zautomatyzowanych narzędzi do skanowania luk w zabezpieczeniach

**Wskazówki:** Postępuj zgodnie z zaleceniami Azure Security Center na temat zabezpieczania Azure Cache for Redis i powiązanych zasobów.

Firma Microsoft wykonuje zarządzanie lukami w zabezpieczeniach w systemach bazowych, które obsługują Azure Cache for Redis.

- [Opis Azure Security Center rekomendacji](../security-center/recommendations-reference.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: spis i zarządzanie zasobami.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1. Korzystanie z rozwiązania do automatycznego odnajdywania zasobów

**Wskazówki:** użyj Azure Resource Graph do wykonywania zapytań i odnajdywania wszystkich zasobów (takich jak zasoby obliczeniowe, magazynowe, sieciowe, porty i protokoły itp.) w ramach subskrypcji. Upewnij się, że w dzierżawie są odpowiednie uprawnienia (odczyt) i wyliczenie wszystkich subskrypcji platformy Azure, a także zasobów w ramach twoich subskrypcji.

Mimo że klasyczne zasoby platformy Azure można Resource Graph za pośrednictwem usługi , zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów w przyszłości.

- [Jak tworzyć zapytania za pomocą Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Opis kontroli RBAC na platformie Azure](../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="62-maintain-asset-metadata"></a>6.2. Obsługa metadanych zasobów

**Wskazówki:** Stosowanie tagów do zasobów platformy Azure, które dają metadane w celu logicznego zorganizowania ich w taksonomię.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** Używaj tagowania, grup zarządzania i oddzielnych subskrypcji, jeśli jest to konieczne, do organizowania i śledzenia Azure Cache for Redis i powiązanych zasobów. Regularnie uzgadniaj spis i upewnij się, że nieautoryzowane zasoby są usuwane z subskrypcji w terminowy sposób.

Ponadto użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak tworzyć grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi zasobów i używać ich](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorowanie niezatwierdzonych zasobów platformy Azure

**Wskazówki:** Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Ponadto użyj Azure Resource Graph do wykonywania zapytań o zasoby i odnajdywania ich w ramach subskrypcji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="69-use-only-approved-azure-services"></a>6.9. Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki:** użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Ograniczanie możliwości interakcji użytkowników z Azure Resource Manager

**Wskazówki:** Skonfiguruj dostęp warunkowy platformy Azure, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resource Manager, konfigurując opcję "Blokuj dostęp" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: bezpieczna konfiguracja.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla wystąpień Azure Cache for Redis za pomocą Azure Policy. Użyj Azure Policy w przestrzeni nazw "Microsoft.Cache", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji Azure Cache for Redis wystąpień. Możesz również korzystać z wbudowanych definicji zasad związanych z wystąpieniami Azure Cache for Redis, takich jak:

- Należy włączyć tylko bezpieczne Redis Cache połączenia z siecią

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Jak wyświetlić dostępne Azure Policy aliasy](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** użyj Azure Policy [deny] i [deploy if not exist], aby wymusić bezpieczne ustawienia w zasobach platformy Azure.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Opis Azure Policy danych](../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** Jeśli używasz niestandardowych Azure Policy lub szablonów Azure Resource Manager dla wystąpień usługi Azure Cache for Redis i powiązanych zasobów, użyj usługi Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

- [Jak przechowywać kod w Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Azure Repos dokumentacji](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki:** użyj Azure Policy aliasów w przestrzeni nazw "Microsoft.Cache", aby utworzyć zasady niestandardowe służące do ostrzegania, inspekcji i wymuszania konfiguracji systemu. Ponadto opracuj proces i potok do zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementowanie zautomatyzowanego monitorowania konfiguracji dla zasobów platformy Azure

**Wskazówki:** użyj Azure Policy aliasów w przestrzeni nazw "Microsoft.Cache", aby utworzyć zasady niestandardowe służące do ostrzegania, inspekcji i wymuszania konfiguracji systemu. Użyj Azure Policy [audit], [deny] i [deploy if not exist], aby automatycznie wymuszać konfiguracje dla wystąpień Azure Cache for Redis i powiązanych zasobów.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** W przypadku maszyn wirtualnych platformy Azure lub aplikacji internetowych działających w usłudze Azure App Service używanych do uzyskiwania dostępu do wystąpień usługi Azure Cache for Redis użyj tożsamości usługi zarządzanej w połączeniu z usługą Azure Key Vault, aby uprościć i zabezpieczyć Azure Cache for Redis tajnymi kluczami. Upewnij Key Vault, że włączono usuwanie nie softowe.

- [Jak zintegrować usługę z tożsamościami zarządzanymi platformy Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Jak utworzyć Key Vault](../key-vault/general/quick-create-portal.md)

- [Jak uwierzytelnić się w Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** W przypadku maszyn wirtualnych platformy Azure lub aplikacji internetowych działających w usłudze Azure App Service używanych do uzyskiwania dostępu do wystąpień usługi Azure Cache for Redis użyj tożsamości usługi zarządzanej w połączeniu z usługą Azure Key Vault, aby uprościć i zabezpieczyć Azure Cache for Redis tajnymi kluczami. Upewnij się Key Vault że włączono usuwanie nie softowe.

Tożsamości zarządzane zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory (Azure AD). Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w Azure Key Vault, bez żadnych poświadczeń w kodzie.

- [Jak skonfigurować tożsamości zarządzane](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Jak zintegrować usługę z tożsamościami zarządzanymi platformy Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Wyeliminuj niezamierzone narażenie poświadczeń

**Wskazówki:** implementowanie skanera poświadczeń w celu identyfikowania poświadczeń w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Malware Defense.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Wstępne skanowanie plików do przesłania do zasobów platformy Azure bez obliczeń

**Wskazówki:** Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na bazowym hoście, który obsługuje usługi platformy Azure (na przykład Azure Cache for Redis), ale nie działa w przypadku zawartości klienta.

Wstępnie przeskanuj zawartość przesyłaną do zasobów platformy Azure, które nie są obliczeniowe, takich jak App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL itp. Firma Microsoft nie może uzyskać dostępu do Twoich danych w tych wystąpieniach.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: odzyskiwanie danych.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnianie regularnych automatycznych kopii zapasowej

**Wskazówki:** Włączanie trwałości redis. Trwałość redis umożliwia utrwalanie danych przechowywanych w pamięci redis. Można również zrobić migawki i utworzyć kopię zapasową danych, które można załadować w przypadku awarii sprzętu. Jest to ogromna przewaga nad warstwą Podstawowa lub Standardowa, w której wszystkie dane są przechowywane w pamięci, a w przypadku awarii węzły pamięci podręcznej mogą potencjalnie utracić dane.

Możesz również użyć Azure Cache for Redis Eksportuj. Eksport umożliwia eksportowanie danych przechowywanych w programie Azure Cache for Redis do plików RDB zgodnych z usługą Redis. Ta funkcja umożliwia przenoszenie danych z jednego Azure Cache for Redis do innego lub na inny serwer Redis. Podczas eksportowania na maszynie wirtualnej, która hostuje wystąpienie serwera Azure Cache for Redis, jest tworzony plik tymczasowy, a plik jest przekazywany do wskazanego konta magazynu. Po zakończeniu operacji eksportowania ze stanem powodzenia lub niepowodzenia plik tymczasowy zostanie usunięty.

- [Jak włączyć trwałość redis](cache-how-to-premium-persistence.md)

- [Jak używać eksportowania Azure Cache for Redis danych](cache-how-to-import-export-data.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2. Wykonywanie pełnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki:** Włączanie trwałości redis. Trwałość redis umożliwia utrwalanie danych przechowywanych w pamięci redis. Można również utworzyć migawki i utworzyć kopię zapasową danych, które można załadować w przypadku awarii sprzętu. Jest to ogromna przewaga nad warstwą Podstawowa lub Standardowa, w której wszystkie dane są przechowywane w pamięci, a w przypadku awarii węzłów pamięci podręcznej może do tego dobieć potencjalna utrata danych.

Możesz również użyć funkcji Azure Cache for Redis Eksportuj. Eksport umożliwia eksportowanie danych przechowywanych w programie Azure Cache for Redis do plików RDB zgodnych z usługą Redis. Za pomocą tej funkcji można przenosić dane z jednego Azure Cache for Redis do innego lub na inny serwer Redis. Podczas procesu eksportowania na maszynie wirtualnej, która hostuje wystąpienie serwera Azure Cache for Redis, jest tworzony plik tymczasowy, a plik jest przekazywany na wyznaczone konto magazynu. Po zakończeniu operacji eksportowania ze stanem powodzenia lub niepowodzenia plik tymczasowy jest usuwany.

Jeśli używasz Azure Key Vault do przechowywania poświadczeń dla wystąpień Azure Cache for Redis, upewnij się, że regularne automatyczne kopie zapasowe kluczy.

- [Jak włączyć trwałość redis](cache-how-to-premium-persistence.md)

- [Jak używać eksportowania Azure Cache for Redis danych](cache-how-to-import-export-data.md)

- [Jak utworzyć kopię zapasową Key Vault kluczy](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Zweryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki:** Użyj Azure Cache for Redis importu. Importowanie może służyć do importowania plików RDB zgodnych z usługą Redis z dowolnego serwera Redis działającego w dowolnej chmurze lub środowisku, w tym usługi Redis działającej w systemie Linux, Windows lub dowolnym dostawcy usług w chmurze, takim Amazon Web Services i innych. Importowanie danych to prosty sposób tworzenia pamięci podręcznej ze wstępnie wypełnionymi danymi. Podczas procesu importowania program Azure Cache for Redis pliki RDB z usługi Azure Storage do pamięci, a następnie wstawia klucze do pamięci podręcznej.

Okresowe testowanie przywracania danych Azure Key Vault tajnych.

- [Jak używać importowania Azure Cache for Redis importu](cache-how-to-import-export-data.md)

- [Jak przywrócić Key Vault tajne](/powershell/module/az.keyvault/restore-azkeyvaultsecret?preserve-view=true&view=azps-4.8.0)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Tworzenie przewodnika po reagowaniu na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu.

- [Jak skonfigurować automatyzacje przepływu pracy w Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Centrum zabezpieczeń firmy Microsoft Anatomy of an Incident (Anatomia zdarzenia)](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Klient może również skorzystać z przewodnika obsługi zdarzeń zabezpieczeń komputera NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Tworzenie procedury oceniania i ustalania priorytetów zdarzeń

**Wskazówki:** Security Center przypisuje ważność do każdego alertu, aby ułatwić określanie priorytetów, które alerty powinny być badane w pierwszej kolejności. Ważność jest oparta na tym, jak pewna Security Center znajduje się w znajdowaniu lub analizie użytej do wystawienia alertu, a także na poziomie pewności, że za działaniem, które doprowadziło do tego alertu, znajduje się złośliwy zamiar.

Ponadto wyraźnie oznaczaj subskrypcje (na przykład production, non-prod) i utwórz system nazewnictwa, aby jasno identyfikować i kategoryzować zasoby platformy Azure.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki:** Przećwiczyj ćwiczenia, aby regularnie testować możliwości reagowania na zdarzenia w systemach. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Zapoznaj się z publikacją NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Przewodnik po programach testowych, szkoleniowych i szkoleniowych dla planów i możliwości IT)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Podaj szczegóły kontaktowe zdarzenia zabezpieczeń i skonfiguruj powiadomienia o alertach dla zdarzeń zabezpieczeń

**Wskazówki:** informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do kontaktowania się z Tobą, jeśli użytkownik usługi Centrum zabezpieczeń firmy Microsoft (MSRC) odkryje, że dostęp do danych klienta został uzyskany przez nieupoważnioną lub niezgodną z prawem stronę.  Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt Azure Security Center zabezpieczeń](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Dołączanie alertów zabezpieczeń do systemu reagowania na zdarzenia

**Wskazówki:** eksportowanie alertów Azure Security Center i zaleceń przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia ręczne lub ciągłe eksportowanie alertów i zaleceń. Możesz użyć łącznika danych Azure Security Center, aby przesyłać strumieniowo alerty do Azure Sentinel.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki:** Użyj funkcji automatyzacji przepływu pracy w Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem Logic Apps alertów zabezpieczeń i zaleceń.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [test porównawczy zabezpieczeń platformy Azure: testy penetracyjne i ćwiczenia zespołu red team.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Przeprowadzaj regularne testy penetrowania zasobów platformy Azure i zapewnij korygowanie wszystkich krytycznych ustaleń zabezpieczeń

**Wskazówki:** postępuj zgodnie z regułami testowania penetracyjne firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszały zasad firmy Microsoft. Korzystaj ze strategii firmy Microsoft i wykonywania testów typu „red team” i testów na żywo w zarządzanej przez firmę Microsoft infrastrukturze, usługach i aplikacjach w chmurze. 

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)
