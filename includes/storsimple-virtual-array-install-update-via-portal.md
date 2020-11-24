---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: df572b88cf8a67163b28ec87154dcea01646b9a2
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95554004"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Aby zainstalować aktualizacje za pośrednictwem witryny Azure Portal

1. Przejdź do menedżera urządzeń StorSimple i wybierz pozycję **Urządzenia**. Na liście urządzeń połączonych z usługą wybierz i kliknij urządzenie, które chcesz zaktualizować. 

    ![W obszarze ostatnie Menedżer urządzeń jest wyróżniona i zaznaczona, urządzenia są wyróżnione i wybierane, a MYSSIS1103 urządzenia jest wyróżnione i wybrane.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate1m.png) 

2. W bloku **Ustawienia** kliknij pozycję **Aktualizacje urządzeń**. 

    ![Zostanie wyświetlona informacja dla MYSSIS1103. W obszarze Ustawienia są wyróżnione aktualizacje urządzeń.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate2m.png)  

3. Zostanie wyświetlony komunikat, jeśli aktualizacje oprogramowania są dostępne. Aby sprawdzić dostępność aktualizacji, możesz także kliknąć pozycję **Skanuj**.

    ![W okienku aktualizacje urządzeń zostanie wyróżniony przycisk Skanuj. Zostanie wyświetlony komunikat o czterech wierszach: ostatnio skanowany/nie zeskanowany/wersja oprogramowania/10.0.10280.0.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate3m.png)

    Otrzymasz powiadomienie, gdy skanowanie zostanie uruchomione i zakończy się pomyślnie.

    ![Powiadomienie brzmi "skanowanie aktualizacji dla urządzenia 1103.2:12 PM/pomyślnie ukończono operację".](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate5m.png)

4. Po przeskanowaniu aktualizacji kliknij pozycję **Pobierz aktualizacje**. 

    ![W okienku aktualizacje urządzenia jest wyświetlany komunikat "nowe aktualizacje są dostępne" i "ostatnio skanowane/11/3/2016 2:12 PM/oprogramowanie/10.0.10280.0". Wersja zostanie wyróżniona.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate6m.png)

5. W bloku **Nowe aktualizacje** zapoznaj się z informacjami o konieczności potwierdzenia instalacji po pobraniu aktualizacji. Kliknij przycisk **OK**.

    ![W okienku nowe aktualizacje zostanie wyświetlony komunikat "po pobraniu aktualizacji należy potwierdzić instalację". Przycisk OK jest wyróżniony.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate7m.png)

6. Otrzymasz powiadomienie, gdy przekazywanie zostanie uruchomione i zakończy się pomyślnie.

     ![Powiadomienie informuje o "pobieraniu aktualizacji dla urządzenia"... 2:13 PM ".](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate8m.png)

5. W bloku **Aktualizacje urządzeń** kliknij pozycję **Instaluj**.

     ![W okienku aktualizacje urządzeń zostanie wyświetlony komunikat "Potwierdzanie instalacji aktualizacji". Przycisk Instaluj jest wyróżniony.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate11m.png)   

6. W bloku **Nowe aktualizacje** zostanie wyświetlone ostrzeżenie, że aktualizacja spowoduje utrudnienia. Ponieważ macierz wirtualna to urządzenie o jednym węźle, urządzenie zostanie ponownie uruchomione po zaktualizowaniu. Spowoduje to zakłócenie wszystkich operacji we/wy. Kliknij przycisk **OK**, aby zainstalować aktualizacje. 

    ![Komunikat w okienku nowe aktualizacje to "urządzenie będzie miało przestoje po zainstalowaniu tych aktualizacji". Przycisk OK jest wyróżniony.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate12m.png) 

7. Otrzymasz powiadomienie o uruchomieniu zadania instalacji. 

    ![Powiadomienie brzmi "Instalowanie aktualizacji dla urządzenia MYSSIS1103". 2:15 PM.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate13m.png)

8.  Gdy zadanie instalacji zakończy się pomyślnie, kliknij link **Wyświetl zadanie** w bloku **Aktualizacje urządzeń** w celu monitorowania instalacji. 

    ![W okienku aktualizacje urządzeń zostanie oznaczone łącze "Instalowanie aktualizacji. Widok zadania jest wyróżniony.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate15m.png)

    Spowoduje to przejście do bloku **Instalowanie aktualizacji**. W tym miejscu możesz wyświetlić szczegółowe informacje o zadaniu.

    ![Okienko Instalowanie aktualizacji zawiera informacje dotyczące instalacji, w tym urządzenia, stan, czas rozpoczęcia i czas zakończenia.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate16m.png)

9. Po pomyślnym zainstalowaniu aktualizacji w bloku Aktualizacje urządzenia zostanie wyświetlony odpowiedni komunikat. Wersja oprogramowania zmieni się również na **10.0.10288.0**. 

    ![W okienku aktualizacje urządzeń zostanie wyświetlona wartość "Twoje urządzenie jest aktualne" i jest dostępna wersja oprogramowania (10.0.10288.0).](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate17m.png)
