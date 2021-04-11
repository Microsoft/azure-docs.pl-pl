---
title: Samouczek — Tworzenie przepływów użytkowników i zasad niestandardowych — Azure Active Directory B2C
description: Postępuj zgodnie z tym samouczkiem, aby dowiedzieć się, jak tworzyć przepływy użytkowników i zasady niestandardowe w Azure Portal, aby włączyć rejestrowanie, logowanie i edytowanie profilu użytkownika dla aplikacji w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e41c1e74dbe428ee38d4480a1587050b7f96a55f
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226230"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Samouczek: Tworzenie przepływów użytkowników w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

W aplikacjach mogą istnieć przepływy użytkowników, które umożliwiają użytkownikom rejestrowanie się, logowanie lub Zarządzanie profilem. Możesz tworzyć wiele przepływów użytkowników różnych typów w dzierżawie Azure Active Directory B2C (Azure AD B2C) i używać ich w aplikacjach zgodnie z potrzebami. Przepływy użytkowników mogą być ponownie używane między aplikacjami.

::: zone pivot="b2c-user-flow"
Przepływ użytkownika umożliwia określenie sposobu, w jaki użytkownicy korzystają z aplikacji, gdy wykonują takie czynności jak logowanie, tworzenie konta, edytowanie profilu lub Resetowanie hasła. W tym artykule omówiono sposób wykonywania następujących zadań:
::: zone-end

::: zone pivot="b2c-custom-policy"
[Zasady niestandardowe](custom-policy-overview.md) to pliki konfiguracji, które definiują zachowanie dzierżawy Azure Active Directory B2C (Azure AD B2C). W tym artykule omówiono sposób wykonywania następujących zadań:
::: zone-end

> [!div class="checklist"]
> * Tworzenie przepływu użytkownika dotyczącego rejestracji i logowania
> * Włączanie samoobsługowego resetowania hasła
> * Tworzenie przepływu użytkownika dotyczącego edytowania profilu

::: zone pivot="b2c-user-flow"
> [!IMPORTANT]
> Zmieniliśmy sposób odwoływania się do wersji przepływów użytkownika. Wcześniej oferowaliśmy wersje V1 (gotowe do zastosowań produkcyjnych) oraz wersje V1.1 i V2 (wersje zapoznawcze). Teraz zostały skonsolidowane przepływy użytkowników w **zalecane** wersje (wersja zapoznawcza nowej generacji) i **standardowe** (ogólnie dostępne). Wszystkie przepływy użytkowników starszej wersji programu V 1.1 i V2 są w ścieżce do wycofania od **1 sierpnia 2021**. Aby uzyskać szczegółowe informacje, zobacz [wersje przepływu użytkowników w Azure AD B2C](user-flow-versions.md).
::: zone-end

## <a name="prerequisites"></a>Wymagania wstępne

::: zone pivot="b2c-user-flow"
- Jeśli jeszcze tego nie zrobiono, [Utwórz dzierżawę Azure AD B2C](tutorial-create-tenant.md) , która jest połączona z subskrypcją platformy Azure.
- [Zarejestruj swoją aplikację](tutorial-register-applications.md) w utworzonej dzierżawie, aby mogła ona komunikować się z Azure AD B2C.
::: zone-end

::: zone pivot="b2c-custom-policy"
- Jeśli jeszcze tego nie zrobiono, [Utwórz dzierżawę Azure AD B2C](tutorial-create-tenant.md) , która jest połączona z subskrypcją platformy Azure.
- [Zarejestruj swoją aplikację](tutorial-register-applications.md) w utworzonej dzierżawie, aby mogła ona komunikować się z Azure AD B2C.
- Wykonaj kroki opisane w temacie [Konfigurowanie rejestracji i logowania przy użyciu konta w serwisie Facebook](identity-provider-facebook.md) , aby skonfigurować aplikację w serwisie Facebook. Mimo że aplikacja Facebook nie jest wymagana do korzystania z zasad niestandardowych, jest używana w tym instruktażu, aby zademonstrować włączenie logowania do sieci społecznościowej w zasadach niestandardowych.
::: zone-end

::: zone pivot="b2c-user-flow"
## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Tworzenie przepływu użytkownika dotyczącego rejestracji i logowania

Przepływ użytkowników rejestracji i logowania obsługuje zarówno środowisko tworzenia konta, jak i logowania przy użyciu jednej konfiguracji. Użytkownicy Twojej aplikacji nie wyłączają właściwej ścieżki w zależności od kontekstu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.

    ![B2C dzierżawy, katalogu i subskrypcji, Azure Portal](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. W obszarze **zasady** wybierz pozycję **przepływy użytkowników**, a następnie wybierz pozycję **Nowy przepływ użytkownika**.

    ![Strona przepływy użytkownika w portalu z wyróżnionym przyciskiem nowy przepływ użytkownika](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Na stronie **Tworzenie przepływu użytkownika** wybierz pozycję **zarejestruj się i zaloguj się** .

    ![Wybierz stronę przepływu użytkownika z wyróżnioną pozycją Zarejestruj się i zaloguj się](./media/tutorial-create-user-flows/select-user-flow-type.png)

1. W obszarze **Wybierz wersję** wybierz pozycję **zalecane**, a następnie wybierz pozycję **Utwórz**. ([Dowiedz się więcej](user-flow-versions.md) o wersjach przepływu użytkowników).

    ![Utwórz stronę przepływu użytkownika w Azure Portal z wyróżnionymi właściwościami](./media/tutorial-create-user-flows/select-version.png)

1. Wprowadź **nazwę** przepływu użytkownika. Na przykład *signupsignin1*.
1. W przypadku **dostawców tożsamości** wybierz pozycję **rejestracja w wiadomości e-mail**.
1. W polu **atrybuty użytkownika i oświadczenia** wybierz oświadczenia i atrybuty, które mają być zbierane i wysyłane przez użytkownika podczas tworzenia konta. Na przykład wybierz pozycję **Pokaż więcej**, a następnie wybierz atrybuty i oświadczenia dla **kraju/regionu**, **Nazwa wyświetlana** i **Kod pocztowy**. Kliknij przycisk **OK**.

    ![Strona wyboru atrybutów i oświadczeń z trzema wybranymi oświadczeniami](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Kliknij przycisk **Utwórz** , aby dodać przepływ użytkownika. Prefiks *B2C_1* jest automatycznie dołączany do nazwy.

### <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Wybierz utworzony przepływ użytkownika, aby otworzyć jego stronę przeglądu, a następnie wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *webapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Kliknij pozycję **Uruchom przepływ użytkownika**, a następnie wybierz pozycję **Utwórz konto teraz**.

    ![Uruchom stronę przepływu użytkownika w portalu z wyróżnionym przyciskiem Uruchom przepływ użytkownika](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Wprowadź prawidłowy adres e-mail, kliknij pozycję **Wyślij kod weryfikacyjny**, Wprowadź otrzymany kod weryfikacyjny, a następnie wybierz pozycję **Weryfikuj kod**.
1. Wprowadź nowe hasło i Potwierdź hasło.
1. Wybierz swój kraj i region, wprowadź nazwę, która ma zostać wyświetlona, wprowadź kod pocztowy, a następnie kliknij przycisk **Utwórz**. Token jest zwracany do `https://jwt.ms` i powinien być wyświetlony.
1. Teraz możesz ponownie uruchomić przepływ użytkownika i móc zalogować się przy użyciu utworzonego konta. Zwrócony token obejmuje oświadczenia wybrane w kraju/regionie, nazwie i kodzie pocztowym.

> [!NOTE]
> Środowisko "Uruchamianie przepływu użytkownika" nie jest obecnie zgodne z typem adresu URL odpowiedzi SPA przy użyciu przepływu kodu autoryzacji. Aby użyć środowiska "Uruchamianie przepływu użytkownika" z tego rodzaju aplikacjami, należy zarejestrować adres URL odpowiedzi typu "Web" i włączyć przepływ niejawny zgodnie z opisem w [tym miejscu](tutorial-register-spa.md).

## <a name="enable-self-service-password-reset"></a>Włączanie samoobsługowego resetowania hasła

Aby włączyć funkcję [samoobsługowego resetowania hasła](add-password-reset-policy.md) dla przepływu użytkownika dotyczącego rejestracji lub logowania:

1. Wybierz utworzony przepływ użytkownika rejestracji lub logowania.
1. W obszarze **Ustawienia** w menu po lewej stronie wybierz pozycję **Właściwości**.
1. W obszarze **złożoność hasła** wybierz pozycję **Samoobsługowe resetowanie hasła**.
1. Wybierz pozycję **Zapisz**.

### <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Wybierz utworzony przepływ użytkownika, aby otworzyć jego stronę przeglądu, a następnie wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *webapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Wybierz pozycję **Uruchom przepływ użytkownika**.
1. Na stronie rejestracji lub logowania wybierz pozycję nie **pamiętasz hasła?**.
1. Sprawdź, czy adres e-mail konta, które zostało utworzone wcześniej, a następnie wybierz pozycję **Kontynuuj**.
1. Masz teraz możliwość zmiany hasła dla użytkownika. Zmień hasło i wybierz pozycję **Kontynuuj**. Token jest zwracany do `https://jwt.ms` i powinien być wyświetlony.

## <a name="create-a-profile-editing-user-flow"></a>Tworzenie przepływu użytkownika dotyczącego edytowania profilu

Jeśli chcesz umożliwić użytkownikom edytowanie ich profilu w aplikacji, Użyj profilu użytkownika edytującego profil.

1. W menu strony Przegląd dzierżawy Azure AD B2C wybierz pozycję **przepływy użytkowników**, a następnie wybierz pozycję **Nowy przepływ użytkownika**.
1. Na stronie **Tworzenie przepływu użytkownika** wybierz **profil edytowanie profilu** użytkownika. 
1. W obszarze **Wybierz wersję** wybierz pozycję **zalecane**, a następnie wybierz pozycję **Utwórz**.
1. Wprowadź **nazwę** przepływu użytkownika. Na przykład *profileediting1*.
1. W przypadku **dostawców tożsamości** wybierz opcję **Logowanie do konta lokalnego**.
2. W polu **atrybuty użytkownika** wybierz atrybuty, które mają być edytowane przez klienta w swoim profilu. Na przykład wybierz pozycję **Pokaż więcej**, a następnie wybierz zarówno atrybuty, **jak i oświadczenia** dotyczące **nazwy wyświetlanej** i stanowiska. Kliknij przycisk **OK**.
3. Kliknij przycisk **Utwórz** , aby dodać przepływ użytkownika. Prefiks *B2C_1* jest automatycznie dołączany do nazwy.

### <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Wybierz utworzony przepływ użytkownika, aby otworzyć jego stronę przeglądu, a następnie wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *webapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Kliknij pozycję **Uruchom przepływ użytkownika**, a następnie zaloguj się przy użyciu utworzonego wcześniej konta.
1. Teraz można zmienić nazwę wyświetlaną i stanowisko użytkownika. Kliknij przycisk **Kontynuuj**. Token jest zwracany do `https://jwt.ms` i powinien być wyświetlony.
::: zone-end

::: zone pivot="b2c-custom-policy"
> [!TIP]
> W tym artykule wyjaśniono, jak ręcznie skonfigurować dzierżawę. Możesz zautomatyzować cały proces z tego artykułu. Automatyzacja spowoduje wdrożenie pakietu Azure AD B2C [SocialAndLocalAccountsWithMFA Starter](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack), który zapewni rejestrację i logowanie, resetowanie haseł i przeszukiwanie profilów. Aby zautomatyzować poniższe przewodnik, odwiedź [aplikację instalatora IEF](https://aka.ms/iefsetup) i postępuj zgodnie z instrukcjami.


## <a name="add-signing-and-encryption-keys"></a>Dodaj klucze podpisywania i szyfrowania

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Na stronie Przegląd w obszarze **zasady** wybierz pozycję **platforma obsługi tożsamości**.

### <a name="create-the-signing-key"></a>Utwórz klucz podpisywania

1. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
1. W obszarze **Opcje** wybierz opcję `Generate` .
1. W polu **Nazwa** wprowadź `TokenSigningKeyContainer` . Prefiks `B2C_1A_` może zostać dodany automatycznie.
1. W obszarze **Typ klucza** wybierz pozycję **RSA**.
1. W obszarze **użycie klucza** wybierz pozycję **podpis**.
1. Wybierz przycisk **Utwórz**.

### <a name="create-the-encryption-key"></a>Utwórz klucz szyfrowania

1. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
1. W obszarze **Opcje** wybierz opcję `Generate` .
1. W polu **Nazwa** wprowadź `TokenEncryptionKeyContainer` . Prefiks `B2C_1A` _ może zostać dodany automatycznie.
1. W obszarze **Typ klucza** wybierz pozycję **RSA**.
1. W obszarze **użycie klucza** wybierz pozycję **szyfrowanie**.
1. Wybierz przycisk **Utwórz**.

### <a name="create-the-facebook-key"></a>Tworzenie klucza Facebook

Dodaj [wpis tajny](identity-provider-facebook.md) aplikacji usługi Facebook jako klucz zasad. Możesz użyć klucza tajnego aplikacji utworzonej w ramach wymagań wstępnych tego artykułu.

1. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
1. W obszarze **Opcje** wybierz opcję `Manual` .
1. W obszarze **Nazwa** wprowadź `FacebookSecret` . Prefiks `B2C_1A_` może zostać dodany automatycznie.
1. W **kluczu tajnym** wprowadź *klucz tajny* aplikacji w serwisie Facebook z Developers.Facebook.com. Ta wartość jest kluczem tajnym, a nie IDENTYFIKATORem aplikacji.
1. W obszarze **użycie klucza** wybierz pozycję **podpis**.
1. Wybierz przycisk **Utwórz**.

## <a name="register-identity-experience-framework-applications"></a>Rejestrowanie aplikacji platformy obsługi tożsamości

Azure AD B2C wymaga zarejestrowania dwóch aplikacji używanych do rejestracji i logowania użytkowników przy użyciu kont lokalnych: *IdentityExperienceFramework*, internetowego interfejsu API i *ProxyIdentityExperienceFramework*, natywnej aplikacji z delegowanym uprawnieniem do aplikacji IdentityExperienceFramework. Użytkownicy mogą zarejestrować się przy użyciu adresu e-mail lub nazwy użytkownika i hasła w celu uzyskania dostępu do aplikacji zarejestrowanych w ramach dzierżawy, co powoduje utworzenie "konta lokalnego". Konta lokalne istnieją tylko w dzierżawie Azure AD B2C.

Musisz zarejestrować te dwie aplikacje w dzierżawie Azure AD B2C tylko raz.

### <a name="register-the-identityexperienceframework-application"></a>Rejestrowanie aplikacji IdentityExperienceFramework

Aby zarejestrować aplikację w dzierżawie Azure AD B2C, możesz użyć **rejestracje aplikacji** środowiska.

1. Wybierz pozycję **rejestracje aplikacji**, a następnie wybierz pozycję **Nowa rejestracja**.
1. W obszarze **Nazwa** wprowadź `IdentityExperienceFramework` .
1. W obszarze **obsługiwane typy kont** wybierz opcję **konta tylko w tym katalogu organizacji**.
1. W obszarze **Identyfikator URI przekierowania** wybierz pozycję **Sieć Web**, a następnie wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com` `your-tenant-name` nazwę domeny dzierżawy Azure AD B2C.
1. W obszarze **uprawnienia** zaznacz pole wyboru *Udziel zgody na uprawnienia administratora do OpenID Connect i offline_access* .
1. Wybierz pozycję **Zarejestruj**.
1. Zapisz **Identyfikator aplikacji (klienta)** do użycia w późniejszym kroku.

Następnie udostępnienie interfejsu API przez dodanie zakresu:

1. W menu po lewej stronie w obszarze **Zarządzaj** wybierz opcję **Uwidocznij interfejs API**.
1. Wybierz pozycję **Dodaj zakres**, a następnie wybierz pozycję **Zapisz i Kontynuuj** , aby zaakceptować domyślny identyfikator URI aplikacji.
1. Wprowadź następujące wartości, aby utworzyć zakres, który umożliwia wykonywanie zasad niestandardowych w dzierżawie Azure AD B2C:
    * **Nazwa zakresu**: `user_impersonation`
    * **Nazwa wyświetlana zgody administratora**: `Access IdentityExperienceFramework`
    * **Opis zgody administratora**: `Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. Wybierz pozycję **Dodaj zakres**

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>Rejestrowanie aplikacji ProxyIdentityExperienceFramework

1. Wybierz pozycję **rejestracje aplikacji**, a następnie wybierz pozycję **Nowa rejestracja**.
1. W obszarze **Nazwa** wprowadź `ProxyIdentityExperienceFramework` .
1. W obszarze **obsługiwane typy kont** wybierz opcję **konta tylko w tym katalogu organizacji**.
1. W obszarze **Identyfikator URI przekierowania** Użyj listy rozwijanej, aby wybrać opcję **Klient publiczny/natywny (Mobile & Desktop)**.
1. Dla **identyfikatora URI przekierowania** wprowadź `myapp://auth` .
1. W obszarze **uprawnienia** zaznacz pole wyboru *Udziel zgody na uprawnienia administratora do OpenID Connect i offline_access* .
1. Wybierz pozycję **Zarejestruj**.
1. Zapisz **Identyfikator aplikacji (klienta)** do użycia w późniejszym kroku.

Następnie określ, że aplikacja powinna być traktowana jako klient publiczny:

1. W menu po lewej stronie w obszarze **Zarządzaj** wybierz pozycję **uwierzytelnianie**.
1. W obszarze **Ustawienia zaawansowane** w sekcji **Zezwalaj na publiczne przepływy klientów** ustaw opcję **Włącz następujące przepływy dla urządzeń przenośnych i komputerów stacjonarnych** na **wartość tak**. Upewnij się, że **wartość "allowPublicClient": true** jest ustawiona w manifeście aplikacji. 
1. Wybierz pozycję **Zapisz**.

Teraz Udziel uprawnień do zakresu interfejsu API, który został uwidoczniony we wcześniejszej części rejestracji *IdentityExperienceFramework* :

1. W menu po lewej stronie w obszarze **Zarządzaj** wybierz pozycję **uprawnienia interfejsu API**.
1. W obszarze **skonfigurowane uprawnienia** wybierz pozycję **Dodaj uprawnienie**.
1. Wybierz kartę **Moje interfejsy API** , a następnie wybierz aplikację **IdentityExperienceFramework** .
1. W obszarze **uprawnienie** wybierz wcześniej zdefiniowany zakres **user_impersonation** .
1. Wybierz pozycję **Dodaj uprawnienia**. Poczekaj kilka minut, zanim przejdziesz do kolejnego kroku.
1. Wybierz pozycję **Udziel zgody administratora (nazwa dzierżawy)**.
1. Wybierz obecnie zalogowane konto administratora lub Zaloguj się przy użyciu konta w dzierżawie Azure AD B2C, do którego przypisano co najmniej rolę *administratora aplikacji w chmurze* .
1. Wybierz pozycję **Zaakceptuj**.
1. Wybierz pozycję **Odśwież**, a następnie sprawdź, czy "udzielono dla..." jest wyświetlany w obszarze **stan** dla zakresów-offline_access, openid connect i user_impersonation. Propagowanie uprawnień może potrwać kilka minut.

* * *

## <a name="custom-policy-starter-pack"></a>Pakiet startowy zasad niestandardowych

Zasady niestandardowe są zestawem plików XML przekazywanym do dzierżawy Azure AD B2C, aby zdefiniować profile techniczne i przedziały użytkowników. Udostępniamy pakiety początkowe z kilkoma wstępnie utworzonymi zasadami, aby szybko rozpocząć pracę. Każdy z tych pakietów początkowych zawiera najmniejszą liczbę profilów technicznych i podróży użytkowników, które są konieczne do osiągnięcia opisanych scenariuszy:

- **LocalAccounts** — umożliwia korzystanie tylko z kont lokalnych.
- **SocialAccounts** — umożliwia korzystanie tylko z kont społecznościowych (lub federacyjnych).
- **SocialAndLocalAccounts** — umożliwia korzystanie z kont lokalnych i społecznościowych.
- **SocialAndLocalAccountsWithMFA** — umożliwia korzystanie z opcji uwierzytelniania społecznościowego, lokalnego i wieloskładnikowego.

Każdy początkowy pakiet zawiera:

- **Plik podstawowy** — do podstawy wymagane są kilka modyfikacji. Przykład: *TrustFrameworkBase.xml*
- **Plik rozszerzenia** — ten plik jest miejscem, w którym wprowadzane są większość zmian konfiguracji. Przykład: *TrustFrameworkExtensions.xml*
- **Pliki jednostek uzależnionych** — pliki specyficzne dla zadania wywoływane przez aplikację. Przykłady: *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*

W tym artykule opisano edytowanie niestandardowych plików zasad XML w pakiecie **SocialAndLocalAccounts** Starter. Jeśli potrzebujesz edytora XML, wypróbuj [Visual Studio Code](https://code.visualstudio.com/download), lekki Edytor Międzyplatformowy.

### <a name="get-the-starter-pack"></a>Pobierz pakiet startowy

Pobierz pakiety początkowe dla zasad niestandardowych z usługi GitHub, a następnie zaktualizuj pliki XML w pakiecie SocialAndLocalAccounts Starter przy użyciu nazwy dzierżawy Azure AD B2C.

1. [Pobierz plik zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) lub Sklonuj repozytorium:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. We wszystkich plikach w katalogu **SocialAndLocalAccounts** Zastąp ciąg `yourtenant` nazwą dzierżawy Azure AD B2C.

    Na przykład jeśli nazwa dzierżawy usługi B2C jest *contosotenant*, wszystkie wystąpienia `yourtenant.onmicrosoft.com` stają się dostępne `contosotenant.onmicrosoft.com` .

### <a name="add-application-ids-to-the-custom-policy"></a>Dodawanie identyfikatorów aplikacji do zasad niestandardowych

Dodaj identyfikatory aplikacji do pliku rozszerzeń *TrustFrameworkExtensions.xml*.

1. Otwórz `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** i Znajdź element `<TechnicalProfile Id="login-NonInteractive">` .
1. Zastąp oba wystąpienia z `IdentityExperienceFrameworkAppId` identyfikatorem aplikacji utworzonej wcześniej aplikacji IdentityExperienceFramework.
1. Zastąp oba wystąpienia z `ProxyIdentityExperienceFrameworkAppId` identyfikatorem aplikacji utworzonej wcześniej aplikacji ProxyIdentityExperienceFramework.
1. Zapisz plik.

## <a name="upload-the-policies"></a>Przekazywanie zasad

1. Wybierz element menu **Struktura środowiska tożsamości** w dzierżawie B2C w Azure Portal.
1. Wybierz pozycję **Przekaż zasady niestandardowe**.
1. W tej kolejności należy przekazać pliki zasad:
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *SignUpOrSignin.xml*
    1. *ProfileEdit.xml*
    1. *PasswordReset.xml*

Podczas przekazywania plików platforma Azure dodaje prefiks `B2C_1A_` do każdego z nich.

> [!TIP]
> Jeśli Edytor XML obsługuje walidację, sprawdź poprawność plików względem `TrustFrameworkPolicy_0.3.0.0.xsd` schematu XML, który znajduje się w katalogu głównym pakietu początkowego. Walidacja schematu XML identyfikuje błędy przed przekazaniem.

## <a name="test-the-custom-policy"></a>Testowanie zasad niestandardowych

1. W obszarze **zasady niestandardowe** wybierz pozycję **B2C_1A_signup_signin**.
1. Dla **opcji wybierz aplikację** na stronie Przegląd zasad niestandardowych wybierz aplikację sieci Web o nazwie *webapp1* , która została wcześniej zarejestrowana.
1. Upewnij się, że **adres URL odpowiedzi** to `https://jwt.ms` .
1. Wybierz pozycję **Uruchom teraz**.
1. Utwórz konto przy użyciu adresu e-mail.
1. Ponownie wybierz pozycję **Uruchom teraz** .
1. Zaloguj się przy użyciu tego samego konta, aby potwierdzić, że konfiguracja jest poprawna.

## <a name="add-facebook-as-an-identity-provider"></a>Dodawanie usługi Facebook jako dostawcy tożsamości

Jak wspomniano w [wymaganiach wstępnych](#prerequisites), serwis Facebook *nie* jest wymagany do korzystania z zasad niestandardowych, ale jest używany w tym miejscu do zademonstrowania, jak można włączyć federacyjne logowanie społeczne w zasadach niestandardowych.

1. W `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** pliku Zastąp wartość `client_id` identyfikatorem aplikacji Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Przekaż plik *TrustFrameworkExtensions.xml* do dzierżawy.
1. W obszarze **zasady niestandardowe** wybierz pozycję **B2C_1A_signup_signin**.
1. Wybierz pozycję **Uruchom teraz** i wybierz pozycję Facebook, aby zalogować się za pomocą usługi Facebook i przetestować zasady niestandardowe.

::: zone-end
## <a name="next-steps"></a>Następne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie przepływu użytkownika dotyczącego rejestracji i logowania
> * Tworzenie przepływu użytkownika dotyczącego edytowania profilu
> * Tworzenie przepływu użytkownika dotyczącego resetowania haseł

Następnie Dowiedz się, jak używać Azure AD B2C do logowania się i rejestrowania użytkowników w aplikacji. Postępuj zgodnie z poniższą aplikacją sieci Web ASP.NET lub przejdź do innej aplikacji w spisie treści w obszarze **uwierzytelnianie użytkowników**.

> [!div class="nextstepaction"]
> [Samouczek: Włączanie uwierzytelniania w aplikacji sieci Web przy użyciu Azure AD B2C >](tutorial-web-app-dotnet.md)
