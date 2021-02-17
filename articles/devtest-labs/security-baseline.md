---
title: Podstawa zabezpieczeń platformy Azure dla Azure DevTest Labs
description: Podstawa zabezpieczeń platformy Azure dla Azure DevTest Labs
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: c192f881dd4a8544b9c833eece56fb3765cd07f8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582022"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Podstawa zabezpieczeń platformy Azure dla Azure DevTest Labs

Podstawą zabezpieczeń platformy Azure dla Azure DevTest Labs są zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia.

Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](../security/benchmarks/overview.md), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.

Aby uzyskać więcej informacji, zobacz [podstawy zabezpieczeń platformy Azure — omówienie](../security/benchmarks/security-baselines-overview.md).

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu
**Wskazówki:** Firma Microsoft przechowuje źródła czasu dla zasobów platformy Azure. Można jednak zarządzać ustawieniami synchronizacji czasu dla zasobów obliczeniowych.

Zobacz następujący artykuł, aby dowiedzieć się więcej o konfigurowaniu synchronizacji czasu dla zasobów obliczeniowych platformy Azure: [synchronizacja czasu dla maszyn wirtualnych z systemem Windows na platformie Azure](../virtual-machines/windows/time-sync.md). 

**Monitorowanie Azure Security Center:** Obecnie niedostępne

**Odpowiedzialność:** Programu

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń
**Wskazówki:** Włącz ustawienia diagnostyczne dziennika aktywności platformy Azure i Wyślij dzienniki do obszaru roboczego Log Analytics, centrum zdarzeń platformy Azure lub konta usługi Azure Storage w celu archiwizacji. Dzienniki aktywności zapewniają wgląd w operacje, które zostały wykonane na Azure DevTest Labs wystąpieniach na poziomie płaszczyzny zarządzania. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" w przypadku operacji zapisu (PUT, POST, DELETE) wykonanych na poziomie płaszczyzny zarządzania dla wystąpień usługi DevTest Labs.

Aby uzyskać więcej informacji, zobacz [Tworzenie ustawień diagnostycznych w celu wysyłania dzienników platformy i metryk do różnych miejsc docelowych](../azure-monitor/essentials/diagnostic-settings.md).

**Monitorowanie Azure Security Center:** Obecnie niedostępne

**Odpowiedzialność:** Dział

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure
**Wskazówki:** Włącz ustawienia diagnostyczne dziennika aktywności platformy Azure i Wyślij dzienniki do obszaru roboczego Log Analytics, centrum zdarzeń platformy Azure lub konta usługi Azure Storage w celu archiwizacji. Dzienniki aktywności zapewniają wgląd w operacje, które zostały wykonane na Azure DevTest Labs wystąpieniach na poziomie płaszczyzny zarządzania. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" dla operacji zapisu (PUT, POST, DELETE) wykonanych na poziomie płaszczyzny zarządzania dla wystąpień usługi DevTest Labs.

Aby uzyskać więcej informacji, zobacz [Tworzenie ustawień diagnostycznych w celu wysyłania dzienników platformy i metryk do różnych miejsc docelowych](../azure-monitor/essentials/diagnostic-settings.md).

**Monitorowanie Azure Security Center:** Obecnie niedostępne

**Odpowiedzialność:** Dział

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych
**Wskazówki:** Azure DevTest Labs maszyny wirtualne są tworzone i własnością klienta. W związku z tym organizacja jest odpowiedzialna za monitorowanie. Aby monitorować system operacyjny obliczeń, można użyć Azure Security Center. Dane zbierane przez Security Center z systemu operacyjnego obejmują typ i wersję systemu operacyjnego, system operacyjny (dzienniki zdarzeń systemu Windows), uruchomione procesy, nazwę komputera, adresy IP i zalogowanego użytkownika. Agent Log Analytics również zbiera pliki zrzutu awaryjnego.

Aby uzyskać więcej informacji, zobacz następujące artykuły: 

- [Jak zbierać dzienniki wewnętrznego hosta maszyny wirtualnej platformy Azure z Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)
- [Omówienie zbierania danych Azure Security Center](../security-center/security-center-enable-data-collection.md)

**Monitorowanie Azure Security Center:** Opcję

**Odpowiedzialność:** Dział

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń
***Wskazówki:** W Azure Monitor Ustaw okres przechowywania dziennika dla Log Analytics obszarów roboczych skojarzonych z wystąpieniami Azure DevTest Labs zgodnie z regulacjami zgodności w organizacji.

Aby uzyskać więcej informacji, zobacz następujący artykuł: [jak ustawić parametry przechowywania dziennika](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania
**Wskazówki:** Włącz ustawienia diagnostyczne dziennika aktywności platformy Azure i Wyślij dzienniki do obszaru roboczego Log Analytics. Uruchom zapytania w Log Analytics, aby wyszukiwać terminy, identyfikować trendy, analizować wzorce i udostępniać wiele innych szczegółowych informacji na podstawie danych dziennika aktywności, które mogły zostać zebrane dla Azure DevTest Labs.

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/essentials/diagnostic-settings.md)
- [Jak zbierać i analizować dzienniki aktywności platformy Azure w obszarze roboczym Log Analytics w Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: Włączanie alertów dla nietypowego działania
**Wskazówki:** Za pomocą obszaru roboczego usługi Azure Log Analytics można monitorować i generować alerty o nietypowych działaniach w dziennikach zabezpieczeń oraz zdarzeniach związanych z Azure DevTest Labs.

Aby uzyskać więcej informacji, zobacz następujący artykuł: [jak wysyłać alerty dotyczące danych dziennika usługi log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Monitorowanie Azure Security Center:** Obecnie niedostępne

**Odpowiedzialność:** Dział

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem
**Wskazówki:** Nie dotyczy. Azure DevTest Labs nie przetwarza ani nie tworzy dzienników związanych z oprogramowaniem chroniącym przed złośliwym kodem.

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS
**Wskazówki:** Nie dotyczy. Azure DevTest Labs nie przetwarza ani nie tworzy dzienników związanych z usługą DNS.

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia
**Wskazówki:** Azure DevTest Labs tworzy maszyny obliczeniowe platformy Azure, które są własnością klienta i są przez niego zarządzane. Użyj Microsoft Monitoring Agent na wszystkich obsługiwanych maszynach wirtualnych z systemem Windows Azure, aby zarejestrować zdarzenie tworzenia procesu i `CommandLine` pola. W przypadku obsługiwanych maszyn wirtualnych z systemem Linux na platformie Azure możesz ręcznie skonfigurować rejestrowanie konsoli dla poszczególnych węzłów i użyć dziennika systemowego do przechowywania danych. Należy również użyć obszaru roboczego Log Analytics Azure Monitor, aby przejrzeć dzienniki i uruchamiać zapytania dotyczące zarejestrowanych danych z maszyn wirtualnych platformy Azure.

- [Zbieranie danych w usłudze Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)
- [Jak uruchamiać niestandardowe zapytania w Azure Monitor](../azure-monitor/logs/get-started-queries.md)
- [Syslog data sources in Azure Monitor (Źródła danych usługi Syslog w usłudze Azure Monitor)](../azure-monitor/agents/data-sources-syslog.md)

**Monitorowanie Azure Security Center:** Opcję

**Odpowiedzialność:** Dział

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu
*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: tożsamość i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych
**Wskazówki:** Azure Active Directory (Azure AD) ma wbudowane role, które muszą zostać jawnie przypisane i są queryable. Przy użyciu modułu Azure AD PowerShell można uruchamiać zapytania ad hoc w celu odnajdywania kont należących do grup administracyjnych.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)
- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)
- [Role Azure DevTest Labs](devtest-lab-add-devtest-user.md)  

**Monitorowanie Azure Security Center:** Opcję

**Odpowiedzialność:** Dział

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie
**Wskazówki:** Azure Active Directory (Azure AD) nie ma koncepcji domyślnych haseł. Inne zasoby platformy Azure wymagające hasła wymuszają utworzenie hasła przy użyciu wymagań dotyczących złożoności i minimalnej długości hasła, które różnią się w zależności od usługi. Użytkownik jest odpowiedzialny za aplikacje innych firm i usługi Marketplace, które mogą korzystać z domyślnych haseł.

DevTest Labs nie ma koncepcji domyślnych haseł. 

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych
**Wskazówki:** Tworzenie standardowych procedur operacyjnych wokół korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

Ponadto, aby ułatwić śledzenie dedykowanych kont administracyjnych, można użyć zaleceń z Azure Security Center lub wbudowanych zasad platformy Azure, takich jak:

- Do subskrypcji powinien być przypisany więcej niż jeden właściciel
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

- [Jak używać Azure Security Center do monitorowania tożsamości i dostępu (wersja zapoznawcza)](../security-center/security-center-identity-access.md)  
- [Jak używać Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Role Azure DevTest Labs](devtest-lab-add-devtest-user.md)  

**Monitorowanie Azure Security Center:** Opcję

**Odpowiedzialność:** Dział

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory
**Wskazówki:** DevTest Labs używa usługi Azure AD do zarządzania tożsamościami. Te dwa kluczowe aspekty należy wziąć pod uwagę w przypadku udzielenia użytkownikom dostępu do środowiska w oparciu o DevTest Labs:

- **Zarządzanie zasobami:** Zapewnia ona dostęp do Azure Portal do zarządzania zasobami (tworzenia maszyn wirtualnych, tworzenia środowisk, uruchamiania, zatrzymywania, ponownego uruchamiania, usuwania i stosowania artefaktów itd.). Zarządzanie zasobami odbywa się na platformie Azure przy użyciu kontroli dostępu opartej na rolach (Azure RBAC). Przypisujesz role do użytkowników i ustawisz uprawnienia na poziomie zasobów i dostępu.
- **Maszyny wirtualne (na poziomie sieci)**: w konfiguracji domyślnej maszyny wirtualne używają konta administratora lokalnego. Jeśli istnieje dostępna domena (Azure AD Domain Services, domena lokalna lub domena oparta na chmurze), komputery można przyłączyć do domeny. W celu nawiązania połączenia z maszynami użytkownicy mogą używać ich tożsamości opartych na domenie. 

- [Architektura referencyjna dla DevTest Labs](devtest-lab-reference-architecture.md#architecture)
- [Opis logowania jednokrotnego w usłudze Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory
**Wskazówki:** Włącz usługę Azure Active Directory (AD) Multi-Factor Authentication (MFA) i postępuj zgodnie z zaleceniami Azure Security Center zarządzaniem tożsamościami i dostępem.

- [Jak włączyć usługę MFA na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)  
- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorowanie Azure Security Center:*** tak

**Odpowiedzialność:** Dział


### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych
**Wskazówki:** Użyj stacji roboczych uprzywilejowanego dostępu (dostępem uprzywilejowanym) z usługą MFA skonfigurowaną w celu logowania się i konfigurowania zasobów platformy Azure.

- [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)  
- [Jak włączyć usługę MFA na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)  

**Monitorowanie Azure Security Center:** NIE DOTYCZY

**Odpowiedzialność:** Dział

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: dziennik i alert dotyczący podejrzanego działania z kont administracyjnych
**Wskazówki:** Użyj raportów zabezpieczeń usługi Azure Active Directory (Azure AD) na potrzeby generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku. Użyj Azure Security Center, aby monitorować działania związane z tożsamościami i dostępem.

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych na skutek ryzykownego działania](../active-directory/identity-protection/overview-identity-protection.md)  
- [Jak monitorować działania użytkowników związane z tożsamościami i dostępem w usłudze Azure Security Center](../security-center/security-center-identity-access.md)  

**Monitorowanie Azure Security Center:** Obecnie niedostępne

**Odpowiedzialność:** Dział

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji
**Wskazówki:** Użyj dostępu warunkowego o nazwie lokalizacje, aby zezwolić na dostęp tylko do określonych logicznych grup zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)  

**Monitorowanie Azure Security Center:** Obecnie niedostępne

**Odpowiedzialność:** Dział

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory
**Wskazówki:** Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)  

**Monitorowanie Azure Security Center:** Obecnie niedostępne

**Odpowiedzialność:** Dział

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników
**Wskazówki:** Azure Active Directory (Azure AD) udostępnia dzienniki ułatwiające wykrywanie starych kont. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp.

- [Informacje o raportowaniu usługi Azure AD](../active-directory/reports-monitoring/overview-reports.md)  
- [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)  

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych kont
**Wskazówki:** Masz dostęp do źródeł danych dotyczących logowania w usłudze Azure Active Directory (Azure AD), inspekcji i zdarzeń związanych z ryzykiem, które umożliwiają integrację z dowolnymi narzędziami do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM).

Proces ten można usprawnić, tworząc ustawienia diagnostyczne dla Azure Active Directory kont użytkowników i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics. Można skonfigurować alerty w obszarze roboczym Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)  

**Monitorowanie Azure Security Center:** Obecnie niedostępne

**Odpowiedzialność:** Dział

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta
**Wskazówki:** Użyj funkcji ryzyka i ochrony tożsamości Azure Active Directory (Azure AD), aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)  
- [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)  

**Monitorowanie Azure Security Center:** Obecnie niedostępne

**Odpowiedzialność:** Dział

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej
**Wskazówki:** Skrytka klienta nie jest obecnie obsługiwane dla Azure DevTest Labs.

- [Lista obsługiwanych usług Skrytka klienta](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability) 

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

## <a name="data-protection"></a>Ochrona danych
*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji
**Wskazówki:** Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne
**Wskazówki:** Zaimplementuj osobne subskrypcje lub grupy zarządzania na potrzeby tworzenia, testowania i produkcji. Wystąpienia Azure DevTest Labs powinny być oddzielone przez sieć wirtualną/podsieć i odpowiednio oznakowane. 

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)
- [Tworzenie grup zarządzania](../governance/management-groups/create-management-group-portal.md)
- [Jak skonfigurować sieć wirtualną dla DevTest Labs](devtest-lab-configure-vnet.md)
- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)
- [Tworzenie i używanie tagów dla DevTest Labs](devtest-lab-add-tag.md)

**Monitorowanie Azure Security Center:** Obecnie niedostępne

**Odpowiedzialność:** Dział

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych
**Wskazówki:** Jeszcze niedostępne; funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla Azure DevTest Labs.

Firma Microsoft zarządza podstawową infrastrukturą dla Azure DevTest Labs i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie Azure Security Center:** Obecnie niedostępne

**Odpowiedzialność:** Udostępniać

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania
**Wskazówki:** Azure DevTest Labs domyślnie wymaga komunikacji szyfrowanej przy użyciu protokołu TLS. Protokół TLS w wersji 1,2 jest obecnie obsługiwany. Jeśli Biblioteka klienta lub narzędzie nie obsługuje protokołu TLS, można włączyć nieszyfrowane połączenia za pomocą Azure Portal lub interfejsów API zarządzania. W takich przypadkach, gdy połączenia szyfrowane nie są możliwe, zaleca się umieszczenie aplikacji laboratorium i klienta w sieci wirtualnej.

[Opis sposobu szyfrowania w scenariuszu przesyłania dla laboratoriów DevTest Labs](https://techcommunity.microsoft.com/t5/azure-developer-community-blog/azure-devtest-labs-enforcing-tls-1-2-starting-may-01-2020/ba-p/1236279)

**Monitorowanie Azure Security Center:** Opcję

**Odpowiedzialność:** Udostępniać

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane
**Wskazówki:** Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla Azure DevTest Labs. Wystąpienia tagów zawierające poufne informacje jako takie i Implementuj rozwiązanie innych firm, jeśli jest to niezbędne do celów zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie Azure Security Center:** Obecnie niedostępne

**Odpowiedzialność:** Dział

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów
**Wskazówki:** Za pomocą kontroli dostępu opartej na rolach (Azure RBAC) można kontrolować dostęp do laboratoriów w Azure DevTest Labs.

- [Jak skonfigurować usługę Azure RBAC](../role-based-access-control/role-assignments-portal.md)
- [Informacje o rolach w DevTest Labs](devtest-lab-add-devtest-user.md)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście
**Wskazówki:** Jeśli jest to niezbędne do zgodności zasobów obliczeniowych utworzonych w ramach DevTest Labs, zaimplementuj narzędzie innej firmy, takie jak automatyczne rozwiązanie do ochrony przed utratą danych oparte na hoście, aby wymusić kontrolę dostępu do danych, nawet jeśli dane są kopiowane poza system.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Nie dotyczy

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku
**Wskazówki:** Azure DevTest Labs przechowuje następujące dane klienta:

- [Wyniki artefaktów](add-artifact-vm.md) , które obejmują dzienniki wdrożenia i rozszerzenia wygenerowane na podstawie zastosowania artefaktów
- [Dokumenty formuł](devtest-lab-manage-formulas.md) , które są używane do tworzenia maszyn wirtualnych z formuł
- System operacyjny i dyski danych dla maszyn wirtualnych laboratorium 

Wyniki artefaktów i dokumenty formuł są wysyłane do konta usługi Azure Storage, które jest tworzone w ramach każdego wdrożenia laboratorium. Dane w usłudze Azure Storage są szyfrowane i odszyfrowywane w sposób niewidoczny dla użytkownika przy użyciu 256-bitowego szyfrowania AES, jednego z najsilniejszych szyfrów blokowych i jest zgodny ze standardem FIPS 140-2. Nie można wyłączyć szyfrowania usługi Azure Storage. Możesz polegać na kluczach zarządzanych przez firmę Microsoft na potrzeby szyfrowania konta magazynu lub można zarządzać szyfrowaniem przy użyciu własnych kluczy. Aby uzyskać więcej informacji, zobacz [szyfrowanie dla konta magazynu laboratorium](encrypt-storage.md).

Domyślnie wszystkie dyski systemu operacyjnego i danych programu Lab są szyfrowane za pomocą klucza zarządzanego platformy. Wszystkie dyski zarządzane, migawki, obrazy i dane zapisywane na istniejących dyskach zarządzanych są automatycznie szyfrowane przy użyciu kluczy zarządzanych przez platformę. Jako właściciel laboratorium można skonfigurować dyski systemu operacyjnego laboratorium do szyfrowania przy użyciu klucza zarządzanego przez klienta. Szyfrowanie przy użyciu klucza zarządzanego przez klienta dla dysków danych laboratorium nie jest obecnie konfigurowalne za pośrednictwem laboratorium. Jednak administrator subskrypcji może skonfigurować to ustawienie dla dysków laboratorium w ramach subskrypcji. Aby uzyskać więcej informacji, zobacz artykuł [szyfrowanie dysków systemu operacyjnego w środowisku Lab DevTest Labs przy użyciu kluczy zarządzanych przez klienta](encrypt-disks-customer-managed-keys.md).

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Udostępniać

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure
**Wskazówki:** Użyj Azure Monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dotyczące sytuacji, w których zmiany dotyczą wystąpień DevTest Labs i innych krytycznych lub powiązanych zasobów.

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/alerts/alerts-activity-log.md)
- [Jak utworzyć alerty dla zdarzeń dziennika aktywności DevTest Labs](create-alerts.md)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział



## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach
*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach
**Wskazówki:** Postępuj zgodnie z zaleceniami Azure Security Center na zabezpieczanie wystąpień Azure DevTest Labs i powiązanych zasobów.

Firma Microsoft przeprowadza zarządzanie lukami w zabezpieczeniach zasobów, które obsługują Azure DevTest Labs.

- [Informacje na temat Azure Security Center zaleceń](../security-center/recommendations-reference.md) 

**Monitorowanie Azure Security Center:** Opcję

**Odpowiedzialność:** Udostępniać

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego
**Wskazówki:** Użyj usługi Azure Update Management, aby upewnić się, że najnowsze aktualizacje zabezpieczeń są zainstalowane na maszynach wirtualnych z systemami Windows i Linux hostowanych w ramach usługi DevTest Labs. W przypadku maszyn wirtualnych z systemem Windows upewnij się, że Windows Update została włączona i ustawiona na automatyczne aktualizowanie. To ustawienie nie jest obecnie dostępne do konfigurowania za pośrednictwem DevTest Labs, ale administrator laboratorium/administrator subskrypcji może skonfigurować to ustawienie na podstawowych maszynach wirtualnych obliczeniowych w ich subskrypcji. 

- [Jak skonfigurować Update Management dla maszyn wirtualnych na platformie Azure](../automation/update-management/overview.md)
- [Informacje o zasadach zabezpieczeń platformy Azure monitorowanych przez Security Center](../security-center/policy-reference.md)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Wdróż zautomatyzowane rozwiązanie do zarządzania poprawkami oprogramowania innych firm
***Wskazówki:*** Jako administrator laboratorium możesz użyć [artefaktów DevTest Labs](add-artifact-vm.md) , aby zautomatyzować aktualizacje niestandardowych obrazów laboratorium, w tym poprawki zabezpieczeń i inne aktualizacje. 

Dowiedz się więcej o usłudze [DevTest Labs Image Factory](image-factory-create.md), która jest rozwiązaniem typu "Konfiguracja jako kod", które regularnie kompiluje i dystrybuuje obrazy, z uwzględnieniem wszystkich pożądanych konfiguracji. 

Jako administrator subskrypcji możesz także użyć rozwiązania Update Management platformy Azure do zarządzania aktualizacjami i poprawkami dla maszyn wirtualnych DevTest Labs. Update Management opiera się na lokalnie skonfigurowanym repozytorium aktualizacji w celu zastosowania poprawek obsługiwanych systemów Windows. Narzędzia, takie jak System Center Updates Publisher (aktualizacje wydawcy), umożliwiają publikowanie aktualizacji niestandardowych w programie Windows Server Update Services (WSUS). Ten scenariusz umożliwia Update Management poprawek maszyn, które używają Configuration Manager jako repozytorium aktualizacji z oprogramowaniem innych firm.

- [Update Management rozwiązanie na platformie Azure](../automation/update-management/overview.md)
- [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych](../automation/update-management/overview.md)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu
**Wskazówki:** Eksportuj wyniki skanowania w regularnych odstępach czasu i Porównaj wyniki, aby sprawdzić, czy luki zostały skorygowane. W przypadku korzystania z rekomendacji dotyczących zarządzania lukami w zabezpieczeniach sugerowanej przez Azure Security Center klient może przestawić w portalu wybranego rozwiązania, aby wyświetlić historyczne dane skanowania.

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach
**Wskazówki:** Użyj domyślnych ocen ryzyka (wynik zabezpieczony) zapewnianych przez Azure Security Center.

- [Informacje na temat Azure Security Center zabezpieczeń](../security-center/secure-score-security-controls.md)

**Monitorowanie Azure Security Center:** Opcję

**Odpowiedzialność:** Dział

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami
*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Spis i zarządzanie zasobami](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów
**Wskazówki:** Usługa Azure Resource Graph umożliwia wykonywanie zapytań i odnajdywanie wszystkich zasobów (w tym zasobów DevTest Labs) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i możesz wyliczyć wszystkie subskrypcje i zasoby platformy Azure w ramach subskrypcji.

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)
- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)
- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów
**Wskazówki:** Zastosowanie tagów do zasobów platformy Azure, dzięki czemu metadane są logicznie zorganizowane zgodnie z taksonomią.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)
- [Konfigurowanie tagów dla DevTest Labs](devtest-lab-add-tag.md)

**Monitorowanie Azure Security Center:** Niedostępne

**Odpowiedzialność:** Dział

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure
**Wskazówki:** Korzystaj z tagowania, grup zarządzania i oddzielania subskrypcji, a w razie potrzeby Organizuj i śledź laboratoria oraz zasoby związane z laboratorium. Regularnie Uzgadniaj spis i zapewnij szybkie usuwanie nieautoryzowanych zasobów z subskrypcji.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)
- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)
- [Jak utworzyć laboratorium przy użyciu DevTest Labs](devtest-lab-create-lab.md)
- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)
- [Jak skonfigurować Tagi dla laboratorium](devtest-lab-add-tag.md)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i konserwowanie spisu zatwierdzonych zasobów platformy Azure
**Wskazówki:** Utwórz spis zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych zgodnie z potrzebami organizacji. Jako administrator subskrypcji można także użyć funkcji adaptacyjnego sterowania aplikacjami, która Azure Security Center ułatwia zdefiniowanie zestawu aplikacji, które mogą być uruchamiane w skonfigurowanych grupach maszyn laboratorium. Ta funkcja jest dostępna zarówno na platformie Azure, jak i w systemie Windows (wszystkie wersje, klasyczne lub Azure Resource Manager) i na maszynach z systemem Linux.

- [Jak włączyć adaptacyjną kontrolę aplikacji](../security-center/security-center-adaptive-application.md)
 
**Monitorowanie Azure Security Center:** Niewłaściwa **odpowiedzialność:** klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure
**Wskazówki:** Użyj usługi Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Należy również użyć grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania zasobów w ramach subskrypcji. Może pomóc w wysokich środowiskach opartych na zabezpieczeniach, takich jak te z kontami magazynu.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)
- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych
**Wskazówki:** Azure Automation zapewnia pełną kontrolę podczas wdrażania, działania i likwidowania obciążeń i zasobów. Jako administrator subskrypcji możesz użyć spisu maszyn wirtualnych platformy Azure, aby zautomatyzować zbieranie informacji o całym oprogramowaniu na maszynach wirtualnych DevTest Labs w ramach subskrypcji. Właściwości nazwa oprogramowania, wersja, Wydawca i czas odświeżania są dostępne w Azure Portal. Aby uzyskać dostęp do daty instalacji i innych informacji, klient musi włączyć diagnostykę na poziomie gościa i przenieść dzienniki zdarzeń systemu Windows do obszaru roboczego Log Analytics.

Oprócz używania Change Tracking do monitorowania aplikacji programowych, adaptacyjnych kontroli aplikacji w Azure Security Center korzystania z uczenia maszynowego do analizowania aplikacji uruchomionych na maszynach i tworzenia listy dozwolonych na podstawie tej analizy. Ta funkcja znacznie upraszcza proces konfigurowania i konserwowania zasad listy dozwolonych aplikacji, co pozwala uniknąć niechcianego oprogramowania, które ma być używane w danym środowisku. Można skonfigurować tryb inspekcji lub Tryb wymuszania. Tryb inspekcji tylko przeprowadza inspekcję działania na chronionych maszynach wirtualnych. Tryb wymuszania wymusza reguły i sprawdza, czy aplikacje, które nie są dozwolone do uruchomienia, są zablokowane. 

- [Wprowadzenie do usługi Azure Automation](../automation/automation-intro.md)
- [Jak włączyć Spis maszyn wirtualnych platformy Azure](../automation/automation-tutorial-installed-software.md)
- [Zrozumienie adaptacyjnych kontrolek aplikacji](../security-center/security-center-adaptive-application.md)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział


### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania
**Wskazówki:** Azure Automation zapewnia pełną kontrolę podczas wdrażania, działania i likwidowania obciążeń i zasobów. Administrator subskrypcji może używać Change Tracking do identyfikowania wszystkich programów zainstalowanych na maszynach wirtualnych hostowanych w DevTest Labs. Aby usunąć nieautoryzowane oprogramowanie, można zaimplementować własny proces lub użyć konfiguracji stanu Azure Automation.

- [Wprowadzenie do usługi Azure Automation](../automation/automation-intro.md)
- [Śledź zmiany w środowisku przy użyciu rozwiązania Change Tracking](../automation/change-tracking/overview.md)
- [Przegląd konfiguracji stanu Azure Automation](../automation/automation-dsc-overview.md)

**Monitorowanie Azure Security Center:** Niedostępne

**Odpowiedzialność:** Dział

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji
**Wskazówki:** Administrator subskrypcji może użyć Azure Security Center adaptacyjnych kontroli aplikacji, aby upewnić się, że wykonywane jest tylko autoryzowane oprogramowanie i wszystkie nieautoryzowane oprogramowanie jest blokowane na maszynach wirtualnych platformy Azure hostowanych w DevTest Labs.

- [Jak używać Azure Security Center adaptacyjnych kontroli aplikacji](../security-center/security-center-adaptive-application.md)

**Monitorowanie Azure Security Center:** Opcję

**Odpowiedzialność:** Dział

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure
**Wskazówki:** Użyj usługi Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad: 

- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Zobacz następujące artykuły: 
- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)
- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/index.md)

**Monitorowanie Azure Security Center:** Opcję

**Odpowiedzialność:** Dział


### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: przechowywanie spisu zatwierdzonych tytułów oprogramowania
**Wskazówki:** Adaptacyjna kontrola aplikacji to inteligentne, zautomatyzowane i kompleksowe rozwiązanie od Azure Security Center, które pomaga kontrolować, które aplikacje mogą być uruchamiane na maszynach z platformą Azure i poza platformą Azure (Windows i Linux), hostowane w DevTest Labs. Pamiętaj, że musisz być administratorem subskrypcji, aby skonfigurować to ustawienie dla bazowych zasobów obliczeniowych hostowanych w DevTest Labs. Zaimplementuj rozwiązanie innych firm, jeśli to ustawienie nie spełnia wymagań organizacji.

- [Jak używać Azure Security Center adaptacyjnych kontroli aplikacji](../security-center/security-center-adaptive-application.md)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager
**Wskazówki:** Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość współpracy użytkowników z Azure Resource Manager przez skonfigurowanie **dostępu blokowego**"* *" dla aplikacji **zarządzania Microsoft Azure** .

- [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorowanie Azure Security Center:** Opcję

**Odpowiedzialność:** Dział

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników
**Wskazówki:** W zależności od typu skryptów można użyć konfiguracji specyficznych dla systemu operacyjnego lub zasobów innych firm, aby ograniczyć możliwość wykonywania skryptów w ramach maszyn wirtualnych hostowanych w DevTest Labs. Możesz również użyć Azure Security Center adaptacyjnych kontroli aplikacji, aby upewnić się, że tylko autoryzowane oprogramowanie i wszystkie nieautoryzowane oprogramowanie zostało zablokowane na podstawowych maszynach wirtualnych platformy Azure.

- [Jak kontrolować wykonywanie skryptów programu PowerShell w środowiskach systemu Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)
- [Jak używać Azure Security Center adaptacyjnych kontroli aplikacji](../security-center/security-center-adaptive-application.md)

**Monitorowanie Azure Security Center:** Niedostępne

**Odpowiedzialność:** Dział


### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizycznie lub logicznie segregowaj aplikacje o wysokim ryzyku
**Wskazówki:** Aplikacje o wysokim ryzyku wdrożone w środowisku platformy Azure mogą być izolowane przy użyciu sieci wirtualnej, podsieci, subskrypcji, grup zarządzania itd. i dostatecznie zabezpieczone przez zaporę platformy Azure, zaporę aplikacji sieci Web (WAF) lub sieciową grupę zabezpieczeń (sieciowej grupy zabezpieczeń).

- [Konfigurowanie sieci wirtualnej dla usługi DevTest Labs](devtest-lab-configure-vnet.md)
- [Omówienie Zapory platformy Azure](../firewall/overview.md)
- [Omówienie zapory aplikacji sieci Web](../web-application-firewall/overview.md)
- [Omówienie zabezpieczeń sieci](../virtual-network/network-security-groups-overview.md)
- [Omówienie usługi Azure Virtual Network]()
- [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../governance/management-groups/overview.md)
- [Przewodnik po decyzjach związanych z subskrypcjami](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Monitorowanie Azure Security Center:** Niedostępne

**Odpowiedzialność:** Dział

## <a name="secure-configuration"></a>Bezpieczna konfiguracja
**Aby uzyskać więcej informacji, zobacz Kontrola zabezpieczeń: bezpieczna konfiguracja.**

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure
**Wskazówki:** Użyj aliasów Azure Policy, aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji zasobów platformy Azure utworzonych w ramach DevTest Labs. Możesz również użyć wbudowanych definicji Azure Policy.

Ponadto Azure Resource Manager ma możliwość eksportowania szablonu w JavaScript Object Notation (JSON), który powinien zostać sprawdzony, aby upewnić się, że konfiguracje spełniają/przekroczą wymagania dotyczące zabezpieczeń organizacji.

Możesz również użyć zaleceń z Azure Security Center jako bezpiecznej linii bazowej konfiguracji dla zasobów platformy Azure.

- [Jak wyświetlić dostępne aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)
- [Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](../governance/policy/tutorials/create-and-manage.md)
- [Eksport jednego i kilku zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)
- [Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny](../security-center/recommendations-reference.md)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych
**Wskazówki:** Użyj zaleceń Azure Security Center, aby zachować konfiguracje zabezpieczeń dla wszystkich podstawowych zasobów obliczeniowych utworzonych w ramach DevTest Labs. Ponadto możesz użyć niestandardowych obrazów systemu operacyjnego lub Azure Automation konfiguracji stanu lub artefaktów DevTest Labs, aby określić konfigurację zabezpieczeń systemu operacyjnego wymaganego przez organizację.

- [Jak monitorować zalecenia dotyczące Azure Security Center](../security-center/security-center-recommendations.md)
- [Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny](../security-center/recommendations-reference.md)
- [Przegląd konfiguracji stanu Azure Automation](../automation/automation-dsc-overview.md)
- [Przekazywanie wirtualnego dysku twardego i używanie go do tworzenia nowych maszyn wirtualnych z systemem Windows na platformie Azure](../virtual-machines/windows/upload-generalized-managed.md)
- [Tworzenie maszyny wirtualnej z systemem Linux na podstawie dysku niestandardowego przy użyciu interfejsu wiersza polecenia platformy Azure](../virtual-machines/linux/upload-vhd.md)
- [Tworzenie i dystrybuowanie obrazów niestandardowych do wielu DevTest Labs](image-factory-save-distribute-custom-images.md)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Nie dotyczy

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure
**Wskazówki:** Użyj Azure Policy **Odmów** i **Wdróż, jeśli nie istnieją** reguły, aby wymusić bezpieczne ustawienia dla zasobów platformy Azure utworzonych w ramach usługi DevTest Labs. Ponadto, możesz użyć szablonów Azure Resource Manager, aby zachować konfigurację zabezpieczeń zasobów platformy Azure wymaganych przez organizację.

- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)
- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)
- [Przegląd szablonów Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego
**Wskazówki:** Postępuj zgodnie z zaleceniami Azure Security Center na temat przeprowadzania ocen luk w zabezpieczeniach w Twoich źródłowych zasobach obliczeniowych platformy Azure utworzonych w ramach laboratorium. Ponadto można użyć szablonów Azure Resource Manager, niestandardowych obrazów systemu operacyjnego lub konfiguracji stanu Azure Automation, aby zachować konfigurację zabezpieczeń systemu operacyjnego wymaganego przez organizację. Możesz również użyć rozwiązania fabryki obrazów, które jest rozwiązaniem typu "Konfiguracja jako kod", które automatycznie kompiluje i dystrybuuje obrazy na bieżąco ze wszystkimi wymaganymi konfiguracjami.

Ponadto w firmie Microsoft są zarządzane i utrzymywane w portalu Azure Marketplace obrazy maszyn wirtualnych opublikowane przez firmę Microsoft.

- [Jak zaimplementować zalecenia dotyczące oceny luk w zabezpieczeniach Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)
- [Przegląd konfiguracji stanu Azure Automation](../automation/automation-dsc-overview.md)
- [Przykładowy skrypt do przekazania wirtualnego dysku twardego na platformę Azure i utworzenia nowej maszyny wirtualnej](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)
- [Jak utworzyć fabrykę obrazów w DevTest Labs](image-factory-create.md)

**Monitorowanie Azure Security Center:** Opcję

**Odpowiedzialność:** Udostępniać

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure
**Wskazówki:** Za pomocą usługi Azure DevOps bezpiecznie przechowuj swój kod, taki jak niestandardowe zasady platformy Azure, szablony Azure Resource Manager i skrypty konfiguracji żądanego stanu, i zarządzaj nimi. Aby uzyskać dostęp do zasobów zarządzanych w usłudze Azure DevOps, można udzielić lub odmówić uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w Azure Active Directory (Azure AD) w przypadku integracji z usługą Azure DevOps.

- [Samouczek usługi git Azure Repos](/azure/devops/repos/git/gitworkflow)
- [Informacje o uprawnieniach i grupach](/azure/devops/organizations/security/about-permissions?tabs=preview-page&view=azure-devops)
- [Integracja między Azure DevTest Labs i przepływem pracy usługi Azure DevOps](devtest-lab-dev-ops.md)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego
**Wskazówki:** W przypadku używania obrazów niestandardowych Użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby zapewnić dostęp do obrazów tylko autoryzowanym użytkownikom. Za pomocą udostępnionej galerii obrazów możesz udostępnić swoje obrazy do określonych laboratoriów, które ich potrzebują. W przypadku obrazów kontenerów Zapisz je w Azure Container Registry i Użyj usługi Azure RBAC, aby zapewnić dostęp do obrazów tylko autoryzowanym użytkownikom.

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Jak skonfigurować usługę Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)
- [Konfigurowanie udostępnionej galerii obrazów dla DevTest Labs](configure-shared-image-gallery.md)
- [Informacje na temat usługi Azure RBAC dla Container Registry](../container-registry/container-registry-roles.md)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją systemu
**Wskazówki:** Zdefiniuj i Implementuj standardowe konfiguracje zabezpieczeń dla zasobów platformy Azure przy użyciu Azure Policy. Użyj aliasów Azure Policy, aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci zasobów platformy Azure utworzonych w ramach DevTest Labs. Mogą również korzystać z wbudowanych definicji zasad związanych z konkretnymi zasobami. Ponadto można użyć Azure Automation do wdrożenia zmian konfiguracji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)
- [Jak używać aliasów](../governance/policy/concepts/definition-structure.md#aliases)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych
**Wskazówki:** Azure Automation konfiguracja stanu to usługa zarządzania konfiguracją dla węzłów konfiguracji żądanego stanu (DSC) w dowolnym chmurze lub lokalnym centrum danych. Można łatwo dołączać maszyny, przypisywać do nich konfiguracje deklaracyjne i wyświetlać raporty pokazujące zgodność poszczególnych maszyn z wybranym określonym stanem. Możesz również napisać niestandardowy artefakt, który może zostać zainstalowany na każdej maszynie laboratoryjnej, aby upewnić się, że są one zgodne z zasadami organizacji. 

- [Dołączanie maszyn w celu zarządzania przez Azure Automation konfigurację stanu](../automation/automation-dsc-onboarding.md)
- [Tworzenie niestandardowych artefaktów dla maszyn wirtualnych z DevTest Labs](devtest-lab-artifact-author.md)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla usług platformy Azure
**Wskazówki:** Użyj Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla zasobów platformy Azure utworzonych w ramach DevTest Labs. Ponadto należy używać Azure Policy do powiadamiania i inspekcji konfiguracji zasobów platformy Azure.

- [Jak skorygować zalecenia w Azure Security Center](../security-center/security-center-remediate-recommendations.md)
 
**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych
**Wskazówki:** Użyj Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla ustawień systemu operacyjnego i platformy Docker dla kontenerów.

- [Omówienie rekomendacji dotyczących kontenera usługi Azure Security Center](../security-center/container-security.md)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure
**Wskazówki:** Użyj tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć tajne Zarządzanie aplikacjami w chmurze.

- [Konfigurowanie tożsamości zarządzanej do wdrażania środowisk Azure Resource Manager w usłudze DevTest Labs](use-managed-identities-environments.md)
- [Konfigurowanie tożsamości zarządzanej do wdrażania maszyn wirtualnych w usłudze DevTest Labs](enable-managed-identities-lab-vms.md)
- [Jak utworzyć magazyn kluczy](../key-vault/general/quick-create-portal.md)
- [Jak przeprowadzić uwierzytelnianie w Key Vault](../key-vault/general/authentication.md)
- [Jak przypisać zasady dostępu Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Monitorowanie Azure Security Center:** Opcję

**Odpowiedzialność:** Dział

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami
**Wskazówki:** Użyj tożsamości zarządzanych, aby zapewnić usługom platformy Azure automatyczną tożsamość zarządzaną w usłudze Azure AD. Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie.

- [Konfigurowanie tożsamości zarządzanej do wdrażania środowisk Azure Resource Manager w usłudze DevTest Labs](use-managed-identities-environments.md)
- [Konfigurowanie tożsamości zarządzanej do wdrażania maszyn wirtualnych w usłudze DevTest Labs](enable-managed-identities-lab-vms.md)
 
**Monitorowanie Azure Security Center:** Opcję

**Odpowiedzialność:** Dział

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia
**Wskazówki:** Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

- Jak skonfigurować skaner poświadczeń

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział


## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem
*Aby uzyskać więcej informacji, zobacz Kontrola zabezpieczeń: Obrona złośliwego oprogramowania.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem
**Wskazówki:** Korzystaj z programu Microsoft chroniącego przed złośliwym kodem dla platformy Azure Cloud Services i Virtual Machines do ciągłego monitorowania i obrony zasobów. W przypadku systemu Linux należy użyć rozwiązania chroniącego przed złośliwym kodem innej firmy. Ponadto należy użyć wykrywania zagrożeń Azure Security Center dla usług danych w celu wykrywania złośliwego oprogramowania przekazanego do kont magazynu.

- Jak skonfigurować oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft dla Cloud Services i Virtual Machines
- Ochrona przed zagrożeniami w usłudze Azure Security Center

**Monitorowanie Azure Security Center:** Opcję

**Odpowiedzialność:** Dział

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe
**Wskazówki:** Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Azure App Service hostowane w laboratorium), ale nie jest uruchamiane w Twojej zawartości.
Skanuj wstępnie wszystkie pliki przekazywane do zasobów platformy Azure, które nie są obliczeniowe, takie jak App Service, Data Lake Storage, Blob Storage itd.

Użyj wykrywania zagrożeń Azure Security Center dla usług danych w celu wykrywania złośliwego oprogramowania przekazanego do kont magazynu.

- Informacje na temat ochrony przed złośliwym oprogramowaniem firmy Microsoft Cloud Services i Virtual Machines
- Zrozumienie wykrywania zagrożeń Azure Security Center dla usług danych

**Monitorowanie Azure Security Center:** Opcję

**Odpowiedzialność:** Nie dotyczy


### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane
**Wskazówki:** Po wdrożeniu usługa Microsoft chroniąca przed złośliwym kodem na platformie Azure automatycznie zainstaluje najnowsze aktualizacje sygnatur, platform i aparatu. Postępuj zgodnie z zaleceniami w Azure Security Center: "COMPUTE & Apps", aby upewnić się, że wszystkie punkty końcowe dla podstawowych zasobów obliczeniowych DevTest Labs są aktualne przy użyciu najnowszych sygnatur. System operacyjny Windows może być dodatkowo chroniony przy użyciu dodatkowych zabezpieczeń w celu ograniczenia ryzyka ataków na ataki przez wirusy lub złośliwe oprogramowanie w usłudze Microsoft Defender Advanced Threat Protection, która integruje się z Azure Security Center.

- Jak wdrożyć program Microsoft chroniący przed złośliwym kodem dla platformy Azure Cloud Services i Virtual Machines
- Zaawansowana ochrona przed zagrożeniami w usłudze Microsoft Defender

**Monitorowanie Azure Security Center:** Opcję

**Odpowiedzialność:** Dział

## <a name="data-recovery"></a>Odzyskiwanie danych
*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zapewnianie regularnych zautomatyzowanych kopii zapasowych
**Wskazówki:** Obecnie Azure DevTest Labs nie obsługuje kopii zapasowych maszyn wirtualnych i migawek. Można jednak włączyć i skonfigurować Azure Backup na podstawowych maszynach wirtualnych platformy Azure hostowanych w usłudze DevTest Labs. Ponadto można skonfigurować żądaną częstotliwość i okres przechowywania automatycznych kopii zapasowych, o ile masz odpowiedni dostęp do podstawowych zasobów obliczeniowych. 

- [Omówienie kopii zapasowej maszyny wirtualnej platformy Azure](../backup/backup-azure-vms-introduction.md)
- [Tworzenie kopii zapasowej maszyny wirtualnej platformy Azure z ustawień maszyny wirtualnej](../backup/backup-azure-vms-first-look-arm.md)

**Monitorowanie Azure Security Center:** Opcję

**Odpowiedzialność:** Dział

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta
**Wskazówki:** Obecnie Azure DevTest Labs nie obsługuje kopii zapasowych maszyn wirtualnych i migawek. Można jednak tworzyć migawki podstawowych maszyn wirtualnych platformy Azure hostowanych w laboratoriach DevTest Labs lub dyskach zarządzanych podłączonych do tych wystąpień przy użyciu programu PowerShell lub interfejsów API REST, o ile masz odpowiednie uprawnienia dostępu do podstawowych zasobów obliczeniowych. Można również utworzyć kopię zapasową wszystkich kluczy zarządzanych przez klienta w ramach Azure Key Vault.

Włącz Azure Backup na docelowych maszynach wirtualnych platformy Azure i pożądaną częstotliwość i okres przechowywania. Obejmuje to kompletną kopię zapasową stanu systemu. Jeśli używasz usługi Azure Disk Encryption, kopia zapasowa maszyny wirtualnej platformy Azure automatycznie obsługuje tworzenie kopii zapasowych kluczy zarządzanych przez klienta.

- [Tworzenie kopii zapasowych na maszynach wirtualnych platformy Azure korzystających z szyfrowania](../backup/backup-azure-vms-encryption.md)
- [Omówienie kopii zapasowej maszyny wirtualnej platformy Azure](../backup/backup-azure-vms-introduction.md)
- [Omówienie kopii zapasowej maszyny wirtualnej platformy Azure](../backup/backup-azure-vms-introduction.md)
- [Jak utworzyć kopię zapasową kluczy Key Vault na platformie Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorowanie Azure Security Center:** Opcję

**Odpowiedzialność:** Dział

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta
**Wskazówki:** Pamiętaj, aby okresowo wykonywać przywracanie danych w Azure Backup. W razie potrzeby przetestuj przywracanie zawartości do izolowanej sieci wirtualnej lub subskrypcji. Ponadto Przetestuj przywracanie kopii zapasowych kluczy zarządzanych przez klienta.

Jeśli używasz usługi Azure Disk Encryption, możesz przywrócić maszynę wirtualną platformy Azure z kluczami szyfrowania dysku. W przypadku korzystania z szyfrowania dysków można przywrócić maszynę wirtualną platformy Azure z kluczami szyfrowania dysku.

- [Tworzenie kopii zapasowych na maszynach wirtualnych platformy Azure korzystających z szyfrowania](../backup/backup-azure-vms-encryption.md)
- [Jak odzyskać pliki z kopii zapasowej maszyny wirtualnej platformy Azure](../backup/backup-azure-restore-files-from-vm.md)
- [Jak przywrócić klucze magazynu kluczy na platformie Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)
- [Jak utworzyć kopię zapasową i przywrócić zaszyfrowaną maszynę wirtualną](../backup/backup-azure-vms-encryption.md)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta
**Wskazówki:** Podczas tworzenia kopii zapasowych dysków zarządzanych przy użyciu Azure Backup maszyny wirtualne są szyfrowane przy użyciu szyfrowanie usługi Storage (SSE). Azure Backup może również tworzyć kopie zapasowe maszyn wirtualnych platformy Azure, które są szyfrowane przy użyciu Azure Disk Encryption. Azure Disk Encryption integruje się z kluczami szyfrowania funkcji BitLocker (BEKs), które są chronione w magazynie kluczy jako wpisy tajne. Azure Disk Encryption integruje się również z kluczami szyfrowania klucza Azure Key Vault (KEKs). Włącz Soft-Delete w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

- [Usuwanie nietrwałe dla maszyn wirtualnych](../backup/soft-delete-virtual-machines.md)
- [Azure Key Vault-Soft-Delete — przegląd](../key-vault/general/soft-delete-overview.md)

**Monitorowanie Azure Security Center:** Opcję

**Odpowiedzialność:** Dział

## <a name="incident-response"></a>Reagowanie na zdarzenia
*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odpowiedź na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia
**Wskazówki:** Utwórz Przewodnik po odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że zostały zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników i faz obsługi zdarzeń/zarządzania z wykrywania na potrzeby przeglądu po zdarzeniu.

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
- [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
- [Skorzystaj z przewodnika obsługi zdarzeń zabezpieczeń komputera NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji
**Wskazówki:** Azure Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analizach używanych do wystawiania alertu, a także poziom pewności, że istniało złośliwe zamiar w odniesieniu do działania, które doprowadziło do alertu.

Dodatkowo jasno Oznacz subskrypcje (na przykład Produkcja, inne niż prod) przy użyciu tagów i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne. Odpowiedzialność za korygowanie alertów w oparciu o krytyczne znaczenie zasobów platformy Azure i środowisko, w którym wystąpiło zdarzenie, leży po stronie użytkownika.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)
- [Organizowanie zasobów platformy Azure przy użyciu tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie Azure Security Center:** Opcję

**Odpowiedzialność:** Dział

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe
**Wskazówki:** Przeprowadzaj ćwiczenia w celu przetestowania możliwości reagowania na zdarzenia systemu w ramach regularnego erze, aby pomóc w ochronie zasobów platformy Azure. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Przewodnik po publikacji NIST, który umożliwia testowanie, uczenie i wykonywanie programów dla planów i możliwości IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami
**Wskazówki:** Firma Microsoft będzie używać informacji kontaktowych o zdarzeniach dotyczących zabezpieczeń, aby skontaktować się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia
**Wskazówki:** Eksportuj alerty i zalecenia dotyczące Azure Security Center przy użyciu funkcji eksportu ciągłego, aby pomóc identyfikować zagrożenia dla zasobów platformy Azure. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)
- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

#### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń
**Wskazówki:** Funkcja automatyzacji przepływu pracy w programie Azure Security Center umożliwia automatyczne wyzwalanie odpowiedzi w Logic Apps ramach alertów zabezpieczeń i zaleceń dotyczących ochrony zasobów platformy Azure.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)
 
Monitorowanie Azure Security Center: * * * * nie dotyczy

**Odpowiedzialność:** Dział


## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”
*Aby uzyskać więcej informacji, zobacz Kontrola zabezpieczeń: [testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*


### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń w ciągu 60 dni
**Wskazówki:** Postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj ze strategii firmy Microsoft i wykonywania testów typu „red team” i testów na żywo w zarządzanej przez firmę Microsoft infrastrukturze, usługach i aplikacjach w chmurze.

- [Reguły testowania penetracji zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)
- [Red Teaming w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Udostępniać

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującym artykułem:

- [Alerty zabezpieczeń dla środowisk w Azure DevTest Labs](environment-security-alerts.md)