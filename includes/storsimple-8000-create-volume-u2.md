---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 400c5f0b2281679c3ec913e94643ffec6082d9e6
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552347"
---
#### <a name="to-create-a-volume"></a>Aby utworzyć wolumin
1. Z tabelarycznej listy urządzeń w bloku **Urządzenia** wybierz swoje urządzenie. Kliknij pozycję **+ Dodaj wolumin**.<!--Note to reviewer. No screen updates. Had to rename screens to be able to correct a formatting error.-->

    ![Dodawanie nowego woluminu](./media/storsimple-8000-create-volume-u2/create-volume-01.png)

2. W bloku **Dodawanie woluminu**:
   
   1. Pole **Wybierz urządzenie** jest wypełniane automatycznie przy użyciu danych bieżącego urządzenia.

   2. Z listy rozwijanej wybierz kontener woluminów, do którego chcesz dodać wolumin. 

   3. Wpisz wartość pola **Nazwa** dla woluminu. Nie można zmienić nazwy woluminu po jego utworzeniu.

   4. Z listy rozwijanej wybierz wartość **Typ** dla woluminu. W przypadku obciążeń, które wymagają lokalnych gwarancji, małych opóźnień i większej wydajności, wybierz wolumin typu **Przypięty lokalnie**. W przypadku wszystkich innych danych wybierz wolumin typu **Warstwowy**. Jeśli używasz tego woluminu na potrzeby danych archiwalnych, wybierz opcję **Użyj tego woluminu w przypadku rzadziej używanych danych archiwalnych**.
      
       Wolumin warstwowy jest alokowany elastycznie i można go szybko utworzyć. W przypadku woluminu warstwowego przeznaczonego na dane archiwalne wybranie opcji **Użyj tego woluminu w przypadku rzadziej używanych danych archiwalnych** spowoduje zmianę rozmiaru fragmentu deduplikacji dla woluminu na 512 KB. Jeśli to pole nie zostanie zaznaczone, odpowiedni wolumin warstwowy użyje rozmiaru fragmentu wynoszącego 64 KB. Większy rozmiar fragmentu deduplikacji umożliwia urządzeniu usprawnienie transferu dużej ilości danych archiwalnych w chmurze.
       
       Wolumin przypięty lokalnie jest alokowany nieelastycznie i gwarantuje, że główne dane na woluminie pozostaną na urządzeniu lokalnym i nie zostaną przeniesione do chmury.  W przypadku tworzenia woluminu przypiętego lokalnie urządzenie sprawdza dostępne miejsce w warstwach lokalnych, aby zaalokować żądaną ilość miejsca na woluminie. Operacja tworzenia woluminów przypiętych lokalnie może obejmować przenoszenie istniejących danych z urządzenia do chmury i czas tworzenia woluminu może być długi. Łączny czas zależy od rozmiaru alokowanego woluminu, dostępnej przepustowości sieci i danych na urządzeniu.

   5. Określ **alokowaną pojemność** woluminu. Zanotuj wielkość pojemności dostępnej w oparciu o wybrany typ woluminu. Wybrany rozmiar woluminu nie może przekraczać wielkości dostępnego miejsca.
      
       Na urządzeniu 8100 można alokować woluminy przypięte lokalnie do 8,5 TB lub woluminy warstwowe do 200 TB. Na większym urządzeniu 8600 można alokować woluminy przypięte lokalnie do 22,5 TB lub woluminy warstwowe do 500 TB. Ponieważ lokalne miejsce na urządzeniu jest wymagane do obsługi zestawu roboczego woluminów warstwowych, tworzenie woluminów przypiętych lokalnie ma wpływ na miejsce dostępne do aprowizacji na woluminach warstwowych. Dlatego jeśli tworzysz wolumin przypięty lokalnie, miejsce dostępne na potrzeby tworzenia woluminów warstwowych zmniejsza się. Podobnie jeśli utworzysz wolumin warstwowy, miejsce dostępne na potrzeby tworzenia woluminów przypiętych lokalnie zmniejszy się.
      
       W przypadku alokowania woluminu przypiętego lokalnie o rozmiarze 8,5 TB (maksymalny dozwolony rozmiar) na urządzeniu 8100 całe lokalne miejsce dostępne na urządzeniu zostanie wyczerpane. Od tego momentu nie można tworzyć woluminów warstwowych, ponieważ w urządzeniu nie ma już miejsca lokalnego do hostowania roboczego zestawu woluminu warstwowego. Istniejące woluminy warstwowe również wpływają na dostępne miejsce. Jeśli na przykład masz urządzenie 8100 z woluminami warstwowymi o wielkości około 106 TB, tylko 4 TB są dostępne dla woluminów przypiętych lokalnie.
 
      1. W polu **Połączone hosty** kliknij strzałkę. 

         ![Połączone hosty](./media/storsimple-8000-create-volume-u2/create-volume-02.png)

      1. W bloku **Połączone hosty** wybierz istniejący rekord ACR lub dodaj nowy rekord ACR, wykonując następujące kroki:

         1. Wypełnij pole **Nazwa** dla rekordu ACR.
         2. W obszarze **Nazwa inicjatora iSCSI** podaj kwalifikowaną nazwę iSCSI (IQN) hosta z systemem Windows. Jeśli nie masz nazwy IQN, przejdź do tematu [Pobieranie nazwy IQN hosta z systemem Windows Server](#get-the-iqn-of-a-windows-server-host).

      1. Kliknij pozycję **Utwórz**. Wolumin zostanie utworzony przy użyciu wybranych ustawień.

         ![Kliknięcie pozycji Utwórz](./media/storsimple-8000-create-volume-u2/create-volume-03.png)

         > [!NOTE]
         > Należy pamiętać, że wolumin, który został utworzony w tym miejscu, nie jest chroniony. Aby móc tworzyć zaplanowane kopie zapasowe, konieczne będzie utworzenie zasad tworzenia kopii zapasowych oraz powiązanie ich z tym woluminem. 

