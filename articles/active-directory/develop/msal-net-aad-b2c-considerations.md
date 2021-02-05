---
title: Azure AD B2C i MSAL.NET
titleSuffix: Microsoft identity platform
description: Zagadnienia dotyczące korzystania z Azure AD B2C z biblioteką uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2020
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: b28454e9b60654541d4f62ec1d8455b30cfc2906
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99580831"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Korzystanie z MSAL.NET do logowania użytkowników przy użyciu tożsamości społecznościowych

Możesz użyć MSAL.NET, aby zalogować użytkowników z tożsamościami społecznościowymi przy użyciu [Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/overview.md). Azure AD B2C jest zbudowany wokół koncepcji zasad. W MSAL.NET, określenie zasad tłumaczy się w celu świadczenia urzędu.

- Podczas tworzenia wystąpienia publicznej aplikacji klienta należy określić zasady jako część urzędu certyfikacji.
- Jeśli chcesz zastosować zasady, wywołaj przesłonięcie `AcquireTokenInteractive` , który akceptuje `authority` parametr.

Ten artykuł ma zastosowanie do MSAL.NET 3. x. W przypadku MSAL.NET 2. x zapoznaj się z tematami dotyczącymi [Azure AD B2C w MSAL 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x) w witrynie GitHub na platformie MSAL.NET.

## <a name="authority-for-an-azure-ad-b2c-tenant-and-policy"></a>Urząd Azure AD B2C dzierżawy i zasad

Format urzędu dla Azure AD B2C jest: `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}`

- `azureADB2CHostname` -Nazwa dzierżawy Azure AD B2C i hosta. Na przykład *contosob2c.b2clogin.com*.
- `tenant` — Nazwa domeny lub identyfikator katalogu dzierżawcy Azure AD B2C. Na przykład odpowiednio *contosob2c.onmicrosoft.com* lub GUID.
- `policyName` -Nazwa przepływu użytkownika lub zasad niestandardowych, które mają zostać zastosowane. Na przykład zasady rejestracji/logowania, takie jak *b2c_1_susi*.

Aby uzyskać więcej informacji na temat Azure AD B2C urzędów, zobacz [Ustawianie adresów URL przekierowania na b2clogin.com](../../active-directory-b2c/b2clogin.md).

## <a name="instantiating-the-application"></a>Tworzenie wystąpienia aplikacji

Podaj urząd wywołujący `WithB2CAuthority()` podczas tworzenia obiektu aplikacji:

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string AzureADB2CHostname = "fabrikamb2c.b2clogin.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://{AzureADB2CHostname}/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>Uzyskiwanie tokenu w celu zastosowania zasad

Uzyskanie tokenu dla Azure AD B2C chronionego interfejsu API w publicznej aplikacji klienckiej wymaga użycia zastąpień z urzędem:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application.AcquireTokenInteractive(scopes)
                                           .WithAccount(GetAccountByPolicy(accounts, policy))
                                           .WithParentActivityOrWindow(ParentActivityOrWindow)
                                           .ExecuteAsync();
```

W poprzednim fragmencie kodu:

- `policy` jest ciągiem zawierającym nazwę przepływu użytkownika Azure AD B2C lub zasad niestandardowych (na przykład `PolicySignUpSignIn` ).
- `ParentActivityOrWindow` jest wymagana w przypadku systemu Android (działanie) i jest opcjonalne dla innych platform, które obsługują nadrzędny interfejs użytkownika, taki jak Windows w systemach Microsoft Windows i UIViewController w systemie iOS. Aby uzyskać więcej informacji o interfejsie użytkownika, zobacz [WithParentActivityOrWindow](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow) The MSAL wiki.
- `GetAccountByPolicy(IEnumerable<IAccount>, string)` to metoda, która odnajduje konto dla danej zasady. Na przykład:

  ```csharp
  private IAccount GetAccountByPolicy(IEnumerable<IAccount> accounts, string policy)
  {
      foreach (var account in accounts)
      {
          string userIdentifier = account.HomeAccountId.ObjectId.Split('.')[0];
          if (userIdentifier.EndsWith(policy.ToLower()))
              return account;
      }
      return null;
  }
  ```

Zastosowanie przepływu użytkownika lub zasad niestandardowych (na przykład umożliwienie użytkownikowi edytowania profilu lub resetowania hasła) jest obecnie wykonywane przez wywołanie metody `AcquireTokenInteractive` . W przypadku tych dwóch zasad nie jest używany zwrócony wynik tokenu/uwierzytelniania.

## <a name="profile-edit-policies"></a>Zasady edytowania profilu

Aby umożliwić użytkownikom logowanie się przy użyciu tożsamości społecznościowej, a następnie edytowanie ich profilu, Zastosuj Azure AD B2C edytowanie zasad profilu.

Zrób to, wywołując `AcquireTokenInteractive` z urzędu dla tych zasad. Ponieważ użytkownik jest już zalogowany i ma aktywną sesję plików cookie, użyj, `Prompt.NoPrompt` Aby zapobiec wyświetlaniu okna dialogowego wyboru konta.

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
    IEnumerable<IAccount> accounts = await app.GetAccountsAsync();
    try
    {
        var authResult = await app.AcquireToken(scopes:App.ApiScopes)
                            .WithAccount(GetUserByPolicy(accounts, App.PolicyEditProfile)),
                            .WithPrompt(Prompt.NoPrompt),
                            .WithB2CAuthority(App.AuthorityEditProfile)
                            .ExecuteAsync();
        DisplayBasicTokenInfo(authResult);
    }
    catch
    {
    }
}
```

## <a name="resource-owner-password-credentials-ropc"></a>Poświadczenia hasła właściciela zasobu (ROPC)

Aby uzyskać więcej informacji na temat przepływu ROPC, zobacz [Logowanie przy użyciu hasła właściciela zasobu Przyznaj](v2-oauth-ropc.md).

**Nie zaleca** się przepływu ROPC, ponieważ monitowanie użytkownika o podanie hasła w aplikacji nie jest bezpieczne. Aby uzyskać więcej informacji o tym problemie, zobacz [co to jest rozwiązanie w przypadku rosnącego problemu z hasłami?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/).

Przy użyciu nazwy użytkownika/hasła w przepływie ROPC można naświęcić kilka rzeczy:

- Rdzeń założenia nowoczesnej tożsamości: hasło może być złowione lub odtwarzane, ponieważ wspólny klucz tajny może być przechwytywany. Zgodnie z definicją ROPC jest niezgodny z przepływami bez hasła.
- Użytkownicy, którzy muszą wykonywać uwierzytelnianie wieloskładnikowe, nie będą mogli się zalogować (w przypadku braku interakcji).
- Użytkownicy nie będą mogli korzystać z logowania jednokrotnego (SSO).

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Konfigurowanie przepływu ROPC w Azure AD B2C

W dzierżawie Azure AD B2C Utwórz nowy przepływ użytkownika i wybierz pozycję **Zaloguj się za pomocą ROPC** , aby włączyć ROPC dla przepływu użytkownika. Aby uzyskać więcej informacji, zobacz [Konfigurowanie przepływu poświadczeń hasła właściciela zasobu](../../active-directory-b2c/add-ropc-policy.md).

`IPublicClientApplication` zawiera `AcquireTokenByUsernamePassword` metodę:

```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Ta `AcquireTokenByUsernamePassword` Metoda przyjmuje następujące parametry:

- *Zakresy* , dla których ma zostać uzyskany token dostępu.
- *Nazwa użytkownika*.
- *Hasło* programu SecureString dla użytkownika.

### <a name="limitations-of-the-ropc-flow"></a>Ograniczenia przepływu ROPC

Przepływ ROPC **działa tylko w przypadku kont lokalnych**, w których zaAzure AD B2C rejestrowano użytkowników przy użyciu adresu e-mail lub nazwy użytkownika. Ten przepływ nie działa, gdy federowanie się do zewnętrznego dostawcy tożsamości obsługiwanego przez Azure AD B2C (Facebook, Google itp.).

## <a name="google-auth-and-embedded-webview"></a>Uwierzytelnianie Google i osadzony widok WebView

Jeśli używasz usługi Google jako dostawcy tożsamości, zalecamy użycie przeglądarki systemowej jako Google nie zezwala na [uwierzytelnianie z osadzonych widoków WebView](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Obecnie `login.microsoftonline.com` jest to zaufana organizacja z firmą Google i będzie współdziałać z osadzonym widokiem WebView. `b2clogin.com`Nie jest to jednak zaufany urząd z firmą Google, dlatego użytkownicy nie będą mogli się uwierzytelniać.

Udostępnimy aktualizację tego [problemu](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) , jeśli zmienią się.

## <a name="token-caching-in-msalnet"></a>Buforowanie tokenów w MSAL.NET

### <a name="known-issue-with-azure-ad-b2c"></a>Znany problem z Azure AD B2C

MSAL.NET obsługuje [pamięć podręczną tokenów](/dotnet/api/microsoft.identity.client.tokencache). Klucz buforowania tokenu jest oparty na oświadczeniach zwracanych przez dostawcę tożsamości (dostawcy tożsamości).

Obecnie MSAL.NET potrzebuje dwóch oświadczeń, aby utworzyć klucz pamięci podręcznej tokenu:

- `tid` (identyfikator dzierżawy usługi Azure AD)
- `preferred_username`

W scenariuszach Azure AD B2C mogą brakować obydwu oświadczeń, ponieważ nie wszyscy dostawcy tożsamości społecznościowej (Facebook, Google i innych) zwracają je w tokenach, które zwracają do Azure AD B2C.

Objawem takiego scenariusza jest to, że MSAL.NET zwraca, `Missing from the token response` gdy uzyskujesz dostęp do `preferred_username` wartości w tokenach wystawionych przez Azure AD B2C. MSAL używa `Missing from the token response` wartości dla `preferred_username` , aby zachować zgodność między bibliotekami w pamięci podręcznej.

### <a name="workarounds"></a>Obejścia

#### <a name="mitigation-for-missing-tenant-id"></a>Środki zaradcze dla brakującego identyfikatora dzierżawy

Sugerowane obejście polega na użyciu [pamięci podręcznej opisanej wcześniej przez zasady](#acquire-a-token-to-apply-a-policy) .

Alternatywnie możesz użyć tego żądania, `tid` Jeśli używasz [zasad niestandardowych](../../active-directory-b2c/custom-policy-get-started.md) w Azure AD B2C. Zasady niestandardowe mogą zwracać dodatkowe oświadczenia do aplikacji przy użyciu [transformacji oświadczeń](../../active-directory-b2c/claims-transformation-technical-profile.md).

#### <a name="mitigation-for-missing-from-the-token-response"></a>Środki zaradcze dla braku odpowiedzi tokenu

Jedną z opcji jest użycie `name` roszczeń zamiast `preferred_username` . Aby uwzględnić `name` w tokenach identyfikatorów wystawionych przez Azure AD B2C, wybierz opcję **Nazwa wyświetlana** podczas konfigurowania przepływu użytkownika.

Aby uzyskać więcej informacji na temat określania, które oświadczenia są zwracane przez przepływy użytkownika, zobacz [Samouczek: Tworzenie przepływów użytkowników w Azure AD B2C](../../active-directory-b2c/tutorial-create-user-flows.md).

## <a name="next-steps"></a>Następne kroki

Więcej szczegółów na temat pozyskiwania tokenów interaktywnie za pomocą usługi MSAL.NET dla aplikacji Azure AD B2C przedstawiono w poniższym przykładzie.

| Przykład | Platforma | Opis|
|------ | -------- | -----------|
|[Active-Directory-B2C-Xamarin-Native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, platformy UWP | Aplikacja programu Xamarin Forms korzystająca z usługi MSAL.NET do uwierzytelniania użytkowników za pośrednictwem Azure AD B2C, a następnie uzyskiwania dostępu do internetowego interfejsu API z zwróconymi tokenami.|
