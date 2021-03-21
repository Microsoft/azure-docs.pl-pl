---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: efa9e996cbfbc8954c294b4da8900b1d5bcbeeed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96467385"
---
Aby zweryfikować sieć VPN, można utworzyć konto magazynu, do którego można uzyskać dostęp tylko przez utworzoną sieć wirtualną. Wykonaj następujące kroki, aby utworzyć i skojarzyć to konto magazynu z utworzoną siecią wirtualną:

1. Tworzenie konta magazynu Możesz użyć konta magazynu, które ma być używane z urządzeniem Azure Stack Edge. Spróbuj uzyskać dostęp do konta magazynu z sieci zewnętrznej (poza wybraną siecią). Konto magazynu powinno być dostępne.
2. W Azure Portal przejdź do konta magazynu. 
3. Przejdź do pozycji **zapory i sieci wirtualne**. W prawym okienku dla opcji **Zezwalaj na dostęp z** wybierz opcję **wybrane sieci**. W **bezpiecznym naszym koncie magazynu z sieciami wirtualnymi** wybierz pozycję **+ Dodaj istniejącą sieć wirtualną.**

    ![Weryfikowanie sieci VPN 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-1.png)

4. W bloku **Dodaj sieci** :

    - Wybierz subskrypcję. Jest to ta sama subskrypcja, która jest skojarzona z zasobem Azure Stack Edge/Data Box Gateway. 
    - Z listy rozwijanej wybierz sieć wirtualną, która ma zostać skojarzona z tym kontem magazynu. Wybierz sieć wirtualną utworzoną w poprzednim kroku.
    - W obszarze **podsieci** wybierz **_default_* _ i _GatewaySubnet *. Punkty końcowe usługi zostaną włączone dla tej kombinacji sieci wirtualnej/podsieci. Umożliwienie dostępu trwa do 15 minut.
    - Wybierz pozycję **Włącz**.

    ![Weryfikowanie sieci VPN 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-2.png)
    
4. Zapisz **Ustawienia**.

    ![Weryfikowanie sieci VPN 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-3.png)

5. Po zapisaniu ustawień można zobaczyć podsieci, dla których jest włączona sieć wirtualna.

    ![Weryfikowanie sieci VPN 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-4.png)

5. Aby sprawdzić, czy dane będą teraz przenoszone tylko za pośrednictwem sieci VPN, wykonaj następujące kroki: 
    - Spróbuj uzyskać dostęp do konta magazynu z sieci zewnętrznej (poza wybraną siecią). Konto magazynu nie powinno być dostępne. 
    - Spróbuj uzyskać dostęp do konta magazynu z sieci wirtualnej/podsieci, które zostały włączone w wybranych sieciach. Konto magazynu powinno być dostępne. 
 
Możesz uzyskać dostęp do tego konta magazynu tylko wtedy, gdy jest włączona sieć VPN. W przypadku wyłączenia sieci VPN konieczne będzie również dostosowanie ustawień sieci konta magazynu. 

Aby uzyskać więcej informacji, przejdź do [konfigurowania zapór i sieci wirtualnych usługi Azure Storage](../articles/storage/common/storage-network-security.md). 

