---
title: Usuń maszyny wirtualne z Azure Automation Update Management
description: W tym artykule opisano sposób usuwania maszyn zarządzanych przy użyciu Update Management.
services: automation
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: d7f7e4aa8b2c192688020b4449c8750f94af29f6
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450443"
---
# <a name="remove-vms-from-update-management"></a>Usuwanie maszyn wirtualnych z rozwiązania Update Management

Po zakończeniu zarządzania aktualizacjami na maszynach wirtualnych w środowisku można zatrzymać zarządzanie maszynami wirtualnymi za pomocą funkcji [Update Management](update-mgmt-overview.md) .

## <a name="sign-into-the-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Aby usunąć maszyny wirtualne

1. Z poziomu konta usługi Automation wybierz pozycję **Update Management** w obszarze **Update Management**.

2. Użyj następującego polecenia, aby zidentyfikować identyfikator UUID komputera, który chcesz usunąć z zarządzania.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. W obszarze roboczym Log Analytics w obszarze **Ogólne**uzyskaj dostęp do zapisanych wyszukiwań dla konfiguracji zakresu `MicrosoftDefaultScopeConfig-Updates` .

4. Dla zapisanego wyszukiwania `MicrosoftDefaultComputerGroup` kliknij wielokropek z prawej strony i wybierz pozycję **Edytuj**.

5. Usuń identyfikator UUID dla maszyny wirtualnej.

6. Powtórz kroki dla innych maszyn wirtualnych do usunięcia.

7. Zapisz zapisane wyszukiwanie po zakończeniu edycji.

>[!NOTE]
>Po wyrejestrowaniu maszyn nadal są one wyświetlane, ponieważ zgłaszamy wszystkie maszyny ocenione w ciągu ostatnich 24 godzin. Po rozłączeniu komputera należy odczekać 24 godziny, zanim nie będą już wyświetlane.

## <a name="next-steps"></a>Następne kroki

Aby ponownie włączyć zarządzanie maszyną wirtualną, zobacz [włączanie Update Management przez przeglądanie Azure Portal](update-mgmt-enable-portal.md) lub [Włączanie Update Management z poziomu maszyny wirtualnej platformy Azure](update-mgmt-enable-vm.md).