---
title: Zarządzanie zasadami wyrażania zgody aplikacji w usłudze Azure AD
description: Dowiedz się, jak zarządzać wbudowanymi i niestandardowymi zasadami wyrażania zgody, aby kontrolować, kiedy można udzielić zgody.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 9c269e2ab37a08e48eedd3ee468080a382f9a8e3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558733"
---
# <a name="manage-app-consent-policies"></a>Zarządzanie zasadami wyrażania zgody aplikacji

Za pomocą programu Azure AD PowerShell można wyświetlać zasady zgody aplikacji i zarządzać nimi.

Zasady zgody aplikacji składają się z co najmniej jednego zestawu warunków "Includes" i zero lub więcej zestawów warunków "exclude". Aby można było rozważyć zdarzenie w zasadach zgody aplikacji, musi ono odpowiadać *co najmniej* jednemu zestawowi warunków "Includes" i nie *może być zgodne* z zestawem warunków "excludes".

Każdy zestaw warunków składa się z kilku warunków. Aby można było dopasować zdarzenie do zestawu warunków, muszą zostać spełnione *wszystkie* warunki w ustawieniu warunek.

Zasady zgody aplikacji, w których identyfikator rozpoczyna się od "Microsoft-" są wbudowanymi zasadami. Niektóre z tych wbudowanych zasad są używane w istniejących wbudowanych rolach katalogów. Na przykład `microsoft-application-admin` zasady zgody aplikacji opisują warunki, na podstawie których role Administrator aplikacji i administrator aplikacji w chmurze mogą udzielać zgody administratora w całej dzierżawie. Wbudowane zasady mogą być używane w niestandardowych rolach katalogu i do konfigurowania ustawień zgody użytkowników, ale nie można ich edytować ani usuwać.

## <a name="pre-requisites"></a>Wymagania wstępne

1. Upewnij się, że używasz modułu [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) . Ten krok jest ważny, jeśli zainstalowano moduł [AzureAD](/powershell/module/azuread/) i moduł [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) .

    ```powershell
    Remove-Module AzureAD -ErrorAction SilentlyContinue
    Import-Module AzureADPreview
    ```

1. Nawiązywanie połączenia z usługą Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

## <a name="list-existing-app-consent-policies"></a>Wyświetlanie listy istniejących zasad zgody aplikacji

Warto zacząć od zapoznania się z istniejącymi zasadami wyrażania zgody na aplikacje w organizacji:

1. Wyświetl listę wszystkich zasad zgody aplikacji:

   ```powershell
   Get-AzureADMSPermissionGrantPolicy | ft Id, DisplayName, Description
   ```

1. Wyświetlanie zestawów warunków "Includes" zasad:

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "includes"
    ```

1. Wyświetl zestawy warunków "excludes":

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "excludes"
    ```

## <a name="create-a-custom-app-consent-policy"></a>Tworzenie niestandardowych zasad zgody aplikacji

Wykonaj następujące kroki, aby utworzyć niestandardowe zasady zgody aplikacji:

1. Utwórz nowe, puste zasady zgody aplikacji.

   ```powershell
   New-AzureADMSPermissionGrantPolicy `
       -Id "my-custom-policy" `
       -DisplayName "My first custom consent policy" `
       -Description "This is a sample custom app consent policy."
   ```

1. Dodaj zestawy warunków "Includes".

   ```powershell
   # Include delegated permissions classified "low", for apps from verified publishers
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "includes" `
       -PermissionType "delegated" `
       -PermissionClassification "low" `
       -ClientApplicationsFromVerifiedPublisherOnly $true
   ```

   Powtórz ten krok, aby dodać dodatkowe zestawy warunków "include".

1. Opcjonalnie dodaj zestawy warunków "wykluczenia".

   ```powershell
   # Retrieve the service principal for the Azure Management API
   $azureApi = Get-AzureADServicePrincipal -Filter "servicePrincipalNames/any(n:n eq 'https://management.azure.com/')"

   # Exclude delegated permissions for the Azure Management API
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "excludes" `
       -PermissionType "delegated" `
       -ResourceApplication $azureApi.AppId
   ```

   Powtórz ten krok, aby dodać dodatkowe zestawy warunków "exclude".

Po utworzeniu zasad zgody aplikacji można [zezwolić na zgodę użytkownika](configure-user-consent.md?tabs=azure-powershell#allow-user-consent-subject-to-an-app-consent-policy) na zastosowanie tych zasad.

## <a name="delete-a-custom-app-consent-policy"></a>Usuwanie niestandardowych zasad zgody aplikacji

1. Poniżej przedstawiono sposób usuwania niestandardowych zasad zgody aplikacji. **Nie można tego cofnąć.**

   ```powershell
   Remove-AzureADMSPermissionGrantPolicy -Id "my-custom-policy"
   ```

> [!WARNING]
> Nie można przywrócić usuniętych zasad zgody aplikacji. Jeśli przypadkowo usuniesz zasady zgody aplikacji niestandardowych, należy ponownie utworzyć zasady.

---

### <a name="supported-conditions"></a>Obsługiwane warunki

Poniższa tabela zawiera listę obsługiwanych warunków zasad wyrażania zgody na aplikacje.

| Warunek | Opis|
|:---------------|:----------|
| PermissionClassification | [Klasyfikacja uprawnień](configure-permission-classifications.md) dla przyznawanego uprawnienia lub "wszystkie", aby dopasować się do klasyfikacji uprawnień (w tym uprawnień, które nie zostały sklasyfikowane). Wartość domyślna to "All". |
| Uprawnienie | Typ uprawnienia udzielonego uprawnienia. Użyj "aplikacji" dla uprawnień aplikacji (np. ról aplikacji) lub "delegowane" dla uprawnień delegowanych. <br><br>**Uwaga**: wartość "delegatedUserConsentable" wskazuje delegowane uprawnienia, które nie zostały skonfigurowane przez wydawcę interfejsu API w celu wymagania zgody administratora. Ta wartość może być używana w zasadach przyznawania uprawnień wbudowanych, ale nie można jej używać w niestandardowych zasadach przyznawania uprawnień. Wymagane. |
| ResourceApplication | **Identyfikator appid** aplikacji zasobów (np. interfejsu API), dla którego przyznano uprawnienia, lub "dowolny", aby dopasować się do dowolnej aplikacji lub interfejsu API zasobów. Wartość domyślna to "any". |
| Uprawnienia | Lista identyfikatorów uprawnień dla określonych uprawnień, które mają być zgodne z, lub lista z pojedynczą wartością "All", która jest zgodna z dowolnym uprawnieniem. Wartością domyślną jest pojedyncza wartość "All". <ul><li>Identyfikatory uprawnień delegowanych można znaleźć we właściwości **OAuth2Permissions** głównego obiektu interfejsu API.</li><li>Identyfikatory uprawnień aplikacji można znaleźć we właściwości **AppRoles** głównego obiektu interfejsu API.</li></ol> |
| ClientApplicationIds | Lista wartości **AppID** dla aplikacji klienckich, które mają być zgodne z programem, lub lista z pojedynczą wartością "All" w celu dopasowania do dowolnej aplikacji klienckiej. Wartością domyślną jest pojedyncza wartość "All". |
| ClientApplicationTenantIds | Lista Azure Active Directory identyfikatorów dzierżawy, w których zarejestrowano aplikację kliencką lub listę z pojedynczą wartością "All", która będzie zgodna z aplikacjami klienckimi zarejestrowanymi w dowolnej dzierżawie. Wartością domyślną jest pojedyncza wartość "All". |
| ClientApplicationPublisherIds | Lista identyfikatorów Microsoft Partner Network (MPN) dla [zweryfikowanych wydawców](../develop/publisher-verification-overview.md) aplikacji klienckiej lub lista z pojedynczą wartością "All", która jest zgodna z aplikacjami klienckimi z dowolnego wydawcy. Wartością domyślną jest pojedyncza wartość "All". |
| ClientApplicationsFromVerifiedPublisherOnly | Ustaw wartość tak `$true` , aby była zgodna z aplikacjami klienckimi z [zweryfikowanymi wydawcami](../develop/publisher-verification-overview.md). Ustaw wartość tak `$false` , aby pasowała do dowolnej aplikacji klienckiej, nawet jeśli nie ma zweryfikowanego wydawcy. Wartość domyślna to `$false`. |

## <a name="next-steps"></a>Następne kroki

Dodatkowe informacje:

* [Konfigurowanie ustawień zgody użytkownika](configure-user-consent.md)
* [Konfigurowanie przepływu pracy zgody administratora](configure-admin-consent-workflow.md)
* [Dowiedz się, jak zarządzać zgodą na aplikacje i oszacować wnioski o zgodę](manage-consent-requests.md)
* [Udzielanie zgody administratora całej dzierżawy dla aplikacji](grant-admin-consent.md)
* [Uprawnienia i zgoda na platformie tożsamości firmy Microsoft](../develop/v2-permissions-and-consent.md)

Aby uzyskać pomoc lub znaleźć odpowiedzi na pytania:
* [Usługa Azure AD w firmie Microsoft Q&A](/answers/products/)