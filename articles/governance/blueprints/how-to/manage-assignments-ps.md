---
title: Zarządzanie przypisaniami przy użyciu programu PowerShell
description: Dowiedz się, jak zarządzać przypisaniami planów przy użyciu oficjalnego modułu programu PowerShell platformy Azure, AZ. plan.
ms.date: 08/27/2020
ms.topic: how-to
ms.openlocfilehash: 3bcb3731bd1270497945fa86406d08b2f9750c85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89051410"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Zarządzanie przypisaniami przy użyciu programu PowerShell

Przypisanie planu można zarządzać przy użyciu modułu **AZ. plan** Azure PowerShell. Moduł obsługuje pobieranie, tworzenie, aktualizowanie i usuwanie przypisań. Moduł może również pobrać szczegóły istniejących definicji planu. W tym artykule opisano sposób instalowania modułu i rozpoczynania korzystania z niego.

## <a name="add-the-azblueprint-module"></a>Dodaj moduł AZ. plan

Aby umożliwić Azure PowerShell zarządzania przypisaniami planu, należy dodać moduł. Ten moduł może być używany z lokalnie zainstalowanym programem PowerShell, [programem Azure Cloud Shell](https://shell.azure.com) lub [obrazem Docker programu Azure PowerShell](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Wymagania podstawowe

Moduł plany platformy Azure wymaga następującego oprogramowania:

- Azure PowerShell 1.5.0 lub wyższy. Jeśli jeszcze go nie zainstalowano, postępuj zgodnie z [tymi instrukcjami](/powershell/azure/install-az-ps).
- Modułu PowerShellGet w wersji 2.0.1 lub nowszej. Jeśli jeszcze nie został on zainstalowany lub zaktualizowany, postępuj zgodnie z [tymi instrukcjami](/powershell/scripting/gallery/installing-psget).

### <a name="install-the-module"></a>Instalowanie modułu

Moduł planów platformy Azure dla programu PowerShell to **AZ. plan**.

1. Za pomocą monitu **administracyjnego** programu PowerShell uruchom następujące polecenie:

   ```azurepowershell-interactive
   # Install the Azure Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Jeśli **AZ. Accounts** jest już zainstalowana, może być konieczne użycie `-AllowClobber` programu w celu wymuszenia instalacji.

1. Sprawdź, czy moduł został zaimportowany i czy jest to poprawna wersja (0.2.6):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Pobierz definicje planów

Pierwszym krokiem do pracy z przypisaniem jest często pobranie odwołania do definicji planu.
`Get-AzBlueprint`Polecenie cmdlet pobiera co najmniej jedną definicję planu. Polecenie cmdlet może pobrać definicje planu z grupy zarządzania z `-ManagementGroupId {mgId}` lub z subskrypcją programu `-SubscriptionId {subId}` . Parametr **name** pobiera definicję planu, ale musi być używany z **ManagementGroupId** lub identyfikatorem **subskrypcji**. **Wersja** może być używana z **nazwą** , aby być bardziej jawna, na której jest zwracana definicja planu. Zamiast **wersji**, przełącznik korzysta z `-LatestPublished` ostatnio opublikowanej wersji.

Poniższy przykład używa, `Get-AzBlueprint` Aby pobrać wszystkie wersje definicji planu o nazwie "101-plany-definicja-subskrypcja" z określonej subskrypcji reprezentowanej jako `{subId}` :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

Przykładowe dane wyjściowe dla definicji planu z wieloma wersjami wyglądają następująco:

```output
Name                 : 101-blueprints-definition-subscription
Id                   : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101
                       -blueprints-definition-subscription
DefinitionLocationId : {subId}
Versions             : {1.0, 1.1}
TimeCreated          : 2019-02-25
TargetScope          : Subscription
Parameters           : {storageAccount_storageAccountType, storageAccount_location,
                       allowedlocations_listOfAllowedLocations, [Usergrouporapplicationname]:Reader_RoleAssignmentName}
ResourceGroups       : ResourceGroup
```

[Parametry](../concepts/parameters.md#blueprint-parameters) strategii w definicji strategii mogą być rozwinięte, aby uzyskać więcej informacji.

```azurepowershell-interactive
$blueprints.Parameters
```

```output
Key                                                    Value
---                                                    -----
storageAccount_storageAccountType                      Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
storageAccount_location                                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
allowedlocations_listOfAllowedLocations                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
[Usergrouporapplicationname]:Reader_RoleAssignmentName Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
```

## <a name="get-blueprint-assignments"></a>Pobieranie przypisań planów

Jeśli przypisanie planu już istnieje, można uzyskać odwołanie do niego przy użyciu `Get-AzBlueprintAssignment` polecenia cmdlet. Polecenie cmdlet przyjmuje identyfikator **subskrypcji** i **nazwę** jako parametry opcjonalne. Jeśli nie określono identyfikatora **subskrypcji** , używany jest bieżący kontekst subskrypcji.

Poniższy przykład używa `Get-AzBlueprintAssignment` do uzyskania jednego przypisania planu o nazwie "przypisanie-blokada-zasób-grupy" z określonej subskrypcji reprezentowanej jako `{subId}` :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

Przykładowe dane wyjściowe dla przypisania planu wyglądają następująco:

```output
Name              : Assignment-lock-resource-groups
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssignme
                    nts/Assignment-lock-resource-groups
Scope             : /subscriptions/{subId}
LastModified      : 2019-02-19
LockMode          : AllResourcesReadOnly
ProvisioningState : Succeeded
Parameters        :
ResourceGroups    : ResourceGroup
```

## <a name="create-blueprint-assignments"></a>Tworzenie przypisań strategii

Jeśli przypisanie planu nie istnieje jeszcze, można je utworzyć za pomocą `New-AzBlueprintAssignment` polecenia cmdlet. To polecenie cmdlet używa następujących parametrów:

- **Nazwa** [wymagane]
  - Określa nazwę przypisania strategii
  - Musi być unikatowa i jeszcze nie istnieć w identyfikatorze **subskrypcji**
- **Plan** [wymagany]
  - Określa definicję planu do przypisania
  - Użyj, `Get-AzBlueprint` Aby uzyskać obiekt odwołania
- **Lokalizacja** [wymagana]
  - Określa region dla tożsamości zarządzanej przypisanej do systemu i obiektu wdrożenia subskrypcji
- **Subskrypcja** (opcjonalnie)
  - Określa subskrypcję, do której jest wdrażane przypisanie
  - Jeśli nie zostanie podany, wartością domyślną jest bieżący kontekst subskrypcji
- **Blokada** (opcjonalnie)
  - Definiuje [blokadę zasobów](../concepts/resource-locking.md) planu do użycia dla wdrożonych zasobów
  - Obsługiwane opcje: _none_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Jeśli nie zostanie podany, wartość domyślna to _Brak_ .
- **SystemAssignedIdentity** (opcjonalnie)
  - Wybierz, aby utworzyć zarządzaną przez system tożsamość skojarzoną z przypisaniem i wdrożyć zasoby
  - Wartość domyślna dla zestawu parametrów "Identity"
  - Nie można używać z **UserAssignedIdentity**
- **UserAssignedIdentity** (opcjonalnie)
  - Określa tożsamość zarządzaną przypisaną przez użytkownika do użycia podczas przypisywania i wdrażania zasobów
  - Część zestawu parametrów "Identity"
  - Nie można używać z **SystemAssignedIdentity**
- **Parametr** (opcjonalnie)
  - [Tabela skrótów](/powershell/module/microsoft.powershell.core/about/about_hash_tables) par klucz/wartość służąca do ustawiania [parametrów dynamicznych](../concepts/parameters.md#dynamic-parameters) w przypisaniu planu
  - Wartością domyślną dla parametru dynamicznego jest wartość **DefaultValue** w definicji
  - Jeśli parametr nie jest podany i nie ma **atrybutu DefaultValue**, parametr nie jest opcjonalny

    > [!NOTE]
    > **Parametr** nie obsługuje SecureString.

- **ResourceGroupParameter** (opcjonalnie)
  - [Tablica skrótów](/powershell/module/microsoft.powershell.core/about/about_hash_tables) artefaktów grupy zasobów
  - Każdy symbol zastępczy artefaktu grupy zasobów ma pary klucz/wartość do dynamicznego ustawienia **nazwy** i **lokalizacji** w tym artefaktie grupy zasobów
  - Jeśli nie podano parametru grupy zasobów i nie ma on wartości **DefaultValue**, parametr grupy zasobów nie jest opcjonalny
- **AssignmentFile** (opcjonalnie)
  - Ścieżka do reprezentacji pliku JSON przypisania planu
  - Ten parametr jest częścią zestawu parametrów programu PowerShell, który zawiera tylko **nazwę**, **Plan**i identyfikator **subskrypcji**oraz wspólne parametry.

### <a name="example-1-provide-parameters"></a>Przykład 1: podaj parametry

Poniższy przykład tworzy nowe przypisanie wersji "1,1" w definicji planu "My-strategii" pobranej z `Get-AzBlueprint` , ustawia lokalizację zarządzaną tożsamości i obiektu przypisania na "westus2", blokuje zasoby z _AllResourcesReadOnly_i ustawia tabele skrótów dla **parametrów** i **ResourceGroupParameter** dla określonej subskrypcji reprezentowanej jako `{subId}` :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

Przykładowe dane wyjściowe tworzenia przypisania strategii wyglądają następująco:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

### <a name="example-2-use-a-json-assignment-definition-file"></a>Przykład 2: użycie pliku definicji przypisania JSON

Poniższy przykład tworzy niemal to samo przypisanie jak [przykład 1](#example-1-provide-parameters). Zamiast przekazywania parametrów do polecenia cmdlet, w przykładzie pokazano użycie pliku definicji przypisania JSON i parametru **AssignmentFile** . Ponadto Właściwość **excludedPrincipals** jest konfigurowana jako część **blokad**. Nie istnieje parametr programu PowerShell dla usługi **excludedPrincipals** , a właściwość można skonfigurować tylko przez ustawienie jej przy użyciu pliku definicji przypisania JSON.

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "westus2",
  "properties": {
    "description": "Assignment of the 101-blueprint-definition-subscription",
    "blueprintId": "/subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101-blueprints-definition-subscription",
    "locks": {
      "mode": "AllResourcesReadOnly",
      "excludedPrincipals": [
          "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
          "38833b56-194d-420b-90ce-cff578296714"
      ]
    },
    "parameters": {
      "storageAccount_storageAccountType": {
        "value": "Standard_GRS"
      }
    },
    "resourceGroups": {
      "ResourceGroup": {
        "name": "storage_rg",
        "location": "westus2"
      }
    }
  }
}
```

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -SubscriptionId '{subId}' `
    -AssignmentFile '.\assignment.json'
```

Przykład pliku definicji przypisania JSON dla tożsamości zarządzanej przypisanej przez użytkownika znajduje się w treści żądania na [przykład: przypisanie z tożsamością zarządzaną przez użytkownika](/rest/api/blueprints/assignments/createorupdate#examples) dla interfejsu API REST.

## <a name="update-blueprint-assignments"></a>Aktualizowanie przypisań planów

Czasami trzeba zaktualizować przypisanie planu, które zostało już utworzone. `Set-AzBlueprintAssignment`Polecenie cmdlet obsługuje tę akcję. Polecenie cmdlet pobiera większość z tych samych parametrów, które są używane przez `New-AzBlueprintAssignment` polecenie cmdlet, co umożliwia zaktualizowanie wszystkich elementów ustawionych w przypisaniu. Wyjątkami są _nazwy_, _plany_i identyfikator _subskrypcji_. Tylko podane wartości są aktualizowane.

Aby zrozumieć, co się dzieje w przypadku aktualizowania przypisania planu, zobacz [reguły dotyczące aktualizowania przypisań](./update-existing-assignments.md#rules-for-updating-assignments).

- **Nazwa** [wymagane]
  - Określa nazwę przypisania strategii do zaktualizowania
  - Służy do lokalizowania przypisania do aktualizacji, a nie do zmiany przypisania
- **Plan** [wymagany]
  - Określa definicję planu przypisania planu
  - Użyj, `Get-AzBlueprint` Aby uzyskać obiekt odwołania
  - Służy do lokalizowania przypisania do aktualizacji, a nie do zmiany przypisania
- **Lokalizacja** (opcjonalnie)
  - Określa region dla tożsamości zarządzanej przypisanej do systemu i obiektu wdrożenia subskrypcji
- **Subskrypcja** (opcjonalnie)
  - Określa subskrypcję, do której jest wdrażane przypisanie
  - Jeśli nie zostanie podany, wartością domyślną jest bieżący kontekst subskrypcji
  - Służy do lokalizowania przypisania do aktualizacji, a nie do zmiany przypisania
- **Blokada** (opcjonalnie)
  - Definiuje [blokadę zasobów](../concepts/resource-locking.md) planu do użycia dla wdrożonych zasobów
  - Obsługiwane opcje: _none_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** (opcjonalnie)
  - Wybierz, aby utworzyć zarządzaną przez system tożsamość skojarzoną z przypisaniem i wdrożyć zasoby
  - Wartość domyślna dla zestawu parametrów "Identity"
  - Nie można używać z **UserAssignedIdentity**
- **UserAssignedIdentity** (opcjonalnie)
  - Określa tożsamość zarządzaną przypisaną przez użytkownika do użycia podczas przypisywania i wdrażania zasobów
  - Część zestawu parametrów "Identity"
  - Nie można używać z **SystemAssignedIdentity**
- **Parametr** (opcjonalnie)
  - [Tabela skrótów](/powershell/module/microsoft.powershell.core/about/about_hash_tables) par klucz/wartość służąca do ustawiania [parametrów dynamicznych](../concepts/parameters.md#dynamic-parameters) w przypisaniu planu
  - Wartością domyślną dla parametru dynamicznego jest wartość **DefaultValue** w definicji
  - Jeśli parametr nie jest podany i nie ma **atrybutu DefaultValue**, parametr nie jest opcjonalny

    > [!NOTE]
    > **Parametr** nie obsługuje SecureString.

- **ResourceGroupParameter** (opcjonalnie)
  - [Tablica skrótów](/powershell/module/microsoft.powershell.core/about/about_hash_tables) artefaktów grupy zasobów
  - Każdy symbol zastępczy artefaktu grupy zasobów ma pary klucz/wartość do dynamicznego ustawienia **nazwy** i **lokalizacji** w tym artefaktie grupy zasobów
  - Jeśli nie podano parametru grupy zasobów i nie ma on wartości **DefaultValue**, parametr grupy zasobów nie jest opcjonalny

Poniższy przykład aktualizuje przypisanie wersji "1,1" definicji planu "My-strategii" pobranej `Get-AzBlueprint` przez zmianę trybu blokowania:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

Przykładowe dane wyjściowe tworzenia przypisania strategii wyglądają następująco:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesDoNotDelete
ProvisioningState : Updating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="remove-blueprint-assignments"></a>Usuwanie przypisań strategii

Gdy jest czas, aby można było usunąć przypisanie do planu, `Remove-AzBlueprintAssignment` polecenie cmdlet obsługuje tę akcję. Polecenie cmdlet przyjmuje **nazwę** lub parametr **inputobject** , aby określić, które przypisanie planu ma zostać usunięte. Identyfikator **subskrypcji** jest _wymagany_ i musi być podany we wszystkich przypadkach.

Poniższy przykład pobiera istniejące przypisanie strategii z `Get-AzBlueprintAssignment` , a następnie usuwa je z określonej subskrypcji reprezentowanej jako `{subId}` :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="code-example"></a>Przykładowy kod

Wykonanie wszystkich kroków razem, w poniższym przykładzie pobiera definicję planu, tworzy, aktualizuje i usuwa przypisanie strategii w ramach określonej subskrypcji reprezentowanej jako `{subId}` :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

#region GetBlueprint
# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'
#endregion

#region CreateAssignment
# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
#endregion CreateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region UpdateAssignment
# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
#endregion UpdateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region RemoveAssignment
# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $bpAssignment -SubscriptionId '{subId}'
#endregion
```

## <a name="next-steps"></a>Następne kroki

- Uzyskaj informacje na temat [cyklu życia strategii](../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../concepts/resource-locking.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).