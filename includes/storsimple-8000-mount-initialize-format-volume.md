---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 23d0f16e025727f8d733f973a2c751ba7114fa49
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95556521"
---
#### <a name="to-mount-initialize-and-format-a-volume"></a>Aby zainstalować, zainicjować i sformatować wolumin
1. Uruchom inicjatora iSCSI firmy Microsoft.
2. W oknie **Właściwości inicjatora iSCSI** na karcie **Odnajdowanie** kliknij pozycję **Odnajdź portal**.
3. W oknie dialogowym **Odnajdowanie portalu obiektu docelowego** podaj adres IP interfejsu sieci iSCSI, a następnie kliknij przycisk **OK**. 
4. W oknie **Właściwości inicjatora iSCSI** na karcie **Obiekty docelowe** zlokalizuj wartości **Wykryte obiekty docelowe**. Stan urządzenia powinien zostać wyświetlony jako **Nieaktywne**.
5. Wybierz urządzenie docelowe, a następnie kliknij pozycję **Połącz**. Po połączeniu urządzenia stan powinien zmienić się na **Połączone**. (Aby uzyskać więcej informacji o korzystaniu z inicjatora iSCSI firmy Microsoft, zobacz artykuł [Installing and Configuring Microsoft iSCSI Initiator][1] [Instalowanie i konfigurowanie inicjatora iSCSI firmy Microsoft]).
6. Na hoście systemu Windows naciśnij klawisz z logo systemu Windows + X, a następnie kliknij pozycję **Uruchom**. 
7. W oknie dialogowym **Uruchom** wpisz **Diskmgmt.msc**. Kliknij przycisk **OK**. Zostanie wyświetlone okno **Zarządzanie dyskami**. W prawym okienku zostaną wyświetlone woluminy na hoście.
8. W oknie **Zarządzanie dyskami** zostaną wyświetlone zainstalowane woluminy (tak jak na poniższym rysunku). Kliknij prawym przyciskiem myszy odnaleziony wolumin (kliknij nazwę dysku), a następnie kliknij pozycję **Online**.
   
     ![Inicjowanie i formatowanie woluminu](./media/storsimple-8000-mount-initialize-format-volume/step7initializeformatvolume.png) 
9. Kliknij ponownie prawym przyciskiem myszy wolumin (kliknij nazwę dysku), a następnie kliknij pozycję **Inicjuj**.
10. Aby sformatować prosty wolumin, wykonaj następujące czynności:
    
    1. Wybierz wolumin, kliknij go prawym przyciskiem myszy (kliknij prawy obszar), a następnie kliknij pozycję **Nowy wolumin prosty**.
    2. W Kreatorze nowego woluminu prostego określ literę dysku i rozmiar woluminu, a następnie skonfiguruj wolumin w systemie plików NTFS.
    3. Określ rozmiar jednostki alokacji 64 KB. Ten rozmiar jednostki alokacji dobrze działa z algorytmami deduplikacji używanymi w rozwiązaniu StorSimple.
    4. Przeprowadź szybkie formatowanie.

<!--Link references-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee338480(v=ws.10)