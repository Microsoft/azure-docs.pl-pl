---
title: Ustawianie okresów istnienia tokenów
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak ustawić okresy istnienia tokenów wystawionych przez platformę tożsamości firmy Microsoft. Dowiedz się, jak zarządzać domyślnymi zasadami organizacji, tworzyć zasady logowania w Internecie, tworzyć zasady dla aplikacji natywnej, która wywołuje internetowy interfejs API i zarządzać zaawansowanymi zasadami.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 04/08/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 66e9817c6d3bbcd199418b9afd78eda016c5f291
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363890"
---
# <a name="configure-token-lifetime-policies-preview"></a>Konfigurowanie zasad okresu istnienia tokenu (wersja zapoznawcza)
Można określić okres istnienia tokenu dostępu, saml lub identyfikatora wystawionego przez platformę tożsamości firmy Microsoft. Okresy istnienia tokenów można ustawić dla wszystkich aplikacji w organizacji, dla aplikacji wielodostępnych (dla wielu organizacji) lub dla określonej jednostki usługi w organizacji. Aby uzyskać więcej informacji, przeczytaj [konfigurowalne okresy istnienia tokenu.](active-directory-configurable-token-lifetimes.md)

W tej sekcji ominiemy typowe scenariusze zasad, które mogą ułatwić nakładanie nowych reguł na okres istnienia tokenu. W tym przykładzie dowiesz się, jak utworzyć zasady, które wymagają od użytkowników uwierzytelniania częściej w aplikacji internetowej.

## <a name="get-started"></a>Rozpoczęcie pracy

Aby rozpocząć, pobierz najnowszą wersję zapoznawczą modułu [Azure AD PowerShell Module](https://www.powershellgallery.com/packages/AzureADPreview).

Następnie uruchom polecenie `Connect` , aby zalogować się do konta administratora usługi Azure AD. Uruchom to polecenie za każdym razem, gdy rozpoczynasz nową sesję.

```powershell
Connect-AzureAD -Confirm
```

## <a name="create-a-policy-for-web-sign-in"></a>Tworzenie zasad logowania do sieci Web

W tym przykładzie utworzysz zasady, które wymagają od użytkowników uwierzytelniania częściej w aplikacji internetowej. Te zasady ustawiają okres istnienia tokenów dostępu/identyfikatora na jednostkę usługi aplikacji internetowej.

1. Utwórz zasady okresu istnienia tokenu.

    Te zasady dla logowania w sieci Web ustawiają okres istnienia tokenu dostępu/identyfikatora na dwie godziny.

    Aby utworzyć zasady, uruchom polecenie cmdlet [New-AzureADPolicy:](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true)

    ```powershell
    $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
    ```

    Aby wyświetlić nowe zasady i uzyskać identyfikator **ObjectId** zasad, uruchom polecenie cmdlet [Get-AzureADPolicy:](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true)

    ```powershell
    Get-AzureADPolicy -Id $policy.Id
    ```

1. Przypisz zasady do jednostki usługi. Musisz również uzyskać identyfikator **ObjectId** jednostki usługi.

    Użyj polecenia cmdlet [Get-AzureADServicePrincipal,](/powershell/module/azuread/get-azureadserviceprincipal) aby wyświetlić wszystkie jednostki usługi lub pojedynczą jednostkę usługi w organizacji.

    ```powershell
    # Get ID of the service principal
    $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
    ```

    Jeśli masz jednostkę usługi, uruchom polecenie cmdlet [Add-AzureADServicePrincipalPolicy:](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true)

    ```powershell
    # Assign policy to a service principal
    Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
    ```

## <a name="view-existing-policies-in-a-tenant"></a>Wyświetlanie istniejących zasad w dzierżawie

Aby wyświetlić wszystkie zasady, które zostały utworzone w organizacji, uruchom polecenie cmdlet [Get-AzureADPolicy.](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true)  Wszystkie wyniki ze zdefiniowanymi wartościami właściwości, które różnią się od wartości domyślnych wymienionych powyżej, są w zakresie wycofania.

```powershell
Get-AzureADPolicy -All $true
```

Aby zobaczyć, które aplikacje i jednostki usługi są połączone z określonymi zasadami, uruchom następujące polecenie cmdlet [Get-AzureADPolicyAppliedObject,](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) zastępując **1a37dad8-5da7-4cc8-87c7-efbc0326cf20** dowolnym identyfikatorem zasad. Następnie możesz zdecydować, czy skonfigurować częstotliwość logowania przy użyciu dostępu warunkowego, czy pozostać przy domyślnych ustawieniach usługi Azure AD.

```powershell
Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
```

Jeśli dzierżawa ma zasady definiujące wartości niestandardowe dla właściwości konfiguracji odświeżania i tokenu sesji, firma Microsoft zaleca zaktualizowanie tych zasad do wartości, które odzwierciedlają wartości domyślne opisane powyżej. Jeśli nie zostaną wprowadzone żadne zmiany, usługa Azure AD automatycznie będzie honorować wartości domyślne.

### <a name="troubleshooting"></a>Rozwiązywanie problemów
Niektórzy użytkownicy zgłaszali `Get-AzureADPolicy : The term 'Get-AzureADPolicy' is not recognized` błąd po uruchomieniu `Get-AzureADPolicy` polecenia cmdlet . Aby obejść ten problem, uruchom następujące czynności, aby odinstalować/ponownie zainstalować moduł AzureAD, a następnie zainstalować moduł AzureADPreview:

```powershell
# Uninstall the AzureAD Module
UnInstall-Module AzureAD

# Re-install the AzureAD Module
Install-Module AzureAD

# Install the AzureAD Preview Module adding the -AllowClobber
Install-Module AzureADPreview -AllowClobber

Connect-AzureAD
Get-AzureADPolicy -All $true
```

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [możliwościach zarządzania sesjami uwierzytelniania w](../conditional-access/howto-conditional-access-session-lifetime.md) dostępie warunkowym usługi Azure AD.
