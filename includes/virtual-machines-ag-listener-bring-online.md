---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 760bb5b62e9bba9b7a83f99760f7fe5d8c399dfb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67183182"
---
1. W Menedżer klastra trybu failover rozwiń węzeł **role**, a następnie wybierz grupę dostępności.  

2. Na karcie **zasoby** kliknij prawym przyciskiem myszy nazwę odbiornika, a następnie kliknij polecenie **Właściwości**.

3. Kliknij kartę **zależności** . Jeśli na liście znajduje się wiele zasobów, sprawdź, czy adresy IP mają zależności lub, nie i.  

4. Kliknij przycisk **OK**.

5. Kliknij prawym przyciskiem myszy nazwę odbiornika, a następnie kliknij polecenie **Przełącz do trybu online**.

6. Gdy odbiornik jest w trybie online, na karcie **zasoby** kliknij prawym przyciskiem myszy grupę dostępności, a następnie kliknij polecenie **Właściwości**.
   
    ![Konfigurowanie zasobu grupy dostępności](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. Utwórz zależność od zasobu nazwy odbiornika (nie nazwy zasobów adresów IP), a następnie kliknij przycisk **OK**.
   
    ![Dodaj zależność od nazwy odbiornika](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. Rozpocznij SQL Server Management Studio, a następnie połącz się z repliką podstawową.

9. Przejdź do **AlwaysOn High Availability**obszaru  >  **Availability Groups**  >  **\<AvailabilityGroupName\>**  >  **odbiorniki**grup dostępności funkcji AlwaysOn o wysokiej dostępności.  
    Powinna zostać wyświetlona nazwa odbiornika utworzona w Menedżer klastra trybu failover.

10. Kliknij prawym przyciskiem myszy nazwę odbiornika, a następnie kliknij polecenie **Właściwości**.

11. W polu **port** Określ numer portu dla odbiornika grupy dostępności przy użyciu użytej wcześniej $EndpointPort (w tym samouczku 1433 jest to ustawienie domyślne), a następnie kliknij przycisk **OK**.

