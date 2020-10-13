---
title: Tworzenie aplikacji, które logują się do użytkowników usługi Azure AD
titleSuffix: Microsoft identity platform
description: Pokazuje, w jaki sposób utworzyć aplikację wielodostępną, która może zalogować użytkownika z dowolnego Azure Active Directory dzierżawcy.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 03/17/2020
ms.author: ryanwi
ms.reviewer: jmprieur, lenalepa, sureshja, kkrishna
ms.custom: aaddev
ms.openlocfilehash: 3578562839069eb4b9c99b16d938efe48821fcec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631311"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Instrukcje: Logowanie się dowolnego użytkownika usługi Azure Active Directory za pomocą wzorca aplikacji wielodostępnych

Jeśli oferujesz aplikację oprogramowanie jako usługa (SaaS) w wielu organizacjach, możesz skonfigurować aplikację tak, aby akceptowała logowania z dowolnej dzierżawy usługi Azure Active Directory (Azure AD). Ta konfiguracja jest nazywana *tworzeniem wielu dzierżawców aplikacji*. Użytkownicy w dowolnej dzierżawie usługi Azure AD będą mogli zalogować się do aplikacji po dojściu do korzystania z konta w aplikacji.

Jeśli masz istniejącą aplikację, która ma własny system kont lub obsługuje inne rodzaje logowań z innych dostawców chmury, Dodawanie logowania za pomocą usługi Azure AD z dowolnego dzierżawy jest proste. Po prostu zarejestruj aplikację, Dodaj kod logowania za pośrednictwem OAuth2, OpenID Connect Connect lub SAML i umieść [przycisk "Zaloguj się przy użyciu konta Microsoft"][AAD-App-Branding] w aplikacji.

> [!NOTE]
> W tym artykule przyjęto założenie, że wiesz już, jak kompilować aplikację z jedną dzierżawą dla usługi Azure AD. Jeśli nie, Zacznij od jednego z szybkich samouczków na [stronie głównej przewodnika dla deweloperów][AAD-Dev-Guide].

Istnieją cztery proste kroki umożliwiające przekonwertowanie aplikacji do aplikacji wielodostępnej usługi Azure AD:

1. [Aktualizowanie rejestracji aplikacji w ramach wielu dzierżawców](#update-registration-to-be-multi-tenant)
2. [Aktualizowanie kodu w celu wysyłania żądań do punktu końcowego/typowe](#update-your-code-to-send-requests-to-common)
3. [Zaktualizuj swój kod, aby obsługiwał wiele wartości wystawcy](#update-your-code-to-handle-multiple-issuer-values)
4. [Zrozumienie zgody użytkownika i administratora oraz wprowadzenie odpowiednich zmian w kodzie](#understand-user-and-admin-consent)

Przyjrzyjmy się szczegółowym krokom. Możesz również przejść bezpośrednio do przykładowej kompilacji wielodostępnej [aplikacji sieci Web SaaS, która wywołuje Microsoft Graph przy użyciu usługi Azure AD i OpenID Connect Connect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md).

## <a name="update-registration-to-be-multi-tenant"></a>Aktualizowanie rejestracji w ramach wielu dzierżawców

Domyślnie rejestracje aplikacji sieci Web/interfejsu API w usłudze Azure AD są pojedynczym dzierżawcą. Możesz dokonać rejestracji wielu dzierżawców, wyszukując ustawienia **obsługiwane typy kont** w okienku **uwierzytelnianie** rejestracji aplikacji w [Azure Portal][AZURE-portal] i ustawiając je na **konta w dowolnym katalogu organizacyjnym**.

Aby można było nawiązać aplikację z wieloma dzierżawcami, usługa Azure AD wymaga, aby identyfikator URI aplikacji był globalnie unikatowy. Identyfikator URI identyfikatora aplikacji jest jednym ze sposobów, w jaki aplikacja jest identyfikowana w komunikatach protokołu. W przypadku aplikacji pojedynczej dzierżawy wystarczy, aby identyfikator URI identyfikatora aplikacji był unikatowy w obrębie tej dzierżawy. W przypadku aplikacji wielodostępnej ten identyfikator musi być globalnie unikatowy, dzięki czemu usługa Azure AD będzie mogła znaleźć aplikację we wszystkich dzierżawach. Globalna unikatowość jest wymuszana poprzez wymaganie, aby identyfikator URI identyfikatora aplikacji miał nazwę hosta, która jest zgodna ze zweryfikowaną domeną dzierżawy usługi Azure AD.

Domyślnie aplikacje utworzone za pośrednictwem Azure Portal mają globalnie unikatowy identyfikator URI aplikacji ustawiony podczas tworzenia aplikacji, ale można zmienić tę wartość. Na przykład, jeśli nazwa dzierżawy została contoso.onmicrosoft.com, prawidłowy identyfikator URI aplikacji będzie mieć wartość `https://contoso.onmicrosoft.com/myapp` . Jeśli Twoja dzierżawa ma zweryfikowaną domenę `contoso.com` , prawidłowy identyfikator URI aplikacji również będzie mieć wartość `https://contoso.com/myapp` . Jeśli identyfikator URI identyfikatora aplikacji nie jest zgodny z tym wzorcem, ustawienie aplikacji jako aplikacji wielodostępnej zakończy się niepowodzeniem.

> [!NOTE]
> Natywne rejestracje klienta oraz [aplikacje platformy tożsamości firmy Microsoft](./v2-overview.md) są domyślnie dostępne dla wielu dzierżawców. Nie musisz podejmować żadnych działań w celu przełączenia tych aplikacji do wielu dzierżawców.

## <a name="update-your-code-to-send-requests-to-common"></a>Aktualizowanie kodu w celu wysyłania żądań do/typowe

W jednej aplikacji dzierżawcy żądania logowania są wysyłane do punktu końcowego logowania dzierżawcy. Na przykład dla contoso.onmicrosoft.com punkt końcowy będzie: `https://login.microsoftonline.com/contoso.onmicrosoft.com` . Żądania wysyłane do punktu końcowego dzierżawy mogą logować użytkowników (lub Gości) w tej dzierżawie do aplikacji w tej dzierżawie.

W przypadku aplikacji z wieloma dzierżawcami aplikacja nie wie, z której dzierżawą pochodzi użytkownik, i nie może wysyłać żądań do punktu końcowego dzierżawy. Zamiast tego żądania są wysyłane do punktu końcowego, który ma wszystkie dzierżawy usługi Azure AD: `https://login.microsoftonline.com/common`

Gdy platforma tożsamości firmy Microsoft odbiera żądanie w punkcie końcowym/typowe, podpisuje użytkownika w i, w związku z tym, wykrywa dzierżawę, z której pochodzi użytkownik. Punkt końcowy/typowe współpracuje ze wszystkimi protokołami uwierzytelniania obsługiwanymi przez usługę Azure AD: OpenID Connect Connect, OAuth 2,0, SAML 2,0 i WS-Federation.

Odpowiedź na logowanie do aplikacji zawiera token reprezentujący użytkownika. Wartość wystawcy w tokenie instruuje aplikację, z której pochodzi użytkownik. Po powrocie odpowiedzi z punktu końcowego/typowe wartość wystawcy w tokenie odpowiada dzierżawcy użytkownika.

> [!IMPORTANT]
> Punkt końcowy/typowe nie jest dzierżawcą i nie jest wystawcą. jest tylko multiplekserem. W przypadku korzystania z/typowe logika w aplikacji do weryfikacji tokenów musi zostać zaktualizowana w celu uwzględnienia tego konta.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Zaktualizuj swój kod, aby obsługiwał wiele wartości wystawcy

Aplikacje sieci Web i interfejsy API sieci Web odbierają i weryfikują tokeny z platformy tożsamości firmy Microsoft.

> [!NOTE]
> Chociaż natywne aplikacje klienckie żądają tokenów od platformy tożsamości firmy Microsoft i odbierają je, wysyłają je do interfejsów API, gdzie są weryfikowane. Natywne aplikacje nie weryfikują tokenów i muszą traktować je jako nieprzezroczyste.

Przyjrzyjmy się, jak aplikacja sprawdza poprawność tokenów odbieranych z platformy tożsamości firmy Microsoft. Aplikacja pojedynczej dzierżawy zwykle przyjmuje wartość punktu końcowego, taką jak:

```http
    https://login.microsoftonline.com/contoso.onmicrosoft.com
```

i używa go do konstruowania adresu URL metadanych (w tym przypadku OpenID Connect Connect), takich jak:

```http
    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration
```

Aby pobrać dwie krytyczne informacje, które są używane do weryfikacji tokenów: klucze podpisywania i wartość wystawcy dzierżawcy. Każda dzierżawa usługi Azure AD ma unikatową wartość wystawcy w postaci:

```http
    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/
```

gdzie GUID wartością jest wersja z bezpiecznym zmianami identyfikatora dzierżawy dzierżawy. W przypadku wybrania poprzedniego linku metadanych dla programu `contoso.onmicrosoft.com` można zobaczyć tę wartość wystawcy w dokumencie.

Gdy aplikacja pojedynczej dzierżawy zweryfikuje token, sprawdza podpis tokenu względem kluczy podpisywania z dokumentu metadanych. Ten test pozwala na upewnienie się, że wartość wystawcy w tokenie jest zgodna z tą, która została znaleziona w dokumencie metadanych.

Ponieważ punkt końcowy/typowe nie odpowiada dzierżawcy i nie jest wystawcą, podczas badania wartości wystawcy w metadanych dla/typowe ma adres URL z szablonem, a nie rzeczywistą wartość:

```http
    https://sts.windows.net/{tenantid}/
```

W związku z tym aplikacja wielodostępna nie może zweryfikować tokenów tylko przez dopasowanie wartości wystawcy w metadanych przy użyciu `issuer` wartości w tokenie. Aplikacja wielodostępna wymaga logiki, która decyduje o tym, które wartości wystawcy są prawidłowe i które nie są oparte na części identyfikatora dzierżawy wartości wystawcy.

Jeśli na przykład aplikacja wielodostępna zezwala tylko na logowanie się z określonych dzierżawców, którzy zarejestrowali się w celu korzystania z usługi, musi sprawdzić wartość wystawcy lub `tid` wartość żądania w tokenie, aby upewnić się, że dzierżawa znajduje się na liście subskrybentów. Jeśli aplikacja wielodostępna zajmuje się tylko osobom i nie podejmuje decyzji o dostępie na podstawie dzierżawców, może całkowicie zignorować wartość wystawcy.

W przykładach z [wieloma dzierżawcami][AAD-Samples-MT]weryfikacja wystawcy jest wyłączona, aby umożliwić zalogowanie się w dzierżawie usługi Azure AD.

## <a name="understand-user-and-admin-consent"></a>Zrozumienie zgody użytkownika i administratora

Aby użytkownik mógł zalogować się do aplikacji w usłudze Azure AD, aplikacja musi być reprezentowana w dzierżawie użytkownika. Dzięki temu organizacja może wykonywać takie czynności, jak stosowanie unikatowych zasad, gdy użytkownicy z ich dzierżawy logują się do aplikacji. W przypadku pojedynczej aplikacji dzierżawy ta rejestracja jest prosta. jest to taka, która występuje po zarejestrowaniu aplikacji w [Azure Portal][AZURE-portal].

W przypadku aplikacji z wieloma dzierżawami początkowa Rejestracja aplikacji jest używana w dzierżawie usługi Azure AD używanej przez dewelopera. Gdy użytkownik z innej dzierżawy loguje się do aplikacji po raz pierwszy, usługa Azure AD poprosi o zgodę na uprawnienia wymagane przez aplikację. Jeśli użytkownik wyrazi zgodę, reprezentacja aplikacji zwanej jednostką *usługi* jest tworzona w dzierżawie użytkownika, a logowanie może być kontynuowane. W katalogu zostanie również utworzona delegacja, która rejestruje zgodę użytkownika na aplikację. Aby uzyskać szczegółowe informacje na temat aplikacji i obiektów serviceprincipal aplikacji oraz jak są one ze sobą powiązane, zobacz [obiekty aplikacji i obiekty główne usługi][AAD-App-SP-Objects].

![Przedstawia zgodę na aplikację jednowarstwową][Consent-Single-Tier]

Do tej zgody mają wpływ uprawnienia wymagane przez aplikację. Platforma tożsamości firmy Microsoft obsługuje dwa rodzaje uprawnień, tylko aplikacje i delegowane.

* Delegowane uprawnienie przyznaje aplikacji możliwość działania jako użytkownik zalogowany dla podzbioru rzeczy, które może wykonywać użytkownik. Na przykład można udzielić aplikacji uprawnienia delegowane do odczytu kalendarza zalogowanego użytkownika.
* Uprawnienie tylko do aplikacji jest udzielane bezpośrednio do tożsamości aplikacji. Na przykład można udzielić aplikacji uprawnienia tylko do odczytu listy użytkowników w dzierżawie, niezależnie od tego, kto jest zalogowany do aplikacji.

Niektóre uprawnienia mogą być wysyłane przez zwykłego użytkownika, a inne wymagają zgody administratora dzierżawy.

### <a name="admin-consent"></a>zgoda administratora

Uprawnienia dotyczące tylko aplikacji zawsze wymagają zgody administratora dzierżawy. Jeśli aplikacja żąda uprawnień tylko do aplikacji, a użytkownik próbuje zalogować się do aplikacji, zostanie wyświetlony komunikat o błędzie informujący o tym, że użytkownik nie jest w stanie wyrazić zgody.

Niektóre uprawnienia delegowane wymagają również zgody administratora dzierżawy. Na przykład możliwość zapisu zwrotnego w usłudze Azure AD jako zalogowany użytkownik wymaga zgody administratora dzierżawy. Podobnie jak uprawnienia tylko do aplikacji, jeśli zwykły użytkownik próbuje zalogować się do aplikacji, która żąda delegowanego uprawnienia, które wymaga zgody administratora, aplikacja otrzymuje błąd. Niezależnie od tego, czy uprawnienie wymaga zgody administratora, jest określane przez dewelopera, który opublikował zasób, i można je znaleźć w dokumentacji dotyczącej zasobu. Dokumentacja dotycząca uprawnień dla [interfejsu API Microsoft Graph][MSFT-Graph-permission-scopes] wskazuje, które uprawnienia wymagają zgody administratora.

Jeśli aplikacja korzysta z uprawnień, które wymagają zgody administratora, należy mieć gest, taki jak przycisk lub link, w którym administrator może zainicjować akcję. Żądanie wysyłane przez aplikację dla tej akcji to zwykłe żądanie autoryzacji OAuth2/OpenID Connect połączenia, które zawiera również `prompt=admin_consent` parametr ciągu zapytania. Gdy administrator wyraził zgodę, a jednostka usługi zostanie utworzona w dzierżawie klienta, kolejne żądania logowania nie potrzebują `prompt=admin_consent` parametru. Ze względu na to, że administrator zdecydował się, że żądane uprawnienia są akceptowalne, żaden inny użytkownik w dzierżawie nie zostanie poproszony o zgodę od tego momentu.

Administrator dzierżawy może wyłączyć możliwość wyrażania zgody na aplikacje przez zwykłych użytkowników. Jeśli ta funkcja jest wyłączona, zgoda administratora jest zawsze wymagana do używania aplikacji w dzierżawie. Jeśli chcesz przetestować aplikację z wyłączoną zgodą użytkownika końcowego, możesz znaleźć przełącznik konfiguracji w [Azure Portal][AZURE-portal] w sekcji **[Ustawienia użytkownika](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** w obszarze **aplikacje dla przedsiębiorstw**.

Ten `prompt=admin_consent` parametr może być również używany przez aplikacje żądające uprawnień, które nie wymagają zgody administratora. Przykładem sytuacji, w której będzie on używany, jest to, że aplikacja wymaga środowiska, w którym Administrator dzierżawy jest "jednokrotne", a inni użytkownicy nie otrzymują monitu o zgodę od tego momentu.

Jeśli aplikacja wymaga zgody administratora, a administrator loguje się bez `prompt=admin_consent` wysyłanego parametru, gdy administrator pomyślnie wyraził zgodę na aplikację, zostanie ona zastosowana **tylko do konta użytkownika**. Regularne użytkownicy nadal nie będą mogli zalogować się ani wyrazić zgody na aplikację. Ta funkcja jest przydatna, jeśli chcesz dać administratorowi dzierżawy możliwość eksplorowania aplikacji przed zezwoleniem innym użytkownikom na dostęp.

> [!NOTE]
> Niektóre aplikacje chcą korzystać z funkcji, w których regularnie użytkownicy mogą wyrazić zgodę, a później aplikacja może obejmować uprawnienia administratora i żądania, które wymagają zgody administratora. Obecnie nie ma możliwości wykonania tej czynności za pomocą rejestracji aplikacji w wersji 1.0 w usłudze Azure AD. Korzystanie z punktu końcowego Microsoft Identity platform (v 2.0) umożliwia aplikacjom żądanie uprawnień w czasie wykonywania, a nie w czasie rejestracji, co umożliwia wykonanie tego scenariusza. Aby uzyskać więcej informacji, zobacz [punkt końcowy platformy tożsamości firmy Microsoft][AAD-V2-Dev-Guide].

### <a name="consent-and-multi-tier-applications"></a>Zgody i wielowarstwowe aplikacje

Aplikacja może mieć wiele warstw, z których każda jest reprezentowana przez własną rejestrację w usłudze Azure AD. Na przykład aplikacja natywna, która wywołuje interfejs API sieci Web, lub aplikację sieci Web, która wywołuje interfejs API sieci Web. W obu przypadkach klient (aplikacja natywna lub aplikacja sieci Web) żąda uprawnień do wywoływania zasobu (internetowy interfejs API). Aby klient mógł zostać pomyślnie przesłany do dzierżawy klienta, wszystkie zasoby, do których żąda uprawnień, muszą już istnieć w dzierżawie klienta. Jeśli ten warunek nie zostanie spełniony, usługa Azure AD zwróci błąd, aby najpierw dodać zasób.

#### <a name="multiple-tiers-in-a-single-tenant"></a>Wiele warstw w jednej dzierżawie

Może to być problem, jeśli aplikacja logiczna składa się z co najmniej dwóch rejestracji aplikacji, na przykład oddzielnego klienta i zasobu. Jak należy najpierw pobrać zasób do dzierżawy klienta? Usługa Azure AD omawia ten przypadek, umożliwiając klientowi i zalogowanie się w jednym kroku. Użytkownik widzi łączną sumę uprawnień wymaganych przez klienta i zasób na stronie wyrażania zgody. Aby włączyć to zachowanie, Rejestracja aplikacji zasobu musi zawierać identyfikator aplikacji klienta jako element `knownClientApplications` w [manifeście aplikacji][AAD-App-Manifest]. Na przykład:

```aad-app-manifest
    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]
```

Jest to zademonstrowane w ramach wielowarstwowego, natywnego wywołania interfejsu API sieci Web w sekcji [powiązanej zawartości](#related-content) na końcu tego artykułu. Poniższy diagram zawiera omówienie wyrażania zgody dla aplikacji wielowarstwowej zarejestrowanej w ramach jednej dzierżawy.

![Przedstawia zgodę na znaną aplikację kliencką wielowarstwową][Consent-Multi-Tier-Known-Client]

#### <a name="multiple-tiers-in-multiple-tenants"></a>Wiele warstw w wielu dzierżawcach

Podobny przypadek ma miejsce, jeśli różne warstwy aplikacji są zarejestrowane w różnych dzierżawach. Rozważmy na przykład przypadek tworzenia natywnej aplikacji klienckiej, która wywołuje interfejs API usługi Exchange Online. Aby opracować aplikację natywną, a później w celu uruchomienia aplikacji natywnej w dzierżawie klienta musi być obecna nazwa główna usługi Exchange Online. W takim przypadku deweloper i klient muszą zakupić usługę Exchange Online w celu utworzenia jednostki usługi w swoich dzierżawach.

Jeśli jest to interfejs API zbudowany przez organizację inną niż Microsoft, deweloper interfejsu API musi zapewnić klientom zgodę na stosowanie aplikacji do dzierżawców klientów. Zalecany projekt jest przeznaczony dla deweloperów innych firm do kompilowania interfejsu API w taki sposób, że może on również działać jako klient sieci Web w celu zaimplementowania rejestracji. W tym celu:

1. Postępuj zgodnie z wcześniejszymi sekcjami, aby upewnić się, że interfejs API implementuje wymagania dotyczące rejestracji/kodu aplikacji wielodostępnej.
2. Oprócz udostępniania zakresów/ról interfejsu API upewnij się, że rejestracja obejmuje uprawnienie "Logowanie i odczyt profilu użytkownika" (domyślnie dostępne).
3. Zaimplementuj stronę logowania/rejestracji w kliencie sieci Web i postępuj zgodnie ze wskazówkami dotyczącymi [zgody administratora](#admin-consent) .
4. Gdy użytkownik wyraża zgodę na aplikację, linki jednostki usługi i delegowania zgody są tworzone w ich dzierżawie, a aplikacja natywna może uzyskać tokeny dla interfejsu API.

Poniższy diagram zawiera omówienie wyrażania zgody dla aplikacji wielowarstwowej zarejestrowanej w różnych dzierżawach.

![Przedstawia zgodę na wielowarstwową aplikację wieloplatformową][Consent-Multi-Tier-Multi-Party]

### <a name="revoking-consent"></a>Odwoływanie zgody

Użytkownicy i Administratorzy mogą odwołać zgodę na aplikację w dowolnym momencie:

* Użytkownicy mogą odwołać dostęp do poszczególnych aplikacji, usuwając je z listy [aplikacji panelu dostępu][AAD-Access-Panel] .
* Administratorzy mogą odwołać dostęp do aplikacji, usuwając je za pomocą sekcji [aplikacje dla przedsiębiorstw](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) w [Azure Portal][AZURE-portal].

Jeśli administrator wyraża zgodę na aplikację dla wszystkich użytkowników w dzierżawie, użytkownicy nie mogą odwołać dostępu pojedynczo. Tylko administrator może odwołać dostęp i tylko dla całej aplikacji.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Aplikacje z wieloma dzierżawcami i buforowanie tokeny dostępu

Aplikacje z wieloma dzierżawcami mogą również uzyskać tokeny dostępu do wywoływania interfejsów API chronionych przez usługę Azure AD. Typowym błędem podczas korzystania z Active Directory Authentication Library (ADAL) z aplikacją wielodostępną jest wstępne zażądanie tokenu dla użytkownika korzystającego z/typowe, odebranie odpowiedzi, a następnie zażądanie kolejnego tokenu dla tego samego użytkownika również przy użyciu/Common. Ponieważ odpowiedź z usługi Azure AD pochodzi z dzierżawy, a nie/typowe, biblioteka ADAL buforuje token jako pochodzący z dzierżawy. Kolejne wywołanie/typowe w celu uzyskania tokenu dostępu dla użytkownika powoduje odrzucenie wpisu pamięci podręcznej, a użytkownik jest monitowany o ponowne zalogowanie. Aby uniknąć braku pamięci podręcznej, upewnij się, że kolejne wywołania dla już zalogowanego użytkownika są nawiązywane w punkcie końcowym dzierżawy.

## <a name="related-content"></a>Zawartość pokrewna

* [Przykład aplikacji z wieloma dzierżawcami](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md)
* [Wytyczne dotyczące oznaczania aplikacji marką][AAD-App-Branding]
* [Obiekty aplikacji i obiekty główne usługi][AAD-App-SP-Objects]
* [Integrowanie aplikacji z usługą Azure Active Directory][AAD-Integrating-Apps]
* [Przegląd struktury zgody][AAD-Consent-Overview]
* [Zakresy uprawnień interfejsu API Microsoft Graph][MSFT-Graph-permission-scopes]

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób tworzenia aplikacji, która może zalogować użytkownika z dowolnej dzierżawy usługi Azure AD. Po włączeniu jednego Sign-On (SSO) między aplikacją i usługą Azure AD możesz także zaktualizować aplikację, aby uzyskać dostęp do interfejsów API udostępnianych przez zasoby firmy Microsoft, takich jak Microsoft 365. Dzięki temu możesz oferować spersonalizowany interfejs w aplikacji, taki jak wyświetlanie informacji kontekstowych dla użytkowników, takich jak ich zdjęcie profilu lub termin następnego kalendarza. Aby dowiedzieć się więcej na temat tworzenia wywołań interfejsu API w usłudze Azure AD i Microsoft 365 usług takich jak Exchange, SharePoint, OneDrive, OneNote i inne, odwiedź stronę [Microsoft Graph API][MSFT-Graph-overview].

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]:howto-add-branding-in-azure-ad-apps.md
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Consent-Overview]:consent-framework.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Samples-MT]: /samples/browse/?products=azure-active-directory
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: /graph/
[MSFT-Graph-permission-scopes]: /graph/permissions-reference

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-single-tier.svg
[Consent-Multi-Tier-Known-Client]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-known-clients.svg
[Consent-Multi-Tier-Multi-Party]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-multi-party.svg

<!--Reference style links -->
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AAD-V2-Dev-Guide]: v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: /graph/permissions-reference
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken