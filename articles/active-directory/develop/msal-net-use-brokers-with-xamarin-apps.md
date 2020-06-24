---
title: Korzystanie z brokerów z systemem Xamarin iOS & Android | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak skonfigurować aplikacje Xamarin iOS, które mogą używać Microsoft Authenticator i biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET). Dowiedz się również, jak przeprowadzić migrację z biblioteki uwierzytelniania usługi Azure AD dla programu .NET (ADAL.NET) do biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1a57173311278c5e3e0304aeb12d4d6999379eb5
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84692329"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Używanie Microsoft Authenticator lub Intune — Portal firmy w aplikacjach platformy Xamarin

W systemach Android i iOS brokerzy, takie jak Microsoft Authenticator i Portal firmy Microsoft Intune specyficzne dla systemu Android, umożliwiają:

- Logowanie jednokrotne **(SSO)**: użytkownicy nie muszą logować się do poszczególnych aplikacji.
- **Identyfikacja urządzenia**: Broker uzyskuje dostęp do certyfikatu urządzenia. Ten certyfikat jest tworzony na urządzeniu, gdy jest on przyłączony do miejsca pracy.
- **Weryfikacja identyfikacji aplikacji**: gdy aplikacja wywołuje brokera, przekazuje adres URL przekierowania. Broker weryfikuje adres URL.

Aby włączyć jedną z tych funkcji, użyj `WithBroker()` parametru podczas wywoływania `PublicClientApplicationBuilder.CreateApplication` metody. `.WithBroker()`Parametr jest domyślnie ustawiony na wartość true. 

Należy również użyć instrukcji w poniższych sekcjach, aby skonfigurować uwierzytelnianie obsługiwane przez brokera dla aplikacji [systemu iOS](#brokered-authentication-for-ios) lub aplikacji [systemu Android](#brokered-authentication-for-android) .

## <a name="brokered-authentication-for-ios"></a>Uwierzytelnianie obsługiwane przez brokera dla systemu iOS

Wykonaj następujące kroki, aby umożliwić aplikacji platformy Xamarin. iOS komunikację z aplikacją [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

### <a name="step-1-enable-broker-support"></a>Krok 1. Włączanie obsługi brokera
Należy włączyć obsługę brokera dla poszczególnych wystąpień `PublicClientApplication` . Obsługa jest domyślnie wyłączona. Podczas tworzenia `PublicClientApplication` za pomocą `PublicClientApplicationBuilder` należy użyć `WithBroker()` parametru, jak pokazano w poniższym przykładzie. `WithBroker()`Parametr jest domyślnie ustawiony na wartość true.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Krok 2. Włączanie dostępu do łańcucha kluczy

Aby włączyć dostęp do łańcucha kluczy, musisz mieć grupę dostępu łańcucha kluczy dla swojej aplikacji. Korzystając z `WithIosKeychainSecurityGroup()` interfejsu API, można ustawić grupę dostępu łańcucha kluczy podczas tworzenia aplikacji:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Aby uzyskać więcej informacji, zobacz [Włączanie dostępu do łańcucha kluczy](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Krok 3. Aktualizacja AppDelegate do obsługi wywołania zwrotnego
Gdy biblioteka uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET) wywołuje brokera, Broker wywołuje z powrotem do aplikacji za pomocą `OpenUrl` metody `AppDelegate` klasy. Ponieważ MSAL czeka na odpowiedź brokera, aplikacja musi współpracować w celu wywołania MSAL.NET z powrotem. Aby włączyć tę współpracę, zaktualizuj `AppDelegate.cs` plik, aby zastąpić poniższą metodę.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, 
                             string sourceApplication,
                             NSObject annotation)
{
    if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
    {
      AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
      return true;
    }
    
    else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
    {                
         return false;                  
    }
    
    return true;     
}            
```

Ta metoda jest wywoływana za każdym razem, gdy aplikacja jest uruchomiona. Jest on używany jako okazja do przetworzenia odpowiedzi z brokera i ukończenia procesu uwierzytelniania, który MSAL.NET uruchomiony.

### <a name="step-4-set-uiviewcontroller"></a>Krok 4. Ustawianie UIViewController ()
Nadal w `AppDelegate.cs` pliku należy ustawić okno obiektu. Zwykle w przypadku platformy Xamarin iOS nie trzeba ustawiać okna obiektu. Ale potrzebujesz okna obiektu, aby wysyłać i odbierać odpowiedzi z brokera. 

Aby skonfigurować okno obiektu: 
1. W `AppDelegate.cs` pliku Ustaw `App.RootViewController` Nowy `UIViewController()` . To przypisanie zapewnia, że wywołanie do brokera zawiera `UIViewController` . Jeśli to ustawienie jest przypisane nieprawidłowo, może wystąpić następujący błąd:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. W `AcquireTokenInteractive` wywołaniu użyj, `.WithParentActivityOrWindow(App.RootViewController)` a następnie Przekaż odwołanie do okna obiektu, którego będziesz używać.

    W pliku `App.cs`:

    ```csharp
       public static object RootViewController { get; set; }
    ```

    W pliku `AppDelegate.cs`:

    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```

    W `AcquireToken` wywołaniu:

    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

### <a name="step-5-register-a-url-scheme"></a>Krok 5. rejestrowanie schematu adresu URL
MSAL.NET używa adresów URL do wywołania brokera, a następnie zwrócenia odpowiedzi brokera do aplikacji. Aby zakończyć rundy, zarejestruj schemat adresu URL aplikacji w `Info.plist` pliku.

`CFBundleURLSchemes`Nazwa musi zawierać `msauth.` prefiks. Postępuj zgodnie z prefiksem `CFBundleURLName` . 

W schemacie URL `BundleId` jednoznacznie identyfikuje aplikację: `$"msauth.(BundleId)"` . W takim `BundleId` przypadku `com.yourcompany.xforms` schemat adresu URL to `msauth.com.yourcompany.xforms` .

> [!NOTE]
> Ten schemat adresu URL staje się częścią identyfikatora URI przekierowania, który jednoznacznie identyfikuje aplikację po odebraniu odpowiedzi z brokera.

```XML
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Krok 6. Dodawanie identyfikatora brokera do sekcji LSApplicationQueriesSchemes

MSAL używa `–canOpenURL:` do sprawdzenia, czy na urządzeniu zainstalowano brokera. W systemie iOS 9 program Apple zablokował schematy, dla których aplikacja może wykonać zapytanie. 

Dodaj `msauthv2` do `LSApplicationQueriesSchemes` sekcji `Info.plist` pliku, tak jak w poniższym przykładzie:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>Krok 7. Rejestrowanie identyfikatora URI przekierowania w portalu aplikacji

W przypadku korzystania z brokera identyfikator URI przekierowania ma dodatkowy wymóg. Identyfikator URI przekierowania _musi_ mieć następujący format:

```csharp
$"msauth.{BundleId}://auth"
```

Przykład:

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```

Zwróć uwagę, że identyfikator URI przekierowania jest zgodny z `CFBundleURLSchemes` nazwą zawartą w `Info.plist` pliku.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Krok 8. Upewnij się, że identyfikator URI przekierowania jest zarejestrowany w aplikacji

Identyfikator URI przekierowania musi być zarejestrowany w [portalu rejestracji aplikacji](https://portal.azure.com) jako prawidłowy identyfikator URI przekierowania dla aplikacji. 

Portal rejestracji aplikacji zawiera nowe środowisko ułatwiające Obliczanie identyfikatora URI odpowiedzi obsługiwanej przez brokera z identyfikatora pakietu. 

Aby obliczyć identyfikator URI przekierowania:

1. W portalu rejestracji aplikacji wybierz pozycję **uwierzytelnianie**  >  **Wypróbuj nowe środowisko**.

   ![Wypróbuj nowe środowisko rejestracji aplikacji](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Wybierz pozycję **Dodaj platformę**.

   ![Dodaj platformę](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Jeśli lista platform jest obsługiwana, wybierz pozycję **iOS**.

   ![Konfigurowanie systemu iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Wprowadź żądany identyfikator pakietu, a następnie wybierz pozycję **Konfiguruj**.

   ![Wprowadź identyfikator pakietu](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Po wykonaniu kroków zostanie obliczony identyfikator URI przekierowania.

![Kopiuj identyfikator URI przekierowania](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Uwierzytelnianie obsługiwane przez brokera dla systemu Android

### <a name="step-1-enable-broker-support"></a>Krok 1. Włączanie obsługi brokera

Obsługa brokera jest włączana dla poszczególnych PublicClientApplication. Jest on domyślnie wyłączony. Użyj `WithBroker()` parametru (domyślnie ustawione na true) podczas tworzenia `IPublicClientApplication` przy użyciu `PublicClientApplicationBuilder` .

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) //(see step 4 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Krok 2. Aktualizacja AppDelegate do obsługi wywołania zwrotnego

Gdy MSAL.NET wywołuje brokera, Broker będzie z kolei wywoływać z powrotem do aplikacji za pomocą metody OnActivityResult (). Ponieważ MSAL będzie oczekiwać na odpowiedź z brokera, aplikacja musi skierować wynik do MSAL.NET.
Można to osiągnąć przez kierowanie wyniku do programu `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` przez zastąpienie metody OnActivityResult (), jak pokazano poniżej.

```CSharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Ta metoda jest wywoływana za każdym razem, gdy aplikacja brokera jest uruchamiana i służy jako szansa do przetwarzania odpowiedzi z brokera i ukończenia procesu uwierzytelniania uruchomionego przez MSAL.NET.

### <a name="step-3-set-an-activity"></a>Krok 3. Ustawianie działania

Aby uwierzytelnianie obsługiwane przez brokera działało, należy ustawić działanie, aby MSAL mogły wysyłać i odbierać odpowiedzi z brokera.

W tym celu należy podać działanie (zazwyczaj główne) `WithParentActivityOrWindow(object parent)` jako obiekt nadrzędny. 

**Przykład:**

W wywołaniu tokenu pozyskiwania:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-register-your-redirecturi-in-the-application-portal"></a>Krok 4. rejestrowanie RedirectUri w portalu aplikacji

MSAL używa adresów URL do wywołania brokera, a następnie powrotu do aplikacji. Aby ukończyć tę rundę, należy zarejestrować schemat adresu URL dla aplikacji. Ten identyfikator URI przekierowania musi być zarejestrowany w portalu rejestracji aplikacji usługi Azure AD jako prawidłowy identyfikator URI przekierowania dla aplikacji.


Identyfikator URI przekierowania wymagany dla aplikacji zależy od certyfikatu użytego do podpisania APK.

```
Example: msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

Ostatnia część identyfikatora URI, `hgbUYHVBYUTvuvT&Y6tr554365466=` , jest sygnaturą, z którą APK jest podpisany algorytmem Base64.
Jednak w fazie opracowywania aplikacji przy użyciu programu Visual Studio, jeśli debugujesz kod bez podpisywania apk z określonym certyfikatem, program Visual Studio podpisuje APK do celów debugowania, dając APK unikatowy podpis dla maszyny, na której jest on zbudowany. W ten sposób za każdym razem, gdy kompilujesz aplikację na innym komputerze, musisz zaktualizować identyfikator URI przekierowania w kodzie aplikacji i zarejestrować aplikację w Azure Portal, aby uwierzytelnić się za pomocą MSAL. 

Podczas debugowania może wystąpić wyjątek MSAL (lub komunikat dziennika) z informacją, że podany identyfikator URI przekierowania jest nieprawidłowy. **Ten wyjątek spowoduje również dostarczenie identyfikatora URI przekierowania, który powinien być używany** z bieżącą maszyną, na której odbywa się debugowanie. Możesz użyć tego identyfikatora URI przekierowania, aby kontynuować opracowywanie na czas.

Gdy wszystko będzie gotowe do sfinalizowania kodu, pamiętaj o zaktualizowaniu identyfikatora URI przekierowania w kodzie i w rejestracji aplikacji w Azure Portal, aby użyć podpisu certyfikatu, za pomocą którego będzie podpisywany APK.

W tym przypadku należy zarejestrować identyfikator URI przekierowania dla każdego członka zespołu oraz identyfikator URI przekierowania dla podpisanej wersji APK.

Możesz również obliczyć ten podpis samodzielnie, podobnie jak MSAL to: 

```CSharp
   private string GetRedirectUriForBroker()
   {
      string packageName = Application.Context.PackageName;
      string signatureDigest = this.GetCurrentSignatureForPackage(packageName);
      if (!string.IsNullOrEmpty(signatureDigest))
      {
            return string.Format(CultureInfo.InvariantCulture, "{0}://{1}/{2}", RedirectUriScheme,
               packageName.ToLowerInvariant(), signatureDigest);
      }

      return string.Empty;
   }

   private string GetCurrentSignatureForPackage(string packageName)
   {
            PackageInfo info = Application.Context.PackageManager.GetPackageInfo(packageName,
               PackageInfoFlags.Signatures);
            if (info != null && info.Signatures != null && info.Signatures.Count > 0)
            {
               // First available signature. Applications can be signed with multiple signatures.
               // The order of Signatures is not guaranteed.
               Signature signature = info.Signatures[0];
               MessageDigest md = MessageDigest.GetInstance("SHA");
               md.Update(signature.ToByteArray());
               return Convert.ToBase64String(md.Digest(), Base64FormattingOptions.None);
               // Server side needs to register all other tags. ADAL will
               // send one of them.
            }
   }
```

Istnieje również możliwość uzyskania podpisu dla pakietu przy użyciu narzędzia z następującymi poleceniami:

Dla systemu Windows:`keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android`

Dla komputerów Mac:`keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64`

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat zagadnień dotyczących używania platforma uniwersalna systemu Windows z MSAL.NET](msal-net-uwp-considerations.md).
