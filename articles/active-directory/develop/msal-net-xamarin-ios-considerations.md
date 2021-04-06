---
title: Uwagi dotyczące platformy Xamarin iOS (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Informacje dotyczące korzystania z platformy Xamarin iOS z biblioteką uwierzytelniania firmy Microsoft dla programu .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 62eb4ab9eb6e4b0e7be0f7aadae1173950d21615
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98064491"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>Zagadnienia dotyczące korzystania z platformy Xamarin iOS z MSAL.NET

W przypadku korzystania z biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET) w systemie Xamarin iOS należy:

- Zastąp i zaimplementuj `OpenUrl` funkcję w `AppDelegate` .
- Włącz grupy pęku kluczy.
- Włącz udostępnianie pamięci podręcznej tokenu.
- Włącz dostęp do łańcucha kluczy.
- Poznaj znane problemy z systemami iOS 12 i iOS 13 oraz uwierzytelnianiem.

## <a name="implement-openurl"></a>Implementuj OpenUrl

Zastąp `OpenUrl` metodę `FormsApplicationDelegate` klasy pochodnej i wywołanie `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs` . Oto przykład:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Należy również wykonać następujące zadania:

* Zdefiniuj schemat identyfikatora URI przekierowania.
* Wymagaj uprawnień aplikacji do wywoływania innej aplikacji.
* Ma określony formularz dla identyfikatora URI przekierowania.
* [Zarejestruj identyfikator URI przekierowania](quickstart-register-app.md#add-a-redirect-uri) w Azure Portal.

### <a name="enable-keychain-access"></a>Włącz dostęp do łańcucha kluczy

Aby włączyć dostęp do łańcucha kluczy, upewnij się, że aplikacja ma grupę dostępu łańcucha kluczy. Grupę dostępu łańcucha kluczy można ustawić podczas tworzenia aplikacji przy użyciu `WithIosKeychainSecurityGroup()` interfejsu API.

Aby korzystać z pamięci podręcznej i logowania jednokrotnego, Ustaw grupę dostępu łańcucha kluczy na taką samą wartość we wszystkich aplikacjach.

Ten przykład instalacji używa MSAL 4. x:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Włącz również dostęp do łańcucha kluczy w `Entitlements.plist` pliku. Użyj jednej z następujących grup dostępu lub własnej grupy dostępu.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

W przypadku korzystania z `WithIosKeychainSecurityGroup()` interfejsu API program MSAL automatycznie dołącza grupę zabezpieczeń na końcu *identyfikatora zespołu* aplikacji ( `AppIdentifierPrefix` ). MSAL dodaje grupę zabezpieczeń, ponieważ w przypadku kompilowania aplikacji w Xcode zostanie ona taka sama. Dlatego uprawnienia w `Entitlements.plist` pliku muszą być dołączone `$(AppIdentifierPrefix)` przed grupą dostępu pęku kluczy.

Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą uprawnień systemu iOS](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps).

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Włączanie udostępniania pamięci podręcznej tokenów w aplikacjach dla systemu iOS

Począwszy od MSAL 2. x, można określić grupę dostępu łańcucha kluczy, aby zachować pamięć podręczną tokenów w wielu aplikacjach. To ustawienie umożliwia współużytkowanie pamięci podręcznej tokenów z kilkoma aplikacjami, które mają tę samą grupę dostępu pęku kluczy. Można udostępnić pamięć podręczną tokenów między aplikacjami [ADAL.NET](https://aka.ms/adal-net) , MSAL.NET aplikacje Xamarin. iOS i natywne aplikacje systemu iOS, które zostały opracowane w bibliotece [ADAL. objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) lub [MSAL. objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc).

Udostępniając pamięć podręczną tokenów, można zezwolić na logowanie jednokrotne między wszystkimi aplikacjami, które korzystają z tej samej grupy dostępu pęku kluczy.

Aby włączyć udostępnianie pamięci podręcznej, użyj `WithIosKeychainSecurityGroup()` metody w celu ustawienia grupy dostępu pęku kluczy na taką samą wartość we wszystkich aplikacjach, które współużytkują tę samą pamięć podręczną. Pierwszy przykład kodu w tym artykule pokazuje, jak używać metody.

Wcześniej w tym artykule przedstawiono, że MSAL dodaje się `$(AppIdentifierPrefix)` za każdym razem, gdy korzystasz z `WithIosKeychainSecurityGroup()` interfejsu API. MSAL dodaje ten element, ponieważ identyfikator zespołu `AppIdentifierPrefix` zapewnia, że tylko aplikacje utworzone przez tego samego wydawcę mogą udostępniać dostęp do łańcucha kluczy.

> [!NOTE]
> `KeychainSecurityGroup`Właściwość jest przestarzała. `iOSKeychainSecurityGroup`Zamiast tego użyj właściwości. `TeamId`Prefiks nie jest wymagany w przypadku użycia `iOSKeychainSecurityGroup` .

### <a name="use-microsoft-authenticator"></a>Użyj Microsoft Authenticator

Aplikacja może używać Microsoft Authenticator jako brokera do włączenia:

- **Logowanie jednokrotne**: po włączeniu rejestracji jednokrotnej użytkownicy nie muszą logować się do poszczególnych aplikacji.
- **Identyfikacja urządzenia**: Użyj identyfikacji urządzenia do uwierzytelniania, uzyskując dostęp do certyfikatu urządzenia. Ten certyfikat jest tworzony na urządzeniu, gdy jest on przyłączony do miejsca pracy. Aplikacja będzie gotowa, jeśli Administratorzy dzierżawy będą mogli korzystać z dostępu warunkowego związanego z urządzeniami.
- **Weryfikacja identyfikacji aplikacji**: gdy aplikacja wywołuje brokera, przekazuje adres URL przekierowania. Broker weryfikuje adres URL przekierowania.

Aby uzyskać szczegółowe informacje na temat włączania brokera, zobacz [używanie Microsoft Authenticator lub Microsoft Intune portal firmy w aplikacjach Xamarin iOS i Android](msal-net-use-brokers-with-xamarin-apps.md).

## <a name="known-issues-with-ios-12-and-authentication"></a>Znane problemy z systemem iOS 12 i uwierzytelnianiem

Firma Microsoft udostępniła [Poradnik zabezpieczeń](https://github.com/aspnet/AspNetCore/issues/4647) dotyczący niezgodności między systemem iOS 12 a niektórymi typami uwierzytelniania. Niezgodność powoduje uszkodzenie sieci społecznościowych, WSFed i OIDC. Poradnik zabezpieczeń pomaga zrozumieć, jak usunąć ograniczenia zabezpieczeń ASP.NET z aplikacji, aby zapewnić ich zgodność z systemem iOS 12.

Podczas tworzenia aplikacji MSAL.NET w systemie Xamarin iOS może wystąpić nieskończona pętla podczas próby zalogowania się do witryn sieci Web z systemu iOS 12. Takie zachowanie jest podobne do tego problemu ADAL w serwisie GitHub: [nieskończona pętla podczas próby zalogowania się do witryny sieci Web z systemu iOS 12 #1329](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329).

W systemie iOS 12 Safari można także zobaczyć przerwy w ASP.NET Core OIDC uwierzytelniania. Aby uzyskać więcej informacji, zobacz ten [problem z WebKit](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="known-issues-with-ios-13-and-authentication"></a>Znane problemy z systemem iOS 13 i uwierzytelnianiem

Jeśli aplikacja wymaga dostępu warunkowego lub uwierzytelniania certyfikatu, zezwól aplikacji na komunikację z aplikacją brokera Microsoft Authenticator. MSAL jest odpowiedzialny za obsługę żądań i odpowiedzi między aplikacją a Microsoft Authenticator.

W systemie iOS 13 firma Apple wprowadziła istotną zmianę interfejsu API, usuwając możliwość odczytywania aplikacji źródłowej podczas otrzymywania odpowiedzi z zewnętrznej aplikacji za pośrednictwem niestandardowych schematów adresów URL.

Dokumentacja firmy Apple dla Stanów [UIApplicationOpenURLOptionsSourceApplicationKey](https://developer.apple.com/documentation/uikit/uiapplicationopenurloptionssourceapplicationkey?language=objc) :

> *Jeśli żądanie pochodzi z innej aplikacji należącej do zespołu, strukturze UIKit ustawia wartość tego klucza na identyfikator tej aplikacji. Jeśli identyfikator zespołu aplikacji inicjującej różni się od identyfikatora zespołu bieżącej aplikacji, wartość klucza jest równa zero.*

Ta zmiana jest przerywana dla MSAL, ponieważ polega ona na `UIApplication.SharedApplication.OpenUrl` sprawdzeniu komunikacji między MSAL i aplikacją Microsoft Authenticator.

Ponadto w systemie iOS 13 deweloper jest zobowiązany do udostępnienia kontrolera prezentacji podczas korzystania z programu `ASWebAuthenticationSession` .

Jeśli tworzysz aplikację z Xcode 11 i używasz programu iOS Broker lub `ASWebAuthenticationSession` .

W takich przypadkach należy użyć [MSAL.NET 4.4.0 +](https://www.nuget.org/packages/Microsoft.Identity.Client/) , aby włączyć pomyślne uwierzytelnianie.

### <a name="additional-requirements"></a>Wymagania dodatkowe

- W przypadku korzystania z najnowszych bibliotek MSAL upewnij się, że na urządzeniu jest zainstalowana **Microsoft Authenticator wersja 6.3.19 +** .
- Podczas aktualizowania do MSAL.NET 4.4.0 + zaktualizuj `LSApplicationQueriesSchemes` plik w pliku *info. plist* i Dodaj `msauthv3` :

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

    Dodawanie `msauthv3` do pliku *info. plist* jest niezbędne w celu wykrycia obecności najnowszej aplikacji Microsoft Authenticator na urządzeniu obsługującym system iOS 13.

## <a name="report-an-issue"></a>Zgłaszanie problemu

Jeśli masz pytania lub chcesz zgłosić problem znaleziony w programie MSAL.NET, Otwórz w usłudze GitHub problem w repozytorium [AzureAD/Microsoft-Authentication-Library-for-dotnet](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues) .

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje o właściwościach programu Xamarin iOS, zobacz artykuł dotyczący [zagadnień specyficznych dla systemu iOS](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) w następującym przykładowym pliku Readme.MD:

Przykład | Platforma | Opis
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, platforma uniwersalna systemu Windows (platformy UWP) | Prosta aplikacja Xamarin Forms, która pokazuje, jak używać MSAL do uwierzytelniania kont osobistych firmy Microsoft i usługi Azure AD za pośrednictwem punktu końcowego usługi Azure AD 2,0. Aplikacja pokazuje również, jak używać tokenu uzyskanego do uzyskiwania dostępu do Microsoft Graph.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->
