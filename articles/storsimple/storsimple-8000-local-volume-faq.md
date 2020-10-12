---
title: StorSimple woluminy przypięte lokalnie — często zadawane pytania | Microsoft Docs
description: Zawiera odpowiedzi na często zadawane pytania dotyczące StorSimple woluminów przypiętych lokalnie.
services: storsimple
documentationcenter: NA
author: manuaery
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2017
ms.author: manuaery
ms.openlocfilehash: 483fa81b409e1bd740af85b431a86b6c814831e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85511510"
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple woluminy przypięte lokalnie: często zadawane pytania
## <a name="overview"></a>Omówienie
Poniżej znajdują się pytania i odpowiedzi, które mogą wystąpić podczas tworzenia woluminu przypiętego lokalnie StorSimple, konwertowania woluminu warstwowego na wolumin przypięty lokalnie (i odwrotnie) lub tworzenia kopii zapasowej i przywracania woluminu przypiętego lokalnie.

Pytania i odpowiedzi są podzielone na następujące kategorie:

* Tworzenie woluminu przypiętego lokalnie
* Tworzenie kopii zapasowej przypiętej lokalnie
* Konwertowanie woluminu warstwowego na wolumin przypięty lokalnie
* Przywracanie woluminu przypiętego lokalnie
* Przechodzenie w tryb failover na woluminie przypiętym lokalnie

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Pytania dotyczące tworzenia woluminu przypiętego lokalnie
**Pytania.** Jaki jest maksymalny rozmiar woluminu przypiętego lokalnie, który można utworzyć na urządzeniach z serii 8000?

**A** na urządzeniach z systemem StorSimple 8000 z aktualizacją Update 3,0 można obsługiwać woluminy przypięte lokalnie do 8,5 TB lub woluminy warstwowe do 200 TB 8100 na urządzeniu. Na większym urządzeniu 8600 można alokować woluminy przypięte lokalnie do 22,5 TB lub woluminy warstwowe do 500 TB.

**Pytania.** Ostatnio uaktualniono moje urządzenie 8100 w celu zaktualizowania 3,0 i gdy próbuję utworzyć wolumin przypięty lokalnie, maksymalny dostępny rozmiar to 6 TB i nie 8,5 TB. Dlaczego nie mogę utworzyć woluminu o pojemności 8,5 TB?

**A** Jeśli na urządzeniu jest uruchomiona aktualizacja 3,0, można zarezerwować woluminy przypięte lokalnie do 8,5 TB lub woluminów warstwowych do 200 TB na urządzeniu 8100. Jeśli urządzenie ma już woluminy warstwowe, miejsce dostępne na potrzeby tworzenia woluminu przypiętego lokalnie będzie mniejsze niż ten maksymalny limit. Na przykład jeśli około 106 TB woluminów warstwowych została już zainicjowana na urządzeniu 8100 (czyli połowie pojemności warstwowej), maksymalny rozmiar woluminu lokalnego, który można utworzyć na urządzeniu 8100, zostanie odpowiednio zmniejszony do 4 TB (w przybliżeniu połowa maksymalnej pojemności przypiętej lokalnie).

Ponieważ niektóre lokalne miejsce na urządzeniu jest używane do hostowania zestawu roboczego woluminów warstwowych, dostępne miejsce na potrzeby tworzenia woluminu przypiętego lokalnie jest zmniejszane, jeśli urządzenie ma woluminy warstwowe. Z drugiej strony, Tworzenie woluminu przypiętego lokalnie zmniejsza ilość dostępnego miejsca dla woluminów warstwowych. W poniższych tabelach przedstawiono podsumowanie dostępnej pojemności warstwowej na urządzeniach 8100 i 8600 podczas tworzenia woluminów przypiętych lokalnie.

#### <a name="update-30"></a>Aktualizacja 3,0 

| Wolumin przypięty lokalnie | Dostępna pojemność do aprowizacji woluminów warstwowych — 8100 | Dostępna pojemność do aprowizacji woluminów warstwowych — 8600 |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176,5 TB |477,8 TB |
| 4 TB |105,9 TB |411,1 TB |
| 8,5 TB |0 TB |311,1 TB |
| 10 TB |Nie dotyczy |277,8 TB |
| 15 TB |Nie dotyczy |166,7 TB |
| 22,5 TB |Nie dotyczy |0 TB |

**Pytania.** Dlaczego przypięty wolumin lokalnie jest czasochłonną operacją?

**Z.** Woluminy przypięte lokalnie są alokowane elastycznie. Aby można było utworzyć miejsce w warstwach lokalnych urządzenia, niektóre dane z istniejących woluminów warstwowych mogą zostać wypchnięte do chmury podczas procesu aprowizacji. Ponieważ jest to zależne od rozmiaru woluminu, który jest inicjowany, istniejące dane na urządzeniu i dostępną przepustowość do chmury, czas potrzebny na utworzenie woluminu lokalnego może być kilka godzin.

**Pytania.** Jak długo trwa Tworzenie woluminu przypiętego lokalnie?

**Z.** Ze względu na to, że woluminy przypięte lokalnie są alokowane elastycznie, niektóre istniejące dane z woluminów warstwowych mogą być wypychane do chmury podczas procesu aprowizacji. W związku z tym czas potrzebny na utworzenie woluminu przypiętego lokalnie zależy od wielu czynników, takich jak rozmiar woluminu, dane na urządzeniu i dostępną przepustowość. Na świeżo zainstalowanym urządzeniu, które nie ma woluminów, czas na utworzenie woluminu przypiętego lokalnie wynosi około 10 minut na terabajt danych. Jednak Tworzenie woluminów lokalnych może potrwać kilka godzin w zależności od czynników opisanych powyżej na urządzeniu, które jest w użyciu.

**Pytania.** Chcę utworzyć wolumin przypięty lokalnie. Czy istnieją najlepsze rozwiązania, których należy wiedzieć?

**Z.** Woluminy przypięte lokalnie są odpowiednie dla obciążeń, które wymagają lokalnych gwarancji danych przez cały czas i są wrażliwe na opóźnienia w chmurze. Rozważając użycie woluminów lokalnych dla dowolnego z obciążeń, należy pamiętać o następujących kwestiach:

* Woluminy przypięte lokalnie są alokowane elastycznie i Tworzenie woluminów lokalnych ma wpływ na dostępne miejsce dla woluminów warstwowych. W związku z tym sugerujemy rozpoczęcie od woluminów o mniejszym rozmiarze i skalowanie w górę w miarę wzrostu wymagań dotyczących magazynu.
* Inicjowanie obsługi woluminów lokalnych to długotrwała operacja, która może polegać na wypchnięciu istniejących danych z woluminów warstwowych do chmury. W związku z tym może wystąpić zmniejszona wydajność na tych woluminach.
* Inicjowanie obsługi woluminów lokalnych jest czasochłonną operacją. Rzeczywisty czas trwania zależy od wielu czynników: rozmiar woluminu, który jest inicjowany, dane na urządzeniu i dostępna przepustowość. Jeśli nie wykonano kopii zapasowej istniejących woluminów w chmurze, Tworzenie woluminów jest wolniejsze. Przed udostępnieniem woluminu lokalnego zalecamy wykonanie migawek w chmurze istniejących woluminów.
* Istniejące woluminy warstwowe można przekonwertować na woluminy przypięte lokalnie, a ta konwersja obejmuje zainicjowanie miejsca na urządzeniu dla powstającego woluminu przypiętego lokalnie (oprócz przenoszenia danych warstwowych, jeśli istnieją, w chmurze). Jest to długotrwała operacja, która zależy od czynników, które zostały omówione powyżej. Zalecamy utworzenie kopii zapasowej istniejących woluminów przed konwersją, ponieważ proces będzie jeszcze wolniejszy, jeśli nie zostanie utworzona kopia zapasowa istniejących woluminów. W trakcie tego procesu może również wystąpić zmniejszona wydajność urządzenia.

Więcej informacji na temat [tworzenia woluminu przypiętego lokalnie](storsimple-8000-manage-volumes-u2.md#add-a-volume)

**Pytania.** Czy można utworzyć wiele lokalnie przypiętych woluminów w tym samym czasie?

**Z.** Tak, ale wszystkie lokalnie przypięte zadania tworzenia i rozwijania woluminów są przetwarzane sekwencyjnie.

Woluminy przypięte lokalnie są alokowane elastycznie i wymagają utworzenia lokalnego miejsca na urządzeniu (co może spowodować wypychanie istniejących danych z woluminów warstwowych do chmury podczas procesu aprowizacji). W związku z tym, jeśli zadanie aprowizacji jest w toku, inne zadania tworzenia woluminu lokalnego zostaną dodane do kolejki do momentu zakończenia tego zadania.

Podobnie, jeśli istniejący wolumin lokalny jest rozwinięty lub wolumin warstwowy jest konwertowany na wolumin przypięty lokalnie, tworzenie nowego przypiętego lokalnie jest umieszczane w kolejce do momentu ukończenia poprzedniego zadania. Rozszerzanie rozmiaru woluminu przypiętego lokalnie obejmuje rozszerzenie istniejącego lokalnego miejsca dla tego woluminu. Konwersja z warstwy na wolumin przypięty lokalnie obejmuje również Tworzenie lokalnego miejsca dla powstającego woluminu przypiętego lokalnie. W obu tych operacjach, tworzenie lub rozszerzanie miejsca lokalnego to długotrwałe zadanie.

Te zadania można wyświetlić w bloku **zadania** usługi StorSimple Menedżer urządzeń. Zadanie, które jest aktywnie przetwarzane, jest aktualizowane w sposób ciągły, aby odzwierciedlało postęp udostępniania miejsca. Pozostałe woluminy przypięte lokalnie są oznaczane jako uruchomione, ale ich postęp jest wstrzymany i są one wybierane w kolejności, w której zostały dodane do kolejki.

**Pytania.** Został usunięty wolumin przypięty lokalnie. Dlaczego odzyskiwane miejsce nie jest widoczne w dostępnym miejscu, gdy próbuję utworzyć nowy wolumin?

**Z.** Jeśli usuniesz wolumin przypięty lokalnie, miejsce dostępne dla nowych woluminów może nie zostać natychmiast zaktualizowane. Usługa StorSimple Menedżer urządzeń aktualizuje miejsce lokalne dostępne co godzinę. Zalecamy poczekanie na godzinę przed podjęciem próby utworzenia nowego woluminu.

**Pytania.** Czy woluminy przypięte lokalnie są obsługiwane na urządzeniu w chmurze?

**Z.** Woluminy przypięte lokalnie nie są obsługiwane w urządzeniu w chmurze (urządzenia 8010 i 8020, wcześniej określane jako urządzenie wirtualne StorSimple).

**Pytania.** Czy można używać poleceń cmdlet Azure PowerShell do tworzenia woluminów przypiętych lokalnie i zarządzania nimi?

**Z.** Nie, nie można tworzyć woluminów przypiętych lokalnie za pomocą poleceń cmdlet Azure PowerShell (każdy wolumin tworzony za pośrednictwem Azure PowerShell jest warstwowy). Zalecamy również, aby nie używać poleceń cmdlet Azure PowerShell, aby modyfikować wszystkie właściwości woluminu przypiętego lokalnie, ponieważ będzie on miał nieoczekiwany efekt modyfikacji typu woluminu na warstwowy.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Pytania dotyczące tworzenia kopii zapasowej woluminu przypiętego lokalnie
**Pytania.** Czy są to lokalne migawki obsługiwane lokalnie przypiętych woluminów?

**Z.** Tak, możesz utworzyć lokalne migawki woluminów przypiętych lokalnie. Zdecydowanie zaleca się, aby regularnie tworzyć kopie zapasowe przypiętych lokalnie woluminów z migawkami w chmurze, aby zapewnić ochronę danych w przypadku awarii.

Należy pamiętać, że lokalne migawki woluminów przypiętych lokalnie mogą również znajdować się w warstwie w chmurze i nie mogą pozostać w warstwie lokalnej urządzenia.

**Pytania.** Czy istnieją jakieś wskazówki dotyczące zarządzania migawkami lokalnymi dla woluminów przypiętych lokalnie?

**Z.** Częste migawki lokalne z dużą szybkością zmiany danych na woluminie przypiętym lokalnie mogą spowodować, że lokalne miejsce na urządzeniu ma być używane szybko i spowodować, że dane z woluminów warstwowych są przekazywane do chmury. W związku z tym sugerujemy zminimalizowanie liczby lokalnych migawek.

**Pytania.** Otrzymuję alert informujący o tym, że moje lokalne migawki woluminów przypiętych lokalnie mogą być unieważnione. Kiedy to się dzieje?

**Z.** Częste migawki lokalne z dużą szybkością zmiany danych na woluminie przypiętym lokalnie mogą spowodować szybkie użycie lokalnego miejsca na urządzeniu. Jeśli warstwa lokalna urządzenia jest intensywnie używana, rozszerzona awaria w chmurze może spowodować zapełnienie urządzenia, a operacje zapisu przychodzącego na woluminie mogą spowodować unieważnienie migawek (bez odstępów w celu zaktualizowania migawek, aby odwołać się do starszych bloków danych, które zostały nadpisywane). W takiej sytuacji zapisywanie w woluminie będzie nadal obsługiwane, ale migawki lokalne mogą być nieprawidłowe. Nie ma to wpływu na istniejące migawki w chmurze.

Ostrzeżenie o alercie polega na tym, że taka sytuacja może wystąpić, i upewnić się, że jest to możliwe w odpowiednim czasie, przez przejrzenie harmonogramów migawek lokalnych w celu przetworzenia mniejszych migawek lokalnych lub usunięcia starszych migawek lokalnych, które nie są już wymagane.

Jeśli migawki lokalne są unieważnione, zostanie wyświetlony alert informacyjny z informacją o tym, że lokalne migawki dla określonych zasad tworzenia kopii zapasowych zostały unieważnione wraz z listą sygnatur czasowych migawek lokalnych, które zostały unieważnione. Te migawki zostaną usunięte i nie będą już mogły być wyświetlane w bloku **wykazów kopii zapasowych** w Azure Portal.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Pytania dotyczące konwertowania woluminu warstwowego na wolumin przypięty lokalnie
**Pytania.** Podczas konwertowania woluminu warstwowego na wolumin przypięty lokalnie zaobserwuję trochę wolnego czasu na urządzeniu. Dlaczego tak się dzieje?

**Z.** Proces konwersji obejmuje dwa kroki:

1. Inicjowanie obsługi miejsca na urządzeniu na potrzeby przekonwertowanego woluminu przypiętego lokalnie.
2. Pobieranie danych warstwowych z chmury w celu zapewnienia lokalnych gwarancji.

Oba te kroki są długotrwałymi operacjami zależnymi od rozmiaru konwertowanego woluminu, danych na urządzeniu i dostępnej przepustowości. Ponieważ niektóre dane z istniejących woluminów warstwowych mogą zostać przelane do chmury w ramach procesu aprowizacji, w tym czasie urządzenie może obniżyć wydajność. Ponadto proces konwersji może być wolniejszy, jeśli:

* Nie wykonano kopii zapasowej istniejących woluminów w chmurze; Zalecamy, aby utworzyć kopię zapasową woluminów przed zainicjowaniem konwersji.
* Zasady ograniczania przepustowości zostały zastosowane, co może ograniczyć dostępną przepustowość do chmury; w związku z tym zalecamy korzystanie z dedykowanego 40 MB/s lub większej liczby połączeń z chmurą.
* Proces konwersji może potrwać kilka godzin, ze względu na wiele czynników opisanych powyżej; w związku z tym sugerujemy wykonanie tej operacji w czasie poza szczytem lub w weekend, aby uniknąć wpływu na użytkowników końcowych.

Więcej informacji na temat [konwertowania woluminu warstwowego na wolumin przypięty lokalnie](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)

**Pytania.** Czy mogę anulować operację konwersji woluminu?

**Z.** Nie, nie można anulować operacji konwersji po zainicjowaniu. Zgodnie z opisem w powyższym pytaniu należy pamiętać o potencjalnych problemach z wydajnością, które mogą wystąpić podczas procesu, i postępować zgodnie z najlepszymi rozwiązaniami wymienionymi powyżej podczas planowania konwersji.

**Pytania.** Co się stanie z moim woluminem, jeśli operacja konwersji nie powiedzie się?

**Z.** Konwersja woluminu może zakończyć się niepowodzeniem z powodu problemów z łącznością z chmurą. Urządzenie może ostatecznie zatrzymać proces konwersji po serii nieudanych prób przełączenia danych warstwowych z chmury. W takim scenariuszu Typ woluminu będzie nadal typem woluminu źródłowego przed konwersją i:

* Zostanie zgłoszony alert krytyczny informujący o niepowodzeniu konwersji woluminu. Więcej informacji na temat [alertów dotyczących woluminów przypiętych lokalnie](storsimple-8000-manage-alerts.md#locally-pinned-volume-alerts)
* W przypadku konwertowania warstwowego na wolumin przypięty lokalnie, wolumin będzie nadal wykazywał właściwości woluminu warstwowego, ponieważ dane mogą nadal znajdować się w chmurze. Zalecamy rozwiązanie problemów z łącznością, a następnie ponów próbę wykonania operacji konwersji.
* Podobnie, gdy konwersja z lokalnie przypięta do woluminu warstwowego kończy się niepowodzeniem, mimo że wolumin zostanie oznaczony jako wolumin przypięty lokalnie, będzie działać jako wolumin warstwowy (ponieważ dane mogły zostać rozlane w chmurze). Będzie jednak nadal zajmował miejsce w warstwach lokalnych urządzenia. Ten obszar nie będzie dostępny dla innych woluminów przypiętych lokalnie. Zalecamy ponowienie tej operacji, aby upewnić się, że konwersja woluminów została ukończona, a lokalne miejsce na urządzeniu może być odzyskiwane.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Pytania dotyczące przywracania woluminu przypiętego lokalnie
**Pytania.** Czy woluminy przypięte lokalnie są przywracane natychmiast?

**Z.** Tak. woluminy przypięte lokalnie są przywracane natychmiastowo. Gdy tylko metadane woluminu są pobierane z chmury w ramach operacji przywracania, wolumin jest przełączany w tryb online i jest dostępny dla hosta. Jednak lokalne gwarancje dla danych woluminu nie będą obecne, dopóki nie zostaną pobrane wszystkie dane z chmury, a wydajność przywracania może być ograniczona.

**Pytania.** Jak długo trwa przywracanie woluminu przypiętego lokalnie?

**Z.** Woluminy przypięte lokalnie są przywracane natychmiast i przenoszone do trybu online zaraz po pobraniu informacji o metadanych woluminu z chmury, podczas gdy dane woluminu nadal są pobierane w tle. Ta ostatnia część operacji przywracania — przywrócenie lokalnych gwarancji dla danych woluminu — jest długotrwałą operacją i może potrwać kilka godzin, aby ponownie uruchomić wszystkie dane. Czas potrzebny do ukończenia tego samego procesu zależy od wielu czynników, takich jak rozmiar przywracanego woluminu i dostępna przepustowość. W przypadku usunięcia oryginalnego przywracanego woluminu zostanie podjęta dodatkowa godzina w celu utworzenia lokalnego miejsca na urządzeniu w ramach operacji przywracania.

**Pytania.** Chcę przywrócić swój istniejący wolumin przypięty lokalnie do starszej migawki (pobrany, gdy wolumin został warstwowy). Czy wolumin zostanie przywrócony jako warstwowy w tym przypadku?

**Z.** Nie, wolumin zostanie przywrócony jako wolumin przypięty lokalnie. Mimo że migawki dat są zależne od czasu, gdy wolumin został warstwowy, podczas przywracania istniejących woluminów StorSimple zawsze używa typu woluminu na dysku, ponieważ już istnieje.

**Pytania.** Ostatnio został rozszerzony wolumin przypięty lokalnie, ale teraz trzeba przywrócić dane do momentu mniejszego rozmiaru woluminu. Czy program zmieni rozmiar bieżącego woluminu i będzie musiał zwiększyć rozmiar woluminu po zakończeniu przywracania?

**Z.** Tak, przywrócenie spowoduje zmianę rozmiaru woluminu i będzie konieczne zwiększenie rozmiaru woluminu po zakończeniu przywracania.

**Pytania.** Czy mogę zmienić typ woluminu podczas przywracania?

**Z.** Nie, nie można zmienić typu woluminu podczas przywracania.

* Woluminy, które zostały usunięte, są przywracane jako typ przechowywany w migawce.
* Istniejące woluminy są przywracane na podstawie ich bieżącego typu, niezależnie od typu przechowywanego w migawce (zobacz poprzednie dwa pytania).

**Pytania.** Chcę przywrócić wolumin przypięty lokalnie, ale wybrano nieprawidłową migawkę w czasie. Czy mogę anulować bieżącą operację przywracania?

**Z.** Tak, możesz anulować trwającą operację przywracania. Stan woluminu zostanie przywrócony z powrotem do stanu na początku przywracania. Jednak wszelkie zapisy wprowadzone do woluminu w trakcie przywracania zostaną utracone.

**Pytania.** Uruchomiono operację przywracania na jednym z woluminów przypiętych lokalnie, a teraz w katalogu zaległości pojawia się migawka, którą nie zbieram ponownie. Do czego służy?

**Z.** Jest to tymczasowa migawka utworzona przed operacją przywracania i jest używana do wycofywania na wypadek, gdyby Przywracanie zostało anulowane lub nie powiodło się. Nie usuwaj tej migawki; zostanie ona automatycznie usunięta po zakończeniu przywracania. Takie zachowanie może wystąpić, jeśli zadanie przywracania ma tylko woluminy przypięte lokalnie lub mieszaninę przypiętą lokalnie i woluminy warstwowe. Jeśli zadanie przywracania obejmuje tylko woluminy warstwowe, to zachowanie nie zostanie wykonane.

**Pytania.** Czy można sklonować wolumin przypięty lokalnie?

**Z.** Tak, możesz. Jednak wolumin przypięty lokalnie zostanie sklonowany jako wolumin warstwowy domyślnie. Więcej informacji na temat [klonowania woluminu przypiętego lokalnie](storsimple-8000-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Pytania dotyczące przełączenia awaryjnego woluminu przypiętego lokalnie
**Pytania.** Muszę przełączyć urządzenie do trybu failover na innym urządzeniu fizycznym. Czy moje woluminy przypięte lokalnie zostaną przełączone w tryb failover jako przypięty lokalnie czy warstwowe?

**Z.** Woluminy przypięte lokalnie są przenoszone do trybu failover jako przypięte lokalnie, jeśli na urządzeniu docelowym jest uruchomiony program StorSimple 8000 Series Update 3 lub nowszy.

Więcej informacji na temat [trybu failover i odzyskiwania woluminów przypiętych lokalnie w różnych wersjach](storsimple-8000-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**Pytania.** Czy woluminy przypięte lokalnie są przywracane natychmiast podczas odzyskiwania po awarii (DR)?

**Z.** Tak, woluminy przypięte lokalnie są przywracane natychmiast podczas pracy w trybie failover. Gdy tylko metadane woluminu są pobierane z chmury w ramach operacji przejścia w tryb failover, wolumin zostanie przełączony w tryb online na urządzeniu docelowym i będzie dostępny dla hosta. W tym czasie dane woluminu będą nadal pobierane w tle, a na tych woluminach może wystąpić zmniejszona wydajność w czasie trwania przejścia w tryb failover.

**Pytania.** Jak zostało wykonane zadanie trybu failover, jak śledzić postęp woluminu przypiętego lokalnie, który jest przywracany na urządzeniu docelowym?

**Z.** Podczas pracy w trybie failover zadanie pracy awaryjnej jest oznaczane jako ukończone, gdy wszystkie woluminy w zestawie trybu failover zostaną natychmiast przywrócone i przenoszone do trybu online na urządzeniu docelowym. Obejmuje to wszystkie woluminy przypięte lokalnie, które mogły zostać przełączone do trybu failover; jednak lokalne gwarancje danych będą dostępne tylko wtedy, gdy zostaną pobrane wszystkie dane dla danego woluminu. Ten postęp można śledzić dla każdego woluminu przypiętego lokalnie, który został przetworzony w trybie failover, monitorując odpowiednie zadania przywracania, które są tworzone w ramach przejścia w tryb pracy awaryjnej. Te zadania przywracania są tworzone tylko dla woluminów przypiętych lokalnie.

**Pytania.** Czy mogę zmienić typ woluminu podczas pracy w trybie failover?

**Z.** Nie, nie można zmienić typu woluminu podczas pracy w trybie failover. W przypadku przełączenia w tryb failover na innym urządzeniu fizycznym z uruchomioną StorSimple 8000 serii Update 3 woluminy są przełączane w tryb failover na podstawie typu woluminu przechowywanego w migawce.

**Pytania.** Czy można przenieść kontener woluminów do trybu failover za pomocą lokalnie przypiętych woluminów do urządzenia w chmurze?

**Z.** Tak, możesz. Woluminy przypięte lokalnie zostaną przełączone w tryb failover jako woluminy warstwowe. Więcej informacji na temat [trybu failover i odzyskiwania woluminów przypiętych lokalnie w różnych wersjach](storsimple-8000-device-failover-disaster-recovery.md#common-considerations-for-device-failover)

