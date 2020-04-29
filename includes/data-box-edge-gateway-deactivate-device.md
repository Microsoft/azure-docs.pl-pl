---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79129377"
---
Aby zresetować urządzenie, należy bezpiecznie wyczyścić wszystkie dane na dysku z danymi i dysku rozruchowego urządzenia. 

Użyj polecenia `Reset-HcsAppliance` cmdlet, aby wyczyścić zarówno dyski danych, jak i dysk rozruchowy, albo tylko dyski danych. Przełączniki `ClearData` i `BootDisk` umożliwiają odpowiednio czyszczenie dysków danych i dysku rozruchowego.

`BootDisk` Przełącznik powoduje wymazanie dysku rozruchowego i uniemożliwić korzystanie z tego urządzenia. Powinno być używane tylko wtedy, gdy urządzenie musi zostać zwrócone do firmy Microsoft. Aby uzyskać więcej informacji, zobacz [zwracają urządzenie do firmy Microsoft](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device).

Jeśli używasz resetowania urządzenia w lokalnym interfejsie użytkownika sieci Web, tylko dyski danych są bezpiecznie czyszczone, ale dysk rozruchowy jest nienaruszony. Dysk rozruchowy zawiera konfigurację urządzenia.

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
