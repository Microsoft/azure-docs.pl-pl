---
title: SAP HANA Konfiguracja magazynu maszyn wirtualnych platformy Azure | Microsoft Docs
description: Zalecenia dotyczące magazynu dla maszyny wirtualnej, w której wdrożono SAP HANA.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b0d8228586c0e20e4314331339aa2f2c46a38c9a
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792160"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Konfiguracje magazynu maszyn wirtualnych platformy Azure SAP HANA

Platforma Azure oferuje różne typy magazynów, które są odpowiednie dla maszyn wirtualnych platformy Azure z systemem SAP HANA. **SAP HANA certyfikowane typy magazynów platformy Azure** , które mogą być brane pod uwagę w przypadku list wdrożeń SAP HANA, takich jak: 

- SSD w warstwie Premium platformy Azure  
- [Dysk w warstwie Ultra](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Aby dowiedzieć się więcej o tych typach dysków, zobacz artykuł [Wybieranie typu dysku](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

Platforma Azure oferuje dwie metody wdrażania dysków VHD w usłudze Azure Standard i Premium Storage. Jeśli ogólny scenariusz zezwala na korzystanie z [usługi Azure Managed Disk](https://azure.microsoft.com/services/managed-disks/) Deployments. 

Aby uzyskać listę typów magazynu i ich umowy SLA w przypadku operacji we/wy i przepływności magazynu, zapoznaj się z [dokumentacją platformy Azure dla dysków zarządzanych](https://azure.microsoft.com/pricing/details/managed-disks/).

> [!IMPORTANT]
> Niezależnie od wybranego typu usługi Azure Storage System plików, który jest używany w tym magazynie, musi być obsługiwany przez SAP dla danego systemu operacyjnego i DBMS. [Uwaga dotycząca pomocy technicznej SAP #405827](https://launchpad.support.sap.com/#/notes/405827) zawiera listę obsługiwanych systemów plików dla różnych systemów operacyjnych i baz danych, w tym SAP HANA. Dotyczy to wszystkich woluminów, SAP HANA mogą uzyskać dostęp do odczytu i zapisu dla każdego zadania. W przypadku korzystania z systemu plików NFS na platformie Azure SAP HANA dodatkowe ograniczenia dotyczące wersji systemu plików NFS mają zastosowanie zgodnie z opisem w dalszej części tego artykułu. 


Minimalny SAP HANA warunki certyfikowania dla różnych typów magazynu to: 

- Usługa Azure SSD w warstwie Premium —/Hana/log jest wymagana w pamięci podręcznej w usłudze Azure [Akcelerator zapisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). Wolumin/Hana/Data można umieścić na SSD w warstwie Premium bez platformy Azure akcelerator zapisu lub na dysku Ultra
- Platforma Azure Ultra Disk co najmniej dla woluminu/Hana/log. Wolumin/Hana/Data można umieścić na SSD w warstwie Premium bez akcelerator zapisu platformy Azure lub w celu szybszego ponownego uruchomienia komputera Ultra Disk
- Woluminy **NFS v 4.1** na Azure NetApp Files dla/Hana/log **i** /Hana/Data. Wolumin/Hana/Shared może korzystać z protokołu NFS v3 lub NFS v 4.1. Protokół NFS v 4.1 jest obowiązkowy dla woluminów/Hana/Data i/Hana/log.

Niektóre typy magazynów można łączyć. Na przykład można umieścić/Hana/Data na Premium Storage, a/Hana/log może być umieszczony w magazynie Ultra Disk, aby uzyskać wymagane małe opóźnienia. Jeśli jednak korzystasz z woluminu systemu plików NFS w wersji 4.1 opartego na ANF dla/Hana/Data, musisz użyć innego woluminu systemu plików NFS v 4.1 na podstawie ANF dla/Hana/log. Korzystanie z systemu plików NFS na ANF dla jednego z woluminów (na przykład/Hana/Data) i platformy Azure Premium Storage lub Ultra Disk dla drugiego woluminu (na przykład/Hana/log) **nie jest obsługiwane**.

W lokalnym świecie rzadko trzeba zadbać o podsystemy we/wy i możliwości. Przyczyną jest to, że dostawca urządzenia jest wymagany do upewnienia się, że minimalne wymagania dotyczące magazynu są spełnione dla SAP HANA. Podczas tworzenia infrastruktury platformy Azure należy pamiętać o niektórych wymaganiach wystawionych przez SAP. Niektóre z minimalnych cech przepływności, które są wymagane przez oprogramowanie SAP, są wynikiem:

- Włącz odczyt/zapis na **/Hana/log** z 250 MB/s z 1 MB we/wy
- Włącz działanie odczytu przez co najmniej 400 MB/s dla **/Hana/Data** dla 16 mb i 64 MB we/wy
- Włącz działanie zapisu przez co najmniej 250 MB/s dla **/Hana/Data** z 16 mb i 64 MB we/wy

Uwzględniając, że niskie opóźnienie magazynu ma krytyczne znaczenie dla systemów DBMS, nawet jako DBMS, takie jak SAP HANA, Zachowaj dane w pamięci. Ścieżka krytyczna w magazynie jest zwykle wokół zapisów w dzienniku transakcji w systemach DBMS. Ale również operacje, takie jak zapisywanie punktów zapisu lub ładowanie danych w pamięci po awarii, mogą być krytyczne. W związku z tym, **obowiązkowe** jest korzystanie z dysków Azure Premium dla woluminów **/Hana/Data** i **/Hana/log** . Aby zapewnić minimalną przepływność **/Hana/log** i **/Hana/Data** zgodnie z wymaganiami SAP, należy skompilować RAID 0 za pomocą MDADM lub LVM na wielu dyskach Premium Storage platformy Azure. I używaj woluminów RAID jako woluminów **/Hana/Data** i **/Hana/log** . 

**Zalecenie: jako rozmiary rozłożenia dla RAID 0 zaleca się użycie:**

- 256 KB dla **/Hana/Data**
- 32 KB dla **/Hana/log**

> [!IMPORTANT]
> Rozmiar rozłożonego elementu/Hana/Data został zmieniony z wcześniejszych zaleceń wywołujących 64 KB lub 128 KB do 256 KB w oparciu o środowisko klienta w przypadku nowszych wersji systemu Linux. Rozmiar 256 KB zapewnia nieco lepszą wydajność

> [!NOTE]
> Nie musisz konfigurować żadnego poziomu nadmiarowości przy użyciu woluminów RAID, ponieważ usługa Azure Premium i magazyn w warstwie Standardowa przechowują trzy obrazy wirtualnego dysku twardego. Użycie woluminu RAID ma charakter wyłącznie skonfigurowany w celu zapewnienia wystarczającej przepływności we/wy.

Gromadzenie wielu wirtualnych dysków twardych platformy Azure poniżej macierzy RAID powoduje gromadzenie informacji o przepływności operacji we/wy na sekundę. Dzięki temu, jeśli umieścisz macierz RAID 0 przez 3 x P30 Azure Premium Storage, powinno to dać trzy razy większą liczbę operacji we/wy i trzykrotnie przekroczyć przepływność magazynu pojedynczego dysku P30 Azure Premium Storage.

Należy również pamiętać o ogólnej przepływności we/wy maszyny wirtualnej podczas ustalania rozmiarów lub podejmowania decyzji dotyczących maszyny wirtualnej. Ogólna przepływność magazynu maszyny wirtualnej jest udokumentowana w artykule [zoptymalizowane rozmiary maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Tryb harmonogramu we/wy systemu Linux
System Linux ma kilka różnych trybów planowania operacji we/wy. Typowym zaleceniem za pośrednictwem dostawców systemu Linux i SAP jest ponowna konfiguracja trybu harmonogramu we/wy dla woluminów dysku z trybu **MQ/nieprzekraczalny** lub **kyber** do **aktualizujący nie działa** (poza autokolejką) lub **Brak** dla (wielokolejkowe). Szczegółowe informacje znajdują się w [#1984787 uwagi SAP](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Rozwiązania z Premium Storage i akcelerator zapisu platformy Azure dla maszyn wirtualnych z serii M platformy Azure
Usługa Azure akcelerator zapisu to funkcja dostępna wyłącznie dla maszyn wirtualnych z serii M platformy Azure. Podobnie jak w przypadku nazw, celem funkcji jest poprawa opóźnień operacji we/wy w odniesieniu do Premium Storage platformy Azure. W przypadku SAP HANA akcelerator zapisu powinna być używana tylko w odniesieniu do woluminu **/Hana/log** . W związku z tym **/Hana/Data** i **/Hana/log** to oddzielne woluminy z platformą Azure akcelerator zapisu obsługują tylko wolumin **/Hana/log** . 

> [!IMPORTANT]
> W przypadku korzystania z usługi Azure Premium Storage użycie usługi Azure [Akcelerator zapisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) lub woluminu/Hana/log jest obowiązkowe. Akcelerator zapisu jest dostępny tylko dla maszyn wirtualnych w warstwie Premium i serii M i serii Mv2.

W poniższych zaleceń dotyczących buforowania przyjęto założenie, że właściwości we/wy dla SAP HANA są takie jak:

- Istnieją trudne do odczytania obciążenie dla plików danych platformy HANA. Wyjątki są duże I/OS po ponownym uruchomieniu wystąpienia HANA lub podczas ładowania danych do platformy HANA. Kolejną wielkością operacji odczytu I/OS dla plików danych może być kopia zapasowa bazy danych HANA. Ze względu na to, że buforowanie odczytu nie ma sensu, ponieważ w większości przypadków, wszystkie woluminy plików danych muszą zostać całkowicie odczytane.
- Zapisywanie względem plików danych jest zainstalowano na podstawie obciążeń opartych na platformie HANA punktów zapisu i HANA. Pisanie punktów zapisu jest asynchroniczne i nie utrzymuje żadnych transakcji użytkownika. Zapisywanie danych podczas odzyskiwania po awarii ma kluczowe znaczenie dla wydajności w celu szybkiego reagowania na system. Jednak odzyskiwanie awaryjne powinno być raczej wyjątkowymi sytuacjami
- Istnieją twarde operacje odczytu z plików. Wyjątki są duże we/wy podczas wykonywania kopii zapasowych dziennika transakcji, odzyskiwania po awarii lub w fazie ponownego uruchomienia wystąpienia platformy HANA.  
- Główne obciążenie w odniesieniu do pliku dziennika SAP HANA wykonaj ponownie jest zapisem. Zależnie od charakteru obciążenia można korzystać z operacji we/wy na małą wartość 4 KB lub w innych przypadkach, w których rozmiar operacji wejścia/wyjścia wynosi 1 MB lub więcej. Opóźnienie zapisu w dzienniku SAP HANA ponownego wykonywania ma krytyczne znaczenie dla wydajności.
- Wszystkie zapisy muszą być utrwalane na dysku w niezawodny sposób

**Zalecenie: w wyniku tych zaobserwowanych wzorców we/wy według SAP HANA, buforowanie dla różnych woluminów przy użyciu usługi Azure Premium Storage powinno być ustawione na takie:**

- **/Hana/Data** — brak buforowania
- **/Hana/log** — brak buforowania — wyjątek dla serii M i Mv2, gdzie akcelerator zapisu powinien być włączony bez buforowania odczytu. 
- **/Hana/Shared** — buforowanie odczytu

### <a name="production-recommended-storage-solution"></a>Zalecane rozwiązanie do produkcji magazynu

> [!IMPORTANT]
> Certyfikat SAP HANA dla maszyn wirtualnych z serii M systemu Azure jest oparty wyłącznie na platformie Azure akcelerator zapisu dla woluminu **/Hana/log** . W efekcie scenariusz produkcyjny SAP HANA wdrożenia na maszynach wirtualnych z serii M na platformie Azure powinien zostać skonfigurowany za pomocą usługi Azure akcelerator zapisu dla woluminu **/Hana/log** .  

> [!NOTE]
> W przypadku scenariuszy produkcyjnych Sprawdź, czy określony typ maszyny wirtualnej jest obsługiwany dla SAP HANA przez SAP w [dokumentacji SAP dla IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Zalecenia często przekraczają minimalne wymagania SAP określone wcześniej w tym artykule. Wymienione zalecenia są kompromisem między zaleceniami dotyczącymi rozmiaru w oprogramowaniu SAP a maksymalną przepływności magazynu, które zapewniają różne typy maszyn wirtualnych.

**Zalecenie: zalecane konfiguracje dla scenariuszy produkcyjnych wyglądają następująco:**

| Jednostka SKU maszyny wirtualnej | Pamięć RAM | Maksymalnie z WE/WY MASZYNY WIRTUALNEJ<br /> Przepływność | /hana/data | /hana/log | /hana/shared | wolumin/root | /usr/sap | Hana/kopia zapasowa |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

Sprawdź, czy przepływność magazynu dla różnych sugerowanych woluminów spełnia obciążenie, które chcesz uruchomić. Jeśli obciążenie wymaga wyższych woluminów dla **/Hana/Data** i **/Hana/log**, należy zwiększyć liczbę wirtualnych dysków twardych Premium Storage platformy Azure. Ustalanie wielkości woluminu o większej liczbie dysków VHD nie powoduje zwiększenia przepływności operacji we/wy w ramach limitów typu maszyny wirtualnej platformy Azure.

Usługa Azure akcelerator zapisu działa tylko w połączeniu z usługą [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Tak więc co najmniej dyski usługi Azure Premium Storage tworzące wolumin **/Hana/log** muszą zostać wdrożone jako dyski zarządzane.

Istnieją limity wirtualnych dysków twardych Premium Storage platformy Azure dla maszyny wirtualnej, które mogą być obsługiwane przez usługę Azure akcelerator zapisu. Bieżące limity to:

- 16 wirtualnych dysków twardych dla maszyny wirtualnej z M128xx i M416xx
- 8 wirtualnych dysków twardych dla maszyny wirtualnej z M64xx i M208xx
- 4 wirtualne dyski twarde dla maszyny wirtualnej M32xx

Więcej szczegółowych instrukcji dotyczących włączania usługi Azure akcelerator zapisu można znaleźć w artykule [Akcelerator zapisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Szczegółowe informacje i ograniczenia dotyczące usługi Azure akcelerator zapisu można znaleźć w tej samej dokumentacji.

**Zalecenie: należy używać akcelerator zapisu dla dysków tworzących wolumin/Hana/log**


### <a name="cost-conscious-azure-storage-configuration"></a>Przystępna cena konfiguracja usługi Azure Storage
W poniższej tabeli przedstawiono konfigurację typów maszyn wirtualnych używanych również przez klientów do hostowania SAP HANA na maszynach wirtualnych platformy Azure. Mogą istnieć typy maszyn wirtualnych, które mogą nie spełniać wszystkich minimalnych kryteriów magazynowania dla SAP HANA lub nie są oficjalnie obsługiwane w SAP HANA przez SAP. Jednak te maszyny wirtualne były przeznaczone do użycia w scenariuszach nieprodukcyjnych. **/Hana/Data** i **/Hana/log** są połączone z jednym woluminem. W związku z tym użycie usługi Azure akcelerator zapisu może stać się ograniczeniem w zakresie operacji we/wy na sekundę.

> [!NOTE]
> W przypadku scenariuszy obsługiwanych przez oprogramowanie SAP Sprawdź, czy określony typ maszyny wirtualnej jest obsługiwany dla SAP HANA przez SAP w [dokumentacji SAP dla IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> Zmiana z wcześniejszych zaleceń dotyczących rozwiązania z ekonomicznego kosztu polega na przejściu z [usługi azure HDD w warstwie Standardowa disks](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) w celu lepszego wykonywania i bardziej niezawodnego działania [dysków SSD w warstwie Standardowa platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)

Zalecenia często przekraczają minimalne wymagania SAP określone wcześniej w tym artykule. Wymienione zalecenia są kompromisem między zaleceniami dotyczącymi rozmiaru w oprogramowaniu SAP a maksymalną przepływności magazynu, które zapewniają różne typy maszyn wirtualnych.

| Jednostka SKU maszyny wirtualnej | Pamięć RAM | Maksymalnie z WE/WY MASZYNY WIRTUALNEJ<br /> Przepływność | /Hana/Data i/Hana/log<br /> rozłożone z LVM lub MDADM | /hana/shared | wolumin/root | /usr/sap | Hana/kopia zapasowa |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1 200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2 000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1 000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1 000 GiB | 1 000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1 750 GiB | 1 000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2 000 GiB | 2 000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3 800 GiB | 2 000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2 000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


Dyski zalecane dla mniejszych typów maszyn wirtualnych z 3 x P20 zalewaniu woluminy dotyczące zaleceń dotyczących miejsca zgodnie z oficjalnym [dokumentem magazynu SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Jednak wybór wyświetlany w tabeli został utworzony w celu zapewnienia wystarczającej przepływności dysku dla SAP HANA. Jeśli potrzebujesz zmian w woluminie **/Hana/Backup** , który miał rozmiar do przechowywania kopii zapasowych, które reprezentują dwa razy wolumin pamięci, możesz go dostosować.   
Sprawdź, czy przepływność magazynu dla różnych sugerowanych woluminów spełnia obciążenie, które chcesz uruchomić. Jeśli obciążenie wymaga wyższych woluminów dla **/Hana/Data** i **/Hana/log**, należy zwiększyć liczbę wirtualnych dysków twardych Premium Storage platformy Azure. Ustalanie wielkości woluminu o większej liczbie dysków VHD nie powoduje zwiększenia przepływności operacji we/wy w ramach limitów typu maszyny wirtualnej platformy Azure. 

> [!NOTE]
> Powyższe konfiguracje nie korzystają z [umowy SLA dla jednej maszyny wirtualnej platformy Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) , ponieważ używa ona kombinacji usługi Azure Premium Storage i usługi Azure Standard Storage. Jednak wybór został wybrany w celu optymalizacji kosztów. Aby zapewnić zgodność konfiguracji maszyny wirtualnej z umową SLA platformy Azure, należy wybrać Premium Storage dla wszystkich powyższych dysków wymienionych jako usługa Azure Standard Storage (SXX).


> [!NOTE]
> Podane zalecenia dotyczące konfiguracji dysków dotyczą minimalnych wymagań SAP Express na rzecz dostawców infrastruktury. W rzeczywistych wdrożeniach klientów i scenariuszach obciążeń napotkano sytuacje, w których te zalecenia nadal nie zapewniały wystarczających możliwości. Mogą to być sytuacje, w których klient wymagał szybszego załadowania danych po ponownym uruchomieniu platformy HANA lub w przypadku, gdy konfiguracje kopii zapasowych wymagały wyższego poziomu przepustowości dla magazynu. Inne przypadki uwzględnione w **/Hana/log** , gdzie 5000 IOPS nie są wystarczające dla określonego obciążenia. W związku z tym zapoznaj się z tymi zaleceniami jako punkt wyjścia i Dostosuj się na podstawie wymagań obciążeń.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Konfiguracja usługi Azure Ultra Disk Storage dla SAP HANA
Firma Microsoft jest w trakcie wdrażania nowego typu usługi Azure Storage o nazwie [Azure Ultra Disk](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk). Istotna różnica między usługą Azure Storage zaoferowana do tej pory i niezwykle dyskiem oznacza, że możliwości dysków nie są już powiązane z rozmiarem dysku. Jako klient można zdefiniować te możliwości dla Ultra Disk:

- Rozmiar dysku z zakresu od 4 GiB do 65 536 GiB
- Zakres IOPS z 100 operacji we/wy na sekundę (wartość maksymalna zależy również od typów maszyn wirtualnych)
- Przepływność magazynu z 300 MB/s do 2 000 MB/s

Niezwykle dysk daje możliwość zdefiniowania jednego dysku, który spełnia rozmiar, operacje we/wy i zakres przepływności dysku. Zamiast używać menedżerów woluminów logicznych, takich jak LVM lub MDADM na platformie Azure Premium Storage, do konstruowania woluminów, które spełniają wymagania dotyczące przepustowości operacji wejścia/wyjścia na sekundę. Konfigurację można uruchomić między dyskami Ultra i Premium Storage. W związku z tym można ograniczyć użycie Ultra Disk do krytycznych woluminów/Hana/Data i/Hana/log, a także pokryć inne woluminy za pomocą Premium Storage platformy Azure

Inne zalety programu Ultra Disk mogą być lepszym opóźnieniem odczytu w porównaniu do Premium Storage. Szybsze odczyty mogą mieć zalety, gdy chcesz zmniejszyć czasy uruchamiania platformy HANA i kolejne obciążenie danymi do pamięci. Zalety magazynu Ultra Disk można również wypróbować, gdy HANA zapisuje punktów zapisu. Ponieważ Premium Storage dyski dla/Hana/Data nie są zwykle akcelerator zapisu pamięci podręcznej, opóźnienie zapisu do/Hana/Data na Premium Storage w porównaniu z dyskiem Ultra jest wyższe. Można spodziewać się, że zapis w punkcie zapisu za pomocą Ultra Disk działa lepiej na obudowie Ultra Disk.

> [!IMPORTANT]
> Niezwykle dysk nie jest jeszcze obecny we wszystkich regionach świadczenia usługi Azure i nie obsługuje jeszcze wszystkich typów maszyn wirtualnych wymienionych poniżej. Aby uzyskać szczegółowe informacje o tym, gdzie jest dostępna funkcja Ultra Disk i które rodziny maszyn wirtualnych są obsługiwane, zobacz artykuł [jakie typy dysków są dostępne na platformie Azure?](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk).

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Zalecane rozwiązanie do produkcji magazynu z czystym konfiguracją Ultra Disk
W tej konfiguracji należy osobno przechowywać woluminy/Hana/Data i/Hana/log. Sugerowane wartości są wyprowadzane z kluczowych wskaźników wydajności, które SAP muszą certyfikowanie typów maszyn wirtualnych dla konfiguracji SAP HANA i magazynu zgodnie z zaleceniami w [dokumentacji magazynu SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

Zalecenia często przekraczają minimalne wymagania SAP określone wcześniej w tym artykule. Wymienione zalecenia są kompromisem między zaleceniami dotyczącymi rozmiaru w oprogramowaniu SAP a maksymalną przepływności magazynu, które zapewniają różne typy maszyn wirtualnych.

| Jednostka SKU maszyny wirtualnej | Pamięć RAM | Maksymalnie z WE/WY MASZYNY WIRTUALNEJ<br /> Przepływność | wolumin/Hana/Data | przepływność we/wy/Hana/Data | /Hana/Data IOPS | wolumin/Hana/log | przepływność we/wy/Hana/log | /Hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1 200 MB/s | 600 GB | 700 MB/s | 7500 | 512 GB | 500 MB/s  | 2000 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 MB/s | 7500 | 256 GB | 250 MB/s  | 2000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 400 MB/s | 7500 | 256 GB | 250 MB/s  | 2000 |
| M64ls | 512 GiB | 1 000 MB/s | 600 GB | 600 MB/s | 7500 | 512 GB | 400 MB/s  | 2500 |
| M64s | 1 000 GiB | 1 000 MB/s |  1 200 GB | 600 MB/s | 7500 | 512 GB | 400 MB/s  | 2500 |
| M64ms | 1 750 GiB | 1 000 MB/s | 2 100 GB | 600 MB/s | 7500 | 512 GB | 400 MB/s  | 2500 |
| M128s | 2 000 GiB | 2 000 MB/s |2 400 GB | 1 200 MB/s |9000 | 512 GB | 800 MB/s  | 3000 | 
| M128ms | 3 800 GiB | 2 000 MB/s | 4 800 GB | 1200 Mb/s |9000 | 512 GB | 800 MB/s  | 3000 | 
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 3 500 GB | 1 000 MB/s | 9000 | 512 GB | 400 MB/s  | 2500 | 
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 7 200 GB | 1 000 MB/s | 9000 | 512 GB | 400 MB/s  | 2500 | 
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 7 200 GB | 1 500 MB/s | 9000 | 512 GB | 800 MB/s  | 3000 | 
| M416ms_v2 | 11 400 GiB | 2 000 MB/s | 14 400 GB | 1 500 MB/s | 9000 | 512 GB | 800 MB/s  | 3000 |   

Wymienione wartości mają być punktem początkowym i muszą być oceniane względem rzeczywistych wymagań. Zalety korzystania z usługi Azure Ultra Disk polega na tym, że wartości operacji we/wy i przepływności można dostosować bez konieczności wyłączania maszyny wirtualnej lub zatrzymania obciążenia zastosowanego do systemu.   

> [!NOTE]
> Do tej pory migawki magazynu z magazynem Ultra Disk nie są dostępne. Spowoduje to zablokowanie użycia migawek maszyn wirtualnych za pomocą usług Azure Backup

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>Oszczędne rozwiązanie magazynu z czystym konfiguracją Ultra Disk
W tej konfiguracji woluminy/Hana/Data i/Hana/log są na tym samym dysku. Sugerowane wartości są wyprowadzane z kluczowych wskaźników wydajności, które SAP muszą certyfikowanie typów maszyn wirtualnych dla konfiguracji SAP HANA i magazynu zgodnie z zaleceniami w [dokumentacji magazynu SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). 

Zalecenia często przekraczają minimalne wymagania SAP określone wcześniej w tym artykule. Wymienione zalecenia są kompromisem między zaleceniami dotyczącymi rozmiaru w oprogramowaniu SAP a maksymalną przepływności magazynu, które zapewniają różne typy maszyn wirtualnych.

| Jednostka SKU maszyny wirtualnej | Pamięć RAM | Maksymalnie z WE/WY MASZYNY WIRTUALNEJ<br /> Przepływność | Wolumin dla/Hana/Data i/log | /Hana/Data i przepływność we/wy dziennika | /Hana/Data i operacje we/wy dziennika |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB | 1 200 MB/s | 1 200 GB | 1 200 MB/s | 9 500 | 
| M32ts | 192 GiB | 500 MB/s | 512 GB | 400 MB/s | 9 500 | 
| M32ls | 256 GiB | 500 MB/s | 600 GB | 400 MB/s | 9 500 | 
| M64ls | 512 GiB | 1 000 MB/s | 1 100 GB | 900 MB/s | 10 000 | 
| M64s | 1 000 GiB | 1 000 MB/s |  1 700 GB | 900 MB/s | 10 000 | 
| M64ms | 1 750 GiB | 1 000 MB/s | 2 600 GB | 900 MB/s | 10 000 | 
| M128s | 2 000 GiB | 2 000 MB/s |2 900 GB | 1 800 MB/s |12 000 | 
| M128ms | 3 800 GiB | 2 000 MB/s | 5 300 GB | 1 800 MB/s |12 000 |  
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 4 000 GB | 900 MB/s | 10 000 |  
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 7 700 GB | 900 MB/s | 10 000 | 
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 7 700 GB | 1, 800MBps | 12 000 |  
| M416ms_v2 | 11 400 GiB | 2 000 MB/s | 15 000 GB | 1 800 MB/s | 12 000 |    

Wymienione wartości mają być punktem początkowym i muszą być oceniane względem rzeczywistych wymagań. Zalety korzystania z usługi Azure Ultra Disk polega na tym, że wartości operacji we/wy i przepływności można dostosować bez konieczności wyłączania maszyny wirtualnej lub zatrzymania obciążenia zastosowanego do systemu.  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Woluminy NFS v 4.1 na Azure NetApp Files
Azure NetApp Files zapewnia natywne udziały NFS, które mogą być używane dla woluminów/Hana/Shared,/Hana/Data i/Hana/log. Używanie udziałów systemu plików NFS opartych na ANF dla woluminów/Hana/Data i/Hana/log wymaga użycia protokołu systemu plików NFS w wersji 4.1. Protokół NFS v3 nie jest obsługiwany w przypadku używania woluminów/Hana/Data i/Hana/log w przypadku, gdy udziały są oparte na ANF. 

> [!IMPORTANT]
> Protokół NFS v3 zaimplementowany w Azure NetApp Files **nie** jest obsługiwany do użycia dla/Hana/Data i/Hana/log. Użycie systemu plików NFS 4,1 jest obowiązkowe dla woluminów/Hana/Data i/Hana/log z punktu widzenia funkcjonalnego. W przypadku woluminu/Hana/Shared można użyć systemu NFS v3 lub protokołu NFS v 4.1 z punktu widzenia funkcjonalnego.

### <a name="important-considerations"></a>Ważne zagadnienia
Rozważając Azure NetApp Files dla oprogramowania SAP NetWeaver i SAP HANA, należy pamiętać o następujących kwestiach:

- Minimalna Pula pojemności to 4 TiB.  
- Minimalny rozmiar woluminu to 100 GiB
- Azure NetApp Files i wszystkie maszyny wirtualne, w których zostaną zainstalowane woluminy Azure NetApp Files, muszą znajdować się w tej samej Virtual Network platformy Azure lub w [równorzędnych sieciach wirtualnych](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) w tym samym regionie.  
- Wybrana Sieć wirtualna musi mieć podsieć delegowaną do Azure NetApp Files.
- Przepływność woluminu NetApp Azure to funkcja limitu przydziału woluminu i poziomu usługi, zgodnie z opisem w obszarze [poziom usług dla Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Podczas ustalania wielkości woluminów platformy Azure NetApp, upewnij się, że przepływność spełnia wymagania systemowe HANA.  
- Azure NetApp Files oferuje [zasady eksportowania](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): można kontrolować dozwolonych klientów, typ dostępu (odczyt&zapis, tylko do odczytu itp.). 
- Funkcja Azure NetApp Files nie jest jeszcze dostępna dla strefy. Obecnie Azure NetApp Files funkcja nie jest wdrażana we wszystkich strefach dostępności w regionie świadczenia usługi Azure. Weź pod uwagę potencjalne konsekwencje opóźnienia w niektórych regionach świadczenia usługi Azure.  
- Ważne jest, aby maszyny wirtualne wdrożone w pobliżu usługi Azure NetApp Storage w małych opóźnieniach. 
- Identyfikator użytkownika dla identyfikatora <b>SID</b>adm i identyfikator grupy dla `sapsys` maszyn wirtualnych muszą być zgodne z konfiguracją w Azure NetApp Files. 

> [!IMPORTANT]
> W przypadku obciążeń SAP HANA małe opóźnienia są krytyczne. Skontaktuj się z przedstawicielem firmy Microsoft, aby upewnić się, że maszyny wirtualne i woluminy Azure NetApp Files są wdrożone w bliskiej bliskości.  

> [!IMPORTANT]
> Jeśli istnieje niezgodność między IDENTYFIKATORem użytkownika dla identyfikatora <b>SID</b>adm a identyfikatorem grupy `sapsys` między maszyną wirtualną a konfiguracją usługi Azure NetApp, uprawnienia do plików na woluminach NetApp platformy Azure, zainstalowane na maszynach wirtualnych, będą `nobody`wyświetlane jako. Upewnij się, że określisz prawidłowy identyfikator użytkownika dla <b>identyfikatora SID</b>adm i identyfikator grupy `sapsys`dla, podczas gdy [Nowy system](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) ma Azure NetApp Files.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Ustalanie wielkości dla bazy danych HANA na Azure NetApp Files

Przepływność woluminu NetApp Azure to funkcja rozmiaru woluminu i poziomu usługi, zgodnie z opisem w obszarze [poziom usług dla Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

Podczas projektowania infrastruktury dla oprogramowania SAP na platformie Azure należy pamiętać o minimalnych wymaganiach dotyczących przepływności magazynu przez oprogramowanie SAP, które przekładają się na minimalne charakterystyki przepływności:

- Włącz odczyt/zapis na/Hana/log z 250 MB/s z 1 MB we/wy  
- Włącz działanie odczytu przez co najmniej 400 MB/s dla/Hana/Data dla 16 MB i 64 MB we/wy  
- Włącz działanie zapisu przez co najmniej 250 MB/s dla/Hana/Data z 16 MB i 64 MB we/wy  

[Limity przepływności Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) na 1 TIB przydziału woluminu są następujące:
- Warstwa Premium Storage-64 MiB/s  
- Warstwa Ultra Storage — 128 MiB/s  

> [!IMPORTANT]
> Niezależna od pojemności wdrożonej na pojedynczym woluminie systemu plików NFS przepływność oczekuje na Plateau w zakresie przepustowości 1,2-1.4 GB/s wykorzystanej przez odbiorcę na maszynie wirtualnej. Należy to zrobić z podstawową architekturą oferty ANF i powiązanymi limitami sesji systemu Linux w systemie plików NFS. Numery wydajności i przepływności zgodnie z opisem w artykule [wyniki testów porównawczych wydajności artykułu dla Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/performance-benchmarks-linux) zostały wykonane na jednym udostępnionym woluminie NFS z wieloma maszynami wirtualnymi klienta i w wyniku wielu sesji. Ten scenariusz jest różny dla scenariusza, który mierzę w oprogramowaniu SAP. Gdzie mierzy przepływność z pojedynczej maszyny wirtualnej do woluminu systemu plików NFS. hostowane w witrynie ANF.

W celu spełnienia minimalnych wymagań dotyczących przepływności SAP dla danych i dziennika, a zgodnie z wytycznymi dla `/hana/shared`, zalecane rozmiary będą wyglądać następująco:

| Wolumin | Rozmiar<br /> Warstwa Premium Storage | Rozmiar<br /> Warstwa Ultra Storage | Obsługiwany protokół NFS |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v 4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v 4.1 |
| /hana/shared | Maks. (512 GB, 1xRAM) na 4 węzły procesu roboczego | Maks. (512 GB, 1xRAM) na 4 węzły procesu roboczego | v3 lub v 4.1 |


> [!NOTE]
> Zalecenia dotyczące ustalania wielkości Azure NetApp Files podane w tym miejscu są celem spełnienia minimalnych wymagań SAP Express do dostawców infrastruktury. W rzeczywistych scenariuszach wdrażania klientów i obciążeń, które mogą nie być wystarczające. Użyj tych zaleceń jako punktu wyjścia i adaptacji na podstawie wymagań określonego obciążenia.  

W związku z tym można rozważyć wdrożenie podobnej przepływności dla woluminów ANF, tak jak w przypadku magazynu Ultra Disk. Należy również wziąć pod uwagę rozmiary rozmiarów wymienionych dla woluminów dla różnych jednostek SKU maszyn wirtualnych, które są już wykonywane w tabelach Ultra Disk.

> [!TIP]
> Woluminy Azure NetApp Files można zmieniać dynamicznie, bez konieczności stosowania `unmount` woluminów, zatrzymywać maszyny wirtualne lub zatrzymywać SAP HANA. Dzięki temu można elastycznie spełnić wymagania dotyczące przepływności aplikacji zarówno w oczekiwany sposób, jak i nieprzewidziane.

Dokumentacja dotycząca sposobu wdrażania SAP HANA skalowania w poziomie za pomocą węzła rezerwy przy użyciu woluminów NFS w wersji 4.1, które są hostowane w ANF, jest publikowana w [SAP HANA skalowanie w poziomie za pomocą węzła gotowości na maszynach wirtualnych platformy Azure z Azure NetApp Files na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse).


## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz:

- [SAP HANA Przewodnik wysokiej dostępności dla maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
