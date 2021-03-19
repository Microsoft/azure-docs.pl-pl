---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a5a286753e438b7d65f3d33a82669c4f7e79a282
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86544843"
---
#### <a name="to-create-public-endpoints-on-the-cloud-appliance"></a>Aby utworzyć publiczne punkty końcowe na urządzeniu w chmurze

1. Zaloguj się w witrynie Azure Portal.
2. Kliknij opcję **Maszyny wirtualne**, a następnie wybierz maszynę wirtualną, która jest używana jako urządzenie w chmurze.
    
3. Musisz utworzyć regułę sieciowej grupy zabezpieczeń, aby kontrolować przepływ ruchu do i z maszyny wirtualnej. Wykonaj poniższe kroki, aby utworzyć regułę sieciowej grupy zabezpieczeń.
    1. Wybierz pozycję **Sieciowa grupa zabezpieczeń**.
        ![Zrzut ekranu przedstawiający stronę maszyny wirtualnej. W sekcji Ustawienia zostanie wyróżniona Grupa zabezpieczeń sieci.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. Kliknij przedstawioną domyślną sieciową grupę zabezpieczeń.
        ![Zrzut ekranu przedstawiający stronę Network Security Group. Zostanie wyróżniona domyślna grupa zabezpieczeń sieci.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. Wybierz pozycję **Reguły zabezpieczeń dla ruchu przychodzącego**.
        ![Zrzut ekranu przedstawiający stronę z właściwościami domyślnej sieciowej grupy zabezpieczeń. W okienku nawigacji są wyróżnione reguły zabezpieczeń dla ruchu przychodzącego.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. Kliknij pozycję **+ Dodaj** w celu utworzenia reguły zabezpieczeń dla ruchu przychodzącego.
        ![Zrzut ekranu przedstawiający stronę reguły zabezpieczeń dla ruchu przychodzącego. Znak plus i Dodaj wyraz są obok siebie i są wyróżnione.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        W bloku Dodawanie reguły zabezpieczeń dla ruchu przychodzącego:

        1. W polu **Nazwa** wpisz następującą nazwę punktu końcowego: WinRMHttps.
        
        2. W polu **Priorytet** wybierz numer mniejszy niż 1000 (czyli priorytet reguły domyślnej). Im większa wartość, tym niższy priorytet.

        3. Ustaw wartość pola **Źródło** na **Dowolne**.

        4. W polu **Usługa** wybierz pozycję **WinRM**. Wartość pola **Protokół** jest automatycznie ustawiana na **TCP**, a wartość pola **Zakres portów** na **5986**.

        5. Kliknij przycisk **OK**, aby utworzyć regułę.

            ![Zrzut ekranu przedstawiający blok Dodawanie reguły zabezpieczeń dla ruchu przychodzącego. Wartości są wypełniane zgodnie z opisem w procedurze, a przycisk OK jest wyróżniony.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. Ostatni krok to skojarzenie sieciowej grupy zabezpieczeń z podsiecią lub określonym interfejsem sieciowym. Wykonaj poniższe kroki, aby skojarzyć sieciową grupę zabezpieczeń z podsiecią.
    1. Przejdź do pozycji **Podsieci**.
    2. Kliknij pozycję **+ Skojarz**.
        ![Zrzut ekranu przedstawiający stronę podsieci. Znak plus i słowo skojarzone są obok siebie i są wyróżnione.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. Wybierz sieć wirtualną, a następnie wybierz odpowiednią podsieć.
    4. Kliknij przycisk **OK**, aby utworzyć regułę.

        ![Zrzut ekranu przedstawiający stronę Skojarz podsieć. Wybrana jest sieć wirtualna, a przycisk OK jest wyróżniony.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

Po utworzeniu reguły można wyświetlić jej szczegóły, aby ustalić publiczny wirtualny adres IP (VIP). Zapisz ten adres.


