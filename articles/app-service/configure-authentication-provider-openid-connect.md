---
title: Konfigurowanie dostawcy połączenia OpenID Connect (wersja zapoznawcza)
description: Dowiedz się, jak skonfigurować dostawcę połączenia usługi OpenID Connect jako dostawcę tożsamości dla aplikacji App Service lub Azure Functions.
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.openlocfilehash: e8112f2dc20175e81cfa8388440b2d9aef6a419c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983873"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-login-using-an-openid-connect-provider-preview"></a>Konfigurowanie aplikacji usługi App Service lub Azure Functions pod kątem logowania za pomocą dostawcy uwierzytelniania OpenID Connect (wersja zapoznawcza)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym artykule opisano sposób konfigurowania Azure App Service lub Azure Functions do używania niestandardowego dostawcy uwierzytelniania zgodnego ze [specyfikacją OpenID Connect Connect](https://openid.net/connect/). OpenID Connect Connect (OIDC) jest standardem branżowym używanym przez wielu dostawców tożsamości (dostawców tożsamości). Nie trzeba zrozumieć szczegółów specyfikacji w celu skonfigurowania aplikacji do korzystania z adherent dostawcy tożsamości.

Można skonfigurować aplikację tak, aby korzystała z co najmniej jednego dostawcy OIDC. Każdy z nich musi mieć unikatową nazwę w konfiguracji, a tylko jeden może służyć jako domyślny cel przekierowania.

> [!CAUTION]
> Włączenie dostawcy połączenia usługi OpenID Connect spowoduje wyłączenie zarządzania funkcją uwierzytelniania i autoryzacji App Service dla aplikacji za pomocą niektórych klientów, takich jak Azure Portal, interfejs wiersza polecenia platformy Azure i Azure PowerShell. Ta funkcja opiera się na nowej powierzchni interfejsu API, która w trakcie korzystania z wersji zapoznawczej nie jest jeszcze uwzględniona we wszystkich środowiskach zarządzania.

## <a name="register-your-application-with-the-identity-provider"></a><a name="register"> </a>Rejestrowanie aplikacji przy użyciu dostawcy tożsamości

Dostawca będzie wymagał rejestracji szczegółowych informacji o aplikacji. Zapoznaj się z instrukcjami dotyczącymi tego dostawcy. Należy zebrać **Identyfikator klienta** i **klucz tajny klienta** dla aplikacji.

> [!IMPORTANT]
> Wpis tajny aplikacji jest ważnym poświadczeniem zabezpieczeń. Nie należy udostępniać tego klucza tajnego nikomu ani rozpowszechniać go w aplikacji klienckiej.
>

> [!NOTE]
> Niektórzy dostawcy mogą wymagać dodatkowych czynności konfiguracyjnych oraz sposobu używania wartości, które zapewnia. Na przykład firma Apple udostępnia klucz prywatny, który nie jest używany jako klucz tajny klienta OIDC, a zamiast tego musi korzystać z tokenu JWT, który jest traktowany jako klucz tajny w konfiguracji aplikacji (zobacz sekcję "Tworzenie klucza tajnego klienta" w [dokumentacji logowania za pomocą firmy Apple](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)).
>

Dodaj wpis tajny klienta jako [Ustawienia aplikacji](./configure-common.md#configure-app-settings) dla aplikacji przy użyciu wybranej nazwy ustawienia. Zanotuj tę nazwę jako nowszą.

Dodatkowo wymagane są metadane OpenID Connect Connect dla dostawcy. Jest to często uwidaczniane za pośrednictwem [dokumentu metadanych konfiguracji](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig), który jest adresem URL wystawcy dostawcy sufiksem `/.well-known/openid-configuration` . Zbierz ten adres URL konfiguracji.

Jeśli nie można użyć dokumentu metadanych konfiguracji, należy oddzielnie zebrać następujące wartości:

- Adres URL wystawcy (czasami wyświetlany jako `issuer` )
- [Punkt końcowy autoryzacji OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-3.1) (czasami wyświetlany jako `authorization_endpoint` )
- [Punkt końcowy tokenu OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-3.2) (czasami wyświetlany jako `token_endpoint` )
- Adres URL [zestawu kluczy sieci Web JSON protokołu OAuth 2,0](https://tools.ietf.org/html/rfc8414#section-2) (czasami wyświetlany jako `jwks_uri` )

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>Dodawanie informacji o dostawcy do aplikacji

> [!NOTE]
> Wymagana konfiguracja ma nowy format interfejsu API, który jest obecnie obsługiwany tylko przez [konfigurację opartą na plikach (wersja zapoznawcza)](.\app-service-authentication-how-to.md#config-file). Należy postępować zgodnie z poniższymi krokami, używając takiego pliku.

Ta sekcja przeprowadzi Cię przez proces aktualizowania konfiguracji w celu uwzględnienia nowej dostawcy tożsamości. Poniżej przedstawiono przykładową konfigurację.

1. W `identityProviders` obiekcie Dodaj `openIdConnectProviders` obiekt, jeśli jeszcze nie istnieje.
1. W `openIdConnectProviders` obiekcie Dodaj klucz dla nowego dostawcy. Jest to przyjazna nazwa używana do odwoływania się do dostawcy w pozostałej części konfiguracji. Na przykład jeśli chcesz, aby wszystkie żądania były uwierzytelniane za pomocą tego dostawcy, ustaw wartość `globalValidation.unauthenticatedClientAction` "RedirectToLoginPage" i ustaw `redirectToProvider` na tę samą przyjazną nazwę.
1. Przypisz obiekt do tego klucza z `registration` obiektem w tym obiekcie i opcjonalnie `login` obiekt:
    
    ```json
    "myCustomIDP" : {
       "registration" : {},
       "login": {
             "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
             "scope": [],
             "loginParameterNames": [],
       }
    }
    ```

1. W obiekcie rejestracji ustaw wartość `clientId` na identyfikator klienta, który został zebrany, ustaw `clientCredential.secretSettingName` na nazwę ustawienia aplikacji, w którym zapisano klucz tajny klienta i Utwórz `openIdConnectConfiguration` obiekt:

    ```json
    "registration": {
      "clientId": "bd96cf8a-3f2b-4806-b180-d3c5fd11a2be",
      "clientCredential": {
         "secretSettingName": "IDP_CLIENT_SECRET"
      },
      "openIdConnectConfiguration" : {}
    }
    ```

1. W `openIdConnectConfiguration` obiekcie Podaj wcześniej zebrane metadane OpenID Connect Connect. Na podstawie zebranych informacji są dostępne dwie opcje:

    - Ustaw `wellKnownOpenIdConfiguration` Właściwość na adres URL metadanych konfiguracji, który został zebrany wcześniej.
    - Alternatywnie można ustawić cztery poszczególne wartości zebrane w następujący sposób:
        - Ustaw `issuer` adres URL wystawcy
        - Ustaw `authorizationEndpoint` punkt końcowy autoryzacji
        - Ustaw `tokenEndpoint` jako punkt końcowy tokenu
        - Ustaw `certificationUri` adres URL dokumentu zestawu klucza internetowego JSON

    Te dwie opcje wykluczają się wzajemnie.

Po ustawieniu tej konfiguracji można przystąpić do uwierzytelniania w aplikacji za pomocą dostawcy połączenia usługi OpenID Connect.

Przykładowa konfiguracja może wyglądać następująco (przy użyciu logowania za pomocą firmy Apple Przykładowo, gdy ustawienie APPLE_GENERATED_CLIENT_SECRET wskazuje wygenerowany token JWT zgodnie z [dokumentacją firmy Apple](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)):

```json
{
    "platform": {
        "enabled": true
    },
    "globalValidation": {
        "redirectToProvider": "apple",
        "unauthenticatedClientAction": "RedirectToLoginPage"
    },
    "identityProviders": {
        "openIdConnectProviders": {
            "apple": {
                "registration": {
                    "clientId": "com.contoso.example.client",
                    "clientCredential": {
                        "secretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
                    },
                    "openIdConnectConfiguration": {
                        "wellKnownOpenIdConfiguration": "https://appleid.apple.com/.well-known/openid-configuration"
                    }
                },
                "login": {
                    "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
                    "scope": [],
                    "loginParameterNames": []
                }
            }
        }
    },
    "login": {
        "tokenStore": {
            "enabled": true
        }
    }     
}
```

## <a name="next-steps"></a><a name="related-content"> </a>Następne kroki

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
