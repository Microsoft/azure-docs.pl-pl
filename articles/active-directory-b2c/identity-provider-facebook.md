---
title: Skonfiguruj konto i zaloguj się przy użyciu konta w usłudze Facebook
titleSuffix: Azure AD B2C
description: Zalogować się do klientów przy użyciu kont usługi Facebook w swoich aplikacjach za pomocą Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 7e7a99daa169c994a0b9656786926f0715fa17a2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104580066"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Skonfiguruj konto i zaloguj się za pomocą konta w usłudze Facebook przy użyciu Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-facebook-application"></a>Tworzenie aplikacji w usłudze Facebook

Aby włączyć Logowanie użytkowników przy użyciu konta w serwisie Facebook w Azure Active Directory B2C (Azure AD B2C), musisz utworzyć aplikację na [pulpicie nawigacyjnym aplikacji usługi Facebook](https://developers.facebook.com/). Aby uzyskać więcej informacji, zobacz [Programowanie aplikacji](https://developers.facebook.com/docs/development). Jeśli nie masz jeszcze konta w serwisie Facebook, możesz zarejestrować się w usłudze [https://www.facebook.com/](https://www.facebook.com/) .

1. Zaloguj się do usługi [Facebook dla deweloperów](https://developers.facebook.com/) przy użyciu poświadczeń konta w serwisie Facebook.
1. Jeśli jeszcze tego nie zrobiono, należy zarejestrować się jako deweloper w serwisie Facebook. W tym celu wybierz pozycję **Rozpocznij** w prawym górnym rogu strony, zaakceptuj zasady serwisu Facebook i wykonaj kroki rejestracji.
1. Wybierz pozycję **Moje aplikacje** , a następnie **Utwórz aplikację**.
1. Wybierz opcję **Kompiluj połączone środowiska**.
1. Wprowadź **nazwę wyświetlaną** i prawidłowy **kontaktowy adres e-mail**.
1. Wybierz pozycję **Utwórz identyfikator aplikacji**. Może to wymagać zaakceptowania zasad platformy Facebook i ukończenia kontroli zabezpieczeń w trybie online.
1. Wybierz pozycję **Ustawienia**  >  **podstawowe**.
    1. Wybierz **kategorię**, na przykład `Business and Pages` . Ta wartość jest wymagana przez serwis Facebook, ale nie jest używana do Azure AD B2C.
    1. Wprowadź adres URL dla **adresu URL warunków świadczenia usługi**, na przykład `http://www.contoso.com/tos` . Adres URL zasad to strona, która jest utrzymywana w celu zapewnienia warunków i postanowień aplikacji.
    1. Wprowadź adres URL **zasad zachowania poufności informacji**, na przykład `http://www.contoso.com/privacy` . Adres URL zasad to strona, którą przechowujesz, aby zapewnić informacje o ochronie prywatności dla aplikacji.
1. W dolnej części strony wybierz pozycję **Dodaj platformę**, a następnie wybierz pozycję **Witryna sieci Web**.
1. W polu **adres URL witryny** wprowadź adres witryny sieci Web, na przykład `https://contoso.com` . 
1. Wybierz pozycję **Zapisz zmiany**.
1. W górnej części strony skopiuj wartość **Identyfikator aplikacji**.
1. Wybierz pozycję **Pokaż** i skopiuj wartość **wpisu tajnego aplikacji**. Oba te elementy umożliwiają skonfigurowanie usługi Facebook jako dostawcy tożsamości w dzierżawie. **Wpis tajny aplikacji** jest ważnym poświadczeniem zabezpieczeń.
1. Z menu wybierz znak **Plus** obok pozycji **produkty**. W obszarze **Dodawanie produktów do aplikacji** wybierz pozycję **Konfiguruj** w obszarze **Logowanie do serwisu Facebook**.
1. Z menu wybierz pozycję **Logowanie w serwisie Facebook**, wybierz pozycję **Ustawienia**.
1. W **prawidłowych identyfikatorach URI przekierowania OAuth** wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Jeśli używasz [domeny niestandardowej](custom-domain.md), wprowadź `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Zamień na `your-tenant-name` nazwę dzierżawy i `your-domain-name` domenę niestandardową. 
1. Wybierz pozycję **Zapisz zmiany** w dolnej części strony.
1. Aby udostępnić aplikację w serwisie Facebook Azure AD B2C, wybierz selektor stanu w prawym górnym rogu strony i **Włącz go,** aby udostępnić aplikację jako publiczną, a następnie wybierz pozycję **Przełącz tryb**.  W tym momencie stan powinien ulec zmianie z **opracowywania** na na **żywo**.

::: zone pivot="b2c-user-flow"

## <a name="configure-facebook-as-an-identity-provider"></a>Konfigurowanie usługi Facebook jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **Facebook**.
1. Wprowadź **nazwę**. Na przykład w *serwisie Facebook*.
1. W polu **Identyfikator klienta** wprowadź identyfikator aplikacji w usłudze Facebook, który został utworzony wcześniej.
1. W polu **klucz tajny klienta** Wprowadź zarejestrowany wpis tajny aplikacji.
1. Wybierz pozycję **Zapisz**.

## <a name="add-facebook-identity-provider-to-a-user-flow"></a>Dodawanie dostawcy tożsamości Facebook do przepływu użytkownika 

W tym momencie dostawca tożsamości w serwisie Facebook został skonfigurowany, ale nie jest jeszcze dostępny na żadnej stronie logowania. Aby dodać dostawcę tożsamości Facebook do przepływu użytkownika:

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
1. Kliknij przepływ użytkownika, do którego chcesz dodać dostawcę tożsamości w serwisie Facebook.
1. W obszarze **dostawcy tożsamości społecznościowej** wybierz pozycję **Facebook**.
1. Wybierz pozycję **Zapisz**.
1. Aby przetestować zasady, wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *testapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Wybierz przycisk **Uruchom przepływ użytkownika** .
1. Na stronie rejestracji lub logowania wybierz pozycję **Facebook** , aby zalogować się przy użyciu konta w serwisie Facebook.

Jeśli proces logowania powiedzie się, przeglądarka zostanie przekierowana do `https://jwt.ms` , która wyświetla zawartość tokenu zwróconego przez Azure AD B2C.


::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Musisz przechowywać klucz tajny aplikacji, który został wcześniej zarejestrowany w dzierżawie Azure AD B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog, w którym znajduje się Twoja dzierżawa.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Na stronie Przegląd wybierz pozycję **Struktura środowiska tożsamości**.
5. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
6. W obszarze **Opcje** wybierz opcję `Manual` .
7. Wprowadź **nazwę** klucza zasad. Na przykład `FacebookSecret`. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
8. W **kluczu tajnym** wprowadź wcześniej zarejestrowany klucz tajny aplikacji.
9. W obszarze **użycie klucza** wybierz opcję `Signature` .
10. Kliknij pozycję **Utwórz**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Konfigurowanie konta w serwisie Facebook jako dostawcy tożsamości

1. W `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** pliku Zastąp wartość `client_id` identyfikatorem aplikacji Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

## <a name="upload-and-test-the-policy"></a>Przekazywanie i testowanie zasad

Zaktualizuj plik jednostki uzależnionej (RP), który inicjuje utworzoną przez Ciebie podróż użytkownika.

1. Przekaż plik *TrustFrameworkExtensions.xml* do dzierżawy.
1. W obszarze **zasady niestandardowe** wybierz pozycję **B2C_1A_signup_signin**.
1. W obszarze **Wybierz aplikację** wybierz aplikację sieci Web o nazwie *testapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Wybierz przycisk **Uruchom teraz** .
1. Na stronie rejestracji lub logowania wybierz pozycję **Facebook** , aby zalogować się przy użyciu konta w serwisie Facebook.

Jeśli proces logowania powiedzie się, przeglądarka zostanie przekierowana do `https://jwt.ms` , która wyświetla zawartość tokenu zwróconego przez Azure AD B2C.

::: zone-end

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [przekazać token Facebook do aplikacji](idp-pass-through-user-flow.md).
