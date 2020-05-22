---
title: Usuwanie maszyn wirtualnych z Azure Automation Change Tracking i spisu
description: W tym artykule opisano sposób usuwania maszyn wirtualnych z Change Tracking i spisu.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 1bd94beaa40f6ff793b50e261138cc31453f7016
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749165"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Usuwanie maszyn wirtualnych z Change Tracking i spisu

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

Po zakończeniu wdrażania zmian w maszynach wirtualnych w środowisku można je usunąć z funkcji [Change Tracking i spisu](change-tracking.md) .

1. Z poziomu konta usługi Automation wybierz pozycję **śledzenie zmian** lub **spis** w obszarze **Zarządzanie konfiguracją**.

2. Użyj następującego polecenia, aby zidentyfikować identyfikator UUID maszyny wirtualnej, która ma zostać usunięta z zarządzania.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. W obszarze roboczym Log Analytics w obszarze **Ogólne**uzyskaj dostęp do zapisanych wyszukiwań.

4. Dla zapisanego wyszukiwania `MicrosoftDefaultComputerGroup` kliknij wielokropek z prawej strony i wybierz pozycję **Edytuj**. 

5. Usuń identyfikator UUID dla maszyny wirtualnej.

6. Powtórz kroki dla innych maszyn wirtualnych do usunięcia.

7. Zapisz zapisane wyszukiwanie po zakończeniu edycji. 

## <a name="next-steps"></a>Następne kroki

* [Przegląd Change Tracking i spisu](change-tracking.md)
* [Zarządzanie Change Tracking i spisem](change-tracking-file-contents.md)
* [Odłączanie obszaru roboczego od konta usługi Automation dla Change Tracking i spisu](automation-unlink-workspace-change-tracking.md)
* [Włączanie Change Tracking i spisu na podstawie konta usługi Automation](automation-enable-changes-from-auto-acct.md)
* [Włącz Change Tracking i spis z Azure Portal](automation-enable-changes-from-browse.md)
* [Włączanie Change Tracking i spisu z elementu Runbook](automation-enable-changes-from-runbook.md)
* [Włączanie Change Tracking i spisu na podstawie maszyny wirtualnej platformy Azure](automation-enable-changes-from-vm.md)
* [Rozwiązywanie problemów dotyczących zmian na maszynie wirtualnej platformy Azure](automation-tutorial-troubleshoot-changes.md)
* [Rozwiązywanie problemów dotyczących Change Tracking i spisu](troubleshoot/change-tracking.md)