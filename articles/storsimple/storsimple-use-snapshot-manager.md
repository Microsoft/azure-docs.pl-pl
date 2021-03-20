---
title: Interfejs użytkownika StorSimple Snapshot Manager | Microsoft Docs
description: Opisuje interfejs użytkownika programu StorSimple Snapshot Manager i wyjaśnia, jak go używać do zarządzania zadaniami tworzenia kopii zapasowych i wykazem kopii zapasowych.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 35fa0fc134045585eebd21324352f9b4ff272e87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954243"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>Korzystanie z StorSimple Snapshot Manager interfejsu użytkownika do zarządzania zadaniami tworzenia kopii zapasowych i wykazem kopii zapasowych

## <a name="overview"></a>Omówienie
StorSimple Snapshot Manager ma intuicyjny interfejs użytkownika, za pomocą którego można wykonywać kopie zapasowe i zarządzać nimi. Ten samouczek zawiera wprowadzenie do interfejsu użytkownika, a następnie wyjaśnia, jak używać poszczególnych składników programu. Aby uzyskać szczegółowy opis Snapshot Manager StorSimple, zobacz [co to jest StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Opis konsoli
Aby wyświetlić interfejs użytkownika, kliknij ikonę StorSimple Snapshot Manager na pulpicie. Zostanie wyświetlone okno konsoli, jak pokazano na poniższej ilustracji.

![Okienka Snapshot Manager StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

Okno konsoli ma pięć głównych elementów. Kliknij odpowiedni link, aby uzyskać pełny opis każdego elementu.

* [Pasek menu](#menu-bar) 
* [Pasek narzędzi](#tool-bar) 
* [Okienko zakresu](#scope-pane) 
* [Okienko wyników](#results-pane) 
* [Okienko akcje](#actions-pane) 

Ponadto Snapshot Manager StorSimple obsługuje [nawigację klawiaturową i wiele skrótów](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Dostępność konsoli
Interfejs użytkownika programu StorSimple Snapshot Manager obsługuje funkcje ułatwień dostępu udostępniane przez system operacyjny Windows i program Microsoft Management Console (MMC), a także niektóre skróty klawiaturowe, które są specyficzne dla Snapshot Manager StorSimple. 

* Aby uzyskać opis funkcji ułatwień dostępu systemu Windows, przejdź do pozycji [skróty klawiaturowe dla systemu Windows](https://support.microsoft.com/kb/126449). 
* Aby uzyskać opis funkcji ułatwień dostępu programu MMC, przejdź do obszaru [dostępność dla programu mmc 3,0](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc766075(v=ws.11))
* Opis funkcji ułatwień dostępu w programie StorSimple Snapshot Manager można znaleźć na stronie [Nawigacja i skróty klawiaturowe](#keyboard-navigation-and-shortcuts).

## <a name="menu-bar"></a>Pasek menu
Pasek menu w górnej części okna konsoli zawiera menu [plik](#file-menu), [Akcja](#action-menu), [Widok](#view-menu), [Ulubione](#favorites-menu), [okno](#window-menu)i [Pomoc](#help-menu) .

Kliknij dowolny element na pasku menu, aby wyświetlić listę dostępnych poleceń w tym menu. Poniższy przykład pokazuje menu **Widok** wybrane na pasku menu.

![Wybrano menu Widok](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Menu Plik
Menu **plik** zawiera standardowe polecenia programu Microsoft Management Console (MMC).

#### <a name="menu-access"></a>Dostęp do menu
Aby wyświetlić menu **plik** , kliknij przycisk **plik** na pasku menu. Zostanie wyświetlone następujące menu.

![Menu pliku Snapshot Manager StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Opis menu
W poniższej tabeli opisano elementy, które pojawiają się w menu **plik** .

| Element menu | Opis |
|:--- |:--- |
| Nowy |Kliknij pozycję **Nowy** , aby utworzyć nową konsolę opartą na StorSimple Snapshot Manager. |
| Otwórz |Kliknij przycisk **Otwórz** , aby otworzyć istniejącą konsolę. |
| Zapisz |Kliknij przycisk **Zapisz** , aby zapisać bieżącą konsolę. |
| Zapisz jako |Kliknij pozycję **Zapisz jako** , aby utworzyć nowe, nazwane wystąpienie bieżącej konsoli. Użyj opcji **Zapisz jako** , aby dostosować widok i zapisać go do późniejszego pobrania. Można na przykład utworzyć przystawki Snapshot Manager StorSimple wskazujące określone serwery. |
| Dodaj/Usuń przystawkę |Kliknij przycisk **Dodaj/Usuń przystawkę** , aby dodać lub usunąć przystawki i zorganizować węzły w okienku **zakres** . Aby uzyskać więcej informacji, przejdź do pozycji [Dodawanie, usuwanie i organizowanie przystawek i rozszerzeń w programie MMC 3,0](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc722035(v=ws.11)). |
| Opcje |Kliknij przycisk **Opcje** , aby zmienić ikonę konsoli, określ tryby dostępu użytkowników i uprawnienia lub Usuń pliki konsoli, aby zwiększyć ilość dostępnego miejsca na dysku. |
| Lista ścieżek plików |Kliknij ścieżkę na liście numerowanej, aby ponownie otworzyć ostatnio otwarty plik. |
| Zakończ |Kliknij przycisk **Zakończ** , aby zamknąć menu **plik** . |

### <a name="action-menu"></a>Menu akcji
Użyj menu **Akcja** , aby wybrać spośród dostępnych akcji. Elementy dostępne dla użytkownika zależą od wyboru dokonanego w okienku **zakres** lub w okienku **wyników** .

#### <a name="menu-access"></a>Dostęp do menu
Aby wyświetlić menu **Akcja** , wykonaj jedną z następujących czynności:

* Kliknij prawym przyciskiem myszy element w okienku **zakres** lub w okienku **wyników** .
* Wybierz element w okienku **zakres** lub w okienku **wyników** , a następnie kliknij pozycję **Akcja** na pasku menu. 

Na przykład, jeśli wybierzesz górny węzeł w okienku **zakres** , a następnie kliknij prawym przyciskiem myszy lub kliknij pozycję **Akcja** na pasku menu, pojawi się następujące menu.

![Menu akcji Snapshot Manager StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

Okienko **Akcje** (po prawej stronie konsoli) zawiera tę samą listę akcji, co menu **Akcja** . Ponadto okienko **Akcje** zawiera opcje menu **Widok** , które umożliwiają tworzenie widoku niestandardowego okienka **wyników** .

![Okienko akcji z otwartym menu Widok](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Opis menu
Poniższa tabela zawiera alfabetyczną listę akcji StorSimple Snapshot Manager. 

* Kolumna **akcji** zawiera listę akcji, które można wykonywać na węzłach i wynikach. 
* W kolumnie **nawigacji** wyjaśniono, jak wyświetlić odpowiednie menu **akcji** , aby można było wybrać akcję. Niektóre akcje są wyświetlane w wielu menu **akcji** . Dla tych akcji wybierz jedną opcję **nawigacji** z listy punktowanej. 
* W kolumnie **Opis** opisano, jak używać poszczególnych akcji w menu **akcji** lub w okienku Akcje i wyjaśniono, co robi.

> [!NOTE]
> Okienko **Akcje** i menu **akcji** zawierają dodatkowe opcje, takie jak **Widok**, **nowe okno z tego miejsca**, **odświeżanie**, **Eksportowanie listy** i **Pomoc**. Te opcje są dostępne jako część programu MMC i nie są specyficzne dla StorSimple Snapshot Manager. Tabela zawiera opisy tych opcji.
> 
> 

| Akcja | Nawigacja | Opis |
|:--- |:--- |:--- |
| Uwierzytelnianie |Kliknij węzeł **urządzenia** , a następnie kliknij prawym przyciskiem myszy urządzenie w okienku **wyników** . |Kliknij przycisk **Uwierzytelnij** , aby wprowadzić hasło skonfigurowane dla urządzenia. |
| Klonowanie |Rozwiń węzeł **wykaz kopii zapasowych**, rozwiń węzeł **migawki w chmurze**, kliknij datowaną kopię zapasową, a następnie wybierz wolumin w okienku **wyników** . |Kliknij przycisk **Klonuj** , aby utworzyć kopię migawki w chmurze i zapisać ją w wyznaczonej lokalizacji. |
| Konfigurowanie urządzenia |Kliknij prawym przyciskiem myszy węzeł **urządzenia** . |Kliknij pozycję **Konfiguruj urządzenie** , aby skonfigurować jedno urządzenie lub wiele urządzeń w celu nawiązania połączenia z hostem systemu Windows. |
| Tworzenie zasad kopii zapasowych |Wykonaj jedną z następujących czynności:<ul><li>Kliknij prawym przyciskiem myszy pozycję **zasady tworzenia kopii zapasowych**.</li><li>Kliknij lub rozwiń pozycję **grupy woluminów**, a następnie kliknij prawym przyciskiem myszy grupę woluminów.</li><li>Kliknij lub rozwiń pozycję **wykaz kopii zapasowych**, a następnie kliknij prawym przyciskiem myszy grupę woluminów.</li></ul> |Kliknij pozycję **Utwórz zasady tworzenia kopii zapasowych** , aby skonfigurować zaplanowaną kopię zapasową dla grupy woluminów. |
| Utwórz grupę woluminów |Wykonaj jedną z następujących czynności:<ul><li>Kliknij węzeł **woluminy** , a następnie kliknij prawym przyciskiem myszy wolumin w okienku **wyników** .</li><li>Kliknij prawym przyciskiem myszy węzeł **grupy woluminów** .</li></ul> |Kliknij pozycję **Utwórz grupę woluminów** , aby przypisać woluminy do grupy woluminów. |
| Usuń |Kliknij węzeł lub wynik (ten element jest wyświetlany w wielu menu **akcji** i w okienkach  **Akcje** ). |Kliknij przycisk **Usuń** , aby usunąć wybrany węzeł lub wynik. Gdy pojawi się okno dialogowe potwierdzenia, Potwierdź lub Anuluj usunięcie. |
| Szczegóły |Kliknij węzeł **urządzenia** , a następnie kliknij prawym przyciskiem myszy urządzenie w okienku **wyników** . |Kliknij przycisk **szczegóły** , aby wyświetlić szczegóły konfiguracji urządzenia. |
| Edytuj |Kliknij pozycję **zasady tworzenia kopii zapasowych**, a następnie kliknij prawym przyciskiem myszy zasady w okienku **wyników** . |Kliknij przycisk **Edytuj** , aby zmienić harmonogram tworzenia kopii zapasowych dla grupy woluminów. |
| Eksportuj listę |Kliknij dowolny węzeł lub wynik (ten element pojawia się we wszystkich menu **akcji** i w okienkach **Akcje** ). |Kliknij przycisk **Eksportuj listę** , aby zapisać listę w pliku z wartościami rozdzielanymi przecinkami (CSV). Następnie można zaimportować ten plik do aplikacji arkusza kalkulacyjnego do analizy. |
| Help |Kliknij dowolny węzeł lub wynik. (Ten element pojawia się we wszystkich menu **akcji** i w okienkach **Akcje** ). |Kliknij przycisk **Pomoc** , aby otworzyć Pomoc online w osobnym oknie przeglądarki. |
| Nowe okno w tym miejscu |Kliknij dowolny węzeł lub wynik (ten element pojawia się we wszystkich menu **akcji** i w okienkach **Akcje** ). |Kliknij pozycję **nowe okno w tym miejscu** , aby otworzyć nowe okno Snapshot Manager StorSimple. |
| Odśwież |Kliknij dowolny węzeł lub wynik (ten element pojawia się we wszystkich menu **akcji** i w okienkach **Akcje** ). |Kliknij przycisk **Odśwież** , aby zaktualizować aktualnie wyświetlane okno StorSimple Snapshot Manager. |
| Odśwież urządzenie |Kliknij węzeł **urządzenia** , a następnie kliknij prawym przyciskiem myszy urządzenie w okienku **wyników** . |Kliknij przycisk **Odśwież urządzenie** , aby zsynchronizować określone podłączone urządzenie z StorSimple Snapshot Manager. |
| Odświeżanie urządzeń |Kliknij prawym przyciskiem myszy węzeł **urządzenia** . |Kliknij przycisk **Odśwież urządzenia** , aby zsynchronizować listę połączonych urządzeń z StorSimple Snapshot Manager. |
| Skanuj ponownie woluminy |Kliknij prawym przyciskiem myszy węzeł **woluminy** . |Kliknij pozycję **Skanuj ponownie woluminy** , aby zaktualizować listę woluminów, które pojawiają się w okienku **wyników** . |
| Przywracanie |Rozwiń węzeł **wykaz kopii zapasowych**, rozwiń grupę woluminów, rozwiń węzeł **migawki lokalne** lub **migawki w chmurze**, a następnie kliknij prawym przyciskiem myszy kopię zapasową. |Kliknij przycisk **Przywróć** , aby zastąpić bieżące dane grupy woluminów danymi z wybranej kopii zapasowej. |
| Utwórz kopię zapasową |Wykonaj jedną z następujących czynności:<ul><li>Rozwiń węzeł **grupy woluminów**, a następnie kliknij prawym przyciskiem myszy grupę woluminów.</li><li>Rozwiń węzeł **wykaz kopii zapasowych**, a następnie kliknij prawym przyciskiem myszy grupę woluminów.</li></ul> |Kliknij pozycję **Utwórz kopię zapasową** , aby natychmiast uruchomić zadanie tworzenia kopii zapasowej. |
| Przełącz wyświetlanie importów |Kliknij prawym przyciskiem myszy górny węzeł w okienku **zakres** ( **StorSimple Snapshot Manager** węzła w przykładach). |Kliknij pozycję **Przełącz Importy** , aby wyświetlić lub ukryć grupy woluminów i skojarzone kopie zapasowe, które zostały zaimportowane z pulpitu nawigacyjnego usługi StorSimple Device Manager. |

### <a name="view-menu"></a>Menu Widok
Użyj menu **Widok** , aby utworzyć niestandardowy widok zawartości okienka **wyników** . Menu **Widok** zawiera **Dodawanie/usuwanie kolumn** i **Dostosowywanie** opcji.

#### <a name="menu-access"></a>Dostęp do menu
Możesz uzyskać dostęp do menu **Widok** na pasku menu lub w okienku **Akcje** .

![StorSimple — menu widoku Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Opis menu
W poniższej tabeli opisano elementy, które pojawiają się w menu **Widok** .

| Element menu | Opis |
|:--- |:--- |
| Dodaj/Usuń kolumny |Kliknij przycisk **Dodaj/Usuń kolumny** , aby dodać lub usunąć kolumny w okienku **wyników** . |
| Dostosowywanie |Kliknij przycisk **Dostosuj** , aby pokazać lub ukryć elementy w oknie konsoli Snapshot Manager StorSimple. |

### <a name="favorites-menu"></a>Menu Ulubione
Za pomocą menu **Ulubione** można dodawać, usuwać i organizować często używane widoki stron i zadania. 

#### <a name="menu-access"></a>Dostęp do menu
Możesz uzyskać dostęp do menu **Ulubione** na pasku menu.

![Menu StorSimple Snapshot Manager Ulubione](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Opis menu
W poniższej tabeli opisano elementy, które pojawiają się w menu **Ulubione** .

| Element menu | Opis |
|:--- |:--- |
| Dodaj do ulubionych |Kliknij przycisk **Dodaj do ulubionych** , aby dodać bieżący widok do listy ulubionych. |
| Organizuj ulubione |Kliknij przycisk **Organizuj ulubione** , aby zorganizować zawartość folderu Ulubione. |

### <a name="window-menu"></a>Menu okno
Za pomocą menu **okno** można dodawać i zmieniać rozmieszczenie StorSimple Snapshot Manager konsoli programu.

#### <a name="menu-access"></a>Dostęp do menu
Możesz uzyskać dostęp do menu **okna** na pasku menu.

![StorSimple menu okna Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

Lista numerowana u dołu menu zawiera okna, które są aktualnie otwarte. Kliknij dowolne okno na tej liście, aby przenieść okno na pierwszy plan. 

#### <a name="menu-description"></a>Opis menu
W poniższej tabeli opisano elementy, które pojawiają się w menu okno.

| Element menu | Opis |
|:--- |:--- |
| Nowe okno |Kliknij przycisk **nowe okno** , aby otworzyć nowe okno konsoli (oprócz istniejącego okna). |
| Cascade |Kliknij przycisk **Kaskada** , aby wyświetlić otwarte okna konsoli w stylu kaskadowym. |
| Kafelek w poziomie |Kliknij przycisk **sąsiadująco w poziomie** , aby wyświetlić otwarte okna konsoli w formacie kafelka (lub siatki). |
| Rozmieść ikony |Jeśli masz wiele okien konsoli otwartych i rozpraszasz na pulpicie, Zminimalizuj je, a następnie kliknij przycisk **Rozmieść ikony** , aby rozmieścić je w poziomym wierszu w dolnej części ekranu. |

### <a name="help-menu"></a>Menu Pomoc
Skorzystaj z menu **Pomoc** , aby wyświetlić dostępną pomoc online do StorSimple Snapshot Manager i programu MMC. Możesz również wyświetlić informacje o wersjach oprogramowania MMC i StorSimple Snapshot Manager zainstalowanych obecnie w systemie. 

Dostęp do menu **Pomoc** można uzyskać na pasku menu. Możesz również uzyskać dostęp do tematów pomocy StorSimple Snapshot Manager z okienka **Akcje** .

![StorSimple — menu Pomoc Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Opis menu
W poniższej tabeli opisano elementy, które pojawiają się w menu Pomoc.

| Element menu | Opis |
|:--- |:--- |
| Pomoc na temat StorSimple Snapshot Manager |Kliknij pozycję **Pomoc na StorSimple Snapshot Manager** , aby otworzyć Snapshot Manager pomoc w osobnym oknie. |
| Tematy pomocy |Kliknij przycisk **Tematy pomocy** , aby otworzyć Pomoc online programu MMC w osobnym oknie. |
| Witryna sieci Web TechCenter |Kliknij pozycję **Witryna sieci Web TechCenter** , aby otworzyć stronę główną centrum Tech. Microsoft TechNet w osobnym oknie. |
| Informacje o programie Microsoft Management Console |Kliknij pozycję **Informacje o programie Microsoft Management Console** , aby sprawdzić, która wersja programu Microsoft Management Console została zainstalowana w systemie. |
| Informacje o StorSimple Snapshot Manager |Kliknij pozycję **informacje StorSimple Snapshot Manager** , aby sprawdzić, która wersja przystawki jest zainstalowana w systemie. |

## <a name="tool-bar"></a>Pasek narzędzi
Pasek narzędzi, znajdujący się pod paskiem menu, zawiera ikony nawigacyjne i zadania. Każda ikona jest skrótem do określonego zadania.

### <a name="icon-descriptions"></a>Opisy ikon
W poniższej tabeli opisano ikony, które pojawiają się na pasku narzędzi. 

| Ikona | Opis |
|:--- |:--- |
| ![Strzałka w lewo](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Kliknij ikonę strzałki w lewo, aby wrócić do poprzedniej strony. |
| ![Strzałka w prawo](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Kliknij strzałkę w prawo, aby przejść do następnej strony (jeśli strzałka jest szara, akcja jest niedostępna). |
| ![Ikona góry](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |Kliknij ikonę do góry, aby przejść w górę o jeden poziom w drzewie konsoli (okienko **zakres** ). |
| ![Pokaż/Ukryj drzewo konsoli](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Kliknij ikonę Pokaż/Ukryj drzewo konsoli, aby wyświetlić lub ukryć okienko **zakres** . |
| ![Eksportuj listę](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Kliknij ikonę Eksportuj listę, aby wyeksportować listę do określonego pliku CSV. |
| ![Ikona pomocy](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Kliknij ikonę Pomoc, aby otworzyć temat pomocy programu MMC online. |
| ![Okienko Pokaż/Ukryj akcje](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |Kliknij ikonę okienka Pokaż/Ukryj **Akcje** , aby wyświetlić lub ukryć okienko **Akcje** . |

## <a name="scope-pane"></a>Okienko zakresu
Okienko **zakres** jest okienkiem z lewej strony w interfejsie użytkownika StorSimple Snapshot Manager. Zawiera drzewo konsoli (lub węzła) i jest głównym mechanizmem nawigacji dla StorSimple Snapshot Manager. 

### <a name="scope-pane-structure"></a>Struktura okienka zakresu
Okienko **zakres** zawiera serię obiektów, które są klikane (węzły) uporządkowane w strukturze drzewa. 

![Okienko zakresu](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Aby rozwinąć lub zwinąć węzeł, kliknij ikonę strzałki obok nazwy węzła.
* Aby wyświetlić stan lub zawartość węzła, kliknij nazwę węzła. Informacje są wyświetlane w okienku **wyników** . 

Okienko **zakres** zawiera następujące węzły: 

* [Węzeł urządzeń](#devices-node) 
* [Węzeł woluminów](#volumes-node) 
* [Węzeł grupy woluminów](#volume-groups-node) 
* [Węzeł zasad kopii zapasowych](#backup-policies-node) 
* [Węzeł katalogu kopii zapasowych](#backup-catalog-node) 
* [Węzeł zadań](#jobs-node) 

### <a name="scope-pane-tasks"></a>Zadania okienka zakresu
Możesz użyć okienka **zakres** , aby ukończyć akcję w określonym węźle. Aby wybrać zadanie, wykonaj jedną z następujących czynności:

* Kliknij prawym przyciskiem myszy węzeł, a następnie wybierz zadanie z wyświetlonego menu.
* Kliknij węzeł, a następnie kliknij pozycję **Akcja** na pasku menu. Wybierz zadanie z wyświetlonego menu.
* Kliknij węzeł, a następnie wybierz akcję w okienku **Akcje** .

Po wybraniu węzła i użyciu dowolnej z tych metod, aby wyświetlić listę zadań, wyświetlane są tylko te akcje, które można wykonać w tym węźle.

### <a name="devices-node"></a>Węzeł urządzeń
Węzeł **urządzenia** reprezentuje urządzenia StorSimple i StorSimple urządzenia wirtualne, które są połączone z usługą StorSimple Snapshot Manager. Wybierz ten węzeł, aby nawiązać połączenie i skonfigurować urządzenie oraz zaimportować skojarzone z nim woluminy, grupy woluminów i istniejące kopie zapasowe. Do jednego hosta można podłączyć wiele urządzeń.

* Aby rozwinąć węzeł, kliknij ikonę strzałki obok pozycji **urządzenia**.
* Aby wyświetlić menu dostępne akcje, kliknij prawym przyciskiem myszy węzeł **urządzenia** lub kliknij prawym przyciskiem myszy dowolny węzeł, który pojawia się w widoku rozszerzonym.
* Aby wyświetlić listę skonfigurowanych urządzeń, kliknij pozycję **urządzenia** w okienku **zakres** . Lista urządzeń wraz z informacjami o poszczególnych urządzeniach zostanie wyświetlona w okienku **wyników** .

### <a name="volumes-node"></a>Węzeł woluminów
Węzeł **woluminy** reprezentuje dyski odpowiadające woluminom zainstalowanym przez hosta, w tym odnajdywane za pomocą interfejsu iSCSI i odnajdywane za pomocą urządzenia. Ten węzeł umożliwia wyświetlenie listy dostępnych woluminów i przypisanie poszczególnych woluminów do grup woluminów.

* Aby rozwinąć węzeł, kliknij ikonę strzałki obok pozycji **woluminy**.
* Aby wyświetlić menu dostępne akcje, kliknij prawym przyciskiem myszy węzeł **woluminy** lub kliknij prawym przyciskiem myszy dowolny węzeł, który pojawia się w widoku rozszerzonym.
* Aby wyświetlić listę woluminów, kliknij pozycję **woluminy** w okienku **zakres** . W okienku **wyników** zostanie wyświetlona lista woluminów wraz z informacjami o każdym woluminie.

### <a name="volume-groups-node"></a>Węzeł grupy woluminów
Grupy woluminów są również znane jako grupy spójności. Każda grupa woluminów jest pulą woluminów związanych z aplikacją, które pomagają zapewnić spójność aplikacji podczas operacji tworzenia kopii zapasowej. Węzeł **grupy woluminów** służy do konfigurowania tych grup i wykonywania interakcyjnych kopii zapasowych oraz tworzenia harmonogramów tworzenia kopii zapasowych. 

* Aby rozwinąć węzeł, kliknij ikonę strzałki obok pozycji **grupy woluminów**.
* Aby wyświetlić menu dostępne akcje, kliknij prawym przyciskiem myszy węzeł **grupy woluminów** lub kliknij prawym przyciskiem myszy dowolny węzeł, który pojawia się w widoku rozszerzonym.
* Aby wyświetlić listę grup woluminów, kliknij pozycję **grupy woluminów** w okienku **zakres** . Lista grup woluminów wraz z informacjami o każdej grupie woluminów zostanie wyświetlona w okienku **wyników** .

### <a name="backup-policies-node"></a>Węzeł zasad kopii zapasowych
Zasady tworzenia kopii zapasowych są harmonogramami zadań dla migawek lokalnych i chmurowych. Za pomocą węzła **zasady tworzenia kopii zapasowych** można określić, jak często jest tworzona kopia zapasowa i jak długo ma być przechowywana kopia zapasowa. 

* Aby rozwinąć węzeł, kliknij ikonę strzałki obok pozycji **zasady tworzenia kopii zapasowych**.
* Aby wyświetlić menu dostępne akcje, kliknij prawym przyciskiem myszy węzeł **zasady tworzenia kopii zapasowych** lub kliknij prawym przyciskiem myszy dowolny węzeł, który pojawia się w widoku rozszerzonym.
* Aby wyświetlić listę zasad tworzenia kopii zapasowych, kliknij pozycję **zasady tworzenia kopii zapasowych** w okienku **zakres** . W okienku **wyników** zostanie wyświetlona lista zasad tworzenia kopii zapasowych wraz z informacjami dotyczącymi poszczególnych zasad.

> [!NOTE]
> Można zachować maksymalnie 64 kopii zapasowych.


### <a name="backup-catalog-node"></a>Węzeł katalogu kopii zapasowych
Węzeł **wykaz kopii zapasowych** zawiera listy kopii zapasowych w lokacji i poza lokacją woluminów StorSimple platformy Azure. Ten węzeł jest zorganizowany według grupy woluminów, a każdy kontener grupy woluminów zawiera oddzielne struktury migawek lokalnych (węzła **lokalnego migawki**) i migawek w chmurze (węzeł **migawek chmury** ). Po rozwinięciu każdy kontener grupy woluminów zawiera wszystkie pomyślne kopie zapasowe, które zostały wykonane interaktywnie lub przez skonfigurowane zasady.

* Aby rozwinąć węzeł, kliknij ikonę strzałki obok pozycji **wykaz kopii zapasowych**.
* Aby wyświetlić menu dostępne akcje, kliknij prawym przyciskiem myszy węzeł **wykaz kopii zapasowych** lub kliknij prawym przyciskiem myszy dowolny węzeł, który pojawia się w widoku rozszerzonym.
* Aby wyświetlić listę migawek kopii zapasowych, kliknij pozycję **wykaz kopii zapasowych** w okienku **zakres** . Lista migawek wraz z informacjami o każdej z nich zostanie wyświetlona w okienku **wyników** .

### <a name="local-snapshots-node"></a>Węzeł migawek lokalnych
Węzeł **migawki lokalne** zawiera listę lokalnych migawek dla określonej grupy woluminów. Węzeł znajduje się w węźle **wykaz kopii zapasowych** w okienku **zakres** . Migawki lokalne to kopie danych woluminu do punktu w czasie przechowywane na urządzeniu StorSimple platformy Azure. Zazwyczaj ten typ kopii zapasowej można utworzyć i przywrócić szybko. Możesz użyć lokalnej migawki w taki sam sposób, jak w przypadku lokalnej kopii zapasowej.

* Aby rozwinąć węzeł, kliknij ikonę strzałki obok pozycji **migawki lokalne**.
* Aby wyświetlić menu dostępne akcje, kliknij prawym przyciskiem myszy węzeł **migawki lokalne** lub kliknij prawym przyciskiem myszy dowolny węzeł, który pojawia się w widoku rozszerzonym.
* Aby wyświetlić listę migawek lokalnych, kliknij pozycję **migawki lokalne** w okienku **zakres** . Lista migawek wraz z informacjami o każdej z nich zostanie wyświetlona w okienku **wyników** .

### <a name="cloud-snapshots-node"></a>Węzeł migawek chmury
Węzeł **migawki chmury** zawiera listę migawek chmur dla określonej grupy woluminów. Węzeł znajduje się w węźle **wykaz kopii zapasowych** w okienku **zakres** . Migawki w chmurze to kopie danych woluminu w danym momencie, które są przechowywane w chmurze. Migawka w chmurze jest równoważna z migawką replikowaną w innym systemie magazynu poza lokacją. Migawki w chmurze są szczególnie przydatne w scenariuszach odzyskiwania po awarii.

* Aby rozwinąć węzeł, kliknij ikonę strzałki obok pozycji **migawki chmury**.
* Aby wyświetlić menu dostępne akcje, kliknij prawym przyciskiem myszy węzeł **migawki chmury** lub kliknij prawym przyciskiem myszy dowolny węzeł, który pojawia się w widoku rozszerzonym.
* Aby wyświetlić listę migawek w chmurze, kliknij pozycję **migawki w chmurze** w okienku **zakres** . Lista migawek wraz z informacjami o każdej z nich zostanie wyświetlona w okienku **wyników** .

### <a name="jobs-node"></a>Węzeł zadań
Węzeł **zadania** zawiera informacje o zaplanowanych, uruchomionych i ostatnio zakończonych zadaniach tworzenia kopii zapasowej. 

* Aby rozwinąć węzeł, kliknij ikonę strzałki obok pozycji **zadania**.
* Aby wyświetlić menu dostępne akcje, kliknij prawym przyciskiem myszy węzeł **zadania** lub kliknij prawym przyciskiem myszy dowolny węzeł, który pojawia się w widoku rozszerzonym.
* Aby wyświetlić listę zaplanowanych zadań, rozwiń węzeł **zadania** , a następnie kliknij pozycję **zaplanowane**. W okienku **wyników** zostanie wyświetlona lista wcześniej skonfigurowanych zadań i informacji dotyczących poszczególnych zadań. 
* Aby wyświetlić listę ostatnio ukończonych zadań, rozwiń węzeł **zadania** , a następnie kliknij pozycję **ostatnie 24 godziny**. W okienku **wyników** zostanie wyświetlona lista zadań ukończonych w ciągu ostatnich 24 godzin. Okienko **wyników** zawiera również informacje o każdym ukończonym zadaniu.
* Aby wyświetlić listę aktualnie uruchomionych zadań, rozwiń węzeł **zadania** , a następnie kliknij przycisk **uruchomione**. Lista aktualnie uruchomionych zadań i informacji o każdym zadaniu zostanie wyświetlona w okienku **wyników** .

## <a name="results-pane"></a>Okienko wyników
Okienko **wyników** to środkowe okienko w interfejsie użytkownika Snapshot Manager StorSimple. Zawiera on listy i szczegółowe informacje o stanie dla węzła wybranego w okienku **zakres** .

### <a name="example"></a>Przykład
Aby wyświetlić Poniższy przykład, kliknij węzeł **grupy woluminów** w okienku **zakres** . W okienku **wyników** zostanie wyświetlona lista grup woluminów ze szczegółowymi informacjami o każdej z nich.

![Okienko wyników](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Można skonfigurować Szczegóły wyświetlane w okienku **wyników** : kliknij prawym przyciskiem myszy węzeł w okienku **zakres** , kliknij polecenie **Widok**, a następnie kliknij polecenie **Dodaj/Usuń kolumny**.

## <a name="actions-pane"></a>Okienko akcje
Okienko **Akcje** jest prawym okienkiem w interfejsie użytkownika StorSimple Snapshot Manager. Zawiera menu operacji, które można wykonać w węźle, widoku lub danych, które można wybrać w okienku **zakres** lub w okienku **wyników** . Okienko **Akcje** zawiera te same polecenia, co menu **akcji** , które są dostępne dla elementów w okienku **zakres** i w okienku **wyników** . Opis poszczególnych akcji zawiera tabela w sekcji **Akcja** menu.

### <a name="examples"></a>Przykłady
Aby wyświetlić Poniższy przykład, w okienku **zakres** rozwiń węzeł **zadania** , a następnie kliknij pozycję **zaplanowane**. W okienku **Akcje** wyświetlane są akcje dostępne dla **zaplanowanego** węzła.

![Przykład zaplanowanych zadań w okienku Akcje](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Aby wyświetlić więcej opcji, w okienku **zakres** rozwiń węzeł **zadania** , kliknij pozycję **zaplanowane**, a następnie kliknij zaplanowane zadanie w okienku **wyników** . W okienku **Akcje** wyświetlane są akcje dostępne dla zaplanowanego zadania, jak pokazano w poniższym przykładzie.

![Przykład akcji zadania okienka Akcje](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Nawigacja i skróty klawiaturowe
StorSimple Snapshot Manager włącza funkcje ułatwień dostępu systemu operacyjnego Windows i programu Microsoft Management Console (MMC). Zawiera również pewne funkcje i skróty nawigacyjne klawiatury, które są specyficzne dla StorSimple Snapshot Manager, zgodnie z opisem w poniższych sekcjach.

* [Klawisze nawigacyjne klawiatury](#keyboard-navigation-keys) 
* [Klawisze skrótów paska menu](#menu-bar-shortcut-keys) 
* [Klawisze skrótów w okienku zakresu](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Klawisze nawigacyjne klawiatury
W poniższej tabeli opisano klucze, których można użyć do nawigowania po interfejsie użytkownika StorSimple Snapshot Manager. 

| Klawisz nawigacyjny | Akcja |
|:--- |:--- |
| Klawisz Strzałka w dół |Użyj klawisza Strzałka w dół, aby przenieść w pionie do następnego elementu w menu lub okienku. |
| Enter |Naciśnij klawisz ENTER, aby ukończyć akcję, a następnie przejdź do następnego kroku. Na przykład możesz nacisnąć klawisz ENTER, aby wybrać **Następny**, **OK** lub **Create**, a następnie przejść do następnego kroku w kreatorze. |
| Esc |Naciśnij klawisz ESC, aby zamknąć menu lub anulować i zamknąć stronę. |
| F1 |Naciśnij klawisz F1, aby wyświetlić temat pomocy dla aktualnie aktywnego okna. |
| F5 |Naciśnij klawisz F5, aby odświeżyć węzeł. |
| F6 |Naciśnij klawisz F6, aby przejść z okienka **zakres** do okienka **wyników** . |
| F10 |Naciśnij klawisz F10, aby przejść do paska menu. |
| Klawisz ze strzałką w lewo |Użyj klawisza Strzałka w lewo, aby przesunąć poziomo od opcji paska menu do poprzedniej opcji. Po przejściu do poprzedniego elementu na pasku menu pojawi się menu Akcja (lub kontekst) dla poprzedniego elementu. |
| Klawisz ze strzałką w prawo |Użyj klawisza Strzałka w prawo, aby przesunąć poziom od jednej opcji paska menu do następnego. Po przejściu do następnego elementu na pasku menu pojawi się menu Akcja (lub kontekst) dla nowego elementu. |
| Klawisz Tab |Użyj klawisza Tab, aby przejść do następnego okienka w konsoli programu lub do następnego zaznaczenia lub pola tekstowego na stronie. |
| Klawisz Strzałka w górę |Użyj klawisza Strzałka w górę, aby przejść w pionie do poprzedniego elementu w menu lub okienku. |

### <a name="menu-bar-shortcut-keys"></a>Klawisze skrótów paska menu
W poniższej tabeli opisano kombinacje klawiszy skrótów dla paska menu. Po naciśnięciu klawiszy skrótów i otwarciu menu można użyć klawiszy skrótów menu (podkreślone klawisze w menu). Aby uzyskać więcej informacji na pasku menu, przejdź do [paska menu](#menu-bar).

| Skrót | Wynik | Klawisz skrótu menu | Wynik |
|:--- |:--- |:--- |:--- |
| ALT + F |Otwiera menu **plik** . |N |Otwiera nowe wystąpienie konsoli. |
|  |O |Otwiera stronę **Narzędzia administracyjne** . | |
|  |S |Zapisuje konsolę Snapshot Manager StorSimple. | |
|  |A |Otwiera stronę **Zapisz jako** . | |
|  |M |Otwiera stronę **Dodaj/Usuń przystawkę** . | |
|  |P |Otwiera stronę **Opcje** . | |
|  |H |Otwiera pomoc online. | |
| ALT + A |Otwiera menu **Akcja** . |I |Włącza i wyłącza opcję wyświetlania importu. |
|  |W |Otwiera nową konsolę Snapshot Manager StorSimple. | |
|  |F |Aktualizuje konsolę Snapshot Manager StorSimple. | |
|  |L |Otwiera stronę **Eksportuj listę** . | |
|  |H |Otwiera pomoc online. | |
| ALT + V |Otwiera menu **Widok** . |A |Otwiera stronę **Dodawanie/usuwanie kolumn** . |
|  |U |Otwiera stronę **Dostosowywanie widoku** . | |
| ALT+O |Otwiera menu **Ulubione** . |A |Otwiera stronę **Dodawanie do ulubionych** . |
|  |O |Otwiera stronę **Organizuj ulubione** . | |
| ALT + W |Otwiera menu **okno** . |N |Otwiera inne okno StorSimple Snapshot Manager. |
|  |C |Wyświetla wszystkie otwarte okna konsoli w stylu kaskadowym. | |
|  |T |Wyświetla wszystkie otwarte okna konsoli w deseniu siatki. | |
|  |I |Rozmieszcza ikony w poziomie wiersza w dolnej części ekranu. | |
| ALT + H |Otwiera menu **Pomoc** . |H |Otwiera pomoc online. |
|  |T |Otwiera stronę sieci Web centrum Tech. Microsoft TechNet. | |
|  |A |Otwiera stronę **Informacje o programie Microsoft Management Console** . | |

### <a name="scope-pane-shortcut-keys"></a>Klawisze skrótów w okienku zakresu
W poniższych tabelach przedstawiono kombinacje klawiszy skrótów dla każdego węzła w okienku **zakres** . 

* [Klawisze skrótów dla węzła urządzenia](#devices-node-shortcut-keys)
* [Skróty do węzłów woluminów](#volumes-node-shortcut-keys)
* [Klawisze skrótów dla węzła grupy woluminów](#volume-groups-node-shortcut-keys)
* [Klawisze skrótów dla węzła zasady kopii zapasowej](#backup-policies-node-shortcut-keys)
* [Klawisze skrótów dla węzła wykazu kopii zapasowych](#backup-catalog-node-shortcut-keys)
* [Klawisze skrótów dla węzła zadania](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Klawisze skrótów dla węzła urządzenia
| Skrót menu | Wynik |
|:--- |:--- |
| C |Otwiera stronę **Konfigurowanie urządzenia** . |
| D |Odświeża listę urządzeń i szczegóły urządzenia. |
| V |Otwiera menu **Widok** . |
| W |Otwiera nową konsolę Snapshot Manager StorSimple na węźle **szczegóły** . |
| F |Aktualizuje konsolę Snapshot Manager StorSimple. |
| L |Otwiera stronę **Eksportuj listę** . |
| H |Otwiera pomoc online. |

#### <a name="volumes-node-shortcut-keys"></a>Skróty do węzłów woluminów
| Skrót menu | Wynik |
|:--- |:--- |
| V |Aktualizuje listę woluminów. |
| V (dwa razy klawisz) |Otwiera menu **Widok** . |
| W |Otwiera nową konsolę Snapshot Manager StorSimple w węźle **woluminy** . |
| F |Aktualizuje konsolę Snapshot Manager StorSimple. |
| L |Otwiera stronę **Eksportuj listę** . |
| H |Otwiera pomoc online. |

#### <a name="volume-groups-node-shortcut-keys"></a>Klawisze skrótów dla węzła grupy woluminów
| Skrót menu | Wynik |
|:--- |:--- |
| G |Otwiera stronę **Tworzenie grupy woluminów** . |
| V |Otwiera menu **Widok** . |
| W |Otwiera nową konsolę Snapshot Manager StorSimple, która koncentruje się na węźle **grupy woluminów** . |
| F |Aktualizuje konsolę Snapshot Manager StorSimple. |
| L |Otwiera stronę **Eksportuj listę** . |
| H |Otwiera pomoc online. |

#### <a name="backup-policies-node-shortcut-keys"></a>Klawisze skrótów dla węzła zasady kopii zapasowej
| Skrót menu | Wynik |
|:--- |:--- |
| B |Otwiera stronę **Tworzenie zasad** . |
| V |Otwiera menu **Widok** . |
| W |Otwiera nową konsolę Snapshot Manager StorSimple, która koncentruje się na węźle **grupy woluminów** . |
| F |Aktualizuje konsolę Snapshot Manager StorSimple. |
| L |Otwiera stronę **Eksportuj listę** . |
| H |Otwiera pomoc online. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Klawisze skrótów dla węzła wykazu kopii zapasowych
| Skrót menu | Wynik |
|:--- |:--- |
| W |Otwiera nową konsolę Snapshot Manager StorSimple, która koncentruje się na węźle **grupy woluminów** . |
| F |Aktualizuje konsolę Snapshot Manager StorSimple. |
| H |Otwiera pomoc online. |

#### <a name="jobs-node-shortcut-keys"></a>Klawisze skrótów dla węzła zadania
| Skrót menu | Wynik |
|:--- |:--- |
| V |Otwiera menu **Widok** . |
| W |Otwiera nową konsolę Snapshot Manager StorSimple, która skupia się na węźle **Jobs** . |
| F |Aktualizuje konsolę Snapshot Manager StorSimple. |
| L |Otwiera stronę **Eksportuj listę** . |
| H |Otwiera pomoc online |

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [używać Snapshot Manager StorSimple do administrowania rozwiązaniem StorSimple](storsimple-snapshot-manager-admin.md).
* Dowiedz się, jak [używać Snapshot Manager StorSimple do łączenia urządzeń i zarządzania nimi](storsimple-snapshot-manager-manage-devices.md).