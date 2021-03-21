---
title: Pobieranie danych z pamięci podręcznej platformy Azure HPC — kopia ręczna
description: Jak używać poleceń CP do przenoszenia danych do docelowego magazynu obiektów BLOB w pamięci podręcznej Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: f96a0fa264124f9d050e667b003d98579da63b77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87092341"
---
# <a name="azure-hpc-cache-data-ingest---manual-copy-method"></a>Pobieranie danych z pamięci podręcznej platformy Azure HPC — metoda kopiowania ręcznego

Ten artykuł zawiera szczegółowe instrukcje dotyczące ręcznego kopiowania danych do kontenera magazynu obiektów BLOB do użycia z pamięcią podręczną platformy Azure HPC. Używa wielowątkowych operacji równoległych do optymalizowania szybkości kopiowania.

Aby dowiedzieć się więcej na temat przenoszenia danych do magazynu obiektów BLOB w pamięci podręcznej platformy Azure HPC, przeczytaj temat [przenoszenie danych do usługi Azure Blob Storage](hpc-cache-ingest.md).

## <a name="simple-copy-example"></a>Przykład prostego kopiowania

Można ręcznie utworzyć kopię wielowątkową na kliencie, uruchamiając więcej niż jedno polecenie kopiowania jednocześnie w tle względem wstępnie zdefiniowanych zestawów plików lub ścieżek.

Polecenie Linux/UNIX ``cp`` zawiera argument, ``-p`` Aby zachować własność i mtime metadane. Dodanie tego argumentu do poniższych poleceń jest opcjonalne. (Dodanie argumentu zwiększa liczbę wywołań systemu plików wysyłanych z klienta do docelowego systemu plików na potrzeby modyfikacji metadanych).

Ten prosty przykład kopiuje jednocześnie dwa pliki:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Po wydaniu tego polecenia `jobs` polecenie wyświetli, że dwa wątki są uruchomione.

## <a name="copy-data-with-predictable-file-names"></a>Kopiowanie danych z przewidywalnymi nazwami plików

Jeśli nazwy plików są przewidywalne, można użyć wyrażeń do tworzenia równoległych wątków kopiowania.

Na przykład, jeśli katalog zawiera 1000 plików, które są numerowane sekwencyjnie od `0001` do `1000` , można użyć następujących wyrażeń, aby utworzyć 10 równoległych wątków, które każdy z nich 100 skopiuje:

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination1/ & \
cp /mnt/source/file2* /mnt/destination1/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination1/ & \
cp /mnt/source/file5* /mnt/destination1/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination1/ & \
cp /mnt/source/file8* /mnt/destination1/ & \
cp /mnt/source/file9* /mnt/destination1/
```

## <a name="copy-data-with-unstructured-file-names"></a>Kopiowanie danych z nazwami plików bez struktury

Jeśli struktura nazewnictwa plików nie jest przewidywalna, można grupować pliki według nazw katalogów.

Ten przykład zbiera wszystkie katalogi do wysłania do ``cp`` poleceń jako zadania w tle:

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

Po zebraniu plików można uruchomić polecenia kopiowania równoległego w celu rekursywnego skopiowania podkatalogów i całej zawartości:

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

## <a name="when-to-add-mount-points"></a>Kiedy należy dodać punkty instalacji

Po dodaniu wystarczającej liczby równoległych wątków do jednego docelowego punktu instalacji systemu plików będzie dostępny punkt, w którym Dodawanie kolejnych wątków nie zapewnia większej przepływności. (Przepływność będzie mierzona w plikach/s lub bajtach na sekundę, w zależności od typu danych). Lub gorsze, nadmierne wątki może czasami spowodować spadek przepływności.

W takim przypadku można dodać punkty instalacji po stronie klienta do innych adresów instalacji pamięci podręcznej platformy Azure HPC przy użyciu tej samej ścieżki instalacji systemu plików zdalnych:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

Dodanie punktów instalacji po stronie klienta umożliwia rozwidlenie dodatkowych poleceń kopiowania do dodatkowych `/mnt/destination[1-3]` punktów instalacji, co zapewnia dalsze równoległość.

Na przykład, jeśli pliki są bardzo duże, możesz zdefiniować polecenia kopiowania, aby użyć odrębnych ścieżek docelowych, a następnie wysłać więcej poleceń równolegle od klienta wykonującego kopię.

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination2/ & \
cp /mnt/source/file2* /mnt/destination3/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination2/ & \
cp /mnt/source/file5* /mnt/destination3/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination2/ & \
cp /mnt/source/file8* /mnt/destination3/ & \
```

W powyższym przykładzie wszystkie trzy docelowe punkty instalacji są wskazywane przez procesy kopiowania plików przez klienta.

## <a name="when-to-add-clients"></a>Kiedy należy dodać klientów

Na koniec po osiągnięciu możliwości klienta dodanie większej liczby wątków kopiowania lub dodatkowych punktów instalacji nie spowoduje wzrostu jakichkolwiek dodatkowych plików/s lub bajtów/s. W takiej sytuacji można wdrożyć innego klienta z tym samym zestawem punktów instalacji, na których będą uruchomione własne zestawy procesów kopiowania plików.

Przykład:

```bash
Client1: cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
Client1: cp -R /mnt/source/dir2/dir2a /mnt/destination/dir2/ &
Client1: cp -R /mnt/source/dir3/dir3a /mnt/destination/dir3/ &

Client2: cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
Client2: cp -R /mnt/source/dir2/dir2b /mnt/destination/dir2/ &
Client2: cp -R /mnt/source/dir3/dir3b /mnt/destination/dir3/ &

Client3: cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ &
Client3: cp -R /mnt/source/dir2/dir2c /mnt/destination/dir2/ &
Client3: cp -R /mnt/source/dir3/dir3c /mnt/destination/dir3/ &

Client4: cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
Client4: cp -R /mnt/source/dir2/dir2d /mnt/destination/dir2/ &
Client4: cp -R /mnt/source/dir3/dir3d /mnt/destination/dir3/ &
```

## <a name="create-file-manifests"></a>Tworzenie manifestów plików

Po zrozumieniu powyższych metod (wiele wątków kopiowania na miejsce docelowe, wielu miejsc docelowych na klienta, wielu klientów na system plików źródłowych dostępnych dla sieci) należy wziąć pod uwagę następujące zalecenia: Kompiluj manifesty plików, a następnie użyj ich z poleceniami kopiowania na wielu klientach.

W tym scenariuszu ``find`` do tworzenia manifestów plików lub katalogów służy polecenie systemu UNIX:

```bash
user@build:/mnt/source > find . -mindepth 4 -maxdepth 4 -type d
./atj5b55c53be6-01/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-01/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-01/support/trace/rolling
./atj5b55c53be6-03/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-03/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-03/support/trace/rolling
./atj5b55c53be6-02/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-02/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-02/support/trace/rolling
```

Przekieruj ten wynik do pliku: `find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

Następnie można wykonać iterację w manifeście przy użyciu poleceń BASH do zliczania plików i określania rozmiarów podkatalogów:

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l` `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
7      16K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:12:19UTC
8      83K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:17:17UTC
575    7.7M    ./atj5b5ab44b7f-02/support/cores/armada_main.2000.1531980253.gsi
33     4.4G    ./atj5b5ab44b7f-02/support/trace/rolling
281    6.6M    ./atj5b5ab44b7f-01/support/gsi/2018-07-18T00:07:03EDT
15     182K    ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-18T05:01:00UTC
244    17M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-19T01:01:01UTC
299    31M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T01:01:00UTC
256    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T21:59:41UTC_partial
889    7.7M    ./atj5b5ab44b7f-01/support/gsi/2018-07-20T21:59:41UTC
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
11     248K    ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:12:19UTC
11     88K     ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:17:17UTC
645    11M     ./atj5b5ab44b7f-01/support/cores/armada_main.2019.1531980253.gsi
33     4.0G    ./atj5b5ab44b7f-01/support/trace/rolling
244    2.1M    ./atj5b5ab44b7f-03/support/gsi/2018-07-18T00:07:03EDT
9      158K    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.3M    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T01:01:00UTC
131    12M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    8.4M    ./atj5b5ab44b7f-03/support/gsi/2018-07-20T21:59:41UTC
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_hpccache_catchup
7      159K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:12:19UTC
7      157K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:17:17UTC
576    12M     ./atj5b5ab44b7f-03/support/cores/armada_main.2013.1531980253.gsi
33     2.8G    ./atj5b5ab44b7f-03/support/trace/rolling
```

Na koniec należy porównać do klientów rzeczywiste polecenia kopiowania plików.

Jeśli masz czterech klientów, użyj tego polecenia:

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

Jeśli masz pięć klientów, użyj podobnej do tego:

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

I przez sześć.... Ekstrapolacja w razie konieczności.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

Otrzymasz *n* pliki wynikowe, po jednym dla każdego z *N* klientów, którzy mają nazwy ścieżek do katalogów o poziomie do czterech uzyskanych jako część danych wyjściowych `find` polecenia.

Użyj każdego pliku do skompilowania polecenia COPY:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

Powyższe dane będą zawierać *N* plików, z których każda zawiera polecenie copy w poszczególnych wierszach, które można uruchomić jako skrypt bash na kliencie.

Celem jest jednoczesne uruchamianie wielu wątków tych skryptów na kliencie równolegle na wielu klientach.
