---
title: Opcje logowania pojedynczego w usłudze Azure AD
description: Dowiedz się więcej o opcjach dostępnych dla logowania jednokrotnego (SSO) w Azure Active Directory.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: iangithinji
ms.reviewer: arvindh, japere
ms.openlocfilehash: 202ccf6f540ec78c2bb30e0f0a0173609bba578c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751458"
---
# <a name="single-sign-on-options-in-azure-ad"></a>Opcje logowania pojedynczego w usłudze Azure AD

Logowanie pojedyncze zapewnia wiele korzyści w przeciwieństwie do tradycyjnych metod logowania.

- **W przypadku** logowania pojedynczego użytkownicy logują się raz przy użyciu jednego konta, aby uzyskać dostęp do urządzeń przyłączonych do domeny, zasobów firmy, aplikacji typu oprogramowanie jako usługa (SaaS) i aplikacji internetowych. Po zalogowaniu użytkownik może uruchamiać aplikacje z portalu usługi Office 365 lub witryny Moje aplikacje. Administratorzy mogą scentralizować zarządzanie kontami użytkowników oraz automatycznie dodawać lub usuwać dostęp użytkowników do aplikacji na podstawie członkostwa w grupie.

- **Bez logowania pojedynczego użytkownicy** muszą zapamiętać hasła specyficzne dla aplikacji i zalogować się do każdej aplikacji. Pracownicy działu IT muszą tworzyć i aktualizować konta użytkowników dla każdej aplikacji, takiej jak Microsoft 365, Box i Salesforce. Użytkownicy muszą zapamiętać swoje hasła, a także poświęcić czas na logowanie się do każdej aplikacji.

Aby dowiedzieć się więcej na temat logowania pojedynczego, zobacz [Co to jest logowanie pojedyncze?](what-is-single-sign-on.md).

## <a name="choosing-a-single-sign-on-method"></a>Wybieranie metody logowania pojedynczego

Istnieje kilka sposobów konfigurowania aplikacji do logowania pojedynczego. Wybór metody logowania pojedynczego zależy od tego, jak aplikacja jest skonfigurowana do uwierzytelniania.

- Aplikacje w chmurze mogą używać OpenID Connect, OAuth, SAML, opartych na hasłach, połączonych lub wyłączonych metod logowania pojedynczego. 
- Aplikacje lokalne mogą używać opartych na hasłach, Integrated Windows Authentication, opartych na nagłówkach, połączonych lub wyłączonych metod logowania się. Opcje lokalne działają, gdy aplikacje są skonfigurowane pod serwer proxy aplikacji.

Ten schemat blokowy pomaga zdecydować, która metoda logowania pojedynczego jest najlepsza w Twojej sytuacji.

![Schemat blokowy decyzji dla metody logowania pojedynczego](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

W poniższej tabeli przedstawiono podsumowanie metod logowania pojedynczego i linki do większej liczby szczegółów.

| Metoda logowania pojedynczego | Typy aplikacji | Kiedy stosować |
| :------ | :------- | :----- |
| [OpenID Connect i OAuth](#openid-connect-and-oauth) | chmura i lokalne | Użyj OpenID Connect i OAuth podczas tworzenia nowej aplikacji. Ten protokół upraszcza konfigurację aplikacji, ma łatwe w użyciu zestawy SDK i umożliwia aplikacji korzystanie z programu MS Graph.
| [SAML](#saml-sso) | chmura i lokalne | Jeśli to możliwe, wybierz saml dla istniejących aplikacji, które nie korzystają OpenID Connect ani protokołu OAuth. Protokół SAML działa w przypadku aplikacji, które uwierzytelniają się przy użyciu jednego z protokołów SAML.|
| [Oparte na hasłach](#password-based-sso) | chmura i lokalne | Wybierz opcję opartą na hasłach podczas uwierzytelniania aplikacji przy użyciu nazwy użytkownika i hasła. Logowanie pojedyncze oparte na hasłach umożliwia bezpieczne przechowywanie i powtarzanie haseł aplikacji przy użyciu rozszerzenia przeglądarki internetowej lub aplikacji mobilnej. Ta metoda używa istniejącego procesu logowania dostarczonego przez aplikację, ale umożliwia administratorowi zarządzanie hasłami. |
| [Połączone](#linked-sign-on) | chmura i lokalne | Wybierz logowanie połączone, gdy aplikacja jest skonfigurowana do logowania pojedynczego w innej usłudze dostawcy tożsamości. Ta opcja nie powoduje dodania logowania pojedynczego do aplikacji. Jednak aplikacja może już mieć zaimplementowane logowanie pojedyncze przy użyciu innej usługi, takiej jak Active Directory Federation Services.|
| [Disabled](#disabled-sso) | chmura i lokalne | Wybierz opcję wyłączonego logowania pojedynczego, gdy aplikacja nie jest gotowa do skonfigurowania na logowanie. Ten tryb jest domyślny podczas tworzenia aplikacji.|
| [Integrated Windows Authentication (IWA)](#integrated-windows-authentication-iwa-sso) | Tylko lokalnie | W przypadku aplikacji, które korzystają z aplikacji z Integrated Windows Authentication [(IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)lub aplikacji z oświadczeniami, wybierz opcję Logowanie pojedynczej aplikacji IWA. W przypadku funkcji IWA łączniki serwer proxy aplikacji używają ograniczonego delegowania protokołu Kerberos (KCD) do uwierzytelniania użytkowników w aplikacji. |
| [Oparte na nagłówku](#header-based-sso) | Tylko lokalnie | Użyj logowania pojedynczego opartego na nagłówku, gdy aplikacja używa nagłówków do uwierzytelniania. serwer proxy aplikacji używa usługi Azure AD do uwierzytelniania użytkownika, a następnie przekazuje ruch przez usługę łącznika.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect i OAuth

Podczas tworzenia nowych aplikacji użyj nowoczesnych protokołów, takich jak OpenID Connect i OAuth, aby uzyskać najlepsze środowisko logowania pojedynczego dla aplikacji na wielu platformach urządzeń. Uwierzytelnianie OAuth umożliwia użytkownikom lub administratorom [wyrażanie zgody](configure-user-consent.md) dla chronionych zasobów, takich [Microsoft Graph](/graph/overview). Zapewniamy łatwe do zastosowania [zestawy SDK](../develop/reference-v2-libraries.md) dla aplikacji, a ponadto aplikacja będzie gotowa do użycia w [Microsoft Graph](/graph/overview).

Aby uzyskać więcej informacji, zobacz:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Przewodnik dewelopera platformy tożsamości firmy Microsoft.](../develop/index.yml)

## <a name="saml-sso"></a>Logowanie jednokrotne SAML

W **przypadku logowania pojedynczego SAML** usługa Azure AD uwierzytelnia się w aplikacji przy użyciu konta użytkownika usługi Azure AD. Usługa Azure AD komunikuje informacje dotyczące logowania do aplikacji za pośrednictwem protokołu połączenia. Za pomocą logowania pojedynczego opartego na saml można mapować użytkowników na określone role aplikacji na podstawie reguł określonych w oświadczeniach SAML.

Jeśli aplikacja je obsługuje, wybierz pozycję Logowanie pojedyncze oparte na saml.

Logowanie pojedynczej oparte na saml jest obsługiwane w przypadku aplikacji, które korzystają z dowolnego z tych protokołów:

- SAML 2.0
- WS-Federation

Aby skonfigurować aplikację SaaS dla logowania pojedynczego opartego na saml, zobacz Konfigurowanie logowania pojedynczego opartego [na saml.](configure-saml-single-sign-on.md) Ponadto wiele aplikacji typu oprogramowanie jako usługa [](../saas-apps/tutorial-list.md) (SaaS) zawiera samouczek specyficzny dla aplikacji, który zawiera szczegółowe instrukcje dotyczące konfiguracji logowania pojedynczego opartego na saml.

Aby skonfigurować aplikację dla usługi WS-Federation, postępuj zgodnie z tym samymi wskazówkami, aby skonfigurować aplikację dla logowania pojedynczego opartego na saml. W kroku konfigurowania aplikacji do korzystania z usługi Azure AD należy zastąpić adres URL logowania usługi Azure AD dla WS-Federation końcowego `https://login.microsoftonline.com/<tenant-ID>/wsfed` .

Aby skonfigurować aplikację lokalną dla logowania pojedynczego opartego na saml, zobacz [SAML single-sign-on for on-premises applications with serwer proxy aplikacji](application-proxy-configure-single-sign-on-on-premises-apps.md)(Logowanie pojedyncze SAML dla aplikacji lokalnych przy użyciu serwer proxy aplikacji .

Aby uzyskać więcej informacji na temat protokołu SAML, zobacz [Single sign-on SAML protocol](../develop/single-sign-on-saml-protocol.md)(Protokół SAML logowania pojedynczego).

## <a name="password-based-sso"></a>Logowanie jednokrotne oparte na haśle

W przypadku logowania opartego na hasłach użytkownicy logują się do aplikacji przy użyciu nazwy użytkownika i hasła przy pierwszym uzyskiwaniu do nich dostępu. Po pierwszym zalogowaniu usługa Azure AD dostarcza nazwę użytkownika i hasło do aplikacji.

Logowanie pojedyncze oparte na hasłach korzysta z istniejącego procesu uwierzytelniania udostępnianego przez aplikację. Po włączeniu logowania pojedynczego hasła dla aplikacji usługa Azure AD zbiera i bezpiecznie przechowuje nazwy użytkowników i hasła dla aplikacji. Poświadczenia użytkownika są przechowywane w stanie zaszyfrowanym w katalogu.

Ponadto administrator może włączyć zasady dostępu warunkowego usługi Azure AD lub uwierzytelnianie wieloskładnikowe dla logowania jednokrotnego opartego na hasłach.

Wybierz logowanie jednorazowe oparte na hasłach, gdy:

- Aplikacja nie obsługuje protokołu logowania pojedynczego SAML.
- Aplikacja uwierzytelnia się przy użyciu nazwy użytkownika i hasła zamiast tokenów dostępu i nagłówków.

Logowanie pojedyncze oparte na hasłach jest obsługiwane w przypadku każdej aplikacji opartej na chmurze, która ma stronę logowania opartą na języku HTML. Użytkownik może używać dowolnej z następujących przeglądarek:

- Internet Explorer 11 w systemie Windows 7 lub nowszym
   > [!NOTE]
   > Internet Explorer jest w ograniczonej pomocy technicznej i nie otrzymuje już nowych aktualizacji oprogramowania. Microsoft Edge jest zalecaną przeglądarką.

- Microsoft Edge w Windows 10 Anniversary Edition lub nowszej
- Microsoft Edge dla systemów iOS i Android
- Intune Managed Browser
- Chrome w systemie Windows 7 lub nowszym oraz w systemie macOS X lub nowszym
- Firefox 26.0 lub nowszy w systemie Windows XP z dodatkiem SP2 lub nowszym oraz w systemie macOS X 10.6 lub nowszym

Aby skonfigurować aplikację w chmurze dla logowania pojedynczego opartego na hasłach, zobacz [Configure password single sign-on](configure-password-single-sign-on-non-gallery-applications.md)(Konfigurowanie logowania pojedynczego przy użyciu hasła).

Aby skonfigurować aplikację lokalną do logowania się za pośrednictwem usługi serwer proxy aplikacji, zobacz [Magazyn](application-proxy-configure-single-sign-on-password-vaulting.md) haseł na przykład do logowania serwer proxy aplikacji

### <a name="how-authentication-works-for-password-based-sso"></a>Jak działa uwierzytelnianie dla logowania jednokrotnego opartego na hasłach

Aby uwierzytelnić użytkownika w aplikacji, usługa Azure AD pobiera poświadczenia użytkownika z katalogu i wprowadza je na stronie logowania aplikacji.  Usługa Azure AD bezpiecznie przekazuje poświadczenia użytkownika za pośrednictwem rozszerzenia przeglądarki internetowej lub aplikacji mobilnej. Ten proces umożliwia administratorowi zarządzanie poświadczeniami użytkownika i nie wymaga od użytkowników pamiętania hasła.

> [!IMPORTANT]
> Poświadczenia są zaciemniane przez użytkownika podczas procesu automatycznego logowania. Jednak poświadczenia można odnaleźć za pomocą narzędzi do debugowania w Internecie. Użytkownicy i administratorzy muszą przestrzegać tych samych zasad zabezpieczeń, tak jakby poświadczenia zostały wprowadzone bezpośrednio przez użytkownika.

### <a name="managing-credentials-for-password-based-sso"></a>Zarządzanie poświadczeniami logowania jednokrotnego opartego na hasłach

Hasłami dla każdej aplikacji może zarządzać administrator usługi Azure AD lub użytkownicy.

Gdy administrator usługi Azure AD zarządza poświadczeniami:  

- Użytkownik nie musi resetować ani zapamiętuje nazwy użytkownika i hasła. Użytkownik może uzyskać dostęp do aplikacji, klikając ją w swoim Moje aplikacje lub za pośrednictwem podanego linku.
- Administrator może wykonywać zadania zarządzania na poświadczeniach. Na przykład administrator może zaktualizować dostęp do aplikacji zgodnie z członkostwem w grupach użytkowników i stanem pracownika.
- Administrator może użyć poświadczeń administracyjnych, aby zapewnić dostęp do aplikacji udostępnianych wielu użytkownikom. Administrator może na przykład zezwolić wszystkim użytkownikom, którzy mogą uzyskać dostęp do aplikacji, na dostęp do mediów społecznościowych lub aplikacji do udostępniania dokumentów.

Gdy użytkownik końcowy zarządza poświadczeniami:

- Użytkownicy mogą zarządzać hasłami, aktualizując je lub usuwając zgodnie z potrzebami.
- Administratorzy nadal mogą ustawiać nowe poświadczenia dla aplikacji.

## <a name="linked-sign-on"></a>Połączone logowanie
Logowanie połączone umożliwia usłudze Azure AD zapewnianie logowania pojedynczego do aplikacji, która jest już skonfigurowana do logowania pojedynczego w innej usłudze. Połączona aplikacja może być wyświetlana użytkownikom końcowym w portalu usługi Office 365 lub portalu MyApps usługi Azure AD. Na przykład użytkownik może uruchomić aplikację skonfigurowaną do logowania pojedynczego w usłudze Active Directory Federation Services 2.0 (AD FS) z portalu usługi Office 365. Dodatkowe raportowanie jest również dostępne dla połączonych aplikacji, które są uruchomione z portalu usługi Office 365 lub portalu MyApps usługi Azure AD. Aby skonfigurować aplikację do logowania połączonego, zobacz [Konfigurowanie połączonego logowania.](configure-linked-sign-on.md)

### <a name="linked-sign-on-for-application-migration"></a>Połączone logowanie do migracji aplikacji

Połączone logowanie może zapewnić spójne środowisko użytkownika podczas migrowania aplikacji w czasie. W przypadku migrowania aplikacji do usługi Azure Active Directory można użyć logowania połączonego, aby szybko opublikować linki do wszystkich aplikacji, które mają zostać zmigrowane.  Użytkownicy mogą znaleźć wszystkie linki w portalu [MyApps](../user-help/my-apps-portal-end-user-access.md) lub [w Microsoft 365 uruchamiania aplikacji.](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) Użytkownicy nie będą wiedzieć, że mają dostęp do połączonej aplikacji lub zmigrowanych aplikacji.  

Po uwierzytelnieniu użytkownika za pomocą połączonej aplikacji należy utworzyć rekord konta, zanim użytkownik końcowy będzie mieć dostęp do logowania pojedynczego. Aprowizowanie tego rekordu konta może nastąpić automatycznie lub może nastąpić ręcznie przez administratora.

>[!NOTE]
>Do połączonej aplikacji nie można stosować zasad dostępu warunkowego ani uwierzytelniania wieloskładnikowego. Wynika to z tego, że połączona aplikacja nie zapewnia możliwości logowania pojedynczego za pośrednictwem usługi Azure AD. Podczas konfigurowania połączonej aplikacji wystarczy dodać link, który będzie wyświetlany w aplikacji uruchamiania lub portalu MyApps. 

## <a name="disabled-sso"></a>Wyłączone logowanie jednokrotne

Tryb wyłączony oznacza, że logowanie pojedyncze nie jest używane w aplikacji. Jeśli logowanie pojedyncze jest wyłączone, użytkownicy mogą wymagać dwukrotnego uwierzytelnienia. Najpierw użytkownicy uwierzytelniają się w usłudze Azure AD, a następnie logują się do aplikacji.

Użyj wyłączonego trybu logowania pojedynczego:

- Jeśli nie chcesz jeszcze integrować tej aplikacji z logowaniem pojedynczym usługi Azure AD lub
- W przypadku testowania innych aspektów aplikacji lub
- Warstwa zabezpieczeń aplikacji lokalnej, która nie wymaga od użytkowników uwierzytelniania. Po wyłączeniu użytkownik musi się uwierzytelnić.

Należy pamiętać, że jeśli aplikacja została skonfigurowana na podstawie logowania jednokrotnego opartego na programie SPL i zmienisz tryb logowania jednokrotnego w celu wyłączenia tej funkcji, nie uniemożliwi to użytkownikom logowania się do aplikacji poza portalem MyApps. Aby to osiągnąć, musisz wyłączyć [możliwość logowania użytkowników](disable-user-sign-in-portal.md)

## <a name="integrated-windows-authentication-iwa-sso"></a>Integrated Windows Authentication logowania jednokrotnego (IWA)

[serwer proxy aplikacji](application-proxy.md) umożliwia logowanie jednokrotne (SSO) do aplikacji, które [korzystają Integrated Windows Authentication (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)lub aplikacji z oświadczeniami. Jeśli aplikacja używa funkcji IWA, serwer proxy aplikacji uwierzytelnia się w aplikacji przy użyciu ograniczonego delegowania protokołu Kerberos (KCD). W przypadku aplikacji z oświadczeniami, która ufa Azure Active Directory, logowanie pojedyncze działa, ponieważ użytkownik został już uwierzytelniony przy użyciu usługi Azure AD.

Wybierz Integrated Windows Authentication w trybie logowania pojedynczego, aby zapewnić logowanie pojedyncze do aplikacji lokalnej, która uwierzytelnia się w aplikacji IWA.

Aby skonfigurować aplikację lokalną dla IWA, zobacz Ograniczone delegowanie [protokołu Kerberos](application-proxy-configure-single-sign-on-with-kcd.md)na temat logowania się do aplikacji za pomocą serwer proxy aplikacji .

### <a name="how-single-sign-on-with-kcd-works"></a>Jak działa logowanie pojedyncze z KCD
Na tym diagramie wyjaśniono przepływ, gdy użytkownik uzyskuje dostęp do aplikacji lokalnej korzystającej z IWA.

![Microsoft Azure AD przepływu uwierzytelniania](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Użytkownik wprowadza adres URL w celu uzyskania dostępu do aplikacji lokalnej za pośrednictwem serwer proxy aplikacji.
1. serwer proxy aplikacji przekierowuje żądanie do usług uwierzytelniania usługi Azure AD w celu wstępnego uwierzytelnienia. W tym momencie usługa Azure AD stosuje wszelkie odpowiednie zasady uwierzytelniania i autoryzacji, takie jak uwierzytelnianie wieloskładnikowe. Jeśli użytkownik zostanie zweryfikowany, usługa Azure AD utworzy token i wyśle go do użytkownika.
1. Użytkownik przekazuje token do usługi serwer proxy aplikacji.
1. serwer proxy aplikacji zweryfikuje token i pobierze z tokenu główną nazwę użytkownika (UPN). Następnie wysyła żądanie, nazwę UPN i główną nazwę usługi (SPN) do łącznika za pośrednictwem podwójnie uwierzytelnionego bezpiecznego kanału.
1. Łącznik używa negocjacji ograniczonego delegowania protokołu Kerberos (KCD) z lokalną usługą AD, personifikując użytkownika w celu uzyskania tokenu protokołu Kerberos do aplikacji.
1. Usługa Active Directory wysyła token protokołu Kerberos dla aplikacji do łącznika.
1. Łącznik wysyła oryginalne żądanie do serwera aplikacji przy użyciu tokenu protokołu Kerberos otrzymanego z usługi AD.
1. Aplikacja wysyła odpowiedź do łącznika, który jest następnie zwracany do usługi serwer proxy aplikacji i na końcu do użytkownika.

## <a name="header-based-sso"></a>Logowanie jednokrotne oparte na nagłówku

Logowanie pojedyncze oparte na nagłówku działa w przypadku aplikacji, które używają nagłówków HTTP do uwierzytelniania.

Wybierz logowanie pojedyncze oparte na nagłówku, serwer proxy aplikacji skonfigurowane dla aplikacji lokalnej.

Aby dowiedzieć się więcej na temat uwierzytelniania opartego na nagłówku, zobacz [Logowanie jednokrotne oparte na nagłówku.](application-proxy-configure-single-sign-on-with-headers.md)


## <a name="next-steps"></a>Następne kroki
* [Seria przewodników Szybki start na temat zarządzania aplikacją](view-applications-portal.md)
* [Planowanie wdrożenia logowania jednokrotnego](plan-sso-deployment.md)
* [Logowanie pojedyncze z aplikacjami lokalnymi](application-proxy-config-sso-how-to.md)
