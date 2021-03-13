---
title: Konfiguracja kodu i rozwiązywanie problemów z programem Xamarin Android (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Informacje dotyczące korzystania z platformy Xamarin Android z biblioteką uwierzytelniania firmy Microsoft dla programu .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 11642480ac817b50d102e396b8ab5e200948a615
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199566"
---
# <a name="configuration-requirements-and-troubleshooting-tips-for-xamarin-android-with-msalnet"></a>Wymagania dotyczące konfiguracji i wskazówki dotyczące rozwiązywania problemów z platformą Xamarin Android z MSAL.NET

Istnieje kilka zmian konfiguracji, które są wymagane do wprowadzenia w kodzie podczas korzystania z platformy Xamarin Android z biblioteką uwierzytelniania firmy Microsoft dla programu .NET (MSAL.NET). W poniższych sekcjach opisano wymagane modyfikacje oraz sekcję [rozwiązywania problemów](#troubleshooting) , która pomaga uniknąć niektórych typowych problemów.

## <a name="set-the-parent-activity"></a>Ustawianie działania nadrzędnego

W programie Xamarin Android Ustaw działanie nadrzędne, aby token zwracał po interakcji:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

W MSAL.NET 4,2 i nowszych można również ustawić tę funkcję na poziomie [PublicClientApplication][PublicClientApplication]. Aby to zrobić, użyj wywołania zwrotnego:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Jeśli używasz [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin), [`PublicClientApplication`][PublicClientApplication] kod konstruktora powinien wyglądać podobnie do tego fragmentu kodu:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Upewnij się, że kontrolka powraca do MSAL

Gdy Interaktywna część przepływu uwierzytelniania jest zakończona, zwróć kontrolę do MSAL przez zastąpienie [`Activity`][Activity] .[`OnActivityResult()`][OnActivityResult] Method.

W zastąpieniu Wywołaj MSAL. Sieć `AuthenticationContinuationHelper` .`SetAuthenticationContinuationEventArgs()` Metoda zwracająca sterowanie do MSAL na końcu interaktywnej części przepływu uwierzytelniania.

```csharp
protected override void OnActivityResult(int requestCode,
                                         Result resultCode,
                                         Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    // Return control to MSAL
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                            resultCode,
                                                                            data);
}
```

## <a name="update-the-android-manifest-for-system-webview-support"></a>Aktualizowanie manifestu systemu Android dla obsługi systemu WebView 

Aby można było obsługiwać System WebView, plik *AndroidManifest.xml* powinien zawierać następujące wartości:

```xml
<activity android:name="microsoft.identity.client.BrowserTabActivity" android:configChanges="orientation|screenSize">
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="msal{Client Id}" android:host="auth" />
  </intent-filter>
</activity>
```

`android:scheme`Wartość jest tworzona na podstawie identyfikatora URI przekierowania skonfigurowanego w portalu aplikacji. Na przykład jeśli identyfikator URI przekierowania to `msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842://auth` , `android:scheme` wpis w manifeście będzie wyglądać podobnie do tego przykładu:

```xml
<data android:scheme="msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842" android:host="auth" />
```

Alternatywnie możesz [utworzyć działanie w kodzie](/xamarin/android/platform/android-manifest#the-basics) zamiast ręcznie edytować *AndroidManifest.xml*. Aby utworzyć działanie w kodzie, najpierw Utwórz klasę, która zawiera `Activity` atrybut i `IntentFilter` atrybut.

Oto przykład klasy, która reprezentuje wartości pliku XML:

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="use-system-webview-in-brokered-authentication"></a>Używanie systemu WebView w uwierzytelnianiu obsługiwanym przez brokera

Aby użyć systemu WebView jako rezerwowego do uwierzytelniania interaktywnego po skonfigurowaniu aplikacji do korzystania z uwierzytelniania obsługiwanego przez brokera, a na urządzeniu nie jest zainstalowany żaden Broker, Włącz MSAL do przechwytywania odpowiedzi uwierzytelniania przy użyciu identyfikatora URI przekierowania brokera. Program MSAL podejmie próbę uwierzytelnienia przy użyciu domyślnego programu System WebView na urządzeniu, gdy wykryje, że Broker jest niedostępny. Korzystanie z tego ustawienia domyślnego nie powiedzie się, ponieważ identyfikator URI przekierowania jest skonfigurowany do korzystania z brokera, a System WebView nie wie, jak używać go do powrotu do MSAL. Aby rozwiązać ten problem, Utwórz _filtr konwersji_ przy użyciu identyfikatora URI przekierowania brokera, który został wcześniej skonfigurowany. Dodaj filtr zamierzeń, modyfikując manifest aplikacji podobny do tego przykładu:

```xml
<!--Intent filter to capture System WebView or Authenticator calling back to our app after sign-in-->
<activity
      android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
          <action android:name="android.intent.action.VIEW" />
          <category android:name="android.intent.category.DEFAULT" />
          <category android:name="android.intent.category.BROWSABLE" />
          <data android:scheme="msauth"
              android:host="Enter_the_Package_Name"
              android:path="/Enter_the_Signature_Hash" />
    </intent-filter>
</activity>
```

Zastąp nazwę pakietu zarejestrowanego w Azure Portal `android:host=` wartości. Zastąp skrót klucza, który został zarejestrowany w Azure Portal dla tej `android:path=` wartości. Wartość skrótu podpisu *nie* powinna być zakodowana w adresie URL. Upewnij się, że `/` na początku skrótu podpisu zostanie wyświetlony wiodący ukośnik ().

### <a name="xamarinforms-43x-manifest"></a>Manifest platformy Xamarin. Forms 4.3. x

Xamarin. Forms 4.3. x generuje kod, który ustawia `package` atrybut na `com.companyname.{appName}` w *AndroidManifest.xml*. Jeśli używasz `DataScheme` jako `msal{client_id}` , możesz chcieć zmienić wartość tak, aby odpowiadała wartości `MainActivity.cs` przestrzeni nazw.

## <a name="android-11-support"></a>Obsługa systemu Android 11

Aby użyć przeglądarki systemowej i uwierzytelniania obsługiwanego przez brokera w systemie Android 11, należy najpierw zadeklarować te pakiety, aby były widoczne dla aplikacji. Aplikacje przeznaczone dla systemu Android 10 (interfejs API 29) i starsze mogą wysyłać zapytania do systemu operacyjnego, aby uzyskać listę pakietów, które są dostępne na urządzeniu w danym momencie. W celu zapewnienia prywatności i bezpieczeństwa system Android 11 zmniejsza widoczność pakietu do domyślnej listy pakietów systemu operacyjnego i pakietów, które są określone w pliku *AndroidManifest.xml* aplikacji. 

Aby włączyć uwierzytelnianie aplikacji przy użyciu przeglądarki systemu i brokera, Dodaj następującą sekcję do *AndroidManifest.xml*:

```xml
<!-- Required for API Level 30 to make sure the app can detect browsers and other apps where communication is needed.-->
<!--https://developer.android.com/training/basics/intents/package-visibility-use-cases-->
<queries>
  <package android:name="com.azure.authenticator" />
  <package android:name="{Package Name}" />
  <package android:name="com.microsoft.windowsintune.companyportal" />
  <!-- Required for API Level 30 to make sure the app detect browsers
      (that don't support custom tabs) -->
  <intent>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="https" />
  </intent>
  <!-- Required for API Level 30 to make sure the app can detect browsers that support custom tabs -->
  <!-- https://developers.google.com/web/updates/2020/07/custom-tabs-android-11#detecting_browsers_that_support_custom_tabs -->
  <intent>
    <action android:name="android.support.customtabs.action.CustomTabsService" />
  </intent>
</queries>
``` 

Zamień `{Package Name}` na nazwę pakietu aplikacji. 

Zaktualizowany manifest, który obejmuje obsługę przeglądarki systemowej i uwierzytelniania obsługiwanego przez brokera, powinien wyglądać podobnie do tego przykładu:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.companyname.XamarinDev">
    <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="30" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <application android:theme="@android:style/Theme.NoTitleBar">
        <activity android:name="microsoft.identity.client.BrowserTabActivity" android:configChanges="orientation|screenSize">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842" android:host="auth" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msauth" android:host="com.companyname.XamarinDev" android:path="/Fc4l/5I4mMvLnF+l+XopDuQ2gEM=" />
            </intent-filter>
        </activity>
    </application>
    <!-- Required for API Level 30 to make sure we can detect browsers and other apps we want to
     be able to talk to.-->
    <!--https://developer.android.com/training/basics/intents/package-visibility-use-cases-->
    <queries>
        <package android:name="com.azure.authenticator" />
        <package android:name="com.companyname.xamarindev" />
        <package android:name="com.microsoft.windowsintune.companyportal" />
        <!-- Required for API Level 30 to make sure we can detect browsers
        (that don't support custom tabs) -->
        <intent>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="https" />
        </intent>
        <!-- Required for API Level 30 to make sure we can detect browsers that support custom tabs -->
        <!-- https://developers.google.com/web/updates/2020/07/custom-tabs-android-11#detecting_browsers_that_support_custom_tabs -->
        <intent>
            <action android:name="android.support.customtabs.action.CustomTabsService" />
        </intent>
    </queries>
</manifest>
```

## <a name="use-the-embedded-web-view-optional"></a>Użyj osadzonego widoku sieci Web (opcjonalnie)

Domyślnie MSAL.NET używa przeglądarki sieci Web systemu. Ta przeglądarka umożliwia korzystanie z funkcji logowania jednokrotnego (SSO) przy użyciu aplikacji sieci Web i innych aplikacji. W niektórych rzadkich przypadkach może być konieczne, aby system używał osadzonego widoku sieci Web.

Ten przykład kodu pokazuje, jak skonfigurować osadzony widok sieci Web:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Aby uzyskać więcej informacji, zobacz [Korzystanie z przeglądarek sieci Web dla MSAL.NET](msal-net-web-browsers.md) i aplikacji platformy [Xamarin Android](msal-net-system-browser-android-considerations.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="general-tips"></a>Porady ogólne

- Zaktualizuj istniejący pakiet NuGet MSAL.NET do [najnowszej wersji programu MSAL.NET](https://www.nuget.org/packages/Microsoft.Identity.Client/).
- Sprawdź, czy platforma Xamarin. Forms znajduje się w najnowszej wersji.
- Sprawdź, czy platforma Xamarin. Android. support. v4 znajduje się w najnowszej wersji.
- Upewnij się, że wszystkie pakiety platformy Xamarin. Android. Support są przeznaczone dla najnowszej wersji.
- Wyczyść lub ponownie skompiluj aplikację.
- W programie Visual Studio spróbuj ustawić maksymalną liczbę równoległych kompilacji projektu na **1**. W tym celu wybierz pozycję **Opcje**  >  **projekty i rozwiązania**  >  **Kompiluj i uruchom**  >  **maksymalną liczbę kompilacji projektów równoległych**.
- Jeśli tworzysz z wiersza polecenia i używasz polecenia `/m` , spróbuj usunąć ten element z polecenia.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Błąd: Nazwa AuthenticationContinuationHelper nie istnieje w bieżącym kontekście

Jeśli błąd wskazuje, że `AuthenticationContinuationHelper` nie istnieje w bieżącym kontekście, program Visual Studio mógł nieprawidłowo zaktualizować plik *Android. csproj \** . Czasami ścieżka pliku w `<HintPath>` elemencie nieprawidłowo zawiera `netstandard13` `monoandroid90` .

Ten przykład zawiera poprawną ścieżkę pliku:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz przykład [aplikacji mobilnej Xamarin korzystającej z platformy tożsamości firmy Microsoft](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations). Poniższa tabela zawiera podsumowanie odpowiednich informacji w pliku Readme.

| Przykład | Platforma | Opis |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin. iOS, Android, platformy UWP | Prosta aplikacja Xamarin. Forms, która pokazuje, jak używać MSAL do uwierzytelniania kont osobistych firmy Microsoft i usługi Azure AD za pomocą punktu końcowego usługi Azure AD 2,0. Aplikacja pokazuje również, jak uzyskać dostęp do Microsoft Graph i wyświetlić otrzymany token. <br>![Diagram przepływu uwierzytelniania](media/msal-net-xamarin-android-considerations/topology.png) |

<!-- REF LINKS -->
[PublicClientApplication]: /dotnet/api/microsoft.identity.client.publicclientapplication
[OnActivityResult]: /dotnet/api/android.app.activity.onactivityresult
[Activity]: /dotnet/api/android.app.activity
