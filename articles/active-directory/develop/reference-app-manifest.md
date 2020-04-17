---
title: Opis manifestu aplikacji usługi Azure Active Directory
description: Szczegółowe pokrycie manifestu aplikacji usługi Azure Active Directory, który reprezentuje konfigurację tożsamości aplikacji w dzierżawie usługi Azure AD i jest używany w celu ułatwienia autoryzacji OAuth, środowiska zgody i innych.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: 9f2ed6ea8cc75e2ee72f15c14f3de7bb8bf8cef6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450889"
---
# <a name="azure-active-directory-app-manifest"></a>Manifest aplikacji usługi Azure Active Directory

Manifest aplikacji zawiera definicję wszystkich atrybutów obiektu aplikacji na platformie tożsamości firmy Microsoft. Służy również jako mechanizm aktualizacji obiektu aplikacji. Aby uzyskać więcej informacji na temat jednostki Aplikacji i jej schematu, zobacz [dokumentację jednostki aplikacji interfejsu API wykresu](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity).

Atrybuty aplikacji można skonfigurować za pośrednictwem witryny Azure Portal lub programowo przy użyciu [interfejsu API REST](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) lub programu [PowerShell.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications) Istnieją jednak scenariusze, w których należy edytować manifest aplikacji, aby skonfigurować atrybut aplikacji. Scenariusze obejmują:

* Jeśli aplikacja została zarejestrowana jako wielodostępne i osobiste konta Microsoft usługi Azure AD, nie można zmienić obsługiwanych kont Microsoft w interfejsie użytkownika. Zamiast tego należy użyć edytora manifestów aplikacji, aby zmienić typ obsługiwanego konta.
* Jeśli chcesz zdefiniować uprawnienia i role, które obsługuje aplikacja, należy zmodyfikować manifest aplikacji.

## <a name="configure-the-app-manifest"></a>Konfigurowanie manifestu aplikacji

Aby skonfigurować manifest aplikacji:

1. Przejdź do [witryny Azure portal](https://portal.azure.com). Wyszukaj i wybierz usługę **Azure Active Directory.**
1. Wybierz pozycję **Rejestracje aplikacji**.
1. Wybierz aplikację, którą chcesz skonfigurować.
1. Na stronie **Przegląd** aplikacji wybierz sekcję **Manifest**. Otworzy się edytor manifestów opartych na sieci Web, umożliwiający edycję manifestu w portalu. Opcjonalnie można wybrać **opcję Pobierz,** aby edytować manifest lokalnie, a następnie użyć **funkcji Przekaż,** aby ponownie zastosować go do aplikacji.

## <a name="manifest-reference"></a>Odwołanie do manifestu

W tej sekcji opisano atrybuty znalezione w manifeście aplikacji.

### <a name="id-attribute"></a>atrybut id

| Klucz | Typ wartości |
| :--- | :--- |
| id | Ciąg |

Unikatowy identyfikator aplikacji w katalogu. Ten identyfikator nie jest identyfikatorem używanym do identyfikowania aplikacji w dowolnej transakcji protokołu. Jest on używany do odwoływania się do obiektu w kwerendach katalogu.

Przykład:

```json
    "id": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="accesstokenacceptedversion-attribute"></a>atrybut accessTokenAcceptedVersion

| Klucz | Typ wartości |
| :--- | :--- |
| accessTokenAcceptedVersion | Nullable Int32 |

Określa wersję tokenu dostępu oczekiwaną przez zasób. Ten parametr zmienia wersję i format JWT produkowane niezależnie od punktu końcowego lub klienta używanego do żądania tokenu dostępu.

Używany punkt końcowy, v1.0 lub v2.0, jest wybierany przez klienta i ma wpływ tylko na wersję id_tokens. Zasoby należy jawnie `accesstokenAcceptedVersion` skonfigurować, aby wskazać format tokenu dostępu obsługiwane.

Możliwe wartości `accesstokenAcceptedVersion` dla to 1, 2 lub null. Jeśli wartość jest null, ten parametr domyślnie 1, który odpowiada punkt końcowy w wersji 1.0.

Jeśli `signInAudience` `AzureADandPersonalMicrosoftAccount`tak, wartość `2`musi być .

Przykład:

```json
    "accessTokenAcceptedVersion": 2,
```

### <a name="addins-attribute"></a>Atrybut addIns

| Klucz | Typ wartości |
| :--- | :--- |
| Dodatków | Collection |

Definiuje niestandardowe zachowanie, którego usługa zużywająca może używać do wywoływania aplikacji w określonych kontekstach. Na przykład aplikacje, które mogą renderować strumienie plików może ustawić `addIns` właściwość dla jego funkcji "FileHandler". Ten parametr umożliwia usługi, takie jak Office 365, wywołania aplikacji w kontekście dokumentu, nad który użytkownik pracuje.

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

### <a name="allowpublicclient-attribute"></a>zezwalanie Nastawę PublicznejKlient

| Klucz | Typ wartości |
| :--- | :--- |
| zezwalaj na publicznekluczanie | Wartość logiczna |

Określa typ aplikacji rezerwowej. Usługa Azure AD domyślnie wywnioskować typ aplikacji z replyUrlsWithType. Istnieją pewne scenariusze, w których usługa Azure AD nie może określić typu aplikacji klienckiej. Na przykład jednym z takich scenariuszy jest przepływ [ROPC,](https://tools.ietf.org/html/rfc6749#section-4.3) w którym żądanie HTTP odbywa się bez przekierowania adresu URL). W takich przypadkach usługa Azure AD będzie interpretować typ aplikacji na podstawie wartości tej właściwości. Jeśli ta wartość jest ustawiona na true typ aplikacji rezerwowej jest ustawiony jako klient publiczny, takich jak zainstalowana aplikacja uruchomiona na urządzeniu przenośnym. Wartość domyślna jest false, co oznacza, że rezerwowy typ aplikacji jest klientem poufnym, takim jak aplikacja internetowa.

Przykład:

```json
    "allowPublicClient": false,
```

### <a name="availabletoothertenants-attribute"></a>atrybut availableToOtherTenants

| Klucz | Typ wartości |
| :--- | :--- |
| availableToOtherTenants | Wartość logiczna |

Ustaw wartość true, jeśli aplikacja jest współużytkowana z innymi dzierżawcami; w przeciwnym razie, false.

> [!NOTE]
> Ten atrybut jest dostępny tylko w środowiska **rejestracji aplikacji (Legacy).** Zastąpiony `signInAudience` przez w [środowiska rejestracji aplikacji.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="appid-attribute"></a>Atrybut appId

| Klucz | Typ wartości |
| :--- | :--- |
| appId | Ciąg |

Określa unikatowy identyfikator aplikacji, która jest przypisana do aplikacji przez usługę Azure AD.

Przykład:

```json
    "appId": "601790de-b632-4f57-9523-ee7cb6ceba95",
```

### <a name="approles-attribute"></a>Atrybut appRoles

| Klucz | Typ wartości |
| :--- | :--- |
| AppRoles (Zaułki) | Collection |

Określa kolekcję ról, które aplikacja może zadeklarować. Te role można przypisać do użytkowników, grup lub podmiotów korzystających z usługi. Aby uzyskać więcej przykładów i informacji, zobacz [Dodawanie ról aplikacji w aplikacji i odbieranie ich w tokenie.](howto-add-app-roles-in-azure-ad-apps.md)

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

### <a name="displayname-attribute"></a>atrybut displayName

| Klucz | Typ wartości |
| :--- | :--- |
| displayName | Ciąg |

Wyświetlana nazwa aplikacji.

> [!NOTE]
> Ten atrybut jest dostępny tylko w środowiska **rejestracji aplikacji (Legacy).** Zastąpiony `name` przez w [środowiska rejestracji aplikacji.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="errorurl-attribute"></a>atrybut errorUrl

| Klucz | Typ wartości |
| :--- | :--- |
| błądUrl | Ciąg |

Nieobsługiwane.

### <a name="groupmembershipclaims-attribute"></a>groupMembershipKlawy atrybut

| Klucz | Typ wartości |
| :--- | :--- |
|groupMembershipClaims | Ciąg |

Konfiguruje `groups` oświadczenie wystawione w tokenie dostępu użytkownika lub OAuth 2.0, który oczekuje aplikacji. Aby ustawić ten atrybut, należy użyć jednej z następujących prawidłowych wartości ciągu:

- `"None"`
- `"SecurityGroup"`(dla grup zabezpieczeń i ról usługi Azure AD)
- `"All"`(spowoduje to uzyskanie wszystkich grup zabezpieczeń, grup dystrybucyjnych i ról katalogu usługi Azure AD, których członkiem jest zalogowany użytkownik.

Przykład:

```json
    "groupMembershipClaims": "SecurityGroup",
```

### <a name="homepage-attribute"></a>Atrybut strony głównej

| Klucz | Typ wartości |
| :--- | :--- |
| Strony głównej |Ciąg |

Adres URL strony głównej aplikacji.

> [!NOTE]
> Ten atrybut jest dostępny tylko w środowiska **rejestracji aplikacji (Legacy).** Zastąpiony `signInUrl` przez w [środowiska rejestracji aplikacji.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="objectid-attribute"></a>Atrybut objectId

| Klucz | Typ wartości |
| :--- | :--- |
|Objectid | Ciąg |

Unikatowy identyfikator aplikacji w katalogu.

Jest to dostępne tylko w środowiska **rejestracji aplikacji (Legacy).** Zastąpiony `id` przez w [środowiska rejestracji aplikacji.](https://go.microsoft.com/fwlink/?linkid=2083908)

Przykład:

```json
    "objectId": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="optionalclaims-attribute"></a>Atrybut optionalClaims

| Klucz | Typ wartości |
| :--- | :--- |
| opcjonalneClaims | Ciąg |

Opcjonalne oświadczenia zwrócone w tokenie przez usługę tokenu zabezpieczającego dla tej konkretnej aplikacji.

W tej chwili aplikacje, które obsługują zarówno konta osobiste, jak i usługę Azure AD (zarejestrowane za pośrednictwem portalu rejestracji aplikacji) nie mogą używać oświadczeń opcjonalnych. Jednak aplikacje zarejestrowane tylko dla usługi Azure AD przy użyciu punktu końcowego w wersji 2.0 można uzyskać opcjonalne oświadczenia, których zażądali w manifeście. Aby uzyskać więcej informacji, zobacz [Oświadczenia opcjonalne](active-directory-optional-claims.md).

Przykład:

```json
    "optionalClaims": null,
```



### <a name="identifieruris-attribute"></a>Atrybut identifierUris

| Klucz | Typ wartości |
| :--- | :--- |
| identyfikatorUris | Tablica ciągów |

Identyfikatory URI zdefiniowane przez użytkownika, które jednoznacznie identyfikują aplikację sieci Web w dzierżawie usługi Azure AD lub w zweryfikowanej domenie niestandardowej, jeśli aplikacja jest wielodostępna.

Przykład:

```json
    "identifierUris": "https://MyRegisteredApp",
```

### <a name="informationalurls-attribute"></a>atrybut informationalUrls

| Klucz | Typ wartości |
| :--- | :--- |
| informacjeUrls | Ciąg |

Określa łącza do warunków korzystania z usługi i zasad zachowania poufności informacji. Warunki korzystania z usługi i zasady zachowania poufności informacji są ujmowania użytkowników za pośrednictwem środowiska zgody użytkownika. Aby uzyskać więcej informacji, zobacz [Jak: Dodawanie warunków świadczenia usług i zasad zachowania poufności informacji dla zarejestrowanych aplikacji usługi Azure AD](howto-add-terms-of-service-privacy-statement.md).

Przykład:

```json
    "informationalUrls": {
        "termsOfService": "https://MyRegisteredApp/termsofservice",
        "support": "https://MyRegisteredApp/support",
        "privacy": "https://MyRegisteredApp/privacystatement",
        "marketing": "https://MyRegisteredApp/marketing"
    },
```

### <a name="keycredentials-attribute"></a>Atrybut keyCredentials

| Klucz | Typ wartości |
| :--- | :--- |
| keyCredentials | Collection |

Przechowuje odwołania do poświadczeń przypisanych do aplikacji, wspólnych wpisów tajnych opartych na ciągu i certyfikatów X.509. Te poświadczenia są używane podczas żądania tokenów dostępu (gdy aplikacja działa jako klient, a nie jako zasób).

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

### <a name="knownclientapplications-attribute"></a>znany AtrybutClientApplications

| Klucz | Typ wartości |
| :--- | :--- |
| znaneClientApplications | Tablica ciągów |

Służy do tworzenia łączenia zgody, jeśli masz rozwiązanie, które zawiera dwie części: aplikację kliencką i niestandardową aplikację interfejsu API sieci web. Jeśli wprowadzisz appID aplikacji klienckiej do tej wartości, użytkownik będzie musiał wyrazić zgodę tylko raz do aplikacji klienckiej. Usługa Azure AD będzie wiedziała, że zgoda na klienta oznacza niejawną zgodę na internetowy interfejs API. Będzie automatycznie aprowizowania jednostki usługi dla klienta i interfejsu API sieci web w tym samym czasie. Zarówno klient, jak i aplikacja interfejsu API sieci web muszą być zarejestrowane w tej samej dzierżawie.

Przykład:

```json
    "knownClientApplications": ["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"],
```

### <a name="logourl-attribute"></a>atrybut logoUrl

| Klucz | Typ wartości |
| :--- | :--- |
| logoUrl | Ciąg |

Tylko do odczytu wartość, która wskazuje adres URL sieci CDN do logo, który został przekazany w portalu.

Przykład:

```json
    "logoUrl": "https://MyRegisteredAppLogo",
```

### <a name="logouturl-attribute"></a>atrybut logoutUrl

| Klucz | Typ wartości |
| :--- | :--- |
| wyloguj Sięrl | Ciąg |

Adres URL do wylogowania się z aplikacji.

Przykład:

```json
    "logoutUrl": "https://MyRegisteredAppLogout",
```

### <a name="name-attribute"></a>atrybut name

| Klucz | Typ wartości |
| :--- | :--- |
| name | Ciąg |

Wyświetlana nazwa aplikacji.

Przykład:

```json
    "name": "MyRegisteredApp",
```

### <a name="oauth2allowimplicitflow-attribute"></a>atrybut oauth2AllowImplicitFlow

| Klucz | Typ wartości |
| :--- | :--- |
| oauth2AllowImplicitFlow | Wartość logiczna |

Określa, czy ta aplikacja sieci web może żądać tokenów dostępu niejawnego przepływu OAuth2.0. Wartością domyślną jest false. Ta flaga jest używana w aplikacjach opartych na przeglądarce, takich jak aplikacje jednostronicowe JavaScript. Aby dowiedzieć `OAuth 2.0 implicit grant flow` się więcej, wprowadź spis treści i zobacz tematy dotyczące przepływu niejawnego.

Przykład:

```json
    "oauth2AllowImplicitFlow": false,
```

### <a name="oauth2allowidtokenimplicitflow-attribute"></a>oauth2AllowIdTokenImplicitFlow atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| oauth2AllowIdTokenImplicitFlow | Wartość logiczna |

Określa, czy ta aplikacja sieci web może żądać tokenów identyfikatorów niejawnego przepływu OAuth2.0. Wartością domyślną jest false. Ta flaga jest używana w aplikacjach opartych na przeglądarce, takich jak aplikacje jednostronicowe JavaScript.

Przykład:

```json
    "oauth2AllowIdTokenImplicitFlow": false,
```

### <a name="oauth2permissions-attribute"></a>atrybut oauth2Permissions

| Klucz | Typ wartości |
| :--- | :--- |
| oauth2Permissions | Collection |

Określa kolekcję zakresów uprawnień OAuth 2.0 udostępnianych aplikacjom klienckim przez aplikację interfejsu API (zasobu). Te zakresy uprawnień mogą być przyznawane aplikacjom klienckim podczas wyrażania zgody.

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

### <a name="oauth2requiredpostresponse-attribute"></a>oauth2RequiredPostResponse atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| oauth2RequiredPostResponse | Wartość logiczna |

Określa, czy w ramach żądań tokenu OAuth 2.0 usługa Azure AD zezwala na żądania POST, w przeciwieństwie do żądań GET. Wartość domyślna to false, która określa, że dozwolone będą tylko żądania GET.

Przykład:

```json
    "oauth2RequirePostResponse": false,
```

### <a name="parentalcontrolsettings-attribute"></a>atrybut parentalControlSettings

| Klucz | Typ wartości |
| :--- | :--- |
| parentalControlSettings (Ustawienia kontroli) | Ciąg |

- `countriesBlockedForMinors`określa kraje, w których aplikacja jest zablokowana dla nieletnich.
- `legalAgeGroupRule`określa regułę prawnej grupy wiekowej, która ma zastosowanie do użytkowników aplikacji. Można ustawić `Allow`na `RequireConsentForPrivacyServices` `RequireConsentForMinors`, `RequireConsentForKids`, `BlockMinors`, lub .  

Przykład:

```json
    "parentalControlSettings": {
        "countriesBlockedForMinors": [],
        "legalAgeGroupRule": "Allow"
    },
```

### <a name="passwordcredentials-attribute"></a>atrybut passwordCredentials

| Klucz | Typ wartości |
| :--- | :--- |
| passwordCredentials (łagodzi hasło) | Collection |

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

### <a name="preauthorizedapplications-attribute"></a>PreAuthorizedApplications atrybut

| Klucz | Typ wartości |
| :--- | :--- |
| preAuthorizedApplications (PreAuthorizedApplications) | Collection |

Wyświetla listę aplikacji i żądanych uprawnień do niejawnej zgody. Wymaga, aby administrator wyraził zgodę na aplikację. preAuthorizedApplications nie wymagają od użytkownika zgody na żądane uprawnienia. Uprawnienia wymienione w preAuthorizedApplications nie wymagają zgody użytkownika. Jednak wszelkie dodatkowe wymagane uprawnienia niewymienione w preAuthorizedApplications wymagają zgody użytkownika.

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

### <a name="publicclient-attribute"></a>atrybut publicClient

| Klucz | Typ wartości |
| :--- | :--- |
| publicKlucza | Wartość logiczna|

Określa, czy ta aplikacja jest klientem publicznym (na przykład zainstalowaną aplikacją działającą na urządzeniu przenośnym). 

Ta właściwość jest dostępna tylko w środowiska **rejestracji aplikacji (Legacy).** Zastąpiony `allowPublicClient` przez w [środowiska rejestracji aplikacji.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="publisherdomain-attribute"></a>atrybut wydawcaDojada

| Klucz | Typ wartości |
| :--- | :--- |
| wydawcaDomena | Ciąg |

Zweryfikowana domena wydawcy dla aplikacji. Tylko do odczytu.

Przykład:

```json
    "publisherDomain": "https://www.contoso.com",
````

### <a name="replyurls-attribute"></a>atrybut replyUrls

| Klucz | Typ wartości |
| :--- | :--- |
| replyUrls (Liczba odpowiedzi) | Tablica ciągów |

Ta wielowartościowa właściwość zawiera listę zarejestrowanych wartości redirect_uri, które usługa Azure AD zaakceptuje jako miejsca docelowe podczas zwracania tokenów.

Ta właściwość jest dostępna tylko w środowiska **rejestracji aplikacji (Legacy).** Zastąpiony `replyUrlsWithType` przez w [środowiska rejestracji aplikacji.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="replyurlswithtype-attribute"></a>atrybut replyUrlsWithType

| Klucz | Typ wartości |
| :--- | :--- |
| replyUrlsWithType | Collection |

Ta wielowartościowa właściwość zawiera listę zarejestrowanych wartości redirect_uri, które usługa Azure AD zaakceptuje jako miejsca docelowe podczas zwracania tokenów. Każda wartość URI powinna zawierać skojarzoną wartość typu aplikacji. Obsługiwane wartości typu to:

- `Web`
- `InstalledClient`

Aby dowiedzieć się więcej, zobacz [replyUrl ograniczenia i ograniczenia](https://docs.microsoft.com/azure/active-directory/develop/reply-url).

Przykład:

```json
    "replyUrlsWithType": [
       {
          "url": "https://localhost:4400/services/office365/redirectTarget.html",
          "type": "InstalledClient"
       }
    ],
```

### <a name="requiredresourceaccess-attribute"></a>atrybut requiredResourceAccess

| Klucz | Typ wartości |
| :--- | :--- |
| requiredResourceAccess | Collection |

Dzięki dynamicznej `requiredResourceAccess` zgodzie, dyski środowiska zgody administratora i środowiska zgody użytkownika dla użytkowników, którzy korzystają ze statycznej zgody. Jednak ten parametr nie dysk środowiska zgody użytkownika dla ogólnego przypadku.

- `resourceAppId`jest unikatowym identyfikatorem zasobu, do których aplikacja wymaga dostępu. Ta wartość powinna być równa appId zadeklarowane w aplikacji zasobu docelowego.
- `resourceAccess`jest tablicą, która zawiera listę zakresów uprawnień OAuth2.0 i ról aplikacji, które aplikacja wymaga od określonego zasobu. Zawiera `id` i `type` wartości określonych zasobów.

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

### <a name="samlmetadataurl-attribute"></a>atrybut samlMetadataUrl

| Klucz | Typ wartości |
| :--- | :--- |
| samlMetadataUrl | Ciąg |

Adres URL metadanych SAML dla aplikacji.

Przykład:

```json
    "samlMetadataUrl": "https://MyRegisteredAppSAMLMetadata",
```

### <a name="signinurl-attribute"></a>atrybut signInUrl

| Klucz | Typ wartości |
| :--- | :--- |
| znakInUrl | Ciąg |

Określa adres URL strony głównej aplikacji.

Przykład:

```json
    "signInUrl": "https://MyRegisteredApp",
```

### <a name="signinaudience-attribute"></a>atrybut signInAudience

| Klucz | Typ wartości |
| :--- | :--- |
| znakInAudience | Ciąg |

Określa, jakie konta Microsoft są obsługiwane dla bieżącej aplikacji. Obsługiwane wartości to:
- `AzureADMyOrg`- Użytkownicy z kontem służbowym firmy Microsoft w dzierżawie usługi Azure AD w mojej organizacji (na przykład pojedyncza dzierżawa)
- `AzureADMultipleOrgs`- Użytkownicy z kontem służbowym firmy Microsoft w dzierżawie usługi Azure AD dowolnej organizacji (na przykład wielodostępnych)
- `AzureADandPersonalMicrosoftAccount`- Użytkownicy z osobistym kontem Microsoft lub kontem służbowym w dzierżawie usługi Azure AD dowolnej organizacji
- `PersonalMicrosoftAccount`- Konta osobiste, które są używane do logowania się do usług, takich jak Xbox i Skype.

Przykład:

```json
    "signInAudience": "AzureADandPersonalMicrosoftAccount",
```

### <a name="tags-attribute"></a>atrybut tagów

| Klucz | Typ wartości |
| :--- | :--- |
| tags | Tablica ciągów  |

Ciągi niestandardowe, które mogą służyć do kategoryzowanie i identyfikowanie aplikacji.

Przykład:

```json
    "tags": [
       "ProductionApp"
    ],
```

## <a name="common-issues"></a>Typowe problemy

### <a name="manifest-limits"></a>Granice manifestu

Manifest aplikacji ma wiele atrybutów, które są określane jako kolekcje; na przykład appRoles, keyCredentials, knownClientApplications, identifierUris, redirectUris, requiredResourceAccess i oauth2Permissions. W ramach pełnego manifestu zgłoszenia dla każdego zgłoszenia łączna liczba wpisów we wszystkich połączonych zbiorach została ograniczona do 1200. Jeśli wcześniej określić 100 przekierowanie identyfikatorów URI w manifeście aplikacji, a następnie jesteś tylko w lewo z 1100 pozostałych wpisów do użycia we wszystkich innych kolekcjach połączonych, które tworzą manifest.

> [!NOTE]
> W przypadku próby dodania więcej niż 1200 wpisów w manifeście aplikacji może zostać wyświetlony błąd **"Nie można zaktualizować aplikacji xxxxxx. Szczegóły błędu: rozmiar manifestu przekroczył limit. Zmniejsz liczbę wartości i ponów próbę żądania."**

### <a name="unsupported-attributes"></a>Nieobsługiwały atrybuty

Manifest aplikacji reprezentuje schemat podstawowego modelu aplikacji w usłudze Azure AD. W miarę ewolucji schematu źródłowego edytor manifestu będzie aktualizowany w celu odzwierciedlenia nowego schematu od czasu do czasu. W rezultacie można zauważyć nowe atrybuty wyświetlane w manifeście aplikacji. W rzadkich przypadkach można zauważyć zmiany składniowe lub semantyczne w istniejących atrybutów lub może się okazać, że atrybut, który istniał wcześniej nie są już obsługiwane. Na przykład zobaczysz nowe atrybuty w [rejestracji aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908), które są znane z inną nazwą w rejestracji aplikacji (Legacy) środowiska.

| Rejestracje aplikacji (Starsza wersja)| Rejestracje aplikacji           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Opisy tych atrybutów można znaleźć w sekcji [odwołania do manifestu.](#manifest-reference)

Podczas próby przesłania wcześniej pobranego manifestu może zostać wyświetlony jeden z następujących błędów. Ten błąd jest prawdopodobne, ponieważ edytor manifestów obsługuje teraz nowszą wersję schematu, która nie pasuje do tej, którą próbujesz przekazać.

* "Nie można zaktualizować aplikacji xxxxxx. Szczegóły błędu: Nieprawidłowy identyfikator obiektu "undefined". []."
* "Nie można zaktualizować aplikacji xxxxxx. Szczegóły błędu: jedna lub więcej określonych wartości właściwości są nieprawidłowe. []."
* "Nie można zaktualizować aplikacji xxxxxx. Szczegóły błędu: Nie można ustawić availableToOtherTenants w tej wersji interfejsu API do aktualizacji. []."
* "Nie można zaktualizować aplikacji xxxxxx. Szczegóły błędu: Aktualizacje właściwości "replyUrls" nie jest dozwolone dla tej aplikacji. Zamiast tego należy użyć właściwości "replyUrlsWithType". []."
* "Nie można zaktualizować aplikacji xxxxxx. Szczegóły błędu: znaleziono wartość bez nazwy typu i nie jest dostępny żaden oczekiwany typ. Gdy model jest określony, każda wartość w ładunku musi mieć typ, który może być określony w ładunku, jawnie przez wywołującego lub niejawnie wywnioskowane z wartości nadrzędnej. []"

Gdy widzisz jeden z tych błędów, zaleca się następujące akcje:

1. Edytuj atrybuty indywidualnie w edytorze manifestów zamiast przekazywania wcześniej pobranego manifestu. Użyj tabeli [odwołań](#manifest-reference) do manifestu, aby zrozumieć składnię i semantykę starych i nowych atrybutów, dzięki czemu można pomyślnie edytować atrybuty, które Cię interesują. 
1. Jeśli przepływ pracy wymaga zapisania manifestów w repozytorium źródłowym do późniejszego użycia, zalecamy ponowne osanie zapisanych manifestów w repozytorium za pomocą tego, które widzisz w obszarze **rejestracji aplikacji.**

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat relacji między obiektami aplikacji a jednostkami główną usługi, zobacz [Obiekty głównej aplikacji i usługi w usłudze Azure AD](app-objects-and-service-principals.md).
* Definicje niektórych podstawowych koncepcji deweloperskich platformy tożsamości firmy Microsoft można znaleźć w [glosariuszu dewelopera platformy tożsamości](developer-glossary.md) firmy Microsoft.

Skorzystaj z poniższej sekcji komentarzy, aby przekazać opinię, która pomaga uściślić i kształtować naszą zawartość.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
