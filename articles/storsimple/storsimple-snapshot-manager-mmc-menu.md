---
title: Akcje menu StorSimple Snapshot Manager MMC | Microsoft Docs
description: Opisuje sposób używania standardowych akcji menu programu Microsoft Management Console (MMC) w programie StorSimple Snapshot Manager.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 78ef81af-0d3a-4802-be54-ad192f9ac8a6
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 01064c3668b673baea7aedd9a65c92b03c48dc10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90056007"
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Użyj akcji menu programu MMC w StorSimple Snapshot Manager

## <a name="overview"></a>Omówienie
W StorSimple Snapshot Manager zostaną wyświetlone następujące akcje wymienione na liście wszystkie menu akcji i wszystkie zmiany w okienku **Akcje** .

* Widok
* Nowe okno w tym miejscu 
* Odśwież 
* Eksportuj listę 
* Help 

Te akcje są częścią programu Microsoft Management Console (MMC) i nie są specyficzne dla StorSimple Snapshot Manager. W tym samouczku opisano te akcje i wyjaśniono, jak używać poszczególnych z nich w programie StorSimple Snapshot Manager.

## <a name="view"></a>Widok
Możesz użyć opcji **Widok** , aby zmienić widok okienka **wyników** i zmienić widok okna konsoli. 

#### <a name="to-change-the-results-pane-view"></a>Aby zmienić widok okienka wyników
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager.
2. W okienku **zakres** kliknij prawym przyciskiem myszy dowolny węzeł lub rozwiń węzeł, a następnie kliknij prawym przyciskiem myszy element w okienku **wyników** , a następnie kliknij opcję **Wyświetl** . 
3. Aby dodać lub usunąć kolumny, które są wyświetlane w okienku **wyników** , kliknij przycisk **Dodaj/Usuń kolumny**. Zostanie wyświetlone okno dialogowe **Dodaj/Usuń kolumny** .
   
    ![Dodawanie lub usuwanie kolumn z okienka wyników](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. Wypełnij formularz w następujący sposób:
   
   * Wybierz pozycję elementy z listy **dostępne** kolumny, a następnie kliknij przycisk **Dodaj** , aby dodać je do listy **wyświetlanych kolumn** . 
   * Kliknij pozycję elementy na liście **wyświetlane kolumny** , a następnie kliknij przycisk **Usuń** , aby usunąć je z listy. 
   * Wybierz element na liście **wyświetlane** kolumny i kliknij pozycję **Przenieś w górę** lub **Przenieś w dół** , aby przenieść element w górę lub w dół na liście. 
   * Kliknij przycisk **Przywróć domyślne** , aby powrócić do domyślnej konfiguracji okienka **wyników** . 
5. Po zakończeniu zaznaczania opcji kliknij przycisk **OK**. 

#### <a name="to-change-the-console-window-view"></a>Aby zmienić widok okna konsoli
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager.
2. W okienku **zakres** kliknij prawym przyciskiem myszy dowolny węzeł, kliknij polecenie **Widok**, a następnie kliknij przycisk **Dostosuj**. Zostanie wyświetlone okno dialogowe **Dostosuj** .
   
    ![Dostosowywanie okna konsoli](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. Zaznacz lub wyczyść pola wyboru, aby pokazać lub ukryć elementy w oknie konsoli. Po zakończeniu zaznaczania opcji kliknij przycisk **OK**.

## <a name="new-window-from-here"></a>Nowe okno w tym miejscu
Możesz użyć **nowego okna z tego miejsca** , aby otworzyć nowe okno konsoli.

#### <a name="to-open-a-new-console-window"></a>Aby otworzyć nowe okno konsoli
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager.
2. W okienku **zakres** kliknij prawym przyciskiem myszy dowolny węzeł, a następnie kliknij pozycję **nowe okno w tym miejscu**. 
   
    Zostanie wyświetlone nowe okno z widocznym tylko wybranym zakresem. Na przykład po kliknięciu prawym przyciskiem myszy węzła **zasady tworzenia kopii** zapasowych nowe okno będzie zawierać tylko węzeł **zasady tworzenia kopii zapasowych** w okienku **zakres** oraz listę zdefiniowanych zasad tworzenia kopii zapasowych w okienku **wyników** . Zobacz poniższy przykład.
   
    ![Nowe okno w tym miejscu](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>Odśwież
Możesz użyć akcji **Odśwież** , aby zaktualizować okno konsoli.

#### <a name="to-update-the-console-window"></a>Aby zaktualizować okno konsoli
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager.
2. W okienku **zakres** kliknij prawym przyciskiem myszy dowolny węzeł lub rozwiń węzeł, a następnie kliknij prawym przyciskiem myszy element w okienku **wyników** , a następnie kliknij przycisk **Odśwież**. 

## <a name="export-list"></a>Eksportuj listę
Za pomocą akcji **Eksportuj listę** można zapisać listę w pliku z wartościami rozdzielanymi przecinkami (CSV). Można na przykład wyeksportować listę zasad tworzenia kopii zapasowych lub wykazu kopii zapasowych. Następnie można zaimportować plik CSV do aplikacji arkusza kalkulacyjnego do analizy.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Aby zapisać listę w pliku wartości rozdzielanych przecinkami (CSV)
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager. 
2. W okienku **zakres** kliknij prawym przyciskiem myszy dowolny węzeł lub rozwiń węzeł, a następnie kliknij prawym przyciskiem myszy element w okienku **wyników** , a następnie kliknij pozycję **Eksportuj listę**. 
3. Zostanie wyświetlone okno dialogowe **Eksportuj listę** . Wypełnij formularz w następujący sposób: 
   
   1. W polu **Nazwa pliku** wpisz nazwę pliku CSV lub kliknij strzałkę, aby wybrać z listy rozwijanej.
   2. W polu **Zapisz jako typ** kliknij strzałkę i wybierz typ pliku z listy rozwijanej.
   3. Aby zapisać tylko wybrane elementy, zaznacz wiersze, a następnie kliknij pole wyboru **Zapisz tylko zaznaczone wiersze** . Aby zapisać wszystkie wyeksportowane listy, wyczyść pole wyboru **Zapisz tylko zaznaczone wiersze** .
   4. Kliknij pozycję **Zapisz**.
      
      ![Eksportuj listę jako plik z wartościami rozdzielanymi przecinkami](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>Help
Możesz użyć menu **Pomoc** , aby wyświetlić dostępną pomoc online do StorSimple Snapshot Manager i programu MMC.

#### <a name="to-view-available-online-help"></a>Aby wyświetlić dostępną pomoc online
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager.
2. W okienku **zakres** kliknij prawym przyciskiem myszy dowolny węzeł lub rozwiń węzeł, a następnie kliknij prawym przyciskiem myszy element w okienku **wyników** , a następnie kliknij polecenie **Pomoc**. 

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [interfejsie użytkownika StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).
* Dowiedz się więcej o [korzystaniu z usługi StorSimple Snapshot Manager do administrowania rozwiązaniem StorSimple](storsimple-snapshot-manager-admin.md).

