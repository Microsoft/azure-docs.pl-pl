---
title: Konfigurowanie dostawców tożsamości (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak używać różnych urzędów, takich jak B2C, suwerenne chmury i użytkownicy-Goście, z MSAL dla systemów iOS i macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: d8a176fff0da932d0fafd40b9ab895b635acc5f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96169447"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>Instrukcje: Konfigurowanie MSAL dla systemów iOS i macOS w celu korzystania z różnych dostawców tożsamości

W tym artykule opisano sposób konfigurowania aplikacji biblioteki uwierzytelniania firmy Microsoft dla systemów iOS i macOS (MSAL) dla różnych urzędów, takich jak Azure Active Directory (Azure AD), typu Business-to-Consumer (B2C), suwerennych chmur i użytkowników-Gości.  W tym artykule można ogólnie traktować urząd jako dostawcę tożsamości.

## <a name="default-authority-configuration"></a>Domyślna konfiguracja urzędu

`MSALPublicClientApplication` jest skonfigurowany przy użyciu domyślnego adresu URL urzędu `https://login.microsoftonline.com/common` , który jest odpowiedni dla większości scenariuszy Azure Active Directory (AAD). Chyba że wdrażasz zaawansowane scenariusze, takie jak chmury krajowe, lub pracujesz z programem B2C, nie musisz go zmieniać.

> [!NOTE]
> Nowoczesne uwierzytelnianie z Active Directory Federation Services jako dostawca tożsamości (ADFS) nie jest obsługiwane (szczegółowe informacje można znaleźć [w usługach ADFS dla deweloperów](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios) ). Usługi ADFS są obsługiwane za poorednictwem Federacji.

## <a name="change-the-default-authority"></a>Zmiana domyślnego urzędu

W niektórych scenariuszach, takich jak Business-to-Consumer (B2C), może zajść potrzeba zmiany domyślnego urzędu.

### <a name="b2c"></a>B2C

Aby można było korzystać z B2C, [Biblioteka uwierzytelniania firmy Microsoft (MSAL)](reference-v2-libraries.md) wymaga innej konfiguracji urzędu certyfikacji. MSAL rozpoznaje jeden format adresu URL urzędu jako B2C. Rozpoznany format B2Cego urzędu certyfikacji jest `https://<host>/tfp/<tenant>/<policy>` na przykład `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy` . Można jednak również użyć innych obsługiwanych adresów URL urzędu certyfikacji B2C przez zadeklarowanie urzędu jako urzędu certyfikacji B2C.

Aby zapewnić obsługę dowolnego formatu adresu URL dla B2C, `MSALB2CAuthority` można ustawić dowolny adres URL, na przykład:

Obiektowy C
```objc
NSURL *authorityURL = [NSURL URLWithString:@"arbitrary URL"];
MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
```
Swift
```swift
guard let authorityURL = URL(string: "arbitrary URL") else {
    // Handle error
    return
}
let b2cAuthority = try MSALB2CAuthority(url: authorityURL)
```

Wszystkie urzędy B2C, które nie korzystają z domyślnego formatu urzędu B2C, muszą być zadeklarowane jako znane urzędy.

Każdy inny urząd B2C należy dodać do listy znanych urzędów, nawet jeśli urzędy różnią się w zasadzie.

Obiektowy C
```objc
MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:b2cAuthority];
b2cApplicationConfig.knownAuthorities = @[b2cAuthority];
```
Swift
```swift
let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)
b2cApplicationConfig.knownAuthorities = [b2cAuthority]
```

Gdy aplikacja zażąda nowych zasad, należy zmienić adres URL urzędu, ponieważ adres URL urzędu jest inny dla każdej zasady. 

Aby skonfigurować aplikację B2C, `@property MSALAuthority *authority` przed utworzeniem należy ustawić wystąpienie elementu `MSALB2CAuthority` in `MSALPublicClientApplicationConfig` `MSALPublicClientApplication` , jak to:

Obiektowy C
```ObjC
    // Create B2C authority URL
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy"];
    
    MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
    if (!b2cAuthority)
    {
        // Handle error
        return;
    }
    
    // Create MSALPublicClientApplication configuration
    MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                                   initWithClientId:@"your-client-id"
                                                                   redirectUri:@"your-redirect-uri"
                                                                   authority:b2cAuthority];

    // Initialize MSALPublicClientApplication
    MSALPublicClientApplication *b2cApplication =
    [[MSALPublicClientApplication alloc] initWithConfiguration:b2cApplicationConfig error:&error];
    
    if (!b2cApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    // Create B2C authority URL
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy") else {
        // Handle error
        return
    }
    let b2cAuthority = try MSALB2CAuthority(url: authorityURL)

    // Create MSALPublicClientApplication configuration
    let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)

    // Initialize MSALPublicClientApplication
    let b2cApplication = try MSALPublicClientApplication(configuration: b2cApplicationConfig)
} catch {
    // Handle error
}
```

### <a name="sovereign-clouds"></a>Suwerenne chmury

Jeśli aplikacja jest uruchomiona w chmurze suwerennej, może zajść konieczność zmiany adresu URL urzędu w `MSALPublicClientApplication` . Poniższy przykład ustawia adres URL urzędu do pracy z niemiecką chmurą AAD:

Obiektowy C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.de/common"];
    MSALAuthority *sovereignAuthority = [MSALAuthority authorityWithURL:authorityURL error:&authorityError];
    
    if (!sovereignAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:sovereignAuthority];
    
    
    MSALPublicClientApplication *sovereignApplication = [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    
    if (!sovereignApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.de/common") else {
        //Handle error
        return
    }
    let sovereignAuthority = try MSALAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: sovereignAuthority)
            
    let sovereignApplication = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

Może być konieczne przekazanie różnych zakresów do poszczególnych suwerennych chmur. Zakresy do wysłania zależą od zasobu, którego używasz. Na przykład możesz użyć `"https://graph.microsoft.com/user.read"` w chmurze ogólnoświatowej i `"https://graph.microsoft.de/user.read"` w chmurze niemieckiej.

### <a name="signing-a-user-into-a-specific-tenant"></a>Podpisywanie użytkownika do określonej dzierżawy

Jeśli adres URL urzędu jest ustawiony na `"login.microsoftonline.com/common"` , użytkownik zostanie zalogowany do swojej dzierżawy domowej. Niektóre aplikacje mogą jednak wymagać podpisania użytkownika w innej dzierżawie, a niektóre aplikacje współpracują tylko z jedną dzierżawą.

Aby podpisać użytkownika w określonej dzierżawie, należy skonfigurować `MSALPublicClientApplication` z określonym Urzędem. Na przykład:

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

Poniżej pokazano, jak podpisać użytkownika w określonej dzierżawie:

Obiektowy C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4"];
    MSALAADAuthority *tenantedAuthority = [[MSALAADAuthority alloc] initWithURL:authorityURL error:&authorityError];
    
    if (!tenantedAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:tenantedAuthority];
    
    MSALPublicClientApplication *application =
    [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    if (!application)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4") else {
        //Handle error
        return
    }    
    let tenantedAuthority = try MSALAADAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: tenantedAuthority)
            
    let application = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

## <a name="supported-authorities"></a>Obsługiwane urzędy

### <a name="msalauthority"></a>MSALAuthority

`MSALAuthority`Klasa jest podstawową klasą abstrakcyjną dla klas urzędu MSAL. Nie próbuj tworzyć wystąpienia elementu przy użyciu `alloc` lub `new` . Zamiast tego należy utworzyć jedną z jej podklas bezpośrednio ( `MSALAADAuthority` , `MSALB2CAuthority` ) lub użyć metody fabryki `authorityWithURL:error:` do utworzenia podklas przy użyciu adresu URL urzędu.

Użyj `url` właściwości, aby uzyskać znormalizowany adres URL urzędu. Dodatkowe parametry i składniki ścieżki lub fragmenty, które nie są częścią urzędu, nie będą znajdować się w zwracanym znormalizowanym adresie URL.

Poniżej znajdują się klasy `MSALAuthority` , które można utworzyć w zależności od urzędu, którego chcesz użyć.

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority` reprezentuje urząd usługi AAD. Adres URL urzędu powinien mieć następujący format, gdzie `<port>` jest opcjonalny: `https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CAuthority

`MSALB2CAuthority` reprezentuje urząd B2C. Domyślnie adres URL urzędu B2C powinien mieć następujący format, gdzie `<port>` jest opcjonalny: `https://<host>:<port>/tfp/<tenant>/<policy>` . Jednak MSAL obsługuje również inne B2Ce formaty urzędów certyfikacji.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [przepływów uwierzytelniania i scenariuszy aplikacji](authentication-flows-app-scenarios.md)