---
title: Podstawa zabezpieczeń platformy Azure dla Cognitive Services
description: Linia bazowa zabezpieczeń Cognitive Services zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b243fa18b17fdd15f3c39545b7d81f5796bd8429
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699865"
---
# <a name="azure-security-baseline-for-cognitive-services"></a>Podstawa zabezpieczeń platformy Azure dla Cognitive Services

Ta linia bazowa zabezpieczeń stosuje wskazówki z programu [Azure Security test w wersji 1,0](../security/benchmarks/overview-v1.md) do Cognitive Services. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące Cognitive Services. **Kontrolki** nie mają zastosowania do Cognitive Services zostały wykluczone.

 
Aby dowiedzieć się, jak Cognitive Services całkowicie mapować do testu porównawczego zabezpieczeń platformy Azure, zobacz [pełny Cognitive Services pliku mapowania linii bazowej zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: Microsoft Azure Cognitive Services udostępnia model zabezpieczeń warstwowych. Ten model umożliwia zabezpieczanie kont usług Cognitive Services w określonym podzestawie sieci. W przypadku skonfigurowania reguł sieci tylko aplikacje żądające danych za pośrednictwem określonego zestawu sieci mogą uzyskiwać dostęp do konta. Można ograniczyć dostęp do zasobów za pomocą filtrowania żądań, zezwalając tylko na żądania pochodzące z określonych adresów IP, zakresów adresów IP lub z listy podsieci w sieciach wirtualnych platformy Azure.

Obsługa punktów końcowych sieci wirtualnych i usług dla Cognitive Services jest ograniczona do określonego zestawu regionów.

- [Jak skonfigurować usługi Azure Cognitive Services Virtual Networks](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal)

- [Omówienie sieci wirtualnych platformy Azure](../virtual-network/virtual-networks-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki**: gdy Virtual Machines są wdrożone w tej samej sieci wirtualnej co kontener Cognitive Services, można użyć sieciowych grup zabezpieczeń, aby zmniejszyć ryzyko związane z eksfiltracji danych. Włączenie sieciowych grup zabezpieczeń przepływ dzienników i wysyłanie dzienników do konta usługi Azure Storage na potrzeby inspekcji ruchu. Możesz również wysyłać dzienniki przepływu grup zabezpieczeń sieci do obszaru roboczego Log Analytics i używać Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: jeśli używasz Cognitive Services w kontenerze, możesz rozszerzyć wdrożenie kontenera za pomocą wbudowanego rozwiązania zapory aplikacji sieci Web, które filtruje złośliwy ruch i obsługuje kompleksowe szyfrowanie protokołu TLS, zachowując prywatny i bezpieczny punkt końcowy kontenera. 

Należy pamiętać, że kontenery Cognitive Services są wymagane do przesyłania informacji pomiarowych na potrzeby rozliczeń. Jedynym wyjątkiem jest kontenery w trybie offline, ponieważ są one zgodne z inną metodą rozliczania. Niezezwolenie na wyświetlanie listy różnych kanałów sieciowych, na których zależą Cognitive Services kontenery, uniemożliwi pracę kontenera. Host powinien zezwalać na listę portów 443 i następujących domen:

- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Należy również pamiętać, że należy wyłączyć głębokie inspekcje pakietów dla swojego rozwiązania zapory w bezpiecznych kanałach, które kontenery Cognitive Services tworzyć na serwerach firmy Microsoft. Niewykonanie tej czynności uniemożliwi poprawne działanie kontenera.

- [Informacje o zabezpieczeniach kontenera Cognitive Services platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi niezłośliwymi adresami IP

**Wskazówki**: gdy maszyny wirtualne są wdrażane w tej samej sieci wirtualnej co kontener Cognitive Services, zdefiniuj i Implementuj standardowe konfiguracje zabezpieczeń dla powiązanych zasobów sieciowych z Azure Policy. Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft. CognitiveServices" i "Microsoft. Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci w pamięci podręcznej platformy Azure dla wystąpień Redis. Możesz również używać wbudowanych definicji zasad, takich jak:

- Należy włączyć Standard DDoS Protection

Korzystając z planów platformy Azure, można uprościć wdrożenia platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów środowiska, takich jak szablony Azure Resource Manager, kontrola dostępu oparta na rolach (Azure RBAC) i zasady, w ramach jednej definicji planu. Łatwo stosuj plan do nowych subskrypcji i środowisk, a następnie dostosuj kontrolę i zarządzanie przy użyciu wersji.

Jeśli używasz Cognitive Services w kontenerze, możesz rozszerzyć wdrożenie kontenera za pomocą wbudowanego rozwiązania zapory aplikacji sieci Web, które filtruje złośliwy ruch i obsługuje kompleksowe szyfrowanie protokołu TLS, utrzymując i bezpieczny punkt końcowy kontenera. 

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak utworzyć Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

- [Informacje o zabezpieczeniach kontenera Cognitive Services platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: gdy maszyny wirtualne są wdrażane w tej samej sieci wirtualnej co kontener Cognitive Services, można użyć sieciowych grup zabezpieczeń, aby zmniejszyć ryzyko związane z eksfiltracji danych. Włączenie sieciowych grup zabezpieczeń przepływ dzienników i wysyłanie dzienników do konta usługi Azure Storage na potrzeby inspekcji ruchu. Możesz również wysyłać dzienniki przepływu grup zabezpieczeń sieci do obszaru roboczego Log Analytics i używać Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: w przypadku używania Cognitive Services w obrębie kontenera można rozszerzyć wdrożenie kontenera za pomocą wbudowanego rozwiązania zapory aplikacji sieci Web, które filtruje złośliwy ruch i obsługuje kompleksowe szyfrowanie protokołu TLS, zachowując prywatny i bezpieczny punkt końcowy kontenera.  Możesz wybrać ofertę z portalu Azure Marketplace, która obsługuje funkcje identyfikatorów/adresów IP, umożliwiając wyłączenie inspekcji ładunku.

Należy pamiętać, że kontenery Cognitive Services są wymagane do przesyłania informacji pomiarowych na potrzeby rozliczeń. Jedynym wyjątkiem są kontenery w trybie offline, ponieważ są one zgodne z inną metodą rozliczania. Niezezwolenie na wyświetlanie listy różnych kanałów sieciowych, na których zależą Cognitive Services kontenery, uniemożliwi pracę kontenera. Host powinien zezwalać na listę portów 443 i następujących domen:

- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Należy również pamiętać, że należy wyłączyć głębokie inspekcje pakietów dla swojego rozwiązania zapory w bezpiecznych kanałach, które kontenery Cognitive Services tworzyć na serwerach firmy Microsoft. Niewykonanie tej czynności uniemożliwi poprawne działanie kontenera.

- [Informacje o zabezpieczeniach kontenera Cognitive Services platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: w przypadku używania Cognitive Services w obrębie kontenera można rozszerzyć wdrożenie kontenera za pomocą wbudowanego rozwiązania zapory aplikacji sieci Web, które filtruje złośliwy ruch i obsługuje kompleksowe szyfrowanie protokołu TLS, zachowując prywatny i bezpieczny punkt końcowy kontenera. 

Należy pamiętać, że kontenery Cognitive Services są wymagane do przesyłania informacji pomiarowych na potrzeby rozliczeń. Jedynym wyjątkiem jest kontenery w trybie offline, ponieważ są one zgodne z inną metodą rozliczania. Niezezwolenie na wyświetlanie listy różnych kanałów sieciowych, na których zależą Cognitive Services kontenery, uniemożliwi pracę kontenera. Host powinien zezwalać na listę portów 443 i następujących domen:

- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Należy również pamiętać, że należy wyłączyć głębokie inspekcje pakietów dla swojego rozwiązania zapory w bezpiecznych kanałach, które kontenery Cognitive Services tworzyć na serwerach firmy Microsoft. Niewykonanie tej czynności uniemożliwi poprawne działanie kontenera.

- [Informacje o zabezpieczeniach kontenera Cognitive Services platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi sieci wirtualnej, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapory platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (na przykład ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

Aby uprościć konfigurację zabezpieczeń, można również użyć grup zabezpieczeń aplikacji. Grupy zabezpieczeń aplikacji umożliwiają skonfigurowanie zabezpieczeń sieci jako naturalnego rozszerzenia struktury aplikacji, co pozwala na grupowanie maszyn wirtualnych i definiowanie zasad zabezpieczeń sieci na podstawie tych grup.

- [Tagi usługi sieci wirtualnej](../virtual-network/service-tags-overview.md)

- [Grupy zabezpieczeń aplikacji](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview#application-security-groups)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych związanych z kontenerem Cognitive Services z Azure Policy. Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft. CognitiveServices" i "Microsoft. Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci w pamięci podręcznej platformy Azure dla wystąpień Redis.

Możesz również użyć planów platformy Azure, aby uprościć wdrożenia platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów środowiska, takich jak szablony Azure Resource Manager, kontrola dostępu oparta na rolach (RBAC) i zasady, w ramach jednej definicji planu. Łatwo stosuj plan do nowych subskrypcji i środowisk, a następnie dostosuj kontrolę i zarządzanie przy użyciu wersji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak utworzyć Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Użyj tagów dla zasobów sieciowych skojarzonych z kontenerem Cognitive Services, aby logicznie zorganizować je w taksonomię.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z kontenerem Cognitive Services. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Jak utworzyć alerty w Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: Włączanie ustawień diagnostycznych dziennika aktywności platformy Azure i wysyłanie dzienników do obszaru roboczego log Analytics, usługi Azure Event Hub lub konta usługi Azure Storage w celu archiwizacji. Dzienniki aktywności zapewniają wgląd w operacje, które zostały wykonane na Cognitive Services kontenerze na poziomie płaszczyzny kontroli. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" dla każdej operacji zapisu (PUT, POST, DELETE) wykonanej na poziomie płaszczyzny kontroli dla pamięci podręcznej platformy Azure dla wystąpień Redis.

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: na potrzeby rejestrowania inspekcji płaszczyzny kontroli, Włącz ustawienia diagnostyki dziennika aktywności platformy Azure i Wyślij dzienniki do obszaru roboczego log Analytics, centrum zdarzeń platformy Azure lub konta usługi Azure Storage w celu archiwizacji. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" dla operacji zapisu (PUT, POST, DELETE) wykonanych na poziomie płaszczyzny kontroli dla zasobów platformy Azure.

Ponadto Cognitive Services wysyła zdarzenia diagnostyczne, które mogą być zbierane i używane na potrzeby analiz, alertów i raportów. Ustawienia diagnostyczne dla kontenera Cognitive Services można skonfigurować za pośrednictwem Azure Portal. Można wysłać jedno lub więcej zdarzeń diagnostycznych do konta magazynu, centrum zdarzeń lub obszaru roboczego Log Analytics.

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Korzystanie z ustawień diagnostycznych w usłudze Azure Cognitive Services](diagnostic-logging.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: w obszarze Azure monitor Ustaw okres przechowywania log Analytics obszaru roboczego zgodnie z regulacjami zgodności w organizacji. Używaj kont usługi Azure Storage do przechowywania długoterminowego/archiwizowania.

- [Jak ustawić parametry przechowywania dzienników dla obszarów roboczych Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: Włączanie ustawień diagnostycznych dziennika aktywności platformy Azure i wysyłanie dzienników do obszaru roboczego log Analytics. Te dzienniki zapewniają rozbudowane, częste dane dotyczące operacji zasobu, które są używane do identyfikacji i debugowania problemu. Wykonuj zapytania w Log Analytics, aby wyszukiwać terminy, identyfikować trendy, analizować wzorce i udostępniać wiele innych szczegółowych informacji na podstawie danych dziennika aktywności, które mogły zostać zebrane dla Cognitive Services platformy Azure.

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log)

- [Jak zbierać i analizować dzienniki aktywności platformy Azure w obszarze roboczym Log Analytics w Azure Monitor](/azure/azure-monitor/platform/activity-log)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: można zgłaszać alerty dla obsługiwanych metryk w Cognitive Services, przechodząc do sekcji alerty i metryki w programie Azure monitor.

Skonfiguruj ustawienia diagnostyczne dla kontenera Cognitive Services i Wyślij dzienniki do Log Analytics obszaru roboczego. W obszarze roboczym Log Analytics Skonfiguruj alerty, które mają być wykonywane po wstępnym zdefiniowanym zestawie warunków. Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

- [Tworzenie i wyświetlanie alertów dzienników oraz zarządzanie nimi za pomocą Azure Monitor](/azure/azure-monitor/platform/alerts-log)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz informacje o [teście zabezpieczeń Azure: Identity i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: Azure Active Directory (Azure AD) ma wbudowane role, które muszą być jawnie przypisane i są queryable. Za pomocą modułu Azure AD PowerShell można wykonywać zapytania ad hoc w celu odnajdywania kont należących do grup administracyjnych.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: płaszczyzna kontroli dostęp do Cognitive Services jest kontrolowany za pomocą Azure Active Directory (Azure AD). Usługa Azure AD nie ma koncepcji domyślnych haseł.

Dostęp do Cognitive Services płaszczyzny danych jest kontrolowany przez klucze dostępu. Te klucze są używane przez klientów nawiązujących połączenie z pamięcią podręczną i mogą być ponownie generowane w dowolnym momencie.

Nie zaleca się tworzenia domyślnych haseł do aplikacji. Zamiast tego można przechowywać hasła w Azure Key Vault a następnie pobierać je za pomocą usługi Azure AD.

- [Jak wygenerować ponownie pamięć podręczną platformy Azure dla kluczy dostępu Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#settings)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

Ponadto, aby ułatwić śledzenie dedykowanych kont administracyjnych, można użyć zaleceń z Security Center lub wbudowanych zasad platformy Azure, takich jak:

- Do subskrypcji powinien być przypisany więcej niż jeden właściciel
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

- [Jak używać Azure Security Center do monitorowania tożsamości i dostępu (wersja zapoznawcza)](../security-center/security-center-identity-access.md)

- [Jak używać Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Użyj Azure Active Directory logowania jednokrotnego (SSO)

**Wskazówki**: Cognitive Services używa kluczy dostępu do uwierzytelniania użytkowników i nie obsługuje logowania jednokrotnego na poziomie płaszczyzny danych. Dostęp do płaszczyzny kontroli dla Cognitive Services jest dostępny za pośrednictwem interfejsu API REST i obsługuje logowanie jednokrotne. Aby przeprowadzić uwierzytelnianie, należy ustawić nagłówek autoryzacji dla żądań na token sieci Web JSON (JavaScript Object Notation) uzyskany z Azure Active Directory (Azure AD).

- [Informacje o interfejsie API REST usługi Azure Cognitive Services](/rest/api/cognitiveservices/)

- [Opis logowania jednokrotnego w usłudze Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego dla wszystkich Azure Active Directory dostępu opartego na usłudze

**Wskazówki**: Włączanie uwierzytelniania wieloskładnikowego Azure Active Directory (Azure AD) i przestrzeganie Security Center zalecenia dotyczące zarządzania tożsamościami i dostępem.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Korzystaj z bezpiecznych stacji roboczych zarządzanych przez platformę Azure na potrzeby zadań administracyjnych

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

**Wskazówki**: Skonfiguruj nazwane lokalizacje w Azure Active Directory (Azure AD) dostęp warunkowy, aby zezwolić na dostęp tylko z określonych logicznych grup zakresów adresów IP lub krajów i regionów.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych, a także Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika. Jeśli przypadek użycia obsługuje uwierzytelnianie usługi AD, Użyj usługi Azure AD do uwierzytelniania żądań do interfejsu API Cognitive Services. 

Obecnie tylko interfejs API przetwarzania obrazów, Face API, interfejs API analizy tekstu, czytniki immersyjny, aparat rozpoznawania nietypowego wykrywania i wszystkie usługi Bing, z wyjątkiem wyszukiwanie niestandardowe Bing obsługa uwierzytelniania przy użyciu usługi Azure AD.

- [Jak uwierzytelniać żądania do Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-azure-active-directory)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Azure Active Directory (Azure AD) zawiera dzienniki ułatwiające wykrywanie starych kont. Ponadto Klient może korzystać z przeglądów dostępu do tożsamości platformy Azure w celu wydajnego zarządzania członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkownika może być regularnie przeglądany, aby upewnić się, że tylko aktywni użytkownicy mają ciągły dostęp.

Klient do obsługi spisu API Management kont użytkowników, uzgadniania dostępu zgodnie z wymaganiami. W API Management deweloperzy są użytkownikami interfejsów API udostępnianych przy użyciu API Management. Nowo utworzone konta dewelopera są domyślnie aktywne i skojarzone z grupą deweloperzy. Konta deweloperów, które znajdują się w stanie aktywnym, mogą być używane do uzyskiwania dostępu do wszystkich interfejsów API, dla których mają subskrypcje.

- [Zarządzanie kontami użytkowników w usłudze Azure API Management](../api-management/api-management-howto-create-or-invite-developers.md)

- [Jak uzyskać listę API Management użytkowników](https://docs.microsoft.com/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-4.8.0&amp;preserve-view=true)

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

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: niedostępne dla Cognitive Services. Skrytka klienta nie jest jeszcze obsługiwana dla Cognitive Services.

- [Lista usług obsługiwanych przez Skrytka klienta](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

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

**Wskazówki**: implementowanie oddzielnych subskrypcji lub grup zarządzania na potrzeby tworzenia, testowania i produkcji. Zasoby powinny być oddzielone sieciami wirtualnymi lub podsieciami, odpowiednio otagowane i zabezpieczone przez sieciową grupę zabezpieczeń lub zaporę platformy Azure. Zasoby przechowujące lub przetwarzające dane poufne powinny być wystarczająco odizolowane. Aby Virtual Machines przechowywać lub przetwarzać dane poufne, należy wdrożyć zasady i procedury, aby je wyłączyć, gdy nie są używane.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Tworzenie grup zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

- [Jak utworzyć Virtual Network](../virtual-network/quick-create-portal.md)

- [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

- [Jak wdrożyć zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Jak skonfigurować alert lub alert i odmówić za pomocą zapory platformy Azure](../firewall/threat-intel.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: nie są jeszcze dostępne. Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla Cognitive Services.

Firma Microsoft zarządza podstawową infrastrukturą dla Cognitive Services i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: wszystkie Cognitive Services punkty końcowe są uwidocznione za pośrednictwem protokołu HTTP Wymuś protokół TLS 1,2. W przypadku wymuszonego protokołu zabezpieczeń klienci próbujący wywołać Cognitive Services punkt końcowy powinien przestrzegać następujących wytycznych:

- System operacyjny klienta (OS) musi obsługiwać protokół TLS 1,2.
- Język (i platforma) służący do wywołania protokołu HTTP musi określać protokół TLS 1,2 w ramach żądania. W zależności od języka i platformy, określanie protokołu TLS jest wykonywane jawnie lub jawnie.

- [Opis Transport Layer Security platformy Azure Cognitive Services](cognitive-services-security.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla Cognitive Services. Wystąpienia tagów zawierające poufne informacje jako takie i Implementuj rozwiązanie innych firm, jeśli jest to wymagane do celów zgodności.

Firma Microsoft zarządza podstawową platformą i traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, chroniąc przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów 

**Wskazówki**: Użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby kontrolować dostęp do płaszczyzny kontroli Cognitive Services (tj. Azure Portal). 

- [Jak skonfigurować usługę Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: szyfrowanie w spoczynku dla Cognitive Services jest zależne od określonej używanej usługi. W większości przypadków dane są szyfrowane i odszyfrowywane przy użyciu zgodności ze standardem FIPS 140-2 256-bitowym szyfrowaniem AES. Szyfrowanie i odszyfrowywanie są przezroczyste, co oznacza, że szyfrowanie i dostęp są zarządzane dla klientów firmy Microsoft. Dane klientów są domyślnie bezpieczne i nie muszą modyfikować ich kodu ani aplikacji, aby korzystać z szyfrowania.

Możesz również użyć Azure Key Vault do przechowywania kluczy zarządzanych przez klienta. Możesz utworzyć własne klucze i zapisać je w magazynie kluczy lub użyć Azure Key Vault interfejsów API do wygenerowania kluczy.

- [Lista usług, które szyfrują informacje w spoczynku](/azure/cognitive-services/encryption/cognitive-services-encryption-keys-portal)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dla sytuacji, w których zmiany są wprowadzane do wystąpień produkcyjnych Cognitive Services i innych krytycznych lub powiązanych zasobów.

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań lub odnajdywania wszystkich zasobów (takich jak obliczenia, magazyn, Sieć, porty, protokoły itd.) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i Wylicz wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

Mimo że klasyczne zasoby platformy Azure mogą zostać odnalezione za pośrednictwem grafu zasobów, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów do przodu.

- [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

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

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Tworzenie grup zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Ponadto za pomocą usługi Azure Resource Graph można wysyłać zapytania lub odnajdywać zasoby w ramach subskrypcji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

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

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla kontenera Cognitive Services przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. CognitiveServices", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji pamięci podręcznej platformy Azure dla wystąpień Redis.

- [Jak wyświetlić dostępne aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje] efektów, aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji Azure Policy lub szablonów Azure Resource Manager dla kontenerów Cognitive Services i powiązanych zasobów, użyj programu Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

- [Jak przechowywać kod w usłudze Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Dokumentacja Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. cache", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. CognitiveServices", aby utworzyć niestandardowe definicje Azure Policy na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Użyj Azure Policy [Audit], [Odmów] i [Wdróż, jeśli nie istnieje] efektów, aby automatycznie wymuszać konfiguracje dla swojej pamięci podręcznej platformy Azure dla wystąpień Redis i powiązanych zasobów.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: w przypadku maszyn wirtualnych platformy Azure lub aplikacji sieci Web działających na Azure App Service używanym do uzyskiwania dostępu do interfejsu API Cognitive Services Użyj tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć Cognitive Services zarządzanie kluczami. Upewnij się, że Key Vault usuwanie trwałe jest włączone.

- [Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Jak utworzyć Key Vault](/azure/key-vault/quick-create-portal)

- [Jak przeprowadzić uwierzytelnianie w Key Vault](../key-vault/general/authentication.md)

- [Jak przypisać zasady dostępu Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: w przypadku maszyn wirtualnych platformy Azure lub aplikacji sieci Web działających na Azure App Service używanym do uzyskiwania dostępu do interfejsu API Cognitive Services Użyj tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć Cognitive Services zarządzanie kluczami. Upewnij się, że Key Vault usuwanie trwałe jest włączone.

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

Wstępnie Przeskanuj zawartość przekazywaną do zasobów platformy Azure, które nie są obliczeniowe, takich jak App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL itd. Firma Microsoft nie może uzyskać dostępu do danych w tych wystąpieniach.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zapewnianie regularnych zautomatyzowanych kopii zapasowych

**Wskazówki**: dane na koncie magazynu Microsoft Azure są zawsze automatycznie replikowane w celu zapewnienia trwałości i wysokiej dostępności. Usługa Azure Storage kopiuje dane tak, aby były chronione przed planowanymi i nieplanowanymi zdarzeniami, w tym przejściowymi awariami sprzętowymi, siecią lub przestojem, a także ogromnymi katastrofami naturalnymi. Dane można replikować w tym samym centrum danych, w różnych centrach danych w tym samym regionie lub w regionach geograficznie rozdzielonych. 

Za pomocą funkcji zarządzania cyklem życia można także tworzyć kopie zapasowe danych w warstwie archiwum. Ponadto Włącz usuwanie nietrwałe dla kopii zapasowych przechowywanych na koncie magazynu.

- [Informacje o nadmiarowości usługi Azure Storage i umowach Service-Level](../storage/common/storage-redundancy.md)

- [Zarządzanie cyklem życia magazynu usługi Azure Blob Storage](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Soft delete for Azure Storage blobs](../storage/blobs/soft-delete-blob-overview.md) (Usuwanie nietrwałe dla obiektów blob usługi Azure Storage)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: Użyj Azure Resource Manager, aby wdrożyć Cognitive Services i powiązane zasoby. Azure Resource Manager zapewnia możliwość eksportowania szablonów, co pozwala na ponowne wdrożenie rozwiązania w całym cyklu projektowania i niepewność, że zasoby są wdrażane w spójnym stanie. Użyj Azure Automation, aby regularnie wywoływać interfejs API eksportowania szablonu Azure Resource Manager. Wykonaj kopię zapasową kluczy wstępnych w Azure Key Vault.

- [Omówienie usługi Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Jak utworzyć zasób Cognitive Services przy użyciu szablonu Azure Resource Manager](https://docs.microsoft.com/azure/cognitive-services/resource-manager-template?tabs=portal)

- [Eksport jednego i kilku zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Grupy zasobów — Eksportowanie szablonu](/rest/api/resources/resourcegroups/exporttemplate)

- [Wprowadzenie do Azure Automation](../automation/automation-intro.md)

- [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: Upewnij się, że w razie potrzeby okresowo Wdrażaj regularnie wdrożenia szablonów Azure Resource Manager. Testowanie przywracania kopii zapasowych kluczy wstępnych.

- [Wdrażanie zasobów za pomocą szablonów ARM i Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

- [Jak przywrócić klucze magazynu kluczy na platformie Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: usługa Azure DevOps umożliwia bezpieczne przechowywanie szablonów Azure Resource Manager i zarządzanie nimi. Aby chronić zasoby zarządzane w usłudze Azure DevOps, można udzielić lub odmówić uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w Azure Active Directory (Azure AD), jeśli jest zintegrowany z usługą Azure DevOps lub Active Directory, jeśli jest zintegrowany z Team Foundation Server.

Używanie kontroli dostępu opartej na rolach na platformie Azure w celu ochrony kluczy zarządzanych przez klienta. Włącz ochronę Soft-Delete i przeczyszczania w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem. 

- [Jak przechowywać kod w usłudze Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Informacje o uprawnieniach i grupach na platformie Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Soft delete for Azure Storage blobs](../storage/blobs/soft-delete-blob-overview.md) (Usuwanie nietrwałe dla obiektów blob usługi Azure Storage)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu.

- [Jak skonfigurować automatyzację przepływu pracy w programie Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Możesz również wykorzystać Przewodnik obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

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

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w programie Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.

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

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
