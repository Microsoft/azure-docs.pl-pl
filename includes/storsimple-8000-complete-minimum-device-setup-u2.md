---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 867cdc97ff91d5932230b733dee4d7660d499c39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96025482"
---
#### <a name="to-complete-the-minimum-storsimple-device-setup"></a>Wykonywanie minimalnej konfiguracji urządzenia StorSimple

   > [!NOTE]
   > Nie można zmienić nazwy urządzenia po zakończeniu minimalnej konfiguracji urządzenia.
   
1. Z tabelarycznej listy urządzeń w bloku **Urządzenia** wybierz i kliknij swoje urządzenie. Urządzenie jest w stanie **Gotowe do skonfigurowania**. Zostanie otwarty blok **Konfigurowanie urządzenia**.

     ![Interfejsy sieciowe minimalnej konfiguracji urządzenia StorSimple](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig1.png)

2. W bloku **Konfigurowanie urządzenia**:
   
   1. Określ **przyjazną nazwę** dla urządzenia. Domyślna nazwa urządzenia odzwierciedla takie informacje, jak model urządzenia i numer seryjny. Do celów zarządzania urządzeniem można przypisać przyjazną nazwę o długości maksymalnie 64 znaków.
   2. Ustaw **strefę czasową** na podstawie lokalizacji geograficznej, w której jest wdrażane urządzenie. Wszystkie zaplanowane operacje urządzenia są wykonywane w ramach tej strefy czasowej.
   3. W obszarze **ustawień interfejsu DATA 0**:

       1. Interfejs sieciowy DATA 0 jest pokazywany jako włączony z ustawieniami sieciowymi (adres IP, podsieć, brama) skonfigurowanymi za pośrednictwem kreatora konfiguracji. Interfejs DATA 0 jest także automatycznie włączony dla chmury i interfejsu iSCSI.

       2. Podaj stałe adresy IP dla kontrolera 0 i 1. **Stałe adresy IP kontrolera muszą być wolnymi adresami IP w obrębie podsieci dostępnej za pośrednictwem adresu IP urządzenia.** Jeśli interfejs DATA 0 został skonfigurowany do obsługi protokołu IPv4, stałe adresy IP muszą być podawane w formacie IPv4. Jeśli podano prefiks podczas konfiguracji protokołu IPv6 , stałe adresy IP są wypełniane automatycznie w tych polach.

            ![StorSimple minimalna konfiguracja urządzenia interfejsy sieciowe 2](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig2.png)

            Stałe adresy IP kontrolera są używane do obsługi aktualizacji urządzenia i odzyskiwania pamięci. W związku z tym stałe adresy IP muszą być routowalne i mieć możliwość łączenia z Internetem. Możesz sprawdzić, czy stałe adresy IP kontrolera są routowalne, używając polecenia cmdlet [Test-HcsmConnection][Test]. W poniższym przykładzie przedstawiono stałe adresy IP kontrolera, które są kierowane do Internetu i mogą uzyskać dostęp do serwerów usługi Microsoft Update.

            ![Polecenie Test-HcsmConnection pokazujące rutowalne adresy IP](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig3.png)

1. Kliknij przycisk **OK**. Rozpocznie się konfigurowanie urządzenia. Po zakończeniu konfiguracji urządzenia zostanie wyświetlone powiadomienie. Stan urządzenia w bloku **Urządzenia** zmieni się na **Online**.

    ![StorSimple minimalna konfiguracja urządzenia interfejsy sieciowe 3](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig4.png)

<!--Link reference-->
[Test]: /previous-versions/windows/powershell-scripting/dn715782(v=wps.630)