---
title: Ochrona Microsoft 365 z wykorzystaniem lokalnych ataków
description: Wskazówki dotyczące zapewnienia, że ataku lokalnego nie ma wpływu na Microsoft 365
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
ms.openlocfilehash: 71d64552520db28b3fe38cc354a30ee5470ee091
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060738"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>Ochrona Microsoft 365 z wykorzystaniem lokalnych ataków

Wielu klientów nawiązuje połączenie prywatnych sieci firmowych w celu Microsoft 365 uzyskania korzyści dla użytkowników, urządzeń i aplikacji. Istnieje jednak wiele dobrze udokumentowanych sposobów zabezpieczenia tych sieci prywatnych. Ponieważ Microsoft 365 działa jako "system nerwowy" dla wielu organizacji, ma kluczowe znaczenie dla ochrony przed naruszeniem infrastruktury lokalnej.

W tym artykule opisano sposób konfigurowania systemów w celu ochrony środowiska chmury Microsoft 365 z poziomu bezpieczeństwa lokalnego. Przede wszystkim skupiamy się na ustawieniach konfiguracji dzierżawy usługi Azure AD, jak można bezpiecznie połączyć dzierżawy usługi Azure AD z systemami lokalnymi oraz kompromisy wymagane do obsługi systemów w sposób chroniący systemy w chmurze przed naruszeniem lokalnym.

Zdecydowanie zalecamy zaimplementowanie tych wskazówek w celu zabezpieczenia środowiska chmury Microsoft 365.
> [!NOTE]
> Ten artykuł został początkowo opublikowany jako wpis w blogu. Została przeniesiona tutaj do eksploatacji i konserwacji. <br>
Aby utworzyć wersję w trybie offline tego artykułu, użyj funkcji drukowania do formatu PDF w przeglądarce. Sprawdź tutaj często aktualizacje.

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>Podstawowe wektory zagrożeń ze złamanych środowisk lokalnych


Środowisko chmury Microsoft 365 korzyści z rozbudowanej infrastruktury monitorowania i zabezpieczeń. Korzystanie z uczenia maszynowego i analizy ludzkiej, która przegląda cały ruch na całym świecie, może szybko wykrywać ataki i umożliwiać ponowną konfigurację w czasie niemal rzeczywistym. W przypadku wdrożeń hybrydowych, które łączą się z infrastrukturą lokalną w celu Microsoft 365, wiele organizacji delegowanie zaufania do składników lokalnych w celu uwierzytelniania krytycznego i podejmowania decyzji dotyczących zarządzania stanem obiektu katalogu.
Niestety, jeśli środowisko lokalne zostanie naruszone, te relacje zaufania powodują, że użytkownicy atakujący mogą złamać środowisko Microsoft 365.

Dwa podstawowe wektory zagrożeń to **relacje zaufania federacji** i **Synchronizacja konta.** Oba wektory mogą udzielić atakującemu dostępu administracyjnego do chmury.

* **Federacyjne relacje zaufania**, takie jak uwierzytelnianie SAML, są używane do uwierzytelniania Microsoft 365 za pośrednictwem lokalnej infrastruktury tożsamości. Jeśli certyfikat podpisywania tokenu SAML zostanie naruszony, Federacja zezwoli każdemu użytkownikowi tego certyfikatu na personifikację dowolnego użytkownika w chmurze. **Zalecamy wyłączenie relacji zaufania federacji na potrzeby uwierzytelniania, aby Microsoft 365, gdy jest to możliwe.**

* **Synchronizacja konta** może służyć do modyfikowania użytkowników uprzywilejowanych (łącznie z ich poświadczeniami) lub grup, którym przyznano uprawnienia administracyjne w Microsoft 365. **Zalecamy upewnienie się, że zsynchronizowane obiekty nie przechowują żadnych uprawnień poza użytkownikiem w Microsoft 365,** bezpośrednio lub przez dołączenie w zaufanych rolach lub grupach. Upewnij się, że te obiekty nie mają bezpośredniego lub zagnieżdżonego przypisania w zaufanych rolach lub grupach w chmurze.

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>Ochrona Microsoft 365 z poziomu naruszenia lokalnego


Aby rozwiązać opisane powyżej wektory zagrożeń, zalecamy przestrzeganie zasad przedstawionych poniżej:

![Architektura referencyjna do ochrony Microsoft 365 ](media/protect-m365/protect-m365-principles.png)

*  **W pełni Izoluj konta administratorów Microsoft 365.** Powinny być

    * W usłudze Azure AD.

     * Uwierzytelnianie za pomocą uwierzytelniania wieloskładnikowego (MFA).

     *  Zabezpieczony przez dostęp warunkowy usługi Azure AD.

     *  Dostępne tylko przy użyciu zarządzanych stacji roboczych platformy Azure.

Są to konta z ograniczeniami użycia. **W Microsoft 365 nie powinno być kont lokalnych z uprawnieniami administracyjnymi.** Aby uzyskać więcej informacji, zobacz ten [Przegląd ról administratorów Microsoft 365](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide).
Zobacz też [role Microsoft 365 w Azure Active Directory](../roles/m365-workload-docs.md).

*  **Zarządzanie urządzeniami z poziomu Microsoft 365.** Za pomocą funkcji dołączania do usługi Azure AD i zarządzania urządzeniami przenośnymi (MDM) w chmurze można wyeliminować zależności od lokalnej infrastruktury zarządzania urządzeniami, które mogą naruszyć kontrolę nad urządzeniami i zabezpieczeniami.

* **Żadne konto lokalne nie ma podniesionych uprawnień do Microsoft 365.**
    Konta z dostępem do aplikacji lokalnych, które wymagają uwierzytelniania NTLM, LDAP lub Kerberos, muszą mieć konto w lokalnej infrastrukturze tożsamości w organizacji. Upewnij się, że te konta, w tym konta usług, nie są uwzględnione w uprzywilejowanych rolach lub grupach w chmurze i że zmiany tych kont nie wpłyną na integralność środowiska chmury. Uprzywilejowane oprogramowanie lokalne nie może wpływać na Microsoft 365 uprzywilejowanych kont lub ról.

*  **Użyj uwierzytelniania w chmurze usługi Azure AD** , aby wyeliminować zależności dotyczące poświadczeń lokalnych. Zawsze używaj silnego uwierzytelniania, takich jak Windows Hello, FIDO, Microsoft Authenticator lub Azure AD MFA.

## <a name="specific-recommendations"></a>Konkretne zalecenia


Poniższe sekcje zawierają szczegółowe wskazówki dotyczące sposobu wdrażania zasad opisanych powyżej.

### <a name="isolate-privileged-identities"></a>Izolowanie tożsamości uprzywilejowanych


W usłudze Azure AD użytkownicy z rolami uprzywilejowanymi, takimi jak Administratorzy, są głównymi zaufania do kompilowania reszty środowiska i zarządzania nim. Zaimplementuj poniższe praktyki, aby zminimalizować wpływ naruszenia.

* Używaj kont tylko w chmurze dla usługi Azure AD i ról uprzywilejowanych Microsoft 365. d

* Wdróż [uprzywilejowane urządzenia dostępu](https://docs.microsoft.com/security/compass/privileged-access-devices#device-roles-and-profiles) dla uprzywilejowanego dostępu do zarządzania Microsoft 365 i usługi Azure AD.

*  Wdróż [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) dla dostępu just in Time (JIT) do wszystkich kont ludzkich, które mają uprzywilejowane role, i wymagaj silnego uwierzytelniania, aby aktywować role.

* Podaj role administracyjne o [najniższych uprawnieniach, które można wykonać w celu wykonania swoich zadań](../roles/delegate-by-task.md).

* Aby włączyć bardziej zaawansowane środowisko przypisywania ról, które obejmuje delegowanie i wiele ról w tym samym czasie, należy rozważyć użycie grup zabezpieczeń usługi Azure AD lub grup Microsoft 365 (grup w chmurze) i [włączenie kontroli dostępu opartej na rolach](../roles/groups-assign-role.md). Za pomocą [jednostek administracyjnych](../roles/administrative-units.md) można także ograniczyć zakres ról do części organizacji.

* Wdróż [konta dostępu awaryjnego](../roles/security-emergency-access.md) i nie używaj lokalnych magazynów haseł do przechowywania poświadczeń.

Aby uzyskać więcej informacji, zobacz [Zabezpieczanie uprzywilejowanego dostępu](https://aka.ms/SPA), który ma szczegółowe wskazówki dotyczące tego tematu. Należy również zapoznać się z tematami [dotyczącymi bezpiecznego dostępu dla administratorów w usłudze Azure AD](../roles/security-planning.md).

### <a name="use-cloud-authentication"></a>Korzystanie z uwierzytelniania w chmurze 

Poświadczenia są podstawowym wektorem ataków. Aby zapewnić bezpieczeństwo poświadczeń, należy zaimplementować poniższe zalecenia.

* [Wdróż uwierzytelnianie bezchronione hasłem](../authentication/howto-authentication-passwordless-deployment.md): Zmniejsz użycie haseł tak często, jak to możliwe, wdrażając poświadczenia bezhaseł. Te poświadczenia są zarządzane i weryfikowane natywnie w chmurze. Wybierz spośród opcji:

   * [Funkcja Windows Hello dla firm](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [Aplikacja Authenticator](../authentication/howto-authentication-passwordless-phone.md)

   * [FIDO2 klucze zabezpieczeń](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [Wdróż Multi-Factor Authentication](https://aka.ms/deploymentplans/mfa): Zainicjuj obsługę [wielu silnych poświadczeń przy użyciu usługi Azure AD MFA](../fundamentals/resilience-in-credentials.md). Dzięki temu dostęp do zasobów w chmurze będzie wymagał poświadczeń, które są zarządzane w usłudze Azure AD, a także hasła lokalnego, które można manipulować.

   * Aby uzyskać więcej informacji, zobacz [Tworzenie elastycznej strategii zarządzania kontroli dostępu w usłudze Azure Active Directory](https://aka.ms/resilientaad).

**Ograniczenia i kompromisy**

* Zarządzanie hasłami do konta hybrydowego wymaga składników hybrydowych, takich jak agenci ochrony haseł i agenci zapisywania zwrotnego haseł. Jeśli infrastruktura lokalna zostanie naruszona, osoby atakujące mogą kontrolować komputery, na których znajdują się agenci. Chociaż nie spowoduje to naruszenia infrastruktury chmurowej, konta w chmurze nie będą chronić tych składników przed naruszeniem lokalnym.

*  Konta lokalne zsynchronizowane z Active Directory są oznaczone jako niewygasające w usłudze Azure AD, w oparciu o założenie, że lokalne zasady haseł usługi AD spowodują rozwiązanie tego problemu. Jeśli nastąpi naruszenie zabezpieczeń lokalnej usługi AD, a synchronizacja z programu AD Connect musi być wyłączona, należy ustawić opcję [EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="provision-user-access-from-the-cloud"></a>Inicjowanie dostępu użytkowników z chmury

Inicjowanie obsługi odnosi się do tworzenia kont użytkowników i grup w aplikacjach lub dostawcach tożsamości.

![Diagram architektury aprowizacji](media/protect-m365/protect-m365-provision.png)

* **Udostępnianie aplikacji w chmurze w usłudze Azure AD:** Pozwala to na odizolowanie lokalnego kompromisu bez zakłócania pracy łącznika — od aplikacji KADRowych w chmurze do usługi Azure AD.

* **Aplikacje w chmurze:** Jeśli to możliwe, wdróż [aplikacja usługi Azure AD aprowizacji](../app-provisioning/user-provisioning.md) w przeciwieństwie do lokalnych rozwiązań aprowizacji. Spowoduje to ochronę niektórych aplikacji SaaS przed zatruciem ze złośliwymi profilami użytkowników z powodu naruszeń lokalnych. 

* **Tożsamości zewnętrzne:** Użyj [funkcji współpracy B2B usługi Azure AD](../external-identities/what-is-b2b.md).
    Pozwoli to ograniczyć zależność od kont lokalnych do współpracy zewnętrznej z partnerami, klientami i dostawcami. Starannie Oceń każdą bezpośrednią Federację z innymi dostawcami tożsamości. Zalecamy ograniczenie kont gościa B2B w następujący sposób.

   *  Ogranicz dostęp gościa do grup przeglądania i innych właściwości w katalogu. Użyj zewnętrznych ustawień współpracy, aby ograniczyć możliwość odczytywania grup, których nie są członkami. 

    *   Blokuj dostęp do Azure Portal. Można dokonać rzadkich niepotrzebnych wyjątków.  Utwórz zasady dostępu warunkowego, które obejmują wszystkich Gości i użytkowników zewnętrznych, a następnie [Zaimplementuj zasady w celu zablokowania dostępu](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management.md). 

* **Odłączone lasy:** Korzystanie z [aprowizacji w chmurze usługi Azure AD](../cloud-provisioning/what-is-cloud-provisioning.md). Pozwala to na łączenie się z odłączonymi lasami, eliminując konieczność ustanowienia łączności między lasami lub relacji zaufania, co może rozszerzyć wpływ naruszenia lokalnego. * 
 
**Ograniczenia i wady:**

* Gdy jest używany do aprowizacji kont hybrydowych, usługa Azure AD z systemu kadr w chmurze korzysta z synchronizacji lokalnej do kończenia przepływu danych z usługi AD do usługi Azure AD. Jeśli synchronizacja zostanie przerwana, nowe rekordy pracowników nie będą dostępne w usłudze Azure AD.

## <a name="use-cloud-groups-for-collaboration-and-access"></a>Korzystanie z grup w chmurze w celu współpracy i dostępu

Grupy chmury umożliwiają rozdzielenie swojej współpracy i dostępu z infrastruktury lokalnej.

* **Współpraca:** Korzystaj z grup Microsoft 365 i Microsoft Teams w celu uzyskania nowoczesnej współpracy. Likwidowanie lokalnych list dystrybucyjnych i [uaktualnianie list dystrybucyjnych do grup Microsoft 365 w programie Outlook](https://docs.microsoft.com/office365/admin/manage/upgrade-distribution-lists?view=o365-worldwide).

* **Dostęp:** Użyj grup zabezpieczeń usługi Azure AD lub grup Microsoft 365, aby autoryzować dostęp do aplikacji w usłudze Azure AD.
* **Licencjonowanie pakietu Office 365:** Użyj licencjonowania opartego na grupach, aby udostępnić pakiet Office 365 przy użyciu grup opartych tylko na chmurze. Powoduje to oddzielenie kontroli nad członkostwem w grupach z infrastruktury lokalnej.

Właściciele grup używanych do uzyskiwania dostępu powinni być uznawani za tożsamości uprzywilejowane, aby uniknąć przejęcia członkostwa z zasobów lokalnych.
Przejmowanie obejmuje bezpośrednie manipulowanie członkostwem w grupie lokalnie lub manipulowanie atrybutami lokalnymi, które mogą mieć wpływ na członkostwo w grupie dynamicznej w Microsoft 365.

## <a name="manage-devices-from-the-cloud"></a>Zarządzanie urządzeniami z chmury


Funkcje usługi Azure AD umożliwiają bezpieczne zarządzanie urządzeniami.

-   **Korzystanie z stacji roboczych z systemem Windows 10:** [wdrażanie urządzeń przyłączonych do usługi Azure AD](../devices/azureadjoin-plan.md) przy użyciu zasad MDM. Włącz automatyczne środowisko aprowizacji [systemu Windows](https://docs.microsoft.com/mem/autopilot/windows-autopilot) .

    -   Zaniechanie Windows 8.1 i wcześniejszych maszyn.

    -   Nie należy wdrażać maszyn systemu operacyjnego serwera jako stacji roboczych.

    -   Użyj [Microsoft Intune](https://www.microsoft.com/en/microsoft-365/enterprise-mobility-security/microsoft-intune) jako źródła autoryzacji wszystkich obciążeń związanych z zarządzaniem urządzeniami.

-   [**Wdróż uprzywilejowane urządzenia dostępu**](https://docs.microsoft.com/security/compass/privileged-access-devices#device-roles-and-profiles) dla uprzywilejowanego dostępu do zarządzania Microsoft 365 i usługi Azure AD.

 ## <a name="workloads-applications-and-resources"></a>Obciążenia, aplikacje i zasoby 

-   **Lokalne systemy rejestracji jednokrotnej:** Należy zaniechać infrastruktury zarządzania lokalnego i usługi Dostęp w sieci Web i skonfigurować aplikacje do korzystania z usług Azure AD.  

-   **SaaS i aplikacje LOB obsługujące nowoczesne protokoły uwierzytelniania:** [Użyj usługi Azure AD do logowania jednokrotnego](../manage-apps/what-is-single-sign-on.md). Im więcej aplikacji skonfigurowanych do korzystania z usługi Azure AD do uwierzytelniania, tym mniejsze ryzyko w przypadku naruszenia bezpieczeństwa lokalnego.


* **Starsze aplikacje** 

   * Uwierzytelnianie, autoryzacja i dostęp zdalny do starszych aplikacji, które nie obsługują nowoczesnego uwierzytelniania, można włączyć za pośrednictwem [platformy Azure serwer proxy aplikacji usługi Azure AD](../manage-apps/application-proxy.md). Można je również włączyć za pośrednictwem rozwiązania sieciowego lub kontrolera dostarczania aplikacji przy użyciu  [bezpiecznych integracji partnera dostępu hybrydowego](../manage-apps/secure-hybrid-access.md).   

   * Wybierz dostawcę sieci VPN, który obsługuje nowoczesne uwierzytelnianie i Zintegruj jego uwierzytelnianie z usługą Azure AD. W przypadku złamania zabezpieczeń Anon, możesz użyć usługi Azure AD, aby wyłączyć lub zablokować dostęp poprzez wyłączenie sieci VPN.

*  **Serwery aplikacji i obciążenia**

   * Aplikacje lub zasoby, które są wymagane przez serwery, można migrować do usługi Azure IaaS i użyć [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) (AD DS platformy Azure), aby oddzielić zaufanie i zależność od lokalnego usługi AD. Aby osiągnąć to oddzielenie, sieci wirtualne używane na potrzeby usługi Azure AD DS nie powinny mieć połączenia z sieciami firmowymi.

   * Postępuj zgodnie ze wskazówkami dotyczącymi obsługi [warstw poświadczeń](https://aka.ms/TierModel). Serwery aplikacji są zwykle uznawane za zasoby warstwy 1.

 ## <a name="conditional-access-policies"></a>Zasady dostępu warunkowego

Użyj dostępu warunkowego usługi Azure AD w celu interpretacji sygnałów i podejmowania decyzji dotyczących uwierzytelniania na podstawie tych zdarzeń. Aby uzyskać więcej informacji, zobacz [plan wdrożenia dostępu warunkowego.](https://aka.ms/deploymentplans/ca)

* [Starsze protokoły uwierzytelniania](../fundamentals/auth-sync-overview.md): Użyj dostępu warunkowego, aby [blokować starsze protokoły uwierzytelniania](../conditional-access/howto-conditional-access-policy-block-legacy.md) zawsze wtedy, gdy jest to możliwe. Ponadto należy wyłączyć starsze protokoły uwierzytelniania na poziomie aplikacji przy użyciu konfiguracji specyficznej dla aplikacji.

   * Zobacz szczegółowe informacje dotyczące [usługi Exchange Online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) i [SharePoint Online](https://docs.microsoft.com/powershell/module/sharepoint-online/set-spotenant?view=sharepoint-ps).

* Zaimplementuj zalecane [konfiguracje i dostęp do urządzeń.](https://docs.microsoft.com/microsoft-365/security/office-365-security/identity-access-policies?view=o365-worldwide)

* Jeśli używasz wersji usługi Azure AD, która nie obejmuje dostępu warunkowego, upewnij się, że używasz [domyślnych ustawień zabezpieczeń usługi Azure AD](../fundamentals/concept-fundamentals-security-defaults.md).

   * Aby uzyskać więcej informacji na temat licencjonowania funkcji usługi Azure AD, zobacz [Przewodnik po cenach usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="monitoring"></a>Monitorowanie 

Po skonfigurowaniu środowiska do ochrony Microsoft 365 z poziomu naruszenia bezpieczeństwa lokalnego należy [aktywnie monitorować](../reports-monitoring/overview-monitoring.md) środowisko.
### <a name="scenarios-to-monitor"></a>Scenariusze do monitorowania

Monitoruj poniższe scenariusze, oprócz wszelkich scenariuszy specyficznych dla Twojej organizacji. Na przykład należy aktywnie monitorować dostęp do aplikacji i zasobów o kluczowym znaczeniu dla firmy.

* **Podejrzane działanie**: wszystkie [zdarzenia dotyczące ryzyka związane z usługą Azure AD](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection#risk-detection-and-remediation) powinny być monitorowane w przypadku podejrzanych działań. [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) jest natywnie zintegrowany z Azure Security Center.

   * Zdefiniuj Sieć [o nazwie lokalizacje](../reports-monitoring/quickstart-configure-named-locations.md) , aby uniknąć wykrywania szumów w przypadku sygnałów opartych na lokalizacji. 
*  **Alerty analizy zachowań jednostek użytkownika (UEBA)** Użyj UEBA, aby uzyskać szczegółowe informacje o wykrywaniu anomalii.
   * Usługa Microsoft Cloud App Discovery (MCAS) zapewnia [UEBA w chmurze](https://docs.microsoft.com/cloud-app-security/tutorial-ueba).

   * Możesz [zintegrować lokalne UEBA z usługi Azure ATP](https://docs.microsoft.com/defender-for-identity/install-step2). MCAS odczytuje sygnały z Azure AD Identity Protection. 

* **Działania dotyczące kont dostępu awaryjnego**: każdy dostęp przy użyciu [kont dostępu awaryjnego](../roles/security-emergency-access.md) powinien być monitorowany, a alerty są tworzone na potrzeby badań. Monitorowanie musi obejmować: 

   * Logowania. 

   * Zarządzanie poświadczeniami. 

   * Wszystkie aktualizacje członkostwa w grupach. 

   *    Przypisania aplikacji. 
* **Działanie Privileged role**: Konfigurowanie i przeglądanie [alertów zabezpieczeń wygenerowanych przez usługę Azure AD PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-configure-security-alerts?tabs=new#security-alerts).
    Monitoruj bezpośrednie przypisanie uprzywilejowanych ról poza usługą PIM przez generowanie alertów za każdym razem, gdy użytkownik jest przypisany bezpośrednio.
* **Konfiguracje dla całej dzierżawy usługi Azure AD**: Każda zmiana konfiguracji w całej dzierżawie powinna generować alerty w systemie. Należą do nich, ale nie są ograniczone do
  *  Aktualizowanie domen niestandardowych  

  * Zmiany listy dozwolonych/zablokowanych w usłudze Azure AD B2B.
  * Dozwolone dostawcy tożsamości usługi Azure AD B2B (SAML dostawców tożsamości za pośrednictwem bezpośredniej Federacji lub logowania społecznego).  
  * Zmiany dostępu warunkowego lub zasad ryzyka 

* **Obiekty główne aplikacji i usługi**:
   * Nowe aplikacje lub jednostki usługi, które mogą wymagać zasad dostępu warunkowego. 

   * Dodano dodatkowe poświadczenia do podmiotów usługi.
   * Działanie zgody aplikacji. 

* **Role niestandardowe**:
   * Aktualizacje definicji ról niestandardowych. 

   * Utworzono nowe role niestandardowe. 

### <a name="log-management"></a>Zarządzanie dziennikami

Zdefiniuj magazyn dzienników i strategię przechowywania, projekt i implementację, aby ułatwić spójny zestaw narzędzi, taki jak systemy SIEM, takie jak platformy Azure, typowe zapytania i badania oraz dowodowych elementy PlayBook.

* **Dzienniki usługi Azure AD** Pobieranie dzienników i sygnałów przy zachowaniu spójnych najlepszych rozwiązań, w tym ustawień diagnostycznych, przechowywania dzienników i pozyskiwania SIEM. Strategia dziennika musi zawierać następujące dzienniki usługi Azure AD:
   * Aktywność związana z logowaniem 

   * Dzienniki inspekcji 

   * Zdarzenia o podwyższonym ryzyku 

Usługa Azure AD zapewnia [Azure monitor integrację](../reports-monitoring/concept-activity-logs-azure-monitor.md) z dziennikami aktywności logowania i dziennikami inspekcji. Zdarzenia o podwyższonym ryzyku można pozyskać za poorednictwem [Microsoft Graph interfejsu API](https://aka.ms/AzureADSecuredAzure/32b). Dzienniki usługi [Azure AD można przesyłać strumieniowo do dzienników usługi Azure monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

* **Dzienniki zabezpieczeń systemu operacyjnego infrastruktury hybrydowej.** Wszystkie dzienniki systemu operacyjnego infrastruktury tożsamości hybrydowej powinny być archiwizowane i starannie monitorowane jako <br>System warstwy 0 ma wpływ na powierzchnię. Obejmuje to następujące działania: 

   *  Azure AD Connect. Aby monitorować synchronizację tożsamości, należy wdrożyć [Azure AD Connect Health](https://aka.ms/AzureADSecuredAzure/32e) .

   *  Agenci serwera proxy aplikacji 


   * Agenci zapisywania zwrotnego haseł 

   * Maszyny bramy ochrony hasła  

   * Serwer NPS z rozszerzeniem usługi RADIUS Azure MFA 

## <a name="next-steps"></a>Następne kroki
* [Tworzenie odporności na zarządzanie tożsamościami i dostępem za pomocą usługi Azure AD](resilience-overview.md)

* [Zabezpieczanie dostępu zewnętrznego do zasobów](secure-external-access-resources.md) 
* [Integruj wszystkie aplikacje z usługą Azure AD](five-steps-to-full-application-integration-with-azure-ad.md)
