---
title: Przenoszenie zasobów między regionami przy użyciu programu PowerShell w środowisku przenoszenia zasobów platformy Azure
description: Dowiedz się, jak przenosić zasoby między regionami przy użyciu programu PowerShell w środowisku przenoszenia zasobów platformy Azure.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/21/2021
ms.author: raynew
ms.openlocfilehash: b728170521570ff0d83b67671109e82adb7fa7b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102584426"
---
# <a name="move-resources-across-regions-in-powershell"></a>Przenoszenie zasobów między regionami w programie PowerShell

W tym artykule opisano sposób przenoszenia zasobów platformy Azure do innego regionu platformy Azure przy użyciu programu PowerShell w środowisku [przenoszenia zasobów platformy Azure](overview.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdź wymagania wstępne i wymagania.
> * Skonfiguruj kolekcję przenoszenia.
> * Dodawanie zasobów do kolekcji przenoszenia i rozwiązywanie zależności.
> * Przygotuj i Przenieś źródłową grupę zasobów. 
> * Przygotuj i Przenieś inne zasoby.
> * Zdecyduj, czy chcesz odrzucić lub zatwierdzić przeniesienie. 
> * Opcjonalnie można usunąć zasoby w regionie źródłowym po przeniesieniu.

> [!NOTE]
> Samouczki pokazują najszybszą ścieżkę w celu wypróbowania scenariusza i używają opcji domyślnych. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/). Następnie zaloguj się do [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Wymagania wstępne
**Wymaganie** | **Opis**
--- | ---
**Uprawnienia subskrypcji** | Sprawdź, czy masz dostęp *właściciela* do subskrypcji zawierającej zasoby, które chcesz przenieść<br/><br/> **Dlaczego mam dostęp do właściciela?** Przy pierwszym dodawaniu zasobu dla określonej pary źródłowej i docelowej w ramach subskrypcji platformy Azure usługa zarządzania zasobami tworzy [tożsamość zarządzaną przypisaną przez system](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (znaną wcześniej jako identyfikator usługi zarządzanej (msi), która jest zaufana przez subskrypcję. Aby utworzyć tożsamość i przypisać do niej wymaganą rolę (współautor lub administratora dostępu użytkownika w subskrypcji źródłowej), konto używane do dodawania zasobów wymaga uprawnień *właściciela* do subskrypcji. [Dowiedz się więcej](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) na temat ról platformy Azure.
**Obsługa przenoszenia zasobów** | [Przejrzyj](common-questions.md) Obsługiwane regiony i inne często zadawane pytania.
**Obsługa maszyn wirtualnych** |  Sprawdź, czy wszystkie maszyny wirtualne, które chcesz przenieść, są obsługiwane.<br/><br/> - [Sprawdź](support-matrix-move-region-azure-vm.md#windows-vm-support) obsługiwane maszyny wirtualne z systemem Windows.<br/><br/> - [Sprawdź](support-matrix-move-region-azure-vm.md#linux-vm-support) obsługiwane maszyny wirtualne i wersje jądra systemu Linux.<br/><br/> -Sprawdź obsługiwane ustawienia [obliczeń](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [magazynu](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)i [sieci](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) .
**Obsługa SQL** | Jeśli chcesz przenieść zasoby SQL, zapoznaj się z [listą wymagań SQL](tutorial-move-region-sql.md#check-sql-requirements).
**Subskrypcja docelowa** | Subskrypcja w regionie docelowym wymaga wystarczającego limitu przydziału, aby można było utworzyć zasoby, które są przenoszone w regionie docelowym. Jeśli nie ma limitu przydziału, [Zażądaj dodatkowych limitów](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Opłaty w regionie docelowym** | Sprawdź ceny i opłaty związane z regionem docelowym, do którego przenosisz maszyny wirtualne. Skorzystaj z [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/) , aby uzyskać pomoc.

### <a name="review-powershell-requirements"></a>Przegląd wymagań programu PowerShell

Większość operacji przenoszenia zasobów jest taka sama niezależnie od tego, czy jest używana Azure Portal czy PowerShell, z kilkoma wyjątkami.

**Operacja** | **Program PowerShell** | **Portal**
--- | --- | ---
**Tworzenie kolekcji przenoszenia** | Kolekcja przenoszenia (lista wszystkich przenoszonych zasobów) jest tworzona automatycznie. Wymagane uprawnienia tożsamości są przypisywane w zapleczu przez portal. | Polecenia cmdlet programu PowerShell są używane do:<br/><br/> -Utwórz grupę zasobów dla kolekcji Move i określ dla niej lokalizację.<br/><br/> -Przypisz zarządzaną tożsamość do kolekcji.<br/><br/> -Dodaj zasoby do kolekcji.
**Usuwanie kolekcji przenoszenia** | Nie można bezpośrednio usunąć kolekcji przenoszenia w portalu. | Aby usunąć kolekcję przenoszenia, należy użyć polecenia cmdlet programu PowerShell.
**Operacje przenoszenia zasobów**<br/><br/> (Przygotuj, zainicjuj przeniesienie, zatwierdzenie itp.).| Pojedyncze kroki z automatyczną walidacją według przenoszenia zasobów. | Polecenia cmdlet programu PowerShell:<br/><br/> 1) Sprawdź poprawność zależności.<br/><br/> 2) Przeprowadź przeniesienie.
**Usuwanie zasobów źródłowych** | Bezpośrednio w portalu przenoszenia zasobów. | Polecenia cmdlet programu PowerShell na poziomie typu zasobu.


### <a name="sample-values"></a>Przykładowe wartości

Używamy tych wartości w naszych przykładach skryptu:

**Ustawienie** | **Wartość** 
--- | ---
Identyfikator subskrypcji | Identyfikator subskrypcji
Region źródłowy |  Central US
Region docelowy | Zachodnio-środkowe stany USA
Grupa zasobów (przechowywanie metadanych dla kolekcji przenoszenia) | RG — Movecollection-demoRMS
Nazwa przeniesienia kolekcji | PS-środkowe-westcentralus-demoRMS
Grupa zasobów (region źródłowy) | PSDemoRM 
Grupa zasobów (region docelowy) | PSDemoRM — obiekt docelowy
Lokalizacja usługi przenoszenia zasobów | Wschodnie stany USA 2
IdentityType | SystemAssigned
Maszyna wirtualna do przeniesienia | PSDemoVM


## <a name="sign-into-azure"></a>Logowanie na platformie Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia cmdlet Connect-AzAccount:

```azurepowershell-interactive
Connect-AzAccount – Subscription "<subscription-id>"
```

## <a name="set-up-the-move-collection"></a>Konfigurowanie kolekcji przenoszenia

Obiekt Movecollection przechowuje metadane i informacje o konfiguracji dotyczące zasobów, które chcesz przenieść. Aby skonfigurować kolekcję przenoszenia, wykonaj następujące czynności:

- Utwórz grupę zasobów dla kolekcji Move.
- Zarejestruj dostawcę usług w subskrypcji, aby można było utworzyć zasób Movecollection.
- Utwórz obiekt Movecollection z zarządzaną tożsamością. Aby obiekt Movecollection mógł uzyskać dostęp do subskrypcji, w której znajduje się usługa przenoszenia zasobów, musi mieć [skojarzoną z nią tożsamość zarządzaną](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (wcześniej znaną jako tożsamość usługi ZARZĄDZANEJ (msi)), która jest zaufana przez subskrypcję.
- Przyznaj dostęp do subskrypcji przenoszenia zasobów dla tożsamości zarządzanej.

### <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów dla informacji o metadanych i konfiguracji przenoszenia kolekcji w następujący sposób:

```azurepowershell-interactive
New-AzResourceGroup -Name "RG-MoveCollection-demoRMS" -Location "East US 2"
```
**Dane wyjściowe**:

![Tekst wyjściowy po utworzeniu grupy zasobów](./media/tutorial-move-region-powershell/create-metadata-resource-group.png)

### <a name="register-the-resource-provider"></a>Rejestrowanie dostawcy zasobów

1. Zarejestruj dostawcę zasobów Microsoft. Migruj, aby można było utworzyć zasób Movecollection w następujący sposób:

    ```azurepowershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Migrate
    
2. Wait for registration:

    ```azurepowershell-interactive 
    While(((Get-AzResourceProvider -ProviderNamespace Microsoft.Migrate)| where {$_.RegistrationState -eq "Registered" -and $_.ResourceTypes.ResourceTypeName -eq "moveCollections"}|measure).Count -eq 0)
    {
        Start-Sleep -Seconds 5
        Write-Output "Waiting for registration to complete."
    }
    ```
### <a name="create-a-movecollection-object"></a>Utwórz obiekt Movecollection

Utwórz obiekt Movecollection i przypisz do niego zarządzaną tożsamość, wykonując następujące czynności: 

```azurepowershell-interactive
New-AzResourceMoverMoveCollection -Name "PS-centralus-westcentralus-demoRMS"  -ResourceGroupName "RG-MoveCollection-demoRMS" -SourceRegion "centralus" -TargetRegion "westcentralus" -Location "centraluseuap" -IdentityType "SystemAssigned"
```

**Dane wyjściowe**:

![Tekst wyjściowy po utworzeniu kolekcji przenoszenia](./media/tutorial-move-region-powershell/output-move-collection.png)


### <a name="grant-access-to-the-managed-identity"></a>Udzielanie dostępu do tożsamości zarządzanej

Przyznaj zarządzanej tożsamości dostęp do subskrypcji przenoszenia zasobów w następujący sposób. Musisz być właścicielem subskrypcji.

1. Pobierz szczegóły tożsamości z obiektu Movecollection.

    ```azurepowershell-interactive
    $moveCollection = Get-AzResourceMoverMoveCollection -SubscriptionId $subscriptionId -ResourceGroupName "RG-MoveCollection-demoRMS" -Name "PS-centralus-westcentralus-demoRMS"

    $identityPrincipalId = $moveCollection.IdentityPrincipalId   
    ``` 

2. Przypisz wymagane role do tożsamości, aby usługa Azure Resource przenoszona mogła uzyskać dostęp do subskrypcji w celu ułatwienia przenoszenia zasobów.

    ```azurepowershell-interactive
    New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName Contributor -Scope "/subscriptions/$subscriptionId"

    New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName "User Access Administrator" -Scope "/subscriptions/$subscriptionId"
    ``` 

## <a name="add-resources-to-the-move-collection"></a>Dodawanie zasobów do kolekcji przenoszenia

Pobierz identyfikatory dla istniejących zasobów źródłowych, które chcesz przenieść. Utwórz obiekt ustawień zasobów docelowych, a następnie Dodaj zasoby do kolekcji Move.

> [!NOTE]
> Zasoby dodane do kolekcji przenoszenia muszą znajdować się w tej samej subskrypcji, ale mogą znajdować się w różnych grupach zasobów.

Dodaj zasoby w następujący sposób:

1. Pobierz identyfikator zasobu źródłowego:

    ```azurepowershell-interactive
    Get-AzResource -Name PSDemoVM -ResourceGroupName PSDemoRM
    ```

    **Dane wyjściowe**

    ![Tekst wyjściowy po pobraniu identyfikatora zasobu](./media/tutorial-move-region-powershell/output-retrieve-resource.png)

2. Utwórz obiekt ustawień zasobów docelowych zgodnie z przenoszonym zasobem. W naszym przypadku jest to maszyna wirtualna.

    ```azurepowershell-interactive
    $targetResourceSettingsObj = New-Object Microsoft.Azure.PowerShell.Cmdlets.ResourceMover.Models.Api202101.VirtualMachineResourceSettings
    ```

3. Ustaw typ zasobu i nazwę zasobu docelowego dla obiektu.

    ```azurepowershell-interactive
    $targetResourceSettingsObj.ResourceType = "Microsoft.Compute/virtualMachines"
    $targetResourceSettingsObj.TargetResourceName = "PSDemoVM"
    ```
    > [!NOTE]
    > Nasza docelowa maszyna wirtualna ma taką samą nazwę jak maszyna wirtualna w regionie źródłowym. Możesz wybrać inną nazwę.

4. Dodaj zasoby źródłowe do kolekcji Move przy użyciu podanych lub utworzonych obiektu ustawienia identyfikator zasobu i docelowy.

    ```azurepowershell-interactive
    Add-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx xxxxxxxxxxxx/resourceGroups/
    PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM" -Name "PSDemoVM" -ResourceSetting $targetResourceSettingsObj
    ```

    **Dane wyjściowe** ![ Tekst wyjściowy po dodaniu zasobu](./media/tutorial-move-region-powershell/output-add-resource.png)

## <a name="validate-and-add-dependencies"></a>Weryfikowanie i Dodawanie zależności

Sprawdź, czy dodane zasoby mają jakiekolwiek zależności od innych zasobów i Dodaj je w razie konieczności. 

1. Sprawdź poprawność zależności w następujący sposób:

    ```azurepowershell-interactive
    Resolve-AzResourceMoverMoveCollectionDependency -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```
    **Dane wyjściowe (gdy istnieją zależności)**

    ![Tekst wyjściowy po walidacji zależności](./media/tutorial-move-region-powershell/dependency-output.png)

2. Brak zależności tożsamości:

    - Aby pobrać listę wszystkich brakujących zależności:

        ```azurepowershell-interactive
        Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Descendant"
        ```
        **Dane wyjściowe** ![ Tekst wyjściowy po pobraniu listy wszystkich zależności](./media/tutorial-move-region-powershell/dependencies-list.png)  

    - Aby pobrać tylko zależności pierwszego poziomu (bezpośrednie zależności dla zasobu):

        ```azurepowershell-interactive
        Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Direct
        ```
        **Dane wyjściowe** ![ Tekst wyjściowy po pobraniu listy zależności pierwszego poziomu](./media/tutorial-move-region-powershell/dependencies-list-direct.png)  

3. Aby dodać brakujące niespełnione zależności, powtórz powyższe instrukcje, aby [dodać zasoby do kolekcji przenoszenia i ponownie](#add-resources-to-the-move-collection)sprawdzić poprawność, dopóki nie zostaną niewykorzystane zasoby.

> [!NOTE]
> Jeśli z jakiegoś powodu chcesz usunąć zasoby z kolekcji zasobów, postępuj zgodnie z instrukcjami w [tym artykule](remove-move-resources.md).

## <a name="add-the-source-resource-group"></a>Dodaj źródłową grupę zasobów

Dodaj źródłową grupę zasobów zawierającą zasoby, które chcesz przenieść do kolekcji przenoszenia.

1. Pobierz identyfikator grupy zasobów.

    ```azurepowershell-interactive
    Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Direct
    ```
    **Dane wyjściowe** ![ Tekst wyjściowy po pobraniu identyfikatora źródłowej grupy zasobów](./media/tutorial-move-region-powershell/source-resource-group-id.png)  

    > [!NOTE]
    > Korzystamy z grupy zasobów, która znajduje się już w regionie docelowym.

 
2. Aby dodać grupę zasobów do kolekcji, użyj pobranego identyfikatora.

    ```azurepowershell-interactive
    Add-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS"  -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/psdemorm"  -Name "psdemorm"  -ExistingTargetId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/PSDemoRM-target"
    ```
    **Dane wyjściowe** ![ Tekst wyjściowy po dodaniu źródłowej grupy zasobów do kolekcji przenoszenia](./media/tutorial-move-region-powershell/add-source-resource-group.png)

3. Sprawdź zależności, aby upewnić się, że nie pominięto żadnych elementów po dodaniu grupy zasobów.

    ```azurepowershell-interactive
    Resolve-AzResourceMoverMoveCollectionDependency -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```
4. Widzimy, że nie ma żadnych oczekujących zależności.

    **Dane wyjściowe** ![ Tekst wyjściowy po sprawdzeniu zależności](./media/tutorial-move-region-powershell/all-dependencies-added.png)


## <a name="prepare-resources"></a>Przygotowywanie zasobów

Zwykle należy przygotować zasoby w regionie źródłowym przed przeniesieniem. Na przykład:

- Aby przenieść bezstanowe zasoby, takie jak sieci wirtualne platformy Azure, karty sieciowe, moduły równoważenia obciążenia i sieciowe grupy zabezpieczeń, może być konieczne wyeksportowanie szablonu Azure Resource Manager.
- Aby przenieść zasoby stanowe, takie jak maszyny wirtualne platformy Azure i bazy danych SQL, może być konieczne rozpoczęcie replikowania zasobów z regionu źródłowego do docelowego.

W tym samouczku, ponieważ przenosimy maszyny wirtualne, musimy przygotować źródłową grupę zasobów, a następnie zainicjować i zatwierdzić jej przemieszczenie, zanim będziemy mogli rozpocząć przygotowywanie maszyn wirtualnych.

> [!NOTE]
> Jeśli masz istniejącą docelową grupę zasobów, możesz bezpośrednio zatwierdzić przeniesienie dla źródłowej grupy zasobów i pominąć etapy przygotowywania i inicjowania przenoszenia.

  
### <a name="prepare-the-source-resource-group"></a>Przygotuj źródłową grupę zasobów:

1. Przygotuj grupę zasobów:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    **Dane wyjściowe**

    ![Tekst wyjściowy po przygotowaniu źródłowej grupy zasobów](./media/tutorial-move-region-powershell/prepare-source-resource-group.png)

2. Zainicjuj przenoszenie źródłowej grupy zasobów.

    ```azurepowershell-interactive
    "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    ![Tekst wyjściowy po zainicjowaniu przenoszenia źródłowej grupy zasobów](./media/tutorial-move-region-powershell/initiate-move-source-resource-group.png)

3. Zatwierdź przeniesienie dla źródłowej grupy zasobów.

    ```azurepowershell-interactive
    Invoke-AzResourceMoverCommit -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    **Dane wyjściowe**

    ![Tekst wyjściowy po zatwierdzeniu źródłowej grupy zasobów](./media/tutorial-move-region-powershell/commit-move-source-resource-group.png)


### <a name="prepare-vm-resources"></a>Przygotowywanie zasobów maszyny wirtualnej

Po przygotowaniu i przeniesieniu źródłowej grupy zasobów można przygotować zasoby maszyn wirtualnych do przeniesienia.

1. Sprawdź poprawność zależności przed przygotowaniem zasobów maszyny wirtualnej.

    ```azurepowershell-interactive
    $resp = Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm') -ValidateOnly
    ```
    **Dane wyjściowe**

    ![Tekst wyjściowy po walidacji maszyny wirtualnej przed jej przygotowaniem](./media/tutorial-move-region-powershell/validate-vm-before-move.png)

2. Pobierz zasoby zależne, które muszą zostać przygotowane wraz z maszyną wirtualną.

    ```azurepowershell-interactive
    $resp.AdditionalInfo[0].InfoMoveResource
    ```
    **Dane wyjściowe**

    ![Tekst wyjściowy po pobraniu zależnych zasobów maszyny wirtualnej](./media/tutorial-move-region-powershell/get-resources-before-prepare.png)

3. Zainicjuj proces przygotowywania dla wszystkich zasobów zależnych.

    ```azurepowershell-interactive
    Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('PSDemoVM','psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg')
    ```
    **Dane wyjściowe**

    ![Tekst wyjściowy po initating przygotowanie wszystkich zasobów](./media/tutorial-move-region-powershell/initiate-prepare-all.png)


    > [!NOTE]
    > Możesz podać identyfikator zasobu źródłowego zamiast nazwy zasobu jako parametry wejściowe dla polecenia cmdlet Prepare, a także w poleceniach inicjowania przenoszenia i zatwierdzania. Aby to zrobić, uruchom:


    ```azurepowershell-interactive
        Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -MoveResourceInputType MoveResourceSourceId  -MoveResource $('/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/PSDemoRMS/providers/Microsoft.Network/networkSecurityGroups/PSDemoVM-nsg')
    ```

## <a name="initiate-move-of-vm-resources"></a>Inicjowanie przenoszenia zasobów maszyny wirtualnej

1. Sprawdź, czy zasoby maszyny wirtualnej znajdują się w stanie *inicjowania przenoszenia oczekujące* :

    ```azurepowershell-interactive
    Get-AzResourceMoverMoveResource  -SubscriptionId “ xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx “ -ResourceGroupName “RG-MoveCollection-demoRMS” -MoveCollectionName “PS-centralus-westcentralus-demoRMS ”   | Where-Object {  $_.MoveStatusMoveState -eq “InitiateMovePending” } | Select Name
    ```    

    **Dane wyjściowe**

    ![Tekst wyjściowy po sprawdzeniu stanu inicjowania](./media/tutorial-move-region-powershell/verify-initiate-move-pending.png)

2. Zainicjuj przenoszenie:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverInitiateMove -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
    ```    

    **Dane wyjściowe**

    ![Tekst wyjściowy po zainicjowaniu przenoszenia zasobów](./media/tutorial-move-region-powershell/initiate-resources-move.png)


## <a name="discard-or-commit"></a>Odrzucić lub zatwierdzić?

Po początkowym przeniesieniu możesz zdecydować, czy chcesz zatwierdzić przeniesienie, czy go odrzucić. 

- **Odrzuć**: możesz odrzucić przeniesienie, jeśli testujesz, i nie chcesz faktycznie przenosić zasobu źródłowego. Odrzucanie przesunięcia spowoduje zwrócenie zasobu do stanu *inicjacja oczekującego przeniesienia*. W razie konieczności można ponownie zainicjować operację przenoszenia.
- **Zatwierdzenie**: zatwierdzenie powoduje zakończenie przejścia do regionu docelowego. Po zatwierdzeniu zasób źródłowy będzie w stanie *oczekiwania na usunięcie źródła* i można zdecydować, czy ma zostać usunięty.

### <a name="discard-the-move"></a>Odrzuć przeniesienie

Aby odrzucić przeniesienie:

```azurepowershell-interactive
Invoke-AzResourceMoverDiscard -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
```
**Dane wyjściowe**

![Tekst wyjściowy po odrzuceniu przenoszenia](./media/tutorial-move-region-powershell/discard-move.png)


### <a name="commit-the-move"></a>Zatwierdź przeniesienie

1. Zatwierdź przeniesienie w następujący sposób:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverCommit -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
    ```
    **Dane wyjściowe**

    ![Tekst wyjściowy po zatwierdzeniu przenoszenia](./media/tutorial-move-region-powershell/commit-move.png)

2. Sprawdź, czy wszystkie zasoby zostały przeniesione do regionu docelowego:

    ```azurepowershell-interactive
    Get-AzResourceMoverMoveResource  -ResourceGroupName “RG-MoveCollection-demoRMS ” -MoveCollectionName “PS-centralus-westcentralus-demoRMS”   
    ```
    Wszystkie zasoby są teraz w stanie *oczekiwania na usunięcie źródła* w regionie docelowym.

## <a name="delete-source-resources"></a>Usuwanie zasobów źródłowych

Po zatwierdzeniu przenoszenia i sprawdzeniu, czy zasoby działają zgodnie z oczekiwaniami w regionie docelowym, można usunąć wszystkie zasoby źródłowe w [Azure Portal](../azure-resource-manager/management/manage-resources-portal.md#delete-resources) [przy użyciu programu PowerShell](../azure-resource-manager/management/manage-resources-powershell.md#delete-resources)lub [interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/management/manage-resources-cli.md#delete-resources).

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Przenoszenie maszyn wirtualnych platformy Azure do innego regionu platformy Azure przy użyciu programu PowerShell.
> * Przenoszenie zasobów skojarzonych z maszynami wirtualnymi do innego regionu.

Teraz trwa próba przeniesienia maszyn wirtualnych platformy Azure przy użyciu portalu

> [!div class="nextstepaction"]
> [Przenoszenie maszyn wirtualnych platformy Azure w portalu](./tutorial-move-region-virtual-machines.md)


