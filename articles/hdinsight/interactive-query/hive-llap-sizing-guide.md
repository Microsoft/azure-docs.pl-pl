---
title: Przewodnik dotyczący ustalania wielkości klastra interakcyjnych zapytań usługi HDInsight (LLAP)
description: Przewodnik po zmianie wielkości LLAP
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: 626b061cc237f7238d47863a3e1ed88961d2f742
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680512"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Przewodnik dotyczący zmiany wielkości klastra interakcyjnych zapytań usługi Azure HDInsight (Hive LLAP)

W tym dokumencie opisano rozmiar klastra interakcyjnych zapytań usługi HDInsight (klaster Hive LLAP) dla typowego obciążenia w celu uzyskania odpowiedniej wydajności. Należy pamiętać, że zalecenia zawarte w tym dokumencie są ogólnymi wskazówkami i określone obciążenia mogą wymagać określonego dostrajania.

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**Domyślne typy maszyn wirtualnych platformy Azure dla klastra interakcyjnych zapytań usługi HDInsight (LLAP)**

| Typ węzła      | Wystąpienie | Rozmiar     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | 8 procesorów wirtualnych vCPU, 56 GB pamięci RAM, dysk SSD 400 GB   |
| Odpowiedzialn   | **D14 v2**        | **16 procesorów wirtualnych vCPU, 112 GB pamięci RAM, dysk SSD 800 GB**       |
| ZooKeeper   | A4 v2        | 4 procesorów wirtualnych vCPU, 8 GB pamięci RAM, dysk SSD 40 GB       |

**_Uwaga: wszystkie zalecane wartości konfiguracji są oparte na węźle procesu roboczego D14 v2 o typie_* _  

### <a name="_configuration"></a>_ *Konfiguracja:**    
| Klucz konfiguracji      | Zalecana wartość  | Opis |
| :---        |    :----:   | :---     |
| przędzy. nodemanager. Resource. Memory-MB | 102400 (MB) | Całkowita ilość pamięci wyrażona w MB dla wszystkich kontenerów PRZĘDZy w węźle | 
| przędzy. Scheduler. Maximum-Allocation-MB | 102400 (MB) | Maksymalna alokacja każdego żądania kontenera w Menedżerze zasobów w MB. Żądania pamięci wyższe niż ta wartość nie zaczną obowiązywać |
| przędzy. Scheduler. Maximum-Allocation-rdzeni wirtualnych | 12 |Maksymalna liczba rdzeni procesora CPU dla każdego żądania kontenera w Menedżer zasobów. Żądania wyższe niż ta wartość nie zostaną zastosowane. |
| przędzy. nodemanager. Resource. CPU — rdzeni wirtualnych | 12 | Liczba rdzeni procesora CPU na Węzełmanager, które można przydzielić dla kontenerów. |
| przędzy. Scheduler. pojemność. root. llap. Pojemność | 85 (%) | Alokacja pojemności PRZĘDZy dla kolejki llap  |
| tez. am. Resource. Memory. MB | 4096 (MB) | Ilość pamięci w MB, która ma być używana przez tez AppMaster |
| Hive. serwer2. tez. Sessions. per. default. Queue | <number_of_worker_nodes> |Liczba sesji dla każdej kolejki o nazwie w elemencie Hive. serwer2. tez. default. Queues. Ta liczba odpowiada liczbie koordynatorów zapytań (tez AMs) |
| Hive. tez. Container. size | 4096 (MB) | Określony rozmiar kontenera tez w MB |
| hive.llap.daemon.num.executors | 19 | Liczba modułów wykonujących na demona LLAP | 
| Hive. llap. IO. wątków. size | 19 | Rozmiar puli wątków dla programów wykonujących |
| Hive. llap. Demon. przędz. Container. MB | 81920 (MB) | Całkowita ilość pamięci używana przez pojedyncze demoy LLAP (pamięć na demon)
| Hive. llap. IO. Memory. size | 242688 (MB) | Rozmiar pamięci podręcznej w MB na LLAP demona dysku SSD jest włączony |
| Hive. Auto. Convert. Join. noconditionaltask. size | 2048 (MB) | rozmiar pamięci w MB do przyłączenia do mapy |

### <a name="llap-architecturecomponents"></a>**LLAP architektury/składniki:**  

!["LLAP architektury/składniki"](./media/hive-llap-sizing-guide/LLAP_architecture_sizing_guide.png "LLAP architektury/składniki")

### <a name="llap-daemon-size-estimations"></a>**Oszacowania rozmiaru demona LLAP:** 

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1. Określanie całkowitej alokacji pamięci PRZĘDZy dla wszystkich kontenerów w węźle**    
Konfiguracja: **_przędzy. nodemanager. Resource. Memory-MB_* _  

Ta wartość wskazuje maksymalną ilość pamięci (w MB), która może być używana przez kontenery PRZĘDZy w każdym węźle. Określona wartość powinna być mniejsza niż całkowita ilość pamięci fizycznej w tym węźle.   
Całkowita ilość pamięci dla wszystkich kontenerów PRZĘDZy w węźle = (całkowita ilość pamięci fizycznej — pamięć dla systemu operacyjnego i innych usług)  
Ustaw tę wartość na ~ 90% dostępnej wielkości pamięci RAM.  
W przypadku D14 v2 zalecaną wartością jest _ * 102400 MB * *. 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2. Określanie maksymalnej ilości pamięci na żądanie kontenera PRZĘDZy**  
Konfiguracja: **_przędz. Scheduler. Maximum-Allocation-MB_* _

Ta wartość wskazuje maksymalną alokację dla każdego żądania kontenera w Menedżer zasobów w MB. Żądania pamięci wyższe niż określona wartość nie zostaną zastosowane. Menedżer zasobów może zapewniać pamięć do kontenerów z przyrostami _yarn. Scheduler. minimalna alokacja-MB * i nie może przekraczać rozmiaru określonego przez *przędzę. Scheduler. Maximum-Allocation-MB*. Określona wartość nie powinna być większa niż całkowita ilość danej pamięci dla wszystkich kontenerów w węźle określonym przez *przędzę. nodemanager. Resource. Memory-MB*.    
W przypadku węzłów procesu roboczego z D14 v2 zalecaną wartością jest **102400 MB**

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3. Określanie maksymalnej ilości rdzeni wirtualnych na żądanie kontenera PRZĘDZy**  
Konfiguracja: **_przędz. Scheduler. Maximum-Allocation-rdzeni wirtualnych_* _  

Ta wartość wskazuje maksymalną liczbę rdzeni procesora CPU dla każdego żądania kontenera w Menedżer zasobów. Żądanie większej liczby rdzeni wirtualnych niż ta wartość zacznie obowiązywać. Jest to globalna Właściwość harmonogramu PRZĘDZy. Dla kontenera demona LLAP ta wartość może być ustawiona na 75% całkowitej dostępnej rdzeni wirtualnych. Pozostałe 25% powinno być zarezerwowane dla węzłów Nodemanager, datanode i innych usług uruchomionych w węzłach procesu roboczego.  
Na maszynach wirtualnych z systemem D14 v2 jest 16 rdzeni wirtualnych oraz 75% całkowitej 16 rdzeni wirtualnych może być używany przez kontener demona LLAP.  
W przypadku D14 v2 zalecaną wartością jest _ * 12 * *.  

#### <a name="4-number-of-concurrent-queries"></a>**4. liczba współbieżnych zapytań**  
Konfiguracja: **_Hive. serwer2. tez. Sessions. per. default. Queue_*

Ta wartość konfiguracji określa liczbę sesji tez, które mogą być uruchamiane równolegle. Te sesje tez będą uruchamiane dla każdej z kolejek określonych przez "Hive. serwer2. tez. default. Queues". Odnosi się do liczby tez AMs (koordynatorów zapytań). Zaleca się, aby była taka sama jak liczba węzłów procesu roboczego. Liczba tez AMs może być większa niż liczba węzłów demona LLAP. Główną odpowiedzialnością tez jest koordynowanie wykonywania zapytania i przypisywanie fragmentów planu zapytania do odpowiednich demonów LLAP do wykonania. Zachowaj tę wartość jako wielokrotność wielu węzłów demona LLAP, aby uzyskać większą przepływność.  

Domyślny klaster usługi HDInsight ma cztery demoy LLAP uruchomione w czterech węzłach procesu roboczego, więc zalecana wartość to _ * 4 * *.  

**Suwak interfejsu użytkownika Ambari dla zmiennej konfiguracyjnej Hive `hive.server2.tez.sessions.per.default.queue` :**

!["Maksymalna liczba współbieżnych zapytań" LLAP "](./media/hive-llap-sizing-guide/LLAP_sizing_guide_max_concurrent_queries.png "LLAP Maksymalna liczba współbieżnych zapytań")

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5. tez kontener i rozmiar główny aplikacji tez**    
Konfiguracja: **_tez. am. Resource. Memory. MB, Hive. tez. Container. size_* _  

_tez. am. Resource. Memory. MB * — definiuje rozmiar główny aplikacji tez.  
Zalecana wartość to **4096 MB**.
   
*Hive. tez. Container. size* — definiuje ilość pamięci podaną dla kontenera tez. Ta wartość musi być ustawiona między minimalnym rozmiarem kontenera PRZĘDZy (*przędzy. Scheduler. minimalna liczba alokacji: MB*) i maksymalny rozmiar kontenera przędzy (*przędz. Scheduler. Maximum-Allocation-MB*). Program wykonujący demona LLAP korzysta z tej wartości, aby ograniczyć użycie pamięci na wykonawcę.  
Zalecana wartość to **4096 MB**.  

#### <a name="6-llap-queue-capacity-allocation"></a>**6. alokacja pojemności kolejki LLAP**   
Konfiguracja: **_przędz. Scheduler. pojemność. root. llap. Pojemność_* _  

Ta wartość wskazuje procent pojemności dla kolejki llap. Alokacje pojemności mogą mieć różne wartości dla różnych obciążeń, w zależności od konfiguracji kolejek PRZĘDZy. Jeśli obciążenie jest operacją tylko do odczytu, należy ustawić ją tak, aby 90% pojemności była zadziałała. Jeśli jednak obciążenie jest mieszane z operacji aktualizowania/usuwania/scalania przy użyciu tabel zarządzanych, zaleca się przyznanie 85% pojemności dla kolejki llap. Pozostałe 15% pojemności może być używane przez inne zadania, takie jak kompaktowanie itp., aby przydzielić kontenery z kolejki domyślnej. Dzięki temu zadania w kolejce domyślnej nie pozbawienia zasobów PRZĘDZy.    

Dla węzłów procesu roboczego D14v2 zalecaną wartością dla kolejki llap jest _ * 85 * *.     
(W przypadku obciążeń w trybie tylko do odczytu można zwiększyć do 90 w odpowiedni sposób).  

#### <a name="7-llap-daemon-container-size"></a>**7. rozmiar kontenera demona LLAP**    
Konfiguracja: **_Hive. llap. Demon. przędz. Container. MB_* _  
   
Demon LLAP jest uruchamiany jako kontener PRZĘDZy w każdym węźle procesu roboczego. Łączny rozmiar pamięci dla kontenera demona LLAP zależy od następujących czynników:    
_ Konfiguracje rozmiaru kontenera PRZĘDZy (przędzy. Scheduler. minimalna alokacja — MB, przędzy. Scheduler. Maximum-Allocation-MB, przędzy. nodemanager. Resource. Memory-MB)
*  Liczba tez AMs w węźle
*  Całkowita ilość pamięci skonfigurowanej dla wszystkich kontenerów w węźle i pojemności kolejki LLAP  

Pamięć wymagana przez wzorce aplikacji tez (tez AM) można obliczyć w następujący sposób.  
Tez działa jako koordynator zapytań, a liczba tez AMs powinna być skonfigurowana na podstawie wielu współbieżnych zapytań, które mają być obsługiwane. Teoretycznie można rozważyć jedno tez AM na węzeł procesu roboczego. Jednak może być widoczny więcej niż jeden tez AM w węźle procesu roboczego. Na potrzeby obliczeń przyjęto jednolite dystrybucję usługi tez AMs we wszystkich węzłach/węzłach demona LLAP/procesów roboczych.
Zalecane jest posiadanie 4 GB pamięci na tez AM.  

Liczba tez AMS = wartość określona przez gałąź config ***Hive. serwer2. tez. Sessions. per. default. Queue**.  
Liczba węzłów demona LLAP = określona przez zmienną ENV _*_num_llap_nodes_for_llap_daemons_*_ w interfejsie użytkownika Ambari.  
Tez AM rozmiar kontenera = wartość określona przez tez config _*_tez. am. Resource. Memory. MB_*_.  

Tez am pamięci na węzeł = _ *(** ceil — **(** liczba tez AMs = **/** Liczba węzłów demona LLAP **)** **x** tez rozmiar kontenera **)**  
W przypadku wersji D14 v2 domyślna konfiguracja ma cztery tez AMs i cztery węzły demona LLAP.  
Tez AM pamięci na węzeł = (ceil — (4/4) x 4 GB) = 4 GB

Łączną ilość dostępnej pamięci dla węzła procesu roboczego dla kolejki LLAP można obliczyć w następujący sposób:  
Ta wartość zależy od łącznej ilości pamięci dostępnej dla wszystkich kontenerów PRZĘDZy w węźle (*przędzy. nodemanager. Resource. Memory-MB*) oraz wartości procentowej pojemności skonfigurowanej dla kolejki llap (*przędz. Scheduler. pojemność. root. llap. Pojemność*).  
Całkowita ilość pamięci dla kolejki LLAP w węźle procesu roboczego = całkowita ilość dostępnej pamięci dla wszystkich kontenerów PRZĘDZy w węźle x procent pojemności dla kolejki LLAP.  
W przypadku D14 v2 ta wartość jest (100 GB x 0,85) = 85 GB.

Rozmiar kontenera demona LLAP jest obliczany w następujący sposób:

**Rozmiar kontenera demona LLAP = (całkowita ilość pamięci dla kolejki LLAP na workernode) — (tez ilość pamięci na węzeł) — (rozmiar kontenera głównego usługi)**  
W klastrze jest zduplikowany tylko jeden serwer główny usługi (Application Master for LLAP) w jednym z węzłów procesu roboczego. W celu obliczenia należy wziąć pod uwagę jeden wzorzec usługi dla każdego węzła procesu roboczego.  
W przypadku węzła roboczego D14 v2 HDI 4,0 — Zalecana wartość to (85 GB-4 GB-1 GB)) = **80 GB**   
(W przypadku HDI 3,6 Zalecana wartość to **79 GB** , ponieważ należy zarezerwować dodatkowe ~ 2 GB dla suwaka am).  

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8. Określanie liczby programów wykonujących na demona LLAP**  
Konfiguracja: **_hive.llap.daemon.num.executors_* _, _*_Hive. llap. IO. wątków. size_*_

_*_Cutorshive.llap.daemon.num.exe_*_:   
Ta konfiguracja określa liczbę modułów wykonujących, które mogą wykonywać równolegle zadania równoległe na demona LLAP. Ta wartość zależy od liczby rdzeni wirtualnych, ilości pamięci używanej na wykonawcę i ilości całkowitej dostępnej pamięci dla kontenera demona LLAP.    Liczba modułów wykonujących można zasubskrybować do 120% dostępnych rdzeni wirtualnych na węzeł procesu roboczego. Należy jednak dostosować ją, jeśli nie spełni wymagania dotyczącej pamięci na podstawie ilości pamięci wymaganej dla wykonawcy i rozmiaru kontenera demona LLAP.

Każdy moduł wykonujący jest odpowiednikiem kontenera tez i może zużywać 4 GB (tez rozmiar kontenera) pamięci. Wszystkie wykonawcy w demonze LLAP mają tę samą pamięć sterty. Przy założeniu, że nie wszystkie wykonawcze uruchamiają operacje intensywnie korzystające z pamięci, można rozważyć 75% rozmiaru kontenera tez (4 GB) na wykonawcę. W ten sposób można zwiększyć liczbę modułów wykonujących, dając każdy programowi wykonującemu mniejszą ilość pamięci (np. 3 GB). Jednak zaleca się dostrojenie tego ustawienia dla docelowego obciążenia.

Na maszynach wirtualnych z systemem D14 v2 jest 16 rdzeni wirtualnych.
W przypadku D14 v2 Zalecana wartość dla liczby modułów wykonujących to (16 rdzeni wirtualnych x 120%) ~ = _ *19** na każdym węźle roboczym, biorąc pod uwagę włączoną na wykonawcę.

**_Hive. llap. IO. Hive. size_*_: Ta wartość określa rozmiar puli wątków dla wykonawców. Ponieważ wykonawcy są rozprawione jako określone, będzie taka sama jak liczba wykonawców na demona LLAP. W przypadku D14 v2 zalecaną wartością jest _* 19**.

#### <a name="9-determining-llap-daemon-cache-size"></a>**9. Określanie rozmiaru pamięci podręcznej demona LLAP**  
Konfiguracja: **_Hive. llap. IO. Memory. size_* _

Pamięć kontenera demona LLAP składa się z następujących składników: Pomieszczenie głowy
*  Pamięć sterty używana przez program wykonujący (XMX)
*  Pamięć podręczna w pamięci na demon (rozmiar pamięci poza stertą, nie ma zastosowania, gdy włączono pamięć podręczną dysków SSD)
*  Rozmiar metadanych pamięci podręcznej w pamięci (ma zastosowanie tylko wtedy, gdy włączono pamięć podręczną SSD)

**Rozmiar** pasma: ten rozmiar wskazuje część pamięci poza stertą używaną do narzutu na maszynę wirtualną języka Java (w przypadku wątków, stosu, struktur danych GC itp.). Ogólnie rzecz biorąc, to obciążenie ma około 6% rozmiaru sterty (XMX). Aby można było po stronie bezpieczniejszej, tę wartość można obliczyć jako 6% całkowitego rozmiaru pamięci demona LLAP.  
W przypadku D14 v2 zalecaną wartością jest ceil — (80 GB x 0,06) ~ = **4 GB**.  

**Rozmiar sterty (XMX)**: ilość pamięci sterty dostępna dla wszystkich wykonawców.
Łączny rozmiar sterty = liczba modułów wykonujących x 3 GB  
W przypadku D14 v2 ta wartość to 19 x 3 GB = **57 GB**  

`Ambari environment variable for LLAP heap size:`

!["Rozmiar sterty LLAP"](./media/hive-llap-sizing-guide/LLAP_sizing_guide_llap_heap_size.png "Rozmiar sterty LLAP")

Gdy pamięć podręczna dysków SSD jest wyłączona, pamięć podręczna w pamięci jest ilością pamięci, która pozostała po wyjęciu rozmiaru i rozmiaru sterty z rozmiaru kontenera demona LLAP.

Obliczanie rozmiaru pamięci podręcznej różni się w przypadku włączenia pamięci podręcznej SSD.
Ustawienie *Hive. llap. IO. Alokator. wywołania MMAP* = true spowoduje włączenie buforowania SSD.
Gdy pamięć podręczna dysków SSD jest włączona, część pamięci zostanie użyta do przechowywania metadanych dla pamięci podręcznej SSD. Metadane są przechowywane w pamięci i powinny być ~ 8% rozmiaru pamięci podręcznej SSD.   
Rozmiar metadanych w pamięci podręcznej SSD = rozmiar kontenera demona LLAP — (pomieszczenie główne + rozmiar sterty)  
W przypadku wersji D14 v2 z HDI 4,0 dysk SSD pamięci podręcznej w pamięci, rozmiar = 80 GB — (4 GB + 57 GB) = **19 GB**  
W przypadku D14 v2, z HDI 3,6, rozmiar metadanych pamięci podręcznej SSD w pamięci: 79 GB — (4 GB + 57 GB) = **18 GB**

Uwzględniając rozmiar dostępnej pamięci na potrzeby przechowywania metadanych pamięci podręcznej SSD, możemy obliczyć rozmiar pamięci podręcznej SSD, która może być obsługiwana.  
Rozmiar metadanych w pamięci dla buforu SSD = rozmiar kontenera demona LLAP — (pomieszczenie główne + rozmiar sterty) = 19 GB     
Rozmiar pamięci podręcznej SSD = rozmiar metadanych w pamięci dla pamięci podręcznej SSD (19 GB)/0,08 (8 procent)  

W przypadku D14 v2 i HDI 4,0 zalecany rozmiar pamięci podręcznej SSD = 19 GB/0,08 ~ = **237 GB**  
W przypadku D14 v2 i HDI 3,6 zalecany rozmiar pamięci podręcznej SSD = 18 GB/0,08 ~ = **225 GB**

#### <a name="10-adjusting-map-join-memory"></a>**10. Dostosowywanie pamięci przyłączania mapy**   
Konfiguracja: **_Hive. Auto. Convert. Join. noconditionaltask. size_* _

Upewnij się, że masz _hive. Auto. Convert. Join. noconditionaltask * włączone, aby ten parametr zaczęł obowiązywać.
Ta konfiguracja określa próg wyboru MapJoin przez optymalizator Hive, który traktuje nadsubskrypcję pamięci z innych programów wykonujących, aby uzyskać więcej miejsca dla tabel skrótów w pamięci, aby umożliwić większą konwersję sprzężeń map. Biorąc pod uwagę WŁĄCZONĄ na wykonawcę, ten rozmiar można zasubskrybować do WŁĄCZONĄ, ale niektóre pamięci sterty mogą być również używane w przypadku buforów sortowania, losowych buforów itp. i innych operacji.   
W przypadku D14 v2 z 3 GB pamięci na wykonawcę zaleca się ustawienie tej wartości na **2048 MB**.  

(Uwaga: Ta wartość może wymagać dopasowania, które są odpowiednie dla obciążenia. Ustawienie tej wartości za mało może nie używać funkcji autoConvert. Ustawienie zbyt wysokie może skutkować niektórymi wyjątkami pamięci lub zatrzymaniem GC, co może spowodować niekorzystny wpływ na wydajność.  

#### <a name="11-number-of-llap-daemons"></a>**11. liczba demonów LLAP**
Zmienne środowiskowe Ambari: **_num_llap_nodes, num_llap_nodes_for_llap_daemons_* _  

_ *num_llap_nodes** — określa liczbę węzłów używanych przez usługę Hive llap. obejmuje to węzły z uruchomionym systemem llap demona, Llap Service Master i tez Application Master (tez am).  

!["Liczba węzłów usługi LLAP"](./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes.png "Liczba węzłów dla usługi LLAP")  

**num_llap_nodes_for_llap_daemons** — określona liczba węzłów używanych tylko dla demonów llap. Rozmiary kontenerów demona LLAP są ustawione na wartość maks. w węźle, więc spowoduje to powstanie jednego demona LLAP w każdym węźle.

!["Liczba węzłów dla demonów LLAP"](./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes_for_llap_daemons.png "Liczba węzłów dla demonów LLAP")

Zaleca się, aby obie wartości były takie same jak liczba węzłów procesu roboczego w klastrze zapytań interaktywnych.

### <a name="considerations-for-workload-management"></a>**Zagadnienia dotyczące zarządzania obciążeniami**  
Jeśli chcesz włączyć zarządzanie obciążeniami dla LLAP, pamiętaj, aby zapewnić zarezerwowanie wystarczającej pojemności do zarządzania obciążeniami zgodnie z oczekiwaniami. Zarządzanie obciążeniami wymaga konfiguracji niestandardowej kolejki PRZĘDZy, która jest uzupełnieniem `llap` kolejki. Należy pamiętać, aby podzielić łączną pojemność zasobów klastra między kolejką llap i kolejką zarządzania obciążeniami zgodnie z wymaganiami dotyczącymi obciążenia.
Zarządzanie obciążeniami powoduje powiększanie wzorców aplikacji tez (tez AMs) podczas aktywowania planu zasobów.
Uwaga:

* Tez AMs zduplikowane przez aktywowanie planu zasobów zużywa zasoby z kolejki zarządzania obciążeniami określone przez `hive.server2.tez.interactive.queue` .  
* Liczba tez AMs zależy od wartości `QUERY_PARALLELISM` określonej w planie zasobów.  
* Po uaktywnieniu zarządzania obciążeniami usługa tez AMs w kolejce llap nie zostanie użyta. Do koordynacji zapytań są używane tylko tez AMs z kolejki zarządzania obciążeniami. Tez AMs w `llap` kolejce są używane, gdy zarządzanie obciążeniami jest wyłączone.
 
Na przykład: łączna pojemność klastra = 100 GB pamięci, podzielona między LLAP, zarządzanie obciążeniami i kolejki domyślne w następujący sposób:
 - Pojemność kolejki llap = 70 GB
 - Pojemność kolejki zarządzania obciążeniami = 20 GB
 - Domyślna pojemność kolejki = 10 GB

W przypadku przepustowości kolejki zarządzania obciążeniami w ramach planu zasobów można określić `QUERY_PARALLELISM` wartość równą pięć, co oznacza, że zarządzanie obciążeniami może uruchamiać pięć tez AMs z rozmiarem kontenera o rozmiarze 4 GB. Jeśli `QUERY_PARALLELISM` jest większa niż pojemność, może być widoczny stan teza AMs `ACCEPTED` . Interaktywny serwera hiveserver2 nie może przesłać fragmentów zapytania do tez AMs, które nie są w `RUNNING` stanie.


#### <a name="next-steps"></a>**Następne kroki**
Jeśli ustawienie tych wartości nie rozwiązało problemu, odwiedź jedną z następujących czynności...

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się za pomocą [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).  

* ##### <a name="other-references"></a>**Inne odwołania:**
  * [Skonfiguruj inne właściwości LLAP](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_setup_llap.html)  
  * [Konfigurowanie rozmiaru sterty serwera Hive](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_hiveserver_heap_sizing.html)  
  * [Rozmiar mapowania pamięci przyłączania do LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)  
  * [Właściwości aparatu wykonywania tez](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_tez_engine_properties.html)  
  * [Gałąź LLAP głębokie szczegółowe](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)