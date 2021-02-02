---
title: Informacje na temat logowania jednokrotnego opartego na protokole SAML dla aplikacji w Azure Active Directory
description: Informacje na temat logowania jednokrotnego opartego na protokole SAML dla aplikacji w Azure Active Directory
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/28/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: f6422c3f751a7aaa430b93ee68ca1a3520ac915f
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257696"
---
# <a name="understand-saml-based-single-sign-on"></a>Omówienie logowania jednokrotnego opartego na protokole SAML

W [serii szybkiego startu](view-applications-portal.md) w zarządzaniu aplikacjami wiesz, jak używać usługi Azure AD jako dostawcy tożsamości (dostawcy tożsamości) dla aplikacji. W tym artykule opisano opcję opartą na protokole SAML do logowania jednokrotnego. 


## <a name="before-you-begin"></a>Zanim rozpoczniesz

Korzystanie z usługi Azure AD jako dostawcy tożsamości (dostawcy tożsamości) i Konfigurowanie logowania jednokrotnego (SSO) może być proste lub złożone w zależności od używanej aplikacji. Niektóre aplikacje można skonfigurować za pomocą zaledwie kilku akcji. Inne wymagają konfiguracji szczegółowej. Aby szybko uzyskać informacje, zapoznaj się z [serią szybkiego startu](view-applications-portal.md) w zarządzaniu aplikacjami. Jeśli dodawana aplikacja jest prosta, prawdopodobnie nie musisz czytać tego artykułu. Jeśli dodawana aplikacja wymaga konfiguracji niestandardowej dla logowania jednokrotnego opartego na protokole SAML, ten artykuł jest dla Ciebie.

W [serii szybkiego startu](add-application-portal-setup-sso.md)znajduje się artykuł dotyczący konfigurowania logowania jednokrotnego. W tym temacie dowiesz się, jak uzyskać dostęp do strony konfiguracji protokołu SAML dla aplikacji. Strona konfiguracja protokołu SAML zawiera pięć sekcji. Te sekcje zostały szczegółowo omówione w tym artykule.

> [!IMPORTANT] 
> Istnieją sytuacje, w których opcja **logowania** jednokrotnego nie będzie obecna w nawigacji dla aplikacji w aplikacjach dla **przedsiębiorstw**. 
>
> Jeśli aplikacja została zarejestrowana przy użyciu **rejestracje aplikacji** , funkcja logowania jednokrotnego jest domyślnie skonfigurowana do używania protokołu OAuth OIDC. W takim przypadku opcja **logowania** jednokrotnego nie będzie widoczna w obszarze nawigacji w obszarze **aplikacje dla przedsiębiorstw**. W przypadku dodawania niestandardowej aplikacji przy użyciu **rejestracje aplikacji** można skonfigurować opcje w pliku manifestu. Aby dowiedzieć się więcej na temat pliku manifestu, zobacz [Azure Active Directory manifest aplikacji](../develop/reference-app-manifest.md). Aby dowiedzieć się więcej na temat standardów rejestracji jednokrotnej, zobacz [uwierzytelnianie i autoryzacja przy użyciu platformy tożsamości firmy Microsoft](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform). 
>
> Inne scenariusze, w których nie będzie można korzystać z **logowania** jednokrotnego w nawigacji, obejmują, gdy aplikacja jest hostowana w innej dzierżawie lub że Twoje konto nie ma wymaganych uprawnień (Administrator globalny, administrator aplikacji w chmurze, administrator aplikacji lub właściciel jednostki usługi). Uprawnienia mogą również prowadzić do scenariusza, w którym można otworzyć **Logowanie jednokrotne** , ale nie będzie można go zapisać. Aby dowiedzieć się więcej na temat ról administracyjnych usługi Azure AD, zobacz https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-saml-configuration"></a>Podstawowa konfiguracja SAML

Należy uzyskać wartości od dostawcy aplikacji. Można ręcznie wprowadzić wartości lub przekazać plik metadanych w celu wyodrębnienia wartości pól.

> [!TIP]
> Wiele aplikacji jest już wstępnie skonfigurowanych do pracy z usługą Azure AD. Te aplikacje są wymienione w galerii aplikacji, które można przeglądać po dodaniu aplikacji do dzierżawy usługi Azure AD. [Seria szybkiego startu](add-application-portal-setup-sso.md) przeprowadzi Cię przez proces. W przypadku aplikacji w galerii znajdziesz szczegółowe instrukcje krok po kroku. Aby uzyskać dostęp do tych czynności, możesz kliknąć link na stronie Konfiguracja protokołu SAML dla aplikacji zgodnie z opisem w serii szybkiego startu lub przejrzeć listę wszystkich samouczków konfiguracji aplikacji w [samouczkach konfiguracji aplikacji SaaS](../saas-apps/tutorial-list.md).

| Podstawowe ustawienia konfiguracji SAML | Zainicjowane przez dostawcę usługi | Zainicjowane przez dostawcę tożsamości | Opis |
|:--|:--|:--|:--|
| **Identyfikator (identyfikator jednostki)** | Wymagane w przypadku niektórych aplikacji | Wymagane w przypadku niektórych aplikacji | Jednoznacznie identyfikuje aplikację. Usługa Azure AD wysyła identyfikator do aplikacji jako parametr odbiorców tokenu SAML. Aplikacja powinna go zweryfikować. Ta wartość jest widoczna również jako identyfikator jednostki w dowolnych metadanych SAML udostępnianych przez aplikację. Wprowadź adres URL, który używa następującego wzorca: "https:// <subdomain> . contoso.com" *można znaleźć tę wartość jako element **Issuer** w **AuthnRequest** (żądanie SAML) wysyłanej przez aplikację.* |
| **Adres URL odpowiedzi** | Wymagane | Wymagane | Określa miejsce, w którym aplikacja oczekuje otrzymać token języka SAML. Adres URL odpowiedzi jest również nazywany adresem URL usługi Assertion Consumer Service (ACS). Aby określić wiele adresów URL odpowiedzi, można użyć pól dodatkowych adresów URL odpowiedzi. Na przykład mogą być potrzebne dodatkowe adresy URL odpowiedzi dla wielu poddomen. Lub w celach testowych można jednocześnie określić wiele adresów URL odpowiedzi (lokalnego hosta i publicznych adresów URL). |
| **Adres URL logowania** | Wymagane | Nie podawaj | Gdy użytkownik otwiera ten adres URL, dostawca usługi przekierowuje go do usługi Azure AD w celu uwierzytelnienia i zalogowania. Usługa Azure AD używa adresu URL do uruchamiania aplikacji z Microsoft 365 lub Moje aplikacje usługi Azure AD. Gdy to pole jest puste, usługa Azure AD wykonuje logowanie zainicjowane przez dostawcy tożsamości, gdy użytkownik uruchamia aplikację z Microsoft 365, Azure AD moje aplikacje lub adres URL rejestracji jednokrotnej usługi Azure AD.|
| **Stan przekaźnika** | Opcjonalne | Opcjonalne | Określa aplikacji, dokąd przekierować użytkownika po zakończeniu uwierzytelniania. Zazwyczaj wartość jest prawidłowym adresem URL dla aplikacji. Jednak niektóre aplikacje używają tego pola inaczej. Aby uzyskać więcej informacji, skontaktuj się z dostawcą aplikacji.
| **Adres URL wylogowywania** | Opcjonalne | Opcjonalne | Służy do wysyłania odpowiedzi na wylogowanie SAML z powrotem do aplikacji.

## <a name="user-attributes-and-claims"></a>Atrybuty użytkownika i oświadczenia 

Gdy użytkownik uwierzytelnia się w aplikacji, usługa Azure AD wystawia aplikację tokenem SAML z informacjami (lub oświadczeniami) dotyczącymi użytkownika, który jednoznacznie identyfikuje je. Domyślnie te informacje obejmują nazwę użytkownika, adres e-mail, imię i nazwisko. Może być konieczne dostosowanie tych oświadczeń, jeśli na przykład aplikacja wymaga określonych wartości oświadczenia lub formatu **nazwy** innej niż nazwa użytkownika. 

> [!IMPORTANT]
> Wiele aplikacji jest już wstępnie skonfigurowanych i nie trzeba martwić się o Ustawianie oświadczeń użytkowników i grup. [Seria szybkiego startu](add-application-portal.md) przeprowadzi Cię przez proces dodawania i konfigurowania aplikacji.


**Unikatowy identyfikator użytkownika (identyfikator nazwy)** to wymagane żądanie i jest ważne. Wartość domyślna to *User. userPrincipalName*. Identyfikator użytkownika jednoznacznie identyfikuje każdego użytkownika w aplikacji. Jeśli na przykład adresem e-mail jest zarówno nazwa użytkownika, jak i unikatowy identyfikator, ustaw wartość *user.mail*.

Aby dowiedzieć się więcej o dostosowywaniu oświadczeń SAML, zobacz [How to: Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](../develop/active-directory-saml-claims-customization.md).

Możesz dodać nowe oświadczenia, aby uzyskać szczegółowe informacje, zobacz [Dodawanie oświadczeń specyficznych dla aplikacji](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims) lub Dodawanie oświadczeń grup, zobacz [Konfigurowanie oświadczeń grup](../hybrid/how-to-connect-fed-group-claims.md).


> [!NOTE]
> Dodatkowe sposoby dostosowywania tokenu SAML z usługi Azure AD do aplikacji można znaleźć w następujących zasobach.
>- Aby utworzyć role niestandardowe za pośrednictwem Azure Portal, zobacz [Konfigurowanie oświadczeń ról](../develop/active-directory-enterprise-app-role-management.md).
>- Aby dostosować oświadczenia za pomocą programu PowerShell, zobacz [Dostosowywanie oświadczeń — PowerShell](../develop/active-directory-claims-mapping.md).
>- Aby zmodyfikować manifest aplikacji w celu skonfigurowania opcjonalnych oświadczeń dla aplikacji, zobacz [Konfigurowanie opcjonalnych oświadczeń](../develop/active-directory-optional-claims.md).
>- Aby ustawić zasady okresu istnienia tokenu dla tokenów odświeżania, tokenów dostępu, tokenów sesji i tokenów identyfikatorów, zobacz [Konfigurowanie okresów istnienia tokenu](../develop/active-directory-configurable-token-lifetimes.md). Aby ograniczyć sesje uwierzytelniania za pośrednictwem dostępu warunkowego usługi Azure AD, zobacz [możliwości zarządzania sesjami uwierzytelniania](../conditional-access/howto-conditional-access-session-lifetime.md).

## <a name="saml-signing-certificate"></a>Certyfikat podpisywania SAML

Usługa Azure AD używa certyfikatu do podpisywania tokenów SAML wysyłanych do aplikacji. Ten certyfikat jest wymagany do skonfigurowania relacji zaufania między usługą Azure AD a aplikacją. Aby uzyskać szczegółowe informacje na temat formatu certyfikatu, zobacz dokumentację SAML aplikacji. Aby uzyskać więcej informacji, zobacz [Zarządzanie certyfikatami dla federacyjnego logowania](manage-certificates-for-federated-single-sign-on.md) jednokrotnego i [Zaawansowane opcje podpisywania certyfikatu w tokenie SAML](certificate-signing-options.md).

> [!IMPORTANT]
> Wiele aplikacji jest już wstępnie skonfigurowanych i znajduje się w galerii aplikacji i nie ma potrzeby szczegółowe do certyfikatów. [Seria szybkiego startu](add-application-portal.md) przeprowadzi Cię przez proces dodawania i konfigurowania aplikacji.

W usłudze Azure AD można pobrać aktywny certyfikat w formacie base64 lub RAW bezpośrednio z poziomu głównego **Ustawienia Single Sign-On ze stroną SAML** . Ponadto można uzyskać aktywny certyfikat przez pobranie pliku XML metadanych aplikacji lub użycie adresu URL metadanych federacji aplikacji. Aby wyświetlić, utworzyć lub pobrać certyfikaty (aktywne lub nieaktywne), wykonaj następujące kroki.

Niektóre typowe elementy do sprawdzenia, czy certyfikat obejmują: 
   - *Poprawna data wygaśnięcia.* Można skonfigurować datę wygaśnięcia na maksymalnie trzy lata w przyszłości.
   - *Stan aktywny dla odpowiedniego certyfikatu.* Jeśli stan jest **nieaktywny**, Zmień stan na **aktywny**. Aby zmienić stan, kliknij prawym przyciskiem myszy wiersz certyfikatu i wybierz pozycję **Ustaw certyfikat jako aktywny**.
   - *Poprawna opcja podpisywania i algorytm.*
   - *Poprawne adresy e-mail powiadomień.* Gdy aktywny certyfikat zbliża się do daty wygaśnięcia, usługa Azure AD wyśle powiadomienie na adres e-mail skonfigurowany w tym polu.

Czasami może być konieczne pobranie certyfikatu. Należy zachować ostrożność, gdzie ją zapisujesz! Aby pobrać certyfikat, wybierz jedną z opcji format Base64, format nieprzetworzony lub XML metadanych Federacji. Usługa Azure AD udostępnia również **adres URL metadanych federacji aplikacji** , w którym można uzyskać dostęp do metadanych specyficznych dla aplikacji w formacie `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` .

> [!NOTE]
> Aplikacja powinna mieć możliwość obsługi znacznika kolejności bajtów znajdującego się w kodzie XML renderowanym podczas korzystania z programu https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id} . Znacznik kolejności bajtów jest reprezentowany jako znak ASCII niedrukowalny» ¿y i w formacie szesnastkowym, który jest reprezentowany przez EF BBbinding podczas przeglądania danych XML.

Aby wprowadzić zmiany w certyfikatach, wybierz przycisk Edytuj. Na stronie **certyfikatu podpisywania SAML** można wykonać kilka czynności:
   - Utwórz nowy certyfikat: wybierz pozycję **nowy certyfikat**, wybierz **datę wygaśnięcia**, a następnie wybierz pozycję **Zapisz**. Aby uaktywnić certyfikat, wybierz menu kontekstowe (**...**) i wybierz pozycję **Ustaw certyfikat jako aktywny**.
   - Przekaż certyfikat z kluczem prywatnym i poświadczeniami PFX: wybierz pozycję **Importuj certyfikat** i przejdź do certyfikatu. Wprowadź **hasło PFX**, a następnie wybierz pozycję **Dodaj**.  
   - Skonfiguruj zaawansowane podpisywanie certyfikatu. Aby uzyskać więcej informacji na temat tych opcji, zobacz [Zaawansowane opcje podpisywania certyfikatu](certificate-signing-options.md).
   - Powiadamiaj dodatkowe osoby, gdy aktywny certyfikat zbliża się do daty wygaśnięcia: wprowadź adresy e-mail w polach **powiadomienia e-mail adresy** .

## <a name="set-up-the-application-to-use-azure-ad"></a>Konfigurowanie aplikacji do korzystania z usługi Azure AD

Sekcja **Konfigurowanie \<applicationName>** zawiera listę wartości, które należy skonfigurować w aplikacji, aby używały usługi Azure AD jako dostawcy tożsamości SAML. Należy ustawić wartości na stronie konfiguracji w witrynie sieci Web aplikacji. Na przykład jeśli konfigurujesz usługi GitHub, przejdź do witryny github.com i ustaw wartości. Jeśli aplikacja została już wstępnie skonfigurowana i w galerii usługi Azure AD, znajdziesz link umożliwiający **wyświetlenie instrukcji krok po kroku**. W przeciwnym razie należy znaleźć dokumentację dla konfigurowanej aplikacji. 

Wartości **adresu URL logowania** i **wylogowania** są rozpoznawane w tym samym punkcie końcowym, który jest punktem końcowym obsługi żądania SAML dla dzierżawy usługi Azure AD. 

**Identyfikator usługi Azure AD** jest wartością **wystawcy** w tokenie SAML wystawionym dla aplikacji.

## <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Po skonfigurowaniu aplikacji do korzystania z usługi Azure AD jako dostawcy tożsamości opartej na protokole SAML można przetestować ustawienia, aby sprawdzić, czy logowanie jednokrotne działa dla Twojego konta. 

Wybierz pozycję **test** , a następnie wybierz opcję testowania przy użyciu aktualnie zalogowanego użytkownika lub innej osoby. 

Jeśli logowanie powiedzie się, możesz przydzielić użytkowników i grupy do aplikacji SAML. Gratulacje!

Jeśli zostanie wyświetlony komunikat o błędzie, wykonaj następujące czynności:

1. Skopiuj szczegóły i wklej je w polu **Jak wygląda błąd?**.

    ![Uzyskiwanie wskazówek dotyczących rozwiązywania](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Wybierz pozycję **Uzyskaj wskazówki dotyczące rozpoznawania**. Zostaną wyświetlone główna przyczyna i wskazówki dotyczące rozwiązywania.  W tym przykładzie użytkownik nie został przypisany do aplikacji.

3. Przeczytaj wskazówki dotyczące rozdzielczości, a następnie spróbuj rozwiązać problem.

4. Uruchamiaj test ponownie, dopóki nie zostanie pomyślnie zakończony.

Aby uzyskać więcej informacji, zobacz Debugowanie logowania jednokrotnego [opartego na protokole SAML do aplikacji w Azure Active Directory](./debug-saml-sso-issues.md).


## <a name="next-steps"></a>Następne kroki

- [Seria szybkiego startu w zarządzaniu aplikacjami](view-applications-portal.md)
- [Przypisywanie użytkowników lub grup do aplikacji](./assign-user-or-group-access-portal.md)
- [Konfigurowanie automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [Protokół SAML logowania jednokrotnego](../develop/single-sign-on-saml-protocol.md)
