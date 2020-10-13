---
title: Przeniesienie danych do avere vFXT dla platformy Azure
description: Jak dodać dane do nowego woluminu magazynu do użycia z avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: 76bbe60397ebb01aed5694d933b3067f778a4c21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85505600"
---
# <a name="moving-data-to-the-vfxt-cluster---parallel-data-ingest"></a>Przeniesienie danych do klastra vFXT — pozyskiwanie danych równoległych

Po utworzeniu nowego klastra vFXT pierwsze zadanie może być przeniesienie danych na nowy wolumin magazynu na platformie Azure. Jeśli jednak zwykła Metoda przeniesienia danych jest wydawana proste polecenie kopiowania z jednego klienta, prawdopodobnie zostanie wyświetlona powolna wydajność kopiowania. Kopiowanie jednowątkowe nie jest dobrą opcją do kopiowania danych do magazynu zaplecza avere vFXT klastra.

Ponieważ avere vFXT for Azure Cluster to skalowalna pamięć podręczna z wieloma klientami, najszybszą i najbardziej wydajną metodą kopiowania danych do niej jest wiele klientów. Ta technika parallelizes pozyskiwanie plików i obiektów.

![Diagram przedstawiający wiele klientów, przenoszenie danych wielowątkowych: w lewym górnym rogu ikona lokalnego magazynu sprzętu ma wiele strzałek. Strzałki wskazują cztery komputery klienckie. Z każdego komputera klienckiego trzy strzałki wskazują na avere vFXT. Z avere vFXT, wiele strzałek wskazuje na usługi BLOB Storage.](media/avere-vfxt-parallel-ingest.png)

``cp``Polecenia lub ``copy`` , które są często używane do przesyłania danych z jednego systemu magazynu do innego, to procesy jednowątkowe, które kopiującą tylko jeden plik jednocześnie. Oznacza to, że serwer plików pobiera tylko jeden plik w czasie, który jest odpadami zasobów klastra.

W tym artykule opisano strategie tworzenia wieloskładnikowego systemu kopiowania plików wielowątkowych do przenoszenia danych do klastra vFXT avere. Objaśniono w nim koncepcje transferu plików i punkty decyzyjne, które mogą być używane do wydajnego kopiowania danych przy użyciu wielu klientów i prostych poleceń kopiowania.

Wyjaśniono również niektóre narzędzia, które mogą pomóc. ``msrsync``Narzędzie może służyć do częściowo automatyzowania procesu dzielenia zestawu danych na przedziały i używania ``rsync`` poleceń. ``parallelcp``Skrypt jest innym narzędziem, które odczytuje Katalog źródłowy i automatycznie wystawia polecenia kopiowania. Ponadto ``rsync`` Narzędzie może być używane w dwóch fazach w celu zapewnienia szybszego kopiowania, które nadal zapewnia spójność danych.

Kliknij link, aby przejść do sekcji:

* [Przykład ręcznego kopiowania](#manual-copy-example) — dokładne wyjaśnienie przy użyciu poleceń kopiowania
* [Przykład dwufazowy rsync](#use-a-two-phase-rsync-process)
* [Przykład częściowo zautomatyzowany (msrsync)](#use-the-msrsync-utility)
* [Przykład kopiowania równoległego](#use-the-parallel-copy-script)

## <a name="data-ingestor-vm-template"></a>Szablon maszyny wirtualnej pozyskiwania danych

Szablon Menedżer zasobów jest dostępny w witrynie GitHub, aby automatycznie utworzyć maszynę wirtualną z narzędziami pozyskiwania danych równoległych wymienionymi w tym artykule.

![Diagram przedstawiający wiele strzałek z magazynu obiektów blob, magazynu sprzętu i źródeł plików platformy Azure. Strzałki wskazują na "maszynę wirtualną pozyskiwania danych" i z niej wiele strzałek wskazują na avere vFXT](media/avere-vfxt-ingestor-vm.png)

Maszyna wirtualna pozyskiwania danych jest częścią samouczka, w którym nowo utworzona maszyna wirtualna instaluje klaster avere vFXT i pobiera jego skrypt Bootstrap z klastra. Aby uzyskać szczegółowe informacje [, przeczytaj maszynę wirtualną ładowania początkowego](https://github.com/Azure/Avere/blob/master/docs/data_ingestor.md) .

## <a name="strategic-planning"></a>Planowanie strategiczne

Podczas projektowania strategii w celu równoległego kopiowania danych należy zrozumieć wady dotyczące wielkości plików, liczby plików i głębokości katalogów.

* Gdy pliki są małe, Metryka jest równa plików na sekundę.
* Gdy pliki są duże (10MiBi lub większe), Metryka jest równa bajty na sekundę.

Każdy proces kopiowania ma stawkę przepływności i szybkość transferu plików, która może być mierzona przez chronometraż czasu polecenia kopiowania i współczynnik rozmiaru pliku i liczby plików. Wyjaśnienie, jak zmierzyć stawki, wykracza poza zakres tego dokumentu, ale ważne jest, aby zrozumieć, czy będziesz mieć do czynienia z małymi lub dużymi plikami.

## <a name="manual-copy-example"></a>Przykład kopiowania ręcznego

Można ręcznie utworzyć kopię wielowątkową na kliencie, uruchamiając więcej niż jedno polecenie kopiowania jednocześnie w tle względem wstępnie zdefiniowanych zestawów plików lub ścieżek.

Polecenie Linux/UNIX ``cp`` zawiera argument, ``-p`` Aby zachować własność i mtime metadane. Dodanie tego argumentu do poniższych poleceń jest opcjonalne. (Dodanie argumentu zwiększa liczbę wywołań systemu plików wysyłanych z klienta do docelowego systemu plików na potrzeby modyfikacji metadanych).

Ten prosty przykład kopiuje jednocześnie dwa pliki:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Po wydaniu tego polecenia `jobs` polecenie wyświetli, że dwa wątki są uruchomione.

### <a name="predictable-filename-structure"></a>Prognozowana struktura nazw plików

Jeśli nazwy plików są przewidywalne, można użyć wyrażeń do tworzenia równoległych wątków kopiowania.

Na przykład, jeśli katalog zawiera 1000 plików, które są numerowane sekwencyjnie od `0001` do `1000` , można użyć następujących wyrażeń, aby utworzyć dziesięć równoległych wątków, które każdy z nich 100 skopiuje:

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

### <a name="unknown-filename-structure"></a>Nieznana struktura nazwy pliku

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

### <a name="when-to-add-mount-points"></a>Kiedy należy dodać punkty instalacji

Po dodaniu wystarczającej liczby równoległych wątków do jednego docelowego punktu instalacji systemu plików będzie dostępny punkt, w którym Dodawanie kolejnych wątków nie zapewnia większej przepływności. (Przepływność będzie mierzona w plikach/s lub bajtach na sekundę, w zależności od typu danych). Lub gorsze, nadmierne wątki może czasami spowodować spadek przepływności.

W takim przypadku można dodać punkty instalacji po stronie klienta do innych adresów IP klastra vFXT przy użyciu tej samej ścieżki instalacji zdalnej systemu plików:

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

### <a name="when-to-add-clients"></a>Kiedy należy dodać klientów

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

### <a name="create-file-manifests"></a>Tworzenie manifestów plików

Po zrozumieniu powyższych metod (wiele wątków kopiowania na miejsce docelowe, wielu miejsc docelowych na klienta, wielu klientów na dostęp do sieci), należy wziąć pod uwagę następujące zalecenia: Kompiluj manifesty plików, a następnie użyj ich z poleceniami kopiowania na wielu klientach.

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
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
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
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
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
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_vfxt_catchup
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

## <a name="use-a-two-phase-rsync-process"></a>Korzystanie z dwuetapowego procesu rsync

Narzędzie standardowe nie ``rsync`` działa dobrze w przypadku wypełniania magazynu w chmurze za pomocą avere vFXT dla systemu Azure, ponieważ generuje dużą liczbę operacji tworzenia i zmiany nazw plików w celu zagwarantowania integralności danych. Można jednak bezpiecznie użyć ``--inplace`` opcji z, ``rsync`` Aby pominąć procedurę kopiowania bardziej ostrożnie po wykonaniu tej czynności z drugim przebiegiem, który sprawdza integralność plików.

Standardowa ``rsync`` operacja kopiowania tworzy plik tymczasowy i wypełnia go danymi. Jeśli transfer danych zakończy się pomyślnie, nazwa pliku tymczasowego zostanie zmieniona na oryginalną nazwę pliku. Ta metoda gwarantuje spójność, nawet jeśli pliki są dostępne podczas kopiowania. Jednak ta metoda generuje więcej operacji zapisu, co spowalnia przenoszenie plików przez pamięć podręczną.

Opcja ``--inplace`` zapisuje nowy plik bezpośrednio w jego lokalizacji końcowej. Pliki nie są gwarantowane spójność podczas przesyłania, ale nie są istotne, jeśli napełnianiu system przechowywania do użytku w przyszłości.

Druga ``rsync`` operacja służy jako kontrola spójności podczas pierwszej operacji. Ponieważ pliki zostały już skopiowane, druga faza to szybkie skanowanie, aby upewnić się, że pliki w miejscu docelowym pasują do plików w źródle. Jeśli jakiekolwiek pliki nie są zgodne, zostaną ponownie skopiowane.

Obie etapy można wydać razem w jednym z poleceń:

```bash
rsync -azh --inplace <source> <destination> && rsync -azh <source> <destination>
```

Ta metoda to prosta i bezterminowa Metoda dla zestawów danych do liczby plików, które może obsłużyć wewnętrzny Menedżer katalogów. (Zazwyczaj jest to 200 000 000 plików dla klastra 3-węzłowego, 500 000 000 plików dla klastra z sześcioma węzłami itd.)

## <a name="use-the-msrsync-utility"></a>Korzystanie z narzędzia msrsync

``msrsync``Narzędzia te można również użyć do przenoszenia danych do podstawowego pliku dla klastra avere. To narzędzie służy do optymalizowania użycia przepustowości przez uruchamianie wielu procesów równoległych ``rsync`` . Jest on dostępny w witrynie GitHub pod adresem <https://github.com/jbd/msrsync> .

``msrsync`` dzieli Katalog źródłowy na oddzielne "zasobniki", a następnie uruchamia poszczególne ``rsync`` procesy w każdym przedziale.

Testowanie wstępne przy użyciu maszyny wirtualnej z czterema rdzeniami wykazało najlepszą wydajność podczas korzystania z 64 procesów. Użyj ``msrsync`` opcji, ``-p`` Aby ustawić liczbę procesów na 64.

Można również użyć ``--inplace`` argumentu z ``msrsync`` poleceniami. W przypadku użycia tej opcji należy rozważyć uruchomienie drugiego polecenia (podobnie jak w przypadku [rsync](#use-a-two-phase-rsync-process)opisanego powyżej) w celu zapewnienia integralności danych.

``msrsync`` można zapisywać tylko na woluminach lokalnych i z nich. Źródło i miejsce docelowe muszą być dostępne jako instalacje lokalne w sieci wirtualnej klastra.

Aby ``msrsync`` wypełnić wolumin w chmurze platformy Azure za pomocą klastra avere, wykonaj następujące instrukcje:

1. Zainstaluj program ``msrsync`` i jego wymagania wstępne (rsync i Python 2,6 lub nowsze)
1. Określ łączną liczbę plików i katalogów, które mają zostać skopiowane.

   Na przykład użyj narzędzia avere ``prime.py`` z argumentami ```prime.py --directory /path/to/some/directory``` (dostępne przez pobieranie adresu URL <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py> ).

   Jeśli nie korzystasz z programu ``prime.py`` , możesz obliczyć liczbę elementów za pomocą ``find`` Narzędzia GNU w następujący sposób:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Podziel liczbę elementów na 64, aby określić liczbę elementów w procesie. Użyj tej liczby z ``-f`` opcją, aby ustawić rozmiar zasobników po uruchomieniu polecenia.

1. Wydaj ``msrsync`` polecenie, aby skopiować pliki:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   W przypadku użycia ``--inplace`` Dodaj drugie wykonanie bez opcji, aby sprawdzić, czy dane zostały prawidłowo skopiowane:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH> && msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Na przykład to polecenie jest przeznaczone do przenoszenia plików 11 000 w procesach 64 z/test/Source-Repository do/mnt/vfxt/Repository:

   ``msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository && msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository``

## <a name="use-the-parallel-copy-script"></a>Użyj skryptu kopiowania równoległego

``parallelcp``Skrypt może być również przydatny do przeniesienia danych do magazynu zaplecza klastra vFXT.

Poniższy skrypt doda plik wykonywalny `parallelcp` . (Ten skrypt jest przeznaczony dla Ubuntu; w przypadku korzystania z innej dystrybucji należy zainstalować ``parallel`` osobno).

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM
#!/bin/bash

display_usage() {
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n"
}

if [  \$# -le 1 ] ; then
    display_usage
    exit 1
fi

if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then
    display_usage
    exit 0
fi

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

### <a name="parallel-copy-example"></a>Przykład kopiowania równoległego

W tym przykładzie użyto skryptu kopiowania równoległego do kompilowania ``glibc`` przy użyciu plików źródłowych z klastra avere.
<!-- xxx what is stored where? what is 'the avere cluster mount point'? xxx -->

Pliki źródłowe są przechowywane w punkcie instalacji klastra avere, a pliki obiektów są przechowywane na lokalnym dysku twardym.

Ten skrypt używa skryptu kopiowania równoległego powyżej. Ta opcja ``-j`` jest używana z ``parallelcp`` i ``make`` do uzyskania przetwarzanie równoległe.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/node1 avere
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
