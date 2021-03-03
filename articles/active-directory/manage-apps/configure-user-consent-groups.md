---
title: Konfigurowanie zgody właściciela grupy na aplikacje uzyskujące dostęp do danych grupy przy użyciu usługi Azure AD
description: Dowiedz się, czy właściciele grup i zespołów mogą wyrazić zgodę na aplikacje, które będą miały dostęp do danych grupy lub zespołu.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 24b2b8ee6398ef1306aee59b5d7ca7525284f96b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644551"
---
# <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Konfigurowanie zgody właściciela grupy na aplikacje uzyskujące dostęp do danych grupy

Właściciele grup i zespołów mogą autoryzować aplikacje, takie jak aplikacje opublikowane przez dostawców innych firm, w celu uzyskania dostępu do danych organizacji skojarzonych z grupą. Na przykład właściciel zespołu w usłudze Microsoft Teams może zezwalać aplikacji na odczytywanie wszystkich zespołów komunikatów w zespole lub wyświetlanie listy profilu podstawowego członków grupy. Aby dowiedzieć się więcej, zobacz temat [wyrażanie zgody na zasoby w usłudze Microsoft Teams](/microsoftteams/resource-specific-consent) .

## <a name="manage-group-owner-consent-to-apps"></a>Zarządzanie zgodą właściciela grupy na aplikacje

Można skonfigurować, którzy użytkownicy mogą wyrazić zgodę na aplikacje uzyskujące dostęp do swoich grup lub zespołów, lub wyłączyć tę opcję dla wszystkich użytkowników.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wykonaj następujące kroki, aby zarządzać zgodą właściciela grupy na aplikacje uzyskujące dostęp do danych grupy:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako [administrator globalny](../roles/permissions-reference.md#global-administrator).
2. Wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**  >  **i uprawnienia do**  >  **ustawień zgody użytkownika**.
3. W obszarze **właściciel grupy wyrażanie zgody na aplikacje uzyskujące dostęp do danych** wybierz opcję, którą chcesz włączyć.
4. Wybierz pozycję **Zapisz** , aby zapisać ustawienia.

W tym przykładzie wszyscy właściciele grupy mogą wyrazić zgodę na aplikacje uzyskujące dostęp do danych ich grup:

:::image type="content" source="media/configure-user-consent-groups/group-owner-consent.png" alt-text="Ustawienia zgody właściciela grupy":::

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Możesz użyć modułu Azure AD PowerShell w wersji zapoznawczej, [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview), aby włączyć lub wyłączyć możliwość wyrażania zgody na dostęp do danych organizacji przez właścicieli grup.

1. Upewnij się, że używasz modułu [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) . Ten krok jest ważny, jeśli zainstalowano moduł [AzureAD](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0) i moduł [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) .

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Nawiązywanie połączenia z usługą Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

1. Pobierz bieżącą wartość ustawień **zasad** dotyczących ustawień katalogu w dzierżawie. Wymaga to sprawdzenia, czy zostały utworzone ustawienia katalogu dla tej funkcji, a jeśli nie, użyj wartości z odpowiedniego szablonu ustawień katalogu.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

1. Zapoznaj się z wartościami ustawień. Istnieją dwie wartości ustawień, które definiują, którzy użytkownicy będą mogli zezwolić aplikacji na dostęp do danych swojej grupy:

    | Ustawienie       | Typ         | Opis  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Wartość logiczna | Flaga oznaczająca, czy właściciele grup mogą udzielać uprawnień specyficznych dla grupy. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid (identyfikator GUID) | Jeśli _EnableGroupSpecificConsent_ jest ustawiona na wartość "true", a ta wartość jest ustawiona na identyfikator obiektu grupy, członkowie wskazanej grupy będą uprawnieni do przyznawania uprawnień specyficznych dla grupy do grup, których są właścicielami. |

1. Zaktualizuj wartości ustawień dla żądanej konfiguracji:

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

1. Zapisz ustawienia.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
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
* [Usługa Azure AD w firmie Microsoft Q&A ](/answers/topics/azure-active-directory.html)