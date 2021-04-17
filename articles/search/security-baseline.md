---
title: Punkt odniesienia zabezpieczeń platformy Azure dla Azure Cognitive Search
description: Punkt Azure Cognitive Search zabezpieczeń zawiera wskazówki proceduralne i zasoby dotyczące wdrażania zaleceń dotyczących zabezpieczeń określonych w te testach porównawczych zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: search
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 313ac05d8e4fdc19736d0c35285c2b854ec26968
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589197"
---
# <a name="azure-security-baseline-for-azure-cognitive-search"></a>Punkt odniesienia zabezpieczeń platformy Azure dla Azure Cognitive Search

Ten punkt odniesienia zabezpieczeń stosuje wskazówki z testu porównawczego zabezpieczeń platformy Azure w wersji [1.0](../security/benchmarks/overview-v1.md) do Azure Cognitive Search. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontrolek zabezpieczeń** zdefiniowanych przez test porównawczy zabezpieczeń platformy Azure i powiązanych wskazówek dotyczących Azure Cognitive Search. **Mechanizmy** kontroli nie Azure Cognitive Search lub klient został wykluczony.

Aby zobaczyć, Azure Cognitive Search mapuje całkowicie na test porównawczy zabezpieczeń platformy Azure, zobacz pełny Azure Cognitive Search [mapowania punktów odniesienia zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1. Ochrona zasobów platformy Azure w sieciach wirtualnych

**Wskazówki:** Upewnij się, Microsoft Azure Virtual Network wdrożenia podsieci sieciowych mają zastosowaną sieciową grupę zabezpieczeń z regułami implementowania schematu dostępu "najmniej uprzywilejowanego". Zezwalaj na dostęp tylko do zaufanych portów i zakresów adresów IP aplikacji. Wd Azure Cognitive Search z prywatnym punktem końcowym platformy Azure, jeśli jest to możliwe, aby umożliwić prywatny dostęp do usług z sieci wirtualnej.

Cognitive Search obsługuje również dodatkowe funkcje zabezpieczeń sieci do zarządzania listami kontroli dostępu do sieci. Skonfiguruj usługę wyszukiwania tak, aby zezwalała tylko na komunikację z zaufanymi źródłami, ograniczając dostęp z określonych zakresów publicznych adresów IP przy użyciu możliwości zapory.

- [Jak skonfigurować prywatne punkty końcowe dla Azure Cognitive Search](service-create-private-endpoint.md)

- [Jak skonfigurować zaporę Azure Cognitive Search sieciową](service-configure-firewall.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2. Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i karty sieciowe

**Wskazówki:** Cognitive Search nie można wdrożyć bezpośrednio w sieci wirtualnej. Jeśli jednak aplikacja kliency lub źródła danych znajdują się w sieci wirtualnej, można monitorować i rejestrować ruch dla składników sieciowych, w tym żądań wysyłanych do usługi wyszukiwania w chmurze. Standardowe zalecenia obejmują włączanie dziennika przepływu sieciowej grupy zabezpieczeń i wysyłanie dzienników do usługi Azure Storage lub obszaru roboczego usługi Log Analytics. Opcjonalnie możesz użyć Analiza ruchu, aby uzyskać wgląd w wzorce ruchu.

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** usługa Cognitive Search nie zapewnia konkretnej funkcji umożliwiającej atak typu "rozproszona odmowa usługi", ale można włączyć usługę DDoS Protection Standard w sieciach wirtualnych skojarzonych z usługą Cognitive Search w celu zapewnienia ogólnej ochrony.

- [Jak skonfigurować ochronę przed DDoS](../ddos-protection/manage-ddos-protection.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="15-record-network-packets"></a>1.5. Rejestrowanie pakietów sieciowych

**Wskazówki:** Włącz dzienniki przepływu sieciowych grup zabezpieczeń dla sieciowych grup zabezpieczeń chroniących usługę Azure Virtual Machines (VM), które będą nawiązywać połączenie z usługą Cognitive Search zabezpieczeń. Wysyłanie dzienników na konto usługi Azure Storage w celu inspekcji ruchu. 

Włącz Network Watcher przechwytywania pakietów, jeśli jest to wymagane do badania anomalii działań.

- [Jak włączyć dzienniki przepływu sieciowej organizacji zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć Network Watcher](../network-watcher/network-watcher-create.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6. Wdrażanie opartych na sieci systemów wykrywania włamań/zapobiegania włamań (IDS/IPS)

**Wskazówki:** Cognitive Search nie obsługuje wykrywania włamań do sieci, ale jako środki zaradcze włamań można skonfigurować reguły zapory w celu określenia adresów IP akceptowanych przez usługę Cognitive Search sieci. Skonfiguruj prywatny punkt końcowy, aby zachować ruch wyszukiwania z dala od publicznego Internetu.

- [Jak skonfigurować klucze zarządzane przez klienta na potrzeby szyfrowania danych](search-security-manage-encryption-keys.md)

- [Jak uzyskać informacje o kluczu zarządzanym przez klienta z indeksów i map synonimów](search-security-get-encryption-keys.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8. Zminimalizuj złożoność i koszty administracyjne związane z regułami zabezpieczeń sieci

**Wskazówki:** Użyj tagów usługi, jeśli korzystasz z indeksatorów i umiejętności w usłudze Cognitive Search, aby reprezentować zakres adresów IP, które mają uprawnienia do łączenia się z zasobami zewnętrznymi. 

Zezwalanie na ruch do zasobów lub odmawianie go przez określenie nazwy tagu usługi (na przykład AzureCognitiveSearch) w odpowiednim polu źródła lub miejsca docelowego reguły. 

- [Tagi usługi dla sieci wirtualnej](../virtual-network/service-tags-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="110-document-traffic-configuration-rules"></a>1.10: Udokumentowanie reguł konfiguracji ruchu

**Wskazówki:** możesz skonfigurować usługę Cognitive Search prywatnym punktem końcowym platformy Azure, aby zintegrować usługę wyszukiwania z siecią wirtualną.  Użyj tagów zasobów dla sieciowych grup zabezpieczeń i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. W przypadku poszczególnych reguł sieciowej grupy zabezpieczeń użyj pola "Opis", aby udokumentować reguły zezwalania na ruch do/z sieci. 
 

Użyj dowolnej wbudowanej definicji usługi Azure Policy związanej z tagami, takiej jak efekty "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów, i powiadamiać o istniejących nieotagowanych zasobach. 

Możesz użyć interfejsu Azure PowerShell wiersza polecenia platformy Azure do wyszukiwania lub wykonywania akcji na zasobach na podstawie ich tagów.  

 
- [Jak utworzyć prywatny punkt końcowy dla Cognitive Search](service-create-private-endpoint.md) 

 
 
- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

- [Jak utworzyć usługę Azure Virtual Network](../virtual-network/quick-create-portal.md) 

 
- [Jak filtrować ruch sieciowy przy użyciu reguł sieciowej grupy zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: rejestrowanie i monitorowanie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2. Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** pozyskaj dzienniki związane z usługami Cognitive Search za pośrednictwem usługi Azure Monitor, aby agregować dane zabezpieczeń generowane przez urządzenia punktu końcowego, zasoby sieciowe i inne systemy zabezpieczeń. W Azure Monitor obszarów roboczych usługi Log Analytics można wykonywać zapytania i przeprowadzać analizy, a także używać kont usługi Azure Storage do przechowywania długoterminowego i archiwalnej. Alternatywnie możesz włączyć i dołączyć te dane do rozwiązania Azure Sentinel lub rozwiązania SIEM innej firmy.
 

 
- [Jak rozpocząć pracę z Azure Monitor integracją rozwiązania SIEM i rozwiązania SIEM innej firmy](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)
 

 
- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 
 

 
- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki:** Dzienniki diagnostyczne i operacyjne zapewniają wgląd w szczegółowe operacje usługi Cognitive Search i są przydatne do monitorowania usługi i obciążeń, które mają dostęp do usługi.  Aby przechwycić dane diagnostyczne, włącz rejestrowanie, określając miejsce przechowywania informacji rejestrowania.
 

 
- [Jak zbierać i analizować dane dzienników na Azure Cognitive Search](search-monitor-logs.md) 

 
- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Search:**

[!INCLUDE [Resource Policy for Microsoft.Search 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.search-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5. Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** Dane historyczne, które są wykorzystywane do metryk diagnostycznych, są domyślnie Cognitive Search przez 30 dni. W przypadku dłuższego przechowywania należy włączyć ustawienie, które określa opcję magazynu dla utrwalania zarejestrowanych zdarzeń i metryk.
 

 
W Azure Monitor ustaw okres przechowywania obszaru roboczego usługi Log Analytics zgodnie z przepisami organizacji dotyczącymi zgodności. Używaj kont usługi Azure Storage do przechowywania długoterminowego i archiwalnej. 
 

 
- [Zmiana okresu przechowywania danych w uciekierach usługi Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period) 

 
- [Jak skonfigurować zasady przechowywania dla dzienników konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#enable-logs)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="26-monitor-and-review-logs"></a>2.6. Monitorowanie i przeglądanie dzienników

**Wskazówki:** analizowanie i monitorowanie dzienników z Cognitive Search w celu zachowania anomalii. Użyj Azure Monitor Log Analytics firmy , aby przeglądać dzienniki i wykonywać zapytania dotyczące danych dziennika. Alternatywnie możesz włączyć i dołączyć dane do rozwiązania Azure Sentinel lub rozwiązania SIEM innej firmy. 

 
 
- [Jak zbierać i analizować dane dziennika na Cognitive Search](search-monitor-logs.md)
 
- [Jak wizualizować dane dziennika wyszukiwania w Power BI](search-monitor-logs-powerbi.md)
 

 
- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)
 

 
- [Dowiedz się więcej o usłudze Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)
 

 
- [Jak wykonywać zapytania niestandardowe w Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7. Włącz alerty dla działań anomalii

**Wskazówki:** używanie Security Center usługi Log Analytics do monitorowania i alertów dotyczących anomalii w dziennikach zabezpieczeń i zdarzeniach. Alternatywnie możesz włączyć i włączyć dane, aby Azure Sentinel.
 

 
- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)
 

 
- [Jak zarządzać alertami w Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)
 

 
- [Jak rejestrować alerty dotyczące danych dzienników analizy dzienników](../azure-monitor/alerts/tutorial-response.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: tożsamość i Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1. Utrzymywanie spisu kont administracyjnych

**Wskazówki:** Kontrola dostępu oparta na rolach (RBAC) platformy Azure umożliwia zarządzanie dostępem do zasobów platformy Azure za pomocą przypisań ról. Te role można przypisać do użytkowników, grupować jednostki usługi i tożsamości zarządzane. Dla niektórych zasobów istnieją wstępnie zdefiniowane wbudowane role i te role można zinwentaryzować lub wykonywać względem nich zapytania za pomocą narzędzi takich jak interfejs wiersza polecenia platformy Azure, program Azure PowerShell lub witryna Azure Portal.

Cognitive Search są skojarzone z uprawnieniami, które obsługują zadania zarządzania poziomem usług. Te role nie przyznają dostępu do punktu końcowego usługi. Dostęp do operacji względem punktu końcowego (takich jak zarządzanie indeksami, populacja indeksów i zapytania dotyczące danych wyszukiwania) umożliwia uwierzytelnianie żądania za pomocą kluczy interfejsu API.

- [Ustawianie ról dostępu administracyjnego do Azure Cognitive Search](search-security-rbac.md)

- [Tworzenie kluczy api-keys dla usługi Azure Cognitive Search zarządzania](search-security-api-keys.md)

- [Jak uzyskać rolę katalogu w usłudze Azure Active Directory (Azure AD) przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Używanie dedykowanych kont administracyjnych

**Wskazówki:** Cognitive Search nie ma koncepcji kont administratorów na poziomie lokalnym lub Azure Active Directory (Azure AD), które mogą służyć do zarządzania indeksami i operacjami. 

Użyj wbudowanych ról usługi Azure AD, które muszą być jawnie przypisane do operacji zarządzania. Wywołaj moduł programu PowerShell usługi Azure AD, aby wykonać zapytania ad hoc w celu odnajdywania kont, które są członkami grup administracyjnych.

- [Jak używać ról na użytek dostępu administracyjnego w Cognitive Search](search-security-rbac.md)

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Korzystanie z logowania jednokrotnego (SSO) z Azure Active Directory

**Wskazówki:** Użyj uwierzytelniania za pomocą logowania jednokrotnego Azure Active Directory (Azure AD), aby uzyskać dostęp do informacji o usłudze wyszukiwania dla operacji zarządzania obsługiwanych za pośrednictwem Azure Resource Manager. 

Ustanów proces zmniejszenia liczby tożsamości i poświadczeń, włączając logowanie jednokrotne dla usługi przy użyciu wcześniej istniejących tożsamości w organizacji.

- [Understand SSO with Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Używaj uwierzytelniania wieloskładnikowego dla wszystkich Azure Active Directory dostępu opartego na danych

**Wskazówki:** włącz Azure Active Directory uwierzytelniania wieloskładnikowego usługi Azure AD i postępuj zgodnie Security Center z zaleceniami dotyczącymi tożsamości i dostępu firmy.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Jak monitorować tożsamość i dostęp w ramach Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6. Użyj dedykowanych maszyn (stacji roboczych z dostępem uprzywilejowanym) do wykonywania wszystkich zadań administracyjnych

**Wskazówki:** używanie stacji roboczej z dostępem uprzywilejowanym (PAW) z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się do zasobów platformy Azure i uzyskiwania do nich dostępu.
 

 
- [Opis bezpiecznych stacji roboczych zarządzanych przez platformę Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 

 
- [Jak włączyć uwierzytelnianie wieloskładnikowe Azure Active Directory (Azure AD)](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Rejestrowanie i alerty dotyczące podejrzanych działań z kont administracyjnych

**Wskazówki:** użyj Azure Active Directory zabezpieczeń i monitorowania usługi Azure AD, aby wykryć, kiedy w środowisku występuje podejrzane lub niebezpieczne działanie. Użyj Security Center, aby monitorować działania tożsamości i dostępu.

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych na skutek ryzykownego działania](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak monitorować działania użytkowników związane z tożsamościami i dostępem w usłudze Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="39-use-azure-active-directory"></a>3.9: Użyj Azure Active Directory

**Wskazówki:** Użyj usługi Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji dla zadań zarządzania poziomem usług w Azure Cognitive Search. Tożsamości usługi Azure AD nie przyznają dostępu do punktu końcowego usługi wyszukiwania.  Dostęp do operacji, takich jak zarządzanie indeksami, populacja indeksów i zapytania dotyczące danych wyszukiwania, jest dostępny za pośrednictwem kluczy interfejsu API.

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Tworzenie kluczy api-keys dla usługi Azure Cognitive Search zarządzania](search-security-api-keys.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** usługa Azure Active Directory (Azure AD) udostępnia dzienniki, które ułatwiają odnajdywanie nieaktywnego konta. Używaj przeglądów tożsamości i dostępu usługi Azure AD, aby efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisań ról. Dostęp użytkowników można regularnie przeglądać, aby upewnić się, że tylko odpowiedni użytkownicy mają dalszy dostęp. 

Przejrzyj dzienniki diagnostyczne z Cognitive Search aktywności w punkcie końcowym usługi wyszukiwania, takich jak zarządzanie indeksami, populacja indeksów i zapytania.

- [Informacje o raportowaniu w usłudze Azure AD](/azure/active-directory/reports-monitoring/)

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Monitorowanie operacji i działań Azure Cognitive Search](search-monitor-usage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych poświadczeń

**Wskazówki:** Dostęp do usługi Azure Active Directory logowania, inspekcji i dzienników zdarzeń ryzyka (Azure AD) umożliwiają integrację z dowolnym narzędziem SIEM lub narzędziem do monitorowania.

Usprawnij ten proces, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając dzienniki inspekcji i logowania do obszaru roboczego usługi Log Analytics. Konfigurowanie żądanych alertów w obszarze roboczym usługi Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alert o odchyleniu zachowania logowania do konta

**Wskazówki:** używanie Azure Active Directory usługi Identity Protection (Azure AD) do konfigurowania automatycznych odpowiedzi na wykryte podejrzane akcje związane z tożsamościami użytkowników. Pozysuj dane do Azure Sentinel w celu dalszego badania, zgodnie z potrzebami.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady ryzyka związane z usługą Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Utrzymywanie spisu informacji poufnych

**Wskazówki:** używanie tagów do śledzenia zasobów platformy Azure, które przechowują lub przetwarzają poufne informacje.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Odizoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki:** Zaim implementuj oddzielne subskrypcje i/lub grupy zarządzania na rzecz testowania, testowania i tworzenia aplikacji. Zasoby powinny być oddzielone siecią wirtualną/podsiecią, odpowiednio oznakowane i zabezpieczone w ramach sieciowej grupy zabezpieczeń lub Azure Firewall. Zasoby przechowujące lub przetwarzające poufne dane powinny być izolowane. Użyj Private Link, aby skonfigurować prywatny punkt końcowy do Cognitive Search.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

- [Jak utworzyć prywatny punkt końcowy dla Cognitive Search](service-create-private-endpoint.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego transferu poufnych informacji

**Wskazówki:** Użyj rozwiązania innej firmy firmy Azure Marketplace w obwodach sieci w celu monitorowania nieautoryzowanego transferu poufnych informacji i blokowania takich transferów podczas powiadamiania specjalistów ds. zabezpieczeń informacji.

Firma Microsoft zarządza podstawową platformą i traktuje całą zawartość klientów jako poufne i chroni przed utratą i utratą danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych funkcji i kontroli ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5. Identyfikowanie poufnych danych za pomocą aktywnego narzędzia odnajdywania

**Wskazówki:** Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla Cognitive Search. W razie potrzeby zaim implementuj rozwiązanie innej firmy w celu zapewnienia zgodności. 

Firma Microsoft zarządza podstawową platformą i traktuje całą zawartość klientów jako poufne i chroni przed utratą i utratą danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych funkcji i kontroli ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6. Zarządzanie dostępem do zasobów przy użyciu kontroli RBAC platformy Azure

**Wskazówki:** Do administrowania usługą użyj kontroli dostępu opartej na rolach (RBAC) platformy Azure, aby zarządzać dostępem do kluczy i konfiguracji. W przypadku operacji na zawartości, takich jak indeksowanie i zapytania, Cognitive Search klucze zamiast modelu kontroli dostępu opartej na tożsamościach. Kontrola dostępu na platformie Azure przy użyciu kontroli dostępu na platformie Azure umożliwia kontrolowanie dostępu do kluczy. 

 
- [Jak skonfigurować RBAC platformy Azure](../role-based-access-control/role-assignments-portal.md)  
 
 
- [Jak używać ról do uzyskiwania dostępu administracyjnego do Cognitive Search](search-security-rbac.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Szyfrowanie poufnych informacji w spoczynku

**Wskazówki:** Cognitive Search automatycznie szyfruje indeksowana zawartość w spoczynku przy użyciu kluczy zarządzanych przez firmę Microsoft. Jeśli potrzebna jest większa ochrona, można uzupełnić szyfrowanie domyślne drugą warstwą szyfrowania przy użyciu kluczy tworzyć i zarządzać nimi w Azure Key Vault.

- [Konfigurowanie kluczy zarządzanych przez klienta na potrzeby szyfrowania danych w Azure Cognitive Search](search-security-manage-encryption-keys.md)

- [Informacje o szyfrowaniu danych magazynowanych na platformie Azure](../security/fundamentals/encryption-atrest.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Rejestrowanie i alerty dotyczące zmian w krytycznych zasobach platformy Azure

**Wskazówki:** użyj Azure Monitor dziennika aktywności platformy Azure, aby tworzyć alerty dotyczące sytuacji, w których wystąpią zmiany w wystąpieniach produkcyjnych Cognitive Search i innych krytycznych lub powiązanych zasobów. 

 
- [Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/alerts/alerts-activity-log.md) 

 
- [Jak tworzyć alerty dla Cognitive Search działań](search-monitor-logs.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: spis i zarządzanie zasobami.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1. Korzystanie z rozwiązania do automatycznego odnajdywania zasobów

**Wskazówki:** użyj Azure Resource Graph do wyszukiwania i odnajdywania wszystkich zasobów (takich jak zasoby obliczeniowe, magazynowe, sieciowe, porty, protokoły itp.) w subskrypcjach.

Upewnij się, że w dzierżawie są odpowiednie uprawnienia (odczyt) i wyliczenie wszystkich subskrypcji platformy Azure, a także zasobów w twoich subskrypcjach.

- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlać subskrypcje platformy Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Opis kontroli RBAC na platformie Azure](../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="62-maintain-asset-metadata"></a>6.2. Obsługa metadanych zasobów

**Wskazówki:** Stosowanie tagów do zasobów platformy Azure z metadanymi w celu logicznego zorganizowania ich w taksonomię.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="63-delete-unauthorized-azure-resources"></a>6.3. Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** W celu organizowania i śledzenia zasobów używaj tagowania, grup zarządzania i oddzielnych subskrypcji, jeśli jest to konieczne. Regularnie uzgadniaj spis i upewnij się, że nieautoryzowane zasoby zostaną usunięte z subskrypcji w terminowy sposób.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak tworzyć grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4. Definiowanie i utrzymywanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki:** Zdefiniuj listę zatwierdzonych zasobów platformy Azure związanych z indeksowaniem i przetwarzaniem Cognitive Search.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorowanie niezatwierdzonych zasobów platformy Azure

**Wskazówki:** zaleca się zdefiniowanie spisu zasobów platformy Azure, które zostały wcześniej zatwierdzone do użycia zgodnie z zasadami i standardami organizacji, a następnie monitorowanie niezatwierdzonych zasobów platformy Azure za pomocą Azure Policy lub Azure Resource Graph.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md) 

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="69-use-only-approved-azure-services"></a>6.9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki:** użyj Azure Policy, aby nakładać ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Użyj Azure Resource Graph, aby odpytywać lub odnajdywać zasoby w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku zostały zatwierdzone.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](/azure/governance/policy/samples/)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Ograniczanie możliwości interakcji użytkowników z Azure Resource Manager

**Wskazówki:** Użyj dostępu warunkowego platformy Azure, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resource Manager, konfigurując opcję "Blokuj dostęp" dla aplikacji "Microsoft Azure Management".  

 
Kontroluj dostęp do kluczy używanych do uwierzytelniania żądań dla wszystkich innych operacji, szczególnie tych związanych z zawartością Cognitive Search.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: bezpieczna konfiguracja.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki:** użyj Azure Policy w przestrzeni nazw "Microsoft.Search", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji Azure Cognitive Search zasobów. Możesz również użyć wbudowanych definicji Azure Policy usług Cognitive Search takich jak:

Włączanie rejestrowania inspekcji dla zasobów platformy Azure

Azure Resource Manager szablon można wyeksportować w pliku JavaScript Object Notation (JSON), który należy przejrzeć, aby upewnić się, że konfiguracje spełniają wymagania dotyczące zabezpieczeń w organizacji.

Możesz również użyć zaleceń z witryny Azure Security Center jako bezpiecznej konfiguracji odniesienia dla zasobów platformy Azure.

- [Azure Policy kontroli zgodności z przepisami dla Azure Cognitive Search](security-controls-policy.md)

- [Jak wyświetlić dostępne Azure Policy aliasy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** użyj Azure Policy [odmów] i [wd wdrażaj, jeśli nie istnieje], aby wymusić bezpieczne ustawienia w zasobach Cognitive Search usługi. 

Azure Resource Manager szablonów można użyć do zachowania konfiguracji zabezpieczeń zasobów platformy Azure wymaganych przez organizację. 

- [Zrozumienie Azure Policy danych](../governance/policy/concepts/effects.md)

- [Azure Policy kontroli zgodności z przepisami dla Azure Cognitive Search](security-controls-policy.md)

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)

- [omówienie Azure Resource Manager szablonów](../azure-resource-manager/templates/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** Jeśli używasz niestandardowych Azure Policy, użyj Azure DevOps lub Azure Repos, aby bezpiecznie przechowywać kod i zarządzać jego kodem.

- [Jak przechowywać kod w Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Azure Repos dokumentacji](/azure/devops/repos/index)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów Cognitive Search przy użyciu Azure Policy. 

Użyj aliasów, aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci. Możesz również korzystać z wbudowanych definicji zasad związanych z określonymi zasobami. 

Ponadto można użyć programu Azure Automation do wdrażania zmian konfiguracji i zarządzania wyjątkami zasad. 

- [Azure Policy kontroli zgodności z przepisami dla Azure Cognitive Search](security-controls-policy.md)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementowanie zautomatyzowanego monitorowania konfiguracji dla zasobów platformy Azure

**Wskazówki:** użyj Security Center, aby przeprowadzić skanowanie bazowe zasobów Cognitive Search usługi.  Ponadto użyj Azure Policy alertów i inspekcji konfiguracji zasobów. 

- [Jak korygować zalecenia w Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Azure Policy kontroli zgodności z przepisami dla Azure Cognitive Search](security-controls-policy.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** Używaj tożsamości zarządzanych platformy Azure w połączeniu z Azure Key Vault, aby uprościć zarządzanie kluczami tajnymi dla aplikacji w chmurze.

- [Jak używać tożsamości zarządzanych dla zasobów platformy Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Jak utworzyć Key Vault](../key-vault/general/quick-create-portal.md)

- [Jak zapewnić uwierzytelnianie Key Vault tożsamości zarządzanej](../key-vault/general/assign-access-policy-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** użyj tożsamości zarządzanej platformy Azure, aby udzielić usłudze Cognitive Search dostępu do innych usług platformy Azure, takich jak źródła danych usługi Key Vault i indeksatora, przy użyciu automatycznie zarządzanej tożsamości w usłudze Azure Active Directory (Azure AD). Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym Azure Key Vault, bez żadnych poświadczeń w kodzie. 

- [Konfigurowanie połączenia indeksatora ze źródłem danych przy użyciu tożsamości zarządzanej](search-howto-managed-identities-data-sources.md)

- [Konfigurowanie kluczy zarządzanych przez klienta na potrzeby szyfrowania danych przy użyciu tożsamości zarządzanej](search-security-manage-encryption-keys.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Malware Defense.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Pliki przed skanowaniem, które mają zostać przekazane do zasobów platformy Azure bez obliczeń

**Wskazówki:** wstępnie przeskanuj zawartość przesyłaną do zasobów platformy Azure, które nie są obliczeniowe, takich jak Cognitive Search, Blob Storage, Azure SQL Database i tak dalej. 

To Ty odpowiadasz za wstępne skanowanie zawartości przekazywanej do zasobów platformy Azure, które nie są obliczeniowe. Firma Microsoft nie może uzyskać dostępu do danych klienta i w związku z tym nie może przeprowadzać skanowania zawartości klienta w poszukiwaniu złośliwego oprogramowania w Jego imieniu.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: Upewnij się, że oprogramowanie do ochrony przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki:** Nie dotyczy Cognitive Search. Nie zezwala na to, aby na jego zasobach były instalowane rozwiązania chroniące przed złośliwym kodem. W przypadku podstawowej platformy firma Microsoft obsługuje aktualizowanie dowolnego oprogramowania chroniącego przed złośliwym oprogramowaniem i podpisów.  

 
W przypadku wszystkich zasobów obliczeniowych należących do organizacji i używanych w rozwiązaniu do wyszukiwania postępuj zgodnie z zaleceniami w te Security Center, Compute Apps (Aplikacje obliczeniowe), aby upewnić się, że wszystkie punkty końcowe są aktualne i mają najnowsze &amp; sygnatury. W przypadku systemu Linux użyj rozwiązania do ochrony przed złośliwym kodem innej firmy.

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: odzyskiwanie danych.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnianie regularnych automatycznych kopii zapasowej

**Wskazówki:** Kopii zapasowej zawartości przechowywanej w usłudze wyszukiwania nie można tworzyć za pomocą programu Azure Backup ani żadnego innego wbudowanego mechanizmu, ale można ponownie skompilować indeks z kodu źródłowego aplikacji i podstawowych źródeł danych lub utworzyć niestandardowe narzędzie do pobierania i przechowywania indeksowanych zawartości. 

 
- [GitHub Index-backup-restore sample (Przykład polecenia GitHub Index-backup-restore)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/index-backup-restore)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2. Wykonywanie pełnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki:** Cognitive Search obecnie nie obsługuje automatycznego tworzenia kopii zapasowych danych w usłudze wyszukiwania i należy utworzyć kopię zapasową za pośrednictwem procesu ręcznego. Możesz również utworzyć kopię zapasową kluczy zarządzanych przez klienta w Azure Key Vault.
 

- [Back up and restore an Azure Cognitive Search index (Kopii zapasowej i przywracania indeksu Azure Cognitive Search danych)](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Jak utworzyć kopię zapasową Key Vault na platformie Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Zweryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki:** Cognitive Search obecnie nie obsługuje automatycznego tworzenia kopii zapasowych danych w usłudze wyszukiwania i należy utworzyć kopię zapasową i przywrócić je za pośrednictwem procesu ręcznego. Okresowo przeprowadzaj przywracanie danych zawartości, dla których kopia zapasowa jest samodzielnie tworzyć kopie zapasowe, aby zapewnić spójność procesu tworzenia kopii zapasowej.

- [Back up and restore an Azure Cognitive Search index (Kopii zapasowej i przywracania indeksu Azure Cognitive Search danych)](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Jak przywrócić klucze Key Vault na platformie Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4. Zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** Cognitive Search obecnie nie obsługuje automatycznego tworzenia kopii zapasowych danych w usłudze wyszukiwania i należy utworzyć kopię zapasową za pośrednictwem procesu ręcznego.  Możesz również utworzyć kopię zapasową kluczy zarządzanych przez klienta w Azure Key Vault.  

 
Włącz usuwanie nie soft i ochronę przed przeczyszczaniem w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem. Jeśli usługa Azure Storage jest używana do przechowywania ręcznych kopii zapasowych, włącz usuwanie nie softowe, aby zapisywać i odzyskiwać dane po usunięciu obiektów blob lub migawek obiektów blob. 
 

 
- [Back up and restore an Azure Cognitive Search index](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)
 

 
- [Jak włączyć ochronę w formie usuwania nietrwałego i przeczyszczania w usłudze Key Vault](../storage/blobs/soft-delete-blob-overview.md) 

- [Usuwanie nie softowe dla usługi Azure Blob Storage](../storage/blobs/soft-delete-blob-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Tworzenie przewodnika po reagowaniu na zdarzenia

**Wskazówki:** Opracowywanie przewodnika reagowania na zdarzenia dla organizacji. Upewnij się, że istnieją zapisane plany reagowania na zdarzenia, które definiują wszystkie role personelu, a także fazy obsługi zdarzeń i zarządzania nimi od wykrywania do przeglądu po zdarzeniu.

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Centrum zabezpieczeń firmy Microsoft Anatomy of an Incident (Anatomia zdarzenia)](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Klient może również skorzystać z przewodnika obsługi zdarzeń zabezpieczeń komputera NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Tworzenie procedury oceniania i ustalania priorytetów zdarzeń

**Wskazówki:** Security Center przypisać ważność do każdego alertu, aby ułatwić priorytetyzowanie alertów, które należy zbadać w pierwszej kolejności. Ważność jest oparta na tym, jak pewna Security Center znajduje się w znalezieniu lub analitycznej podstawie użytej do wystawienia alertu, a także na poziomie pewności, że za działaniem, które doprowadziło do tego alertu, istnieje złośliwe intencje.

Ponadto należy oznaczyć subskrypcje przy użyciu tagów i utworzyć system nazewnictwa w celu identyfikowania i kategoryzowania zasobów platformy Azure, szczególnie tych przetwarzających poufne dane. To Ty odpowiadasz za ustalanie priorytetów korygowania alertów na podstawie krytyczności zasobów i środowiska platformy Azure, w których wystąpiło zdarzenie.

- [Organizowanie zasobów platformy Azure przy użyciu tagów](../azure-resource-manager/management/tag-resources.md)

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki:** Przećwiczyj ćwiczenia, aby regularnie testować możliwości reagowania na zdarzenia w systemach. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Zapoznaj się z publikacją NIST "Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities" (Przewodnik po programach testowych, szkoleniowych i szkoleniowych dla planów i możliwości IT)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Podaj szczegóły kontaktowe zdarzenia zabezpieczeń i skonfiguruj powiadomienia o alertach dla zdarzeń zabezpieczeń

**Wskazówki:** informacje kontaktowe dotyczące zdarzeń naruszenia zabezpieczeń będą używane przez firmę Microsoft do kontaktowania się z tobą, jeśli Centrum zabezpieczeń firmy Microsoft (MSRC) odkryje, że dostęp do danych użytkownika został uzyskany przez niedozwoloną lub niedozwoloną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt Azure Security Center zabezpieczeń](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Dołączanie alertów zabezpieczeń do systemu reagowania na zdarzenia

**Wskazówki:** eksportowanie alertów Security Center i zaleceń przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia ręczne lub ciągłe eksportowanie alertów i zaleceń. Możesz użyć łącznika danych Security Center, aby przesyłać strumieniowo alerty do Azure Sentinel.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki:** Użyj funkcji automatyzacji przepływu pracy w Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [test porównawczy zabezpieczeń platformy Azure: testy penetracyjne i ćwiczenia zespołu czerwonego.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Przeprowadzaj regularne testy penetrowania zasobów platformy Azure i zapewniaj korygowanie wszystkich krytycznych ustaleń zabezpieczeń

**Wskazówki:** postępuj zgodnie z regułami testowania penetracyjnego firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszały zasad firmy Microsoft. Korzystaj ze strategii firmy Microsoft i wykonywania testów typu „red team” i testów na żywo w zarządzanej przez firmę Microsoft infrastrukturze, usługach i aplikacjach w chmurze.

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
