---
title: Przewodnik dotyczący ustalania wielkości klastra interakcyjnego zapytania w usłudze Azure HDInsight
description: Przewodnik dotyczący ustalania wielkości zapytania interaktywnego w usłudze Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: a81eff1dcf48996c319933aa4dd46170043b943b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83664935"
---
# <a name="interactive-query-cluster-sizing-guide-in-azure-hdinsight"></a>Przewodnik dotyczący ustalania wielkości klastra interakcyjnego zapytania w usłudze Azure HDInsight

W tym dokumencie opisano rozmiar klastra interakcyjnych zapytań usługi HDInsight (LLAP) dla typowego obciążenia w celu uzyskania odpowiedniej wydajności. Zalecenia zawarte w tym dokumencie są ogólnymi i określonymi obciążeniami mogą wymagać określonego dostrajania.

## <a name="default-vm-types-for-interactive-query"></a>Domyślne typy maszyn wirtualnych dla zapytania interaktywnego

| Typ węzła | Wystąpienie | Rozmiar |
|---|---|---|
| Head | D13 v2 | 8 PROCESORÓW WIRTUALNYCH VCPU, 56 GB PAMIĘCI RAM, DYSK SSD 400 GB |
| Odpowiedzialn | D14 v2 | 16 PROCESORÓW WIRTUALNYCH VCPU, 112 GB PAMIĘCI RAM, DYSK SSD 800 GB |
| ZooKeeper | A4 v2 | 4 PROCESORÓW WIRTUALNYCH VCPU, 8 GB PAMIĘCI RAM, DYSK SSD 40 GB |

## <a name="recommended-configurations"></a>Zalecane konfiguracje

Zalecane wartości konfiguracji są oparte na węźle procesu roboczego typu D14 v2.

| Klucz | Wartość | Opis |
|---|---|---|
| przędzy. nodemanager. Resource. Memory-MB | 102400 (MB) | Całkowita ilość pamięci wyrażona w MB dla wszystkich kontenerów PRZĘDZy w węźle. |
| przędzy. Scheduler. Maximum-Allocation-MB | 102400 (MB) | Maksymalna alokacja każdego żądania kontenera w Menedżerze zasobów w MB. Żądania pamięci wyższe niż ta wartość nie zostaną zastosowane. |
| przędzy. Scheduler. Maximum-Allocation-rdzeni wirtualnych | 12 |Maksymalna liczba rdzeni procesora CPU dla każdego żądania kontenera w Menedżer zasobów. Żądania wyższe niż ta wartość nie zostaną zastosowane. |
| przędzy. Scheduler. pojemność. root. llap. Pojemność | 90% | Alokacja pojemności PRZĘDZy dla kolejki LLAP.  |
| Hive. serwer2. tez. Sessions. per. default. Queue | number_of_worker_nodes |Liczba sesji dla każdej kolejki o nazwie w elemencie Hive. serwer2. tez. default. Queues. Ta liczba odpowiada liczbie koordynatorów zapytań (tez AMs). |
| tez. am. Resource. Memory. MB | 4096 (MB) | Ilość pamięci (w MB), która ma być używana przez tez AppMaster. |
| Hive. tez. Container. size | 4096 (MB) | Określony rozmiar kontenera tez w MB. |
| hive.llap.daemon.num.executors | 12 | Liczba modułów wykonujących na demona LLAP. |
| Hive. llap. IO. wątków. size | 12 | Rozmiar puli wątków dla wykonawców. |
| Hive. llap. Demon. przędz. Container. MB | 86016 (MB) | Całkowita ilość pamięci używana przez pojedyncze demoy LLAP (pamięć na demon).|
| Hive. llap. IO. Memory. size | 409600 (MB) | Rozmiar pamięci podręcznej w MB na przepełnienie pamięci podręcznej LLAP dysku SSD jest włączone. |
| Hive. Auto. Convert. Join. noconditionaltask. size | 2048 (MB) | rozmiar pamięci w MB do przełączenia mapy. |

## <a name="llap-daemon-size-estimations"></a>Oszacowania rozmiaru demona LLAP  

### <a name="yarnnodemanagerresourcememory-mb"></a>przędzy. nodemanager. Resource. Memory-MB

Ta wartość wskazuje maksymalną sumę pamięci (w MB) używaną przez kontenery PRZĘDZy w każdym węźle. Określa ilość pamięci, która może być używana w tym węźle, więc ta wartość powinna być mniejsza niż całkowita ilość pamięci w tym węźle.

Ustaw tę wartość = [całkowita ilość pamięci fizycznej w węźle] – [pamięć dla systemu operacyjnego i innych usług].

Zaleca się ustawienie tej wartości na wartość ~ 90% dostępnej pamięci RAM. W przypadku D14 v2 zalecaną wartością jest **102400 MB**.

### <a name="yarnschedulermaximum-allocation-mb"></a>przędzy. Scheduler. Maximum-Allocation-MB

Ta wartość wskazuje maksymalną alokację dla każdego żądania kontenera w Menedżer zasobów w MB. Żądania pamięci wyższe niż określona wartość nie zostaną zastosowane. Menedżer zasobów może przydzielić tylko pamięć do kontenerów z przyrostami `yarn.scheduler.minimum-allocation-mb` i nie może przekraczać rozmiaru określonego przez `yarn.scheduler.maximum-allocation-mb` . Ta wartość nie powinna być większa niż całkowita ilość pamięci w węźle, która jest określona przez `yarn.nodemanager.resource.memory-mb` .

W przypadku węzłów procesu roboczego z D14 v2 zalecaną wartością jest **102400 MB**

### <a name="yarnschedulermaximum-allocation-vcores"></a>przędzy. Scheduler. Maximum-Allocation-rdzeni wirtualnych

Ta konfiguracja wskazuje maksymalną liczbę rdzeni procesora CPU dla każdego żądania kontenera w Menedżer zasobów. Żądanie wyższej wartości niż ta konfiguracja zacznie obowiązywać. Ta konfiguracja jest globalną właściwością harmonogramu PRZĘDZy. Dla kontenera demona LLAP ta wartość może być ustawiona na 75% całkowitej liczby dostępnych rdzeni wirtualnych (rdzeni wirtualnych). Pozostałe 25% powinno być zarezerwowane dla węzłów Nodemanager, datanode i innych usług uruchomionych w węzłach procesu roboczego.  

W przypadku węzłów procesu roboczego z D14 v2 można uzyskać 16 rdzeni wirtualnych i 75% 16 rdzeni wirtualnych. Zalecaną wartością dla kontenera demona LLAP jest **12**.

### <a name="hiveserver2tezsessionsperdefaultqueue"></a>Hive. serwer2. tez. Sessions. per. default. Queue

Ta wartość konfiguracji określa liczbę sesji tez, które powinny być uruchamiane równolegle dla każdej z kolejek określonych przez `hive.server2.tez.default.queues` . Wartość odpowiada liczbie tez AMs (koordynatorów zapytań). Zaleca się, aby była taka sama jak liczba węzłów procesu roboczego, która ma jeden tez AM na węzeł. Liczba tez AMs może być większa niż liczba węzłów demona LLAP. Ich podstawową odpowiedzialnością jest koordynowanie wykonywania zapytania i przypisywanie fragmentów planu zapytania do odpowiednich demonów LLAP do wykonania. Zalecane jest, aby zachować ją jako wielokrotność wielu węzłów demona LLAP w celu uzyskania większej przepływności.  

Domyślny klaster usługi HDInsight ma cztery demoy LLAP uruchomione w czterech węzłach procesu roboczego, więc zalecana wartość to **4**.  

### <a name="tezamresourcememorymb-hivetezcontainersize"></a>tez. am. Resource. Memory. MB, Hive. tez. Container. size

`tez.am.resource.memory.mb` definiuje rozmiar główny aplikacji tez.  
Zalecana wartość to **4096 MB**.

`hive.tez.container.size` definiuje ilość pamięci podaną dla kontenera tez. Ta wartość musi być ustawiona między minimalnym rozmiarem kontenera PRZĘDZy ( `yarn.scheduler.minimum-allocation-mb` ) i maksymalnym rozmiarem kontenera przędzy ( `yarn.scheduler.maximum-allocation-mb` ).  
Zalecane jest ustawienie wartości **4096 MB**.  

Ogólna zasada polega na tym, że jest ona mniejsza niż ilość pamięci na procesor, biorąc pod uwagę jeden procesor na kontener. `Rreserve` pamięć dla liczby tez AMs w węźle przed pokazaniem pamięci do demona LLAP. Na przykład jeśli używasz dwóch tez AMs (4 GB każdego) na węzeł, Zapewnij 82 GB z 90 GB dla demona LLAP z zachowaniem 8 GB dla dwóch tez AMs.

### <a name="yarnschedulercapacityrootllapcapacity"></a>przędzy. Scheduler. pojemność. root. llap. Pojemność

Ta wartość wskazuje procent pojemności dla kolejki LLAP. Klaster usługi HDInsight Interactive Query zapewnia 90% całkowitej pojemności dla kolejki LLAP, a pozostałe 10% jest ustawione na domyślną kolejkę dla innych alokacji kontenerów.  
Dla węzłów procesu roboczego D14v2 zalecaną wartością jest **90** dla kolejki LLAP.

### <a name="hivellapdaemonyarncontainermb"></a>Hive. llap. Demon. przędz. Container. MB

Łączny rozmiar pamięci dla demona LLAP zależy od następujących składników:

* Konfiguracja rozmiaru kontenera PRZĘDZy ( `yarn.scheduler.maximum-allocation-mb` , `yarn.scheduler.maximum-allocation-mb` , `yarn.nodemanager.resource.memory-mb` )

* Pamięć sterty używana przez program wykonujący (XMX)

    Ilość dostępnej pamięci RAM po wyjęciu jej rozmiaru.  
    Dla D14 v2, HDI 4,0 — ta wartość jest (86 GB-6 GB) = 80 GB  
    Dla D14 v2, HDI 3,6 — ta wartość jest (84 GB-6 GB) = 78 GB

* Pamięć podręczna w pamięci podręcznej na demona (Hive. llap. IO. Memory. size)

* Świt

    Jest to część pamięci poza stertą służącą do narzutu na maszynę wirtualną języka Java (w przypadku wątków, stosu, struktur danych GC itd.). Ta część jest obserwowana na około 6% rozmiaru sterty (XMX). Aby można było po stronie bezpieczniejsze, można obliczyć jako 6% całkowitego rozmiaru pamięci demona LLAP. Ponieważ jest to możliwe, gdy pamięć podręczna SSD jest włączona, umożliwia demona LLAP użycie całego dostępnego miejsca w pamięci do użycia tylko dla sterty.  
    W przypadku D14 v2 zalecaną wartością jest ceil — (86 GB x 0,06) ~ = **6 GB**.  

Pamięć na demona = [rozmiar pamięci podręcznej w pamięci] + [rozmiar sterty] + [wysokość].

Można go obliczyć w następujący sposób:

Tez AM pamięci na węzeł = [(liczba tez AMs/liczba węzłów demona LLAP) * tez AM).
Rozmiar kontenera demona LLAP = [90% maksymalnej ilości pamięci kontenera] – [tez AM na węzeł].

W przypadku węzła roboczego D14 v2 HDI 4,0 — Zalecana wartość to (90-(1/1 * 4 GB)) = **86 GB**.
(W przypadku HDI 3,6 Zalecana wartość to **84 GB** , ponieważ należy zarezerwować ~ 2 GB dla suwaka).  

### <a name="hivellapiomemorysize"></a>Hive. llap. IO. Memory. size

Ta konfiguracja jest ilością pamięci dostępnej jako pamięć podręczna dla demona LLAP. Demony LLAP mogą używać dysków SSD jako pamięci podręcznej. Ustawienie `hive.llap.io.allocator.mmap` = true spowoduje włączenie buforowania SSD. D14 v2 zawiera 800 GB dysku SSD, a buforowanie SSD jest domyślnie włączone dla interaktywnego klastra zapytań (LLAP). Jest on skonfigurowany do używania 50% miejsca na dysku SSD dla pamięci podręcznej poza stertą.

W przypadku D14 v2 zalecaną wartością jest **409600 MB**.  

W przypadku innych maszyn wirtualnych bez włączonego buforowania dysków SSD korzystne jest udostępnienie części dostępnej pamięci RAM na potrzeby buforowania LLAP w celu uzyskania lepszej wydajności. Dostosuj łączny rozmiar pamięci dla demona LLAP w następujący sposób:  

Łączna pamięć demona LLAP = [rozmiar pamięci podręcznej LLAP] + [rozmiar sterty] + [wysokość].

Zaleca się dostosowanie rozmiaru pamięci podręcznej i rozmiaru sterty, która jest najbardziej odpowiednia dla obciążenia.  

### <a name="hivellapdaemonnumexecutors"></a>hive.llap.daemon.num.executors

Ta konfiguracja określa liczbę modułów wykonujących, które mogą wykonywać równolegle zadania równoległe na demona LLAP. Ta wartość jest sumą liczby dostępnych rdzeni wirtualnych, ilości pamięci podaną na wykonawcę oraz całkowitej ilości dostępnej pamięci na demona LLAP. Zazwyczaj ta wartość powinna być możliwie jak najbliżej liczby rdzeni.

W przypadku systemu D14 v2 dostępne są 16 rdzeni wirtualnych, ale nie wszystkie rdzeni wirtualnych. W węzłach procesu roboczego działają również inne usługi, takie jak Nodemanager, datanode i monitor metryk, które wymagają pewnej części dostępnych rdzeni wirtualnych. Tę wartość można skonfigurować do 75% łącznej liczby rdzeni wirtualnych dostępnych w tym węźle.

W przypadku D14 v2 zalecaną wartością jest (. 75 X 16) = **12**

Zaleca się, aby zarezerwować ~ 6 GB miejsca na stercie na wykonawcę. Dostosuj liczbę wykonawców na podstawie dostępnego rozmiaru demona LLAP oraz liczbę dostępnych rdzeni wirtualnych na węzeł.  

### <a name="hivellapiothreadpoolsize"></a>Hive. llap. IO. wątków. size

Ta wartość określa rozmiar puli wątków dla modułów wykonujących. Ponieważ wykonawcy są rozprawione jako określone, będzie taka sama jak liczba wykonawców na demona LLAP.

W przypadku D14 v2 zaleca się ustawienie tej wartości na **12**.

Ta konfiguracja nie może przekroczyć `yarn.nodemanager.resource.cpu-vcores` wartości.

### <a name="hiveautoconvertjoinnoconditionaltasksize"></a>Hive. Auto. Convert. Join. noconditionaltask. size

Upewnij się, że `hive.auto.convert.join.noconditionaltask` włączono, aby ten parametr został uwzględniony. Ta konfiguracja umożliwia użytkownikowi określenie rozmiaru tabel, które mogą być dopasowane do przyłączenia do mapy pamięci. Jeśli suma rozmiaru n-1 `tables/partitions` dla sprzężenia n-kierunkowego jest mniejsza niż skonfigurowana wartość, zostanie wybrana opcja przyłączenia do mapy. Aby obliczyć próg dla autoConvert mapowania, należy użyć rozmiaru pamięci programu wykonującego LLAP.

W przypadku D14 v2 zaleca się ustawienie tej wartości na **2048 MB**.

Zalecamy dostosowanie tej wartości, która jest odpowiednia dla obciążenia, ponieważ ustawienie tej wartości za mało może nie używać funkcji autoConvert. Ustawienie zbyt dużej wartości może spowodować zatrzymanie operacji GC, co może negatywnie wpłynąć na wydajność zapytań.

## <a name="next-steps"></a>Następne kroki

* [Wskazówki dotyczące bramy](gateway-best-practices.md)
* [Demystify Apache Tez dostrajania pamięci — krok po kroku](https://community.cloudera.com/t5/Community-Articles/Demystify-Apache-Tez-Memory-Tuning-Step-by-Step/ta-p/245279)
* [Rozmiar mapowania pamięci przyłączania do LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)
* [LLAP — Przegląd architektury jednostronicowej](https://community.cloudera.com/t5/Community-Articles/LLAP-a-one-page-architecture-overview/ta-p/247439)
* [Gałąź LLAP głębokie szczegółowe](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)
