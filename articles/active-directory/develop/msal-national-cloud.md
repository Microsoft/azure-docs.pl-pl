---
title: Korzystanie z MSAL w aplikacji w chmurze krajowej | Azure
titleSuffix: Microsoft identity platform
description: Biblioteka Microsoft Authentication Library (MSAL) umożliwia deweloperom aplikacji uzyskanie tokenów w celu wywołania zabezpieczonych interfejsów API sieci Web. Te interfejsy API sieci Web mogą być Microsoft Graph, inne interfejsy API firmy Microsoft, interfejsy API sieci Web partnera lub własny internetowy interfejs API. MSAL obsługuje wiele architektur aplikacji i platform.
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
ms.openlocfilehash: 566c429fc70ae3f0fc18f024b745d8142535932c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348411"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Korzystanie z MSAL w środowisku chmury krajowej

[Chmury narodowe](authentication-national-cloud.md), znane również jako suwerenne chmury, to fizyczne izolowane wystąpienia platformy Azure. Te regiony pomocy systemu Azure zapewniają, że wymagania dotyczące miejsca zamieszkania, suwerenności i zgodności są honorowane w granicach geograficznych.

Oprócz chmury Microsoft Worldwide Library, biblioteka uwierzytelniania firmy Microsoft (MSAL) umożliwia deweloperom aplikacji w chmurach narodowych uzyskanie tokenów w celu uwierzytelniania i wywoływania zabezpieczonych interfejsów API sieci Web. Te interfejsy API sieci Web mogą być Microsoft Graph lub innych interfejsów API firmy Microsoft.

W tym chmurę globalną usługa Azure Active Directory (Azure AD) jest wdrażana w następujących chmurach narodowych:  

- Azure Government
- Azure w Chinach — 21Vianet
- Azure (Niemcy)

W tym przewodniku pokazano, jak zalogować się do kont służbowych, uzyskać token dostępu i wywołać interfejs API Microsoft Graph w środowisku [chmury Azure Government](https://azure.microsoft.com/global-infrastructure/government/) .

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że spełniono te wymagania wstępne.

### <a name="choose-the-appropriate-identities"></a>Wybierz odpowiednie tożsamości

Aplikacje [Azure Government](../../azure-government/index.yml) mogą uwierzytelniać użytkowników przy użyciu tożsamości instytucji rządowych usługi Azure AD i publicznych tożsamości usługi Azure AD. Ponieważ można użyć dowolnej z tych tożsamości, należy zdecydować, który punkt końcowy urzędu należy wybrać dla danego scenariusza:

- Publiczne usługi Azure AD: często używane, jeśli organizacja ma już dzierżawę publiczną usługi Azure AD do obsługi Microsoft 365 (publiczna lub w zatoce) lub innej aplikacji.
- Usługa Azure AD dla instytucji rządowych: często używane, jeśli organizacja ma już dzierżawę programu Azure AD dla instytucji rządowych, która obsługuje pakiet Office 365 (w serwisie lub wyższej), lub tworzy nową dzierżawę w usłudze Azure AD dla instytucji rządowych.

Po podjęciu decyzji, szczególnym zagadnieniem jest przeprowadzenie rejestracji aplikacji. Jeśli wybierzesz tożsamość publiczną usługi Azure AD dla aplikacji Azure Government, musisz zarejestrować aplikację w dzierżawie publicznej usługi Azure AD.

### <a name="get-an-azure-government-subscription"></a>Pobierz subskrypcję usługi Azure Government

Aby uzyskać subskrypcję Azure Government, zobacz [Zarządzanie subskrypcją w programie Azure Government i nawiązywanie z nią połączenia](../../azure-government/compare-azure-government-global-azure.md).

Jeśli nie masz subskrypcji Azure Government, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/global-infrastructure/government/request/) .

Aby uzyskać szczegółowe informacje na temat korzystania z chmury krajowej z określonym językiem programowania, wybierz kartę pasującą do języka:

## <a name="net"></a>[.NET](#tab/dotnet)

Możesz użyć MSAL.NET, aby zalogować użytkowników, uzyskać tokeny i wywołać interfejs API Microsoft Graph w chmurach krajowych.

W poniższych samouczkach pokazano, jak utworzyć aplikację sieci Web platformy .NET Core 2,2 MVC. Aplikacja używa usługi OpenID Connect Connect do logowania użytkowników przy użyciu konta służbowego w organizacji, która należy do chmury krajowej.

- Aby zalogować użytkowników i uzyskać tokeny, postępuj zgodnie z tym samouczkiem: [ASP.NET Core Tworzenie aplikacji sieci Web służącej do logowania w chmurach suwerennych za pomocą platformy tożsamości firmy Microsoft](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Aby wywołać interfejs API Microsoft Graph, wykonaj czynności opisane w tym samouczku: [Korzystanie z platformy tożsamości firmy Microsoft do wywoływania interfejsu API Microsoft Graph z poziomu aplikacji internetowej ASP.NET Core 2. x w imieniu logowania użytkownika przy użyciu konta służbowego w chmurze krajowej firmy Microsoft](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="javascript"></a>[JavaScript](#tab/javascript)

Aby włączyć aplikację MSAL.js dla suwerennych chmur:

### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.us/).

   Aby znaleźć Azure Portal punkty końcowe dla innych chmur narodowych, zobacz [punkty końcowe rejestracji aplikacji](authentication-national-cloud.md#app-registration-endpoints).

1. Jeśli Twoje konto zapewnia dostęp do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu, a następnie ustaw sesję portalu na żądaną dzierżawę usługi Azure AD.
1. Przejdź do strony [rejestracje aplikacji](https://aka.ms/ra/ff) na platformie tożsamości firmy Microsoft dla deweloperów.
1. Po wyświetleniu strony **Rejestrowanie aplikacji** wprowadź nazwę aplikacji.
1. W obszarze **obsługiwane typy kont** wybierz pozycję **konta w dowolnym katalogu organizacyjnym**.
1. W sekcji **Identyfikator URI przekierowania** Wybierz platformę **sieci Web** i ustaw wartość na adres URL aplikacji na podstawie serwera sieci Web. Zobacz następne sekcje, aby uzyskać instrukcje dotyczące ustawiania i uzyskiwania adresu URL przekierowania w programie Visual Studio i węźle.
1. Wybierz pozycję **Zarejestruj**.
1. Na stronie **Przegląd** aplikacji zanotuj wartość **Identyfikator aplikacji (klienta)**.
1. Ten samouczek wymaga włączenia [niejawnego przepływu dotacji](v2-oauth2-implicit-grant-flow.md). W lewym okienku zarejestrowanej aplikacji wybierz pozycję **uwierzytelnianie**.
1. W obszarze **Ustawienia zaawansowane** w obszarze **niejawne przyznanie** zaznacz pola wyboru **tokeny identyfikatorów** i **tokeny dostępu** . Tokeny identyfikatorów i tokeny dostępu są wymagane, ponieważ ta aplikacja musi zalogować użytkowników i wywołać interfejs API.
1. Wybierz pozycję **Zapisz**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Krok 2. Konfigurowanie serwera lub projektu sieci Web

- [Pobierz pliki projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) dla lokalnego serwera sieci Web, na przykład węzeł.

  lub

- [Pobierz projekt programu Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Następnie pominięcie, aby [skonfigurować swój Spa JavaScript](#step-4-configure-your-javascript-spa) w celu skonfigurowania przykładu kodu przed jego uruchomieniem.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Krok 3. zalogowanie użytkownika przy użyciu biblioteki uwierzytelniania firmy Microsoft

Wykonaj kroki opisane w [samouczku języka JavaScript](tutorial-v2-javascript-spa.md#create-your-project) , aby utworzyć projekt i zintegrować go z MSAL, aby zalogować użytkownika.

### <a name="step-4-configure-your-javascript-spa"></a>Krok 4. Konfigurowanie protokołu JavaScript SPA

W `index.html` pliku utworzonym podczas instalacji projektu Dodaj informacje o rejestracji aplikacji. Dodaj następujący kod w górnej części `<script></script>` tagów w treści `index.html` pliku:

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

- `Enter_the_Application_Id_here` jest wartością **identyfikatora aplikacji (klienta)** dla zarejestrowanej aplikacji.
- `Enter_the_Tenant_Info_Here` jest ustawiona na jedną z następujących opcji:
    - Jeśli aplikacja obsługuje **konta w tym katalogu organizacyjnym**, Zastąp tę wartość identyfikatorem dzierżawy lub nazwą dzierżawy (na przykład contoso.Microsoft.com).
    - Jeśli aplikacja obsługuje **konta w dowolnym katalogu organizacyjnym**, Zastąp tę wartość wartością `organizations` .

    Aby znaleźć punkty końcowe uwierzytelniania dla wszystkich chmur narodowych, zobacz [punkty końcowe uwierzytelniania usługi Azure AD](./authentication-national-cloud.md#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Osobiste konta Microsoft nie są obsługiwane w chmurach krajowych.

- `graphEndpoint` jest punktem końcowym Microsoft Graph w chmurze firmy Microsoft dla instytucji rządowych USA.

   Aby znaleźć Microsoft Graph punkty końcowe dla wszystkich chmur narodowych, zobacz [Microsoft Graph punktów końcowych w chmurach krajowych](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="python"></a>[Python](#tab/python)

Aby włączyć aplikację MSAL języka Python dla suwerennych chmur:

- Zarejestruj swoją aplikację w określonym portalu, w zależności od chmury. Aby uzyskać więcej informacji na temat wybierania portalu odwołań do [punktów końcowych rejestracji aplikacji](authentication-national-cloud.md#app-registration-endpoints)
- Użyj dowolnych [przykładów](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) z repozytorium z kilkoma zmianami w konfiguracji, w zależności od chmury, która jest wspomniana dalej.
- Użyj określonego urzędu w zależności od chmury, w której zarejestrowano aplikację. Aby uzyskać więcej informacji na temat urzędów dla różnych chmur, odwołaj się do [punktów końcowych uwierzytelniania usługi Azure AD](authentication-national-cloud.md#azure-ad-authentication-endpoints).

    Oto przykładowa władza:

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```

- Aby wywołać program Microsoft Graph, wymagany jest konkretny adres URL punktu końcowego grafu, który zależy od używanej chmury. Aby znaleźć Microsoft Graph punkty końcowe dla wszystkich chmur narodowych, zapoznaj się z [głównymi punktami końcowymi usługi Microsoft Graph i Eksplorator grafów](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

    Oto przykład punktu końcowego grafu z zakresem:

    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```

## <a name="java"></a>[Java](#tab/java)

Aby włączyć MSAL dla aplikacji Java dla suwerennych chmur:

- Zarejestruj swoją aplikację w określonym portalu, w zależności od chmury. Aby uzyskać więcej informacji na temat wybierania portalu odwołań do [punktów końcowych rejestracji aplikacji](authentication-national-cloud.md#app-registration-endpoints)
- Użyj dowolnych [przykładów](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) z repozytorium z kilkoma zmianami w konfiguracji, w zależności od chmury, które są wymienione dalej.
- Użyj określonego urzędu w zależności od chmury, w której zarejestrowano aplikację. Aby uzyskać więcej informacji na temat urzędów dla różnych chmur, odwołaj się do [punktów końcowych uwierzytelniania usługi Azure AD](authentication-national-cloud.md#azure-ad-authentication-endpoints).

Oto przykładowa władza:

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- Aby wywołać program Microsoft Graph, wymagany jest konkretny adres URL punktu końcowego grafu, który zależy od używanej chmury. Aby znaleźć Microsoft Graph punkty końcowe dla wszystkich chmur narodowych, zapoznaj się z [głównymi punktami końcowymi usługi Microsoft Graph i Eksplorator grafów](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

Oto przykład punktu końcowego grafu z zakresem:

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-c"></a>[Objective-C](#tab/objc)

MSAL dla systemów iOS i macOS mogą być używane do uzyskiwania tokenów w chmurach narodowych, ale wymaga dodatkowej konfiguracji podczas tworzenia `MSALPublicClientApplication` .

Na przykład jeśli chcesz, aby aplikacja była aplikacją wielodostępną w chmurze krajowej (w tym przypadku dla instytucji rządowych USA), możesz napisać:

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

MSAL dla systemów iOS i macOS mogą być używane do uzyskiwania tokenów w chmurach narodowych, ale wymaga dodatkowej konfiguracji podczas tworzenia `MSALPublicClientApplication` .

Na przykład jeśli chcesz, aby aplikacja była aplikacją wielodostępną w chmurze krajowej (w tym przypadku dla instytucji rządowych USA), możesz napisać:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej:

- [Uwierzytelnianie w chmurach narodowych](authentication-national-cloud.md)
- [Azure Government](../../azure-government/index.yml)
- [Azure w Chinach — 21Vianet](/azure/china/)
- [Azure (Niemcy)](../../germany/index.yml)