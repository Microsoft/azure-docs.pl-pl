---
title: Projektowanie i implementowanie bazy danych Oracle na platformie Azure | Microsoft Docs
description: Projektuj i Implementuj bazę danych Oracle w środowisku platformy Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dbakevlar
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: kegorman
ms.openlocfilehash: 70de7363634cee2790a40ecf056134cf002c762d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91274711"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Projektowanie i implementowanie bazy danych Oracle na platformie Azure

## <a name="assumptions"></a>Założenia

- Planujesz przeprowadzić migrację bazy danych Oracle ze środowiska lokalnego na platformę Azure.
- Masz [pakiet diagnostyczny](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) dla Oracle Database, który chcesz zmigrować
- Masz świadomość różnych metryk w raportach programu Oracle AWR.
- Masz podstawowe informacje na temat wydajności aplikacji i wykorzystania platformy.

## <a name="goals"></a>Cele

- Dowiedz się, jak zoptymalizować wdrożenie oprogramowania Oracle na platformie Azure.
- Poznaj opcje dostrajania wydajności bazy danych Oracle w środowisku platformy Azure.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>Różnice między wdrożeniem lokalnym i platformą Azure 

Poniżej przedstawiono kilka istotnych kwestii, które należy wziąć pod uwagę podczas migrowania aplikacji lokalnych na platformę Azure. 

Istotną różnicą jest to, że w implementacji platformy Azure zasoby, takie jak maszyny wirtualne, dyski i sieci wirtualne, są współużytkowane przez innych klientów. Ponadto zasoby mogą być ograniczone w zależności od wymagań. Zamiast skupić się na unikaniu niepowodzenia (MTBF), platforma Azure jest bardziej ukierunkowana na zaniechanie awarii (MTTR).

W poniższej tabeli wymieniono niektóre różnice między implementacją lokalną a implementacją bazy danych Oracle na platformie Azure.


|  | Implementacja lokalna | Implementacja platformy Azure |
| --- | --- | --- |
| **Sieć** |LAN/WAN  |SDN (sieć zdefiniowana przez oprogramowanie)|
| **Grupa zabezpieczeń** |Narzędzia ograniczeń adresów IP/portów |[Sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń)](https://azure.microsoft.com/blog/network-security-groups) |
| **Odporności** |MTBF (średni czas między niepowodzeńmi) |MTTR (średni czas odzyskiwania)|
| **Planowana konserwacja** |Poprawki/uaktualnienia|[Zestawy dostępności](../../windows/infrastructure-example.md) (poprawki/uaktualnienia zarządzane przez platformę Azure) |
| **Zasób** |Dedykowane  |Współużytkowane z innymi klientami|
| **Regiony** |Centra danych |[Pary regionów](../../regions.md#region-pairs)|
| **Storage** |SAN/dyski fizyczne |[Magazyn zarządzany przez platformę Azure](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
| **Skalowanie** |Skalowanie w pionie |Skalowanie w poziomie|


### <a name="requirements"></a>Wymagania

- Określ rozmiar bazy danych i szybkość wzrostu.
- Określ wymagania IOPS, które można oszacować na podstawie raportów Oracle AWR lub innych narzędzi do monitorowania sieci.

## <a name="configuration-options"></a>Opcje konfiguracji

Istnieją cztery potencjalne obszary, które można dostosować w celu zwiększenia wydajności w środowisku platformy Azure:

- Rozmiar maszyny wirtualnej
- Przepływność sieci
- Typy i konfiguracje dysków
- Ustawienia pamięci podręcznej dysku

### <a name="generate-an-awr-report"></a>Generowanie raportu AWR

Jeśli masz istniejącą bazę danych Oracle i planujesz migrację do platformy Azure, masz kilka opcji. Jeśli masz [pakiet diagnostyczny](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) dla wystąpień programu Oracle, możesz uruchomić raport Oracle AWR, aby uzyskać metryki (IOPS, MB/s, gibs itd.). Następnie wybierz maszynę wirtualną na podstawie zebranych metryk. Możesz też skontaktować się z zespołem infrastruktury, aby uzyskać podobne informacje.

Możesz rozważyć uruchomienie raportu AWR w ramach obciążeń zwykłych i szczytowych, aby można było porównać. Na podstawie tych raportów można zmienić rozmiar maszyn wirtualnych na podstawie średniego obciążenia lub maksymalnego obciążenia.

Poniżej przedstawiono przykład sposobu generowania raportu AWR (generowanie raportów AWR przy użyciu programu Oracle Enterprise Manager, jeśli aktualna instalacja jest taka):

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Kluczowe metryki

Poniżej przedstawiono metryki, które można uzyskać z raportu AWR:

- Łączna liczba rdzeni
- Szybkość zegara procesora
- Całkowita ilość pamięci w GB
- Wykorzystanie procesora
- Szczytowy współczynnik transferu danych
- Częstotliwość zmian we/wy (odczyt i zapis)
- Częstotliwość ponownego rejestrowania (MB/s)
- Przepływność sieci
- Częstotliwość opóźnień sieci (niska/wysoka)
- Rozmiar bazy danych w GB
- Bajty odebrane za pośrednictwem programu SQL * NET od/do klienta

### <a name="virtual-machine-size"></a>Rozmiar maszyny wirtualnej

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. oszacowanie rozmiaru maszyny wirtualnej na podstawie użycia procesora CPU, pamięci i operacji we/wy z raportu AWR

Jedną z najważniejszych zdarzeń na pierwszym planie, które wskazują, gdzie znajdują się wąskie gardła systemu.

Na przykład na poniższym diagramie synchronizacja pliku dziennika znajduje się u góry. Wskazuje liczbę czekań, które są wymagane, zanim LGWR zapisze bufor dziennika w pliku dziennika ponownego wykonywania. Te wyniki wskazują, że lepsze przeprowadzenie magazynu lub dysków jest wymagane. Ponadto diagram przedstawia liczbę procesorów CPU (rdzenie) i ilość pamięci.

![Zrzut ekranu strony raportu AWR](./media/oracle-design/cpu_memory_info.png)

Na poniższym diagramie przedstawiono łączną liczbę operacji we/wy odczytu i zapisu. Wystąpiło 59 GB odczytu i 247,3 GB zapisu w czasie wykonywania raportu.

![Zrzut ekranu strony raportu AWR](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Wybierz maszynę wirtualną

Na podstawie informacji zebranych w raporcie AWR następnym krokiem jest wybranie maszyny wirtualnej o podobnym rozmiarze, która spełnia Twoje wymagania. Listę dostępnych maszyn wirtualnych można znaleźć w artykule [zoptymalizowane pod kątem pamięci](../../sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. Dostosuj rozmiar maszyny wirtualnej przy użyciu podobnej serii maszyn wirtualnych na podstawie ACU

Po wybraniu maszyny wirtualnej należy zwrócić uwagę na ACU maszyny wirtualnej. Możesz wybrać inną maszynę wirtualną na podstawie wartości ACU, która lepiej odpowiada Twoim wymaganiom. Aby uzyskać więcej informacji, zobacz [Azure COMPUTE Unit](../../acu.md).

![Zrzut ekranu przedstawiający stronę jednostek ACU](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Przepływność sieci

Na poniższym diagramie przedstawiono relację między przepływem i liczbą operacji we/wy:

![Zrzut ekranu przedstawiający przepływność](./media/oracle-design/throughput.png)

Łączna przepływność sieci jest szacowana na podstawie następujących informacji:
- Ruch sieciowy w programie SQL *
- Liczba serwerów MB/s (strumień wychodzący, taki jak Oracle Data Guard)
- Inne czynniki, takie jak replikacja aplikacji

![Zrzut ekranu przedstawiający przepływność netto w usłudze SQL *](./media/oracle-design/sqlnet_info.png)

Na podstawie wymagań dotyczących przepustowości sieci istnieją różne typy bram do wyboru. Należą do nich: Basic, VpnGw i Azure ExpressRoute. Aby uzyskać więcej informacji, zobacz [stronę cennika usługi VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h).

**Zalecenia**

- Opóźnienie sieci jest większe w porównaniu z wdrożeniem lokalnym. Zmniejszenie liczby podróży sieci może znacznie poprawić wydajność.
- Aby zmniejszyć liczbę rund, Konsoliduj aplikacje, które mają wysokie transakcje lub aplikacje "rozmawiania" na tej samej maszynie wirtualnej.
- Użyj Virtual Machines z [szybszymi sieciami](../../../virtual-network/create-vm-accelerated-networking-cli.md) , aby zapewnić lepszą wydajność sieci.
- W przypadku niektórych dystrybucji systemu Linux Rozważ włączenie [obsługi przycinania/mapowania](../../linux/configure-lvm.md#trimunmap-support).
- Zainstaluj program [Oracle Enterprise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) na oddzielnej maszynie wirtualnej.
- Ogromne strony nie są domyślnie włączone w systemie Linux. Rozważ włączenie dużych stron i ustawienie `use_large_pages = ONLY` na Oracle DB. Może to pomóc zwiększyć wydajność. Więcej informacji można znaleźć [tutaj](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390).

### <a name="disk-types-and-configurations"></a>Typy i konfiguracje dysków

- *Domyślne dyski systemu operacyjnego*: te typy dysków oferują trwałe dane i buforowanie. Są one zoptymalizowane pod kątem dostępu do systemu operacyjnego przy uruchamianiu i nie są przeznaczone do obciążeń transakcyjnych lub magazynu danych (analitycznych).

- *Dyski niezarządzane*: przy użyciu tych typów dysków można zarządzać kontami magazynu, które przechowują pliki wirtualnego dysku twardego (VHD), które odpowiadają DYSKOM maszyny wirtualnej. Pliki VHD są przechowywane jako stronicowe obiekty blob na kontach usługi Azure Storage.

- *Dyski zarządzane*: platforma Azure zarządza kontami magazynu używanymi dla dysków maszyny wirtualnej. Należy określić typ dysku (Premium lub standardowa) oraz wymagany rozmiar dysku. Platforma Azure tworzy dysk i zarządza nim.

- *Dyski usługi Premium Storage*: te typy dysków najlepiej nadają się do obciążeń produkcyjnych. Usługa Premium Storage obsługuje dyski maszyn wirtualnych, które mogą być dołączone do określonych maszyn wirtualnych serii rozmiarów, takich jak usługi DS, DSv2, GS i maszyny wirtualne z serii F. Dysk w warstwie Premium ma różne rozmiary i można wybrać między dyskami od 32 GB do 4 096 GB. Każdy rozmiar dysku ma własne specyfikacje wydajności. W zależności od wymagań aplikacji można dołączyć jeden lub więcej dysków do maszyny wirtualnej.

Podczas tworzenia nowego dysku zarządzanego w portalu można wybrać **Typ konta** dla typu dysku, który ma być używany. Należy pamiętać, że nie wszystkie dostępne dyski są wyświetlane w menu rozwijanym. Po wybraniu określonego rozmiaru maszyny wirtualnej w menu zostaną wyświetlone tylko dostępne jednostki SKU magazynu w warstwie Premium, które są oparte na tym rozmiarze maszyny wirtualnej.

![Zrzut ekranu przedstawiający stronę dysk zarządzany](./media/oracle-design/premium_disk01.png)

Po skonfigurowaniu magazynu na maszynie wirtualnej warto przetestować je przed utworzeniem bazy danych. Znajomość współczynnika we/wy pod względem opóźnień i przepływności może pomóc w ustaleniu, czy maszyny wirtualne obsługują oczekiwaną przepływność z obiektami docelowymi opóźnienia.

Istnieje wiele narzędzi do testowania obciążenia aplikacji, takich jak Oracle Orion, Sysbench i FIO.

Uruchom test obciążenia ponownie po wdrożeniu bazy danych Oracle. Zacznij od zwykłych i szczytowych obciążeń, a wyniki przedstawiają linię bazową środowiska.

Może być bardziej ważne, aby rozmiar magazynu był większy od szybkości operacji we/wy, a nie rozmiaru magazynu. Jeśli na przykład wymagana liczba operacji we/wy to 5 000, ale potrzebna jest tylko 200 GB, można nadal uzyskać dysk klasy P30 Premium nawet wtedy, gdy ma on więcej niż 200 GB miejsca w magazynie.

Stawkę IOPS można uzyskać z raportu AWR. Jest ona określana przez dziennik ponownego wykonywania, odczyty fizyczne i szybkość zapisywania.

![Zrzut ekranu strony raportu AWR](./media/oracle-design/awr_report.png)

Na przykład rozmiar ponawiania to 12 200 000 bajtów na sekundę, co jest równe 11,63 MB/s.
Liczba operacji we/wy to 12 200 000/2 358 = 5 174.

Po wybraniu jasnego obrazu wymagań we/wy możesz wybrać kombinację dysków, które najlepiej pasują do tych wymagań.

**Zalecenia**

- W przypadku obszaru tabel danych rozłożenie obciążenia we/wy na kilka dysków przy użyciu zarządzanego magazynu lub zestawu Oracle ASM.
- Ponieważ rozmiar bloku we/wy zwiększa się w przypadku operacji wymagających intensywnego odczytu i intensywnego zapisu, Dodaj więcej dysków danych.
- Zwiększ rozmiar bloku dla dużych procesów sekwencyjnych.
- Używaj kompresji danych, aby zmniejszyć liczbę operacji we/wy (dla danych i indeksów).
- Rozdziel dzienniki ponownego wykonywania, system i temp, a następnie Cofnij TS na oddzielnych dyskach danych.
- Nie umieszczaj żadnych plików aplikacji na domyślnych dyskach systemu operacyjnego (/dev/SDA). Te dyski nie są zoptymalizowane pod kątem krótkich czasów rozruchu maszyn wirtualnych i mogą nie zapewniać dobrej wydajności aplikacji.
- W przypadku korzystania z maszyn wirtualnych serii M w usłudze Premium Storage Włącz [Akcelerator zapisu](../../how-to-enable-write-accelerator.md) na dysku dzienników ponownego wykonywania.

### <a name="disk-cache-settings"></a>Ustawienia pamięci podręcznej dysku

Istnieją trzy opcje buforowania hosta:

- *Tylko do odczytu*: wszystkie żądania są buforowane na potrzeby przyszłych operacji odczytu. Wszystkie zapisy są utrwalane bezpośrednio w usłudze Azure Blob Storage.

- *ReadWrite*: jest to algorytm "Ready". Operacje odczytu i zapisu są buforowane na potrzeby przyszłych operacji odczytu. Operacje zapisu inne niż Write-through są najpierw utrwalane w lokalnej pamięci podręcznej. Zapewnia również najniższe opóźnienie dysku dla obciążeń lekkich. Używanie pamięci podręcznej ReadWrite z aplikacją, która nie obsługuje utrwalania wymaganych danych może spowodować utratę danych, jeśli maszyna wirtualna ulegnie awarii.

- *Brak* (wyłączone): za pomocą tej opcji można ominąć pamięć podręczną. Wszystkie dane są przekazywane na dysk i utrwalane w usłudze Azure Storage. Ta metoda zapewnia najwyższą szybkość operacji we/wy dla obciążeń intensywnie korzystających z operacji we/wy. Należy również wziąć pod uwagę "koszt transakcji".

**Zalecenia**

Aby zmaksymalizować przepływność, zalecamy rozpoczęcie od **braku** dla buforowania hosta. W przypadku Premium Storage należy pamiętać o wyłączeniu "barier" podczas instalowania systemu plików z opcjami **ReadOnly** lub **none** . Zaktualizuj plik/etc/fstab za pomocą identyfikatora UUID na dyskach.

![Zrzut ekranu przedstawiający stronę dysk zarządzany](./media/oracle-design/premium_disk02.png)

- W przypadku dysków systemu operacyjnego Użyj domyślnego buforowania **odczytu i zapisu** .
- Dla opcji SYSTEM, TEMP i UNDO **nie** można używać buforowania.
- W przypadku danych Użyj opcji **Brak** do buforowania. Ale jeśli baza danych jest w trybie tylko do odczytu lub do odczytu, należy użyć buforowania **tylko do odczytu** .

Po zapisaniu ustawienia dysku z danymi nie można zmienić ustawienia pamięci podręcznej hosta, jeśli dysk nie zostanie odinstalowany na poziomie systemu operacyjnego, a następnie ponownie zainstalowany po dokonaniu zmiany.

## <a name="security"></a>Zabezpieczenia

Po skonfigurowaniu i skonfigurowaniu środowiska platformy Azure następnym krokiem jest zabezpieczenie sieci. Poniżej przedstawiono niektóre zalecenia:

- *Zasady sieciowej grupy zabezpieczeń*: sieciowej grupy zabezpieczeń może być zdefiniowana przez podsieć lub kartę sieciową. Łatwiej jest kontrolować dostęp na poziomie podsieci zarówno w przypadku zabezpieczeń, jak i wymuszania routingu dla takich elementów jak zapory aplikacji.

- *Serwera przesiadkowego*: Aby uzyskać bardziej bezpieczny dostęp, Administratorzy nie powinni bezpośrednio łączyć się z usługą aplikacji lub bazą danych. Serwera przesiadkowego jest używany jako nośnik między komputerem administratora i zasobami platformy Azure.
![Zrzut ekranu przedstawiający stronę topologii serwera przesiadkowego](./media/oracle-design/jumpbox.png)

    Komputer administratora powinien oferować dostęp z ograniczonym dostępem do adresów IP tylko do serwera przesiadkowego. Serwera przesiadkowego powinien mieć dostęp do aplikacji i bazy danych.

- *Sieć prywatna* (podsieci): zalecamy posiadanie usługi aplikacji i bazy danych w oddzielnych podsieciach, dzięki czemu można ustawić lepszą kontrolę przy użyciu zasad sieciowej grupy zabezpieczeńymi.


## <a name="additional-reading"></a>Materiały uzupełniające

- [Konfigurowanie programu Oracle ASM](configure-oracle-asm.md)
- [Konfigurowanie środowiska Oracle Data Guard](configure-oracle-dataguard.md)
- [Konfigurowanie firmy Oracle — Złotej Bramy](configure-oracle-golden-gate.md)
- [Kopia zapasowa Oracle i odzyskiwanie](oracle-backup-recovery.md)

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Tworzenie maszyn wirtualnych o wysokiej dostępności](../../linux/create-cli-complete.md)
- [Eksplorowanie przykładów interfejsu wiersza polecenia platformy Azure wdrożenia maszyny wirtualnej](../../linux/cli-samples.md)
