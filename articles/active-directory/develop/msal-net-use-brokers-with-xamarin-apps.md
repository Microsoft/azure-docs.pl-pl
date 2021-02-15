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
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 6958302a429fd88d4e26087b860b7f473bf4a1f9
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103995"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Używanie Microsoft Authenticator lub Intune — Portal firmy w aplikacjach platformy Xamarin

W systemach Android i iOS brokerzy, takie jak Microsoft Authenticator i Portal firmy Microsoft Intune specyficzne dla systemu Android, umożliwiają:

- Logowanie jednokrotne **(SSO)**: użytkownicy nie muszą logować się do poszczególnych aplikacji.
- **Identyfikacja urządzenia**: Broker uzyskuje dostęp do certyfikatu urządzenia. Ten certyfikat jest tworzony na urządzeniu, gdy jest on przyłączony do miejsca pracy.
- **Weryfikacja identyfikacji aplikacji**: gdy aplikacja wywołuje brokera, przekazuje adres URL przekierowania. Broker weryfikuje adres URL.

Aby włączyć jedną z tych funkcji, użyj `WithBroker()` parametru podczas wywoływania `PublicClientApplicationBuilder.CreateApplication` metody. `.WithBroker()`Parametr jest domyślnie ustawiony na wartość true.

Konfigurowanie uwierzytelniania obsługiwanego przez brokera w bibliotece uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET) zależy od platformy:

* [aplikacje dla systemu iOS](#brokered-authentication-for-ios)
* [Aplikacje dla systemu Android](#brokered-authentication-for-android)

## <a name="brokered-authentication-for-ios"></a>Uwierzytelnianie obsługiwane przez brokera dla systemu iOS

Aby umożliwić aplikacji platformy Xamarin. iOS komunikację z aplikacją [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) , wykonaj następujące czynności. W przypadku korzystania z systemu iOS 13 Rozważ zapoznanie się z informacjami o [zmianach w interfejsie API firmy Apple](./msal-net-xamarin-ios-considerations.md).

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

Gdy MSAL.NET wywołuje brokera, Broker wywołuje z powrotem do aplikacji za pomocą `OpenUrl` metody `AppDelegate` klasy. Ponieważ MSAL czeka na odpowiedź brokera, aplikacja musi współpracować w celu wywołania MSAL.NET z powrotem. Aby włączyć tę współpracę, zaktualizuj plik *AppDelegate.cs* , aby zastąpić poniższą metodę.

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

Nadal w pliku *AppDelegate.cs* Ustaw okno obiektu. Zazwyczaj nie trzeba ustawiać okna obiektu dla platformy Xamarin iOS, ale potrzebujesz okna obiektu, aby wysyłać i odbierać odpowiedzi z brokera.

Aby skonfigurować okno obiektu:

1. W pliku *AppDelegate.cs* ustaw wartość `App.RootViewController` Nowy `UIViewController()` . To przypisanie zapewnia, że wywołanie do brokera zawiera `UIViewController` . Jeśli to ustawienie jest przypisane nieprawidłowo, może wystąpić następujący błąd:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. W `AcquireTokenInteractive` wywołaniu użyj, `.WithParentActivityOrWindow(App.RootViewController)` a następnie Przekaż odwołanie do okna obiektu, którego będziesz używać.

    W *App.cs*:

    ```csharp
       public static object RootViewController { get; set; }
    ```

    W *AppDelegate.cs*:

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

MSAL.NET używa adresów URL do wywołania brokera, a następnie zwrócenia odpowiedzi brokera do aplikacji. Aby ukończyć rundy, zarejestruj schemat adresu URL aplikacji w pliku *info. plist* .

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

Dodaj `msauthv2` do `LSApplicationQueriesSchemes` sekcji pliku *info. plist* , jak w poniższym przykładzie:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-add-a-redirect-uri-to-your-app-registration"></a>Krok 7. Dodawanie identyfikatora URI przekierowania do rejestracji aplikacji

W przypadku korzystania z brokera identyfikator URI przekierowania ma dodatkowy wymóg. Identyfikator URI przekierowania _musi_ mieć następujący format:

```csharp
$"msauth.{BundleId}://auth"
```

Oto przykład:

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth";
```

Zwróć uwagę, że identyfikator URI przekierowania jest zgodny z `CFBundleURLSchemes` nazwą zawartą w pliku *info. plist* .

Dodaj identyfikator URI przekierowania do rejestracji aplikacji w [Azure Portal](https://portal.azure.com). Aby wygenerować poprawnie sformatowany identyfikator URI przekierowania, użyj **rejestracje aplikacji** w Azure Portal, aby wygenerować identyfikator URI przekierowania obsługiwanego przez brokera z identyfikatora pakietu.

**Aby wygenerować identyfikator URI przekierowania:**

1. Zaloguj się w witrynie <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Wybierz **Azure Active Directory**  >  **rejestracje aplikacji** > zarejestrowanej aplikacji
1. Wybieranie **uwierzytelniania**  >  **Dodawanie platformy**  >  **iOS/macOS**
1. Wprowadź identyfikator pakietu, a następnie wybierz pozycję **Konfiguruj**.

    Skopiuj wygenerowany identyfikator URI przekierowania, który pojawia się w polu tekstowym **Identyfikator URI przekierowania** , aby uwzględnić w kodzie:

    :::image type="content" source="media/msal-net-use-brokers-with-xamarin-apps/portal-01-ios-platform-settings.png" alt-text="Ustawienia platformy iOS z wygenerowanym identyfikatorem URI przekierowania w Azure Portal":::
1. Wybierz pozycję **gotowe** , aby ukończyć generowanie identyfikatora URI przekierowania.

## <a name="brokered-authentication-for-android"></a>Uwierzytelnianie obsługiwane przez brokera dla systemu Android

### <a name="step-1-enable-broker-support"></a>Krok 1. Włączanie obsługi brokera

Obsługa brokera jest włączona na `PublicClientApplication` podstawie. Jest on domyślnie wyłączony. Użyj `WithBroker()` parametru (domyślnie ustawione na true) podczas tworzenia `IPublicClientApplication` przy użyciu `PublicClientApplicationBuilder` .

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) // See step #4
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Krok 2. Aktualizacja AppDelegate do obsługi wywołania zwrotnego

Gdy MSAL.NET wywołuje brokera, Broker będzie z kolei wywoływać z powrotem do aplikacji za pomocą `OnActivityResult()` metody. Ponieważ MSAL będzie oczekiwać na odpowiedź z brokera, aplikacja musi skierować wynik do MSAL.NET.

Skieruj wynik do `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` metody, zastępując `OnActivityResult()` metodę, jak pokazano poniżej:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Ta metoda jest wywoływana za każdym razem, gdy uruchamiana jest aplikacja brokera i służy jako szansa do przetwarzania odpowiedzi z brokera i ukończenia procesu uwierzytelniania uruchomionego przez MSAL.NET.

### <a name="step-3-set-an-activity"></a>Krok 3. Ustawianie działania

Aby włączyć uwierzytelnianie obsługiwane przez brokera, ustaw działanie tak, aby program MSAL mógł wysyłać i odbierać odpowiedź do i z brokera. W tym celu należy podać działanie (zazwyczaj `MainActivity` ) do `WithParentActivityOrWindow(object parent)` obiektu nadrzędnego.

Na przykład w wywołaniu `AcquireTokenInteractive()` :

```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-add-a-redirect-uri-to-your-app-registration"></a>Krok 4. Dodawanie identyfikatora URI przekierowania do rejestracji aplikacji

MSAL używa adresów URL do wywołania brokera, a następnie powrotu do aplikacji. Aby ukończyć tę rundę, zarejestruj **Identyfikator URI przekierowania** dla aplikacji przy użyciu [Azure Portal](https://portal.azure.com).

Format identyfikatora URI przekierowania dla aplikacji zależy od certyfikatu użytego do podpisania APK. Na przykład:

```
msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

Ostatnia część identyfikatora URI `hgbUYHVBYUTvuvT&Y6tr554365466=` jest zakodowana w formacie base64 wersja podpisu, z którym jest podpisana APK. Podczas tworzenia aplikacji w programie Visual Studio, jeśli debugujesz kod bez podpisywania APK z określonym certyfikatem, program Visual Studio podpisuje APK do celów debugowania. Gdy program Visual Studio podpisuje APK w ten sposób, nadaje mu unikatowy podpis dla maszyny, w której jest ona wbudowana. W ten sposób za każdym razem, gdy kompilujesz aplikację na innym komputerze, musisz zaktualizować identyfikator URI przekierowania w kodzie aplikacji i zarejestrować aplikację w Azure Portal, aby uwierzytelnić się za pomocą MSAL.

Podczas debugowania może wystąpić wyjątek MSAL (lub komunikat dziennika) z informacją, że podany identyfikator URI przekierowania jest nieprawidłowy. **Komunikat o wyjątku lub dzienniku wskazuje również identyfikator URI przekierowania, który powinien być używany** z bieżącą maszyną, która jest debugowana. Możesz użyć podanego identyfikatora URI przekierowania, aby kontynuować tworzenie aplikacji, o ile identyfikator URI przekierowania zostanie zaktualizowany w kodzie i Dodaj podany identyfikator URI przekierowania do rejestracji aplikacji w Azure Portal.

Gdy wszystko będzie gotowe do sfinalizowania kodu, zaktualizuj identyfikator URI przekierowania w kodzie i rejestrację aplikacji w Azure Portal, aby użyć podpisu certyfikatu, za pomocą którego podpiszesz APK.

W rzeczywistości oznacza to, że należy rozważyć dodanie identyfikatora URI przekierowania dla każdego członka zespołu deweloperów *oraz* identyfikatora URI przekierowania dla podpisanej wersji APK.

Możesz obliczyć podpis samodzielnie, podobnie jak MSAL to:

```csharp
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

Istnieje również możliwość uzyskania podpisu dla pakietu przy użyciu **Narzędzia** z następującymi poleceniami:

* W systemie Windows:
    ```console
    keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
    ````
* macOS:
    ```console
    keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
    ````

### <a name="step-5-optional-fall-back-to-the-system-browser"></a>Krok 5 (opcjonalny): powrót do przeglądarki systemowej

Jeśli MSAL jest skonfigurowany do korzystania z brokera, ale Broker nie jest zainstalowany, MSAL powróci do korzystania z widoku sieci Web (przeglądarki). Usługa MSAL podejmie próbę uwierzytelnienia przy użyciu domyślnej przeglądarki systemowej na urządzeniu, co nie powiedzie się, ponieważ identyfikator URI przekierowania jest skonfigurowany dla brokera, a przeglądarka systemowa nie wie, jak używać jej do nawigacji z powrotem do MSAL. Aby uniknąć awarii, można skonfigurować *filtr konwersji* z identyfikatorem URI przekierowania brokera użytym w kroku 4.

Zmodyfikuj manifest aplikacji, aby dodać filtr zamierzeń:

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="Package Name"
                    android:path="/Package Signature"/>
```

Na przykład jeśli masz identyfikator URI przekierowania z `msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=` , manifest powinien wyglądać podobnie do poniższego fragmentu kodu XML.

`/`Wymagany jest ukośnik () znajdujący się przed sygnaturą w `android:path` wartości. 

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="com.microsoft.xforms.testApp"
                    android:path="/hgbUYHVBYUTvuvT&Y6tr554365466="/>
```

Alternatywnie można skonfigurować MSAL, aby powracać do osadzonej przeglądarki, która nie polega na identyfikatorze URI przekierowania:

```csharp
.WithUseEmbeddedWebUi(true)
```

## <a name="troubleshooting-tips-for-android-brokered-authentication"></a>Wskazówki dotyczące rozwiązywania problemów z uwierzytelnianiem przez brokera systemu Android

Poniżej przedstawiono kilka wskazówek dotyczących unikania problemów podczas implementowania uwierzytelniania obsługiwanego przez brokera w systemie Android:

- **Identyfikator URI przekierowania** — Dodaj identyfikator URI przekierowania do rejestracji aplikacji w [Azure Portal](https://portal.azure.com/). Brakujący lub nieprawidłowy identyfikator URI przekierowania jest typowym problemem napotykanym przez deweloperów.
- **Wersja brokera** — Zainstaluj minimalną wymaganą wersję aplikacji brokera. Jedna z tych dwóch aplikacji może służyć do uwierzytelniania obsługiwanego przez brokera w systemie Android.
  - [Intune — portal firmy](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) (wersja 5.0.4689.0 lub nowsza)
  - [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) (wersja 6.2001.0140 lub nowsza).
- **Priorytet brokera** — MSAL komunikuje się z *pierwszym brokerem zainstalowanym* na urządzeniu po zainstalowaniu wielu brokerów.

    Przykład: w przypadku pierwszej instalacji Microsoft Authenticator a następnie zainstalowania Intune — Portal firmy uwierzytelnianie przez brokera będzie miało miejsce *tylko* w Microsoft Authenticator.
- **Dzienniki** — w przypadku wystąpienia problemu z uwierzytelnianiem przez brokera Wyświetlanie dzienników brokera może ułatwić zdiagnozowanie przyczyny.
  - Wyświetlanie dzienników Microsoft Authenticator:

    1. Wybierz przycisk menu w prawym górnym rogu aplikacji.
    1. Wybierz pozycję **Pomoc**  >  **wysyłanie dzienników**  >  **Wyświetl dzienniki**.
    1. Wybierz pozycję **Kopiuj wszystko** , aby skopiować dzienniki brokera do schowka urządzenia.

    Najlepszym sposobem na Debugowanie za pomocą tych dzienników jest wysłanie ich pocztą e-mail i wyświetlenie ich na komputerze deweloperskim. Łatwiejsze może okazać się analizowanie dzienników na komputerze, a nie na samym urządzeniu. Możesz również użyć edytora testów w systemie Android, aby zapisać dzienniki jako plik tekstowy, a następnie użyć kabla USB, aby skopiować plik na komputer.

  - Wyświetlanie dzienników Intune — Portal firmy:

    1. Wybierz przycisk menu w lewym górnym rogu aplikacji
    1. Wybierz **Ustawienia**  >  **dane diagnostyczne**
    1. Wybierz opcję **Kopiuj dzienniki** , aby skopiować dzienniki brokera na kartę SD urządzenia.
    1. Podłącz urządzenie do komputera za pomocą kabla USB, aby wyświetlić dzienniki na komputerze deweloperskim.

    Po umieszczeniu dzienników możesz przeszukiwać je w celu uwierzytelnienia za pomocą identyfikatora korelacji. Identyfikator korelacji jest dołączany do każdego żądania uwierzytelnienia. Aby znaleźć błędy zwrócone przez punkt końcowy uwierzytelniania platformy tożsamości firmy Microsoft, wyszukaj ciąg `AADSTS` .

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat zagadnień dotyczących używania platforma uniwersalna systemu Windows z MSAL.NET](msal-net-uwp-considerations.md).
