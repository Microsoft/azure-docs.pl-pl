---
title: Klonowanie woluminu w serii StorSimple 8000 | Microsoft Docs
description: Opisuje różne typy klonów i użycie i wyjaśnia, jak można użyć zestawu kopii zapasowych w celu sklonowania poszczególnych woluminów na urządzeniu z serii StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.date: 07/15/2020
ms.author: alkohli
ms.openlocfilehash: 5ebb7f1d9440bc7a8b75afe64403762532213c1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91767569"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>Klonowanie woluminu przy użyciu usługi Menedżer urządzeń StorSimple w witrynie Azure Portal

## <a name="overview"></a>Omówienie

W tym samouczku opisano, jak można użyć zestawu kopii zapasowych w celu sklonowania pojedynczego woluminu za pośrednictwem bloku **wykazu kopii zapasowych** . Wyjaśniono również różnicę między *przejściowymi* i *trwałymi* klonami. Wskazówki zawarte w tym samouczku dotyczą wszystkich urządzeń z serii StorSimple 8000 z aktualizacją Update 3 lub nowszą.

Blok **wykazu kopii zapasowych** usługi StorSimple Device Manager wyświetla wszystkie zestawy kopii zapasowych, które są tworzone podczas tworzenia ręcznych lub zautomatyzowanych kopii zapasowych. Następnie można wybrać wolumin w zestawie kopii zapasowych, który ma zostać sklonowany.

 ![Lista zestawów kopii zapasowych](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>Zagadnienia dotyczące klonowania woluminu

Podczas klonowania woluminu należy wziąć pod uwagę następujące informacje.

- Klon zachowuje się tak samo jak w przypadku zwykłego woluminu. Wszystkie operacje, które są możliwe na woluminie, są dostępne dla klonu.

- Monitorowanie i domyślna kopia zapasowa są automatycznie wyłączane na sklonowanym woluminie. Należy skonfigurować sklonowany wolumin dla wszystkich kopii zapasowych.

- Wolumin przypięty lokalnie jest sklonowany jako wolumin warstwowy. Jeśli wolumin sklonowany ma być przypięty lokalnie, można przekonwertować klon na wolumin przypięty lokalnie po pomyślnym zakończeniu operacji klonowania. Aby uzyskać informacje o konwertowaniu woluminu warstwowego na wolumin przypięty lokalnie, przejdź do pozycji [Zmień typ woluminu](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).

- W przypadku próby przekonwertowania sklonowanego woluminu z warstwowego na lokalnie przypięty natychmiast po sklonowaniu (gdy nadal jest to przejściowe klonowanie) konwersja kończy się niepowodzeniem z następującym komunikatem o błędzie:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    Ten błąd jest odbierany tylko w przypadku klonowania na innym urządzeniu. Można pomyślnie skonwertować wolumin na lokalnie przypięty, jeśli najpierw przekonwertujesz przejściowe klon na trwałe klonowanie. Utwórz migawkę w chmurze dla przejściowego klonu, aby przekonwertować ją na trwałe klonowanie.

## <a name="create-a-clone-of-a-volume"></a>Tworzenie klonu woluminu

Można utworzyć klon na tym samym urządzeniu, innym urządzeniu lub nawet urządzeniu w chmurze przy użyciu migawki lokalnej lub w chmurze.

W poniższej procedurze opisano sposób tworzenia klona z wykazu kopii zapasowych.  

Wykonaj następujące kroki, aby utworzyć klon woluminu z wykazu kopii zapasowych.

#### <a name="to-clone-a-volume"></a>Aby sklonować wolumin

1. Przejdź do usługi StorSimple Device Manager, a następnie kliknij pozycję **wykaz kopii zapasowych**.

2. Wybierz zestaw kopii zapasowych w następujący sposób:
   
   1. Wybierz odpowiednie urządzenie.
   2. Z listy rozwijanej wybierz wolumin lub zasady kopii zapasowej dla kopii zapasowej, którą chcesz wybrać.
   3. Określ zakres czasu.
   4. Kliknij przycisk **Zastosuj** , aby wykonać to zapytanie.

      Kopie zapasowe skojarzone z wybranym woluminem lub zasadami tworzenia kopii zapasowych powinny pojawić się na liście zestawów kopii zapasowych.
   
      ![Lista zestawów kopii zapasowych 2](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Rozwiń zestaw kopii zapasowych, aby wyświetlić skojarzony wolumin i wybrać wolumin w zestawie kopii zapasowych. Kliknij prawym przyciskiem myszy, a następnie z menu kontekstowego wybierz polecenie **Klonuj**.

   ![Lista zestawów kopii zapasowych 3](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. W bloku **klonowania** wykonaj następujące czynności:
   
   1. Zidentyfikuj urządzenie docelowe. Jest to lokalizacja, w której zostanie utworzony klon. Możesz wybrać to samo urządzenie lub określić inne urządzenie.

      > [!NOTE]
      > Upewnij się, że pojemność wymagana dla klonu jest mniejsza niż pojemność dostępna na urządzeniu docelowym.
       
   2. Określ unikatową nazwę woluminu dla klonu. Nazwa musi zawierać od 3 do 127 znaków.
      
       > [!NOTE]
       > **Klonowanie woluminu jako** pola zostanie **warstwowe** nawet w przypadku klonowania woluminu przypiętego lokalnie. Nie można zmienić tego ustawienia. Jeśli jednak wolumin sklonowany ma być również przypięty lokalnie, można przekonwertować klon na wolumin przypięty lokalnie po pomyślnym utworzeniu klona. Aby uzyskać informacje o konwertowaniu woluminu warstwowego na wolumin przypięty lokalnie, przejdź do pozycji [Zmień typ woluminu](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).
          
   3. W obszarze **połączone hosty** Określ rekord kontroli dostępu (ACR) dla klonu. Można dodać nowy ACR lub wybrać z istniejącej listy. ACR określi, które hosty mogą uzyskać dostęp do tego klona.
      
       ![Lista zestawów kopii zapasowych 4](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

   4. Kliknij przycisk **Klonuj** , aby ukończyć operację.

4. Zostało zainicjowane zadanie klonowania i zostanie wyświetlone powiadomienie o pomyślnym utworzeniu klona. Kliknij pozycję powiadomienie o zadaniu lub przejdź do bloku **zadania** , aby monitorować zadanie klonowania.

    ![Lista zestawów kopii zapasowych 5](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. Po ukończeniu zadania klonowania przejdź do urządzenia, a następnie kliknij pozycję **woluminy**. Na liście woluminów powinien zostać wyświetlony klon, który został właśnie utworzony w tym samym kontenerze woluminów z woluminem źródłowym.

    ![Lista zestawów kopii zapasowych 6](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

Klon tworzony w ten sposób jest przejściowym klonem. Aby uzyskać więcej informacji o typach klonowania, zobacz [przejściowe i trwałe klony](#transient-vs-permanent-clones).


## <a name="transient-vs-permanent-clones"></a>Przejściowe i trwałe klony
Klony przejściowe są tworzone tylko w przypadku klonowania na innym urządzeniu. Można sklonować określony wolumin z zestawu kopii zapasowych na inne urządzenie zarządzane przez StorSimple Device Manager. Przejściowy klon zawiera odwołania do danych w oryginalnym woluminie i używa tych danych do odczytu i zapisu lokalnego na urządzeniu docelowym.

Po wykonaniu migawki przejściowej klonu w chmurze powstaje klonowanie *trwałe* . W trakcie tego procesu kopia danych jest tworzona w chmurze, a godzina kopiowania danych jest określana na podstawie rozmiaru danych i opóźnień na platformie Azure (jest to kopia platformy Azure na platformę Azure). Ten proces może potrwać kilka dni do tygodni. Przejściowe klonowanie stał się trwałym klonem i nie ma żadnych odwołań do pierwotnych danych woluminu, z których zostały sklonowane.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Scenariusze dotyczące przejściowych i trwałych klonów
W poniższych sekcjach opisano przykładowe sytuacje, w których można użyć przejściowych i trwałych klonów.

### <a name="item-level-recovery-with-a-transient-clone"></a>Odzyskiwanie na poziomie elementu z klonem przejściowym
Konieczne jest odzyskanie jednoletniego pliku prezentacji programu Microsoft PowerPoint. Administrator IT identyfikuje określoną kopię zapasową w tym czasie, a następnie filtruje wolumin. Następnie administrator klonuje wolumin, lokalizuje plik, którego szukasz, i udostępnia go. W tym scenariuszu używany jest klon przejściowy.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testowanie w środowisku produkcyjnym z trwałym klonem
Należy sprawdzić usterkę testową w środowisku produkcyjnym. Można utworzyć klon woluminu w środowisku produkcyjnym, a następnie wykonać migawkę w chmurze tego klona, aby utworzyć niezależny wolumin sklonowany. W tym scenariuszu używany jest trwały klon.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [przywrócić wolumin StorSimple z zestawu kopii zapasowych](storsimple-8000-restore-from-backup-set-u2.md).
* Dowiedz się [, jak zarządzać urządzeniem StorSimple przy użyciu usługi StorSimple Device Manager](storsimple-8000-manager-service-administration.md).

