---
title: Monitorowanie urządzenia z serii StorSimple 8000
description: Opisuje, jak używać usługi StorSimple Menedżer urządzeń do monitorowania użycia, wydajności we/wy i wykorzystania pojemności.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: 1c2df017a9af47bfa330c8e0fa7561bf5b2d5bae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85514698"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>Monitorowanie urządzenia StorSimple przy użyciu usługi StorSimple Menedżer urządzeń

## <a name="overview"></a>Omówienie
Usługi StorSimple Menedżer urządzeń można użyć do monitorowania konkretnych urządzeń w ramach rozwiązania StorSimple. Wykresy niestandardowe można tworzyć na podstawie wydajności operacji we/wy, wykorzystania pojemności, przepustowości sieci i metryk wydajności urządzeń oraz przypinać je do pulpitu nawigacyjnego. Aby uzyskać więcej informacji, przejdź do obszaru [Dostosowywanie pulpitu nawigacyjnego portalu](../azure-portal/azure-portal-dashboards.md).

Aby wyświetlić informacje o monitorowaniu dla określonego urządzenia, w Azure Portal wybierz usługę StorSimple Menedżer urządzeń. Z listy urządzeń wybierz urządzenie, a następnie przejdź do pozycji **monitorowanie**. Następnie można zobaczyć wykresy **pojemności**, **użycia**i **wydajności** dla wybranego urządzenia.

## <a name="capacity"></a>Pojemność
**Pojemność** śledzi miejsce zainicjowane i miejsce pozostało na urządzeniu. Pozostała pojemność jest następnie wyświetlana jako przypięty lokalnie lub w warstwach.

Przydzielona i pozostała pojemność jest podzielona na woluminy przypięte warstwowo i lokalnie. Dla każdego woluminu zostanie wyświetlona pojemność zainicjowana i pozostała pojemność na urządzeniu.

![Wydajność we/wy](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Użycie
**Użycie** śledzi metryki dotyczące ilości miejsca do magazynowania danych używanego przez woluminy, kontenery woluminów lub urządzenie. Możesz tworzyć raporty na podstawie wykorzystania pojemności magazynu podstawowego, magazynu w chmurze lub magazynu urządzeń. Wykorzystanie pojemności można mierzyć na określonym woluminie, konkretnym kontenerze woluminów lub wszystkich kontenerach woluminów.
Domyślnie zostanie zgłoszone użycie w ciągu ostatnich 24 godzin. Można edytować wykres, aby zmienić czas, w którym jest raportowane użycie, wybierając z:
* Ostatnie 24 godziny
* Ostatnie 7 dni
* Ostatnie 30 dni
* Ostatnie 90 dni
* Ostatni rok

Dla wykresów użycia są zgłaszane dwie kluczowe metryki, wzrost i zakres. Zakres odnosi się do wartości maksymalnej i minimalnej wartości użycia raportowanej w wybranym czasie (w ciągu ostatnich 7 dni).

Wzrost odnosi się do wzrostu użycia od pierwszego dnia do ostatniego dnia w wybranym okresie. 

Wzrost i zakres mogą być również reprezentowane przez następujące równania:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

Używany magazyn podstawowy, Chmura i lokalna można opisać w następujący sposób:

### <a name="primary-storage-usage"></a>Użycie magazynu podstawowego
Te wykresy przedstawiają ilość danych zapisanych do woluminów StorSimple, zanim dane zostaną deduplikowane i skompresowane. Można wyświetlić magazyn podstawowy używany przez wszystkie woluminy w kontenerze woluminów lub dla pojedynczego woluminu. Używany magazyn podstawowy został poddany dalszej rozbiciu na podstawowe użycie magazynu warstwowego i używany jest podstawowy magazyn przypięty lokalnie.

Poniższe wykresy przedstawiają magazyn podstawowy używany przez urządzenie StorSimple przed wykonaniem migawki w chmurze i po jej wykonaniu. Ponieważ jest to tylko dane woluminu, migawka w chmurze nie powinna zmieniać magazynu podstawowego. Jak widać, wykres nie wyświetla żadnych różnic w odniesieniu do magazynu podstawowego lub przypiętego lokalnie użytych w wyniku przetworzenia migawki w chmurze. Migawka w chmurze rozpoczęła się o około 11:50 pm na tym urządzeniu.

![Wykorzystanie pojemności podstawowej po migawce w chmurze](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Jeśli teraz uruchamiasz operacje we/wy na hoście podłączonym do urządzenia z systemem StorSimple, zobaczysz wzrost podstawowego magazynu warstwowego lub podstawowego magazynu przypiętego lokalnie, który jest używany w zależności od tego, które woluminy (przypięte warstwowo lub lokalnie) są zapisywane do programu. Poniżej przedstawiono podstawowe wykresy użycia magazynu dla urządzenia StorSimple. Na tym urządzeniu Host StorSimple obsługuje zapisy o około 2:30 PM na woluminie warstwowym na urządzeniu. Można zobaczyć szczyt w bajtach zapisu/s odpowiadających operacji wejścia/wyjścia na hoście.

![Wydajność w przypadku uruchamiania operacji we/wy na woluminach warstwowych](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Jeśli przeszukiwany jest podstawowy magazyn warstwowy, który został usunięty, a podstawowe użycie przypięty lokalnie pozostaje niezmienione, ponieważ nie ma żadnych zapisów obsłużonych lokalnie przypiętych woluminów na urządzeniu.

![Podstawowe wykorzystanie pojemności podczas uruchamiania operacji we/wy na woluminach warstwowych](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

W przypadku korzystania z aktualizacji Update 3 lub nowszej można rozbić podstawowe wykorzystanie pojemności magazynu przez pojedynczy wolumin, wszystkie woluminy, wszystkie woluminy warstwowe i wszystkie woluminy przypięte lokalnie, jak pokazano poniżej. Podział przez wszystkie woluminy przypięte lokalnie pozwala szybko ustalić, jaka część warstwy lokalnej jest używana.

![Podstawowe wykorzystanie pojemności dla wszystkich woluminów warstwowych](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Podstawowe wykorzystanie pojemności dla wszystkich przypiętych lokalnie woluminów](./media/storsimple-8000-monitor-device/monitor-usage4.png)

Możesz również kliknąć poszczególne woluminy na liście i zobaczyć odpowiednie użycie.

![Podstawowe wykorzystanie pojemności dla wszystkich przypiętych lokalnie woluminów](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Użycie magazynu w chmurze
Te wykresy przedstawiają ilość używanego magazynu w chmurze. Te dane są deduplikowane i kompresowane. Ta kwota zawiera migawki w chmurze, które mogą zawierać dane, które nie są odzwierciedlone w woluminie głównym i są przechowywane na potrzeby starszych lub wymaganych operacji przechowywania. Można porównać dane użycia magazynu podstawowego i w chmurze, aby uzyskać pomysł dotyczący szybkości redukcji danych, chociaż nie jest to dokładne.

Poniższe wykresy przedstawiają wykorzystanie magazynu w chmurze urządzenia StorSimple podczas tworzenia migawki w chmurze.

* Migawka w chmurze rozpoczęła się o około 11:50 na tym urządzeniu i zobaczysz, że przed migawką chmury nie został użyty magazyn w chmurze. 
* Po zakończeniu migawki w chmurze zostanie zastrzelone wykorzystanie magazynu w chmurze o 0,89 GB. 
* Podczas gdy migawka chmury była w toku, istnieje również odpowiedni szczyt w operacji we/wy z urządzenia do chmury.

    ![Wykorzystanie magazynu w chmurze przed migawką w chmurze](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Wykorzystanie magazynu w chmurze po migawce w chmurze](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![We/wy z urządzenia do chmury podczas tworzenia migawki w chmurze](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Użycie magazynu lokalnego
Te wykresy przedstawiają łączne wykorzystanie urządzenia, które będzie większe niż użycie magazynu podstawowego, ponieważ obejmuje warstwę liniową SSD. Ta warstwa zawiera ilość danych, które również istnieją na innych warstwach urządzenia. Pojemność w warstwie liniowej SSD jest przetwarzana w taki sposób, aby w przypadku, gdy nowe dane są dostępne, stare dane są przenoszone do warstwy dysków twardych (w których czas jest deduplikowany i kompresowany), a następnie do chmury.

W miarę upływu czasu użycie magazynu podstawowego i używanego magazynu lokalnego najprawdopodobniej zwiększy się do momentu, aż dane staną się warstwą chmury. W tym momencie używany magazyn lokalny prawdopodobnie zacznie się Plateau, ale używany magazyn podstawowy zwiększy się, gdy zostanie zapisywana większa ilość danych.

Poniższe wykresy przedstawiają magazyn podstawowy używany przez urządzenie StorSimple podczas tworzenia migawki w chmurze. Migawka w chmurze rozpoczęła się o godzinie 11:50, a Magazyn lokalny został uruchomiony w tym czasie. Używany magazyn lokalny został przeszedł z 1,445 GB do 1,09 GB. Oznacza to, że najprawdopodobniej nieskompresowane dane w liniowej warstwie dysków SSD zostały deduplikowane, skompresowane i przeniesione do warstwy dysków twardych. Należy pamiętać, że jeśli urządzenie ma już dużą ilość danych w warstwach SSD i dysk twardy, może to spowodować, że ten spadek nie jest widoczny. W tym przykładzie urządzenie ma niewielką ilość danych.

![Wykorzystanie magazynu lokalnego po migawce w chmurze](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Wydajność
**Wydajność** śledzi metryki dotyczące liczby operacji odczytu i zapisu między interfejsami inicjatora iSCSI na serwerze hosta a urządzeniem lub urządzeniem oraz chmurą. Tę wydajność można mierzyć dla określonego woluminu, określonego kontenera woluminów lub wszystkich kontenerów woluminów. Wydajność obejmuje również wykorzystanie procesora CPU i przepływność sieci dla różnych interfejsów sieciowych na urządzeniu.

### <a name="io-performance-for-initiator-to-device"></a>Wydajność we/wy dla inicjatora dla urządzenia
Na poniższym wykresie przedstawiono we/wy dla inicjatora urządzenia dla wszystkich woluminów dla urządzenia produkcyjnego. Wykreślane metryki to bajty odczytu i zapisu na sekundę. Można również tworzyć wykresy operacji odczytu, zapisu i oczekujące operacje odczytu i zapisu oraz opóźniać odczyt i zapis.

![Wydajność we/wy dla inicjatora dla urządzenia](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>Wydajność operacji we/wy dla urządzenia w chmurze
Dla tego samego urządzenia operacje we/wy są kreślone dla danych z urządzenia do chmury dla wszystkich kontenerów woluminów. Na tym urządzeniu dane są tylko w warstwie liniowej, a nic nie zostało przelane do chmury. Brak operacji odczytu i zapisu z urządzenia do chmury. W związku z tym szczytów na wykresie są z przedziałem 5 minut, który odnosi się do częstotliwości sprawdzania pulsu między urządzeniem a usługą.

Dla tego samego urządzenia zrobiono migawkę w chmurze dla danych woluminu, zaczynając od 11:50 am. Spowodowało to przepływanie danych z urządzenia do chmury. Operacje zapisu zostały obsłużone w chmurze w tym czasie. Wykres we/wy przedstawia szczyt w bajtach zapisu/s odpowiadający czasowi utworzenia migawki.

![We/wy z urządzenia do chmury podczas tworzenia migawki w chmurze](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Przepływność sieci dla interfejsów sieciowych urządzeń
**Przepływność sieci** śledzi metryki związane z ilością danych transferowanych z interfejsów sieciowych inicjatora iSCSI na serwerze hosta oraz między urządzeniem a chmurą. Można monitorować tę metrykę dla każdego interfejsu sieciowego iSCSI na urządzeniu.

Na poniższych wykresach przedstawiono przepływność sieci dla sieci danych 0, 1 1 GbE na urządzeniu, w przypadku których włączono obsługę chmury (domyślnie) i opcję iSCSI. Na tym urządzeniu w dniu 14 czerwca wynoszącym około 9 PM dane były podzielone na chmurę (w tym momencie nie podjęto migawek chmurowych, które wskazują na warstwowy mechanizm przenoszenia danych do chmury), co spowodowało, że operacje we/wy są obsługiwane w chmurze. Istnieje odpowiedni szczyt wykresu przepływności sieci dla tego samego czasu i większość ruchu sieciowego jest wychodząca do chmury.

![Przepływność sieci dla danych 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Jeśli przejdziesz do wykresu przepływności interfejsu danych 1, inny interfejs sieciowy 1 GbE, który był obsługiwany tylko przy użyciu protokołu iSCSI, w tym czasie wystąpił praktycznie żaden ruch sieciowy.

![Przepływność sieci dla danych 1](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>Użycie procesora CPU przez urządzenie
**Użycie procesora** śledzi metryki związane z procesorem używanym na urządzeniu. Na poniższym wykresie przedstawiono statystyki użycia procesora CPU dla urządzenia w środowisku produkcyjnym.

![Użycie procesora CPU przez urządzenie](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Następne kroki
* Dowiedz się [, jak korzystać z pulpitu nawigacyjnego urządzenia usługi StorSimple Menedżer urządzeń](storsimple-device-dashboard.md).
* Dowiedz się [, jak zarządzać urządzeniem StorSimple przy użyciu usługi StorSimple Menedżer urządzeń](storsimple-manager-service-administration.md).

