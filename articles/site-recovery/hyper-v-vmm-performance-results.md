---
title: Testowanie replikacji maszyny wirtualnej funkcji Hyper-V do lokacji dodatkowej za pomocą programu VMM przy użyciu Azure Site Recovery
description: Ten artykuł zawiera informacje o testowaniu wydajności replikacji maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM do lokacji dodatkowej przy użyciu Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: 3edd182e335bc679d95d7be64f45b617a9f54c1a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "73663174"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>Wyniki testu dla replikacji funkcji Hyper-V do lokacji dodatkowej


Ten artykuł zawiera wyniki testów wydajności podczas replikowania maszyn wirtualnych funkcji Hyper-V w chmurach System Center Virtual Machine Manager (VMM) do dodatkowego centrum danych.

## <a name="test-goals"></a>Cele testu

Celem testowania było zbadanie, jak Site Recovery wykonuje podczas replikacji o stałej kondycji.

- Replikacja o stałym stanie występuje, gdy maszyny wirtualne ukończyją replikację początkową i synchronizują zmiany różnicowe.
- Ważne jest, aby mierzyć wydajność przy użyciu stałego stanu, ponieważ jest to stan, w którym są pozostawi większość maszyn wirtualnych, chyba że wystąpi nieoczekiwany czas przestoju.
- Wdrożenie testowe obejmuje dwie lokacje lokalne z serwerem programu VMM w każdej lokacji. To wdrożenie testowe jest typowym wdrożeniem biura/oddziału, z siedzibą firmy jako lokacją główną, a biurem oddziału jako lokacji dodatkowej lub odzyskania.

## <a name="what-we-did"></a>Co się stało

Oto co się dzieje w przebiegu testu:

1. Tworzenie maszyn wirtualnych przy użyciu szablonów programu VMM.
2. Uruchomiono maszyny wirtualne i przechwycone metryki wydajności przez 12 godzin.
3. Utworzono chmury na podstawowym i odzyskiwanym serwerze programu VMM.
4. Skonfigurowana replikacja w Site Recovery, łącznie z mapowaniem między chmurami źródłowymi i odzyskiwania.
5. Włączono ochronę dla maszyn wirtualnych i zezwalają na ukończenie replikacji początkowej.
6. Zaczekaj kilka godzin dla stabilizacji systemu.
7. Przechwycone metryki wydajności przez 12 godzin, gdzie wszystkie maszyny wirtualne pozostawały w oczekiwanym stanie replikacji przez 12 godzin.
8. Pomiar różnicy między podstawowymi metrykami wydajności i metrykami wydajności replikacji.


## <a name="primary-server-performance"></a>Podstawowa wydajność serwera

* Funkcja Hyper-V Replica (używana przez Site Recovery) asynchronicznie śledzi zmiany w pliku dziennika przy minimalnym obciążeniu magazynu na serwerze podstawowym.
* Funkcja Hyper-V Replica wykorzystuje nieobsługiwaną pamięć podręczną pamięci, aby zminimalizować obciążenie operacji we/wy na potrzeby śledzenia. Zapisuje zapisy w dysku VHDX w pamięci i opróżnia je do pliku dziennika przed upływem czasu wysłania dziennika do lokacji odzyskiwania. Opróżnianie dysku następuje również wtedy, gdy w zapisie osiągnięto wstępnie określony limit.
* Wykres poniżej przedstawia obciążenie operacji wejścia/wyjścia o stałym stanie dla replikacji. Możemy zobaczyć, że obciążenie IOPS spowodowane replikacją wynosi około 5%, co jest dość małe.

  ![Podstawowe wyniki](./media/hyper-v-vmm-performance-results/IC744913.png)

Funkcja Hyper-V Replica używa pamięci na serwerze podstawowym w celu zoptymalizowania wydajności dysków. Jak pokazano na poniższym wykresie, obciążenie pamięci na wszystkich serwerach w klastrze podstawowym jest marginalne. Pokazanym obciążeniem pamięci jest procent pamięci używanej przez replikację w porównaniu do całkowitej zainstalowanej pamięci na serwerze funkcji Hyper-V.

![Podstawowe wyniki](./media/hyper-v-vmm-performance-results/IC744914.png)

Funkcja Hyper-V Replica ma minimalne obciążenie procesora CPU. Jak pokazano na wykresie, obciążenie związane z replikacją znajduje się w zakresie 2-3%.

![Podstawowe wyniki](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Wydajność serwera pomocniczego

Funkcja Hyper-V Replica korzysta z niewielkiej ilości pamięci na serwerze odzyskiwania, aby zoptymalizować liczbę operacji magazynowania. Wykres podsumowuje użycie pamięci na serwerze odzyskiwania. Pokazanym obciążeniem pamięci jest procent pamięci używanej przez replikację w porównaniu do całkowitej zainstalowanej pamięci na serwerze funkcji Hyper-V.

![Wyniki pomocnicze](./media/hyper-v-vmm-performance-results/IC744916.png)

Liczba operacji we/wy w lokacji odzyskiwania jest funkcją liczby operacji zapisu w lokacji głównej. Przyjrzyjmy się łącznym operacjom we/wy w lokacji odzyskiwania w porównaniu z całkowitymi operacjami we/wy i operacjami zapisu w lokacji głównej. Wykresy pokazują, że łączna liczba operacji we/wy w lokacji odzyskiwania jest

* Około 1,5 razy liczba operacji we/wy zapisu na serwerze podstawowym.
* Około 37% całkowitej liczby operacji we/wy w lokacji głównej.

![Wyniki pomocnicze](./media/hyper-v-vmm-performance-results/IC744917.png)

![Wyniki pomocnicze](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>Wpływ na wykorzystanie sieci

Wykorzystano średnią z 275 MB na sekundę przepustowości sieci między węzłami głównymi i odzyskiwania (z włączoną kompresją), korzystając z istniejącej przepustowości wynoszącej 5 GB na sekundę.

![Wyniki wykorzystania sieci](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>Wpływ na wydajność maszyny wirtualnej

Ważnym zagadnieniem jest wpływ replikacji w obciążeniach produkcyjnych uruchomionych na maszynach wirtualnych. Jeśli lokacja główna jest odpowiednio obsługiwana na potrzeby replikacji, nie ma żadnego wpływu na obciążenia. Mechanizm uproszczonego śledzenia funkcji Hyper-V Replica zapewnia, że obciążenia działające na maszynach wirtualnych nie będą miały wpływu na replikację z ustalonym stanem. Przedstawiono to na poniższych wykresach.

Ten wykres pokazuje liczbę operacji we/wy wykonywanych przez maszyny wirtualne z różnymi obciążeniami, przed i po włączeniu replikacji. Można zauważyć, że nie ma różnicy między tymi dwoma.

![Wyniki działania repliki](./media/hyper-v-vmm-performance-results/IC744920.png)

Na poniższym wykresie przedstawiono przepływność maszyn wirtualnych z różnymi obciążeniami, przed i po włączeniu replikacji. Można obserwować, że replikacja nie ma znaczącego wpływu.

![Efekty replik wyników](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Podsumowanie

Wyniki jasno pokazują, że Site Recovery, w połączeniu z funkcją Hyper-V Replica, skaluje się dobrze przy minimalnym obciążeniu dla dużego klastra. Site Recovery zapewnia proste wdrażanie, replikację, zarządzanie i monitorowanie. Funkcja Hyper-V Replica zapewnia infrastrukturę niezbędną do pomyślnego skalowania replikacji. 

## <a name="test-environment-details"></a>Szczegóły środowiska testowego

### <a name="primary-site"></a>Lokacja główna

* Lokacja główna ma klaster zawierający pięć serwerów funkcji Hyper-V, na których działają maszyny wirtualne 470.
* Maszyny wirtualne uruchamiają różne obciążenia, a wszystkie Site Recovery ochrony są włączone.
* Magazyn dla węzła klastra jest udostępniany przez sieć SAN iSCSI. Model — Hitachi HUS130.
* Każdy serwer klastra ma cztery karty sieciowe (nic) o jednej GB/s każdy.
* Dwie karty sieciowe są połączone z siecią prywatną iSCSI, a dwie są połączone z zewnętrzną siecią przedsiębiorstwa. Jedna z sieci zewnętrznych jest zarezerwowana tylko do komunikacji z klastrem.

![Podstawowe wymagania sprzętowe](./media/hyper-v-vmm-performance-results/IC744922.png)

| Serwer | Pamięć RAM | Model | Procesor | Liczba procesorów | Karta sieciowa | Oprogramowanie |
| --- | --- | --- | --- | --- | --- | --- |
| Serwery funkcji Hyper-V w klastrze: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB – HOST25 ma 256 |Dell™ PowerEdge™ R820 |Procesor Intel (R) Xeon (R) E5-4620 0 \@ 2.20 GHz |4 |I GB/s x 4 |Windows Server Datacenter 2012 R2 (x64) + rola funkcji Hyper-V |
| Serwer VMM |2 | | |2 |1 Gb/s |Baza danych systemu Windows Server 2012 R2 (x64) + VMM 2012 R2 |

### <a name="secondary-site"></a>Lokacja dodatkowa

* Lokacja dodatkowa zawiera klaster trybu failover z sześcioma węzłami.
* Magazyn dla węzła klastra jest udostępniany przez sieć SAN iSCSI. Model — Hitachi HUS130.

![Podstawowa specyfikacja sprzętu](./media/hyper-v-vmm-performance-results/IC744923.png)

| Serwer | Pamięć RAM | Model | Procesor | Liczba procesorów | Karta sieciowa | Oprogramowanie |
| --- | --- | --- | --- | --- | --- | --- |
| Serwery funkcji Hyper-V w klastrze: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell™ PowerEdge™ R720 |Procesor Intel (R) Xeon (R) E5-2630 0 \@ 2.30 GHz |2 |I GB/s x 4 |Windows Server Datacenter 2012 R2 (x64) + rola funkcji Hyper-V |
| ESTLAB-HOST17 |128 |Dell™ PowerEdge™ R820 |Procesor Intel (R) Xeon (R) E5-4620 0 \@ 2.20 GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + rola funkcji Hyper-V |
| ESTLAB-HOST24 |256 |Dell™ PowerEdge™ R820 |Procesor Intel (R) Xeon (R) E5-4620 0 \@ 2.20 GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + rola funkcji Hyper-V |
| Serwer VMM |2 | | |2 |1 Gb/s |Baza danych systemu Windows Server 2012 R2 (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Obciążenia serwera

* Dla celów testowych wybieramy obciążenia często używane w scenariuszach klientów korporacyjnych.
* Używamy [IOMeter](http://www.iometer.org) z charakterystyką obciążenia podsumowaną w tabeli na potrzeby symulacji.
* Wszystkie profile IOMeter są ustawione do pisania losowych bajtów w celu symulowania wzorców zapisu dla najgorszego przypadku dla obciążeń.

| Obciążenie | Rozmiar we/wy (KB) | % Dostępu | % Odczytu | Oczekujące operacje I/OS | Wzorzec we/wy |
| --- | --- | --- | --- | --- | --- |
| Serwer plików |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |Wszystkie losowe 100% |
| SQL Server (Tom 1)<br />SQL Server (wolumin 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% losowo<br />100% sekwencyjne |
| Exchange |32 |100% |67% |8 |100% losowo |
| Stacja robocza/infrastruktura VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |Co 100% losowo |
| Serwer plików sieci Web |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |Wszystkie losowe 75% |

### <a name="vm-configuration"></a>Konfiguracja maszyny wirtualnej

* 470 maszyn wirtualnych w klastrze podstawowym.
* Wszystkie maszyny wirtualne z dyskiem VHDX.
* Maszyny wirtualne z uruchomionymi obciążeniami zostały podsumowane w tabeli. Wszystkie zostały utworzone przy użyciu szablonów programu VMM.

| Obciążenie | Liczba maszyn wirtualnych | Minimalna pamięć RAM (GB) | Maksymalna ilość pamięci RAM (GB) | Rozmiar dysku logicznego (GB) na maszynę wirtualną | Maksymalna liczba IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Serwer plików |50 |1 |2 |552 |22 |
| Infrastruktura VDI |149 |.5 |1 |80 |6 |
| Serwer sieci Web |149 |.5 |1 |80 |6 |
| SUMA |470 | | |96,83 TB |4108 |

### <a name="site-recovery-settings"></a>Ustawienia Site Recovery

* Site Recovery została skonfigurowana na potrzeby ochrony lokalnej w lokalnej usłudze
* Serwer programu VMM ma skonfigurowane cztery chmury zawierające serwery klastra funkcji Hyper-V i ich maszyny wirtualne.

| Podstawowa chmura programu VMM | Chronione maszyny wirtualne | Częstotliwość replikacji | Dodatkowe punkty odzyskiwania |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 minut |Brak |
| PrimaryCloudRpo30s |47 |30 s |Brak |
| PrimaryCloudRpo30sArp1 |47 |30 s |1 |
| PrimaryCloudRpo5m |235 |5 min |Brak |

### <a name="performance-metrics"></a>Metryki wydajności

Tabela zawiera podsumowanie metryk wydajności i liczników, które były mierzone we wdrożeniu.

| Metryka | Licznik |
| --- | --- |
| Procesor CPU |\Processor(_Total)\% Processor Time |
| Dostępna pamięć |\Memory\Available (MB) |
| Liczba operacji we/wy na sekundę |\PhysicalDisk (_Total) \Bajty transfery/s |
| Operacje odczytu maszyny wirtualnej (IOPS)/s |\Hyper-V wirtualne urządzenie magazynujące ( \<VHD> ) \Read operacje/s |
| Operacje zapisu maszyny wirtualnej (IOPS)/s |\Hyper-V wirtualne urządzenie magazynujące ( \<VHD> ) \Write operacje/S |
| Przepływność odczytu maszyny wirtualnej |\Hyper-V wirtualne urządzenie magazynujące ( \<VHD> ) \Read bajty/s |
| Przepływność zapisu maszyny wirtualnej |\Hyper-V wirtualne urządzenie magazynujące ( \<VHD> ) \Write bajty/s |

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie replikacji](hyper-v-vmm-disaster-recovery.md)
