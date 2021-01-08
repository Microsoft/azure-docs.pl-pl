---
title: Informacje o manifeście aplikacji Azure Active Directory
titleSuffix: Microsoft identity platform
description: Szczegółowy zakres manifestu aplikacji Azure Active Directory, który reprezentuje konfigurację tożsamości aplikacji w dzierżawie usługi Azure AD i służy do ułatwienia autoryzacji uwierzytelniania OAuth, środowiska zgody i nie tylko.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 04/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: b29d8007ba7c6fb41209ad4f792069667416616b
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98011547"
---
# <a name="azure-active-directory-app-manifest"></a>Manifest aplikacji usługi Azure Active Directory

Manifest aplikacji zawiera definicję wszystkich atrybutów obiektu aplikacji na platformie tożsamości firmy Microsoft. Służy również jako mechanizm aktualizowania obiektu aplikacji. Aby uzyskać więcej informacji na temat jednostki aplikacji i jej schematu, zobacz [dokumentację jednostki aplikacji interfejs API programu Graph](/graph/api/resources/application).

Atrybuty aplikacji można skonfigurować za pomocą Azure Portal lub programowo przy użyciu [interfejsu API REST](/graph/api/resources/application) lub [programu PowerShell](/powershell/module/azuread#applications). Istnieje jednak kilka scenariuszy, w których należy edytować manifest aplikacji w celu skonfigurowania atrybutu aplikacji. Scenariusze obejmują:

* Jeśli aplikacja została zarejestrowana jako usługa Azure AD z wieloma dzierżawcami i osobistymi kontami Microsoft, nie można zmienić obsługiwanych kont Microsoft w interfejsie użytkownika. Zamiast tego należy użyć edytora manifestu aplikacji, aby zmienić obsługiwany typ konta.
* Jeśli konieczne jest zdefiniowanie uprawnień i ról obsługiwanych przez aplikację, należy zmodyfikować manifest aplikacji.

## <a name="configure-the-app-manifest"></a>Konfigurowanie manifestu aplikacji

Aby skonfigurować manifest aplikacji:

1. Przejdź do <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. Wyszukaj i wybierz usługę **Azure Active Directory** .
1. Wybierz pozycję **Rejestracje aplikacji**.
1. Wybierz aplikację, którą chcesz skonfigurować.
1. Na stronie **Przegląd** aplikacji wybierz sekcję **Manifest**. Zostanie otwarty Edytor manifestu oparty na sieci Web, który umożliwia edytowanie manifestu w portalu. Opcjonalnie możesz wybrać pozycję **Pobierz** , aby edytować manifest lokalnie, a następnie użyć opcji **Przekaż** , aby ponownie zastosować ją do aplikacji.

## <a name="manifest-reference"></a>Odwołanie do manifestu

W tej sekcji opisano atrybuty znalezione w manifeście aplikacji.

### <a name="id-attribute"></a>ID — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| identyfikator | Ciąg |

Unikatowy identyfikator aplikacji w katalogu. Ten identyfikator nie jest identyfikatorem używanym do identyfikowania aplikacji w żadnej transakcji protokołu. Służy do odwoływania się do obiektu w zapytaniach w katalogu.

Przykład:

```json
    "id": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="accesstokenacceptedversion-attribute"></a>accessTokenAcceptedVersion — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| accessTokenAcceptedVersion | Dopuszczający wartość null |

Określa wersję tokenu dostępu oczekiwaną przez zasób. Ten parametr zmienia wersję i format JWT generowanej niezależnie od punktu końcowego lub klienta użytego do żądania tokenu dostępu.

Używany punkt końcowy, wersja 1.0 lub 2.0, jest wybierany przez klienta i ma wpływ tylko na wersję id_tokens. Zasoby muszą być jawnie skonfigurowane `accesstokenAcceptedVersion` w taki sposób, aby wskazywały format obsługiwanego tokenu dostępu.

Możliwe wartości `accesstokenAcceptedVersion` to 1, 2 lub null. Jeśli wartość jest równa null, ten parametr ma wartość domyślną 1, która odnosi się do punktu końcowego v 1.0.

Jeśli `signInAudience` jest `AzureADandPersonalMicrosoftAccount` , wartość musi być `2` .

Przykład:

```json
    "accessTokenAcceptedVersion": 2,
```

### <a name="addins-attribute"></a>atrybut dodatków

| Klucz | Typ wartości |
| :--- | :--- |
| addIns | Kolekcja |

Definiuje niestandardowe zachowanie używane przez usługę do wywoływania aplikacji w określonych kontekstach. Na przykład aplikacje, które mogą renderować strumienie plików, mogą ustawiać `addIns` Właściwość dla swojej funkcji "FileHandler". Ten parametr umożliwia usługom, takim jak Microsoft 365 wywoływanie aplikacji w kontekście dokumentu, nad którym pracuje użytkownik.

Przykład:

```json
    "addIns": [
       {
        "id": "968A844F-7A47-430C-9163-07AE7C31D407",
        "type":" FileHandler",
        "properties": [
           {
              "key": "version",
              "value": "2"
           }
        ]
       }
    ],
```

### <a name="allowpublicclient-attribute"></a>allowPublicClient — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| allowPublicClient | Wartość logiczna |

Określa typ aplikacji rezerwowej. Usługa Azure AD domyślnie wnioskuje typ aplikacji z replyUrlsWithType. Istnieją pewne scenariusze, w których usługa Azure AD nie może określić typu aplikacji klienta. Na przykład, taki scenariusz to [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) , gdzie żądanie HTTP odbywa się bez przekierowania adresu URL. W takich przypadkach usługa Azure AD interpretuje typ aplikacji na podstawie wartości tej właściwości. Jeśli ta wartość jest równa true, typ aplikacji rezerwowej jest ustawiany jako klient publiczny, taki jak zainstalowana aplikacja uruchomiona na urządzeniu przenośnym. Wartość domyślna to false, co oznacza, że rezerwowy typ aplikacji to poufny klient, taki jak aplikacja sieci Web.

Przykład:

```json
    "allowPublicClient": false,
```

### <a name="availabletoothertenants-attribute"></a>availableToOtherTenants — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| availableToOtherTenants | Wartość logiczna |

Ustaw wartość PRAWDA, jeśli aplikacja jest udostępniana innym dzierżawcom; w przeciwnym razie false.

> [!NOTE]
> Ten atrybut jest dostępny tylko w środowisku **rejestracje aplikacji (starsza wersja)** . Zastąpione przez `signInAudience` środowisko [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) .

### <a name="appid-attribute"></a>atrybut appId

| Klucz | Typ wartości |
| :--- | :--- |
| appId | Ciąg |

Określa unikatowy identyfikator aplikacji przypisanej do aplikacji przez usługę Azure AD.

Przykład:

```json
    "appId": "601790de-b632-4f57-9523-ee7cb6ceba95",
```

### <a name="approles-attribute"></a>appRoles — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| appRoles | Kolekcja |

Określa kolekcję ról, które może deklarować aplikacja. Role te można przypisać do użytkowników, grup lub podmiotów usługi. Aby uzyskać więcej przykładów i informacji, zobacz [Dodawanie ról aplikacji w aplikacji i odbieranie ich w tokenie](howto-add-app-roles-in-azure-ad-apps.md).

Przykład:

```json
    "appRoles": [
        {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Read-only access to device information",
           "displayName": "Read Only",
           "id": "601790de-b632-4f57-9523-ee7cb6ceba95",
           "isEnabled": true,
           "value": "ReadOnly"
        }
    ],
```

### <a name="displayname-attribute"></a>displayName — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| displayName | Ciąg |

Nazwa wyświetlana aplikacji.

> [!NOTE]
> Ten atrybut jest dostępny tylko w środowisku **rejestracje aplikacji (starsza wersja)** . Zastąpione przez `name` środowisko [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) .

### <a name="errorurl-attribute"></a>errorUrl — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| errorUrl | Ciąg |

Ich.

### <a name="groupmembershipclaims-attribute"></a>groupMembershipClaims — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
|groupMembershipClaims | Ciąg |

Konfiguruje `groups` w tokenie dostępu użytkownika lub OAuth 2,0, który oczekuje aplikacja. Aby ustawić ten atrybut, należy użyć jednej z następujących prawidłowych wartości ciągu:

- `"None"`
- `"SecurityGroup"` (w przypadku grup zabezpieczeń i ról usługi Azure AD)
- `"ApplicationGroup"` (Ta opcja obejmuje tylko grupy, które są przypisane do aplikacji)
- `"All"` (spowoduje to wyświetlenie wszystkich grup zabezpieczeń, grup dystrybucyjnych i ról katalogu usługi Azure AD, do których należy użytkownik zalogowany).

Przykład:

```json
    "groupMembershipClaims": "SecurityGroup",
```

### <a name="homepage-attribute"></a>atrybut strony głównej

| Klucz | Typ wartości |
| :--- | :--- |
| głównej |Ciąg |

Adres URL strony głównej aplikacji.

> [!NOTE]
> Ten atrybut jest dostępny tylko w środowisku **rejestracje aplikacji (starsza wersja)** . Zastąpione przez `signInUrl` środowisko [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) .

### <a name="objectid-attribute"></a>objectId — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
|objectId | Ciąg |

Unikatowy identyfikator aplikacji w katalogu.

Ta wartość jest dostępna tylko w środowisku **rejestracje aplikacji (starsza wersja)** . Zastąpione przez `id` środowisko [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) .

Przykład:

```json
    "objectId": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="optionalclaims-attribute"></a>optionalClaims — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| optionalClaims | Ciąg |

Opcjonalne oświadczenia zwracane w tokenie przez usługę tokenu zabezpieczającego dla tej konkretnej aplikacji.

W tej chwili aplikacje obsługujące zarówno konta osobiste, jak i usługi Azure AD (zarejestrowane za pomocą portalu rejestracji aplikacji) nie mogą używać oświadczeń opcjonalnych. Jednak aplikacje zarejestrowane dla tylko usługi Azure AD przy użyciu punktu końcowego v 2.0 mogą uzyskać opcjonalne oświadczenia, które zażądały w manifeście. Aby uzyskać więcej informacji, zobacz [opcjonalne oświadczenia](active-directory-optional-claims.md).

Przykład:

```json
    "optionalClaims": null,
```



### <a name="identifieruris-attribute"></a>identifierUris — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| identifierUris | Tablica ciągów |

Zdefiniowane przez użytkownika identyfikatory URI, które w unikatowy sposób identyfikują aplikację sieci Web w ramach dzierżawy usługi Azure AD lub w ramach zweryfikowanej domeny niestandardowej, jeśli aplikacja ma wiele dzierżawców.

Przykład:

```json
    "identifierUris": "https://MyRegisteredApp",
```

### <a name="informationalurls-attribute"></a>informationalUrls — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| informationalUrls | Ciąg |

Określa linki do warunków użytkowania i zasad zachowania poufności informacji aplikacji. Warunki użytkowania usługi i zasady zachowania poufności informacji są udostępniane użytkownikom za pomocą funkcji wyrażania zgody użytkownika. Aby uzyskać więcej informacji, zobacz [jak: Dodawanie warunków użytkowania i zasad zachowania poufności informacji dla zarejestrowanych aplikacji usługi Azure AD](howto-add-terms-of-service-privacy-statement.md).

Przykład:

```json
    "informationalUrls": {
        "termsOfService": "https://MyRegisteredApp/termsofservice",
        "support": "https://MyRegisteredApp/support",
        "privacy": "https://MyRegisteredApp/privacystatement",
        "marketing": "https://MyRegisteredApp/marketing"
    },
```

### <a name="keycredentials-attribute"></a>atrybut poświadczeń

| Klucz | Typ wartości |
| :--- | :--- |
| keyCredentials | Kolekcja |

Zawiera odwołania do poświadczeń przypisanych do aplikacji, wspólnych haseł opartych na ciągach oraz certyfikatów X. 509. Te poświadczenia są używane podczas żądania tokenów dostępu (gdy aplikacja działa jako klient, a nie jako zasób).

Przykład:

```json
    "keyCredentials": [
        {
           "customKeyIdentifier":null,
           "endDate":"2018-09-13T00:00:00Z",
           "keyId":"<guid>",
           "startDate":"2017-09-12T00:00:00Z",
           "type":"AsymmetricX509Cert",
           "usage":"Verify",
           "value":null
        }
    ],
```

### <a name="knownclientapplications-attribute"></a>knownClientApplications — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| knownClientApplications | Tablica ciągów |

Używane do grupowania wyrazów, jeśli masz rozwiązanie, które zawiera dwie części: aplikację kliencką i niestandardową aplikację interfejsu API sieci Web. Jeśli wprowadzisz identyfikator appID aplikacji klienckiej do tej wartości, użytkownik będzie musiał tylko raz wyrazić zgodę na aplikację kliencką. Usługa Azure AD będzie wiedzieć, że wyrażanie zgody na klienta oznacza niejawne przesłanie do internetowego interfejsu API. Automatycznie udostępnia jednostki usługi zarówno dla klienta, jak i interfejsu API sieci Web. Zarówno klient, jak i aplikacja internetowego interfejsu API muszą być zarejestrowani w tej samej dzierżawie.

Przykład:

```json
    "knownClientApplications": ["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"],
```

### <a name="logourl-attribute"></a>logoUrl — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| logoUrl | Ciąg |

Wartość tylko do odczytu wskazująca adres URL usługi CDN na logo, które zostało przekazane w portalu.

Przykład:

```json
    "logoUrl": "https://MyRegisteredAppLogo",
```

### <a name="logouturl-attribute"></a>logoutUrl — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| logoutUrl | Ciąg |

Adres URL do wylogowania z aplikacji.

Przykład:

```json
    "logoutUrl": "https://MyRegisteredAppLogout",
```

### <a name="name-attribute"></a>atrybut nazwy

| Klucz | Typ wartości |
| :--- | :--- |
| name | Ciąg |

Nazwa wyświetlana aplikacji.

Przykład:

```json
    "name": "MyRegisteredApp",
```

### <a name="oauth2allowimplicitflow-attribute"></a>oauth2AllowImplicitFlow — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| oauth2AllowImplicitFlow | Wartość logiczna |

Określa, czy ta aplikacja sieci Web może żądać niejawnych tokenów dostępu protokołu OAuth 2.0. Wartością domyślną jest false. Ta flaga jest używana w przypadku aplikacji opartych na przeglądarce, takich jak aplikacje jednostronicowe języka JavaScript. Aby dowiedzieć się więcej, wprowadź `OAuth 2.0 implicit grant flow` w spisie treści i zobacz tematy dotyczące przepływu niejawnego.

Przykład:

```json
    "oauth2AllowImplicitFlow": false,
```

### <a name="oauth2allowidtokenimplicitflow-attribute"></a>oauth2AllowIdTokenImplicitFlow — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| oauth2AllowIdTokenImplicitFlow | Wartość logiczna |

Określa, czy ta aplikacja sieci Web może żądać niejawnych tokenów identyfikatora przepływu OAuth 2.0. Wartością domyślną jest false. Ta flaga jest używana w przypadku aplikacji opartych na przeglądarce, takich jak aplikacje jednostronicowe języka JavaScript.

Przykład:

```json
    "oauth2AllowIdTokenImplicitFlow": false,
```

### <a name="oauth2permissions-attribute"></a>oauth2Permissions — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| oauth2Permissions | Kolekcja |

Określa kolekcję zakresów uprawnień OAuth 2,0, które aplikacja Web API (zasób) uwidacznia dla aplikacji klienckich. Te zakresy uprawnień mogą być przyznawane aplikacjom klienckim podczas wyrażania zgody.

Przykład:

```json
    "oauth2Permissions": [
       {
          "adminConsentDescription": "Allow the app to access resources on behalf of the signed-in user.",
          "adminConsentDisplayName": "Access resource1",
          "id": "<guid>",
          "isEnabled": true,
          "type": "User",
          "userConsentDescription": "Allow the app to access resource1 on your behalf.",
          "userConsentDisplayName": "Access resources",
          "value": "user_impersonation"
        }
    ],
```

### <a name="oauth2requiredpostresponse-attribute"></a>oauth2RequiredPostResponse — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| oauth2RequiredPostResponse | Wartość logiczna |

Określa, czy w ramach żądania tokenu OAuth 2,0 usługa Azure AD będzie zezwalać na żądania POST, w przeciwieństwie do żądań GET. Wartość domyślna to false, co oznacza, że dozwolone są tylko żądania GET.

Przykład:

```json
    "oauth2RequirePostResponse": false,
```

### <a name="parentalcontrolsettings-attribute"></a>parentalControlSettings — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| parentalControlSettings | Ciąg |

- `countriesBlockedForMinors` Określa kraje/regiony, w których aplikacja jest zablokowana dla małoletnich.
- `legalAgeGroupRule` Określa regułę grupy wieku prawnego, która ma zastosowanie do użytkowników aplikacji. Można ustawić na `Allow` , `RequireConsentForPrivacyServices` ,, `RequireConsentForMinors` `RequireConsentForKids` lub `BlockMinors` .

Przykład:

```json
    "parentalControlSettings": {
        "countriesBlockedForMinors": [],
        "legalAgeGroupRule": "Allow"
    },
```

### <a name="passwordcredentials-attribute"></a>passwordCredentials — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| passwordCredentials | Kolekcja |

Zobacz opis `keyCredentials` właściwości.

Przykład:

```json
    "passwordCredentials": [
      {
        "customKeyIdentifier": null,
        "endDate": "2018-10-19T17:59:59.6521653Z",
        "keyId": "<guid>",
        "startDate":"2016-10-19T17:59:59.6521653Z",
        "value":null
      }
    ],
```

### <a name="preauthorizedapplications-attribute"></a>preAuthorizedApplications — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| preAuthorizedApplications | Kolekcja |

Wyświetla listę aplikacji i żądanych uprawnień dla niejawnej zgody. Wymaga, aby administrator wyraził zgodę na aplikację. preAuthorizedApplications nie wymaga, aby użytkownik wyrażał zgodę na żądane uprawnienia. Uprawnienia wymienione w preAuthorizedApplications nie wymagają zgody użytkownika. Jednak wszelkie dodatkowe żądane uprawnienia, które nie są wymienione w preAuthorizedApplications, wymagają zgody użytkownika.

Przykład:

```json
    "preAuthorizedApplications": [
       {
          "appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",
          "permissionIds": [
             "8748f7db-21fe-4c83-8ab5-53033933c8f1"
            ]
        }
    ],
```

### <a name="publicclient-attribute"></a>publicClient — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| publicClient | Wartość logiczna|

Określa, czy ta aplikacja jest klientem publicznym (na przykład zainstalowaną aplikacją uruchomioną na urządzeniu przenośnym).

Ta właściwość jest dostępna tylko w środowisku **rejestracje aplikacji (starsza wersja)** . Zastąpione przez `allowPublicClient` środowisko [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) .

### <a name="publisherdomain-attribute"></a>publisherDomain — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| publisherDomain | Ciąg |

Zweryfikowana domena wydawcy dla aplikacji. Tylko do odczytu.

Przykład:

```json
    "publisherDomain": "https://www.contoso.com",
````

### <a name="replyurls-attribute"></a>replyUrls — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| replyUrls | Tablica ciągów |

Ta właściwość wielowartościowa zawiera listę zarejestrowanych wartości redirect_uri, które usługa Azure AD będzie akceptować jako miejsca docelowe podczas zwracania tokenów.

Ta właściwość jest dostępna tylko w środowisku **rejestracje aplikacji (starsza wersja)** . Zastąpione przez `replyUrlsWithType` środowisko [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) .

### <a name="replyurlswithtype-attribute"></a>replyUrlsWithType — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| replyUrlsWithType | Kolekcja |

Ta właściwość wielowartościowa zawiera listę zarejestrowanych wartości redirect_uri, które usługa Azure AD będzie akceptować jako miejsca docelowe podczas zwracania tokenów. Każda wartość identyfikatora URI powinna zawierać skojarzoną wartość typu aplikacji. Obsługiwane wartości typu:

- `Web`
- `InstalledClient`
- `Spa`

Aby dowiedzieć się więcej, zobacz [ograniczenia i ograniczenia dotyczące replyUrl](./reply-url.md).

Przykład:

```json
    "replyUrlsWithType": [
       {
          "url": "https://localhost:4400/services/office365/redirectTarget.html",
          "type": "InstalledClient"
       }
    ],
```

### <a name="requiredresourceaccess-attribute"></a>requiredResourceAccess — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| requiredResourceAccess | Kolekcja |

Dzięki dynamicznej zgody można korzystać `requiredResourceAccess` z funkcji zgody administratora oraz w przypadku użytkowników, którzy używają zgody statycznej. Jednak ten parametr nie ma wpływu na środowisko zgody użytkownika na potrzeby ogólnego przypadku.

- `resourceAppId` jest unikatowym identyfikatorem zasobu, do którego aplikacja wymaga dostępu. Ta wartość powinna być taka sama jak identyfikator appId zadeklarowany w docelowej aplikacji zasobów.
- `resourceAccess` jest tablicą zawierającą zakresy uprawnień OAuth 2.0 i role aplikacji wymagane przez aplikację od określonego zasobu. Zawiera `id` wartości i `type` określonych zasobów.

Przykład:

```json
    "requiredResourceAccess": [
        {
            "resourceAppId": "00000002-0000-0000-c000-000000000000",
            "resourceAccess": [
                {
                    "id": "311a71cc-e848-46a1-bdf8-97ff7156d8e6",
                    "type": "Scope"
                }
            ]
        }
    ],
```

### <a name="samlmetadataurl-attribute"></a>samlMetadataUrl — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| samlMetadataUrl | Ciąg |

Adres URL metadanych SAML dla aplikacji.

Przykład:

```json
    "samlMetadataUrl": "https://MyRegisteredAppSAMLMetadata",
```

### <a name="signinurl-attribute"></a>signInUrl — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| signInUrl | Ciąg |

Określa adres URL strony głównej aplikacji.

Przykład:

```json
    "signInUrl": "https://MyRegisteredApp",
```

### <a name="signinaudience-attribute"></a>signInAudience — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| signInAudience | Ciąg |

Określa, jakie konta Microsoft są obsługiwane przez bieżącą aplikację. Obsługiwane są następujące wartości:
- `AzureADMyOrg` -Użytkownicy z kontem służbowym firmy Microsoft w dzierżawie usługi Azure AD w organizacji (na przykład z jedną dzierżawą)
- `AzureADMultipleOrgs` -Użytkownicy z kontem służbowym firmy Microsoft w dzierżawie usługi Azure AD w organizacji (na przykład z wieloma dzierżawcami)
- `AzureADandPersonalMicrosoftAccount` — Użytkownicy z konto Microsoft osobistym lub kontem służbowym w ramach dzierżawy usługi Azure AD w organizacji
- `PersonalMicrosoftAccount` — Konta osobiste, które są używane do logowania się do usług, takich jak Xbox i Skype.

Przykład:

```json
    "signInAudience": "AzureADandPersonalMicrosoftAccount",
```

### <a name="tags-attribute"></a>Tag — atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| tags | Tablica ciągów  |

Niestandardowe ciągi, których można użyć do kategoryzowania i identyfikowania aplikacji.

Przykład:

```json
    "tags": [
       "ProductionApp"
    ],
```

## <a name="common-issues"></a>Typowe problemy

### <a name="manifest-limits"></a>Limity manifestu

Manifest aplikacji ma wiele atrybutów, które są określane jako kolekcje; na przykład appRoles, poświadczenia, knownClientApplications, identifierUris, redirectUris, requiredResourceAccess i oauth2Permissions. W ramach kompletnego manifestu aplikacji dla dowolnej aplikacji łączna liczba wpisów we wszystkich łączonych kolekcjach została ograniczona o 1200. Jeśli wcześniej określono 100 identyfikatorów URI przekierowania w manifeście aplikacji, pozostało tylko z 1100 pozostałymi wpisami do użycia we wszystkich innych kolekcjach, które składają się na manifest.

> [!NOTE]
> W przypadku próby dodania więcej niż 1200 wpisów w manifeście aplikacji może zostać wyświetlony komunikat o błędzie **"nie można zaktualizować aplikacji xxxxxx. Szczegóły błędu: rozmiar manifestu przekroczył swój limit. Zmniejsz liczbę wartości i ponów próbę wykonania żądania ".**

### <a name="unsupported-attributes"></a>Nieobsługiwane atrybuty

Manifest aplikacji reprezentuje schemat modelu bazowego aplikacji w usłudze Azure AD. W miarę rozwoju podstawowego schematu Edytor manifestu zostanie zaktualizowany w celu odzwierciedlenia nowego schematu od czasu do czasu. W związku z tym można zauważyć, że nowe atrybuty pojawiają się w manifeście aplikacji. W rzadkich przypadkach może być zauważalna zmiana składni lub semantyki w istniejących atrybutach lub znalezienie wcześniej nieobsługiwanego atrybutu. Na przykład zobaczysz nowe atrybuty w [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908), które są znane z inną nazwą w środowisku rejestracje aplikacji (starsza wersja).

| Rejestracje aplikacji (starsza wersja)| Rejestracje aplikacji           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Opisy tych atrybutów znajdują się w sekcji [Dokumentacja manifestu](#manifest-reference) .

Podczas próby przekazania wcześniej pobranego manifestu może zostać wyświetlony jeden z następujących błędów. Ten błąd może być spowodowany tym, że Edytor manifestu obsługuje teraz nowszą wersję schematu, która nie jest zgodna z tą, którą próbujesz przekazać.

* "Nie można zaktualizować aplikacji xxxxxx. Szczegóły błędu: nieprawidłowy identyfikator obiektu "undefined". []."
* "Nie można zaktualizować aplikacji xxxxxx. Szczegóły błędu: co najmniej jedna podana wartość właściwości jest nieprawidłowa. []."
* "Nie można zaktualizować aplikacji xxxxxx. Szczegóły błędu: nie można ustawić availableToOtherTenants w tej wersji interfejsu API na potrzeby aktualizacji. []."
* "Nie można zaktualizować aplikacji xxxxxx. Szczegóły błędu: aktualizacje właściwości "replyUrls" nie są dozwolone dla tej aplikacji. Zamiast tego użyj właściwości "replyUrlsWithType". []."
* "Nie można zaktualizować aplikacji xxxxxx. Szczegóły błędu: nie znaleziono wartości bez nazwy typu i nie jest dostępny żaden oczekiwany typ. Gdy model jest określony, każda wartość w ładunku musi mieć typ, który może być określony w ładunku, jawnie przez obiekt wywołujący lub niejawnie wywnioskowany z wartości nadrzędnej. []"

Po wyświetleniu jednego z tych błędów zalecamy wykonanie następujących czynności:

1. Edytuj atrybuty osobno w edytorze manifestu zamiast przekazywania wcześniej pobranego manifestu. Użyj tabeli [referencyjnej manifestu](#manifest-reference) , aby zrozumieć składnię i semantykę starych i nowych atrybutów, dzięki czemu możesz pomyślnie edytować interesujące Cię atrybuty.
1. Jeśli przepływ pracy wymaga zapisania manifestów w repozytorium źródłowym na potrzeby późniejszego użycia, sugerujemy oparcie zapisanych manifestów w repozytorium z tym, co widzisz w **rejestracje aplikacji** środowisku.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat relacji między aplikacją aplikacji a obiektami głównymi usługi, zobacz temat [obiekty główne aplikacji i usługi w usłudze Azure AD](app-objects-and-service-principals.md).
* Zapoznaj się z [słownikiem dla deweloperów Microsoft Identity platform](developer-glossary.md) , aby zapoznać się z definicjami niektórych podstawowych koncepcji deweloperów platformy tożsamości firmy Microsoft.

Skorzystaj z następującej sekcji komentarzy, aby przekazać Opinie, które ułatwiają udoskonalenie i kształtowanie naszej zawartości.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: ./quickstart-register-app.md
[O365-PERM-DETAILS]: /graph/permissions-reference
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
