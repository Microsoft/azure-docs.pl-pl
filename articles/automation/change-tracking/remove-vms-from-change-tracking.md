---
title: Usuwanie maszyn wirtualnych z Azure Automation Change Tracking i spisu
description: W tym artykule opisano sposób usuwania maszyn wirtualnych z Change Tracking i spisu.
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 0b79fa22d3203504e63161aba03b32830d74d016
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93131279"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Usuwanie maszyn wirtualnych ze śledzenia zmian i spisu

Po zakończeniu śledzenia zmian na maszynach wirtualnych w środowisku można przestać zarządzać nimi za pomocą funkcji [Change Tracking i spisu](overview.md) . Aby przestać zarządzać nimi, Edytuj zapisane zapytanie wyszukiwania `MicrosoftDefaultComputerGroup` w obszarze roboczym log Analytics, który jest połączony z kontem usługi Automation.

## <a name="sign-into-the-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Aby usunąć maszyny wirtualne

1. W Azure Portal Uruchom **Cloud Shell** od górnego nawigowania Azure Portal. Jeśli nie znasz Azure Cloud Shell, zobacz [omówienie Azure Cloud Shell](../../cloud-shell/overview.md).

2. Użyj następującego polecenia, aby zidentyfikować identyfikator UUID komputera, który chcesz usunąć z zarządzania.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. W Azure Portal przejdź do **obszaru log Analytics obszary robocze**. Z listy wybierz swój obszar roboczy.

4. W obszarze roboczym Log Analytics wybierz pozycję **dzienniki** , a następnie wybierz pozycję **Eksplorator zapytań** z menu najważniejsze akcje.

5. W **Eksploratorze zapytań** w okienku po prawej stronie rozwiń pozycję **zapisane Queries\Updates** i wybierz zapisane zapytanie wyszukiwania, `MicrosoftDefaultComputerGroup` aby je edytować.

6. W edytorze zapytań przejrzyj zapytanie i Znajdź identyfikator UUID dla maszyny wirtualnej. Usuń identyfikator UUID dla maszyny wirtualnej i powtórz kroki dla innych maszyn wirtualnych, które chcesz usunąć.

7. Zapisz zapisane wyszukiwanie po zakończeniu edycji, wybierając pozycję **Zapisz** na górnym pasku.

>[!NOTE]
>Po wyrejestrowaniu maszyn nadal są one wyświetlane, ponieważ zgłaszamy wszystkie maszyny ocenione w ciągu ostatnich 24 godzin. Po usunięciu komputera należy odczekać 24 godziny, zanim nie będą już wyświetlane.

## <a name="next-steps"></a>Następne kroki

Aby ponownie włączyć tę funkcję, zobacz [włączanie Change Tracking i spisu na podstawie konta usługi Automation](enable-from-automation-account.md), [Włącz Change Tracking i spis, przeglądając Azure Portal](enable-from-portal.md), [włączyć Change Tracking i spis z elementu Runbook](enable-from-runbook.md), lub [włączyć Change Tracking i spis z maszyny wirtualnej platformy Azure](enable-from-vm.md).