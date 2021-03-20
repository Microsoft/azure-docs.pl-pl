---
title: Samouczek dotyczący tworzenia kopii zapasowej macierzy wirtualnej Microsoft Azure StorSimple | Microsoft Docs
description: Utwórz zaplanowane i ręczne kopie zapasowe Microsoft Azure StorSimple macierzy wirtualnej i wykonaj odzyskiwanie na poziomie elementu, aby przywrócić usunięty plik w macierzy wirtualnej.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: e3cdcd9e-33b1-424d-82aa-b369d934067e
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 99c7f4ab92e4cce3937a933fec97008c281a0eae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018294"
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>Tworzenie kopii zapasowej udziałów lub woluminów w macierzy wirtualnej StorSimple

## <a name="overview"></a>Omówienie

Wirtualna macierz StorSimple to hybrydowe urządzenie wirtualne magazynu w chmurze, które można skonfigurować jako serwer plików lub serwer iSCSI. Tablica wirtualna umożliwia użytkownikowi tworzenie zaplanowanych i ręcznych kopii zapasowych wszystkich udziałów lub woluminów na urządzeniu. Po skonfigurowaniu jako serwer plików, umożliwia również odzyskiwanie na poziomie elementu. W tym samouczku opisano sposób tworzenia zaplanowanych i ręcznych kopii zapasowych oraz odzyskiwania na poziomie elementu w celu przywrócenia usuniętego pliku w macierzy wirtualnej.

Ten samouczek dotyczy tylko macierzy wirtualnych StorSimple. Aby uzyskać informacje na temat serii 8000, przejdź do obszaru [Tworzenie kopii zapasowej dla urządzenia z serii 8000](./storsimple-8000-manage-backup-policies-u2.md)

## <a name="back-up-shares-and-volumes"></a>Tworzenie kopii zapasowych udziałów i woluminów

Kopie zapasowe zapewniają ochronę w czasie, zwiększają możliwości odzyskiwania i minimalizują czasy przywracania udziałów i woluminów. Możesz utworzyć kopię zapasową udziału lub woluminu na urządzeniu StorSimple na dwa sposoby: **zaplanowane** lub **Ręczne**. Każda z tych metod została omówiona w poniższych sekcjach.

## <a name="change-the-backup-start-time"></a>Zmień godzinę rozpoczęcia tworzenia kopii zapasowej

> [!NOTE]
> W tej wersji zaplanowane kopie zapasowe są tworzone przy użyciu domyślnych zasad, które są uruchamiane codziennie o określonej godzinie, i wykonują kopie zapasowe wszystkich udziałów lub woluminów na urządzeniu. W tej chwili nie można tworzyć zasad niestandardowych dla zaplanowanych kopii zapasowych.


Wirtualna macierz StorSimple ma domyślne zasady tworzenia kopii zapasowych, które zaczynają się od określonego dnia (22:30) i tworzy kopię zapasową wszystkich udziałów lub woluminów na urządzeniu raz dziennie. Można zmienić czas, w którym rozpocznie się wykonywanie kopii zapasowej, ale nie można zmienić częstotliwości i przechowywania (która określa liczbę kopii zapasowych do zachowania). W tych kopiach zapasowych zostanie utworzona kopia zapasowa całego urządzenia wirtualnego. Może to mieć wpływ na wydajność urządzenia i wpływ na obciążenia wdrożone na urządzeniu. W związku z tym zaleca się zaplanowanie tych kopii zapasowych w godzinach poza godzinami szczytu.

 Aby zmienić domyślną godzinę rozpoczęcia tworzenia kopii zapasowej, wykonaj następujące czynności w [Azure Portal](https://portal.azure.com/).

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Aby zmienić godzinę rozpoczęcia dla domyślnych zasad tworzenia kopii zapasowych

1. Przejdź do pozycji **urządzenia**. Zostanie wyświetlona lista urządzeń zarejestrowanych w usłudze StorSimple Device Manager. 
   
    ![Przejdź do urządzeń](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Wybierz i kliknij urządzenie. Zostanie wyświetlony blok **Ustawienia** . Przejdź do obszaru **zarządzanie > zasadami kopii zapasowych**.
   
    ![Wybierz urządzenie](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. W bloku **zasady tworzenia kopii zapasowych** domyślną wartością czasu rozpoczęcia jest 22:30. Możesz określić nowy czas rozpoczęcia dziennego harmonogramu w strefie czasowej urządzenia.
   
    ![Przejdź do zasad kopii zapasowych](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Kliknij pozycję **Zapisz**.

### <a name="take-a-manual-backup"></a>Ręczne tworzenie kopii zapasowej

Oprócz zaplanowanych kopii zapasowych można w dowolnym momencie wykonać ręcznie (na żądanie) kopię zapasową danych urządzeń.

#### <a name="to-create-a-manual-backup"></a>Ręczne tworzenie kopii zapasowej

1. Przejdź do pozycji **urządzenia**. Wybierz urządzenie, a następnie kliknij prawym przyciskiem myszy pozycję.. **.** najdalej w wybranym wierszu. Z menu kontekstowego wybierz polecenie **Utwórz kopię zapasową**.
   
    ![Przejdź do tworzenia kopii zapasowej](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. W bloku **Utwórz kopię zapasową** kliknij pozycję **Utwórz kopię zapasową**. Spowoduje to utworzenie kopii zapasowej wszystkich udziałów na serwerze plików lub na wszystkich woluminach na serwerze iSCSI. 
   
    ![Rozpoczynanie tworzenia kopii zapasowej](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    Rozpocznie się tworzenie kopii zapasowej na żądanie i zobaczysz, że uruchomiono zadanie tworzenia kopii zapasowej.
   
    ![kopia zapasowa — początek 2](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    Po pomyślnym zakończeniu zadania zostanie wyświetlone powiadomienie. Następnie rozpocznie się proces tworzenia kopii zapasowej.
   
    ![utworzono zadanie tworzenia kopii zapasowej](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. Aby śledzić postęp tworzenia kopii zapasowych i przeglądać szczegóły zadania, kliknij powiadomienie. Spowoduje to przejście do  **szczegółów zadań**.
   
     ![Szczegóły zadania kopii zapasowej](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. Po zakończeniu tworzenia kopii zapasowej przejdź do pozycji **zarządzanie > wykaz kopii zapasowych**. Zobaczysz migawkę w chmurze wszystkich udziałów (lub woluminów) na urządzeniu.
   
    ![Zakończona kopia zapasowa](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Wyświetl istniejące kopie zapasowe
Aby wyświetlić istniejące kopie zapasowe, wykonaj następujące kroki w Azure Portal.

#### <a name="to-view-existing-backups"></a>Aby wyświetlić istniejące kopie zapasowe

1. Przejdź do bloku **urządzenia** . Wybierz i kliknij urządzenie. W bloku **Ustawienia** przejdź do pozycji **Zarządzanie > wykaz kopii zapasowych**.
   
    ![Przejdź do katalogu kopii zapasowych](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. Określ następujące kryteria, które mają być używane do filtrowania:
   
   - **Zakres czasu** — może być **wcześniejsza niż 1 godzina**, **ostatnie 24 godziny**, **ostatnie 7 dni**, **ostatnie 30 dni**, **ubiegły rok** i **Data niestandardowa**.
    
   - **Urządzenia** — wybierz z listy serwerów plików lub serwerów iSCSI zarejestrowanych w usłudze StorSimple Device Manager.
   
   - **Zainicjowane** — może być automatycznie **zaplanowana** (według zasad tworzenia kopii zapasowej) lub **ręcznie** zainicjowane (przez użytkownika).
   
     ![Filtruj kopie zapasowe](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Kliknij pozycję **Zastosuj**. Filtrowana lista kopii zapasowych jest wyświetlana w bloku **wykazu kopii zapasowych** . Uwaga tylko 100 elementów kopii zapasowej może być wyświetlanych w danym momencie.
   
    ![Zaktualizowany wykaz kopii zapasowych](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat administrowania wirtualną macierzą StorSimple](storsimple-ova-web-ui-admin.md).