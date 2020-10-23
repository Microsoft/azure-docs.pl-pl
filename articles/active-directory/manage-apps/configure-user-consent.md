---
title: Konfigurowanie sposobu, w jaki użytkownicy końcowi wyrażają zgodę na aplikacje za pomocą usługi Azure AD
description: Dowiedz się, jak i kiedy użytkownicy mogą wyrazić zgodę na aplikacje, które będą miały dostęp do danych organizacji.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperfq2
ms.openlocfilehash: c1c0c3038c687b7f91d3c75d8c4c9589c5e245a3
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427632"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Konfigurowanie sposobu, w jaki użytkownicy końcowi wyrażają zgodę na aplikacje

Aplikacje można zintegrować z platformą tożsamości firmy Microsoft, aby umożliwić użytkownikom logowanie się przy użyciu konta służbowego i uzyskiwanie dostępu do danych organizacji w celu zapewnienia zaawansowanych środowisk opartych na danych.

Aby aplikacja mogła uzyskać dostęp do danych organizacji, użytkownik musi udzielić uprawnień aplikacji. Różne uprawnienia zezwalają na różne poziomy dostępu. Domyślnie wszyscy użytkownicy mogą wyrazić zgodę na aplikacje dla uprawnień, które nie wymagają zgody administratora. Na przykład użytkownik może wyrazić zgodę na zezwolenie aplikacji na dostęp do swojej skrzynki pocztowej, ale nie może wyrazić zgody, aby umożliwić aplikacji swobodnego dostęp do odczytu i zapisu do wszystkich plików w organizacji.

Dzięki umożliwieniu użytkownikom dostępu do danych aplikacji użytkownicy mogą łatwo uzyskać użyteczne aplikacje i pracować wydajnie. Jednak w niektórych sytuacjach taka konfiguracja może reprezentować ryzyko, jeśli nie jest dokładnie monitorowana i kontrolowana.

> [!IMPORTANT]
> Aby zmniejszyć ryzyko złośliwych aplikacji próbujących nakłonić użytkowników do udzielenia im dostępu do danych organizacji, zalecamy zezwolenie użytkownikom na zgodę tylko na aplikacje, które zostały opublikowane przez [zweryfikowanego wydawcę](../develop/publisher-verification-overview.md).

## <a name="user-consent-settings"></a>Ustawienia zgody użytkownika

Zasady zgody aplikacji opisują warunki, które muszą zostać spełnione, aby można było przystąpić do aplikacji. Te zasady mogą obejmować warunki dotyczące aplikacji żądającej dostępu, a także uprawnienia, do których aplikacja żąda.

Wybierając zasady zgody aplikacji, które mają zastosowanie do wszystkich użytkowników, można ustawić limity, kiedy użytkownicy końcowi mogą przyznawać zgodę na aplikacje oraz kiedy będą musieli zażądać przeglądu i zatwierdzenia przez administratora:

* **Wyłącz wyrażanie zgody użytkownika** — użytkownicy nie mogą przyznawać uprawnień do aplikacji. Użytkownicy mogą nadal logować się do aplikacji, które wcześniej wyraziły zgodę lub które są do nich wysyłane przez administratorów w ich imieniu, ale nie będą mogli wyrazić zgody na nowe uprawnienia lub do nowych aplikacji. Tylko użytkownicy, którym przypisano rolę katalogu, która obejmuje uprawnienie do udzielania zgody, będą mogli wyrazić zgodę na nowe aplikacje.

* **Użytkownicy mogą wyrazić zgodę na aplikacje od zweryfikowanych wydawców lub w organizacji, ale tylko dla wybranych uprawnień** — wszyscy użytkownicy będą mogli wyrazić zgodę tylko na aplikacje, które zostały opublikowane przez [zweryfikowanego wydawcę](../develop/publisher-verification-overview.md) i aplikacje zarejestrowane w dzierżawie. Użytkownicy mogą wyrazić zgodę tylko na uprawnienia, które zostały sklasyfikowane jako "niski wpływ". Należy [sklasyfikować uprawnienia](configure-permission-classifications.md) , aby wybrać uprawnienia, do których użytkownicy mogą wyrazić zgodę.

* **Użytkownicy mogą wyrazić zgodę na wszystkie aplikacje** — ta opcja umożliwia wszystkim użytkownikom wyrażanie zgody na wszelkie uprawnienia, które nie wymagają zgody administratora na żadną aplikację.

* **Zasady zgody aplikacji niestandardowych** — Aby uzyskać jeszcze więcej opcji dotyczących warunków, które są stosowane w przypadku wyrażania zgody użytkownika, możesz [utworzyć niestandardowe zasady zgody na aplikacje](manage-app-consent-policies.md#create-a-custom-app-consent-policy)i skonfigurować je do stosowania w przypadku zgody użytkownika.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby skonfigurować ustawienia zgody użytkowników za pomocą Azure Portal:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako [administrator globalny](../roles/permissions-reference.md#global-administrator--company-administrator).
1. Wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**  >  **i uprawnienia do**  >  **ustawień zgody użytkownika**.
1. W obszarze **wyrażanie zgody użytkownika na aplikacje**wybierz ustawienie zgody, które chcesz skonfigurować dla wszystkich użytkowników.
1. Wybierz pozycję **Zapisz** , aby zapisać ustawienia.

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="Ustawienia zgody użytkownika":::

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Możesz użyć najnowszego modułu programu Azure AD PowerShell w wersji zapoznawczej, [AzureADPreview](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true), aby wybrać zasady zgody aplikacji, które regulują zgodę użytkownika na aplikacje.

#### <a name="disable-user-consent"></a>Wyłącz wyrażanie zgody użytkownika

Aby wyłączyć wyrażanie zgody użytkownika, ustaw zasady zgody, które regulują zgodę użytkownika na wartość pustą:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

#### <a name="allow-user-consent-subject-to-an-app-consent-policy"></a>Zezwalaj na zgodę użytkownika na podlegające zasadom zgody na aplikacje

Aby zezwolić użytkownikom na zgodę, wybierz zasady zgody aplikacji, które powinny zarządzać autoryzacją użytkowników w celu udzielenia zgody na aplikacje:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.{consent-policy-id}")
  ```

Zamień `{consent-policy-id}` na identyfikator zasad, które chcesz zastosować. Możesz wybrać [niestandardowe zasady zgody aplikacji](manage-app-consent-policies.md#create-a-custom-app-consent-policy) , które zostały utworzone, lub wybrać jedną z następujących zasad:

| ID (Identyfikator) | Opis |
|:---|:------------|
| Microsoft-User-wartość domyślna — niska | **Zezwalaj użytkownikom na wyrażanie zgody na aplikacje od zweryfikowanych wydawców dla wybranych uprawnień**<br /> Zezwalaj na ograniczoną zgodę użytkownika tylko w przypadku aplikacji z zweryfikowanych wydawców i aplikacji zarejestrowanych w dzierżawie i tylko dla uprawnień sklasyfikowanych jako "niski wpływ". (Nie zapomnij [sklasyfikować uprawnień](configure-permission-classifications.md) , aby wybrać uprawnienia, do których użytkownicy mogą wyrazić zgodę). |
| Microsoft-User-default-Legacy | **Zezwalaj na wyrażanie zgody użytkownika na aplikacje**<br /> Ta opcja umożliwia wszystkim użytkownikom wyrażanie zgody na wszelkie uprawnienia, które nie wymagają zgody administratora, dla żadnej aplikacji |
  
Na przykład, aby włączyć zgodę użytkownika zgodnie z zasadami wbudowanymi `microsoft-user-default-low` :

```powershell
Set-AzureADMSAuthorizationPolicy `
   -Id "authorizationPolicy" `
   -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.microsoft-user-default-low")
```

---

> [!TIP]
> [Włącz przepływ pracy zgody administratora](configure-admin-consent-workflow.md) , aby umożliwić użytkownikom zażądanie przeglądu i zatwierdzenia aplikacji, do których użytkownik nie może wyrazić zgody — na przykład gdy użytkownik ma zgodę na jego wyłączenie lub gdy aplikacja żąda uprawnień, których użytkownik nie może udzielić.

## <a name="risk-based-step-up-consent"></a>Wyrażanie zgody na ryzyko

Wyrażanie ryzyka krok po kroku pomaga zmniejszyć narażenie użytkowników na złośliwe aplikacje, które podejmują [nielegalne żądania zgody](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants). Jeśli firma Microsoft wykryje ryzykowne żądanie zgody użytkownika końcowego, żądanie będzie wymagało zgody administratora na "krok po kroku". Ta funkcja jest domyślnie włączona, ale powoduje to zmianę zachowania tylko wtedy, gdy jest włączona zgoda użytkownika końcowego.

Po wykryciu ryzykownego żądania zgody w monicie zostanie wyświetlony komunikat informujący o konieczności zatwierdzenia przez administratora. Jeśli [przepływ pracy żądania zgody administratora](configure-admin-consent-workflow.md) jest włączony, użytkownik może wysłać żądanie do administratora w celu dalszej kontroli bezpośrednio z poziomu monitu o zgodę. Jeśli nie jest włączona, zostanie wyświetlony następujący komunikat:

* **AADSTS90094:** &lt; clientAppDisplayName &gt; musi mieć uprawnienia dostępu do zasobów w organizacji, które mogą przyznawać tylko Administratorzy. Poproś administratora o udzielenie uprawnienia do tej aplikacji, aby można było z niej korzystać.

W takim przypadku zdarzenie inspekcji zostanie również zarejestrowane z kategorią "ApplicationManagement", typem działania "zgody na aplikację" oraz przyczynie stanu "wykryto ryzykowną aplikację".

> [!IMPORTANT]
> Administratorzy powinni uważnie [oszacować wszystkie żądania zgody](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) przed zatwierdzeniem żądania, szczególnie w przypadku wykrycia ryzyka przez firmę Microsoft.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Wyłączenie lub ponowne włączenie zgody na ryzyko krok po kroku przy użyciu programu PowerShell

Możesz użyć modułu Azure AD PowerShell w wersji zapoznawczej, aby wyłączyć krok do zgody administratora wymagany w [przypadkach, w](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true)których firma Microsoft wykryje ryzyko lub ponownie je włączać, jeśli wcześniej została wyłączona.

1. Upewnij się, że używasz modułu [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true) . Ten krok jest ważny, jeśli zainstalowano moduł [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0&preserve-view=true) i moduł [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true) .

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

    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```

1. Opis wartości ustawień:

    | Ustawienie       | Typ         | Opis  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Wartość logiczna |  Flaga oznaczająca, czy zgoda użytkownika zostanie zablokowana w przypadku wykrycia ryzykownego żądania. |

1. Zaktualizuj wartość ustawień dla żądanej konfiguracji:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
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

## <a name="next-steps"></a>Następne kroki

Dodatkowe informacje:

* [Konfigurowanie ustawień zgody użytkownika](configure-user-consent.md)
* [Zarządzanie zasadami wyrażania zgody aplikacji](manage-app-consent-policies.md)
* [Konfigurowanie przepływu pracy zgody administratora](configure-admin-consent-workflow.md)
* [Dowiedz się, jak zarządzać zgodą na aplikacje i oszacować wnioski o zgodę](manage-consent-requests.md)
* [Udzielanie zgody administratora całej dzierżawy dla aplikacji](grant-admin-consent.md)
* [Uprawnienia i zgoda na platformie tożsamości firmy Microsoft](../develop/active-directory-v2-scopes.md)

Aby uzyskać pomoc lub znaleźć odpowiedzi na pytania:
* [Usługa Azure AD w systemie StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
