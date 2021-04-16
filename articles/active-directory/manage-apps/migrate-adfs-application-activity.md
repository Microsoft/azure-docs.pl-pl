---
title: Użyj raportu aktywności, aby przenieść aplikacje AD FS do Azure Active Directory | Microsoft Docs"
description: Raport Active Directory Federation Services (AD FS) aplikacji umożliwia szybką migrację aplikacji z usługi AD FS do Azure Active Directory (Azure AD). To narzędzie do migracji AD FS zgodność z usługą Azure AD i zapewnia wskazówki dotyczące migracji.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 01/14/2019
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b9265a61b0879078332b8ccc2d10e711b4ac8f1
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377424"
---
# <a name="use-the-ad-fs-application-activity-report-to-migrate-applications-to-azure-ad"></a>Migrowanie aplikacji AD FS do usługi Azure AD przy użyciu raportu aktywności aplikacji

Wiele organizacji używa Active Directory Federation Services (AD FS) do zapewnienia logowania pojedynczego do aplikacji w chmurze. Przenoszenie aplikacji mobilnych do usługi Azure AD na AD FS w celu uwierzytelniania przynosi znaczące korzyści, szczególnie w zakresie zarządzania kosztami, zarządzania ryzykiem, produktywności, zgodności i ładu. Jednak zrozumienie, które aplikacje są zgodne z usługą Azure AD i identyfikowanie określonych kroków migracji, może być czasochłonne.

Raport AD FS aktywności aplikacji w Azure Portal pozwala szybko zidentyfikować, które aplikacje mogą być migrowane do usługi Azure AD. Ocenia wszystkie aplikacje AD FS pod kątem zgodności z usługą Azure AD, sprawdza wszelkie problemy i zapewnia wskazówki dotyczące przygotowywania poszczególnych aplikacji do migracji. Raport aktywności AD FS aplikacji umożliwia:

* **Odnajdywanie AD FS aplikacji i zakres migracji.** Raport AD FS aktywności aplikacji zawiera listę wszystkich AD FS aplikacji w organizacji, które miały aktywne logowanie użytkownika w ciągu ostatnich 30 dni. Raport wskazuje gotowość aplikacji do migracji do usługi Azure AD. W raporcie nie są wyświetlane jednostki zależne powiązane z firmą Microsoft w usługach AD FS, takich jak Office 365. Na przykład jednostki zależne o nazwie "urn:federation:MicrosoftOnline".

* **Określanie priorytetów aplikacji do migracji.** Uzyskaj liczbę unikatowych użytkowników, którzy zalogowali się do aplikacji w ciągu ostatnich 1, 7 lub 30 dni, aby ułatwić określenie krytyczności lub ryzyka migracji aplikacji.
* **Uruchamianie testów migracji i rozwiązywanie problemów.** Usługa raportowania automatycznie uruchamia testy w celu określenia, czy aplikacja jest gotowa do migracji. Wyniki są wyświetlane w raporcie AD FS aplikacji jako stan migracji. Jeśli konfiguracja AD FS nie jest zgodna z konfiguracją usługi Azure AD, możesz uzyskać konkretne wskazówki dotyczące sposobu rozwiązania tej konfiguracji w usłudze Azure AD.

Dane AD FS aplikacji są dostępne dla użytkowników, którym przypisano dowolną z tych ról administratora: administratora globalnego, czytelnika raportów, czytelnika zabezpieczeń, administratora aplikacji lub administratora aplikacji w chmurze.

## <a name="prerequisites"></a>Wymagania wstępne

* Twoja organizacja musi obecnie używać usługi AD FS do uzyskiwania dostępu do aplikacji.
* Azure AD Connect Health musi być włączona w dzierżawie usługi Azure AD.
* Należy Azure AD Connect Health agenta AD FS agenta.
   * [Dowiedz się więcej o Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md)
   * [Wprowadzenie do konfigurowania Azure AD Connect Health i instalowania agenta AD FS](../hybrid/how-to-connect-health-agent-install.md)

>[!IMPORTANT] 
>Istnieje kilka powodów, dla których nie zobaczysz wszystkich aplikacji, których oczekujesz po zainstalowaniu Azure AD Connect Health. Raport AD FS aktywności aplikacji pokazuje tylko AD FS jednostki zależne z identyfikatorami logowania użytkownika w ciągu ostatnich 30 dni. Ponadto w raporcie nie będą wyświetlane jednostki zależne powiązane z firmą Microsoft, takie jak usługa Office 365.

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>Odnajdywanie AD FS, które można migrować 

Raport AD FS aktywności aplikacji jest dostępny w witrynie Azure Portal w obszarze Raportowanie szczegółowych informacji o użyciu **&** Azure AD. Raport AD FS aktywności aplikacji analizuje każdą aplikację AD FS, aby określić, czy można ją zmigrować w stanie, w jaki jest, lub czy jest potrzebny dodatkowy przegląd. 

1. Zaloguj się do usługi [Azure Portal](https://portal.azure.com) przy użyciu roli administratora, która ma dostęp do danych aktywności aplikacji usługi AD FS (administratora globalnego, czytelnika raportów, czytelnika zabezpieczeń, administratora aplikacji lub administratora aplikacji w chmurze).

2. Wybierz **Azure Active Directory**, a następnie wybierz pozycję **Aplikacje dla przedsiębiorstw.**

3. W **obszarze** Działanie wybierz **pozycję & Insights,** a następnie wybierz pozycję **AD FS działanie** aplikacji, aby otworzyć listę wszystkich AD FS aplikacji w organizacji.

   ![AD FS działania aplikacji](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. Dla każdej aplikacji na AD FS aktywności aplikacji wyświetl **stan migracji:**

   * **Gotowość do migracji** oznacza, AD FS konfiguracji aplikacji jest w pełni obsługiwana w usłudze Azure AD i można ją migrować w stanie, w jaki jest.

   * **Wymaga przeglądu** oznacza, że niektóre ustawienia aplikacji można migrować do usługi Azure AD, ale należy przejrzeć ustawienia, których nie można migrować w stanie, w jaki jest.

   * **Dodatkowe wymagane kroki** oznaczają, że usługa Azure AD nie obsługuje niektórych ustawień aplikacji, więc nie można migrować aplikacji w bieżącym stanie.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>Ocena gotowości aplikacji do migracji 

1. Na liście AD FS aplikacji kliknij stan w kolumnie **Stan** migracji, aby otworzyć szczegóły migracji. Zobaczysz podsumowanie testów konfiguracji, które zostały pomyślnie przeprowadzone, wraz z potencjalnymi problemami z migracją.

   ![Szczegóły dotyczące migracji](media/migrate-adfs-application-activity/migration-details.png)

2. Kliknij komunikat, aby otworzyć dodatkowe szczegóły reguły migracji. Aby uzyskać pełną listę przetestowanych właściwości, zobacz tabelę [testów AD FS aplikacji](#ad-fs-application-configuration-tests) poniżej.

   ![Szczegóły reguły migracji](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>AD FS testów konfiguracji aplikacji

W poniższej tabeli wymieniono wszystkie testy konfiguracji, które są wykonywane AD FS aplikacji.

|Wynik  |Pass/Warning/Fail  |Opis  |
|---------|---------|---------|
|Test-ADFSRPAdditionalAuthenticationRules <br> Wykryto co najmniej jedną regułę niemigracyjną dla dodatkowego uwierzytelniania.       | Pass/Warning          | Strona jednostki zależnej ma reguły monitowania o uwierzytelnianie wieloskładnikowe (MFA). Aby przejść do usługi Azure AD, przetłumacz te reguły na zasady dostępu warunkowego. Jeśli używasz lokalnej usługi MFA, zalecamy przejście do usługi Azure AD MFA. [Dowiedz się więcej o dostępie warunkowym.](../authentication/concept-mfa-howitworks.md)        |
|Test-ADFSRPAdditionalWSFedEndpoint <br> Dla jednostki zależnej wartość AdditionalWSFedEndpoint jest ustawiona na true.       | Powodzenie/niepowodzenie          | Strona jednostki zależnej w AD FS umożliwia wiele WS-Fed końcowych asercji.Obecnie usługa Azure AD obsługuje tylko jedną usługę.Jeśli masz scenariusz, w którym ten wynik blokuje migrację, [daj nam znać](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints).     |
|Test-ADFSRPAllowedAuthenticationClassReferences <br> Dla jednostki zależnej ustawiono wartość AllowedAuthenticationClassReferences.       | Powodzenie/niepowodzenie          | To ustawienie w AD FS pozwala określić, czy aplikacja jest skonfigurowana tak, aby zezwalać tylko na niektóre typy uwierzytelniania. Zalecamy korzystanie z dostępu warunkowego w celu osiągnięcia tej możliwości. Jeśli masz scenariusz, w którym ten wynik blokuje migrację, [daj nam znać](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication).  [Dowiedz się więcej o dostępie warunkowym.](../authentication/concept-mfa-howitworks.md)          |
|Test-ADFSRPAlwaysRequireAuthentication <br> AlwaysRequireAuthenticationCheckResult      | Powodzenie/niepowodzenie          | To ustawienie w AD FS pozwala określić, czy aplikacja jest skonfigurowana do ignorowania plików cookie logowania jednokrotnego i opcji Zawsze monituj **o uwierzytelnianie.** W usłudze Azure AD można zarządzać sesją uwierzytelniania przy użyciu zasad dostępu warunkowego, aby osiągnąć podobne zachowanie. [Dowiedz się więcej o konfigurowaniu zarządzania sesjami uwierzytelniania przy użyciu dostępu warunkowego.](../conditional-access/howto-conditional-access-session-lifetime.md)          |
|Test-ADFSRPAutoUpdateEnabled <br> Dla jednostki zależnej dla ustawienia AutoUpdateEnabled ustawiono wartość true       | Pass/Warning          | To ustawienie w AD FS pozwala określić, AD FS skonfigurowano do automatycznego aktualizowania aplikacji na podstawie zmian w metadanych federacji. Usługa Azure AD obecnie nie obsługuje tej usługi, ale nie powinna blokować migracji aplikacji do usługi Azure AD.           |
|Test-ADFSRPClaimsProviderName <br> Strona jednostki zależnej ma włączonych wiele elementów ClaimsProviders       | Powodzenie/niepowodzenie          | To ustawienie w AD FS wywołuje dostawców tożsamości, od których strona jednostki zależnej akceptuje oświadczenia. W usłudze Azure AD możesz włączyć współpracę zewnętrzną przy użyciu usługi Azure AD B2B. [Dowiedz się więcej o usłudze Azure AD B2B.](../external-identities/what-is-b2b.md)          |
|Test-ADFSRPDelegationAuthorizationRules      | Powodzenie/niepowodzenie          | Aplikacja ma zdefiniowane niestandardowe reguły autoryzacji delegowania. Jest to pojęcie WS-Trust, które obsługuje usługa Azure AD, korzystając z nowoczesnych protokołów uwierzytelniania, takich jak OpenID Connect i OAuth 2.0. [Dowiedz się więcej o platformie tożsamości firmy Microsoft.](../develop/v2-protocols-oidc.md)          |
|Test-ADFSRPImpersonationAuthorizationRules       | Pass/Warning          | Aplikacja ma zdefiniowane niestandardowe reguły autoryzacji personifikacji.Jest to pojęcie WS-Trust, które obsługuje usługa Azure AD, korzystając z nowoczesnych protokołów uwierzytelniania, takich jak OpenID Connect i OAuth 2.0. [Dowiedz się więcej o platformie tożsamości firmy Microsoft.](../develop/v2-protocols-oidc.md)          |
|Test-ADFSRPIssuanceAuthorizationRules <br> Wykryto co najmniej jedną regułę, która nie jest migrowaalna dla autoryzacji wystawiania.       | Pass/Warning          | Aplikacja ma niestandardowe reguły autoryzacji wystawiania zdefiniowane w AD FS.Usługa Azure AD obsługuje tę funkcję za pomocą dostępu warunkowego usługi Azure AD. [Dowiedz się więcej o dostępie warunkowym.](../conditional-access/overview.md) <br> Możesz również ograniczyć dostęp do aplikacji przez użytkowników lub grupy przypisane do aplikacji. [Dowiedz się więcej na temat przypisywania użytkowników i grup w celu uzyskiwania dostępu do aplikacji.](./assign-user-or-group-access-portal.md)            |
|Test-ADFSRPIssuanceTransformRules <br> Wykryto co najmniej jedną regułę, która nie jest migrowaalna dla issuancetransform.       | Pass/Warning          | Aplikacja ma niestandardowe reguły przekształcania wystawiania zdefiniowane w AD FS. Usługa Azure AD obsługuje dostosowywanie oświadczeń wystawionych w tokenie. Aby dowiedzieć się więcej, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw.](../develop/active-directory-saml-claims-customization.md)           |
|Test-ADFSRPMonitoringEnabled <br> Dla jednostki zależnej ustawienie MonitoringEnabled ma wartość true.       | Pass/Warning          | To ustawienie w AD FS pozwala określić, AD FS skonfigurowano automatyczne aktualizowanie aplikacji na podstawie zmian w metadanych federacji. Usługa Azure AD nie obsługuje tego obecnie, ale nie powinna blokować migracji aplikacji do usługi Azure AD.           |
|Test-ADFSRPNotBeforeSkew <br> NotBeforeSkewCheckResult      | Pass/Warning          | AD FS pozwala na niesyłanie czasu na podstawie notBefore i NotOnOrAfter razy w tokenie SAML. Usługa Azure AD domyślnie obsługuje to automatycznie.          |
|Test-ADFSRPRequestMFAFromClaimsProviders <br> Dla jednostki zależnej dla elementów RequestMFAFromClaimsProviders ustawiono wartość true.       | Pass/Warning          | To ustawienie w AD FS określa zachowanie usługi MFA, gdy użytkownik pochodzi od innego dostawcy oświadczeń. W usłudze Azure AD możesz włączyć współpracę zewnętrzną przy użyciu usługi Azure AD B2B. Następnie można zastosować zasady dostępu warunkowego, aby chronić dostęp gościa. Dowiedz się więcej o [usłudze Azure AD B2B](../external-identities/what-is-b2b.md) i [dostępie warunkowym.](../conditional-access/overview.md)          |
|Test-ADFSRPSignedSamlRequestsRequired <br> Strona jednostki zależnej ma wartość SignedSamlRequestsRequired ustawioną na wartość true       | Powodzenie/niepowodzenie          | Aplikacja jest skonfigurowana w AD FS w celu zweryfikowania podpisu w żądaniu SAML. Usługa Azure AD akceptuje podpisane żądanie SAML. Jednak nie zweryfikuje podpisu. Usługa Azure AD oferuje różne metody ochrony przed złośliwymi wywołaniami. Na przykład usługa Azure AD używa adresów URL odpowiedzi skonfigurowanych w aplikacji do zweryfikowania żądania SAML. Usługa Azure AD wyśle token tylko na adresy URL odpowiedzi skonfigurowane dla aplikacji. Jeśli masz scenariusz, w którym ten wynik blokuje migrację, [daj nam znać](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature).          |
|Test-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | Pass/Warning         | Aplikacja jest skonfigurowana na czas istnienia tokenu niestandardowego. Wartość AD FS to jedna godzina.Usługa Azure AD obsługuje tę funkcję przy użyciu dostępu warunkowego. Aby dowiedzieć się więcej, zobacz [Konfigurowanie zarządzania sesją uwierzytelniania przy użyciu dostępu warunkowego.](../conditional-access/howto-conditional-access-session-lifetime.md)          |
|Jednostki zależnej jest ustawiona do szyfrowania oświadczeń. Jest to obsługiwane przez usługę Azure AD       | Zdane          | Usługa Azure AD umożliwia szyfrowanie tokenu wysyłanego do aplikacji. Aby dowiedzieć się więcej, zobacz Configure Azure AD SAML token encryption (Konfigurowanie [szyfrowania tokenów SAML usługi Azure AD).](./howto-saml-token-encryption.md)          |
|EncryptedNameIdRequiredCheckResult      | Powodzenie/niepowodzenie          | Aplikacja jest skonfigurowana do szyfrowania oświadczenia nameID w tokenie SAML.Usługa Azure AD umożliwia szyfrowanie całego tokenu wysyłanego do aplikacji.Szyfrowanie określonych oświadczeń nie jest jeszcze obsługiwane. Aby dowiedzieć się więcej, zobacz [Konfigurowanie szyfrowania tokenów SAML usługi Azure AD.](./howto-saml-token-encryption.md)         |

## <a name="check-the-results-of-claim-rule-tests"></a>Sprawdzanie wyników testów reguł oświadczenia

Jeśli skonfigurowano regułę oświadczenie dla aplikacji w AD FS, środowisko zapewni szczegółową analizę dla wszystkich reguł oświadczenia. Zobaczysz, które reguły roszczeń można przenieść do usługi Azure AD, a które wymagają dalszego przeglądu.

1. Na liście AD FS aplikacji kliknij stan w kolumnie **Stan** migracji, aby otworzyć szczegóły migracji. Zobaczysz podsumowanie testów konfiguracji, które zostały pomyślnie przeprowadzone, wraz z potencjalnymi problemami z migracją.

2. Na stronie **Szczegóły reguły migracji** rozwiń wyniki, aby wyświetlić szczegółowe informacje o potencjalnych problemach z migracją i uzyskać dodatkowe wskazówki. Aby uzyskać szczegółową listę wszystkich przetestowanych reguł roszczeń, zobacz tabelę Sprawdź wyniki testów [reguł](#check-the-results-of-claim-rule-tests) oświadczenia poniżej.

   W poniższym przykładzie przedstawiono szczegóły reguły migracji dla reguły IssuanceTransform. Zawiera on listę określonych części oświadczenia, które należy przejrzeć i rozwiązać, zanim będzie można migrować aplikację do usługi Azure AD.

   ![Dodatkowe wskazówki dotyczące szczegółów reguły migracji](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Testy reguł oświadczenia

W poniższej tabeli wymieniono wszystkie testy reguł oświadczenia, które są wykonywane AD FS aplikacji.

|Właściwość  |Opis  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | Instrukcja warunek używa wyrażeń regularnych do oceny, czy oświadczenie pasuje do określonego wzorca.Aby osiągnąć podobną funkcjonalność w usłudze Azure AD, można użyć wstępnie zdefiniowanych przekształceń, takich jak IfEmpty(), StartWith(), Contains(), między innymi. Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw.](../develop/active-directory-saml-claims-customization.md)          |
|UNSUPPORTED_CONDITION_CLASS      | Instrukcja warunek ma wiele warunków, które należy ocenić przed uruchomieniem instrukcji wystawiania.Usługa Azure AD może obsługiwać tę funkcję za pomocą funkcji przekształcania oświadczenia, w których można ocenić wiele wartości oświadczenia.Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw.](../develop/active-directory-saml-claims-customization.md)          |
|UNSUPPORTED_RULE_TYPE      | Nie można rozpoznać reguły oświadczenia. Aby uzyskać więcej informacji na temat konfigurowania oświadczeń w usłudze Azure AD, zobacz Dostosowywanie oświadczeń wystawionych w [tokenie SAML dla aplikacji dla przedsiębiorstw.](../develop/active-directory-saml-claims-customization.md)          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | Instrukcja warunek używa wystawcy, który nie jest obsługiwany w usłudze Azure AD.Obecnie usługa Azure AD nie jest źródłem oświadczeń z magazynów innych niż usługa Active Directory lub Azure AD. Jeśli blokuje to migrowanie aplikacji do usługi Azure AD, [daj nam znać](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).         |
|UNSUPPORTED_CONDITION_FUNCTION      | Instrukcja warunek używa funkcji agregowania do wystawienia lub dodania pojedynczego oświadczenia niezależnie od liczby dopasowania.W usłudze Azure AD można ocenić atrybut użytkownika, aby określić, jakiej wartości należy użyć dla oświadczenia, między innymi za pomocą funkcji, takich jak IfEmpty(), StartWith(), Contains().Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw.](../develop/active-directory-saml-claims-customization.md)          |
|RESTRICTED_CLAIM_ISSUED      | Instrukcja warunek używa oświadczenia ograniczonego w usłudze Azure AD. Możliwe jest wystawienie oświadczenia ograniczonego, ale nie można zmodyfikować jego źródła ani zastosować żadnych przekształceń. Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń emitowanych w tokenach dla określonej aplikacji w usłudze Azure AD.](../develop/active-directory-claims-mapping.md)          |
|EXTERNAL_ATTRIBUTE_STORE      | Instrukcja wystawiania używa magazynu atrybutów innego niż usługa Active Directory. Obecnie usługa Azure AD nie jest źródłem oświadczeń z magazynów innych niż usługa Active Directory lub Azure AD. Jeśli ten wynik blokuje migrowanie aplikacji do usługi Azure AD, [daj nam znać](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).          |
|UNSUPPORTED_ISSUANCE_CLASS      | Instrukcja wystawiania używa instrukcji ADD, aby dodać oświadczenia do zestawu oświadczeń przychodzących. W usłudze Azure AD można to skonfigurować jako przekształcenia wielu oświadczenia.Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw.](../develop/active-directory-claims-mapping.md)         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | Instrukcja wystawiania używa wyrażeń regularnych do przekształcania wartości oświadczenia do emisji.Aby osiągnąć podobną funkcjonalność w usłudze Azure AD, można użyć wstępnie zdefiniowanych przekształceń, takich jak Extract(), Trim(), ToLower, między innymi. Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw.](../develop/active-directory-saml-claims-customization.md)          |

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="cant-see-all-my-ad-fs-applications-in-the-report"></a>Nie widzę wszystkich aplikacji AD FS w raporcie

 Jeśli zainstalowano usługę Azure AD Connect Health, ale nadal jest wyświetlany monit o jej zainstalowanie lub nie widzisz wszystkich aplikacji usługi AD FS w raporcie, być może nie masz aktywnych aplikacji usługi AD FS lub aplikacje AD FS są aplikacjami firmy Microsoft.
 
 Raport AD FS aktywności aplikacji zawiera listę wszystkich AD FS aplikacji w organizacji z aktywnymi użytkownikami logują się w ciągu ostatnich 30 dni. Ponadto w raporcie nie są wyświetlane jednostki zależne powiązane z firmą Microsoft w usługach AD FS, takich jak office 365. Na przykład jednostki zależne o nazwach "urn:federation:MicrosoftOnline", "microsoftonline", "microsoft:winhello:cert:prov:server" nie będą wyświetlane na liście.





## <a name="next-steps"></a>Następne kroki

- [Wideo: Jak migrować aplikację za pomocą AD FS raportu aktywności](https://www.youtube.com/watch?v=OThlTA239lU)
- [Zarządzanie aplikacjami przy użyciu usługi Azure Active Directory](what-is-application-management.md)
- [Zarządzanie dostępem do aplikacji](what-is-access-management.md)
- [Azure AD Connect federacji](../hybrid/how-to-connect-fed-whatis.md)
