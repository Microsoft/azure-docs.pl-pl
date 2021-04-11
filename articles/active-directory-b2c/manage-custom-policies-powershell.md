---
title: Zarządzanie zasadami niestandardowymi przy użyciu programu PowerShell
titleSuffix: Azure AD B2C
description: Użyj polecenia cmdlet programu PowerShell Azure Active Directory (Azure AD), aby zarządzać programistycznymi Azure AD B2C zasad niestandardowych. Tworzenie, odczytywanie, aktualizowanie i usuwanie zasad niestandardowych przy użyciu programu PowerShell.
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cbe465c41363e8c0779440affeae78f999b4632d
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257436"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>Zarządzanie zasadami niestandardowymi Azure AD B2C przy użyciu Azure PowerShell

Azure PowerShell udostępnia kilka poleceń cmdlet dla niestandardowego zarządzania zasadami dla wiersza polecenia i opartego na skryptach w dzierżawie Azure AD B2C. Dowiedz się, jak używać modułu Azure AD PowerShell do:

* Wyświetlanie listy zasad niestandardowych w dzierżawie Azure AD B2C
* Pobieranie zasad z dzierżawy
* Aktualizowanie istniejących zasad przez zastąpienie jej zawartości
* Przekaż nowe zasady do dzierżawy Azure AD B2C
* Usuwanie zasad niestandardowych z dzierżawy

## <a name="prerequisites"></a>Wymagania wstępne

* [Azure AD B2C dzierżawy](tutorial-create-tenant.md)i poświadczenia dla użytkownika w katalogu z rolą [administratora zasad B2C IEF](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)
* [Zasady niestandardowe](tutorial-create-user-flows.md?pivots=b2c-custom-policy) przekazane do dzierżawy
* [Moduł Azure AD PowerShell dla programu Graph w **wersji zapoznawczej**](/powershell/azure/active-directory/install-adv2)

## <a name="connect-powershell-session-to-b2c-tenant"></a>Połącz sesję programu PowerShell z dzierżawcą B2C

Aby móc korzystać z zasad niestandardowych w dzierżawie Azure AD B2C, musisz najpierw połączyć sesję programu PowerShell z dzierżawcą za pomocą polecenia [Connect-AzureAD][Connect-AzureAD] .

Wykonaj następujące polecenie, zastępując `{b2c-tenant-name}` nazwę dzierżawy Azure AD B2C. Zaloguj się przy użyciu konta, któremu przypisano rolę [administrator zasad B2C IEF](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator) w katalogu.

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

Przykładowe dane wyjściowe polecenia zawierające pomyślne Logowanie:

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>Wyświetlanie listy wszystkich zasad niestandardowych w dzierżawie

Odnajdywanie zasad niestandardowych umożliwia administratorowi Azure AD B2C przeglądanie i Dodawanie logiki biznesowej do swoich operacji oraz zarządzanie nimi. Użyj polecenia [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] , aby zwrócić listę identyfikatorów zasad niestandardowych w dzierżawie Azure AD B2C.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy
```

Przykładowe dane wyjściowe polecenia:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy

Id
--
B2C_1A_TrustFrameworkBase
B2C_1A_TrustFrameworkExtensions
B2C_1A_signup_signin
B2C_1A_ProfileEdit
B2C_1A_PasswordReset
```

## <a name="download-a-policy"></a>Pobieranie zasad

Po przejrzeniu listy identyfikatorów zasad można wskazać określone zasady za pomocą [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] , aby pobrać jego zawartość.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

W tym przykładzie zostanie pobrane zasady z IDENTYFIKATORem *B2C_1A_signup_signin* :

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="contosob2c.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://contosob2c.onmicrosoft.com/B2C_1A_signup_signin" TenantObjectId="00000000-0000-0000-0000-000000000000">
  <BasePolicy>
    <TenantId>contosob2c.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

Aby edytować zawartość zasad lokalnie, należy przekazać dane wyjściowe polecenia do pliku z `-OutputFilePath` argumentem, a następnie otworzyć plik w ulubionym edytorze.

Przykładowe polecenie wysyła dane wyjściowe do pliku:

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>Aktualizowanie istniejących zasad

Po edytowaniu utworzonego lub pobranego pliku zasad można opublikować zaktualizowane zasady do Azure AD B2C za pomocą polecenia [Set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] .

Jeśli wydajesz `Set-AzureADMSTrustFrameworkPolicy` polecenie z identyfikatorem zasad, które już istnieją w dzierżawie Azure AD B2C, zawartość tych zasad zostanie zastąpiona.

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Przykładowe polecenie:

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

Więcej przykładów można znaleźć w dokumentacji polecenia [Set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] .

## <a name="upload-a-new-policy"></a>Przekaż nowe zasady

Po wprowadzeniu zmian w zasadach niestandardowych, które działają w środowisku produkcyjnym, można opublikować wiele wersji zasad dla scenariuszy testowania rezerwowego lub A/B. Można też utworzyć kopię istniejących zasad, zmodyfikować ją z kilkoma małymi zmianami, a następnie przekazać ją jako nowe zasady do użytku przez inną aplikację.

Użyj polecenia [New-AzureADMSTrustFrameworkPolicy][New-AzureADMSTrustFrameworkPolicy] , aby przekazać nowe zasady:

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Przykładowe polecenie:

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>Usuwanie zasad niestandardowych

Aby zachować czysty cykl życia operacji, zalecamy okresowe usuwanie nieużywanych zasad niestandardowych. Na przykład może być konieczne usunięcie starych wersji zasad po przeprowadzeniu migracji do nowego zestawu zasad i zweryfikowaniu funkcjonalności nowych zasad. Ponadto jeśli spróbujesz opublikować zestaw zasad niestandardowych i otrzymasz błąd, warto usunąć zasady, które zostały utworzone w ramach wydania zakończonego niepowodzeniem.

Użyj polecenia [Remove-AzureADMSTrustFrameworkPolicy][Remove-AzureADMSTrustFrameworkPolicy] , aby usunąć zasady z dzierżawy.

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

Przykładowe polecenie:

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>Rozwiązywanie problemów z przekazywaniem zasad

Podczas próby opublikowania nowych zasad niestandardowych lub zaktualizowania istniejących zasad niewłaściwe formatowanie XML i błędy w łańcuchu dziedziczenia plików zasad mogą spowodować błędy walidacji.

Załóżmy na przykład, że podjęto próbę zaktualizowania zasad o zawartości zawierającej źle sformułowany kod XML (dane wyjściowe są obcinane dla zwięzłości):

```Console
PS C:\> Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
Set-AzureADMSTrustFrameworkPolicy : Error occurred while executing PutTrustFrameworkPolicy
Code: AADB2C
Message: Validation failed: 1 validation error(s) found in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com".Schema validation error found at line
14 col 55 in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com": The element 'OutputClaims' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06' cannot contain text. List of possible elements expected: 'OutputClaim' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06'.
...
```

Aby uzyskać informacje na temat rozwiązywania problemów z zasadami niestandardowymi, zobacz [Rozwiązywanie problemów Azure AD B2C zasad niestandardowych i platformy obsługi tożsamości](./troubleshoot-custom-policies.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje o używaniu programu PowerShell do wdrażania zasad niestandardowych w ramach potoku ciągłej integracji/ciągłego dostarczania (CI/CD), zobacz [wdrażanie zasad niestandardowych z potoku usługi Azure DevOps](deploy-custom-policies-devops.md).

<!-- LINKS - External -->
[Connect-AzureAD]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/set-azureadmstrustframeworkpolicy
