---
title: Usuń maszyny wirtualne z Azure Automation Update Management
description: W tym artykule opisano sposób usuwania maszyn wirtualnych z Update Management.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 796cf18ae4dbab50eb7f968bda065ae0351f2ae8
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84169410"
---
# <a name="remove-vms-from-update-management"></a>Usuwanie maszyn wirtualnych z rozwiązania Update Management

Po zakończeniu wdrażania aktualizacji na maszynach wirtualnych w środowisku programu można je usunąć z funkcji [Update Management](automation-update-management.md) .

## <a name="to-remove-your-vms"></a>Aby usunąć maszyny wirtualne

1. Z poziomu konta usługi Automation wybierz pozycję **Update Management** w obszarze **Update Management**.

2. Użyj następującego polecenia, aby zidentyfikować identyfikator UUID maszyny wirtualnej, która ma zostać usunięta z zarządzania.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. W obszarze roboczym Log Analytics w obszarze **Ogólne**uzyskaj dostęp do zapisanych wyszukiwań dla konfiguracji zakresu `MicrosoftDefaultScopeConfig-Updates` .

4. Dla zapisanego wyszukiwania `MicrosoftDefaultComputerGroup` kliknij wielokropek z prawej strony i wybierz pozycję **Edytuj**. 

5. Usuń identyfikator UUID dla maszyny wirtualnej.

6. Powtórz kroki dla innych maszyn wirtualnych do usunięcia.

7. Zapisz zapisane wyszukiwanie po zakończeniu edycji. 

## <a name="next-steps"></a>Następne kroki

* Aby kontynuować pracę z Update Management, zobacz [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](automation-tutorial-update-management.md).
* Aby rozwiązać ogólne problemy z funkcjami, zobacz [Rozwiązywanie problemów Update Management](troubleshoot/update-management.md).
* Problemy związane z usługą Windows Update Agent można znaleźć w temacie [Rozwiązywanie problemów z usługą Windows Update Agent](troubleshoot/update-agent-issues.md).
* Problemy z agentem aktualizacji systemu Linux można znaleźć w temacie [Rozwiązywanie problemów z usługą Linux Update Agent](troubleshoot/update-agent-issues-linux.md).