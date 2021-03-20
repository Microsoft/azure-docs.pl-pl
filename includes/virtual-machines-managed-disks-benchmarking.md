---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/29/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 25404837d5bc66ff415be8d8670eb6650475c30f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99094672"
---
## <a name="warm-up-the-cache"></a>Rozgrzewanie pamięci podręcznej

Dysk z pamięcią podręczną hosta w trybie tylko do odczytu jest w stanie zapewnić większą liczbę operacji we/wy niż limit dysku. Aby uzyskać maksymalną wydajność odczytu z pamięci podręcznej hosta, należy najpierw wykonać rozgrzewanie pamięci podręcznej tego dysku. Zapewnia to, że odczytywanie systemu IOs, które narzędzie testuje na woluminie CacheReads, rzeczywiście trafi pamięć podręczną, a nie dysk bezpośrednio. Trafienia pamięci podręcznej powoduje zwiększenie liczby IOPS z dysku z włączoną obsługą jednej pamięci podręcznej.

> [!IMPORTANT]
> Należy rozgrzewać pamięć podręczną przed uruchomieniem testu porównawczego za każdym razem, gdy maszyna wirtualna jest ponownie uruchamiana.

## <a name="diskspd"></a>NARZĘDZIA DISKSPD

[Pobierz narzędzie DISKSP](https://github.com/Microsoft/diskspd) na maszynie wirtualnej. NARZĘDZIA DISKSPD to narzędzie, które można dostosować w celu utworzenia własnych obciążeń syntetycznych. Użyjemy tego samego Instalatora opisanego powyżej do uruchamiania testów porównawczych. Możesz zmienić specyfikacje, aby przetestować różne obciążenia.

W tym przykładzie używamy następującego zestawu parametrów linii bazowej:

- -c200G: tworzy (lub ponownie tworzy) przykładowy plik używany w teście. Można ją ustawić w bajtach, KiB, MiB, GiB lub blokach. W takim przypadku do zminimalizowania buforowania pamięci jest używany duży plik 200-GiB pliku docelowego.
- -W100: określa procent operacji, które są żądaniami zapisu (-W0 jest równoważne do 100% odczytu).
- -b4K: wskazuje rozmiar bloku w bajtach, KiB, MiB lub GiB. W takim przypadku rozmiar bloku 4K jest używany do symulowania losowego testu we/wy.
- -F4: ustawia łącznie cztery wątki.
- -r: wskazuje losowy test we/wy (zastępuje parametr-s).
- -o128: wskazuje liczbę oczekujących żądań we/wy na element docelowy na wątek. Ta wartość jest również znana jako głębokość kolejki. W tym przypadku 128 jest używany do nanaprężenia procesora CPU.
- -W7200: określa czas trwania rozgrzewania przed rozpoczęciem pomiarów.
- -D30: określa czas trwania testu, nie obejmuje rozgrzewania.
- -Sh: Wyłącz buforowanie zapisu oprogramowania i sprzętu (równoważne parametrowi-suw).

Aby zapoznać się z pełną listą parametrów, zobacz [repozytorium GitHub](https://github.com/Microsoft/diskspd/wiki/Command-line-and-parameters).

### <a name="maximum-write-iops"></a>Maksymalna liczba operacji we/wy zapisu
Używana jest wysoka głębokość kolejki 128, mały rozmiar bloku wynoszący 8 KB i cztery wątki robocze do prowadzenia operacji zapisu. Pracownicy zapisu są kierowani ruchem na woluminie "NoCacheWrites", który ma trzy dyski z pamięcią podręczną ustawioną na wartość "none".

Uruchom następujące polecenie w ciągu 30 sekund od rozgrzewania i 30 sekund pomiaru:

`diskspd -c200G -w100 -b8K -F4 -r -o128 -W30 -d30 -Sh testfile.dat`

Wyniki pokazują, że maszyna wirtualna Standard_D8ds_v4 zapewnia maksymalny limit liczby operacji we/wy zapisu wynoszący 12 800.

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-write-io-per-second.png" alt-text="W przypadku 3208642560 całkowita liczba bajtów, Maksymalna łączna liczba operacji we/wy z 391680, łącznie z 101,97 MiB/s i łącznie z elementem 13052,65 WE/na sekundę.":::

### <a name="maximum-read-iops"></a>Maksymalna liczba operacji we/wy odczytu

Używana jest wysoka głębokość kolejki 128, mały rozmiar bloku wynoszący 4 KB i cztery wątki robocze do prowadzenia operacji odczytu. Pracownicy odczytu są kierowani ruchem na woluminie "CacheReads", który ma jeden dysk z pamięcią podręczną ustawioną na wartość "ReadOnly".

Uruchom następujące polecenie przez dwie godziny rozgrzewania i 30 sekund pomiaru:

`diskspd -c200G -b4K -F4 -r -o128 -W7200 -d30 -Sh testfile.dat`

Wyniki pokazują, że maszyna wirtualna Standard_D8ds_v4 zapewnia maksymalny limit liczby operacji odczytu równy 77 000.

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-read-io-per-second.png" alt-text="W przypadku 9652785152 całkowitej liczby bajtów wprowadzono 2356637 łączną wartość we/wy, przy 306,72 całkowitej MiB/s i łączną liczbę 78521,23 we/wy na sekundę.":::

### <a name="maximum-throughput"></a>Maksymalna przepływność

Aby uzyskać maksymalną przepustowość odczytu i zapisu, można zmienić rozmiar bloku o większym rozmiarze 64 KB.
## <a name="fio"></a>FIO

FIO to popularne narzędzie do monitorowania wydajności magazynu na maszynach wirtualnych z systemem Linux. Umożliwia wybór różnych rozmiarów operacji we/wy, sekwencyjnych lub losowych odczytów i zapisów. Powoduje to zduplikowanie wątków roboczych lub procesów w celu wykonania określonych operacji we/wy. Można określić typ operacji we/wy, które każdy wątek roboczy musi wykonać przy użyciu plików zadań. Utworzyliśmy jeden plik zadania na scenariusz przedstawiony w poniższych przykładach. Możesz zmienić specyfikacje w tych plikach zadań, aby testować różne obciążenia działające w Premium Storage. W przykładach używamy Standard_D8ds_v4 z systemem **Ubuntu**. Użyj tego samego Instalatora opisanego na początku sekcji testu porównawczego i rozgrzewania pamięci podręcznej przed uruchomieniem testów porównawczych.

Przed rozpoczęciem [Pobierz FIO](https://github.com/axboe/fio) i zainstaluj go na maszynie wirtualnej.

Uruchom następujące polecenie dla Ubuntu,

```
apt-get install fio
```

Używamy czterech wątków roboczych do prowadzenia operacji zapisu i czterech wątków roboczych do prowadzenia operacji odczytu na dyskach. Pracownicy zapisu są kierowani ruchem na woluminie "nocache", który ma trzy dyski z pamięcią podręczną ustawioną na wartość "none". Pracownicy odczytu są kierowani ruchem na woluminie "readcache", który ma jeden dysk z pamięcią podręczną ustawioną na wartość "ReadOnly".

### <a name="maximum-write-iops"></a>Maksymalna liczba operacji we/wy zapisu

Utwórz plik zadania z następującymi specyfikacjami, aby uzyskać maksymalną liczbę operacji we/wy zapisu. Nadaj mu nazwę "fiowrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[writer1]
rw=randwrite
directory=/mnt/nocache
```

Zwróć uwagę na następujące kwestie, które są zgodne z wytycznymi projektowania omówionymi w poprzednich sekcjach. Te specyfikacje mają kluczowe znaczenie dla maksymalnego liczby operacji we/wy na sekundę,  

* Duża głębokość kolejki 256.  
* Mały rozmiar bloku wynoszący 4 KB.  
* Wiele wątków wykonujących losowe operacje zapisu.

Uruchom następujące polecenie, aby uruchomić test FIO przez 30 sekund,  

```
sudo fio --runtime 30 fiowrite.ini
```

Podczas przebiegu testu można sprawdzić liczbę operacji we/wy zapisu, które są dostarczane przez maszynę wirtualną i dyski w warstwie Premium. Jak pokazano w poniższym przykładzie, maszyna wirtualna Standard_D8ds_v4 zapewnia maksymalny limit liczby operacji we/wy zapisu wynoszący 12 800 operacji wejścia/wyjścia na sekundę.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-uncached-writes-1.jpg" alt-text="Liczba operacji zapisu w przypadku maszyn wirtualnych IOPS i dysków SSD Premium, które są dostarczane, pokazują, że zapisy są 13.1 w liczbie IOPS.":::

### <a name="maximum-read-iops"></a>Maksymalna liczba operacji we/wy odczytu

Utwórz plik zadania z następującymi specyfikacjami, aby uzyskać maksymalną liczbę operacji we/wy odczytu. Nadaj mu nazwę "fioread.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache
```

Zwróć uwagę na następujące kwestie, które są zgodne z wytycznymi projektowania omówionymi w poprzednich sekcjach. Te specyfikacje mają kluczowe znaczenie dla maksymalnego liczby operacji we/wy na sekundę,

* Duża głębokość kolejki 256.  
* Mały rozmiar bloku wynoszący 4 KB.  
* Wiele wątków wykonujących losowe operacje zapisu.

Uruchom następujące polecenie, aby uruchomić test FIO przez 30 sekund,

```
sudo fio --runtime 30 fioread.ini
```

Podczas przebiegu testu można zobaczyć liczbę IOPS operacji odczytu, które są dostarczane przez maszynę wirtualną i dyski w warstwie Premium. Jak pokazano w poniższym przykładzie, maszyna wirtualna Standard_D8ds_v4 dostarcza więcej niż 77 000 operacji we/wy odczytu. Jest to kombinacja dysku i wydajności pamięci podręcznej.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-cached-reads-1.jpg" alt-text="Zrzut ekranu przedstawiający liczbę operacji zapisujących IOPS maszyn wirtualnych i dysków SSD w warstwie Premium jest dostarczanych, co oznacza, że odczyty są 78.6 k.":::

### <a name="maximum-read-and-write-iops"></a>Maksymalna liczba operacji we/wy odczytu i zapisu

Utwórz plik zadania z następującymi specyfikacjami, aby uzyskać maksymalną łączną liczbę operacji we/wy odczytu i zapisu. Nadaj mu nazwę "fioreadwrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=3200
```

Zwróć uwagę na następujące kwestie, które są zgodne z wytycznymi projektowania omówionymi w poprzednich sekcjach. Te specyfikacje mają kluczowe znaczenie dla maksymalnego liczby operacji we/wy na sekundę,

* Duża głębokość kolejki 128.  
* Mały rozmiar bloku wynoszący 4 KB.  
* Wiele wątków wykonujących losowe operacje odczytu i zapisu.

Uruchom następujące polecenie, aby uruchomić test FIO przez 30 sekund,

```
sudo fio --runtime 30 fioreadwrite.ini
```

Podczas przebiegu testowego można zobaczyć liczbę operacji we/wy odczytu i zapisu, które są dostarczane przez maszynę wirtualną i dyski w warstwie Premium. Jak pokazano w poniższym przykładzie, maszyna wirtualna Standard_D8ds_v4 dostarcza ponad 90 000 operacji we/wy odczytu i zapisu. Jest to kombinacja dysku i wydajności pamięci podręcznej.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-both-1.jpg" alt-text="Połączone operacje we/wy odczytu i zapisu, pokazują, że odczyty są 78.3 k, a operacje zapisu są 12,6 jednostki w liczbie IOPS.":::

### <a name="maximum-combined-throughput"></a>Maksymalna łączna przepływność

Aby uzyskać maksymalną łączną przepustowość odczytu i zapisu, użyj większego rozmiaru bloku i dużej głębokości kolejki z wieloma wątkami wykonującymi operacje odczytu i zapisu. Można użyć rozmiaru bloku 64 KB i głębokości kolejki 128.