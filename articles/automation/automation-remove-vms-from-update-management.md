---
title: Usuń maszyny wirtualne z Azure Automation Update Management
description: W tym artykule opisano sposób usuwania maszyn wirtualnych z Update Management.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 26a38c0851643fbd6446acddd99a05abf8d5b26c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836655"
---
# <a name="remove-vms-from-update-management"></a>Usuwanie maszyn wirtualnych z rozwiązania Update Management

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

* Aby kontynuować pracę z Update Management, zobacz [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](automation-tutorial-update-management.md).
* Aby rozwiązać ogólne problemy z funkcjami, zobacz [Rozwiązywanie problemów Update Management](troubleshoot/update-management.md).
* Problemy związane z usługą Windows Update Agent można znaleźć w temacie [Rozwiązywanie problemów z usługą Windows Update Agent](troubleshoot/update-agent-issues.md).
* Problemy z agentem aktualizacji systemu Linux można znaleźć w temacie [Rozwiązywanie problemów z usługą Linux Update Agent](troubleshoot/update-agent-issues-linux.md).