---
title: plik dołączany
description: plik dołączany
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/18/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 65d5a88f7b5d059deb633f062639e455c64ef2f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183755"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Aby zainstalować aktualizacje za pośrednictwem witryny Azure Portal

1. Przejdź do menedżera urządzeń StorSimple i wybierz pozycję **Urządzenia**. Na liście urządzeń połączonych z usługą wybierz i kliknij urządzenie, które chcesz zaktualizować.

2. W obszarze **Ustawienia**kliknij pozycję **aktualizacje urządzeń**.  

3. Zostanie wyświetlony komunikat, jeśli aktualizacje oprogramowania są dostępne. Aby sprawdzić dostępność aktualizacji, możesz także kliknąć pozycję **Skanuj**. Zanotuj uruchomioną wersję oprogramowania. 

    ![aktualizowanie urządzenia](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    Otrzymasz powiadomienie, gdy skanowanie zostanie uruchomione i zakończy się pomyślnie.
 
4. Po przeskanowaniu aktualizacji kliknij pozycję **Pobierz aktualizacje**. W obszarze **nowe aktualizacje**zapoznaj się z informacjami o wersji. Należy również pamiętać, że po pobraniu aktualizacji musisz potwierdzić instalację. Kliknij przycisk **OK**.

    ![aktualizowanie urządzenia](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    Otrzymasz powiadomienie, gdy przekazywanie zostanie uruchomione i zakończy się pomyślnie.

5. W obszarze **aktualizacje urządzeń**kliknij przycisk **Zainstaluj**.

     ![aktualizowanie urządzenia](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. W obszarze **nowe aktualizacje**zostanie wyświetlone ostrzeżenie, że aktualizacja jest zakłócona. Ponieważ macierz wirtualna to urządzenie o jednym węźle, urządzenie zostanie ponownie uruchomione po zaktualizowaniu. Spowoduje to zakłócenie wszystkich operacji we/wy. Kliknij przycisk **OK**, aby zainstalować aktualizacje.

    ![aktualizowanie urządzenia](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    Otrzymasz powiadomienie o uruchomieniu zadania instalacji.

7.  Po pomyślnym zakończeniu zadania instalacji kliknij link **Wyświetl zadanie** . Ta akcja spowoduje przejście do bloku **Instalowanie aktualizacji** . W tym miejscu możesz wyświetlić szczegółowe informacje o zadaniu. 

    ![aktualizowanie urządzenia](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. Po rozpoczęciu pracy z macierzą wirtualną z uruchomioną aktualizacją Update 1 (10.0.10296.0) jest teraz uruchomiona aktualizacja 1,1 i gotowe. Możesz pominąć pozostałe kroki. 

    Po rozpoczęciu pracy z macierzą wirtualną z uruchomioną aktualizacją Update 0,6 (10.0.10293.0), użytkownik jest teraz aktualizowany do aktualizacji 1,0. Zobaczysz kolejny komunikat wskazujący, że aktualizacje są dostępne. Powtórz kroki 4-7, aby zainstalować aktualizację 1,1.

    

