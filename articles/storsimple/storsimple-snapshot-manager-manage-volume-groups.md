---
title: StorSimple Snapshot Manager grupy woluminów | Microsoft Docs
description: Opisuje sposób używania przystawki StorSimple Snapshot Manager MMC do tworzenia grup woluminów i zarządzania nimi.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 7a232414-6a28-4b81-bd7b-cf61e28b33d7
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 805ab0e78f0679ce483af3a73203b41132be2a59
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022959"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Tworzenie grup woluminów i zarządzanie nimi za pomocą StorSimple Snapshot Manager
## <a name="overview"></a>Omówienie
Węzeł **grupy woluminów** w okienku **zakres** służy do przypisywania woluminów do grup woluminów, wyświetlania informacji o grupie woluminów, planowania tworzenia kopii zapasowych i edytowania grup woluminów.

Grupy woluminów są pulami powiązanych woluminów używanych do zapewnienia, że kopie zapasowe są spójne z aplikacjami. Aby uzyskać więcej informacji, zobacz [woluminy i grupy woluminów](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) oraz [integrację z systemem Windows Usługa kopiowania woluminów w tle](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

> [!IMPORTANT]
> * Wszystkie woluminy w grupie woluminów muszą pochodzić od jednego dostawcy usług w chmurze.
> * Podczas konfigurowania grup woluminów nie należy mieszać woluminów udostępnionych klastra (CSV) i innych niż CSV w tej samej grupie woluminów. StorSimple Snapshot Manager nie obsługuje kombinacji CSV i innych niż CSV w tej samej migawce.

![Węzeł grupy woluminów](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Rysunek 1: StorSimple Snapshot Manager węzeł grupy woluminów** 

W tym samouczku wyjaśniono, jak można użyć Snapshot Manager StorSimple do:

* Wyświetl informacje o grupach woluminów
* Utwórz grupę woluminów
* Tworzenie kopii zapasowej grupy woluminów
* Edytowanie grupy woluminów
* Usuń grupę woluminów

Wszystkie te akcje są również dostępne w okienku **Akcje** .

## <a name="view-volume-groups"></a>Wyświetl grupy woluminów
Po kliknięciu węzła **grupy woluminów** w okienku **wyników** zostaną wyświetlone następujące informacje dotyczące poszczególnych grup woluminów, w zależności od wybranych opcji. (Kolumny w okienku **wyników** można konfigurować. Kliknij prawym przyciskiem myszy węzeł **woluminy** , wybierz pozycję **Widok**, a następnie wybierz polecenie **Dodaj/Usuń kolumny**.

| Kolumna wyników | Opis |
|:--- |:--- |
| Nazwa |Kolumna **name** zawiera nazwę grupy woluminów. |
| Aplikacja |W kolumnie **aplikacje** jest wyświetlana liczba modułów ZAPISUJĄCYCH usługi VSS aktualnie zainstalowanych i uruchomionych na hoście z systemem Windows. |
| Wybrane |W **zaznaczonej** kolumnie jest wyświetlana liczba woluminów znajdujących się w grupie woluminów. Zero (0) oznacza, że żadna aplikacja nie jest skojarzona z woluminami w grupie woluminów. |
| Importu |**Zaimportowana** kolumna pokazuje liczbę zaimportowanych woluminów. Po ustawieniu na **wartość true** ta kolumna wskazuje, że grupa woluminów została zaimportowana z Azure Portal i nie została utworzona w Snapshot Manager StorSimple. |

> [!NOTE]
> StorSimple Snapshot Manager grupy woluminów są również wyświetlane na karcie **zasady tworzenia kopii zapasowych** w Azure Portal.
> 
> 

## <a name="create-a-volume-group"></a>Utwórz grupę woluminów
Aby utworzyć grupę woluminów, należy wykonać poniższą procedurę.

#### <a name="to-create-a-volume-group"></a>Aby utworzyć grupę woluminów
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager.
2. W okienku **zakres** kliknij prawym przyciskiem myszy pozycję **grupy woluminów**, a następnie kliknij polecenie **Utwórz grupę woluminów**.
   
    ![Utwórz grupę woluminów](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    Zostanie wyświetlone okno dialogowe **Tworzenie grupy woluminów** .
   
    ![Okno dialogowe Tworzenie grupy woluminów](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Wprowadź następujące informacje:
   
   1. W polu **Nazwa** wpisz unikatową nazwę nowej grupy woluminów.
   2. W polu **aplikacje** wybierz pozycję Aplikacje skojarzone z woluminami, które zostaną dodane do grupy woluminów.
      
       W polu **aplikacje** są wyświetlane tylko te aplikacje, które korzystają z woluminów StorSimple i są dla nich włączone składniki zapisywania usługi VSS. Składnik zapisywania usługi VSS jest dostępny tylko wtedy, gdy wszystkie woluminy, których dotyczy składnik zapisywania, są woluminami StorSimple. Jeśli pole aplikacje jest puste, nie są zainstalowane żadne aplikacje używające woluminów StorSimple platformy Azure oraz obsługiwane składniki zapisywania usługi VSS. (Obecnie usługa Azure StorSimple obsługuje program Microsoft Exchange i SQL Server.) Aby uzyskać więcej informacji na temat składników zapisywania usługi VSS, zobacz [integracja z systemem Windows Usługa kopiowania woluminów w tle](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).
      
       W przypadku wybrania aplikacji automatycznie wybierane są wszystkie skojarzone z nią woluminy. Z drugiej strony, jeśli wybierzesz woluminy skojarzone z określoną aplikacją, aplikacja zostanie automatycznie wybrana w polu **aplikacje** . 
   3. W polu **woluminy** wybierz pozycję StorSimple woluminy do dodania do grupy woluminów. 
      
      * Można uwzględnić woluminy z jedną lub wieloma partycjami. (Wiele woluminów partycji może być dyskami dynamicznymi lub dyskami podstawowymi z wieloma partycjami.) Wolumin zawierający wiele partycji jest traktowany jako pojedyncza jednostka. W związku z tym po dodaniu tylko jednej z partycji do grupy woluminów wszystkie pozostałe partycje są automatycznie dodawane do tej grupy woluminów w tym samym czasie. Po dodaniu woluminu z wieloma partycjami do grupy woluminów wolumin z wieloma partycjami będzie nadal traktowany jako pojedyncza jednostka.
      * Puste grupy woluminów można utworzyć, nie przypisując do nich woluminów. 
      * Nie mieszaj woluminów udostępnionych klastra (CSV) i innych niż CSV w tej samej grupie woluminów. StorSimple Snapshot Manager nie obsługuje mieszanki woluminów CSV i woluminów innych niż woluminy CSV w tej samej migawce.
4. Kliknij przycisk **OK** , aby zapisać grupę woluminów.

## <a name="back-up-a-volume-group"></a>Tworzenie kopii zapasowej grupy woluminów
Aby utworzyć kopię zapasową grupy woluminów, wykonaj czynności opisane w poniższej procedurze.

#### <a name="to-back-up-a-volume-group"></a>Aby utworzyć kopię zapasową grupy woluminów
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager.
2. W okienku **zakres** rozwiń węzeł **grupy woluminów** , kliknij prawym przyciskiem myszy nazwę grupy woluminów, a następnie kliknij polecenie Utwórz **kopię zapasową**.
   
    ![Natychmiast wykonaj kopię zapasową grupy woluminów](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. W oknie dialogowym **Wykonaj kopię zapasową** wybierz pozycję **migawka lokalna** lub **migawka w chmurze**, a następnie kliknij pozycję **Utwórz**.
   
    ![Utwórz kopię zapasową okna dialogowego](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. Aby upewnić się, że kopia zapasowa jest uruchomiona, rozwiń węzeł **zadania** , a następnie kliknij przycisk **uruchomione**. Kopia zapasowa powinna być wyświetlana na liście.
5. Aby wyświetlić ukończoną migawkę, rozwiń węzeł **wykaz kopii zapasowych** , rozwiń nazwę grupy woluminów, a następnie kliknij pozycję **migawka lokalna** lub **migawka w chmurze**. Kopia zapasowa będzie wyświetlana, jeśli zakończyła się pomyślnie.

## <a name="edit-a-volume-group"></a>Edytowanie grupy woluminów
Aby edytować grupę woluminów, należy wykonać czynności opisane w poniższej procedurze.

#### <a name="to-edit-a-volume-group"></a>Aby edytować grupę woluminów
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager.
2. W okienku **zakres** rozwiń węzeł **grupy woluminów** , kliknij prawym przyciskiem myszy nazwę grupy woluminów, a następnie kliknij polecenie **Edytuj**.
3. Zostanie wyświetlone okno dialogowe * * Tworzenie grupy woluminów * *. Można zmienić **nazwy**, **aplikacje** i wpisy **woluminów** .
4. Kliknij przycisk **OK**, aby zapisać zmiany.

## <a name="delete-a-volume-group"></a>Usuń grupę woluminów
Aby usunąć grupę woluminów, należy wykonać czynności opisane w poniższej procedurze. 

> [!WARNING]
> Spowoduje to również usunięcie wszystkich kopii zapasowych skojarzonych z grupą woluminów.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Aby usunąć grupę woluminów
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager.
2. W okienku **zakres** rozwiń węzeł **grupy woluminów** , kliknij prawym przyciskiem myszy nazwę grupy woluminów, a następnie kliknij polecenie **Usuń**.
3. Zostanie wyświetlone okno dialogowe **Usuwanie grupy woluminów** . W polu tekstowym wpisz **potwierdzenie** , a następnie kliknij przycisk **OK**.
   
    Usunięta Grupa woluminów znika z listy w okienku **wyników** , a wszystkie kopie zapasowe skojarzone z tą grupą woluminów są usuwane z wykazu kopii zapasowych.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [używać Snapshot Manager StorSimple do administrowania rozwiązaniem StorSimple](storsimple-snapshot-manager-admin.md).
* Dowiedz się, jak [używać Snapshot Manager StorSimple do tworzenia zasad tworzenia kopii zapasowych i zarządzania nimi](storsimple-snapshot-manager-manage-backup-policies.md).

