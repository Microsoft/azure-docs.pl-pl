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
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: 0c9844d5e3f65dba5e51170367cfd16715a08883
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763469"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Konfigurowanie sposobu, w jaki użytkownicy końcowi wyrażają zgodę na aplikacje

Aplikacje można zintegrować z platformą tożsamości firmy Microsoft, aby umożliwić użytkownikom logowanie się przy użyciu konta służbowego i uzyskiwanie dostępu do danych organizacji w celu zapewnienia zaawansowanych środowisk opartych na danych.

Aby aplikacja mogła uzyskać dostęp do danych organizacji, użytkownik musi udzielić uprawnień aplikacji. Różne uprawnienia zezwalają na różne poziomy dostępu. Domyślnie wszyscy użytkownicy mogą wyrazić zgodę na aplikacje dla uprawnień, które nie wymagają zgody administratora. Na przykład użytkownik może wyrazić zgodę na zezwolenie aplikacji na dostęp do swojej skrzynki pocztowej, ale nie może wyrazić zgody, aby umożliwić aplikacji swobodnego dostęp do odczytu i zapisu do wszystkich plików w organizacji.

Dzięki umożliwieniu użytkownikom dostępu do danych aplikacji użytkownicy mogą łatwo uzyskać użyteczne aplikacje i pracować wydajnie. Jednak w niektórych sytuacjach taka konfiguracja może reprezentować ryzyko, jeśli nie jest dokładnie monitorowana i kontrolowana.

## <a name="user-consent-settings"></a>Ustawienia zgody użytkownika

Aby kontrolować, jakie przypadki użytkownicy mogą wyrazić zgodę na aplikacje, wybierz zasady zgody, które będą stosowane do wszystkich użytkowników. Poniżej przedstawiono trzy opcje zasad zgody:

* **Wyłącz wyrażanie zgody użytkownika** — użytkownicy nie mogą przyznawać uprawnień do aplikacji. Użytkownicy mogą nadal logować się do aplikacji, które wcześniej wyraziły zgodę lub które są do nich wysyłane przez administratorów w ich imieniu, ale nie będą mogli wyrazić zgody na nowe uprawnienia lub do nowych aplikacji. Tylko użytkownicy, którym przypisano rolę katalogu, która obejmuje uprawnienie do udzielania zgody, będą mogli wyrazić zgodę na nowe uprawnienia lub nowe aplikacje.

* **Użytkownicy mogą wyrazić zgodę na aplikacje od zweryfikowanych wydawców, ale tylko dla wybranych uprawnień (wersja zapoznawcza)** — wszyscy użytkownicy mogą wyrazić zgodę tylko na aplikacje, które zostały opublikowane przez [zweryfikowanego wydawcę](../develop/publisher-verification-overview.md) i aplikacje zarejestrowane w dzierżawie. Użytkownicy mogą wyrazić zgodę tylko na uprawnienia, które zostały sklasyfikowane jako "niski wpływ".

  Należy pamiętać o [klasyfikowaniu uprawnień](#configure-permission-classifications-preview) w celu wybrania uprawnień, które użytkownicy mogą wyrazić zgodę.

* **Użytkownicy mogą wyrazić zgodę na wszystkie aplikacje** — ta opcja umożliwia wszystkim użytkownikom wyrażanie zgody na wszelkie uprawnienia, które nie wymagają zgody administratora na żadną aplikację. 

   Aby zmniejszyć ryzyko złośliwych aplikacji próbujących nakłonić użytkowników do udzielenia im dostępu do danych organizacji, zalecamy zezwolenie użytkownikom na zgodę tylko na aplikacje, które zostały opublikowane przez [zweryfikowanego wydawcę](../develop/publisher-verification-overview.md).

### <a name="configure-user-consent-settings-from-the-azure-portal"></a>Skonfiguruj ustawienia zgody użytkownika na podstawie Azure Portal

Aby skonfigurować ustawienia zgody użytkowników za pomocą Azure Portal:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako [administrator globalny](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
1. Wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**  >  **i uprawnienia do**  >  **ustawień zgody użytkownika**.
1. W obszarze **wyrażanie zgody użytkownika na aplikacje**wybierz ustawienie zgody, które chcesz skonfigurować dla wszystkich użytkowników.
1. Wybierz pozycję **Zapisz** , aby zapisać ustawienia.

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="Ustawienia zgody użytkownika":::

> [!TIP]
> Rozważ [włączenie przepływu pracy zgody administratora](configure-admin-consent-workflow.md) , aby umożliwić użytkownikom zażądanie przeglądu i zatwierdzenia aplikacji, do których użytkownik nie może wyrazić zgodę — na przykład jeśli użytkownik wyraża zgodę użytkownika na wyłączenie lub gdy aplikacja żąda uprawnień, których użytkownik nie może udzielić.

### <a name="configure-user-consent-settings-using-powershell"></a>Konfigurowanie ustawień zgody użytkowników przy użyciu programu PowerShell

Możesz użyć najnowszego modułu programu Azure AD PowerShell w wersji zapoznawczej, [AzureADPreview](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview), aby określić, które zasady zgody decydują o zgodę użytkownika na aplikacje.

* **Wyłącz zgodę użytkownika** — aby wyłączyć wyrażanie zgody użytkownika, ustaw zasady zgody, które regulują zgodę użytkownika na wartość pustą:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

* **Zezwalaj użytkownikom na wyrażanie zgody na aplikacje od zweryfikowanych wydawców dla wybranych uprawnień (wersja zapoznawcza)** — aby zezwolić na ograniczoną zgodę użytkownika tylko za aplikacje zweryfikowanych wydawców i aplikacji zarejestrowanych w dzierżawie i tylko dla uprawnień sklasyfikowanych jako "niski wpływ", skonfiguruj wbudowane zasady zgody o nazwie `microsoft-user-default-low` :

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-low")
  ```

   Nie zapomnij [sklasyfikować uprawnień](#configure-permission-classifications-preview) , aby wybrać uprawnienia, do których użytkownicy mogą wyrazić zgodę.

* **Zezwalaj użytkownikom na wyrażanie zgody na wszystkie aplikacje** — aby zezwolić użytkownikom na wyrażanie zgody na wszystkie aplikacje:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-legacy")
  ```

   Ta opcja umożliwia wszystkim użytkownikom wyrażanie zgody na wszelkie uprawnienia, które nie wymagają zgody administratora na żadną aplikację. Firma Microsoft zaleca, aby zezwolić użytkownikom na zgodę tylko na aplikacje przed zweryfikowanymi wydawcami.

## <a name="configure-permission-classifications-preview"></a>Konfigurowanie klasyfikacji uprawnień (wersja zapoznawcza)

Klasyfikacje uprawnień umożliwiają zidentyfikowanie wpływu różnych uprawnień zgodnie z zasadami organizacji i ocenami ryzyka. Można na przykład użyć klasyfikacji uprawnień w zasadach zgody, aby zidentyfikować zbiór uprawnień dozwolonych dla użytkowników.

> [!NOTE]
> Obecnie obsługiwana jest tylko Klasyfikacja uprawnień "niska wpływ". Tylko uprawnienia delegowane, które nie wymagają zgody administratora, mogą być klasyfikowane jako "niski wpływ".

### <a name="classify-permissions-using-the-azure-portal"></a>Klasyfikowanie uprawnień przy użyciu Azure Portal

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako [administrator globalny](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
1. Wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**  >  **i**  >  **klasyfikacje uprawnień**.
1. Wybierz pozycję **Dodaj uprawnienia** , aby sklasyfikować inne uprawnienie jako "niski wpływ". 
1. Wybierz interfejs API, a następnie wybierz delegowane uprawnienia.

W tym przykładzie został sklasyfikowany minimalny zestaw uprawnień wymaganych do logowania jednokrotnego:

:::image type="content" source="media/configure-user-consent/permission-classifications.png" alt-text="Klasyfikacje uprawnień":::

> [!TIP]
> W przypadku interfejsu API Microsoft Graph minimalne uprawnienia niezbędne do wykonania podstawowego logowania jednokrotnego to `openid` , `profile` `User.Read` i `offline_access` . Dzięki tym uprawnieniom aplikacja może odczytywać szczegóły profilu zalogowanego użytkownika i może obsługiwać ten dostęp nawet wtedy, gdy użytkownik nie korzysta już z aplikacji.

### <a name="classify-permissions-using-powershell"></a>Klasyfikowanie uprawnień przy użyciu programu PowerShell

Aby sklasyfikować uprawnienia, można użyć najnowszego modułu programu Azure AD PowerShell w wersji zapoznawczej ( [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)). Klasyfikacje uprawnień są konfigurowane w obiekcie **Serviceprincipal** interfejsu API, który publikuje uprawnienia.

#### <a name="to-read-the-current-permission-classifications-for-an-api"></a>Aby odczytać bieżące klasyfikacje uprawnień dla interfejsu API:

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

#### <a name="to-classify-a-permission-as-low-impact"></a>Aby sklasyfikować uprawnienia jako "niski wpływ":

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

#### <a name="to-remove-a-delegated-permission-classification"></a>Aby usunąć delegowaną klasyfikację uprawnień:

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

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Konfigurowanie zgody właściciela grupy na aplikacje uzyskujące dostęp do danych grupy

Właściciele grupy mogą autoryzować aplikacje, takie jak aplikacje opublikowane przez dostawców innych firm, w celu uzyskania dostępu do danych organizacji skojarzonych z grupą. Na przykład właściciel zespołu w usłudze Microsoft Teams może zezwalać aplikacji na odczytywanie wszystkich zespołów komunikatów w zespole lub wyświetlanie listy profilu podstawowego członków grupy.

Można skonfigurować, którzy użytkownicy mogą wyrazić zgodę na aplikacje uzyskujące dostęp do swoich grup lub można wyłączyć tę funkcję.

### <a name="configure-group-owner-consent-using-the-azure-portal"></a>Konfigurowanie zgody właściciela grupy przy użyciu Azure Portal

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako [administrator globalny](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
2. Wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**  >  **i uprawnienia do**  >  **ustawień zgody użytkownika**.
3. W obszarze **właściciel grupy wyrażanie zgody na aplikacje uzyskujące dostęp do danych** wybierz opcję, którą chcesz włączyć.
4. Wybierz pozycję **Zapisz** , aby zapisać ustawienia.

W tym przykładzie wszyscy właściciele grupy mogą wyrazić zgodę na aplikacje uzyskujące dostęp do danych ich grup:

:::image type="content" source="media/configure-user-consent/group-owner-consent.png" alt-text="Ustawienia zgody właściciela grupy":::

### <a name="configure-group-owner-consent-using-powershell"></a>Konfigurowanie zgody właściciela grupy przy użyciu programu PowerShell

Możesz użyć modułu Azure AD PowerShell w wersji zapoznawczej, [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview), aby włączyć lub wyłączyć możliwość wyrażania zgody na dostęp do danych organizacji przez właścicieli grup.

1. Upewnij się, że używasz modułu [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) . Ten krok jest ważny, jeśli zainstalowano moduł [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) i moduł [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) .

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

## <a name="configure-risk-based-step-up-consent"></a>Skonfiguruj zgodę na podwyższenie ryzyka

Wyrażanie ryzyka krok po kroku pomaga zmniejszyć narażenie użytkowników na złośliwe aplikacje, które podejmują [nielegalne żądania zgody](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants). Jeśli firma Microsoft wykryje ryzykowne żądanie zgody użytkownika końcowego, żądanie będzie wymagało zgody administratora na "krok po kroku". Ta funkcja jest domyślnie włączona, ale powoduje to zmianę zachowania tylko wtedy, gdy jest włączona zgoda użytkownika końcowego.

Po wykryciu ryzykownego żądania zgody w monicie zostanie wyświetlony komunikat informujący o konieczności zatwierdzenia przez administratora. Jeśli [przepływ pracy żądania zgody administratora](configure-admin-consent-workflow.md) jest włączony, użytkownik może wysłać żądanie do administratora w celu dalszej kontroli bezpośrednio z poziomu monitu o zgodę. Jeśli nie jest włączona, zostanie wyświetlony następujący komunikat:

* **AADSTS90094:** &lt; clientAppDisplayName &gt; musi mieć uprawnienia dostępu do zasobów w organizacji, które mogą przyznawać tylko Administratorzy. Poproś administratora o udzielenie uprawnienia do tej aplikacji, aby można było z niej korzystać.

W takim przypadku zdarzenie inspekcji zostanie również zarejestrowane z kategorią "ApplicationManagement", typem działania "zgody na aplikację" oraz przyczynie stanu "wykryto ryzykowną aplikację".

> [!IMPORTANT]
> Administratorzy powinni uważnie [oszacować wszystkie żądania zgody](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) przed zatwierdzeniem żądania, szczególnie w przypadku wykrycia ryzyka przez firmę Microsoft.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Wyłączenie lub ponowne włączenie zgody na ryzyko krok po kroku przy użyciu programu PowerShell

Możesz użyć modułu Azure AD PowerShell w wersji zapoznawczej, aby wyłączyć krok do zgody administratora wymagany w [przypadkach, w](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)których firma Microsoft wykryje ryzyko lub ponownie je włączać, jeśli wcześniej została wyłączona.

Można to zrobić przy użyciu tych samych kroków, jak pokazano powyżej, aby [skonfigurować zgodę właściciela grupy przy użyciu programu PowerShell](#configure-group-owner-consent-using-powershell), ale zastąpić inną wartość ustawienia. Istnieją trzy różnice w krokach: 

1. Zapoznaj się z wartościami ustawień dla pozwolenia krok po kroku:

    | Ustawienie       | Typ         | Opis  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Wartość logiczna |  Flaga oznaczająca, czy zgoda użytkownika zostanie zablokowana w przypadku wykrycia ryzykownego żądania. |

1. Zastąp następującą wartość w kroku 3:

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
    
1. Zastąp jedną z następujących czynności w kroku 5:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>Następne kroki

Dodatkowe informacje:

* [Konfigurowanie przepływu pracy zgody administratora](configure-admin-consent-workflow.md)
* [Dowiedz się, jak zarządzać zgodą na aplikacje i oszacować wnioski o zgodę](manage-consent-requests.md)
* [Udzielanie zgody administratora całej dzierżawy dla aplikacji](grant-admin-consent.md)
* [Uprawnienia i zgoda na platformie tożsamości firmy Microsoft](../develop/active-directory-v2-scopes.md)

Aby uzyskać pomoc lub znaleźć odpowiedzi na pytania:
* [Usługa Azure AD w systemie StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
