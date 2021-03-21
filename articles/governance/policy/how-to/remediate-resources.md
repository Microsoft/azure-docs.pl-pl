---
title: Korygowanie niezgodnych zasobów
description: Ten przewodnik przeprowadzi Cię przez korygowanie zasobów, które są niezgodne z zasadami w Azure Policy.
ms.date: 02/17/2021
ms.topic: how-to
ms.openlocfilehash: e567bedf48393a36215c1ac3f3d11f467ae7badd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101742232"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Koryguj niezgodne zasoby za pomocą Azure Policy

Zasoby, które są niezgodne z zasadami **deployIfNotExistsymi** lub **modyfikacjami** , mogą zostać umieszczone w stanie zgodnym przez **skorygowanie**. Korygowanie jest realizowane przez podzielenie Azure Policy do uruchamiania efektu **deployIfNotExists** lub **Modyfikowanie operacji** przypisanych zasad w istniejących zasobach i subskrypcjach, niezależnie od tego, czy przypisanie jest grupą zarządzania, subskrypcją, grupą zasobów czy pojedynczym zasobem. W tym artykule przedstawiono kroki niezbędne do zrozumienia i wykonania korygowania Azure Policy.

## <a name="how-remediation-security-works"></a>Jak działa zabezpieczenia korygujące

Gdy Azure Policy uruchamia szablon w definicji zasad **deployIfNotExists** , robi to przy użyciu [tożsamości zarządzanej](../../../active-directory/managed-identities-azure-resources/overview.md).
Azure Policy tworzy tożsamość zarządzaną dla każdego przydziału, ale musi mieć szczegółowe informacje o rolach, które mają udzielić zarządzanej tożsamości. Jeśli zarządzana tożsamość nie ma ról, podczas przypisywania zasad lub inicjatywy jest wyświetlany błąd. W przypadku korzystania z portalu Azure Policy automatycznie przyznaje zarządzane tożsamości po rozpoczęciu przypisywania. W przypadku korzystania z zestawu SDK role muszą zostać ręcznie przyznane zarządzanej tożsamości. _Lokalizacja_ zarządzanej tożsamości nie ma wpływu na jej działanie z Azure Policy.

:::image type="content" source="../media/remediate-resources/missing-role.png" alt-text="Zrzut ekranu zasad deployIfNotExists, w którym brakuje zdefiniowanego uprawnienia do zarządzanej tożsamości." border="false":::

> [!IMPORTANT]
> W następujących scenariuszach zarządzana tożsamość przypisywania musi mieć [ręcznie przyznane uprawnienia](#manually-configure-the-managed-identity) lub wdrożenie korygowania zakończy się niepowodzeniem:
>
> - Jeśli przypisanie jest tworzone za pomocą zestawu SDK
> - Jeśli zasób zmodyfikowany przez **deployIfNotExists** lub **Modyfikuj** jest poza zakresem przypisania zasad
> - Jeśli szablon uzyskuje dostęp do właściwości zasobów poza zakresem przypisania zasad

## <a name="configure-policy-definition"></a>Konfigurowanie definicji zasad

Pierwszym krokiem jest zdefiniowanie ról, które **deployIfNotExists** i **modyfikują** potrzeby w definicji zasad, aby pomyślnie wdrożyć zawartość dołączonego szablonu. W obszarze właściwości **szczegóły** Dodaj właściwość **roleDefinitionIds** . Ta właściwość jest tablicą ciągów, które pasują do ról w danym środowisku. Aby zapoznać się z pełnymi przykładami, zobacz [przykład deployIfNotExists](../concepts/effects.md#deployifnotexists-example) lub [Modify przykłady](../concepts/effects.md#modify-examples).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

Właściwość **roleDefinitionIds** używa pełnego identyfikatora zasobu i nie przyjmuje krótkie **rolename** roli. Aby uzyskać identyfikator roli "Współautor" w Twoim środowisku, użyj następującego kodu:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Ręcznie skonfiguruj zarządzaną tożsamość

Podczas tworzenia przypisania przy użyciu portalu, Azure Policy zarówno generuje tożsamość zarządzaną, jak i przyznaje jej role zdefiniowane w **roleDefinitionIds**. W poniższych warunkach należy ręcznie wykonać kroki umożliwiające utworzenie tożsamości zarządzanej i przypisanie jej uprawnień:

- Podczas korzystania z zestawu SDK (takiego jak Azure PowerShell)
- Gdy zasób poza zakresem przypisania jest modyfikowany przez szablon
- Gdy zasób poza zakresem przypisania jest odczytywany przez szablon

### <a name="create-managed-identity-with-powershell"></a>Tworzenie tożsamości zarządzanej przy użyciu programu PowerShell

Aby utworzyć tożsamość zarządzaną podczas przypisywania zasad, należy zdefiniować **lokalizację** i **AssignIdentity** . Poniższy przykład pobiera definicję wbudowanego zasad **Wdróż nieprzezroczyste szyfrowanie danych SQL DB**, ustawia docelową grupę zasobów, a następnie tworzy przypisanie.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

`$assignment`Zmienna zawiera teraz Identyfikator podmiotu zabezpieczeń zarządzanej oraz standardowe wartości zwracane podczas tworzenia przypisania zasad. Dostęp do niego można uzyskać za poorednictwem `$assignment.Identity.PrincipalId` .

### <a name="grant-defined-roles-with-powershell"></a>Przyznawanie zdefiniowanych ról przy użyciu programu PowerShell

Nowa tożsamość zarządzana musi ukończyć replikację za pomocą Azure Active Directory, zanim będzie można przyznać wymagane role. Po zakończeniu replikacji Poniższy przykład iteruje definicję zasad w programie `$policyDef` dla **roleDefinitionIds** i używa funkcji [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) do udzielenia nowej tożsamości zarządzanej role.

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>Przyznawanie zdefiniowanych ról za poorednictwem portalu

Istnieją dwa sposoby udzielenia tożsamości zarządzanej przez przypisanie zdefiniowane role przy użyciu portalu przy użyciu **kontroli dostępu (IAM)** lub przez edytowanie przypisania zasad lub inicjatywy i wybranie opcji **Zapisz**.

Aby dodać rolę do zarządzanej tożsamości przypisania, wykonaj następujące kroki:

1. Uruchom usługę Azure Policy w Azure Portal, wybierając pozycję **wszystkie usługi**, a następnie wyszukując i wybierając pozycję **zasady**.

1. Wybierz pozycję **Przypisania** w lewej części strony usługi Azure Policy.

1. Znajdź przypisanie, które ma zarządzaną tożsamość i wybierz nazwę.

1. Znajdź właściwość **ID przypisania** na stronie Edycja. Identyfikator przypisania będzie wyglądać następująco:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   Nazwa tożsamości zarządzanej jest ostatnią częścią identyfikatora zasobu przypisania, która jest `2802056bfc094dfb95d4d7a5` w tym przykładzie. Skopiuj tę część identyfikatora zasobu przypisania.

1. Przejdź do zasobu lub kontenera nadrzędnego zasobów (grupy zasobów, subskrypcji, grupy zarządzania), które wymagają ręcznego dodania definicji roli.

1. Wybierz łącze **Kontrola dostępu (IAM)** na stronie zasoby, a następnie wybierz pozycję **+ Dodaj przypisanie roli** w górnej części strony kontroli dostępu.

1. Wybierz odpowiednią rolę zgodną z **roleDefinitionIdsą** z definicji zasad.
   Pozostaw pozycję **Przypisz dostęp, aby** ustawić domyślną wartość "użytkownik, Grupa lub aplikacja usługi Azure AD". W polu **wyboru** Wklej lub wpisz część identyfikatora zasobu przypisania zlokalizowanego wcześniej. Po zakończeniu wyszukiwania zaznacz obiekt o tej samej nazwie, aby wybrać pozycję Identyfikator, a następnie wybierz pozycję **Zapisz**.

## <a name="create-a-remediation-task"></a>Utwórz zadanie korygowania

### <a name="create-a-remediation-task-through-portal"></a>Tworzenie zadania korygującego za pomocą portalu

Podczas obliczania przypisanie zasad z **deployIfNotExists** lub **modyfikowaniem** efektów określa, czy istnieją niezgodne zasoby lub subskrypcje. Gdy zostaną znalezione niezgodne zasoby lub subskrypcje, szczegóły są dostępne na stronie **korygowanie** . Wraz z listą zasad, które mają niezgodne zasoby lub subskrypcje, jest opcja wyzwalająca **zadanie korygowania**.
Ta opcja polega na utworzeniu wdrożenia z szablonu **deployIfNotExists** lub operacji **modyfikowania** .

Aby utworzyć **zadanie korygowania**, wykonaj następujące kroki:

1. Uruchom usługę Azure Policy w Azure Portal, wybierając pozycję **wszystkie usługi**, a następnie wyszukując i wybierając pozycję **zasady**.

   :::image type="content" source="../media/remediate-resources/search-policy.png" alt-text="Zrzut ekranu przedstawiający wyszukiwanie zasad w wszystkich usługach." border="false":::

1. Wybierz pozycję **korygowanie** po lewej stronie Azure Policy.

   :::image type="content" source="../media/remediate-resources/select-remediation.png" alt-text="Zrzut ekranu przedstawiający węzeł korygowania na stronie zasady." border="false":::

1. Wszystkie przypisania zasad **deployIfNotExists** i **Modify** z niezgodnymi zasobami są zawarte w **zasadach do korygowania** kart i tabeli danych. Wybierz zasadę z zasobami, które są niezgodne. Zostanie otwarta strona **nowe zadanie korygowania** .

   > [!NOTE]
   > Alternatywnym sposobem otwarcia strony **zadania korygowania** jest znalezienie i wybranie zasad ze strony **zgodność** , a następnie wybranie przycisku **Utwórz zadanie korygujące** .

1. Na stronie **nowe zadanie korygowania** odfiltruj zasoby do skorygowania, używając wielokropka **zakresu** , aby wybrać zasoby podrzędne, z których są przypisane zasady (w tym w dół do poszczególnych obiektów zasobów). Ponadto należy użyć listy rozwijanej **lokalizacje** do dalszej odfiltrowania zasobów. Korygowane są tylko zasoby wymienione w tabeli.

   :::image type="content" source="../media/remediate-resources/select-resources.png" alt-text="Zrzut ekranu przedstawiający węzeł korygowanie oraz siatkę zasobów do skorygowania." border="false":::

1. Rozpocznij zadanie korygowania, gdy zasoby zostały odfiltrowane, wybierając pozycję **Koryguj**. Strona zgodność zasad zostanie otwarta na karcie **zadania korygowania** , aby wyświetlić stan zadań postępu. Wdrożenia utworzone przez zadanie korygowania zaczynają się od razu.

   :::image type="content" source="../media/remediate-resources/task-progress.png" alt-text="Zrzut ekranu przedstawiający kartę zadania korygowania i postęp istniejących zadań korygowania." border="false":::

1. Aby uzyskać szczegółowe informacje o postępie, wybierz **zadanie korygowania** na stronie zgodność zasad. Filtrowanie używane na potrzeby zadania jest wyświetlane wraz z listą zasobów, które są korygowane.

1. Na stronie **zadanie korygowania** kliknij prawym przyciskiem myszy zasób, aby wyświetlić jego wdrożenie lub zasób. Na końcu wiersza wybierz pozycję dla **powiązanych zdarzeń** , aby wyświetlić szczegóły, takie jak komunikat o błędzie.

   :::image type="content" source="../media/remediate-resources/resource-task-context-menu.png" alt-text="Zrzut ekranu przedstawiający menu kontekstowe dla zasobu na karcie korygowanie zadania." border="false":::

Zasoby wdrożone za pośrednictwem **zadania korygowania** są dodawane do karty **wdrożone zasoby** na stronie Zgodność z zasadami.

### <a name="create-a-remediation-task-through-azure-cli"></a>Tworzenie zadania korygującego za pomocą interfejsu wiersza polecenia platformy Azure

Aby utworzyć **zadanie korygowania** przy użyciu interfejsu wiersza polecenia platformy Azure, użyj `az policy remediation` poleceń. Zastąp ciąg `{subscriptionId}` identyfikatorem subskrypcji i `{myAssignmentId}` identyfikatorem przydziału **deployIfNotExists** lub **zmodyfikuj** zasady.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Aby poznać inne polecenia i przykłady korygowania, zobacz [AZ Policy korygowanie](/cli/azure/policy/remediation) Commands.

### <a name="create-a-remediation-task-through-azure-powershell"></a>Utwórz zadanie korygujące za pośrednictwem Azure PowerShell

Aby utworzyć **zadanie korygowania** przy użyciu Azure PowerShell, użyj `Start-AzPolicyRemediation` poleceń. Zastąp ciąg `{subscriptionId}` identyfikatorem subskrypcji i `{myAssignmentId}` identyfikatorem przydziału **deployIfNotExists** lub **zmodyfikuj** zasady.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Inne polecenia cmdlet służące do korygowania i przykłady można znaleźć w module [AZ. PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) .

### <a name="create-a-remediation-task-during-policy-assignment-in-the-azure-portal"></a>Utwórz zadanie korygowania podczas przypisywania zasad w Azure Portal

Ulepszony sposób tworzenia zadania korygowania polega na Azure Portal podczas przypisywania zasad. Jeśli definicja zasad, która ma zostać przypisana, to efekt **deployIfNotExists** lub **modyfikacji** , Kreator na karcie **korygowanie** oferuje opcję _tworzenia zadania korygującego_ . W przypadku wybrania tej opcji zadanie korygowania jest tworzone jednocześnie z przypisaniem zasad.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).
- Dowiedz się, jak [programowo utworzyć zasady](programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](get-compliance-data.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).
