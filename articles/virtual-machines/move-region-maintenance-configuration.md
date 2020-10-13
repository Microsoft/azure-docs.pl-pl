---
title: Przenoszenie konfiguracji konserwacji do innego regionu platformy Azure
description: Dowiedz się, jak przenieść konfigurację obsługi maszyny wirtualnej do innego regionu platformy Azure
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 4cff7eb4a69005f2e74747b6e58447f100c69b60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86501606"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>Przenoszenie konfiguracji kontroli konserwacji do innego regionu

Postępuj zgodnie z tym artykułem, aby przenieść konfigurację kontroli konserwacji do innego regionu platformy Azure. Możesz chcieć przenieść konfigurację z kilku powodów. Na przykład w celu skorzystania z nowego regionu, wdrożenia funkcji lub usług dostępnych w określonym regionie, spełnienia wymagań wewnętrznych zasad i zarządzania lub w reakcji na planowanie pojemności.

Kontrola konserwacji z dostosowanymi konfiguracjami konserwacji pozwala kontrolować sposób stosowania aktualizacji platformy do maszyn wirtualnych z [systemem Windows](./maintenance-control-cli.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) i [Linux](./maintenance-control-cli.md?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) oraz do hostów dedykowanych platformy Azure. Istnieje kilka scenariuszy związanych z przechodzeniem kontroli konserwacji między regionami:

- Aby przenieść konfigurację kontroli konserwacji, ale nie zasobów skojarzonych z konfiguracją, postępuj zgodnie z instrukcjami w tym artykule.
- Aby przenieść zasoby skojarzone z konfiguracją konserwacji, ale nie sama konfiguracja, wykonaj [te instrukcje](move-region-maintenance-configuration-resources.md).
- Aby przenieść konfigurację konserwacji i skojarzone z nią zasoby, najpierw postępuj zgodnie z instrukcjami w tym artykule. Następnie postępuj zgodnie z [tymi instrukcjami](move-region-maintenance-configuration-resources.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem przechodzenia do konfiguracji kontroli konserwacji:

- Konfiguracje konserwacji są skojarzone z maszynami wirtualnymi platformy Azure lub dedykowanymi hostami platformy Azure. Przed rozpoczęciem upewnij się, że zasoby maszyny wirtualnej/hosta znajdują się w nowym regionie.
- Ustalenia 
    - Istniejące konfiguracje kontroli konserwacji.
    - Grupy zasobów, w których aktualnie znajdują się istniejące konfiguracje. 
    - Grupy zasobów, do których zostaną dodane konfiguracje po przejściu do nowego regionu. 
    - Zasoby skojarzone z konfiguracją konserwacji, która ma zostać przeniesiona.
    - Sprawdź, czy zasoby w nowym regionie są takie same jak te, które są skojarzone z bieżącymi konfiguracjami konserwacji. Konfiguracje mogą mieć takie same nazwy w nowym regionie, jak w starym, ale nie jest to wymagane.

## <a name="prepare-and-move"></a>Przygotowywanie i przenoszenie 

1. Pobierz wszystkie konfiguracje konserwacji w ramach każdej subskrypcji. Uruchom interfejs wiersza polecenia [AZ Maintenance Configuration list](/cli/azure/ext/maintenance/maintenance/configuration?view=azure-cli-latest#ext-maintenance-az-maintenance-configuration-list) , aby to zrobić, ZASTĘPUJĄC $subId identyfikatorem subskrypcji.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. Przejrzyj listę zwracanych tabel rekordów konfiguracji w ramach subskrypcji. Oto przykład. Lista będzie zawierać wartości dla określonego środowiska.

    **Nazwa** | **Lokalizacja** | **Grupa zasobów**
    --- | --- | ---
    Pomiń konserwację | eastus2 | Konfiguracja — Grupa zasobów
    IgniteDemoConfig | eastus2 | Konfiguracja — Grupa zasobów
    defaultMaintenanceConfiguration — wschód | eastus | Test-konfiguracja
    

3. Zapisz listę do odwołania. Podczas przenoszenia konfiguracji można sprawdzić, czy wszystkie elementy zostały przeniesione.
4. Jako odwołanie, Mapuj każdą konfigurację/grupę zasobów do nowej grupy zasobów w nowym regionie.
5. Utwórz nowe konfiguracje konserwacji w nowym regionie przy użyciu [programu PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)lub [interfejsu wiersza polecenia](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration).
6. Skojarz konfiguracje z zasobami w nowym regionie przy użyciu [programu PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)lub [interfejsu wiersza polecenia](../virtual-machines/maintenance-control-cli.md#assign-the-configuration).


## <a name="verify-the-move"></a>Weryfikowanie przenoszenia

Po przeniesieniu konfiguracji należy porównać konfiguracje i zasoby w nowym regionie z przygotowaną listą tabel.


## <a name="clean-up-source-resources"></a>Czyszczenie zasobów źródłowych

Po przeniesieniu należy rozważyć usunięcie przenoszonych konfiguracji konserwacji w regionie źródłowym, programie [PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)lub [interfejsie wiersza polecenia](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration).


## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z [tymi instrukcjami](move-region-maintenance-configuration-resources.md) , jeśli chcesz przenieść zasoby skojarzone z konfiguracjami konserwacji. 
