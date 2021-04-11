---
title: Podstawa zabezpieczeń platformy Azure dla usługi Azure cache for Redis
description: Podstawowa pamięć podręczna systemu Azure dla usługi Redis Security zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: cache
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f07eece54bfe456e173e664b19777cfc98b71368
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566867"
---
# <a name="azure-security-baseline-for-azure-cache-for-redis"></a>Podstawa zabezpieczeń platformy Azure dla usługi Azure cache for Redis

Ta linia bazowa zabezpieczeń stosuje wskazówki z [wersji 1,0 usługi Azure Security test](../security/benchmarks/overview-v1.md) do usługi Azure cache for Redis. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez usługę Azure Security test i powiązane wskazówki dotyczące usługi Azure cache for Redis. **Formanty** nie mają zastosowania do pamięci podręcznej platformy Azure dla Redis zostały wykluczone.

 
Aby dowiedzieć się, jak usługa Azure cache for Redis jest całkowicie mapowana na test porównawczy zabezpieczeń platformy Azure, zapoznaj się z [pełnym plikiem mapowania zabezpieczeń usługi Azure cache for Redis](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: Wdróż wystąpienie usługi Azure cache for Redis w sieci wirtualnej (VNET). Sieć wirtualna jest siecią prywatną w chmurze. Gdy usługa Azure cache for Redis jest skonfigurowana przy użyciu sieci wirtualnej, nie jest ona publicznie adresowana i można uzyskać do niej dostęp tylko z maszyn wirtualnych i aplikacji w sieci wirtualnej.

Możesz również określić reguły zapory z zakresem adresów IP początkowy i końcowy. Po skonfigurowaniu reguł zapory tylko połączenia klienckie z określonych zakresów adresów IP mogą łączyć się z pamięcią podręczną.

- [Jak skonfigurować obsługę Virtual Network w przypadku pamięci podręcznej Premium platformy Azure dla Redis](cache-how-to-premium-vnet.md)

- [Jak skonfigurować usługę Azure cache for Redis reguły zapory](./cache-configure.md#firewall)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki**: gdy Virtual Machines są wdrożone w tej samej sieci wirtualnej co wystąpienie usługi Azure cache for Redis, można użyć sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń), aby zmniejszyć ryzyko eksfiltracji danych. Włącz dzienniki przepływu sieciowej grupy zabezpieczeń i Wyślij dzienniki do konta usługi Azure Storage na potrzeby inspekcji ruchu. Możesz również wysłać dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego Log Analytics i użyć Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi niezłośliwymi adresami IP

**Wskazówki**: wdrażanie za pomocą usługi Azure Virtual Network (VNET) zapewnia ulepszone zabezpieczenia i izolację pamięci podręcznej platformy Azure dla usługi Redis, a także podsieci, zasady kontroli dostępu i inne funkcje w celu dodatkowego ograniczenia dostępu. W przypadku wdrożenia w sieci wirtualnej usługa Azure cache for Redis nie jest publicznie adresowana i można do niej uzyskać dostęp tylko z maszyn wirtualnych i aplikacji w sieci wirtualnej.

Włącz DDoS Protection Standard na sieci wirtualnych skojarzonym z pamięcią podręczną platformy Azure dla wystąpień Redis, aby chronić przed atakami typu "odmowa usługi" (DDoS). Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi lub nieużywanymi adresami IP.

- [Jak skonfigurować obsługę Virtual Network w przypadku pamięci podręcznej Premium platformy Azure dla Redis](cache-how-to-premium-vnet.md)

- [Zarządzanie standardem Azure DDoS Protection przy użyciu Azure Portal](../ddos-protection/manage-ddos-protection.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: gdy maszyny wirtualne są wdrażane w tej samej sieci wirtualnej co wystąpienie usługi Azure cache for Redis, można użyć sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) w celu zmniejszenia ryzyka eksfiltracji danych. Włącz dzienniki przepływu sieciowej grupy zabezpieczeń i Wyślij dzienniki do konta usługi Azure Storage na potrzeby inspekcji ruchu. Możesz również wysłać dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego Log Analytics i użyć Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: Jeśli korzystasz z usługi Azure cache for Redis z aplikacjami sieci Web działającymi na Azure App Service lub wystąpieniami obliczeniowymi, wdróż wszystkie zasoby w usłudze Azure Virtual Network (VNET) i zabezpiecz je za pomocą zapory aplikacji sieci Web platformy Azure (WAF) w Application Gateway sieci Web. Skonfiguruj WAF do uruchamiania w "trybie zapobiegania". Tryb zapobiegania blokuje wtargnięcie i ataki wykrywane przez zasady. Osoba atakująca otrzymuje wyjątek "403 nieautoryzowany dostęp", a połączenie jest zamknięte. Tryb zapobiegania rejestruje takie ataki w dziennikach WAF.

Alternatywnie możesz wybrać ofertę z portalu Azure Marketplace, która obsługuje funkcje identyfikatorów/adresów IP z możliwościami inspekcji ładunku i/lub wykrywania anomalii.

- [Omówienie możliwości usługi Azure WAF](../web-application-firewall/ag/ag-overview.md)

- [Jak wdrożyć usługę Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: używanie tagów usługi sieci wirtualnej do definiowania kontroli dostępu do sieci w grupach zabezpieczeń sieci (sieciowej grupy zabezpieczeń) lub w zaporze platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

Możesz również użyć grup zabezpieczeń aplikacji (ASG), aby uprościć złożoną konfigurację zabezpieczeń. Grupy zabezpieczeń aplikacji umożliwiają skonfigurowanie zabezpieczeń sieci jako naturalnego rozszerzenia struktury aplikacji, co pozwala na grupowanie maszyn wirtualnych i definiowanie zasad zabezpieczeń sieci na podstawie tych grup.

- [Tagi usługi sieci wirtualnej](../virtual-network/service-tags-overview.md)

- [Grupy zabezpieczeń aplikacji](../virtual-network/network-security-groups-overview.md#application-security-groups)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych związanych z pamięcią podręczną platformy Azure dla wystąpień Redis z Azure Policy. Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft. cache" i "Microsoft. Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci pamięci podręcznej platformy Azure dla wystąpień Redis. Możesz również używać wbudowanych definicji zasad, takich jak:
- Należy włączyć tylko bezpieczne połączenia z Redis Cache

- Należy włączyć Standard DDoS Protection

Możesz również użyć planów platformy Azure, aby uprościć wdrożenia platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów środowiska, takich jak szablony Azure Resource Manager, kontrola dostępu oparta na rolach na platformie Azure (RBAC) i zasady, w ramach jednej definicji planu. Łatwo stosuj plan do nowych subskrypcji i środowisk, a następnie dostosuj kontrolę i zarządzanie przy użyciu wersji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak utworzyć Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Użyj tagów dla zasobów sieciowych skojarzonych z pamięcią podręczną platformy Azure w celu wdrożenia Redis, aby logicznie zorganizować je w taksonomię.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z pamięcią podręczną platformy Azure dla wystąpień Redis. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Jak utworzyć alerty w Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: Włączanie ustawień diagnostycznych dziennika aktywności platformy Azure i wysyłanie dzienników do obszaru roboczego log Analytics, usługi Azure Event Hub lub konta usługi Azure Storage w celu archiwizacji. Dzienniki aktywności zapewniają wgląd w operacje wykonane w pamięci podręcznej platformy Azure dla wystąpień Redis na poziomie płaszczyzny kontroli. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" dla każdej operacji zapisu (PUT, POST, DELETE) wykonanej na poziomie płaszczyzny kontroli dla pamięci podręcznej platformy Azure dla wystąpień Redis.

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Włączanie ustawień diagnostycznych dziennika aktywności platformy Azure i wysyłanie dzienników do obszaru roboczego log Analytics, usługi Azure Event Hub lub konta usługi Azure Storage w celu archiwizacji. Dzienniki aktywności zapewniają wgląd w operacje wykonane w pamięci podręcznej platformy Azure dla wystąpień Redis na poziomie płaszczyzny kontroli. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" dla każdej operacji zapisu (PUT, POST, DELETE) wykonanej na poziomie płaszczyzny kontroli dla pamięci podręcznej platformy Azure dla wystąpień Redis.

Chociaż metryki są dostępne przez włączenie ustawień diagnostycznych, rejestrowanie inspekcji w płaszczyźnie danych nie jest jeszcze dostępne dla usługi Azure cache for Redis.

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: w Azure monitor ustawić okres przechowywania dziennika dla log Analytics obszarów roboczych skojarzonych z pamięcią podręczną platformy Azure dla wystąpień Redis zgodnie z przepisami obowiązującymi w organizacji.

Należy zauważyć, że rejestrowanie inspekcji w płaszczyźnie danych nie jest jeszcze dostępne dla usługi Azure cache for Redis.

- [Jak ustawić parametry przechowywania dziennika](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: Włączanie ustawień diagnostycznych dziennika aktywności platformy Azure i wysyłanie dzienników do obszaru roboczego log Analytics. Wykonuj zapytania w Log Analytics, aby wyszukiwać terminy, identyfikować trendy, analizować wzorce i udostępniać wiele innych szczegółowych informacji na podstawie danych dziennika aktywności, które mogły zostać zebrane dla usługi Azure cache for Redis.

Należy zauważyć, że rejestrowanie inspekcji w płaszczyźnie danych nie jest jeszcze dostępne dla usługi Azure cache for Redis.

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md)

- [Jak zbierać i analizować dzienniki aktywności platformy Azure w obszarze roboczym Log Analytics w Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: można skonfigurować w taki sposób, aby otrzymywać alerty na podstawie metryk i dzienników aktywności związanych z pamięcią podręczną platformy Azure dla wystąpień Redis. Azure Monitor umożliwia skonfigurowanie alertu w celu wysłania powiadomienia e-mail, wywołania elementu webhook lub wywoływać aplikację logiki platformy Azure.

Chociaż metryki są dostępne przez włączenie ustawień diagnostycznych, rejestrowanie inspekcji w płaszczyźnie danych nie jest jeszcze dostępne dla usługi Azure cache for Redis.

- [Jak skonfigurować alerty dla usługi Azure cache for Redis](./cache-how-to-monitor.md#alerts)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz informacje o [teście zabezpieczeń Azure: Identity i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: Azure Active Directory (Azure AD) ma wbudowane role, które muszą być jawnie przypisane i są queryable. Za pomocą modułu Azure AD PowerShell można wykonywać zapytania ad hoc w celu odnajdywania kont należących do grup administracyjnych.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: płaszczyzna kontroli dostęp do usługi Azure cache for Redis jest kontrolowany za pomocą Azure Active Directory (Azure AD). Usługa Azure AD nie ma koncepcji domyślnych haseł.

Dostęp za pomocą płaszczyzny danych do usługi Azure cache for Redis jest kontrolowany przez klucze dostępu. Te klucze są używane przez klientów nawiązujących połączenie z pamięcią podręczną i mogą być ponownie generowane w dowolnym momencie.

Nie zaleca się tworzenia domyślnych haseł do aplikacji. Zamiast tego można przechowywać hasła w Azure Key Vault a następnie pobierać je za pomocą usługi Azure AD.

- [Jak wygenerować ponownie pamięć podręczną platformy Azure dla kluczy dostępu Redis](./cache-configure.md#settings)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

Ponadto, aby ułatwić śledzenie dedykowanych kont administracyjnych, można użyć zaleceń z Azure Security Center lub wbudowanych zasad platformy Azure, takich jak:

- Do subskrypcji powinien być przypisany więcej niż jeden właściciel

- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Jak używać Azure Security Center do monitorowania tożsamości i dostępu (wersja zapoznawcza)](../security-center/security-center-identity-access.md)

- [Jak używać Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Użyj Azure Active Directory logowania jednokrotnego (SSO)

**Wskazówki**: usługa Azure cache for Redis korzysta z kluczy dostępu do uwierzytelniania użytkowników i nie obsługuje logowania jednokrotnego na poziomie płaszczyzny danych. Dostęp do płaszczyzny kontroli dla usługi Azure cache for Redis jest dostępny za pośrednictwem interfejsu API REST i obsługuje logowanie jednokrotne. Aby przeprowadzić uwierzytelnianie, należy ustawić nagłówek autoryzacji dla żądań na token sieci Web JSON uzyskany z Azure Active Directory (Azure AD).

- [Informacje o usłudze Azure cache for Redis — interfejs API REST](/rest/api/redis/)

- [Opis logowania jednokrotnego w usłudze Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego dla wszystkich Azure Active Directory dostępu opartego na usłudze

**Wskazówki**: Włączanie uwierzytelniania wieloskładnikowego Azure Active Directory (Azure AD) i przestrzeganie Azure Security Center zalecenia dotyczące zarządzania tożsamościami i dostępem.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj stacji roboczych dostępu uprzywilejowanego (dostępem uprzywilejowanym) z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania zasobów platformy Azure. 

- [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: Użyj Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku.

Ponadto za pomocą funkcji wykrywania ryzyka usługi Azure AD można wyświetlać alerty i raporty na temat ryzykownego zachowania użytkowników.

- [Jak wdrożyć Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Omówienie wykrywania ryzyka usługi Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Skonfiguruj nazwane lokalizacje w Azure Active Directory (Azure AD) dostęp warunkowy, aby zezwolić na dostęp tylko z określonych logicznych grup zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

Uwierzytelniania usługi Azure AD nie można używać do bezpośredniego dostępu do usługi Azure cache for Redis ", jednak poświadczenia usługi Azure AD mogą być używane do administrowania na poziomie płaszczyzny kontroli (tj. Azure Portal) do sterowania usługą Azure cache dla kluczy dostępu Redis.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Azure Active Directory (Azure AD) zawiera dzienniki ułatwiające odnajdywanie starych kont. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp.

- [Informacje o raportowaniu usługi Azure AD](../active-directory/reports-monitoring/index.yml)

- [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: masz dostęp do źródeł działań związanych z logowaniem do usługi Azure Active Directory (Azure AD), inspekcją i ryzykiem związanym z dziennikami zdarzeń, które umożliwiają integrację z platformą Azure, lub SIEMą innych firm.

Proces ten można usprawnić, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics. Żądane alerty dzienników można skonfigurować w ramach Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Jak przejść do tablicy wskaźnikowej platformy Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: odchylenia zachowania alertu dotyczącego logowania na koncie

**Wskazówki**: w przypadku odróżnienia zachowania logowania do konta na płaszczyźnie kontroli użyj funkcji Azure Active Directory (Azure AD) Identity Protection i wykrywania ryzyka, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. Możesz również pozyskać dane do usługi Azure wskaźnikowej na potrzeby dalszej analizy.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie oddzielnych subskrypcji i/lub grup zarządzania na potrzeby tworzenia, testowania i produkcji. Pamięć podręczna Azure dla wystąpień Redis powinna być oddzielona przez sieć wirtualną/podsieć i odpowiednio oznakowane. Opcjonalnie użyj usługi Azure cache for Redis, aby zdefiniować reguły, tak aby tylko połączenia klienckie z określonych zakresów adresów IP mogły łączyć się z pamięcią podręczną.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak wdrożyć usługę Azure cache for Redis w sieci wirtualnej](cache-how-to-premium-vnet.md)

- [Jak skonfigurować usługę Azure cache for Redis reguły zapory](./cache-configure.md#firewall)

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: nie są jeszcze dostępne; funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla usługi Azure cache for Redis.

Firma Microsoft zarządza podstawową infrastrukturą dla usługi Azure cache for Redis i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: usługa Azure cache for Redis wymaga domyślnie komunikacji szyfrowanej przy użyciu protokołu TLS. Protokoły TLS w wersji 1,0, 1,1 i 1,2 są obecnie obsługiwane. Jednak protokoły TLS 1,0 i 1,1 znajdują się na ścieżce, aby wycofać całe branże, więc Użyj protokołu TLS 1,2, jeśli jest to możliwe. Jeśli Biblioteka klienta lub narzędzie nie obsługuje protokołu TLS, można włączyć nieszyfrowane połączenia za pomocą Azure Portal lub interfejsów API zarządzania. W takich przypadkach, gdy połączenia szyfrowane nie są możliwe, będzie zalecane umieszczenie pamięci podręcznej i aplikacji klienckiej w sieci wirtualnej.

- [Informacje o szyfrowaniu podczas przesyłania dla usługi Azure cache for Redis](cache-best-practices.md)

- [Zrozumienie wymaganych portów używanych w scenariuszach pamięci podręcznej sieci wirtualnej](./cache-how-to-premium-vnet.md#outbound-port-requirements)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/azure/security-center/security-center-recommendations). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/azure/security-center/azure-defender) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. cache**:

[!INCLUDE [Resource Policy for Microsoft.Cache 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.cache-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla usługi Azure cache for Redis. Wystąpienia tagów zawierające poufne informacje jako takie i Implementuj rozwiązanie innych firm, jeśli jest to wymagane do celów zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: stosowanie kontroli dostępu opartej na rolach w celu kontrolowania dostępu do zasobów

**Wskazówki**: Użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby kontrolować dostęp do pamięci podręcznej platformy Azure dla płaszczyzny kontroli Redis (tj. Azure Portal).

- [Jak skonfigurować usługę Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: pamięć podręczna systemu Azure dla Redis przechowuje dane klientów w pamięci, a jednocześnie silnie chronione przez wiele formantów wdrożonych przez firmę Microsoft pamięć nie jest domyślnie szyfrowana. Jeśli jest to wymagane przez organizację, Zaszyfruj zawartość przed przechowywaniem w pamięci podręcznej platformy Azure dla Redis.

W przypadku korzystania z usługi Azure cache for Redis z funkcją "Redis danych trwałości" dane są wysyłane do konta usługi Azure Storage, które jest używane i zarządzane przez użytkownika. Można skonfigurować trwałość z poziomu bloku "New Azure cache for Redis" podczas tworzenia pamięci podręcznej oraz w menu zasobów dla istniejących pamięci podręcznych w warstwie Premium.

Dane w usłudze Azure Storage są szyfrowane i odszyfrowywane w sposób niewidoczny dla użytkownika przy użyciu 256-bitowego szyfrowania AES, jednego z najsilniejszych szyfrów blokowych i jest zgodny ze standardem FIPS 140-2. Nie można wyłączyć szyfrowania usługi Azure Storage. Możesz polegać na kluczach zarządzanych przez firmę Microsoft na potrzeby szyfrowania konta magazynu lub można zarządzać szyfrowaniem przy użyciu własnych kluczy.

- [Jak skonfigurować trwałość w pamięci podręcznej platformy Azure dla Redis](cache-how-to-premium-persistence.md)

- [Informacje o szyfrowaniu dla kont usługi Azure Storage](../storage/common/storage-service-encryption.md)

- [Omówienie ochrony danych klientów platformy Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty, kiedy zmiany są wprowadzane do wystąpień produkcyjnych usługi Azure cache for Redis i innych krytycznych lub powiązanych zasobów.

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: Postępuj zgodnie z zaleceniami Azure Security Center na Zabezpieczanie pamięci podręcznej systemu Azure dla wystąpień Redis i powiązanych zasobów.

Firma Microsoft przeprowadza zarządzanie lukami w systemach bazowych, które obsługują usługę Azure cache for Redis.

- [Informacje na temat Azure Security Center zaleceń](../security-center/recommendations-reference.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania wszystkich zasobów (takich jak obliczenia, magazyn, Sieć, porty i protokoły itp.) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i Wylicz wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

Mimo że klasyczne zasoby platformy Azure mogą zostać odnalezione za pośrednictwem grafu zasobów, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów do przodu.

- [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie to konieczne, do organizowania i śledzenia usługi Azure cache dla wystąpień Redis i powiązanych zasobów. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

Ponadto należy używać Azure Policy do umieszczania ograniczeń dotyczących typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Tworzenie grup zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak utworzyć i używać tagów zasobów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Ponadto za pomocą grafu zasobów platformy Azure można wysyłać zapytania o zasoby w ramach subskrypcji i odnajdywać je.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Skonfiguruj dostęp warunkowy platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla usługi Azure cache dla wystąpień Redis z Azure Policy. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. cache", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji pamięci podręcznej platformy Azure dla wystąpień Redis. Możesz również używać wbudowanych definicji zasad związanych z pamięcią podręczną platformy Azure dla wystąpień Redis, takich jak:

- Należy włączyć tylko bezpieczne połączenia z Redis Cache

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Jak wyświetlić dostępne aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji Azure Policy lub szablonów Azure Resource Manager dla pamięci podręcznej platformy Azure dla wystąpień Redis i powiązanych zasobów, użyj Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

- [Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Dokumentacja Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. cache", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. cache", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Użyj Azure Policy [Audit], [Odmów] i [Wdróż, jeśli nie istnieje], aby automatycznie wymusić konfiguracje dla swojej pamięci podręcznej platformy Azure dla wystąpień Redis i powiązanych zasobów.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: w przypadku maszyn wirtualnych platformy Azure lub aplikacji sieci Web działających na Azure App Service używanym do uzyskiwania dostępu do pamięci podręcznej platformy Azure dla wystąpień Redis należy używać tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć pamięć podręczną systemu Azure na potrzeby zarządzania tajnymi Redis. Upewnij się, że Key Vault usuwanie trwałe jest włączone.

- [Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Jak utworzyć Key Vault](../key-vault/general/quick-create-portal.md)

- [Jak przeprowadzić uwierzytelnianie w Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: w przypadku maszyn wirtualnych platformy Azure lub aplikacji sieci Web działających na Azure App Service używanym do uzyskiwania dostępu do pamięci podręcznej platformy Azure dla wystąpień Redis należy używać tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć pamięć podręczną systemu Azure na potrzeby zarządzania tajnymi Redis. Upewnij się, że Key Vault usuwanie trwałe jest włączone.

Użyj tożsamości zarządzanych, aby zapewnić usługom platformy Azure automatyczną tożsamość zarządzaną w usłudze Azure Active Directory (Azure AD). Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Azure Key Vault, bez żadnych poświadczeń w kodzie.

- [Jak skonfigurować tożsamości zarządzane](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład Azure cache for Redis), ale nie jest uruchamiane na treści klienta.

Przed przeskanowaniem zawartość przekazywana do zasobów platformy Azure, które nie są obliczeniowe, takich jak App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL itp. Firma Microsoft nie może uzyskać dostępu do danych w tych wystąpieniach.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zapewnianie regularnych zautomatyzowanych kopii zapasowych

**Wskazówki**: Włączanie trwałości Redis. Trwałość Redis umożliwia utrwalanie danych przechowywanych w Redis. Można również tworzyć migawki i tworzyć kopie zapasowe danych, które można ładować w przypadku awarii sprzętu. Jest to ogromna korzyść w porównaniu z warstwą podstawową lub standardową, w której wszystkie dane są przechowywane w pamięci i może istnieć potencjalna utrata danych w przypadku awarii, w której węzły pamięci podręcznej nie działają.

Możesz również użyć usługi Azure cache do eksportowania Redis. Eksport pozwala wyeksportować dane przechowywane w pamięci podręcznej platformy Azure dla Redis do Redis zgodnych plików RDB. Ta funkcja służy do przenoszenia danych z jednej pamięci podręcznej platformy Azure dla wystąpienia Redis do innego lub do innego serwera Redis. Podczas eksportowania plik tymczasowy jest tworzony na maszynie wirtualnej, która hostuje wystąpienie serwera usługi Azure cache for Redis, i plik zostanie przekazany do wskazanego konta magazynu. Po zakończeniu operacji eksportowania ze stanem powodzenie lub niepowodzenie plik tymczasowy zostanie usunięty.

- [Jak włączyć trwałość Redis](cache-how-to-premium-persistence.md)

- [Jak używać usługi Azure cache do eksportowania Redis](cache-how-to-import-export-data.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: Włączanie trwałości Redis. Trwałość Redis umożliwia utrwalanie danych przechowywanych w Redis. Można również tworzyć migawki i tworzyć kopie zapasowe danych, które można ładować w przypadku awarii sprzętu. Jest to ogromna korzyść w porównaniu z warstwą podstawową lub standardową, w której wszystkie dane są przechowywane w pamięci i może istnieć potencjalna utrata danych w przypadku awarii, w której węzły pamięci podręcznej nie działają.

Możesz również użyć usługi Azure cache do eksportowania Redis. Eksport pozwala wyeksportować dane przechowywane w pamięci podręcznej platformy Azure dla Redis do Redis zgodnych plików RDB. Ta funkcja służy do przenoszenia danych z jednej pamięci podręcznej platformy Azure dla wystąpienia Redis do innego lub do innego serwera Redis. Podczas eksportowania plik tymczasowy jest tworzony na maszynie wirtualnej, która hostuje wystąpienie serwera usługi Azure cache for Redis, i plik zostanie przekazany do wskazanego konta magazynu. Po zakończeniu operacji eksportowania ze stanem powodzenie lub niepowodzenie plik tymczasowy zostanie usunięty.

W przypadku używania Azure Key Vault do przechowywania poświadczeń dla wystąpień usługi Azure cache for Redis należy zapewnić regularne automatyczne tworzenie kopii zapasowych kluczy.

- [Jak włączyć trwałość Redis](cache-how-to-premium-persistence.md)

- [Jak używać usługi Azure cache do eksportowania Redis](cache-how-to-import-export-data.md)

- [Jak utworzyć kopię zapasową kluczy Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: Użyj usługi Azure cache do importowania Redis. Import może służyć do przenoszenia plików RDB zgodnych z Redis z dowolnego serwera Redis działającego w dowolnej chmurze lub środowisku, w tym Redis działających w systemie Linux, Windows lub dowolnym dostawcy chmury, takim jak Amazon Web Services i inne. Importowanie danych to prosty sposób tworzenia pamięci podręcznej z wstępnie wypełnionymi danymi. Podczas procesu importowania usługa Azure cache for Redis ładuje pliki RDB z usługi Azure Storage do pamięci, a następnie wstawia klucze do pamięci podręcznej.

Okresowe testowanie przywracania danych tajnych Azure Key Vault.

- [Jak używać usługi Azure cache do importowania Redis](cache-how-to-import-export-data.md)

- [Jak przywrócić Key Vault wpisy tajne](/powershell/module/az.keyvault/restore-azkeyvaultsecret?preserve-view=true&view=azps-4.8.0)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu.

- [Jak skonfigurować automatyzację przepływu pracy w programie Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Klient może również korzystać z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analizach używanych do wystawiania alertu, a także poziom pewności, że istniało złośliwe zamiar w odniesieniu do działania, które doprowadziło do alertu.

Dodatkowo jasno Oznacz subskrypcje (na przykład produkcyjny, nieprodukcyjny) i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Zapoznaj się z publikacją NIST: Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych klienta został uzyskany przez nielegalną lub nieautoryzowaną osobę.  Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w programie Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz test [porównawczy zabezpieczeń platformy Azure: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: Postępuj zgodnie z regułami testowania penetracji Microsoft Cloud, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj ze strategii firmy Microsoft i wykonywania testów typu „red team” i testów na żywo w zarządzanej przez firmę Microsoft infrastrukturze, usługach i aplikacjach w chmurze. 

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)
