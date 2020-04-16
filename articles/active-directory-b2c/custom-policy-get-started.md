---
title: Wprowadzenie do zasad niestandardowych
titleSuffix: Azure AD B2C
description: Dowiedz się, jak rozpocząć pracę z niestandardowymi zasadami w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 856bd6c2a3546a438293e89a0b576e1392d9c6a5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407292"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Wprowadzenie do niestandardowych zasad w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Zasady niestandardowe](custom-policy-overview.md) to pliki konfiguracyjne definiujące zachowanie dzierżawy usługi Azure Active Directory B2C (Azure AD B2C). W tym artykule utworzysz niestandardową zasadę, która obsługuje rejestrację konta lokalnego lub logowanie się przy użyciu adresu e-mail i hasła. Należy również przygotować środowisko do dodawania dostawców tożsamości.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jeszcze go nie masz, [utwórz dzierżawę usługi Azure AD B2C](tutorial-create-tenant.md) połączona z subskrypcją platformy Azure.
- [Zarejestruj aplikację](tutorial-register-applications.md) w utworzonej dzierżawie, aby mogła komunikować się z usługą Azure AD B2C.
- Wykonaj czynności opisane w temacie [Konfigurowanie rejestracji i logowanie się za pomocą konta na Facebooku w](identity-provider-facebook.md) celu skonfigurowania aplikacji na Facebooku. Mimo że aplikacja Facebooka nie jest wymagana do korzystania z zasad niestandardowych, jest używana w tym instruktażu, aby zademonstrować włączenie logowania społecznościowego w zasadach niestandardowych.

## <a name="add-signing-and-encryption-keys"></a>Dodawanie kluczy podpisywania i szyfrowania

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz ikonę **Katalog + Subskrypcja** na pasku narzędzi portalu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W witrynie Azure portal wyszukaj i wybierz **pozycję Azure AD B2C**.
1. Na stronie przegląd w obszarze **Zasady**wybierz pozycję **Identity Experience Framework**.

### <a name="create-the-signing-key"></a>Tworzenie klucza podpisywania

1. Wybierz pozycję **Klawisze zasad,** a następnie wybierz pozycję **Dodaj**.
1. W **Options**przypadku `Generate`opcji wybierz opcję .
1. W **nazwie** `TokenSigningKeyContainer`wprowadź . Prefiks `B2C_1A_` może zostać dodany automatycznie.
1. W przypadku **typu klucz wybierz** **RSA**.
1. Aby uzyskać **opcję Użycie klucza,** wybierz **pozycję Podpis**.
1. Wybierz pozycję **Utwórz**.

### <a name="create-the-encryption-key"></a>Tworzenie klucza szyfrowania

1. Wybierz pozycję **Klawisze zasad,** a następnie wybierz pozycję **Dodaj**.
1. W **Options**przypadku `Generate`opcji wybierz opcję .
1. W **nazwie** `TokenEncryptionKeyContainer`wprowadź . Prefiks `B2C_1A`_ może zostać dodany automatycznie.
1. W przypadku **typu klucz wybierz** **RSA**.
1. Aby uzyskać **użycie klucza,** wybierz **opcję Szyfrowanie**.
1. Wybierz pozycję **Utwórz**.

### <a name="create-the-facebook-key"></a>Tworzenie klucza Facebooka

Dodaj klucz [tajnego aplikacji](identity-provider-facebook.md) Facebooka jako klucz zasad. Można użyć klucza tajnego aplikacji utworzonej jako część wymagań wstępnych tego artykułu.

1. Wybierz pozycję **Klawisze zasad,** a następnie wybierz pozycję **Dodaj**.
1. W **Options**przypadku `Manual`opcji wybierz opcję .
1. W **yjmij nazwę**, wprowadź `FacebookSecret`. Prefiks `B2C_1A_` może zostać dodany automatycznie.
1. W **tajemnicy**wpisz sekret *aplikacji* Facebooka z developers.facebook.com. Ta wartość jest kluczem tajnym, a nie identyfikatorem aplikacji.
1. Aby uzyskać **opcję Użycie klucza,** wybierz **pozycję Podpis**.
1. Wybierz pozycję **Utwórz**.

## <a name="register-identity-experience-framework-applications"></a>Rejestrowanie aplikacji struktury środowiska tożsamości

Usługa Azure AD B2C wymaga zarejestrowania dwóch aplikacji, których używa do rejestrowania się i logowania użytkowników za pomocą kont lokalnych: *IdentityExperienceFramework*, interfejsu API sieci web i *ProxyIdentityExperienceFramework*, natywnej aplikacji z delegowanymi uprawnieniami do aplikacji IdentityExperienceFramework. Użytkownicy mogą zarejestrować się przy użyciu adresu e-mail lub nazwy użytkownika oraz hasła, aby uzyskać dostęp do aplikacji zarejestrowanych przez dzierżawę, które tworzą "konto lokalne". Konta lokalne istnieją tylko w dzierżawie usługi Azure AD B2C.

Musisz zarejestrować te dwie aplikacje w dzierżawie usługi Azure AD B2C tylko raz.

### <a name="register-the-identityexperienceframework-application"></a>Zarejestruj aplikację IdentityExperienceFramework

Aby zarejestrować aplikację w dzierżawie usługi Azure AD B2C, można użyć **środowiska rejestracji aplikacji (Legacy)** lub naszego nowego środowiska rejestracji aplikacji ujednoliconej **(w wersji zapoznawczej).** [Dowiedz się więcej na temat nowego środowiska](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikacje](#tab/applications/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W witrynie Azure portal wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W menu przegląd usługi **Azure Active Directory** w obszarze **Zarządzanie**wybierz pozycję **Rejestracje aplikacji (Starsza wersja)**.
1. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
1. W **yjmij nazwę**, wprowadź `IdentityExperienceFramework`.
1. W przypadku **typu aplikacji**wybierz pozycję **Aplikacja/interfejs API w sieci Web**.
1. W przypadku **adresu URL logowania**wprowadź , `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`gdzie `your-tenant-name` jest twoja nazwa domeny dzierżawy usługi Azure AD B2C. Wszystkie adresy URL powinny teraz używać [b2clogin.com](b2clogin.md).
1. Wybierz pozycję **Utwórz**. Po jego utworzeniu skopiuj identyfikator aplikacji i zapisz go do późniejszego użycia.

#### <a name="app-registrations-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Wybierz **pozycję Rejestracje aplikacji (Wersja zapoznawcza),** a następnie wybierz pozycję **Nowa rejestracja**.
1. W **yjmij nazwę**, wprowadź `IdentityExperienceFramework`.
1. W obszarze **Obsługiwane typy kont**wybierz pozycję **Konta tylko w tym katalogu organizacyjnym**.
1. W obszarze **Przekierowanie identyfikatora URI** `your-tenant-name` wybierz pozycję Sieć **Web**, a następnie wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`miejsce, w którym znajduje się nazwa domeny dzierżawy usługi Azure AD B2C.
1. W obszarze **Uprawnienia**zaznacz pole wyboru *Udzielaj zgody administratora na otwieranie i offline_access uprawnienia.*
1. Wybierz pozycję **Zarejestruj**.
1. Zarejestruj **identyfikator aplikacji (klienta)** do użycia w późniejszym kroku.

Następnie udostępnij interfejs API, dodając zakres:

1. W obszarze **Zarządzanie**wybierz pozycję **Uwidaczniaj interfejs API**.
1. Wybierz **pozycję Dodaj zakres**, a następnie wybierz pozycję Zapisz i **kontynuuj** akceptowanie domyślnego identyfikatora URI identyfikatora aplikacji.
1. Wprowadź następujące wartości, aby utworzyć zakres, który umożliwia wykonanie niestandardowych zasad w dzierżawie usługi Azure AD B2C:
    * **Nazwa zakresu**:`user_impersonation`
    * **Nazwa wyświetlana zgody administratora:**`Access IdentityExperienceFramework`
    * **Opis zgody administratora**:`Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. Wybierz **pozycję Dodaj zakres**

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>Zarejestruj aplikację ProxyIdentityExperienceFramework

#### <a name="applications"></a>[Aplikacje](#tab/applications/)

1. W **obszarze Rejestracje aplikacji (Legacy)** wybierz pozycję **Nowa rejestracja aplikacji**.
1. W **yjmij nazwę**, wprowadź `ProxyIdentityExperienceFramework`.
1. W przypadku **typu aplikacji**wybierz opcję **Natywna**.
1. W polu **Przekieruj identyfikator URI**wprowadź `myapp://auth`.
1. Wybierz pozycję **Utwórz**. Po jego utworzeniu skopiuj identyfikator aplikacji i zapisz go do późniejszego użycia.
1. Wybierz **pozycję Ustawienia**, a następnie wybierz pozycję Wymagane **uprawnienia**, a następnie wybierz pozycję **Dodaj**.
1. Wybierz **pozycję Wybierz interfejs API**, wyszukaj i wybierz **identityExperienceFramework**, a następnie kliknij przycisk **Wybierz**.
1. Zaznacz pole wyboru obok pozycji **Access IdentityExperienceFramework**, kliknij pozycję **Zaznacz**, a następnie kliknij przycisk **Gotowe**.
1. Wybierz **pozycję Udziel uprawnień**, a następnie potwierdź, wybierając pozycję **Tak**.

#### <a name="app-registrations-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Wybierz **pozycję Rejestracje aplikacji (Wersja zapoznawcza),** a następnie wybierz pozycję **Nowa rejestracja**.
1. W **yjmij nazwę**, wprowadź `ProxyIdentityExperienceFramework`.
1. W obszarze **Obsługiwane typy kont**wybierz pozycję **Konta tylko w tym katalogu organizacyjnym**.
1. W obszarze **Przekierowanie identyfikatora URI**użyj listy rozwijanej, aby wybrać **opcję Klient publiczny/natywny (mobilny & pulpit)**.
1. W polu **Przekieruj identyfikator URI**wprowadź `myapp://auth`.
1. W obszarze **Uprawnienia**zaznacz pole wyboru *Udzielaj zgody administratora na otwieranie i offline_access uprawnienia.*
1. Wybierz pozycję **Zarejestruj**.
1. Zarejestruj **identyfikator aplikacji (klienta)** do użycia w późniejszym kroku.

Następnie należy określić, że aplikacja powinna być traktowana jako klient publiczny:

1. W obszarze **Zarządzanie**wybierz pozycję **Uwierzytelnianie**.
1. Wybierz **wypróbuj nowe środowisko** (jeśli jest to pokazane).
1. W obszarze **Ustawienia zaawansowane**włącz **opcję Traktuj aplikację jako klienta publicznego** (wybierz **tak**).
1. Wybierz pozycję **Zapisz**.

Teraz udziel uprawnień do zakresu interfejsu API, który został ujawniony wcześniej w rejestracji *IdentityExperienceFramework:*

1. W obszarze **Zarządzanie**wybierz pozycję **Uprawnienia interfejsu API**.
1. W obszarze **Skonfigurowane uprawnienia**wybierz pozycję **Dodaj uprawnienie**.
1. Wybierz kartę **Moje interfejsy API,** a następnie wybierz aplikację **IdentityExperienceFramework.**
1. W obszarze **Uprawnienie**wybierz **user_impersonation** zakres zdefiniowany wcześniej.
1. Wybierz pozycję **Dodaj uprawnienia**. Zgodnie z zaleceniami, odczekaj kilka minut, zanim przejdziesz do następnego kroku.
1. Wybierz **pozycję Przyznaj zgodę administratora dla (nazwa dzierżawy)**.
1. Wybierz aktualnie zalogowane konto administratora lub zaloguj się za pomocą konta w dzierżawie usługi Azure AD B2C, do których przypisano co najmniej rolę administratora aplikacji w *chmurze.*
1. Wybierz pozycję **Zaakceptuj**.
1. Wybierz **pozycję Odśwież**, a następnie sprawdź, czy "Przyznane dla ..." pojawia się w obszarze **Stan** dla obu zakresów. Może upłynąć kilka minut, aby uprawnienia do propagacji.

* * *

## <a name="custom-policy-starter-pack"></a>Pakiet startowy zasad niestandardowych

Zasady niestandardowe to zestaw plików XML przekazanych do dzierżawy usługi Azure AD B2C w celu zdefiniowania profilów technicznych i podróży użytkowników. Dostarczamy pakiety startowe z kilkoma wstępnie utworzonymi zasadami, które szybko ci się odejdą. Każdy z tych pakietów startowych zawiera najmniejszą liczbę profili technicznych i podróży użytkownika potrzebnych do osiągnięcia opisanych scenariuszy:

- **LocalAccounts** — umożliwia korzystanie tylko z kont lokalnych.
- **SocialAccounts** — umożliwia korzystanie tylko z kont społecznościowych (lub federowanych).
- **SocialAndLocalAccounts** — umożliwia korzystanie z kont lokalnych i społecznościowych.
- **SocialAndLocalAccountsWithMFA** — umożliwia opcje uwierzytelniania społecznościowego, lokalnego i wieloskładnikowego.

Każdy pakiet startowy zawiera:

- **Plik podstawowy** — do bazy wymagana jest kilka modyfikacji. Przykład: *TrustFrameworkBase.xml*
- **Plik rozszerzenia** — ten plik jest, gdzie większość zmian konfiguracji są wprowadzane. Przykład: *TrustFrameworkExtensions.xml*
- **Pliki jednostki uzależniona** — pliki specyficzne dla zadania wywoływane przez aplikację. Przykłady: *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*

W tym artykule edytujesz niestandardowe pliki zasad XML w pakiecie startowym **SocialAndLocalAccounts.** Jeśli potrzebujesz edytora XML, wypróbuj [Visual Studio Code](https://code.visualstudio.com/download), lekki edytor między platformami.

### <a name="get-the-starter-pack"></a>Zdobądź pakiet startowy

Pobierz pakiety startowe zasad niestandardowych z usługi GitHub, a następnie zaktualizuj pliki XML w pakiecie startowym SocialAndLocalAccounts o nazwie dzierżawy usługi Azure AD B2C.

1. [Pobierz plik zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) lub sklonuj repozytorium:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. We wszystkich plikach w katalogu **SocialAndLocalAccounts** zastąp ciąg `yourtenant` nazwą dzierżawy usługi Azure AD B2C.

    Na przykład, jeśli nazwa dzierżawy B2C jest *contosotenant*, wszystkie wystąpienia `yourtenant.onmicrosoft.com` stają się `contosotenant.onmicrosoft.com`.

### <a name="add-application-ids-to-the-custom-policy"></a>Dodawanie identyfikatorów aplikacji do zasad niestandardowych

Dodaj identyfikatory aplikacji do pliku rozszerzenia *TrustFrameworkExtensions.xml*.

1. Otwórz `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** i znajdź `<TechnicalProfile Id="login-NonInteractive">`element .
1. Zastąp `IdentityExperienceFrameworkAppId` oba wystąpienia identyfikatorem aplikacji IdentityExperienceFramework, który został utworzony wcześniej.
1. Zastąp `ProxyIdentityExperienceFrameworkAppId` oba wystąpienia identyfikatorem aplikacji ProxyIdentityExperienceFramework, który został utworzony wcześniej.
1. Zapisz plik.

## <a name="upload-the-policies"></a>Prześlij zasady

1. Wybierz element menu **struktury środowiska tożsamości** w dzierżawie B2C w witrynie Azure portal.
1. Wybierz **pozycję Przekaż zasady niestandardowe**.
1. W tej kolejności prześlij pliki zasad:
    1. *Strona ZaufaniaFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *SignUpOrSignin.xml*
    1. *Plik ProfilEdit.xml*
    1. *Plik PasswordReset.xml*

Podczas przekazywania plików platforma Azure `B2C_1A_` dodaje prefiks do każdego.

> [!TIP]
> Jeśli edytor XML obsługuje sprawdzanie poprawności, `TrustFrameworkPolicy_0.3.0.0.xsd` sprawdź poprawność plików względem schematu XML znajdującego się w katalogu głównym pakietu startowego. Sprawdzanie poprawności schematu XML identyfikuje błędy przed przekazaniem.

## <a name="test-the-custom-policy"></a>Testowanie zasad niestandardowych

1. W **obszarze Zasady niestandardowe**wybierz **pozycję B2C_1A_signup_signin**.
1. W przypadku **wybierz aplikację** na stronie przegląd zasad niestandardowych wybierz wcześniej zarejestrowaną aplikację sieci web o nazwie *webapp1.*
1. Upewnij się, że `https://jwt.ms`adres URL **odpowiedzi** jest .
1. Wybierz **pozycję Uruchom teraz**.
1. Zarejestruj się przy użyciu adresu e-mail.
1. Wybierz **ponownie pozycję Uruchom teraz.**
1. Zaloguj się przy tym samym koncie, aby potwierdzić, że masz prawidłową konfigurację.

## <a name="add-facebook-as-an-identity-provider"></a>Dodawanie Facebooka jako dostawcy tożsamości

Jak wspomniano w [wymaganiach wstępnych,](#prerequisites)Facebook *nie* jest wymagany do korzystania z zasad niestandardowych, ale jest używany tutaj, aby zademonstrować, w jaki sposób można włączyć federacyjne logowanie społecznościowe w zasadach niestandardowych.

1. `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** W pliku zastąp `client_id` wartość identyfikatorem aplikacji Facebooka:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Przekaż *plik TrustFrameworkExtensions.xml* do dzierżawy.
1. W **obszarze Zasady niestandardowe**wybierz **pozycję B2C_1A_signup_signin**.
1. Wybierz **pozycję Uruchom teraz** i wybierz pozycję Facebook, aby zalogować się za pomocą Facebooka i przetestować zasady niestandardowe.

## <a name="next-steps"></a>Następne kroki

Następnie spróbuj dodać usługę Azure Active Directory (Azure AD) jako dostawcę tożsamości. Plik podstawowy używany w tym przewodniku wprowadzenie zawiera już niektóre z zawartości, które są potrzebne do dodawania innych dostawców tożsamości, takich jak usługa Azure AD.

Aby uzyskać informacje dotyczące konfigurowania usługi Azure AD jako dostawcy tożsamości, zobacz [Konfigurowanie rejestracji i logowania się za pomocą konta usługi Azure Active Directory przy użyciu zasad niestandardowych Usługi Active Directory B2C](identity-provider-azure-ad-single-tenant-custom.md).
