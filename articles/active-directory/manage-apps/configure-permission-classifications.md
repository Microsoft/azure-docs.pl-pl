---
title: Konfigurowanie klasyfikacji uprawnień za pomocą usługi Azure AD
description: Dowiedz się, jak zarządzać klasyfikacjami uprawnień delegowanych.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: phsignor
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 12a4ffb311e01ebb78b1ae392d1243c5d67eff6b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101644568"
---
# <a name="configure-permission-classifications"></a>Konfigurowanie klasyfikacji uprawnień

Klasyfikacje uprawnień umożliwiają zidentyfikowanie wpływu różnych uprawnień zgodnie z zasadami organizacji i ocenami ryzyka. Można na przykład użyć klasyfikacji uprawnień w zasadach zgody, aby zidentyfikować zbiór uprawnień dozwolonych dla użytkowników.

## <a name="manage-permission-classifications"></a>Zarządzaj klasyfikacjami uprawnień

Obecnie obsługiwana jest tylko Klasyfikacja uprawnień "niska wpływ". Tylko uprawnienia delegowane, które nie wymagają zgody administratora, mogą być klasyfikowane jako "niski wpływ".

> [!TIP]
> Minimalnymi uprawnieniami wymaganymi do logowania podstawowego są `openid` ,, `profile` `email` , `User.Read` i `offline_access` , które są wszystkie uprawnienia delegowane na Microsoft Graph. Dzięki tym uprawnieniom aplikacja może odczytać pełne szczegóły profilu zalogowanego użytkownika i może obsługiwać ten dostęp nawet wtedy, gdy użytkownik nie korzysta już z aplikacji.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wykonaj następujące kroki, aby sklasyfikować uprawnienia przy użyciu Azure Portal:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator [globalny](../roles/permissions-reference.md#global-administrator), [administrator aplikacji](../roles/permissions-reference.md#application-administrator)lub [administrator aplikacji w chmurze](../roles/permissions-reference.md#cloud-application-administrator)
1. Wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**  >  **i**  >  **klasyfikacje uprawnień**.
1. Wybierz pozycję **Dodaj uprawnienia** , aby sklasyfikować inne uprawnienie jako "niski wpływ".
1. Wybierz interfejs API, a następnie wybierz delegowane uprawnienia.

W tym przykładzie został sklasyfikowany minimalny zestaw uprawnień wymaganych do logowania jednokrotnego:

:::image type="content" source="media/configure-permission-classifications/permission-classifications.png" alt-text="Klasyfikacje uprawnień":::

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby sklasyfikować uprawnienia, można użyć najnowszego modułu programu Azure AD PowerShell w wersji zapoznawczej ( [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)). Klasyfikacje uprawnień są konfigurowane w obiekcie **Serviceprincipal** interfejsu API, który publikuje uprawnienia.

#### <a name="list-the-current-permission-classifications-for-an-api"></a>Wyświetlanie listy aktualnych klasyfikacji uprawnień dla interfejsu API

1. Pobierz obiekt **serviceprincipal** dla interfejsu API. W tym miejscu pobieramy obiekt serviceprincipal dla interfejsu API Microsoft Graph:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Przeczytaj klasyfikacje delegowanych uprawnień dla interfejsu API:

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="classify-a-permission-as-low-impact"></a>Klasyfikowanie uprawnień jako "słaby wpływ"

1. Pobierz obiekt **serviceprincipal** dla interfejsu API. W tym miejscu pobieramy obiekt serviceprincipal dla interfejsu API Microsoft Graph:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Znajdź delegowane uprawnienie, które chcesz sklasyfikować:

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. Ustaw klasyfikację uprawnień przy użyciu nazwy i identyfikatora uprawnienia:

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="remove-a-delegated-permission-classification"></a>Usuń klasyfikację uprawnień delegowanych

1. Pobierz obiekt **serviceprincipal** dla interfejsu API. W tym miejscu pobieramy obiekt serviceprincipal dla interfejsu API Microsoft Graph:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Znajdź klasyfikację uprawnień delegowanych, którą chcesz usunąć:

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. Usuń klasyfikację uprawnień:

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

---

## <a name="next-steps"></a>Następne kroki

Dodatkowe informacje:

* [Konfigurowanie ustawień zgody użytkownika](configure-user-consent.md)
* [Konfigurowanie przepływu pracy zgody administratora](configure-admin-consent-workflow.md)
* [Dowiedz się, jak zarządzać zgodą na aplikacje i oszacować wnioski o zgodę](manage-consent-requests.md)
* [Udzielanie zgody administratora całej dzierżawy dla aplikacji](grant-admin-consent.md)
* [Uprawnienia i zgoda na platformie tożsamości firmy Microsoft](../develop/v2-permissions-and-consent.md)

Aby uzyskać pomoc lub znaleźć odpowiedzi na pytania:
* [Usługa Azure AD w firmie Microsoft Q&A](/answers/topics/azure-active-directory.html)