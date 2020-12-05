---
title: 'Samouczek: Dodawanie dostawców tożsamości do aplikacji'
titleSuffix: Azure AD B2C
description: Postępuj zgodnie z tym samouczkiem, aby dowiedzieć się, jak dodać dostawców tożsamości do aplikacji w Azure Active Directory B2C przy użyciu Azure Portal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 166bdb7a2cf15a84e1b826a9a798042c568bb227
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608235"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Samouczek: Dodawanie dostawców tożsamości do aplikacji w Azure Active Directory B2C

W swoich aplikacjach możesz umożliwić użytkownikom logowanie się za pomocą różnych dostawców tożsamości. *Dostawca tożsamości* tworzy, i przechowuje informacje dotyczące tożsamości oraz zarządza nimi, zapewniając jednocześnie aplikacjom usługi uwierzytelniania. Można dodać dostawców tożsamości, które są obsługiwane przez Azure Active Directory B2C (Azure AD B2C) do [przepływów użytkownika](user-flow-overview.md) przy użyciu Azure Portal.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie aplikacji dostawcy tożsamości
> * Dodaj dostawców tożsamości do swojej dzierżawy — zarówno w serwisie Facebook, jak i w Azure Active Directory
> * Dodawanie dostawców tożsamości do przepływu użytkownika

W aplikacjach zwykle używany jest tylko jeden dostawca tożsamości, ale można dodać więcej. W tym samouczku przedstawiono sposób dodawania dostawcy tożsamości usługi Azure AD i dostawcy tożsamości Facebook do aplikacji. Dodawanie obu tych dostawców tożsamości do aplikacji jest opcjonalne. Możesz również dodać innych dostawców tożsamości, takich jak [Amazon](identity-provider-amazon.md), [GitHub](identity-provider-github.md), [Google](identity-provider-google.md), [LinkedIn](identity-provider-linkedin.md), [Microsoft](identity-provider-microsoft-account.md)lub [Twitter](identity-provider-twitter.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz przepływ użytkownika](tutorial-create-user-flows.md), aby umożliwić użytkownikom rejestrowanie się w aplikacji i logowanie do niej.

## <a name="create-applications"></a>Tworzenie aplikacji

Aplikacje dostawcy tożsamości dostarczają identyfikator i klucz, aby umożliwić komunikację z dzierżawą Azure AD B2C. W tej części samouczka utworzysz aplikację usługi Azure AD i aplikację Facebook, z której są uzyskiwane identyfikatory i klucze umożliwiające dodawanie dostawców tożsamości do dzierżawy. Jeśli dodajesz tylko jednego z dostawców tożsamości, wystarczy utworzyć aplikację dla tego dostawcy.

### <a name="create-an-azure-active-directory-application"></a>Tworzenie aplikacji Azure Active Directory

Aby włączyć Logowanie użytkowników z usługi Azure AD, musisz zarejestrować aplikację w dzierżawie usługi Azure AD. Dzierżawa usługi Azure AD nie jest taka sama jak dzierżawa Azure AD B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD, wybierając filtr **katalog + subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawę usługi Azure AD.
1. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję **rejestracje aplikacji**.
1. Wybierz pozycję **Nowa rejestracja**.
1. Wprowadź nazwę aplikacji. Na przykład `Azure AD B2C App`.
1. Zaakceptuj wybór **kont w tym katalogu organizacyjnym tylko** dla tej aplikacji.
1. W polu **Identyfikator URI przekierowania** Zaakceptuj wartość **sieci Web** i wprowadź następujący adres URL w postaci małych liter, zastępując `your-B2C-tenant-name` nazwę dzierżawy Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Na przykład `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Wszystkie adresy URL powinny teraz używać [b2clogin.com](b2clogin.md).

1. Wybierz pozycję **zarejestruj**, a następnie zarejestruj **Identyfikator aplikacji (klienta)** , którego używasz w późniejszym kroku.
1. W obszarze **Zarządzaj** w menu aplikacji wybierz pozycję **Certyfikaty & wpisy tajne**, a następnie wybierz pozycję **nowy klucz tajny klienta**.
1. Wprowadź **Opis** wpisu tajnego klienta. Na przykład `Azure AD B2C App Secret`.
1. Wybierz okres ważności. W przypadku tej aplikacji zaakceptuj wybór **w ciągu 1 roku**.
1. Wybierz pozycję **Dodaj**, a następnie Zapisz wartość nowego klucza tajnego klienta, którego używasz w późniejszym kroku.

### <a name="create-a-facebook-application"></a>Tworzenie aplikacji w usłudze Facebook

Aby użyć konta w serwisie Facebook jako dostawcy tożsamości w Azure AD B2C, musisz utworzyć aplikację w serwisie Facebook. Jeśli nie masz jeszcze konta w serwisie Facebook, możesz je uzyskać pod adresem [https://www.facebook.com/](https://www.facebook.com/) .

1. Zaloguj się do usługi [Facebook dla deweloperów](https://developers.facebook.com/) przy użyciu poświadczeń konta w serwisie Facebook.
1. Jeśli jeszcze tego nie zrobiono, należy zarejestrować się jako deweloper w serwisie Facebook. W tym celu wybierz pozycję **Rozpocznij** w prawym górnym rogu strony, zaakceptuj zasady serwisu Facebook i wykonaj kroki rejestracji.
1. Wybierz pozycję **Moje aplikacje** , a następnie **Utwórz aplikację**.
1. Wprowadź **nazwę wyświetlaną** i prawidłowy **kontaktowy adres e-mail**.
1. Kliknij pozycję **Utwórz identyfikator aplikacji**. Może to wymagać zaakceptowania zasad platformy Facebook i ukończenia kontroli zabezpieczeń w trybie online.
1. Wybierz pozycję **Ustawienia**  >  **podstawowe**.
1. Wybierz **kategorię**, na przykład `Business and Pages` . Ta wartość jest wymagana przez serwis Facebook, ale nie jest używana przez Azure AD B2C.
1. W dolnej części strony wybierz pozycję **Dodaj platformę**, a następnie wybierz pozycję **Witryna sieci Web**.
1. W polu **adres URL witryny** wprowadź `https://your-tenant-name.b2clogin.com/` zamianę na `your-tenant-name` nazwę dzierżawy.
1. Wprowadź adres URL **zasad zachowania poufności informacji**, na przykład `http://www.contoso.com/` . Adres URL zasad zachowania poufności informacji to strona, którą przechowujesz, aby zapewnić informacje o ochronie prywatności dla aplikacji.
1. Wybierz pozycję **Zapisz zmiany**.
1. W górnej części strony Zapisz wartość **identyfikatora aplikacji**.
1. Obok pozycji **klucz tajny aplikacji** wybierz pozycję **Pokaż** i Zapisz jej wartość. W celu skonfigurowania usługi Facebook jako dostawcy tożsamości w dzierżawie należy użyć identyfikatora aplikacji i klucza tajnego aplikacji. **Klucz tajny aplikacji** to ważne poświadczenie zabezpieczeń, które należy przechowywać w bezpiecznym miejscu.
1. Wybierz znak plus obok pozycji **produkty**, a następnie w obszarze **Logowanie do serwisu Facebook** wybierz pozycję **Konfiguruj**.
1. W obszarze **Logowanie do serwisu Facebook** w menu po lewej stronie wybierz pozycję **Ustawienia**.
1. W **prawidłowych identyfikatorach URI przekierowania OAuth** wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Zamień `your-tenant-name` na nazwę dzierżawy. Wybierz pozycję **Zapisz zmiany** w dolnej części strony.
1. Aby udostępnić aplikację Facebook Azure AD B2C, kliknij selektor **stanu** w prawym górnym rogu strony i **Włącz go,** aby udostępnić aplikację, a następnie kliknij przycisk **Potwierdź**. W tym momencie stan powinien ulec zmianie z **opracowywania** na na **żywo**.

## <a name="add-the-identity-providers"></a>Dodawanie dostawców tożsamości

Po utworzeniu aplikacji dla dostawcy tożsamości, który chcesz dodać, należy dodać dostawcę tożsamości do dzierżawy.

### <a name="add-the-azure-active-directory-identity-provider"></a>Dodawanie dostawcy tożsamości Azure Active Directory

1. Upewnij się, że używasz katalogu, który zawiera Azure AD B2C dzierżawcy. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **Nowy dostawca połączenia OpenID Connect**.
1. Wprowadź **nazwę**. Na przykład wprowadź nazwę *contoso Azure AD*.
1. W polu **adres URL metadanych** wprowadź następujący adres URL zastępujący `{tenant}` nazwą domeny dzierżawy usługi Azure AD:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Na przykład `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
    Na przykład `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`.

1. W polu **Identyfikator klienta** wprowadź wcześniej zarejestrowany identyfikator aplikacji.
1. W polu **klucz tajny klienta** wprowadź wcześniej zarejestrowany klucz tajny klienta.
1. Dla **zakresu**, wprowadź `openid profile` .
1. Pozostaw wartości domyślne dla **typu odpowiedzi** i **trybu odpowiedzi**.
1. Obowiązkowe Dla wskazówki dotyczącej **domeny** wprowadź wartość `contoso.com` . Aby uzyskać więcej informacji, zobacz [Konfigurowanie bezpośredniego logowania przy użyciu Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. W obszarze **Mapowanie oświadczeń dostawcy tożsamości** wybierz następujące oświadczenia:

    * **Identyfikator użytkownika**: *OID*
    * **Nazwa wyświetlana**: *Nazwa*
    * **Imię:** *given_name*
    * **Nazwisko**: *family_name*
    * **Adres e-mail**: *unique_name*

1. Wybierz pozycję **Zapisz**.

### <a name="add-the-facebook-identity-provider"></a>Dodawanie dostawcy tożsamości w usłudze Facebook

1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **Facebook**.
1. Wprowadź **nazwę**. Na przykład w *serwisie Facebook*.
1. W polu **Identyfikator klienta** wprowadź identyfikator aplikacji w usłudze Facebook, który został utworzony wcześniej.
1. W polu **klucz tajny klienta** Wprowadź zarejestrowany wpis tajny aplikacji.
1. Wybierz pozycję **Zapisz**.

## <a name="update-the-user-flow"></a>Aktualizowanie przepływu użytkownika

W samouczku, który został ukończony w ramach wymagań wstępnych, został utworzony przepływ użytkownika na potrzeby rejestracji i logowania o nazwie *B2C_1_signupsignin1*. W tej sekcji dodasz dostawców tożsamości do przepływu użytkownika *B2C_1_signupsignin1* .

1. Wybierz pozycję **przepływy użytkownika**, a następnie wybierz pozycję *B2C_1_signupsignin1* przepływ użytkownika.
2. Wybierz pozycję **dostawcy tożsamości**, wybierz dostawców tożsamości w usłudze **Facebook** i **contoso usługi Azure AD** .
3. Wybierz pozycję **Zapisz**.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Na stronie Przegląd utworzonego przepływu użytkownika wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *webapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Wybierz pozycję **Uruchom przepływ użytkownika**, a następnie zaloguj się przy użyciu wcześniej dodanego dostawcy tożsamości.
1. Powtórz kroki od 1 do 3 dla innych dostawców tożsamości, które zostały dodane.

Jeśli operacja logowania zakończy się pomyślnie, nastąpi przekierowanie do, `https://jwt.ms` który wyświetla zdekodowany token, podobny do:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "<key-ID>"
}.{
  "exp": 1562346892,
  "nbf": 1562343292,
  "ver": "1.0",
  "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
  "sub": "20000000-0000-0000-0000-000000000000",
  "aud": "30000000-0000-0000-0000-000000000000",
  "nonce": "defaultNonce",
  "iat": 1562343292,
  "auth_time": 1562343292,
  "name": "User Name",
  "idp": "facebook.com",
  "postalCode": "12345",
  "tfp": "B2C_1_signupsignin1"
}.[Signature]
```

## <a name="next-steps"></a>Następne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji dostawcy tożsamości
> * Dodawanie dostawców tożsamości do dzierżawy
> * Dodawanie dostawców tożsamości do przepływu użytkownika

Następnie Dowiedz się, jak dostosować interfejs użytkownika stron widocznych dla użytkowników w ramach obsługi tożsamości w aplikacjach:

> [!div class="nextstepaction"]
> [Dostosuj interfejs użytkownika aplikacji w Azure Active Directory B2C](tutorial-customize-ui.md)
