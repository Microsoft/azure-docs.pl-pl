---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 1cf5bbdad555c50c418851904f36a578522843b2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67183293"
---
#### <a name="to-create-public-endpoints-on-the-cloud-appliance"></a>Aby utworzyć publiczne punkty końcowe na urządzeniu w chmurze

1. Zaloguj się do witryny Azure Portal.
2. Kliknij opcję **Maszyny wirtualne**, a następnie wybierz maszynę wirtualną, która jest używana jako urządzenie w chmurze.
    
3. Musisz utworzyć regułę sieciowej grupy zabezpieczeń, aby kontrolować przepływ ruchu do i z maszyny wirtualnej. Wykonaj poniższe kroki, aby utworzyć regułę sieciowej grupy zabezpieczeń.
    1. Wybierz pozycję **Sieciowa grupa zabezpieczeń**.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. Kliknij przedstawioną domyślną sieciową grupę zabezpieczeń.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. W obszarze **Reguły zabezpieczeń dla ruchu przychodzącego**.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. Kliknij pozycję **+ Dodaj** w celu utworzenia reguły zabezpieczeń dla ruchu przychodzącego.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        W bloku Dodawanie reguły zabezpieczeń dla ruchu przychodzącego:

        1. W polu **Nazwa** wpisz następującą nazwę punktu końcowego: WinRMHttps.
        
        2. W polu **Priorytet** wybierz numer mniejszy niż 1000 (czyli priorytet reguły domyślnej). Im większa wartość, tym niższy priorytet.

        3. Ustaw wartość pola **Źródło** na **Dowolne**.

        4. W polu **Usługa** wybierz pozycję **WinRM**. Wartość pola **Protokół** jest automatycznie ustawiana na **TCP**, a wartość pola **Zakres portów** na **5986**.

        5. Kliknij przycisk **OK**, aby utworzyć regułę.

            ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. Ostatni krok to skojarzenie sieciowej grupy zabezpieczeń z podsiecią lub określonym interfejsem sieciowym. Wykonaj poniższe kroki, aby skojarzyć sieciową grupę zabezpieczeń z podsiecią.
    1. Przejdź do pozycji **Podsieci**.
    2. Kliknij pozycję **+ Skojarz**.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. Wybierz sieć wirtualną, a następnie wybierz odpowiednią podsieć.
    4. Kliknij przycisk **OK**, aby utworzyć regułę.

        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

Po utworzeniu reguły można wyświetlić jej szczegóły, aby ustalić publiczny wirtualny adres IP (VIP). Zapisz ten adres.


