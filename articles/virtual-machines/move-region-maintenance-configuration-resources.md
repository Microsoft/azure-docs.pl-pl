---
title: Przenoszenie zasobów skojarzonych z konfiguracją konserwacji do innego regionu
description: Dowiedz się, jak przenieść zasoby skojarzone z konfiguracją konserwacji maszyny wirtualnej do innego regionu świadczenia usługi Azure
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 4427071edf237d82e8a99d44678d77d23e180fff
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865247"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>Przenoszenie zasobów w konfiguracji kontrolki konserwacji do innego regionu

Postępuj zgodnie z tym artykułem, aby przenieść zasoby skojarzone z konfiguracją kontrolki konserwacji do innego regionu świadczenia usługi Azure. Konfigurację można przenieść z kilku powodów. Na przykład w celu skorzystania z nowego regionu, wdrożenia funkcji lub usług dostępnych w określonym regionie, spełnienia wewnętrznych wymagań dotyczących zasad i ładu lub w odpowiedzi na planowanie pojemności.

[Kontrolka konserwacji](maintenance-control.md)z dostosowanymi konfiguracjami konserwacji umożliwia kontrolowanie sposobu stosowania aktualizacji platformy do maszyn wirtualnych i dedykowanych hostów platformy Azure. Istnieje kilka scenariuszy przenoszenia kontroli konserwacji między regionami:

- Aby przenieść zasoby skojarzone z konfiguracją konserwacji, ale nie samą konfiguracją, postępuj zgodnie z tym artykułem.
- Aby przenieść konfigurację sterowania konserwacją, ale nie zasoby skojarzone z konfiguracją, postępuj zgodnie [z tymi instrukcjami.](move-region-maintenance-configuration.md)
- Aby przenieść zarówno konfigurację konserwacji, jak i skojarzone z nią zasoby, najpierw postępuj zgodnie [z tymi instrukcjami.](move-region-maintenance-configuration.md) Następnie postępuj zgodnie z instrukcjami w tym artykule.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem przenoszenia zasobów skojarzonych z konfiguracją sterowania konserwacją:

- Przed rozpoczęciem upewnij się, że przenoszące zasoby istnieją w nowym regionie.
- Sprawdź konfiguracje kontroli konserwacji skojarzone z maszynami wirtualnych platformy Azure i dedykowanymi hostami platformy Azure, które chcesz przenieść. Sprawdź każdy zasób osobno. Obecnie nie ma możliwości pobrania konfiguracji dla wielu zasobów.
- Podczas pobierania konfiguracji dla zasobu:
    - Upewnij się, że używasz identyfikatora subskrypcji dla konta, a nie identyfikatora Azure Dedicated Host subskrypcji.
    - Interfejs wiersza polecenia: parametr --output tabeli jest używany tylko do odczytu i można go usunąć lub zmienić.
    - PowerShell: parametr Format-Table Name jest używany tylko do odczytu i można go usunąć lub zmienić.
    - Jeśli używasz programu PowerShell, jeśli spróbujesz wyświetlić listę konfiguracji dla zasobu, który nie ma żadnych skojarzonych konfiguracji, wystąpi błąd. Błąd będzie podobny do: "Operacja nie powiodła się ze stanem: Nie znaleziono". Szczegóły: Błąd klienta 404: Nie znaleziono adresu URL".

    
## <a name="prepare-to-move"></a>Przygotowanie do przeniesienia

1. Przed rozpoczęciem zdefiniuj te zmienne. Dla każdego z nich podano przykład.

    **Zmienna** | **Szczegóły** | **Przykład**
    --- | ---
    $subId | Identyfikator subskrypcji zawierającej konfiguracje konserwacji | "our-subscription-ID"
    $rsrcGroupName | Nazwa grupy zasobów (maszyna wirtualna platformy Azure) | "VMResourceGroup"
    $vmName | Nazwa zasobu maszyny wirtualnej |  "myVM"
    $adhRsrcGroupName |  Grupa zasobów (dedykowane hosty) | "HostResourceGroup"
    $adh | Nazwa dedykowanego hosta | "myHost"
    $adhParentName | Nazwa zasobu nadrzędnego | "HostGroup"
    
2. Aby pobrać konfiguracje konserwacji za pomocą polecenia [Get-AZConfigurationAssignment](/powershell/module/az.maintenance/get-azconfigurationassignment) programu PowerShell:

    - W przypadku dedykowanych hostów platformy Azure uruchom:
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - W przypadku maszyn wirtualnych platformy Azure uruchom:

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. Aby pobrać konfiguracje konserwacji za pomocą polecenia [az maintenance assignment interfejsu](/cli/azure/maintenance/assignment) wiersza polecenia:

    - W przypadku dedykowanych hostów platformy Azure:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - W przypadku maszyn wirtualnych platformy Azure:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>Move 

1. [Postępuj zgodnie z tymi instrukcjami,](../site-recovery/azure-to-azure-tutorial-migrate.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) aby przenieść maszyny wirtualne platformy Azure do nowego regionu.
2. Po przenieść zasoby, ponowniezastosuj konfiguracje konserwacji do zasobów w nowym regionie zgodnie z potrzebami, w zależności od tego, czy przeniesiono konfiguracje konserwacji. Konfigurację konserwacji można zastosować do zasobu przy użyciu programu [PowerShell lub](../virtual-machines/maintenance-control-powershell.md) interfejsu wiersza [polecenia.](../virtual-machines/maintenance-control-cli.md)


## <a name="verify-the-move"></a>Weryfikowanie przeniesienia

Zweryfikuj zasoby w nowym regionie i zweryfikuj skojarzone konfiguracje zasobów w nowym regionie. 

## <a name="clean-up-source-resources"></a>Czyszczenie zasobów źródłowych

Po zakończeniu przenoszenia rozważ usunięcie przeniesionych zasobów w regionie źródłowym.


## <a name="next-steps"></a>Następne kroki

Postępuj [zgodnie z tymi](move-region-maintenance-configuration.md) instrukcjami, jeśli musisz przenieść konfiguracje konserwacji. 
