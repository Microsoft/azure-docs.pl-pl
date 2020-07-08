---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 394b242ab46da7821f77e8d008836753f4e358e2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67183180"
---
W tym kroku ręcznie utworzysz odbiornik grupy dostępności w Menedżer klastra trybu failover i SQL Server Management Studio.

1. Otwórz Menedżer klastra trybu failover z węzła, który hostuje replikę podstawową.

2. Wybierz węzeł **sieci** , a następnie zanotuj nazwę sieci klastra. Ta nazwa jest używana w zmiennej $ClusterNetworkName w skrypcie programu PowerShell.

3. Rozwiń węzeł Nazwa klastra, a następnie kliknij pozycję **role**.

4. W okienku **role** kliknij prawym przyciskiem myszy nazwę grupy dostępności, a następnie wybierz pozycję **Dodaj zasób**  >  **Klient punkt dostępu**.
   
    ![Dodawanie punktu dostępu klienta dla grupy dostępności](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. W polu **Nazwa** Utwórz nazwę dla nowego odbiornika, kliknij przycisk **dalej** dwa razy, a następnie kliknij przycisk **Zakończ**.  
    W tym momencie nie należy przenosić odbiornika ani zasobu do trybu online.

6. Kliknij kartę **zasoby** , a następnie rozwiń właśnie utworzony punkt dostępu klienta. 
    Zostanie wyświetlony zasób adres IP dla każdej sieci klastra w klastrze. Jeśli jest to rozwiązanie wyłącznie platformy Azure, zostanie wyświetlony tylko jeden zasób adresu IP.

7. Wykonaj jedną z następujących czynności:
   
   * Aby skonfigurować rozwiązanie hybrydowe:
     
        a. Kliknij prawym przyciskiem myszy zasób adresu IP, który odpowiada lokalnej podsieci, a następnie wybierz polecenie **Właściwości**. Zanotuj nazwę adresu IP i nazwę sieci.
   
        b. Wybierz pozycję **statyczny adres IP**, przypisz nieużywany adres IP, a następnie kliknij przycisk **OK**.
 
   * Aby skonfigurować rozwiązanie tylko do platformy Azure:

        a. Kliknij prawym przyciskiem myszy zasób adresu IP odpowiadający podsieci platformy Azure, a następnie wybierz polecenie **Właściwości**.
       
       > [!NOTE]
       > Jeśli odbiornik później nie przejdzie do trybu online z powodu konfliktu adresów IP wybranego przez usługę DHCP, można skonfigurować prawidłowy statyczny adres IP w tym oknie właściwości.
       > 
       > 

       b. W tym samym oknie właściwości **adresu IP** Zmień **nazwę adresu IP**.  
        Ta nazwa jest używana w zmiennej $IPResourceName skryptu programu PowerShell. Jeśli rozwiązanie obejmuje wiele sieci wirtualnych platformy Azure, Powtórz ten krok dla każdego zasobu IP.

