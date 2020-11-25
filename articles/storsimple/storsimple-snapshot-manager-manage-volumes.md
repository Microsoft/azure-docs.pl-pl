---
title: StorSimple Snapshot Manager i woluminy | Microsoft Docs
description: Opisuje sposób używania przystawki StorSimple Snapshot Manager MMC do wyświetlania woluminów i zarządzania nimi oraz do konfigurowania kopii zapasowych.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: alkohli
ms.openlocfilehash: 309fa85d0a4d877522a89dd8f1e6e71fb2074744
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022942"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Używanie Snapshot Manager StorSimple do wyświetlania woluminów i zarządzania nimi
## <a name="overview"></a>Omówienie
Aby wybrać woluminy i wyświetlić informacje o nich, można użyć węzła StorSimple **woluminy** Snapshot Manager (w okienku **zakres** ). Woluminy są przedstawiane jako dyski odpowiadające woluminom zainstalowanym przez hosta. Węzeł **woluminy** zawiera woluminy lokalne i typy woluminów obsługiwane przez StorSimple, w tym woluminy wykryte za pomocą interfejsu iSCSI i urządzenia. 

Aby uzyskać więcej informacji o obsługiwanych woluminach, przejdź do [pomocy technicznej dla wielu typów woluminów](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Lista woluminów w okienku wyników](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

Węzeł **woluminy** umożliwia także ponowne skanowanie lub usuwanie woluminów po StorSimple Snapshot Manager je odnajduje. 

W tym samouczku wyjaśniono, jak można instalować, inicjować i formatować woluminy, a następnie używać StorSimple Snapshot Manager do:

* Wyświetl informacje o woluminach 
* Usuń woluminy
* Skanuj ponownie woluminy 
* Skonfiguruj wolumin podstawowy i wykonaj jego kopię zapasową
* Skonfiguruj dynamiczny wolumin dublowany i wykonaj jego kopię zapasową

> [!NOTE]
> Wszystkie akcje węzła **woluminu** są również dostępne w okienku **Akcje** .
> 
> 

## <a name="mount-volumes"></a>Zainstaluj woluminy
Użyj następującej procedury, aby zainstalować, zainicjować i sformatować woluminy StorSimple. Ta procedura obejmuje zarządzanie dyskami, Narzędzie systemowe do zarządzania dyskami twardymi oraz odpowiednie woluminy lub partycje. Aby uzyskać więcej informacji na temat zarządzania dyskami, przejdź do obszaru [Zarządzanie dyskami](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770943(v=ws.11)) w witrynie sieci Web Microsoft TechNet.

#### <a name="to-mount-volumes"></a>Aby zainstalować woluminy
1. Na komputerze hosta Uruchom inicjatora iSCSI firmy Microsoft.
2. Podaj jeden z adresów IP interfejsu jako portal docelowy lub adres IP odnajdywania, a następnie połącz się z urządzeniem. Po nawiązaniu połączenia z urządzeniem woluminy będą dostępne dla systemu Windows. Aby uzyskać więcej informacji o korzystaniu z inicjatora iSCSI firmy Microsoft, przejdź do sekcji "łączenie się z urządzeniem docelowym iSCSI" w artykule [Instalowanie i Konfigurowanie inicjatora iSCSI firmy Microsoft][1].
3. Aby uruchomić Zarządzanie dyskami, użyj dowolnej z następujących opcji:
   
   * Wpisz diskmgmt. msc w polu **Run (uruchamianie** ).
   * Rozpocznij Menedżer serwera, rozwiń węzeł **Magazyn** , a następnie wybierz pozycję **Zarządzanie dyskami**.
   * Uruchom **Narzędzia administracyjne**, rozwiń węzeł **Zarządzanie komputerem** , a następnie wybierz pozycję **Zarządzanie dyskami**. 
     
     > [!NOTE]
     > Aby uruchomić Zarządzanie dyskami, należy użyć uprawnień administratora.
     > 
     > 
4. Przełącz woluminy w tryb online:
   
   1. W przystawce Zarządzanie dyskami kliknij prawym przyciskiem myszy dowolny wolumin oznaczony jako **offline**.
   2. Kliknij pozycję **Uaktywnij ponownie dysk**. Dysk należy oznaczyć jako **online** po ponownym uaktywnieniu dysku.
5. Zainicjuj woluminy:
   
   1. Kliknij prawym przyciskiem myszy odnalezione woluminy.
   2. W menu wybierz opcję **zainicjuj dysk**.
   3. W oknie dialogowym **Inicjowanie dysku** Wybierz dyski, które chcesz zainicjować, a następnie kliknij przycisk **OK**.
6. Formatowanie prostych woluminów:
   
   1. Kliknij prawym przyciskiem myszy wolumin, który chcesz sformatować.
   2. Z menu wybierz pozycję **Nowy wolumin prosty**.
   3. Użyj Kreatora nowego woluminu prostego do sformatowania woluminu:
      
      * Określ rozmiar woluminu.
      * Podaj literę dysku.
      * Wybierz system plików NTFS.
      * Określ rozmiar jednostki alokacji 64 KB.
      * Przeprowadź szybkie formatowanie.
7. Sformatuj woluminy wielopartycjowe. Aby uzyskać instrukcje, przejdź do sekcji "partycje i woluminy" podczas [implementowania zarządzania dyskami](/previous-versions/tn-archive/dd163556(v=technet.10)).

## <a name="view-information-about-your-volumes"></a>Wyświetlanie informacji o woluminach
Poniższa procedura umożliwia wyświetlenie informacji na temat lokalnych i StorSimple woluminów platformy Azure.

#### <a name="to-view-volume-information"></a>Aby wyświetlić informacje o woluminie
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager. 
2. W okienku **zakres** kliknij węzeł **woluminy** . W okienku **wyników** zostanie wyświetlona lista woluminów lokalnych i zainstalowanych, w tym wszystkich woluminów StorSimple platformy Azure. Kolumny w okienku **wyników** można konfigurować. (Kliknij prawym przyciskiem myszy węzeł **woluminy** , wybierz pozycję **Widok**, a następnie wybierz polecenie **Dodaj/Usuń kolumny**).
   
    ![Konfiguruj kolumny](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Kolumna wyników | Opis |
   |:--- |:--- |
   |  Nazwa |Kolumna **name** zawiera literę dysku przypisaną do każdego wykrytego woluminu. |
   |  Urządzenie |Kolumna **Device** zawiera adres IP urządzenia podłączonego do komputera hosta. |
   |  Nazwa woluminu urządzenia |Kolumna **Nazwa woluminu urządzenia** zawiera nazwę woluminu urządzenia, do którego należy wybrany wolumin. Jest to nazwa woluminu zdefiniowana w Azure Portal dla tego określonego woluminu. |
   |  Ścieżki dostępu |W kolumnie **ścieżki dostępu** jest wyświetlana ścieżka dostępu do woluminu. Jest to litera dysku lub punkt instalacji, w którym wolumin jest dostępny na komputerze-hoście. |

## <a name="delete-a-volume"></a>Usuwanie woluminu
Aby usunąć wolumin z StorSimple Snapshot Manager, należy wykonać czynności opisane w poniższej procedurze.

> [!NOTE]
> Woluminu nie można usunąć, jeśli jest częścią dowolnej grupy woluminów. (Opcja usuwania jest niedostępna dla woluminów, które są elementami członkowskimi grupy woluminów). Aby usunąć wolumin, należy usunąć całą grupę woluminów.

#### <a name="to-delete-a-volume"></a>Aby usunąć wolumin
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager.
2. W okienku **zakres** kliknij węzeł **woluminy** . 
3. W okienku **wyników** kliknij prawym przyciskiem myszy wolumin, który chcesz usunąć.
4. W menu kliknij pozycję **Usuń**. 
   
    ![Usuwanie woluminu](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. Zostanie wyświetlone okno dialogowe **Usuwanie woluminu** . W polu tekstowym wpisz **potwierdzenie** , a następnie kliknij przycisk **OK**.
6. Domyślnie program StorSimple Snapshot Manager kopię zapasową woluminu przed jego usunięciem. Dzięki temu można ochronić się przed utratą danych w przypadku niezamierzonego usunięcia. StorSimple Snapshot Manager wyświetla komunikat **automatycznego postępu migawki** podczas tworzenia kopii zapasowej woluminu. 
   
    ![Automatyczny komunikat migawki](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Skanuj ponownie woluminy
Aby ponownie przeskanować woluminy połączone z StorSimple Snapshot Manager, wykonaj czynności opisane w poniższej procedurze.

#### <a name="to-rescan-the-volumes"></a>Aby ponownie przeskanować woluminy
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager.
2. W okienku **zakres** kliknij prawym przyciskiem myszy pozycję **woluminy**, a następnie kliknij polecenie **Skanuj woluminy** ponownie.
   
    ![Skanuj ponownie woluminy](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Ta procedura służy do synchronizowania listy woluminów z StorSimple Snapshot Manager. Wszelkie zmiany, takie jak nowe woluminy lub usunięte woluminy, zostaną odzwierciedlone w wynikach.

## <a name="configure-and-back-up-a-basic-volume"></a>Konfigurowanie i tworzenie kopii zapasowej woluminu podstawowego
Poniższa procedura służy do konfigurowania kopii zapasowej woluminu podstawowego, a następnie natychmiastowego rozpoczęcia tworzenia kopii zapasowej lub tworzenia zasad dla zaplanowanych kopii zapasowych.

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem:

* Upewnij się, że urządzenie StorSimple i komputer hosta są prawidłowo skonfigurowane. Aby uzyskać więcej informacji, przejdź do obszaru [wdrażanie lokalnego urządzenia StorSimple](./storsimple-8000-deployment-walkthrough-u2.md).
* Zainstaluj i skonfiguruj Snapshot Manager StorSimple. Aby uzyskać więcej informacji, przejdź do pozycji [Wdróż StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Aby skonfigurować kopię zapasową woluminu podstawowego
1. Utwórz wolumin podstawowy na urządzeniu StorSimple.
2. Instalowanie, inicjowanie i formatowanie woluminu zgodnie z opisem w artykule [woluminy instalacji](#mount-volumes). 
3. Kliknij ikonę Snapshot Manager StorSimple na pulpicie. Zostanie wyświetlone okno StorSimple Snapshot Manager. 
4. W okienku **zakres** kliknij prawym przyciskiem myszy węzeł **woluminy** , a następnie wybierz polecenie **Skanuj woluminy** ponownie. Po zakończeniu skanowania w okienku **wyników** powinna zostać wyświetlona lista woluminów. 
5. W okienku **wyników** kliknij prawym przyciskiem myszy wolumin, a następnie wybierz polecenie **Utwórz grupę woluminów**. 
   
    ![Utwórz grupę woluminów](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. W oknie dialogowym **Utwórz grupę woluminów** wpisz nazwę grupy woluminów, przypisz do niej woluminy, a następnie kliknij przycisk **OK**.
7. W okienku **zakres** rozwiń węzeł **grupy woluminów** . Nowa grupa woluminów powinna zostać wyświetlona w węźle **grupy woluminów** . 
8. Kliknij prawym przyciskiem myszy nazwę grupy woluminów.
   
   * Aby uruchomić interaktywne zadanie tworzenia kopii zapasowej (na żądanie), kliknij przycisk **Utwórz kopię zapasową**. 
   * Aby zaplanować automatyczne tworzenie kopii zapasowej, kliknij pozycję **Utwórz zasady tworzenia kopii zapasowych**. Na stronie **Ogólne** wybierz grupę woluminów z listy. Na stronie **harmonogram** wprowadź szczegóły harmonogramu. Po zakończeniu kliknij przycisk **OK**. 
9. Aby upewnić się, że zadanie tworzenia kopii zapasowej zostało uruchomione, rozwiń węzeł **zadania** w okienku **zakres** , a następnie kliknij **uruchomiony** węzeł. Lista aktualnie uruchomionych zadań pojawia się w okienku **wyników** . 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Konfigurowanie i tworzenie kopii zapasowej dynamicznego woluminu dublowanego
Wykonaj następujące kroki, aby skonfigurować kopię zapasową dynamicznego woluminu dublowanego:

* Krok 1. Tworzenie dynamicznego woluminu dublowanego za pomocą przystawki Zarządzanie dyskami. 
* Krok 2. Aby skonfigurować kopię zapasową, użyj Snapshot Manager StorSimple.

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem:

* Upewnij się, że urządzenie StorSimple i komputer hosta są prawidłowo skonfigurowane. Aby uzyskać więcej informacji, przejdź do obszaru [wdrażanie lokalnego urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Zainstaluj i skonfiguruj Snapshot Manager StorSimple. Aby uzyskać więcej informacji, przejdź do pozycji [Wdróż StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
* Skonfiguruj dwa woluminy na urządzeniu StorSimple. (W przykładach dostępne woluminy to **dysk 1** i **dysk 2**). 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Krok 1. Tworzenie dynamicznego woluminu dublowanego za pomocą przystawki Zarządzanie dyskami
Zarządzanie dyskami to narzędzie systemowe do zarządzania dyskami twardymi oraz woluminy lub partycje, które zawierają. Aby uzyskać więcej informacji na temat zarządzania dyskami, przejdź do obszaru [Zarządzanie dyskami](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770943(v=ws.11)) w witrynie sieci Web Microsoft TechNet.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Aby utworzyć dynamiczny wolumin dublowany
1. Aby uruchomić Zarządzanie dyskami, użyj dowolnej z następujących opcji: 
   
   * Otwórz pole **Run** , wpisz **diskmgmt. msc**, a następnie naciśnij klawisz ENTER.
   * Rozpocznij Menedżer serwera, rozwiń węzeł **Magazyn** , a następnie wybierz pozycję **Zarządzanie dyskami**. 
   * Uruchom **Narzędzia administracyjne**, rozwiń węzeł **Zarządzanie komputerem** , a następnie wybierz pozycję **Zarządzanie dyskami**. 
2. Upewnij się, że na urządzeniu StorSimple są dostępne dwa woluminy. (W tym przykładzie dostępne woluminy to **dysk 1** i **dysk 2**). 
3. W oknie Zarządzanie dyskami w prawej kolumnie dolnego okienka kliknij prawym przyciskiem myszy **dysk 1** i wybierz polecenie **Nowy wolumin dublowany**. 
   
    ![Nowy wolumin dublowany](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. Na stronie Kreator **nowej woluminu dublowanego** kliknij przycisk **dalej**.
5. Na stronie **Wybierz dyski** wybierz pozycję **dysk 2** w **wybranym** okienku, kliknij przycisk **Dodaj**, a następnie kliknij przycisk **dalej**. 
6. Na stronie **Przypisz literę dysku lub ścieżkę** zaakceptuj wartości domyślne, a następnie kliknij przycisk **dalej**. 
7. Na stronie **Format woluminu** w polu **rozmiar jednostki alokacji** wybierz pozycję **64 KB**. Zaznacz pole wyboru **Wykonaj szybkie formatowanie** , a następnie kliknij przycisk **dalej**. 
8. Na stronie **Kończenie nowego woluminu dublowanego** przejrzyj ustawienia, a następnie kliknij przycisk **Zakończ**. 
9. Zostanie wyświetlony komunikat informujący o tym, że dysk podstawowy zostanie przekonwertowany na dysk dynamiczny. Kliknij przycisk **Yes** (Tak).
   
    ![Komunikat dotyczący konwersji dysku dynamicznego](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. W przystawce Zarządzanie dyskami Sprawdź, czy dysk 1 i dysk 2 są wyświetlane jako woluminy dynamiczne dublowane. (**Dynamiczny** powinien pojawić się w kolumnie Stan, a kolor paska dyspozycyjności powinien zmienić na czerwony, wskazując wolumin dublowany). 
    
    ![Woluminy dublowane zarządzania dyskami dynamicznymi](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Krok 2. Konfigurowanie kopii zapasowej za pomocą Snapshot Manager StorSimple
Aby skonfigurować dynamiczny wolumin dublowany, należy wykonać czynności opisane w poniższej procedurze lub utworzyć zasady dla zaplanowanych kopii zapasowych.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Aby skonfigurować kopię zapasową dynamicznego woluminu dublowanego
1. Kliknij ikonę Snapshot Manager StorSimple na pulpicie. Zostanie wyświetlone okno StorSimple Snapshot Manager. 
2. W okienku **zakres** kliknij prawym przyciskiem myszy węzeł **woluminy** , a następnie wybierz polecenie **Skanuj woluminy** ponownie. Po zakończeniu skanowania w okienku **wyników** powinna zostać wyświetlona lista woluminów. Dynamiczny wolumin dublowany jest wymieniony jako pojedynczy wolumin. 
3. W okienku **wyników** kliknij prawym przyciskiem myszy dynamiczny wolumin dublowany, a następnie kliknij polecenie **Utwórz grupę woluminów**. 
4. W oknie dialogowym **Utwórz grupę woluminów** wpisz nazwę grupy woluminów, przypisz dynamiczny wolumin dublowany do tej grupy, a następnie kliknij przycisk **OK**. 
5. W okienku **zakres** rozwiń węzeł **grupy woluminów** . Nowa grupa woluminów powinna zostać wyświetlona w węźle  **grupy woluminów** . 
6. Kliknij prawym przyciskiem myszy nazwę grupy woluminów. 
   
   * Aby uruchomić interaktywne zadanie tworzenia kopii zapasowej (na żądanie), kliknij przycisk **Utwórz kopię zapasową**. 
   * Aby zaplanować automatyczne tworzenie kopii zapasowej, kliknij pozycję **Utwórz zasady tworzenia kopii zapasowych**. Na stronie **Ogólne** wybierz grupę woluminów z listy. Na stronie **harmonogram** wprowadź szczegóły harmonogramu. Po zakończeniu kliknij przycisk **OK**. 
7. Zadanie tworzenia kopii zapasowej można monitorować w trakcie jego działania. W okienku **zakres** rozwiń węzeł **zadania** , a następnie kliknij przycisk **uruchomione**. Szczegóły zadania są wyświetlane w okienku **wyników** . Po zakończeniu zadania tworzenia kopii zapasowej szczegóły są przesyłane do listy zadań z **ostatnich 24** godzin. 

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [używać Snapshot Manager StorSimple do administrowania rozwiązaniem StorSimple](storsimple-snapshot-manager-admin.md).
* Dowiedz się [, jak tworzyć grupy woluminów i zarządzać nimi za pomocą Snapshot Manager StorSimple](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee338480(v=ws.10)