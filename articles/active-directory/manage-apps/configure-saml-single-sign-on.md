---
title: Understand SAML-based single sign-on (SSO) for apps in Azure Active Directory
description: Understand SAML-based single sign-on (SSO) for apps in Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/28/2020
ms.author: iangithinji
ms.reviewer: arvinh,luleon
ms.openlocfilehash: b7468f33c75dd58e70c344f3ef19c51e220a7abb
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374492"
---
# <a name="understand-saml-based-single-sign-on"></a>Understand SAML-based single sign-on (Informacje na temat logowania pojedynczego opartego na saml)

W serii [przewodników Szybki](view-applications-portal.md) start dotyczącej zarządzania aplikacją opisano, jak używać usługi Azure AD jako dostawcy tożsamości dla aplikacji. W tym artykule bardziej szczegółowo opisano opcję logowania pojedynczego opartą na języku SAML. 


## <a name="before-you-begin"></a>Zanim rozpoczniesz

Korzystanie z usługi Azure AD jako dostawcy tożsamości i konfigurowanie logowania jednokrotnego może być proste lub złożone w zależności od używanej aplikacji. Niektóre aplikacje można skonfigurować za pomocą zaledwie kilku akcji. Inne wymagają szczegółowej konfiguracji. Aby szybko uzyskać wiedzę, należy przejść przez [serię przewodników Szybki start](view-applications-portal.md) na temat zarządzania aplikacją. Jeśli aplikacja, która dodajesz, jest prosta, prawdopodobnie nie musisz czytać tego artykułu. Jeśli dodawania aplikacji wymaga niestandardowej konfiguracji logowania jednokrotnego opartego na saml, ten artykuł jest dla Ciebie.

W serii [przewodników Szybki](add-application-portal-setup-sso.md)start znajduje się artykuł na temat konfigurowania logowania pojedynczego. Z tego pliku dowiesz się, jak uzyskać dostęp do strony konfiguracji SAML dla aplikacji. Strona konfiguracji saml zawiera pięć sekcji. Te sekcje zostały szczegółowo omówione w tym artykule.

> [!IMPORTANT] 
> Istnieją pewne scenariusze, w których opcja logowania **pojedynczego** nie będzie dostępna w nawigacji dla aplikacji w **aplikacjach dla przedsiębiorstw.** 
>
> Jeśli aplikacja została zarejestrowana przy użyciu **Rejestracje aplikacji,** funkcja logowania pojedynczego jest domyślnie skonfigurowana do używania OIDC OAuth. W takim przypadku opcja Logowanie **pojedyncze** nie będzie pokazywana w obszarze nawigacji w obszarze **Aplikacje dla przedsiębiorstw.** Gdy używasz **Rejestracje aplikacji** do dodawania aplikacji niestandardowej, konfigurujesz opcje w pliku manifestu. Aby dowiedzieć się więcej na temat pliku manifestu, [zobacz Azure Active Directory manifestu aplikacji](../develop/reference-app-manifest.md). Aby dowiedzieć się więcej na temat standardów logowania jednokrotnego, zobacz [Authentication and authorization using Microsoft identity platform (Uwierzytelnianie i autoryzacja przy użyciu platformy tożsamości firmy Microsoft).](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform) 
>
> Inne scenariusze, w których w nawigacji brakuje logowania pojedynczego, obejmują sytuacje, w których aplikacja jest hostowana w innej dzierżawie lub jeśli Konto nie ma wymaganych uprawnień (administrator globalny, administrator aplikacji w chmurze, administrator aplikacji lub właściciel jednostki usługi).  Uprawnienia mogą również spowodować scenariusz, w którym można otworzyć logowanie **pojedyncze,** ale nie będzie można go zapisać. Aby dowiedzieć się więcej na temat ról administracyjnych usługi Azure AD, zobacz ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-saml-configuration"></a>Podstawowa konfiguracja saml

Należy pobrać wartości od dostawcy aplikacji. Możesz ręcznie wprowadzić wartości lub przekazać plik metadanych, aby wyodrębnić wartość pól.

> [!TIP]
> Wiele aplikacji zostało już wstępnie skonfigurowanych do pracy z usługą Azure AD. Te aplikacje są wyświetlane w galerii aplikacji, które można przeglądać po dodaniu aplikacji do dzierżawy usługi Azure AD. W [tej serii przewodników](add-application-portal-setup-sso.md) Szybki start osłaniasz ten proces. W przypadku aplikacji w galerii znajdują się szczegółowe instrukcje krok po kroku. Aby uzyskać dostęp do kroków, możesz kliknąć link na stronie konfiguracji SAML dla aplikacji zgodnie z opisem w serii Szybki start lub przejrzeć listę wszystkich samouczków konfiguracji aplikacji w samouczkach konfiguracji aplikacji [SaaS.](../saas-apps/tutorial-list.md)

| Podstawowe ustawienie konfiguracji saml | Zainicjowane przez dostawcę usługi | Zainicjowane przez dostawcę tożsamości | Opis |
|:--|:--|:--|:--|
| **Identyfikator (identyfikator jednostki)** | Wymagane w przypadku niektórych aplikacji | Wymagane w przypadku niektórych aplikacji | Unikatowo identyfikuje aplikację. Usługa Azure AD wysyła identyfikator do aplikacji jako parametr odbiorców tokenu SAML. Aplikacja powinna go zweryfikować. Ta wartość jest widoczna również jako identyfikator jednostki w dowolnych metadanych SAML udostępnianych przez aplikację. Wprowadź adres URL, który używa następującego wzorca: "https:// .contoso.com" Tę wartość można znaleźć jako <subdomain> element ***Wystawca** w **żądaniu SAML (AuthnRequest)*** wysłanym przez aplikację. |
| **Adres URL odpowiedzi** | Wymagane | Wymagane | Określa miejsce, w którym aplikacja oczekuje otrzymać token języka SAML. Adres URL odpowiedzi jest również nazywany adresem URL usługi Assertion Consumer Service (ACS). Możesz użyć dodatkowych pól adresu URL odpowiedzi, aby określić wiele adresów URL odpowiedzi. Na przykład mogą być potrzebne dodatkowe adresy URL odpowiedzi dla wielu poddomen. Do celów testowych można również określić wiele adresów URL odpowiedzi (hosta lokalnego i publicznych adresów URL) jednocześnie. |
| **Adres URL logowania** | Wymagane | Nie podawaj | Gdy użytkownik otwiera ten adres URL, dostawca usługi przekierowuje go do usługi Azure AD w celu uwierzytelnienia i zalogowania. Usługa Azure AD używa adresu URL do uruchamiania aplikacji z Microsoft 365 lub Azure AD Moje aplikacje. Jeśli ta wartość jest pusta, usługa Azure AD uruchamia logowanie inicjowane przez idP, gdy użytkownik uruchamia aplikację z usługi Microsoft 365, Azure AD Moje aplikacje lub adresu URL logowania jednokrotnego usługi Azure AD.|
| **Stan przekaźnika** | Opcjonalne | Opcjonalne | Określa aplikacji, dokąd przekierować użytkownika po zakończeniu uwierzytelniania. Zazwyczaj wartość jest prawidłowym adresem URL aplikacji. Jednak niektóre aplikacje używają tego pola inaczej. Aby uzyskać więcej informacji, skontaktuj się z dostawcą aplikacji.
| **Adres URL wylogowywania** | Opcjonalne | Opcjonalne | Służy do wysyłania odpowiedzi wylogowania SAML z powrotem do aplikacji.

## <a name="user-attributes-and-claims"></a>Atrybuty i oświadczenia użytkownika 

Gdy użytkownik uwierzytelnia się w aplikacji, usługa Azure AD wydaje aplikacji token SAML z informacjami (lub oświadczeniami) o użytkowniku, który jednoznacznie je identyfikuje. Domyślnie te informacje obejmują nazwę użytkownika, adres e-mail, imię i nazwisko użytkownika. Może być konieczne dostosowanie tych oświadczeń, jeśli na przykład aplikacja wymaga określonych wartości oświadczeń lub formatu **nazwy** innego niż nazwa użytkownika. 

> [!IMPORTANT]
> Wiele aplikacji jest już wstępnie skonfigurowanych i w galerii aplikacji i nie musisz martwić się o ustawianie oświadczeń użytkowników i grup. Seria [przewodników Szybki](add-application-portal.md) start umożliwia dodawanie i konfigurowanie aplikacji.


Wartość **unikatowego identyfikatora użytkownika (identyfikatora nazwy)** jest oświadczeniem wymaganym i jest ważna. Wartość domyślna to *user.userprincipalname*. Identyfikator użytkownika jednoznacznie identyfikuje każdego użytkownika w aplikacji. Jeśli na przykład adresem e-mail jest zarówno nazwa użytkownika, jak i unikatowy identyfikator, ustaw wartość *user.mail*.

Aby dowiedzieć się więcej na temat dostosowywania oświadczeń SAML, zobacz Jak: dostosowywanie oświadczeń wystawionych w [tokenie SAML dla aplikacji dla przedsiębiorstw.](../develop/active-directory-saml-claims-customization.md)

Możesz dodać nowe oświadczenia. Aby uzyskać szczegółowe informacje, zobacz [Dodawanie oświadczeń specyficznych](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims) dla aplikacji lub Dodawanie oświadczeń grupy, zobacz [Konfigurowanie oświadczeń grupy.](../hybrid/how-to-connect-fed-group-claims.md)


> [!NOTE]
> Aby uzyskać dodatkowe sposoby dostosowywania tokenu SAML z usługi Azure AD do aplikacji, zobacz następujące zasoby.
>- Aby utworzyć role niestandardowe za pośrednictwem Azure Portal, [zobacz Konfigurowanie oświadczeń roli.](../develop/active-directory-enterprise-app-role-management.md)
>- Aby dostosować oświadczenia za pośrednictwem programu PowerShell, zobacz [Dostosowywanie oświadczeń — PowerShell.](../develop/active-directory-claims-mapping.md)
>- Aby zmodyfikować manifest aplikacji w celu skonfigurowania opcjonalnych oświadczeń dla aplikacji, [zobacz Konfigurowanie opcjonalnych oświadczeń](../develop/active-directory-optional-claims.md).
>- Aby ustawić zasady okresu istnienia tokenów dla tokenów odświeżania, tokenów dostępu, tokenów sesji i tokenów identyfikatorów, zobacz [Konfigurowanie okresów istnienia tokenów.](../develop/active-directory-configurable-token-lifetimes.md) Aby ograniczyć sesje uwierzytelniania za pośrednictwem dostępu warunkowego usługi Azure AD, zobacz [możliwości zarządzania sesjami uwierzytelniania.](../conditional-access/howto-conditional-access-session-lifetime.md)

## <a name="saml-signing-certificate"></a>Certyfikat podpisywania SAML

Usługa Azure AD używa certyfikatu do podpisywania tokenów SAML, które wysyła do aplikacji. Ten certyfikat jest potrzebny do skonfigurowania relacji zaufania między usługą Azure AD i aplikacją. Aby uzyskać szczegółowe informacje na temat formatu certyfikatu, zobacz dokumentację SAML aplikacji. Aby uzyskać więcej informacji, zobacz [Manage certificates for federated single sign-on](manage-certificates-for-federated-single-sign-on.md) (Zarządzanie certyfikatami dla federowego logowania pojedynczego) i Advanced certificate signing options (Zaawansowane opcje podpisywania certyfikatów) [w tokenie SAML](certificate-signing-options.md).

> [!IMPORTANT]
> Wiele aplikacji jest już wstępnie skonfigurowanych i w galerii aplikacji i nie trzeba zagłębiać się w certyfikaty. Seria [przewodników Szybki](add-application-portal.md) start umożliwia rozpoczęcie dodawania i konfigurowania aplikacji.

Z usługi Azure AD możesz pobrać aktywny certyfikat w formacie Base64 lub Raw bezpośrednio z głównej strony Konfigurowanie aplikacji Sign-On **saml.** Ponadto możesz uzyskać aktywny certyfikat, pobierając plik XML metadanych aplikacji lub używając adresu URL metadanych federacji aplikacji. Aby wyświetlić, utworzyć lub pobrać certyfikaty (aktywne lub nieaktywne), wykonaj następujące kroki.

Niektóre typowe rzeczy, które należy sprawdzić, aby zweryfikować certyfikat, to: 
   - *Prawidłowa data wygaśnięcia.* Datę wygaśnięcia można skonfigurować na maksymalnie trzy lata w przyszłości.
   - *Stan aktywny dla odpowiedniego certyfikatu.* Jeśli stan to **Nieaktywny,** zmień stan na **Aktywny.** Aby zmienić stan, kliknij prawym przyciskiem myszy wiersz certyfikatu i wybierz polecenie **Aktywna certyfikatu.**
   - *Poprawna opcja podpisywania i algorytm.*
   - *Poprawne adresy e-mail powiadomień.* Gdy aktywny certyfikat zbliża się do daty wygaśnięcia, usługa Azure AD wysyła powiadomienie na adres e-mail skonfigurowany w tym polu.

Czasami może być konieczne pobranie certyfikatu. Zachowaj ostrożność w miejscu, w którym go zapisujesz. Aby pobrać certyfikat, wybierz jedną z opcji dla formatu Base64, formatu nieprzetworzowego lub XML metadanych federacji. Usługa Azure AD udostępnia również **adres URL** metadanych federacji aplikacji, w którym można uzyskać dostęp do metadanych specyficznych dla aplikacji w formacie `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` .

> [!NOTE]
> Aplikacja powinna być w stanie obsługi znacznika kolejności bajtów obecnego w pliku XML renderowanym podczas korzystania z elementu https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id} . Znak kolejności bajtów jest reprezentowany jako znak ASCII niedrukowalny »", a w formacie Szesnastka jest reprezentowany jako EF BB BF podczas przeglądania danych XML.

Aby wprowadzić zmiany certyfikatu, wybierz przycisk Edytuj. Na stronie Certyfikat podpisywania **SAML** można wykonać kilka czynności:
   - Utwórz nowy certyfikat: wybierz **pozycję Nowy certyfikat,** wybierz pozycję **Data wygaśnięcia,** a następnie wybierz pozycję **Zapisz.** Aby aktywować certyfikat, wybierz menu kontekstowe (**...**), a następnie wybierz pozycję **Aktywuj certyfikat.**
   - Przekaż certyfikat z kluczem prywatnym i poświadczeniami pfx: wybierz **pozycję Importuj certyfikat** i przejdź do certyfikatu. Wprowadź hasło **PFX,** a następnie wybierz pozycję **Dodaj**.  
   - Konfigurowanie zaawansowanego podpisywania certyfikatów. Aby uzyskać więcej informacji na temat tych opcji, zobacz [Zaawansowane opcje podpisywania certyfikatów.](certificate-signing-options.md)
   - Powiadom dodatkowe osoby, gdy aktywny certyfikat zbliża się do daty wygaśnięcia: wprowadź adresy e-mail w polach **Adresy e-mail powiadomień.**

## <a name="set-up-the-application-to-use-azure-ad"></a>Konfigurowanie aplikacji do korzystania z usługi Azure AD

Sekcja **Konfigurowanie \<applicationName> zawiera** listę wartości, które należy skonfigurować w aplikacji, aby używać usługi Azure AD jako dostawcy tożsamości SAML. Wartości są ustawiane na stronie konfiguracji w witrynie internetowej aplikacji. Jeśli na przykład konfigurujesz usługę GitHub, przejdź do witryny github.com i ustaw wartości. Jeśli aplikacja jest już wstępnie skonfigurowana i w galerii usługi Azure AD, znajdziesz link do instrukcji krok **po kroku dotyczących wyświetlania.** W przeciwnym razie należy znaleźć dokumentację konfigurowalnej aplikacji. 

Zarówno **wartości adresu URL logowania,** jak i adresu **URL** wylogowywu są rozpoznane jako ten sam punkt końcowy, który jest punktem końcowym obsługi żądań SAML dla dzierżawy usługi Azure AD. 

Identyfikator **usługi Azure AD jest** wartością wystawcy w tokenie SAML wystawionym dla aplikacji. 

## <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Po skonfigurowaniu aplikacji do korzystania z usługi Azure AD jako dostawcy tożsamości opartego na saml możesz przetestować ustawienia, aby sprawdzić, czy logowanie pojedynczej działa dla Twojego konta. 

Wybierz **pozycję Testuj,** a następnie wybierz opcję testowania z aktualnie zalogowanym użytkownikiem lub inną osobą. 

Jeśli logowanie powiedzie się, możesz przypisać użytkowników i grupy do aplikacji SAML. Gratulacje!

Jeśli zostanie wyświetlony komunikat o błędzie, wykonaj następujące czynności:

1. Skopiuj szczegóły i wklej je w polu **Jak wygląda błąd?**.

    ![Uzyskiwanie wskazówek dotyczących rozwiązywania](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Wybierz **pozycję Uzyskaj wskazówki dotyczące rozwiązywania problemów.** Zostaną wyświetlone główna przyczyna i wskazówki dotyczące rozwiązywania.  W tym przykładzie użytkownik nie został przypisany do aplikacji.

3. Przeczytaj wskazówki dotyczące rozwiązywania problemu, a następnie spróbuj rozwiązać problem.

4. Uruchamiaj test ponownie, dopóki nie zostanie pomyślnie zakończony.

Aby uzyskać więcej informacji, zobacz [Debug SAML-based single sign-on to applications in Azure Active Directory](./debug-saml-sso-issues.md).


## <a name="next-steps"></a>Następne kroki

- [Seria przewodników Szybki start na temat zarządzania aplikacją](view-applications-portal.md)
- [Przypisywanie użytkowników lub grup do aplikacji](./assign-user-or-group-access-portal.md)
- [Konfigurowanie automatycznego aprowizowania kont użytkowników](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [Protokół SAML logowania jednokrotnego](../develop/single-sign-on-saml-protocol.md)
