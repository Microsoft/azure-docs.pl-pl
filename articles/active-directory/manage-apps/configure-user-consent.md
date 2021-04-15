---
title: Konfigurowanie sposobu wyrażania zgody przez użytkowników końcowych na aplikacje przy użyciu usługi Azure AD
description: Dowiedz się, jak i kiedy użytkownicy mogą wyrazić zgodę na aplikacje, które będą mieć dostęp do danych organizacji.
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
ms.openlocfilehash: 95a651f6201c9f60500c9191821edb7eb76b8535
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374441"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Konfigurowanie sposobu, w jaki użytkownicy końcowi wyrażają zgodę na aplikacje

Aplikacje można zintegrować z platformą tożsamości firmy Microsoft, aby umożliwić użytkownikom logowanie się przy użyciu konta służbowego i uzyskiwanie dostępu do danych organizacji w celu zapewnienia rozbudowanych doświadczeń opartych na danych.

Zanim aplikacja będzie mieć dostęp do danych organizacji, użytkownik musi udzielić aplikacji uprawnień, aby to zrobić. Różne uprawnienia zezwalają na różne poziomy dostępu. Domyślnie wszyscy użytkownicy mogą wyrazić zgodę na aplikacje w przypadku uprawnień, które nie wymagają zgody administratora. Na przykład domyślnie użytkownik może wyrazić zgodę na dostęp aplikacji do skrzynki pocztowej, ale nie może wyrazić zgody na umożliwienie aplikacji niesprawdowego dostępu do odczytu i zapisu we wszystkich plikach w organizacji.

Zezwalając użytkownikom na udzielanie aplikacjom dostępu do danych, użytkownicy mogą łatwo uzyskać przydatne aplikacje i pracować wydajnie. Jednak w niektórych sytuacjach taka konfiguracja może stanowić ryzyko, jeśli nie jest dokładnie monitorowana i kontrolowana.

> [!IMPORTANT]
> Aby zmniejszyć ryzyko, że złośliwe aplikacje spróbują nakłonić użytkowników do udzielenia im dostępu do danych organizacji, zalecamy wyrażenie zgody tylko dla aplikacji opublikowanych przez zweryfikowanego [wydawcę.](../develop/publisher-verification-overview.md)

## <a name="user-consent-settings"></a>Ustawienia zgody użytkownika

Zasady wyrażania zgody aplikacji opisują warunki, które muszą zostać spełnione, aby można było wyrazić zgodę na aplikację. Te zasady mogą zawierać warunki dotyczące aplikacji żądających dostępu, a także uprawnienia, których żąda aplikacja.

Wybierając zasady wyrażania zgody dla wszystkich użytkowników, można ustawić limity dotyczące sytuacji, w której użytkownicy końcowi mogą udzielić zgody na aplikacje oraz kiedy będą oni wymagać weryfikacji i zatwierdzenia przez administratora:

* **Wyłącz zgodę użytkownika —** użytkownicy nie mogą udzielać uprawnień aplikacjom. Użytkownicy mogą nadal logować się do aplikacji, na które wcześniej wyrażali zgodę lub na które administratorzy wyrażali zgodę w ich imieniu, ale nie będą mogli wyrazić zgody na nowe uprawnienia lub na nowe aplikacje samodzielnie. Tylko użytkownicy, którym udzielono roli katalogu, która obejmuje uprawnienie do udzielania zgody, będą mogli wyrazić zgodę na nowe aplikacje.

* Użytkownicy mogą wyrazić zgodę na aplikacje od zweryfikowanych wydawców lub **organizacji,** ale tylko w przypadku [](../develop/publisher-verification-overview.md) wybranych uprawnień — wszyscy użytkownicy mogą wyrazić zgodę tylko na aplikacje opublikowane przez zweryfikowanego wydawcę i aplikacje zarejestrowane w dzierżawie. Użytkownicy mogą wyrazić zgodę tylko na uprawnienia sklasyfikowane jako "niski wpływ". Należy [sklasyfikować uprawnienia,](configure-permission-classifications.md) aby wybrać uprawnienia, na które użytkownicy mogą wyrazić zgodę.

* **Użytkownicy mogą wyrazić zgodę na wszystkie aplikacje** — ta opcja umożliwia wszystkim użytkownikom wyrażanie zgody na wszelkie uprawnienia, które nie wymagają zgody administratora dla żadnej aplikacji.

* **Niestandardowe zasady wyrażania** zgody aplikacji — aby uzyskać jeszcze [](manage-app-consent-policies.md#create-a-custom-app-consent-policy)więcej opcji dotyczących warunków, które obowiązują podczas wyrażania zgody przez użytkownika, możesz utworzyć niestandardowe zasady wyrażania zgody dla aplikacji i skonfigurować je do stosowania w celu wyrażenia zgody przez użytkownika.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby skonfigurować ustawienia zgody użytkownika za pośrednictwem Azure Portal:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako [administrator globalny.](../roles/permissions-reference.md#global-administrator)
1. Wybierz **Azure Active Directory**  >  **przedsiębiorstwa Ustawienia**  >  **wyrażania zgody i** uprawnień przez  >  **użytkownika.**
1. W **obszarze Zgoda użytkownika dla aplikacji** wybierz ustawienie zgody, które chcesz skonfigurować dla wszystkich użytkowników.
1. Wybierz **pozycję Zapisz,** aby zapisać ustawienia.

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="Ustawienia zgody użytkownika":::

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Możesz użyć najnowszego modułu Azure AD PowerShell (wersja zapoznawcza), [AzureADPreview,](/powershell/azure/active-directory/install-adv2?preserve-view=true&view=azureadps-2.0-preview)aby wybrać zasady wyrażania zgody aplikacji, które określają zgodę użytkownika dla aplikacji.

#### <a name="disable-user-consent"></a>Wyłączanie zgody użytkownika

Aby wyłączyć zgodę użytkownika, ustaw puste zasady wyrażania zgody, które określają zgodę użytkownika:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

#### <a name="allow-user-consent-subject-to-an-app-consent-policy"></a>Zezwalanie na zgodę użytkownika w przypadku zasad zgody aplikacji

Aby zezwolić na zgodę użytkownika, wybierz zasady wyrażania zgody dla aplikacji, które powinny określać autoryzację użytkowników w celu udzielania zgody na aplikacje:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.{consent-policy-id}")
  ```

Zastąp `{consent-policy-id}` identyfikatorem zasad, które chcesz zastosować. Możesz wybrać niestandardowe [zasady wyrażania](manage-app-consent-policies.md#create-a-custom-app-consent-policy) zgody dla aplikacji, które zostały utworzone, lub wybrać jedną z następujących wbudowanych zasad:

| ID (Identyfikator) | Opis |
|:---|:------------|
| microsoft-user-default-low | **Zezwalaj na zgodę użytkownika dla aplikacji zweryfikowanych wydawców dla wybranych uprawnień**<br /> Zezwalaj na ograniczoną zgodę użytkownika tylko dla aplikacji zweryfikowanych wydawców i aplikacji zarejestrowanych w Twojej dzierżawie i tylko dla uprawnień sklasyfikowanych jako "Niski wpływ". (Nie zapomnij [sklasyfikować](configure-permission-classifications.md) uprawnień, aby wybrać uprawnienia, na które użytkownicy mogą wyrazić zgodę). |
| microsoft-user-default-legacy | **Zezwalaj na zgodę użytkownika dla aplikacji**<br /> Ta opcja umożliwia wszystkim użytkownikom wyrażanie zgody na wszelkie uprawnienia, które nie wymagają zgody administratora dla żadnej aplikacji |
  
Aby na przykład włączyć zgodę użytkownika na podstawie wbudowanych zasad `microsoft-user-default-low` :

```powershell
Set-AzureADMSAuthorizationPolicy `
   -Id "authorizationPolicy" `
   -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.microsoft-user-default-low")
```

---

> [!TIP]
> [](configure-admin-consent-workflow.md) Włącz przepływ pracy wyrażania zgody przez administratora, aby umożliwić użytkownikom żądanie przejrzenia i zatwierdzenia przez administratora aplikacji, na które użytkownik nie może wyrazić zgody — na przykład gdy zgoda użytkownika została wyłączona lub gdy aplikacja żąda uprawnień, na które użytkownik nie może udzielić.

## <a name="risk-based-step-up-consent"></a>Zgoda na krok po kroku oparta na ryzyku

Zgoda krokowa oparta na ryzyku pomaga zmniejszyć narażenie użytkowników na złośliwe aplikacje, które [żądają niedozwolonej zgody.](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) Jeśli firma Microsoft wykryje ryzykowne żądanie zgody użytkownika końcowego, zamiast tego będzie wymagać "kroku" w celu wyrażenia zgody przez administratora. Ta funkcja jest domyślnie włączona, ale spowoduje zmianę zachowania tylko wtedy, gdy jest włączona zgoda użytkownika końcowego.

Po wykryciu ryzykownych żądań zgody w wierszu zgody zostanie wyświetlony komunikat informujący o potrzebie zatwierdzenia przez administratora. Jeśli przepływ [pracy żądania zgody administratora jest](configure-admin-consent-workflow.md) włączony, użytkownik może wysłać żądanie do administratora w celu dalszego przejrzenia bezpośrednio z monitu o wyrażenie zgody. Jeśli ta opcja nie jest włączona, zostanie wyświetlony następujący komunikat:

* **AADSTS90094:** &lt; Element clientAppDisplayName wymaga uprawnień dostępu do zasobów w organizacji, &gt; które może przyznać tylko administrator. Poproś administratora o udzielenie uprawnienia do tej aplikacji, aby można było z niej korzystać.

W takim przypadku zdarzenie inspekcji zostanie również zarejestrowane z kategorią "ApplicationManagement", typem działania "Zgoda na aplikację" i przyczyną stanu "Wykryto ryzykowną aplikację".

> [!IMPORTANT]
> Administratorzy powinni dokładnie [ocenić wszystkie żądania zgody](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) przed zatwierdzeniem żądania, szczególnie gdy firma Microsoft wykryła ryzyko.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Wyłączanie lub ponowne włączanie zgody krokowej opartej na ryzyku przy użyciu programu PowerShell

Możesz użyć modułu Azure AD PowerShell (wersja zapoznawcza), [AzureADPreview,](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)aby wyłączyć krok do zgody administratora wymagany w przypadkach, gdy firma Microsoft wykryje ryzyko lub ponownie je włączy, jeśli została wcześniej wyłączona.

1. Upewnij się, że używasz modułu [AzureADPreview.](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) Ten krok jest ważny, jeśli zainstalowano zarówno moduł [AzureAD,](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0) jak i [moduł AzureADPreview).](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Połącz się z programem PowerShell usługi Azure AD.

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

    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```

1. Opis wartości ustawień:

    | Ustawienie       | Typ         | Opis  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Wartość logiczna |  Flaga wskazująca, czy zgoda użytkownika zostanie zablokowana po wykryciu ryzykownych żądań. |

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
* [Dowiedz się, jak zarządzać zgodami na aplikacje i oceniać żądania zgody](manage-consent-requests.md)
* [Udzielanie zgody administratora całej dzierżawy dla aplikacji](grant-admin-consent.md)
* [Uprawnienia i zgoda na platformie tożsamości firmy Microsoft](../develop/v2-permissions-and-consent.md)

Aby uzyskać pomoc lub znaleźć odpowiedzi na swoje pytania:
* [Usługa Azure AD w witrynie Microsoft Q&A.](/answers/topics/azure-active-directory.html)