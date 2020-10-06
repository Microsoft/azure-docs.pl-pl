---
title: Klonowanie kopii zapasowej macierzy wirtualnej StorSimple | Microsoft Docs
description: Dowiedz się, jak sklonować kopię zapasową i odzyskać plik z macierzy wirtualnej StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: af6e979c-55e3-477c-b53e-a76a697f80c9
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: b6cc89cb082cd0ed32abd88e3a6683c60a27ba90
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91742183"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>Klonowanie z kopii zapasowej macierzy wirtualnej StorSimple

## <a name="overview"></a>Omówienie

W tym artykule opisano krok po kroku, jak sklonować zestaw kopii zapasowych udziałów lub woluminów w Microsoft Azure StorSimple macierzy wirtualnej. Sklonowana kopia zapasowa służy do odzyskania usuniętego lub utraconego pliku. Artykuł zawiera również szczegółowe kroki umożliwiające przeprowadzenie odzyskiwania na poziomie elementu w macierzy wirtualnej StorSimple skonfigurowanej jako serwer plików.

## <a name="clone-shares-from-a-backup-set"></a>Klonowanie udziałów z zestawu kopii zapasowych

**Przed podjęciem próby sklonowania udziałów upewnij się, że na urządzeniu jest wystarczająca ilość miejsca, aby ukończyć tę operację.** Aby sklonować z kopii zapasowej, w [Azure Portal](https://portal.azure.com/)wykonaj następujące czynności.

#### <a name="to-clone-a-share"></a>Aby sklonować udział

1. Przejdź do bloku **urządzenia** . Wybierz i kliknij urządzenie, a następnie kliknij pozycję **udziały**. Wybierz udział, który chcesz sklonować, kliknij prawym przyciskiem myszy udział, aby wywołać menu kontekstowe. Wybierz pozycję **Klonuj**.
   
   ![Klonowanie kopii zapasowej](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. W bloku **klonowania** kliknij pozycję **kopia zapasowa > wybierz** , a następnie wykonaj następujące czynności: 
   
   a.    Odfiltruj kopię zapasową na tym urządzeniu w oparciu o zakres czasu. Możesz wybrać jedną z **ostatnich 7 dni**, **ostatnie 30 dni**i **ostatni rok**.
   
   b.    Na wyświetlonej liście filtrowanych kopii zapasowych wybierz kopię zapasową do sklonowania.
   
   c.    Kliknij przycisk **OK**.
   
   ![Klonowanie kopii zapasowej 2](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. W bloku **klonowania** kliknij pozycję **ustawienia docelowe** , a następnie wykonaj następujące czynności:
   
   a.    Podaj nazwę udziału. Nazwa udziału musi zawierać 3-127 znaków.
   
   b.    Opcjonalnie podaj opis sklonowanego udziału.
   
   c.    Nie można zmienić typu udziału, do którego przywracasz. Udział warstwowy jest sklonowany jako warstwowy i przypięty lokalnie jako przypięty lokalnie.
   
   d.    Pojemność jest ustawiana jako równa rozmiarowi udziału, z którego są klonowane.
   
   e.    Przypisz administratorów dla tego udziału. Po ukończeniu klonowania będzie można zmodyfikować właściwości udziału za pomocą Eksploratora plików.
   
   f.    Kliknij przycisk **OK**.
   
   ![Klonowanie kopii zapasowej 3](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Kliknij przycisk **Klonuj** , aby uruchomić zadanie klonowania. Po zakończeniu zadania Operacja klonowania zostanie uruchomiona i zostanie wyświetlone powiadomienie. Aby monitorować postęp klonowania, przejdź do bloku **zadania** i kliknij zadanie, aby wyświetlić szczegóły zadania.
5. Po pomyślnym utworzeniu klonu przejdź z powrotem do bloku **udziały** na urządzeniu.
6. Teraz można wyświetlić nowy sklonowany udział na liście udziałów na urządzeniu. Udział warstwowy jest sklonowany jako warstwowy i przypięty lokalnie jako udział przypięty lokalnie.
   
   ![Klonowanie kopii zapasowej 4](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Klonowanie woluminów z zestawu kopii zapasowych

Aby sklonować z kopii zapasowej, w Azure Portal należy wykonać kroki podobne do tych w przypadku klonowania udziału. Operacja klonowania klonuje kopię zapasową do nowego woluminu na tym samym urządzeniu wirtualnym. nie można sklonować do innego urządzenia.

#### <a name="to-clone-a-volume"></a>Aby sklonować wolumin

1. Przejdź do bloku **urządzenia** . Wybierz i kliknij urządzenie, a następnie kliknij przycisk **woluminy**. Wybierz wolumin, który ma zostać sklonowany, kliknij prawym przyciskiem myszy wolumin, aby wywołać menu kontekstowe. Wybierz pozycję **Klonuj**.
   
   ![Klonowanie woluminu](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. W bloku **klonowania** kliknij pozycję **kopia zapasowa** , a następnie wykonaj następujące czynności: 
   
   a.    Odfiltruj kopię zapasową na tym urządzeniu w oparciu o zakres czasu. Możesz wybrać jedną z **ostatnich 7 dni**, **ostatnie 30 dni**i **ostatni rok**. 
   
   b.    Na wyświetlonej liście filtrowanych kopii zapasowych wybierz kopię zapasową do sklonowania.
   
   c.    Kliknij przycisk **OK**.
   
   ![Klonowanie woluminu 2](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. W bloku **klonowania** kliknij pozycję **Ustawienia woluminu docelowego** , a następnie wykonaj następujące czynności:
   
   a. Nazwa urządzenia jest wypełniana automatycznie.
   
   b. Podaj nazwę woluminu **sklonowanego woluminu**. Nazwa woluminu musi zawierać od 3 do 127 znaków.
   
   c. Typ woluminu jest automatycznie ustawiany na oryginalny wolumin. Wolumin warstwowy jest sklonowany jako warstwowy i wolumin przypięty lokalnie jako przypięty lokalnie.
   
   d. W przypadku **podłączonych hostów**kliknij przycisk **Wybierz**.
   
   ![Klonowanie woluminu 3](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. W bloku **połączone hosty** wybierz jedną z istniejących ACR lub Dodaj nową ACR. Aby dodać nowy ACR, należy podać nazwę ACR i IQN hosta. Kliknij pozycję **Wybierz**.
   
   ![Klonowanie woluminu 4](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Kliknij przycisk **Klonuj** , aby uruchomić zadanie klonowania.
   
   ![Klonowanie woluminu 5](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. Po utworzeniu zadania klonowania rozpocznie się klonowanie. Po utworzeniu klonu zostanie on wyświetlony w bloku woluminy na urządzeniu. Należy zauważyć, że wolumin warstwowy jest sklonowany jako warstwowy i wolumin przypięty lokalnie został sklonowany jako wolumin przypięty lokalnie.
   
   ![Klonowanie woluminu 6](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. Gdy wolumin zostanie wyświetlony w trybie online na liście woluminów, wolumin jest dostępny do użycia. Na hoście inicjatora iSCSI Odśwież listę elementów docelowych w oknie właściwości inicjatora iSCSI. Nowy element docelowy zawierający sklonowaną nazwę woluminu powinien być wyświetlany jako "nieaktywny" w kolumnie Stan.
8. Wybierz element docelowy, a następnie kliknij przycisk **Połącz**. Po powiązaniu inicjatora z elementem docelowym stan powinien zmienić się na **połączono**.
9. W oknie **Zarządzanie dyskami** zainstalowane woluminy są wyświetlane jak pokazano na poniższej ilustracji. Kliknij prawym przyciskiem myszy odnaleziony wolumin (kliknij nazwę dysku), a następnie kliknij pozycję **Online**.

> [!IMPORTANT]
> W przypadku próby sklonowania woluminu lub udziału z zestawu kopii zapasowych w przypadku niepowodzenia zadania klonowania w portalu nadal można utworzyć wolumin docelowy lub udział. Ważne jest, aby usunąć ten wolumin docelowy lub udział w portalu w celu zminimalizowania wszelkich przyszłych problemów wynikających z tego elementu.
> 
> 

## <a name="item-level-recovery-ilr"></a>Odzyskiwanie na poziomie elementu (ILR)

W tej wersji wprowadzono odzyskiwanie na poziomie elementu (ILR) w StorSimpleej macierzy wirtualnej skonfigurowanej jako serwer plików. Funkcja ta umożliwia szczegółowe odzyskiwanie plików i folderów z kopii zapasowej w chmurze wszystkich udziałów na urządzeniu StorSimple. Usuniętych plików z najnowszych kopii zapasowych można pobrać przy użyciu modelu samoobsługowego.

Każdy udział ma folder *kopie zapasowe* , który zawiera najnowsze kopie zapasowe. Możesz przejść do żądanej kopii zapasowej, skopiować odpowiednie pliki i foldery z kopii zapasowej i przywrócić je. Ta funkcja eliminuje wywołania administratorów do przywracania plików z kopii zapasowych.

1. Podczas wykonywania ILR można wyświetlić kopie zapasowe za pomocą Eksploratora plików. Kliknij odpowiedni udział, dla którego chcesz wyszukać kopię zapasową. Zostanie wyświetlony folder *kopie zapasowe* utworzony w ramach udziału, w którym są przechowywane wszystkie kopie zapasowe. Rozwiń folder *kopie zapasowe* , aby wyświetlić kopie zapasowe. Folder pokazuje rozwinięty widok całej hierarchii kopii zapasowych. Ten widok jest tworzony na żądanie i zazwyczaj trwa tylko kilka sekund.
   
   Pięć ostatnich kopii zapasowych jest wyświetlanych w ten sposób i można go użyć do przeprowadzenia odzyskiwania na poziomie elementu. Pięć najnowszych kopii zapasowych obejmuje zarówno domyślne zaplanowane, jak i ręczne kopie zapasowe.
   
   * **Zaplanowane kopie zapasowe** nazwane jako &lt; Nazwa urządzenia &gt; DAILYSCHEDULE-RRRRMMDD-HHMMSS-UTC.
   * **Ręczne kopie zapasowe** o nazwie ad-hoc-RRRRMMDD-HHMMSS-UTC.
     
     ![Zrzut ekranu Eksploratora plików przedstawiający folder kopie zapasowe. W tym folderze jest wybierany folder ręczne kopie zapasowe.](./media/storsimple-virtual-array-clone/image14.png)

2. Zidentyfikuj kopię zapasową zawierającą najnowszą wersję usuniętego pliku. Mimo że nazwa folderu zawiera sygnaturę czasową UTC w każdym z powyższych przypadków, czas, w którym folder został utworzony, to rzeczywisty czas urządzenia podczas tworzenia kopii zapasowej. Użyj sygnatury czasowej folderu, aby zlokalizować i zidentyfikować kopie zapasowe.

3. Zlokalizuj folder lub plik, który chcesz przywrócić w kopii zapasowej, który został zidentyfikowany w poprzednim kroku. Należy pamiętać, że można wyświetlić tylko te pliki lub foldery, do których masz uprawnienia. Jeśli nie możesz uzyskać dostępu do określonych plików lub folderów, skontaktuj się z administratorem udziału. Za pomocą Eksploratora plików można edytować uprawnienia udziału i zapewniać dostęp do określonego pliku lub folderu. Zalecanym najlepszym rozwiązaniem jest, że administrator udostępniania jest grupą użytkowników, a nie pojedynczym użytkownikiem.

4. Skopiuj plik lub folder do odpowiedniego udziału na serwerze plików StorSimple.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o sposobach [administrowania wirtualną macierzą StorSimple przy użyciu lokalnego interfejsu użytkownika sieci Web](storsimple-ova-web-ui-admin.md).

