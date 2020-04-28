---
title: Uwagi dotyczące platformy Xamarin Android (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Poznaj zagadnienia dotyczące korzystania z platformy Xamarin Android z biblioteką uwierzytelniania firmy Microsoft dla programu .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81b55253d757f641979c6f72001803d7d38d9af3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77132515"
---
# <a name="considerations-for-using-xamarin-android-with-msalnet"></a>Zagadnienia dotyczące korzystania z platformy Xamarin Android z MSAL.NET
W tym artykule omówiono zagadnienia, które należy wziąć pod uwagę podczas korzystania z platformy Xamarin Android z biblioteką uwierzytelniania firmy Microsoft dla programu .NET (MSAL.NET).

## <a name="set-the-parent-activity"></a>Ustawianie działania nadrzędnego

W programie Xamarin Android Ustaw działanie nadrzędne, tak aby token zwracał się po interakcji. Oto przykład kodu:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

W MSAL 4,2 i nowszych można również ustawić tę funkcję na poziomie `PublicClientApplication`. Aby to zrobić, użyj wywołania zwrotnego:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Jeśli używasz [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin), kod `PublicClientApplication` konstruktora wygląda podobnie do poniższego przykładu.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Upewnij się, że kontrolka powraca do MSAL 
Po zakończeniu interaktywnej części przepływu uwierzytelniania upewnij się, że formant wraca do MSAL. W systemie Android Zastąp `OnActivityResult` metodę `Activity`. Następnie Wywołaj `SetAuthenticationContinuationEventArgs` metodę klasy `AuthenticationContinuationHelper` MSAL. 

Przykład:

```csharp
protected override void OnActivityResult(int requestCode, 
                                         Result resultCode, Intent data)
{
 base.OnActivityResult(requestCode, resultCode, data);
 AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                         resultCode,
                                                                         data);
}

```

Ten wiersz zapewnia, że kontrolka powraca do MSAL na końcu interaktywnej części przepływu uwierzytelniania.

## <a name="update-the-android-manifest"></a>Aktualizowanie manifestu systemu Android
Plik *pliku AndroidManifest. XML* powinien zawierać następujące wartości:

<!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
```
  <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
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

Zastąp nazwę pakietu zarejestrowanego w Azure Portal `android:host=` wartości. Zastąp skrót klucza, który został zarejestrowany w Azure Portal dla tej `android:path=` wartości. Wartość skrótu podpisu *nie* powinna być zakodowana w adresie URL. Upewnij się, że na początku skrótu`/`podpisu zostanie wyświetlony wiodący ukośnik ().

Alternatywnie możesz [utworzyć działanie w kodzie](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) zamiast ręcznie edytować *pliku AndroidManifest. XML*. Aby utworzyć działanie w kodzie, najpierw Utwórz klasę, która zawiera `Activity` atrybut i `IntentFilter` atrybut. 

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

### <a name="xamarinforms-43x-manifest"></a>Manifest platformy Xamarin. Forms 4.3. X

Xamarin. Forms 4.3. x generuje kod, który ustawia `package` atrybut na `com.companyname.{appName}` w *pliku AndroidManifest. XML*. Jeśli używasz `DataScheme` jako, `msal{client_id}`możesz chcieć zmienić wartość tak, aby odpowiadała wartości `MainActivity.cs` przestrzeni nazw.

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


## <a name="troubleshoot"></a>Rozwiązywanie problemów
Można utworzyć nową aplikację platformy Xamarin. Forms i dodać odwołanie do pakietu NuGet MSAL.NET.
Ale mogą wystąpić problemy z kompilacją w przypadku uaktualnienia istniejącej aplikacji platformy Xamarin. Forms do wersji zapoznawczej 1.1.2 lub nowszej.

Aby rozwiązać problemy z kompilacją:

- Zaktualizuj istniejący pakiet NuGet MSAL.NET do wersji MSAL.NET Preview 1.1.2 lub nowszej.
- Sprawdź, czy platforma Xamarin. Forms została automatycznie zaktualizowana do wersji 2.5.0.122203. W razie potrzeby zaktualizuj program Xamarin. Forms do tej wersji.
- Sprawdź, czy platforma Xamarin. Android. support. v4 została automatycznie zaktualizowana do wersji 25.4.0.2. W razie potrzeby zaktualizuj do wersji 25.4.0.2.
- Upewnij się, że wszystkie pakiety platformy Xamarin. Android. Support mają wersję docelową 25.4.0.2.
- Wyczyść lub ponownie skompiluj aplikację.
- W programie Visual Studio spróbuj ustawić maksymalną liczbę równoległych kompilacji projektu na 1. W tym celu wybierz pozycję **Opcje** > **projekty i rozwiązania** > **Kompiluj i uruchom** > **maksymalną liczbę kompilacji projektów równoległych**.
- Jeśli tworzysz z wiersza polecenia i używasz polecenia `/m`, spróbuj usunąć ten element z polecenia.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Błąd: Nazwa AuthenticationContinuationHelper nie istnieje w bieżącym kontekście

Jeśli błąd wskazuje, że `AuthenticationContinuationHelper` nie istnieje w bieżącym kontekście, program Visual Studio mógł nieprawidłowo zaktualizować plik Android. csproj *. Czasami ścieżka pliku * \<HintPath>* nieprawidłowo zawiera *netstandard13* zamiast *monoandroid90*.

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
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin. iOS, Android, platformy UWP | Prosta aplikacja Xamarin. Forms, która pokazuje, jak używać MSAL do uwierzytelniania kont osobistych firmy Microsoft i usługi Azure AD za pomocą punktu końcowego usługi Azure AD 2,0. Aplikacja pokazuje również, jak uzyskać dostęp do Microsoft Graph i wyświetlić otrzymany token. <br>![Topologia](media/msal-net-xamarin-android-considerations/topology.png) |
