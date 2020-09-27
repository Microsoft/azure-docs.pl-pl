---
title: Podstawa zabezpieczeń Azure dla usługi Azure Wyszukiwanie poznawcze
description: Podstawowa usługa Azure Wyszukiwanie poznawcze Security zawiera wskazówki i zasoby związane z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: search
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b2dea32163fbb2827daed616087c893631429aea
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400864"
---
# <a name="azure-security-baseline-for-azure-cognitive-search"></a>Podstawa zabezpieczeń Azure dla usługi Azure Wyszukiwanie poznawcze

Ta linia bazowa zabezpieczeń ma zastosowanie do Wyszukiwanie poznawcze platformy Azure wskazówek dotyczących usługi [Azure Security test w wersji 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview) . Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez usługę Azure Security test i powiązane wskazówki dotyczące usługi Azure wyszukiwanie poznawcze. **Kontrolki** nie mają zastosowania do usługi Azure wyszukiwanie poznawcze lub klient został wykluczony.

Aby dowiedzieć się, jak platforma Azure Wyszukiwanie poznawcze całkowicie zamapowana na test porównawczy zabezpieczeń platformy Azure, zapoznaj się z [pełnym plikiem mapowania podstawy zabezpieczeń usługi azure wyszukiwanie poznawcze](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [wzorzec zabezpieczeń Azure: zabezpieczenia sieci](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: Upewnij się, że wszystkie wdrożenia podsieci Microsoft Azure Virtual Network mają zastosowana sieciową grupę zabezpieczeń z regułami dotyczącymi implementacji "najmniej uprzywilejowanego" schematu dostępu. Zezwalaj na dostęp tylko do zaufanych portów i zakresów adresów IP aplikacji. Wdróż usługę Azure Wyszukiwanie poznawcze przy użyciu prywatnego punktu końcowego platformy Azure, gdzie to możliwe, aby umożliwić prywatny dostęp do usług z sieci wirtualnej.

Wyszukiwanie poznawcze obsługuje również dodatkowe funkcje zabezpieczeń sieci do zarządzania listami kontroli dostępu do sieci. Skonfiguruj usługę wyszukiwania, aby zezwalać na komunikację z zaufanymi źródłami przez ograniczenie dostępu z określonych zakresów publicznych adresów IP przy użyciu funkcji zapory.

- [Jak skonfigurować prywatne punkty końcowe dla usługi Azure Wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/service-create-private-endpoint)

- [Jak skonfigurować zaporę usługi Azure Wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/service-configure-firewall)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: wyszukiwanie poznawcze nie mogą być wdrażane bezpośrednio w sieci wirtualnej. Jeśli jednak aplikacja kliencka lub źródła danych znajdują się w sieci wirtualnej, można monitorować i rejestrować ruch dla tych składników w sieci, w tym żądań wysyłanych do usługi wyszukiwania w chmurze. Standardowe zalecenia obejmują włączenie dziennika przepływu sieciowych grup zabezpieczeń i wysyłanie dzienników do usługi Azure Storage lub obszaru roboczego Log Analytics. Opcjonalnie można użyć Analiza ruchu do wglądu w wzorce ruchu.

- [Jak włączyć dzienniki przepływu sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Jak włączyć i używać Analiza ruchu](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. To zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: wyszukiwanie poznawcze nie udostępnia konkretnej funkcji do walki z rozproszonym atakiem typu "odmowa usługi", ale można włączyć Standard DDoS Protection w sieciach wirtualnych skojarzonych z usługą wyszukiwanie poznawcze na potrzeby ochrony ogólnej.

- [Jak skonfigurować ochronę DDoS](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: Włączanie dzienników przepływu sieciowych grup zabezpieczeń dla sieciowych grup zabezpieczeń Ochrona Virtual Machines platformy Azure, która będzie podłączana do usługi Wyszukiwanie poznawcze. Wyślij dzienniki do konta usługi Azure Storage na potrzeby inspekcji ruchu. 

Włącz funkcję przechwytywania pakietów Network Watcher, jeśli jest wymagana do badania nietypowej aktywności.

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Jak włączyć Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: wyszukiwanie poznawcze nie obsługuje wykrywania intruzów w sieci, ale w przypadku ograniczenia włamania można skonfigurować reguły zapory, aby określić adresy IP akceptowane przez usługę wyszukiwanie poznawcze. Skonfiguruj prywatny punkt końcowy, aby zachować ruch wyszukiwania poza publiczny Internet.

- [Jak skonfigurować klucze zarządzane przez klienta do szyfrowania danych](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)

- [Jak uzyskać informacje o kluczu zarządzanym przez klienta z indeksów i map synonimów](https://docs.microsoft.com/azure/search/search-security-get-encryption-keys)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. To zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi, jeśli używasz indeksatorów i umiejętności w wyszukiwanie poznawcze, aby reprezentować zakres adresów IP, które mają uprawnienia do nawiązywania połączeń z zasobami zewnętrznymi. 

Zezwalaj lub Odmawiaj ruchu do zasobów, określając nazwę tagu usługi (na przykład AzureCognitiveSearch) w odpowiednim polu źródłowym lub docelowym reguły. 

- [Tagi usługi sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: wyszukiwanie poznawcze nie ma lub zależą od zasobów sieciowych przez zaprojektowanie. Aplikacje klienckie i źródła danych powiązane z aplikacją wyszukiwania mogą znajdować się w sieci wirtualnej, ale usługa wyszukiwania nie jest wdrażana w sieci. 

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: można skonfigurować wyszukiwanie poznawcze za pomocą prywatnego punktu końcowego platformy Azure, aby zintegrować usługę wyszukiwania z siecią wirtualną.  Użyj tagów zasobów dla sieciowych grup zabezpieczeń i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. Dla poszczególnych reguł grup zabezpieczeń sieci Użyj pola "opis", aby udokumentować reguły zezwalające na ruch do/z sieci. 

Użyj dowolnych wbudowanych definicji Azure Policy związanych ze znakowaniem, takimi jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o istniejących nieoznakowanych zasobach. 

Możesz użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów. 

- [Jak utworzyć prywatny punkt końcowy dla Wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/service-create-private-endpoint)

- [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Jak utworzyć Virtual Network platformy Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Jak filtrować ruch sieciowy przy użyciu reguł sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: wyszukiwanie poznawcze nie mają lub nie są zależne od żadnych składników sieciowych, dlatego nie można monitorować konfiguracji tych zasobów.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu

**Wskazówki**: wyszukiwanie poznawcze nie obsługuje konfigurowania własnych źródeł synchronizacji czasu. Usługa wyszukiwania korzysta ze źródeł synchronizacji czasu firmy Microsoft i nie jest dostępna dla klientów w celu ich konfiguracji.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: pobieranie dzienników związanych z wyszukiwanie poznawcze za pośrednictwem Azure monitor do agregowania danych zabezpieczeń wygenerowanych przez urządzenia, zasoby sieciowe i inne systemy zabezpieczeń. W Azure Monitor używać Log Analytics obszarów roboczych do wykonywania zapytań i przeprowadzania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego i archiwizowania.
Alternatywnie możesz włączyć i dołączyć te dane do usługi Azure wskaźnikowej lub SIEM innej firmy.

- [Jak rozpocząć pracę z usługą Azure Monitor i integracją SIEM innej firmy](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [Jak dołączyć wskaźnik na platformie Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: dzienniki diagnostyczne i operacyjne zapewniają wgląd w szczegółowe operacje wyszukiwanie poznawcze i są przydatne do monitorowania usługi oraz obciążeń, które uzyskują dostęp do usługi.  Aby przechwytywać dane diagnostyczne, należy włączyć rejestrowanie przez określenie, gdzie są przechowywane informacje o rejestrowaniu.

- [Jak zbierać i analizować dane dzienników dla usługi Azure Wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/search-monitor-logs)

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) 

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. To zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: dane historyczne, które są strumieniowo uwzględniane w metrykach diagnostycznych, są domyślnie zachowywane przez wyszukiwanie poznawcze przez 30 dni. Aby zapewnić dłuższe przechowywanie, należy włączyć ustawienie określające opcję magazynu dla utrwalania zarejestrowanych zdarzeń i metryk.

W Azure Monitor Ustaw okres przechowywania obszaru roboczego Log Analytics zgodnie z regulacjami dotyczącymi zgodności w organizacji. Używaj kont usługi Azure Storage do przechowywania długoterminowego i archiwizowania. 

- [Zmień okres przechowywania danych w Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Jak skonfigurować zasady przechowywania dla dzienników kont usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: analizowanie i monitorowanie dzienników z usługi Wyszukiwanie poznawcze w celu nietypowego zachowania. Użyj Log Analytics Azure Monitor, aby przejrzeć dzienniki i wykonywać zapytania dotyczące danych dziennika. Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

- [Jak zbierać i analizować dane dzienników dla Wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/search-monitor-logs)

- [Jak wizualizować dane dziennika wyszukiwania w Power BI](https://docs.microsoft.com/azure/search/search-monitor-logs-powerbi)

- [Jak dołączyć wskaźnik na platformie Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Dowiedz się więcej o usłudze Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

- [Jak wykonywać niestandardowe zapytania w Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: Użyj Security Center z obszarem roboczym log Analytics do monitorowania i generowania alertów dotyczących nietypowego działania znalezionego w dziennikach i zdarzeniach zabezpieczeń. Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej.

- [Jak dołączyć wskaźnik na platformie Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Jak zarządzać alertami w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

- [Jak ostrzec dane dziennika usługi log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. Firma Microsoft zarządza rozwiązaniem chroniącym przed złośliwym oprogramowaniem dla podstawowej platformy.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. Nie produkuje ani nie zużywa dzienników DNS.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. Inspekcja w wierszu polecenia nie jest dostępna dla Wyszukiwanie poznawcze.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: tożsamość i kontrola dostępu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: Kontrola dostępu oparta na rolach (Azure RBAC) umożliwia zarządzanie dostępem do zasobów platformy Azure za pomocą przypisań ról. Te role można przypisać do użytkowników, grup jednostek usługi i zarządzanych tożsamości. Istnieją wstępnie zdefiniowane wbudowane role dla niektórych zasobów i te role można spisować lub odpytać za pomocą narzędzi takich jak interfejs wiersza polecenia platformy Azure, Azure PowerShell lub Azure Portal.

Role Wyszukiwanie poznawcze są skojarzone z uprawnieniami, które obsługują zadania zarządzania poziomem usług.  Role te nie udzielają dostępu do punktu końcowego usługi. Dostęp do operacji względem punktu końcowego (takiego jak zarządzanie indeksami, populacja indeksu i zapytania dotyczące danych wyszukiwania) za pomocą kluczy interfejsu API można uwierzytelniać żądanie.

- [Ustawianie ról dla dostępu administracyjnego do usługi Azure Wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/search-security-rbac)

- [Tworzenie i zarządzanie kluczami interfejsu API dla usługi Wyszukiwanie poznawcze platformy Azure](https://docs.microsoft.com/azure/search/search-security-api-keys)

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)
- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. Nie ma koncepcji domyślnych haseł.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: wyszukiwanie poznawcze nie ma koncepcji żadnych kont administratorów na poziomie lokalnym lub Azure Active Directory (Azure AD), których można użyć do zarządzania indeksami i operacjami. 

Użyj wbudowanych ról usługi Azure AD, które muszą być jawnie przypisane do operacji zarządzania. Wywołaj moduł PowerShell usługi Azure AD, aby wykonywać zapytania ad hoc w celu odnajdywania kont należących do grup administracyjnych.

- [Jak używać ról do dostępu administracyjnego w programie Wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/search-security-rbac)

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: Użyj uwierzytelniania jednokrotnego w usłudze Azure Active Directory (Azure AD), aby uzyskać dostęp do informacji o usłudze Search dla operacji zarządzania obsługiwanych przez Azure Resource Manager. 

Ustanów proces, aby zmniejszyć liczbę tożsamości i poświadczeń przez włączenie logowania jednokrotnego dla usługi przy użyciu istniejących tożsamości w organizacji.

- [Opis logowania jednokrotnego w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: włączanie funkcji Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA) i postępuj zgodnie z zaleceniami i dostępem Security Center.

- [Jak włączyć usługę MFA na platformie Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) 

- [Jak monitorować tożsamość i dostęp w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access) 

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj stacji roboczej dostępu uprzywilejowanego (dostępem uprzywilejowanym) z usługą Multi-Factor Authentication (MFA) skonfigurowaną w celu logowania się do zasobów platformy Azure i uzyskiwania do nich dostępu.

- [Informacje na temat bezpiecznych stacji roboczych zarządzanych przez platformę Azure](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-managed-workstation)
 

- [Jak włączyć usługę Azure AD MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)
 

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: Użyj raportów i monitorowania zabezpieczeń Azure Active Directory (Azure AD), aby wykryć, kiedy w środowisku występuje podejrzane lub niebezpieczne działania. Użyj Security Center, aby monitorować działania związane z tożsamościami i dostępem.

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych w celu działania ryzykownego](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Jak monitorować działania związane z tożsamościami i dostępem użytkowników w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: Zarządzaj zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. Nie obsługuje on użycia zatwierdzonej lokalizacji jako warunek dostępu.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji na potrzeby zadań zarządzania na poziomie usług w usłudze Azure wyszukiwanie poznawcze. Tożsamości usługi Azure AD nie udzielają dostępu do punktu końcowego usługi wyszukiwania.  Dostęp do operacji takich jak zarządzanie indeksami, populacja indeksu i zapytania dotyczące danych wyszukiwania są dostępne za pośrednictwem kluczy interfejsu API.

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

- [Tworzenie i zarządzanie kluczami interfejsu API dla usługi Wyszukiwanie poznawcze platformy Azure](https://docs.microsoft.com/azure/search/search-security-api-keys)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Azure Active Directory (Azure AD) zawiera dzienniki ułatwiające wykrywanie starych kont. Za pomocą przeglądów tożsamości i dostępu w usłudze Azure AD można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp. 

Przegląd dzienników diagnostycznych z Wyszukiwanie poznawcze dla działania w punkcie końcowym usługi wyszukiwania, takich jak zarządzanie indeksami, populacja indeksu i zapytania.

- [Informacje o raportowaniu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

- [Monitoruj operacje i działania Wyszukiwanie poznawcze platformy Azure](https://docs.microsoft.com/azure/search/search-monitor-usage)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: usługa dostęp do usługi Azure Active Directory (Azure AD) operacje logowania, Inspekcja i źródła dzienników zdarzeń o podwyższonym ryzyku umożliwiają integrację z dowolnym narzędziem Siem lub monitorowaniem.

Usprawnij ten proces, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics. Skonfiguruj żądane alerty w obszarze roboczym Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics) 

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: Użyj funkcji ochrony tożsamości Azure Active Directory (Azure AD), aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. W razie potrzeby Pozyskaj dane do badania na platformie Azure.

- [Jak wyświetlić ryzykowne logowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

- [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies) 

- [Jak dołączyć wskaźnik na platformie Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard) 

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. Skrytka klienta nie obsługuje Wyszukiwanie poznawcze.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Azure Security test: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie oddzielnych subskrypcji i/lub grup zarządzania na potrzeby tworzenia, testowania i produkcji. Zasoby powinny być oddzielone przez sieć wirtualną/podsieć, odpowiednio otagowane i zabezpieczone w ramach sieciowej grupy zabezpieczeń lub zapory platformy Azure. Zasoby przechowujące lub przetwarzające dane poufne powinny być izolowane. Użyj linku prywatnego, aby skonfigurować prywatny punkt końcowy do Wyszukiwanie poznawcze.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription) 

- [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Jak utworzyć prywatny punkt końcowy dla Wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/service-create-private-endpoint)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: Użyj rozwiązania innej firmy z witryny Azure Marketplace w obszarze sieci, aby monitorować nieautoryzowany transfer informacji poufnych i blokować takie transfery podczas powiadamiania specjalistów ds. bezpieczeństwa informacji.

Firma Microsoft zarządza podstawową platformą i traktuje całą zawartość klienta jako poufną i chroni przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Zrozumienie ochrony danych klientów na platformie Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data) 

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: wyszukiwanie poznawcze szyfruje dane podczas przesyłania z Transport Layer Security 1,2 i wymusza szyfrowanie (SSL/TLS) przez cały czas dla wszystkich połączeń. Gwarantuje to, że wszystkie dane są szyfrowane "podczas przesyłania" między klientem a usługą.

- [Informacje o szyfrowaniu podczas przesyłania na platformę Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit) 

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla wyszukiwanie poznawcze. Zaimplementuj rozwiązanie innych firm, jeśli jest to niezbędne do celów zgodności. 

Firma Microsoft zarządza podstawową platformą i traktuje całą zawartość klienta jako poufną i chroni przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Zrozumienie ochrony danych klientów na platformie Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Korzystaj z usługi Azure RBAC, aby zarządzać dostępem do zasobów

**Wskazówki**: w celu administrowania usługą Użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby zarządzać dostępem do kluczy i konfiguracji. W przypadku operacji związanych z zawartością, takich jak indeksowanie i kwerendy, Wyszukiwanie poznawcze używa kluczy zamiast modelu kontroli dostępu opartego na tożsamości. Użyj funkcji RBAC platformy Azure, aby kontrolować dostęp do kluczy.
- [Jak skonfigurować RBAC na platformie Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 

 
- [Jak używać ról na potrzeby dostępu administracyjnego do Wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/search-security-rbac)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. Te wytyczne są przeznaczone dla zasobów obliczeniowych. 

Firma Microsoft zarządza podstawową infrastrukturą dla Wyszukiwanie poznawcze i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

- [Zrozumienie ochrony danych klientów na platformie Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: wyszukiwanie poznawcze automatycznie szyfruje zawartość indeksowaną przy użyciu kluczy zarządzanych przez firmę Microsoft. Jeśli jest wymagana większa ochrona, można uzupełnić domyślne szyfrowanie z użyciem drugiej warstwy szyfrowania przy użyciu kluczy tworzonych i zarządzanych w Azure Key Vault.

- [Konfigurowanie kluczy zarządzanych przez klienta na potrzeby szyfrowania danych w usłudze Azure Wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)

- [Informacje o szyfrowaniu na platformie Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: udostępnione

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dla sytuacji, w których zmiany są wprowadzane do wystąpień produkcyjnych wyszukiwanie poznawcze i innych krytycznych lub powiązanych zasobów.

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

- [Jak utworzyć alerty dla działań Wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/search-monitor-logs)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie lukami w zabezpieczeniach](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: aktualnie niedostępne do wyszukiwanie poznawcze.  W przypadku klastrów przechowujących zawartość usługi Search firma Microsoft jest odpowiedzialna za zarządzanie lukami w zabezpieczeniach tych klastrów.

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. To zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Wdróż automatyczne rozwiązanie do zarządzania poprawkami dla tytułów oprogramowania innych firm

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. To zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. Firma Microsoft przeprowadza zarządzanie lukami w systemach podstawowych, które obsługują usługi Wyszukiwanie poznawcze.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. W przypadku wyników skanowania w poszukiwaniu luk w zabezpieczeniach nie ma żadnego standardowej oceny ryzyka ani systemu oceniania.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: usługa Azure Resource Graph umożliwia wykonywanie zapytań dotyczących wszystkich zasobów (np. obliczeń, magazynu, sieci, portów, protokołów itd.) w Twoich subskrypcjach i odnajdywanie ich.  

Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i Wylicz wszystkie subskrypcje platformy Azure oraz zasoby w Twoich subskrypcjach.  

- [Jak tworzyć zapytania za pomocą Eksploratora Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal) 

- [Jak wyświetlić subskrypcje platformy Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0) 

- [Opis kontroli RBAC platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: Zastosuj Tagi do zasobów platformy Azure za pomocą metadanych, aby logicznie zorganizować je w taksonomię.

- [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji w miarę potrzeb w celu organizowania i śledzenia zasobów. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.
- [Jak utworzyć dodatkowe subskrypcje platformy Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription) 

- [Jak utworzyć Grupy zarządzania](https://docs.microsoft.com/azure/governance/management-groups/create) 

- [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) 

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i obsługa spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: zdefiniuj listę zatwierdzonych zasobów platformy Azure związanych z indeksowaniem i przetwarzaniem zestawu umiejętności w wyszukiwanie poznawcze.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: zaleca się zdefiniowanie spisu zasobów platformy Azure, które zostały zatwierdzone do użycia zgodnie z zasadami organizacji i ze standardami, a następnie Monitoruj dla niezatwierdzonych zasobów platformy Azure za pomocą Azure Policy lub grafu zasobów platformy Azure.

- [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage) 

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal) 

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. Te wskazówki są przeznaczone do zasobów obliczeniowych.

Zalecane jest posiadanie spisu aplikacji, które zostały uznane za zatwierdzone zgodnie z zasadami organizacji i standardami zabezpieczeń, oraz monitorują wszystkie niezatwierdzone tytuły oprogramowania zainstalowane w zasobach obliczeniowych platformy Azure.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. To zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. Nie uwidacznia żadnych zasobów obliczeniowych ani nie zezwala na instalację aplikacji na żadnym z zasobów.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Użyj grafu zasobów platformy Azure do wykonywania zapytań lub odnajdywania zasobów w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

- [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage) 

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types) 

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: przechowywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. To zalecenie jest przeznaczone dla aplikacji uruchamianych na zasobach obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: w celu zarządzania usługami Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość współpracy użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management". 

Kontroluj dostęp do kluczy używanych do uwierzytelniania żądań dla wszystkich innych operacji, szczególnie tych związanych z zawartością z Wyszukiwanie poznawcze.

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. To zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. To zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. Search", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji zasobów wyszukiwanie poznawcze platformy Azure. Możesz również użyć wbudowanych Azure Policy definicji dla usług Wyszukiwanie poznawcze, takich jak:

- Włącz rejestrowanie inspekcji dla zasobów platformy Azure

Azure Resource Manager ma możliwość eksportowania szablonu w JavaScript Object Notation (JSON), który powinien zostać sprawdzony w celu upewnienia się, że konfiguracje spełniają wymagania dotyczące zabezpieczeń Twojej organizacji. 

Możesz również użyć zaleceń z Azure Security Center jako bezpiecznej konfiguracji odniesienia dla zasobów platformy Azure. 

- [Azure Policy kontroli zgodności z przepisami dla Wyszukiwanie poznawcze platformy Azure](https://docs.microsoft.com/azure/search/security-controls-policy)

- [Jak wyświetlić dostępne aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. To zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje] efektów, aby wymusić bezpieczne ustawienia dla zasobów usługi Wyszukiwanie poznawcze. 

Szablony Azure Resource Manager mogą służyć do obsługi konfiguracji zabezpieczeń zasobów platformy Azure wymaganych przez organizację. 

- [Zrozumienie efektów Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [Azure Policy kontroli zgodności z przepisami dla Wyszukiwanie poznawcze platformy Azure](https://docs.microsoft.com/azure/search/security-controls-policy)

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Przegląd szablonów Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. To zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji Azure Policy, Użyj usługi Azure DevOps lub Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

- [Jak przechowywać kod w usłudze Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow)

- [Dokumentacja Azure Repos](https://docs.microsoft.com/azure/devops/repos/index)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. To zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów usługi Wyszukiwanie poznawcze przy użyciu Azure Policy. 

Aliasy służą do tworzenia zasad niestandardowych do inspekcji lub wymuszania konfiguracji sieci. Można również używać wbudowanych definicji zasad związanych z konkretnymi zasobami. 

Ponadto można użyć Azure Automation do wdrożenia zmian konfiguracji i zarządzania wyjątkami zasad. 

- [Azure Policy kontroli zgodności z przepisami dla Wyszukiwanie poznawcze platformy Azure](https://docs.microsoft.com/azure/search/security-controls-policy)

- [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. To zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj Security Center, aby przeprowadzić skanowanie linii bazowej zasobów usługi Wyszukiwanie poznawcze.  Ponadto należy używać Azure Policy do powiadamiania i inspekcji konfiguracji zasobów. 

- [Jak skorygować zalecenia w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

- [Azure Policy kontroli zgodności z przepisami dla Wyszukiwanie poznawcze platformy Azure](https://docs.microsoft.com/azure/search/security-controls-policy)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. To zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: Użyj tożsamości zarządzanych przez platformę Azure w połączeniu z Azure Key Vault, aby uprościć zarządzanie tajnymi aplikacjami w chmurze.
- [Jak używać tożsamości zarządzanych dla zasobów platformy Azure](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity) 
- [Jak utworzyć Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal) 

- [Jak zapewnić uwierzytelnianie Key Vault przy użyciu tożsamości zarządzanej](https://docs.microsoft.com/azure/key-vault/managed-identity) 

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: Użyj tożsamości zarządzanej platformy Azure, aby zapewnić wyszukiwanie poznawcze dostęp do innych usług platformy Azure, takich jak Key Vault i indeksatory źródeł danych przy użyciu automatycznie zarządzanej tożsamości w Azure Active Directory (Azure AD). Zarządzane tożsamości umożliwiają uwierzytelnianie w dowolnej usłudze obsługującej uwierzytelnianie w usłudze Azure AD, w tym Azure Key Vault, bez żadnych poświadczeń w kodzie. 

- [Konfigurowanie połączenia indeksatora ze źródłem danych przy użyciu tożsamości zarządzanej](https://docs.microsoft.com/azure/search/search-howto-managed-identities-data-sources)

- [Konfigurowanie kluczy zarządzanych przez klienta na potrzeby szyfrowania danych przy użyciu tożsamości zarządzanej](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#3---create-a-service-identity)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. Nie hostuje kodu i nie ma żadnych poświadczeń do zidentyfikowania.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Obrona złośliwego oprogramowania](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym kodem

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. To zalecenie jest przeznaczone do zasobów obliczeniowych.

Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Azure Wyszukiwanie poznawcze), ale nie jest uruchamiane na zawartość klienta.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: wstępnie Przeskanuj zawartość przekazywaną do zasobów platformy Azure, które nie są obliczeniowe, takich jak Wyszukiwanie poznawcze, Blob Storage, Azure SQL Database itd. 

Ponosisz odpowiedzialność za wstępne skanowanie zawartości przekazywanej do zasobów platformy Azure, które nie są obliczeniowe. Firma Microsoft nie może uzyskać dostępu do danych klienta i w związku z tym nie może przeprowadzić skanowania zawartości klienta przez oprogramowanie chroniące przed złośliwym kodem.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: nie mają zastosowania do wyszukiwanie poznawcze. Nie zezwala na instalację rozwiązań chroniących przed złośliwym oprogramowaniem. W przypadku podstawowej platformy firma Microsoft obsługuje aktualizowanie wszelkich programów chroniących przed złośliwym oprogramowaniem i podpisów. 

W przypadku wszelkich zasobów obliczeniowych należących do organizacji i używanych w rozwiązaniu wyszukiwania postępuj zgodnie z zaleceniami w Security Center, aplikacje obliczeniowe, &amp; Aby upewnić się, że wszystkie punkty końcowe są aktualne przy użyciu najnowszych sygnatur. W przypadku systemu Linux Użyj rozwiązania chroniącego przed złośliwym oprogramowaniem innej firmy.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: odzyskiwanie danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: nie można utworzyć kopii zapasowej zawartości przechowywanej w usłudze wyszukiwania za pośrednictwem Azure Backup lub innego wbudowanego mechanizmu, ale można skompilować indeks z kodu źródłowego aplikacji i podstawowych źródeł danych albo utworzyć niestandardowe narzędzie do pobierania i przechowywania indeksowanej zawartości.

- [Przykład — Przywracanie kopii zapasowej w usłudze GitHub](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/index-backup-restore)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: wyszukiwanie poznawcze obecnie nie obsługuje zautomatyzowanej kopii zapasowej danych w usłudze wyszukiwania i należy wykonać kopię zapasową w procesie ręcznym.  Można również utworzyć kopię zapasową kluczy zarządzanych przez klienta w Azure Key Vault. 

- [Tworzenie kopii zapasowej i przywracanie indeksu Wyszukiwanie poznawcze platformy Azure](https://docs.microsoft.com/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Jak utworzyć kopię zapasową kluczy Key Vault na platformie Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: wyszukiwanie poznawcze obecnie nie obsługuje zautomatyzowanej kopii zapasowej danych w usłudze wyszukiwania i należy wykonać kopię zapasową i przywrócić ją w procesie ręcznym.  Okresowo wykonuj przywracanie danych kopii zapasowej ręcznie, aby zapewnić kompleksową integralność procesu tworzenia kopii zapasowej.

- [Tworzenie kopii zapasowej i przywracanie indeksu Wyszukiwanie poznawcze platformy Azure](https://docs.microsoft.com/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Jak przywrócić klucze Key Vault na platformie Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: obecnie wyszukiwanie poznawcze nie obsługuje zautomatyzowanej kopii zapasowej danych w usłudze wyszukiwania i należy wykonać kopię zapasową w procesie ręcznym.  Można również utworzyć kopię zapasową kluczy zarządzanych przez klienta w Azure Key Vault. 

Włącz trwałe usuwanie i przeczyszczanie ochrony w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem. Jeśli usługa Azure Storage jest używana do przechowywania ręcznych kopii zapasowych, należy włączyć opcję usuwania nietrwałego, aby zapisywać i odzyskiwać dane po usunięciu obiektów blob lub migawek obiektów BLOB. 

- [Tworzenie kopii zapasowej i przywracanie indeksu Wyszukiwanie poznawcze platformy Azure](https://docs.microsoft.com/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Jak włączyć funkcję usuwania nietrwałego i przeczyszczania w programie Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Usuwanie nietrwałe dla magazynu obiektów blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: odpowiedź na zdarzenia](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Tworzenie przewodnika odpowiedzi na zdarzenia dla organizacji. Upewnij się, że istnieją jednokierunkowe plany reagowania na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń i zarządzania z wykrywania na potrzeby przeglądu po zdarzeniu.

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Klient może również korzystać z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznie używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które spowodowało wygenerowanie alertu.

Ponadto należy oznaczyć subskrypcje przy użyciu tagów i utworzyć system nazewnictwa, aby identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają poufne dane. Odpowiedzialność za korygowanie alertów zależy od zagrożenia dla zasobów platformy Azure i środowiska, w którym wystąpiło zdarzenie.

- [Organizowanie zasobów platformy Azure przy użyciu tagów](https://docs.microsoft.com/azure/azure-resource-manager/management/tag-resources)

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy i popraw plan zgodnie z wymaganiami.

- [Zapoznaj się z publikacją NIST, "przewodnikiem dotyczącym testowania, uczenia i ćwiczeń programów dla planów i możliwości IT".](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły pozwala na ręczne eksportowanie alertów i zaleceń. Możesz użyć łącznika danych Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

- [Jak skonfigurować eksport ciągły](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowego](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w programie Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz test [porównawczy zabezpieczeń platformy Azure: testy penetracji i czerwone ćwiczenia zespołu](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: Postępuj zgodnie z regułami testowania penetracji Microsoft Cloud, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj z strategii firmy Microsoft i wykonywania testów na żywo z obsługą tworzenia zespołu, usług i aplikacji w chmurze, które są zarządzane przez firmę Microsoft.
- [Reguły testowania penetracji zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)
- [Microsoft Cloud Tworzenie zespołu Red.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
