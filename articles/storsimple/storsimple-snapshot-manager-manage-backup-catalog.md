---
title: StorSimple Snapshot Manager wykaz kopii zapasowych | Microsoft Docs
description: Opisuje sposób używania przystawki StorSimple Snapshot Manager MMC do wyświetlania wykazu kopii zapasowych i zarządzania nim.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6abdbfd2-22ce-45a5-aa15-38fae4c8f4ec
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 2efc35e65ca1db2b5241e1d3b2798e068880c87e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90054997"
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Zarządzanie wykazem kopii zapasowych przy użyciu Snapshot Manager StorSimple

## <a name="overview"></a>Omówienie
Podstawową funkcją StorSimple Snapshot Manager jest umożliwienie tworzenia kopii zapasowej spójnej na poziomie aplikacji dla woluminów StorSimple w formie migawek. Migawki są następnie wyświetlane w pliku XML nazywanym *wykazem kopii zapasowych*. Wykaz kopii zapasowych organizuje migawki według grupy woluminów, a następnie według migawek lokalnych lub migawek w chmurze.

W tym samouczku opisano, jak można użyć węzła **wykazu kopii zapasowych** , aby wykonać następujące zadania:

* Przywróć wolumin
* Klonowanie woluminu lub grupy woluminów
* Usuń kopię zapasową
* Odzyskiwanie pliku
* Przywracanie bazy danych StorSimple Snapshot Manager

Wykaz kopii zapasowych można wyświetlić, rozwijając węzeł **wykaz kopii zapasowych** w okienku **zakres** , a następnie rozszerzając grupę woluminów.

* Jeśli klikniesz nazwę grupy woluminów, w okienku **wyników** zostanie wyświetlona liczba lokalnych migawek i migawek chmury dostępnych dla grupy woluminów. 
* Jeśli klikniesz pozycję **migawka lokalna** lub **migawka w chmurze**, w okienku **wyników** zostaną wyświetlone następujące informacje o każdej migawce kopii zapasowej (w zależności od ustawień **widoku** ):
  
  * **Nazwa** — czas utworzenia migawki.
  * **Typ** — czy jest to migawka lokalna czy migawka w chmurze.
  * **Owner** — właściciel zawartości. 
  * **Dostępne** — czy migawka jest obecnie dostępna. **Wartość true** wskazuje, że migawka jest dostępna i można ją przywrócić; **Wartość false** wskazuje, że migawka nie jest już dostępna. 
  * **Zaimportowano** — czy kopia zapasowa została zaimportowana. **Wartość true** wskazuje, że kopia zapasowa została zaimportowana z usługi StorSimple Device Manager w chwili, gdy urządzenie zostało skonfigurowane w StorSimple Snapshot Manager; **Wartość false** wskazuje, że nie została zaimportowana, ale została utworzona przez StorSimple Snapshot Manager. (Można łatwo zidentyfikować zaimportowaną grupę woluminów, ponieważ dodawany jest sufiks identyfikujący urządzenie, z którego została zaimportowana Grupa woluminów).
    
    ![Wykaz kopii zapasowych](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* W przypadku rozszerzenia **migawki lokalnej** lub **migawki w chmurze**, a następnie kliknięcia pojedynczej nazwy migawki w okienku **wyników** zostaną wyświetlone następujące informacje dotyczące wybranej migawki:
  
  * **Nazwa** — wolumin identyfikowany za pomocą litery dysku. 
  * **Nazwa lokalna** — nazwa lokalna dysku (jeśli jest dostępna). 
  * **Device** — nazwa urządzenia, na którym znajduje się wolumin. 
  * **Dostępne** — czy migawka jest obecnie dostępna. **Wartość true** wskazuje, że migawka jest dostępna i można ją przywrócić; **Wartość false** wskazuje, że migawka nie jest już dostępna. 

## <a name="restore-a-volume"></a>Przywróć wolumin
Aby przywrócić wolumin z kopii zapasowej, wykonaj czynności opisane w poniższej procedurze.

#### <a name="prerequisites"></a>Wymagania wstępne
Jeśli jeszcze tego nie zrobiono, Utwórz wolumin i grupę woluminów, a następnie Usuń wolumin. Domyślnie program StorSimple Snapshot Manager tworzy kopię zapasową woluminu przed zezwoleniem na jego usunięcie. Dzięki temu można zapobiec utracie danych, jeśli wolumin zostanie usunięty przypadkowo lub jeśli dane wymagają odzyskania z dowolnego powodu. 

StorSimple Snapshot Manager wyświetla następujący komunikat podczas tworzenia kopii zapasowej z zachowaniem ostrożności.

![Automatyczny komunikat migawki](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> Nie można usunąć woluminu, który jest częścią grupy woluminów. Opcja usuwania jest niedostępna. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>Aby przywrócić wolumin
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager. 
2. W okienku **zakres** rozwiń węzeł **wykaz kopii zapasowych** , rozwiń grupę woluminów, a następnie kliknij pozycję **migawki lokalne** lub **migawki w chmurze**. W okienku **wyników** zostanie wyświetlona lista migawek kopii zapasowych.
3. Znajdź kopię zapasową, którą chcesz przywrócić, kliknij prawym przyciskiem myszy, a następnie kliknij polecenie **Przywróć**.
   
    ![Przywróć wykaz kopii zapasowych](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. Na stronie Potwierdzenie przejrzyj szczegóły, wpisz polecenie **Potwierdź**, a następnie kliknij przycisk **OK**. StorSimple Snapshot Manager używa kopii zapasowej do przywrócenia woluminu.
   
    ![Przywróć komunikat z potwierdzeniem](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. Możesz monitorować akcję przywracania w trakcie jej działania. W okienku **zakres** rozwiń węzeł **zadania** , a następnie kliknij przycisk **uruchomione**. Szczegóły zadania są wyświetlane w okienku **wyników** . Po zakończeniu zadania przywracania szczegóły zadania są przesyłane do listy **ostatnich 24 godzin** .

## <a name="clone-a-volume-or-volume-group"></a>Klonowanie woluminu lub grupy woluminów
Poniższa procedura umożliwia utworzenie duplikatu (klonowania) woluminu lub grupy woluminów.

#### <a name="to-clone-a-volume-or-volume-group"></a>Aby sklonować wolumin lub grupę woluminów
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager.
2. W okienku **zakres** rozwiń węzeł **wykaz kopii zapasowych** , rozwiń grupę woluminów, a następnie kliknij pozycję **migawki w chmurze**. W okienku **wyników** zostanie wyświetlona lista kopii zapasowych.
3. Znajdź wolumin lub grupę woluminów, które chcesz sklonować, kliknij prawym przyciskiem myszy nazwę woluminu lub grupy woluminów, a następnie kliknij polecenie **Klonuj**. Zostanie wyświetlone okno dialogowe **klonowanie migawki w chmurze** .
   
    ![Klonowanie migawki w chmurze](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Wypełnij okno dialogowe **klonowanie migawki chmury** w następujący sposób: 
   
   1. W polu tekstowym **Nazwa** wpisz nazwę sklonowanego woluminu. Ta nazwa zostanie wyświetlona w węźle **woluminy** . 
   2. (Opcjonalnie) wybierz pozycję **dysk**, a następnie wybierz z listy rozwijanej literę dysku.
   3. (Opcjonalnie) wybierz pozycję **folder (NTFS)**, a następnie wpisz ścieżkę folderu lub kliknij przycisk Przeglądaj i wybierz lokalizację folderu. 
   4. Kliknij pozycję **Utwórz**.
5. Po zakończeniu procesu klonowania należy zainicjować sklonowany wolumin. Uruchom Menedżer serwera, a następnie uruchom Zarządzanie dyskami. Aby uzyskać szczegółowe instrukcje, zobacz [woluminy instalacji](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Po jego zainicjowaniu wolumin zostanie wyświetlony w węźle **woluminy** w okienku **zakres** . Jeśli nie widzisz na liście woluminów, Odśwież listę woluminów (kliknij prawym przyciskiem myszy węzeł **woluminy** , a następnie kliknij polecenie **Odśwież**).

## <a name="delete-a-backup"></a>Usuń kopię zapasową
Aby usunąć migawkę z wykazu kopii zapasowych, należy wykonać czynności opisane w poniższej procedurze. 

> [!NOTE]
> Usunięcie migawki spowoduje usunięcie kopii zapasowej danych skojarzonych z migawką. Jednak proces czyszczenia danych z chmury może zająć trochę czasu.<br>


#### <a name="to-delete-a-backup"></a>Aby usunąć kopię zapasową
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager.
2. W okienku **zakres** rozwiń węzeł **wykaz kopii zapasowych** , rozwiń grupę woluminów, a następnie kliknij pozycję **migawki lokalne** lub **migawki w chmurze**. W okienku **wyników** zostanie wyświetlona lista migawek.
3. Kliknij prawym przyciskiem myszy migawkę, którą chcesz usunąć, a następnie kliknij polecenie **Usuń**.
4. Po wyświetleniu komunikatu potwierdzenia kliknij przycisk **OK**.

## <a name="recover-a-file"></a>Odzyskiwanie pliku
Jeśli plik zostanie przypadkowo usunięty z woluminu, można odzyskać plik, pobierając migawkę, która wstępnie wprowadza daty usunięcia, przy użyciu migawki do utworzenia klonu woluminu, a następnie kopiując plik ze sklonowanego woluminu do woluminu oryginalnego.

#### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że masz bieżącą kopię zapasową grupy woluminów. Następnie usuń plik przechowywany na jednym z woluminów w tej grupie woluminów. Na koniec wykonaj następujące kroki, aby przywrócić usunięty plik z kopii zapasowej. 

#### <a name="to-recover-a-deleted-file"></a>Aby odzyskać usunięty plik
1. Kliknij ikonę Snapshot Manager StorSimple na pulpicie. Zostanie wyświetlone okno konsoli Snapshot Manager StorSimple. 
2. W okienku **zakres** rozwiń węzeł **wykaz kopii zapasowych** i przejdź do migawki zawierającej usunięty plik. Zazwyczaj należy wybrać migawkę, która została utworzona tuż przed usunięciem.
3. Znajdź wolumin, który ma zostać sklonowany, kliknij prawym przyciskiem myszy, a następnie kliknij polecenie **Klonuj**. Zostanie wyświetlone okno dialogowe **klonowanie migawki w chmurze** .
   
    ![Klonowanie migawki w chmurze](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Wypełnij okno dialogowe **klonowanie migawki chmury** w następujący sposób: 
   
   1. W polu tekstowym **Nazwa** wpisz nazwę sklonowanego woluminu. Ta nazwa zostanie wyświetlona w węźle **woluminy** . 
   2. Obowiązkowe Wybierz pozycję **dysk**, a następnie wybierz z listy rozwijanej literę dysku. 
   3. Obowiązkowe Wybierz pozycję **folder (NTFS)**, a następnie wpisz ścieżkę folderu lub kliknij przycisk **Przeglądaj** i wybierz lokalizację folderu. 
   4. Kliknij pozycję **Utwórz**. 
5. Po zakończeniu procesu klonowania należy zainicjować sklonowany wolumin. Uruchom Menedżer serwera, a następnie uruchom Zarządzanie dyskami. Aby uzyskać szczegółowe instrukcje, zobacz [woluminy instalacji](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Po jego zainicjowaniu wolumin zostanie wyświetlony w węźle **woluminy** w okienku **zakres** . 
   
    Jeśli nie widzisz na liście woluminów, Odśwież listę woluminów (kliknij prawym przyciskiem myszy węzeł **woluminy** , a następnie kliknij polecenie **Odśwież**).
6. Otwórz folder NTFS zawierający sklonowany wolumin, rozwiń węzeł **woluminy** , a następnie otwórz sklonowany wolumin. Znajdź plik do odzyskania i skopiuj go na wolumin podstawowy.
7. Po przywróceniu pliku można usunąć folder systemu plików NTFS, który zawiera sklonowany wolumin.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Przywracanie bazy danych StorSimple Snapshot Manager
Należy regularnie tworzyć kopie zapasowe bazy danych StorSimple Snapshot Manager na komputerze-hoście. Jeśli wystąpi awaria lub komputer hosta nie powiedzie się z jakiegokolwiek powodu, można go przywrócić z kopii zapasowej. Tworzenie kopii zapasowej bazy danych jest procesem ręcznym.

#### <a name="to-back-up-and-restore-the-database"></a>Aby utworzyć kopię zapasową i przywrócić bazę danych
1. Zatrzymaj usługę Microsoft StorSimple Management:
   
   1. Uruchom Menedżera serwera.
   2. Na pulpicie nawigacyjnym Menedżer serwera w menu **Narzędzia** wybierz pozycję **usługi**.
   3. W oknie **usługi** wybierz **usługę Microsoft StorSimple Management**.
   4. W prawym okienku w obszarze **usługi Microsoft StorSimple Management** kliknij pozycję **Zatrzymaj usługę**.
2. Na komputerze-hoście przejdź do C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > FolderProgramData jest folderem ukrytym.
   > 
   > 
3. Znajdź plik XML katalogu, skopiuj plik i Zapisz kopię w bezpiecznej lokalizacji lub w chmurze. Jeśli host nie powiedzie się, można użyć tego pliku kopii zapasowej, aby pomóc w odzyskiwaniu zasad tworzenia kopii zapasowych utworzonych w StorSimple Snapshot Manager.
   
    ![Plik wykazu kopii zapasowych usługi Azure StorSimple](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Uruchom ponownie usługę Microsoft StorSimple Management: 
   
   1. Na pulpicie nawigacyjnym Menedżer serwera w menu **Narzędzia** wybierz pozycję **usługi**.
   2. W oknie **usługi** wybierz **usługę Microsoft StorSimple Management**.
   3. W prawym okienku w obszarze **usługi Microsoft StorSimple Management** kliknij pozycję **Uruchom ponownie usługę**.
5. Na komputerze-hoście przejdź do C:\ProgramData\Microsoft\StorSimple\BACatalog. 
6. Usuń plik XML katalogu i zastąp go utworzoną wersją kopii zapasowej. 
7. Kliknij ikonę StorSimple pulpitu Snapshot Manager, aby uruchomić StorSimple Snapshot Manager. 

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [korzystaniu z usługi StorSimple Snapshot Manager do administrowania rozwiązaniem StorSimple](storsimple-snapshot-manager-admin.md).
* Dowiedz się więcej o [StorSimple Snapshot Manager zadaniach i przepływach pracy](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).

