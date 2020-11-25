---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 3129bbe171329ecf37f8712394cedf9b70188220
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "96005824"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Aby zainstalować aktualizacje za pośrednictwem witryny Azure Portal

1. Przejdź do menedżera urządzeń StorSimple i wybierz pozycję **Urządzenia**. Na liście urządzeń połączonych z usługą wybierz i kliknij urządzenie, które chcesz zaktualizować.

    ![W obszarze ostatnie Menedżer urządzeń jest wyróżniona i zaznaczona, urządzenia są wyróżnione i wybierane, a MYSSIS1103 urządzenia jest wyróżnione i wybrane.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. W bloku **Ustawienia** kliknij pozycję **Aktualizacje urządzeń**.

    ![Informacje dla MYSSIS1103 są wyświetlane. W obszarze Ustawienia są wyróżnione aktualizacje urządzeń.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Zostanie wyświetlony komunikat, jeśli aktualizacje oprogramowania są dostępne. Aby sprawdzić dostępność aktualizacji, możesz także kliknąć pozycję **Skanuj**. Zanotuj uruchomioną wersję oprogramowania. 

    ![Okienko aktualizacje urządzenia ma wartość "nowe aktualizacje są dostępne" oraz "ostatnio przeskanowane/11/01/2017 10:56 AM/oprogramowanie/10.0.10289.0". Wersja zostanie wyróżniona.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    Otrzymasz powiadomienie, gdy skanowanie zostanie uruchomione i zakończy się pomyślnie.

    ![Powiadomienie brzmi "skanowanie aktualizacji dla urządzenia MySVAFS110.... 10:57 AM/operacja jest w toku. "](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. Po przeskanowaniu aktualizacji kliknij pozycję **Pobierz aktualizacje**.

    ![W okienku aktualizacje urządzeń jest wyświetlany komunikat "nowe aktualizacje są dostępne". Przycisk Pobierz aktualizacje zostanie wyróżniony.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. W bloku **nowe aktualizacje** zapoznaj się z informacjami o wersji. Należy również pamiętać, że po pobraniu aktualizacji musisz potwierdzić instalację. Kliknij przycisk **OK**.

    ![W okienku nowe aktualizacje zostanie wyświetlony komunikat "po pobraniu aktualizacji należy potwierdzić instalację". Przycisk OK jest wyróżniony.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate7m.png)

6. Otrzymasz powiadomienie, gdy przekazywanie zostanie uruchomione i zakończy się pomyślnie.

     ![Powiadomienie informuje o "pobieraniu aktualizacji dla urządzenia MySVAFS... 11:07 AM ".](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate8m.png)

5. W bloku **Aktualizacje urządzeń** kliknij pozycję **Instaluj**.

     ![Aktualizacje urządzeń to "Potwierdź zainstalowanie aktualizacji". Przycisk Instaluj jest wyróżniony.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate11m1.png)

6. W bloku **Nowe aktualizacje** zostanie wyświetlone ostrzeżenie, że aktualizacja spowoduje utrudnienia. Ponieważ macierz wirtualna to urządzenie o jednym węźle, urządzenie zostanie ponownie uruchomione po zaktualizowaniu. Spowoduje to zakłócenie wszystkich operacji we/wy. Kliknij przycisk **OK**, aby zainstalować aktualizacje.

    ![Komunikat w okienku nowe aktualizacje to "urządzenie będzie miało przestoje po zainstalowaniu tych aktualizacji". Przycisk OK jest wyróżniony.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate12m.png)

7. Otrzymasz powiadomienie o uruchomieniu zadania instalacji.

    ![Powiadomienie brzmi "Instalowanie aktualizacji dla urządzenia MySVAFS110.... 11:09 AM ".](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate13m.png)

8.  Gdy zadanie instalacji zakończy się pomyślnie, kliknij link **Wyświetl zadanie** w bloku **Aktualizacje urządzeń** w celu monitorowania instalacji. 

    ![W okienku aktualizacje urządzeń znajduje się link z etykietą "Instalowanie aktualizacji. Wyświetl zadanie ". Przycisk Instaluj jest wyróżniony.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate15m1.png)

    Ta akcja spowoduje przejście do bloku **Instalowanie aktualizacji** . W tym miejscu możesz wyświetlić szczegółowe informacje o zadaniu.

    ![Okienko Instalowanie aktualizacji zawiera informacje dotyczące instalacji, w tym urządzenia, stan, czas rozpoczęcia i czas zakończenia.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. Po rozpoczęciu pracy z macierzą wirtualną z uruchomioną aktualizacją oprogramowania 0,6 (10.0.10293.0) jest teraz uruchomiona aktualizacja 1 i gotowe. Możesz pominąć pozostałe kroki. Po rozpoczęciu pracy z macierzą wirtualną z uruchomioną wersją oprogramowania przed aktualizacją 0,6 (10.0.10293.0) użytkownik jest teraz aktualizowany do aktualizacji 0,6. Zobaczysz kolejny komunikat wskazujący, że aktualizacje są dostępne. Powtórz kroki 4-8, aby zainstalować aktualizację Update 1.

    ![Okienko aktualizacje urządzenia ma wartość "nowe aktualizacje są dostępne" oraz "ostatnio przeskanowane/11/1/2017 10:56 AM/oprogramowanie/10.0.10293.0".](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)

