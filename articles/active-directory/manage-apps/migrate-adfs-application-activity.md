---
title: Użyj raportu działania, aby przenieść AD FS aplikacje do Azure Active Directory | Microsoft Docs "
description: Raport działania aplikacji Active Directory Federation Services (AD FS) umożliwia szybkie Migrowanie aplikacji z AD FS do Azure Active Directory (Azure AD). To narzędzie do migracji AD FS identyfikuje zgodność z usługą Azure AD i zapewnia wskazówki dotyczące migracji.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/14/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59502e01a96b603067bd80b92bcf49136f8cef4e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85339162"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>Korzystanie z raportu działania aplikacji AD FS (wersja zapoznawcza) w celu migrowania aplikacji do usługi Azure AD

Wiele organizacji używa Active Directory Federation Services (AD FS), aby zapewnić Logowanie jednokrotne do aplikacji w chmurze. Istnieją znaczne korzyści związane z przenoszenium AD FS aplikacji do usługi Azure AD w celu uwierzytelniania, szczególnie w zakresie zarządzania kosztami, zarządzania ryzykiem, produktywności, zgodności i zarządzania. Należy jednak zrozumieć, które aplikacje są zgodne z usługą Azure AD, a określenie konkretnych kroków migracji może być czasochłonne.

Raport aktywność aplikacji AD FS (wersja zapoznawcza) w Azure Portal pozwala szybko określić, które aplikacje mogą być migrowane do usługi Azure AD. Ocenia wszystkie AD FS aplikacje pod kątem zgodności z usługą Azure AD, sprawdza pod kątem problemów i zapewnia wskazówki dotyczące przygotowania poszczególnych aplikacji do migracji. Za pomocą raportu działania aplikacji AD FS można:

* **Odkryj AD FS aplikacje i zakres migracji.** Raport aktywność aplikacji AD FS zawiera listę wszystkich aplikacji AD FS w organizacji i wskazuje ich gotowość do migracji do usługi Azure AD.
* **Ustalanie priorytetów aplikacji do migracji.** Uzyskaj liczbę unikatowych użytkowników, którzy zalogowali się do aplikacji w ciągu ostatnich 1, 7 lub 30 dni, aby pomóc w ustaleniu zagrożenia lub ryzyka migracji aplikacji.
* **Uruchamianie testów migracji i rozwiązywanie problemów.** Usługa raportowania automatycznie uruchamia testy, aby określić, czy aplikacja jest gotowa do migracji. Wyniki są wyświetlane w raporcie AD FS działanie aplikacji jako stan migracji. W przypadku zidentyfikowania potencjalnych problemów z migracją należy uzyskać szczegółowe wskazówki dotyczące rozwiązywania problemów.

AD FS dane działania aplikacji są dostępne dla użytkowników, którym przypisano dowolne z tych ról administratora: Administrator globalny, czytelnik raportu, czytelnik zabezpieczeń, administrator aplikacji lub administrator aplikacji w chmurze.

## <a name="prerequisites"></a>Wymagania wstępne

* Twoja organizacja musi obecnie używać AD FS, aby uzyskiwać dostęp do aplikacji.
* Należy włączyć Azure AD Connect Health w dzierżawie usługi Azure AD.
* Azure AD Connect Health agenta AD FS musi być zainstalowany.
   * [Dowiedz się więcej o Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)
   * [Wprowadzenie do konfigurowania Azure AD Connect Health i instalowania agenta AD FS](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>Odkryj AD FS aplikacje, które można migrować 

Raport aktywność aplikacji AD FS jest dostępny w Azure Portal w obszarze usługi Azure AD **Usage & Insights** Reporting. Raport aktywność aplikacji AD FS analizuje każdą aplikację AD FS, aby określić, czy można przeprowadzić migrację w niezmienionej postaci lub jeśli jest wymagana dodatkowa weryfikacja. 

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu roli administratora, która ma dostęp do AD FS danych działania aplikacji (Administrator globalny, czytelnik raportu, czytelnik zabezpieczeń, administrator aplikacji lub administrator aplikacji w chmurze).

2. Wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **aplikacje dla przedsiębiorstw**.

3. W obszarze **działanie**wybierz pozycję **użycie & Insights (wersja zapoznawcza)**, a następnie wybierz pozycję **AD FS aktywność aplikacji** , aby otworzyć listę wszystkich AD FS aplikacji w organizacji.

   ![Działanie aplikacji AD FS](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. Dla każdej aplikacji na liście działanie AD FS aplikacji Wyświetl **stan migracji**:

   * **Przygotowana do migracji** oznacza, że konfiguracja aplikacji AD FS jest w pełni obsługiwana w usłudze Azure AD i można ją migrować w taki sam sposób.

   * **Wymaga przeglądu** oznacza, że niektóre ustawienia aplikacji można migrować do usługi Azure AD, ale musisz przejrzeć ustawienia, których nie można migrować w stanie takim, w jakim się znajdują.

   * **Wymagane dodatkowe kroki** oznacza, że usługa Azure AD nie obsługuje niektórych ustawień aplikacji, więc nie można migrować aplikacji w jej bieżącym stanie.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>Oceń gotowość aplikacji do migracji 

1. Na liście działanie aplikacji AD FS kliknij stan w kolumnie **stan migracji** , aby otworzyć Szczegóły migracji. Zobaczysz podsumowanie testów konfiguracji, które zakończono, wraz z potencjalnymi problemami migracji.

   ![Szczegóły dotyczące migracji](media/migrate-adfs-application-activity/migration-details.png)

2. Kliknij komunikat, aby otworzyć dodatkowe szczegóły reguły migracji. Aby zapoznać się z pełną listą przetestowanych właściwości, zapoznaj się z tabelą [testy konfiguracji aplikacji AD FS](#ad-fs-application-configuration-tests) poniżej.

   ![Szczegóły reguły migracji](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>AD FS testy konfiguracji aplikacji

W poniższej tabeli wymieniono wszystkie testy konfiguracji, które są wykonywane na AD FS aplikacji.

|Wynik  |Pass/Warning/niepowodzenie  |Opis  |
|---------|---------|---------|
|Test-ADFSRPAdditionalAuthenticationRules <br> Wykryto co najmniej jedną regułę migrowalna dla AdditionalAuthentication.       | Przekaż/Ostrzegaj          | Jednostka uzależniona ma reguły monitowania o uwierzytelnianie wieloskładnikowe (MFA). Aby przejść do usługi Azure AD, Przetłumacz te reguły na zasady dostępu warunkowego. Jeśli używasz lokalnej usługi MFA, zalecamy przejście na usługę Azure MFA. [Dowiedz się więcej na temat dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).        |
|Test-ADFSRPAdditionalWSFedEndpoint <br> Jednostka uzależniona ma AdditionalWSFedEndpoint ustawioną wartość true.       | Powodzenie/niepowodzenie          | Jednostka uzależniona w AD FS umożliwia wielu punktów końcowych potwierdzenia WS-Fed.Obecnie usługa Azure AD obsługuje tylko jeden.Jeśli masz scenariusz, w którym ten wynik blokuje migrację, [daj nam znać](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints).     |
|Test-ADFSRPAllowedAuthenticationClassReferences <br> Jednostka uzależniona ustawił AllowedAuthenticationClassReferences.       | Powodzenie/niepowodzenie          | To ustawienie w AD FS pozwala określić, czy aplikacja jest skonfigurowana tak, aby zezwalać tylko na określone typy uwierzytelniania. Zalecamy korzystanie z dostępu warunkowego w celu osiągnięcia tej możliwości. Jeśli masz scenariusz, w którym ten wynik blokuje migrację, [daj nam znać](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication).  [Dowiedz się więcej na temat dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).          |
|Test-ADFSRPAlwaysRequireAuthentication <br> AlwaysRequireAuthenticationCheckResult      | Powodzenie/niepowodzenie          | To ustawienie w AD FS pozwala określić, czy aplikacja jest skonfigurowana do ignorowania plików cookie logowania jednokrotnego i **zawsze monitować o uwierzytelnienie**. W usłudze Azure AD sesja uwierzytelniania może być zarządzana przy użyciu zasad dostępu warunkowego w celu osiągnięcia podobnego zachowania. [Dowiedz się więcej o konfigurowaniu zarządzania sesjami uwierzytelniania przy użyciu dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).          |
|Test-ADFSRPAutoUpdateEnabled <br> Jednostka uzależniona ma AutoUpdateEnabled ustawioną wartość PRAWDA       | Przekaż/Ostrzegaj          | To ustawienie w AD FS pozwala określić, czy AD FS jest skonfigurowany do automatycznej aktualizacji aplikacji na podstawie zmian w metadanych Federacji. Usługa Azure AD nie obsługuje tego dzisiaj, ale nie należy blokować migracji aplikacji do usługi Azure AD.           |
|Test-ADFSRPClaimsProviderName <br> Jednostka uzależniona ma włączoną wiele ClaimsProviders       | Powodzenie/niepowodzenie          | To ustawienie w AD FS wywołuje dostawców tożsamości, z których jednostka uzależniona akceptuje oświadczenia. W usłudze Azure AD można włączyć współpracę zewnętrzną przy użyciu usługi Azure AD B2B. [Dowiedz się więcej o usłudze Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b).          |
|Test-ADFSRPDelegationAuthorizationRules      | Powodzenie/niepowodzenie          | Aplikacja ma zdefiniowane niestandardowe reguły autoryzacji delegowania. Jest to WS-Trust koncepcji obsługiwanej przez usługę Azure AD przy użyciu nowoczesnych protokołów uwierzytelniania, takich jak OpenID Connect Connect i OAuth 2,0. [Dowiedz się więcej o platformie tożsamości firmy Microsoft](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc).          |
|Test-ADFSRPImpersonationAuthorizationRules       | Przekaż/Ostrzegaj          | Aplikacja ma zdefiniowane niestandardowe reguły autoryzacji personifikacji.Jest to WS-Trust koncepcji obsługiwanej przez usługę Azure AD przy użyciu nowoczesnych protokołów uwierzytelniania, takich jak OpenID Connect Connect i OAuth 2,0. [Dowiedz się więcej o platformie tożsamości firmy Microsoft](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc).          |
|Test-ADFSRPIssuanceAuthorizationRules <br> Wykryto co najmniej jedną regułę migrowalna dla IssuanceAuthorization.       | Przekaż/Ostrzegaj          | Aplikacja ma niestandardowe reguły autoryzacji wystawiania zdefiniowane w AD FS.Usługa Azure AD obsługuje tę funkcję przy użyciu dostępu warunkowego usługi Azure AD. [Dowiedz się więcej na temat dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/overview). <br> Możesz również ograniczyć dostęp do aplikacji przez użytkownika lub grupy przypisane do aplikacji. [Dowiedz się więcej o przypisywaniu użytkowników i grup w celu uzyskania dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups).            |
|Test-ADFSRPIssuanceTransformRules <br> Wykryto co najmniej jedną regułę migrowalna dla IssuanceTransform.       | Przekaż/Ostrzegaj          | Aplikacja ma niestandardowe reguły przekształcania wystawiania zdefiniowane w AD FS. Usługa Azure AD obsługuje Dostosowywanie oświadczeń wystawionych w tokenie. Aby dowiedzieć się więcej, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).           |
|Test-ADFSRPMonitoringEnabled <br> Jednostka uzależniona ma parametry monitoringenabled ustawioną wartość true.       | Przekaż/Ostrzegaj          | To ustawienie w AD FS pozwala określić, czy AD FS jest skonfigurowany do automatycznej aktualizacji aplikacji na podstawie zmian w metadanych Federacji. Usługa Azure AD nie obsługuje tego dzisiaj, ale nie należy blokować migracji aplikacji do usługi Azure AD.           |
|Test-ADFSRPNotBeforeSkew <br> NotBeforeSkewCheckResult      | Przekaż/Ostrzegaj          | AD FS umożliwia przechylenie czasu na podstawie czasów NotBefore i NotOnOrAfter w tokenie SAML. Usługa Azure AD automatycznie obsługuje to ustawienie domyślnie.          |
|Test-ADFSRPRequestMFAFromClaimsProviders <br> Jednostka uzależniona ma RequestMFAFromClaimsProviders ustawioną wartość true.       | Przekaż/Ostrzegaj          | To ustawienie w AD FS określa zachowanie usługi MFA, gdy użytkownik pochodzi od innego dostawcy oświadczeń. W usłudze Azure AD można włączyć współpracę zewnętrzną przy użyciu usługi Azure AD B2B. Następnie można zastosować zasady dostępu warunkowego w celu ochrony dostępu gościa. Dowiedz się więcej na temat [usługi Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) i [dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).          |
|Test-ADFSRPSignedSamlRequestsRequired <br> Jednostka uzależniona ma SignedSamlRequestsRequired ustawioną wartość PRAWDA       | Powodzenie/niepowodzenie          | Aplikacja jest skonfigurowana w AD FS, aby zweryfikować podpis w żądaniu SAML. Usługa Azure AD akceptuje podpisane żądanie SAML. nie spowoduje to jednak zweryfikowania podpisu. Usługa Azure AD ma różne metody ochrony przed złośliwymi wywołaniami. Na przykład usługa Azure AD używa adresów URL odpowiedzi skonfigurowanych w aplikacji do sprawdzania poprawności żądania SAML. Usługa Azure AD będzie wysyłać token tylko do adresów URL skonfigurowanych dla aplikacji. Jeśli masz scenariusz, w którym ten wynik blokuje migrację, [daj nam znać](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature).          |
|Test-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | Przekaż/Ostrzegaj         | Aplikacja jest skonfigurowana pod kątem niestandardowego okresu istnienia tokenu. Wartość domyślna AD FS wynosi godzinę.Usługa Azure AD obsługuje tę funkcję przy użyciu dostępu warunkowego. Aby dowiedzieć się więcej, zobacz [Konfigurowanie zarządzania sesjami uwierzytelniania przy użyciu dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).          |
|Jednostka uzależniona jest ustawiona na szyfrowanie oświadczeń. Jest to obsługiwane przez usługę Azure AD       | Chodzenia          | Za pomocą usługi Azure AD można zaszyfrować token wysyłany do aplikacji. Aby dowiedzieć się więcej, zobacz [Konfigurowanie szyfrowania tokenów SAML usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).          |
|EncryptedNameIdRequiredCheckResult      | Powodzenie/niepowodzenie          | Aplikacja jest skonfigurowana do szyfrowania żądania nameID w tokenie SAML.Za pomocą usługi Azure AD można zaszyfrować cały token wysłany do aplikacji.Szyfrowanie określonych oświadczeń nie jest jeszcze obsługiwane. Aby dowiedzieć się więcej, zobacz [Konfigurowanie szyfrowania tokenów SAML usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).         |

## <a name="check-the-results-of-claim-rule-tests"></a>Sprawdź wyniki testów reguł dotyczących roszczeń

Jeśli skonfigurowano regułę dotyczącej roszczeń dla aplikacji w AD FS, środowisko to zapewni szczegółową analizę wszystkich reguł dotyczących roszczeń. Zobaczysz, które reguły dotyczące roszczeń można przenieść do usługi Azure AD, a które wymagają dalszych przeglądów.

1. Na liście działanie aplikacji AD FS kliknij stan w kolumnie **stan migracji** , aby otworzyć Szczegóły migracji. Zobaczysz podsumowanie testów konfiguracji, które zakończono, wraz z potencjalnymi problemami migracji.

2. Na stronie **szczegóły reguły migracji** rozwiń wyniki, aby wyświetlić szczegółowe informacje o potencjalnych problemach migracji i uzyskać dodatkowe wskazówki. Aby uzyskać szczegółową listę wszystkich przetestowanych reguł dotyczących roszczeń, zobacz sekcję [Sprawdź wyniki testów reguł dotyczących roszczeń](#check-the-results-of-claim-rule-tests) poniżej.

   W poniższym przykładzie przedstawiono szczegóły reguły migracji dla reguły IssuanceTransform. Zawiera on listę konkretnych części, które należy przejrzeć i rozwiązać, aby można było przeprowadzić migrację aplikacji do usługi Azure AD.

   ![Dodatkowe wskazówki dotyczące reguł migracji](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Testy reguły dotyczącej roszczeń

W poniższej tabeli wymieniono wszystkie testy reguł dotyczących roszczeń, które są wykonywane na AD FS aplikacjach.

|Właściwość  |Opis  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | Instrukcja Condition używa wyrażeń regularnych w celu obliczenia, czy oświadczenie pasuje do określonego wzorca.Aby osiągnąć podobną funkcjonalność w usłudze Azure AD, możesz użyć wstępnie zdefiniowanego przekształcenia, takiego jak IfEmpty (), StartWith (), zawiera (), między innymi. Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|UNSUPPORTED_CONDITION_CLASS      | Instrukcja Condition ma wiele warunków, które muszą być oceniane przed uruchomieniem instrukcji wystawiania.Usługa Azure AD może obsługiwać tę funkcję za pomocą funkcji przekształcania roszczeń, w których można oszacować wiele wartości.Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|UNSUPPORTED_RULE_TYPE      | Nie można rozpoznać reguły dotyczącej roszczeń. Aby uzyskać więcej informacji na temat konfigurowania oświadczeń w usłudze Azure AD, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | Instrukcja Condition używa wystawcy, który nie jest obsługiwany w usłudze Azure AD.Obecnie usługa Azure AD nie jest źródłem oświadczeń z magazynów innych niż Active Directory lub Azure AD. Jeśli uniemożliwiasz Migrowanie aplikacji do usługi Azure AD, [daj nam znać](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).         |
|UNSUPPORTED_CONDITION_FUNCTION      | Instrukcja Condition używa funkcji agregującej, aby wystawić lub dodać pojedyncze oświadczenie, niezależnie od liczby dopasowań.W usłudze Azure AD można oszacować atrybut użytkownika w celu określenia wartości, która ma być używana dla tego żądania z funkcjami takimi jak IfEmpty (), StartWith (), zawiera (), między innymi.Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|RESTRICTED_CLAIM_ISSUED      | Instrukcja Condition używa oświadczenia, które jest ograniczone w usłudze Azure AD. Może być możliwe wystawienie ograniczonego odszkodowania, ale nie można zmodyfikować jego źródła ani zastosować żadnego przekształcenia. Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń emitowanych w tokenach dla określonej aplikacji w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).          |
|EXTERNAL_ATTRIBUTE_STORE      | Instrukcja wystawiania używa magazynu atrybutów innego, który Active Directory. Obecnie usługa Azure AD nie jest źródłem oświadczeń z magazynów innych niż Active Directory lub Azure AD. Jeśli ten wynik uniemożliwia Migrowanie aplikacji do usługi Azure AD, skontaktuj się z [nami](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).          |
|UNSUPPORTED_ISSUANCE_CLASS      | Instrukcja wystawiania służy do dodawania oświadczeń do zestawu oświadczeń przychodzących. W usłudze Azure AD można to skonfigurować jako wiele przekształceń roszczeń.Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | Instrukcja wystawiania używa wyrażeń regularnych w celu przekształcenia wartości oświadczenia do emisji.Aby osiągnąć podobną funkcjonalność w usłudze Azure AD, możesz użyć wstępnie zdefiniowanego przekształcenia, takiego jak Extract (), Trim (), ToLower, między innymi. Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |


## <a name="next-steps"></a>Następne kroki

- [Wideo: jak używać raportu działania AD FS do migrowania aplikacji](https://www.youtube.com/watch?v=OThlTA239lU)
- [Zarządzanie aplikacjami przy użyciu usługi Azure Active Directory](what-is-application-management.md)
- [Zarządzanie dostępem do aplikacji](what-is-access-management.md)
- [Azure AD Connect Federacja](../hybrid/how-to-connect-fed-whatis.md)
