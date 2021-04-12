---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: 6e42fef2aa4415373ed0bac39284f36ba330a4d9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "101730651"
---
Połącz się z maszyną wirtualną z systemem Windows przy użyciu Remote Desktop Protocol (RDP) za pośrednictwem adresu IP, który został przesłany podczas tworzenia maszyny wirtualnej.

1. Na kliencie Otwórz program RDP. 
1. Przejdź do **menu Start**, a następnie wpisz polecenie **Mstsc**.
1. W okienku **Podłączanie pulpitu zdalnego** wprowadź adres IP maszyny wirtualnej i poświadczenia dostępu użyte w pliku parametrów szablonu maszyny wirtualnej, a następnie wybierz pozycję **Połącz**.

   ![Zrzut ekranu przedstawiający okienko Podłączanie pulpitu zdalnego do nawiązywania połączenia za pośrednictwem protokołu RDP z maszyną wirtualną z systemem Windows.](media/azure-stack-edge-gateway-connect-vm-windows/connect-vm-rdp-1.png)

   > [!NOTE]
   > Może być konieczne zatwierdzenie połączenia z niezaufanym komputerem. 

Użytkownik jest zalogowany do maszyny wirtualnej, która jest uruchamiana na urządzeniu. 
