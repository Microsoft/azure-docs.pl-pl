---
title: SAP HANA Konfiguracja magazynu maszyn wirtualnych platformy Azure | Microsoft Docs
description: Zalecenia dotyczące magazynu dla maszyny wirtualnej, w której wdrożono SAP HANA.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure HANA, Storage Ultra Disk, Premium Storage
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/03/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 48d1c04e59d316ac19000f5b890c0cb1c96fb213
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549443"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Konfiguracje magazynu maszyn wirtualnych platformy Azure SAP HANA

Platforma Azure oferuje różne typy magazynów, które są odpowiednie dla maszyn wirtualnych platformy Azure z systemem SAP HANA. **SAP HANA certyfikowane typy magazynów platformy Azure** , które mogą być brane pod uwagę w przypadku list wdrożeń SAP HANA, takich jak: 

- Dysk SSD Azure Premium lub Premium Storage 
- [Dysk w warstwie Ultra](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Aby dowiedzieć się więcej o tych typach dysków, zapoznaj się z artykułem [typy magazynów Azure dla obciążeń SAP](./planning-guide-storage.md) i [Wybierz typ dysku](../../disks-types.md)

Platforma Azure oferuje dwie metody wdrażania dysków VHD w usłudze Azure Standard i Premium Storage. Oczekujemy, że korzystasz z [dysku zarządzanego na platformie Azure](https://azure.microsoft.com/services/managed-disks/) na potrzeby wdrożeń magazynu blokowego platformy Azure. 

Aby uzyskać listę typów magazynu i ich umowy SLA w przypadku operacji we/wy i przepływności magazynu, zapoznaj się z [dokumentacją platformy Azure dla dysków zarządzanych](https://azure.microsoft.com/pricing/details/managed-disks/).

> [!IMPORTANT]
> Niezależnie od wybranego typu usługi Azure Storage System plików, który jest używany w tym magazynie, musi być obsługiwany przez SAP dla danego systemu operacyjnego i DBMS. [Uwaga dotycząca pomocy technicznej SAP #2972496](https://launchpad.support.sap.com/#/notes/2972496) zawiera listę obsługiwanych systemów plików dla różnych systemów operacyjnych i baz danych, w tym SAP HANA. Dotyczy to wszystkich woluminów, SAP HANA mogą uzyskać dostęp do odczytu i zapisu dla każdego zadania. W przypadku korzystania z systemu plików NFS na platformie Azure SAP HANA dodatkowe ograniczenia dotyczące wersji systemu plików NFS mają zastosowanie zgodnie z opisem w dalszej części tego artykułu. 


Minimalny SAP HANA warunki certyfikowania dla różnych typów magazynu to: 

- Usługa Azure Premium Storage — **/Hana/log** jest wymagana do obsługi przez usługę Azure [Akcelerator zapisu](../../how-to-enable-write-accelerator.md). Wolumin **/Hana/Data** może być umieszczony w magazynie w warstwie Premium bez systemu Azure akcelerator zapisu lub na dysku Ultra Disk
- Platforma Azure Ultra Disk co najmniej dla woluminu **/Hana/log** . Wolumin **/Hana/Data** można umieścić w usłudze Premium Storage bez systemu Azure akcelerator zapisu lub w celu szybszego ponownego uruchomienia komputera
- Woluminy **NFS v 4.1** na Azure NetApp Files dla **/Hana/log i/Hana/Data**. Wolumin/Hana/Shared może korzystać z protokołu NFS v3 lub NFS v 4.1

Niektóre typy magazynów można łączyć. Na przykład można umieścić **/Hana/Data** w usłudze Premium Storage, a **/Hana/log** może zostać umieszczony w magazynie Ultra Disk w celu uzyskania wymaganego niskiego opóźnienia. Jeśli używasz woluminu opartego na ANF dla **/Hana/Data**, wolumin  **/Hana/log** musi być oparty na systemie plików NFS również na ANF. Korzystanie z systemu plików NFS na ANF dla jednego z woluminów (na przykład/Hana/Data) i usługi Azure Premium Storage lub Ultra Disk dla innego woluminu (na przykład **/Hana/log**) **nie jest obsługiwane**.

W lokalnym świecie rzadko trzeba zadbać o podsystemy we/wy i możliwości. Przyczyną jest to, że dostawca urządzenia jest wymagany do upewnienia się, że minimalne wymagania dotyczące magazynu są spełnione dla SAP HANA. Podczas tworzenia infrastruktury platformy Azure należy pamiętać o niektórych wymaganiach wystawionych przez SAP. Niektóre z minimalnych cech przepływności zalecane przez SAP to:

- Odczyt/zapis w **/Hana/log** z 250 MB/s z 1 MB we/wy
- Działanie odczytu z co najmniej 400 MB/s dla **/Hana/Data** dla 16 mb i 64 MB we/wy
- Działanie zapisu o rozmiarze co najmniej 250 MB/s dla **/Hana/Data** z 16 mb i 64 MB we/wy

Uwzględniając, że niskie opóźnienie magazynu ma krytyczne znaczenie dla systemów DBMS, nawet jako DBMS, takie jak SAP HANA, Zachowaj dane w pamięci. Ścieżka krytyczna w magazynie jest zwykle wokół zapisów w dzienniku transakcji w systemach DBMS. Ale również operacje, takie jak zapisywanie punktów zapisu lub ładowanie danych w pamięci po awarii, mogą być krytyczne. W związku z tym, **obowiązkowe** jest korzystanie z usługi Azure Premium Storage, Ultra Disk lub ANF dla woluminów **/Hana/Data** i **/Hana/log** . 


Niektóre zasady dotyczące identyfikatorów w ramach wybierania konfiguracji magazynu dla platformy HANA można wymienić na takie:

- Wybieranie typu magazynu w oparciu o [typy magazynów platformy Azure dla obciążenia SAP](./planning-guide-storage.md) i [Wybierz typ dysku](../../disks-types.md)
- Ogólna przepustowość operacji we/wy maszyny wirtualnej i limity liczby operacji wejścia/wyjścia na sekundę podczas ustalania rozmiarów lub podejmowania decyzji dotyczących maszyny wirtualnej. Ogólna przepływność magazynu maszyn wirtualnych jest udokumentowana w [rozmiarze artykułu zoptymalizowanego pod kątem pamięci](../../sizes-memory.md)
- Podczas decydowania o konfiguracji magazynu spróbuj utrzymać się poniżej ogólnej przepływności maszyny wirtualnej przy użyciu konfiguracji woluminu **/Hana/Data** . Pisanie punktów zapisu, SAP HANA może być agresywne wydawanie I/OS. Podczas pisania punktu zapisu można łatwo wypchnąć do limitów przepływności woluminu **/Hana/Data** . Jeśli dyski, które kompilują wolumin **/Hana/Data** , mają wyższą przepływność niż zezwala na korzystanie z maszyny wirtualnej, można wypróbować sytuacje, w których przepływność wykorzystywana przez zapis punktu zapisu jest zakłócał wymagania dotyczące przepływności w zapisie dziennika wykonaj ponownie. Sytuacja, która może mieć wpływ na przepływność aplikacji


> [!IMPORTANT]
> Sugestie dotyczące konfiguracji magazynu są przeznaczone do rozpoczęcia pracy z programem. Uruchamianie obciążeń i analizowanie wzorców wykorzystania magazynu, można zapamiętać, że nie korzystasz ze wszystkich dostępnych przepustowości magazynu lub operacji we/wy na sekundę. Warto rozważyć downsizing magazynu. Lub w przeciwieństwie do obciążenia może być potrzebna większa przepustowość magazynu niż sugerowane w przypadku tych konfiguracji. W związku z tym może być konieczne wdrożenie większej pojemności, operacji we/wy lub przepływności. W polu napięcia między wymaganą pojemnością magazynu wymagane jest opóźnienie magazynu, wymagana przepustowość magazynu i liczba operacji we/wy i tańsza konfiguracja. platforma Azure oferuje wystarczającą liczbę różnych typów magazynów z różnymi możliwościami i różnymi punktami cenowymi, które umożliwiają znalezienie i dostosowanie odpowiedniego kompromisu dla używanego obciążenia i obciążeń platformy HANA.


## <a name="stripe-sets-versus-sap-hana-data-volume-partitioning"></a>Zestawy rozłożone a SAP HANA partycjonowanie woluminów danych
Korzystając z usługi Azure Premium Storage, można osiągnąć najlepszy współczynnik cen/wydajności podczas rozłożenia woluminu **/Hana/Data** i/lub **/Hana/log** na wiele dysków platformy Azure. Zamiast wdrażać większe woluminy, które zapewniają więcej informacji na temat operacji wejścia/wyjścia na sekundę lub przepływności. Do tej pory zostało osiągnięte przy użyciu LVM i MDADMych menedżerów woluminów, które są częścią systemu Linux. Metoda dysków rozłożonych jest stara i dobrze znana. W miarę jak woluminy rozłożone są dostępne w celu uzyskania możliwości operacji we/wy na sekundę lub przepływności, które mogą być potrzebne, zwiększa się stopień komplikacji związane z zarządzaniem woluminami rozłożonymi. Szczególnie w przypadku, gdy woluminy muszą uzyskać zwiększoną wydajność. Co najmniej dla **/Hana/Data**, system SAP wprowadza alternatywną metodę, która osiąga ten sam cel, co rozłożenie na wiele dysków platformy Azure. Ponieważ SAP HANA 2,0 SPS03, platforma HANA IndexServer jest w stanie rozdzielić aktywność we/wy na wiele plików danych HANA, które znajdują się na różnych dyskach platformy Azure. Zaletą jest to, że nie trzeba już korzystać z tworzenia woluminów rozłożonych i zarządzania nimi na różnych dyskach platformy Azure. SAP HANA funkcji partycjonowania woluminów danych opisano szczegółowo w temacie:

- [Podręcznik administratora platformy HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/40b2b2a880ec4df7bac16eae3daef756.html?q=hana%20data%20volume%20partitioning)
- [Blog o SAP HANA — partycjonowanie woluminów danych](https://blogs.sap.com/2020/10/07/sap-hana-partitioning-data-volumes/)
- [#2400005 uwagi SAP](https://launchpad.support.sap.com/#/notes/2400005)
- [#2700123 uwagi SAP](https://launchpad.support.sap.com/#/notes/2700123)

Odczytywanie szczegółowych informacji jest oczywiste, że korzystanie z tej funkcji jest czasochłonne w zestawach paskowych opartych na programie Volume Manager. Należy również pamiętać, że partycjonowanie woluminu danych HANA nie działa tylko w przypadku magazynu blokowego platformy Azure, takiego jak Azure Premium Storage. Możesz również użyć tej funkcji, aby rozdzielić między udziałami NFS w przypadku, gdy te udziały mają ograniczenia IOPS lub przepływności.  


## <a name="linux-io-scheduler-mode"></a>Tryb harmonogramu we/wy systemu Linux
System Linux ma kilka różnych trybów planowania operacji we/wy. Typowym zaleceniem za pośrednictwem dostawców systemu Linux i SAP jest ponowne skonfigurowanie trybu harmonogramu we/wy dla woluminów dyskowych z trybu **MQ/nieprzekraczalny** lub **kyber** do **aktualizujący nie działa** (poza wielokolejką) lub **Brak** dla (wielokolejowej), jeśli nie zostało to jeszcze zrobione przez profile SLES saptune. Szczegóły znajdują się w: 

- [#1984787 uwagi SAP](https://launchpad.support.sap.com/#/notes/1984787)
- [#2578899 uwagi SAP](https://launchpad.support.sap.com/#/notes/2578899) 
- [Problem z ustawieniem aktualizujący nie działa w SLES 12 SP4](https://www.suse.com/support/kb/doc/?id=000019547)

W systemie Red Hat Pozostaw ustawienia określone przez określone profile dostrajania dla różnych aplikacji SAP.


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Rozwiązania z usługami Premium Storage i Azure akcelerator zapisu dla maszyn wirtualnych z serii M platformy Azure
Usługa Azure akcelerator zapisu to funkcja dostępna wyłącznie dla maszyn wirtualnych z serii M platformy Azure. Podobnie jak w przypadku nazw, celem funkcji jest poprawa opóźnień operacji we/wy w usłudze Azure Premium Storage. W przypadku SAP HANA akcelerator zapisu powinna być używana tylko w odniesieniu do woluminu **/Hana/log** . W związku z tym **/Hana/Data** i **/Hana/log** to oddzielne woluminy z platformą Azure akcelerator zapisu obsługują tylko wolumin **/Hana/log** . 

> [!IMPORTANT]
> W przypadku korzystania z usługi Azure Premium Storage użycie usługi Azure [Akcelerator zapisu](../../how-to-enable-write-accelerator.md) dla woluminu **/Hana/log** jest obowiązkowe. Akcelerator zapisu jest dostępna tylko dla usługi Premium Storage i serii M i Mv2-Series maszyn wirtualnych. Akcelerator zapisu nie działa w połączeniu z innymi rodzinami maszyn wirtualnych platformy Azure, takimi jak Esv3 lub Edsv4.

Zalecenia dotyczące buforowania dla dysków z systemem Azure Premium są zakładane przy założeniu, że charakterystyk we/wy SAP HANA tej listy:

- Istnieją trudne do odczytania obciążenie dla plików danych platformy HANA. Wyjątki są duże I/OS po ponownym uruchomieniu wystąpienia HANA lub podczas ładowania danych do platformy HANA. Kolejną wielkością operacji odczytu I/OS dla plików danych może być kopia zapasowa bazy danych HANA. Ze względu na to, że buforowanie odczytu nie ma sensu, ponieważ w większości przypadków, wszystkie woluminy plików danych muszą zostać całkowicie odczytane. 
- Zapisywanie względem plików danych jest zainstalowano na podstawie obciążeń opartych na platformie HANA punktów zapisu i HANA. Pisanie punktów zapisu jest asynchroniczne i nie utrzymuje żadnych transakcji użytkownika. Zapisywanie danych podczas odzyskiwania po awarii ma kluczowe znaczenie dla wydajności w celu szybkiego reagowania na system. Jednak odzyskiwanie awaryjne powinno być raczej wyjątkowymi sytuacjami
- Istnieją twarde operacje odczytu z plików. Wyjątki są duże we/wy podczas wykonywania kopii zapasowych dziennika transakcji, odzyskiwania po awarii lub w fazie ponownego uruchomienia wystąpienia platformy HANA.  
- Główne obciążenie w odniesieniu do pliku dziennika SAP HANA wykonaj ponownie jest zapisem. Zależnie od charakteru obciążenia można korzystać z operacji we/wy na małą wartość 4 KB lub w innych przypadkach, w których rozmiar operacji wejścia/wyjścia wynosi 1 MB lub więcej. Opóźnienie zapisu w dzienniku SAP HANA ponownego wykonywania ma krytyczne znaczenie dla wydajności.
- Wszystkie zapisy muszą być utrwalane na dysku w niezawodny sposób

**Zalecenie: w wyniku tych obserwowanych wzorców we/wy według SAP HANA, buforowanie dla różnych woluminów przy użyciu usługi Azure Premium Storage powinno być ustawione na przykład:**

- **/Hana/Data** — brak buforowania lub buforowanie odczytu
- **/Hana/log** — brak buforowania — wyjątek dla maszyn wirtualnych z systemem M i Mv2-Series, w których usługa Azure akcelerator zapisu powinna być włączona 
- **/Hana/Shared** — buforowanie odczytu
- **Dysk systemu operacyjnego** — nie zmieniaj domyślnego buforowania ustawionego przez platformę Azure podczas tworzenia maszyny wirtualnej


Jeśli używasz LVM lub mdadm do tworzenia zestawów rozłożonych na kilku dyskach w warstwie Premium platformy Azure, musisz zdefiniować rozmiary rozłożenia. Rozmiary te różnią się między **/Hana/Data** i **/Hana/log**. **Zalecenie: jako rozmiary rozłożone zaleca się użycie:**

- 256 KB dla **/Hana/Data**
- 64 KB dla **/Hana/log**

> [!NOTE]
> Rozmiar rozłożonego elementu **/Hana/Data** został zmieniony z wcześniejszych zaleceń wywołujących 64 kb lub 128 kb do 256 KB w oparciu o środowisko klienta w przypadku nowszych wersji systemu Linux. Rozmiar 256 KB zapewnia nieco lepszą wydajność. Zmieniono również zalecenia dotyczące rozmiarów rozłożonych **/Hana/log** od 32 kb do 64 KB w celu uzyskania wystarczającej przepływności o większych rozmiarach operacji we/wy.

> [!NOTE]
> Nie trzeba konfigurować żadnego poziomu nadmiarowości przy użyciu woluminów RAID, ponieważ usługa Azure Block Storage utrzymuje trzy obrazy wirtualnego dysku twardego. Użycie zestawu rozłożonego z dyskami w warstwie Premium platformy Azure ma charakter wyłącznie skonfigurowany w celu zapewnienia wystarczającej przepływności operacji we/wy i/lub operacji wejścia/wyjścia.

Gromadzenie wielu wirtualnych dysków twardych platformy Azure pod zestawem rozłożonym jest kumulowane ze względu na przepustowość operacji we/wy na sekundę. Tak więc, jeśli umieścisz zestaw rozłożony na ponad 3 x P30 dyskach usługi Azure Premium Storage, powinieneś to trzykrotnie przekroczyć liczbę IOPS i trzykrotnie przekroczyć przepływność magazynu pojedynczego dysku P30 usługi Azure Premium Storage.

> [!IMPORTANT]
> W przypadku korzystania z programu LVM lub mdadm jako Menedżera woluminów do tworzenia zestawów rozłożonych na wielu dyskach Premium systemu Azure, trzy systemy plików SAP HANA/Data,/log i/Shared nie mogą być umieszczane w domyślnej lub głównej grupie woluminów. Zdecydowanie zaleca się przestrzeganie wskazówek dotyczących dostawców systemu Linux, które zwykle umożliwiają tworzenie pojedynczych grup woluminów dla/Data,/log i/Shared.


### <a name="azure-burst-functionality-for-premium-storage"></a>Azure w warstwie Premium Storage
W przypadku dysków usługi Azure Premium Storage o rozmiarze mniejszym lub równym 512 GiB jest oferowana funkcja pojemności. Dokładny sposób działania tworzenia [serii dysków w artykule.](../../disk-bursting.md) Po przeczytaniu artykułu rozumiesz koncepcję naliczania liczby operacji we/wy i przepływności w czasie, gdy obciążenie wejścia/wyjścia jest poniżej wartości nominalnych IOPS i przepływności dysków (Aby uzyskać szczegółowe informacje na temat nominalnej przepływności, zobacz [Cennik dysku zarządzanego](https://azure.microsoft.com/pricing/details/managed-disks/)). Nastąpi naliczanie różnic między bieżącym użyciem a wartością nominalną dysku. Liczby serii są ograniczone do maksymalnie 30 minut.

Idealnymi przypadkami, w których można zaplanować tę funkcję, jest prawdopodobnie woluminy lub dyski zawierające pliki danych dla różnych systemów DBMS. Obciążenie we/wy oczekiwane na te woluminy, zwłaszcza w przypadku systemów z małym i średnim zakresem, powinny wyglądać następująco:

- Od najmniejszej do średniego obciążenia, ponieważ dane są w pamięci podręcznej, a jak w przypadku platformy HANA, powinny być całkowite w pamięci
- Rozerwania zapisu wyzwalane przez punkty kontrolne bazy danych lub punktów zapisu, które są wydawane regularnie
- Obciążenie kopii zapasowej, które odczytuje w ciągłym strumieniu w przypadkach, gdy kopie zapasowe nie są wykonywane za pośrednictwem migawek magazynu
- Aby uzyskać SAP HANA, Załaduj dane do pamięci po ponownym uruchomieniu wystąpienia

Szczególnie w przypadku mniejszych systemów DBMS, w których obciążenie obsługuje tylko setki transakcji na sekundę, takie funkcje szeregów mogą również mieć sens dla dysków lub woluminów, które przechowują transakcję lub Dziennik ponownego wykonywania. Oczekiwane obciążenie dla tego dysku lub woluminów wygląda następująco:

- Regularne zapisywanie na dysku, które są zależne od obciążenia i charakter obciążenia, ponieważ każde zatwierdzenie wystawione przez aplikację może wyzwolić operację we/wy
- Wyższe obciążenie przepływności dla przypadków wykonywania zadań operacyjnych, takich jak tworzenie lub rekompilowanie indeksów
- Odczytywanie obciążeń podczas wykonywania dziennika transakcji lub wykonywania kopii zapasowych dziennika


### <a name="production-recommended-storage-solution-based-on-azure-premium-storage"></a>Zalecane rozwiązanie do produkcji w oparciu o usługę Azure Premium Storage

> [!IMPORTANT]
> Certyfikat SAP HANA dla maszyn wirtualnych z serii M systemu Azure jest oparty wyłącznie na platformie Azure akcelerator zapisu dla woluminu **/Hana/log** . W efekcie scenariusz produkcyjny SAP HANA wdrożenia na maszynach wirtualnych z serii M na platformie Azure powinien zostać skonfigurowany za pomocą usługi Azure akcelerator zapisu dla woluminu **/Hana/log** .  

> [!NOTE]
> W scenariuszach obejmujących usługę Azure Premium Storage wdrażamy możliwości serii w konfiguracji. Korzystając z narzędzi do testowania magazynu dla dowolnego kształtu lub formularza, należy pamiętać, aby mieć świadomość, jak [działa duże wykorzystanie dysku platformy Azure](../../disk-bursting.md) . W przypadku uruchamiania testów magazynu dostarczonych za pomocą narzędzia SAP HWCCT lub HCMT nie oczekujemy, że wszystkie testy przechodzą kryteria, ponieważ niektóre testy przekroczą kredyty na rozerwanie, które można gromadzić. Szczególnie wtedy, gdy wszystkie testy działają sekwencyjnie bez przerwy.


> [!NOTE]
> W przypadku scenariuszy produkcyjnych Sprawdź, czy określony typ maszyny wirtualnej jest obsługiwany dla SAP HANA przez SAP w [dokumentacji SAP dla IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

**Zalecenie: zalecane konfiguracje z usługą Azure Premium Storage na potrzeby scenariuszy produkcyjnych wyglądają następująco:**

Konfiguracja woluminu SAP **/Hana/Data** :

| Jednostka SKU maszyny wirtualnej | Pamięć RAM | Maksymalnie z WE/WY MASZYNY WIRTUALNEJ<br /> Przepływność | /hana/data | Aprowizowana przepływność | Maksymalna przepływność serii | Liczba operacji we/wy na sekundę | Operacje we/wy na sekundę |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MB/s | 4 x P6 | 200 MB/s | 680 MB/s | 960 | 14 000 |
| M32ls | 256 GiB | 500 MB/s | 4 x P6 | 200 MB/s | 680 MB/s | 960 | 14 000 |
| M64ls | 512 GiB | 1 000 MB/s | 4 x P10 | 400 MB/s | 680 MB/s | 2000 | 14 000 |
| M64s | 1 000 GiB | 1 000 MB/s | 4 x P15 | 500 MB/s | 680 MB/s | 4 400 | 14 000 |
| M64ms | 1 750 GiB | 1 000 MB/s | 4 x P20 | 600 MB/s | 680 MB/s | 9 200 | 14 000 |  
| M128s | 2 000 GiB | 2 000 MB/s | 4 x P20 | 600 MB/s | 680 MB/s | 9 200| 14 000 | 
| M128ms | 3 800 GiB | 2 000 MB/s | 4 x P30 | 800 MB/s | bez rozerwania | 20 000 | bez rozerwania | 
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 4 x P30 | 800 MB/s | bez rozerwania | 20 000| bez rozerwania | 
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 4 x P40 | 1 000 MB/s | bez rozerwania | 30 000 | bez rozerwania |
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 4 x P40 | 1 000 MB/s | bez rozerwania | 30 000 | bez rozerwania |
| M416ms_v2 | 11 400 GiB | 2 000 MB/s | 4 x P50 | 2 000 MB/s | bez rozerwania | 30 000 | bez rozerwania |


Dla woluminu **/Hana/log** . Konfiguracja będzie wyglądać następująco:

| Jednostka SKU maszyny wirtualnej | Pamięć RAM | Maksymalnie z WE/WY MASZYNY WIRTUALNEJ<br /> Przepływność | wolumin **/Hana/log** | Aprowizowana przepływność | Maksymalna przepływność serii | Liczba operacji we/wy na sekundę | Operacje we/wy na sekundę |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MB/s | 3 x P10 | 300 MB/s | 510 MB/s | 1500 | 10 500 | 
| M32ls | 256 GiB | 500 MB/s | 3 x P10 | 300 MB/s | 510 MB/s | 1500 | 10 500 | 
| M64ls | 512 GiB | 1 000 MB/s | 3 x P10 | 300 MB/s | 510 MB/s | 1500 | 10 500 | 
| M64s | 1 000 GiB | 1 000 MB/s | 3 x P15 | 375 MB/s | 510 MB/s | 3 300 | 10 500 | 
| M64ms | 1 750 GiB | 1 000 MB/s | 3 x P15 | 375 MB/s | 510 MB/s | 3 300 | 10 500 |  
| M128s | 2 000 GiB | 2 000 MB/s | 3 x P15 | 375 MB/s | 510 MB/s | 3 300 | 10 500|  
| M128ms | 3 800 GiB | 2 000 MB/s | 3 x P15 | 375 MB/s | 510 MB/s | 3 300 | 10 500 | 
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 3 x P15 | 375 MB/s | 510 MB/s | 3 300 | 10 500 |  
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 3 x P15 | 375 MB/s | 510 MB/s | 3 300 | 10 500 |  
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 3 x P15 | 375 MB/s | 510 MB/s | 3 300 | 10 500 |  
| M416ms_v2 | 11 400 GiB | 2 000 MB/s | 3 x P15 | 375 MB/s | 510 MB/s | 3 300 | 10 500 | 


W przypadku innych woluminów konfiguracja będzie wyglądać następująco:

| Jednostka SKU maszyny wirtualnej | Pamięć RAM | Maksymalnie z WE/WY MASZYNY WIRTUALNEJ<br /> Przepływność | /hana/shared | wolumin/root | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 1 x P15 | 1 x P6 | 1 x P6 |
| M32ls | 256 GiB | 500 MB/s |  1 x P15 | 1 x P6 | 1 x P6 |
| M64ls | 512 GiB | 1000 MB/s | 1 x P20 | 1 x P6 | 1 x P6 |
| M64s | 1 000 GiB | 1 000 MB/s | 1 x P30 | 1 x P6 | 1 x P6 |
| M64ms | 1 750 GiB | 1 000 MB/s | 1 x P30 | 1 x P6 | 1 x P6 | 
| M128s | 2 000 GiB | 2 000 MB/s | 1 x P30 | 1 x P10 | 1 x P6 | 
| M128ms | 3 800 GiB | 2 000 MB/s | 1 x P30 | 1 x P10 | 1 x P6 |
| M208s_v2 | 2 850 GiB | 1 000 MB/s |  1 x P30 | 1 x P10 | 1 x P6 |
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 1 x P30 | 1 x P10 | 1 x P6 | 
| M416s_v2 | 5 700 GiB | 2 000 MB/s |  1 x P30 | 1 x P10 | 1 x P6 | 
| M416ms_v2 | 11 400 GiB | 2 000 MB/s | 1 x P30 | 1 x P10 | 1 x P6 | 


Sprawdź, czy przepływność magazynu dla różnych sugerowanych woluminów spełnia obciążenie, które chcesz uruchomić. Jeśli obciążenie wymaga wyższych woluminów dla **/Hana/Data** i **/Hana/log**, należy zwiększyć liczbę wirtualnych dysków twardych usługi Azure Premium Storage. Ustalanie wielkości woluminu o większej liczbie dysków VHD nie powoduje zwiększenia przepływności operacji we/wy w ramach limitów typu maszyny wirtualnej platformy Azure.

Usługa Azure akcelerator zapisu działa tylko w połączeniu z usługą [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Dlatego co najmniej dyski usługi Azure Premium Storage tworzące wolumin **/Hana/log** muszą zostać wdrożone jako dyski zarządzane. Więcej szczegółowych instrukcji i ograniczeń dotyczących usługi Azure akcelerator zapisu można znaleźć w artykule [Akcelerator zapisu](../../how-to-enable-write-accelerator.md).

Dla certyfikowanych maszyn wirtualnych platformy Azure [Esv3](../../ev3-esv3-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#esv3-series) Family i [Edsv4](../../edv4-edsv4-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#edsv4-series)należy ANF dla woluminu **/Hana/Data** i **/Hana/log** . Lub musisz użyć usługi Azure Ultra Disk Storage zamiast magazynu Azure Premium Storage tylko dla woluminu **/Hana/log** . W związku z tym konfiguracje woluminu **/Hana/Data** w usłudze Azure Premium Storage mogą wyglądać następująco:

| Jednostka SKU maszyny wirtualnej | Pamięć RAM | Maksymalnie z WE/WY MASZYNY WIRTUALNEJ<br /> Przepływność | /hana/data | Aprowizowana przepływność | Maksymalna przepływność serii | Liczba operacji we/wy na sekundę | Operacje we/wy na sekundę |
| --- | --- | --- | --- | --- | --- | --- |
| E20ds_v4 | 160 GiB | 480 MB/s | 3 x P10 | 300 MB/s | 510 MB/s | 1500 | 10 500 |
| E32ds_v4 | 256 GiB | 768 MB/s | 3 x P10 |  300 MB/s | 510 MB/s | 1500 | 10 500|
| E48ds_v4 | 384 GiB | 1 152 Mb/s | 3 x P15 |  375 MB/s |510 MB/s | 3 300  | 10 500 | 
| E64ds_v4 | 504 GiB | 1 200 MB/s | 3 x P15 |  375 MB/s | 510 MB/s | 3 300 | 10 500 | 
| E64s_v3 | 432 GiB | 1 200 MB/s | 3 x P15 |  375 MB/s | 510 MB/s | 3 300 | 10 500 | 

W przypadku innych woluminów, w tym **/Hana/log** na obudowie Ultra Disk, konfiguracja może wyglądać następująco:

| Jednostka SKU maszyny wirtualnej | Pamięć RAM | Maksymalnie z WE/WY MASZYNY WIRTUALNEJ<br /> Przepływność | wolumin/Hana/log | przepływność we/wy/Hana/log | /Hana/log IOPS | /hana/shared | wolumin/root | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MB/s | 80 GB | 250 MB/s | 1800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E32ds_v4 | 256 GiB | 768 MB/s | 128 GB | 250 MB/s | 1800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E48ds_v4 | 384 GiB | 1 152 Mb/s | 192 GB | 250 MB/s | 1800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64ds_v4 | 504 GiB | 1 200 MB/s | 256 GB | 250 MB/s | 1800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64s_v3 | 432 GiB | 1 200 MB/s | 220 GB | 250 MB/s | 1800 | 1 x P20 | 1 x P6 | 1 x P6 |


## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Konfiguracja usługi Azure Ultra Disk Storage dla SAP HANA
Inny typ magazynu platformy Azure nosi nazwę [Azure Ultra Disk](../../disks-types.md#ultra-disk). Istotna różnica między usługą Azure Storage zaoferowana do tej pory i niezwykle dyskiem oznacza, że możliwości dysków nie są już powiązane z rozmiarem dysku. Jako klient można zdefiniować te możliwości dla Ultra Disk:

- Rozmiar dysku z zakresu od 4 GiB do 65 536 GiB
- Zakres IOPS z 100 operacji we/wy na sekundę (wartość maksymalna zależy również od typów maszyn wirtualnych)
- Przepływność magazynu z 300 MB/s do 2 000 MB/s

Niezwykle dysk daje możliwość zdefiniowania jednego dysku, który spełnia rozmiar, operacje we/wy i zakres przepływności dysku. Zamiast korzystać z menedżerów woluminów logicznych, takich jak LVM lub MDADM, w usłudze Azure Premium Storage w celu konstruowania woluminów, które spełniają wymagania dotyczące przepustowości operacji wejścia/wyjścia na sekundę. Można uruchomić konfigurację między programem Ultra Disk i Premium Storage. W związku z tym można ograniczyć użycie Ultra Disk do krytycznych woluminów **/Hana/Data** i **/Hana/log** , a także pokryć inne woluminy za pomocą usługi Azure Premium Storage

Inne zalety korzystania z programu Ultra Disk mogą być lepszym opóźnieniem odczytu w porównaniu do magazynu w warstwie Premium. Szybsze odczyty mogą mieć zalety, gdy chcesz zmniejszyć czasy uruchamiania platformy HANA i kolejne obciążenie danymi do pamięci. Zalety magazynu Ultra Disk można również wypróbować, gdy HANA zapisuje punktów zapisu. 

> [!NOTE]
> Niezwykle dysk nie jest jeszcze obecny we wszystkich regionach świadczenia usługi Azure i nie obsługuje jeszcze wszystkich typów maszyn wirtualnych wymienionych poniżej. Aby uzyskać szczegółowe informacje o tym, gdzie jest dostępna funkcja Ultra Disk i które rodziny maszyn wirtualnych są obsługiwane, zobacz artykuł [jakie typy dysków są dostępne na platformie Azure?](../../disks-types.md#ultra-disk).

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Zalecane rozwiązanie do produkcji magazynu z czystym konfiguracją Ultra Disk
W tej konfiguracji należy osobno przechowywać woluminy **/Hana/Data** i **/Hana/log** . Sugerowane wartości są wyprowadzane z kluczowych wskaźników wydajności, które SAP muszą certyfikowanie typów maszyn wirtualnych dla konfiguracji SAP HANA i magazynu zgodnie z zaleceniami w [dokumentacji magazynu SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

Zalecenia często przekraczają minimalne wymagania SAP określone wcześniej w tym artykule. Wymienione zalecenia są kompromisem między zaleceniami dotyczącymi rozmiaru w oprogramowaniu SAP a maksymalną przepływności magazynu, które zapewniają różne typy maszyn wirtualnych.

> [!NOTE]
> Usługa Azure Ultra Disk wymusza minimalną liczbę operacji we/wy na sekundę dysku


| Jednostka SKU maszyny wirtualnej | Pamięć RAM | Maksymalnie z WE/WY MASZYNY WIRTUALNEJ<br /> Przepływność | wolumin/Hana/Data | przepływność we/wy/Hana/Data | /Hana/Data IOPS | wolumin/Hana/log | przepływność we/wy/Hana/log | /Hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MB/s | 200 GB | 400 MB/s | 2500 | 80 GB | 250 MB | 1800 |
| E32ds_v4 | 256 GiB | 768 MB/s | 300 GB | 400 MB/s | 2500 | 128 GB | 250 MB/s | 1800 |
| E48ds_v4 | 384 GiB | 1152 MB/s | 460 GB | 400 MB/s | 3000 | 192 GB | 250 MB/s | 1800 |
| E64ds_v4 | 504 GiB | 1200 MB/s | 610 GB | 400 MB/s | 3500 |  256 GB | 250 MB/s | 1800 |
| E64s_v3 | 432 GiB | 1 200 MB/s | 610 GB | 400 MB/s | 3500 | 220 GB | 250 MB | 1800 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 MB/s | 2500 | 96 GB | 250 MB/s  | 1800 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 400 MB/s | 2500 | 256 GB | 250 MB/s  | 1800 |
| M64ls | 512 GiB | 1 000 MB/s | 620 GB | 400 MB/s | 3500 | 256 GB | 250 MB/s  | 1800 |
| M64s | 1 000 GiB | 1 000 MB/s |  1 200 GB | 600 MB/s | 5000 | 512 GB | 250 MB/s  | 2500 |
| M64ms | 1 750 GiB | 1 000 MB/s | 2 100 GB | 600 MB/s | 5000 | 512 GB | 250 MB/s  | 2500 |
| M128s | 2 000 GiB | 2 000 MB/s |2 400 GB | 750 MB/s | 7 000 | 512 GB | 250 MB/s  | 2500 | 
| M128ms | 3 800 GiB | 2 000 MB/s | 4 800 GB | 750 MB/s |9600 | 512 GB | 250 MB/s  | 2500 | 
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 3 500 GB | 750 MB/s | 7 000 | 512 GB | 250 MB/s  | 2500 | 
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 7 200 GB | 750 MB/s | 14 400 | 512 GB | 250 MB/s  | 2500 | 
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 7 200 GB | 1 000 MB/s | 14 400 | 512 GB | 400 MB/s  | 4000 | 
| M416ms_v2 | 11 400 GiB | 2 000 MB/s | 14 400 GB | 1 500 MB/s | 28 800 | 512 GB | 400 MB/s  | 4000 |   

**Wymienione wartości mają być punktem początkowym i muszą być oceniane względem rzeczywistych wymagań.** Zalety korzystania z usługi Azure Ultra Disk polega na tym, że wartości operacji we/wy i przepływności można dostosować bez konieczności wyłączania maszyny wirtualnej lub zatrzymania obciążenia zastosowanego do systemu.   

> [!NOTE]
> Do tej pory migawki magazynu z magazynem Ultra Disk nie są dostępne. Spowoduje to zablokowanie użycia migawek maszyn wirtualnych za pomocą usług Azure Backup


## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Woluminy NFS v 4.1 na Azure NetApp Files
Aby uzyskać szczegółowe informacje na temat ANF for HANA, Przeczytaj [woluminy systemu plików NFS v 4.1 w Azure NetApp Files SAP HANA](./hana-vm-operations-netapp.md)




## <a name="cost-conscious-solution-with-azure-premium-storage"></a>Ekonomiczne rozwiązanie dzięki usłudze Azure Premium Storage
Do tej pory rozwiązanie Azure Premium Storage opisane w tym dokumencie znajduje się w sekcji [rozwiązania z magazynem w warstwie Premium i usługą azure akcelerator zapisu dla maszyn wirtualnych z serii M dla systemu Azure](#solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines) przeznaczone dla SAP HANA obsługiwanych scenariuszy produkcyjnych. Jedną z cech konfiguracji obsługiwanych przez produkcyjną jest rozdzielenie woluminów dla SAP HANA danych i ponowne zalogowanie się do dwóch różnych woluminów. Przyczyną takiego oddzielenia jest fakt, że charakterystyki obciążenia woluminów są różne. Ponadto w przypadku sugerowanych konfiguracji produkcyjnych istnieje różne typy buforowania, a nawet różne rodzaje magazynu blokowego platformy Azure. W przypadku scenariuszy innych niż produkcyjne niektóre zagadnienia związane z systemami produkcyjnymi mogą nie dotyczyć bardziej małych systemów nieprodukcyjnych. W związku z tym można połączyć dane i wolumin dziennika platformy HANA. Mimo że niektóre culprits, takie jak ostatecznie nie spełniają wymagań dotyczących przepływności lub wskaźników czasu oczekiwania, które są wymagane dla systemów produkcyjnych. Innym aspektem obniżenia kosztów w takich środowiskach może być użycie [usługi Azure SSD w warstwie Standardowa Storage](./planning-guide-storage.md#azure-standard-ssd-storage). Należy pamiętać, że wybranie SSD w warstwie Standardowa lub HDD w warstwie Standardowa usługi Azure Storage ma wpływ na pojedynczą maszynę wirtualną umowy SLA zgodnie z opisem w artykule  [SLA umowy dotyczącej Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines).

Mniej kosztowna alternatywa dla takich konfiguracji może wyglądać następująco:


| Jednostka SKU maszyny wirtualnej | Pamięć RAM | Maksymalnie z WE/WY MASZYNY WIRTUALNEJ<br /> Przepływność | /Hana/Data i/Hana/log<br /> rozłożone z LVM lub MDADM | /hana/shared | wolumin/root | /usr/sap | komentarz |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | Nie osiąga 1 ms magazynu mniejszego niż czas oczekiwania<sup>1</sup> |
| E16v3 | 128 GiB | 384 MB/s | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | Typ maszyny wirtualnej nie ma certyfikatu HANA <br /> Nie osiąga 1 ms magazynu mniejszego niż czas oczekiwania<sup>1</sup> |
| M32ts | 192 GiB | 500 MB/s | 3 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | Użycie akcelerator zapisu do połączonych danych i woluminu dziennika spowoduje ograniczenie liczby operacji we/wy do 5 000<sup>2</sup> |
| E20ds_v4 | 160 GiB | 480 MB/s | 4 x P6 | 1 x E15 | 1 x E6 | 1 x E6 | Nie osiąga 1 ms magazynu mniejszego niż czas oczekiwania<sup>1</sup> |
| E32v3 | 256 GiB | 768 MB/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | Typ maszyny wirtualnej nie ma certyfikatu HANA <br /> Nie osiąga 1 ms magazynu mniejszego niż czas oczekiwania<sup>1</sup> |
| E32ds_v4 | 256 GiB | 768 MB/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | Nie osiąga 1 ms magazynu mniejszego niż czas oczekiwania<sup>1</sup> |
| M32ls | 256 GiB | 500 MB/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | Użycie akcelerator zapisu do połączonych danych i woluminu dziennika spowoduje ograniczenie liczby operacji we/wy do 5 000<sup>2</sup> |
| E48ds_v4 | 384 GiB | 1 152 Mb/s | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Nie osiąga 1 ms magazynu mniejszego niż czas oczekiwania<sup>1</sup> |
| E64v3 | 432 GiB | 1 200 MB/s | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Nie osiąga 1 ms magazynu mniejszego niż czas oczekiwania<sup>1</sup> |
| E64ds_v4 | 504 GiB | 1200 MB/s |  7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Nie osiąga 1 ms magazynu mniejszego niż czas oczekiwania<sup>1</sup> |
| M64ls | 512 GiB | 1 000 MB/s | 7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Użycie akcelerator zapisu do połączonych danych i woluminu dziennika spowoduje ograniczenie liczby operacji we/wy do 10 000<sup>2</sup> |
| M64s | 1 000 GiB | 1 000 MB/s | 7 x P15 | 1 x E30 | 1 x E6 | 1 x E6 | Użycie akcelerator zapisu do połączonych danych i woluminu dziennika spowoduje ograniczenie liczby operacji we/wy do 10 000<sup>2</sup> |
| M64ms | 1 750 GiB | 1 000 MB/s | 6 x P20 | 1 x E30 | 1 x E6 | 1 x E6 | Użycie akcelerator zapisu do połączonych danych i woluminu dziennika spowoduje ograniczenie liczby operacji we/wy do 10 000<sup>2</sup> |
| M128s | 2 000 GiB | 2 000 MB/s |6 x P20 | 1 x E30 | 1 x E10 | 1 x E6 | Użycie akcelerator zapisu do połączonych danych i woluminu dziennika spowoduje ograniczenie liczby operacji we/wy do 20 000<sup>2</sup> |
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | Użycie akcelerator zapisu do połączonych danych i woluminu dziennika spowoduje ograniczenie liczby operacji we/wy do 10 000<sup>2</sup> |
| M128ms | 3 800 GiB | 2 000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | Użycie akcelerator zapisu do połączonych danych i woluminu dziennika spowoduje ograniczenie liczby operacji we/wy do 20 000<sup>2</sup> |
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | Użycie akcelerator zapisu do połączonych danych i woluminu dziennika spowoduje ograniczenie liczby operacji we/wy do 10 000<sup>2</sup> |
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | Użycie akcelerator zapisu do połączonych danych i woluminu dziennika spowoduje ograniczenie liczby operacji we/wy do 20 000<sup>2</sup> |
| M416ms_v2 | 11400 GiB | 2 000 MB/s | 7 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | Użycie akcelerator zapisu do połączonych danych i woluminu dziennika spowoduje ograniczenie liczby operacji we/wy do 20 000<sup>2</sup> |


nie można użyć <sup>1</sup> [Akcelerator zapisu platformy Azure](../../how-to-enable-write-accelerator.md) z rodziną maszyn wirtualnych Ev4 i Ev4. W wyniku korzystania z usługi Azure Premium Storage opóźnienie operacji we/wy nie będzie mniejsze niż 1 ms

<sup>2</sup> rodzina maszyn wirtualnych obsługuje [platformę Azure akcelerator zapisu](../../how-to-enable-write-accelerator.md), ale istnieje możliwość ograniczenia liczby IOPS akceleratora zapisu, która może ograniczyć liczbę operacji we/wy konfiguracji dysków

W przypadku łączenia danych i woluminu dziennika dla SAP HANA dyski tworzące wolumin rozłożony nie powinny mieć włączonej pamięci podręcznej odczytu lub odczytu i zapisu.

Istnieją wymienione typy maszyn wirtualnych, które nie są certyfikowane za pomocą oprogramowania SAP i nie są wymienione w tym [katalogu jako SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Opinie klientów polegają na tym, że niewymienione typy maszyn wirtualnych zostały pomyślnie użyte dla niektórych zadań nieprodukcyjnych.


## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz:

- [SAP HANA Przewodnik wysokiej dostępności dla maszyn wirtualnych platformy Azure](./sap-hana-availability-overview.md).