---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 48ce594fa5f4b736e69b5b4ef7a10011fe8031fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96026488"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Aby zainstalować aktualizacje za pośrednictwem witryny Azure Portal

1. Przejdź do menedżera urządzeń StorSimple i wybierz pozycję **Urządzenia**. Na liście urządzeń połączonych z usługą wybierz i kliknij urządzenie, które chcesz zaktualizować. 

    ![W obszarze ostatnie Device Manager jest wyróżniona i zaznaczona, urządzenia są wyróżnione i wybierane, a MYSSIS1103 urządzenia jest wyróżnione i wybrane.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. W bloku **Ustawienia** kliknij pozycję **Aktualizacje urządzeń**. 

    ![Informacje dla MYSSIS1103 są wyświetlane. W obszarze Ustawienia są wyróżnione aktualizacje urządzeń.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Zostanie wyświetlony komunikat, jeśli aktualizacje oprogramowania są dostępne. Aby sprawdzić dostępność aktualizacji, możesz także kliknąć pozycję **Skanuj**.

    ![W okienku aktualizacje urządzenia jest wyświetlany komunikat "nowe aktualizacje są dostępne" i "ostatnio skanowane/18/01/2017 2:42 PM/oprogramowanie/10.0.10288.0". Wersja zostanie wyróżniona.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate3m1.png)

    Otrzymasz powiadomienie, gdy skanowanie zostanie uruchomione i zakończy się pomyślnie.

    ![Powiadomienie brzmi "skanowanie aktualizacji dla urządzeń MySSIS1103. 2:12 PM/pomyślnie ukończono operację ".](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate5m.png)

4. Po przeskanowaniu aktualizacji kliknij pozycję **Pobierz aktualizacje**. 

    ![W okienku aktualizacje urządzeń jest wyświetlany komunikat "nowe aktualizacje są dostępne". Przycisk Pobierz aktualizacje zostanie wyróżniony.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate6m.png)

5. W bloku **Nowe aktualizacje** zapoznaj się z informacjami o konieczności potwierdzenia instalacji po pobraniu aktualizacji. Kliknij przycisk **OK**.

    ![W okienku nowe aktualizacje zostanie wyświetlony komunikat "po pobraniu aktualizacji należy potwierdzić instalację". Przycisk OK jest wyróżniony.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate7m.png)

6. Otrzymasz powiadomienie, gdy przekazywanie zostanie uruchomione i zakończy się pomyślnie.

     ![Powiadomienie informuje o "pobieraniu aktualizacji dla urządzenia MySSIS1... 2:13 PM ".](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate8m.png)

5. W bloku **Aktualizacje urządzeń** kliknij pozycję **Instaluj**.

     ![Aktualizacje urządzeń to "Potwierdź zainstalowanie aktualizacji". Istnieje przycisk Instaluj.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate11m1.png)   

6. W bloku **Nowe aktualizacje** zostanie wyświetlone ostrzeżenie, że aktualizacja spowoduje utrudnienia. Ponieważ macierz wirtualna to urządzenie o jednym węźle, urządzenie zostanie ponownie uruchomione po zaktualizowaniu. Spowoduje to zakłócenie wszystkich operacji we/wy. Kliknij przycisk **OK**, aby zainstalować aktualizacje. 

    ![Komunikat w okienku nowe aktualizacje to "urządzenie będzie miało przestoje po zainstalowaniu tych aktualizacji". Przycisk OK jest wyróżniony.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate12m.png) 

7. Otrzymasz powiadomienie o uruchomieniu zadania instalacji. 

    ![Powiadomienie brzmi "Instalowanie aktualizacji dla urządzenia MySSIS1103. 2:15 PM ".](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate13m.png)

8.  Gdy zadanie instalacji zakończy się pomyślnie, kliknij link **Wyświetl zadanie** w bloku **Aktualizacje urządzeń** w celu monitorowania instalacji. 

    ![W okienku aktualizacje urządzeń znajduje się link z etykietą "Instalowanie aktualizacji. Wyświetl zadanie ".](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate15m1.png)

    Spowoduje to przejście do bloku **Instalowanie aktualizacji**. W tym miejscu możesz wyświetlić szczegółowe informacje o zadaniu.

    ![Okienko Instalowanie aktualizacji zawiera informacje dotyczące instalacji, w tym urządzenia, stan, czas rozpoczęcia i czas zakończenia.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate16m1.png)

9. Po pomyślnym zainstalowaniu aktualizacji w bloku **Aktualizacje urządzenia** zostanie wyświetlony odpowiedni komunikat. 
