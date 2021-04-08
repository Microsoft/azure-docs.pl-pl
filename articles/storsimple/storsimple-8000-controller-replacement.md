---
title: Zastępowanie kontrolera urządzeń z serii StorSimple 8000 | Microsoft Docs
description: Wyjaśnia sposób usuwania i zastępowania jednego lub obu modułów kontrolera na urządzeniu z serii StorSimple 8000.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 9d8b75c48da2bb13d843258ead378d3e849da951
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85514077"
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Zastępowanie modułu kontrolera na urządzeniu StorSimple
## <a name="overview"></a>Omówienie
W tym samouczku wyjaśniono sposób usuwania i zastępowania jednego lub obu modułów kontrolera na urządzeniu StorSimple. Omówiono w nim również podstawową logikę dla scenariuszy zastępczych jednego i podwójnego kontrolera.

> [!NOTE]
> Przed przeprowadzeniem wymiany kontrolera zalecamy, aby zawsze aktualizować oprogramowanie układowe kontrolera do najnowszej wersji.
> 
> Aby zapobiec uszkodzeniu urządzenia StorSimple, nie należy wysunąć kontrolera, dopóki diody LED nie będą wyświetlane jako jedna z następujących:
> 
> * Wszystkie sygnalizatory są wyłączone.
> * Dioda LED 3, ![ zielonego znacznika wyboru ](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png) i ![ czerwona ikona krzyżyka ](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) są migane i dioda LED 0 i dioda LED 7 są **włączone**.


W poniższej tabeli przedstawiono obsługiwane scenariusze wymiany kontrolerów.

| Sprawa | Scenariusz zastępczy | Odpowiednia procedura |
|:--- |:--- |:--- |
| 1 |Jeden kontroler jest w stanie niepowodzenia, drugi kontroler jest w dobrej kondycji i aktywny. |[Zastępowanie jednego kontrolera](#replace-a-single-controller), które opisuje [logikę za jednym zastępowaniem kontrolera](#single-controller-replacement-logic), a także [kroki wymiany](#single-controller-replacement-steps). |
| 2 |Oba kontrolery zakończyły się niepowodzeniem i wymagają zastąpienia. Obudowy, dyski i obudowy dysków są w dobrej kondycji. |[Podwójne zastępowanie kontrolerów](#replace-both-controllers), które opisuje [logikę za zastępowaniem podwójnego kontrolera](#dual-controller-replacement-logic), a także [kroki zastępcze](#dual-controller-replacement-steps). |
| 3 |Kontrolery z tego samego urządzenia lub z różnych urządzeń są wymieniane. Obudowa, dyski i obudowy dysku są w dobrej kondycji. |Zostanie wyświetlony komunikat o niezgodności gniazda. |
| 4 |Brak jednego kontrolera i inny kontroler kończy się niepowodzeniem. |[Podwójne zastępowanie kontrolerów](#replace-both-controllers), które opisuje [logikę za zastępowaniem podwójnego kontrolera](#dual-controller-replacement-logic), a także [kroki zastępcze](#dual-controller-replacement-steps). |
| 5 |Co najmniej jeden kontroler zakończył się niepowodzeniem. Nie można uzyskać dostępu do urządzenia za pomocą konsoli szeregowej lub komunikacji zdalnej programu Windows PowerShell. |[Skontaktuj się pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md) , aby uzyskać ręczną procedurę zastępczą kontrolera. |
| 6 |Kontrolery mają inną wersję kompilacji, co może być spowodowane:<ul><li>Kontrolery mają inną wersję oprogramowania.</li><li>Kontrolery mają inną wersję oprogramowania układowego.</li></ul> |Jeśli wersje oprogramowania kontrolera są różne, logika wymiany wykrywa i aktualizuje wersję oprogramowania na kontrolerze zamiennym.<br><br>Jeśli wersje oprogramowania układowego kontrolera są różne i stara wersja oprogramowania układowego **nie** jest automatycznie uaktualniana, w Azure Portal zostanie wyświetlony komunikat o alercie. Należy przeprowadzić skanowanie w poszukiwaniu aktualizacji i zainstalować aktualizacje oprogramowania układowego.</br></br>Jeśli wersje oprogramowania układowego kontrolera są różne, a stara wersja oprogramowania układowego jest automatycznie uaktualniana, logika zastępowania kontrolerów wykryje ten stan, a po uruchomieniu kontrolera oprogramowanie układowe zostanie automatycznie zaktualizowane. |

Jeśli zakończyło się niepowodzeniem, należy usunąć moduł kontrolera. Jeden lub oba moduły kontrolera mogą zakończyć się niepowodzeniem, co może spowodować zastępowanie jednego kontrolera lub podwójnego kontrolera. Aby zapoznać się z procedurami zastępczymi i związanymi z nimi logiką, zobacz następujące tematy:

* [Wymiana jednego kontrolera](#replace-a-single-controller)
* [Zastąp oba kontrolery](#replace-both-controllers)
* [Usuwanie kontrolera](#remove-a-controller)
* [Wstaw kontroler](#insert-a-controller)
* [Identyfikowanie aktywnego kontrolera na urządzeniu](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> Przed usunięciem i zastępowaniem kontrolera zapoznaj się z informacjami o zabezpieczeniach [zastępują składniki sprzętowe StorSimple](storsimple-8000-hardware-component-replacement.md).
> 
> 

## <a name="replace-a-single-controller"></a>Wymiana jednego kontrolera
Jeśli jeden z dwóch kontrolerów na urządzeniu Microsoft Azure StorSimple nie powiódł się, działa nieprawidłowo lub nie jest zainstalowany, należy zastąpić pojedynczy kontroler.

### <a name="single-controller-replacement-logic"></a>Logika wymiany pojedynczego kontrolera
W przypadku wymiany jednego kontrolera należy najpierw usunąć kontroler zakończony niepowodzeniem. (Pozostały kontroler na urządzeniu jest aktywnym kontrolerem). Podczas wstawiania kontrolera wymiany są wykonywane następujące akcje:

1. Kontroler zamienny natychmiast rozpoczyna komunikację z urządzeniem StorSimple.
2. Migawka wirtualnego dysku twardego (VHD) dla aktywnego kontrolera jest kopiowana na kontrolerze zamiennym.
3. Migawka jest modyfikowana tak, że gdy kontroler zamienny zaczyna się od tego wirtualnego dysku twardego, zostanie rozpoznany jako kontroler gotowości.
4. Po zakończeniu modyfikacji kontroler zamienny zostanie uruchomiony jako kontroler gotowości.
5. Po uruchomieniu obu kontrolerów klaster jest dostępny w trybie online.

### <a name="single-controller-replacement-steps"></a>Kroki wymiany pojedynczego kontrolera
Jeśli jeden z kontrolerów na urządzeniu Microsoft Azure StorSimple nie powiedzie się, wykonaj następujące czynności. (Drugi kontroler musi być aktywny i uruchomiony. Jeśli oba kontrolery zakończą się niepowodzeniem lub nieprawidłowo, przejdź do [dwóch kroków zastępczych kontrolera](#dual-controller-replacement-steps).

> [!NOTE]
> Ponowne uruchomienie i całkowite odzyskanie kontrolera przez kontroler może potrwać od 30 do 45 minut. Całkowity czas dla całej procedury, w tym dołączenie kabli, wynosi około 2 godziny.


#### <a name="to-remove-a-single-failed-controller-module"></a>Aby usunąć jeden z uszkodzonych modułów kontrolera
1. W Azure Portal przejdź do usługi StorSimple Device Manager, kliknij pozycję **urządzenia**, a następnie kliknij nazwę urządzenia, które chcesz monitorować.
2. Przejdź do pozycji **monitoruj > kondycja sprzętu**. Stan kontrolera 0 lub 1 powinien mieć wartość czerwony, co oznacza awarię.
   
   > [!NOTE]
   > Kontroler zakończony niepowodzeniem w przypadku zastępowania pojedynczego kontrolera jest zawsze kontrolerem gotowości.
   
3. Użyj rysunku 1 i poniższej tabeli, aby zlokalizować moduł kontrolera zakończonego niepowodzeniem.
   
    ![Planowanie planu podstawowego modułów obudowy urządzenia](./media/storsimple-controller-replacement/IC740994.png)
   
    **Rysunek 1** Tył urządzenia StorSimple
   
   | Etykieta | Opis |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontroler 0 |
   | 4 |Kontroler 1 |
4. Na kontrolerze zakończonym niepowodzeniem Usuń wszystkie połączone kable sieciowe z portów danych. Jeśli używasz modelu 8600, Usuń również kable SAS łączące kontroler z kontrolerem EBOD.
5. Wykonaj kroki opisane w sekcji [Usuwanie kontrolera](#remove-a-controller) , aby usunąć kontroler zakończony niepowodzeniem.
6. Zainstaluj zastąpienie fabryki w tym samym miejscu, z którego został usunięty kontroler zakończony niepowodzeniem. Spowoduje to wyzwolenie logiki wymiany pojedynczego kontrolera. Aby uzyskać więcej informacji, zobacz [logika wymiany na jednym kontrolerze](#single-controller-replacement-logic).
7. Gdy logika wymiany na pojedynczym kontrolerze jest postępem w tle, podłącz kable ponownie. Należy zadbać o połączenie wszystkich kabli dokładnie tak samo, jak przed zastąpieniem.
8. Po ponownym uruchomieniu kontrolera Sprawdź **stan kontrolera** i **stan klastra** w Azure Portal, aby sprawdzić, czy kontroler jest w dobrej kondycji i czy jest w stanie wstrzymania.

> [!NOTE]
> Jeśli urządzenie jest monitorowane za pośrednictwem konsoli szeregowej, można zobaczyć wiele ponownych uruchomień, gdy kontroler odzyska odzyskiwanie z procedury zastępczej. Gdy zostanie wyświetlone menu konsoli szeregowej, wiadomo, że zastępowanie zostało zakończone. Jeśli menu nie jest wyświetlane w ciągu dwóch godzin od zastępowania kontrolera, [skontaktuj się z pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md).
>
> Począwszy od aktualizacji Update 4, można także użyć polecenia cmdlet `Get-HCSControllerReplacementStatus` w interfejsie programu Windows PowerShell urządzenia do monitorowania stanu procesu zastępowania kontrolera.
> 

## <a name="replace-both-controllers"></a>Zastąp oba kontrolery
W przypadku niepowodzenia obu kontrolerów na urządzeniu Microsoft Azure StorSimple są one wadliwe lub nie są dostępne, należy zamienić oba kontrolery. 

### <a name="dual-controller-replacement-logic"></a>Logika wymiany podwójnego kontrolera
W przypadku wymiany podwójnych kontrolerów należy najpierw usunąć zarówno kontrolery, które uległy awarii, jak i wstawiać zamiany. Po wstawieniu dwóch kontrolerów zastępczych są wykonywane następujące akcje:

1. Kontroler zastępczy w gnieździe 0 sprawdza następujące elementy:
   
   1. Czy są używane bieżące wersje oprogramowania układowego i oprogramowania?
   2. Czy jest częścią klastra?
   3. Czy kontroler równorzędny działa i czy jest on klastrowany?
      
      Jeśli żaden z tych warunków nie jest spełniony, kontroler szuka najnowszej codziennej kopii zapasowej (znajdującej się w **nonDOMstorage** na dysku S). Kontroler kopiuje najnowszą migawkę wirtualnego dysku twardego z kopii zapasowej.
2. Kontroler w gnieździe 0 używa migawki do samego obrazu.
3. W tym czasie kontroler w gnieździe 1 czeka na ukończenie tworzenia obrazu przez kontroler 0 i uruchomienie.
4. Po uruchomieniu kontrolera 0 Kontroler 1 wykryje klaster utworzony przez kontroler 0, który wyzwala logikę wymiany pojedynczego kontrolera. Aby uzyskać więcej informacji, zobacz [logika wymiany na jednym kontrolerze](#single-controller-replacement-logic).
5. Następnie na obu kontrolerach zostanie uruchomiony program, a klaster przejdzie w tryb online.

> [!IMPORTANT]
> Po przeprowadzeniu podwójnego kontrolera po skonfigurowaniu urządzenia StorSimple ważne jest ręczne wykonanie kopii zapasowej urządzenia. Codzienne kopie zapasowe konfiguracji urządzeń nie są wyzwalane po upływie 24 godzin. Aby ręcznie utworzyć kopię zapasową urządzenia, Pracuj z [Pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md) .


### <a name="dual-controller-replacement-steps"></a>Podwójne etapy wymiany kontrolera
Ten przepływ pracy jest wymagany w przypadku awarii obu kontrolerów na urządzeniu Microsoft Azure StorSimple. Może się to zdarzyć w centrum danych, w którym system chłodzenia przestanie działać i w związku z tym oba kontrolery kończą się niepowodzeniem w krótkim czasie. W zależności od tego, czy urządzenie StorSimple jest wyłączone, czy włączone i czy jest używany model 8600 lub 8100, wymagany jest inny zestaw kroków.

> [!IMPORTANT]
> Ponowne uruchomienie kontrolera i całkowite odzyskanie przez kontroler może potrwać od 45 minut do 1 godziny. Całkowity czas dla całej procedury, w tym dołączenie kabli, wynosi około 2,5 godzin.

#### <a name="to-replace-both-controller-modules"></a>Aby zamienić oba moduły kontrolera
1. Jeśli urządzenie jest wyłączone, Pomiń ten krok i przejdź do następnego kroku. Jeśli urządzenie jest włączone, wyłącz urządzenie.
   
   1. Jeśli korzystasz z modelu 8600, najpierw wyłącz podstawową obudowę, a następnie wyłącz obudowę EBOD.
   2. Zaczekaj, aż urządzenie zostanie całkowicie zamknięte. Wszystkie diody LED z tyłu urządzenia zostaną wyłączone.
2. Usuń wszystkie kable sieciowe połączone z portami danych. Jeśli używasz modelu 8600, Usuń również kable SAS łączące podstawową obudowę z obudową EBOD.
3. Usuń oba kontrolery z urządzenia StorSimple. Aby uzyskać więcej informacji, zobacz [Usuwanie kontrolera](#remove-a-controller).
4. Najpierw Wstaw zamiennik fabryczny dla kontrolera 0, a następnie włóż kontroler 1. Aby uzyskać więcej informacji, zobacz [Wstawianie kontrolera](#insert-a-controller). Spowoduje to wyzwolenie logiki zamiennej z podwójnym kontrolerem. Aby uzyskać więcej informacji, zobacz [logika wymiany podwójnego kontrolera](#dual-controller-replacement-logic).
5. Podczas gdy logika zastępowania kontrolera w tle, podłącz kable ponownie. Należy zadbać o połączenie wszystkich kabli dokładnie tak samo, jak przed zastąpieniem. Zapoznaj się ze szczegółowymi instrukcjami dotyczącymi modelu w sekcji Podłączanie urządzenia z [StorSimple 8100](storsimple-8100-hardware-installation.md) lub instalacją urządzenia [StorSimple 8600](storsimple-8600-hardware-installation.md).
6. Włącz urządzenie StorSimple. Jeśli używasz modelu 8600:
   
   1. Upewnij się, że obudowa EBOD jest włączona w pierwszej kolejności.
   2. Poczekaj na uruchomienie obudowy EBOD.
   3. Włącz obudowę obudowy podstawowej.
   4. Po ponownym uruchomieniu pierwszego kontrolera i stanie się on w dobrej kondycji system zostanie uruchomiony.
      
      > [!NOTE]
      > Jeśli urządzenie jest monitorowane za pośrednictwem konsoli szeregowej, można zobaczyć wiele ponownych uruchomień, gdy kontroler odzyska odzyskiwanie z procedury zastępczej. Gdy zostanie wyświetlone menu konsoli szeregowej, wiadomo, że zastępowanie zostało zakończone. Jeśli menu nie pojawia się w ciągu 2,5 godzin od rozpoczęcia zastępowania kontrolera, [skontaktuj się z pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md).
     
## <a name="remove-a-controller"></a>Usuwanie kontrolera
Aby usunąć błędny moduł kontrolera z urządzenia StorSimple, wykonaj czynności opisane w poniższej procedurze.

> [!NOTE]
> Poniższe ilustracje dotyczą kontrolera 0. W przypadku kontrolera 1 zostałyby one cofnięte.


#### <a name="to-remove-a-controller-module"></a>Aby usunąć moduł kontrolera
1. Opanujesz zatrzaśnięcie modułu między kciukiem i forefinger.
2. Łagodnie ściskanie kciuka i forefinger w celu zwolnienia zamka kontrolera.
   
    ![Zwalnianie zatrzaśnięcia kontrolera](./media/storsimple-controller-replacement/IC741047.png)
   
    **Rysunek 2** Zwalnianie zatrzaśnięcia kontrolera
3. Użyj zamka jako uchwytu, aby przesuwać kontroler z obudowy.
   
    ![Ruchomy kontroler z obudowy](./media/storsimple-controller-replacement/IC741048.png)
   
    **Rysunek 3** Przesuwanie kontrolera z obudowy

## <a name="insert-a-controller"></a>Wstaw kontroler
Aby zainstalować moduł kontrolera dostarczone fabrycznie po usunięciu wadliwego modułu z urządzenia StorSimple, należy wykonać poniższą procedurę.

#### <a name="to-install-a-controller-module"></a>Aby zainstalować moduł kontrolera
1. Sprawdź, czy występują jakieś uszkodzenia łączników interfejsu. Nie należy instalować modułu, jeśli którykolwiek z pinów łączników jest uszkodzony lub wygięty.
2. Przesuń moduł kontrolera do obudowy, gdy zatrzask jest w pełni wystawiony.
   
    ![Przesuwany kontroler do obudowy](./media/storsimple-controller-replacement/IC741053.png)
   
    **Rysunek 4** Przesuwany kontroler do obudowy
3. Po wstawieniu modułu kontroler Rozpocznij zamykanie zamka podczas kontynuowania wypychania modułu kontrolera do obudowy. Zamk zaangażuje się w Przewodnik po kontrolerze.
   
    ![Zamykanie zamka kontrolera](./media/storsimple-controller-replacement/IC741054.png)
   
    **Rysunek 5** Zamykanie zamka kontrolera
4. Po dodaniu zamka do miejsca. Teraz  powinna być włączona.
   
   > [!NOTE]
   > Aby można było aktywować kontroler i DIODa, może upłynąć do 5 minut.
  
5. Aby sprawdzić, czy zastępowanie zakończyło się pomyślnie, w Azure Portal przejdź do urządzenia, a następnie przejdź do pozycji **Monitoruj**  >  **kondycję sprzętu** i upewnij się, że zarówno kontroler 0, jak i kontroler 1 są w dobrej kondycji (stan jest zielony).

## <a name="identify-the-active-controller-on-your-device"></a>Identyfikowanie aktywnego kontrolera na urządzeniu
Istnieje wiele sytuacji, takich jak rejestracja urządzeń po raz pierwszy lub zastąpienie kontrolera, które wymagają zlokalizowania aktywnego kontrolera na urządzeniu StorSimple. Aktywny kontroler przetwarza wszystkie operacje sprzętowe i sieciowe na dysku. Można użyć dowolnej z poniższych metod identyfikacji aktywnego kontrolera:

* [Użyj Azure Portal, aby zidentyfikować aktywnego kontrolera](#use-the-azure-portal-to-identify-the-active-controller)
* [Użyj program Windows PowerShell dla usługi StorSimple, aby zidentyfikować aktywnego kontrolera](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Sprawdź urządzenie fizyczne, aby zidentyfikować aktywnego kontrolera](#check-the-physical-device-to-identify-the-active-controller)

Każda z tych procedur została opisana dalej.

### <a name="use-the-azure-portal-to-identify-the-active-controller"></a>Użyj Azure Portal, aby zidentyfikować aktywnego kontrolera
W Azure Portal przejdź do urządzenia, a następnie **Monitoruj**  >  **kondycję sprzętu** i przewiń do sekcji **Kontrolery** . W tym miejscu możesz sprawdzić, który kontroler jest aktywny.

![Identyfikowanie aktywnego kontrolera w Azure Portal](./media/storsimple-controller-replacement/IC752072.png)

**Rysunek 6** . Azure Portal wyświetlania aktywnego kontrolera

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Użyj program Windows PowerShell dla usługi StorSimple, aby zidentyfikować aktywnego kontrolera
Gdy uzyskujesz dostęp do urządzenia za pomocą konsoli szeregowej, zostanie wyświetlony komunikat transparentu. Komunikat transparent zawiera podstawowe informacje o urządzeniu, takie jak model, nazwa, zainstalowana wersja oprogramowania i stan kontrolera, do którego uzyskuje się dostęp. Na poniższej ilustracji przedstawiono przykład wiadomości transparentu:

![Wiadomość transparentu szeregowego](./media/storsimple-controller-replacement/IC741098.png)

**Rysunek 7** Wiadomość banerowa przedstawiająca kontroler 0 jako aktywny

Możesz użyć wiadomości transparentu, aby określić, czy kontroler, z którym nawiązano połączenie, jest aktywny, czy pasywny.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Sprawdź urządzenie fizyczne, aby zidentyfikować aktywnego kontrolera
Aby zidentyfikować aktywny kontroler na urządzeniu, zlokalizuj niebieskie diody LED powyżej portu DATA 5 z tyłu obudowy podstawowej.

W przypadku migotania kontroler jest aktywny, a drugi kontroler jest w trybie wstrzymania. Użyj poniższego diagramu i tabeli jako pomocy.

![Płyta podstawowa urządzenia — planowanie wstępne z portami danych](./media/storsimple-controller-replacement/IC741055.png)

**Rysunek 8** . Tył obudowy podstawowej z portami danych i diodami LED monitorowania

| Etykieta | Opis |
|:--- |:--- |
| 1-6 |DANE: 0 – 5 portów sieciowych |
| 7 |Niebieska dioda LED |

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [wymianie składników sprzętowych StorSimple](storsimple-8000-hardware-component-replacement.md).

