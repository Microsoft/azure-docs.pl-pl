---
title: Przenoszenie uwierzytelniania aplikacji z AD FS do Azure Active Directory
description: Dowiedz się, jak za Azure Active Directory zastąpić Active Directory Federation Services (AD FS), zapewniając użytkownikom logowanie jednorazowe do wszystkich swoich aplikacji.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: iangithinji
ms.reviewer: baselden
ms.openlocfilehash: b0fd3dae2ff9c6de39462d19dc41a32ba51171e0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534877"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Przenoszenie uwierzytelniania aplikacji z usługi Active Directory Federation Services do usługi Azure Active Directory

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) oferuje uniwersalną platformę tożsamości, która zapewnia osobom, partnerom i klientom jedną tożsamość na potrzeby dostępu do aplikacji oraz współpracy z dowolnej platformy i urządzenia. Usługa Azure AD oferuje [pełny zestaw funkcji zarządzania tożsamościami.](../fundamentals/active-directory-whatis.md) Standaryzacja uwierzytelniania aplikacji i autoryzacji w usłudze Azure AD zapewnia te korzyści.

> [!TIP]
> Ten artykuł jest napisany dla deweloperów. Menedżerowie projektów i administratorzy planujący przeniesienie aplikacji do usługi Azure AD powinni rozważyć przeczytanie książki [Migrowanie uwierzytelniania aplikacji do usługi Azure AD.](migrate-application-authentication-to-azure-active-directory.md)

## <a name="azure-ad-benefits"></a>Korzyści z usługi Azure AD

Jeśli masz katalog lokalnego, który zawiera konta użytkowników, prawdopodobnie masz wiele aplikacji, w których użytkownicy się uwierzytelniają. Każda z tych aplikacji jest skonfigurowana do uzyskiwania dostępu przez użytkowników przy użyciu ich tożsamości.

Użytkownicy mogą również uwierzytelniać się bezpośrednio w lokalna usługa Active Directory. Active Directory Federation Services (AD FS) to oparta na standardach lokalna usługa zarządzania tożsamościami. Rozszerza możliwość korzystania z funkcji logowania jednokrotnego (SSO) między zaufanymi partnerami biznesowymi, dzięki czemu użytkownicy nie muszą logować się oddzielnie do każdej aplikacji. Jest to nazywane tożsamością federacyjną.

Wiele organizacji ma oprogramowanie jako usługę (SaaS) lub niestandardowe aplikacje biznesowe federowane bezpośrednio z AD FS, wraz z Microsoft 365 i aplikacjami opartymi na usłudze Azure AD.

  ![Aplikacje połączone bezpośrednio w środowisku lokalnym](media/migrate-adfs-apps-to-azure/app-integration-before-migration-1.png)

> [!Important]
> Aby zwiększyć bezpieczeństwo aplikacji, twoim celem jest zastosowanie jednego zestawu kontroli dostępu i zasad w środowiskach lokalnych i w chmurze.

  ![Aplikacje połączone za pośrednictwem usługi Azure AD](media/migrate-adfs-apps-to-azure/app-integration-after-migration-1.png)

## <a name="types-of-apps-to-migrate"></a>Typy aplikacji do migracji

Migracja całego uwierzytelniania aplikacji do usługi Azure AD jest optymalna, ponieważ zapewnia pojedynczą płaszczyznę kontroli do zarządzania tożsamościami i dostępem.

Aplikacje mogą używać nowoczesnych lub starszych protokołów do uwierzytelniania. Planując migrację do usługi Azure AD, rozważ najpierw migrację aplikacji, które korzystają z nowoczesnych protokołów uwierzytelniania (takich jak SAML i Open ID Connect). Te aplikacje można ponownie skonfigurować do uwierzytelniania w usłudze Azure AD za pośrednictwem wbudowanego łącznika z galerii aplikacji platformy Azure lub przez zarejestrowanie aplikacji w usłudze Azure AD. Aplikacje korzystające ze starszych protokołów można zintegrować przy użyciu serwer proxy aplikacji.

Aby uzyskać więcej informacji, zobacz:

* [Używanie usługi Azure AD serwer proxy aplikacji do publikowania aplikacji lokalnych dla użytkowników zdalnych.](what-is-application-proxy.md)
* [Co to jest zarządzanie aplikacjami?](what-is-application-management.md)
* [AD FS działania aplikacji w celu migrowania aplikacji do usługi Azure AD.](migrate-adfs-application-activity.md)
* [Monitoruj AD FS przy użyciu Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md).

### <a name="the-migration-process"></a>Proces migracji

Podczas procesu przenoszenia uwierzytelniania aplikacji do usługi Azure AD przetestuj aplikacje i konfigurację. Zalecamy dalsze używanie istniejących środowisk testowych do testowania migracji podczas przechodzenia do środowiska produkcyjnego. Jeśli środowisko testowe nie jest obecnie dostępne, możesz je skonfigurować przy użyciu programu [Azure App Service](https://azure.microsoft.com/services/app-service/) lub [usługi Azure Virtual Machines](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB), w zależności od architektury aplikacji.

Możesz skonfigurować oddzielną dzierżawę testów usługi Azure AD, w której będą opracowywać konfiguracje aplikacji.

Proces migracji może wyglądać tak:

#### <a name="stage-1--current-state-the-production-app-authenticates-with-ad-fs"></a>Etap 1 — bieżący stan: aplikacja produkcyjna uwierzytelnia się za pomocą AD FS

  ![Etap 1 migracji ](media/migrate-adfs-apps-to-azure/stage1.jpg)

#### <a name="stage-2--optional-point-a-test-instance-of-the-app-to-the-test-azure-ad-tenant"></a>Etap 2 — (opcjonalnie) Wskaż testowe wystąpienie aplikacji testowej dzierżawie usługi Azure AD

Zaktualizuj konfigurację, aby wskazać testowe wystąpienie aplikacji do testowej dzierżawy usługi Azure AD i wprowadzić wymagane zmiany. Aplikację można przetestować z użytkownikami w testowej dzierżawie usługi Azure AD. Podczas procesu tworzenia aplikacji można używać narzędzi, takich jak [Fiddler,](https://www.telerik.com/fiddler) do porównywania i weryfikowania żądań i odpowiedzi.

Jeśli skonfigurowanie oddzielnej dzierżawy testowej nie jest możliwe, pomiń ten etap i wskaż wystąpienie testowe aplikacji dzierżawie produkcyjnej usługi Azure AD zgodnie z opisem w etapie 3 poniżej.

  ![Etap 2 migracji ](media/migrate-adfs-apps-to-azure/stage2.jpg)

#### <a name="stage-3--point-a-test-instance-of-the-app-to-the-production-azure-ad-tenant"></a>Etap 3 . Wskaż testowe wystąpienie aplikacji dzierżawie produkcyjnej usługi Azure AD

Zaktualizuj konfigurację, aby wskazać wystąpienie testowe aplikacji do produkcyjnej dzierżawy usługi Azure AD. Teraz możesz testować z użytkownikami w dzierżawie produkcyjnej. W razie potrzeby zapoznaj się z sekcją tego artykułu na temat przechodzenia użytkowników.

  ![Etap 3 migracji ](media/migrate-adfs-apps-to-azure/stage3.jpg)

#### <a name="stage-4--point-the-production-app-to-the-production-azure-ad-tenant"></a>Etap 4 — wskaż produkcyjną dzierżawę usługi Azure AD w aplikacji produkcyjnej

Zaktualizuj konfigurację aplikacji produkcyjnej, aby wskazać produkcyjną dzierżawę usługi Azure AD.

  ![Etap 4 migracji ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 Aplikacje, które uwierzytelniają się AD FS mogą używać grup usługi Active Directory na potrzeby uprawnień. Użyj [Azure AD Connect synchronizacji,](../hybrid/how-to-connect-sync-whatis.md) aby zsynchronizować dane tożsamości między środowiskiem lokalnym i usługą Azure AD przed rozpoczęciem migracji. Przed migracją sprawdź te grupy i członkostwo, aby można było udzielić dostępu tym samym użytkownikom podczas migracji aplikacji.

### <a name="line-of-business-apps"></a>Aplikacje biznesowe

Aplikacje biznesowe to aplikacje opracowane przez organizację lub te, które są standardowymi produktami w pakiecie. Przykłady obejmują aplikacje sbudowaną w programie Windows Identity Foundation i aplikacjach SharePoint (nie SharePoint Online).

Aplikacje biznesowe, które używają protokołu OAuth 2.0, OpenID Connect lub WS-Federation można zintegrować z usługą Azure AD jako [rejestracje aplikacji.](../develop/quickstart-register-app.md) Zintegruj aplikacje niestandardowe, które używają saml 2.0 lub WS-Federation jako aplikacje spoza galerii na stronie aplikacji [dla przedsiębiorstw](https://portal.azure.com/)w Azure Portal . [](add-application-portal.md)

## <a name="saml-based-single-sign-on"></a>Logowanie pojedyncze oparte na samL

Aplikacje, które używają protokołu SAML 2.0 do uwierzytelniania, można skonfigurować dla logowania jednokrotnego (SSO) opartego na [samL.](what-is-single-sign-on.md) Za pomocą logowania jednokrotnego opartego na saml można mapować użytkowników na określone role aplikacji na podstawie reguł określonych w oświadczeniach SAML.

Aby skonfigurować aplikację SaaS dla logowania jednokrotnego opartego na saml, zobacz Szybki start: konfigurowanie logowania jednokrotnego opartego [na saml.](add-application-portal-setup-sso.md)

  ![Zrzuty ekranu użytkownika SSO SAML ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

Wiele aplikacji SaaS zawiera samouczek [specyficzny](../saas-apps/tutorial-list.md) dla aplikacji, który zawiera kroki konfiguracji logowania jednokrotnego opartego na saml.

  ![samouczek dotyczący aplikacji](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Migracja niektórych aplikacji jest prosta. Aplikacje z bardziej złożonymi wymaganiami, takimi jak oświadczenia niestandardowe, mogą wymagać dodatkowej konfiguracji w usłudze Azure AD i/lub Azure AD Connect. Aby uzyskać informacje na temat obsługiwanych mapowań oświadczeń, zobacz Temat Jak: Dostosowywanie oświadczeń emitowanych w tokenach dla określonej aplikacji w dzierżawie [(wersja zapoznawcza).](../develop/active-directory-claims-mapping.md)

Podczas mapowania atrybutów należy pamiętać o następujących ograniczeniach:

* Nie wszystkie atrybuty, które mogą być wystawiane w usłudze AD FS są wyświetlane w usłudze Azure AD jako atrybuty do emitowania do tokenów SAML, nawet jeśli te atrybuty są synchronizowane. Podczas edytowania atrybutu na **liście rozwijanej** Wartość są dostępne różne atrybuty dostępne w usłudze Azure AD. Sprawdź [Azure AD Connect tematach](../hybrid/how-to-connect-sync-whatis.md) synchronizacji, aby upewnić się, że wymagany atrybut — na przykład **samAccountName**— jest synchronizowany z usługą Azure AD. Za pomocą atrybutów rozszerzenia można emitować dowolne oświadczenie, które nie jest częścią standardowego schematu użytkownika w usłudze Azure AD.
* W najbardziej typowych scenariuszach aplikacja wymaga tylko oświadczenia **NameID** i innych typowych oświadczeń identyfikatora użytkownika. Aby ustalić, czy są wymagane jakiekolwiek dodatkowe oświadczenia, sprawdź, jakie oświadczenia są wystawiane z AD FS.
* Nie wszystkie oświadczenia mogą być wystawiane, ponieważ niektóre oświadczenia są chronione w usłudze Azure AD.
* Możliwość używania zaszyfrowanych tokenów SAML jest teraz w wersji zapoznawczej. Zobacz How to: customize claims issued in the SAML token for enterprise applications (Jak dostosować oświadczenia wystawione w [tokenie SAML dla aplikacji dla przedsiębiorstw).](../develop/active-directory-saml-claims-customization.md)

### <a name="software-as-a-service-saas-apps"></a>Aplikacje SaaS (oprogramowanie jako usługa)

Jeśli użytkownicy logują się do aplikacji SaaS, takich jak Salesforce, ServiceNow lub Workday, i są zintegrowani z usługą AD FS, używasz logowania federowego dla aplikacji SaaS.

Większość aplikacji SaaS można skonfigurować w usłudze Azure AD. Firma Microsoft ma wiele wstępnie skonfigurowanych połączeń z aplikacjami SaaS w galerii aplikacji usługi  [Azure AD,](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)co ułatwia przejście. Aplikacje SAML 2.0 można zintegrować z usługą Azure AD za pośrednictwem galerii aplikacji usługi Azure AD lub jako aplikacje [spoza galerii.](add-application-portal.md)

Aplikacje, które korzystają z protokołu OAuth 2.0 lub OpenID Connect, można podobnie zintegrować z usługą Azure AD jako [rejestracje aplikacji.](../develop/quickstart-register-app.md) Aplikacje, które używają starszych protokołów, mogą używać usługi Azure AD serwer proxy aplikacji do [uwierzytelniania](application-proxy.md) w usłudze Azure AD.

W przypadku jakichkolwiek problemów dotyczących dołączania aplikacji SaaS możesz skontaktować się z aliasem pomocy technicznej integracji aplikacji [SaaS](mailto:SaaSApplicationIntegrations@service.microsoft.com).

### <a name="saml-signing-certificates-for-sso"></a>Certyfikaty podpisywania SAML dla logowania jednokrotnego

Certyfikaty podpisywania są ważną częścią każdego wdrożenia logowania jednokrotnego. Usługa Azure AD tworzy certyfikaty podpisywania, aby ustanowić federacyjną logowanie jednokrotne opartą na saml dla aplikacji SaaS. Po dodaniu aplikacji z galerii lub spoza galerii należy skonfigurować dodaną aplikację przy użyciu opcji federowania logowania jednokrotnego. Zobacz [Manage certificates for federated single sign-on in Azure Active Directory](manage-certificates-for-federated-single-sign-on.md)(Zarządzanie certyfikatami dla federowego logowania Azure Active Directory ).

### <a name="saml-token-encryption"></a>Szyfrowanie tokenów SAML

Zarówno AD FS, jak i usługa Azure AD zapewniają szyfrowanie tokenów — możliwość szyfrowania asercji zabezpieczeń SAML, które trafiają do aplikacji. Asercja jest szyfrowana za pomocą klucza publicznego i odszyfrowyowana przez aplikację odbieraującą przy użyciu pasującego klucza prywatnego. Podczas konfigurowania szyfrowania tokenu należy przekazać pliki certyfikatów X.509 w celu podania kluczy publicznych.

Aby uzyskać informacje na temat szyfrowania tokenów SAML usługi Azure AD i sposobu jego konfigurowania, zobacz How to: Configure Azure AD SAML token encryption (3.0: Konfigurowanie szyfrowania [tokenów SAML usługi Azure AD).](howto-saml-token-encryption.md)  

> [!NOTE]
> Szyfrowanie tokenów jest funkcją Azure Active Directory (Azure AD) Premium. Aby dowiedzieć się więcej o wersjach, funkcjach i cenach usługi Azure AD, zobacz [Cennik usługi Azure AD.](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="apps-and-configurations-that-can-be-moved-today"></a>Aplikacje i konfiguracje, które można przenosić dzisiaj

Obecnie można łatwo przenosić aplikacje SAML 2.0, które używają standardowego zestawu elementów konfiguracji i oświadczeń. Te elementy standardowe to:

* Nazwa główna użytkownika
* Adres e-mail
* Imię
* Nazwisko
* Atrybut alternatywny, taki jak **NameID** w języku SAML, w tym atrybut poczty usługi Azure AD, prefiks poczty, identyfikator pracownika, atrybuty rozszerzenia 1–15 lub lokalny atrybut **SamAccountName**. Aby uzyskać więcej informacji, zobacz [Edytowanie oświadczenia NameIdentifier](../develop/active-directory-saml-claims-customization.md).
* Oświadczenia niestandardowe.

W celu przeprowadzenia migracji do usługi Azure AD wymagane są dodatkowe czynności konfiguracyjne:

* Niestandardowe reguły autoryzacji lub uwierzytelniania wieloskładnikowego (MFA) w AD FS. Można je skonfigurować przy użyciu [funkcji dostępu warunkowego usługi Azure AD.](../conditional-access/overview.md)
* Aplikacje z wieloma punktami końcowymi adresu URL odpowiedzi. Można je skonfigurować w usłudze Azure AD przy użyciu programu PowerShell lub Azure Portal interfejsu.
* Aplikacje usług federacyjnych w Internecie, takie jak aplikacje programu SharePoint, które wymagają tokenów w wersji 1.1 protokołu SAML. Można je skonfigurować ręcznie przy użyciu programu PowerShell. Możesz również dodać wstępnie zintegrowany szablon ogólny dla aplikacji SharePoint i SAML 1.1 z galerii. Obsługujemy protokół SAML 2.0.
* Wystawianie złożonych oświadczeń przekształca reguły. Aby uzyskać informacje na temat obsługiwanych mapowań oświadczeń, zobacz:
   *  [Mapowanie oświadczeń w Azure Active Directory](../develop/active-directory-claims-mapping.md).
   * [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw w programie Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Aplikacje i konfiguracje, które nie są obecnie obsługiwane w usłudze Azure AD

Obecnie nie można migrować aplikacji, które wymagają pewnych funkcji.

#### <a name="protocol-capabilities"></a>Możliwości protokołu

Obecnie nie można migrować aplikacji, które wymagają następujących funkcji protokołu:

* Obsługa wzorca WS-Trust ActAs
* Rozpoznawanie artefaktów języka SAML
* Weryfikacja podpisu podpisanych żądań SAML
  > [!Note]
  > Podpisane żądania są akceptowane, ale podpis nie jest weryfikowany.

  Ponieważ usługa Azure AD zwraca token tylko do punktów końcowych wstępnie skonfigurowanych w aplikacji, weryfikacja podpisu prawdopodobnie nie jest wymagana w większości przypadków.

#### <a name="claims-in-token-capabilities"></a>Oświadczenia w możliwościach tokenu

Obecnie nie można migrować aplikacji, które wymagają następujących oświadczeń w możliwościach tokenu.

* Oświadczenia z atrybutu przechowuje inne niż katalog usługi Azure AD, chyba że te dane są synchronizowane z usługą Azure AD. Aby uzyskać więcej informacji, zobacz Omówienie interfejsu [API synchronizacji usługi Azure AD.](/graph/api/resources/synchronization-overview)
* Wystawianie atrybutów o wielu wartościach katalogu. Na przykład w tej chwili nie można wydać oświadczenia wielowartościowego dla adresów serwera proxy.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>Mapowanie ustawień aplikacji z AD FS do usługi Azure AD

Migracja wymaga oceny sposobu konfigurowania aplikacji lokalnie, a następnie mapowania tej konfiguracji na usługę Azure AD. Usługi AD FS i Azure AD działają podobnie, dlatego pojęcia dotyczące konfigurowania relacji zaufania, adresów URL związanych z logowaniem i wylogowywaniem oraz identyfikatorów można stosować w obydwu przypadkach. Udokumentuj AD FS konfiguracji aplikacji, aby można je było łatwo skonfigurować w usłudze Azure AD.

### <a name="map-app-configuration-settings"></a>Mapowanie ustawień konfiguracji aplikacji

W poniższej tabeli opisano niektóre najbardziej typowe mapowanie ustawień między relacją zaufania jednostki AD FS jednostki zależnej do aplikacji azure AD Enterprise:

* AD FS — znajdź ustawienie w AD FS zaufania jednostki zależnej dla aplikacji. Kliknij prawym przyciskiem myszy jednostkę jednostki zależnej i wybierz pozycję Właściwości.
* Azure AD — ustawienie jest konfigurowane Azure Portal [we](https://portal.azure.com/) właściwościach logowania jednokrotnego poszczególnych aplikacji.

| Ustawienie konfiguracji| AD FS| Jak skonfigurować w usłudze Azure AD| SAML Token |
| - | - | - | - |
| **Adres URL logowania do aplikacji** <p>Adres URL, pod który użytkownik może zalogować się do aplikacji w przepływie SAML zainicjowanego przez dostawcę usług.| Nie dotyczy| Otwieranie podstawowej konfiguracji SAML z logowania opartego na saml| Nie dotyczy |
| **Adres URL odpowiedzi aplikacji** <p>Adres URL aplikacji z perspektywy dostawcy tożsamości. Identyfikator wysyła tutaj użytkownika i token po zalogowaniu się użytkownika do tego identyfikatora.  Jest to również nazywane punktem **końcowym konsumenta asercji SAML.**| Wybieranie karty **Punkty** końcowe| Otwieranie podstawowej konfiguracji saml z logowania opartego na saml| Element docelowy w tokenie SAML. Przykładowa wartość: `https://contoso.my.salesforce.com` |
| **Adres URL wylogowania aplikacji** <p>Jest to adres URL, do którego są wysyłane żądania czyszczenia wylogowania, gdy użytkownik wyloguje się z aplikacji. Identyfikator wysyła żądanie wylogowania użytkownika ze wszystkich innych aplikacji.| Wybieranie karty **Punkty** końcowe| Otwieranie podstawowej konfiguracji saml z logowania opartego na saml| Nie dotyczy |
| **Identyfikator aplikacji** <p>Jest to identyfikator aplikacji z perspektywy idP. Wartość adresu URL logowania jest często używana jako identyfikator (ale nie zawsze).  Czasami aplikacja nazywa to "identyfikatorem jednostki".| Wybierz **kartę Identyfikatory**|Otwieranie podstawowej konfiguracji saml z logowania opartego na saml| Mapuje do **elementu Audience** (Odbiorcy) w tokenie SAML. |
| **Metadane federacji aplikacji** <p>Jest to lokalizacja metadanych federacji aplikacji. Używana przez dostawcę tożsamości do automatycznego aktualizowania określonych ustawień konfiguracji, takich jak punkty końcowe lub certyfikaty szyfrowania.| Wybieranie **karty Monitorowanie**| Nie dotyczy. Usługa Azure AD nie obsługuje bezpośredniego zużywania metadanych federacji aplikacji. Metadane federacji można zaimportować ręcznie.| Nie dotyczy |
| **Identyfikator użytkownika/identyfikator nazwy** <p>Atrybut używany do jednoznacznego wskazywania tożsamości użytkownika z usług Azure AD lub AD FS do aplikacji.  Ten atrybut to zazwyczaj nazwa UPN lub adres e-mail użytkownika.| Reguły oświadczenia. W większości przypadków reguła oświadczenia wydaje oświadczenie z typem, który kończy się **na NameIdentifier**.| Identyfikator można znaleźć w nagłówku **Atrybuty użytkownika i oświadczenia**. Domyślnie używana jest wartość UPN| Mapuje na **element NameID** w tokenie SAML. |
| **Inne oświadczenia** <p>Przykłady innych informacji o oświadczeniach, które są często wysyłane od podmiotu tożsamości do aplikacji, obejmują imię, nazwisko, adres e-mail i członkostwo w grupie.| W usługach AD FS są to inne reguły oświadczenia powiązane z jednostką uzależnioną.| Identyfikator można znaleźć w nagłówku Atrybuty użytkownika i **& oświadczenia.** Wybierz pozycję **Wyświetl** i edytuj wszystkie inne atrybuty użytkownika.| Nie dotyczy |

### <a name="map-identity-provider-idp-settings"></a>Mapowanie ustawień dostawcy tożsamości

Skonfiguruj aplikacje tak, aby wskazać usługę Azure AD, a AD FS na logowanie jednokrotne. W tym miejscu koncentrujemy się na aplikacjach SaaS, które korzystają z protokołu SAML. Jednak ta koncepcja dotyczy również niestandardowych aplikacji biznesowych.

> [!NOTE]
> Wartości konfiguracji usługi Azure AD są zgodnie ze wzorcem, w którym identyfikator dzierżawy platformy Azure zastępuje wartość {tenant-id}, a identyfikator aplikacji zastępuje wartość {application-id}. Te informacje znajdują się w oknie [Azure Portal](https://portal.azure.com/) w **Azure Active Directory > Właściwości:**

* Wybierz pozycję Identyfikator katalogu, aby wyświetlić identyfikator dzierżawy.
* Wybierz pozycję Identyfikator aplikacji, aby wyświetlić swój identyfikator aplikacji.

 Na wysokim poziomie zamapuj następujące kluczowe elementy konfiguracji aplikacji SaaS na usługę Azure AD.

| Element| Wartość konfiguracji |
| - | - |
| Wystawca dostawcy tożsamości| https: \/ /sts.windows.net/{identyfikator-dzierżawy}/ |
| Adres URL logowania dostawcy tożsamości| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Adres URL wylogowywu dostawcy tożsamości| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Lokalizacja metadanych federacji| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |

### <a name="map-sso-settings-for-saas-apps"></a>Mapowanie ustawień logowania jednokrotnego dla aplikacji SaaS

Aplikacje SaaS muszą wiedzieć, gdzie wysyłać żądania uwierzytelniania i jak walidować odebrane tokeny. W poniższej tabeli opisano elementy służące do konfigurowania ustawień logowania jednokrotnego w aplikacji oraz ich wartości lub lokalizacje w usługach AD FS i Azure AD

| Ustawienie konfiguracji| AD FS| Jak skonfigurować w usłudze Azure AD |
| - | - | - |
| **Adres URL logowania idP** <p>Adres URL logowania z perspektywy aplikacji (gdzie użytkownik jest przekierowywany do logowania).| Adres AD FS logowania to nazwa usługi feder AD FS, po której następuje "/adfs/ls/". <p>Na przykład: `https://fs.contoso.com/adfs/ls/`| Zastąp element {tenant-id} identyfikatorem dzierżawy. <p> W przypadku aplikacji, które korzystają z protokołu SAML-P: [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>W przypadku aplikacji, które używają WS-Federation protokołu: [https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **Adres URL wylogowywu z usługi IdP**<p>Adres URL wylogowywania z perspektywy aplikacji (gdzie użytkownik jest przekierowywany, gdy zdecyduje się wylogować się z aplikacji).| Adres URL wylogowania jest taki sam jak adres URL logowania lub ten sam adres URL z dołączonym znakiem "wa=wsignout1.0". Na przykład: `https://fs.contoso.com/adfs/ls/?wa=wsignout1.0`| Zastąp element {tenant-id} identyfikatorem dzierżawy.<p>W przypadku aplikacji, które korzystają z protokołu SAML-P:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> W przypadku aplikacji, które używają WS-Federation protokołu: [https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **Certyfikat podpisywania tokenu**<p>Identyfikator używa klucza prywatnego certyfikatu do podpisywania wystawionych tokenów. Weryfikuje, że token pochodzi od tego samego dostawcy tożsamości, z którym aplikacja ma skonfigurowaną relację zaufania.| Certyfikat podpisywania tokenu usług AD FS znajduje się w funkcji zarządzania usługami AD FS w obszarze **Certyfikaty**.| Znajdź go w Azure Portal we właściwościach  logowania pojedynczego aplikacji w obszarze certyfikatu podpisywania **SAML nagłówka**. W tym miejscu możesz pobrać certyfikat, aby następnie przekazać go do aplikacji.  <p>Jeśli aplikacja ma więcej niż jeden certyfikat, wszystkie certyfikaty można znaleźć w pliku XML metadanych federacji. |
| **Identyfikator/"wystawca"**<p>Identyfikator podmiotu tożsamości z perspektywy aplikacji (czasami nazywany "identyfikatorem wystawcy").<p>W tokenie SAML wartość jest wyświetlana jako element Wystawca.| Identyfikatorem usługi AD FS jest zazwyczaj identyfikator usługi federacyjne w usłudze AD FS Management w obszarze **Service > Edytuj usługa federacyjna właściwości.** Na przykład: `http://fs.contoso.com/adfs/services/trust`| Zastąp element {tenant-id} identyfikatorem dzierżawy.<p>https: \/ /sts.windows.net/{identyfikator-dzierżawy}/ |
| **Metadane federacji idP**<p>Lokalizacja publicznie dostępnych metadanych federacji idP. (Niektóre aplikacje używają metadanych federacji jako alternatywy sytuacji, w której administrator indywidualnie konfiguruje adresy URL, identyfikator i certyfikat podpisywania tokenu).| Adres URL AD FS metadanych federacji można znaleźć w usłudze AD FS Management w obszarze **Service > Endpoints > Metadata > Type(** Metadane federacji). Na przykład: `https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| Odpowiadająca wartość dla usługi Azure AD jest zgodna ze wzorcem [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml) . Zastąp element {TenantDomainName} nazwą dzierżawy w formacie "contoso.onmicrosoft.com".   <p>Aby uzyskać więcej informacji, zobacz [Metadane federacji](../azuread-dev/azure-ad-federation-metadata.md). |

## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>Reprezentacja AD FS zabezpieczeń w usłudze Azure AD

Podczas przenoszenia uwierzytelniania aplikacji do usługi Azure AD utwórz mapowania z istniejących zasad zabezpieczeń na ich odpowiedniki lub alternatywne warianty dostępne w usłudze Azure AD. Zapewnienie, że te mapowania można wykonać przy jednoczesnym spełnianiu standardów zabezpieczeń wymaganych przez właścicieli aplikacji, znacznie ułatwia pozostałą część migracji aplikacji.

Dla każdego przykładu reguły pokazujemy, jak wygląda reguła w usłudze AD FS, AD FS równoważny kod języka reguły i jak jest mapowa na usługę Azure AD.

### <a name="map-authorization-rules"></a>Mapowanie reguł autoryzacji

Poniżej przedstawiono przykłady różnych typów reguł autoryzacji w usłudze AD FS oraz sposób mapowania ich na usługę Azure AD.

#### <a name="example-1-permit-access-to-all-users"></a>Przykład 1: Zezwolenie na dostęp wszystkim użytkownikom

Zezwalaj na dostęp do wszystkich użytkowników w AD FS:

  ![Zrzut ekranu przedstawia okno dialogowe Konfigurowanie Sign-On saml.](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-1.png)

Mapuje to do usługi Azure AD w jeden z następujących sposobów:

1. Dla **ustawienia Wymagane przypisanie użytkownika ustaw** wartość **Nie.**

    ![edytowanie zasad kontroli dostępu dla aplikacji SaaS ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    > [!Note]
    > Ustawienie **Wymagane przypisanie użytkownika** na **Tak** wymaga przypisania użytkowników do aplikacji w celu uzyskania dostępu. W przypadku ustawienia **na wartość Nie** wszyscy użytkownicy mają dostęp. Ten przełącznik nie kontroluje tego, co użytkownicy widzą w **Moje aplikacje** użytkownika.

1. Na karcie **Użytkownicy i grupy przypisz** aplikację do grupy automatycznej **Wszyscy** użytkownicy. Aby grupa [Wszyscy użytkownicy było dostępna,](../enterprise-users/groups-create-rule.md)  należy włączyć grupy dynamiczne w dzierżawie usługi Azure AD.

    ![Moje aplikacje SaaS w usłudze Azure AD ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)

#### <a name="example-2-allow-a-group-explicitly"></a>Przykład 2: Jawne zezwalanie na grupę

Jawna autoryzacja grupy w AD FS:

  ![Zrzut ekranu przedstawiający okno dialogowe Edytowanie reguły dla reguły Zezwalaj administratorom domeny na oświadczenie.](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)

Aby zamapować tę regułę na usługę Azure AD:

1. W [Azure Portal](https://portal.azure.com/)utwórz [grupę użytkowników odpowiadającą](../fundamentals/active-directory-groups-create-azure-portal.md) grupie użytkowników z AD FS.
1. Przypisz uprawnienia aplikacji do grupy:

    ![Dodawanie przypisania ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)

#### <a name="example-3-authorize-a-specific-user"></a>Przykład 3: Autoryzowanie określonego użytkownika

Jawna autoryzacja użytkownika w AD FS:

  ![Zrzut ekranu przedstawiający okno dialogowe Edytowanie reguły dla reguły Zezwalaj na określone oświadczenie użytkownika z typem oświadczenia przychodzącego Podstawowe identyfikatory ID.](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

Aby zamapować tę regułę na usługę Azure AD:

* W [Azure Portal](https://portal.azure.com/)dodaj użytkownika do aplikacji za pośrednictwem karty Dodaj przypisanie aplikacji, jak pokazano poniżej:

    ![Moje aplikacje SaaS na platformie Azure ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

### <a name="map-multi-factor-authentication-rules"></a>Mapowanie reguł uwierzytelniania wieloskładnikowego

Lokalne wdrożenie usługi [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) i usługi AD FS nadal działa po migracji, ponieważ użytkownik jest federowany z AD FS. Rozważ jednak migrowanie do wbudowanych funkcji uwierzytelniania wieloskładnikowego platformy Azure, które są powiązane z przepływami pracy dostępu warunkowego usługi Azure AD.

Poniżej przedstawiono przykłady typów reguł MFA w usłudze AD FS oraz sposób mapowania ich na usługę Azure AD na podstawie różnych warunków.

Ustawienia reguły usługi MFA w AD FS:

  ![Zrzut ekranu przedstawia warunki dla usługi Azure A D w Azure Portal.](media/migrate-adfs-apps-to-azure/mfa-settings-common-for-all-examples.png)

#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>Przykład 1: Wymuszanie uwierzytelniania wieloskładnikowego na podstawie użytkowników/grup

Selektor użytkowników/grup jest regułą, która umożliwia wymuszanie uwierzytelniania wieloskładnikowego dla grupę (identyfikator SID grupy) lub na użytkownika (podstawowy identyfikator SID). Oprócz przypisań użytkowników/grup wszystkie dodatkowe pola wyboru w interfejsie użytkownika konfiguracji usługi AD FS MFA działają jako dodatkowe reguły, które są oceniane po wymuszenia reguły użytkowników/grup.

Określ reguły uwierzytelniania wieloskładnikowego dla użytkownika lub grupy w usłudze Azure AD:

1. Utwórz nowe [zasady dostępu warunkowego.](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)
1. Wybierz pozycję **Przypisania**. Dodaj użytkowników lub grupy, dla których chcesz wymusić usługę MFA.
1. Skonfiguruj **opcje Kontroli dostępu,** jak pokazano poniżej:

    ‎![Zrzut ekranu przedstawia okienko Udzielanie, w którym można udzielić dostępu.](media/migrate-adfs-apps-to-azure/mfa-users-groups.png)

 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>Przykład 2: Wymuszanie uwierzytelniania wieloskładnikowego dla niezarejestrowanych urządzeń

Określ reguły uwierzytelniania wieloskładnikowego dla niezarejestrowanych urządzeń w usłudze Azure AD:

1. Utwórz nowe [zasady dostępu warunkowego.](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)
1. W **ustawieniach Przypisania ustaw** **wartość Wszyscy użytkownicy.**
1. Skonfiguruj **opcje Kontroli dostępu,** jak pokazano poniżej:

    ![Zrzut ekranu przedstawiający okienko Udzielanie, w którym można udzielić dostępu i określić inne ograniczenia.](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

Po wybraniu  opcji Wymagaj wielu kontrolek na wartość Wymagaj jednej z wybranych kontrolek oznacza **to,** że jeśli którykolwiek z warunków określonych przez użytkownika zostanie spełniony przez użytkownika, użytkownikowi zostanie udzielony dostęp do aplikacji.

#### <a name="example-3-enforce-mfa-based-on-location"></a>Przykład 3: Wymuszanie uwierzytelniania wieloskładnikowego na podstawie lokalizacji

Określ reguły uwierzytelniania wieloskładnikowego na podstawie lokalizacji użytkownika w usłudze Azure AD:

1. Utwórz nowe [zasady dostępu warunkowego.](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)
1. W **ustawieniach Przypisania ustaw** **wartość Wszyscy użytkownicy.**
1. [Skonfiguruj nazwane lokalizacje w usłudze Azure AD.](../reports-monitoring/quickstart-configure-named-locations.md) W przeciwnym razie federacja z wewnątrz sieci firmowej jest zaufana.
1. Skonfiguruj reguły **warunków,** aby określić lokalizacje, dla których chcesz wymusić uwierzytelniania wieloskładnikowego.

    ![Zrzut ekranu przedstawiający okienko Lokalizacje dla pozycji Reguły warunków.](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

1. Skonfiguruj **opcje Kontroli dostępu,** jak pokazano poniżej:

    ![Mapowanie zasad kontroli dostępu](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

### <a name="map-emit-attributes-as-claims-rule"></a>Mapowanie atrybutów emitowania jako reguły oświadczeń

Emituj atrybuty jako regułę oświadczeń w AD FS:

  ![Zrzut ekranu przedstawia okno dialogowe Edytowanie reguły dla atrybutów emitowania jako oświadczeń.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-1.png)

Aby zamapować regułę na usługę Azure AD:

1. W [Azure Portal](https://portal.azure.com/)wybierz pozycję **Aplikacje**  dla przedsiębiorstw, a następnie pozycję Logowanie pojedyncze, aby wyświetlić konfigurację logowania opartego na technologii SAML:

    ![Zrzut ekranu przedstawia stronę logowania pojedynczego dla aplikacji dla przedsiębiorstw.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-2.png)

1. Wybierz **pozycję Edytuj** (wyróżniona), aby zmodyfikować atrybuty:

    ![Jest to strona edytowania atrybutów i oświadczeń użytkownika](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-3.png)

### <a name="map-built-in-access-control-policies"></a>Mapowanie wbudowanych zasad kontroli dostępu

Wbudowane zasady kontroli dostępu w programie AD FS 2016:

  ![Wbudowana kontrola dostępu w usłudze Azure AD](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-1.png)

Aby zaimplementować wbudowane zasady w usłudze Azure AD, użyj nowych zasad dostępu warunkowego i skonfiguruj mechanizmy kontroli dostępu lub użyj niestandardowego projektanta zasad w programie AD FS 2016, aby skonfigurować zasady kontroli dostępu. [](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json) Edytor reguł zawiera wyczerpującą listę opcji Zezwolenia i Z wyjątkiem, które mogą ułatwić wszelkiego rodzaju permutacje.

  ![Zasady kontroli dostępu w usłudze Azure AD](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-2.png)

W tej tabeli wymieniono kilka przydatnych opcji Zezwolenia i Z wyjątkiem oraz sposób mapowania ich na usługę Azure AD.

| Opcja | Jak skonfigurować opcję Zezwolenia w usłudze Azure AD?| Jak skonfigurować opcję Except w usłudze Azure AD? |
| - | - | - |
| Z określonych sieci| Mapuje na [nazwaną lokalizację w](../reports-monitoring/quickstart-configure-named-locations.md) usłudze Azure AD| Użyj opcji **Wyklucz** dla [zaufanych lokalizacji](../conditional-access/location-condition.md) |
| Z określonych grupy| [Ustawianie przypisania użytkownika/grupy](assign-user-or-group-access-portal.md)| Użyj opcji **Wykluczanie** w grupie Użytkownicy i grupy |
| Z urządzeń z określonym poziomem zaufania| Ustaw tę wartość w **kontrolce Stan urządzenia** w obszarze Przypisania -> warunki| Użyj opcji **Wyklucz** w obszarze Warunek stanu urządzenia i **Uwzględnij wszystkie urządzenia** |
| Z określonymi oświadczeniami w żądaniu| Nie można migrować tego ustawienia| Nie można migrować tego ustawienia |

Oto przykład sposobu konfigurowania opcji Wykluczanie dla zaufanych lokalizacji w Azure Portal:

  ![Zrzut ekranu przedstawiający mapowanie zasad kontroli dostępu](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-3.png)

## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Przechodzenie użytkowników z AD FS do usługi Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Synchronizowanie AD FS grupy w usłudze Azure AD

Podczas mapowania reguł autoryzacji aplikacje uwierzytelniane w usłudze AD FS mogą używać grup usługi Active Directory w celu uzyskania uprawnień. W takim przypadku należy użyć [Azure AD Connect,](https://go.microsoft.com/fwlink/?LinkId=615771) aby zsynchronizować te grupy z usługą Azure AD przed migracją aplikacji. Przed migracją upewnij się, że te grupy i członkostwo zostały zweryfikowane, aby można było udzielić dostępu tym samym użytkownikom podczas migracji aplikacji.

Aby uzyskać więcej informacji, zobacz [Wymagania wstępne dotyczące używania atrybutów grupy synchronizowanych z usługi Active Directory.](../hybrid/how-to-connect-fed-group-claims.md)

### <a name="set-up-user-self-provisioning"></a>Konfigurowanie samodzielnej aprowrowi użytkowników

Niektóre aplikacje SaaS obsługują możliwość samodzielnego aprowizowania użytkowników po pierwszym zalogowaniu się do aplikacji. W usłudze Azure AD aprowizowanie aplikacji oznacza automatyczne tworzenie tożsamości użytkowników i ról w aplikacjach w chmurze[(SaaS),](https://azure.microsoft.com/overview/what-is-saas/)do których użytkownicy muszą uzyskać dostęp. Migrowane użytkownicy mają już konto w aplikacji SaaS. Należy aprowizować wszystkich nowych użytkowników dodanych po migracji. [Przetestuj aprowizowanie](../app-provisioning/user-provisioning.md) aplikacji SaaS po migracji aplikacji.

### <a name="sync-external-users-in-azure-ad"></a>Synchronizowanie użytkowników zewnętrznych w usłudze Azure AD

Istniejących użytkowników zewnętrznych można skonfigurować na następujące dwa sposoby w AD FS:

* **Użytkownicy zewnętrzni z kontem lokalnym w** organizacji — nadal używasz tych kont w taki sam sposób, jak konta użytkowników wewnętrznych. Te konta użytkowników zewnętrznych mają nazwę podstawową w organizacji, chociaż adres e-mail konta może być wskazuje zewnętrznie. W trakcie migracji możesz korzystać z zalet usługi [Azure AD B2B,](../external-identities/what-is-b2b.md) migrując tych użytkowników do używania własnej tożsamości firmowej, gdy taka tożsamość jest dostępna. Usprawnia to proces logowania dla tych użytkowników, ponieważ często są zalogowani przy użyciu własnego logowania firmowego. Administrowanie organizacją jest również łatwiejsze, ponieważ nie trzeba zarządzać kontami użytkowników zewnętrznych.
* **Federowane tożsamości** zewnętrzne — jeśli obecnie federujesz z organizacją zewnętrzną, możesz skorzystać z kilku metod:
  * [Dodaj Azure Active Directory użytkowników współpracy B2B w Azure Portal](../external-identities/add-users-administrator.md). Możesz proaktywnie wysyłać zaproszenia do współpracy B2B z portalu administracyjnego usługi Azure AD do organizacji partnerskiej, aby poszczegłoni członkowie nadal używali używanych przez nich aplikacji i zasobów.
  * [Utwórz samoobsługowy przepływ pracy rejestracji B2B,](../external-identities/self-service-portal.md) który generuje żądanie dla poszczególnych użytkowników w organizacji partnerskiej przy użyciu interfejsu API zaproszenia B2B.

Niezależnie od tego, jak są skonfigurowani istniejący użytkownicy zewnętrzni, prawdopodobnie mają oni uprawnienia skojarzone z ich kontem, w członkostwie w grupie lub w określonych uprawnieniach. Oceń, czy te uprawnienia muszą zostać zmigrowane, czy wyczyszczone. Konta w organizacji, które reprezentują użytkownika zewnętrznego, muszą zostać wyłączone po migracji użytkownika do tożsamości zewnętrznej. Proces migracji powinien zostać omówiony z partnerami biznesowymi, ponieważ może wystąpić przerwa w ich możliwości łączenia się z zasobami.

## <a name="migrate-and-test-your-apps"></a>Migrowanie i testowanie aplikacji

Postępuj zgodnie z procesem migracji szczegółowo w tym artykule. Następnie przejdź do [Azure Portal,](https://aad.portal.azure.com/) aby sprawdzić, czy migracja się udało.

Postępuj zgodnie z tymi instrukcjami:

1. Wybierz **pozycję Aplikacje dla** przedsiębiorstw  >  **Wszystkie** aplikacje i znajdź swoją aplikację na liście.
1. Wybierz **pozycję Zarządzaj**  >  **użytkownikami i grupami,** aby przypisać do aplikacji co najmniej jednego użytkownika lub grupę.
1. Wybierz **pozycję Zarządzaj**  >  **dostępem warunkowym.** Przejrzyj listę zasad i upewnij się, że nie blokujesz dostępu do aplikacji przy użyciu [zasad dostępu warunkowego.](../conditional-access/overview.md)

W zależności od sposobu konfigurowania aplikacji sprawdź, czy logowanie jednokrotne działa prawidłowo.

| Typ uwierzytelniania| Testowanie |
| :- | :- |
| Uwierzytelnianie OAuth/OpenID Connect| Wybierz **pozycję Aplikacje > uprawnienia** i upewnij się, że wyrażasz zgodę na aplikację w ustawieniach użytkownika aplikacji.|
| Logowanie jednokrotne oparte na języku SAML | Użyj [przycisku Testuj ustawienia saml](debug-saml-sso-issues.md) w obszarze **Logowanie pojedyncze.** |
| Password-Based jednokrotne |  Pobierz i zainstaluj [rozszerzenie bezpiecznego logowania myApps.](../user-help/my-apps-portal-end-user-access.md) [-](../user-help/my-apps-portal-end-user-access.md) [](../user-help/my-apps-portal-end-user-access.md) To rozszerzenie ułatwia uruchamianie dowolnych aplikacji w chmurze organizacji, które wymagają korzystania z procesu logowania jednokrotnego. |
| Serwer proxy aplikacji | Upewnij się, że łącznik jest uruchomiony i przypisany do aplikacji. Odwiedź stronę [serwer proxy aplikacji rozwiązywania problemów, aby](application-proxy-troubleshoot.md) uzyskać dalszą pomoc. |

> [!NOTE]
> Pliki cookie ze starego AD FS są utrwalane na maszynach użytkowników. Te pliki cookie mogą powodować problemy z migracją, ponieważ użytkownicy mogą być kierowani do starego AD FS logowania w porównaniu z nowym identyfikatorem logowania usługi Azure AD. Może być konieczne wyczyszczenie plików cookie przeglądarki użytkownika ręcznie lub za pomocą skryptu. Można również użyć System Center Menedżer konfiguracji lub podobnej platformy.

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli w teście zmigrowanych aplikacji występują błędy, rozwiązywanie problemów może być pierwszym krokiem przed powrotem do istniejących AD FS jednostki zależne. Zobacz [How to debug SAML-based single sign-on to applications in Azure Active Directory](debug-saml-sso-issues.md).

### <a name="rollback-migration"></a>Migracja wycofywania

Jeśli migracja zakończy się niepowodzeniem, zalecamy pozostawienie istniejących stronom jednostki zależnej na serwerach AD FS i usunięcie dostępu do jednostki zależnej. Pozwala to na szybkie rezerwowe w razie potrzeby podczas wdrażania.

### <a name="employee-communication"></a>Komunikacja z pracownikiem

Chociaż samo planowane okno przestoju może być minimalne, nadal należy zaplanować proaktywną komunikację tych ram czasowych z pracownikami przy AD FS do usługi Azure AD. Upewnij się, że środowisko aplikacji ma przycisk opinii lub wskaźniki do pomocy technicznej dotyczące problemów.

Po zakończeniu wdrażania możesz poinformować użytkowników o pomyślnym wdrożeniu i przypomnieć im o wszelkich czynnościach, które należy wykonać.

* Poinstruuj użytkowników, [aby Moje aplikacje](https://myapps.microsoft.com) dostępu do wszystkich zmigrowanych aplikacji.
* Przypominanie użytkownikom, że może być konieczne zaktualizowanie ustawień usługi MFA.
* Jeśli Self-Service resetowania hasła, może być konieczne zaktualizowanie lub zweryfikowanie metod uwierzytelniania przez użytkowników. Zobacz [MfA](https://aka.ms/mfatemplates) and SSPR end-user communication templates (Szablony komunikacji użytkownika końcowego usługi MFA i funkcji [SSPR).](https://aka.ms/ssprtemplates)

### <a name="external-user-communication"></a>Komunikacja z użytkownikami zewnętrznymi

Ta grupa użytkowników ma zazwyczaj największe znaczenie w przypadku problemów. Jest to szczególnie istotne, jeśli poziom zabezpieczeń wymaga innego zestawu reguł dostępu warunkowego lub profilów ryzyka dla partnerów zewnętrznych. Upewnij się, że partnerzy zewnętrzni wiedzą o harmonogramie migracji do chmury i mają harmonogram, w którym są zachęcani do uczestnictwa we wdrożeniu pilotażowym, które testuje wszystkie przepływy unikatowe dla współpracy zewnętrznej. Na koniec upewnij się, że mają one sposób uzyskiwania dostępu do pomocy technicznej w przypadku problemów.

## <a name="next-steps"></a>Następne kroki

* Przeczytaj [migrowanie uwierzytelniania aplikacji do usługi Azure AD.](https://aka.ms/migrateapps/whitepaper)
* Skonfiguruj dostęp [warunkowy i](../conditional-access/overview.md) [usługę MFA.](../authentication/concept-mfa-howitworks.md)
* Wypróbuj przykładowy kod krok po kroku: AD FS migracji aplikacji usługi[Azure AD dla deweloperów.](https://aka.ms/adfsplaybook)
