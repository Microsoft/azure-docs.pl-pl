---
title: Zadania tworzenia kopii zapasowych StorSimple Snapshot Manager | Microsoft Docs
description: Opisuje sposób używania przystawki StorSimple Snapshot Manager MMC do wyświetlania zaplanowanych, aktualnie uruchomionych i zakończonych zadań tworzenia kopii zapasowych oraz zarządzania nimi.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 3c26a84e32a17cba83b5ca895f146e561072fa62
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90054963"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Używanie Snapshot Manager StorSimple do wyświetlania zadań tworzenia kopii zapasowych i zarządzania nimi

## <a name="overview"></a>Omówienie
W węźle **zadania** w okienku **zakres** wyświetlane są **zaplanowane**, **ostatnie 24 godziny**i **uruchomione** zadania tworzenia kopii zapasowej zainicjowane interaktywnie lub przez skonfigurowane zasady. 

W tym samouczku wyjaśniono, jak można użyć węzła **zadania** do wyświetlania informacji o zaplanowanych, ostatnich i aktualnie uruchomionych zadaniach tworzenia kopii zapasowej. (Lista zadań i odpowiadające im informacje są wyświetlane w okienku **wyników** ). Ponadto można kliknąć prawym przyciskiem myszy wyświetlone zadanie i wyświetlić menu kontekstowe zawierające listę dostępnych akcji.

## <a name="view-scheduled-jobs"></a>Wyświetlanie zaplanowanych zadań
Aby wyświetlić zaplanowane zadania tworzenia kopii zapasowej, należy wykonać poniższą procedurę.

#### <a name="to-view-scheduled-jobs"></a>Aby wyświetlić zaplanowane zadania
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager. 
2. W okienku **zakres** rozwiń węzeł **zadania** , a następnie kliknij pozycję **zaplanowane**. W okienku **wyników** zostaną wyświetlone następujące informacje:
   
   * **Name** — nazwa zaplanowanej migawki
   * **Następne uruchomienie** — Data i godzina następnej zaplanowanej migawki
   * **Ostatnie uruchomienie** — Data i godzina ostatniej zaplanowanej migawki
     
     > [!NOTE]
     > W przypadku migawek jednorazowych **następne uruchomienie** i **ostatni przebieg** będą takie same.
     
     ![Zaplanowane zadania tworzenia kopii zapasowej](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. Aby wykonać dodatkowe akcje w określonym zadaniu, kliknij prawym przyciskiem myszy nazwę zadania w okienku **wyników** , a następnie wybierz jedną z opcji menu.

## <a name="view-recent-jobs"></a>Wyświetl ostatnie zadania
Aby wyświetlić zadania tworzenia kopii zapasowej i przywracania wykonane w ciągu ostatnich 24 godzin, wykonaj czynności opisane w poniższej procedurze.

#### <a name="to-view-recent-jobs"></a>Aby wyświetlić ostatnie zadania
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager.
2. W okienku **zakres** rozwiń węzeł **zadania** , a następnie kliknij pozycję **ostatnie 24 godziny**. W okienku **wyników** wyświetlane są zadania tworzenia kopii zapasowej dla ostatnich 24 godzin (maksymalnie 64 zadań). Poniższe informacje są wyświetlane w okienku **wyników** w zależności od wybranych opcji **widoku** :
   
   * **Name** — nazwa zaplanowanej migawki.
   * **Rozpoczęte** — Data i godzina rozpoczęcia migawki.
   * **Zatrzymane** — Data i godzina zakończenia lub zakończenia migawki.
   * **Upłynęło** — ilość czasu między **rozpoczęciem** i **zatrzymaniem** .
   * **Status** — stan ostatnio wykonanego zadania. **Sukces** wskazuje, że tworzenie kopii zapasowej zostało pomyślnie utworzone. **Niepowodzenie** oznacza, że zadanie nie zostało wykonane pomyślnie.
   * **Informacje** — Przyczyna błędu.
   * **Przetworzone bajty (MB)** — ilość danych z grupy woluminów przetworzonych (w MB). 
     
     ![Zadania uruchomione w ciągu ostatnich 24 godzin](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. Aby wykonać dodatkowe akcje w określonym zadaniu, kliknij prawym przyciskiem myszy nazwę zadania w okienku **wyników** , a następnie wybierz jedną z opcji menu.
   
    ![Usuwanie zadania](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Wyświetl aktualnie uruchomione zadania
Aby wyświetlić aktualnie uruchomione zadania, należy wykonać poniższą procedurę.

#### <a name="to-view-currently-running-jobs"></a>Aby wyświetlić aktualnie uruchomione zadania
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager.
2. W okienku **zakres** rozwiń węzeł **zadania** , a następnie kliknij przycisk **uruchomione**. W zależności od określonych opcji **widoku** w okienku **wyników** zostaną wyświetlone następujące informacje:
   
   * **Name** — nazwa zaplanowanej migawki.
   * **Rozpoczęte** — Data i godzina rozpoczęcia migawki.
   * **Checkpoint** — Bieżąca akcja kopii zapasowej.
   * **Status** — procent ukończenia.
   * **Upłynęło** — ilość czasu, która upłynęła od momentu rozpoczęcia tworzenia kopii zapasowej. 
   * **Średnia przepływność (MB)** — stosunek całkowitej liczby bajtów przetworzonych na ten łączny czas przetwarzania (MB).
   * **Przetworzone bajty (MB)** — łączne bajty przetworzonych danych (w MB).
   * **Bajty zapisywane (MB)** — całkowita liczba bajtów zapisanych (w MB). Obejmuje to zarówno dane, jak i metadane, a więc jest zazwyczaj większa niż liczba przetworzonych bajtów.
     
     ![Aktualnie uruchomione zadania](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. Aby wykonać dodatkowe akcje w określonym zadaniu, kliknij prawym przyciskiem myszy nazwę zadania w okienku **wyników** , a następnie wybierz jedną z opcji menu.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [używać Snapshot Manager StorSimple do administrowania rozwiązaniem StorSimple](storsimple-snapshot-manager-admin.md).
* Dowiedz się, jak [za pomocą Snapshot Manager StorSimple zarządzać katalogiem kopii zapasowych](storsimple-snapshot-manager-manage-backup-catalog.md).

