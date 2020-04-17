---
title: Używanie usługi MSAL w aplikacji chmury krajowej | Azure
titleSuffix: Microsoft identity platform
description: Biblioteka uwierzytelniania firmy Microsoft (MSAL) umożliwia deweloperom aplikacji nabywanie tokenów w celu wywołania zabezpieczonych interfejsów API sieci web. Te internetowe interfejsy API mogą być microsoft graph, inne interfejsy API firmy Microsoft, interfejsy API sieci web partnera lub własny interfejs API sieci web. MSAL obsługuje wiele architektur aplikacji i platform.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f3bb4dd1c564e5f6c4a8ee1bb5bf7424a74a339e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533993"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Używanie usługi MSAL w środowisku chmury krajowej

[Chmury krajowe](authentication-national-cloud.md), znane również jako suwerenne chmury, są fizycznie izolowanymi wystąpieniami platformy Azure. Te regiony platformy Azure pomagają upewnić się, że wymagania dotyczące rezydencji danych, suwerenności i zgodności są przestrzegane w granicach geograficznych.

Oprócz chmury microsoft na całym świecie, Biblioteka uwierzytelniania firmy Microsoft (MSAL) umożliwia deweloperom aplikacji w chmurach krajowych nabywanie tokenów w celu uwierzytelniania i wywoływania zabezpieczonych interfejsów API sieci Web. Te internetowe interfejsy API mogą być microsoft graph lub inne interfejsy API firmy Microsoft.

W tym chmury globalnej, usługa Azure Active Directory (Azure AD) jest wdrażana w następujących chmurach krajowych:  

- Azure Government
- Azure w Chinach — 21Vianet
- Azure (Niemcy)

W tym przewodniku pokazano, jak zalogować się do pracy i kont szkolnych, uzyskać token dostępu i wywołać interfejs API programu Microsoft Graph w środowisku [chmury azure dla instytucji rządowych.](https://azure.microsoft.com/global-infrastructure/government/)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że spełniasz te wymagania wstępne.

### <a name="choose-the-appropriate-identities"></a>Wybierz odpowiednie tożsamości

Aplikacje [platformy Azure dla](https://docs.microsoft.com/azure/azure-government/) instytucji rządowych mogą używać tożsamości usługi Azure AD dla instytucji rządowych i tożsamości publicznych usługi Azure AD do uwierzytelniania użytkowników. Ponieważ można użyć dowolnej z tych tożsamości, należy zdecydować, który punkt końcowy urzędu należy wybrać dla scenariusza:

- Usługa Azure AD Public: często używane, jeśli twoja organizacja ma już dzierżawy publicznej usługi Azure AD do obsługi usługi Office 365 (public lub GCC) lub innej aplikacji.
- Usługi Azure AD dla instytucji: często używane, jeśli twoja organizacja ma już dzierżawę usługi Azure AD DLA instytucji do obsługi usługi Office 365 (GCC High lub DoD) lub tworzy nową dzierżawę w usłudze Azure AD DLA INSTYTUCJI.

Po podjęciu decyzji szczególną uwagę należy zwrócić na miejsce rejestracji aplikacji. Jeśli wybierzesz tożsamości publiczne usługi Azure AD dla aplikacji platformy Azure dla instytucji rządowych, należy zarejestrować aplikację w dzierżawie publicznej usługi Azure AD.

### <a name="get-an-azure-government-subscription"></a>Uzyskaj subskrypcję platformy Azure dla instytucji rządowych

Aby uzyskać subskrypcję platformy Azure dla instytucji rządowych, zobacz [Zarządzanie subskrypcją i łączenie się z nimi w usłudze Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).

Jeśli nie masz subskrypcji platformy Azure dla instytucji rządowych, utwórz [bezpłatne konto](https://azure.microsoft.com/global-infrastructure/government/request/) przed rozpoczęciem.

Aby uzyskać szczegółowe informacje na temat korzystania z chmury krajowej z określonym językiem programowania, wybierz kartę pasującą do twojego języka:

## <a name="net"></a>[.NET](#tab/donet)

MSAL.NET można używać do logowania użytkowników, uzyskiwania tokenów i wywoływania interfejsu API programu Microsoft Graph w chmurach krajowych.

Poniższe samouczki pokazują, jak utworzyć aplikację sieci Web .NET Core 2.2 MVC. Aplikacja używa OpenID Connect do logowania użytkowników za pomocą konta służbowego w organizacji należącej do chmury krajowej.

- Aby zalogować się do użytkowników i uzyskać tokeny, wykonaj ten samouczek: [Tworzenie ASP.NET core aplikacji sieci Web logujących się użytkowników w suwerennych chmurach za pomocą platformy tożsamości firmy Microsoft](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Aby wywołać interfejs API programu Microsoft Graph, wykonaj ten samouczek: [Używanie platformy tożsamości firmy Microsoft do wywoływania interfejsu API programu Microsoft Graph z aplikacji sieci Web ASP.NET Core 2.x w imieniu użytkownika logującego się przy użyciu konta służbowego i szkolnego w chmurze głównej firmy Microsoft](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="javascript"></a>[JavaScript](#tab/javascript)

Aby włączyć aplikację MSAL.js dla suwerennych chmur:

### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.us/).

   Aby znaleźć punkty końcowe witryny Azure portal dla innych chmur krajowych, zobacz [Punkty końcowe rejestracji aplikacji](authentication-national-cloud.md#app-registration-endpoints).

1. Jeśli twoje konto daje dostęp do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu i ustaw sesję portalu na żądaną dzierżawę usługi Azure AD.
1. Przejdź do strony [Rejestracje aplikacji](https://aka.ms/ra/ff) na platformie tożsamości firmy Microsoft dla deweloperów.
1. Po wyświetleniu strony **Rejestrowanie aplikacji** wprowadź nazwę aplikacji.
1. W obszarze **Obsługiwane typy kont**wybierz pozycję **Konta w dowolnym katalogu organizacji**.
1. W sekcji **Przekierowanie identyfikatora URI** wybierz platformę **sieci Web** i ustaw wartość adresu URL aplikacji na podstawie serwera sieci Web. Zobacz następne sekcje, aby uzyskać instrukcje dotyczące ustawiania i uzyskiwania adresu URL przekierowania w programie Visual Studio i node.
1. Wybierz pozycję **Zarejestruj**.
1. Na stronie **Przegląd** aplikacji zanotuj wartość **Identyfikator aplikacji (klienta)**.
1. Ten samouczek wymaga włączenia [niejawnego przepływu dotacji](v2-oauth2-implicit-grant-flow.md). W lewym okienku zarejestrowanej aplikacji wybierz pozycję **Uwierzytelnianie**.
1. W **obszarze Ustawienia zaawansowane**w obszarze **Niejawne przyznawanie wybierz**pola wyboru **Tokeny identyfikatorów** i **Tokeny dostępu.** Tokeny identyfikatorów i tokeny dostępu są wymagane, ponieważ ta aplikacja musi zalogować się do użytkowników i wywołać interfejs API.
1. Wybierz pozycję **Zapisz**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Krok 2: Konfigurowanie serwera www lub projektu

- [Pobierz pliki projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) dla lokalnego serwera sieci web, takiego jak Node.

  lub

- [Pobierz projekt programu Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Następnie przejdź do [konfigurowania środowiska JavaScript SPA,](#step-4-configure-your-javascript-spa) aby skonfigurować przykład kodu przed jego uruchomieniem.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Krok 3: Logowanie użytkownika za pomocą biblioteki uwierzytelniania firmy Microsoft

Wykonaj kroki opisane w [samouczku JavaScript,](tutorial-v2-javascript-spa.md#create-your-project) aby utworzyć projekt i zintegrować się z msal, aby zalogować się do użytkownika.

### <a name="step-4-configure-your-javascript-spa"></a>Krok 4: Konfigurowanie środowiska JavaScript SPA

W `index.html` pliku utworzonym podczas konfigurowania projektu dodaj informacje o rejestracji aplikacji. Dodaj następujący kod u góry `<script></script>` w tagach w `index.html` treści pliku:

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

W tym kodzie:

- `Enter_the_Application_Id_here`jest wartością **identyfikatora aplikacji (klienta)** dla zarejestrowanej aplikacji.
- `Enter_the_Tenant_Info_Here`jest ustawiona na jedną z następujących opcji:
    - Jeśli aplikacja obsługuje **konta w tym katalogu organizacyjnym,** zastąp tę wartość identyfikatorem dzierżawy lub nazwą dzierżawy (na przykład contoso.microsoft.com).
    - Jeśli aplikacja obsługuje **konta w dowolnym katalogu organizacyjnym,** zastąp `organizations`tę wartość .

    Aby znaleźć punkty końcowe uwierzytelniania dla wszystkich chmur krajowych, zobacz [Punkty końcowe uwierzytelniania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Osobiste konta Microsoft nie są obsługiwane w chmurach krajowych.

- `graphEndpoint`to punkt końcowy programu Microsoft Graph dla chmury microsoft dla instytucji rządowych w Stanach Zjednoczonych.

   Aby znaleźć punkty końcowe programu Microsoft Graph dla wszystkich chmur krajowych, zobacz [Punkty końcowe programu Microsoft Graph w chmurach krajowych](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="python"></a>[Python](#tab/python)

Aby włączyć aplikację MSAL Python dla suwerennych chmur:

- Zarejestruj aplikację w określonym portalu, w zależności od chmury. Aby uzyskać więcej informacji na temat wyboru portalu, zapoznaj się z [punktami końcowymi rejestracji aplikacji](authentication-national-cloud.md#app-registration-endpoints)
- Użyj dowolnego z próbek z repozytorium z kilkoma [zmianami](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) w konfiguracji, w zależności od chmury, która jest wymieniona dalej.
- Użyj określonego urzędu, w zależności od chmury, w której aplikacja została zarejestrowana. Aby uzyskać więcej informacji na temat urzędów dla różnych chmur, zapoznaj się z [punktami końcowymi uwierzytelniania usługi Azure AD](authentication-national-cloud.md#azure-ad-authentication-endpoints).

    Oto przykładowy autorytet:

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```

- Aby wywołać wykres firmy Microsoft wymaga określonego adresu URL punktu końcowego wykresu, który zależy od chmury, której używasz. Aby znaleźć punkty końcowe programu Microsoft Graph dla wszystkich chmur krajowych, zobacz [główne punkty końcowe usługi Microsoft Graph i Graph Explorer](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

    Oto przykład punktu końcowego wykresu z zakresem:

    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```

## <a name="java"></a>[Java](#tab/java)

Aby włączyć aplikację MSAL dla języka Java dla suwerennych chmur:

- Zarejestruj aplikację w określonym portalu, w zależności od chmury. Aby uzyskać więcej informacji na temat wyboru portalu, zapoznaj się z [punktami końcowymi rejestracji aplikacji](authentication-national-cloud.md#app-registration-endpoints)
- Użyj dowolnego z próbek z repozytorium z kilkoma [zmianami](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) w konfiguracji, w zależności od chmury, które są wymienione dalej.
- Użyj określonego urzędu, w zależności od chmury, w której aplikacja została zarejestrowana. Aby uzyskać więcej informacji na temat urzędów dla różnych chmur, zapoznaj się z [punktami końcowymi uwierzytelniania usługi Azure AD](authentication-national-cloud.md#azure-ad-authentication-endpoints).

Oto przykładowy autorytet:

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- Aby wywołać wykres firmy Microsoft wymaga określonego adresu URL punktu końcowego wykresu, który zależy od chmury, której używasz. Aby znaleźć punkty końcowe programu Microsoft Graph dla wszystkich chmur krajowych, zobacz [główne punkty końcowe usługi Microsoft Graph i Graph Explorer](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

Oto przykład punktu końcowego wykresu z zakresem:

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-c"></a>[Obiektowy C](#tab/objc)

MsAL dla systemu iOS i macOS może służyć do pozyskiwania tokenów w `MSALPublicClientApplication`chmurach krajowych, ale wymaga dodatkowej konfiguracji podczas tworzenia .

Na przykład, jeśli chcesz, aby aplikacja była aplikacją wielodostępną w chmurze krajowej (w tym przypadku rząd STANÓW Zjednoczonych), możesz napisać:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

## <a name="swift"></a>[Swift](#tab/swift)

MsAL dla systemu iOS i macOS może służyć do pozyskiwania tokenów w `MSALPublicClientApplication`chmurach krajowych, ale wymaga dodatkowej konfiguracji podczas tworzenia .

Na przykład, jeśli chcesz, aby aplikacja była aplikacją wielodostępną w chmurze krajowej (w tym przypadku rząd STANÓW Zjednoczonych), możesz napisać:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usługach:

- [Uwierzytelnianie w chmurach krajowych](authentication-national-cloud.md)
- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure w Chinach — 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure (Niemcy)](https://docs.microsoft.com/azure/germany/)
