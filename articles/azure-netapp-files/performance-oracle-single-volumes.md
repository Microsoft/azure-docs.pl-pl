---
title: Wydajność bazy danych Oracle na Azure NetApp Files pojedynczym woluminie | Microsoft Docs
description: Opisuje wyniki testu wydajności dla Azure NetApp Files pojedynczego woluminu w bazie danych Oracle.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: c6cdf2f6dada0aa4dea2f70f18237b7ee39e3ea1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91571415"
---
# <a name="oracle-database-performance-on-azure-netapp-files-single-volumes"></a>Wydajność bazy danych Oracle na pojedynczych woluminach usługi Azure NetApp Files

W tym artykule omówiono następujące tematy dotyczące programu Oracle w chmurze. Te tematy mogą być szczególnie przydatne w przypadku administratora bazy danych, architekta chmury lub architekta magazynu:   

* W przypadku obsługi obciążenia przetwarzania transakcji online (OLTP) (głównie losowych operacji we/wy) lub obciążenia przetwarzania analitycznego online (OLAP) (w większości sekwencyjnych operacji we/wy), jak wygląda wydajność?   
* Jaka jest różnica w wydajności między zwykłym klientem systemu plików NFS jądra z systemem Linux (kNFS) i własnym, bezpośrednim klientem systemu plików NFS firmy Oracle?
* W odniesieniu do przepustowości jest wystarczająca wydajność jednego Azure NetApp Files woluminu?

## <a name="testing-environment-and-components"></a>Testowanie środowiska i składników

Na poniższym diagramie przedstawiono środowisko używane do testowania. W celu zapewnienia spójności i prostoty rozwiązania ansible elementy PlayBook zostały użyte do wdrożenia wszystkich elementów DNA testowego.

![Środowisko testowania Oracle](../media/azure-netapp-files/performance-oracle-test-environment.png)  

### <a name="virtual-machine-configuration"></a>Konfiguracja maszyny wirtualnej

Testy używały następujących ustawień dla maszyny wirtualnej:
* System operacyjny:   
    RedHat Enterprise Linux 7,8 (wle-ora01)
* Typy wystąpień:   
    W testowaniu użyto dwóch modeli — D32s_v3 i D64s_v3
* Liczba interfejsów sieciowych:   
    Jeden (1) umieszczony w podsieci 3  
* Dysku   
    Pliki binarne Oracle i system operacyjny zostały umieszczone w jednym dysku Premium

### <a name="azure-netapp-files-configuration"></a><a name="anf_config"></a>Konfiguracja Azure NetApp Files
Testy używały następującej konfiguracji Azure NetApp Files:   

* Rozmiar puli pojemności:  
    Skonfigurowano różne rozmiary puli: 4 TiB, 8 TiB, 16 TiB, 32 TiB 
* Poziom usługi:  
    Ultra (128 MiB/s przepustowości na 1 TiB przydzieloną pojemność woluminu)
* Objętości  
    Oceniono jeden i dwa testy zbiorcze

### <a name="workload-generator"></a>Generator obciążenia 

Użyte testy wygenerowały obciążenie SLOB 2.5.4.2. SLOB (niewielki test porównawczy Oracle) to dobrze znany Generator obciążeń w przestrzeni programu Oracle, zaprojektowany w celu naprężenia i przetestowania podsystemu we/wy z SGA fizycznym obciążeniem operacji we/wy.  

SLOB 2.5.4.2 nie obsługuje podłączanej bazy danych (PDB). W związku z tym, dodano zmianę do `setup.sh` skryptów i, `runit.sh` Aby dodać do niej obsługę PDB.  

Zmienne SLOB użyte w testach są opisane w poniższych sekcjach.

#### <a name="workload-80-select-20-update--random-io--slobconf-variables"></a>Obciążenie 80% SELECT, 20% aktualizacji | Losowe we/wy — `slob.conf` zmienne   

`UPDATE_PCT=20`   
`SCAN_PCT=0`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

#### <a name="workload-100-select--sequential-io--slobconf-variables"></a>Obciążenie 100% SELECT | Sekwencyjne we/wy — `slob.conf` zmienne

`UPDATE_PCT=0`   
`SCAN_PCT=100`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

### <a name="database"></a>baza danych

Wersja programu Oracle używana do testów jest Oracle Database Enterprise Edition 19.3.0.0.

Parametry programu Oracle są następujące:  
* `sga_max_size`: 4096M
* `sga_target`: 4096
* `db_writer_processes`: 12
* `awr_pdb_autoflush_enabled`: prawda
* `filesystemio_options`: SETALL
* `log_buffer`: 134217728

Plik PDB został utworzony dla bazy danych SLOB.

Na poniższym diagramie przedstawiono obszar tabel o nazwie PERFIO o rozmiarze 600 GB (20 plików danych, co najmniej 30 GB) utworzonych do hostowania czterech schematów użytkownika SLOB. Każdy schemat użytkownika miał rozmiar 125 GB.

![Baza danych Oracle](../media/azure-netapp-files/performance-oracle-tablespace.png)  

## <a name="performance-metrics"></a>Metryki wydajności

Celem było zgłoszenie wydajności operacji we/wy w trakcie działania aplikacji. W związku z tym wszystkie diagramy w tym artykule wykorzystują metryki raportowane przez bazę danych Oracle za pośrednictwem raportów automatycznego repozytorium obciążeń (AWR). Metryki używane w diagramach są następujące:   

* **Średnia liczba żądań We/Wy/s**   
    Odpowiada sumie średniego żądania we/wy odczytu/s i średniego żądania we/wy zapisu/s z sekcji Załaduj profil
* **Średnia liczba operacji we/wy/s**   
    Odnosi się do sumy średniej operacji we/wy odczytu MB/s i średniego zapisu we/wy w sekcji profil ładowania
* **Średnie opóźnienie odczytu**   
    Odpowiada średniemu opóźnieniu zdarzenia oczekiwania Oracle "plik bazy danych sekwencyjne Odczyt" w mikrosekundach
* **Liczba wątków/schemat**   
    Odpowiada liczbie wątków SLOB na schemat użytkownika

## <a name="performance-measurement-results"></a>Wyniki pomiaru wydajności  

W tej sekcji opisano wyniki pomiaru wydajności.

### <a name="linux-knfs-client-vs-oracle-direct-nfs"></a>Linux kNFS Client a Oracle Direct NFS

Ten scenariusz został uruchomiony na maszynie wirtualnej platformy Azure Standard_D32s_v3 (Intel E5-2673 v4 # 2,30 GHz). Obciążenie wynosi 75% SELECT i 25% aktualizacji, przede wszystkim losowe we/wy i z buforem bazy danych o wartości ~ 7,5%. 

Jak pokazano na poniższym diagramie, klient Oracle DNFS dostarczał do ponad 2,8 x więcej przepływności niż zwykły klient systemu Linux kNFS:  

![Klient kNFS z systemem Linux w porównaniu z usługą Oracle Direct NFS](../media/azure-netapp-files/performance-oracle-kfns-compared-dnfs.png)  

Na poniższym diagramie przedstawiono krzywą opóźnienia operacji odczytu. W tym kontekście wąskie gardła dla klienta kNFS to pojedyncze połączenie gniazda TCP w systemie plików NFS ustanowione między klientem a serwerem NFS (wolumin Azure NetApp Files).  

![Klient kNFS z systemem Linux w porównaniu z krzywą opóźnień w usłudze Oracle Direct NFS](../media/azure-netapp-files/performance-oracle-latency-curve.png)  

Klient DNFS mógł wypchnąć więcej żądań We/Wy/s z powodu możliwości tworzenia setek połączeń gniazda TCP, co pozwala na wykorzystanie równoległości. Zgodnie z opisem w [Azure NetApp Files konfiguracji](#anf_config), każdy dodatkowy TIB zdolności produkcyjnych umożliwia dodatkowe 128MiB/s przepustowości. DNFS topped o 1 GiB/s przepływności, który jest limitem narzuconym przez 8-TiB pojemności. Nastąpiła większa pojemność, tym większa przepustowość.

Przepływność jest tylko jednym z tych zagadnień. Kolejną kwestią jest opóźnienie, które ma podstawowy wpływ na środowisko użytkownika. Jak przedstawiono na poniższym diagramie, wzrost opóźnienia może być znacznie bardziej szybszy w przypadku kNFS niż z DNFS. 

![Klient kNFS z systemem Linux w porównaniu z opóźnieniem odczytu z programu Oracle Direct](../media/azure-netapp-files/performance-oracle-read-latency.png)  

Histogramy zapewniają doskonałe wgląd w opóźnienia baz danych. Poniższy diagram przedstawia pełny widok z perspektywy zarejestrowanego "pliku bazy danych sekwencyjnego odczytu", przy użyciu DNFS w najwyższym punkcie danych współbieżności (32 wątki/schemat). Jak pokazano na poniższym diagramie, 47% wszystkich operacji odczytu zostało rozmieszczonych między 512 mikrosekundami i 1000 mikrosekund, podczas gdy 90% wszystkich operacji odczytu zostało obsłużone w czasie oczekiwania poniżej 2 ms.

![Klient kNFS z systemem Linux w porównaniu z funkcją Oracle Direct NFS histogramy](../media/azure-netapp-files/performance-oracle-histogram-read-latency.png)  

Z tego względu jest jasne, że DNFS jest to konieczne, gdy nastąpi poprawa wydajności wystąpienia bazy danych Oracle w systemie plików NFS.

### <a name="single-volume-performance-limits"></a>Limity wydajności pojedynczego woluminu

W tej sekcji opisano limity wydajności pojedynczego woluminu z losową operacji we/wy i sekwencyjnym we/wy. 

#### <a name="random-io"></a>Losowe we/wy

DNFS jest w stanie zużywać znacznie większą przepustowość niż to, co jest obsługiwane przez 8 TB przydziału wydajności Azure NetApp Files. Zwiększając Azure NetApp Files pojemności woluminu na 16 TiB, czyli chwilową zmianę, ilość przepustowości woluminu wzrosła z 1024 MiB/s przez 2 – 2048 MiB/s. 

Na poniższym diagramie przedstawiono konfigurację dla 80% wyboru i 20% obciążenia aktualizacji oraz współczynnik trafień buforu bazy danych wynoszący 8%. SLOB mógł obsługiwać pojedynczy wolumin do 200 000 żądań we/wy systemu plików NFS na sekundę. Biorąc pod uwagę, że każda operacja ma rozmiar 8 KiB, system podlegający testowi mógł dostarczyć ~ 200 000 żądań We/Wy/s lub 1600 MiB/s.
 
![Przepływność Oracle DNFS](../media/azure-netapp-files/performance-oracle-dnfs-throughput.png)  

Poniższy diagram krzywych opóźnień odczytu pokazuje, że w miarę wzrostu przepływności odczytu czas opóźnienia zwiększa się bezproblemowo poniżej linii 1-ms i trafi na kolanę krzywej w ~ 165 000 średnie żądania we/wy odczytu/s po średnim opóźnieniu odczytu wynoszącym ~ 1,3 ms.  Ta wartość jest niezwykłą wartością opóźnienia dla współczynnika we/wy nieosiągalna z niemal każdą inną technologią w chmurze platformy Azure. 

![Krzywa opóźnienia programu Oracle DNFS](../media/azure-netapp-files/performance-oracle-dnfs-latency-curve.png)  

#### <a name="sequential-io"></a>Sekwencyjne we/wy  

Jak pokazano na poniższym diagramie, nie wszystkie we/wy mają charakter losowy, biorąc pod uwagę RMAN kopię zapasową lub pełne skanowanie tabeli, na przykład jako obciążenia wymagające możliwie dużej przepustowości.  Korzystając z tej samej konfiguracji, jak opisano wcześniej, ale w woluminie o rozmiarze zmienionym na 32 TiB, na poniższym diagramie przedstawiono, że pojedyncze wystąpienie Oracle DB może zwiększyć przepływność w górę o 3 900 MB/s, blisko limitu przydziału wydajności woluminu Azure NetApp Files z 32 TB (128 MB/s * 32 = 4096 MB/s).

![DNFS we/wy Oracle](../media/azure-netapp-files/performance-oracle-dnfs-io.png)  

Podsumowując, Azure NetApp Files ułatwiają przejmowanie baz danych Oracle do chmury. Jest ona dostarczana na wydajność, gdy baza danych jej wymaga. W dowolnym momencie można dynamicznie i disruptively zmienić rozmiar przydziału woluminu.

## <a name="next-steps"></a>Następne kroki

- [Zalecenia dotyczące testu wydajności dla usługi Azure NetApp Files](azure-netapp-files-performance-metrics-volumes.md)
- [Testy porównawcze wydajności dla systemu Linux](performance-benchmarks-linux.md)