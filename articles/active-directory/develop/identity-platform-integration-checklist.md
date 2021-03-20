---
title: Najlepsze rozwiązania dotyczące platformy tożsamości firmy Microsoft | Azure
description: Zapoznaj się z najlepszymi rozwiązaniami, zaleceniami i typowymi informacjami dotyczącymi integracji z platformą tożsamości firmy Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: a6a7bf24571660d8e728c1acba29af2504539a18
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99219964"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Najlepsze rozwiązania i zalecenia dotyczące platformy tożsamości firmy Microsoft

W tym artykule przedstawiono najlepsze rozwiązania, zalecenia i szczegółowe informacje dotyczące integracji z platformą tożsamości firmy Microsoft.  Ta lista kontrolna przeprowadzi Cię do wysokiej jakości i bezpiecznej integracji. Przejrzyj tę listę w regularnych odstępach czasu, aby zapewnić zachowanie jakości i bezpieczeństwa integracji aplikacji z platformą tożsamości. Lista kontrolna nie ma na celu przejrzenia całej aplikacji. Zawartość listy kontrolnej może ulec zmianie w miarę wprowadzania ulepszeń na platformie.

Jeśli dopiero zaczynasz, zapoznaj się z [dokumentacją platformy tożsamości firmy Microsoft](index.yml) , aby poznać podstawowe informacje dotyczące uwierzytelniania, scenariusze aplikacji na platformie tożsamości firmy Microsoft i inne elementy.

Użyj poniższej listy kontrolnej, aby upewnić się, że aplikacja jest efektywnie zintegrowana z [platformą tożsamości firmy Microsoft](./index.yml).

> [!TIP]
> *Asystent integracji* w Azure Portal może pomóc w zastosowaniu wielu najlepszych rozwiązań i zaleceń. W Azure Portal wybierz dowolną [rejestrację aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) , a następnie wybierz element menu **Asystent Integration** , aby rozpocząć pracę z asystentem.

## <a name="basics"></a>Podstawy

![pole wyboru ](./media/active-directory-integration-checklist/checkbox-two.svg) Odczytaj i zapoznaj się z [zasadami platformy firmy Microsoft](/legal/microsoft-identity-platform/terms-of-use). Upewnij się, że aplikacja jest zgodna z warunkami, które zostały zaprojektowane w celu ochrony użytkowników i platformy.

## <a name="ownership"></a>Własność

![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) Upewnij się, że informacje skojarzone z kontem używanym do rejestrowania aplikacji i zarządzania nimi są aktualne.

## <a name="branding"></a>Znakowanie

![pole wyboru ](./media/active-directory-integration-checklist/checkbox-two.svg) jest zgodne ze [wskazówkami dotyczącymi oznakowania dla aplikacji](howto-add-branding-in-azure-ad-apps.md).

![pole wyboru ](./media/active-directory-integration-checklist/checkbox-two.svg) udostępnia opisową nazwę i logo dla aplikacji. Te informacje są wyświetlane w [monicie o zgodę aplikacji](application-consent-experience.md). Upewnij się, że Twoja nazwa i logo są reprezentatywne dla Twojej firmy/produktu, aby użytkownicy mogli podejmować świadome decyzje. Upewnij się, że nie są naruszane żadne znaki towarowe.

## <a name="privacy"></a>Prywatność

![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) Podaj linki do warunków użytkowania i zasad zachowania poufności informacji aplikacji.

## <a name="security"></a>Zabezpieczenia

![pole wyboru ](./media/active-directory-integration-checklist/checkbox-two.svg) Zarządzaj identyfikatorami URI przekierowania: <ul><li>Zachować własność wszystkich identyfikatorów URI przekierowania i zachować Aktualności rekordów DNS.</li><li>Nie używaj symboli wieloznacznych (*) w identyfikatorach URI.</li><li>W przypadku usługi Web Apps upewnij się, że wszystkie identyfikatory URI są bezpieczne i szyfrowane (na przykład przy użyciu schematów https).</li><li>W przypadku klientów publicznych Użyj identyfikatorów URI przekierowania specyficznych dla platformy, jeśli są odpowiednie (głównie dla systemów iOS i Android). W przeciwnym razie Użyj identyfikatorów URI przekierowania z dużą ilością losowości, aby zapobiec kolizjom podczas wywoływania z powrotem do aplikacji.</li><li>Jeśli aplikacja jest używana przez izolowanego agenta sieci Web, można użyć programu `https://login.microsoftonline.com/common/oauth2/nativeclient` .</li><li>Regularnie Przeglądaj i przycinaj wszystkie nieużywane lub niepotrzebne identyfikatory URI przekierowań.</li></ul>

![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) Jeśli Twoja aplikacja jest zarejestrowana w katalogu, Zminimalizuj i ręcznie Monitoruj listę właścicieli rejestracji aplikacji.

![pole wyboru ](./media/active-directory-integration-checklist/checkbox-two.svg) nie włącza obsługi [niejawnego przepływu OAuth2](v2-oauth2-implicit-grant-flow.md) , chyba że jest to wymagane jawnie. Zapoznaj się z prawidłowym [scenariuszem](v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant).

![pole wyboru jest ](./media/active-directory-integration-checklist/checkbox-two.svg) przenoszone poza nazwę użytkownika i hasło. Nie używaj [przepływu poświadczeń hasła właściciela zasobu (ROPC)](v2-oauth-ropc.md), który bezpośrednio obsługuje hasła użytkowników. Ten przepływ wymaga wysokiego stopnia zaufania i zagrożeń użytkownika i powinien być używany tylko wtedy, gdy inne, bezpieczniejsze, nie można używać przepływów. Ten przepływ jest nadal wymagany w niektórych scenariuszach (na przykład DevOps), ale uważaj, że jego użycie spowoduje nałożenie ograniczeń dotyczących aplikacji.  Aby uzyskać bardziej nowoczesne podejścia, Przeczytaj [przepływy uwierzytelniania i scenariusze aplikacji](authentication-flows-app-scenarios.md).

![pole wyboru ](./media/active-directory-integration-checklist/checkbox-two.svg) umożliwia ochronę poufnych poświadczeń aplikacji sieci Web, interfejsów API sieci Web i aplikacji demona oraz zarządzanie nimi. Użyj [poświadczeń certyfikatu](active-directory-certificate-credentials.md), a nie poświadczeń hasła (kluczy tajnych klienta). Jeśli musisz użyć poświadczenia hasła, nie ustawiaj go ręcznie. Nie należy przechowywać poświadczeń w kodzie ani w konfiguracji, a nigdy nie zezwalać na ich obsługę przez człowieka. Jeśli to możliwe, użyj [zarządzanych tożsamości dla zasobów platformy Azure](../managed-identities-azure-resources/overview.md) lub [Azure Key Vault](../../key-vault/general/basic-concepts.md) do przechowywania i regularnego rotacji Twoich poświadczeń.

![Upewnij ](./media/active-directory-integration-checklist/checkbox-two.svg) się, że aplikacja żąda najniższych uprawnień. Tylko Poproś o uprawnienia wymagane przez aplikację i tylko wtedy, gdy są one potrzebne. Zapoznaj się z różnymi [rodzajami uprawnień](v2-permissions-and-consent.md#permission-types). W razie potrzeby używaj uprawnień aplikacji; Użyj uprawnień delegowanych, jeśli jest to możliwe. Aby uzyskać pełną listę uprawnień Microsoft Graph, zobacz to [odwołanie do uprawnień](/graph/permissions-reference).

![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) W przypadku zabezpieczania interfejsu API przy użyciu platformy tożsamości firmy Microsoft należy uważnie zastanowić się nad uprawnieniami, które powinien ujawnić. Weź pod uwagę, jaki jest poziom szczegółowości rozwiązania i jakie uprawnienia wymagają zgody administratora. Przed podjęciem decyzji o autoryzacji Sprawdź, czy w tokenach przychodzących są oczekiwane uprawnienia.

## <a name="implementation"></a>Implementacja

![pole wyboru ](./media/active-directory-integration-checklist/checkbox-two.svg) Użyj nowoczesnych rozwiązań uwierzytelniania (OAuth 2,0, [OpenID Connect Connect](v2-protocols-oidc.md)) do bezpiecznego logowania użytkowników.

![pole wyboru ](./media/active-directory-integration-checklist/checkbox-two.svg) nie program bezpośrednio w przypadku protokołów takich jak OAuth 2,0 i Open ID. Zamiast tego Skorzystaj z [biblioteki Microsoft Authentication Library (MSAL)](msal-overview.md). Biblioteki MSAL bezpiecznie zawijają protokoły zabezpieczeń w łatwej w użyciu bibliotece i udostępniamy wbudowaną obsługę scenariuszy [dostępu warunkowego](../conditional-access/overview.md) , [logowania](../manage-apps/what-is-single-sign-on.md)jednokrotnego dla całego urządzenia oraz wbudowanej obsługi buforowania tokenów. Aby uzyskać więcej informacji, zobacz listę [bibliotek klienckich](reference-v2-libraries.md)obsługiwanych przez firmę Microsoft. Jeśli konieczne jest ręczne przekazanie protokołów uwierzytelniania, należy postępować [zgodnie z tą](https://www.microsoft.com/sdl/default.aspx) metodą. Zwróć szczególną uwagę na kwestie dotyczące zabezpieczeń w specyfikacjach standardów dla każdego protokołu.

![pole wyboru ](./media/active-directory-integration-checklist/checkbox-two.svg) Migruj istniejące aplikacje z [biblioteki uwierzytelniania Azure Active Directory (ADAL)](../azuread-dev/active-directory-authentication-libraries.md) do [biblioteki uwierzytelniania firmy Microsoft](msal-overview.md). MSAL to najnowsze rozwiązanie platformy tożsamości firmy Microsoft i jest preferowane dla biblioteki ADAL. Jest ona dostępna w systemach .NET, JavaScript, Android, iOS i macOS, a także w publicznej wersji zapoznawczej dla języków Python i Java. Dowiedz się więcej na temat migrowania aplikacji [ADAL.NET](msal-net-migration.md), [ADAL.js](msal-compare-msal-js-and-adal-js.md)i [ADAL.NET i dla brokera systemu iOS](msal-net-migration-ios-broker.md) .

![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) W przypadku aplikacji mobilnych skonfiguruj każdą platformę przy użyciu środowiska rejestracji aplikacji. Aby aplikacja mogła korzystać z Microsoft Authenticator lub Microsoft Portal firmy do logowania jednokrotnego, aplikacja wymaga skonfigurowanego "identyfikatora URI przekierowania brokera". Dzięki temu firma Microsoft może zwrócić kontrolę do aplikacji po uwierzytelnieniu. Podczas konfigurowania każdej platformy środowisko rejestracji aplikacji przeprowadzi Cię przez proces. Aby pobrać przykład pracy, Skorzystaj z przewodnika Szybki Start. W systemie iOS należy używać brokerów i systemu WebView, gdy jest to możliwe.

![Zaznacz pole wyboru ](./media/active-directory-integration-checklist/checkbox-two.svg) w obszarze aplikacje sieci Web lub interfejsy API sieci Web, Zachowaj jedną pamięć podręczną tokenów na konto.  W przypadku usługi Web Apps bufor tokenów powinien być poprzedzony IDENTYFIKATORem konta.  W przypadku interfejsów API sieci Web konto powinno być poprzedzone przez skrót tokenu używanego do wywoływania interfejsu API. MSAL.NET udostępnia niestandardową serializację pamięci podręcznej tokeny w podplatformach .NET Framework i .NET Core. Ze względu na bezpieczeństwo i wydajność nasze zalecenia polegają na serializacji jednej pamięci podręcznej na użytkownika. Aby uzyskać więcej informacji, Przeczytaj o [serializacji pamięci podręcznej tokenu](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application).

![pole wyboru ](./media/active-directory-integration-checklist/checkbox-two.svg) , jeśli dane wymagane przez aplikację są dostępne za pośrednictwem [Microsoft Graph](https://developer.microsoft.com/graph), zażądaj uprawnień dla tych danych przy użyciu punktu końcowego Microsoft Graph, a nie do poszczególnych interfejsów API.

![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) Nie sprawdzaj wartości tokenu dostępu lub spróbuj przeanalizować ją jako klienta.  Mogą zmieniać wartości, formaty lub nawet być szyfrowane bez ostrzeżenia — zawsze używaj id_token, jeśli klient musi poznać coś dotyczące użytkownika lub wywołać Microsoft Graph.  Tylko interfejsy API sieci Web powinny analizować tokeny dostępu (ponieważ są one definiowane przy użyciu formatowania i ustawiania kluczy szyfrowania).

## <a name="end-user-experience"></a>Środowisko użytkownika końcowego

![pole wyboru ](./media/active-directory-integration-checklist/checkbox-two.svg) [zawiera informacje o doświadczeniu w zakresie zgody](application-consent-experience.md) i konfiguracji fragmentów monitu o zgodę aplikacji, dzięki czemu użytkownicy końcowi i Administratorzy mają wystarczającą ilość informacji, aby określić, czy ufają aplikacji.

![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) Zminimalizuj, ile razy użytkownik musi wprowadzić poświadczenia logowania podczas korzystania z aplikacji, próbując przeprowadzić uwierzytelnianie dyskretne (dyskretny token) przed interaktywnymi przepływami.

![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) Nie używaj "Prompt = zgody" przy każdym logowaniu. Należy używać monitu = wyrażanie zgody, jeśli określono, że należy zażądać zgody na dodatkowe uprawnienia (na przykład jeśli zmieniono wymagane uprawnienia aplikacji).

![pole wyboru ](./media/active-directory-integration-checklist/checkbox-two.svg) , gdzie to możliwe, wzbogacanie aplikacji o dane użytkownika. Korzystanie z [interfejsu API Microsoft Graph](https://developer.microsoft.com/graph) to prosty sposób. Narzędzie [Eksplorator grafów](https://developer.microsoft.com/graph/graph-explorer) ułatwiające rozpoczęcie pracy.

![pole wyboru ](./media/active-directory-integration-checklist/checkbox-two.svg) rejestruje pełen zestaw uprawnień wymaganych przez aplikację, aby administratorzy mogli łatwo udzielić zgody na swoją dzierżawę. Używaj [przyrostowej zgody](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) w czasie wykonywania, aby ułatwić użytkownikom zrozumienie, Dlaczego aplikacja żąda uprawnień, które mogą mieć problemy lub mylić użytkowników, gdy są żądane podczas pierwszego uruchomienia.

![pole wyboru ](./media/active-directory-integration-checklist/checkbox-two.svg) implementuje [czyste środowisko logowania](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut)jednokrotnego. Jest to ochrona prywatności i wymagania dotyczące zabezpieczeń oraz zapewnia dobre środowisko użytkownika.

## <a name="testing"></a>Testowanie

![CheckBox ](./media/active-directory-integration-checklist/checkbox-two.svg) test dla [zasad dostępu warunkowego](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) , które mogą mieć wpływ na możliwość korzystania z aplikacji przez użytkowników.

![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) Przetestuj swoją aplikację ze wszystkimi możliwymi kontami, które planujesz obsługiwać (na przykład konta służbowe, osobiste konta Microsoft, konta podrzędne i suwerenne konta).

## <a name="additional-resources"></a>Dodatkowe zasoby

Uzyskaj szczegółowe informacje na temat wersji 2.0:

* [Platforma tożsamości firmy Microsoft (omówienie)](v2-overview.md)
* [Informacje o protokołach Microsoft Identity platform](active-directory-v2-protocols.md)
* [Dokumentacja tokenów dostępu](access-tokens.md)
* [Dokumentacja tokenów identyfikatorów](id-tokens.md)
* [Dokumentacja bibliotek uwierzytelniania](reference-v2-libraries.md)
* [Uprawnienia i zgoda na platformie tożsamości firmy Microsoft](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)
