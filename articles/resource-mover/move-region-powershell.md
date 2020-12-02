---
title: Przenoszenie zasobów między regionami przy użyciu programu PowerShell w środowisku przenoszenia zasobów platformy Azure
description: Dowiedz się, jak przenosić zasoby między regionami przy użyciu programu PowerShell w środowisku przenoszenia zasobów platformy Azure.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 11/30/2020
ms.author: raynew
ms.openlocfilehash: 0aca0e49d72025686cf44d434fa7a43ae0c86e0b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461089"
---
# <a name="move-resources-across-regions-in-powershell"></a>Przenoszenie zasobów między regionami w programie PowerShell

Dowiedz się, jak przenieść zasoby platformy Azure do innego regionu przy użyciu programu PowerShell w usłudze Azure Resource Recovery. 

> [!NOTE]
> Usługa przenoszenia zasobów platformy Azure jest obecnie dostępna w wersji zapoznawczej.



## <a name="before-you-start"></a>Przed rozpoczęciem

- Twoja subskrypcja platformy Azure [powinna mieć dostęp](../role-based-access-control/built-in-roles.md#owner) do przenoszenia zasobów i należy mieć uprawnienia administratora lub [dostępu użytkownika](../role-based-access-control/built-in-roles.md#user-access-administrator) do subskrypcji.
- Program przenoszenia zasobów nie śledzi zmian i uaktualnień, dlatego przed rozpoczęciem przenoszenia należy wprowadzić wszelkie wymagane zmiany zasobów.
- W przypadku przenoszenia zasobów przy użyciu programu PowerShell nie można obecnie edytować żadnych ustawień regionu docelowego. Zmodyfikuj te ustawienia bezpośrednio w portalu.
- Po dodaniu zasobów do kolekcji przenoszenia w przygotowaniu do przenoszenia ich do innego regionu metadane dotyczące przenoszenia są przechowywane w grupie zasobów utworzonej dla tego celu. Obecnie ta grupa zasobów może znajdować się w regionach Wschodnie stany USA 2 lub Europa Północna. Zasoby platformy Azure można przenosić między wszystkimi regionami publicznymi przy użyciu metadanych znajdujących się w jednym z tych regionów.

## <a name="sample-values"></a>Przykładowe wartości

Używamy tych wartości w naszych przykładach skryptu:

**Ustawienie** | **Wartość** 
--- | ---
Identyfikator subskrypcji | Identyfikator subskrypcji
Lokalizacja usługi przenoszenia zasobów | Wschodnie stany USA 2
Grupa zasobów metadanych | RegionMoveRG-środkowe-westcentralus
Lokalizacja grupy zasobów metadanych | Wschodnie stany USA 2
Nazwa przeniesienia kolekcji | Movecollection-środkowe-westcentralus
Region źródłowy |  centralus
Źródłowa Grupa zasobów | PSDemoRM
Region docelowy | Zachodnio-środkowe stany USA
Docelowa Grupa zasobów | PSDemoRMtgt
Maszyna wirtualna do przeniesienia | PSDemoVM

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure.

```azurepowershell-interactive
# Sign in to an Azure subscription
Connect-AzAccount – Subscription "<subscription-id>"
```

## <a name="create-the-resource-group-for-metadata"></a>Utwórz grupę zasobów dla metadanych

Utwórz grupę zasobów, aby przechowywać metadane i informacje o konfiguracji przenoszenia kolekcji.

```azurepowershell-interactive
# Create the resource group for metadata
New-AzResourceGroup -Name MoveCollection-centralus-westcentralus -Location "East US 2"
```

**Oczekiwane dane wyjściowe**:

![Tekst wyjściowy po utworzeniu grupy zasobów](./media/move-region-powershell/output-create-resource-group.png)

## <a name="register-the-resource-provider"></a>Rejestrowanie dostawcy zasobów

Zarejestruj dostawcę zasobów Microsoft. Migruj, aby można było utworzyć zasób Movecollection.

```azurepowershell-interactive
# Register the provider
Register-AzResourceProvider -ProviderNamespace Microsoft.Migrate 

# Wait for registration
While(((Get-AzResourceProvider -ProviderNamespace Microsoft.Migrate)| where {$_.RegistrationState -eq "Registered" -and $_.ResourceTypes.ResourceTypeName -eq "moveCollections"}|measure).Count -eq 0)
  {
      Start-Sleep -Seconds 5
      Write-Output "Waiting for registration to complete."
  }
```

## <a name="create-the-move-collection"></a>Tworzenie kolekcji przenoszenia

Obiekt Movecollection przechowuje metadane i informacje o konfiguracji dotyczące zasobów, które chcesz przenieść.

- Aby obiekt Movecollection mógł uzyskać dostęp do subskrypcji, w której znajduje się usługa Azure Resource przenosząca, musi mieć [tożsamość zarządzaną przypisaną przez system](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (wcześniej znaną jako tożsamość usługi ZARZĄDZANEJ (msi)), która jest zaufana przez subskrypcję.
- Do tożsamości jest przypisany współautor lub rola administratora dostępu użytkowników dla subskrypcji źródłowej.
- Aby przypisać rolę do tożsamości, musisz mieć rolę w subskrypcji (takiej jak właściciel lub administrator dostępu użytkowników) z następującymi uprawnieniami:
    -  Microsoft.Authorization/roleAssignments/write
    - Microsoft. Authorization/roleAssignments/Delete


```azurepowershell-interactive
# Create a MoveCollection object
New-AzResourceMoverMoveCollection -Name "MoveCollection-centralus-westcentralus" -ResourceGroupName "RegionMoveRG-centralus-westcentralus" -SubscriptionId "<subscription-id>" -SourceRegion "centralus" -TargetRegion "westcentralus" -Location  "East US 2" -IdentityType SystemAssigned
```
**Oczekiwane dane wyjściowe**:

![Tekst wyjściowy po utworzeniu kolekcji przenoszenia](./media/move-region-powershell/output-move-collection.png)


## <a name="assign-a-managed-identity"></a>Przypisywanie tożsamości zarządzanej 

Ustaw identyfikator subskrypcji, Pobierz podmiot zabezpieczeń tożsamości obiektu Movecollection i przypisz do niego role platformy Azure.


```azurepowershell-interactive
# Set the subscriptionId
$subscriptionId = "<subscription-id>"

# Retrieve the principal managed identity of the MoveCollection
$moveCollection = Get-AzResourceMoverMoveCollection -SubscriptionId $subscriptionId -ResourceGroupName "RegionMoveRG-centralus-westcentralus" -Name "MoveCollection-centralus-westcentralus"

# Set the IdentityPrincipalID
$identityPrincipalId = $moveCollection.IdentityPrincipalId

# Assign the role to the IdentityPrincipalID
New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName Contributor -Scope "/subscriptions/$subscriptionId"

New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName "User Access Administrator" -Scope "/subscriptions/$subscriptionId"
```

## <a name="add-resources-to-the-move-collection"></a>Dodawanie zasobów do kolekcji przenoszenia

Pobierz identyfikator zasobu źródłowego, który chcesz przenieść. Następnie dodaj go do kolekcji Move.

```azurepowershell-interactive
# Retrieve the resource ID
Get-AzResource -Name PSDemoVM -ResourceGroupName PSDemoRM
```

**Oczekiwane dane wyjściowe**

![Tekst wyjściowy po pobraniu identyfikatora zasobu](./media/move-region-powershell/output-retrieve-resource.png)


```azurepowershell-interactive
# Add the resource to the move collection
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -SourceId “/subscriptions/e80eb9fa-c996-4435-aa32-5af6f3d3077c/resourceGroups/PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM” -Name “PSDemoVM” -ResourceSettingResourceType “Microsoft.Compute/virtualMachines” -ResourceSettingTargetResourceName “PSDemoVM”
```

**Oczekiwane dane wyjściowe** 
 ![ Tekst wyjściowy po dodaniu zasobu](./media/move-region-powershell/output-add-resource.png)

## <a name="resolve-dependencies"></a>Rozwiązywanie zależności

Sprawdź poprawność dodanych zasobów i rozwiąż wszelkie zależności od innych zasobów.

```azurepowershell-interactive
# Resolve dependencies
Resolve-AzResourceMoverMoveCollectionDependency -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus”
```
**Dane wyjściowe, jeśli istnieją zależności**

Jeśli zasób w kolekcji Move ma zależności od innych zasobów, zostanie wystawiony komunikat o błędzie.

![Tekst wyjściowy po sprawdzeniu zależności](./media/move-region-powershell/dependency-error.png)

### <a name="retrieve-dependencies"></a>Pobieranie zależności

Jeśli zasób, który chcesz przenieść, ma zależności od innych zasobów, możesz pobrać informacje o brakujących zależnościach.

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -SubscriptionId  “<subscription-id>”
```

**Oczekiwane dane wyjściowe**

![Tekst wyjściowy po pobraniu zależności](./media/move-region-powershell/missing-dependencies.png)

W tym przykładzie dwie zależności są identyfikowane jako brakujące:

- Źródłowa Grupa zasobów: PSDemoRM
- Karta sieciowa na maszynie wirtualnej: PSDemoVM62

## <a name="add-dependencies-to-collection"></a>Dodaj zależności do kolekcji


Korzystając z podanych przez Ciebie identyfikatorów zasobów, zidentyfikuj nazwy brakujących zasobów i Dodaj je do kolekcji Move.

### <a name="add-the-nic"></a>Dodaj kartę sieciową

Pobierz nazwę i typ karty sieciowej, a następnie dodaj ją do kolekcji.

```azurepowershell-interactive
# Get the NIC name and resource type
Get-AzResource -ResourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm/providers/microsoft.network/networkinterfaces/psdemovm62”
```

**Oczekiwane dane wyjściowe**

![Tekst wyjściowy po pobraniu karty sieciowej](./media/move-region-powershell/output-retrieve-nic.png)

Teraz Dodaj kartę sieciową do kolekcji Move. Ten przykład ma taką samą nazwę dla docelowej karty sieciowej. Zachowaj ustawienia i wielkość liter.

```azurepowershell-interactive
# Add the NIC to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -SourceId “/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkInterfaces/psdemovm62” -Name “psdemovm62” -ResourceSettingResourceType “Microsoft.Network/networkInterfaces” -ResourceSettingTargetResourceName “psdemovm62”
```

**Oczekiwane dane wyjściowe**

![Tekst wyjściowy po dodaniu karty sieciowej do kolekcji](./media/move-region-powershell/add-nic.png)

## <a name="add-the-source-resource-group"></a>Dodaj źródłową grupę zasobów

```azurepowershell-interactive
# Get the resource group name and resource type
Get-AzResource -ResourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm”

# Add the resource group to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -SourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm” -Name “psdemorm” -ResourceSettingResourceType “resourcegroups” -ResourceSettingTargetResourceName “psdemorm”
```

**Oczekiwane dane wyjściowe**

![Tekst wyjściowy po dodaniu grupy zasobów do kolekcji](./media/move-region-powershell/add-source-resource-group.png)

## <a name="recheck-dependencies"></a>Ponownie sprawdź zależności

Sprawdź ponownie w poszukiwaniu brakujących zależności.

```azurepowershell-interactive
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -SubscriptionId  “<subscription-id>”
```

**Dane wyjściowe**

Mamy dodatkowe niespełnione zależności.

![Tekst wyjściowy po ponownym sprawdzeniu zależności](./media/move-region-powershell/recheck-dependencies.png)

## <a name="retrieve-additional-dependencies"></a>Pobieranie dodatkowych zależności

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -SubscriptionId  “<subscription-id>”
```

**Oczekiwane dane wyjściowe**


![Tekst wyjściowy po pobraniu dodatkowych zależności](./media/move-region-powershell/additional-missing-dependencies.png)

W tym przykładzie są identyfikowane trzy więcej zależności:

- Publiczny adres IP: psdemovm-IP
- Azure Virtual Network: psdemorm — Sieć wirtualna
- Sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń): psdemovm-sieciowej grupy zabezpieczeń

## <a name="add-additional-dependencies"></a>Dodawanie dodatkowych zależności

1. [Postępuj zgodnie z instrukcjami,](#add-dependencies-to-collection) aby dodać te zasoby do kolekcji Move.
2. Po dodaniu zasobów ponownie sprawdź poprawność, dopóki nie zostaną dodane wszystkie zależności.


## <a name="prepare-and-move-the-source-resource-group"></a>Przygotowywanie i przenoszenie źródłowej grupy zasobów

W regionie źródłowym należy przygotować zasoby przed ich przeniesieniem. Proces przygotowywania zależy od zasobów, które są przenoszone. Na przykład w przypadku bezstanowych przygotowań Przygotuj i wyeksportuj szablon Azure Resource Manager (ARM). Lub w przypadku zasobów stanowych replikacja może się nie powieść. 

Przed przystąpieniem do przygotowania zasobów źródłowych należy przygotować i przenieść źródłową grupę zasobów.

### <a name="prepare"></a>Przygotowywanie

```azurepowershell-interactive
# Prepare the source resource group
Invoke-AzResourceMoverPrepare -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -MoveResource “PSDemoRM”
```

**Oczekiwane dane wyjściowe**

![Tekst wyjściowy po przygotowaniu źródłowej grupy zasobów](./media/move-region-powershell/prepare-source-resource-group.png)

### <a name="initiate-move"></a>Inicjowanie przenoszenia

```azurepowershell-interactive
# Initiate move
Invoke-AzResourceMoverInitiateMove -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -MoveResource “PSDemoRM”
```

**Oczekiwane dane wyjściowe**

![Tekst wyjściowy po zainicjowaniu przenoszenia źródłowej grupy zasobów](./media/move-region-powershell/initiate-move-resource-group.png)


```azurepowershell-interactive
# Commit move
Invoke-AzResourceMoverCommit -SubscriptionId “<subscription-id” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “PS-centralus-
westcentralus-demoRM” -MoveResource “PSDemoRM”
```

**Oczekiwane dane wyjściowe**

![Tekst wyjściowy po zatwierdzeniu źródłowej grupy zasobów](./media/move-region-powershell/commit-move-resource-group.png)


## <a name="prepare-resources"></a>Przygotowywanie zasobów

Po przeniesieniu źródłowej grupy zasobów do regionu docelowego Pobierz listę zasobów w kolekcji przenoszenia i przygotuj je do przeniesienia.

```azurepowershell-interactive
# Retrieve resources in the collection
Get-AzResourceMoverMoveResource  -SubscriptionId “<subscription-id>“ -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus”   | Where-Object {  $_.MoveStatusMoveState -eq “PreparePending” } | Select Name
```
**Oczekiwane dane wyjściowe**

![Tekst wyjściowy po pobraniu zasobów w kolekcji](./media/move-region-powershell/collection-resources.png)

Teraz przygotuj zasoby.

```azurepowershell-interactive
# Prepare the resources, using the resource name
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
Alternatywnie możesz przygotować i przenieść zasoby przy użyciu identyfikatora zasobu, a nie nazwy:

```azurepowershell-interactive
# Prepare the resources using the resource ID
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus  -MoveResourceInputType MoveResourceSourceId  -MoveResource $('/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkSecurityGroups/PSDemoVM-nsg')
```

**Oczekiwane dane wyjściowe**

![Tekst wyjściowy po przygotowaniu zasobów w kolekcji](./media/move-region-powershell/collection-prepare.png)

## <a name="initiate-resource-move"></a>Inicjowanie przenoszenia zasobów

Po przygotowaniu zasobów zainicjuj przechodzenie.

```azurepowershell-interactive
# Initiate the move 
Invoke-AzResourceMoverInitiateMove -SubscriptionId <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Oczekiwane dane wyjściowe**

![Tekst wyjściowy po inicjującym przeniesieniu zasobów](./media/move-region-powershell/initiate-resource-move.png)

## <a name="discard-or-commit-the-move"></a>Odrzuć lub Zatwierdź przeniesienie

Po początkowym przeniesieniu możesz zdecydować, czy chcesz zatwierdzić przeniesienie, czy go odrzucić. 

- **Odrzuć**: możesz odrzucić przeniesienie, jeśli testujesz, i nie chcesz faktycznie przenosić zasobu źródłowego. Odrzucanie przesunięcia spowoduje zwrócenie zasobu do stanu *inicjacja oczekującego przeniesienia*. W razie konieczności można ponownie zainicjować operację przenoszenia.
- **Zatwierdzenie**: zatwierdzenie powoduje zakończenie przejścia do regionu docelowego. Po zatwierdzeniu zasób źródłowy będzie w stanie *oczekiwania na usunięcie źródła* i można zdecydować, czy ma zostać usunięty.

### <a name="discard"></a>Odrzuć

Aby odrzucić przeniesienie:

```azurepowershell-interactive
# Discard the move 
Invoke-AzResourceMoverDiscard -SubscriptionId  <subscription-id> `-ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Oczekiwane dane wyjściowe**

![Tekst wyjściowy po odrzuceniu przenoszenia](./media/move-region-powershell/discard-move.png)


### <a name="commit"></a>Zatwierdzenie

Aby zatwierdzić przeniesienie:

```azurepowershell-interactive
# Commit the move 
Invoke-AzResourceMoverCommit -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Oczekiwane dane wyjściowe**

![Tekst wyjściowy po zatwierdzeniu przenoszenia](./media/move-region-powershell/commit-move.png)

## <a name="delete-source-resources"></a>Usuwanie zasobów źródłowych

Po zatwierdzeniu przenoszenia i sprawdzeniu, czy zasoby działają zgodnie z oczekiwaniami w regionie docelowym, można usunąć zasoby źródłowe w [Azure Portal](../azure-resource-manager/management/manage-resources-portal.md#delete-resources) [przy użyciu programu PowerShell](../azure-resource-manager/management/manage-resources-powershell.md#delete-resources)lub [interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/management/manage-resources-cli.md#delete-resources).

## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak](remove-move-resources.md) usunąć zasoby z kolekcji przenoszenia.
