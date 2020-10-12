---
title: Typy magazynów platformy Azure dla obciążeń SAP
description: Planowanie typów magazynu platformy Azure dla obciążeń SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/23/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 819ac1f01cc182c79571de35ec0753f694dc7722
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88653617"
---
# <a name="azure-storage-types-for-sap-workload"></a>Typy usługi Azure Storage dla obciążeń SAP
Platforma Azure ma wiele typów magazynów, które różnią się znacznie w zależności od możliwości, przepływności, opóźnień i cen. Niektóre typy magazynów nie są lub są ograniczone do użycia w scenariuszach SAP. Niektóre typy magazynów platformy Azure są dobrze dopasowane lub zoptymalizowane pod kątem określonych scenariuszy obciążeń SAP. Szczególnie w przypadku SAP HANA niektóre typy magazynów platformy Azure uzyskały certyfikat do użycia z SAP HANA. W tym dokumencie przechodzą różne typy magazynów i opisano ich możliwości i użyteczność przy użyciu obciążeń SAP i składników SAP.

Uwagi dotyczące jednostek używanych w tym artykule. Dostawcy chmury publicznej przeniesieli do używania GiB ([nazywana gigabajtem i](https://en.wikipedia.org/wiki/Gibibyte)) lub TIB ([Tebibyte](https://en.wikipedia.org/wiki/Tebibyte) jako jednostki rozmiaru, a nie gigabajtów lub terabajtów). W związku z tym cała dokumentacja platformy Azure i Prizing korzystają z tych jednostek.  W całym dokumencie są odwołujące się do tych jednostek rozmiarów baz MiB, GiB i TiB na wyłączność. Może być konieczne zaplanowanie z MB, GB i TB. Należy więc pamiętać o niewielkich różnicach w obliczeniach, jeśli trzeba zmienić rozmiar przepływności MiB/s 400, 250 zamiast przepływności MiB/s.

## <a name="microsoft-azure-storage-resiliency"></a>Odporność Microsoft Azure Storage

Microsoft Azure Storage HDD w warstwie Standardowa, SSD w warstwie Standardowa, Azure Premium Storage i Ultra Disk przechowuj podstawowy wirtualny dysk twardy (z systemem operacyjnym) i dyskami z danymi dołączonymi do maszyny wirtualnej w trzech kopiach na trzech różnych węzłach magazynu. Przechodzenie w tryb failover do innej repliki i wypełnianie nowej repliki w przypadku awarii węzła magazynu jest niewidoczne. W wyniku tej nadmiarowości **nie** jest wymagane użycie żadnego rodzaju warstwy nadmiarowości magazynu na wielu dyskach platformy Azure. Ten fakt nosi nazwę magazynu lokalnego nadmiarowego (LRS). LRS jest wartością domyślną dla tego typu magazynu na platformie Azure. [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) zapewnia wystarczającą nadmiarowość do osiągnięcia tego samego umowy SLA, co w przypadku innych natywnych magazynów platformy Azure.

Istnieje kilka metod nadmiarowości, które zostały opisane w artykule [Replikacja usługi Azure Storage](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) , która ma zastosowanie do niektórych różnych typów magazynów platformy Azure. 

### <a name="azure-managed-disks"></a>Azure Managed disks

Dyski zarządzane są typu zasobów w Azure Resource Manager, które mogą być używane zamiast wirtualnych dysków twardych przechowywanych na kontach usługi Azure Storage. Managed Disks automatycznie wyrównać z [zestaw dostępności] [Virtual-Machines-Manage-Availability] maszyny wirtualnej, do której są dołączone, i w związku z tym zwiększyć dostępność maszyny wirtualnej i usługi, które są uruchomione na maszynie wirtualnej. Aby uzyskać więcej informacji, zapoznaj się z [artykułem Omówienie](../../managed-disks-overview.md).

W poniższym przykładzie przedstawiono zalety korzystania z dysków zarządzanych:

- Wdrażasz dwie maszyny wirtualne DBMS dla systemu SAP w zestawie dostępności platformy Azure 
- Ponieważ platforma Azure wdraża maszyny wirtualne, dysk z obrazem systemu operacyjnego zostanie umieszczony w innym klastrze magazynu. Pozwala to uniknąć, że obie maszyny wirtualne mają wpływ na problem jednego klastra usługi Azure Storage
- Podczas tworzenia nowych dysków zarządzanych przypisanych do tych maszyn wirtualnych w celu przechowywania danych i plików dziennika bazy danych, nowe dyski dla tych dwóch maszyn wirtualnych są również wdrażane w oddzielnych klastrach magazynu, dlatego nie ma potrzeby udostępniania klastrów magazynów na dyskach drugiej maszyny wirtualnej.

Wdrażanie bez dysków zarządzanych na kontach magazynu zdefiniowanych przez klienta, przydzielanie dysków jest arbitralne i nie ma świadomość faktu, że maszyny wirtualne są wdrażane w AvSet na potrzeby odporności.

> [!NOTE]
> Z tego powodu i kilku innych ulepszeń, które są dostępne wyłącznie za pośrednictwem usługi Managed disks, wymagamy, aby nowe wdrożenia maszyn wirtualnych korzystających z magazynu blokowego platformy Azure dla swoich dysków (wszystkie magazyny Azure z wyjątkiem Azure NetApp Files) musiały używać usługi Azure Managed disks dla podstawowych dysków VHD/OS, dysków z danymi zawierającymi pliki bazy danych SAP. Niezależnie od tego, czy maszyny wirtualne są wdrażane za pomocą zestawu dostępności, w Strefy dostępności lub niezależnie od zestawów i stref. Dyski, które są używane do przechowywania kopii zapasowych, nie muszą być dyskami zarządzanymi.

> [!NOTE]
> Usługa Azure Managed disks udostępnia tylko ustawienia nadmiarowości lokalnej (LRS). 


## <a name="storage-scenarios-with-sap-workloads"></a>Scenariusze magazynu z obciążeniami SAP
Utrwalany magazyn jest wymagany w obciążeniu SAP w różnych składnikach stosu wdrożonych na platformie Azure. Te scenariusze są na liście co najmniej podobne do następujących:

- Stały wirtualny dysk twardy z maszyną wirtualną, która zawiera system operacyjny i inne oprogramowanie instalowane na tym dysku. Dysk/dysk VHD jest katalogiem głównym maszyny wirtualnej. Wszelkie zmiany wprowadzone do niego muszą zostać utrwalone. Dlatego w następnym momencie można zatrzymać i ponownie uruchomić maszynę wirtualną, wszystkie zmiany wprowadzone przed nadal istnieją. Szczególnie w przypadku, gdy maszyna wirtualna jest wdrażana przez platformę Azure na innym hoście niż pierwotnie działała
- Utrwalone dyski danych. Te dyski są dyskami VHD, które są dołączone do przechowywania danych aplikacji. Te dane aplikacji mogą być plikami danych i dziennika/ponownego wykonywania w ramach bazy danych, plików kopii zapasowej lub instalacji oprogramowania. Oznacza dowolny dysk spoza podstawowego wirtualnego dysku twardego, który zawiera system operacyjny
- Udziały plików lub dyski udostępnione, które zawierają Globalny katalog transportu dla NetWeaver lub S/4HANA. Zawartość tych udziałów jest używana przez oprogramowanie działające na wielu maszynach wirtualnych lub służy do tworzenia scenariuszy klastra trybu failover o wysokiej dostępności
- Katalog/sapmnt lub typowe udziały plików dla procesów EDI lub podobnych. Zawartość tych udziałów jest używana przez oprogramowanie działające na wielu maszynach wirtualnych lub służy do tworzenia scenariuszy klastra trybu failover o wysokiej dostępności

W następnych sekcjach omówiono różne typy magazynów platformy Azure i ich użyteczność dla obciążeń SAP, które dotyczą czterech powyższych scenariuszy. Ogólna Kategoryzacja, w jaki sposób należy używać różnych typów magazynu platformy Azure, jest udokumentowana w artykule [jakie typy dysków są dostępne na platformie Azure?](../../disks-types.md). Zalecenia dotyczące korzystania z różnych typów usługi Azure Storage dla obciążeń SAP nie będą się różnić w znacznym zakresie.

Aby uzyskać informacje na temat ograniczeń dotyczących obsługi typów magazynu platformy Azure dla warstwy oprogramowania SAP NetWeaver/4HANA, zapoznaj się z [uwagami dotyczącymi obsługi sap 2015553](https://launchpad.support.sap.com/#/notes/2015553) w przypadku SAP HANA certyfikowane i obsługiwane typy usługi Azure Storage przeczytaj artykuł [SAP HANA konfiguracje magazynu maszyn wirtualnych platformy Azure](./hana-vm-operations-storage.md).

Sekcje opisujące różne typy magazynów platformy Azure zapewniają więcej informacji o ograniczeniach i możliwościach korzystających z obsługiwanego magazynu SAP. 

## <a name="storage-recommendations-for-sap-storage-scenarios"></a>Zalecenia dotyczące magazynu dla scenariuszy magazynu SAP
Przed przejściem do szczegółów prezentujemy podsumowanie i zalecenia, które znajdują się już na początku dokumentu. Szczegóły dotyczące konkretnych typów usługi Azure Storage znajdują się w tej sekcji dokumentu. Podsumowanie zaleceń dotyczących magazynu dla scenariuszy magazynu SAP w tabeli wygląda następująco:

| Scenariusz użycia | Dysk HDD w warstwie Standardowa | Dysk SSD w warstwie Standardowa | Premium Storage | Dysk w warstwie Ultra | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| Dysk systemu operacyjnego | nieodpowiednie |  odpowiednie ograniczenia (inne niż produkcyjne) | zalecane | niemożliwa | niemożliwa |
| Globalny katalog transportu | nieobsługiwane | nieobsługiwane | zalecane | zalecane | zalecane |
| /sapmnt | nieodpowiednie | odpowiednie ograniczenia (inne niż produkcyjne) | zalecane | zalecane | zalecane |
| Wolumin danych w systemie DBMS SAP HANA rodziny maszyn wirtualnych M/Mv2 | nieobsługiwane | nieobsługiwane | zalecane | zalecane | zalecane<sup>2</sup> |
| Wolumin dziennika DBMS SAP HANA rodzin maszyn wirtualnych M/Mv2 | nieobsługiwane | nieobsługiwane | zalecane<sup>1</sup> | zalecane | zalecane<sup>2</sup> | 
| Wolumin danych w systemie DBMS SAP HANA rodziny maszyn wirtualnych Esv3/Edsv4 | nieobsługiwane | nieobsługiwane | zalecane | zalecane | zalecane<sup>2</sup> |
| Wolumin dziennika DBMS SAP HANA rodziny maszyn wirtualnych Esv3/Edsv4 | nieobsługiwane | nieobsługiwane | nieobsługiwane | zalecane | zalecane<sup>2</sup> | 
| Ilość danych w systemie DBMS nienależących do platformy HANA | nieobsługiwane | odpowiednie ograniczenia (inne niż produkcyjne) | zalecane | zalecane | nieobsługiwane |
| Woluminy dziennika systemu DBMS nie należą do rodziny maszyn wirtualnych M/Mv2 | nieobsługiwane | odpowiednie ograniczenia (inne niż produkcyjne) | zalecane<sup>1</sup> | zalecane | nieobsługiwane |
| Woluminy dziennika systemu DBMS z rodziną maszyn wirtualnych non-M/Mv2 | nieobsługiwane | odpowiednie ograniczenia (inne niż produkcyjne) | odpowiednie dla do średniego obciążenia | zalecane | nieobsługiwane |


<sup>1</sup> dzięki użyciu [usługi Azure akcelerator zapisu](../../how-to-enable-write-accelerator.md) dla rodzin maszyn wirtualnych M/Mv2 dla woluminów dziennika dziennika/ponownego wykonywania <sup>2</sup> i ANF wymaga/Hana/Data, a także/Hana/log do ANF 

Właściwości, których można oczekiwać od różnych typów magazynów, takich jak:

| Scenariusz użycia | Dysk HDD w warstwie Standardowa | Dysk SSD w warstwie Standardowa | Premium Storage | Dysk w warstwie Ultra | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| Umowa SLA dotycząca przepływności/operacji we/wy | nie | nie | tak | tak | tak |
| Odczyty opóźnień | wysoka | średni na wysoki | małą | podmilisekunda | podmilisekunda |
| Zapisy opóźnienia | wysoka | średni na wysoki  | Niska (sub-milisekunda<sup>1</sup>) | podmilisekunda | podmilisekunda |
| Obsługiwane przez platformę HANA | nie | nie | tak<sup>1</sup> | tak | tak |
| Możliwe migawki dysków | tak | tak | tak | nie | tak |
| Przydzielanie dysków w różnych klastrach magazynu przy użyciu zestawów dostępności | za poorednictwem dysków zarządzanych | za poorednictwem dysków zarządzanych | za poorednictwem dysków zarządzanych | typ dysku nie jest obsługiwany w przypadku maszyn wirtualnych wdrożonych za pośrednictwem zestawów dostępności | Nr<sup>3</sup> |
| Wyrównane z Strefy dostępności | tak | tak | tak | tak | wymaga zaangażowania firmy Microsoft |
| Nadmiarowość strefowa | nie dla dysków zarządzanych | nie dla dysków zarządzanych | nie dla dysków zarządzanych | nie | nie |
| Nadmiarowość geograficzna | nie dla dysków zarządzanych | nie dla dysków zarządzanych | nie | nie | nie |


<sup>1</sup> z użyciem [Akcelerator zapisu platformy Azure](../../how-to-enable-write-accelerator.md) dla rodzin maszyn wirtualnych M/Mv2 dla woluminów dziennika dziennika/ponownego wykonywania

<sup>2</sup> koszty zależą od aprowizacji operacji we/wy na sekundę

<sup>3</sup> tworzenie różnych pul pojemności ANF nie gwarantuje wdrożenia pul pojemności na różnych jednostkach magazynu


> [!IMPORTANT]
> Aby osiągnąć mniej niż 1 milisekundę opóźnienia we/wy przy użyciu Azure NetApp Files (ANF), musisz współpracować z firmą Microsoft, aby rozmieścić poprawne rozmieszczenie między maszynami wirtualnymi i udziałami NFS w oparciu o ANF. Nie istnieje mechanizm zapewniający automatyczną bliskość między wdrożoną maszyną wirtualną a woluminy NFS hostowane w ANF. Uwzględniając różne ustawienia różnych regionów świadczenia usługi Azure, dodane opóźnienie sieci może wypchnąć opóźnienie operacji we/wy poza 1 milisekundy, jeśli maszyna wirtualna i udział NFS nie są przydzielane w sąsiedztwie.


> [!IMPORTANT]
> Żaden z aktualnie zaproponowanych dysków zarządzanych w usłudze Azure Block Storage lub Azure NetApp Files nie oferuje żadnej wielostrefowej lub geograficznej nadmiarowości. W związku z tym należy upewnić się, że architektury wysokiej dostępności i odzyskiwania po awarii nie polegają na żadnym typie replikacji natywnych magazynów platformy Azure dla tych dysków zarządzanych, plików NFS lub SMB.


## <a name="azure-premium-storage"></a>Azure Premium Storage
Magazyn SSD systemu Azure w warstwie Premium został wprowadzony w celu zapewnienia:

* Niskie opóźnienie we/wy
* Umowy SLA dla operacji we/wy na sekundę
* Mniej zmienności w opóźnieniu we/wy

Ten typ magazynu dotyczy obciążeń systemu DBMS, ruchu magazynu, który wymaga niskich jednocyfrowych opóźnień, a umowy SLA w przypadku operacji we/wy na sekundę w przypadku usługi Azure Premium Storage nie jest rzeczywistym woluminem danych przechowywanym na tych dyskach, ale z kategorią rozmiaru dysku, niezależnie od ilości danych przechowywanych na dysku. Można również tworzyć dyski w magazynie w warstwie Premium, które nie są bezpośrednio mapowane do kategorii rozmiaru przedstawionych w artykule [SSD w warstwie Premium](../../disks-types.md#premium-ssd). Wnioski z tego artykułu są następujące:

- Magazyn jest zorganizowany w zakresach. Na przykład dysk w zakresie 513 GiB do 1024 GiB może mieć te same możliwości i te same miesięczne koszty
- Liczba operacji we/wy na sekundę nie jest śledzona liniowo w kategoriach rozmiaru. Mniejsze dyski poniżej 32 GiB mają wyższą szybkość operacji we/wy na GiB. W przypadku dysków poza 32 GiB do 1024 GiB szybkość operacji we/wy na GiB wynosi od 4-5 IOPS na GiB. W przypadku większych dysków o pojemności do 32 767 GiB szybkość operacji we/wy na GiB jest mniejsza niż 1
- Przepływność we/wy dla tego magazynu nie jest liniowa z rozmiarem kategorii dysku. W przypadku mniejszych dysków, takich jak kategoria między 65 GiB i 128 GiB pojemności, przepływność jest około 780KB/GiB. W przypadku bardzo dużych dysków, takich jak dysk GiB 32 767, przepływność jest około 28KB/GiB
- Nie można zmienić umowy SLA IOPS i przepływności bez zmiany pojemności dysku

Platforma Azure ma umowę SLA na maszynę wirtualną o pojemności 99,9%, która jest powiązana z użyciem usługi Azure Premium Storage lub Azure Ultra Disk Storage. Umowa SLA jest udokumentowana w [umowie SLA dla Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Aby zapewnić zgodność z tą samą umową SLA maszyny wirtualnej, podstawowy dysk VHD oraz **wszystkie** dołączone dyski muszą być w usłudze Azure Premium Storage lub Azure Ultra Disk Storage.

Macierz możliwości dla obciążeń SAP wygląda następująco:

| Możliwość| Komentarz| Notatki/linki | 
| --- | --- | --- | 
| Podstawowy dysk twardy systemu operacyjnego | wiek | Wszystkie systemy |
| Dysk danych | wiek | Wszystkie systemy — [specjalnie dla SAP HANA](../../how-to-enable-write-accelerator.md) |
| Globalny katalog transportu SAP | TAK | [Obsługiwał](https://launchpad.support.sap.com/#/notes/2015553) |
| Sapmnt SAP | wiek | Wszystkie systemy |
| Magazyn kopii zapasowych | wiek | do krótkoterminowego przechowywania kopii zapasowych |
| Udziały/dysk udostępniony | niedostępne | Potrzebuje plików platformy Azure w warstwie Premium lub innej firmy |
| Odporność | LRS | Brak GRS lub ZRS dostępnych dla dysków |
| Opóźnienie | średnio do średnika | - |
| UMOWA SLA DLA OPERACJI IOPS | TAK | - |
| Wydajność operacji we/wy na sekundę | Średni liniowy w nawiasach klamrowych  | [Cennik dysku zarządzanego](https://azure.microsoft.com/pricing/details/managed-disks/) |
| Maksymalna liczba operacji we/wy na dysk | 20 000 [zależnie od rozmiaru dysku](https://azure.microsoft.com/pricing/details/managed-disks/) | Uwzględnij również [limity maszyn wirtualnych](../../sizes.md) |
| Umowa SLA dotycząca przepływności | TAK | - |
| Przepływność liniowa do pojemności | Średni liniowy w nawiasach klamrowych | [Cennik dysku zarządzanego](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA certyfikowane | TAK | [specjalnie dla SAP HANA](../../how-to-enable-write-accelerator.md) |
| Możliwe migawki dysków | TAK | - |
| Możliwe Azure Backup migawek maszyn wirtualnych | TAK | z wyjątkiem dysków [Akcelerator zapisu](../../how-to-enable-write-accelerator.md) w pamięci podręcznej  |
| Koszty | ŚREDNIOOKRESOW | - |

Usługa Azure Premium Storage nie spełnia SAP HANA kluczowych wskaźników wydajności magazynu, które są dostępne w usłudze Azure Premium Storage. Aby spełnić kluczowe wskaźniki wydajności magazynu dla SAP HANA zapisów w dzienniku, należy użyć buforowania akcelerator zapisu platformy Azure zgodnie z opisem w artykule [włączanie akcelerator zapisu](../../how-to-enable-write-accelerator.md). Na platformie Azure akcelerator zapisu wszystkie inne systemy DBMS do zapisu w dziennikach transakcji i ponownego wykonywania operacji zapisywania dzienników. W związku z tym zaleca się używanie go we wszystkich wdrożeniach SAP DBMS. W przypadku SAP HANA użycie usługi Azure akcelerator zapisu w połączeniu z usługą Azure Premium Storage jest obowiązkowe.



**Podsumowanie:** Usługa Azure Premium Storage to jeden z typów magazynu platformy Azure zalecany dla obciążeń SAP. To zalecenie ma zastosowanie do nieprodukcyjnego, a także systemów produkcyjnych. Usługa Azure Premium Storage jest odpowiednia do obsługi obciążeń bazy danych. Użycie usługi Azure akcelerator zapisu pozwala znacznie zwiększyć opóźnienie zapisu na dyskach w warstwie Premium platformy Azure. Jednak w przypadku systemów DBMS o dużej liczbie operacji we/wy i szybkości przepływności należy wykonać nadmierną obsługę administracyjną pojemności magazynu lub użyć funkcji, takich jak miejsca do magazynowania systemu Windows lub menedżerów woluminów logicznych w systemie Linux, aby utworzyć zestawy pasków, które zapewniają odpowiednią pojemność po jednej stronie, ale także wymaganą liczbę IOPS lub przepływność przy najlepszej wydajności.


### <a name="azure-burst-functionality-for-premium-storage"></a>Azure w warstwie Premium Storage
W przypadku dysków usługi Azure Premium Storage o rozmiarze mniejszym lub równym 512 GiB jest oferowana funkcja pojemności. Dokładny sposób działania tworzenia [serii dysków w artykule.](../../linux/disk-bursting.md) Po przeczytaniu artykułu rozumiesz koncepcję naliczania liczby operacji we/wy i przepływności w czasie, gdy obciążenie wejścia/wyjścia jest poniżej wartości nominalnych IOPS i przepływności dysków (Aby uzyskać szczegółowe informacje na temat nominalnej przepływności, zobacz [Cennik dysku zarządzanego](https://azure.microsoft.com/pricing/details/managed-disks/)). Nastąpi naliczanie różnic między bieżącym użyciem a wartością nominalną dysku. Liczby serii są ograniczone do maksymalnie 30 minut.

Idealnymi przypadkami, w których można zaplanować tę funkcję, jest prawdopodobnie woluminy lub dyski zawierające pliki danych dla różnych systemów DBMS. Obciążenie we/wy oczekiwane na te woluminy, zwłaszcza w przypadku systemów z małym i średnim zakresem, powinny wyglądać następująco:

- Od najmniejszej do średniego obciążenia, ponieważ dane są w pamięci podręcznej, a jak w przypadku platformy HANA, powinny być całkowite w pamięci
- Rozerwania zapisu wyzwalane przez punkty kontrolne bazy danych lub punktów zapisu, które są wydawane regularnie
- Obciążenie kopii zapasowej, które odczytuje w ciągłym strumieniu w przypadkach, gdy kopie zapasowe nie są wykonywane za pośrednictwem migawek magazynu
- Aby uzyskać SAP HANA, Załaduj dane do pamięci po ponownym uruchomieniu wystąpienia

Szczególnie w przypadku mniejszych systemów DBMS, w których obciążenie obsługuje tylko setki transakcji na sekundę, takie funkcje szeregów mogą również mieć sens dla dysków lub woluminów, które przechowują transakcję lub Dziennik ponownego wykonywania. Oczekiwane obciążenie dla tego dysku lub woluminów wygląda następująco:

- Regularne zapisywanie na dysku, które są zależne od obciążenia i charakter obciążenia, ponieważ każde zatwierdzenie wystawione przez aplikację może wyzwolić operację we/wy
- Wyższe obciążenie przepływności dla przypadków wykonywania zadań operacyjnych, takich jak tworzenie lub rekompilowanie indeksów
- Odczytywanie obciążeń podczas wykonywania dziennika transakcji lub wykonywania kopii zapasowych dziennika


## <a name="azure-ultra-disk"></a>Azure Ultra Disk
Dyski platformy Azure w warstwie Ultra oferują magazyn danych na dysku z wysoką przepływnością, dużą liczbą operacji we/wy na sekundę i stałym małym opóźnieniem dla maszyn wirtualnych usługi Azure IaaS. Niektóre dodatkowe zalety funkcji Ultra disks to możliwość dynamicznego zmieniania liczby operacji we/wy na sekundę oraz przepływności dysku wraz z obciążeniami bez konieczności ponownego uruchamiania maszyn wirtualnych. Ultra dyski są odpowiednie dla obciążeń intensywnie korzystających z danych, takich jak obciążenie systemu DBMS. Ultra disks można używać tylko jako dysków danych i nie mogą być używane jako podstawowy dysk VHD przechowujący system operacyjny. Zalecamy użycie usługi Azure Premium Storage jako dysku VHD.

Podczas tworzenia dysku można zdefiniować trzy wymiary:

- Pojemność dysku. Zakresy są z przedziału od 4 GiB do 65 536 GiB
- Zainicjowano obsługę operacji we/wy na dysku. Różne wartości maksymalne mają zastosowanie do pojemności dysku. Zapoznaj się z artykułem [Ultra Disk](../../disks-types.md#ultra-disk) , aby uzyskać więcej szczegółów
- Zainicjowana przepustowość magazynu. Inna przepustowość Maksymalna jest zależna od pojemności dysku. Zapoznaj się z artykułem [Ultra Disk](../../disks-types.md#ultra-disk) , aby uzyskać więcej szczegółów

Koszt pojedynczego dysku jest określany przez trzy wymiary, które można zdefiniować osobno dla poszczególnych dysków. 


Macierz możliwości dla obciążeń SAP wygląda następująco:

| Możliwość| Komentarz| Notatki/linki | 
| --- | --- | --- | 
| Podstawowy dysk twardy systemu operacyjnego | nie działa | - |
| Dysk danych | wiek | Wszystkie systemy  |
| Globalny katalog transportu SAP | TAK | [Obsługiwał](https://launchpad.support.sap.com/#/notes/2015553) |
| Sapmnt SAP | wiek | Wszystkie systemy |
| Magazyn kopii zapasowych | wiek | do krótkoterminowego przechowywania kopii zapasowych |
| Udziały/dysk udostępniony | niedostępne | Wymaga innej firmy |
| Odporność | LRS | Brak GRS lub ZRS dostępnych dla dysków |
| Opóźnienie | bardzo niski | - |
| UMOWA SLA DLA OPERACJI IOPS | TAK | - |
| Wydajność operacji we/wy na sekundę | Średni liniowy w nawiasach klamrowych  | [Cennik dysku zarządzanego](https://azure.microsoft.com/pricing/details/managed-disks/) |
| Maksymalna liczba operacji we/wy na dysk | 1 200 do 160 000 | zależne od pojemności dysku |
| Umowa SLA dotycząca przepływności | TAK | - |
| Przepływność liniowa do pojemności | Średni liniowy w nawiasach klamrowych | [Cennik dysku zarządzanego](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA certyfikowane | TAK | - |
| Możliwe migawki dysków | NO | - |
| Możliwe Azure Backup migawek maszyn wirtualnych | NO | - |
| Koszty | Wyższa niż Usługa Premium Storage | - |



**Podsumowanie:** Usługa Azure Ultra disks jest odpowiednim magazynem z małymi opóźnieniami dla wszystkich rodzajów obciążeń SAP. Do tej pory dysk można używać tylko w połączeniu z maszynami wirtualnymi, które zostały wdrożone za pośrednictwem Strefy dostępności (wdrożenie strefowe). Niezwykle dysk nie obsługuje migawek magazynu w tym momencie. W przeciwieństwie do wszystkich innych magazynów dysk twardy dysku VHD nie może być używany w przypadku dysku podstawowego. Niezwykle dysk jest idealnym rozwiązaniem w przypadku, gdy obciążenie we/wy wymaga dużej ilości danych i chcesz dostosować wdrożoną przepływność magazynu lub liczbę operacji wejścia/wyjścia do wzorców obciążenia magazynu, zamiast zmieniać rozmiar w celu maksymalnego użycia przepustowości i liczby operacji wejścia/wyjścia na sekundę.


## <a name="azure-netapp-files-anf"></a>Pliki NetApp platformy Azure (ANF)
[Azure NetApp Files](https://azure.microsoft.com/services/netapp/) jest wynikiem współpracy między firmą Microsoft i NetAppą w celu zapewnienia wysokiej wydajności NATYWNYCH plików NFS i udziałów SMB platformy Azure. Nacisk ma na celu zapewnienie dużej przepustowości i magazynu o małym opóźnieniu, który umożliwia scenariusze wdrażania systemu DBMS, a jednocześnie pozwala na obsługę typowych funkcji operacyjnych magazynu NetApp za pośrednictwem platformy Azure. Udziały NFS/SMB są oferowane na trzech różnych poziomach usługi, które różnią się w zależności od przepływności magazynu i ceny. Poziomy usług są udokumentowane w artykule [poziomy usługi Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). W przypadku różnych typów obciążeń SAP zalecane są następujące poziomy usług:

- Obciążenie systemu SAP DBMS: wydajność, najlepiej Ultra
- SAPMNT: wydajność, najlepiej Ultra
- Globalny katalog transportu: wydajność, najlepiej Ultra

> [!NOTE]
> Minimalny rozmiar aprowizacji to 4 jednostka TiB o nazwie Pula pojemności. Następnie można utworzyć woluminy z tej puli pojemności. Najmniejszy wolumin, który można skompilować, to 100 GiB. Pulę pojemności można rozszerzyć w ramach kroków TiB. Aby uzyskać cennik, zapoznaj się z artykułem [Azure NetApp Files cennika](https://azure.microsoft.com/pricing/details/netapp/)

Magazyn ANF jest obecnie obsługiwany w kilku scenariuszach obciążeń SAP:

- Udostępnianie udziałów SMB lub NFS dla globalnego katalogu transportowego SAP
- Udział sapmnt w scenariuszach wysokiej dostępności, zgodnie z opisem w:
    - [Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie Windows z Azure NetApp Files (SMB) dla aplikacji SAP](./high-availability-guide-windows-netapp-files-smb.md)
    - [Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server z Azure NetApp Files dla aplikacji SAP](./high-availability-guide-suse-netapp-files.md)
    - [Platforma Azure Virtual Machines wysoka dostępność dla oprogramowania SAP NetWeaver na Red Hat Enterprise Linux z Azure NetApp Files dla aplikacji SAP](./high-availability-guide-rhel-netapp-files.md)
- SAP HANA wdrożenia przy użyciu udziałów NFS v 4.1 dla woluminów/Hana/Data i/Hana/log oraz woluminów NFS v 4.1 lub NFS v3 dla woluminów/Hana/Shared, zgodnie z opisem w artykule [SAP HANA konfiguracje magazynu maszyn wirtualnych platformy Azure](./hana-vm-operations-storage.md)

> [!NOTE]
> Żadne inne obciążenia systemu DBMS nie są obsługiwane w przypadku udziałów opartych na Azure NetApp Filesach NFS i SMB. Aktualizacje i zmiany zostaną podane w przypadku zmiany.

Podobnie jak w przypadku usługi Azure Premium Storage, ustalony lub liniowy przepływność na GB może być problemem, gdy wymagane jest przestrzeganie pewnych minimalnych liczb w przepływności. Tak jak w przypadku SAP HANA. Dzięki ANF problem ten może być bardziej wymawiany od dysku Azure Premium. W przypadku dysku z systemem Azure Premium można utworzyć kilka mniejszych dysków z stosunkowo wysoką przepływność na GiB i rozłożonych na siebie, aby zapewnić opłacalność i zwiększyć przepustowość przy mniejszej wydajności. Ten rodzaj rozłożenia nie działa w przypadku udziałów NFS i SMB hostowanych w ANF. To ograniczenie spowodowało wdrożenie nadwyżkowej wydajności, na przykład:

- Aby osiągnąć na przykład przepływność 250 MiB/s na woluminie NFS hostowanym w ANF, należy wdrożyć 1,95 TiB wydajności na poziomie Ultra usługi. 
- Aby osiągnąć 400 MiB/s, należy wdrożyć 3,125 wydajności TiB. Jednak może zajść potrzeba nadmiernej aprowizacji pojemności, aby osiągnąć wymaganą przepływność woluminu. Dzięki temu nadmiernej aprowizacji wydajności wpływ na ceny mniejszych wystąpień platformy HANA. 
- W przypadku korzystania z systemu plików NFS w systemie ANF dla katalogu SAP/sapmnt zwykle jest to najmniejsza liczba 100 GiB do 150 GiB, który jest wymuszany przez Azure NetApp Files. Jednak środowisko klienta wykazało, że powiązana przepływność 12,8 MiB/s (przy użyciu poziomu usługi Ultra Service) może być niewystarczająca i mieć negatywny wpływ na stabilność systemu SAP. W takich przypadkach klienci mogą uniknąć problemów przez zwiększenie ilości woluminu/sapmnt, tak aby zapewnić większą przepływność na tym woluminie.

Macierz możliwości dla obciążeń SAP wygląda następująco:

| Możliwość| Komentarz| Notatki/linki | 
| --- | --- | --- | 
| Podstawowy dysk twardy systemu operacyjnego | nie działa | - |
| Dysk danych | wiek | Tylko SAP HANA  |
| Globalny katalog transportu SAP | TAK | Protokół SMB i system plików NFS |
| Sapmnt SAP | wiek | Wszystkie systemy SMB (tylko Windows) lub NFS (tylko system Linux) |
| Magazyn kopii zapasowych | wiek | - |
| Udziały/dysk udostępniony | TAK | SMB 3,0, system plików NFS v3 i system plików NFS v 4.1 |
| Odporność | LRS | Brak GRS lub ZRS dostępnych dla dysków |
| Opóźnienie | bardzo niski | - |
| UMOWA SLA DLA OPERACJI IOPS | TAK | - |
| Wydajność operacji we/wy na sekundę | ściśle liniowe  | Zależne od [poziomu usługi](../../../azure-netapp-files/azure-netapp-files-service-levels.md) |
| Umowa SLA dotycząca przepływności | TAK | - |
| Przepływność liniowa do pojemności | Średni liniowy w nawiasach klamrowych | Zależne od [poziomu usługi](../../../azure-netapp-files/azure-netapp-files-service-levels.md) |
| HANA certyfikowane | TAK | - |
| Możliwe migawki dysków | TAK | - |
| Możliwe Azure Backup migawek maszyn wirtualnych | NO | - |
| Koszty | Wyższa niż Usługa Premium Storage | - |


Dodatkowe wbudowane funkcje magazynu ANF:

- Możliwość wykonywania migawek woluminu
- Klonowanie woluminów ANF z migawek
- Przywróć woluminy z migawek (przyciąganie i przywracanie)

**Podsumowanie**: Azure NetApp Files to magazyn o małym opóźnieniu z certyfikatem platformy Hana, który umożliwia wdrażanie woluminów i udziałów w systemie plików NFS i SMB. Magazyn obejmuje trzy różne poziomy usług, które zapewniają różną przepływność i operacje we/wy w sposób liniowy dla pojemności GiB woluminu. Magazyn ANF umożliwia SAP HANA wdrażanie scenariuszy skalowalnych w poziomie przy użyciu węzła w stanie wstrzymania. Magazyn jest odpowiedni do udostępniania udziałów plików w razie potrzeby w przypadku globalnego katalogu transportowego/sapmnt lub SAP. Magazyn ANF zapewnia dostępność funkcji, która jest dostępna jako natywne funkcje NetApp.  



## <a name="azure-standard-ssd-storage"></a>Magazyn usługi Azure Standard SSD
W porównaniu z magazynem dysków twardych platformy Azure w warstwie Standardowa dysk SSD systemu Azure zapewnia lepszą dostępność, spójność, niezawodność i opóźnienia. Jest zoptymalizowany pod kątem obciążeń, które wymagają spójnej wydajności na niższych poziomach IOPS. Ten magazyn jest minimalnym magazynem używanym dla nieprodukcyjnych systemów SAP, które mają niewielkie zapotrzebowanie na operacje we/wy i przepływność. Macierz możliwości dla obciążeń SAP wygląda następująco:

| Możliwość| Komentarz| Notatki/linki | 
| --- | --- | --- | 
| Podstawowy dysk twardy systemu operacyjnego | ograniczone odpowiednie | Systemy nieprodukcyjne |
| Dysk danych | ograniczone odpowiednie | Niektóre systemy nieprodukcyjne z niskimi żądaniami IOPS i opóźnieniami |
| Globalny katalog transportu SAP | NO | [Nieobsługiwany](https://launchpad.support.sap.com/#/notes/2015553) |
| Sapmnt SAP | ograniczone odpowiednie | Systemy nieprodukcyjne |
| Magazyn kopii zapasowych | wiek | - |
| Udziały/dysk udostępniony | niedostępne | Wymaga innej firmy |
| Odporność | LRS, GRS | Brak dostępnych ZRS dla dysków |
| Opóźnienie | wysoka | zbyt wysoka w przypadku globalnego katalogu transportowego SAP lub systemów produkcyjnych |
| UMOWA SLA DLA OPERACJI IOPS | NO | - |
| Maksymalna liczba operacji we/wy na dysk | 500 | Niezależny od rozmiaru dysku |
| Umowa SLA dotycząca przepływności | NO | - |
| HANA certyfikowane | NO | - |
| Możliwe migawki dysków | TAK | - |
| Możliwe Azure Backup migawek maszyn wirtualnych | TAK | - |
| Koszty | LOW (priorytet niski) | - |



**Podsumowanie:** Magazyn dysków SSD platformy Azure w warstwie Standardowa jest minimalnym zaleceniem dla nieprodukcyjnych maszyn wirtualnych dla podstawowego wirtualnego dysku twardego, ostatecznych wdrożeń systemu DBMS ze względu na niezgodność opóźnienia i/lub niskiej liczby IOPS i stawek przepływności. Ten typ magazynu platformy Azure nie jest już obsługiwany na potrzeby hostowania globalnego katalogu transportu SAP. 



## <a name="azure-standard-hdd-storage"></a>Magazyn dysków twardych platformy Azure w warstwie Standardowa
Magazyn HDD w warstwie Standardowa platformy Azure był jedynym typem magazynu, gdy infrastruktura platformy Azure uzyskała certyfikat dla obciążenia SAP NetWeaver w roku 2014. W roku 2014 maszyny wirtualne platformy Azure były małe i niskie w przepływności magazynu. W związku z tym, ten typ magazynu był w stanie tylko sprostać wymaganiom. Magazyn jest idealnym rozwiązaniem dla obciążeń niewrażliwych na opóźnienia, co pozwala na twardą pracę w przestrzeni SAP. Dzięki zwiększeniu przepływności maszyn wirtualnych platformy Azure i zwiększonym obciążeniem wytwarzanym przez maszyny wirtualne ten typ magazynu nie jest brany pod uwagę w przypadku korzystania z scenariuszy SAP. Macierz możliwości dla obciążeń SAP wygląda następująco:

| Możliwość| Komentarz| Notatki/linki | 
| --- | --- | --- | 
| Podstawowy dysk twardy systemu operacyjnego | nieodpowiednie | - |
| Dysk danych | nieodpowiednie | - |
| Globalny katalog transportu SAP | NO | [Nieobsługiwany](https://launchpad.support.sap.com/#/notes/2015553) |
| Sapmnt SAP | NO | Nieobsługiwane |
| Magazyn kopii zapasowych | wiek | - |
| Udziały/dysk udostępniony | niedostępne | Potrzebuje Azure Files lub innych firm |
| Odporność | LRS, GRS | Brak dostępnych ZRS dla dysków |
| Opóźnienie | wysoka | zbyt wysoka w przypadku użycia systemu DBMS, globalnego katalogu transportu SAP lub sapmnt/saploc |
| UMOWA SLA DLA OPERACJI IOPS | NO | - |
| Maksymalna liczba operacji we/wy na dysk | 500 | Niezależny od rozmiaru dysku |
| Umowa SLA dotycząca przepływności | NO | - |
| HANA certyfikowane | NO | - |
| Możliwe migawki dysków | TAK | - |
| Możliwe Azure Backup migawek maszyn wirtualnych | TAK | - |
| Koszty | LOW (priorytet niski) | - |



**Podsumowanie:** HDD w warstwie Standardowa jest typem magazynu platformy Azure, który powinien być używany tylko do przechowywania kopii zapasowych SAP. Powinien być używany tylko jako podstawowy dysk VHD dla nieaktywnych systemów, takich jak wycofane systemy używane do wyszukiwania danych w tym miejscu. Nie należy jednak na podstawie tego magazynu mieć aktywnych maszyn wirtualnych, pytań i prac produkcyjnych. Ani pliki bazy danych hostowane w tym magazynie


## <a name="azure-vm-limits-in-storage-traffic"></a>Limity maszyn wirtualnych platformy Azure w ruchu magazynu
W przeciwieństwie do scenariuszy lokalnych wybrany typ wybranej maszyny wirtualnej odgrywa istotną rolę w przepustowości magazynu, którą można osiągnąć. W przypadku różnych typów magazynów należy rozważyć następujące kwestie:

| Typ magazynu| Linux | Windows | Komentarze |
| --- | --- | --- | --- |
| Dysk HDD w warstwie Standardowa | [Rozmiary maszyn wirtualnych z systemem Linux na platformie Azure](../../sizes.md) | [Rozmiary maszyn wirtualnych z systemem Windows na platformie Azure](../../sizes.md) | Trudne do dotknięcia limity magazynu dla średnich lub dużych maszyn wirtualnych |
| Dysk SSD w warstwie Standardowa | [Rozmiary maszyn wirtualnych z systemem Linux na platformie Azure](../../sizes.md) | [Rozmiary maszyn wirtualnych z systemem Windows na platformie Azure](../../sizes.md) | Trudne do dotknięcia limity magazynu dla średnich lub dużych maszyn wirtualnych |
| Premium Storage | [Rozmiary maszyn wirtualnych z systemem Linux na platformie Azure](../../sizes.md) | [Rozmiary maszyn wirtualnych z systemem Windows na platformie Azure](../../sizes.md) | Łatwe osiąganie limitów liczby operacji we/wy na sekundę oraz przepływności maszyn wirtualnych z konfiguracją magazynu |
| Magazyn Ultra Disk | [Rozmiary maszyn wirtualnych z systemem Linux na platformie Azure](../../sizes.md) | [Rozmiary maszyn wirtualnych z systemem Windows na platformie Azure](../../sizes.md) | Łatwe osiąganie limitów liczby operacji we/wy na sekundę oraz przepływności maszyn wirtualnych z konfiguracją magazynu |
| Azure NetApp Files | [Rozmiary maszyn wirtualnych z systemem Linux na platformie Azure](../../sizes.md) | [Rozmiary maszyn wirtualnych z systemem Windows na platformie Azure](../../sizes.md) | Ruch magazynu wykorzystuje przepustowość sieci, a nie przepustowość magazynu! |

Zgodnie z ograniczeniami można zauważyć, że:

- Im mniejsza jest maszyna wirtualna, tym mniej dysków, które można dołączyć. Nie dotyczy to ANF. Ze względu na to, że instalujesz udziały NFS lub SMB, nie ma limitu liczby udostępnionych woluminów do dołączenia
- Maszyny wirtualne mają limity przepływności we/wy i liczby operacji wejścia/wyjścia, które można łatwo przekroczyć za pomocą dysków magazynu w warstwie Premium i bardzo dysków
- W przypadku ANF ruch do udostępnionych woluminów zużywa przepustowość sieci maszyny wirtualnej, a nie przepustowość magazynu
- W przypadku dużych woluminów NFS w TiBej przestrzeni dyskowej, przepływność uzyskujących dostęp do tego woluminu z pojedynczej maszyny wirtualnej jest w oparciu o limity z systemu Linux dla jednej sesji, która współdziała z udostępnionym woluminem. 

W przypadku dużych maszyn wirtualnych platformy Azure w cyklu życia systemu SAP należy oszacować limity przepływności operacji we/wy na sekundę dla nowego i większego typu maszyny wirtualnej. W niektórych przypadkach może być również przydatne dostosowanie konfiguracji magazynu do nowych funkcji maszyny wirtualnej platformy Azure. 


## <a name="striping-or-not-striping"></a>Rozłożenie lub bez rozłożenia
Utworzenie zestawu rozłożonego z wielu dysków platformy Azure w jednym większym woluminie umożliwia gromadzenie operacji we/wy i przepływności poszczególnych dysków w jednym woluminie. Jest ona używana tylko w przypadku usługi Azure Standard Storage i Azure Premium Storage. Azure Ultra Disk, w której można skonfigurować przepływność i operacje we/wy niezależnie od pojemności dysku, nie wymagają użycia zestawów paskowych. Woluminów udostępnionych opartych na systemie plików NFS lub SMB nie można rozmieścić. Ze względu na nieliniową naturę przepływności i liczby operacji we/wy na platformie Azure Premium możesz udostępnić mniejszą pojemność z użyciem tej samej liczby operacji we/wy na sekundę, co w przypadku dużych dysków usługi Azure Premium Storage. Jest to metoda osiągnięcia wyższej przepływności lub liczby operacji we/wy przy niższych kosztach przy użyciu usługi Azure Premium Storage. Na przykład:

- Rozłożenie na dwa dyski usługi P15 Premium Storage zapewnia przepływność 
- 250 MiB/s. Taki wolumin będzie miał pojemność 512 GiB. Jeśli chcesz mieć pojedynczy dysk zapewniający przepustowość 250 MiB na sekundę, należy wybrać dysk P40 o pojemności 2 TiB. 
- Lub można osiągnąć przepływność 400 MiB/s przez rozłożenie czterech dysków usługi P10 Premium Storage o łącznej pojemności 512 GiB przez rozłożenie. Jeśli chcesz mieć pojedynczy dysk z co najmniej 500m przepływności MiB na sekundę, musisz wybrać dysk magazynu P60 Premium z 8 TiB. Ze względu na to, że usługa Costing lub Premium Storage jest bliska wydajności, można wyrównać oszczędności kosztów przy użyciu rozłożenia.

Należy przestrzegać kilku reguł:

- Nie należy używać magazynu skonfigurowanego w maszynie wirtualnej, ponieważ usługa Azure Storage utrzymuje już nadmiarowe dane
- Dyski, do których zastosowano zestaw rozłożony, muszą mieć taki sam rozmiar

Rozłożenie na kilka mniejszych dysków jest najlepszym sposobem osiągnięcia dobrego kosztu/wydajności przy użyciu usługi Azure Premium Storage. Należy zrozumieć, że rozmieszczenie obejmuje pewne dodatkowe obciążenie i zarządzanie.

Aby zapoznać się z konkretnymi zaleceniami dotyczącymi rozmiaru paska, zapoznaj się z dokumentacją dla różnych systemów DBMS, na przykład [SAP HANA konfiguracjami magazynu maszyn wirtualnych platformy Azure](./hana-vm-operations-storage.md).




## <a name="next-steps"></a>Następne kroki
Zapoznaj się z artykułami:

- [Zagadnienia dotyczące wdrażania systemu Azure Virtual Machines DBMS dla obciążeń SAP](./dbms_guide_general.md)
- [Konfiguracje magazynu maszyn wirtualnych platformy Azure SAP HANA](./hana-vm-operations-storage.md)
 