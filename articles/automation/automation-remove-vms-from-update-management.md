---
title: Usuń maszyny wirtualne z Azure Automation Update Management
description: W tym artykule opisano sposób usuwania maszyn wirtualnych z Update Management.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 29d6edd5463de78bba039e4ed6219575d924ac10
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749144"
---
# <a name="remove-vms-from-update-management"></a>Usuń maszyny wirtualne z Update Management

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

Po zakończeniu wdrażania aktualizacji na maszynach wirtualnych w środowisku programu można je usunąć z funkcji [Update Management](automation-update-management.md) .

1. Z poziomu konta usługi Automation wybierz pozycję **Update Management** w obszarze **Update Management**.

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

* [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](automation-tutorial-update-management.md)
* [Odłączanie obszaru roboczego od konta usługi Automation dla Update Management](automation-unlink-workspace-update-management.md)
* [Włączanie Update Management na podstawie konta usługi Automation](automation-onboard-solutions-from-automation-account.md)
* [Włącz Update Management z Azure Portal](automation-onboard-solutions-from-browse.md)
* [Włączanie Update Management z elementu Runbook](automation-onboard-solutions.md)
* [Włączanie Update Management z maszyny wirtualnej platformy Azure](automation-onboard-solutions-from-vm.md)
* [Rozwiązywanie problemów dotyczących Update Management](troubleshoot/update-management.md)
* [Rozwiązywanie problemów z usługą Windows Update Agent](troubleshoot/update-agent-issues.md)
* [Rozwiązywanie problemów z usługą Linux Update Agent](troubleshoot/update-agent-issues-linux.md)
