---
title: Projektowanie i implementowanie bazy danych Oracle na platformie Azure | Microsoft Docs
description: Projektuj i Implementuj bazę danych Oracle w środowisku platformy Azure.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 12/17/2020
ms.author: kegorman
ms.reviewer: tigorman
ms.openlocfilehash: 6e59d0065dfa74979bf3bbc72458bda516e3b641
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101669981"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Projektowanie i implementowanie bazy danych Oracle na platformie Azure

## <a name="assumptions"></a>Założenia

- Planujesz przeprowadzić migrację bazy danych Oracle ze środowiska lokalnego na platformę Azure.
- Masz [pakiet diagnostyczny](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) lub [repozytorium obciążeń automatycznych](https://www.oracle.com/technetwork/database/manageability/info/other-manageability/wp-self-managing-database18c-4412450.pdf) dla Oracle Database, które chcesz zmigrować
- Znasz różne metryki w programie Oracle.
- Masz podstawowe informacje na temat wydajności aplikacji i wykorzystania platformy.

## <a name="goals"></a>Cele

- Dowiedz się, jak zoptymalizować wdrożenie oprogramowania Oracle na platformie Azure.
- Poznaj opcje dostrajania wydajności bazy danych Oracle w środowisku platformy Azure.
- Wyznaczanie wyraźnych oczekiwań między granicami dostosowywania fizycznego poprzez architekturę i zalety albo logiczne dostrojenie kodu bazy danych, (SQL) i ogólnego projektu bazy danych.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>Różnice między wdrożeniem lokalnym i platformą Azure 

Poniżej przedstawiono kilka istotnych kwestii, które należy wziąć pod uwagę podczas migrowania aplikacji lokalnych na platformę Azure. 

Istotną różnicą jest to, że w implementacji platformy Azure zasoby, takie jak maszyny wirtualne, dyski i sieci wirtualne, są współużytkowane przez innych klientów. Ponadto zasoby mogą być ograniczone w zależności od wymagań. Zamiast skupić się na unikaniu niepowodzenia (MTBF), platforma Azure jest bardziej ukierunkowana na zaniechanie awarii (MTTR).

W poniższej tabeli wymieniono niektóre różnice między implementacją lokalną a implementacją bazy danych Oracle na platformie Azure.


|  | Implementacja lokalna | Implementacja platformy Azure |
| --- | --- | --- |
| **Sieć** |LAN/WAN  |SDN (sieć zdefiniowana przez oprogramowanie)|
| **Grupa zabezpieczeń** |Narzędzia ograniczeń adresów IP/portów |[Sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń)](https://azure.microsoft.com/blog/network-security-groups) |
| **Odporności** |MTBF (średni czas między niepowodzeńmi) |MTTR (średni czas odzyskiwania)|
| **Planowana konserwacja** |Poprawki/uaktualnienia|[Zestawy dostępności](/previous-versions/azure/virtual-machines/windows/infrastructure-example) (poprawki/uaktualnienia zarządzane przez platformę Azure) |
| **Zasób** |Dedykowane  |Współużytkowane z innymi klientami|
| **Regiony** |Centra danych |[Pary regionów](../../regions.md#region-pairs)|
| **Storage** |SAN/dyski fizyczne |[Magazyn zarządzany przez platformę Azure](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
| **Skalowanie** |Skalowanie w pionie |Skalowanie w poziomie|


### <a name="requirements"></a>Wymagania

- Określ rzeczywiste użycie procesora, ponieważ licencja Oracle jest licencjonowana przez rdzeń, a rozmiary vCPUu mogą być istotnym rozwiązaniem w zakresie oszczędności kosztów. 
- Określ rozmiar bazy danych, magazyn kopii zapasowych i szybkość wzrostu.
- Określ wymagania we/wy, które można oszacować na podstawie raportów Oracle Statspack i AWR lub z narzędzi monitorowania magazynu na poziomie systemu operacyjnego.

## <a name="configuration-options"></a>Opcje konfiguracji

Istnieją cztery potencjalne obszary, które można dostosować w celu zwiększenia wydajności w środowisku platformy Azure:

- Rozmiar maszyny wirtualnej
- Przepływność sieci
- Typy i konfiguracje dysków
- Ustawienia pamięci podręcznej dysku

### <a name="generate-an-awr-report"></a>Generowanie raportu AWR

Jeśli masz istniejącą bazę danych programu Oracle Enterprise Edition i planujesz migrację do platformy Azure, masz kilka opcji. Jeśli masz [pakiet diagnostyczny](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) dla wystąpień programu Oracle, możesz uruchomić raport Oracle AWR, aby uzyskać metryki (IOPS, MB/s, gibs itd.). W przypadku tych baz danych bez licencji pakietu diagnostycznego lub bazy danych w wersji Standard Edition te same ważne metryki mogą być zbierane z raportem Statspack po zebraniu migawek ręcznych.  Główną różnicą między tymi dwiema metodami raportowania jest to, że AWR jest automatycznie zbierane i zawiera więcej informacji na temat bazy danych niż opcja raportowania poprzedników Statspack.

Możesz rozważyć uruchomienie raportu AWR w ramach obciążeń zwykłych i szczytowych, aby można było porównać. Aby zebrać dokładniejsze obciążenie, należy wziąć pod uwagę rozszerzony raport okna o jeden tydzień, a także przebieg 24-godzinny i pamiętać, że AWR dostarcza średnie w ramach obliczeń w raporcie.  W przypadku migracji centrum danych zalecamy zebranie raportów dotyczących określania rozmiarów w systemach produkcyjnych i oszacowanie pozostałych kopii bazy danych używanych do testowania, testowania, programowania itp. według wartości procentowych (przeprowadzających równe produkcji, testy i opracowywanie 50% wielkości produkcji itp.)

Domyślnie repozytorium AWR zachowuje 8-dniowe dane i wykonuje migawki w odstępach czasu.  Aby uruchomić raport AWR z wiersza polecenia, można wykonać następujące czynności z poziomu terminalu:

```bash
$ sqlplus / as sysdba
SQL> @$ORACLE_HOME/rdbms/admin/awrrpt.sql;
```

### <a name="key-metrics"></a>Kluczowe metryki

Raport wyświetli monit o podanie następujących informacji:
- Typ raportu: HTML lub TEXT (HTML w 12,1 i zawiera dodatkowe informacje niż format tekstu).
- Liczba dni migawek do wyświetlenia (w ciągu jednej godziny, Raport z jednego tygodnia będzie 168 różny w identyfikatorach migawek)
- SnapshotID początku okna raportu.
- Końcowa SnapshotId dla okna raportu.
- Nazwa raportu, który ma zostać utworzony przez skrypt AWR.

W przypadku uruchamiania AWR w klastrze aplikacji w rzeczywistości (RAC) raport wiersza polecenia jest awrgrpt. SQL zamiast awrrpt. SQL.  Raport "g" utworzy raport dla wszystkich węzłów w bazie danych RAC w jednym raporcie i musi on być uruchamiany w każdym węźle z certyfikatem RAC.

Poniżej przedstawiono metryki, które można uzyskać z raportu AWR:

- Nazwa bazy danych, nazwa wystąpienia i nazwa hosta
- Wersja bazy danych, (obsługa przez firmę Oracle)
- Procesor CPU/rdzenie
- SGA/PGA, (i doradcy informujące o tym, czy nie są one niewymiarowe)
- Całkowita ilość pamięci w GB
- Procesor CPU% zajęty
- Procesory DB
- Operacje we/wy (odczyt/zapis)
- MB/s (odczyt/zapis)
- Przepływność sieci
- Częstotliwość opóźnień sieci (niska/wysoka)
- Najważniejsze zdarzenia oczekiwania 
- Ustawienia parametrów dla bazy danych
- Jest certyfikatem RAC, Exadata, przy użyciu zaawansowanych funkcji lub konfiguracji

### <a name="virtual-machine-size"></a>Rozmiar maszyny wirtualnej

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. oszacowanie rozmiaru maszyny wirtualnej na podstawie użycia procesora CPU, pamięci i operacji we/wy z raportu AWR

Jedną z najważniejszych zdarzeń na pierwszym planie, które wskazują, gdzie znajdują się wąskie gardła systemu.

Na przykład na poniższym diagramie synchronizacja pliku dziennika znajduje się u góry. Wskazuje liczbę czekań, które są wymagane, zanim LGWR zapisze bufor dziennika w pliku dziennika ponownego wykonywania. Te wyniki wskazują, że lepsze przeprowadzenie magazynu lub dysków jest wymagane. Ponadto diagram przedstawia liczbę procesorów CPU (rdzenie) i ilość pamięci.

![Zrzut ekranu pokazujący synchronizację pliku dziennika w górnej części tabeli.](./media/oracle-design/cpu_memory_info.png)

Na poniższym diagramie przedstawiono łączną liczbę operacji we/wy odczytu i zapisu. Wystąpiło 59 GB odczytu i 247,3 GB zapisu w czasie wykonywania raportu.

![Zrzut ekranu pokazujący łączną liczbę operacji we/wy operacji odczytu i zapisu.](./media/oracle-design/io_info.png)

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
- W przypadku niektórych dystrybucji systemu Linux Rozważ włączenie [obsługi przycinania/mapowania](/previous-versions/azure/virtual-machines/linux/configure-lvm#trimunmap-support).
- Zainstaluj program [Oracle Enterprise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) na oddzielnej maszynie wirtualnej.
- Ogromne strony nie są domyślnie włączone w systemie Linux. Rozważ włączenie dużych stron i ustawienie `use_large_pages = ONLY` na Oracle DB. Może to pomóc zwiększyć wydajność. Więcej informacji można znaleźć [tutaj](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390).

### <a name="disk-types-and-configurations"></a>Typy i konfiguracje dysków

- *Domyślne dyski systemu operacyjnego*: te typy dysków oferują trwałe dane i buforowanie. Są one zoptymalizowane pod kątem dostępu do systemu operacyjnego przy uruchamianiu i nie są przeznaczone do obciążeń transakcyjnych lub magazynu danych (analitycznych).

- *Dyski zarządzane*: platforma Azure zarządza kontami magazynu używanymi dla dysków maszyny wirtualnej. Należy określić typ dysku (najczęściej jest to dysk SSD w warstwie Premium dla obciążeń Oracle) i rozmiar potrzebnego dysku. Platforma Azure tworzy dysk i zarządza nim.  SSD w warstwie Premium dysk zarządzany jest dostępny tylko dla zoptymalizowanej pod kątem pamięci i specjalnie zaprojektowanej serii maszyn wirtualnych. Po wybraniu określonego rozmiaru maszyny wirtualnej w menu zostaną wyświetlone tylko dostępne jednostki SKU magazynu w warstwie Premium, które są oparte na tym rozmiarze maszyny wirtualnej.

![Zrzut ekranu przedstawiający stronę dysk zarządzany](./media/oracle-design/premium_disk01.png)

Po skonfigurowaniu magazynu na maszynie wirtualnej warto przetestować je przed utworzeniem bazy danych. Znajomość współczynnika we/wy pod względem opóźnień i przepływności może pomóc w ustaleniu, czy maszyny wirtualne obsługują oczekiwaną przepływność z obiektami docelowymi opóźnienia.

Istnieje wiele narzędzi do testowania obciążenia aplikacji, takich jak Oracle Orion, Sysbench, SLOB i FIO.

Uruchom test obciążenia ponownie po wdrożeniu bazy danych Oracle. Zacznij od zwykłych i szczytowych obciążeń, a wyniki przedstawiają linię bazową środowiska.  Być realistyczne w teście obciążenia — nie ma sensu uruchamiania obciążeń, które nie są takie same, jak to, co zostanie uruchomione na maszynie wirtualnej.

Ponieważ Oracle to baza danych intensywnie korzystających z operacji we/wy dla wielu, bardzo ważne jest, aby rozmiar magazynu był oparty na szybkości IOPS, a nie w rozmiarze magazynu. Jeśli na przykład wymagana liczba operacji we/wy to 5 000, ale potrzebna jest tylko 200 GB, można nadal uzyskać dysk klasy P30 Premium nawet wtedy, gdy ma on więcej niż 200 GB miejsca w magazynie.

Stawkę IOPS można uzyskać z raportu AWR. Jest ona określana przez dziennik ponownego wykonywania, odczyty fizyczne i szybkość zapisywania.  Zawsze sprawdzaj, czy wybrana seria maszyn wirtualnych ma możliwość obsługi zbyt dużej liczby operacji we/wy na żądanie.  Jeśli maszyna wirtualna ma niższy limit operacji we/wy niż w przypadku magazynu, wartość maksymalna zostanie ustawiona przez maszynę wirtualną.

![Zrzut ekranu strony raportu AWR](./media/oracle-design/awr_report.png)

Na przykład rozmiar ponawiania to 12 200 000 bajtów na sekundę, co jest równe 11,63 MB/s.
Liczba operacji we/wy to 12 200 000/2 358 = 5 174.

Po wybraniu jasnego obrazu wymagań we/wy możesz wybrać kombinację dysków, które najlepiej pasują do tych wymagań.

**Zalecenia**

- W przypadku obszaru tabel danych rozłożenie obciążenia we/wy na kilka dysków przy użyciu zarządzanego magazynu lub zestawu Oracle ASM.
- Użyj zaawansowanej kompresji Oracle, aby zmniejszyć liczbę operacji we/wy (dla danych i indeksów).
- Oddziel dzienniki ponownego wykonywania, tymczasowe i cofaj obszary tabel na oddzielnych dyskach danych.
- Nie umieszczaj żadnych plików aplikacji na domyślnych dyskach systemu operacyjnego (/dev/SDA). Te dyski nie są zoptymalizowane pod kątem krótkich czasów rozruchu maszyn wirtualnych i mogą nie zapewniać dobrej wydajności aplikacji.
- W przypadku korzystania z maszyn wirtualnych serii M w usłudze Premium Storage Włącz [Akcelerator zapisu](../../how-to-enable-write-accelerator.md) na dysku dzienników ponownego wykonywania.
- Rozważ przeniesienie dzienników ponownego wykonywania z dużym opóźnieniem do Ultra Disk.

### <a name="disk-cache-settings"></a>Ustawienia pamięci podręcznej dysku

Istnieją trzy opcje buforowania hosta, ale dla bazy danych Oracle zaleca się tylko buforowanie tylko do odczytu dla obciążenia bazy danych.  ReadWrite może wprowadzić znaczące luki w zabezpieczeniach pliku, gdzie celem zapisu bazy danych jest zapisanie go w postaci pliku pliku, a nie buforowanie informacji.

W przeciwieństwie do systemu plików lub aplikacji dla bazy danych, zalecenia dotyczące buforowania hosta są *tylko do odczytu*: wszystkie żądania są buforowane na potrzeby przyszłych operacji odczytu. Wszystkie zapisy są nadal zapisywane na dysku.

**Zalecenia**

Aby zmaksymalizować przepływność, zalecamy rozpoczęcie od **tylko do odczytu** pamięci podręcznej hosta, gdy jest to możliwe. W przypadku Premium Storage należy pamiętać o wyłączeniu "barier" podczas instalowania systemu plików z opcjami **tylko do odczytu** . Zaktualizuj plik/etc/fstab za pomocą identyfikatora UUID na dyskach.

![Zrzut ekranu przedstawiający stronę dysk zarządzany z opcjami ReadOnly i none.](./media/oracle-design/premium_disk02.png)

- W przypadku dysków systemu operacyjnego Użyj domyślnego buforowania **odczytu/zapisu** i Użyj dysku SSD w warstwie Premium dla maszyn wirtualnych obciążeń Oracle.  Należy również upewnić się, że wolumin używany do wymiany jest również na dysk SSD w warstwie Premium.
- Dla wszystkich DATAFILES Użyj **tylko** do buforowania. Buforowanie tylko do odczytu jest dostępne tylko dla dysku zarządzanego w warstwie Premium, P30 i nowszych.  Istnieje limit 4095GiB woluminu, który może być używany z buforowaniem tylko do odczytu.  Wszystkie większe alokacje spowodują wyłączenie buforowania hosta.

Jeśli obciążenia różnią się znacznie między dniem i wieczór, a obciążenie we/wy może je obsługiwać, SSD w warstwie Premium P1-P20 z rozbiciem może zapewnić wydajność wymaganą podczas ładowania partii w porze nocnej lub ograniczone wymagania we/wy.  

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
- [Kopia zapasowa Oracle i odzyskiwanie](./oracle-overview.md)

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Tworzenie maszyn wirtualnych o wysokiej dostępności](../../linux/create-cli-complete.md)
- [Eksplorowanie przykładów interfejsu wiersza polecenia platformy Azure wdrożenia maszyny wirtualnej](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
