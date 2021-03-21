---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: c1b56cfb85595b8a17dc18f69a0b162d504c04ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96025955"
---
Aby zresetować urządzenie, należy bezpiecznie wyczyścić wszystkie dane na dysku danych i dysku rozruchowym urządzenia. 

Użyj `Reset-HcsAppliance` polecenia cmdlet, aby wyczyścić zarówno dyski danych, jak i dysk rozruchowy, albo tylko dyski danych. `ClearData`Przełączniki i `BootDisk` umożliwiają odpowiednio czyszczenie dysków danych i dysku rozruchowego.

`BootDisk`Przełącznik powoduje wymazanie dysku rozruchowego i uniemożliwić korzystanie z tego urządzenia. Należy go używać tylko wtedy, gdy urządzenie ma zostać zwrócone firmie Microsoft. Aby uzyskać więcej informacji, zobacz [zwracają urządzenie do firmy Microsoft](../articles/databox-online/azure-stack-edge-return-device.md).

Jeśli użyjesz funkcji resetowania urządzenia w lokalnym internetowym interfejsie użytkownika, tylko dyski danych zostaną bezpiecznie wyczyszczone, a dysk rozruchowy pozostanie nienaruszony. Dysk rozruchowy zawiera konfigurację urządzenia.

1. [Nawiąż połączenie z interfejsem programu PowerShell](#connect-to-the-powershell-interface).
2. W wierszu polecenia wpisz polecenie:

    `Reset-HcsAppliance -ClearData -BootDisk`

    Poniższy przykład pokazuje, jak używać tego polecenia cmdlet:

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```