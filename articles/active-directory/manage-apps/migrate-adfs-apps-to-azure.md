---
title: Przeniesienie uwierzytelniania aplikacji z AD FS do Azure Active Directory
description: Ten artykuł ma pomóc organizacjom zrozumieć, jak przenieść aplikacje do usługi Azure AD, z fokusem na federacyjnych aplikacjach SaaS.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/01/2020
ms.author: kenwith
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57d66c844b7e73f1e3326d628f854a9811ca96fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91802705"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Przeniesienie uwierzytelniania aplikacji z Active Directory Federation Services do Azure Active Directory

[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) oferuje uniwersalną platformę do obsługi tożsamości, która umożliwia osobom, partnerom i klientom pojedynczą tożsamość dostęp do aplikacji i współpracę z dowolnego platformy i urządzenia. Usługa Azure AD ma [pełen pakiet funkcji zarządzania tożsamościami](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Ujednolicenie uwierzytelniania aplikacji i autoryzacji do usługi Azure AD zapewnia korzyści oferowane przez te funkcje.

> [!TIP]
> Ten artykuł jest przeznaczony dla użytkowników deweloperów. Menedżerowie projektów i Administratorzy planują przechodzenie aplikacji do usługi Azure AD, należy rozważyć zapoznanie się z naszym [migrowaniem uwierzytelniania aplikacji do usługi Azure AD](https://aka.ms/migrateapps/whitepaper) oficjalny dokument (PDF).

## <a name="introduction"></a>Wprowadzenie

Jeśli masz katalog lokalny, który zawiera konta użytkowników, możesz mieć wiele aplikacji, do których użytkownicy mogą się uwierzytelniać. Każda z tych aplikacji jest skonfigurowana dla użytkowników, którzy mają do nich dostęp przy użyciu ich tożsamości.


Użytkownicy mogą także uwierzytelniać się bezpośrednio w Active Directory lokalnym. Active Directory Federation Services (AD FS) jest standardem opartym na lokalnej usłudze tożsamości. AD FS rozszerza możliwość korzystania z funkcji logowania jednokrotnego między zaufanymi partnerami biznesowymi bez konieczności logowania się do poszczególnych aplikacji. Jest to znane jako Federacja.

Wiele organizacji korzysta z oprogramowania jako usługi (SaaS) lub niestandardowych aplikacji biznesowych (LOB) federacyjnych bezpośrednio do AD FS, obok Microsoft 365 i aplikacji opartych na usłudze Azure AD.

![Aplikacje połączone bezpośrednio lokalnie](media/migrate-adfs-apps-to-azure/app-integration-before-migration1.png)

**Aby zwiększyć bezpieczeństwo aplikacji, celem jest posiadanie jednego zestawu kontroli dostępu i zasad w środowiskach lokalnych i w chmurze**.

![Aplikacje połączone za pomocą usługi Azure AD](media/migrate-adfs-apps-to-azure/app-integration-after-migration1.png)



## <a name="types-of-apps-to-migrate"></a>Typy aplikacji do migracji

Migrowanie całego uwierzytelniania aplikacji do usługi Azure AD jest optymalne, ponieważ zapewnia jedną płaszczyznę kontroli zarządzania tożsamościami i dostępem.

Aplikacje mogą używać nowoczesnych lub starszych protokołów do uwierzytelniania. Należy rozważyć pierwsze Migrowanie aplikacji korzystających z nowoczesnych protokołów uwierzytelniania (takich jak SAML i Open ID Connect). Te aplikacje można ponownie skonfigurować do uwierzytelniania w usłudze Azure AD za pomocą wbudowanego łącznika w galerii aplikacji lub przez zarejestrowanie aplikacji w usłudze Azure AD. Aplikacje korzystające ze starszych protokołów można zintegrować przy użyciu [serwera proxy aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy).

Aby uzyskać więcej informacji, zobacz [jakie typy aplikacji można zintegrować z usługą Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-management)?

Za pomocą [raportu działanie aplikacji AD FS można migrować aplikacje do usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity) , jeśli [Azure AD Connect Health włączona](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs).

### <a name="the-migration-process"></a>Proces migracji

Podczas procesu przechodzenia uwierzytelniania aplikacji do usługi Azure AD należy odpowiednio przetestować aplikacje i konfigurację. Zalecamy używanie istniejących środowisk testowych do testowania migracji przenoszonego do środowiska produkcyjnego. Jeśli środowisko testowe nie jest obecnie dostępne, można je skonfigurować za pomocą [Azure App Service](https://azure.microsoft.com/services/app-service/) lub [Virtual Machines platformy Azure](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB), w zależności od architektury aplikacji.

Możesz skonfigurować oddzielną dzierżawę usługi Azure AD, która będzie używana podczas tworzenia konfiguracji aplikacji.

Proces migracji może wyglądać następująco:

**Etap 1 — bieżący stan: trwa uwierzytelnianie aplikacji produkcyjnej za pomocą AD FS**

![Etap migracji 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)


**Etap 2 — opcjonalne: testowanie wystąpienia aplikacji wskazującej na przetestowanie dzierżawy platformy Azure**

Zaktualizuj konfigurację, aby wskazywała wystąpienie testowe aplikacji na test dzierżawy usługi Azure AD i wprowadzić wymagane zmiany. Aplikacja może być testowana z użytkownikami w testowej dzierżawie usługi Azure AD. W trakcie procesu tworzenia programu można używać narzędzi, takich jak [programu Fiddler](https://www.telerik.com/fiddler) , do porównywania i weryfikowania żądań i odpowiedzi.

Jeśli skonfigurowanie oddzielnej dzierżawy testowej nie jest możliwe, Pomiń ten etap i przeprowadź testowe wystąpienie aplikacji, a następnie wskaż swoją produkcyjną dzierżawę usługi Azure AD, zgodnie z opisem w części etap 3 poniżej.

![Etap migracji 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

**Etap 3 — Testowanie aplikacji wskazującej na produkcyjną dzierżawę platformy Azure**

Zaktualizuj konfigurację, aby wskazywała wystąpienie testowe aplikacji do wystąpienia produkcyjnego platformy Azure. Teraz można testować z użytkownikami w wystąpieniu produkcyjnym. W razie potrzeby zapoznaj się z sekcją w tym artykule dotyczącym przechodzenia użytkowników.

![Etap migracji 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

**Etap 4 — aplikacja produkcyjna wskazująca na produkcyjną dzierżawę usługi AD**

Zaktualizuj konfigurację aplikacji produkcyjnej, aby wskazywała swoją produkcyjną dzierżawę platformy Azure.

![Etap migracji 1 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 Aplikacje, które uwierzytelniają się za pomocą AD FS mogą używać grup Active Directory w celu uzyskania uprawnień. Aby synchronizować dane tożsamości między środowiskiem lokalnym i usługą Azure AD przed rozpoczęciem migracji, użyj [synchronizacji Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) . Sprawdź te grupy i członkostwo przed migracją, aby umożliwić dostęp do tych samych użytkowników podczas migrowania aplikacji.

### <a name="line-of-business-lob-apps"></a>Aplikacje biznesowe (LOB)

Aplikacje biznesowe są opracowywane wewnętrznie przez organizację lub dostępne jako standardowe produkty spakowane zainstalowane w centrum danych. Przykładami mogą być aplikacje oparte na aplikacjach Windows Identity Foundation i SharePoint (nie SharePoint Online).

Aplikacje LOB korzystające z protokołu OAuth 2,0, OpenID Connect Connect lub WS-Federation mogą być zintegrowane z usługą Azure AD jako [rejestracje aplikacji](../develop/quickstart-register-app.md). Integruj aplikacje niestandardowe korzystające z protokołu SAML 2,0 lub WS-Federation jako [aplikacje nie](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) korzystające z galerii na stronie aplikacje dla przedsiębiorstw w [Azure Portal](https://portal.azure.com/).

## <a name="saml-based-single-sign-on"></a>Logowanie jednokrotne oparte na protokole SAML

Aplikacje korzystające z protokołu SAML 2,0 do uwierzytelniania można skonfigurować dla logowania jednokrotnego [opartego](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) na protokole SAML (SSO przy użyciu protokołu SAML). Za pomocą [logowania jednokrotnego opartego na protokole SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)można mapować użytkowników na określone role aplikacji na podstawie reguł zdefiniowanych w oświadczeniach SAML.

Aby skonfigurować aplikację SaaS dla logowania jednokrotnego opartego na protokole SAML, zobacz Konfigurowanie logowania jednokrotnego [opartego na protokole SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications).

![Zrzuty ekranu użytkownika SAML logowania jednokrotnego ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


Wiele aplikacji SaaS ma [samouczek specyficzny dla aplikacji](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) , który przeprowadzi Cię przez proces konfigurowania logowania jednokrotnego opartego na protokole SAML.

![Samouczek aplikacji](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Migracja niektórych aplikacji jest prosta. Aplikacje o bardziej złożonych wymaganiach, takich jak oświadczenia niestandardowe, mogą wymagać dodatkowej konfiguracji w usłudze Azure AD i/lub programie Azure AD Connect. Aby uzyskać informacje o obsługiwanych mapowaniach oświadczeń, zobacz [Mapowanie oświadczeń w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).

Podczas mapowania atrybutów należy pamiętać o następujących ograniczeniach:

* Nie wszystkie atrybuty, które mogą być wystawiane w AD FS będą wyświetlane w usłudze Azure AD jako atrybuty do emisji do tokenów SAML, nawet jeśli te atrybuty są synchronizowane. Gdy edytujesz atrybut, na liście rozwijanej wartość zostaną wyświetlone różne atrybuty, które są dostępne w usłudze Azure AD. Sprawdź konfigurację [synchronizacji Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) , aby upewnić się, że wymagany atrybut — na przykład samAccountName--jest synchronizowany z usługą Azure AD. Możesz użyć atrybutów rozszerzenia, aby wyemitować wszelkie oświadczenia, które nie jest częścią standardowego schematu użytkownika w usłudze Azure AD.

* W najbardziej typowych scenariuszach aplikacja wymaga tylko oświadczenia NameID i innych typowych oświadczeń identyfikatora użytkownika. Aby określić, czy są wymagane dodatkowe oświadczenia, sprawdź, jakie oświadczenia są wystawiane z AD FS.

* Nie wszystkie oświadczenia mogą być problemy, ponieważ niektóre oświadczenia są chronione w usłudze Azure AD.

* Możliwość korzystania z szyfrowanych tokenów SAML jest teraz w wersji zapoznawczej. Zobacz [How to: Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).



### <a name="software-as-a-service-saas-apps"></a>Aplikacje SaaS (Software as a Service)

Jeśli użytkownik zaloguje się do aplikacji SaaS, takich jak Salesforce, usługi ServiceNow lub Workday, i jest zintegrowany z AD FS, jest używane logowanie federacyjne dla aplikacji SaaS.

Większość aplikacji SaaS można już skonfigurować w usłudze Azure AD. Firma Microsoft ma wiele wstępnie skonfigurowanych połączeń z aplikacjami SaaS w  [galerii aplikacji usługi Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps), co ułatwi Ci przejście. Aplikacje SAML 2,0 można zintegrować z usługą Azure AD za pomocą galerii aplikacji usługi Azure AD lub jako [aplikacje spoza galerii](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app).

Aplikacje używające uwierzytelniania OAuth 2.0 lub OpenID Connect można podobnie zintegrować z usługą Azure AD jako [rejestracje aplikacji](../develop/quickstart-register-app.md). Aplikacje korzystające ze starszych protokołów mogą uwierzytelniać się w usłudze Azure AD za pomocą [usługi azure serwer proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) .

W przypadku problemów z dołączaniem aplikacji SaaS można skontaktować się z [aliasem pomocy technicznej aplikacji SaaS](mailto:SaaSApplicationIntegrations@service.microsoft.com).

**Certyfikaty podpisywania SAML dla logowania jednokrotnego**: certyfikaty podpisywania są ważną częścią dowolnego wdrożenia rejestracji jednokrotnej. Usługa Azure AD tworzy certyfikaty podpisywania w celu ustanowienia federacyjnego logowania jednokrotnego opartego na protokole SAML w aplikacjach SaaS. Po dodaniu galerii lub aplikacji innych niż Galeria skonfigurujesz dodaną aplikację przy użyciu opcji federacyjnego logowania jednokrotnego. Zobacz [Zarządzanie certyfikatami federacyjnego logowania jednokrotnego w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on).

### <a name="apps-and-configurations-that-can-be-moved-today"></a>Aplikacje i konfiguracje, które można przenieść dzisiaj

Aplikacje, które można łatwo przenieść, zawierają aplikacje SAML 2,0, które używają standardowego zestawu elementów konfiguracji i oświadczeń:

* Nazwa główna użytkownika

* Adres e-mail

* Imię

* Nazwisko

* Atrybut alternatywny, taki jak **NameID** w języku SAML, w tym atrybut poczty usługi Azure AD, prefiks poczty, identyfikator pracownika, atrybuty rozszerzenia 1–15 lub lokalny atrybut **SamAccountName**. Aby uzyskać więcej informacji, zobacz [Edytowanie oświadczenia NameIdentifier](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

* Oświadczenia niestandardowe.

Aby przeprowadzić migrację do usługi Azure AD, należy wykonać dodatkowe czynności konfiguracyjne:

* Reguły autoryzacji niestandardowych lub Multi-Factor Authentication (MFA) w programie AD FS. Można je skonfigurować za pomocą funkcji [dostępu warunkowego usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) .

* Aplikacje z wieloma punktami końcowymi adresów URL odpowiedzi. Można je skonfigurować w usłudze Azure AD przy użyciu programu PowerShell lub interfejsu Azure Portal.

* Aplikacje usług federacyjnych w Internecie, takie jak aplikacje programu SharePoint, które wymagają tokenów w wersji 1.1 protokołu SAML. Można je skonfigurować ręcznie przy użyciu programu PowerShell. Możesz również dodać wstępnie zintegrowany szablon ogólny dla aplikacji SharePoint i SAML 1,1 z galerii. Obsługujemy protokół SAML 2,0.

* Złożone reguły przekształcania wystawiania oświadczeń. Informacje o obsługiwanych mapowaniach oświadczeń można znaleźć w temacie:
   *  [Mapowanie oświadczeń w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)
   * [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)



### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Aplikacje i konfiguracje, które nie są obecnie obsługiwane w usłudze Azure AD

Aplikacje, które wymagają następujących możliwości, nie mogą być migrowane dzisiaj.

**Możliwości protokołu**

* Obsługa wzorca WS-Trust ActAs

* Rozpoznawanie artefaktów języka SAML

* Weryfikacja podpisu podpisanych żądań SAML należy zwrócić uwagę, że podpisane żądania są akceptowane, ale podpis nie jest weryfikowany.
Uwzględniając, że usługa Azure AD zwróci tylko token do punktów końcowych wstępnie skonfigurowanych w aplikacji, w większości przypadków weryfikacja podpisu prawdopodobnie nie jest wymagana.

**Oświadczenia w możliwościach tokenu**

* Oświadczenia z magazynów atrybutów innych niż katalog usługi Azure AD, chyba że dane są synchronizowane z usługą Azure AD. Aby uzyskać więcej informacji, zobacz [Omówienie interfejsu API synchronizacji usługi Azure AD](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta).

* Wystawianie atrybutów o wielu wartościach katalogu. Na przykład nie można w tej chwili wydać żądania wielowartościowego dla adresów serwera proxy.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>Mapowanie ustawień aplikacji z AD FS do usługi Azure AD

Migracja rozpoczyna się od oceny konfiguracji aplikacji w środowisku lokalnym i sposobu mapowania tej konfiguracji do usługi Azure AD. AD FS i usługa Azure AD działają podobnie, dlatego pojęcia dotyczące konfigurowania zaufania, logowania i wylogowywania adresów URL oraz identyfikatorów są stosowane w obu przypadkach. Udokumentowanie ustawień konfiguracji AD FS aplikacji, aby można je było łatwo skonfigurować w usłudze Azure AD.

### <a name="map-app-configuration-settings"></a>Mapowanie ustawień konfiguracji aplikacji

W poniższej tabeli opisano niektóre typowe mapowania ustawień między AD FS zaufania jednostki uzależnionej do aplikacji usługi Azure AD Enterprise:

* AD FS — Znajdź ustawienie w AD FS zaufania jednostki uzależnionej dla aplikacji. Kliknij prawym przyciskiem myszy jednostkę uzależnioną i wybierz pozycję Właściwości.

* Azure AD — to ustawienie jest konfigurowane w ramach [Azure Portal](https://portal.azure.com/) we właściwościach logowania jednokrotnego aplikacji.

| Ustawienie konfiguracji| AD FS| Jak skonfigurować usługę Azure AD| Token SAML |
| - | - | - | - |
| **Adres URL logowania do aplikacji** <p>Adres URL użytkownika służącego do logowania się do aplikacji w ramach przepływu SAML zainicjowanego przez dostawcę usług (SP).| Nie dotyczy| Otwórz podstawową konfigurację SAML przy użyciu logowania opartego na protokole SAML| Nie dotyczy |
| **Adres URL odpowiedzi aplikacji** <p>Adres URL aplikacji z perspektywy dostawcy tożsamości (dostawcy tożsamości). Dostawcy tożsamości wysyła użytkownika i token tutaj po zalogowaniu się użytkownika do dostawcy tożsamości.  Jest to również znany jako **punkt końcowy odbiorcy potwierdzenia SAML**.| Wybierz kartę **punkty końcowe**| Otwórz podstawową konfigurację SAML przy użyciu logowania opartego na protokole SAML| Element docelowy w tokenie SAML. Przykładowa wartość: `https://contoso.my.salesforce.com` |
| **Adres URL wylogowania aplikacji** <p>Jest to adres URL, do którego są wysyłane żądania "Czyszczenie wylogowania", gdy użytkownik wyloguje się z aplikacji. Dostawcy tożsamości wysyła żądanie wylogowania użytkownika ze wszystkich innych aplikacji.| Wybierz kartę **punkty końcowe**| Otwórz podstawową konfigurację SAML przy użyciu logowania opartego na protokole SAML| Nie dotyczy |
| **Identyfikator aplikacji** <p>To jest identyfikator aplikacji z perspektywy dostawcy tożsamości. Wartość adresu URL logowania jest często używana dla identyfikatora (ale nie zawsze).  Czasami aplikacja wywołuje ten identyfikator jednostki.| Wybierz kartę **identyfikatory**|Otwórz podstawową konfigurację SAML przy użyciu logowania opartego na protokole SAML| Mapuje do elementu **odbiorców** w tokenie SAML. |
| **Metadane federacji aplikacji** <p>Jest to lokalizacja metadanych federacji aplikacji. Używana przez dostawcę tożsamości do automatycznego aktualizowania określonych ustawień konfiguracji, takich jak punkty końcowe lub certyfikaty szyfrowania.| Wybierz kartę **monitorowanie**| Nie dotyczy. Usługa Azure AD nie obsługuje bezpośredniego korzystania z metadanych federacji aplikacji. Można ręcznie zaimportować metadane federacji.| Nie dotyczy |
| **Identyfikator/nazwa użytkownika** <p>Atrybut używany do jednoznacznego wskazywania tożsamości użytkownika z usług Azure AD lub AD FS do aplikacji.  Ten atrybut jest zazwyczaj nazwą UPN lub adresem e-mail użytkownika.| Reguły dotyczące roszczeń. W większości przypadków reguła dotycząca roszczeń wystawia zgłoszenie z typem kończącym się na NameIdentifier.| Identyfikator można znaleźć pod nagłówkiem **atrybuty użytkownika i oświadczenia**. Domyślnie używana jest nazwa UPN| Mapuje do elementu **NameID** w tokenie języka SAML. |
| **Inne oświadczenia** <p>Przykłady innych informacji o zgłoszeniach, które są często wysyłane z dostawcy tożsamości do aplikacji, obejmują imię, nazwisko, adres E-mail i członkostwo w grupie.| W usługach AD FS są to inne reguły oświadczenia powiązane z jednostką uzależnioną.| Identyfikator można znaleźć w obszarze **atrybuty użytkownika nagłówka & oświadczenia**. Wybierz pozycję **Wyświetl** i edytuj wszystkie inne atrybuty użytkownika.| Nie dotyczy |


### <a name="map-identity-provider-idp-settings"></a>Ustawienia dostawcy tożsamości (dostawcy tożsamości)

Skonfiguruj aplikacje, aby wskazywały usługę Azure AD, a AD FS na logowanie jednokrotne. Tutaj koncentrujemy się na aplikacjach SaaS korzystających z protokołu SAML. Jednak to pojęcie rozszerza również do niestandardowych aplikacji biznesowych.

> [!NOTE]
> Wartości konfiguracji usługi Azure AD są zgodne ze wzorcem, w którym identyfikator dzierżawy platformy Azure zastępuje {dzierżawca}, a identyfikator aplikacji zastępuje {Application-ID}. Te informacje znajdują się w [Azure Portal](https://portal.azure.com/) w obszarze Azure Active Directory > właściwości:

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
| **Certyfikat podpisywania tokenu**<p>Dostawcy tożsamości używa klucza prywatnego certyfikatu do podpisywania wystawionych tokenów. Weryfikuje, że token pochodzi od tego samego dostawcy tożsamości, z którym aplikacja ma skonfigurowaną relację zaufania.| Certyfikat podpisywania tokenu usług AD FS znajduje się w funkcji zarządzania usługami AD FS w obszarze **Certyfikaty**.| Znajdź je w Azure Portal we **właściwościach logowania** jednokrotnego aplikacji, w obszarze **certyfikatu podpisywania SAML**nagłówka. W tym miejscu możesz pobrać certyfikat, aby następnie przekazać go do aplikacji.  <p>Jeśli aplikacja ma więcej niż jeden certyfikat, wszystkie certyfikaty można znaleźć w pliku XML metadanych Federacji. |
| **Identyfikator/"wystawca"**<p>Identyfikator dostawcy tożsamości z perspektywy aplikacji (czasami nazywany "IDENTYFIKATORem wystawcy").<p>W tokenie SAML wartość jest wyświetlana jako element wystawcy.| Identyfikator AD FS jest zazwyczaj identyfikatorem usługi federacyjnej w AD FS Management w obszarze **> Edytowanie właściwości usługa federacyjna**. Na przykład: `http://fs.contoso.com/adfs/services/trust`| Zastąp ciąg {dzierżawca} IDENTYFIKATORem dzierżawy.<p>https: \/ /STS.Windows.NET/{tenant-ID}/ |
| **Metadane federacji dostawcy tożsamości**<p>Lokalizacja publicznie dostępnych metadanych Federacji dostawcy tożsamości. (Niektóre aplikacje używają metadanych federacji jako alternatywy sytuacji, w której administrator indywidualnie konfiguruje adresy URL, identyfikator i certyfikat podpisywania tokenu).| Znajdź adres URL metadanych Federacji AD FS w obszarze Zarządzanie AD FS w obszarze **punkty końcowe > usługi > typ > metadanych: metadane federacji**. Na przykład: `https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| Odpowiadająca wartość w usłudze Azure AD jest zgodna z wzorcem [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml) . Zastąp ciąg {Nazwadomenydzierżawy} nazwą swojej dzierżawy w formacie "contoso.onmicrosoft.com".   <p>Aby uzyskać więcej informacji, zobacz [Metadane federacji](https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-federation-metadata). |


## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>Reprezentacja AD FS zasad zabezpieczeń w usłudze Azure AD

Podczas przechodzenia uwierzytelniania aplikacji do usługi Azure AD Utwórz mapowania z istniejących zasad zabezpieczeń do ich odpowiedników lub alternatywnych wariantów dostępnych w usłudze Azure AD. Zagwarantowanie, że te mapowania można wykonać, a zgodność ze standardami zabezpieczeń wymaganymi przez właścicieli aplikacji spowoduje, że pozostała część migracji aplikacji będzie znacznie łatwiejsza.

W przypadku każdego typu reguły i jego przykładów sugerujemy tutaj, jak wygląda reguła w AD FS, odpowiedni kod języka reguł AD FS i sposób mapowania w usłudze Azure AD.

### <a name="map-authorization-rules"></a>Mapowanie reguł autoryzacji

Poniżej przedstawiono przykłady typów reguł autoryzacji w programie AD FS oraz sposób mapowania ich do usługi Azure AD:

#### <a name="example-1-permit-access-to-all-users"></a>Przykład 1: Zezwalanie na dostęp wszystkim użytkownikom

Zezwalaj na dostęp wszystkim użytkownikom w AD FS:

![Etap migracji 1 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


To mapowanie do usługi Azure AD w jeden z następujących sposobów:

W [Azure Portal](https://portal.azure.com/):
* Opcja 1: Ustaw przypisanie użytkownika wymagane na wartość nie ![Edytowanie zasad kontroli dostępu dla aplikacji SaaS ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    Należy pamiętać, że ustawienie wartość tak dla przypisania użytkownika wymaga, aby użytkownicy przypisani do aplikacji mieli dostęp. Po ustawieniu na wartość nie wszyscy użytkownicy mają dostęp. Ten przełącznik nie kontroluje elementów wyświetlanych dla użytkowników w środowisku moje aplikacje.


* Opcja 2: na karcie Użytkownicy i grupy Przypisz aplikację do grupy automatycznej "Wszyscy użytkownicy". <p>
Aby domyślna grupa "Wszyscy użytkownicy" była dostępna, należy [włączyć grupy dynamiczne](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) w dzierżawie usługi Azure AD.

   ![Moje aplikacje SaaS w usłudze Azure AD ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)


#### <a name="example-2-allow-a-group-explicitly"></a>Przykład 2: jawnie Zezwalaj na grupę

Jawne autoryzacja grupy w AD FS:


![Reguły autoryzacji wystawiania ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)


Oto jak reguła mapuje do usługi Azure AD:

W [Azure Portal](https://portal.azure.com/)najpierw [utworzysz grupę użytkowników](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) , która odnosi się do grupy użytkowników z AD FS, a następnie przypiszesz uprawnienia aplikacji do tej grupy:

![Dodaj przypisanie ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)


#### <a name="example-3-authorize-a-specific-user"></a>Przykład 3: autoryzowanie określonego użytkownika

Jawna Autoryzacja użytkownika w AD FS:

![Reguły autoryzacji wystawiania ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

Oto jak reguła mapuje do usługi Azure AD:

W [Azure Portal](https://portal.azure.com/)Dodaj użytkownika do aplikacji za pomocą karty Dodaj przypisanie w aplikacji, jak pokazano poniżej:

![Moje aplikacje SaaS na platformie Azure ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)


### <a name="map-multi-factor-authentication-rules"></a>Mapowanie reguł Multi-Factor Authentication

Lokalne wdrożenie usługi [Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication) i AD FS będzie nadal działały po migracji, ponieważ pracujesz z usługą AD FS. Należy jednak rozważyć Migrowanie wbudowanych funkcji MFA do platformy Azure, które są powiązane z przepływami pracy dostępu warunkowego usługi Azure AD.

Poniżej przedstawiono przykłady typów reguł usługi MFA w AD FS i sposób mapowania ich do usługi Azure AD na podstawie różnych warunków:

Ustawienia reguły MFA w AD FS:

![Ustawienia usługi Azure AD MFA](media/migrate-adfs-apps-to-azure/mfa-location-1.png)


#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>Przykład 1: Wymuszanie uwierzytelniania MFA na podstawie użytkowników/grup

Selektor użytkownika/grupy to reguła, która umożliwia Wymuszanie uwierzytelniania wieloskładnikowego dla poszczególnych grup (SID grupowe) lub na podstawie użytkownika (podstawowego identyfikatora SID). Poza przypisaniami użytkowników/grup wszystkie dodatkowe pola wyboru w INTERFEJSie użytkownika konfiguracji usługi AD FS MFA pełnią rolę dodatkowych reguł, które są oceniane po wymuszeniu reguły użytkownika/grupy.


Określanie reguł MFA dla użytkownika lub grupy w usłudze Azure AD:

1. Utwórz [nowe zasady dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

2. Wybierz pozycję **Przypisania**. Dodaj użytkowników lub grupy, dla których chcesz wymusić uwierzytelnianie MFA.

3. Skonfiguruj opcje **kontroli dostępu** , jak pokazano poniżej:

![Ustawienia usługi AAD MFA](media/migrate-adfs-apps-to-azure/mfa-usersorgroups.png)


 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>Przykład 2: Wymuszanie uwierzytelniania MFA dla niezarejestrowanego urządzenia

Określ reguły uwierzytelniania MFA dla niezarejestrowanego urządzenia w usłudze Azure AD:

1. Utwórz [nowe zasady dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

2. Ustaw **przydziały** dla **wszystkich użytkowników**.

3. Skonfiguruj opcje **kontroli dostępu** , jak pokazano poniżej:

![Ustawienia usługi AAD MFA](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)


Gdy ustawisz opcję dla wielu kontrolek, aby wymagać jednej z wybranych kontrolek, oznacza to, że jeśli którykolwiek z warunków określonych przez pole wyboru zostanie spełniony przez użytkownika, zostanie udzielony dostęp do aplikacji.

#### <a name="example-3-enforce-mfa-based-on-location"></a>Przykład 3: Wymuś uwierzytelnianie MFA na podstawie lokalizacji

Określ reguły MFA na podstawie lokalizacji użytkownika w usłudze Azure AD:

1. Utwórz [nowe zasady dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

1. Ustaw **przydziały** dla **wszystkich użytkowników**.

1. [Skonfiguruj nazwane lokalizacje w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) , w przeciwnym wypadku Federacji z wewnątrz sieci firmowej, jest zaufana.

1. Skonfiguruj **reguły warunków** , aby określić lokalizacje, dla których chcesz wymusić uwierzytelnianie wieloskładnikowe.

![Ustawienia usługi Azure AD MFA](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

5. Skonfiguruj opcje **kontroli dostępu** , jak pokazano poniżej:


![Mapowanie zasad kontroli dostępu](media/migrate-adfs-apps-to-azure/mfa-location-2.png)


### <a name="map-emit-attributes-as-claims-rule"></a>Mapowanie atrybutów emisji jako reguły oświadczeń

Oto przykład sposobu mapowania atrybutów w AD FS:

![Ustawienia usługi Azure AD MFA](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-1.png)


Oto jak reguła mapuje do usługi Azure AD:

W [Azure Portal](https://portal.azure.com/)wybierz pozycję **aplikacje dla przedsiębiorstw**, **Logowanie jednokrotne**i Dodaj **atrybuty tokenu SAML** , jak pokazano poniżej:

![Ustawienia usługi Azure AD MFA](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-2.png)



### <a name="map-built-in-access-control-policies"></a>Mapowanie wbudowanych zasad kontroli dostępu

AD FS 2016 ma kilka wbudowanych zasad kontroli dostępu, spośród których można wybierać:

![Usługa Azure AD oparta na kontroli dostępu](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-1.png)


Aby zaimplementować wbudowane zasady w usłudze Azure AD, można użyć [nowych zasad dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json) i skonfigurować kontrolę dostępu lub użyć niestandardowego projektanta zasad w AD FS 2016, aby skonfigurować zasady kontroli dostępu. Edytor reguł zawiera wyczerpującą listę opcji zezwalania i z wyjątkiem tych, które mogą pomóc w wprowadzeniu wszystkich rodzajów permutacji.

![Zasady kontroli dostępu w usłudze Azure AD](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-2.png)



W tej tabeli wymieniono niektóre użyteczne zezwolenia i opcje, z wyjątkiem opcji i sposobu mapowania na usługę Azure AD.


| Opcja | Jak skonfigurować opcję zezwalania w usłudze Azure AD?| Jak skonfigurować opcję oprócz opcji w usłudze Azure AD? |
| - | - | - |
| Z określonych sieci| Mapowanie do [nazwanej lokalizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) w usłudze Azure AD| Użyj opcji **exclude** dla [zaufanych lokalizacji](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) |
| Z określonych grupy| [Ustawianie przypisania użytkownika/grup](https://docs.microsoft.com/azure/active-directory/manage-apps/assign-user-or-group-access-portal)| Użyj opcji **Wyklucz** w obszarze Użytkownicy i grupy |
| Z urządzeń z określonym poziomem zaufania| Ustaw tę wartość z kontrolki "stan urządzenia" w obszarze przypisania — warunki >| Użyj opcji **Wyklucz** w obszarze Stan urządzenia i Uwzględnij **wszystkie urządzenia** |
| Z określonymi oświadczeniami w żądaniu| Nie można migrować tego ustawienia| Nie można migrować tego ustawienia |


Przykład sposobu konfigurowania opcji Exclude dla zaufanych lokalizacji w Azure Portal:

![Zrzut ekranu mapowania zasad kontroli dostępu](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-3.png)



## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Przejście użytkowników z AD FS do usługi Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Synchronizuj grupy AD FS w usłudze Azure AD

Podczas mapowania reguł autoryzacji aplikacje, które uwierzytelniają się za pomocą AD FS mogą używać grup Active Directory do uprawnień. W takim przypadku należy użyć [Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) do synchronizowania tych grup z usługą Azure AD przed migracją aplikacji. Przed migracją upewnij się, że te grupy i członkostwo zostały zweryfikowane, aby można było udzielić dostępu do tych samych użytkowników podczas migrowania aplikacji.

Aby uzyskać więcej informacji, zobacz [wymagania wstępne dotyczące używania atrybutów grup synchronizowanych z Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims).

### <a name="setup-user-self-provisioning"></a>Konfigurowanie samoobsługowego udostępniania użytkownika

Niektóre aplikacje SaaS obsługują możliwość samodzielnego udostępniania użytkownikom podczas pierwszego logowania się do aplikacji. W Azure Active Directory (Azure AD) termin aprowizacji aplikacji dotyczy automatycznego tworzenia tożsamości i ról użytkowników w aplikacjach w chmurze ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)), do których użytkownicy potrzebują dostępu. Użytkownicy, którzy zostali zmigrowani, mają już konto w aplikacji SaaS. Wszyscy nowi użytkownicy dodani po migracji muszą zostać zaobsługiwani. [Zainicjowanie obsługi administracyjnej aplikacji SaaS](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) po przeprowadzeniu migracji aplikacji.

### <a name="sync-external-users-in-azure-ad"></a>Synchronizuj użytkowników zewnętrznych w usłudze Azure AD

Istniejących użytkowników zewnętrznych można skonfigurować na dwa sposoby na AD FS:

#### <a name="external-users-with-a-local-account-within-your-organization"></a>Użytkownicy zewnętrzni z kontem lokalnym w organizacji

Nadal będzie można korzystać z tych kont w taki sam sposób, w jaki działają wewnętrzne konta użytkowników. Te konta użytkowników zewnętrznych mają nazwę zasady w organizacji, chociaż adres e-mail konta może wskazywać zewnętrznie. Podczas przeprowadzania migracji można korzystać z zalet oferowanych przez [usługę Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) przez Migrowanie tych użytkowników do korzystania z własnej tożsamości firmowej w przypadku, gdy taka tożsamość jest dostępna. Usprawnia to proces logowania dla tych użytkowników, ponieważ często są one zalogowane przy użyciu logowania firmowego. Administracja w organizacji również będzie rozładowaniua, przez co nie będzie już konieczne zarządzanie kontami użytkowników zewnętrznych.

#### <a name="federated-external-identities"></a>Federacyjne tożsamości zewnętrzne

Jeśli obecnie federowanie się z organizacją zewnętrzną, masz kilka metod, które należy wykonać:

* [Dodaj Azure Active Directory użytkowników współpracy B2B w Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator). Możesz proaktywnie wysyłać zaproszenia do współpracy B2B z portalu administracyjnego usługi Azure AD do organizacji partnera dla poszczególnych członków, aby nadal korzystać z aplikacji i zasobów, do których są używane.

* [Utwórz samoobsługowy przepływ pracy tworzenia konta B2B](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal) , który generuje żądanie dla poszczególnych użytkowników w organizacji partnera przy użyciu interfejsu API zaproszenia B2B.

Bez względu na to, jak już skonfigurowano istniejących użytkowników zewnętrznych, mogą oni mieć uprawnienia skojarzone ze swoim kontem w grupie członkostwo lub określone uprawnienia. Oceń, czy te uprawnienia muszą zostać zmigrowane lub oczyszczone. Konta w organizacji, które reprezentują użytkownika zewnętrznego, muszą zostać wyłączone po przeprowadzeniu migracji użytkownika do tożsamości zewnętrznej. Proces migracji należy omówić z partnerami biznesowymi, ponieważ może istnieć przerwy w możliwości łączenia się z zasobami.

## <a name="migrate-and-test-your-apps"></a>Migrowanie i testowanie aplikacji

Postępuj zgodnie z procesem migracji opisanym w tym artykule.

Następnie przejdź do [Azure Portal](https://aad.portal.azure.com/) , aby sprawdzić, czy migracja zakończyła się pomyślnie. Postępuj zgodnie z poniższymi instrukcjami:
1. Wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje** i Znajdź aplikację z listy.

1. Wybierz pozycję **Zarządzaj**  >  **użytkownikami i grupami** , aby przypisać do aplikacji co najmniej jednego użytkownika lub grupę.

1. Wybierz pozycję **Zarządzaj**  >  **dostępem warunkowym**. Przejrzyj listę zasad i upewnij się, że nie blokujesz dostępu do aplikacji za pomocą [zasad dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).

W zależności od sposobu skonfigurowania aplikacji Sprawdź, czy logowanie jednokrotne działa prawidłowo.

| Typ uwierzytelniania| Testowanie |
| - | - |
| Połączenie OAuth/OpenID Connect| Wybierz pozycję **aplikacje dla przedsiębiorstw > uprawnienia** i upewnij się, że masz zgodę na użycie aplikacji w organizacji w ustawieniach użytkownika aplikacji.
‎ |
| Logowanie jednokrotne oparte na języku SAML| Użyj przycisku [Testuj ustawienia SAML](https://docs.microsoft.com/azure/active-directory/develop/howto-v1-debug-saml-sso-issues) w obszarze **Logowanie jednokrotne**.
‎ |
| Password-Based LOGOWANIE JEDNOKROTNE| Pobierz i zainstaluj rozszerzenie " [bezpieczne logowanie do aplikacji](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)" [-](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction) [in Extension](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction). To rozszerzenie ułatwia rozpoczęcie pracy z aplikacjami w chmurze w organizacji, które wymagają korzystania z procesu rejestracji jednokrotnej.
‎ |
| Serwer proxy aplikacji| Upewnij się, że łącznik jest uruchomiony i przypisany do aplikacji. Więcej informacji można znaleźć w [przewodniku rozwiązywania problemów z serwerem proxy aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot) .
‎ |

> [!NOTE]
> Pliki cookie ze starego środowiska AD FS będą nadal trwałe na maszynach użytkownika. Te pliki cookie mogą powodować problemy z migracją, ponieważ użytkownicy mogą być kierowani do starego środowiska logowania AD FS w porównaniu z nowym logowaniem usługi Azure AD. Może być konieczne ręczne wyczyszczenie plików cookie przeglądarki użytkownika lub użycie skryptu. Można również użyć System Center Configuration Manager lub podobnej platformy.

### <a name="troubleshoot"></a>Rozwiązywanie problemów

W przypadku wystąpienia błędów testów zmigrowanych aplikacji Rozwiązywanie problemów może być pierwszym krokiem przed powracaniem do istniejących AD FS jednostek uzależnionych. Zobacz [jak debugować Logowanie jednokrotne oparte na protokole SAML do aplikacji w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-v1-debug-saml-sso-issues).

### <a name="rollback-migration"></a>Wycofywanie migracji

Jeśli migracja nie powiedzie się, zalecamy pozostawienie istniejących jednostek uzależnionych na serwerach AD FS i usunięcie dostępu do jednostek uzależnionych. Pozwoli to na szybkie wycofanie w razie potrzeby podczas wdrażania.

### <a name="end-user-communication"></a>Komunikacja z użytkownikami końcowymi

Chociaż planowane okno przestoju może być minimalne, nadal należy zaplanować przekazywanie tych okresów do pracowników, jednocześnie przecinanie z AD FS do usługi Azure AD. Upewnij się, że środowisko Twojej aplikacji ma przycisk opinii lub wskaźniki do pomocy technicznej, aby uzyskać problemy.

Po zakończeniu wdrażania można wysłać komunikat informujący użytkowników o pomyślnym wdrożeniu i przypomnieniu o wszelkich nowych krokach, które muszą zostać wykonane.

* Poinstruuj użytkowników, aby używali [aplikacji](https://myapps.microsoft.com) do uzyskiwania dostępu do wszystkich zmigrowanych aplikacji.

* Przypomnij użytkownikom, że mogą oni potrzebować zaktualizować ustawienia usługi MFA.

* W przypadku wdrożenia Self-Service resetowania hasła użytkownicy mogą chcieć zaktualizować lub zweryfikować swoje metody uwierzytelniania. Zobacz szablony komunikacji usługi [MFA](https://aka.ms/mfatemplates) i [SSPR](https://aka.ms/ssprtemplates) dla użytkowników końcowych.

Komunikacja z użytkownikami zewnętrznymi: Ta grupa użytkowników jest zazwyczaj najbardziej krytycznym problemem w przypadku problemów. Jest to szczególnie prawdziwe, jeśli stan zabezpieczeń wymusza różne zestawy reguł dostępu warunkowego lub profile ryzyka dla partnerów zewnętrznych. Upewnij się, że partnerzy zewnętrzni są świadomi harmonogramu migracji do chmury i mają przedział czasu, w którym zaleca się uczestnictwo w wdrożeniu pilotażowym, które testuje wszystkie przepływy w ramach współpracy zewnętrznej. Na koniec upewnij się, że masz dostęp do pomocy technicznej w przypadku problemów z przerywaniem.

## <a name="next-steps"></a>Następne kroki
Odczytywanie  [migracji uwierzytelniania aplikacji do usługi Azure AD](https://aka.ms/migrateapps/whitepaper)<p>
Konfigurowanie [dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) i usługi [MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)
