---
title: Ochrona Microsoft 365 z wykorzystaniem lokalnych ataków
description: Wskazówki dotyczące sposobu zapewnienia, że ataku lokalnego nie ma wpływu na Microsoft 365.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/22/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6d548f4d792d8980e2aa5040b09530eaf7868c4
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102609910"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>Ochrona Microsoft 365 z wykorzystaniem lokalnych ataków

Wielu klientów nawiązuje połączenie prywatnych sieci firmowych w celu Microsoft 365 uzyskania korzyści dla użytkowników, urządzeń i aplikacji. Te sieci prywatne można jednak złamać na wiele dobrze udokumentowanych sposobów. Ponieważ Microsoft 365 działa jako rodzaj systemu nerwowego dla wielu organizacji, ma kluczowe znaczenie dla ochrony przed naruszeniem infrastruktury lokalnej.

W tym artykule opisano sposób konfigurowania systemów w celu ochrony środowiska chmury Microsoft 365 z poziomu bezpieczeństwa lokalnego. Skupiamy się głównie na: 

- Ustawienia konfiguracji dzierżawy usługi Azure Active Directory (Azure AD).
- Jak dzierżawy usługi Azure AD mogą być bezpiecznie połączone z systemami lokalnymi.
- Wady wymagane do obsługi systemów w sposób chroniący systemy w chmurze przed naruszeniem lokalnym.

Zdecydowanie zalecamy zaimplementowanie tych wskazówek w celu zabezpieczenia środowiska chmury Microsoft 365.
> [!NOTE]
> Ten artykuł został początkowo opublikowany jako wpis w blogu. Została przeniesiona do bieżącej lokalizacji na potrzeby eksploatacji i konserwacji.
>
> Aby utworzyć wersję tego artykułu w trybie offline, użyj funkcji drukowania do pliku PDF w przeglądarce. Sprawdź tutaj często aktualizacje.

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>Podstawowe wektory zagrożeń ze złamanych środowisk lokalnych


Środowisko chmury Microsoft 365 korzyści z rozbudowanej infrastruktury monitorowania i zabezpieczeń. Korzystając z uczenia maszynowego i analizy ludzkiej, Microsoft 365 przeszukuje ruch na całym świecie. Może ona szybko wykrywać ataki i umożliwiać zmianę konfiguracji niemal w czasie rzeczywistym. 

W przypadku wdrożeń hybrydowych, które łączą się z infrastrukturą lokalną w celu Microsoft 365, wiele organizacji delegowanie zaufania do składników lokalnych w celu uwierzytelniania krytycznego i podejmowania decyzji dotyczących zarządzania stanem obiektu katalogu.
Niestety, jeśli środowisko lokalne zostało naruszone, te relacje zaufania stają się szansami atakujących na złamanie środowiska Microsoft 365.

Dwa podstawowe wektory zagrożeń to *relacje zaufania federacji* i *Synchronizacja konta.* Oba wektory mogą udzielić atakującemu dostępu administracyjnego do chmury.

* **Federacyjne relacje zaufania**, takie jak uwierzytelnianie SAML, są używane do uwierzytelniania w Microsoft 365 za poorednictwem lokalnej infrastruktury tożsamości. Jeśli certyfikat podpisywania tokenu SAML zostanie naruszony, Federacja zezwoli każdemu, kto ma ten certyfikat do personifikacji dowolnego użytkownika w chmurze. *Zalecamy wyłączenie relacji zaufania federacji na potrzeby uwierzytelniania, aby Microsoft 365, gdy jest to możliwe.*

* Przy użyciu **synchronizacji kont** można modyfikować uprzywilejowanych użytkowników (łącznie z ich poświadczeniami) lub grup, które mają uprawnienia administracyjne w Microsoft 365. *Zalecamy upewnienie się, że zsynchronizowane obiekty nie przechowują żadnych uprawnień poza użytkownikiem w Microsoft 365,* bezpośrednio lub przez dołączenie do zaufanych ról lub grup. Upewnij się, że te obiekty nie mają bezpośredniego lub zagnieżdżonego przypisania w zaufanych rolach lub grupach w chmurze.

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>Ochrona Microsoft 365 z poziomu naruszenia lokalnego


Aby rozwiązać opisane wcześniej wektory zagrożeń, zalecamy przestrzeganie zasad przedstawionych na poniższym diagramie:

![Architektura referencyjna do ochrony Microsoft 365.](media/protect-m365/protect-m365-principles.png)

1. **W pełni Izoluj konta administratorów Microsoft 365.** Powinny być:

    * W usłudze Azure AD.

     * Uwierzytelniane przy użyciu uwierzytelniania wieloskładnikowego.

     *  Zabezpieczony przez dostęp warunkowy usługi Azure AD.

     *  Dostępne tylko przy użyciu stacji roboczych zarządzanych przez platformę Azure.

    Konta tych administratorów są kontami z ograniczeniami. *Żadne konta lokalne nie powinny mieć uprawnień administracyjnych w Microsoft 365.* 

    Aby uzyskać więcej informacji, zobacz [Omówienie ról administratorów Microsoft 365](/microsoft-365/admin/add-users/about-admin-roles). Zobacz też [role Microsoft 365 w usłudze Azure AD](../roles/m365-workload-docs.md).

1. **Zarządzanie urządzeniami z poziomu Microsoft 365.** Użyj funkcji Dołącz do usługi Azure AD i zarządzania urządzeniami przenośnymi w chmurze, aby wyeliminować zależności od lokalnej infrastruktury zarządzania urządzeniami. Te zależności mogą naruszyć kontrolę nad urządzeniami i zabezpieczeniami.

1. **Upewnij się, że żadne konto lokalne nie ma podniesionych uprawnień do Microsoft 365.**
    Niektóre konta uzyskują dostęp do aplikacji lokalnych, które wymagają uwierzytelniania NTLM, LDAP lub Kerberos. Te konta muszą znajdować się w lokalnej infrastrukturze tożsamości w organizacji. Upewnij się, że te konta, w tym konta usług, nie są uwzględnione w uprzywilejowanych rolach lub grupach w chmurze. Należy również upewnić się, że zmiany tych kont nie wpłyną na integralność środowiska chmury. Uprzywilejowane oprogramowanie lokalne nie może wpływać na Microsoft 365 uprzywilejowanych kont lub ról.

1. **Użyj uwierzytelniania w chmurze usługi Azure AD** , aby wyeliminować zależności dotyczące poświadczeń lokalnych. Zawsze używaj silnego uwierzytelniania, takich jak Windows Hello, FIDO, Microsoft Authenticator lub uwierzytelnianie wieloskładnikowe usługi Azure AD.

## <a name="specific-security-recommendations"></a>Szczególne zalecenia dotyczące zabezpieczeń


Poniższe sekcje zawierają szczegółowe wskazówki dotyczące sposobu wdrażania zasad opisanych wcześniej.

### <a name="isolate-privileged-identities"></a>Izolowanie tożsamości uprzywilejowanych


W usłudze Azure AD użytkownicy posiadający role uprzywilejowane, na przykład Administratorzy, są głównym zaufaniem do kompilowania reszty środowiska i zarządzania nim. Zaimplementuj poniższe praktyki, aby zminimalizować wpływ naruszenia.

* Używaj kont tylko w chmurze dla usługi Azure AD i ról uprzywilejowanych Microsoft 365.

* Wdróż [uprzywilejowane urządzenia dostępu](/security/compass/privileged-access-devices#device-roles-and-profiles) dla uprzywilejowanego dostępu do zarządzania Microsoft 365 i usługi Azure AD.

*  Wdróż [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) dla dostępu just-in-Time (JIT) do wszystkich kont ludzkich, które mają uprzywilejowane role. Wymagaj silnego uwierzytelniania, aby aktywować role.

* Podaj role administracyjne, które umożliwiają [najmniejsze uprawnienia wymagane do wykonywania wymaganych zadań](../roles/delegate-by-task.md).

* Aby włączyć zaawansowane środowisko przypisywania ról, które obejmuje delegowanie i wiele ról w tym samym czasie, należy rozważyć użycie grup zabezpieczeń usługi Azure AD lub grup Microsoft 365. Te grupy są nazywane zbiorczo *grupami w chmurze*. [Włącz również kontrolę dostępu opartą na rolach](../roles/groups-assign-role.md). Za pomocą [jednostek administracyjnych](../roles/administrative-units.md) można ograniczyć zakres ról do części organizacji.

* Wdróż [konta dostępu awaryjnego](../roles/security-emergency-access.md). *Nie* należy używać lokalnych magazynów haseł do przechowywania poświadczeń.

Aby uzyskać więcej informacji, zobacz [Zabezpieczanie uprzywilejowanego dostępu](/security/compass/overview). Zobacz też [zasady bezpiecznego dostępu dla administratorów w usłudze Azure AD](../roles/security-planning.md).

### <a name="use-cloud-authentication"></a>Korzystanie z uwierzytelniania w chmurze 

Poświadczenia są podstawowym wektorem ataków. Aby zapewnić bezpieczeństwo poświadczeń, należy zaimplementować następujące rozwiązania:

* [Wdróż uwierzytelnianie bezhaseł](../authentication/howto-authentication-passwordless-deployment.md). W miarę możliwości należy ograniczyć użycie haseł przez wdrożenie poświadczeń bezhaseł. Te poświadczenia są zarządzane i weryfikowane natywnie w chmurze. Wybierz jedną z następujących metod uwierzytelniania:

   * [Funkcja Windows Hello dla firm](/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [Aplikacja Microsoft Authenticator](../authentication/howto-authentication-passwordless-phone.md)

   * [FIDO2 klucze zabezpieczeń](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [Wdróż uwierzytelnianie wieloskładnikowe](../authentication/howto-mfa-getstarted.md). Zainicjuj obsługę [wielu silnych poświadczeń przy użyciu uwierzytelniania wieloskładnikowego usługi Azure AD](../fundamentals/resilience-in-credentials.md). Dzięki temu dostęp do zasobów w chmurze będzie wymagał poświadczeń, które są zarządzane w usłudze Azure AD, a także hasła lokalnego, które można manipulować. Aby uzyskać więcej informacji, zobacz [Tworzenie elastycznej strategii zarządzania kontrolą dostępu przy użyciu usługi Azure AD](./resilience-overview.md).

### <a name="limitations-and-tradeoffs"></a>Ograniczenia i kompromisy

* Zarządzanie hasłami do konta hybrydowego wymaga składników hybrydowych, takich jak agenci ochrony haseł i agenci zapisywania zwrotnego haseł. Jeśli infrastruktura lokalna zostanie naruszona, osoby atakujące mogą kontrolować komputery, na których znajdują się agenci. Ta luka w zabezpieczeniach nie narusza infrastruktury chmurowej. Jednak konta w chmurze nie będą chronić tych składników przed naruszeniem lokalnym.

*  Konta lokalne zsynchronizowane z Active Directory są oznaczone jako niewygasające w usłudze Azure AD. To ustawienie jest zwykle zmniejszane przez lokalne ustawienia hasła Active Directory. Jeśli jednak lokalne wystąpienie Active Directory zostało naruszone, a synchronizacja zostanie wyłączona, należy ustawić opcję [EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md) , aby wymusić zmianę hasła.

## <a name="provision-user-access-from-the-cloud"></a>Inicjowanie dostępu użytkowników z chmury

*Inicjowanie obsługi* odnosi się do tworzenia kont użytkowników i grup w aplikacjach lub dostawcach tożsamości.

![Diagram architektury aprowizacji.](media/protect-m365/protect-m365-provision.png)

Zalecamy stosowanie następujących metod aprowizacji:

* Inicjowanie **obsługi administracyjnej aplikacji w chmurze w usłudze Azure AD**: Ta Provisioning umożliwia odizolowanie lokalnego kompromisu, bez zakłócania pracy łącznika — od aplikacji kadrowych w chmurze do usługi Azure AD.

* **Aplikacje w chmurze**: Jeśli to możliwe, wdróż [Inicjowanie obsługi aplikacji usługi Azure AD](../app-provisioning/user-provisioning.md) w przeciwieństwie do lokalnych rozwiązań aprowizacji. Ta metoda chroni niektóre aplikacje "oprogramowanie jako usługa" (SaaS), na które mają wpływ złośliwe profile hakerów w przypadku naruszeń lokalnych. 

* **Tożsamości zewnętrzne**: Użyj [funkcji współpracy B2B usługi Azure AD](../external-identities/what-is-b2b.md).
    Ta metoda zmniejsza zależność od kont lokalnych do współpracy zewnętrznej z partnerami, klientami i dostawcami. Starannie Oceń każdą bezpośrednią Federację z innymi dostawcami tożsamości. Zalecamy ograniczenie kont gościa B2B w następujący sposób:

   *  Ogranicz dostęp gościa do grup przeglądania i innych właściwości w katalogu. Użyj zewnętrznych ustawień współpracy, aby ograniczyć Gościom możliwość odczytywania grup, których nie są członkami. 

    *   Blokuj dostęp do Azure Portal. Można dokonać rzadkich niepotrzebnych wyjątków.  Utwórz zasady dostępu warunkowego, które obejmują wszystkich Gości i użytkowników zewnętrznych. Następnie [Zaimplementuj zasady, aby zablokować dostęp](../../role-based-access-control/conditional-access-azure-management.md). 

* **Odłączone lasy**: Użyj [usługi Azure AD Cloud aprowizacji](../cloud-sync/what-is-cloud-sync.md). Ta metoda umożliwia łączenie się z odłączonymi lasami, eliminując konieczność ustanowienia łączności między lasami lub relacji zaufania, co może poszerzyć skutki lokalnego naruszenia. 
 
### <a name="limitations-and-tradeoffs"></a>Ograniczenia i kompromisy

W przypadku korzystania z kont hybrydowych usługa Azure-AD-z chmury i kadr opiera się na synchronizacji lokalnej w celu ukończenia przepływu danych z Active Directory do usługi Azure AD. Jeśli synchronizacja zostanie przerwana, nowe rekordy pracowników nie będą dostępne w usłudze Azure AD.

## <a name="use-cloud-groups-for-collaboration-and-access"></a>Korzystanie z grup w chmurze w celu współpracy i dostępu

Grupy chmury umożliwiają rozdzielenie swojej współpracy i dostępu z infrastruktury lokalnej.

* **Współpraca**: Korzystaj z grup Microsoft 365 i Microsoft Teams w celu uzyskania nowoczesnej współpracy. Likwidowanie lokalnych list dystrybucyjnych i [uaktualnianie list dystrybucyjnych do grup Microsoft 365 w programie Outlook](/office365/admin/manage/upgrade-distribution-lists).

* **Dostęp**: Użyj grup zabezpieczeń usługi Azure AD lub grup Microsoft 365, aby autoryzować dostęp do aplikacji w usłudze Azure AD.
* **Licencjonowanie pakietu office 365**: Użyj licencjonowania opartego na grupach, aby zainicjować pakiet Office 365 przy użyciu grup tylko w chmurze. Ta metoda oddzieli kontrolę nad członkostwem w grupie z infrastruktury lokalnej.

Właściciele grup, którzy są używani do uzyskiwania dostępu, powinni być uznawani za tożsamości uprzywilejowane, aby uniknąć przejęcia członkostwa w środowisku lokalnym.
Przejęcie może obejmować bezpośrednie manipulowanie członkostwem w grupie lokalnie lub manipulowanie atrybutami lokalnymi, które mogą mieć wpływ na członkostwo w grupie dynamicznej w Microsoft 365.

## <a name="manage-devices-from-the-cloud"></a>Zarządzanie urządzeniami z chmury


Funkcje usługi Azure AD umożliwiają bezpieczne zarządzanie urządzeniami.

-   **Korzystanie z stacji roboczych z systemem Windows 10**: [wdrażanie urządzeń przyłączonych do usługi Azure AD](../devices/azureadjoin-plan.md) przy użyciu zasad MDM. Włącz automatyczne środowisko aprowizacji [systemu Windows](/mem/autopilot/windows-autopilot) .

    -   Przestarzałe maszyny z systemem Windows 8.1 i wcześniejszymi wersjami.

    -   Nie należy wdrażać maszyn systemu operacyjnego serwera jako stacji roboczych.

    -   Użyj [Microsoft Intune](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/microsoft-intune) jako źródła urzędu dla wszystkich obciążeń związanych z zarządzaniem urządzeniami.

-   [**Wdróż uprzywilejowane urządzenia dostępu**](/security/compass/privileged-access-devices#device-roles-and-profiles): Użyj uprzywilejowanego dostępu, aby zarządzać Microsoft 365 i usługą Azure AD.

## <a name="workloads-applications-and-resources"></a>Obciążenia, aplikacje i zasoby 

-   **Lokalne systemy logowania jednokrotnego (SSO)** 

    Zaniechania infrastruktury zarządzania federacyjnymi i dostępnymi w sieci Web. Skonfiguruj aplikacje do korzystania z usługi Azure AD.  

-   **SaaS i aplikacje biznesowe (LOB) obsługujące nowoczesne protokoły uwierzytelniania** 

    [Użyj usługi Azure AD na potrzeby logowania jednokrotnego](../manage-apps/what-is-single-sign-on.md). Im więcej aplikacji skonfigurowanych do korzystania z usługi Azure AD na potrzeby uwierzytelniania, tym mniejsze ryzyko związane z bezpieczeństwem lokalnym.


* **Starsze aplikacje** 

   * Można włączyć uwierzytelnianie, autoryzację i dostęp zdalny do starszych aplikacji, które nie obsługują nowoczesnego uwierzytelniania. Użyj [usługi Azure serwer proxy aplikacji usługi Azure AD](../manage-apps/application-proxy.md). Można je również włączyć za pośrednictwem rozwiązań sieciowych lub kontroler dostarczania aplikacji, używając [bezpiecznych integracji partnera dostępu hybrydowego](../manage-apps/secure-hybrid-access.md).   

   * Wybierz dostawcę sieci VPN, który obsługuje nowoczesne uwierzytelnianie. Zintegruj swoje uwierzytelnianie z usługą Azure AD. W przypadku naruszenia bezpieczeństwa lokalnego można użyć usługi Azure AD w celu wyłączenia lub zablokowania dostępu przez wyłączenie sieci VPN.

*  **Serwery aplikacji i obciążenia**

   * Aplikacje lub zasoby, które wymagane serwery można migrować do infrastruktury platformy Azure jako usługi (IaaS). Użyj [Azure AD Domain Services](../../active-directory-domain-services/overview.md) (AD DS platformy Azure), aby rozdzielić zaufanie i zależność od lokalnych wystąpień Active Directory. Aby osiągnąć to oddzielenie, upewnij się, że sieci wirtualne używane na platformie Azure AD DS nie mają połączenia z sieciami firmowymi.

   * Postępuj zgodnie ze wskazówkami dotyczącymi obsługi [warstw poświadczeń](/security/compass/privileged-access-access-model#ADATM_BM). Serwery aplikacji są zwykle uznawane za zasoby warstwy 1.

## <a name="conditional-access-policies"></a>Zasady dostępu warunkowego

Użyj dostępu warunkowego usługi Azure AD, aby interpretować sygnały i używać ich do podejmowania decyzji dotyczących uwierzytelniania. Aby uzyskać więcej informacji, zobacz [plan wdrożenia dostępu warunkowego](../conditional-access/plan-conditional-access.md).

* Użyj dostępu warunkowego, aby [zablokować starsze protokoły uwierzytelniania](../conditional-access/howto-conditional-access-policy-block-legacy.md) , gdy jest to możliwe. Ponadto należy wyłączyć starsze protokoły uwierzytelniania na poziomie aplikacji przy użyciu konfiguracji specyficznej dla aplikacji.

   Aby uzyskać więcej informacji, zobacz [starsze protokoły uwierzytelniania](../fundamentals/auth-sync-overview.md). Lub Zobacz szczegółowe informacje dotyczące [usługi Exchange Online](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) i [SharePoint Online](/powershell/module/sharepoint-online/set-spotenant).

* Zaimplementuj zalecane [konfiguracje i dostęp do urządzeń](/microsoft-365/security/office-365-security/identity-access-policies).

* Jeśli używasz wersji usługi Azure AD, która nie zawiera dostępu warunkowego, upewnij się, że używasz [domyślnych ustawień zabezpieczeń usługi Azure AD](../fundamentals/concept-fundamentals-security-defaults.md).

   Aby uzyskać więcej informacji na temat licencjonowania funkcji usługi Azure AD, zobacz [Przewodnik po cenach usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="monitor"></a>Monitor 

Po skonfigurowaniu środowiska do ochrony Microsoft 365 z poziomu naruszenia bezpieczeństwa lokalnego należy [aktywnie monitorować](../reports-monitoring/overview-monitoring.md) środowisko.
### <a name="scenarios-to-monitor"></a>Scenariusze do monitorowania

Monitoruj poniższe scenariusze, oprócz wszelkich scenariuszy specyficznych dla Twojej organizacji. Na przykład należy aktywnie monitorować dostęp do aplikacji i zasobów o kluczowym znaczeniu dla firmy.

* **Podejrzane działania** 

    Monitoruj wszystkie [zdarzenia dotyczące ryzyka związane z usługą Azure AD](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation) w przypadku podejrzanych działań. [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) jest natywnie zintegrowany z Azure Security Center.

    Zdefiniuj Sieć [o nazwie lokalizacje](../reports-monitoring/quickstart-configure-named-locations.md) , aby uniknąć wykrywania szumów w przypadku sygnałów opartych na lokalizacji. 
*  **Alerty analizy zachowań użytkowników i jednostek (UEBA)** 

    Użyj UEBA, aby uzyskać szczegółowe informacje o wykrywaniu anomalii.
    * Microsoft Cloud App Security (MCAS) zapewnia [UEBA w chmurze](/cloud-app-security/tutorial-ueba).

    * Możesz [zintegrować lokalne UEBA z usługą Azure Advanced Threat Protection (ATP)](/defender-for-identity/install-step2). MCAS odczytuje sygnały z Azure AD Identity Protection. 

* **Działanie kont dostępu awaryjnego** 

    Monitoruj dowolny dostęp, który używa [kont dostępu awaryjnego](../roles/security-emergency-access.md). Utwórz alerty dla badań. Monitorowanie musi obejmować: 

   * Logowania. 

   * Zarządzanie poświadczeniami. 

   * Wszystkie aktualizacje członkostwa w grupach. 

   * Przypisania aplikacji. 
* **Działanie uprzywilejowane roli**

    Skonfiguruj i przejrzyj [alerty zabezpieczeń wygenerowane przez Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-how-to-configure-security-alerts.md?tabs=new#security-alerts).
    Monitoruj bezpośrednie przypisanie uprzywilejowanych ról poza usługą PIM przez generowanie alertów za każdym razem, gdy użytkownik jest przypisany bezpośrednio.

* **Konfiguracje dla całej dzierżawy usługi Azure AD**

    Wszystkie zmiany w konfiguracjach w całej dzierżawie powinny generować alerty w systemie. Te zmiany obejmują między innymi:

  *  Zaktualizowano domeny niestandardowe.  

  * Zmiany w usłudze Azure AD B2B w allowlists i listy blokowania adresów.

  * Zmiany w usłudze Azure AD B2B na dozwolonych dostawców tożsamości (dostawcy tożsamości SAML za pośrednictwem bezpośredniej Federacji lub społecznościowych).  

  * Zmiany dostępu warunkowego lub zasad ryzyka. 

* **Application and service principal objects (Obiekty aplikacji i jednostki usługi)**
    
   * Nowe aplikacje lub jednostki usługi, które mogą wymagać zasad dostępu warunkowego. 

   * Dodano poświadczenia do podmiotów usługi.
   * Działanie zgody aplikacji. 

* **Role niestandardowe**
   * Aktualizuje definicje ról niestandardowych. 

   * Nowo utworzone role niestandardowe. 

### <a name="log-management"></a>Zarządzanie dziennikami

Zdefiniuj magazyn dzienników i strategię przechowywania, projektowanie i implementację, aby ułatwić spójne Ustawianie zestawu narzędzi. Można na przykład rozważyć systemy zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM), takie jak Azure, typowe zapytania i badania oraz dowodowych elementy PlayBook.

* **Dzienniki usługi Azure AD**: pozyskiwanie wygenerowanych dzienników i sygnałów przez spójne rozwiązanie dla ustawień takich jak diagnostyka, przechowywanie dzienników i pozyskiwanie Siem. 

    Strategia dziennika musi zawierać następujące dzienniki usługi Azure AD:
   * Aktywność związana z logowaniem 

   * Dzienniki inspekcji 

   * Zdarzenia o podwyższonym ryzyku 

    Usługa Azure AD zapewnia [Azure monitor integrację](../reports-monitoring/concept-activity-logs-azure-monitor.md) z dziennikami aktywności logowania i dziennikami inspekcji. Zdarzenia o podwyższonym ryzyku można uzyskać za pomocą [interfejsu API Microsoft Graph](/graph/api/resources/identityriskevent). Dzienniki usługi [Azure AD można przesyłać strumieniowo do dzienników Azure monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

* **Dzienniki zabezpieczeń systemu operacyjnego infrastruktury hybrydowej**: wszystkie dzienniki systemu operacyjnego infrastruktury tożsamości hybrydowej powinny być archiwizowane i starannie monitorowane jako system warstwy 0 z powodu implikacji obszaru powierzchni. Uwzględnij następujące elementy: 

   *  Azure AD Connect. Aby monitorować synchronizację tożsamości, należy wdrożyć [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md) .

   *  Agenci serwera proxy aplikacji 


   * Agenci zapisywania zwrotnego haseł 

   * Maszyny bramy ochrony hasła  

   * Serwery zasad sieciowych (NPSs) z rozszerzeniem usługi RADIUS uwierzytelniania wieloskładnikowego usługi Azure AD 

## <a name="next-steps"></a>Następne kroki
* [Tworzenie odporności na zarządzanie tożsamościami i dostępem przy użyciu usługi Azure AD](resilience-overview.md)

* [Zabezpieczanie dostępu zewnętrznego do zasobów](secure-external-access-resources.md) 
* [Integruj wszystkie aplikacje z usługą Azure AD](five-steps-to-full-application-integration-with-azure-ad.md)