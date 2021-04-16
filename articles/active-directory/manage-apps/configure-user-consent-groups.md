---
title: Konfigurowanie zgody właściciela grupy na dostęp aplikacji do danych grupy przy użyciu usługi Azure AD
description: Dowiedz się, czy właściciele grupy i zespołu mogą wyrazić zgodę na aplikacje, które będą mieć dostęp do danych grupy lub zespołu.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: iangithinji
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: be28148aacf270f2f3cfabad4cbd5f03afa05d3b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374424"
---
# <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Konfigurowanie zgody właściciela grupy na dostęp aplikacji do danych grupy

Właściciele grupy i zespołu mogą autoryzować aplikacje, takie jak aplikacje opublikowane przez innych dostawców, do uzyskiwania dostępu do danych organizacji skojarzonych z grupą. Na przykład właściciel zespołu w aplikacji Microsoft Teams może zezwolić aplikacji na odczytywanie wszystkich komunikatów usługi Teams w zespole lub wyświetlić podstawowy profil członków grupy. Aby dowiedzieć [się więcej, zobacz](/microsoftteams/resource-specific-consent) Zgoda specyficzna dla zasobów w aplikacji Microsoft Teams.

## <a name="manage-group-owner-consent-to-apps"></a>Zarządzanie zgodą właściciela grupy na aplikacje

Możesz skonfigurować użytkowników, którzy mogą wyrazić zgodę na dostęp aplikacji do danych grup lub zespołów, lub wyłączyć tę opcję dla wszystkich użytkowników.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wykonaj następujące kroki, aby zarządzać zgodą właściciela grupy na dostęp aplikacji do danych grupy:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako [administrator globalny.](../roles/permissions-reference.md#global-administrator)
2. Wybierz **Azure Active Directory**  >  **przedsiębiorstwa Ustawienia**  >  **wyrażania zgody i** uprawnień przez  >  **użytkownika.**
3. W **obszarze Zgoda właściciela grupy dla aplikacji,** które mają dostęp do danych, wybierz opcję, która chcesz włączyć.
4. Wybierz **pozycję Zapisz,** aby zapisać ustawienia.

W tym przykładzie wszyscy właściciele grup mogą wyrazić zgodę na dostęp aplikacji do danych grup:

:::image type="content" source="media/configure-user-consent-groups/group-owner-consent.png" alt-text="Ustawienia zgody właściciela grupy":::

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Możesz użyć modułu Azure AD PowerShell (wersja zapoznawcza), [AzureADPreview,](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)aby włączyć lub wyłączyć możliwość wyrażania zgody przez właścicieli grup na dostęp do danych organizacji dla grup, których są właścicielami.

1. Upewnij się, że używasz modułu [AzureADPreview.](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) Ten krok jest ważny, jeśli zainstalowano zarówno moduł [AzureAD,](/powershell/module/azuread/) jak i [moduł AzureADPreview).](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Nawiązywanie połączenia z programem PowerShell usługi Azure AD.

   ```powershell
   Connect-AzureAD
   ```

1. Pobierz bieżącą wartość ustawień katalogu **Ustawienia zasad zgody** w dzierżawie. Wymaga to sprawdzenia, czy ustawienia katalogu dla tej funkcji zostały utworzone, a jeśli nie, przy użyciu wartości z odpowiedniego szablonu ustawień katalogu.

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

1. Zrozumienie wartości ustawień. Istnieją dwie wartości ustawień, które definiują, którzy użytkownicy będą mogli zezwolić aplikacji na dostęp do danych grupy:

    | Ustawienie       | Typ         | Opis  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Wartość logiczna | Flaga wskazująca, czy właściciele grup mogą udzielać uprawnień specyficznych dla grupy. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid (identyfikator GUID) | Jeśli _parametr EnableGroupSpecificConsent_ jest ustawiony na wartość "True", a ta wartość jest ustawiona na identyfikator obiektu grupy, członkowie zidentyfikowanej grupy będą autoryzowani do udzielania uprawnień specyficznych dla grupy do posiadanych grup. |

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
* [Konfigurowanie przepływu pracy wyrażania zgody przez administratora](configure-admin-consent-workflow.md)
* [Dowiedz się, jak zarządzać zgodami na aplikacje i oceniać żądania zgody](manage-consent-requests.md)
* [Udzielanie zgody administratora całej dzierżawy dla aplikacji](grant-admin-consent.md)
* [Uprawnienia i zgoda na platformie tożsamości firmy Microsoft](../develop/v2-permissions-and-consent.md)

Aby uzyskać pomoc lub znaleźć odpowiedzi na swoje pytania:
* [Usługa Azure AD w witrynie Microsoft Q&A ](/answers/topics/azure-active-directory.html)