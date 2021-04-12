---
title: Eksportowanie zasobów usługi Azure Policy
description: Dowiedz się, jak eksportować Azure Policy zasobów do usługi GitHub, takich jak definicje zasad i przypisania zasad.
ms.date: 03/31/2021
ms.topic: how-to
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8fd2a24d2c01b4214eda88054b45a409cd865a32
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096550"
---
# <a name="export-azure-policy-resources"></a>Eksportowanie zasobów usługi Azure Policy

Ten artykuł zawiera informacje na temat eksportowania istniejących zasobów Azure Policy. Eksportowanie zasobów jest przydatne i zalecane na potrzeby tworzenia kopii zapasowych, ale jest to również ważny krok w podróży dzięki zasadom zarządzania chmurą i traktowaniu [zasady jako kodu](../concepts/policy-as-code.md). Zasoby Azure Policy można eksportować za pomocą [Azure Portal](#export-with-azure-portal), [interfejsu wiersza polecenia platformy Azure](#export-with-azure-cli), [Azure PowerShell](#export-with-azure-powershell)i każdego z obsługiwanych zestawów SDK.

## <a name="export-with-azure-portal"></a>Eksportuj z Azure Portal

Aby wyeksportować definicję zasad z Azure Portal, wykonaj następujące kroki:

1. Uruchom usługę Azure Policy w witrynie Azure Portal, klikając opcję **Wszystkie usługi** i następnie wyszukując i wybierając opcję **Zasada**.

1. Wybierz pozycję **definicje** po lewej stronie Azure Policy.

1. Użyj przycisku **Eksportuj definicje** lub wybierz wielokropek w wierszu definicji zasad, a następnie wybierz pozycję **Eksportuj definicję**.

1. Wybierz przycisk **Zaloguj się przy użyciu usługi GitHub** . Jeśli nie masz jeszcze uwierzytelnienia w usłudze GitHub w celu autoryzowania Azure Policy eksportowania zasobu, sprawdź, czy w nowym otwartym oknie są dostępne wymagania dotyczące [akcji GitHub](https://github.com/features/actions) , a następnie wybierz pozycję **Autoryzuj AzureGitHubActions** , aby kontynuować proces eksportowania. Po zakończeniu nowe okno zostanie automatycznie zamknięte.

1. Na karcie **podstawy** ustaw poniższe opcje, a następnie wybierz kartę **zasady** lub przycisk **Dalej: zasady** w dolnej części strony.

   - **Filtr repozytorium**: Ustaw _Moje repozytoria_ , aby wyświetlić tylko własne repozytoria lub _wszystkie_ repozytoria, aby zobaczyć, że masz dostęp do akcji usługi GitHub.
   - **Repozytorium**: Ustaw repozytorium, do którego chcesz wyeksportować zasoby Azure Policy.
   - **Gałąź**: Ustaw gałąź w repozytorium. Użycie gałęzi innej niż domyślna to dobry sposób na zweryfikowanie aktualizacji przed ich scaleniem w kodzie źródłowym.
   - **Katalog**: _folder poziomu głównego_ , do którego mają zostać wyeksportowane zasoby Azure Policy. Podfoldery w tym katalogu są tworzone na podstawie tego, jakie zasoby są eksportowane.

1. Na karcie **zasady** Ustaw zakres wyszukiwania, wybierając wielokropek i wybierz kombinację grup zarządzania, subskrypcji lub grup zasobów.
   
1. Użyj przycisku **Dodaj definicje zasad** , aby przeszukać zakres obiektów do wyeksportowania. W otwartym oknie Wybierz każdy obiekt do wyeksportowania. Filtrowanie zaznaczenia według pola wyszukiwania lub typu. Po wybraniu wszystkich obiektów do wyeksportowania Użyj przycisku **Dodaj** w dolnej części strony.

1. Dla każdego zaznaczonego obiektu wybierz odpowiednie opcje eksportu, takie jak _tylko definicje_ lub _Definicja i przypisania_ dla definicji zasad. Następnie wybierz kartę **Recenzja + eksport** lub przycisk **Dalej: recenzja + eksport** w dolnej części strony.

   > [!NOTE]
   > Jeśli wybrano opcję _Definicja opcji i przypisania_ , eksportowane są tylko przypisania zasad w zakresie ustawionym przez filtr po dodaniu definicji zasad.

1. Na karcie **Recenzja i eksportowanie** Sprawdź zgodność szczegółów, a następnie użyj przycisku **Eksportuj** w dolnej części strony.

1. Sprawdź repozytorium GitHub, gałąź i _folder poziomu głównego_ , aby zobaczyć, że wybrane zasoby zostały teraz wyeksportowane do kontroli źródła.

Zasoby Azure Policy są eksportowane do następującej struktury w wybranym repozytorium GitHub i _folderze poziomu głównego_:

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

## <a name="export-with-azure-cli"></a>Eksportowanie za pomocą interfejsu wiersza polecenia platformy Azure

Definicje Azure Policy, inicjatywy i przypisania można wyeksportować jako dane JSON przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Każdy z tych poleceń używa parametru **name** , aby określić obiekt, dla którego ma zostać pobrany kod JSON. Właściwość **name** jest często _identyfikatorem GUID_ i nie jest **nazwą DisplayName** obiektu.

- Definicja- [AZ Policy Definition show](/cli/azure/policy/definition#az_policy_definition_show)
- Initiative- [AZ Policy Set-definicja show](/cli/azure/policy/set-definition#az_policy_set_definition_show)
- Przypisanie — [AZ Policy przypisanie show](/cli/azure/policy/assignment#az_policy_assignment_show)

Oto przykład pobierania pliku JSON dla definicji zasad o **nazwie** _VirtualMachineStorage_:

```azurecli-interactive
az policy definition show --name 'VirtualMachineStorage'
```

## <a name="export-with-azure-powershell"></a>Eksportuj z Azure PowerShell

Definicje Azure Policy, inicjatywy i przypisania można wyeksportować w formacie JSON z [Azure PowerShell](/powershell/azure/). Każdy z tych poleceń cmdlet używa parametru **name** , aby określić obiekt, dla którego ma zostać pobrany kod JSON. Właściwość **name** jest często _identyfikatorem GUID_ i nie jest **nazwą DisplayName** obiektu.

- Definicja- [Get-AzPolicyDefinition](/powershell/module/az.resources/get-azpolicydefinition)
- Inicjatywa [Get-AzPolicySetDefinition](/powershell/module/az.resources/get-azpolicysetdefinition)
- Przypisanie- [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment)

Oto przykład pobierania pliku JSON dla definicji zasad o **nazwie** _VirtualMachineStorage_:

```azurepowershell-interactive
Get-AzPolicyDefinition -Name 'VirtualMachineStorage' | ConvertTo-Json -Depth 10
```

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).
- Dowiedz się, jak [programowo utworzyć zasady](programmatically-create.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).
