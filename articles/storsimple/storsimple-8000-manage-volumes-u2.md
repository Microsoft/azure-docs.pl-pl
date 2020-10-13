---
title: Zarządzanie woluminami StorSimple (Aktualizacja Update 3)
description: Wyjaśnia, jak dodawać, modyfikować, monitorować i usuwać woluminy StorSimple oraz jak w razie potrzeby przejmowanie ich w tryb offline.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 12/08/2017
ms.author: alkohli
ms.openlocfilehash: a8fcadb8bdd4862dd95625228e3c190e5fe19a9a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961684"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>Zarządzanie woluminami (Aktualizacja Update 3 lub nowsza) za pomocą usługi StorSimple Menedżer urządzeń

## <a name="overview"></a>Omówienie

W tym samouczku wyjaśniono, jak używać usługi StorSimple Menedżer urządzeń do tworzenia woluminów i zarządzania nimi na urządzeniach z serii StorSimple 8000 z aktualizacją Update 3 lub nowszą.

Usługa StorSimple Menedżer urządzeń jest rozszerzeniem Azure Portal, które umożliwia zarządzanie rozwiązaniem StorSimple z poziomu jednego interfejsu internetowego. Użyj Azure Portal, aby zarządzać woluminami na wszystkich urządzeniach. Można również tworzyć i zarządzać usługami StorSimple Services, zarządzać urządzeniami, zasadami tworzenia kopii zapasowych i wykazem kopii zapasowych oraz wyświetlać alerty.

## <a name="volume-types"></a>Typy woluminów

StorSimple woluminy mogą być następujące:

* **Woluminy przypięte lokalnie**: dane w tych woluminach pozostają na lokalnym urządzeniu StorSimple przez cały czas.
* **Woluminy warstwowe**: dane w tych woluminach mogą zostać rozlane do chmury.

Wolumin archiwalny jest typem woluminu warstwowego. Większy rozmiar fragmentu deduplikacji używany dla woluminów archiwalnych umożliwia urządzeniu przesyłanie dużych segmentów danych do chmury.

W razie potrzeby można zmienić typ woluminu z lokalnego na warstwowy lub z warstwowego na lokalny. Aby uzyskać więcej informacji, przejdź do obszaru [Zmień typ woluminu](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Lokalnie przypięte woluminy

Woluminy przypięte lokalnie to w pełni inicjowane woluminy, które nie warstwą danych w chmurze, zapewniając w ten sposób lokalne gwarancje dotyczące danych podstawowych, niezależnie od łączności z chmurą. Dane na woluminach przypiętych lokalnie nie są deduplikowane i kompresowane; jednak migawki woluminów przypiętych lokalnie są deduplikowane. 

Woluminy przypięte lokalnie są w pełni obsługiwane; w związku z tym podczas tworzenia należy mieć wystarczającą ilość miejsca na urządzeniu. Można zarezerwować woluminy przypięte lokalnie do maksymalnego rozmiaru wynoszącego 8 TB na urządzeniu z systemem StorSimple 8100 i 20 TB na urządzeniu 8600. StorSimple rezerwuje pozostałe lokalne miejsce na urządzeniu na potrzeby migawek, metadanych i przetwarzania danych. Można zwiększyć rozmiar woluminu przypiętego lokalnie do maksymalnego dostępnego miejsca, ale nie można zmniejszyć rozmiaru woluminu po utworzeniu.

W przypadku tworzenia woluminu przypiętego lokalnie dostępne miejsce na potrzeby tworzenia woluminów warstwowych jest zmniejszane. Odwrócenie jest również prawdziwe: Jeśli masz istniejące woluminy warstwowe, miejsce dostępne na potrzeby tworzenia woluminów przypiętych lokalnie będzie mniejsze niż dopuszczalne limity podane powyżej. Aby uzyskać więcej informacji na temat woluminów lokalnych, zapoznaj się z [często zadawanymi pytaniami na woluminach przypiętych lokalnie](storsimple-8000-local-volume-faq.md).

### <a name="tiered-volumes"></a>Woluminy warstwowe

Woluminy warstwowe to woluminy alokowane elastycznie, w których często używane dane pozostają lokalne na urządzeniu, a mniej często używanych danych jest automatycznie warstwą w chmurze. Alokowanie elastyczne jest technologią wirtualizacji, w której dostępny magazyn wydaje się przekroczyć zasoby fizyczne. Zamiast ponownie obsłużyć wystarczającą ilość miejsca w magazynie, StorSimple używa alokowania elastycznego do przydzielania wystarczającej ilości miejsca, aby spełnić bieżące wymagania. Elastyczna natura magazynu w chmurze ułatwia takie podejście, ponieważ Usługa StorSimple może zwiększyć lub zmniejszyć ilość miejsca w magazynie w chmurze, aby sprostać zmiennym potrzebom.

Jeśli używasz woluminu warstwowego dla danych archiwalnych, zaznacz pole wyboru **Użyj tego woluminu dla rzadziej używanych danych archiwalnych** , aby zmienić rozmiar fragmentu deduplikacji dla woluminu na 512 KB. Jeśli nie wybierzesz tej opcji, odpowiedni wolumin warstwowy użyje rozmiaru fragmentu wynoszącego 64 KB. Większy rozmiar fragmentu deduplikacji umożliwia urządzeniu usprawnienie transferu dużej ilości danych archiwalnych w chmurze.


### <a name="provisioned-capacity"></a>Przyobsługiwana pojemność

Zapoznaj się z poniższą tabelą dotyczącą maksymalnej alokowanej pojemności dla każdego typu urządzenia i woluminu. (Należy zauważyć, że woluminy przypięte lokalnie nie są dostępne na urządzeniu wirtualnym).

| Typ | Maksymalny rozmiar warstwowy | Maksymalny rozmiar woluminu przypiętego lokalnie |
| --- | --- | --- |
| **Urządzenia fizyczne** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Urządzenia wirtualne** | | |
| 8010 |30 TB |Nie dotyczy |
| 8020 |64 TB |Nie dotyczy |

## <a name="the-volumes-blade"></a>Blok woluminy

Blok **woluminy** umożliwia zarządzanie woluminami magazynu, które są obsługiwane na urządzeniu Microsoft Azure StorSimple dla inicjatorów (serwerów). Wyświetla listę woluminów na urządzeniach StorSimple podłączonych do usługi.

 ![Strona woluminów](./media/storsimple-8000-manage-volumes-u2/volumeslist.png)

Wolumin składa się z serii atrybutów:

* **Nazwa woluminu** — nazwa opisowa, która musi być unikatowa i pomaga identyfikować wolumin. Ta nazwa jest również używana w raportach monitorowania podczas filtrowania określonego woluminu. Po utworzeniu woluminu nie można zmienić jego nazwy.
* **Stan** — może być w trybie online lub offline. Jeśli wolumin jest w trybie offline, nie jest on widoczny dla inicjatorów (serwerów), które mają dostęp do korzystania z woluminu.
* **Pojemność** — określa łączną ilość danych, które mogą być przechowywane przez inicjatora (serwer). Woluminy przypięte lokalnie są w pełni obsługiwane i znajdują się na urządzeniu StorSimple. Woluminy warstwowe są alokowane elastycznie, a dane są deduplikowane. W przypadku woluminów alokowanych elastycznie urządzenie nie przydziela wstępnie fizycznej pojemności magazynu fizycznego lub w chmurze w zależności od skonfigurowanej pojemności woluminu. Pojemność woluminu jest alokowana i zużywana na żądanie.
* **Typ** — wskazuje, czy wolumin jest **warstwowy** (domyślnie) czy **przypięty lokalnie**.

Skorzystaj z instrukcji przedstawionych w tym samouczku, aby wykonać następujące zadania:

* Dodaj wolumin 
* Modyfikowanie woluminu 
* Zmień typ woluminu
* Usuwanie woluminu 
* Przełącz wolumin w tryb offline 
* Monitorowanie woluminu 

## <a name="add-a-volume"></a>Dodaj wolumin

[Wolumin został utworzony](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) podczas wdrażania urządzenia z serii StorSimple 8000. Dodawanie woluminu jest podobną procedurą.

#### <a name="to-add-a-volume"></a>Aby dodać wolumin

1. Z tabelarycznej listy urządzeń w bloku **Urządzenia** wybierz swoje urządzenie. Kliknij pozycję **+ Dodaj wolumin**.

    ![Dodawanie nowego woluminu](./media/storsimple-8000-manage-volumes-u2/step5createvol1.png)

2. W bloku **Dodawanie woluminu**:
   
    1. Pole **Wybierz urządzenie** jest wypełniane automatycznie przy użyciu danych bieżącego urządzenia.

    2. Z listy rozwijanej wybierz kontener woluminów, do którego chcesz dodać wolumin.

    3.  Wpisz wartość pola **Nazwa** dla woluminu. Po utworzeniu woluminu nie można zmienić jego nazwy.

    4. Z listy rozwijanej wybierz wartość **Typ** dla woluminu. W przypadku obciążeń, które wymagają lokalnych gwarancji, małych opóźnień i większej wydajności, wybierz wolumin typu **Przypięty lokalnie**. W przypadku wszystkich innych danych wybierz wolumin typu **Warstwowy**. Jeśli używasz tego woluminu na potrzeby danych archiwalnych, wybierz opcję **Użyj tego woluminu w przypadku rzadziej używanych danych archiwalnych**.
      
       Wolumin warstwowy jest alokowany elastycznie i można go szybko utworzyć. W przypadku woluminu warstwowego przeznaczonego na dane archiwalne wybranie opcji **Użyj tego woluminu w przypadku rzadziej używanych danych archiwalnych** spowoduje zmianę rozmiaru fragmentu deduplikacji dla woluminu na 512 KB. Jeśli to pole nie zostanie zaznaczone, odpowiedni wolumin warstwowy użyje rozmiaru fragmentu wynoszącego 64 KB. Większy rozmiar fragmentu deduplikacji umożliwia urządzeniu usprawnienie transferu dużej ilości danych archiwalnych w chmurze.
       
       Wolumin przypięty lokalnie jest alokowany nieelastycznie i gwarantuje, że główne dane na woluminie pozostaną na urządzeniu lokalnym i nie zostaną przeniesione do chmury.  W przypadku tworzenia woluminu przypiętego lokalnie urządzenie sprawdza dostępne miejsce w warstwach lokalnych, aby zaalokować żądaną ilość miejsca na woluminie. Operacja tworzenia woluminów przypiętych lokalnie może obejmować przenoszenie istniejących danych z urządzenia do chmury i czas tworzenia woluminu może być długi. Łączny czas zależy od rozmiaru alokowanego woluminu, dostępnej przepustowości sieci i danych na urządzeniu.

    5. Określ **alokowaną pojemność** woluminu. Zanotuj wielkość pojemności dostępnej w oparciu o wybrany typ woluminu. Wybrany rozmiar woluminu nie może przekraczać wielkości dostępnego miejsca.
      
       Na urządzeniu 8100 można alokować woluminy przypięte lokalnie do 8,5 TB lub woluminy warstwowe do 200 TB. Na większym urządzeniu 8600 można alokować woluminy przypięte lokalnie do 22,5 TB lub woluminy warstwowe do 500 TB. Ponieważ lokalne miejsce na urządzeniu jest wymagane do obsługi zestawu roboczego woluminów warstwowych, tworzenie woluminów przypiętych lokalnie ma wpływ na miejsce dostępne do aprowizacji na woluminach warstwowych. Dlatego jeśli tworzysz wolumin przypięty lokalnie, miejsce dostępne na potrzeby tworzenia woluminów warstwowych zmniejsza się. Podobnie jeśli utworzysz wolumin warstwowy, miejsce dostępne na potrzeby tworzenia woluminów przypiętych lokalnie zmniejszy się.
      
       W przypadku alokowania woluminu przypiętego lokalnie o rozmiarze 8,5 TB (maksymalny dozwolony rozmiar) na urządzeniu 8100 całe lokalne miejsce dostępne na urządzeniu zostanie wyczerpane. Od tego momentu nie można tworzyć woluminów warstwowych, ponieważ w urządzeniu nie ma już miejsca lokalnego do hostowania roboczego zestawu woluminu warstwowego. Istniejące woluminy warstwowe również wpływają na dostępne miejsce. Jeśli na przykład masz urządzenie 8100 z woluminami warstwowymi o wielkości około 106 TB, tylko 4 TB są dostępne dla woluminów przypiętych lokalnie.

    6. W polu **Połączone hosty** kliknij strzałkę. W bloku **połączone hosty** wybierz istniejący ACR lub Dodaj nowy ACR. Jeśli wybierzesz nowy ACR, podaj **nazwę** dla ACR, podaj **kwalifikowaną nazwę iSCSI** (IQN) hosta z systemem Windows. Jeśli nie masz nazwy IQN, przejdź do tematu Pobieranie nazwy IQN hosta z systemem Windows Server. Kliknij pozycję **Utwórz**. Wolumin zostanie utworzony przy użyciu wybranych ustawień.

        ![Kliknięcie pozycji Utwórz](./media/storsimple-8000-manage-volumes-u2/step5createvol3.png)

Nowy wolumin jest teraz gotowy do użycia.

> [!NOTE]
> Jeśli utworzysz wolumin przypięty lokalnie, a następnie utworzysz inny lokalnie przypięty wolumin bezpośrednio później, zadania tworzenia woluminu są uruchamiane sekwencyjnie. Pierwsze zadanie tworzenia woluminu musi zakończyć się przed rozpoczęciem kolejnego zadania tworzenia woluminu.

## <a name="modify-a-volume"></a>Modyfikowanie woluminu

Należy zmodyfikować wolumin, gdy trzeba go rozszerzyć lub zmienić hosty, które uzyskują dostęp do woluminu.

> [!IMPORTANT]
> * W przypadku zmodyfikowania rozmiaru woluminu na urządzeniu należy również zmienić rozmiar woluminu na hoście.
> * Kroki po stronie hosta opisane tutaj dotyczą systemu Windows Server 2012 (2012R2). Procedury dla systemu Linux lub innych systemów operacyjnych hosta będą się różnić. Zapoznaj się z instrukcjami dotyczącymi systemu operacyjnego hosta podczas modyfikowania woluminu na hoście z uruchomionym innym systemem operacyjnym.

#### <a name="to-modify-a-volume"></a>Aby zmodyfikować wolumin

1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie kliknij pozycję **Urządzenia**. Z tabelarycznej listy urządzeń wybierz urządzenie z woluminem, który ma zostać zmodyfikowany. Kliknij pozycję **ustawienia > woluminy**.

    ![Przejdź do bloku woluminów](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

2. Na tabelarycznej liście woluminów Wybierz wolumin, a następnie kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe. Wybierz pozycję **Przełącz do trybu offline** , aby przełączyć wolumin do trybu offline.

    ![Wybierz i przejmowanie woluminu w tryb offline](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. W bloku **Przełącz do trybu offline** Przejrzyj wpływ przełączenia woluminu w tryb offline i zaznacz odpowiednie pole wyboru. Upewnij się, że odpowiedni wolumin na hoście jest w pierwszej kolejności w trybie offline. Aby uzyskać informacje na temat sposobu przełączenia woluminu w tryb offline na serwerze hosta połączonym z usługą StorSimple, zobacz instrukcje dotyczące systemu operacyjnego. Kliknij pozycję **Przełącz do trybu offline**.

    ![Przejrzyj wpływ przełączenia woluminu w tryb offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

4. Gdy wolumin jest w trybie offline (jak pokazano w stanie woluminu), wybierz wolumin i kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe. Wybierz pozycję **Modyfikuj wolumin**.

    ![Wybieranie opcji Modyfikuj wolumin](./media/storsimple-8000-manage-volumes-u2/modifyvol9.png)


5. W bloku **Modyfikowanie woluminu** można wprowadzić następujące zmiany:
   
   1. Nie można edytować **nazwy** woluminu.
   2. Konwertuj **Typ** z lokalnie przypięty do warstwowego lub z warstwowego do przypiętego lokalnie (zobacz [Zmień typ woluminu,](#change-the-volume-type) Aby uzyskać więcej informacji).
   3. Zwiększ **pojemność zainicjowaną**. Można zwiększyć **pojemność administracyjną** . Nie można zmniejszyć woluminu po jego utworzeniu.
   4. W obszarze **połączone hosty**można zmodyfikować ACR. Aby zmodyfikować ACR, wolumin musi być w trybie offline.

       ![Zapoznaj się z tematem wpływanie woluminu w tryb offline 2](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

5. Kliknij pozycję **Zapisz**, aby zapisać zmiany. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**. W Azure Portal zostanie wyświetlony komunikat o aktualizowaniu woluminu. Po pomyślnym zaktualizowaniu woluminu zostanie wyświetlony komunikat o powodzeniu.

    ![Zapoznaj się z tematem wpływanie woluminu w tryb offline 3](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

7. Jeśli rozszerzasz wolumin, wykonaj następujące czynności na komputerze hosta z systemem Windows:
   
   1. Przejdź do obszaru **Zarządzanie komputerem**zarządzanie  -> **dyskami**.
   2. Kliknij prawym przyciskiem myszy pozycję **Zarządzanie dyskami** i wybierz polecenie **Skanuj ponownie dyski**.
   3. Na liście dysków wybierz zaktualizowany wolumin, kliknij prawym przyciskiem myszy, a następnie wybierz polecenie **Zwiększ rozmiar woluminu**. Zostanie uruchomiony Kreator rozszerzający wolumin. Kliknij przycisk **Dalej**.
   4. Ukończ pracę kreatora, akceptując wartości domyślne. Po zakończeniu działania kreatora wolumin powinien mieć większy rozmiar.
      
      > [!NOTE]
      > Jeśli rozszerzasz wolumin przypięty lokalnie, a następnie natychmiast rozszerzasz inny wolumin przypięty lokalnie, zadania rozszerzenia woluminu są uruchamiane sekwencyjnie. Pierwsze zadanie rozwinięcia woluminu musi zakończyć się przed rozpoczęciem kolejnego zadania rozszerzenia woluminu.
      

## <a name="change-the-volume-type"></a>Zmień typ woluminu

Można zmienić typ woluminu z warstwowego na przypięty lokalnie lub przypięty lokalnie do warstw. Jednak ta konwersja nie powinna być częstym wystąpieniem.

### <a name="tiered-to-local-volume-conversion-considerations"></a>Zagadnienia dotyczące konwersji warstwowego na wolumin lokalny

Niektóre powody konwersji woluminu z warstwowego na przypięty lokalnie są następujące:

* Lokalne gwarancje dotyczące dostępności i wydajności danych
* Eliminowanie opóźnień w chmurze i problemów z łącznością w chmurze.

Zazwyczaj są to małe istniejące woluminy, które mają być często dostępne. Wolumin przypięty lokalnie jest w pełni zainicjowany podczas jego tworzenia. 

W przypadku konwertowania woluminu warstwowego na wolumin przypięty lokalnie, StorSimple sprawdza, czy masz wystarczającą ilość miejsca na urządzeniu przed rozpoczęciem konwersji. Jeśli masz niewystarczającą ilość miejsca, zostanie wyświetlony komunikat o błędzie, a operacja zostanie anulowana. 

> [!NOTE]
> Przed rozpoczęciem konwersji z warstwy na przypięty lokalnie, należy wziąć pod uwagę wymagania dotyczące miejsca dla innych obciążeń. 

Konwersja z warstwy na wolumin przypięty lokalnie może niekorzystnie wpłynąć na wydajność urządzenia. Ponadto następujące czynniki mogą zwiększyć czas potrzebny do ukończenia konwersji:

* Za mało przepustowości.
* Brak bieżącej kopii zapasowej.

Aby zminimalizować skutki, które mogą mieć te czynniki:

* Przejrzyj zasady ograniczania przepustowości i upewnij się, że dostępna jest dedykowana Przepustowość 40 MB/s.
* Zaplanuj konwersję na godziny poza godzinami szczytu.
* Przed rozpoczęciem konwersji Wykonaj migawkę w chmurze.

W przypadku konwertowania wielu woluminów (obsługujących różne obciążenia) należy określić priorytet konwersji woluminów, tak aby woluminy o wyższym priorytecie były konwertowane jako pierwsze. Na przykład należy przekonwertować woluminy obsługujące maszyny wirtualne lub woluminy z obciążeniami SQL przed konwersją woluminów z obciążeniami udziałów plików.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Zagadnienia dotyczące konwersji woluminów lokalnych na warstwowe

Można zmienić wolumin przypięty lokalnie na wolumin warstwowy, jeśli potrzebne jest dodatkowe miejsce na zainicjowanie obsługi innych woluminów. W przypadku konwertowania woluminu przypiętego lokalnie na warstwowy dostępną pojemność urządzenia zwiększa się o rozmiar wydanej pojemności. Jeśli problemy z łącznością uniemożliwiają konwersję woluminu z typu lokalnego na typ warstwowy, wolumin lokalny będzie wykazywać właściwości woluminu warstwowego do momentu zakończenia konwersji. Wynika to z faktu, że niektóre dane mogły zostać rozlane do chmury. Te rozlane dane nadal zajmują miejsce lokalne na urządzeniu, którego nie można zwolnić, dopóki operacja nie zostanie uruchomiona ponownie i nie zostanie ukończona.

> [!NOTE]
> Konwertowanie woluminu może zająć trochę czasu i nie można anulować konwersji po jego uruchomieniu. Wolumin pozostaje w trybie online podczas konwersji i można tworzyć kopie zapasowe, ale nie można rozwijać ani przywracać woluminu podczas konwersji.


#### <a name="to-change-the-volume-type"></a>Aby zmienić typ woluminu

1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie kliknij pozycję **Urządzenia**. Z tabelarycznej listy urządzeń wybierz urządzenie z woluminem, który ma zostać zmodyfikowany. Kliknij pozycję **ustawienia > woluminy**.

    ![Przejdź do bloku woluminów 2](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Na tabelarycznej liście woluminów Wybierz wolumin, a następnie kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe. Wybierz pozycję **Modyfikuj**.

    ![Wybieranie opcji Modyfikuj z menu kontekstowego](./media/storsimple-8000-manage-volumes-u2/changevoltype2.png)

4. W bloku **Modyfikuj wolumin** Zmień typ woluminu, wybierając nowy typ z listy rozwijanej **Typ** .
   
   * Jeśli zmieniasz typ na **przypięty lokalnie**, StorSimple sprawdzi, czy jest wystarczająca pojemność.
   * Jeśli zmieniasz typ na **warstwowy** i ten wolumin będzie używany na potrzeby archiwizowania danych, zaznacz pole wyboru **Użyj tego woluminu dla rzadziej używanych danych archiwalnych** .
   * Jeśli konfigurujesz wolumin przypięty lokalnie jako warstwowy lub _odwrotnie_, zostanie wyświetlony następujący komunikat.
   
     ![Zmień komunikat o typie woluminu](./media/storsimple-8000-manage-volumes-u2/changevoltype3.png)

7. Kliknij przycisk **Zapisz**, aby zapisać zmiany. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **tak** , aby rozpocząć proces konwersji. 

    ![Zapisz i Potwierdź](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

8. Azure Portal wyświetla powiadomienie dla tworzenia zadania, które aktualizuje wolumin. Kliknij powiadomienie, aby monitorować stan zadania konwersji zbiorczej.

    ![Zadanie konwersji woluminów](./media/storsimple-8000-manage-volumes-u2/changevoltype5.png)

## <a name="take-a-volume-offline"></a>Przełącz wolumin w tryb offline

Jeśli planujesz zmodyfikować lub usunąć wolumin, może być konieczne przełączenie woluminu w tryb offline. Gdy wolumin jest w trybie offline, nie jest dostępny do odczytu i zapisu. Wolumin należy przełączyć do trybu offline na hoście i urządzeniu.

#### <a name="to-take-a-volume-offline"></a>Aby przełączyć wolumin w tryb offline

1. Upewnij się, że dany wolumin nie jest używany przed przełączeniem go w tryb offline.
2. Najpierw Przełącz wolumin do trybu offline na hoście. Eliminuje to potencjalne ryzyko uszkodzenia danych na woluminie. Aby zapoznać się z określonymi krokami, zapoznaj się z instrukcjami dotyczącymi systemu operacyjnego hosta.
3. Gdy host jest w trybie offline, zrób wolumin na urządzeniu w trybie offline, wykonując następujące czynności:
   
    1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie kliknij pozycję **Urządzenia**. Z tabelarycznej listy urządzeń wybierz urządzenie z woluminem, który ma zostać zmodyfikowany. Kliknij pozycję **ustawienia > woluminy**.

        ![Przejdź do bloku woluminów 3](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

    2. Na tabelarycznej liście woluminów Wybierz wolumin, a następnie kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe. Wybierz pozycję **Przełącz do trybu offline** , aby przełączyć wolumin do trybu offline.

        ![Wybierz i przejmowanie woluminu w tryb offline](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. W bloku **Przełącz do trybu offline** Przejrzyj wpływ przełączenia woluminu w tryb offline i zaznacz odpowiednie pole wyboru. Kliknij pozycję **Przełącz do trybu offline**. 

    ![Zapoznaj się z tematem wpływanie woluminu w tryb offline 4](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)
      
      Otrzymasz powiadomienie, gdy wolumin jest w trybie offline. Stan woluminu jest również aktualizowany do trybu offline.
      
4. Jeśli wolumin jest w trybie offline, po wybraniu woluminu i kliknięciu prawym przyciskiem myszy w menu kontekstowym zostanie udostępniona opcja **Przełącz do trybu online** .

> [!NOTE]
> Polecenie **Przełącz do trybu offline** wysyła do urządzenia żądanie przełączenia woluminu w tryb offline. Jeśli hosty nadal korzystają z woluminu, spowoduje to uszkodzenie połączeń, ale przełączenie woluminu w tryb offline nie powiedzie się.

## <a name="delete-a-volume"></a>Usuwanie woluminu

> [!IMPORTANT]
> Wolumin można usunąć tylko wtedy, gdy jest w trybie offline.

Wykonaj następujące kroki, aby usunąć wolumin.

#### <a name="to-delete-a-volume"></a>Aby usunąć wolumin

1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie kliknij pozycję **Urządzenia**. Z tabelarycznej listy urządzeń wybierz urządzenie z woluminem, który ma zostać zmodyfikowany. Kliknij pozycję **ustawienia > woluminy**.

    ![Przejdź do bloku woluminów 4](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Sprawdź stan woluminu, który chcesz usunąć. Jeśli wolumin, który chcesz usunąć, nie jest w trybie offline, najpierw Przełącz go do trybu offline. Wykonaj kroki opisane w sekcji [Zrób wolumin w trybie offline](#take-a-volume-offline).
4. Gdy wolumin jest w trybie offline, wybierz wolumin, kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe, a następnie wybierz polecenie **Usuń**.

    ![Wybierz pozycję Usuń z menu kontekstowego](./media/storsimple-8000-manage-volumes-u2/deletevol1.png)

5. W bloku **Usuń** zapoznaj się z informacjami o wpływie usuwania woluminu i zaznacz pole wyboru. Po usunięciu woluminu zostaną utracone wszystkie dane znajdujące się na tym woluminie. 

    ![Zapisz i potwierdź zmiany](./media/storsimple-8000-manage-volumes-u2/deletevol2.png)

6. Po usunięciu woluminu tabelaryczna lista woluminów jest aktualizowana w celu wskazania usunięcia.

    ![Lista zaktualizowanych woluminów](./media/storsimple-8000-manage-volumes-u2/deletevol3.png)
   
   > [!NOTE]
   > Jeśli usuniesz wolumin przypięty lokalnie, miejsce dostępne dla nowych woluminów może nie zostać natychmiast zaktualizowane. Usługa StorSimple Menedżer urządzeń umożliwia okresowe aktualizowanie dostępnego miejsca lokalnego. Zalecamy poczekanie przez kilka minut przed podjęciem próby utworzenia nowego woluminu.
   >
   > Ponadto, jeśli usuniesz wolumin przypięty lokalnie, a następnie usuniesz inny wolumin przypięty lokalnie później, zadania usuwania woluminów zostaną uruchomione sekwencyjnie. Pierwsze zadanie usuwania woluminu musi zakończyć się przed rozpoczęciem kolejnego zadania usuwania woluminu.

## <a name="monitor-a-volume"></a>Monitorowanie woluminu

Monitorowanie woluminu umożliwia zbieranie danych statystycznych dotyczących operacji we/wy dla woluminu. Monitorowanie jest domyślnie włączone dla pierwszych 32 woluminów, które tworzysz. Monitorowanie dodatkowych woluminów jest domyślnie wyłączone. 

> [!NOTE]
> Monitorowanie sklonowanych woluminów jest domyślnie wyłączone.


Wykonaj następujące kroki, aby włączyć lub wyłączyć monitorowanie dla woluminu.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Aby włączyć lub wyłączyć monitorowanie woluminu

1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie kliknij pozycję **Urządzenia**. Z tabelarycznej listy urządzeń wybierz urządzenie z woluminem, który ma zostać zmodyfikowany. Kliknij pozycję **ustawienia > woluminy**.
2. Na tabelarycznej liście woluminów Wybierz wolumin, a następnie kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe. Wybierz pozycję **Modyfikuj**.
3. W bloku **Modyfikuj wolumin** w obszarze **monitorowanie** wybierz opcję **Włącz** lub **Wyłącz, aby włączyć** lub wyłączyć monitorowanie.

    ![Wyłączanie monitorowania](./media/storsimple-8000-manage-volumes-u2/monitorvol1.png) 

4. Kliknij przycisk **Zapisz** i po wyświetleniu monitu o potwierdzenie kliknij przycisk **tak**. Azure Portal wyświetla powiadomienie o zaktualizowaniu woluminu, a następnie komunikat o powodzeniu po pomyślnym zaktualizowaniu woluminu.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [sklonować wolumin StorSimple](storsimple-8000-clone-volume-u2.md).
* Dowiedz się [, jak zarządzać urządzeniem StorSimple przy użyciu usługi StorSimple Menedżer urządzeń](storsimple-8000-manager-service-administration.md).

