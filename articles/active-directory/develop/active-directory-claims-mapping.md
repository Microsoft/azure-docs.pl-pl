---
title: Dostosowywanie oświadczeń aplikacji dzierżawy usługi Azure AD (PowerShell)
titleSuffix: Microsoft identity platform
description: Na tej stronie opisano Azure Active Directory oświadczeń.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: e77155f8a6efd3916ae90fcb562d688bb5b5126f
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598894"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>How to: Customize claims emited in tokens for a specific app in a tenant (Preview) (Dostosowywanie oświadczeń emitowanych w tokenach dla określonej aplikacji w dzierżawie (wersja zapoznawcza)

> [!NOTE]
> Ta funkcja zastępuje i zastępuje dostosowywanie oświadczeń [oferowane](active-directory-saml-claims-customization.md) obecnie w portalu. W tej samej aplikacji, jeśli oprócz metody Graph/PowerShell opisanej w tym dokumencie dostosujemy oświadczenia przy użyciu portalu, tokeny wystawione dla tej aplikacji zignoruje konfigurację w portalu. Konfiguracje wprowadzone za pomocą metod szczegółowo opisane w tym dokumencie nie zostaną odzwierciedlone w portalu.

> [!NOTE]
> Ta funkcja jest obecnie dostępna w publicznej wersji zapoznawczej. Przygotuj się na przywracanie lub usuwanie wszelkich zmian. Ta funkcja jest dostępna w dowolnej subskrypcji usługi Azure Active Directory (Azure AD) w publicznej wersji zapoznawczej. Jednak gdy funkcja stanie się ogólnie dostępna, niektóre jej aspekty mogą wymagać subskrypcji usługi Azure AD w wersji Premium. Ta funkcja obsługuje konfigurowanie zasad mapowania uwierzytelniania dla protokołów WS-Fed, SAML, OAuth OpenID Connect uwierzytelniania.

Ta funkcja jest używana przez administratorów dzierżawy do dostosowywania oświadczeń emitowanych w tokenach dla określonej aplikacji w ich dzierżawie. Zasad mapowania oświadczeń można używać do:

- Wybierz oświadczenia uwzględnione w tokenach.
- Utwórz typy oświadczenia, które jeszcze nie istnieją.
- Wybierz lub zmień źródło danych emitowanych w określonych oświadczeniach.

W tym artykule opisano kilka typowych scenariuszy, które mogą pomóc w zrozumieniu, jak używać typu zasad [mapowania oświadczeń](reference-claims-mapping-policy-type.md).

Podczas tworzenia zasad mapowania oświadczeń można również emitować oświadczenie z atrybutu rozszerzenia schematu katalogu w tokenach. Użyj *ExtensionID* dla atrybutu rozszerzenia zamiast *id* w `ClaimsSchema` elemencie.  Aby uzyskać więcej informacji na temat atrybutów rozszerzenia, zobacz [Using directory schema extension attributes](active-directory-schema-extensions.md)(Używanie atrybutów rozszerzenia schematu katalogu).

## <a name="prerequisites"></a>Wymagania wstępne

W poniższych przykładach utworzysz, zaktualizujemy, połączysz i usuniesz zasady dla jednostki usługi. Zasady mapowania oświadczeń można przypisywać tylko do obiektów jednostki usługi. Jeśli jesteś nowym użytkownikiem usługi Azure AD, zalecamy zapoznanie się z informacjami na temat sposobu uzyskania dzierżawy usługi [Azure AD](quickstart-create-new-tenant.md) przed kontynuowaniem pracy z tymi przykładami.

Aby rozpocząć pracę, wykonaj następujące czynności:

1. Pobierz najnowszą wersję zapoznawczą modułu [Azure AD PowerShell Module](https://www.powershellgallery.com/packages/AzureADPreview).
1. Uruchom polecenie Połącz, aby zalogować się do konta administratora usługi Azure AD. Uruchom to polecenie za każdym razem, gdy rozpoczynasz nową sesję.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Aby wyświetlić wszystkie zasady utworzone w organizacji, uruchom następujące polecenie. Zalecamy uruchomienie tego polecenia po większości operacji w następujących scenariuszach, aby sprawdzić, czy zasady są tworzone zgodnie z oczekiwaniami.

   ``` powershell
   Get-AzureADPolicy
   ```

## <a name="omit-the-basic-claims-from-tokens"></a>Pomijanie podstawowych oświadczeń z tokenów

W tym przykładzie utworzysz zasady, [](reference-claims-mapping-policy-type.md#claim-sets) które usuwają podstawowy zestaw uwierzytelniania z tokenów wystawionych dla połączonych podmiotów zabezpieczeń usługi.

1. Utwórz zasady mapowania oświadczeń. Te zasady, połączone z określonymi jednostkami usługi, usuwa podstawowy zestaw uwierzytelniania z tokenów.
   1. Aby utworzyć zasady, uruchom to polecenie:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Aby wyświetlić nowe zasady i uzyskać identyfikator ObjectId zasad, uruchom następujące polecenie:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Przypisz zasady do jednostki usługi. Musisz również uzyskać identyfikator ObjectId jednostki usługi.
   1. Aby wyświetlić wszystkie jednostki usługi organizacji, możesz utworzyć zapytanie dotyczące interfejsu [API Microsoft Graph .](/graph/traverse-the-graph) Możesz też w [Microsoft Graph Explorer zalogować](https://developer.microsoft.com/graph/graph-explorer)się do konta usługi Azure AD.
   2. Jeśli masz identyfikator ObjectId jednostki usługi, uruchom następujące polecenie:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="include-the-employeeid-and-tenantcountry-as-claims-in-tokens"></a>Uwzględnij oświadczenia EmployeeID i TenantCountry w tokenach

W tym przykładzie utworzysz zasady, które dodają identyfikatory EmployeeID i TenantCountry do tokenów wystawionych dla połączonych podmiotów zabezpieczeń usługi. Identyfikator EmployeeID jest emitowany jako typ oświadczenia nazwy w tokenach SAML i w obu tokenach JWTs. TenantCountry jest emitowany jako kraj/region typ oświadczenia w tokeny SAML i JWTs. W tym przykładzie nadal uwzględniamy podstawowe oświadczenia ustawione w tokenach.

1. Utwórz zasady mapowania oświadczeń. Te zasady połączone z określonymi jednostkami usługi dodają oświadczenia EmployeeID i TenantCountry do tokenów.
   1. Aby utworzyć zasady, uruchom następujące polecenie:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Aby wyświetlić nowe zasady i uzyskać identyfikator ObjectId zasad, uruchom następujące polecenie:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Przypisz zasady do jednostki usługi. Musisz również uzyskać identyfikator ObjectId jednostki usługi.
   1. Aby wyświetlić wszystkie jednostki usługi organizacji, możesz utworzyć zapytanie dotyczące interfejsu [API Microsoft Graph .](/graph/traverse-the-graph) Lub w [eksploratorze Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)zaloguj się do konta usługi Azure AD.
   2. Jeśli masz identyfikator ObjectId jednostki usługi, uruchom następujące polecenie:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="use-a-claims-transformation-in-tokens"></a>Używanie przekształcania oświadczeń w tokenach

W tym przykładzie utworzysz zasady, które emitują oświadczenie niestandardowe "JoinedData" do JWT wystawionego dla połączonych podmiotów zabezpieczeń usługi. To oświadczenie zawiera wartość utworzoną przez dołączenie danych przechowywanych w extensionattribute1 atrybutu obiektu użytkownika z ".sandbox". W tym przykładzie wykluczamy podstawowe oświadczenia ustawione w tokenach.

1. Utwórz zasady mapowania oświadczeń. Te zasady połączone z określonymi jednostkami usługi dodają oświadczenia EmployeeID i TenantCountry do tokenów.
   1. Aby utworzyć zasady, uruchom następujące polecenie:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Aby wyświetlić nowe zasady i uzyskać identyfikator ObjectId zasad, uruchom następujące polecenie:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Przypisz zasady do jednostki usługi. Musisz również uzyskać identyfikator ObjectId jednostki usługi.
   1. Aby wyświetlić wszystkie jednostki usługi organizacji, możesz utworzyć zapytanie dotyczące interfejsu [API Microsoft Graph .](/graph/traverse-the-graph) Możesz też w [Microsoft Graph Explorer zalogować](https://developer.microsoft.com/graph/graph-explorer)się do konta usługi Azure AD.
   2. Jeśli masz identyfikator ObjectId jednostki usługi, uruchom następujące polecenie:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Aplikacje, które odbierają tokeny, polegają na tym, że wartości uwierzytelniania są autorytatywne wystawiane przez usługę Azure AD i nie mogą zostać naruszone. Jednak podczas modyfikowania zawartości tokenu za pomocą zasad mapowania oświadczeń te założenia mogą nie być już poprawne. Aplikacje muszą jawnie potwierdzić, że tokeny zostały zmodyfikowane przez twórcę zasad mapowania oświadczeń, aby chronić się przed zasadami mapowania oświadczeń utworzonymi przez złośliwych aktorów. Można to zrobić w następujący sposób:

- Konfigurowanie niestandardowego klucza podpisywania
- Zaktualizuj manifest aplikacji, aby akceptował zamapowane oświadczenia.
 
Bez tego usługa Azure AD zwróci kod [ `AADSTS50146` błędu](reference-aadsts-error-codes.md#aadsts-error-codes).

### <a name="custom-signing-key"></a>Niestandardowy klucz podpisywania

Aby dodać niestandardowy klucz podpisywania do obiektu jednostki usługi, możesz użyć polecenia cmdlet Azure PowerShell, aby utworzyć poświadczenie klucza certyfikatu dla [`New-AzureADApplicationKeyCredential`](/powerShell/module/Azuread/New-AzureADApplicationKeyCredential) obiektu Aplikacji.

Aplikacje, które mają włączone mapowanie oświadczeń, muszą weryfikować swoje klucze podpisywania tokenu, dołączając OpenID Connect `appid={client_id}` [żądania metadanych.](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) Poniżej przedstawiono format dokumentu OpenID Connect metadanych, który powinien być używany:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="update-the-application-manifest"></a>Aktualizowanie manifestu aplikacji

Alternatywnie możesz ustawić właściwość `acceptMappedClaims` na w `true` [manifeście aplikacji](reference-app-manifest.md). Zgodnie z dokumentacją typu zasobu [apiApplication](/graph/api/resources/apiapplication#properties)umożliwia to aplikacji korzystanie z mapowania oświadczeń bez określania niestandardowego klucza podpisywania.

Wymaga to, aby żądani odbiorcy tokenów używali zweryfikowanych nazw domen dzierżawy usługi Azure AD, co oznacza, że należy ustawić wartość (reprezentowaną przez obiekt w manifeście aplikacji), na przykład na wartość lub (po prostu używając domyślnej nazwy `Application ID URI` `identifierUris` `https://contoso.com/my-api` `https://contoso.onmicrosoft.com/my-api` dzierżawy).

Jeśli nie używasz zweryfikowanej domeny, usługa Azure AD zwróci kod błędu z komunikatem `AADSTS501461` "AcceptMappedClaims is supported only for a token audience matching the application GUID or an audience within the tenant's verified domains (Akceptowanie zamapowanych uwierzytelniania jest obsługiwane tylko dla odbiorców tokenu pasujących do identyfikatora GUID aplikacji lub odbiorców w domenach zweryfikowanych w *dzierżawie). Zmień identyfikator zasobu lub użyj klucza podpisywania specyficznego dla aplikacji".*

## <a name="next-steps"></a>Następne kroki

- Przeczytaj artykuł [referencyjny na temat typu zasad mapowania oświadczeń,](reference-claims-mapping-policy-type.md) aby dowiedzieć się więcej.
- Aby dowiedzieć się, jak dostosować oświadczenia wystawione w tokenie JĘZYKA SAML za pośrednictwem interfejsu Azure Portal, zobacz How [to: Customize claims issued in the SAML token for enterprise applications](active-directory-saml-claims-customization.md) (Jak dostosować oświadczenia wystawione w tokenie SAML dla aplikacji dla przedsiębiorstw)
- Aby dowiedzieć się więcej o atrybutach rozszerzenia, zobacz Using directory schema extension attributes in claims (Używanie atrybutów rozszerzenia [schematu katalogu w oświadczeniach).](active-directory-schema-extensions.md)
