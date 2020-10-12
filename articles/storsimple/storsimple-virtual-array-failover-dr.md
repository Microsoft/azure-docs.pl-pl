---
title: Tryb failover i odzyskiwanie po awarii dla macierzy wirtualnej StorSimple
description: Informacje na temat odzyskiwania po awarii dla Microsoft Azure StorSimpleej macierzy wirtualnej, w tym szczegółowe kroki umożliwiające przełączenie w tryb failover do innej macierzy wirtualnej.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 3c1f9c62-af57-4634-a0d8-435522d969aa
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 24eb03069689d6dc89d8e237e0e65c71af6c6173
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88184724"
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>Disaster recovery and device failover for your StorSimple Virtual Array via Azure portal (Odzyskiwane po awarii i przełączanie urządzenia w tryb failover dla macierzy wirtualnej StorSimple w witrynie Azure Portal)

## <a name="overview"></a>Omówienie
W tym artykule opisano odzyskiwanie po awarii dla Microsoft Azure StorSimple macierzy wirtualnej, w tym szczegółowe kroki umożliwiające przełączenie w tryb failover do innej macierzy wirtualnej. W trybie failover można przenieść dane z urządzenia *źródłowego* w centrum danych na urządzenie *docelowe* . Urządzenie docelowe może znajdować się w tej samej lub innej lokalizacji geograficznej. Tryb failover urządzenia jest przeznaczony dla całego urządzenia. Podczas pracy w trybie failover dane w chmurze dla urządzenia źródłowego zmieniają własność na urządzenie docelowe.

Ten artykuł dotyczy tylko tablic wirtualnych StorSimple. Aby przełączyć urządzenie z serii 8000, przejdź do [trybu failover urządzenia i odzyskiwanie po awarii urządzenia StorSimple](storsimple-device-failover-disaster-recovery.md).

## <a name="what-is-disaster-recovery-and-device-failover"></a>Co to jest odzyskiwanie po awarii i tryb failover urządzenia?

W scenariuszu odzyskiwania po awarii urządzenie podstawowe przestaje działać. W tym scenariuszu dane w chmurze powiązane z urządzeniem zakończonym niepowodzeniem można przenieść na inne urządzenie. Urządzenia podstawowego można użyć jako *źródła* i określić inne urządzenie jako *element docelowy*. Ten proces jest określany jako *tryb failover*. Podczas pracy w trybie failover wszystkie woluminy lub udziały z urządzenia źródłowego zmieniają własność i są transferowane do urządzenia docelowego. Filtrowanie danych nie jest dozwolone.

Program DR jest modelowany jako pełne przywracanie urządzenia przy użyciu warstwowych i śledzenia opartych na mapie cieplnej. Mapa cieplna jest definiowana przez przypisanie wartości cieplnej do danych na podstawie wzorców odczytu i zapisu. Ta mapa cieplna powoduje, że najpierw warstwy najniższych fragmentów danych ciepła do chmury przy zachowaniu dużej ilości danych w warstwie lokalnej. Podczas odzyskiwania po awarii program StorSimple używa mapy cieplnej do przywracania i dehydratacji danych z chmury. Urządzenie pobiera wszystkie woluminy/udziały w ostatniej najnowszej kopii zapasowej (zgodnie z definicją wewnętrznie) i wykonuje przywracanie z tej kopii zapasowej. Macierz wirtualna organizuje cały proces odzyskiwania po awarii.

> [!IMPORTANT]
> Urządzenie źródłowe zostało usunięte po zakończeniu pracy w trybie failover, a więc powrót po awarii nie jest obsługiwany.
> 
> 

Odzyskiwanie po awarii jest zorganizowane przez funkcję przełączania do trybu failover urządzenia i jest inicjowane z bloku **urządzenia** . Ten blok umożliwia tabulację wszystkich urządzeń StorSimple podłączonych do usługi StorSimple Menedżer urządzeń. Dla każdego urządzenia można wyświetlić przyjazną nazwę, stan, zainicjowaną i maksymalną pojemność, typ i model.

## <a name="prerequisites-for-device-failover"></a>Wymagania wstępne dotyczące trybu failover urządzenia

### <a name="prerequisites"></a>Wymagania wstępne

W przypadku przełączenia urządzenia w tryb failover upewnij się, że spełnione są następujące wymagania wstępne:

* Urządzenie źródłowe musi być w stanie **dezaktywowania** .
* Urządzenie docelowe musi być widoczne jako **gotowe do skonfigurowania** w Azure Portal. Zainicjuj obsługę docelowej macierzy wirtualnej o tej samej lub większej pojemności. Użyj lokalnego interfejsu użytkownika sieci Web do skonfigurowania i pomyślnego zarejestrowania docelowej macierzy wirtualnej.
  
  > [!IMPORTANT]
  > Nie należy próbować konfigurować zarejestrowanego urządzenia wirtualnego za pomocą usługi. Żadna konfiguracja urządzenia nie powinna być wykonywana przez usługę.
  > 
  > 
* Urządzenie docelowe nie może mieć takiej samej nazwy jak urządzenie źródłowe.
* Urządzenie źródłowe i docelowe muszą być tego samego typu. Można przełączyć się w tryb failover macierzy wirtualnej skonfigurowanej jako serwer plików do innego serwera plików. Ta sama wartość dotyczy serwera iSCSI.
* W przypadku serwera plików DR zaleca się dołączenie urządzenia docelowego do tej samej domeny, co źródło. Ta konfiguracja gwarantuje, że uprawnienia udziału są automatycznie rozwiązywane. Obsługiwane jest tylko przejście w tryb failover do urządzenia docelowego w tej samej domenie.
* Dostępne urządzenia docelowe dla odzyskiwania po awarii to urządzenia o tej samej lub większej pojemności w porównaniu z urządzeniem źródłowym. Urządzenia, które są połączone z usługą, ale nie spełniają kryteriów wystarczającej ilości miejsca, nie są dostępne jako urządzenia docelowe.

### <a name="other-considerations"></a>Inne zagadnienia

* W przypadku planowanego przejścia w tryb failover:
  
  * Zalecamy, aby wszystkie woluminy lub udziały na urządzeniu źródłowym zostały przełączony do trybu offline.
  * Zalecamy wykonanie kopii zapasowej urządzenia, a następnie przejście do trybu failover w celu zminimalizowania utraty danych.
* W przypadku nieplanowanego przejścia w tryb failover urządzenie używa najnowszej kopii zapasowej do przywracania danych.

### <a name="device-failover-prechecks"></a>Sprawdzanie przed przełączeniem do trybu failover urządzenia

Przed rozpoczęciem odzyskiwania po awarii urządzenie wykonuje presprawdzanie. Te kontrole zapewniają, że nie występują żadne błędy po rozpoczęciu odzyskiwania. Sprawdzenia są następujące:

* Sprawdzanie poprawności konta magazynu.
* Sprawdzanie łączności z chmurą z platformą Azure.
* Sprawdzanie dostępnego miejsca na urządzeniu docelowym.
* Sprawdzanie, czy wolumin urządzenia źródłowego serwera iSCSI ma
  
  * prawidłowe nazwy ACR.
  * prawidłowa nazwa IQN (nie przekracza 220 znaków).
  * prawidłowe hasła protokołu CHAP (liczba znaków: 12-16).

Jeśli którykolwiek z poprzednich sprawdzania nie powiedzie się, nie można kontynuować wykonywania operacji odzyskiwania po awarii. Rozwiąż te problemy, a następnie ponów próbę wykonania operacji odzyskiwania po awarii.

Po pomyślnym zakończeniu odzyskiwania po awarii, własność danych w chmurze na urządzeniu źródłowym zostanie przetransferowana na urządzenie docelowe. Urządzenie źródłowe nie jest już dostępne w portalu. Dostęp do wszystkich woluminów/udziałów na urządzeniu źródłowym jest zablokowany, a urządzenie docelowe zostanie uaktywnione.

> [!IMPORTANT]
> Chociaż urządzenie nie jest już dostępne, maszyna wirtualna, która została zainicjowana w systemie hosta, nadal zużywa zasoby. Po pomyślnym zakończeniu odzyskiwania po awarii można usunąć tę maszynę wirtualną z systemu hosta.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Przechodzenie w tryb failover do macierzy wirtualnej

Zalecamy, aby przed uruchomieniem tej procedury zainicjować obsługę administracyjną, skonfigurować i zarejestrować kolejną macierz wirtualną StorSimple w usłudze StorSimple Menedżer urządzeń.

> [!IMPORTANT]
> 
> * Nie można przejść w tryb failover z urządzenia z serii StorSimple 8000 do urządzenia wirtualnego 1200.
> * Można przejść w tryb failover z urządzenia wirtualnego z obsługą FIPS (Federal Information Processing Standard) do innego urządzenia z obsługą FIPS lub na urządzenie niezgodne ze standardem FIPS wdrożone w portalu dla instytucji rządowych.


Wykonaj następujące kroki, aby przywrócić urządzenie do docelowego urządzenia wirtualnego StorSimple.

1. Inicjowanie obsługi administracyjnej i Konfigurowanie urządzenia docelowego spełniającego [wymagania wstępne dotyczące przełączenia urządzenia w tryb failover](#prerequisites). Zakończ konfigurację urządzenia za pomocą lokalnego interfejsu użytkownika sieci Web i zarejestruj go w usłudze StorSimple Menedżer urządzeń. W przypadku tworzenia serwera plików przejdź do kroku 1 [konfiguracji jako serwer plików](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device). W przypadku tworzenia serwera iSCSI przejdź do kroku 1 [konfiguracji jako serwer iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device).

2. Przełącz woluminy/udziały w tryb offline na hoście. Aby przełączyć woluminy/udziały w tryb offline, zapoznaj się z instrukcjami dotyczącymi hosta w systemie operacyjnym. Jeśli nie jest jeszcze w trybie offline, musisz przełączyć wszystkie woluminy/udziały w tryb offline na urządzeniu, wykonując następujące czynności.
   
    1. Przejdź do bloku **urządzenia** i wybierz urządzenie.
   
    2. Przejdź do pozycji **ustawienia > Zarządzanie udziałami >** (lub **ustawieniami > zarządzanie woluminami >**). 
   
    3. Wybierz udział/wolumin, kliknij prawym przyciskiem myszy i wybierz polecenie **Przełącz do trybu offline**. 
   
    4. Po wyświetleniu monitu o potwierdzenie Sprawdź, **jaki jest wpływ przełączenia tego udziału w tryb offline.** 
   
    5. Kliknij pozycję **Przełącz do trybu offline**.

3. W usłudze StorSimple Menedżer urządzeń przejdź do pozycji **zarządzanie > urządzenia**. W bloku **urządzenia** wybierz i kliknij swoje urządzenie źródłowe.

4. W bloku **pulpitu nawigacyjnego urządzenia** kliknij pozycję **Dezaktywuj**.

5. W bloku **dezaktywacji** zostanie wyświetlony monit o potwierdzenie. Dezaktywacja urządzenia jest *stałym* procesem, którego nie można cofnąć. Przypomnisz również, że udziały/woluminy zostaną przełączone do trybu offline na hoście. Wpisz nazwę urządzenia, aby potwierdzić, a następnie kliknij pozycję **Dezaktywuj**.
   
    ![Zrzut ekranu bloku dezaktywacji. Pole Nazwa urządzenia jest wypełnione, a przycisk Dezaktywuj jest wyróżniony.](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. Zostanie rozpoczęta dezaktywacja. Po pomyślnym zakończeniu dezaktywacji otrzymasz powiadomienie.
   
    ![Zrzut ekranu przedstawiający pasek postępu wskazujący, że urządzenie jest dezaktywowane.](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. Na stronie urządzenia stan urządzenia zostanie teraz zmieniony na **zdezaktywowane**.
    ![Zrzut ekranu przedstawiający stronę urządzeń. Zostaną wyświetlone właściwości dezaktywowanego urządzenia, w tym stan, który jest wymieniony jako zdezaktywowany.](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. W bloku **urządzenia** wybierz i kliknij dezaktywowane urządzenie źródłowe w celu przełączenia w tryb failover. 
9. W bloku **pulpitu nawigacyjnego urządzenia** kliknij pozycję **tryb failover**. 
10. W bloku **Praca** awaryjna urządzenia wykonaj następujące czynności:
    
    1. Pole urządzenie źródłowe jest wypełniane automatycznie. Zwróć uwagę na łączny rozmiar danych urządzenia źródłowego. Rozmiar danych powinien być mniejszy niż dostępna pojemność na urządzeniu docelowym. Przejrzyj szczegóły skojarzone z urządzeniem źródłowym, takie jak nazwa urządzenia, łączna pojemność i nazwy udziałów, które są przełączone w tryb failover.

    2. Z listy rozwijanej dostępne urządzenia wybierz **urządzenie docelowe**. Na liście rozwijanej są wyświetlane tylko urządzenia z wystarczającą pojemnością.

    3. Upewnij się, że **rozumiem, że ta operacja spowoduje przełączenie danych w tryb failover na urządzenie docelowe**. 

    4. Kliknij pozycję **tryb failover**.
    
        ![Zrzut ekranu przedstawiający blok przełączenia w tryb failover z wypełnionym urządzeniem źródłowym i docelowym, opcja zaznaczona i wyróżniony przycisk Przełącz do trybu failover.](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. Zadanie trybu failover jest inicjowane i otrzymujesz powiadomienie. Przejdź do pozycji **urządzenia > zadania** , aby monitorować tryb failover.
    
     ![Zrzut ekranu przedstawiający pasek postępu wskazujący, że urządzenie jest w trybie failover.](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. W bloku **zadania** zostanie wyświetlone zadanie trybu failover utworzone dla urządzenia źródłowego. To zadanie wykonuje pretesty odzyskiwania po awarii.
    
    ![Zrzut ekranu przedstawiający pomyślne uruchomienie zadania trybu failover.](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     Po pomyślnym sprawdzeniu przez program DR zadania trybu failover spowodują zduplikowanie zadań przywracania dla każdego udziału/woluminu, który istnieje na urządzeniu źródłowym.
    
    ![Zrzut ekranu przedstawiający szczegóły zadania trybu failover, takie jak stan, urządzenie i czas trwania.](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. Po zakończeniu pracy w trybie failover przejdź do bloku **urządzenia** .
    
    1. Wybierz i kliknij urządzenie StorSimple, które było używane jako urządzenie docelowe dla procesu przełączania do trybu failover.
    2. Przejdź do pozycji **ustawienia > zarządzanie > udziały** (lub **woluminy** , jeśli serwer iSCSI). W bloku **udziały** można wyświetlić wszystkie udziały (woluminy) ze starego urządzenia.
        ![Zrzut ekranu przedstawiający blok urządzeń. Urządzenie docelowe jest wyświetlane ze stanem online.](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. Należy [utworzyć alias DNS](https://support.microsoft.com/kb/168322) , aby wszystkie aplikacje, które próbują nawiązać połączenie, mogły zostać przekierowane do nowego urządzenia.

## <a name="errors-during-dr"></a>Błędy podczas odzyskiwania po awarii

**Awaria połączenia z chmurą podczas odzyskiwania po awarii**

Jeśli połączenie z chmurą zostanie zakłócone po rozpoczęciu odzyskiwania po awarii, a ukończenie przywracania urządzenia zakończy się niepowodzeniem. Otrzymasz powiadomienie o niepowodzeniu. Urządzenie docelowe dla odzyskiwania po awarii jest oznaczone jako *niemożliwe do użycia.* Nie można użyć tego samego urządzenia docelowego dla przyszłych DRs.

**Brak zgodnych urządzeń docelowych**

Jeśli dostępne urządzenia docelowe nie mają wystarczającej ilości miejsca, zobaczysz błąd, że nie ma żadnych zgodnych urządzeń docelowych.

**Błędy sprawdzania błędów**

Jeśli jedno z tych zaewidencjonowania nie jest spełnione, zobaczysz błędy sprawdzania wymagań.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Ciągłość działania — odzyskiwanie po awarii (BCDR)

Scenariusz ciągłości działania odzyskiwania po awarii (BCDR) występuje, gdy całe centrum danych platformy Azure przestanie działać. Może to mieć wpływ na usługę StorSimple Menedżer urządzeń i skojarzone urządzenia StorSimple.

W przypadku urządzeń StorSimple, które zostały zarejestrowane tuż przed wystąpieniem awarii, może być konieczne usunięcie tych urządzeń StorSimple. Po awarii możesz ponownie utworzyć i skonfigurować te urządzenia.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o sposobach [administrowania wirtualną macierzą StorSimple przy użyciu lokalnego interfejsu użytkownika sieci Web](storsimple-ova-web-ui-admin.md).

