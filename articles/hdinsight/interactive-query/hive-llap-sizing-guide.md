---
title: Przewodnik dotyczący ustalania wielkości klastra interakcyjnych zapytań usługi HDInsight (LLAP)
description: Przewodnik po zmianie wielkości LLAP
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: 57eba9cb6a9350ed66ae132e6a021098da2a4810
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533057"
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
| przędzy. Scheduler. pojemność. root. llap. Pojemność | 80 (%) | Alokacja pojemności PRZĘDZy dla kolejki llap  |
| tez. am. Resource. Memory. MB | 4096 (MB) | Ilość pamięci w MB, która ma być używana przez tez AppMaster |
| Hive. serwer2. tez. Sessions. per. default. Queue | <number_of_worker_nodes> |Liczba sesji dla każdej kolejki o nazwie w elemencie Hive. serwer2. tez. default. Queues. Ta liczba odpowiada liczbie koordynatorów zapytań (tez AMs) |
| Hive. tez. Container. size | 4096 (MB) | Określony rozmiar kontenera tez w MB |
| hive.llap.daemon.num.executors | 12 | Liczba modułów wykonujących na demona LLAP | 
| Hive. llap. IO. wątków. size | 12 | Rozmiar puli wątków dla programów wykonujących |
| Hive. llap. Demon. przędz. Container. MB | 77824 (MB) | Całkowita ilość pamięci używana przez pojedyncze demoy LLAP (pamięć na demon)
| Hive. llap. IO. Memory. size | 235520 (MB) | Rozmiar pamięci podręcznej w MB na LLAP demona dysku SSD jest włączony |
| Hive. Auto. Convert. Join. noconditionaltask. size | 2048 (MB) | rozmiar pamięci w MB do przyłączenia do mapy |

### <a name="llap-daemon-size-estimations"></a>**Oszacowania rozmiaru demona LLAP:**  

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1. Określanie całkowitej alokacji pamięci PRZĘDZy dla wszystkich kontenerów w węźle**    
Konfiguracja: * *_przędzy. nodemanager. Resource. Memory-MB_* _  

Ta wartość wskazuje maksymalną ilość pamięci (w MB), która może być używana przez kontenery PRZĘDZy w każdym węźle. Określona wartość powinna być mniejsza niż całkowita ilość pamięci fizycznej w tym węźle.   
Całkowita ilość pamięci dla wszystkich kontenerów PRZĘDZy w węźle = [całkowita pamięć fizyczna] — [pamięć dla systemu operacyjnego + innych usług]  
Ustaw tę wartość na ~ 90% dostępnej wielkości pamięci RAM.  
W przypadku D14 v2 zalecaną wartością jest _ * 102400 MB * *. 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2. Określanie maksymalnej ilości pamięci na żądanie kontenera PRZĘDZy**  
Konfiguracja: * *_przędz. Scheduler. Maximum-Allocation-MB_* _

Ta wartość wskazuje maksymalną alokację dla każdego żądania kontenera w Menedżer zasobów w MB. Żądania pamięci wyższe niż określona wartość nie zostaną zastosowane. Menedżer zasobów może zapewniać pamięć do kontenerów z przyrostami _yarn. Scheduler. minimalna alokacja-MB * i nie może przekraczać rozmiaru określonego przez *przędzę. Scheduler. Maximum-Allocation-MB* . Określona wartość nie powinna być większa niż całkowita ilość danej pamięci dla wszystkich kontenerów w węźle określonym przez *przędzę. nodemanager. Resource. Memory-MB* .    
W przypadku węzłów procesu roboczego z D14 v2 zalecaną wartością jest **102400 MB**

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3. Określanie maksymalnej ilości rdzeni wirtualnych na żądanie kontenera PRZĘDZy**  
Konfiguracja: * *_przędz. Scheduler. Maximum-Allocation-rdzeni wirtualnych_* _  

Ta wartość wskazuje maksymalną liczbę rdzeni procesora CPU dla każdego żądania kontenera w Menedżer zasobów. Żądanie większej liczby rdzeni wirtualnych niż ta wartość zacznie obowiązywać. Jest to globalna Właściwość harmonogramu PRZĘDZy. Dla kontenera demona LLAP ta wartość może być ustawiona na 75% całkowitej dostępnej rdzeni wirtualnych. Pozostałe 25% powinno być zarezerwowane dla węzłów Nodemanager, datanode i innych usług uruchomionych w węzłach procesu roboczego.  
Na maszynach wirtualnych z systemem D14 v2 jest 16 rdzeni wirtualnych oraz 75% całkowitej 16 rdzeni wirtualnych może być używany przez kontener demona LLAP.  
W przypadku D14 v2 zalecaną wartością jest _ * 12 * *.  

#### <a name="4-number-of-concurrent-queries"></a>**4. liczba współbieżnych zapytań**  
Konfiguracja: * *_Hive. serwer2. tez. Sessions. per. default. Queue_*

Ta wartość konfiguracji określa liczbę sesji tez, które mogą być uruchamiane równolegle. Te sesje tez będą uruchamiane dla każdej z kolejek określonych przez "Hive. serwer2. tez. default. Queues". Odnosi się do liczby tez AMs (koordynatorów zapytań). Zaleca się, aby była taka sama jak liczba węzłów procesu roboczego. Liczba tez AMs może być większa niż liczba węzłów demona LLAP. Główną odpowiedzialnością tez jest koordynowanie wykonywania zapytania i przypisywanie fragmentów planu zapytania do odpowiednich demonów LLAP do wykonania. Zachowaj tę wartość jako wielokrotność wielu węzłów demona LLAP, aby uzyskać większą przepływność.  

Domyślny klaster usługi HDInsight ma cztery demoy LLAP uruchomione w czterech węzłach procesu roboczego, więc zalecana wartość to _ * 4 * *.  

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5. tez kontener i rozmiar główny aplikacji tez**    
Konfiguracja: * *_tez. am. Resource. Memory. MB, Hive. tez. Container. size_* _  

_tez. am. Resource. Memory. MB * — definiuje rozmiar główny aplikacji tez.  
Zalecana wartość to **4096 MB** .
   
*Hive. tez. Container. size* — definiuje ilość pamięci podaną dla kontenera tez. Ta wartość musi być ustawiona między minimalnym rozmiarem kontenera PRZĘDZy ( *przędzy. Scheduler. minimalna liczba alokacji: MB* ) i maksymalny rozmiar kontenera przędzy ( *przędz. Scheduler. Maximum-Allocation-MB* ). Program wykonujący demona LLAP korzysta z tej wartości, aby ograniczyć użycie pamięci na wykonawcę.  
Zalecana wartość to **4096 MB** .  

#### <a name="6-llap-queue-capacity-allocation"></a>**6. alokacja pojemności kolejki LLAP**   
Konfiguracja: * *_przędz. Scheduler. pojemność. root. llap. Pojemność_* _  

Ta wartość wskazuje procent pojemności dla kolejki llap. Alokacje pojemności mogą mieć różne wartości dla różnych obciążeń, w zależności od konfiguracji kolejek PRZĘDZy. Jeśli obciążenie jest operacją tylko do odczytu, należy ustawić ją tak, aby 90% pojemności była zadziałała. Jeśli jednak obciążenie jest mieszane z operacji aktualizowania/usuwania/scalania przy użyciu tabel zarządzanych, zaleca się przyznanie 80% pojemności dla kolejki llap. Pozostałe 20% pojemności może być używane przez inne zadania, takie jak kompaktowanie itp., aby przydzielić kontenery z kolejki domyślnej. Dzięki temu zadania w kolejce domyślnej nie pozbawienia zasobów PRZĘDZy.    
Dla węzłów procesu roboczego D14v2 zalecaną wartością dla kolejki llap jest _ * 80 * *.   
(W przypadku obciążeń w trybie tylko do odczytu można zwiększyć do 90 w odpowiedni sposób).  

#### <a name="7-llap-daemon-container-size"></a>**7. rozmiar kontenera demona LLAP**    
Konfiguracja: * *_Hive. llap. Demon. przędz. Container. MB_* _  
   
Demon LLAP jest uruchamiany jako kontener PRZĘDZy w każdym węźle procesu roboczego. Łączny rozmiar pamięci dla kontenera demona LLAP zależy od następujących czynników:    
_ Konfiguracje rozmiaru kontenera PRZĘDZy (przędzy. Scheduler. minimalna alokacja — MB, przędzy. Scheduler. Maximum-Allocation-MB, przędzy. nodemanager. Resource. Memory-MB)
*  Liczba tez AMs w węźle
*  Całkowita ilość pamięci skonfigurowanej dla wszystkich kontenerów w węźle i pojemności kolejki LLAP  

Pamięć wymagana przez wzorce aplikacji tez (tez AM) można obliczyć w następujący sposób.  
W przypadku klastra interaktywnego usługi HDInsight domyślnie istnieje jeden tez AM na węzeł procesu roboczego, który działa jako koordynator zapytań. Liczbę tez AMs można skonfigurować na podstawie liczby współbieżnych zapytań, które mają być obsługiwane.
Zalecane jest posiadanie 4 GB pamięci na tez AM.

Tez AM pamięci na węzeł = [ceil — (liczba tez AMs/liczba węzłów demona LLAP)] x [tez rozmiar kontenera]  
W przypadku wersji D14 v2 domyślna konfiguracja ma cztery tez AMs i cztery węzły demona LLAP.  
Tez AM pamięci na węzeł = (ceil — (4/4) x 4 GB) = 4 GB

Łączną ilość dostępnej pamięci dla węzła procesu roboczego dla kolejki LLAP można obliczyć w następujący sposób: Ta wartość zależy od łącznej ilości pamięci dostępnej dla wszystkich kontenerów PRZĘDZy w węźle ( *przędzy. nodemanager. Resource. Memory-MB* ) oraz wartości procentowej pojemności skonfigurowanej dla kolejki LLAP ( *przędz. Scheduler. pojemność. root. LLAP. Pojemność* ).  
Całkowita ilość pamięci dla kolejki LLAP w węźle procesu roboczego = całkowita ilość dostępnej pamięci dla wszystkich kontenerów PRZĘDZy w węźle x procent pojemności dla kolejki LLAP.  
W przypadku D14 v2 ta wartość to [100 GB x 0,80] = 80 GB.

Rozmiar kontenera demona LLAP jest obliczany w następujący sposób:

**Rozmiar kontenera demona LLAP = [całkowita ilość dostępnej pamięci dla kolejki LLAP] — [tez ilość pamięci na węzeł]**  
    
W przypadku węzła roboczego D14 v2 HDI 4,0 — Zalecana wartość to (80 GB – 4 GB)) = **76 GB**   
(W przypadku HDI 3,6 Zalecana wartość to **74 GB** , ponieważ należy zarezerwować dodatkowe ~ 2 GB dla suwaka am).  

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8. Określanie liczby programów wykonujących na demona LLAP**  
Konfiguracja: * *_hive.llap.daemon.num.executors_* _, _*_Hive. llap. IO. wątków. size_*_

_*_Cutorshive.llap.daemon.num.exe_*_ :   
Ta konfiguracja określa liczbę modułów wykonujących, które mogą wykonywać równolegle zadania równoległe na demona LLAP. Ta wartość zależy od liczby rdzeni wirtualnych, ilości pamięci podawanej na wykonawcę i ilości całkowitej dostępnej pamięci dla demona LLAP. Zazwyczaj ta wartość powinna być możliwie najbliżej liczby rdzeni wirtualnych.
Na maszynach wirtualnych z systemem D14 v2 jest 16 rdzeni wirtualnych. Jednak nie wszystkie rdzeni wirtualnych można wykonać, ponieważ inne usługi, takie jak Nodemanager, datanode, monitor metryk itd., wymagają również części dostępnych rdzeni wirtualnych. 

Jeśli trzeba dostosować liczbę wykonawców, zaleca się rozważenie 4 GB pamięci na wykonawcę zgodnie z opisem w _hive. tez. Container. size * i upewnienie się, że całkowita ilość pamięci potrzebna dla wszystkich wykonawców nie przekracza całkowitej ilości dostępnej pamięci dla kontenera demona LLAP.  
Tę wartość można skonfigurować do 75% łącznej liczby rdzeni wirtualnych dostępnych w tym węźle.  
W przypadku D14 v2 zalecaną wartością jest (. 75 X 16) = **12**

**_Hive. llap. IO. Hive. size_*_: Ta wartość określa rozmiar puli wątków dla wykonawców. Ponieważ wykonawcy są rozprawione jako określone, będzie taka sama jak liczba wykonawców na demona LLAP. W przypadku D14 v2 zalecaną wartością jest _* 12** .

#### <a name="9-determining-llap-daemon-cache-size"></a>**9. Określanie rozmiaru pamięci podręcznej demona LLAP**  
Konfiguracja: * *_Hive. llap. IO. Memory. size_* _

Pamięć kontenera demona LLAP składa się z następujących składników: Pomieszczenie głowy
*  Pamięć sterty używana przez program wykonujący (XMX)
*  Pamięć podręczna w pamięci na demon (rozmiar pamięci poza stertą, nie ma zastosowania, gdy włączono pamięć podręczną dysków SSD)
*  Rozmiar metadanych pamięci podręcznej w pamięci (ma zastosowanie tylko wtedy, gdy włączono pamięć podręczną SSD)

**Rozmiar** pasma: ten rozmiar wskazuje część pamięci poza stertą używaną do narzutu na maszynę wirtualną języka Java (w przypadku wątków, stosu, struktur danych GC itp.). Ogólnie rzecz biorąc, to obciążenie ma około 6% rozmiaru sterty (XMX). Aby można było po stronie bezpieczniejszej, tę wartość można obliczyć jako 6% całkowitego rozmiaru pamięci demona LLAP.  
W przypadku D14 v2 zalecaną wartością jest ceil — (76 GB x 0,06) ~ = **5 GB** .  

**Rozmiar sterty (XMX)** : ilość pamięci sterty dostępna dla wszystkich wykonawców.
Łączny rozmiar sterty = liczba modułów wykonujących x 4 GB  
W przypadku D14 v2 wartość ta wynosi 12 x 4 GB = **48 GB**  

Jeśli pamięć podręczna dysków SSD jest wyłączona, pamięć podręczna w pamięci jest ilością pamięci, która pozostała po wyjęciu rozmiaru i rozmiaru sterty z rozmiaru kontenera demona LLAP.

Obliczanie rozmiaru pamięci podręcznej różni się w przypadku włączenia pamięci podręcznej SSD.
Ustawienie *Hive. llap. IO. Alokator. wywołania MMAP* = true spowoduje włączenie buforowania SSD.
Gdy pamięć podręczna dysków SSD jest włączona, część pamięci zostanie użyta do przechowywania metadanych dla pamięci podręcznej SSD. Metadane są przechowywane w pamięci i powinny być ~ 10% rozmiaru pamięci podręcznej SSD.   
Rozmiar metadanych w pamięci podręcznej dysku SSD = [rozmiar kontenera demona LLAP]-[pomieszczenie główne + rozmiar sterty]  
W przypadku wersji D14 v2 z HDI 4,0 dysk SSD w pamięci podręcznej (o rozmiarze 76 GB) — [5 GB + 48 GB] = **23 GB**  
W przypadku D14 v2, z HDI 3,6, rozmiar metadanych pamięci podręcznej SSD = [76 GB]-[5 GB + 48 GB + 2 GB suwaka] = **21 GB**

Uwzględniając rozmiar dostępnej pamięci na potrzeby przechowywania metadanych pamięci podręcznej SSD, możemy obliczyć rozmiar pamięci podręcznej SSD, która może być obsługiwana.  
Rozmiar metadanych pamięci podręcznej SSD = 10% rozmiaru pamięci podręcznej SSD       
Rozmiar pamięci podręcznej SSD = rozmiar metadanych w pamięci dla pamięci podręcznej SSD x 10  

W przypadku D14 v2 i HDI 4,0 zalecany rozmiar pamięci podręcznej SSD = 23 GB x 10 = **230 GB**  
W przypadku D14 v2 i HDI 3,6 zalecany rozmiar pamięci podręcznej SSD = 21 GB x 10 = **210 GB**

#### <a name="10-adjusting-map-join-memory"></a>**10. Dostosowywanie pamięci przyłączania mapy**   
Konfiguracja: * *_Hive. Auto. Convert. Join. noconditionaltask. size_* _

Upewnij się, że masz _hive. Auto. Convert. Join. noconditionaltask * włączone, aby ten parametr zaczęł obowiązywać.
Ta konfiguracja umożliwia użytkownikowi określenie rozmiaru tabel, które mogą być dopasowane do przyłączenia do mapy pamięci. Jeśli suma rozmiaru tabel lub partycji n-1 dla sprzężenia typu "n-Way" jest mniejsza niż skonfigurowana wartość, zostanie wybrane sprzężenie mapowania. Aby obliczyć próg dla autoConvert mapowania, należy użyć rozmiaru pamięci programu wykonującego LLAP.
Każdy wykonawca zakłada, że ma 4 GB rozmiaru sterty, ale nie wszystkie z nich będą dostępne do przyłączenia do mapy. Niektóre pamięci sterty będą używane do sortowania buforów, losowych buforów, tabel skrótów itp., a także innych operacji. Dzięki temu do przyłączania do mapy można przyznać 50% pamięci sterty o pojemności 4 GB.  
Uwaga: Ta wartość może wymagać dopasowania, które są odpowiednie dla obciążenia. Ustawienie tej wartości za mało może nie używać funkcji autoConvert. Ustawienie zbyt wysokie może spowodować utratę wyjątków pamięci lub zatrzymanie GC, co może spowodować niekorzystny wpływ na wydajność.  
W przypadku D14 v2 z 4 GB pamięci na wykonawcę zaleca się ustawienie tej wartości na **2048 MB** .


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