---
title: Azure AD B2C (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Informacje o określonych kwestiach dotyczących używania Azure AD B2C z biblioteką uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2019
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: d31cf3a4e024dc59b865d096cbd0829d50f61a1a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81533959"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Korzystanie z MSAL.NET do logowania użytkowników przy użyciu tożsamości społecznościowych

Możesz użyć MSAL.NET, aby zalogować użytkowników z tożsamościami społecznościowymi przy użyciu [Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c). Azure AD B2C jest zbudowany wokół koncepcji zasad. W MSAL.NET, określenie zasad tłumaczy się w celu świadczenia urzędu.

- Podczas tworzenia wystąpienia publicznej aplikacji klienckiej należy określić zasady w obszarze urząd.
- Jeśli chcesz zastosować zasady, musisz wywołać przesłonięcie `AcquireTokenInteractive` zawierający `authority` parametr.

Ta strona dotyczy MSAL 3. x. Jeśli interesuje Cię MSAL 2. x, zobacz [szczegóły Azure AD B2C w MSAL 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Urząd Azure AD B2C dzierżawy i zasady

Urzędem do użycia jest `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}` :

- `azureADB2CHostname`jest nazwą dzierżawy Azure AD B2C i hostem (na przykład `{your-tenant-name}.b2clogin.com`)
- `tenant`to pełna nazwa dzierżawy Azure AD B2C (na przykład `{your-tenant-name}.onmicrosoft.com`) lub identyfikator GUID dzierżawy,
- `policyName`Nazwa zasad lub przepływu użytkownika do zastosowania (na przykład "b2c_1_susi" do rejestracji/logowania).

Więcej informacji na temat Azure AD B2C urzędów można znaleźć w tej [dokumentacji](/azure/active-directory-b2c/b2clogin).

## <a name="instantiating-the-application"></a>Tworzenie wystąpienia aplikacji

Podczas kompilowania aplikacji należy zapewnić urząd.

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
AuthenticationResult ar = await application .AcquireTokenInteractive(scopes)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .WithParentActivityOrWindow(ParentActivityOrWindow)
                                            .ExecuteAsync();
```

tym:

- `policy`jest to jeden z poprzednich ciągów (na przykład `PolicySignUpSignIn`).
- `ParentActivityOrWindow`jest wymagany w przypadku systemu Android (działanie) i opcjonalnie dla innych platform, które obsługują nadrzędny interfejs użytkownika, na przykład Windows w systemie Windows i UIViewController na platformie iOS. Więcej informacji można znaleźć [w tym miejscu w oknie dialogowym interfejsu użytkownika](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow).
- `GetAccountByPolicy(IEnumerable<IAccount>, string)`to metoda, która odnajduje konto dla danej zasady. Przykład:

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

Zastosowanie zasad lub przepływu użytkownika (na przykład umożliwienie użytkownikowi końcowemu edytowania profilu lub resetowania hasła) jest obecnie wykonywane przez wywołanie `AcquireTokenInteractive`. W przypadku tych dwóch zasad nie jest używany zwrócony wynik tokenu/uwierzytelniania.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Specjalny przypadek zasad EditProfile i ResetPassword

Jeśli chcesz zapewnić użytkownikom końcowym logowanie się przy użyciu tożsamości społecznościowej, a następnie edytować swój profil, chcesz zastosować Azure AD B2C edytowanie zasad profilu. Aby to zrobić, należy wywołać `AcquireTokenInteractive` odpowiednie uprawnienia dla tych zasad, a następnie ustawić `Prompt.NoPrompt` monit o wyłączenie wyświetlania okna dialogowego wyboru konta (w przypadku, gdy użytkownik jest już zalogowany i ma aktywną sesję plików cookie).

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
  . . .
 }
}
```
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Poświadczenia hasła właściciela zasobu (ROPC) z Azure AD B2C
Więcej szczegółów dotyczących przepływu ROPC można znaleźć w tej [dokumentacji](v2-oauth-ropc.md).

Ten przepływ **nie jest zalecany** , ponieważ aplikacja, do której należy użytkownik, nie jest zabezpieczona. Aby uzyskać więcej informacji o tym problemie, zobacz [ten artykuł](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/).

Przy użyciu nazwy użytkownika i hasła podajesz wiele rzeczy:
- Rdzeń założenia nowoczesnej tożsamości: hasło zostanie złowione, powtórzone. Ponieważ mamy koncepcji dotyczącej tajnego udziału, który można przechwycić. Jest to niezgodne z hasłem.
- Użytkownicy, którzy muszą wykonywać uwierzytelnianie wieloskładnikowe, nie będą mogli się zalogować (w przypadku braku interakcji).
- Użytkownicy nie będą mogli korzystać z logowania jednokrotnego.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Konfigurowanie przepływu ROPC w Azure AD B2C
W dzierżawie Azure AD B2C Utwórz nowy przepływ użytkownika i wybierz pozycję **Zaloguj się za pomocą ROPC**. Spowoduje to włączenie zasad ROPCymi dla dzierżawy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie przepływu poświadczeń hasła właściciela zasobu](/azure/active-directory-b2c/configure-ropc) .

`IPublicClientApplication`zawiera metodę:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Ta metoda przyjmuje jako parametry:
- *Zakresy* do żądania tokenu dostępu dla.
- *Nazwa użytkownika*.
- *Hasło* programu SecureString dla użytkownika.

Należy pamiętać, aby użyć urzędu, który zawiera zasady ROPC.

### <a name="limitations-of-the-ropc-flow"></a>Ograniczenia przepływu ROPC
 - Przepływ ROPC **działa tylko dla kont lokalnych** (gdzie rejestrujesz się w Azure AD B2C przy użyciu adresu e-mail lub nazwy użytkownika). Ten przepływ nie działa, jeśli federowanie do któregokolwiek z dostawców tożsamości obsługiwanych przez Azure AD B2C (Facebook, Google itp.).

## <a name="google-auth-and-embedded-webview"></a>Uwierzytelnianie Google i osadzony widok WebView

Jeśli jesteś deweloperem Azure AD B2C przy użyciu usługi Google jako dostawcy tożsamości, używamy przeglądarki systemu, ponieważ usługa Google nie zezwala na [uwierzytelnianie z osadzonych widoków WebView](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Obecnie `login.microsoftonline.com` jest to zaufana organizacja z firmą Google. Użycie tego urzędu będzie działało z osadzonym widokiem WebView. Nie `b2clogin.com` jest to jednak zaufany urząd z firmą Google, dlatego użytkownicy nie będą mogli się uwierzytelniać.

Jeśli zmienią się zmiany, firma Microsoft udostępnia aktualizację tego [problemu](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) .

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Buforowanie przy użyciu Azure AD B2C w MSAL.Net

### <a name="known-issue-with-azure-ad-b2c"></a>Znany problem z Azure AD B2C

MSAL.Net obsługuje [pamięć podręczną tokenów](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). Klucz buforowania tokenu jest oparty na oświadczeniach zwracanych przez dostawcę tożsamości. Obecnie MSAL.Net potrzebuje dwóch oświadczeń do skompilowania klucza pamięci podręcznej tokenu:
- `tid`który jest IDENTYFIKATORem dzierżawy usługi Azure AD, i
- `preferred_username`

W wielu scenariuszach Azure AD B2C brakuje obu tych oświadczeń.

Klient ma wpływ na to, że podczas próby wyświetlenia pola username pojawia się "Brak odpowiedzi w tokenie" jako wartość? W takim przypadku jest to spowodowane tym, że Azure AD B2C nie zwraca wartości IdToken dla preferred_username ze względu na ograniczenia związane z kontami społecznościowymi i zewnętrznymi dostawcami tożsamości (dostawców tożsamości). Usługa Azure AD zwraca wartość preferred_username, ponieważ wie, kto jest użytkownikiem, ale dla Azure AD B2C, ponieważ użytkownik może zalogować się przy użyciu konta lokalnego, Facebook, Google, GitHub itp. nie ma spójnej wartości Azure AD B2C do użycia dla preferred_username. Aby odblokować MSAL przed wdrożeniem zgodności pamięci podręcznej z biblioteką ADAL, firma Microsoft zdecydowała się użyć "braku" z odpowiedzi tokenu "w naszym końcu, gdy chodzi o konta Azure AD B2C, gdy IdToken nie zwróci niczego dla preferred_username. MSAL musi zwracać wartość dla preferred_username, aby zachować zgodność z pamięcią podręczną w różnych bibliotekach.

### <a name="workarounds"></a>Obejścia

#### <a name="mitigation-for-the-missing-tenant-id"></a>Środki zaradcze dla brakującego identyfikatora dzierżawy

Sugerowane obejście polega na użyciu [buforowania według zasad](#acquire-a-token-to-apply-a-policy)

Alternatywnie możesz użyć `tid` oświadczenia, jeśli używasz [zasad niestandardowych B2C](https://aka.ms/ief), ponieważ oferuje możliwość zwracania dodatkowych oświadczeń do aplikacji. Aby dowiedzieć się więcej na temat [transformacji oświadczeń](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>Środki zaradcze dla braku odpowiedzi tokenu
Jedną z opcji jest użycie żądania "name" jako preferowanej nazwy użytkownika. Ten proces jest wymieniony w [B2C doc](../../active-directory-b2c/user-flow-overview.md) ">" w kolumnie oświadczenie zwrotne wybierz oświadczenia, które mają być zwracane w tokenach autoryzacji wysyłanych z powrotem do aplikacji po pomyślnym edytowaniu profilu. Na przykład wybierz pozycję Display Name (kod pocztowy).

## <a name="next-steps"></a>Następne kroki

Więcej szczegółów na temat pozyskiwania tokenów interaktywnie za pomocą usługi MSAL.NET dla aplikacji Azure AD B2C przedstawiono w poniższym przykładzie.

| Przykład | Platforma | Opis|
|------ | -------- | -----------|
|[Active-Directory-B2C-Xamarin-Native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, platformy UWP | Prosta aplikacja Xamarin Forms, która przedstawia, jak używać MSAL.NET do uwierzytelniania użytkowników za pośrednictwem Azure AD B2C i uzyskiwać dostęp do internetowego interfejsu API przy użyciu tokenów z wynikiem.|
