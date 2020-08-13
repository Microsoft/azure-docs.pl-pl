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
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 73303d64d7ebeb94290819226e5e5944f95f6e42
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165706"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>Zagadnienia dotyczące korzystania z platformy Xamarin iOS z MSAL.NET
W przypadku korzystania z biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET) w systemie Xamarin iOS należy: 

- Zastąp i zaimplementuj `OpenUrl` funkcję w `AppDelegate` .
- Włącz grupy pęku kluczy.
- Włącz udostępnianie pamięci podręcznej tokenu.
- Włącz dostęp do łańcucha kluczy.
- Poznaj znane problemy z systemem iOS 12 i uwierzytelnianiem.

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
* Zdefiniuj schemat adresu URL.
* Wymagaj uprawnień aplikacji do wywoływania innej aplikacji.
* Ma określony formularz dla adresu URL przekierowania.
* Zarejestruj adres URL przekierowania w [Azure Portal](https://portal.azure.com).

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

Począwszy od MSAL 2. x, można określić grupę dostępu łańcucha kluczy, aby zachować pamięć podręczną tokenów w wielu aplikacjach. To ustawienie umożliwia współużytkowanie pamięci podręcznej tokenów z kilkoma aplikacjami, które mają tę samą grupę dostępu pęku kluczy. Można udostępniać tokeny w aplikacjach [ADAL.NET](https://aka.ms/adal-net) , MSAL.NET Xamarin. iOS oraz natywnych aplikacjach systemu iOS, które zostały opracowane w bibliotece [ADAL. objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) lub [MSAL. objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc).

Udostępniając pamięć podręczną tokenów, można zezwolić na logowanie jednokrotne między wszystkimi aplikacjami, które korzystają z tej samej grupy dostępu pęku kluczy.

Aby włączyć udostępnianie pamięci podręcznej, użyj `WithIosKeychainSecurityGroup()` metody w celu ustawienia grupy dostępu pęku kluczy na taką samą wartość we wszystkich aplikacjach, które współużytkują tę samą pamięć podręczną. Pierwszy przykład kodu w tym artykule pokazuje, jak używać metody.

Wcześniej w tym artykule przedstawiono, że MSAL dodaje się `$(AppIdentifierPrefix)` za każdym razem, gdy korzystasz z `WithIosKeychainSecurityGroup()` interfejsu API. MSAL dodaje ten element, ponieważ identyfikator zespołu `AppIdentifierPrefix` zapewnia, że tylko aplikacje utworzone przez tego samego wydawcę mogą udostępniać dostęp do łańcucha kluczy.

> [!NOTE]
> `KeychainSecurityGroup`Właściwość jest przestarzała.
> 
> Począwszy od MSAL 2. x, deweloperzy zostali zmuszeni do uwzględnienia `TeamId` prefiksu, gdy używają `KeychainSecurityGroup` właściwości. Ale począwszy od MSAL 2.7. x, podczas korzystania z nowej `iOSKeychainSecurityGroup` Właściwości MSAL rozpoznaje `TeamId` prefiks w czasie wykonywania. Gdy używasz tej właściwości, nie dołączaj `TeamId` prefiksu do wartości. Prefiks nie jest wymagany.
>
> Ponieważ `KeychainSecurityGroup` Właściwość jest przestarzała, użyj `iOSKeychainSecurityGroup` właściwości.

### <a name="use-microsoft-authenticator"></a>Użyj Microsoft Authenticator

Aplikacja może używać Microsoft Authenticator jako brokera do włączenia:

- **Logowanie jednokrotne**: po włączeniu rejestracji jednokrotnej użytkownicy nie muszą logować się do poszczególnych aplikacji.
- **Identyfikacja urządzenia**: Użyj identyfikacji urządzenia do uwierzytelniania, uzyskując dostęp do certyfikatu urządzenia. Ten certyfikat jest tworzony na urządzeniu, gdy jest on przyłączony do miejsca pracy. Aplikacja będzie gotowa, jeśli Administratorzy dzierżawy będą mogli korzystać z dostępu warunkowego związanego z urządzeniami.
- **Weryfikacja identyfikacji aplikacji**: gdy aplikacja wywołuje brokera, przekazuje adres URL przekierowania. Broker weryfikuje adres URL przekierowania.

Aby uzyskać szczegółowe informacje na temat włączania brokera, zobacz [używanie Microsoft Authenticator lub Microsoft Intune portal firmy w aplikacjach Xamarin iOS i Android](msal-net-use-brokers-with-xamarin-apps.md).

## <a name="known-issues-with-ios-12-and-authentication"></a>Znane problemy z systemem iOS 12 i uwierzytelnianiem
Firma Microsoft udostępniła [Poradnik zabezpieczeń](https://github.com/aspnet/AspNetCore/issues/4647) dotyczący niezgodności między systemem iOS 12 a niektórymi typami uwierzytelniania. Niezgodność powoduje uszkodzenie sieci społecznościowych, WSFed i OIDC. Poradnik zabezpieczeń pomaga deweloperom zrozumieć, jak usunąć ograniczenia zabezpieczeń ASP.NET z ich aplikacji, aby były zgodne z systemem iOS 12.  

Podczas tworzenia aplikacji MSAL.NET w systemie Xamarin iOS może wystąpić nieskończona pętla podczas próby zalogowania się do witryn sieci Web z systemu iOS 12. To zachowanie jest podobne do tego [problemu biblioteki ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

W systemie iOS 12 Safari można także zobaczyć przerwy w ASP.NET Core OIDC uwierzytelniania. Aby uzyskać więcej informacji, zobacz ten [problem z WebKit](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje o właściwościach programu Xamarin iOS, zobacz artykuł dotyczący [zagadnień specyficznych dla systemu iOS](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) w następującym przykładowym pliku Readme.MD:

Przykład | Platforma | Opis
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, platforma uniwersalna systemu Windows (platformy UWP) | Prosta aplikacja Xamarin Forms, która pokazuje, jak używać MSAL do uwierzytelniania kont osobistych firmy Microsoft i usługi Azure AD za pośrednictwem punktu końcowego usługi Azure AD 2,0. Aplikacja pokazuje również, jak używać tokenu uzyskanego do uzyskiwania dostępu do Microsoft Graph.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->