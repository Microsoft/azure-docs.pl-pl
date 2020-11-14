---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/11/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 09af5d9af749d43f9d15f42daee6b562a877397b
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2020
ms.locfileid: "94633376"
---
*Rozgrzewanie pamięci podręcznej*  
Dysk z pamięcią podręczną hosta w trybie tylko do odczytu może dać wyższą liczbę operacji we/wy na sekundę. Aby uzyskać maksymalną wydajność odczytu z pamięci podręcznej hosta, należy najpierw wykonać rozgrzewanie pamięci podręcznej tego dysku. Zapewnia to, że odczytywanie systemu IOs, które narzędzie testuje na woluminie CacheReads, rzeczywiście trafi pamięć podręczną, a nie dysk bezpośrednio. Liczba trafień w pamięci podręcznej powoduje dodatkowe IOPS z dysku z włączoną obsługą jednej pamięci podręcznej.

> [!IMPORTANT]
> Przed uruchomieniem testu porównawczego należy przeprowadzić rozgrzewanie pamięci podręcznej, za każdym razem, gdy maszyna wirtualna jest ponownie uruchamiana.

## <a name="iometer"></a>Iometer

[Pobierz narzędzie IOMeter](http://sourceforge.net/projects/iometer/files/iometer-stable/1.1.0/iometer-1.1.0-win64.x86_64-bin.zip/download) na maszynie wirtualnej.

### <a name="test-file"></a>Plik testowy

IOMeter używa pliku testowego, który jest przechowywany w woluminie, na którym jest uruchamiany test porównawczy. Dysk IT odczytuje i zapisuje dane w tym pliku testowym w celu mierzenia liczby operacji we/wy na dysku. IOMeter tworzy ten plik testowy, jeśli nie został on podany. Utwórz plik testowy 200 GB o nazwie iobw. TST na woluminach CacheReads i NoCacheWrites.

### <a name="access-specifications"></a>Specyfikacje dostępu

Specyfikacje, rozmiar we/wy żądania,% odczyt/zapis,% losowy/sekwencyjny, są konfigurowane przy użyciu karty "specyfikacje dostępu" w IOMeter. Utwórz specyfikację dostępu dla każdego scenariusza opisanego poniżej. Utwórz specyfikacje dostępu i "Zapisz" z odpowiednią nazwą, taką jak – RandomWrites \_ 8K, RandomReads \_ 8K. Po uruchomieniu scenariusza testowego wybierz odpowiednią specyfikację.

Poniżej przedstawiono przykładowe specyfikacje dostępu dla scenariusza maksymalnego liczby operacji we/wy zapisu.  
    ![Przykładowe specyfikacje dostępu dla maksymalnej liczby operacji we/wy zapisu](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

### <a name="maximum-iops-test-specifications"></a>Wymagania dotyczące maksymalnej liczby testów IOPS

Aby zademonstrować maksymalną liczbę operacji we/wy, użyj mniejszego rozmiaru żądania. Użyj rozmiaru żądania 8K i Utwórz specyfikacje losowego zapisu i odczytu.

| Specyfikacja dostępu | Rozmiar żądania | Wybranych | Przeczytaj |
| --- | --- | --- | --- |
| RandomWrites \_ 8K |8 tys. |100 |0 |
| RandomReads \_ 8K |8 tys. |100 |100 |

### <a name="maximum-throughput-test-specifications"></a>Wymagania dotyczące maksymalnej przepływności

Aby zademonstrować maksymalną przepływność, użyj większego rozmiaru żądania. Użyj 64 K żądania rozmiaru i Utwórz specyfikacje losowego zapisu i odczytu.

| Specyfikacja dostępu | Rozmiar żądania | Wybranych | Przeczytaj |
| --- | --- | --- | --- |
| RandomWrites \_ 64 KB |64 K |100 |0 |
| RandomReads \_ 64 KB |64 K |100 |100 |

### <a name="run-the-iometer-test"></a>Uruchom test IOMeter

Wykonaj poniższe kroki, aby rozgrzać pamięć podręczną

1. Utwórz dwie specyfikacje dostępu z wartościami podanymi poniżej.

   | Nazwa | Rozmiar żądania | Wybranych | Przeczytaj |
   | --- | --- | --- | --- |
   | RandomWrites \_ MB |1 MB |100 |0 |
   | RandomReads \_ MB |1 MB |100 |100 |
1. Uruchom test IOMeter, aby zainicjować dysk pamięci podręcznej z poniższymi parametrami. Użyj trzech wątków roboczych dla woluminu docelowego i głębokości kolejki 128. Ustaw czas trwania testu na 2 godziny na karcie "Konfiguracja testu".

   | Scenariusz | Wolumin docelowy | Nazwa | Czas trwania |
   | --- | --- | --- | --- |
   | Zainicjuj dysk pamięci podręcznej |CacheReads |RandomWrites \_ MB |2 godz. |
1. Uruchom test IOMeter na potrzeby rozgrzewania dysku pamięci podręcznej przy użyciu następujących parametrów. Użyj trzech wątków roboczych dla woluminu docelowego i głębokości kolejki 128. Ustaw czas trwania testu na 2 godziny na karcie "Konfiguracja testu".

   | Scenariusz | Wolumin docelowy | Nazwa | Czas trwania |
   | --- | --- | --- | --- |
   | Rozgrzewanie dysku pamięci podręcznej |CacheReads |RandomReads \_ MB |2 godz. |

Po rozpoczęciu rozgrzewania dysku pamięci podręcznej wykonaj scenariusze testowe wymienione poniżej. Aby uruchomić test IOMeter, należy użyć co najmniej trzech wątków roboczych dla **każdego** woluminu docelowego. Dla każdego wątku roboczego wybierz wolumin docelowy, ustaw głębokość kolejki, a następnie wybierz jedną z zapisanych specyfikacji testu, jak pokazano w poniższej tabeli, aby uruchomić odpowiedni scenariusz testowy. W tabeli przedstawiono również oczekiwane wyniki operacji we/wy na sekundę i przepływność podczas uruchamiania tych testów. We wszystkich scenariuszach jest używany niewielki rozmiar we/wy wynoszący 8 KB i wysoka głębokość kolejki 128.

| Scenariusz testu | Wolumin docelowy | Nazwa | Wynik |
| --- | --- | --- | --- |
| Maksymalnie z Odczyt operacji we/wy |CacheReads |RandomWrites \_ 8K |LICZBA OPERACJI WE/WY 50 000 |
| Maksymalnie z Zapisz operacje we/wy |NoCacheWrites |RandomReads \_ 8K |LICZBA OPERACJI WE/WY 64 000 |
| Maksymalnie z Połączone operacje we/wy |CacheReads |RandomWrites \_ 8K |LICZBA OPERACJI WE/WY 100 000 |
| NoCacheWrites |RandomReads \_ 8K | &nbsp; | &nbsp; |
| Maksymalnie z Odczyt MB/s |CacheReads |RandomWrites \_ 64 KB |524 MB/s |
| Maksymalnie z Bajty zapisu/s |NoCacheWrites |RandomReads \_ 64 KB |524 MB/s |
| Połączone MB/s |CacheReads |RandomWrites \_ 64 KB |1000 MB/s |
| NoCacheWrites |RandomReads \_ 64 KB | &nbsp; | &nbsp; |

Poniżej znajdują się zrzuty ekranu wyników testu IOMeter w przypadku połączonych operacji we/wy i przepływności.

### <a name="combined-reads-and-writes-maximum-iops"></a>Połączone operacje odczytu i zapisu maksymalnej liczby IOPS

![Połączone operacje odczytu i zapisu maksymalnej liczby IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

### <a name="combined-reads-and-writes-maximum-throughput"></a>Połączone operacje odczytu i zapisu, maksymalna przepływność

![Połączone operacje odczytu i zapisu, maksymalna przepływność](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

## <a name="fio"></a>FIO

FIO to popularne narzędzie do monitorowania wydajności magazynu na maszynach wirtualnych z systemem Linux. Umożliwia wybór różnych rozmiarów operacji we/wy, sekwencyjnych lub losowych odczytów i zapisów. Powoduje to zduplikowanie wątków roboczych lub procesów w celu wykonania określonych operacji we/wy. Można określić typ operacji we/wy, które każdy wątek roboczy musi wykonać przy użyciu plików zadań. Utworzyliśmy jeden plik zadania na scenariusz przedstawiony w poniższych przykładach. Możesz zmienić specyfikacje w tych plikach zadań, aby testować różne obciążenia działające w Premium Storage. W przykładach korzystamy z standardowej maszyny wirtualnej usługi DS 14 z systemem **Ubuntu**. Użyj tego samego Instalatora opisanego na początku sekcji porównawczej i rozgrzewania pamięci podręcznej przed uruchomieniem testów porównawczych.

Przed rozpoczęciem [Pobierz FIO](https://github.com/axboe/fio) i zainstaluj go na maszynie wirtualnej.

Uruchom następujące polecenie dla Ubuntu,

```
apt-get install fio
```

Używamy czterech wątków roboczych do prowadzenia operacji zapisu i czterech wątków roboczych do prowadzenia operacji odczytu na dyskach. Pracownicy zapisu są kierowani ruchem na woluminie "nocache", który ma 10 dysków z pamięcią podręczną ustawioną na wartość "none". Pracownicy odczytu są kierowani ruchem na woluminie "readcache", który ma jeden dysk z pamięcią podręczną ustawioną na wartość "ReadOnly".

### <a name="maximum-write-iops"></a>Maksymalna liczba operacji we/wy zapisu

Utwórz plik zadania z następującymi specyfikacjami, aby uzyskać maksymalną liczbę operacji we/wy zapisu. Nadaj mu nazwę "fiowrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Zwróć uwagę na następujące kwestie, które są zgodne z wytycznymi projektowania omówionymi w poprzednich sekcjach. Te specyfikacje mają kluczowe znaczenie dla maksymalnego liczby operacji we/wy na sekundę,  

* Duża głębokość kolejki 256.  
* Mały rozmiar bloku wynoszący 8 KB.  
* Wiele wątków wykonujących losowe operacje zapisu.

Uruchom następujące polecenie, aby uruchomić test FIO przez 30 sekund,  

```
sudo fio --runtime 30 fiowrite.ini
```

Podczas przebiegu testu można sprawdzić liczbę operacji we/wy zapisu, które są dostarczane przez maszynę wirtualną i dyski w warstwie Premium. Jak pokazano w poniższym przykładzie, maszyna wirtualna DS14 zapewnia maksymalny limit liczby operacji we/wy zapisu wynoszący 50 000 operacji wejścia/wyjścia na sekundę.  
    ![Liczba dostarczanych dysków maszyn wirtualnych IOPS i Premium.](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

### <a name="maximum-read-iops"></a>Maksymalna liczba operacji we/wy odczytu

Utwórz plik zadania z następującymi specyfikacjami, aby uzyskać maksymalną liczbę operacji we/wy odczytu. Nadaj mu nazwę "fioread.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Zwróć uwagę na następujące kwestie, które są zgodne z wytycznymi projektowania omówionymi w poprzednich sekcjach. Te specyfikacje mają kluczowe znaczenie dla maksymalnego liczby operacji we/wy na sekundę,

* Duża głębokość kolejki 256.  
* Mały rozmiar bloku wynoszący 8 KB.  
* Wiele wątków wykonujących losowe operacje zapisu.

Uruchom następujące polecenie, aby uruchomić test FIO przez 30 sekund,

```
sudo fio --runtime 30 fioread.ini
```

Podczas przebiegu testu można zobaczyć liczbę IOPS operacji odczytu, które są dostarczane przez maszynę wirtualną i dyski w warstwie Premium. Jak pokazano w poniższym przykładzie, maszyna wirtualna DS14 dostarcza ponad 64 000 operacji we/wy odczytu. Jest to kombinacja dysku i wydajności pamięci podręcznej.  
    ![Zrzut ekranu przedstawiający liczbę dysków maszyn wirtualnych IOPS i Premium zapisu, które są dostarczane.](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

### <a name="maximum-read-and-write-iops"></a>Maksymalna liczba operacji we/wy odczytu i zapisu

Utwórz plik zadania z następującymi specyfikacjami, aby uzyskać maksymalną łączną liczbę operacji we/wy odczytu i zapisu. Nadaj mu nazwę "fioreadwrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Zwróć uwagę na następujące kwestie, które są zgodne z wytycznymi projektowania omówionymi w poprzednich sekcjach. Te specyfikacje mają kluczowe znaczenie dla maksymalnego liczby operacji we/wy na sekundę,

* Duża głębokość kolejki 128.  
* Mały rozmiar bloku wynoszący 4 KB.  
* Wiele wątków wykonujących losowe operacje odczytu i zapisu.

Uruchom następujące polecenie, aby uruchomić test FIO przez 30 sekund,

```
sudo fio --runtime 30 fioreadwrite.ini
```

Podczas przebiegu testowego można zobaczyć liczbę operacji we/wy odczytu i zapisu, które są dostarczane przez maszynę wirtualną i dyski w warstwie Premium. Jak pokazano w poniższym przykładzie, maszyna wirtualna DS14 dostarcza ponad 100 000 operacji we/wy odczytu i zapisu. Jest to kombinacja dysku i wydajności pamięci podręcznej.  
    ![Połączone operacje we/wy odczytu i zapisu](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

### <a name="maximum-combined-throughput"></a>Maksymalna łączna przepływność

Aby uzyskać maksymalną łączną przepustowość odczytu i zapisu, użyj większego rozmiaru bloku i dużej głębokości kolejki z wieloma wątkami wykonującymi operacje odczytu i zapisu. Można użyć rozmiaru bloku 64 KB i głębokości kolejki 128.
