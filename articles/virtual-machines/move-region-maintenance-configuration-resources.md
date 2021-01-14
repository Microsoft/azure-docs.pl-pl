---
title: Przenoszenie zasobów skojarzonych z konfiguracją konserwacji do innego regionu
description: Informacje o sposobie przenoszenia zasobów skojarzonych z konfiguracją konserwacji maszyn wirtualnych do innego regionu platformy Azure
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 7c4d9f3a5f73e5832e9ff22ed2cd4c215811c845
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197561"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>Przenoszenie zasobów w konfiguracji kontroli konserwacji do innego regionu

Postępuj zgodnie z tym artykułem, aby przenieść zasoby skojarzone z konfiguracją kontroli konserwacji do innego regionu platformy Azure. Możesz chcieć przenieść konfigurację z kilku powodów. Na przykład w celu skorzystania z nowego regionu, wdrożenia funkcji lub usług dostępnych w określonym regionie, spełnienia wymagań wewnętrznych zasad i zarządzania lub w reakcji na planowanie pojemności.

[Kontrola konserwacji](maintenance-control.md)z dostosowanymi konfiguracjami konserwacji pozwala kontrolować sposób stosowania aktualizacji platformy do maszyn wirtualnych oraz do hostów dedykowanych platformy Azure. Istnieje kilka scenariuszy związanych z przechodzeniem kontroli konserwacji między regionami:

- Aby przenieść zasoby skojarzone z konfiguracją konserwacji, ale nie samą konfigurację, wykonaj czynności opisane w tym artykule.
- Aby przenieść konfigurację kontroli konserwacji, ale nie zasobów skojarzonych z konfiguracją, wykonaj [te instrukcje](move-region-maintenance-configuration.md).
- Aby przenieść konfigurację konserwacji i skojarzone z nią zasoby, najpierw postępuj zgodnie z [tymi instrukcjami](move-region-maintenance-configuration.md). Następnie postępuj zgodnie z instrukcjami w tym artykule.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem przeniesienia zasobów skojarzonych z konfiguracją kontroli konserwacji:

- Przed rozpoczęciem upewnij się, że przenoszone zasoby znajdują się w nowym regionie.
- Sprawdź konfiguracje kontroli konserwacji skojarzone z maszynami wirtualnymi platformy Azure i dedykowanymi hostami platformy Azure, które chcesz przenieść. Sprawdź każdy zasób osobno. Obecnie nie ma możliwości pobrania konfiguracji dla wielu zasobów.
- Podczas pobierania konfiguracji dla zasobu:
    - Upewnij się, że używasz identyfikatora subskrypcji dla konta, a nie dedykowanego identyfikatora hosta platformy Azure.
    - Interfejs wiersza polecenia: parametr--Output Table jest używany tylko do odczytu i może zostać usunięty lub zmieniony.
    - PowerShell: parametr name Format-Table jest używany tylko do odczytu i może zostać usunięty lub zmieniony.
    - Jeśli używasz programu PowerShell, występuje błąd, jeśli spróbujesz wyświetlić konfiguracje dla zasobu, który nie ma żadnych skojarzonych konfiguracji. Błąd będzie wyglądać podobnie do: "operacja nie powiodła się. stan: nie znaleziono". Szczegóły: 404 błąd klienta: nie znaleziono adresu URL ".

    
## <a name="prepare-to-move"></a>Przygotuj do przeniesienia

1. Przed rozpoczęciem należy zdefiniować te zmienne. Podano przykład dla każdego z nich.

    **Zmienna** | **Szczegóły** | **Przykład**
    --- | ---
    $subId | Identyfikator subskrypcji zawierającej konfiguracje konserwacji | "nasz-Subscription-ID"
    $rsrcGroupName | Nazwa grupy zasobów (maszyna wirtualna platformy Azure) | "VMResourceGroup"
    $vmName | Nazwa zasobu maszyny wirtualnej |  MyVM
    $adhRsrcGroupName |  Grupa zasobów (hosty dedykowane) | "HostResourceGroup"
    $adh | Nazwa dedykowanego hosta | "Moje host"
    $adhParentName | Nazwa zasobu nadrzędnego | HostGroup
    
2. Aby pobrać konfiguracje konserwacji za pomocą polecenia programu PowerShell [Get-AZConfigurationAssignment](/powershell/module/az.maintenance/get-azconfigurationassignment?view=azps-3.5.0) :

    - W przypadku hostów dedykowanych platformy Azure Uruchom polecenie:
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - W przypadku maszyn wirtualnych platformy Azure Uruchom polecenie:

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. Aby pobrać konfiguracje konserwacji przy użyciu interfejsu wiersza polecenia [AZ Maintenance przypisania](/cli/azure/ext/maintenance/maintenance/assignment) :

    - Dla dedykowanych hostów platformy Azure:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - Dla maszyn wirtualnych platformy Azure:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>Move 

1. [Postępuj zgodnie z tymi instrukcjami](../site-recovery/azure-to-azure-tutorial-migrate.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) , aby przenieść maszyny wirtualne platformy Azure do nowego regionu.
2. Po przeniesieniu zasobów należy ponownie zastosować konfiguracje konserwacji do zasobów w nowym regionie, w zależności od tego, czy zostały przeniesione konfiguracje konserwacji. Konfigurację konserwacji można zastosować do zasobu przy użyciu [programu PowerShell](../virtual-machines/maintenance-control-powershell.md) lub [interfejsu wiersza polecenia](../virtual-machines/maintenance-control-cli.md).


## <a name="verify-the-move"></a>Weryfikowanie przenoszenia

Sprawdź zasoby w nowym regionie i sprawdź skojarzone konfiguracje dla zasobów w nowym regionie. 

## <a name="clean-up-source-resources"></a>Czyszczenie zasobów źródłowych

Po przeniesieniu należy rozważyć usunięcie przeniesionych zasobów w regionie źródłowym.


## <a name="next-steps"></a>Następne kroki

Jeśli chcesz przenieść konfiguracje konserwacji, postępuj zgodnie z [tymi instrukcjami](move-region-maintenance-configuration.md) . 
