---
title: Przenoszenie konfiguracji konserwacji do innego regionu świadczenia usługi Azure
description: Dowiedz się, jak przenieść konfigurację konserwacji maszyny wirtualnej do innego regionu świadczenia usługi Azure
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: f833f3eb9e3d94da6178a0a9a9cf4f95ec0682e7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865373"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>Przenoszenie konfiguracji sterowania konserwacją do innego regionu

Wykonaj czynności z tego artykułu, aby przenieść konfigurację kontrolki konserwacji do innego regionu świadczenia usługi Azure. Konfigurację można przenieść z kilku powodów. Na przykład w celu skorzystania z nowego regionu, wdrożenia funkcji lub usług dostępnych w określonym regionie, spełnienia wewnętrznych wymagań dotyczących zasad i ładu lub w odpowiedzi na planowanie pojemności.

[Kontrolka konserwacji](maintenance-control.md)z dostosowanymi konfiguracjami konserwacji umożliwia kontrolowanie sposobu stosowania aktualizacji platformy do maszyn wirtualnych i hostów dedykowanych platformy Azure. Istnieje kilka scenariuszy przenoszenia kontroli konserwacji między regionami:

- Aby przenieść konfigurację sterowania konserwacją, ale nie zasoby skojarzone z konfiguracją, postępuj zgodnie z instrukcjami w tym artykule.
- Aby przenieść zasoby skojarzone z konfiguracją konserwacji, ale nie samą konfiguracją, postępuj zgodnie [z tymi instrukcjami.](move-region-maintenance-configuration-resources.md)
- Aby przenieść zarówno konfigurację konserwacji, jak i skojarzone z nią zasoby, najpierw postępuj zgodnie z instrukcjami w tym artykule. Następnie postępuj zgodnie z [tymi instrukcjami.](move-region-maintenance-configuration-resources.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem przenoszenia konfiguracji sterowania konserwacją:

- Konfiguracje konserwacji są skojarzone z maszynami wirtualnych platformy Azure lub dedykowanymi hostami platformy Azure. Przed rozpoczęciem upewnij się, że zasoby maszyny wirtualnej/hosta istnieją w nowym regionie.
- Identyfikacji: 
    - Istniejące konfiguracje sterowania konserwacją.
    - Grupy zasobów, w których obecnie znajdują się istniejące konfiguracje. 
    - Grupy zasobów, do których zostaną dodane konfiguracje po przeniesieniu do nowego regionu. 
    - Zasoby skojarzone z konfiguracją konserwacji, którą chcesz przenieść.
    - Sprawdź, czy zasoby w nowym regionie są takie same jak te skojarzone z bieżącymi konfiguracjami konserwacji. Konfiguracje mogą mieć takie same nazwy w nowym regionie, jak w starym, ale nie jest to wymagane.

## <a name="prepare-and-move"></a>Przygotowywanie i przenoszenie 

1. Pobierz wszystkie konfiguracje konserwacji w każdej subskrypcji. W tym celu [uruchom polecenie az maintenance configuration list](/cli/azure/maintenance/configuration#az_maintenance_configuration_list) interfejsu wiersza polecenia, zastępując $subId identyfikatorem subskrypcji.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. Przejrzyj listę zwróconych tabel rekordów konfiguracji w ramach subskrypcji. Oto przykład. Lista będzie zawierać wartości dla określonego środowiska.

    **Nazwa** | **Lokalizacja** | **Grupa zasobów**
    --- | --- | ---
    Pomijanie konserwacji | eastus2 | configuration-resource-group
    IgniteDemoConfig | eastus2 | configuration-resource-group
    defaultMaintenanceConfiguration-eastus | eastus | test-configuration
    

3. Zapisz listę do odwołania. Podczas przenoszenia konfiguracji można sprawdzić, czy wszystko zostało przeniesione.
4. Jako odwołanie zamapuj każdą konfigurację/grupę zasobów na nową grupę zasobów w nowym regionie.
5. Utwórz nowe konfiguracje konserwacji w nowym regionie przy użyciu [programu PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)lub interfejsu [wiersza polecenia](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration).
6. Skojarz konfiguracje z zasobami w nowym regionie przy użyciu [programu PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)lub interfejsu [wiersza polecenia](../virtual-machines/maintenance-control-cli.md#assign-the-configuration).


## <a name="verify-the-move"></a>Weryfikowanie przeniesienia

Po przeniesieniu konfiguracji porównaj konfiguracje i zasoby w nowym regionie z przygotowaną listą tabel.


## <a name="clean-up-source-resources"></a>Czyszczenie zasobów źródłowych

Po zakończeniu przenoszenia rozważ usunięcie przeniesionych konfiguracji konserwacji w regionie źródłowym, [programie PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)lub interfejsie wiersza [polecenia](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration).


## <a name="next-steps"></a>Następne kroki

Postępuj [zgodnie z tymi](move-region-maintenance-configuration-resources.md) instrukcjami, jeśli musisz przenieść zasoby skojarzone z konfiguracjami konserwacji. 
