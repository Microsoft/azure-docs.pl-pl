---
title: Często zadawane pytania dotyczące Azure Active Directory B2C
description: Odpowiedzi na często zadawane pytania dotyczące Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 384f178481f7a6ff202d5363ba09c242b1fc366d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951795"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: często zadawane pytania

Ta strona zawiera odpowiedzi na często zadawane pytania dotyczące Azure Active Directory B2C (Azure AD B2C). Kontynuuj sprawdzanie aktualizacji.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Dlaczego nie mogę uzyskać dostępu do rozszerzenia Azure AD B2C w Azure Portal?

Istnieją dwie typowe przyczyny, dla których rozszerzenie usługi Azure AD nie działa. Azure AD B2C wymaga, aby rola użytkownika w katalogu była administratorem globalnym. Skontaktuj się z administratorem, jeśli uważasz, że masz dostęp. Jeśli masz uprawnienia administratora globalnego, upewnij się, że jesteś w katalogu Azure AD B2C, a nie w katalogu Azure Active Directory. Możesz zobaczyć instrukcje dotyczące [tworzenia dzierżawy Azure AD B2C](tutorial-create-tenant.md).

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Czy mogę używać funkcji Azure AD B2C w mojej istniejącej dzierżawie usługi Azure AD opartej na pracowniku?

Usługa Azure AD i Azure AD B2C są oddzielnymi ofertami produktu i nie mogą współistnieć w tej samej dzierżawie. Dzierżawa usługi Azure AD reprezentuje organizację. Dzierżawca Azure AD B2C reprezentuje kolekcję tożsamości, które mają być używane z aplikacjami jednostek uzależnionych. Dodając **nowego dostawcę połączenia usługi OpenID Connect** w obszarze **Azure AD B2C > dostawców tożsamości** lub zasad niestandardowych, Azure AD B2C można SFEDEROWAĆ z usługą Azure AD, umożliwiając uwierzytelnianie pracowników w organizacji.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-microsoft-365"></a>Czy mogę użyć usługi Azure AD B2C, aby zapewnić logowanie społeczne (Facebook i Google +) do Microsoft 365?

Nie można użyć Azure AD B2C do uwierzytelniania użytkowników dla Microsoft 365. Usługa Azure AD to rozwiązanie firmy Microsoft do zarządzania dostępem pracowników do aplikacji SaaS i zawiera funkcje przeznaczone do tego celu, takie jak Licencjonowanie i dostęp warunkowy. Azure AD B2C zapewnia platformę zarządzania tożsamościami i dostępem do tworzenia aplikacji internetowych i mobilnych. Gdy Azure AD B2C jest skonfigurowany do sfederować dzierżawy usługi Azure AD, dzierżawa usługi Azure AD zarządza dostępem pracowników do aplikacji, które są zależne od Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Co to są konta lokalne w Azure AD B2C? Czym różnią się one od kont służbowych w usłudze Azure AD?

W dzierżawie usługi Azure AD użytkownicy, którzy należą do logowania do dzierżawy przy użyciu adresu e-mail formularza `<xyz>@<tenant domain>` . `<tenant domain>`Jest to jedna z zweryfikowanych domen w dzierżawie lub w `<...>.onmicrosoft.com` domenie początkowej. Ten typ konta jest kontem służbowym.

W dzierżawie Azure AD B2C większość aplikacji chce, aby użytkownik mógł się zalogować przy użyciu dowolnego adresu e-mail (na przykład, joe@comcast.net , bob@gmail.com sarah@contoso.com lub jim@live.com ). Ten typ konta jest kontem lokalnym. Obsługiwane są również dowolne nazwy użytkowników jako konta lokalne (na przykład Jan, Robert, Sarah lub Jim). Podczas konfigurowania dostawców tożsamości dla Azure AD B2C w Azure Portal można wybrać jeden z tych dwóch typów kont lokalnych. W dzierżawie Azure AD B2C wybierz pozycję **dostawcy tożsamości**, wybierz pozycję **konto lokalne**, a następnie wybierz pozycję **Nazwa użytkownika**.

Konta użytkowników dla aplikacji można tworzyć za pomocą przepływu użytkownika tworzenia konta, usługi rejestrowania lub logowania, interfejsu API Microsoft Graph lub Azure Portal.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Którzy dostawcy tożsamości społecznościowej są obecnie obsługiwani? Które z nich planujesz obsługiwać w przyszłości?

Obecnie obsługujemy kilka dostawców tożsamości społecznościowych, takich jak Amazon, Facebook, GitHub (wersja zapoznawcza), Google, LinkedIn, konto Microsoft (MSA), QQ (wersja zapoznawcza), Twitter, WeChat (wersja zapoznawcza) i Weibo (w wersji zapoznawczej). Ocenimy Dodawanie obsługi dla innych popularnych dostawców tożsamości społecznościowych na podstawie popytu klientów.

Azure AD B2C obsługuje również [zasady niestandardowe](custom-policy-overview.md). Zasady niestandardowe umożliwiają tworzenie własnych zasad dla dowolnego dostawcy tożsamości, który obsługuje [OpenID Connect Connect](https://openid.net/specs/openid-connect-core-1_0.html) lub SAML. Rozpocznij pracę z zasadami niestandardowymi, sprawdzając nasz [pakiet dla zasad niestandardowych](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack).

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Czy mogę skonfigurować zakresy, aby zebrać więcej informacji o konsumentach od różnych dostawców tożsamości społecznościowych?

Nie. Domyślne zakresy używane dla naszego obsługiwanego zestawu dostawców tożsamości społecznościowych to:

* Facebook: poczta e-mail
* Google +: adres e-mail
* Konto Microsoft: profil poczty e-mail OpenID Connect
* Amazon: profil
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Czy moja aplikacja musi być uruchamiana na platformie Azure dla pracy z Azure AD B2C?

Nie, możesz hostować aplikację w dowolnym miejscu (w chmurze lub lokalnie). Wszystkie potrzebne do współdziałania z Azure AD B2C to możliwość wysyłania i otrzymywania żądań HTTP w publicznie dostępnych punktach końcowych.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Mam wiele dzierżawców Azure AD B2C. Jak mogę zarządzać nimi na Azure Portal?

Przed otwarciem elementu "Azure AD B2C" w menu po lewej stronie Azure Portal należy przełączyć się do katalogu, który ma być zarządzany. Przełącz katalogi, klikając swoją tożsamość w prawym górnym rogu Azure Portal, a następnie wybierz katalog znajdujący się na liście rozwijanej.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Jak mogę dostosować wiadomości e-mail weryfikacyjnych (zawartość i pole "z:") wysyłane przez Azure AD B2C?

Za pomocą [funkcji znakowania firmowego](../active-directory/fundamentals/customize-branding.md) można dostosować zawartość wiadomości e-mail weryfikacyjnych. W tym celu można dostosować te dwa elementy wiadomości e-mail:

* **Logo transparentu**: widoczne w prawym dolnym rogu.
* **Kolor tła**: wyświetlany w górnej części.

    ![Zrzut ekranu przedstawiający dostosowaną wiadomość e-mail weryfikacyjną](./media/faq/company-branded-verification-email.png)

Podpis e-mail zawiera nazwę dzierżawy Azure AD B2C podaną podczas pierwszego utworzenia dzierżawy Azure AD B2C. Nazwę można zmienić, korzystając z następujących instrukcji:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) jako Administrator globalny.
1. Otwórz blok **Azure Active Directory**.
1. Kliknij kartę **Właściwości** .
1. Zmień wartość pola **Nazwa** .
1. Kliknij przycisk **Zapisz** w górnej części strony.

Obecnie nie ma możliwości zmiany pola "from:" w wiadomości e-mail.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Jak można migrować istniejące nazwy użytkowników, hasła i profile z mojej bazy danych do Azure AD B2C?

Za pomocą interfejsu API Microsoft Graph można napisać narzędzie do migracji. Szczegółowe informacje znajdują się w [przewodniku po migracji użytkowników](user-migration.md) .

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Jakiego przepływu użytkownika hasła używa się do kont lokalnych w Azure AD B2C?

Przepływ użytkownika Azure AD B2C hasło dla kont lokalnych jest oparty na zasadach usługi Azure AD. Usługa Azure AD B2C's do rejestracji, rejestrowania lub logowania oraz resetowania haseł — przepływy użytkownika używają silnych haseł i nie tracą hasła. Aby uzyskać więcej informacji, zobacz [zasady haseł i ograniczenia w Azure Active Directory](../active-directory/authentication/concept-sspr-policy.md).

Informacje o blokadach kont i hasłach znajdują się [w temacie Zarządzanie zagrożeniami do zasobów i danych w programie Azure Active Directory B2C](threat-management.md).

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Czy można używać Azure AD Connect do migrowania tożsamości użytkowników, które są przechowywane na lokalnym Active Directory do Azure AD B2C?

Nie, Azure AD Connect nie jest zaprojektowana do pracy z Azure AD B2C. Rozważ użycie [interfejsu API Microsoft Graph](manage-user-accounts-graph-api.md) do migracji użytkowników. Szczegółowe informacje znajdują się w [przewodniku po migracji użytkowników](user-migration.md) .

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Czy moja aplikacja może otworzyć Azure AD B2C strony w elemencie iFrame?

Nie. ze względów bezpieczeństwa nie można otwierać stron Azure AD B2C w elemencie iFrame. Nasza usługa komunikuje się z przeglądarką w celu zabronienia elementów iFrame. Ogólnie rzecz biorąc, społeczność zabezpieczeń i Specyfikacja OAUTH2 zaleca się użycie elementów iFrame dla środowiska tożsamości ze względu na ryzyko związane z gniazdem kliknięcia.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Czy Azure AD B2C współpracuje z systemami CRM, takimi jak Microsoft Dynamics?

Dostępna jest integracja z portalem systemu Microsoft Dynamics 365. Zobacz [Konfigurowanie portalu Dynamics 365 do korzystania z Azure AD B2C na potrzeby uwierzytelniania](/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Czy Azure AD B2C współpracuje z lokalnym programem SharePoint 2016 lub starszym?

Azure AD B2C nie jest przeznaczona dla scenariusza udostępniania zewnętrznego partnera programu SharePoint; Zamiast tego Zobacz temat [Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/) .

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Czy należy używać Azure AD B2C lub B2B do zarządzania tożsamościami zewnętrznymi?

Przeczytaj temat [porównanie współpracy B2B i B2C w usłudze Azure AD](../active-directory/external-identities/compare-with-b2c.md) , aby dowiedzieć się więcej na temat stosowania odpowiednich funkcji do Twoich zewnętrznych scenariuszy tożsamości.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Jakie funkcje raportowania i inspekcji udostępnia Azure AD B2C? Czy są one takie same jak w Azure AD — wersja Premium?

Nie, Azure AD B2C nie obsługuje tego samego zestawu raportów co Azure AD — wersja Premium. Istnieje jednak wiele commonalities:

* **Raporty logowania** zawierają rekord każdego logowania z mniejszymi informacjami.
* **Raporty inspekcji** obejmują zarówno działanie administratora, jak i działanie aplikacji.
* **Raporty użycia** obejmują liczbę użytkowników, liczbę logowań i ilość usługi MFA.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Czy można zlokalizować interfejs użytkownika stron obsłużonych przez Azure AD B2C? Jakie języki są obsługiwane?

Tak, zobacz [Dostosowywanie języka](user-flow-language-customization.md). Udostępniamy tłumaczenia dla języków 36 i można przesłonić dowolny ciąg, aby odpowiadał Twoim potrzebom.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-contosob2clogincom-to-logincontosocom"></a>Czy mogę używać własnych adresów URL na stronach do rejestracji i logowania, które są obsługiwane przez Azure AD B2C? Czy na przykład mogę zmienić adres URL z contoso.b2clogin.com na login.contoso.com?

Obecnie nie. Ta funkcja jest dostępna w naszym przewodniku. Weryfikowanie domeny na karcie **domeny** w Azure Portal nie ma tego celu. Jednak dzięki b2clogin.com Oferujemy [neutralną domenę najwyższego poziomu](b2clogin.md), a tym samym wygląd zewnętrzny można zaimplementować bez wzmianki firmy Microsoft.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Jak mogę usunąć moją dzierżawę Azure AD B2C?

Wykonaj następujące kroki, aby usunąć dzierżawę Azure AD B2C.

Możesz użyć naszego nowego, ujednoliconego środowiska **rejestracje aplikacji** lub naszych starszych  **aplikacji (starsza wersja)** . [Dowiedz się więcej na temat nowego środowiska](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Rejestracje aplikacji](#tab/app-reg-ga/)

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) jako *administrator subskrypcji*. Użyj tego samego konta służbowego lub tego samego konto Microsoft, które zostało użyte do zarejestrowania się na platformie Azure.
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Usuń wszystkie **przepływy użytkowników (zasady)** w dzierżawie Azure AD B2C.
1. Wybierz pozycję **rejestracje aplikacji**, a następnie wybierz kartę **wszystkie aplikacje** .
1. Usuń wszystkie zarejestrowane aplikacje.
1. Usuń **B2C-Extensions-App**.
1. W obszarze **Zarządzanie** wybierz pozycję **Użytkownicy**.
1. Wybierz każdego użytkownika z kolei (wykluczanie użytkownika *administratora subskrypcji* , który jest obecnie zalogowany). Wybierz pozycję **Usuń** w dolnej części strony i wybierz opcję **tak** po wyświetleniu monitu.
1. Wybierz pozycję **Azure Active Directory** w menu po lewej stronie.
1. W obszarze **Zarządzaj** wybierz pozycję **Ustawienia użytkownika**.
1. W obszarze **Zarządzaj** wybierz pozycję **Właściwości** .
1. W obszarze **Zarządzanie dostępem do zasobów platformy Azure** wybierz pozycję **Tak**, a następnie wybierz przycisk **Zapisz**.
1. Wyloguj się z Azure Portal a następnie zaloguj się ponownie, aby odświeżyć dostęp.
1. Wybierz pozycję **Azure Active Directory** w menu po lewej stronie.
1. Na stronie **Przegląd** wybierz pozycję **Usuń dzierżawcę**. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby ukończyć proces.

#### <a name="applications-legacy"></a>[Aplikacje (starsze)](#tab/applications-legacy/)

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) jako *administrator subskrypcji*. Użyj tego samego konta służbowego lub tego samego konto Microsoft, które zostało użyte do zarejestrowania się na platformie Azure.
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Usuń wszystkie **przepływy użytkownika (zasady)** w dzierżawie Azure AD B2C.
1. Usuń wszystkie **aplikacje (starsze)** zarejestrowane w dzierżawie Azure AD B2C.
1. Wybierz pozycję **Azure Active Directory** w menu po lewej stronie.
1. W obszarze **Zarządzanie** wybierz pozycję **Użytkownicy**.
1. Wybierz każdego użytkownika z kolei (wykluczanie użytkownika *administratora subskrypcji* , który jest obecnie zalogowany). Wybierz pozycję **Usuń** w dolnej części strony i wybierz opcję **tak** po wyświetleniu monitu.
1. W obszarze **Zarządzanie** wybierz pozycję **Rejestracje aplikacji**.
1. Wybierz pozycję **Wyświetl wszystkie aplikacje**
1. Wybierz aplikację o nazwie **B2C-Extensions-App**, wybierz pozycję **Usuń**, a następnie wybierz pozycję **tak** po wyświetleniu monitu.
1. W obszarze **Zarządzaj** wybierz pozycję **Ustawienia użytkownika**.
1. Jeśli jest obecny, w obszarze **połączenia konta serwisu LinkedIn** wybierz pozycję **nie**, a następnie wybierz pozycję **Zapisz**.
1. W obszarze **Zarządzaj** wybierz pozycję **Właściwości** .
1. W obszarze **Zarządzanie dostępem do zasobów platformy Azure** wybierz pozycję **Tak**, a następnie wybierz przycisk **Zapisz**.
1. Wyloguj się z Azure Portal a następnie zaloguj się ponownie, aby odświeżyć dostęp.
1. Wybierz pozycję **Azure Active Directory** w menu po lewej stronie.
1. Na stronie **Przegląd** wybierz pozycję **Usuń katalog**. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby ukończyć proces.

* * *

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Czy mogę uzyskać Azure AD B2C w ramach pakietu Enterprise Mobility Suite?

Nie, Azure AD B2C to usługa Azure z opcją płatność zgodnie z rzeczywistym użyciem i nie jest częścią pakietu Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Jak mogę zgłosić problemy z Azure AD B2C?

Zapoznaj się z tematem [Obsługa plików żądania Azure Active Directory B2C](support-options.md).