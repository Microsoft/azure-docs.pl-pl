---
title: Zarządzanie zasadami wyrażania zgody aplikacji w usłudze Azure AD
description: Dowiedz się, jak zarządzać wbudowanymi i niestandardowymi zasadami wyrażania zgody aplikacji, aby kontrolować, kiedy można wyrazić zgodę.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: iangithinji
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 44299fadd17d1acfa292dd88bd57c8be4a44be36
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375699"
---
# <a name="manage-app-consent-policies"></a>Zarządzanie zasadami wyrażania zgody aplikacji

Za pomocą programu PowerShell usługi Azure AD można wyświetlać zasady wyrażania zgody aplikacji i zarządzać nimi.

Zasady wyrażania zgody aplikacji składają się z co najmniej jednego zestawu warunków "obejmuje" i zero lub więcej zestawów warunków "wyklucza". Aby zdarzenie było rozważane w zasadach zgody  aplikacji, musi być zgodne z co najmniej jednym zestawem warunków *"obejmuje"* i nie może być zgodne z żadnym zestawem warunków "wyklucza".

Każdy zestaw warunków składa się z kilku warunków. Aby zdarzenie było zgodne z zestawem warunków, *muszą* zostać spełnione wszystkie warunki w zestawie warunków.

Zasady wyrażania zgody aplikacji, w przypadku których identyfikator zaczyna się od "microsoft-" są wbudowanymi zasadami. Niektóre z tych wbudowanych zasad są używane w istniejących wbudowanych rolach katalogu. Na przykład zasady wyrażania zgody na aplikację opisują warunki, w których role Administrator aplikacji i Administrator aplikacji w chmurze mogą udzielić zgody administratora `microsoft-application-admin` całej dzierżawy. Wbudowane zasady mogą być używane w niestandardowych rolach katalogu i do konfigurowania ustawień zgody użytkownika, ale nie można ich edytować ani usuwać.

## <a name="pre-requisites"></a>Wymagania wstępne

1. Upewnij się, że używasz modułu [AzureADPreview.](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) Ten krok jest ważny, jeśli zainstalowano zarówno moduł [AzureAD,](/powershell/module/azuread/) jak i [moduł AzureADPreview).](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)

    ```powershell
    Remove-Module AzureAD -ErrorAction SilentlyContinue
    Import-Module AzureADPreview
    ```

1. Nawiązywanie połączenia z programem PowerShell usługi Azure AD.

   ```powershell
   Connect-AzureAD
   ```

## <a name="list-existing-app-consent-policies"></a>Lista istniejących zasad wyrażania zgody dla aplikacji

Warto zacząć od zapoznania się z istniejącymi zasadami wyrażania zgody aplikacji w organizacji:

1. Lista wszystkich zasad wyrażania zgody dla aplikacji:

   ```powershell
   Get-AzureADMSPermissionGrantPolicy | ft Id, DisplayName, Description
   ```

1. Wyświetl zestawy warunków "includes" zasad:

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "includes"
    ```

1. Wyświetl zestawy warunków "wyklucza":

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "excludes"
    ```

## <a name="create-a-custom-app-consent-policy"></a>Tworzenie niestandardowych zasad wyrażania zgody aplikacji

Wykonaj następujące kroki, aby utworzyć niestandardowe zasady wyrażania zgody dla aplikacji:

1. Utwórz nowe puste zasady zgody aplikacji.

   ```powershell
   New-AzureADMSPermissionGrantPolicy `
       -Id "my-custom-policy" `
       -DisplayName "My first custom consent policy" `
       -Description "This is a sample custom app consent policy."
   ```

1. Dodaj zestawy warunków "includes".

   ```powershell
   # Include delegated permissions classified "low", for apps from verified publishers
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "includes" `
       -PermissionType "delegated" `
       -PermissionClassification "low" `
       -ClientApplicationsFromVerifiedPublisherOnly $true
   ```

   Powtórz ten krok, aby dodać dodatkowe zestawy warunków dołączania.

1. Opcjonalnie dodaj zestawy warunków "wyklucza".

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

   Powtórz ten krok, aby dodać dodatkowe zestawy warunków wykluczania.

Po utworzeniu zasad zgody aplikacji możesz [zezwolić](configure-user-consent.md?tabs=azure-powershell#allow-user-consent-subject-to-an-app-consent-policy) użytkownikom na te zasady.

## <a name="delete-a-custom-app-consent-policy"></a>Usuwanie niestandardowych zasad zgody aplikacji

1. Poniżej przedstawiono sposób usuwania niestandardowych zasad zgody aplikacji. **Nie można tego cofnąć.**

   ```powershell
   Remove-AzureADMSPermissionGrantPolicy -Id "my-custom-policy"
   ```

> [!WARNING]
> Nie można przywrócić usuniętych zasad zgody aplikacji. Jeśli przypadkowo usuniesz niestandardowe zasady wyrażania zgody dla aplikacji, konieczne będzie ich ponowne utworzenie.

---

### <a name="supported-conditions"></a>Obsługiwane warunki

W poniższej tabeli przedstawiono listę obsługiwanych warunków dla zasad wyrażania zgody aplikacji.

| Warunek | Opis|
|:---------------|:----------|
| Klasyfikacja uprawnień | Klasyfikacja [uprawnień dla](configure-permission-classifications.md) udzielonego uprawnienia lub "wszystkie" do dopasowania do dowolnej klasyfikacji uprawnień (w tym uprawnień, które nie są klasyfikowane). Wartość domyślna to "all". |
| Permissiontype | Typ uprawnień udzielanych uprawnień. Użyj "aplikacji" dla uprawnień aplikacji (np. ról aplikacji) lub "delegowane" w przypadku uprawnień delegowanych. <br><br>**Uwaga:** Wartość "delegatedUserConsentable" wskazuje uprawnienia delegowane, które nie zostały skonfigurowane przez wydawcę interfejsu API w celu wymagania zgody administratora — ta wartość może być używana we wbudowanych zasadach udzielania uprawnień, ale nie może być używana w niestandardowych zasadach udzielania uprawnień. Wymagane. |
| ResourceApplication | **AppId** aplikacji zasobów (np. interfejsu API), dla której przyznawane jest uprawnienie, lub "any" w celu dopasowania do dowolnej aplikacji zasobu lub interfejsu API. Wartość domyślna to "any". |
| Uprawnienia | Lista identyfikatorów uprawnień dla określonych uprawnień, które mają być zgodne z, lub lista z pojedynczą wartością "wszystkie" do dopasowania do dowolnego uprawnienia. Wartość domyślna to pojedyncza wartość "all". <ul><li>Delegowane identyfikatory uprawnień można znaleźć we właściwości **OAuth2Permissions** obiektu ServicePrincipal interfejsu API.</li><li>Identyfikatory uprawnień aplikacji można znaleźć we właściwości **AppRoles** obiektu ServicePrincipal interfejsu API.</li></ol> |
| ClientApplicationIds | Lista wartości **AppId dla** aplikacji klienckich, z których mają być zgodne, lub lista z pojedynczą wartością "all" w celu dopasowania do dowolnej aplikacji klienckiej. Wartość domyślna to pojedyncza wartość "all". |
| ClientApplicationTenantIds | Lista identyfikatorów Azure Active Directory, w których zarejestrowano aplikację kliency, lub lista z pojedynczą wartością "wszystko" do dopasowania do aplikacji klienckich zarejestrowanych w dowolnej dzierżawie. Wartość domyślna to pojedyncza wartość "all". |
| ClientApplicationPublisherIds | Lista identyfikatorów Microsoft Partner Network (MPN) zweryfikowanych wydawców aplikacji klienckiej lub lista z pojedynczą wartością "wszystko" do dopasowania do aplikacji klienckich od dowolnego wydawcy. [](../develop/publisher-verification-overview.md) Wartość domyślna to pojedyncza wartość "all". |
| ClientApplicationsFromVerifiedPublisherOnly | Ustaw na wartość , aby dopasować wartość tylko `$true` w aplikacjach klienckich ze [zweryfikowanymi wydawcami.](../develop/publisher-verification-overview.md) Ustaw na wartość , aby była dopasowana w dowolnej aplikacji `$false` klienckiej, nawet jeśli nie ma zweryfikowanego wydawcy. Wartość domyślna to `$false`. |

## <a name="next-steps"></a>Następne kroki

Dodatkowe informacje:

* [Konfigurowanie ustawień zgody użytkownika](configure-user-consent.md)
* [Konfigurowanie przepływu pracy zgody administratora](configure-admin-consent-workflow.md)
* [Dowiedz się, jak zarządzać zgodami na aplikacje i oceniać żądania zgody](manage-consent-requests.md)
* [Udzielanie zgody administratora całej dzierżawy dla aplikacji](grant-admin-consent.md)
* [Uprawnienia i zgoda na platformie tożsamości firmy Microsoft](../develop/v2-permissions-and-consent.md)

Aby uzyskać pomoc lub znaleźć odpowiedzi na swoje pytania:
* [Usługa Azure AD w witrynie Microsoft Q&A](/answers/products/)