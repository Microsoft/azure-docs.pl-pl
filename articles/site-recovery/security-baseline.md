---
title: Podstawa zabezpieczeń platformy Azure dla Site Recovery
description: Linia bazowa zabezpieczeń Site Recovery zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c3e2245ae47504e058921c532ed40a220e95423d
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197578"
---
# <a name="azure-security-baseline-for-site-recovery"></a>Podstawa zabezpieczeń platformy Azure dla Site Recovery

Ta linia bazowa zabezpieczeń stosuje wskazówki z programu [Azure Security test w wersji 1,0](../security/benchmarks/overview-v1.md) do Site Recovery. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące Site Recovery. **Kontrolki** nie mają zastosowania do Site Recovery zostały wykluczone. 

Aby dowiedzieć się, jak Site Recovery całkowicie mapować do testu porównawczego zabezpieczeń platformy Azure, zobacz [pełny Site Recovery pliku mapowania linii bazowej zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: Microsoft Azure Site Recovery nie obsługuje wdrażania w Virtual Network platformy Azure. Skonfiguruj usługę Site Recovery przy użyciu prywatnego punktu końcowego platformy Azure, aby wymusić bezpieczną komunikację w sieci.

- [Obsługa linków prywatnych Azure Site Recovery](azure-to-azure-how-to-enable-replication-private-endpoints.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: Usługa Site Recovery obsługuje Tagi usług, które umożliwiają klientom otwieranie ruchu tylko do określonych usług i portów. Klienci muszą zezwolić na dostęp wychodzący do usługi Site Recovery, aby mieć tag usługi "AzureSiteRecovery" w zaporze lub sieciowej grupie zabezpieczeń.

- [Łączność wychodząca przy użyciu tagów usługi](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Użyj tagów zasobów dla sieciowych grup zabezpieczeń i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. Dla poszczególnych reguł grup zabezpieczeń sieci Użyj pola "opis", aby udokumentować reguły zezwalające na ruch do i z sieci. 

Dołączanie dowolnych wbudowanych definicji Azure Policy związanych z tagowaniem, takich jak "Wymagaj tagu i jego wartości", aby mieć pewność, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o istniejących nieoznakowanych zasobach. 

Możesz użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów. 

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md) 

- [Jak utworzyć Virtual Network platformy Azure](../virtual-network/quick-create-portal.md) 

- [Jak filtrować ruch sieciowy przy użyciu reguł sieciowej grupy zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: monitorowanie wszelkich zmian w konfiguracjach zasobów sieciowych związanych z usługą Site Recovery przy użyciu dzienników aktywności platformy Azure. Utwórz alerty w Azure Monitor, aby powiadomić Cię o zmianie krytycznych zasobów sieciowych Site Recovery.

- [Wyświetlanie i pobieranie zdarzeń dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Tworzenie i wyświetlanie alertów dziennika aktywności oraz zarządzanie nimi za pomocą Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: Włączanie ustawień diagnostycznych dziennika aktywności platformy Azure na potrzeby rejestrowania inspekcji i wysyłania dzienników do obszaru roboczego log Analytics, konta usługi Azure Storage lub centrum zdarzeń platformy Azure w celu archiwizacji.

Użyj danych dziennika aktywności platformy Azure, aby określić "co, kto i kiedy" w przypadku operacji zapisu (PUT, POST, DELETE) wykonanych dla zasobów platformy Azure.

Pozyskiwanie Site Recovery dzienników w Azure Monitor do agregowania wygenerowanych danych zabezpieczeń. W Azure Monitor należy używać Log Analytics obszarów roboczych do wykonywania zapytań i wykonywania analiz oraz używania kont magazynu do przechowywania długoterminowego lub archiwizowania. Ponadto możesz włączyć i uzyskać dostęp do danych na platformie Azure, w tym rozwiązania do zarządzania zdarzeniami i zdarzeń związanych z zabezpieczeniami innych firm (SIEM).

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md)

- [Monitorowanie usługi Site Recovery przy użyciu dzienników usługi Azure Monitor](monitor-log-analytics.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Włączanie ustawień diagnostycznych dziennika aktywności platformy Azure na potrzeby rejestrowania inspekcji i wysyłania dzienników do obszaru roboczego log Analytics, konta usługi Azure Storage lub do centrum zdarzeń platformy Azure w celu archiwizacji. 

Użyj danych dziennika aktywności platformy Azure, aby określić "co, kto i kiedy" w przypadku operacji zapisu (PUT, POST, DELETE) wykonanych dla zasobów platformy Azure.

Pozyskiwanie dzienników Site Recovery z Azure Monitor do agregowania wygenerowanych danych zabezpieczeń. W Azure Monitor należy używać Log Analytics obszarów roboczych do wykonywania zapytań i wykonywania analiz oraz używania kont magazynu do przechowywania długoterminowego/archiwizowania. Włączaj i dołączaj dane do usługi Azure wskaźnikowej lub rozwiązania do zarządzania zdarzeniami (SIEM) innych firm.

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md)

- [Monitorowanie usługi Site Recovery przy użyciu dzienników usługi Azure Monitor](monitor-log-analytics.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: Ustawianie okresu przechowywania dzienników dla log Analytics obszarów roboczych skojarzonych z magazynami Recovery Services platformy Azure przy użyciu Azure monitor zgodnie z przepisami obowiązującymi w organizacji. 

- [Jak ustawić parametry przechowywania dziennika](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: Włączanie ustawień diagnostycznych dziennika aktywności platformy Azure i wysyłanie dzienników do obszaru roboczego log Analytics. 

Wykonuj zapytania w Log Analytics, aby wyszukiwać terminy, identyfikować trendy, analizować wzorce i uzyskiwać wgląd w dane dziennika aktywności zebrane z magazynów Recovery Services.

- [Monitorowanie usługi Site Recovery](site-recovery-monitor-and-troubleshoot.md)

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md)

- [Jak zbierać i analizować dzienniki aktywności platformy Azure w obszarze roboczym Log Analytics w Azure Monitor](../azure-monitor/platform/activity-log.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: monitorowanie maszyn replikowanych przez Azure Site Recovery przy użyciu dzienników Azure Monitor i log Analytics. Użyj Log Analytics w Azure Monitor, aby pisać i testować zapytania dzienników oraz interaktywnie analizować dane dziennika. Azure Monitor zbiera dzienniki aktywności i zasobów oraz inne dane monitorowania. 

Wizualizuj i badaj wyniki dzienników oraz Konfiguruj alerty w celu podejmowania działań na podstawie monitorowanych danych. Skonfiguruj alerty w obszarze roboczym Log Analytics na platformie Azure, ponieważ udostępniają one rozwiązanie zautomatyzowanej reakcji (o) aranżacji zabezpieczeń. Pozwala to na automatyczne rozwiązania, takie jak elementy PlayBook, które mają być tworzone i używane do korygowania problemów z zabezpieczeniami. Utwórz niestandardowe alerty dzienników w obszarze roboczym Log Analytics przy użyciu Azure Monitor. 

- [Monitorowanie usługi Site Recovery](site-recovery-monitor-and-troubleshoot.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

- [Tworzenie i wyświetlanie alertów dzienników oraz zarządzanie nimi za pomocą Azure Monitor](../azure-monitor/platform/alerts-log.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz informacje o [teście zabezpieczeń Azure: Identity i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: domyślnie nie są przypisane żadne role. Muszą być one jawnie przypisane w zależności od potrzeb firmy. Wszystkie przypisania ról można sprawdzić za pomocą interfejsu wiersza polecenia programu PowerShell lub Azure Active Directory (Azure AD) w celu odnalezienia kont należących do grup administracyjnych.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Użyj funkcji zarządzania tożsamościami i dostępem Security Center, aby monitorować liczbę kont administracyjnych.

Ponadto, aby ułatwić śledzenie dedykowanych kont administracyjnych, należy skorzystać z rekomendacji Security Center lub wbudowanych zasad platformy Azure, takich jak: 

- Do subskrypcji powinien być przypisany więcej niż jeden właściciel 
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji 

- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

Utwórz proces śledzenia tożsamości i kontroli dostępu dla kont administracyjnych i okresowo sprawdzaj.

- [Jak używać Azure Security Center do monitorowania tożsamości i dostępu](../security-center/security-center-identity-access.md)

- [Jak używać Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: Użyj rejestracji aplikacji platformy Azure z jednostką usługi, aby pobrać token, który ma być używany do współpracy z magazynami Recovery Services za pomocą wywołań interfejsu API.

- [Jak wywołać interfejsy API REST platformy Azure](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Jak zarejestrować aplikację kliencką (nazwę główną usługi) w usłudze Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Informacje o interfejsie API usługi Azure Recovery Services](/rest/api/recoveryservices)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: Włączanie usługi Azure AD, uwierzytelniania wieloskładnikowego i postępuj zgodnie z zaleceniami Security Center i dostępu do nich. 
- [Planowanie wdrożenia usługi Azure AD Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md)

- [Monitorowanie tożsamość i dostępu](../security-center/security-center-identity-access.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: używanie bezpiecznej, zarządzanej przez platformę Azure stacji roboczej (znanej także jako stacja robocza dostępu uprzywilejowanego (dostępem uprzywilejowanym)) z uwierzytelnianiem wieloskładnikowym platformy Azure na potrzeby zadań administracyjnych i wykonywania uprzywilejowanych akcji na Site Recovery zasobach.

- [Stacje robocze z dostępem uprzywilejowanym](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Planowanie wdrożenia usługi Azure AD Multi-Factor Authentication opartej na chmurze](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: użyj funkcji PRIVILEGED Identity Management (PIM) usługi Azure AD w celu generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku.
Wyświetlanie alertów i raportów na temat ryzykownego zachowania użytkowników przy użyciu funkcji wykrywania ryzyka usługi Azure AD.

- [Jak wdrożyć Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Omówienie wykrywania ryzyka usługi Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: Zarządzaj zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby zezwolić na dostęp do Azure Portal tylko z określonych logicznych grup zakresów, regionów lub krajów adresów IP.
- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj usługi Azure AD jako centralnego systemu uwierzytelniania i autoryzacji dla Site Recovery. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych w czasie, w tranzycie, a także Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika. 

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: korzystanie z dzienników usługi Azure AD w celu ułatwienia wykrywania starych kont. 

Wydajnie Zarządzaj członkostwem w grupach, dostępem do aplikacji i ról przedsiębiorstwa przy użyciu tożsamości i dostępu do usługi Azure AD. 

Utwórz proces, aby regularnie przeglądać dostęp użytkowników, aby upewnić się, że tylko użytkownicy z zakończonymi przeglądami dostępu mają stały dostęp. 

- [Informacje o raportowaniu usługi Azure AD](../active-directory/reports-monitoring/index.yml)

- [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: Użyj usługi Azure AD jako centralnego systemu uwierzytelniania i autoryzacji dla Site Recovery zasobów. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych, a także Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

Masz dostęp do źródeł zdarzeń związanych z logowaniem do usługi Azure AD, inspekcją i ryzykiem związanym z dziennikami, które umożliwiają integrację z platformą Azure Marketplace lub dowolnym narzędziem SIEM lub monitorowania dostępnym w witrynie Azure Portal.

Bardziej Usprawnij ten proces, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając dzienniki inspekcji i logowania do obszaru roboczego Log Analytics. Żądane alerty można skonfigurować w obszarze roboczym Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Jak przejść do tablicy wskaźnikowej platformy Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: Użyj usługi Azure AD jako centralnego systemu uwierzytelniania i autoryzacji dla magazynów Recovery Services. 

Korzystanie z funkcji ochrony tożsamości usługi Azure AD na potrzeby wykrywania zachowań logowania na koncie oraz konfigurowania automatycznych odpowiedzi na wykryte podejrzane działania w odniesieniu do tożsamości użytkowników. Pozyskanie danych na platformie Azure — wskaźnik kontroli.

- [Jak wyświetlić ryzykowne logowanie do usługi Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie oddzielnych subskrypcji lub grup zarządzania na potrzeby tworzenia, testowania i produkcji Recovery Services magazynów. Oddziel zasoby z siecią wirtualną lub podsiecią, odpowiednio otagowane i zabezpieczone przez sieciową grupę zabezpieczeń lub zaporę platformy Azure. 

Wyłącz maszyny wirtualne, które przechowują lub przetwarzają dane poufne, gdy nie są używane. Zaimplementuj zasady i procedury, aby wykonać ten proces cykliczny. 

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Omówienie Site Recovery](site-recovery-overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: korzystanie z prywatnego linku lub prywatnego punktu końcowego, sieciowych grup zabezpieczeń oraz tagów usług w celu ograniczenia możliwości eksfiltracji danych z maszyn wirtualnych z włączonymi Site Recovery.

Firma Microsoft zarządza podstawową platformą używaną przez Site Recovery i traktuje całą zawartość klienta jako poufną i chroniącą przed utratą i narażeniem danych przez klienta. Firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych w celu zapewnienia bezpieczeństwa danych klienta na platformie Azure. 

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

- [Replikowanie maszyn wirtualnych ze prywatnymi punktami końcowymi platformy Azure](azure-to-azure-how-to-enable-replication-private-endpoints.md)

- [Replikowanie maszyn wirtualnych za pomocą tagów usługi Azure Site Recovery](azure-to-azure-about-networking.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: Site Recovery używa bezpiecznego kanału https, zaszyfrowanego przy użyciu Advanced Encryption Standard (AES 256) z serwerów obciążenia platformy Azure Site Recovery do usług hostowanych za magazynem Recovery Services.

Bieżące wersje protokołu TLS obsługiwane przez Site Recovery to TLS 1,0, TLS 1,1, TLS 1,2 w regionach, które znajdowały się na żywo przed końcem 2019. TLS 1.2 to jedyna obsługiwana wersja protokołu TLS dla nowych regionów.

- [Informacje o szyfrowaniu podczas przesyłania Azure Site Recovery](physical-azure-set-up-source.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla Site Recovery. 

Wdrożenie rozwiązania innej firmy w razie potrzeby w celu zapewnienia zgodności.

Firma Microsoft zarządza podstawową platformą używaną przez Site Recovery i traktuje całą zawartość klienta jako poufną i chroniącą przed utratą i narażeniem danych przez klienta. Wdrożono i utrzymujemy pakiet niezawodnych kontroli i możliwości ochrony danych w celu zapewnienia bezpieczeństwa danych klienta na platformie Azure. 

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Korzystaj z usługi Azure RBAC, aby zarządzać dostępem do zasobów

**Wskazówki**: Użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby zarządzać dostępem do danych i zasobów związanych z zasobami Site Recovery. 

Oddziel obowiązki służbowe za pomocą funkcji RBAC platformy Azure i Udziel odpowiedniego dostępu do nich. Wbudowane role Site Recovery służą do kontrolowania operacji zarządzania Site Recovery.

- [Jak skonfigurować usługę Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Zarządzanie Azure Site Recovery za pomocą Access Control Role-Based](site-recovery-role-based-linked-access-control.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: Włącz podwójne szyfrowanie przy użyciu zarówno platformy, jak i kluczy zarządzanych przez klienta. Ta funkcja jest dostępna w Site Recovery. 

Site Recovery obsługuje szyfrowanie dla danych. W przypadku obciążeń usługi Azure IaaS dane są szyfrowane przy użyciu szyfrowanie usługi Storage (SSE). 

Tylko klient ma dostęp do klucza szyfrowania podczas korzystania z magazynu Recovery Services szyfrowanego z kluczem zarządzanym przez klienta. Firma Microsoft nigdy nie utrzymuje kopii, nie ma dostępu do klucza i nie odszyfrowuje danych transferowanych z lokalizacji podstawowej do odzyskiwania po awarii w dowolnym momencie. 

- [Obsługa kluczy zarządzanych przez klienta Azure Site Recovery](azure-to-azure-how-to-enable-replication-cmk-disks.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikami aktywności platformy Azure, aby utworzyć alerty w przypadku wprowadzenia zmian do zasobów o znaczeniu krytycznym. Te zasoby mogą obejmować wystąpienia produkcyjne Recovery Services magazynów, zasoby usługi Site Recovery i powiązane zasoby.
- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: usługa Azure Resource Graph umożliwia wykonywanie zapytań lub odnajdywanie wszystkich zasobów, w tym Recovery Services magazynów, w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia do odczytu w dzierżawie i Wylicz wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

Mimo że klasyczne zasoby platformy Azure mogą zostać odnalezione za pośrednictwem grafu zasobów, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów do przodu.

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: Zastosuj znaczniki do magazynów Recovery Services i innych powiązanych zasobów używanych przez Site Recovery z metadanymi, aby logicznie zorganizować je w taksonomię.
- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie jest to konieczne, do organizowania i śledzenia Site Recovery (magazyny Recovery Services) i innych powiązanych zasobów. 

Ponadto należy używać Azure Policy do umieszczania ograniczeń dotyczących typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad: 

- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i obsługa spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: Tworzenie spisu zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania do zasobów obliczeniowych na podstawie wymagań organizacyjnych klienta.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad: 

- Niedozwolone typy zasobów 
- Dozwolone typy zasobów

Użyj grafu zasobów platformy Azure, aby wysyłać zapytania o zasoby w ramach subskrypcji i odnajdywać je.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów 
- Dozwolone typy zasobów

Poznanie sposobu tworzenia zasad i zarządzania nimi na platformie Azure jest ważne w celu zachowania zgodności ze standardami firmy i umowami dotyczącymi poziomu usług.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/index.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management". Może to uniemożliwić tworzenie i wprowadzanie zmian w zasobach w środowisku o wysokim poziomie zabezpieczeń.

- [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla magazynu Recovery Services przy użyciu Azure Policy. 

Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. RecoveryServices", aby utworzyć zasady niestandardowe do inspekcji lub wymusić konfigurację zasobów magazynu Recovery Services usługi Site Recovery.
- [Jak wyświetlić dostępne aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje] efektów, aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.
- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: wybierz Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim, jeśli używasz niestandardowych definicji Azure Policy dla magazynów Recovery Services i powiązanych zasobów.

- [Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Dokumentacja Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Użyj wbudowanych definicji Azure Policy, a także aliasów Azure Policy w przestrzeni nazw "Microsoft. RecoveryServices", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. 

Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj wbudowanych definicji Azure Policy, a także aliasów Azure Policy w przestrzeni nazw "Microsoft. RecoveryServices", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. 

Użyj Azure Policy [Audit], [Odmów] i [Wdróż, jeśli nie istnieje] powoduje automatyczne wymuszanie konfiguracji zasobów platformy Azure.
- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: klient powinien zarządzać kluczami tajnymi Site Recovery zintegrowanymi z usługą Azure Key magazynu, jednocześnie umożliwiając odzyskiwanie po awarii dla maszyn wirtualnych z włączoną obsługą Azure Disk Encryption. 

- [Jak utworzyć magazyn kluczy](../key-vault/general/quick-create-portal.md)

- [Jak uwierzytelnić się w magazynie kluczy](../key-vault/general/authentication.md)

- [Jak przypisać zasady dostępu do magazynu kluczy](../key-vault/general/assign-access-policy-portal.md)

- [Jak włączyć funkcję DR dla maszyn wirtualnych z obsługą Azure Disk Encryption przy użyciu Site Recovery](azure-to-azure-how-to-enable-replication-ade-vms.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: Site Recovery obsługuje tożsamość zarządzaną przez system tylko wtedy, gdy klient może włączyć tożsamość zarządzaną przez system w magazynie Recovery Services. Ta sama metodologia ma zastosowanie do zasobów używanych w ofercie odzyskiwania po awarii w celu zdefiniowania granicy dostępu. 

Użyj tożsamości zarządzanych, aby zapewnić usługom platformy Azure automatyczną tożsamość zarządzaną w usłudze Azure AD. 

Zarządzane tożsamości umożliwiają uwierzytelnianie w dowolnej usłudze obsługującej uwierzytelnianie w usłudze Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie.

- [Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md?tabs=core2x)

- [Jak włączyć tożsamość zarządzaną przez system w magazynie Recovery Services](azure-to-azure-how-to-enable-replication-private-endpoints.md#enable-the-managed-identity-for-the-vault)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład Site Recovery), ale nie jest ono uruchamiane w Twojej zawartości. Skanuj wstępnie wszystkie pliki przesyłane do zasobów platformy Azure, które nie są obliczeniowe, takie jak App Service, Data Lake Storage i Blob Storage.

Użyj wykrywania zagrożeń Security Center dla usług danych w celu wykrywania złośliwego oprogramowania przekazanego do kont magazynu.

- [Informacje na temat ochrony przed złośliwym oprogramowaniem firmy Microsoft Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md)

- [Zrozumienie wykrywania zagrożeń Azure Security Center dla usług danych](../security-center/azure-defender.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: Site Recovery wewnętrznie korzysta z konta usługi Azure Storage w celu utrzymania stanu rozwiązania do odzyskiwania po awarii, zgodnie z konfiguracją klientów w swoich obciążeniach.

Wszystkie zasoby magazynu używane przez metadane usług Site Recovery Services z konfiguracją typu: dostęp z dostępem do odczytu do magazynu geograficznie nadmiarowego (RA-GRS). Konta magazynu o typie powyżej GRS (na przykład RAGRS, RAG-ZRS) mogą replikować dane do regionu pomocniczego (setki kilometrów od lokalizacji głównej danych źródłowych), aby nadal obejść odzyskiwanie po awarii dla klientów w trakcie awarii.

Jest to poza zakresem klienta i Site Recovery zespół zajmuje się nim wewnętrznie. Klient może tworzyć kopie zapasowe kluczy Key Vault na platformie Azure.

- [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: okresowe testowanie przywracania kluczy zarządzanych przez klienta.

- [Jak przywrócić klucze magazynu kluczy na platformie Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: dane są szyfrowane na poziomie przy użyciu szyfrowanie usługi Storage (SSE) za pomocą Virtual Machines opartego na infrastrukturze platformy Azure jako usługi (IaaS). Włącz nietrwałe usuwanie w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

- [Jak włączyć usuwanie nietrwałe w Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. 

Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także fazy obsługi zdarzeń lub zarządzania z wykrywania do oceny po zdarzeniu.

- [Jak skonfigurować automatyzację przepływu pracy w programie Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Klient może również korzystać z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Określanie priorytetu, które alerty powinny być sprawdzane jako pierwsze w oparciu o ważność przypisanych Security Center. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu.

Oznacz subskrypcje jasno (na przykład produkcyjne, nieprodukcyjne) i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md) 

- [Organizowanie zasobów platformy Azure przy użyciu tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy i popraw plan zgodnie z wymaganiami

- [Zapoznaj się z przewodnikiem po publikacji NIST, aby testować, uczenie i ćwiczenie programów dla planów i możliwości IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych klienta został uzyskany przez nielegalną lub nieautoryzowaną osobę. 

Utwórz proces, aby przejrzeć zdarzenia, ogłosić wystąpienie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. 

Użyj łącznika danych Security Center, aby przesyłać strumieniowo alerty do usługi Azure wskaźnikowego, zgodnie z wymaganiami.
- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w programie Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.
- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz test [porównawczy zabezpieczeń platformy Azure: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: Postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [W tym miejscu znajdziesz więcej informacji na temat strategii i wykonywania trójwymiarowych operacji tworzenia zespołu i testowania aplikacji na żywo w witrynie Microsoft.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)