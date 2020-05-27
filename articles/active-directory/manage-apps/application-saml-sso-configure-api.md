---
title: Używanie Microsoft Graph interfejsów API do konfigurowania logowania jednokrotnego opartego na protokole SAML
titleSuffix: Azure Active Directory
description: Musisz skonfigurować Logowanie jednokrotne oparte na protokole SAML dla wielu wystąpień aplikacji? Dowiedz się, jak zaoszczędzić czas przy użyciu interfejsów API Microsoft Graph, aby zautomatyzować konfigurację logowania jednokrotnego opartego na protokole SAML.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
ms.reviewer: luleon
ms.openlocfilehash: fd59dcdd566110d1df02333f5701c0c206442d5d
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/26/2020
ms.locfileid: "83846464"
---
# <a name="automate-saml-based-sso-app-configuration-with-microsoft-graph-api"></a>Automatyzowanie konfiguracji aplikacji SSO opartej na protokole SAML za pomocą interfejsu API Microsoft Graph

W tym artykule dowiesz się, jak utworzyć i skonfigurować aplikację z Galerii Azure Active Directory (Azure AD). W tym artykule użyto AWS jako przykładu, ale można wykonać kroki opisane w tym artykule dla dowolnej aplikacji opartej na protokole SAML w galerii usługi Azure AD.

**Procedura używania Microsoft Graph interfejsów API do automatyzowania konfigurowania logowania jednokrotnego opartego na protokole SAML**

| Krok  | Szczegóły  |
|---------|---------|
| [1. Utwórz aplikację galerii](#step-1-create-the-gallery-application) | Logowanie się do klienta interfejsu API <br> Pobierz aplikację galerii <br> Tworzenie aplikacji galerii|
| [2. Skonfiguruj Logowanie jednokrotne](#step-2-configure-single-sign-on) | Pobierz identyfikator obiektu aplikacji i identyfikator obiektu jednostki usługi <br> Ustawianie trybu logowania jednokrotnego <br> Ustaw podstawowe adresy URL SAML, takie jak identyfikator, adres URL odpowiedzi, adres URL logowania <br> Dodaj role aplikacji (opcjonalnie)|
| [3. Skonfiguruj mapowanie oświadczeń](#step-3-configure-claims-mapping) | Tworzenie zasad mapowania oświadczeń <br> Przypisywanie zasad mapowania oświadczeń do jednostki usługi|
| [4. Skonfiguruj certyfikat podpisywania](#step-4-configure-signing-certificate) | Tworzenie certyfikatu <BR> Dodawanie niestandardowego klucza podpisywania <br> Aktywuj niestandardowy klucz podpisywania|
| [5. Przypisywanie użytkowników](#step-5-assign-users) | Przypisywanie użytkowników i grup do aplikacji
| [6. Skonfiguruj stronę aplikacji](#step-6-configure-the-application-side)| Pobieranie metadanych SAML usługi Azure AD

**Lista wszystkich interfejsów API używanych w dokumentacji**

Upewnij się, że masz odpowiednie uprawnienia do wywoływania następujących interfejsów API.

|Typ zasobu |Metoda |
|---------|---------|
|[applicationTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta)|[ApplicationTemplate listy](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) <br>[Tworzenie wystąpienia applicationTemplate](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http)|
|[Właściwości serviceprincipals](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta)|[Aktualizuj obiekt serviceprincipal](https://docs.microsoft.com/graph/api/serviceprincipal-update?view=graph-rest-beta&tabs=http) <br> [Utwórz appRoleAssignments](https://docs.microsoft.com/graph/api/serviceprincipal-post-approleassignments?view=graph-rest-beta&tabs=http) <br> [Przypisz claimsMappingPolicies](https://docs.microsoft.com/graph/api/serviceprincipal-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http)|
|[zastosowania](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0)|[Aktualizuj aplikację](https://docs.microsoft.com/graph/api/application-update?view=graph-rest-beta&tabs=http)|
|[claimsMappingPolicy](https://docs.microsoft.com/graph/api/resources/claimsmappingpolicy?view=graph-rest-beta)| [Utwórz claimsMappingPolicy](https://docs.microsoft.com/graph/api/claimsmappingpolicy-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http)

>[!NOTE]
>Obiekty odpowiedzi pokazane w tym artykule mogą zostać skrócone w celu zapewnienia czytelności. Wszystkie właściwości zostaną zwrócone z rzeczywistego wywołania.

## <a name="step-1-create-the-gallery-application"></a>Krok 1. Tworzenie aplikacji galerii

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Zaloguj się do Microsoft Graph Explorer (zalecane), programu Poster lub dowolnego innego klienta interfejsu API, którego używasz

1. Uruchom [eksploratora Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)
2. Wybierz pozycję **Zaloguj się za pomocą firmy Microsoft** i zaloguj się przy użyciu poświadczeń administratora globalnego usługi Azure AD lub administratora aplikacji.
3. Po pomyślnym zalogowaniu zobaczysz szczegóły konta użytkownika w okienku po lewej stronie.

### <a name="retrieve-the-gallery-application-template-identifier"></a>Pobierz identyfikator szablonu aplikacji galerii

Aplikacje w galerii aplikacji usługi Azure AD zawierają [szablon aplikacji](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) opisujący metadane dla tej aplikacji. Za pomocą tego szablonu można utworzyć wystąpienie aplikacji i nazwy głównej usługi w dzierżawie na potrzeby zarządzania.

#### <a name="request"></a>Żądanie

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>Odpowiedź

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "Amazon Web Services (AWS)",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>Tworzenie aplikacji galerii

Przy użyciu identyfikatora szablonu, który został pobrany dla aplikacji w ostatnim kroku, [Utwórz wystąpienie](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) aplikacji i nazwy głównej usługi w dzierżawie.

#### <a name="request"></a>Żądanie

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/8b1025e4-1dd2-430b-a150-2ef79cd700f5/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>Odpowiedź


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

```http
HTTP/1.1 201 OK
Content-type: application/json


{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-configure-single-sign-on"></a>Krok 2. Konfigurowanie logowania jednokrotnego

### <a name="retrieve-app-object-id-and-service-principal-object-id"></a>Pobierz identyfikator obiektu aplikacji i identyfikator obiektu jednostki usługi

Użyj odpowiedzi z poprzedniego wywołania, aby pobrać i zapisać identyfikator obiektu aplikacji oraz identyfikator obiektu jednostki usługi.

```
"application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63"
}
"servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e"
}
```
### <a name="set-single-sign-on-mode"></a>Ustawianie trybu logowania jednokrotnego

W tym przykładzie ustawisz `saml` jako tryb logowania jednokrotnego w [typie zasobu serviceprincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta). Inne właściwości logowania jednokrotnego SAML, które można skonfigurować, to: `notificationEmailAddresses` , `loginUrl` i`samlSingleSignOnSettings.relayState`

#### <a name="request"></a>Żądanie

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: servicePrincipal/json

{
    "preferredSingleSignOnMode": "saml",
    "notificationEmailAddresses": [
        "john@contoso.com"
      ]
}
```

#### <a name="response"></a>Odpowiedź

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```

### <a name="set-basic-saml-urls-such-as-identifier-reply-url-sign-on-url"></a>Ustaw podstawowe adresy URL SAML, takie jak identyfikator, adres URL odpowiedzi, adres URL logowania

Ustaw identyfikatory i adresy URL odpowiedzi dla AWS w obiekcie aplikacji.

#### <a name="request"></a>Żądanie

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/beta/applications/cbc071a6-0fa5-4859-8g55-e983ef63df63
Content-type: applications/json

{
    "web": {
        "redirectUris": [
            "https://signin.aws.amazon.com/saml"
        ] 
    },
    "identifierUris": [
        "https://signin.aws.amazon.com/saml"
    ]    
}
```
#### <a name="response"></a>Odpowiedź

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```
### <a name="add-app-roles-optional"></a>Dodaj role aplikacji (opcjonalnie)

Jeśli aplikacja wymaga informacji o roli w tokenie, Dodaj definicję ról w obiekcie aplikacji. W przypadku usługi AWS można [włączyć funkcję aprowizacji użytkowników](../app-provisioning/application-provisioning-configure-api.md) , aby pobrać wszystkie role z tego konta AWS. 

Aby uzyskać więcej informacji, przeczytaj temat [Konfigurowanie roszczeń ról wystawionych w tokenie SAML](../develop/active-directory-enterprise-app-role-management.md)

> [!NOTE] 
> Podczas dodawania ról aplikacji nie należy modyfikować domyślnych ról aplikacji msiam_access. 

#### <a name="request"></a>Żądanie

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/beta/serviceprincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: serviceprincipals/json

{
"appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "454dc4c2-8176-498e-99df-8c4efcde41ef",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-admin,arn:aws:iam::212743507312:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Finance,WAAD",
            "displayName": "Finance,WAAD",
            "id": "8642d5fa-18a3-4245-ab8c-a96000c1a217",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-finance,arn:aws:iam::212743507312:saml-provider/WAAD"
        }
    ]

}
```

#### <a name="response"></a>Odpowiedź

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-3-configure-claims-mapping"></a>Krok 3. Konfigurowanie mapowania oświadczeń

### <a name="create-claims-mapping-policy"></a>Tworzenie zasad mapowania oświadczeń

Oprócz podstawowych oświadczeń skonfiguruj następujące oświadczenia usługi Azure AD do emisji w tokenie SAML:

| Nazwa zgłoszenia | Element źródłowy  |
|---------|---------|
| `https://aws.amazon.com/SAML/Attributes/Role` | assignedroles| 
| `https://aws.amazon.com/SAML/Attributes/RoleSessionName` | userPrincipalName |
| `https://aws.amazon.com/SAML/Attributes/SessionDuration` | "900" |
| role | assignedroles |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` | userPrincipalName |

#### <a name="request"></a>Żądanie

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/policies/claimsMappingPolicies
Content-type: claimsMappingPolicies/json

{
    "definition":[
            "{\"ClaimsMappingPolicy\": {
                \"Version\": 1,
                \"IncludeBasicClaimSet\": \"true\",
                \"ClaimsSchema\": [
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/Role\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"900\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"appRoles\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"
                    }
                ]
            }
        }"

    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

#### <a name="response"></a>Odpowiedź

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.claimsMappingPolicies",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: claimsMappingPolicies/json

{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/claimsMappingPolicies/$entity",
    "id": "6b33aa8e-51f3-41a6-a0fd-d660d276197a",
    "definition": [
        "{\"ClaimsMappingPolicy\": {\"Version\": 1,\"IncludeBasicClaimSet\": \"true\",\"ClaimsSchema\": [{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"https://aws.amazon.com/SAML/Attributes/Role\"\r\n                    },{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"},{\"Source\": \"user\",\"ID\": \"900\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"},{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"appRoles\"},{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"}]}}"
    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

### <a name="assign-claims-mapping-policy-to-service-principal"></a>Przypisywanie zasad mapowania oświadczeń do jednostki usługi

#### <a name="request"></a>Żądanie

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/claimsMappingPolicies/$ref

Content-type: claimsMappingPolicies/json

{
  "@odata.id":"https://graph.microsoft.com/beta/policies/claimsMappingPolicies/6b33aa8e-51f3-41a6-a0fd-d660d276197a"
}
```

#### <a name="response"></a>Odpowiedź

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-4-configure-signing-certificate"></a>Krok 4. Konfigurowanie certyfikatu podpisywania

Użycie interfejsu API [applicationTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta) domyślnie nie powoduje utworzenia certyfikatu podpisywania. Utwórz niestandardowy certyfikat podpisywania i przypisz go do aplikacji. 

### <a name="create-a-custom-signing-certificate"></a>Tworzenie niestandardowego certyfikatu podpisywania

Aby przetestować, możesz użyć następującego polecenia programu PowerShell, aby uzyskać certyfikat z podpisem własnym. Aby utworzyć certyfikat podpisywania dla środowiska produkcyjnego, należy użyć najlepszych rozwiązań w zakresie zabezpieczeń firmy.

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [string]$fqdn,
    [Parameter(Mandatory=$true)]
    [string]$pwd,
    [Parameter(Mandatory=$true)]
    [string]$location
) 

if (!$PSBoundParameters.ContainsKey('location'))
{
    $location = "."
} 

$cert = New-SelfSignedCertificate -certstorelocation cert:\currentuser\my -DnsName $fqdn
$pwdSecure = ConvertTo-SecureString -String $pwd -Force -AsPlainText
$path = 'cert:\currentuser\my\' + $cert.Thumbprint
$cerFile = $location + "\\" + $fqdn + ".cer"
$pfxFile = $location + "\\" + $fqdn + ".pfx" 

Export-PfxCertificate -cert $path -FilePath $pfxFile -Password $pwdSecure
Export-Certificate -cert $path -FilePath $cerFile
```

### <a name="add-a-custom-signing-key"></a>Dodawanie niestandardowego klucza podpisywania

Dodaj następujące informacje do jednostki usługi:

* Klucz prywatny
* Hasło
* Klucz publiczny 

Wyodrębnij klucz prywatny i publiczny szyfrowany algorytmem Base64 z pliku PFX. Aby dowiedzieć się więcej o właściwościach, Odczytaj [Typ zasobu poświadczenia](https://docs.microsoft.com/graph/api/resources/keycredential?view=graph-rest-1.0).

Upewnij się, że keyId dla poświadczenia dla poświadczeń użyty dla "Sign" pasuje do keyId passwordCredential.

Można wygenerować, pobierając `customkeyIdentifier` skrót odcisku palca certyfikatu.

```csharp
  public string GetSha256FromThumbprint(string thumbprint)
  {
      var message = Encoding.ASCII.GetBytes(thumbprint);
      SHA256Managed hashString = new SHA256Managed();
      return Convert.ToBase64String(hashString.ComputeHash(message));
  }
```

#### <a name="request"></a>Żądanie

> [!NOTE]
> Wartość "klucz" we właściwości poświadczenia klucza jest skracana na potrzeby odczytywania.

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "keyCredentials":[
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Sign",
            "key":"MIIKIAIBAz.....HBgUrDgMCERE20nuTptI9MEFCh2Ih2jaaLZBZGeZBRFVNXeZmAAgIH0A==",
            "displayName": "CN=awsAPI"
        },
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "e35a7d11-fef0-49ad-9f3e-aacbe0a42c42",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "key": "MIIDJzCCAg+gAw......CTxQvJ/zN3bafeesMSueR83hlCSyg==",
            "displayName": "CN=awsAPI"
        }

    ],
    "passwordCredentials": [
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "endDateTime": "2022-01-27T19:40:33Z",
            "startDateTime": "2020-04-20T19:40:33Z",
            "secretText": "61891f4ee44d"
        }
    ]
}
```

#### <a name="response"></a>Odpowiedź

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

### <a name="activate-the-custom-signing-key"></a>Aktywuj niestandardowy klucz podpisywania

Należy ustawić `preferredTokenSigningKeyThumbprint` Właściwość na odcisk palca certyfikatu, który ma być używany przez usługę Azure AD do podpisywania odpowiedzi SAML. 

#### <a name="request"></a>Żądanie

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "preferredTokenSigningKeyThumbprint": "AC09FEF18DDE6983EE2A164FBA3C4DD7518BD787"
}
```

#### <a name="response"></a>Odpowiedź

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```
## <a name="step-5-assign-users"></a>Krok 5. Przypisywanie użytkowników

### <a name="assign-users-and-groups-to-the-application"></a>Przypisywanie użytkowników i grup do aplikacji

Przypisz następującego użytkownika do nazwy głównej usługi i przypisz AWS_Role1. 

| Nazwa  | ID  |
|---------|---------|
| Identyfikator użytkownika (principalId) | 6cad4079-4e79-4a3f-9efb-ea30a14bdb26 |
| Typ (PrincipalType) | Użytkownik |
| Identyfikator roli aplikacji (appRoleId) | 454dc4c2-8176-498e-99df-8c4efcde41ef |
| servicePrincipalID (resourceId) | 515f62cb-d18a-4dca-bec3-bb0bf31deeea |

#### <a name="request"></a>Żądanie

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/appRoleAssignments

Content-type: appRoleAssignments/json

{
  "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
  "principalType": "User",
  "appRoleId":"454dc4c2-8176-498e-99df-8c4efcde41ef",
  "resourceId":"515f62cb-d18a-4dca-bec3-bb0bf31deeea"
}
```
#### <a name="response"></a>Odpowiedź

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 201 
Content-type: appRoleAssignments/json

{
    "id": "rq7hyzl4yECaNZleMrTpDV-OCe5TEl5Ao_o76XMrRFU",
    "creationTimestamp": "2020-04-23T17:38:13.2508567Z",
    "appRoleId": "454dc4c2-8176-498e-99df-8c4efcde41ef",
    "principalDisplayName": "User 1",
    "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
    "principalType": "User",
    "resourceDisplayName": "AWS API Created",
    "resourceId": "515f62cb-d18a-4dca-bec3-bb0bf31deeea"
}
```

Aby uzyskać więcej informacji, zobacz [appRoleAssignment](https://docs.microsoft.com/graph/api/resources/approleassignment?view=graph-rest-beta) typu zasobu.

## <a name="step-6-configure-the-application-side"></a>Krok 6. Konfigurowanie strony aplikacji

### <a name="get-azure-ad-saml-metadata"></a>Pobieranie metadanych SAML usługi Azure AD

Użyj następującego adresu URL, aby uzyskać metadane SAML usługi Azure AD dla określonej skonfigurowanej aplikacji. Metadane zawierają takie informacje, jak certyfikat podpisywania, usługi Azure AD entityID i usługa Azure AD SingleSignOnService.

https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id}

## <a name="next-steps"></a>Następne kroki
- [Konfigurowanie aprowizacji użytkowników przy użyciu Microsoft Graph interfejsów API](../app-provisioning/application-provisioning-configure-api.md)
- [Migrowanie aplikacji do usługi Azure AD za pomocą raportu działania aplikacji AD FS](migrate-adfs-application-activity.md)
