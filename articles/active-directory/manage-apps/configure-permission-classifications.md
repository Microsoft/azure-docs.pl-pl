---
title: Konfigurowanie klasyfikacji uprawnień za pomocą usługi Azure AD
description: Dowiedz się, jak zarządzać delegowanymi klasyfikacjami uprawnień.
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
ms.openlocfilehash: be58f5cd18d32302d1e92f00afb7d7e0aae09410
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374475"
---
# <a name="configure-permission-classifications"></a>Konfigurowanie klasyfikacji uprawnień

Klasyfikacje uprawnień umożliwiają zidentyfikowanie wpływu różnych uprawnień zgodnie z zasadami organizacji i ocenami ryzyka. Można na przykład użyć klasyfikacji uprawnień w zasadach wyrażania zgody, aby zidentyfikować zestaw uprawnień, na które użytkownicy mogą się zgadzać.

## <a name="manage-permission-classifications"></a>Zarządzanie klasyfikacjami uprawnień

Obecnie obsługiwana jest tylko klasyfikacja uprawnień "Niski wpływ". Tylko delegowane uprawnienia, które nie wymagają zgody administratora, można sklasyfikować jako "Niski wpływ".

> [!TIP]
> Minimalnymi uprawnieniami wymaganymi do podstawowego logowania są , , i , które są `openid` `profile` `email` `User.Read` `offline_access` uprawnieniami delegowanymi w Microsoft Graph. Dzięki tym uprawnień aplikacja może odczytywać pełne szczegóły profilu zalogowaowego użytkownika i może zachować ten dostęp nawet wtedy, gdy użytkownik nie korzysta już z aplikacji.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wykonaj następujące kroki, aby sklasyfikować uprawnienia przy użyciu Azure Portal:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako [administrator globalny,](../roles/permissions-reference.md#global-administrator) [administrator aplikacji](../roles/permissions-reference.md#application-administrator)lub administrator aplikacji [w chmurze](../roles/permissions-reference.md#cloud-application-administrator)
1. Wybierz **Azure Active Directory**  >  **przedsiębiorstwa**  >  **Zgoda i uprawnienia**  >  **Klasyfikacje uprawnień.**
1. Wybierz **pozycję Dodaj uprawnienia,** aby sklasyfikować kolejne uprawnienie jako "Niski wpływ".
1. Wybierz interfejs API, a następnie wybierz delegowane uprawnienia.

W tym przykładzie sklasyfikowaliśmy minimalny zestaw uprawnień wymaganych dla logowania pojedynczego:

:::image type="content" source="media/configure-permission-classifications/permission-classifications.png" alt-text="Klasyfikacje uprawnień":::

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby sklasyfikować uprawnienia, możesz użyć najnowszego modułu Azure AD PowerShell (wersja [zapoznawcza) AzureADPreview.](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) Klasyfikacje uprawnień są konfigurowane w **obiekcie ServicePrincipal** interfejsu API, który publikuje uprawnienia.

#### <a name="list-the-current-permission-classifications-for-an-api"></a>Lista bieżących klasyfikacji uprawnień dla interfejsu API

1. Pobierz obiekt **ServicePrincipal** dla interfejsu API. W tym miejscu pobierzemy obiekt ServicePrincipal dla interfejsu API Microsoft Graph API:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Odczytaj delegowane klasyfikacje uprawnień dla interfejsu API:

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="classify-a-permission-as-low-impact"></a>Klasyfikowanie uprawnienia jako "Niski wpływ"

1. Pobierz obiekt **ServicePrincipal** dla interfejsu API. W tym miejscu pobierzemy obiekt ServicePrincipal dla interfejsu API Microsoft Graph API:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Znajdź delegowane uprawnienie, które chcesz sklasyfikować:

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. Ustaw klasyfikację uprawnień przy użyciu nazwy i identyfikatora uprawnień:

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="remove-a-delegated-permission-classification"></a>Usuwanie delegowanej klasyfikacji uprawnień

1. Pobierz obiekt **ServicePrincipal** dla interfejsu API. W tym miejscu pobierzemy obiekt ServicePrincipal dla interfejsu API Microsoft Graph API:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Znajdź delegowaną klasyfikację uprawnień, którą chcesz usunąć:

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
* [Dowiedz się, jak zarządzać zgodami na aplikacje i oceniać żądania zgody](manage-consent-requests.md)
* [Udzielanie zgody administratora całej dzierżawy dla aplikacji](grant-admin-consent.md)
* [Uprawnienia i zgoda na platformie tożsamości firmy Microsoft](../develop/v2-permissions-and-consent.md)

Aby uzyskać pomoc lub znaleźć odpowiedzi na swoje pytania:
* [Usługa Azure AD w witrynie Microsoft Q&A](/answers/topics/azure-active-directory.html)