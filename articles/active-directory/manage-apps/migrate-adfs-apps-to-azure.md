---
title: Przeniesienie uwierzytelniania aplikacji z AD FS do Azure Active Directory
description: Dowiedz się, w jaki sposób używać Azure Active Directory do zastępowania Active Directory Federation Services (AD FS), co umożliwia użytkownikom logowanie jednokrotne do wszystkich aplikacji.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: ee1d863ccb974b30213179a1aba9e27d5a3a2bda
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418469"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Przenoszenie uwierzytelniania aplikacji z usługi Active Directory Federation Services do usługi Azure Active Directory

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) oferuje uniwersalną platformę do obsługi tożsamości, która umożliwia osobom, partnerom i klientom pojedynczą tożsamość dostęp do aplikacji i współpracę z dowolnego platformy i urządzenia. Usługa Azure AD ma [pełen pakiet funkcji zarządzania tożsamościami](../fundamentals/active-directory-whatis.md). Ujednolicenie uwierzytelniania aplikacji i autoryzacji do usługi Azure AD zapewnia następujące korzyści.

> [!TIP]
> Ten artykuł jest przeznaczony dla użytkowników deweloperów. Menedżerowie projektów i Administratorzy planują przenoszenie aplikacji do usługi Azure AD należy rozważyć odczytywanie [migracji aplikacji do usługi Azure AD](migrate-application-authentication-to-azure-active-directory.md).

## <a name="azure-ad-benefits"></a>Korzyści z usługi Azure AD

Jeśli masz katalog lokalny, który zawiera konta użytkowników, możesz mieć wiele aplikacji, do których użytkownicy mogą się uwierzytelniać. Każda z tych aplikacji jest skonfigurowana dla użytkowników, którzy mają do nich dostęp przy użyciu ich tożsamości.

Użytkownicy mogą także uwierzytelniać się bezpośrednio w Active Directory lokalnym. Active Directory Federation Services (AD FS) to oparta na standardach lokalna usługa tożsamości. Rozszerza możliwość korzystania z funkcji logowania jednokrotnego między zaufanymi partnerami biznesowymi, aby użytkownicy nie musieli logować się niezależnie do poszczególnych aplikacji. Jest to tzw. Tożsamość federacyjna.

Wiele organizacji korzysta z oprogramowania jako usługi (SaaS) lub niestandardowych aplikacji biznesowych federacyjnych bezpośrednio do AD FS, obok Microsoft 365 i aplikacji opartych na usłudze Azure AD.

  ![Aplikacje połączone bezpośrednio lokalnie](media/migrate-adfs-apps-to-azure/app-integration-before-migration-1.png)

> [!Important]
> Aby zwiększyć bezpieczeństwo aplikacji, celem jest posiadanie jednego zestawu kontroli dostępu i zasad w środowiskach lokalnych i w chmurze.

  ![Aplikacje połączone za pomocą usługi Azure AD](media/migrate-adfs-apps-to-azure/app-integration-after-migration-1.png)

## <a name="types-of-apps-to-migrate"></a>Typy aplikacji do migracji

Migrowanie całego uwierzytelniania aplikacji do usługi Azure AD jest optymalne, ponieważ zapewnia jedną płaszczyznę kontroli zarządzania tożsamościami i dostępem.

Aplikacje mogą używać nowoczesnych lub starszych protokołów do uwierzytelniania. Planując migrację do usługi Azure AD, należy rozważyć Migrowanie aplikacji korzystających z nowoczesnych protokołów uwierzytelniania (takich jak SAML i Open ID Connect). Te aplikacje można ponownie skonfigurować do uwierzytelniania w usłudze Azure AD za pomocą wbudowanego łącznika z galerii aplikacji platformy Azure lub przez zarejestrowanie aplikacji w usłudze Azure AD. Aplikacje korzystające ze starszych protokołów można zintegrować przy użyciu serwera proxy aplikacji.

Aby uzyskać więcej informacji, zobacz:

* [Publikowanie aplikacji lokalnych dla użytkowników zdalnych za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD](what-is-application-proxy.md).
* [Co to jest zarządzanie aplikacjami?](what-is-application-management.md)
* [AD FS raport działania aplikacji do migrowania aplikacji do usługi Azure AD](migrate-adfs-application-activity.md).
* [Monitoruj AD FS przy użyciu Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md).

### <a name="the-migration-process"></a>Proces migracji

Podczas procesu przechodzenia uwierzytelniania aplikacji do usługi Azure AD Przetestuj swoje aplikacje i konfigurację. Zalecamy używanie istniejących środowisk testowych do testowania migracji po przejściu do środowiska produkcyjnego. Jeśli środowisko testowe nie jest obecnie dostępne, można je skonfigurować za pomocą [Azure App Service](https://azure.microsoft.com/services/app-service/) lub [Virtual Machines platformy Azure](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB), w zależności od architektury aplikacji.

Można skonfigurować oddzielną dzierżawę usługi Azure AD, na której można opracowywać konfiguracje aplikacji.

Proces migracji może wyglądać następująco:

#### <a name="stage-1--current-state-the-production-app-authenticates-with-ad-fs"></a>Etap 1 — bieżący stan: aplikacja produkcyjna jest uwierzytelniana za pomocą AD FS

  ![Etap migracji 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

#### <a name="stage-2--optional-point-a-test-instance-of-the-app-to-the-test-azure-ad-tenant"></a>Etap 2 — (opcjonalnie) wskazywanie wystąpienia testowego aplikacji do testowej dzierżawy usługi Azure AD

Zaktualizuj konfigurację, aby wskazywała wystąpienie testowe aplikacji na test dzierżawy usługi Azure AD i wprowadzić wymagane zmiany. Aplikacja może być testowana z użytkownikami w testowej dzierżawie usługi Azure AD. W trakcie procesu tworzenia programu można używać narzędzi, takich jak [programu Fiddler](https://www.telerik.com/fiddler) , do porównywania i weryfikowania żądań i odpowiedzi.

Jeśli nie jest możliwe skonfigurowanie oddzielnej dzierżawy testowej, Pomiń ten etap i wskaż wystąpienie testowe aplikacji dla produkcyjnej dzierżawy usługi Azure AD, zgodnie z opisem w części etap 3 poniżej.

  ![Etap migracji 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

#### <a name="stage-3--point-a-test-instance-of-the-app-to-the-production-azure-ad-tenant"></a>Etap 3 — przejście wystąpienia testowego aplikacji do produkcyjnej dzierżawy usługi Azure AD

Zaktualizuj konfigurację, aby wskazywała wystąpienie testowe aplikacji na swoją produkcyjną dzierżawę usługi Azure AD. Teraz można testować użytkowników w dzierżawie produkcyjnej. W razie potrzeby zapoznaj się z sekcją w tym artykule dotyczącym przenoszenia użytkowników.

  ![Etap migracji 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

#### <a name="stage-4--point-the-production-app-to-the-production-azure-ad-tenant"></a>Etap 4 — wskaż aplikację produkcyjną dla produkcyjnej dzierżawy usługi Azure AD

Zaktualizuj konfigurację aplikacji produkcyjnej, aby wskazywała swoją produkcyjną dzierżawę usługi Azure AD.

  ![Etap migracji 4 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 Aplikacje, które uwierzytelniają się za pomocą AD FS mogą używać grup Active Directory w celu uzyskania uprawnień. Aby synchronizować dane tożsamości między środowiskiem lokalnym i usługą Azure AD przed rozpoczęciem migracji, użyj [synchronizacji Azure AD Connect](../hybrid/how-to-connect-sync-whatis.md) . Sprawdź te grupy i członkostwo przed migracją, aby umożliwić dostęp do tych samych użytkowników podczas migrowania aplikacji.

### <a name="line-of-business-apps"></a>Aplikacje biznesowe

Twoje aplikacje biznesowe to te, które zostały opracowane przez organizację lub te, które są w standardowym pakiecie. Przykładami mogą być aplikacje oparte na aplikacjach Windows Identity Foundation i SharePoint (nie SharePoint Online).

Aplikacje biznesowe, które używają protokołu OAuth 2,0, OpenID Connect Connect lub WS-Federation, można zintegrować z usługą Azure AD jako [rejestracje aplikacji](../develop/quickstart-register-app.md). Integruj aplikacje niestandardowe korzystające z protokołu SAML 2,0 lub WS-Federation jako [aplikacje nie](add-application-portal.md) korzystające z galerii na stronie aplikacje dla przedsiębiorstw w [Azure Portal](https://portal.azure.com/).

## <a name="saml-based-single-sign-on"></a>Logowanie jednokrotne oparte na protokole SAML

Aplikacje korzystające z protokołu SAML 2,0 do uwierzytelniania można skonfigurować dla logowania jednokrotnego [opartego](what-is-single-sign-on.md) na protokole SAML. Za pomocą logowania jednokrotnego opartego na protokole SAML można mapować użytkowników na określone role aplikacji na podstawie reguł zdefiniowanych w oświadczeniach SAML.

Aby skonfigurować aplikację SaaS dla logowania jednokrotnego opartego na protokole SAML, zobacz [Szybki Start: Konfigurowanie logowania jednokrotnego opartego na](add-application-portal-setup-sso.md)protokole SAML.

  ![Zrzuty ekranu użytkownika SAML logowania jednokrotnego ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

Wiele aplikacji SaaS ma [samouczek specyficzny dla aplikacji](../saas-apps/tutorial-list.md) , który przeprowadzi Cię przez konfigurację logowania jednokrotnego opartego na protokole SAML.

  ![Samouczek aplikacji](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Migracja niektórych aplikacji jest prosta. Aplikacje o bardziej złożonych wymaganiach, takie jak oświadczenia niestandardowe, mogą wymagać dodatkowej konfiguracji w usłudze Azure AD i/lub Azure AD Connect. Informacje o obsługiwanych mapowaniach oświadczeń można znaleźć w temacie [How to: Dostosowywanie oświadczeń emitowanych w tokenach dla określonej aplikacji w dzierżawie (wersja zapoznawcza)](../develop/active-directory-claims-mapping.md).

Podczas mapowania atrybutów należy pamiętać o następujących ograniczeniach:

* Nie wszystkie atrybuty, które mogą być wystawiane w AD FS są wyświetlane w usłudze Azure AD jako atrybuty do emisji do tokenów SAML, nawet jeśli te atrybuty są synchronizowane. Podczas edytowania atrybutu na liście rozwijanej **wartość** są wyświetlane różne atrybuty, które są dostępne w usłudze Azure AD. Sprawdź konfigurację [Azure AD Connect tematy dotyczące synchronizacji](../hybrid/how-to-connect-sync-whatis.md) , aby upewnić się, że wymagany atrybut — na przykład **sAMAccountName**— jest SYNCHRONIZOWANY z usługą Azure AD. Możesz użyć atrybutów rozszerzenia, aby wyemitować wszelkie oświadczenia, które nie jest częścią standardowego schematu użytkownika w usłudze Azure AD.
* W najbardziej typowych scenariuszach aplikacja wymaga tylko oświadczenia **NameID** i innych typowych oświadczeń identyfikatora użytkownika. Aby określić, czy są wymagane dodatkowe oświadczenia, sprawdź, jakie oświadczenia są wystawiane z AD FS.
* Nie można wystawić wszystkich oświadczeń, ponieważ niektóre oświadczenia są chronione w usłudze Azure AD.
* Możliwość korzystania z szyfrowanych tokenów SAML jest teraz w wersji zapoznawczej. Zobacz [How to: Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](../develop/active-directory-saml-claims-customization.md).

### <a name="software-as-a-service-saas-apps"></a>Aplikacje SaaS (Software as a Service)

Jeśli użytkownicy logują się do aplikacji SaaS, takich jak Salesforce, usługi ServiceNow lub Workday, i są zintegrowane z AD FS, używasz logowania federacyjnego dla aplikacji SaaS.

Większość aplikacji SaaS można skonfigurować w usłudze Azure AD. Firma Microsoft ma wiele wstępnie skonfigurowanych połączeń z aplikacjami SaaS w  [galerii aplikacji usługi Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps), co ułatwia przechodzenie. Aplikacje SAML 2,0 można zintegrować z usługą Azure AD za pomocą galerii aplikacji usługi Azure AD lub jako [aplikacje spoza galerii](add-application-portal.md).

Aplikacje korzystające z protokołu OAuth 2,0 lub OpenID Connect Connect można podobnie zintegrować z usługą Azure AD jako [rejestracje aplikacji](../develop/quickstart-register-app.md). Aplikacje korzystające ze starszych protokołów mogą uwierzytelniać się w usłudze Azure AD za pomocą [usługi azure serwer proxy aplikacji usługi Azure AD](application-proxy.md) .

W przypadku problemów z dołączaniem aplikacji SaaS można skontaktować się z [aliasem pomocy technicznej aplikacji SaaS](mailto:SaaSApplicationIntegrations@service.microsoft.com).

### <a name="saml-signing-certificates-for-sso"></a>Certyfikaty podpisywania SAML dla logowania jednokrotnego

Certyfikaty podpisywania są ważną częścią dowolnego wdrożenia rejestracji jednokrotnej. Usługa Azure AD tworzy certyfikaty podpisywania w celu ustanowienia federacyjnego logowania jednokrotnego opartego na protokole SAML w aplikacjach SaaS. Po dodaniu galerii lub aplikacji innych niż Galeria skonfigurujesz dodaną aplikację przy użyciu opcji federacyjnego logowania jednokrotnego. Zobacz [Zarządzanie certyfikatami federacyjnego logowania jednokrotnego w Azure Active Directory](manage-certificates-for-federated-single-sign-on.md).

### <a name="saml-token-encryption"></a>Szyfrowanie tokenu języka SAML

Zarówno AD FS, jak i usługa Azure AD zapewniają szyfrowanie tokenów — możliwość szyfrowania potwierdzeń zabezpieczeń SAML, które przechodzą do aplikacji. Potwierdzenia są szyfrowane za pomocą klucza publicznego i odszyfrowywane przez aplikację przejmującą z pasującym kluczem prywatnym. Podczas konfigurowania szyfrowania tokenów należy przekazać pliki certyfikatu X. 509 w celu udostępnienia kluczy publicznych.

Aby uzyskać informacje na temat szyfrowania tokenów SAML usługi Azure AD i sposobu ich konfigurowania, zobacz [How to: Configure Azure AD SAML token](howto-saml-token-encryption.md).  

> [!NOTE]
> Szyfrowanie tokenu jest funkcją usługi Azure Active Directory (Azure AD) Premium. Aby dowiedzieć się więcej o wersjach, funkcjach i cenach usługi Azure AD, zobacz [Cennik usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="apps-and-configurations-that-can-be-moved-today"></a>Aplikacje i konfiguracje, które można przenieść dzisiaj

Aplikacje, które można łatwo przenieść, zawierają aplikacje SAML 2,0, które używają standardowego zestawu elementów konfiguracji i oświadczeń. Te elementy standardowe są następujące:

* Nazwa główna użytkownika
* Adres e-mail
* Imię
* Nazwisko
* Atrybut alternatywny, taki jak **NameID** w języku SAML, w tym atrybut poczty usługi Azure AD, prefiks poczty, identyfikator pracownika, atrybuty rozszerzenia 1–15 lub lokalny atrybut **SamAccountName**. Aby uzyskać więcej informacji, zobacz [Edytowanie oświadczenia NameIdentifier](../develop/active-directory-saml-claims-customization.md).
* Oświadczenia niestandardowe.

Aby przeprowadzić migrację do usługi Azure AD, należy wykonać dodatkowe czynności konfiguracyjne:

* Niestandardowe reguły autoryzacji lub uwierzytelniania wieloskładnikowego (MFA) w AD FS. Należy je skonfigurować przy użyciu funkcji [dostępu warunkowego usługi Azure AD](../conditional-access/overview.md) .
* Aplikacje z wieloma punktami końcowymi adresów URL odpowiedzi. Można je skonfigurować w usłudze Azure AD przy użyciu programu PowerShell lub interfejsu Azure Portal.
* Aplikacje usług federacyjnych w Internecie, takie jak aplikacje programu SharePoint, które wymagają tokenów w wersji 1.1 protokołu SAML. Można je skonfigurować ręcznie przy użyciu programu PowerShell. Możesz również dodać wstępnie zintegrowany szablon ogólny dla aplikacji SharePoint i SAML 1,1 z galerii. Obsługujemy protokół SAML 2,0.
* Złożone reguły przekształcania wystawiania oświadczeń. Informacje o obsługiwanych mapowaniach oświadczeń można znaleźć w temacie:
   *  [Mapowanie oświadczeń w Azure Active Directory](../develop/active-directory-claims-mapping.md).
   * [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw w Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Aplikacje i konfiguracje, które nie są obecnie obsługiwane w usłudze Azure AD

Aplikacje, które wymagają pewnych możliwości, nie mogą być migrowane dzisiaj.

#### <a name="protocol-capabilities"></a>Możliwości protokołu

Obecnie nie można migrować aplikacji, które wymagają następujących możliwości protokołów:

* Obsługa wzorca WS-Trust ActAs
* Rozpoznawanie artefaktów języka SAML
* Weryfikacja podpisu podpisanych żądań SAML
  > [!Note]
  > Podpisane żądania są akceptowane, ale podpis nie został zweryfikowany.

  Jeśli usługa Azure AD zwraca tylko token do punktów końcowych, które zostały wstępnie skonfigurowane w aplikacji, w większości przypadków weryfikacja podpisu prawdopodobnie nie jest wymagana.

#### <a name="claims-in-token-capabilities"></a>Oświadczenia w możliwościach tokenu

Obecnie nie można migrować aplikacji, które wymagają następujących oświadczeń w możliwościach tokenu.

* Oświadczenia z magazynów atrybutów innych niż katalog usługi Azure AD, chyba że dane są synchronizowane z usługą Azure AD. Aby uzyskać więcej informacji, zobacz [Omówienie interfejsu API synchronizacji usługi Azure AD](/graph/api/resources/synchronization-overview?view=graph-rest-beta).
* Wystawianie atrybutów o wielu wartościach katalogu. Na przykład nie można w tej chwili wydać żądania wielowartościowego dla adresów serwera proxy.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>Mapowanie ustawień aplikacji z AD FS do usługi Azure AD

Migracja wymaga oceny sposobu, w jaki aplikacja jest konfigurowana lokalnie, a następnie mapowania tej konfiguracji do usługi Azure AD. Usługi AD FS i Azure AD działają podobnie, dlatego pojęcia dotyczące konfigurowania relacji zaufania, adresów URL związanych z logowaniem i wylogowywaniem oraz identyfikatorów można stosować w obydwu przypadkach. Udokumentowanie ustawień konfiguracji AD FS aplikacji, aby można je było łatwo skonfigurować w usłudze Azure AD.

### <a name="map-app-configuration-settings"></a>Mapowanie ustawień konfiguracji aplikacji

W poniższej tabeli opisano niektóre typowe mapowania ustawień między AD FS zaufania jednostki uzależnionej do aplikacji usługi Azure AD Enterprise:

* AD FS — Znajdź ustawienie w AD FS zaufania jednostki uzależnionej dla aplikacji. Kliknij prawym przyciskiem myszy jednostkę uzależnioną i wybierz pozycję Właściwości.
* Azure AD — to ustawienie jest konfigurowane w ramach [Azure Portal](https://portal.azure.com/) we właściwościach logowania jednokrotnego dla każdej aplikacji.

| Ustawienie konfiguracji| AD FS| Jak skonfigurować usługę Azure AD| Token SAML |
| - | - | - | - |
| **Adres URL logowania do aplikacji** <p>Adres URL użytkownika służącego do logowania się do aplikacji w przepływie SAML zainicjowanym przez dostawcę usług (SP).| Nie dotyczy| Otwórz podstawową konfigurację SAML przy użyciu logowania opartego na protokole SAML| Nie dotyczy |
| **Adres URL odpowiedzi aplikacji** <p>Adres URL aplikacji z perspektywy dostawcy tożsamości (dostawcy tożsamości). Dostawcy tożsamości wysyła użytkownika i token tutaj po zalogowaniu się użytkownika do dostawcy tożsamości.  Jest to również znany jako **punkt końcowy odbiorcy potwierdzenia SAML**.| Wybierz kartę **punkty końcowe**| Otwórz podstawową konfigurację SAML przy użyciu logowania opartego na protokole SAML| Element docelowy w tokenie SAML. Przykładowa wartość: `https://contoso.my.salesforce.com` |
| **Adres URL wylogowania aplikacji** <p>Jest to adres URL, do którego są wysyłane żądania czyszczenia wylogowania, gdy użytkownik wyloguje się z aplikacji. Dostawcy tożsamości wysyła żądanie wylogowania użytkownika ze wszystkich innych aplikacji.| Wybierz kartę **punkty końcowe**| Otwórz podstawową konfigurację SAML przy użyciu logowania opartego na protokole SAML| Nie dotyczy |
| **Identyfikator aplikacji** <p>To jest identyfikator aplikacji z perspektywy dostawcy tożsamości. Wartość adresu URL logowania jest często używana jako identyfikator (ale nie zawsze).  Czasami aplikacja wywołuje ten identyfikator jednostki.| Wybierz kartę **identyfikatory**|Otwórz podstawową konfigurację SAML przy użyciu logowania opartego na protokole SAML| Mapuje do elementu **odbiorców** w tokenie SAML. |
| **Metadane federacji aplikacji** <p>Jest to lokalizacja metadanych federacji aplikacji. Używana przez dostawcę tożsamości do automatycznego aktualizowania określonych ustawień konfiguracji, takich jak punkty końcowe lub certyfikaty szyfrowania.| Wybierz kartę **monitorowanie**| Nie dotyczy. Usługa Azure AD nie obsługuje bezpośredniego korzystania z metadanych federacji aplikacji. Można ręcznie zaimportować metadane federacji.| Nie dotyczy |
| **Identyfikator/nazwa użytkownika** <p>Atrybut używany do jednoznacznego wskazywania tożsamości użytkownika z usług Azure AD lub AD FS do aplikacji.  Ten atrybut jest zazwyczaj nazwą UPN lub adresem e-mail użytkownika.| Reguły dotyczące roszczeń. W większości przypadków reguła dotycząca roszczeń wystawia zgłoszenie z typem kończącym się na **NameIdentifier**.| Identyfikator można znaleźć pod nagłówkiem **atrybuty użytkownika i oświadczenia**. Domyślnie używana jest nazwa UPN| Mapuje do elementu **NameID** w tokenie języka SAML. |
| **Inne oświadczenia** <p>Przykłady innych informacji o zgłoszeniach, które są często wysyłane z dostawcy tożsamości do aplikacji, obejmują imię, nazwisko, adres e-mail i członkostwo w grupie.| W usługach AD FS są to inne reguły oświadczenia powiązane z jednostką uzależnioną.| Identyfikator można znaleźć w obszarze **atrybuty użytkownika nagłówka & oświadczenia**. Wybierz pozycję **Wyświetl** i edytuj wszystkie inne atrybuty użytkownika.| Nie dotyczy |

### <a name="map-identity-provider-idp-settings"></a>Ustawienia dostawcy tożsamości (dostawcy tożsamości)

Skonfiguruj aplikacje, aby wskazywały usługę Azure AD, a AD FS na logowanie jednokrotne. Tutaj koncentrujemy się na aplikacjach SaaS korzystających z protokołu SAML. Jednak takie koncepcje rozszerzają się również do niestandardowych aplikacji biznesowych.

> [!NOTE]
> Wartości konfiguracji usługi Azure AD są zgodne ze wzorcem, w którym identyfikator dzierżawy platformy Azure zastępuje {dzierżawca}, a identyfikator aplikacji zastępuje {Application-ID}. Te informacje znajdują się w [Azure Portal](https://portal.azure.com/) w obszarze **Azure Active Directory > właściwości**:

* Wybierz pozycję Identyfikator katalogu, aby wyświetlić swój identyfikator dzierżawy.
* Wybierz pozycję Identyfikator aplikacji, aby wyświetlić identyfikator aplikacji.

 Na wysokim poziomie Mapuj następujące elementy konfiguracji SaaS Apps na usługę Azure AD.

| Element| Wartość konfiguracji |
| - | - |
| Wystawca dostawcy tożsamości| https: \/ /STS.Windows.NET/{tenant-ID}/ |
| Adres URL logowania dostawcy tożsamości| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Adres URL wylogowania dostawcy tożsamości| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Lokalizacja metadanych Federacji| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |

### <a name="map-sso-settings-for-saas-apps"></a>Mapowanie ustawień logowania jednokrotnego dla aplikacji SaaS

Aplikacje SaaS muszą wiedzieć, gdzie wysyłać żądania uwierzytelniania i jak weryfikować odebrane tokeny. W poniższej tabeli opisano elementy konfigurowania ustawień logowania jednokrotnego w aplikacji oraz ich wartości lub lokalizacje w ramach AD FS i usługi Azure AD

| Ustawienie konfiguracji| AD FS| Jak skonfigurować usługę Azure AD |
| - | - | - |
| **Adres URL logowania dostawcy tożsamości** <p>Adres URL logowania dostawcy tożsamości z perspektywy aplikacji (do którego użytkownik jest przekierowywany na potrzeby logowania).| Adres URL logowania AD FS jest nazwą usługi federacyjnej AD FS, po której następuje "/adfs/ls/." <p>Na przykład: `https://fs.contoso.com/adfs/ls/`| Zastąp ciąg {dzierżawca} IDENTYFIKATORem dzierżawy. <p> W przypadku aplikacji korzystających z protokołu SAML-P: [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>W przypadku aplikacji korzystających z protokołu WS-Federation: [https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **Adres URL wylogowania dostawcy tożsamości**<p>Adres URL wylogowania dostawcy tożsamości z perspektywy aplikacji (do którego użytkownik jest przekierowywany, gdy zdecyduje się wylogować się z aplikacji).| Adres URL wylogowania jest taki sam jak adres URL logowania lub jest to ten sam adres URL z dołączonym parametrem "WA = wsignout 1.0". Na przykład: `https://fs.contoso.com/adfs/ls/?wa=wsignout1.0`| Zastąp ciąg {dzierżawca} IDENTYFIKATORem dzierżawy.<p>W przypadku aplikacji korzystających z protokołu SAML-P:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> W przypadku aplikacji korzystających z protokołu WS-Federation: [https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **Certyfikat podpisywania tokenu**<p>Dostawcy tożsamości używa klucza prywatnego certyfikatu do podpisywania wystawionych tokenów. Weryfikuje, że token pochodzi od tego samego dostawcy tożsamości, z którym aplikacja ma skonfigurowaną relację zaufania.| Certyfikat podpisywania tokenu usług AD FS znajduje się w funkcji zarządzania usługami AD FS w obszarze **Certyfikaty**.| Znajdź je w Azure Portal we **właściwościach logowania** jednokrotnego aplikacji, w obszarze **certyfikatu podpisywania SAML** nagłówka. W tym miejscu możesz pobrać certyfikat, aby następnie przekazać go do aplikacji.  <p>Jeśli aplikacja ma więcej niż jeden certyfikat, wszystkie certyfikaty można znaleźć w pliku XML metadanych Federacji. |
| **Identyfikator/"wystawca"**<p>Identyfikator dostawcy tożsamości z perspektywy aplikacji (czasami nazywany "IDENTYFIKATORem wystawcy").<p>W tokenie SAML wartość jest wyświetlana jako element wystawcy.| Identyfikator AD FS jest zazwyczaj identyfikatorem usługi federacyjnej w AD FS Management w obszarze **> Edytowanie właściwości usługa federacyjna**. Na przykład: `http://fs.contoso.com/adfs/services/trust`| Zastąp ciąg {dzierżawca} IDENTYFIKATORem dzierżawy.<p>https: \/ /STS.Windows.NET/{tenant-ID}/ |
| **Metadane federacji dostawcy tożsamości**<p>Lokalizacja publicznie dostępnych metadanych Federacji dostawcy tożsamości. (Niektóre aplikacje używają metadanych federacji jako alternatywy sytuacji, w której administrator indywidualnie konfiguruje adresy URL, identyfikator i certyfikat podpisywania tokenu).| Znajdź adres URL metadanych Federacji AD FS w obszarze Zarządzanie AD FS w obszarze **punkty końcowe > usługi > typ > metadanych: metadane federacji**. Na przykład: `https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| Odpowiadająca wartość w usłudze Azure AD jest zgodna z wzorcem [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml) . Zastąp ciąg {Nazwadomenydzierżawy} nazwą swojej dzierżawy w formacie "contoso.onmicrosoft.com".   <p>Aby uzyskać więcej informacji, zobacz [Metadane federacji](../azuread-dev/azure-ad-federation-metadata.md). |

## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>Reprezentacja AD FS zasad zabezpieczeń w usłudze Azure AD

Podczas przechodzenia uwierzytelniania aplikacji do usługi Azure AD Utwórz mapowania z istniejących zasad zabezpieczeń do ich odpowiedników lub alternatywnych wariantów dostępnych w usłudze Azure AD. Zagwarantowanie, że te mapowania można wykonać, a zgodność ze standardami zabezpieczeń wymaganymi przez właścicieli aplikacji sprawia, że pozostała część migracji aplikacji jest znacznie łatwiejsza.

Dla każdego przykładu reguły pokazujemy, jak wygląda reguła, w AD FS, odpowiedni kod języka reguł AD FS i sposób mapowania do usługi Azure AD.

### <a name="map-authorization-rules"></a>Mapowanie reguł autoryzacji

Poniżej przedstawiono przykłady różnych typów reguł autoryzacji w programie AD FS i sposób mapowania ich do usługi Azure AD.

#### <a name="example-1-permit-access-to-all-users"></a>Przykład 1: Zezwalanie na dostęp wszystkim użytkownikom

Zezwalaj na dostęp wszystkim użytkownikom w AD FS:

  ![Zrzut ekranu przedstawia okno dialogowe Konfigurowanie pojedynczego Sign-On przy użyciu protokołu SAML.](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-1.png)

To mapowanie do usługi Azure AD w jeden z następujących sposobów:

1. Ustaw **przypisanie użytkownika wymagane** na wartość **nie**.

    ![Edytowanie zasad kontroli dostępu dla aplikacji SaaS ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    > [!Note]
    > Ustawienie **przypisywania użytkownika wymagane** dla **opcji tak** wymaga, aby użytkownicy przypisani do aplikacji mieli dostęp. Po ustawieniu na wartość **nie** wszyscy użytkownicy mają dostęp. Ten przełącznik nie kontroluje, co użytkownicy widzą w środowisku **Moje aplikacje** .

1. Na **karcie Użytkownicy i grupy** Przypisz aplikację do grupy **Wszyscy użytkownicy** automatycznie. Aby domyślna grupa **Wszyscy użytkownicy** była dostępna, należy [włączyć grupy dynamiczne](../enterprise-users/groups-create-rule.md) w dzierżawie usługi Azure AD.

    ![Moje aplikacje SaaS w usłudze Azure AD ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)

#### <a name="example-2-allow-a-group-explicitly"></a>Przykład 2: jawnie Zezwalaj na grupę

Jawne autoryzacja grupy w AD FS:

  ![Zrzut ekranu przedstawia okno dialogowe Edytowanie reguły dla reguły Zezwalaj na administratorów domeny.](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)

Aby zmapować tę regułę do usługi Azure AD:

1. W [Azure Portal](https://portal.azure.com/) [Utwórz grupę użytkowników](../fundamentals/active-directory-groups-create-azure-portal.md) , która odnosi się do grupy użytkowników z AD FS.
1. Przypisz uprawnienia aplikacji do grupy:

    ![Dodaj przypisanie ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)

#### <a name="example-3-authorize-a-specific-user"></a>Przykład 3: autoryzowanie określonego użytkownika

Jawna Autoryzacja użytkownika w AD FS:

  ![Zrzut ekranu przedstawia okno dialogowe Edytowanie reguły dla reguły zezwalania określonej użytkownikowi z typem przychodzącym typu "podstawowa S I D".](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

Aby zmapować tę regułę do usługi Azure AD:

* W [Azure Portal](https://portal.azure.com/)Dodaj użytkownika do aplikacji za pomocą karty Dodaj przypisanie w aplikacji, jak pokazano poniżej:

    ![Moje aplikacje SaaS na platformie Azure ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

### <a name="map-multi-factor-authentication-rules"></a>Mapowanie reguł uwierzytelniania wieloskładnikowego

Lokalne wdrożenie usługi [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) i AD FS nadal działa po migracji, ponieważ pracujesz z usługą AD FS. Należy jednak rozważyć Migrowanie wbudowanych funkcji MFA do platformy Azure, które są powiązane z przepływami pracy dostępu warunkowego usługi Azure AD.

Poniżej przedstawiono przykłady typów reguł usługi MFA w AD FS i sposób mapowania ich do usługi Azure AD na podstawie różnych warunków.

Ustawienia reguły MFA w AD FS:

  ![Zrzut ekranu przedstawia warunki dotyczące platformy Azure A D w Azure Portal.](media/migrate-adfs-apps-to-azure/mfa-settings-common-for-all-examples.png)

#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>Przykład 1: Wymuszanie uwierzytelniania MFA na podstawie użytkowników/grup

Selektor użytkownicy/grupy to reguła, która umożliwia Wymuszanie uwierzytelniania wieloskładnikowego na podstawie grupy (SID grupy) lub na użytkownika (podstawowy identyfikator SID). Poza przypisaniami użytkowników/grup wszystkie dodatkowe pola wyboru w interfejsie użytkownika konfiguracji usługi AD FS MFA pełnią rolę dodatkowych reguł, które są oceniane po wymuszeniu reguły użytkownicy/grupy.

Określanie reguł MFA dla użytkownika lub grupy w usłudze Azure AD:

1. Utwórz [nowe zasady dostępu warunkowego](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).
1. Wybierz pozycję **Przypisania**. Dodaj użytkowników lub grupy, dla których chcesz wymusić uwierzytelnianie wieloskładnikowe.
1. Skonfiguruj opcje **kontroli dostępu** , jak pokazano poniżej:

    ‎![Zrzut ekranu przedstawia okienko Grant, w którym można udzielić dostępu.](media/migrate-adfs-apps-to-azure/mfa-users-groups.png)

 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>Przykład 2: Wymuszanie uwierzytelniania MFA dla niezarejestrowanego urządzenia

Określ reguły uwierzytelniania MFA dla niezarejestrowanego urządzenia w usłudze Azure AD:

1. Utwórz [nowe zasady dostępu warunkowego](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).
1. Ustaw **przydziały** dla **wszystkich użytkowników**.
1. Skonfiguruj opcje **kontroli dostępu** , jak pokazano poniżej:

    ![Zrzut ekranu przedstawia okienko Grant, w którym można udzielić dostępu i określić inne ograniczenia.](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

Gdy ustawisz opcję **dla wielu kontrolek** , aby **wymagać jednej z wybranych kontrolek**, oznacza to, że jeśli dowolny z warunków określonych przez pole wyboru zostanie osiągnięty przez użytkownika, użytkownik otrzymuje dostęp do aplikacji.

#### <a name="example-3-enforce-mfa-based-on-location"></a>Przykład 3: Wymuś uwierzytelnianie MFA na podstawie lokalizacji

Określ reguły MFA na podstawie lokalizacji użytkownika w usłudze Azure AD:

1. Utwórz [nowe zasady dostępu warunkowego](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).
1. Ustaw **przydziały** dla **wszystkich użytkowników**.
1. [Skonfiguruj nazwane lokalizacje w usłudze Azure AD](../reports-monitoring/quickstart-configure-named-locations.md). W przeciwnym razie Federacja z wewnątrz sieci firmowej jest zaufana.
1. Skonfiguruj **reguły warunków** , aby określić lokalizacje, dla których chcesz wymusić uwierzytelnianie wieloskładnikowe.

    ![Zrzut ekranu przedstawia okienko lokalizacje dla reguł warunków.](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

1. Skonfiguruj opcje **kontroli dostępu** , jak pokazano poniżej:

    ![Mapowanie zasad kontroli dostępu](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

### <a name="map-emit-attributes-as-claims-rule"></a>Mapowanie atrybutów emisji jako reguły oświadczeń

Emituj atrybuty jako regułę oświadczeń w AD FS:

  ![Zrzut ekranu przedstawia okno dialogowe Edytowanie reguły dla atrybutów emisji jako oświadczenia.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-1.png)

Aby zmapować regułę do usługi Azure AD:

1. W [Azure Portal](https://portal.azure.com/)wybierz pozycję **aplikacje dla przedsiębiorstw** , a następnie zaloguj się **jednokrotne** , aby wyświetlić konfigurację logowania opartego na protokole SAML:

    ![Zrzut ekranu przedstawia stronę logowania jednokrotnego dla aplikacji przedsiębiorstwa.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-2.png)

1. Wybierz pozycję **Edytuj** (wyróżniony), aby zmodyfikować atrybuty:

    ![Ta strona służy do edytowania atrybutów użytkownika i oświadczeń](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-3.png)

### <a name="map-built-in-access-control-policies"></a>Mapowanie wbudowanych zasad kontroli dostępu

Wbudowane zasady kontroli dostępu w AD FS 2016:

  ![Usługa Azure AD oparta na kontroli dostępu](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-1.png)

Aby zaimplementować zasady wbudowane w usłudze Azure AD, należy użyć [nowych zasad dostępu warunkowego](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json) i skonfigurować kontrolę dostępu lub użyć niestandardowego projektanta zasad w AD FS 2016, aby skonfigurować zasady kontroli dostępu. Edytor reguł zawiera wyczerpującą listę opcji zezwalania i z wyjątkiem tych, które mogą pomóc w wprowadzeniu wszystkich rodzajów permutacji.

  ![Zasady kontroli dostępu w usłudze Azure AD](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-2.png)

W tej tabeli wymieniono niektóre użyteczne zezwolenia i opcje, z wyjątkiem opcji i sposobu mapowania na usługę Azure AD.

| Opcja | Jak skonfigurować opcję zezwalania w usłudze Azure AD?| Jak skonfigurować opcję oprócz opcji w usłudze Azure AD? |
| - | - | - |
| Z określonych sieci| Mapowanie do [nazwanej lokalizacji](../reports-monitoring/quickstart-configure-named-locations.md) w usłudze Azure AD| Użyj opcji **exclude** dla [zaufanych lokalizacji](../conditional-access/location-condition.md) |
| Z określonych grupy| [Ustawianie przypisania użytkownika/grup](assign-user-or-group-access-portal.md)| Użyj opcji **Wyklucz** w obszarze Użytkownicy i grupy |
| Z urządzeń z określonym poziomem zaufania| Ustaw tę wartość z kontrolki **stan urządzenia** w obszarze przypisania — warunki >| Użyj opcji **Wyklucz** w obszarze Stan urządzenia i Uwzględnij **wszystkie urządzenia** |
| Z określonymi oświadczeniami w żądaniu| Nie można migrować tego ustawienia| Nie można migrować tego ustawienia |

Poniżej przedstawiono przykład sposobu konfigurowania opcji wykluczania dla zaufanych lokalizacji w Azure Portal:

  ![Zrzut ekranu mapowania zasad kontroli dostępu](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-3.png)

## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Przejście użytkowników z AD FS do usługi Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Synchronizuj grupy AD FS w usłudze Azure AD

Podczas mapowania reguł autoryzacji aplikacje, które uwierzytelniają się za pomocą AD FS mogą używać grup Active Directory do uprawnień. W takim przypadku należy użyć [Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) do synchronizowania tych grup z usługą Azure AD przed migracją aplikacji. Przed migracją upewnij się, że te grupy i członkostwo zostały zweryfikowane, aby można było udzielić dostępu do tych samych użytkowników podczas migrowania aplikacji.

Aby uzyskać więcej informacji, zobacz [wymagania wstępne dotyczące używania atrybutów grup synchronizowanych z Active Directory](../hybrid/how-to-connect-fed-group-claims.md).

### <a name="set-up-user-self-provisioning"></a>Konfigurowanie samoobsługowego udostępniania użytkowników

Niektóre aplikacje SaaS obsługują możliwość samodzielnego udostępniania użytkownikom podczas pierwszego logowania się do aplikacji. W usłudze Azure AD Inicjowanie obsługi aplikacji odwołuje się do automatycznego tworzenia tożsamości i ról użytkowników w aplikacjach w chmurze ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)), do których użytkownicy muszą uzyskać dostęp. Użytkownicy, którym przeprowadzono migrację, mają już konto w aplikacji SaaS. Wszyscy nowi użytkownicy dodani po migracji muszą zostać zaobsługiwani. [Zainicjowanie obsługi administracyjnej aplikacji SaaS](../app-provisioning/user-provisioning.md) po przeprowadzeniu migracji aplikacji.

### <a name="sync-external-users-in-azure-ad"></a>Synchronizuj użytkowników zewnętrznych w usłudze Azure AD

Istniejących użytkowników zewnętrznych można skonfigurować na dwa sposoby w AD FS:

* **Użytkownicy zewnętrzni z kontem lokalnym w organizacji**— te konta nadal są używane w taki sam sposób, w jaki działają wewnętrzne konta użytkowników. Te konta użytkowników zewnętrznych mają nazwę zasady w organizacji, chociaż adres e-mail konta może wskazywać zewnętrznie. Podczas przeprowadzania migracji można korzystać z zalet oferowanych przez [usługę Azure AD B2B](../external-identities/what-is-b2b.md) przez Migrowanie tych użytkowników do korzystania z własnej tożsamości firmowej w przypadku, gdy taka tożsamość jest dostępna. Usprawnia to proces logowania dla tych użytkowników, ponieważ często są one zalogowane przy użyciu logowania firmowego. Administrowanie organizacją jest łatwiejsze i nie jest konieczne zarządzanie kontami użytkowników zewnętrznych.
* **Federacyjne tożsamości zewnętrzne**— Jeśli obecnie federowanie się z organizacją zewnętrzną, masz kilka metod do podjęcia:
  * [Dodaj Azure Active Directory użytkowników współpracy B2B w Azure Portal](../external-identities/add-users-administrator.md). Możesz proaktywnie wysyłać zaproszenia do współpracy B2B z portalu administracyjnego usługi Azure AD do organizacji partnera dla poszczególnych członków, aby nadal korzystać z aplikacji i zasobów, do których są używane.
  * [Utwórz samoobsługowy przepływ pracy tworzenia konta B2B](../external-identities/self-service-portal.md) , który generuje żądanie dla poszczególnych użytkowników w organizacji partnera przy użyciu interfejsu API zaproszenia B2B.

Bez względu na to, jak już skonfigurowano istniejących użytkowników zewnętrznych, mogą oni mieć uprawnienia skojarzone ze swoim kontem w grupie członkostwo lub określone uprawnienia. Oceń, czy te uprawnienia muszą zostać zmigrowane lub oczyszczone. Konta w organizacji, które reprezentują użytkownika zewnętrznego, muszą zostać wyłączone po przeprowadzeniu migracji użytkownika do tożsamości zewnętrznej. Proces migracji należy omówić z partnerami biznesowymi, ponieważ może istnieć przerwy w możliwości łączenia się z zasobami.

## <a name="migrate-and-test-your-apps"></a>Migrowanie i testowanie aplikacji

Postępuj zgodnie z procesem migracji opisanym w tym artykule. Następnie przejdź do [Azure Portal](https://aad.portal.azure.com/) , aby sprawdzić, czy migracja zakończyła się pomyślnie.

Postępuj zgodnie z tymi instrukcjami:

1. Wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje** i Znajdź aplikację z listy.
1. Wybierz pozycję **Zarządzaj**  >  **użytkownikami i grupami** , aby przypisać do aplikacji co najmniej jednego użytkownika lub grupę.
1. Wybierz pozycję **Zarządzaj**  >  **dostępem warunkowym**. Przejrzyj listę zasad i upewnij się, że nie blokujesz dostępu do aplikacji za pomocą [zasad dostępu warunkowego](../conditional-access/overview.md).

W zależności od sposobu skonfigurowania aplikacji Sprawdź, czy logowanie jednokrotne działa prawidłowo.

| Typ uwierzytelniania| Testowanie |
| :- | :- |
| Połączenie OAuth/OpenID Connect| Wybierz pozycję **aplikacje dla przedsiębiorstw > uprawnienia** i upewnij się, że masz zgodę na aplikację w ustawieniach użytkownika aplikacji.|
| Logowanie jednokrotne oparte na języku SAML | Użyj przycisku [Testuj ustawienia SAML](debug-saml-sso-issues.md) w obszarze **Logowanie jednokrotne**. |
| Password-Based LOGOWANIE JEDNOKROTNE |  Pobierz i zainstaluj rozszerzenie " [bezpieczne logowanie do aplikacji](../user-help/my-apps-portal-end-user-access.md)" [-](../user-help/my-apps-portal-end-user-access.md) [](../user-help/my-apps-portal-end-user-access.md). To rozszerzenie ułatwia rozpoczęcie pracy z aplikacjami w chmurze w organizacji, które wymagają korzystania z procesu rejestracji jednokrotnej. |
| Serwer proxy aplikacji | Upewnij się, że łącznik jest uruchomiony i przypisany do aplikacji. Więcej informacji można znaleźć w [przewodniku rozwiązywania problemów z serwerem proxy aplikacji](application-proxy-troubleshoot.md) . |

> [!NOTE]
> Pliki cookie ze starego środowiska AD FS zachowują się na komputerach użytkowników. Te pliki cookie mogą powodować problemy z migracją, ponieważ użytkownicy mogą być kierowani do starego środowiska logowania AD FS w porównaniu z nowym logowaniem usługi Azure AD. Może być konieczne ręczne wyczyszczenie plików cookie przeglądarki użytkownika lub użycie skryptu. Można również użyć System Center Configuration Manager lub podobnej platformy.

### <a name="troubleshoot"></a>Rozwiązywanie problemów

W przypadku wystąpienia błędów testów zmigrowanych aplikacji Rozwiązywanie problemów może być pierwszym krokiem przed powracaniem do istniejących AD FS jednostek uzależnionych. Zobacz [jak debugować Logowanie jednokrotne oparte na protokole SAML do aplikacji w Azure Active Directory](debug-saml-sso-issues.md).

### <a name="rollback-migration"></a>Wycofywanie migracji

Jeśli migracja nie powiedzie się, zalecamy pozostawienie istniejących jednostek uzależnionych na serwerach AD FS i usunięcie dostępu do jednostek uzależnionych. Pozwala to na szybką rezerwę, jeśli jest to możliwe podczas wdrażania.

### <a name="employee-communication"></a>Komunikacja pracownika

Gdy planowane okno przestoju może być minimalne, nadal należy zaplanować komunikację między nimi w tych okresach z pracownikami przy jednoczesnym przechodzeniu między AD FS a usługą Azure AD. Upewnij się, że środowisko Twojej aplikacji ma przycisk opinii lub wskaźniki do pomocy technicznej, aby uzyskać problemy.

Po zakończeniu wdrażania możesz poinformować użytkowników o pomyślnym wdrożeniu i przypomnić je o wszelkich krokach, które muszą zostać wykonane.

* Poinstruuj użytkowników, aby używali [aplikacji](https://myapps.microsoft.com) do uzyskiwania dostępu do wszystkich zmigrowanych aplikacji.
* Przypomnij użytkownikom, że mogą oni potrzebować zaktualizować ustawienia usługi MFA.
* W przypadku wdrożenia Self-Service resetowania hasła użytkownicy mogą chcieć zaktualizować lub zweryfikować swoje metody uwierzytelniania. Zobacz szablony komunikacji usługi [MFA](https://aka.ms/mfatemplates) i [SSPR](https://aka.ms/ssprtemplates) dla użytkowników końcowych.

### <a name="external-user-communication"></a>Komunikacja z użytkownikami zewnętrznymi

Ta grupa użytkowników jest zazwyczaj najbardziej krytycznym wpływem na wypadek problemów. Jest to szczególnie prawdziwe, jeśli stan zabezpieczeń wymusza różne zestawy reguł dostępu warunkowego lub profile ryzyka dla partnerów zewnętrznych. Upewnij się, że partnerzy zewnętrzni są świadomi harmonogramu migracji do chmury i mają przedział czasu, w którym zaleca się uczestnictwo w wdrożeniu pilotażowym, które testuje wszystkie przepływy w ramach współpracy zewnętrznej. Na koniec upewnij się, że masz dostęp do pomocy technicznej w przypadku wystąpienia problemów.

## <a name="next-steps"></a>Następne kroki

* Przeczytaj temat  [Migrowanie uwierzytelniania aplikacji do usługi Azure AD](https://aka.ms/migrateapps/whitepaper).
* Konfigurowanie [dostępu warunkowego](../conditional-access/overview.md) i usługi [MFA](../authentication/concept-mfa-howitworks.md).
* Wypróbuj przykład kodu krokowego:[AD FS do migracji aplikacji usługi Azure AD element PlayBook dla deweloperów](https://aka.ms/adfsplaybook).
