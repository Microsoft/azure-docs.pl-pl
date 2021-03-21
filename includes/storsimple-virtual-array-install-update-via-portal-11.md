---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/18/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 8ef8b9d3350d0599ab00dfdbb018cf8dd900e316
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95563953"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Aby zainstalować aktualizacje za pośrednictwem witryny Azure Portal

1. Przejdź do menedżera urządzeń StorSimple i wybierz pozycję **Urządzenia**. Na liście urządzeń połączonych z usługą wybierz i kliknij urządzenie, które chcesz zaktualizować.

2. W obszarze **Ustawienia** kliknij pozycję **aktualizacje urządzeń**.  

3. Zostanie wyświetlony komunikat, jeśli aktualizacje oprogramowania są dostępne. Aby sprawdzić dostępność aktualizacji, możesz także kliknąć pozycję **Skanuj**. Zanotuj uruchomioną wersję oprogramowania. 

    ![W okienku aktualizacje urządzenia jest wyświetlany komunikat "nowe aktualizacje są dostępne" (wyróżnione) i "ostatnio przeskanowane/6/22/2018 2:46 PM/oprogramowanie/10.0.10296.0". Wersja zostanie wyróżniona.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    Otrzymasz powiadomienie, gdy skanowanie zostanie uruchomione i zakończy się pomyślnie.
 
4. Po przeskanowaniu aktualizacji kliknij pozycję **Pobierz aktualizacje**. W obszarze **nowe aktualizacje** zapoznaj się z informacjami o wersji. Należy również pamiętać, że po pobraniu aktualizacji musisz potwierdzić instalację. Kliknij przycisk **OK**.

    ![W obszarze Aktualizacje urządzeń przycisk Pobierz aktualizacje jest wyróżniony. W obszarze nowe aktualizacje istnieje link do informacji o wersji i komunikat informujący o konieczności potwierdzenia instalacji po pobraniu aktualizacji. Istnieje przycisk OK.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    Otrzymasz powiadomienie, gdy przekazywanie zostanie uruchomione i zakończy się pomyślnie.

5. W obszarze **aktualizacje urządzeń** kliknij przycisk **Zainstaluj**.

     ![Aktualizacje urządzeń to "Potwierdź zainstalowanie aktualizacji". Zostanie wyróżniony przycisk Instaluj i wersja oprogramowania.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. W obszarze **nowe aktualizacje** zostanie wyświetlone ostrzeżenie, że aktualizacja jest zakłócona. Ponieważ macierz wirtualna to urządzenie o jednym węźle, urządzenie zostanie ponownie uruchomione po zaktualizowaniu. Spowoduje to zakłócenie wszystkich operacji we/wy. Kliknij przycisk **OK**, aby zainstalować aktualizacje.

    ![Nowe aktualizacje to "urządzenie będzie miało przestoje po zainstalowaniu tych aktualizacji". Istnieje link do informacji o wersji i przycisk OK.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    Otrzymasz powiadomienie o uruchomieniu zadania instalacji.

7.  Po pomyślnym zakończeniu zadania instalacji kliknij link **Wyświetl zadanie** . Ta akcja spowoduje przejście do bloku **Instalowanie aktualizacji** . W tym miejscu możesz wyświetlić szczegółowe informacje o zadaniu. 

    ![Aktualizacje urządzeń mają link o nazwie Instalowanie aktualizacji. Wyświetl zadanie ".](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. Po rozpoczęciu pracy z macierzą wirtualną z uruchomioną aktualizacją Update 1 (10.0.10296.0) jest teraz uruchomiona aktualizacja 1,1 i gotowe. Możesz pominąć pozostałe kroki. 

    Po rozpoczęciu pracy z macierzą wirtualną z uruchomioną aktualizacją Update 0,6 (10.0.10293.0), użytkownik jest teraz aktualizowany do aktualizacji 1,0. Zobaczysz kolejny komunikat wskazujący, że aktualizacje są dostępne. Powtórz kroki 4-7, aby zainstalować aktualizację 1,1.

    

