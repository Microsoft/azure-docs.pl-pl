---
title: Projektowanie i wdrażanie bazy danych Oracle na platformie Azure | Dokumenty firmy Microsoft
description: Projektowanie i wdrażanie bazy danych Oracle w środowisku platformy Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: BorisB2015
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: borisb
ms.openlocfilehash: ad446180b3bd864c5b6df808e6e4efac7d6c1c65
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687537"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Projektowanie i wdrażanie bazy danych Oracle na platformie Azure

## <a name="assumptions"></a>Założenia

- Planujesz migrację bazy danych Oracle z lokalnego środowiska na platformę Azure.
- Masz [pakiet diagnostyczny](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) dla bazy danych Oracle Database, którą chcesz przeprowadzić migrację
- Znasz różne metryki w raportach Oracle AWR.
- Masz podstawowej wiedzy o wydajności aplikacji i wykorzystania platformy.

## <a name="goals"></a>Cele

- Dowiedz się, jak zoptymalizować wdrożenie oracle na platformie Azure.
- Poznaj opcje dostrajania wydajności bazy danych Oracle w środowisku platformy Azure.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>Różnice między implementacją lokalną i platformą Azure 

Oto kilka ważnych rzeczy, o których należy pamiętać podczas migracji aplikacji lokalnych na platformę Azure. 

Jedną z istotnych różnic jest to, że w implementacji platformy Azure zasoby, takie jak maszyny wirtualne, dyski i sieci wirtualne są współużytkowane przez innych klientów. Ponadto zasoby mogą być ograniczane na podstawie wymagań. Zamiast skupiać się na unikaniu awarii (MTBF), platforma Azure jest bardziej skoncentrowana na przetrwaniu awarii (MTTR).

W poniższej tabeli wymieniono niektóre różnice między implementacją lokalną a implementacją bazy danych Oracle na platformie Azure.

> 
> |  | **Implementacja lokalna** | **Implementacja platformy Azure** |
> | --- | --- | --- |
> | **Networking** |SIEĆ LAN/WAN  |SDN (sieć definiowana programowo)|
> | **Grupa zabezpieczeń** |Narzędzia ograniczeń IP/portów |[Grupa zabezpieczeń sieci (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **Odporności** |MTBF (średni czas między awariami) |MTTR (średni czas do odzyskania)|
> | **Planowana konserwacja** |Łatanie/uaktualnienia|[Zestawy dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (poprawki/uaktualnienia zarządzane przez platformę Azure) |
> | **Zasób** |Dedykowane  |Współdzielone z innymi klientami|
> | **Regiony** |Centra danych |[Pary regionów](https://docs.microsoft.com/azure/virtual-machines/windows/regions#region-pairs)|
> | **Storage** |SIECI SAN/dyski fizyczne |[Magazyn zarządzany przez platformę Azure](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **Skalowanie** |Skala pionowa |Skalowanie w poziomie|


### <a name="requirements"></a>Wymagania

- Określ rozmiar bazy danych i tempo wzrostu.
- Określ wymagania dotyczące usług We/Wy, które można oszacować na podstawie raportów Oracle AWR lub innych narzędzi do monitorowania sieci.

## <a name="configuration-options"></a>Opcje konfiguracji

Istnieją cztery potencjalne obszary, które można dostroić, aby zwiększyć wydajność w środowisku platformy Azure:

- Rozmiar maszyny wirtualnej
- Przepustowość sieci
- Typy i konfiguracje dysków
- Ustawienia pamięci podręcznej dysku

### <a name="generate-an-awr-report"></a>Generowanie raportu AWR

Jeśli masz istniejącą bazę danych Oracle i planujesz migrację na platformę Azure, masz kilka opcji. Jeśli masz [pakiet diagnostyczny](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) dla wystąpień Oracle, możesz uruchomić raport Oracle AWR, aby uzyskać metryki (IOPS, Mbps, GiBs i tak dalej). Następnie wybierz maszynę wirtualną na podstawie zebranych metryk. Możesz też skontaktować się z zespołem infrastruktury, aby uzyskać podobne informacje.

Można rozważyć uruchomienie raportu AWR podczas regularnych i szczytowych obciążeń, dzięki czemu można porównać. Na podstawie tych raportów można rozmiar maszyn wirtualnych na podstawie średniego obciążenia lub maksymalnego obciążenia.

Oto przykład generowania raportu AWR (Generowanie raportów AWR przy użyciu oracle enterprise managera, jeśli bieżąca instalacja ma taki raport):

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Kluczowe dane

Poniżej przedstawiono metryki, które można uzyskać z raportu AWR:

- Całkowita liczba rdzeni
- Szybkość zegara procesora
- Całkowita ilość pamięci w GB
- Wykorzystanie procesora
- Szczytowa szybkość przesyłania danych
- Szybkość zmian we/wy (odczyt/zapis)
- Szybkość ponownego rejestrowania (MBPs)
- Przepustowość sieci
- Szybkość opóźnienia sieci (niska/wysoka)
- Rozmiar bazy danych w GB
- Bajty odebrane za pośrednictwem języka SQL*Net z/do klienta

### <a name="virtual-machine-size"></a>Rozmiar maszyny wirtualnej

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. Oszacowanie rozmiaru maszyny Wirtualnej na podstawie użycia procesora, pamięci i we/wy z raportu AWR

Jedną z rzeczy, które można spojrzeć na to pięć najważniejszych zdarzeń pierwszego planu, które wskazują, gdzie są wąskie gardła systemu.

Na przykład na poniższym diagramie synchronizacja plików dziennika znajduje się u góry. Wskazuje liczbę oczekujących, które są wymagane, zanim LGWR zapisuje bufor dziennika do pliku dziennika ponawiać. Wyniki te wskazują, że wymagana jest lepsza wydajność magazynu lub dysków. Ponadto diagram pokazuje również liczbę procesorów (rdzeni) i ilość pamięci.

![Zrzut ekranu przedstawiający stronę raportu AWR](./media/oracle-design/cpu_memory_info.png)

Na poniższym diagramie przedstawiono całkowitą liczbę we/wy odczytu i zapisu. W czasie raportu napisano 59 GB odczytu i 247,3 GB.

![Zrzut ekranu przedstawiający stronę raportu AWR](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Wybierz maszynę wirtualną

Na podstawie informacji zebranych z raportu AWR następnym krokiem jest wybranie maszyny Wirtualnej o podobnym rozmiarze, która spełnia twoje wymagania. Listę dostępnych maszyn wirtualnych można znaleźć w artykule [Zoptymalizowano pamięć](../../linux/sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. Dostosuj rozmiar maszyny wirtualnej za pomocą podobnej serii maszyn wirtualnych na podstawie

Po wybraniu maszyny Wirtualnej należy zwrócić uwagę na maszynę ACU dla maszyny Wirtualnej. Możesz wybrać inną maszynę wirtualną na podstawie wartości ACU, która lepiej odpowiada Twoim wymaganiom. Aby uzyskać więcej informacji, zobacz [jednostkę obliczeniową platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/acu).

![Zrzut ekranu przedstawiający stronę jednostek ACU](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Przepustowość sieci

Na poniższym diagramie przedstawiono relację między przepływnością a we/wy:

![Zrzut ekranu przedstawiający przepływność](./media/oracle-design/throughput.png)

Całkowita przepustowość sieci jest szacowana na podstawie następujących informacji:
- SQL*Ruch sieciowy
- Mb/s x liczba serwerów (strumień wychodzący, taki jak Oracle Data Guard)
- Inne czynniki, takie jak replikacja aplikacji

![Zrzut ekranu przedstawiający przepływność sieciOWEJ SQL](./media/oracle-design/sqlnet_info.png)

W zależności od wymagań dotyczących przepustowości sieci istnieją różne typy bram do wyboru. Należą do nich podstawowe, VpnGw i Azure ExpressRoute. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem bramy sieci VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h).

**Zalecenia**

- Opóźnienie sieci jest wyższe w porównaniu do wdrożenia lokalnego. Ograniczenie sieci w obie strony może znacznie poprawić wydajność.
- Aby zmniejszyć rund, skonsoliduj aplikacje, które mają wysokie transakcje lub "chatty" aplikacje na tej samej maszynie wirtualnej.
- Użyj maszyn wirtualnych z [przyspieszoną siecią, aby](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) uzyskać lepszą wydajność sieci.
- W przypadku niektórych distrubutions Linuksa, należy rozważyć włączenie [trim / UNMAP wsparcia](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm#trimunmap-support).
- Zainstaluj [Oracle Enterprise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) na osobnej maszynie wirtualnej.
- Ogromne strony nie są domyślnie włączone w systemie Linux. Rozważ włączenie ogromnych stron `use_large_pages = ONLY` i ustawienie ich w bazie danych Oracle DB. Może to pomóc zwiększyć wydajność. Więcej informacji można znaleźć [tutaj](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390).

### <a name="disk-types-and-configurations"></a>Typy i konfiguracje dysków

- *Domyślne dyski systemu operacyjnego: Te typy dysków*oferują trwałe dane i buforowanie. Są one zoptymalizowane pod kątem dostępu do systemu operacyjnego podczas uruchamiania i nie są przeznaczone do obciążeń transakcyjnych lub magazynów danych (analitycznych).

- *Dyski niezarządzane:* w przypadku tych typów dysków można zarządzać kontami magazynu, które przechowują pliki wirtualnego dysku twardego (VHD), które odpowiadają dyskom maszyn wirtualnych. Pliki VHD są przechowywane jako obiekty blob stron na kontach magazynu platformy Azure.

- *Dyski zarządzane:* platforma Azure zarządza kontami magazynu używanymi dla dysków maszyn wirtualnych. Należy określić typ dysku (premium lub standard) i rozmiar dysku, który jest potrzebny. Platforma Azure tworzy dysk i zarządza nim.

- *Dyski magazynu w wersji premium:* Te typy dysków najlepiej nadają się do obciążeń produkcyjnych. Magazyn w stanie Premium obsługuje dyski maszyn wirtualnych, które można podłączyć do maszyn wirtualnych z serii o określonym rozmiarze, takich jak maszyny wirtualne z serii DS, DSv2, GS i F. Dysk premium ma różne rozmiary i można wybierać między dyskami od 32 GB do 4096 GB. Każdy rozmiar dysku ma własne specyfikacje wydajności. W zależności od wymagań aplikacji można dołączyć jeden lub więcej dysków do maszyny Wirtualnej.

Podczas tworzenia nowego dysku zarządzanego z portalu można wybrać **typ konta** dla typu dysku, którego chcesz użyć. Należy pamiętać, że nie wszystkie dostępne dyski są wyświetlane w menu rozwijanym. Po wybraniu określonego rozmiaru maszyny Wirtualnej menu pokazuje tylko dostępne jednostki SKU magazynu w wersji premium, które są oparte na tym rozmiarze maszyny Wirtualnej.

![Zrzut ekranu przedstawiający stronę dysku zarządzanego](./media/oracle-design/premium_disk01.png)

Po skonfigurowaniu magazynu na maszynie wirtualnej, można załadować przetestować dyski przed utworzeniem bazy danych. Znajomość szybkości we/wy pod względem opóźnienia i przepływności może pomóc określić, czy maszyny wirtualne obsługują oczekiwaną przepływność z docelowymi opóźnieniami.

Istnieje wiele narzędzi do testowania obciążenia aplikacji, takich jak Oracle Orion, Sysbench i Fio.

Ponownie uruchom test obciążenia po wdrożeniu bazy danych Oracle. Rozpocznij regularne i szczytowe obciążenia, a wyniki pokazują linię bazową środowiska.

Może być ważniejsze rozmiar magazynu na podstawie szybkości We/Wy, a nie rozmiar magazynu. Na przykład jeśli wymagane usługi We/Wy są 5000, ale potrzebujesz tylko 200 GB, nadal możesz uzyskać dysk premium klasy P30, nawet jeśli ma więcej niż 200 GB miejsca.

Szybkość We/Wy można uzyskać z raportu AWR. Jest określana przez dziennik ponawiania, odczyty fizyczne i szybkość zapisu.

![Zrzut ekranu przedstawiający stronę raportu AWR](./media/oracle-design/awr_report.png)

Na przykład rozmiar ponawienie wynosi 12 200 000 bajtów na sekundę, co odpowiada 11,63 MB/s.
IOPS jest 12,200,000 / 2,358 = 5,174.

Po jasnym obrazie wymagań we/wy można wybrać kombinację dysków, które najlepiej spełniają te wymagania.

**Zalecenia**

- W przypadku obszaru tabel danych rozłóż obciążenie we/wy na wielu dyskach przy użyciu zarządzanego magazynu lub programu Oracle ASM.
- Wraz ze wzrostem rozmiaru bloku we/wy dla operacji intensywnie korzystających z odczytu i intensywnie zapisu dodaj więcej dysków danych.
- Zwiększ rozmiar bloku dla dużych procesów sekwencyjnych.
- Użyj kompresji danych, aby zmniejszyć we/wy (zarówno dla danych, jak i indeksów).
- Oddziel ponownie dzienniki, system i temps oraz cofnij awarie na oddzielnych dyskach z danymi.
- Nie umieszczaj żadnych plików aplikacji na domyślnych dyskach systemu operacyjnego (/dev/sda). Te dyski nie są zoptymalizowane pod kątem szybkich czasów rozruchu maszyny Wirtualnej i mogą nie zapewniać dobrej wydajności dla aplikacji.
- W przypadku korzystania z maszyn wirtualnych z serii M w magazynie w wersji Premium włącz [akcelerator zapisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) na dysku dziennika ponawianie.

### <a name="disk-cache-settings"></a>Ustawienia pamięci podręcznej dysku

Istnieją trzy opcje buforowania hosta:

- *ReadOnly:* Wszystkie żądania są buforowane dla przyszłych odczytów. Wszystkie zapisy są utrwalone bezpośrednio do magazynu obiektów Blob platformy Azure.

- *ReadWrite:* Jest to algorytm "read-ahead". Odczyty i zapisy są buforowane dla przyszłych odczytów. Zapisy nieprzepisowe są najpierw utrwalone w lokalnej pamięci podręcznej. Zapewnia również najniższe opóźnienie dysku dla lekkich obciążeń. Za pomocą ReadWrite pamięci podręcznej z aplikacją, która nie obsługuje utrwalania wymaganych danych może prowadzić do utraty danych, jeśli maszyna wirtualna ulegnie awarii.

- *Brak* (wyłączone): Za pomocą tej opcji można pominąć pamięci podręcznej. Wszystkie dane są przesyłane na dysk i utrwalone w usłudze Azure Storage. Ta metoda zapewnia najwyższą stawkę we/wy dla obciążeń intensywnie korzystających z we/wy. Należy również wziąć pod uwagę "koszt transakcji".

**Zalecenia**

Aby zmaksymalizować przepływność, zaleca się, aby **rozpocząć** brak dla buforowania hosta. W przypadku magazynu w wersji Premium należy pamiętać, że należy wyłączyć "bariery" podczas instalowania systemu plików z opcjami **ReadOnly** lub **None.** Zaktualizuj /etc/fstab plik z UUID do dysków.

![Zrzut ekranu przedstawiający stronę dysku zarządzanego](./media/oracle-design/premium_disk02.png)

- W przypadku dysków systemu operacyjnego należy użyć domyślnego buforowania **odczytu/zapisu.**
- W systemach, temp i cofnij używać **brak** do buforowania.
- W przypadku danych użyj **none** do buforowania. Ale jeśli baza danych jest tylko do odczytu lub intensywnie odczytu, należy użyć buforowania tylko do **odczytu.**

Po zapisaniu ustawienia dysku danych nie można zmienić ustawienia pamięci podręcznej hosta, chyba że dysk zostanie odinstalowany na poziomie systemu operacyjnego, a następnie ponownie go zamontować po dokonaniu zmiany.

## <a name="security"></a>Zabezpieczenia

Po skonfigurowaniu i skonfigurowaniu środowiska platformy Azure następnym krokiem jest zabezpieczenie sieci. Oto kilka zaleceń:

- *Zasady sieciowej grupy:* sieciowej grupy sieciowej można zdefiniować za pomocą podsieci lub karty sieciowej. Łatwiej jest kontrolować dostęp na poziomie podsieci, zarówno dla zabezpieczeń, jak i routingu siłowego dla takich rzeczy jak zapory aplikacji.

- *Jumpbox*: Aby uzyskać bezpieczniejszy dostęp, administratorzy nie powinni bezpośrednio łączyć się z usługą aplikacji lub bazą danych. Skrzynka szybkiego dostępu jest używana jako nośnik między komputerem administratora a zasobami platformy Azure.
![Zrzut ekranu przedstawiający stronę topologii jumpbox](./media/oracle-design/jumpbox.png)

    Komputer administrator powinien oferować dostęp z ograniczeniami IP tylko do jumpboxa. Jumpbox powinien mieć dostęp do aplikacji i bazy danych.

- *Sieć prywatna* (podsieci): Zaleca się, że masz usługę aplikacji i bazę danych w oddzielnych podsieciach, dzięki czemu lepszą kontrolę można ustawić za pomocą zasad sieciowej grupy.


## <a name="additional-reading"></a>Dodatkowa lektura

- [Konfigurowanie programu Oracle ASM](configure-oracle-asm.md)
- [Konfigurowanie środowiska Oracle Data Guard](configure-oracle-dataguard.md)
- [Konfigurowanie Złotej Bramy Oracle](configure-oracle-golden-gate.md)
- [Tworzenie kopii zapasowych i odzyskiwanie kopii zapasowych i odzyskiwania oracle](oracle-backup-recovery.md)

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Tworzenie maszyn wirtualnych o wysokiej dostępności](../../linux/create-cli-complete.md)
- [Zapoznaj się z przykładami interfejsu wiersza polecenia platformy Azure dotyczące wdrażania maszyn wirtualnych](../../linux/cli-samples.md)
