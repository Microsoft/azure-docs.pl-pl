---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: d4e8d99cd7c67136f359772664eb017c6207e6e4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67183174"
---
### <a name="create-a-tcp-endpoint-for-the-virtual-machine"></a>Utwórz punkt końcowy TCP dla maszyny wirtualnej
Aby można było uzyskać dostęp do SQL Server z Internetu, maszyna wirtualna musi mieć punkt końcowy do nasłuchiwania komunikacji przychodzącej TCP. Ten krok konfiguracji platformy Azure powoduje kierowanie przychodzącego ruchu portów TCP do portu TCP, który jest dostępny dla maszyny wirtualnej.

> [!NOTE]
> Jeśli łączysz się w ramach tej samej usługi w chmurze lub sieci wirtualnej, nie musisz tworzyć dostępnego publicznie punktu końcowego. W takim przypadku można przejść do następnego kroku. Aby uzyskać więcej informacji, zobacz [scenariusze połączeń](../articles/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect.md#connection-scenarios).
> 
> 

1. W witrynie Azure Portal wybierz pozycję **maszyny wirtualne (klasyczne)**.
2. Następnie wybierz SQL Server maszynę wirtualną.
3. Wybierz pozycję **punkty końcowe**, a następnie kliknij przycisk **Dodaj** w górnej części bloku punkty końcowe.
   
    ![Kroki portalu dotyczące tworzenia punktów końcowych](./media/virtual-machines-sql-server-connection-steps/portal-endpoint-creation.png)
4. W bloku **Dodaj punkt końcowy** Podaj **nazwę** , taką jak sqlendpoint.
5. Wybierz wartość **TCP** w polu **Protokół**.
6. W obszarze **port publiczny**Określ numer portu, na przykład **57500**.
7. W przypadku **portu prywatnego**określ port nasłuchiwania SQL Server, który ma wartość domyślną **1433**.
8. Kliknij przycisk **OK** , aby utworzyć punkt końcowy.

