---
title: Usuń maszyny wirtualne z Azure Automation Update Management
description: W tym artykule opisano sposób usuwania maszyn zarządzanych przy użyciu Update Management.
services: automation
ms.topic: conceptual
ms.date: 09/09/2020
ms.custom: mvc
ms.openlocfilehash: 66631adbb56a98431e70f956f3e860b16e8f7ea2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89648631"
---
# <a name="remove-vms-from-update-management"></a>Usuwanie maszyn wirtualnych z rozwiązania Update Management

Po zakończeniu zarządzania aktualizacjami na maszynach wirtualnych w środowisku można zatrzymać zarządzanie maszynami wirtualnymi za pomocą funkcji [Update Management](update-mgmt-overview.md) . Aby przestać zarządzać nimi, Edytuj zapisane zapytanie wyszukiwania `MicrosoftDefaultComputerGroup` w obszarze roboczym log Analytics, który jest połączony z kontem usługi Automation.

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

Aby ponownie włączyć zarządzanie maszyną wirtualną, zobacz [włączanie Update Management przez przeglądanie Azure Portal](update-mgmt-enable-portal.md) lub [Włączanie Update Management z poziomu maszyny wirtualnej platformy Azure](update-mgmt-enable-vm.md).