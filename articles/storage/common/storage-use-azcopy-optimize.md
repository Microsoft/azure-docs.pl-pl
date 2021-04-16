---
title: Optymalizowanie wydajności narzędzia AzCopy w wersji 10 za pomocą usługi Azure Storage | Microsoft Docs
description: Ten artykuł pomaga zoptymalizować wydajność narzędzia AzCopy w wersji 10 przy użyciu usługi Azure Storage.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: b60fc4b1fc20c455c2c409f544a8af16f1dbf8d1
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509135"
---
# <a name="optimize-the-performance-of-azcopy-with-azure-storage"></a>Optymalizowanie wydajności narzędzia AzCopy za pomocą usługi Azure Storage

AzCopy to narzędzie wiersza polecenia, przy użyciu którego można kopiować obiekty blob lub pliki do lub z konta magazynu. Ten artykuł ułatwia optymalizację wydajności.

> [!NOTE]
> Jeśli szukasz zawartości, która pomoże Ci rozpocząć pracę z platformą AzCopy, zobacz [Wprowadzenie do programu AzCopy](storage-use-azcopy-v10.md)

Można testować wydajność, a następnie używać poleceń i zmiennych środowiskowych w celu znalezienia optymalnego kompromisu między wydajnością a zużyciem zasobów.

## <a name="run-benchmark-tests"></a>Uruchamianie testów porównawczych

Test porównawczy wydajności można uruchomić dla określonych kontenerów obiektów blob lub udziałów plików, aby wyświetlić ogólne statystyki wydajności i zidentyfikować wąskie gardła wydajności. Test można uruchomić, przesyłając lub pobierając wygenerowane dane testowe. 

Użyj następującego polecenia, aby uruchomić test porównawczy wydajności.

**Składnia**

`azcopy benchmark 'https://<storage-account-name>.blob.core.windows.net/<container-name>'`

**Przykład**

```azcopy
azcopy benchmark 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

> [!TIP]
> W tym przykładzie argumenty ścieżki są ujęte w a cudzysłów pojedynczy (""). Używaj a cudzysłowów pojedynczych we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójny cudzysłów ("") zamiast cudzysłowów pojedynczych ("").

To polecenie uruchamia test porównawczy wydajności przez przekazanie danych testowych do określonego miejsca docelowego. Dane testowe są generowane w pamięci, przekazywane do miejsca docelowego, a następnie usuwane z miejsca docelowego po zakończeniu testu. Możesz określić liczbę plików do wygenerowania i rozmiar, przy użyciu opcjonalnych parametrów polecenia.

Jeśli wolisz uruchomić ten test, pobierając dane, ustaw `mode` parametr na `download` wartość . Aby uzyskać szczegółowe dokumenty referencyjne, zobacz [azcopy benchmark](storage-ref-azcopy-bench.md). 

## <a name="optimize-for-large-numbers-of-small-files"></a>Optymalizacja pod kątem dużej liczby małych plików

Przepływność może się zmniejszyć podczas przesyłania małych plików, szczególnie w przypadku przesyłania dużej ich liczby. Aby zmaksymalizować wydajność, zmniejsz rozmiar każdego zadania. W przypadku operacji pobierania i przekazywania zwiększ współbieżność, zmniejsz aktywność dziennika i wyłącz funkcje, które poniosą wysokie koszty wydajności.

#### <a name="reduce-the-size-of-each-job"></a>Zmniejszanie rozmiaru każdego zadania

Aby osiągnąć optymalną wydajność, upewnij się, że każde zadania przesyła mniej niż 10 milionów plików. Zadania, które przesyłają ponad 50 milionów plików, mogą działać źle, ponieważ mechanizm śledzenia zadań programu AzCopy wiąże się ze znacznym obciążeniem. Aby zmniejszyć obciążenie, rozważ podzielenie dużych zadań na mniejsze. 

Jednym ze sposobów zmniejszenia rozmiaru zadania jest ograniczenie liczby plików, których dotyczy zadanie. W tym celu można użyć parametrów polecenia. Na przykład zadanie może skopiować tylko podzbiór katalogów przy użyciu `include path` parametru jako części [polecenia azcopy copy.](storage-ref-azcopy-copy.md) 

Użyj `include-pattern` parametru , aby skopiować pliki z określonym rozszerzeniem (na przykład: `*.pdf` ). W oddzielnym zadaniu użyj `exclude-pattern` parametru , aby skopiować wszystkie pliki, które nie mają `*.pdf` rozszerzenia. Zobacz [Przekazywanie określonych plików i](storage-use-azcopy-blobs-upload.md#upload-specific-files) Pobieranie określonych obiektów [blob,](storage-use-azcopy-blobs-download.md#download-specific-blobs) aby uzyskać przykłady.

Po dzieleniu dużych zadań na mniejsze rozważ uruchamianie zadań na więcej niż jednej maszynie wirtualnej.

#### <a name="increase-concurrency"></a>Zwiększanie współbieżności

W przypadku przekazywania lub pobierania plików użyj zmiennej środowiskowej , aby zwiększyć liczbę współbieżnych żądań, które mogą wystąpić `AZCOPY_CONCURRENCY_VALUE` na komputerze. Ustaw tę zmienną tak wysoko, jak to możliwe, bez naruszania wydajności maszyny. Aby dowiedzieć się więcej na temat tej zmiennej, zobacz [sekcję Zwiększanie liczby równoczesnych żądań](#increase-the-number-of-concurrent-requests) w tym artykule.

Jeśli kopiujesz obiekty blob między kontami magazynu, rozważ ustawienie wartości zmiennej środowiskowej na wartość `AZCOPY_CONCURRENCY_VALUE` większą niż `1000` . Tę zmienną można ustawić wysoko, ponieważ program AzCopy używa interfejsów API między serwerami, dlatego dane są kopiowane bezpośrednio między serwerami magazynu i nie wykorzystują mocy obliczeniowej maszyny.  

#### <a name="decrease-the-number-of-logs-generated"></a>Zmniejszanie liczby wygenerowanych dzienników

Wydajność można poprawić, zmniejszając liczbę wpisów dziennika, które program AzCopy tworzy podczas wykonywania operacji. Domyślnie program AzCopy rejestruje wszystkie działania związane z operacją. Aby uzyskać optymalną wydajność, rozważ ustawienie parametru polecenia `log-level` kopiowania, synchronizacji lub usuwania na `ERROR` wartość . W ten sposób azCopy rejestruje tylko błędy. Domyślnie poziom dziennika wartości jest ustawiony na `INFO` wartość . 

#### <a name="turn-off-length-checking"></a>Wyłączanie sprawdzania długości 

W przypadku przekazywania lub pobierania plików rozważ ustawienie dla poleceń kopiowania `--check-length` i synchronizacji wartości `false` . Uniemożliwia to programowi AzCopy weryfikowanie długości pliku po transferze. Domyślnie program AzCopy sprawdza długość, aby upewnić się, że pliki źródłowe i docelowe są zgodne po zakończeniu transferu. Program AzCopy wykonuje to sprawdzanie po każdym transferze plików. To sprawdzenie może obniżyć wydajność, gdy zadania transferują dużą liczbę małych plików. 

#### <a name="turn-on-concurrent-local-scanning-linux"></a>Włączanie współbieżnego skanowania lokalnego (Linux)

Skanowanie plików w niektórych systemach Linux nie jest wykonywane wystarczająco szybko, aby nasycić wszystkie równoległe połączenia sieciowe. W takich przypadkach można ustawić wartość `AZCOPY_CONCURRENT_SCAN` `true` . 

## <a name="increase-the-number-of-concurrent-requests"></a>Zwiększanie liczby żądań współbieżnych

Przepływność można zwiększyć, ustawiając `AZCOPY_CONCURRENCY_VALUE` zmienną środowiskową. Ta zmienna określa liczbę równoczesnych żądań, które mogą wystąpić.  

Jeśli komputer ma mniej niż 5 procesorów CPU, wartość tej zmiennej jest ustawiona na `32` . W przeciwnym razie wartość domyślna jest równa 16-krotności liczby procesorów CPU. Maksymalna wartość domyślna tej zmiennej to , ale można ręcznie ustawić `3000` tę wartość na wyższą lub niższą. 

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **macOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Użyj funkcji `azcopy env` , aby sprawdzić bieżącą wartość tej zmiennej. Jeśli wartość jest pusta, możesz odczytać, która wartość jest używana, patrząc na początek dowolnego pliku dziennika programu AzCopy. Wybrana wartość i przyczyna jej wyboru są tam zgłaszane.

Przed ustawieniem tej zmiennej zalecamy uruchomienie testu porównawczego. Proces testu porównawczego będzie zgłaszać zalecaną wartość współbieżności. Alternatywnie, jeśli warunki i ładunki sieci różnią się, ustaw tę zmienną na wyraz zamiast `AUTO` na określoną liczbę. Spowoduje to, że program AzCopy będzie zawsze uruchamiać ten sam proces automatycznego dostrajania, który jest używany w testach porównawczych.

## <a name="limit-the-throughput-data-rate"></a>Ograniczanie przepływności danych

Możesz użyć `cap-mbps` flagi w poleceniach, aby ustawić limit szybkości danych przepływności. Na przykład następujące polecenie wznawia zadanie i limituje przepływność do `10` megabitów (Mb) na sekundę. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

## <a name="optimize-memory-use"></a>Optymalizowanie użycia pamięci

Ustaw zmienną środowiskową, aby określić maksymalną ilość pamięci systemowej, której ma używać program AzCopy do buforowania podczas pobierania `AZCOPY_BUFFER_GB` i przekazywania plików. Wyrażaj tę wartość w gigabajtach (GB).

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **macOS** | `export AZCOPY_BUFFER_GB=<value>` |

> [!NOTE]
> Śledzenie zadań zawsze wiąże się z dodatkowym obciążeniem w przypadku użycia pamięci. Ta kwota różni się w zależności od liczby transferów w zadaniu. Bufory są największym składnikiem użycia pamięci. Możesz pomóc w kontrolowaniu narzutu, używając narzędzia , aby w przybliżeniu spełnić wymagania, ale nie ma dostępnej flagi, aby ściśle określić `AZCOPY_BUFFER_GB` ogólne użycie pamięci.

## <a name="optimize-file-synchronization"></a>Optymalizowanie synchronizacji plików

Polecenie [synchronizacji](storage-ref-azcopy-sync.md) identyfikuje wszystkie pliki w miejscu docelowym, a następnie porównuje nazwy plików i znaczniki czasu ostatniej modyfikacji przed rozpoczęciem operacji synchronizacji. Jeśli masz dużą liczbę plików, możesz zwiększyć wydajność, eliminując to przetwarzanie z góry. 

Aby to zrobić, zamiast tego użyj [polecenia azcopy copy](storage-ref-azcopy-copy.md) i ustaw `--overwrite` flagę na `ifSourceNewer` . Program AzCopy będzie porównywać pliki, ponieważ są one kopiowane bez przeprowadzania jakichkolwiek z góry skanowań i porównań. Zapewnia to przewagę wydajności w przypadkach, gdy istnieje duża liczba plików do porównania.

Polecenie [azcopy copy](storage-ref-azcopy-copy.md) nie usuwa plików z miejsca docelowego, więc jeśli chcesz usunąć pliki w miejscu docelowym, gdy już nie istnieją w źródle, użyj polecenia [azcopy sync](storage-ref-azcopy-sync.md) z flagą ustawioną na wartość `--delete-destination` lub `true` `prompt` .

## <a name="see-also"></a>Zobacz też

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)