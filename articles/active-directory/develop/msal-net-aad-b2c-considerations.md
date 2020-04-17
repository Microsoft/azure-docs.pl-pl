---
title: Usługa Azure AD B2C (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o konkretnych zagadnieniach podczas korzystania z usługi Azure AD B2C z biblioteką uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
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
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533959"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Używanie MSAL.NET do logowania użytkowników z tożsamościami społecznościowymi

Za pomocą MSAL.NET można logować się do użytkowników za pomocą tożsamości społecznościowych przy użyciu [usługi Azure Active Directory B2C (Azure AD B2C).](https://aka.ms/aadb2c) Usługa Azure AD B2C jest oparta na pojęciu zasad. W MSAL.NET określenie zasad przekłada się na zapewnienie uprawnień.

- Podczas tworzenia wystąpienia publicznej aplikacji klienckiej, należy określić zasady w urzędzie.
- Aby zastosować zasadę, należy wywołać zastąpienie `AcquireTokenInteractive` zawierające parametr. `authority`

Ta strona jest dla MSAL 3.x. Jeśli jesteś zainteresowany msal 2.x, zobacz [specyfika usługi Azure AD B2C w msal 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Urząd dzierżawy i zasad usługi Azure AD B2C

Uprawnienia do korzystania `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}` są tam, gdzie:

- `azureADB2CHostname`to nazwa dzierżawy usługi Azure AD B2C `{your-tenant-name}.b2clogin.com`plus host (na przykład )
- `tenant`jest pełną nazwą dzierżawy usługi Azure AD B2C (na `{your-tenant-name}.onmicrosoft.com`przykład) lub identyfikatorem GUID dla dzierżawy,
- `policyName`nazwę zasad lub przepływ użytkownika do zastosowania (na przykład "b2c_1_susi" do rejestracji/logowania).

Aby uzyskać więcej informacji na temat urzędów usługi Azure AD B2C, zobacz tę [dokumentację](/azure/active-directory-b2c/b2clogin).

## <a name="instantiating-the-application"></a>Tworzenie wystąpienia aplikacji

Podczas tworzenia aplikacji należy podać uprawnienia.

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

## <a name="acquire-a-token-to-apply-a-policy"></a>Uzyskaj token, aby zastosować zasadę

Pobieranie tokenu dla interfejsu API chronionego usługi Azure AD B2C w publicznej aplikacji klienckiej wymaga użycia zastąpienia z urzędem:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireTokenInteractive(scopes)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .WithParentActivityOrWindow(ParentActivityOrWindow)
                                            .ExecuteAsync();
```

tym:

- `policy`jest jednym z poprzednich ciągów `PolicySignUpSignIn`(na przykład ).
- `ParentActivityOrWindow`jest wymagane dla systemu Android (działanie) i opcjonalne dla innych platform, które obsługują nadrzędny interfejs użytkownika, takich jak windows w systemie Windows i UIViewController w systemie iOS. Zobacz więcej informacji [tutaj na temat okna dialogowego interfejsu użytkownika](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow).
- `GetAccountByPolicy(IEnumerable<IAccount>, string)`jest metodą, która znajduje konto dla danej zasady. Przykład:

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

Stosowanie zasad lub przepływu użytkownika (na przykład umożliwienie użytkownikowi końcowemu edytowania profilu lub `AcquireTokenInteractive`resetowania hasła) odbywa się obecnie przez wywołanie . W przypadku tych dwóch zasad nie używasz zwracanego wyniku tokenu/uwierzytelniania.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Szczególny przypadek zasad EditProfile i ResetPassword

Jeśli chcesz zapewnić środowisko, w którym użytkownicy końcowi logują się przy pomocy tożsamości społecznościowej, a następnie edytują swój profil, chcesz zastosować zasady profilu edycji usługi Azure AD B2C. Sposobem na to jest `AcquireTokenInteractive` wywołanie z określonym urzędem dla tej `Prompt.NoPrompt` zasady i Prompt ustawić, aby zapobiec wyświetlaniu okna dialogowego wyboru konta (jak użytkownik jest już zalogowany i ma aktywną sesję plików cookie).

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
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Poświadczenia hasła właściciela zasobu (ROPC) z usługą Azure AD B2C
Więcej informacji na temat przepływu ROPC można znaleźć w tej [dokumentacji.](v2-oauth-ropc.md)

Ten przepływ nie jest **zalecane,** ponieważ aplikacja z prośbą o hasło nie jest bezpieczny. Aby uzyskać więcej informacji na temat tego problemu, zobacz [ten artykuł](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/).

Używając nazwy użytkownika/hasła, rezygnujesz z wielu rzeczy:
- Podstawowe założenia współczesnej tożsamości: hasło zostaje poławione, powtórzone. Ponieważ mamy tę koncepcję tajemnicy akcji, która może zostać przechwycona. Jest to niezgodne z bez hasła.
- Użytkownicy, którzy muszą wykonać uwierzytelnianie wieloskładnikowe, nie będą mogli się zalogować (ponieważ nie ma interakcji).
- Użytkownicy nie będą mogli samodzielnie logować się.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Konfigurowanie przepływu ROPC w usłudze Azure AD B2C
W dzierżawie usługi Azure AD B2C utwórz nowy przepływ użytkowników i wybierz pozycję **Zaloguj się przy użyciu ropc**. Umożliwi to zasady ROPC dla dzierżawy. Aby uzyskać więcej [informacji, zobacz Konfigurowanie przepływu poświadczeń hasła właściciela zasobu.](/azure/active-directory-b2c/configure-ropc)

`IPublicClientApplication`zawiera metodę:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Ta metoda przyjmuje jako parametry:
- *Zakresy,* aby zażądać tokenu dostępu dla.
- *Nazwa użytkownika*.
- *Hasło* SecureString dla użytkownika.

Pamiętaj, aby użyć urzędu zawierającego zasady ROPC.

### <a name="limitations-of-the-ropc-flow"></a>Ograniczenia przepływu ROPC
 - Przepływ ROPC **działa tylko dla kont lokalnych** (gdzie można zarejestrować się w usłudze Azure AD B2C przy użyciu adresu e-mail lub nazwy użytkownika). Ten przepływ nie działa, jeśli federating do dowolnego z dostawców tożsamości obsługiwanych przez usługę Azure AD B2C (Facebook, Google, itp.).

## <a name="google-auth-and-embedded-webview"></a>Google Auth i osadzone Webview

Jeśli jesteś programistą usługi Azure AD B2C korzystającym z Google jako dostawcy tożsamości, zalecamy korzystanie z przeglądarki systemowej, ponieważ Google nie zezwala na [uwierzytelnianie z osadzonych widoków internetowych.](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html) Obecnie `login.microsoftonline.com` jest zaufanym autorytetem w Google. Korzystanie z tego urzędu będzie współpracować z osadzonym webview. Jednak `b2clogin.com` korzystanie z niego nie jest zaufanym urzędem google, więc użytkownicy nie będą mogli uwierzytelniać się.

Jeśli coś się zmieni, udostępnimy aktualizację tego [problemu.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688)

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Buforowanie za pomocą usługi Azure AD B2C w MSAL.Net

### <a name="known-issue-with-azure-ad-b2c"></a>Znany problem z usługą Azure AD B2C

MSAL.Net obsługuje pamięć [podręczną tokenów](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). Klucz buforowania tokenu jest oparty na oświadczeń zwróconych przez dostawcę tożsamości. Obecnie MSAL.Net potrzebuje dwóch oświadczeń do utworzenia klucza pamięci podręcznej tokenu:
- `tid`który jest identyfikatorem dzierżawy usługi Azure AD, oraz
- `preferred_username`

Oba te oświadczenia brakuje w wielu scenariuszach usługi Azure AD B2C.

Wpływ na klienta jest to, że podczas próby wyświetlenia pola nazwy użytkownika, otrzymasz "Brak odpowiedzi tokenu" jako wartość? Jeśli tak, to dlatego, że usługa Azure AD B2C nie zwraca wartość w IdToken dla preferred_username z powodu ograniczeń z kont społecznościowych i zewnętrznych dostawców tożsamości (dostawców tożsamości). Usługa Azure AD zwraca wartość dla preferred_username, ponieważ wie, kim jest użytkownik, ale dla usługi Azure AD B2C, ponieważ użytkownik może zalogować się za pomocą konta lokalnego, Facebook, Google, GitHub, itp. nie ma spójnej wartości dla usługi Azure AD B2C do użycia dla preferred_username. Aby odblokować msal z toczenia zgodności pamięci podręcznej z usługi ADAL, zdecydowaliśmy się użyć "Brak odpowiedzi tokenu" na naszym końcu, gdy do czynienia z kontami usługi Azure AD B2C, gdy IdToken zwraca nic dla preferred_username. MsAL musi zwrócić wartość dla preferred_username, aby zachować zgodność pamięci podręcznej w bibliotekach.

### <a name="workarounds"></a>Obejścia

#### <a name="mitigation-for-the-missing-tenant-id"></a>Łagodzenie brakującego identyfikatora dzierżawy

Sugerowanym rozwiązaniem jest użycie [buforowania według zasad](#acquire-a-token-to-apply-a-policy)

Alternatywnie można użyć `tid` oświadczenia, jeśli używasz [zasad niestandardowych B2C](https://aka.ms/ief), ponieważ zapewnia możliwość zwrócenia dodatkowych oświadczeń do aplikacji. Aby dowiedzieć się więcej o [transformacji oświadczeń](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>Łagodzenie dla "Brak odpowiedzi tokenu"
Jedną z opcji jest użycie oświadczenia "nazwa" jako preferowanej nazwy użytkownika. Proces jest wymieniony w tym [B2C doc](../../active-directory-b2c/user-flow-overview.md) -> "W kolumnie Oświadczenie zwrotu wybierz oświadczenia, które mają być zwrócone w tokenach autoryzacji wysłanych z powrotem do aplikacji po pomyślnym doświadczeniu edycji profilu. Na przykład wybierz opcję Nazwa wyświetlana, Kod pocztowy."

## <a name="next-steps"></a>Następne kroki

Więcej szczegółów na temat pobierania tokenów interaktywnie z MSAL.NET dla aplikacji usługi Azure AD B2C znajdują się w poniższym przykładzie.

| Przykład | Platforma | Opis|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | Prosta aplikacja Xamarin Forms przedstawiająca sposób używania MSAL.NET do uwierzytelniania użytkowników za pośrednictwem usługi Azure AD B2C i uzyskiwania dostępu do internetowego interfejsu API z tokenami wynikowymi.|
