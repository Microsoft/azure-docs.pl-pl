---
title: Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy za pomocą usługi Azure Storage | Microsoft Docs
description: Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy za pomocą usługi Azure Storage. Zmień lokalizację lub usunięcie planu i plików dziennika. Zmień domyślny poziom dziennika.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 23c62562299768afb5f5d87bbcf4f7b19b3235ce
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97897862"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy

AzCopy to narzędzie wiersza polecenia, przy użyciu którego można kopiować obiekty blob lub pliki do lub z konta magazynu. W tym artykule zawarto informacje, które pomogą Ci wykonać zaawansowane zadania konfiguracji oraz rozwiązać problemy związane z używaniem narzędzia AzCopy.

> [!NOTE]
> Jeśli szukasz zawartości ułatwiającej rozpoczęcie pracy z usługą AzCopy, zobacz dowolny z następujących artykułów:
> - [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
> - [Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB](storage-use-azcopy-blobs.md)
> - [Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)
> - [Transferowanie danych za pomocą zasobników AzCopy i Amazon S3](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Konfigurowanie ustawień serwera proxy

Aby skonfigurować ustawienia serwera proxy dla AzCopy, należy ustawić `HTTPS_PROXY` zmienną środowiskową. Jeśli uruchomisz narzędzie AzCopy w systemie Windows, narzędzie AzCopy automatycznie wykryje ustawienia serwera proxy, dlatego w systemie Windows nie trzeba używać tego ustawienia. Jeśli zdecydujesz się użyć tego ustawienia w systemie Windows, przesłoni ono automatyczne wykrywanie.

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | W wierszu polecenia Użyj: `set HTTPS_PROXY=<proxy IP>:<proxy port>`<br> W programie PowerShell Użyj: `$env:HTTPS_PROXY="<proxy IP>:<proxy port>"`|
| **Linux** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |
| **macOS** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |

Obecnie AzCopy nie obsługuje serwerów proxy, które wymagają uwierzytelniania przy użyciu protokołu NTLM lub Kerberos.

### <a name="bypassing-a-proxy"></a>Pomijanie serwera proxy ###

Jeśli uruchamiasz program AzCopy w systemie Windows i chcesz powiedzieć, że w ogóle _nie ma żadnego_ serwera proxy (zamiast wykrywać Autowykrywanie ustawień), Użyj tych poleceń. Przy użyciu tych ustawień AzCopy nie będzie odszukać ani próbować użyć żadnego serwera proxy.

| System operacyjny | Środowisko | Polecenia  |
|--------|-----------|----------|
| **Windows** | Wiersz polecenia (CMD) | `set HTTPS_PROXY=dummy.invalid` <br>`set NO_PROXY=*`|
| **Windows** | PowerShell | `$env:HTTPS_PROXY="dummy.invalid"` <br>`$env:NO_PROXY="*"`<br>|

W innych systemach operacyjnych, należy po prostu pozostawić zmienną HTTPS_PROXY tak, aby nie był używany żaden serwer proxy.

## <a name="optimize-performance"></a>Optymalizowanie wydajności

Możesz przeprowadzić test wydajności, a następnie użyć poleceń i zmiennych środowiskowych, aby znaleźć optymalną kompromis między wydajnością i użyciem zasobów.

Ta sekcja ułatwia wykonywanie następujących zadań optymalizacji:

> [!div class="checklist"]
> * Uruchamianie testów porównawczych
> * Optymalizowanie przepływności
> * Optymalizuj użycie pamięci 
> * Optymalizowanie synchronizacji plików

### <a name="run-benchmark-tests"></a>Uruchamianie testów porównawczych

Test testu porównawczego wydajności dla określonych kontenerów obiektów blob lub udziałów plików można uruchomić w celu wyświetlenia ogólnych statystyk wydajności i problemów z wąskimi gardłami wydajności. Test można uruchomić, przekazując lub pobierając wygenerowane dane testowe. 

Użyj poniższego polecenia, aby uruchomić test porównawczy wydajności.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy benchmark 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **Przykład** | `azcopy benchmark 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> Ten przykład obejmuje argumenty ścieżki z pojedynczym cudzysłowem (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

To polecenie uruchamia wzorzec wydajności przez przekazywanie danych testowych do określonego miejsca docelowego. Dane testowe są generowane w pamięci, przekazane do miejsca docelowego, a następnie usuwane z lokalizacji docelowej po zakończeniu testu. Można określić, ile plików ma być generowanych i jakie rozmiary mają być używane przez opcjonalne parametry polecenia.

Jeśli wolisz uruchomić ten test przez pobranie danych, ustaw `mode` parametr na `download` . Aby uzyskać szczegółowe dokumenty referencyjne, zobacz [azcopyal](storage-ref-azcopy-bench.md). 

### <a name="optimize-throughput"></a>Optymalizowanie przepływności

Możesz użyć `cap-mbps` flagi w poleceniach, aby umieścić górny limit szybkości danych przepływności. Na przykład następujące polecenie wznawia przepływność zadania i `10` wypada do megabitów (MB) na sekundę. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

Przepływność może ulec zmniejszeniu podczas przesyłania małych plików. Przepustowość można zwiększyć przez ustawienie `AZCOPY_CONCURRENCY_VALUE` zmiennej środowiskowej. Ta zmienna określa liczbę równoczesnych żądań, które mogą wystąpić.  

Jeśli komputer ma mniej niż 5 procesorów CPU, wartość tej zmiennej jest ustawiana na `32` . W przeciwnym razie wartość domyślna jest równa 16-krotności liczby procesorów CPU. Maksymalna wartość domyślna tej zmiennej to `3000` , ale można ręcznie ustawić tę wartość na wyższą lub niższą. 

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **macOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Użyj, `azcopy env` Aby sprawdzić bieżącą wartość tej zmiennej. Jeśli wartość jest pusta, można odczytać, która wartość jest używana, przeglądając początek dowolnego pliku dziennika AzCopy. W tym miejscu są raportowane wybrane wartości i powód, w którym została wybrana.

Przed ustawieniem tej zmiennej zalecamy uruchomienie testu porównawczego. Proces testu porównawczego zgłosi zalecaną wartość współbieżności. Alternatywnie, jeśli warunki i ładunki sieciowe różnią się, należy ustawić tę zmienną na słowo `AUTO` zamiast do określonej liczby. Spowoduje to, że AzCopy będzie zawsze uruchamiać ten sam proces dostrajania automatycznego, którego używa w testach porównawczych.

### <a name="optimize-memory-use"></a>Optymalizuj użycie pamięci

Ustaw `AZCOPY_BUFFER_GB` zmienną środowiskową, aby określić maksymalną ilość pamięci systemowej, która ma być używana podczas pobierania i przekazywania plików.
Ta wartość jest wyrażana w gigabajtach (GB).

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **macOS** | `export AZCOPY_BUFFER_GB=<value>` |

### <a name="optimize-file-synchronization"></a>Optymalizowanie synchronizacji plików

Polecenie [Sync](storage-ref-azcopy-sync.md) identyfikuje wszystkie pliki w miejscu docelowym, a następnie porównuje nazwy plików i ostatnio modyfikowane sygnatury czasowe przed rozpoczęciem operacji synchronizacji. W przypadku dużej liczby plików można poprawić wydajność, eliminując to przetwarzanie z góry. 

Aby to osiągnąć, zamiast tego użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) i ustaw `--overwrite` flagę na `ifSourceNewer` . AzCopy będzie porównywać pliki, gdy są one kopiowane bez wykonywania żadnych skanów i porównań z góry. Zapewnia to krawędź wydajności w przypadkach, gdy istnieje duża liczba plików do porównania.

Polecenie [copy AzCopy](storage-ref-azcopy-copy.md) nie usuwa plików z lokalizacji docelowej, dlatego jeśli chcesz usunąć pliki w miejscu docelowym, gdy nie istnieją już w źródle, użyj polecenia [Sync AzCopy](storage-ref-azcopy-sync.md) z `--delete-destination` flagą ustawioną na wartość `true` lub `prompt` . 

## <a name="troubleshoot-issues"></a>Rozwiązywanie problemów

Narzędzie AzCopy tworzy pliki dziennika i planu dla każdego zadania. Przy użyciu dzienników możesz zbadać i rozwiązać potencjalne problemy. 

Dzienniki będą zawierać stan niepowodzeń ( `UPLOADFAILED` , `COPYFAILED` , i `DOWNLOADFAILED` ), pełną ścieżkę i przyczynę niepowodzenia.

Domyślnie pliki dzienników i planów znajdują się w `%USERPROFILE%\.azcopy` katalogu w systemie Windows lub `$HOME$\.azcopy` w katalogu na komputerach Mac i Linux, ale można je zmienić w razie potrzeby.

Odpowiedni błąd nie musi być pierwszym błędem, który pojawia się w pliku. W przypadku błędów, takich jak błędy sieci, limity czasu i błędy zajętości serwera, AzCopy będzie ponawiać próbę do 20 razy, a zazwyczaj proces ponawiania prób zostanie zakończony pomyślnie.  Pierwszy błąd, który widzisz, może być niegroźny, który został pomyślnie ponowiony.  Dlatego zamiast wyszukać pierwszego błędu w pliku, poszukaj błędów, które znajdują się blisko `UPLOADFAILED` , `COPYFAILED` lub `DOWNLOADFAILED` . 

> [!IMPORTANT]
> Podczas przesyłania żądania do pomoc techniczna firmy Microsoft (lub rozwiązywania problemu związanego z jakąkolwiek osobą trzecią) należy udostępnić redagowane wersję polecenia, które chcesz wykonać. Gwarantuje to, że SYGNATURa dostępu współdzielonego nie zostanie przypadkowo udostępniona z każdy. Wersję redagowane można znaleźć na początku pliku dziennika.

### <a name="review-the-logs-for-errors"></a>Przejrzyj dzienniki pod kątem błędów

Następujące polecenie spowoduje wyświetlenie wszystkich błędów o `UPLOADFAILED` stanie z `04dc9ca9-158f-7945-5933-564021086c79` dziennika:

**Windows (program PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Wyświetlanie i wznawianie zadań

Każda operacja transferu spowoduje utworzenie zadania AzCopy. Aby wyświetlić historię zadań, użyj następującego polecenia:

```
azcopy jobs list
```

Aby wyświetlić statystyki zadań, użyj następującego polecenia:

```
azcopy jobs show <job-id>
```

Aby przefiltrować transfer według stanu, użyj następującego polecenia:

```
azcopy jobs show <job-id> --with-status=Failed
```

Użyj poniższego polecenia, aby wznowić zadanie zakończone niepowodzeniem lub anulowane. To polecenie używa jego identyfikatora wraz z tokenem SAS, ponieważ nie jest on trwały ze względów bezpieczeństwa:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> Argumenty ścieżki otaczającej, takie jak token SAS, z pojedynczym cudzysłowem (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

Po wznowieniu zadania AzCopy przegląda plik planu zadań. Plik planu zawiera listę wszystkich plików, które zostały zidentyfikowane do przetwarzania podczas pierwszego utworzenia zadania. Po wznowieniu zadania AzCopy podejmie próbę przetransferowania wszystkich plików wymienionych w pliku planu, który nie został jeszcze przeniesiony.

## <a name="change-the-location-of-the-plan-and-log-files"></a>Zmień lokalizację planu i plików dziennika

Domyślnie pliki planu i dziennika znajdują się w katalogu w `%USERPROFILE%\.azcopy` systemie Windows lub w `$HOME$\.azcopy` katalogu na komputerach Mac i Linux. Można zmienić tę lokalizację.

### <a name="change-the-location-of-plan-files"></a>Zmień lokalizację plików planu

Użyj dowolnego z tych poleceń.

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | Narzędzia`$env:AZCOPY_JOB_PLAN_LOCATION="<value>"` <br> W wierszu polecenia Użyj:: `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **macOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Użyj, `azcopy env` Aby sprawdzić bieżącą wartość tej zmiennej. Jeśli wartość jest pusta, Zaplanuj pliki są zapisywane w domyślnej lokalizacji.

### <a name="change-the-location-of-log-files"></a>Zmień lokalizację plików dziennika

Użyj dowolnego z tych poleceń.

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | Narzędzia`$env:AZCOPY_LOG_LOCATION="<value>"` <br> W wierszu polecenia Użyj:: `set AZCOPY_LOG_LOCATION=<value>`|
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **macOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Użyj, `azcopy env` Aby sprawdzić bieżącą wartość tej zmiennej. Jeśli wartość jest pusta, dzienniki są zapisywane w domyślnej lokalizacji.

## <a name="change-the-default-log-level"></a>Zmień domyślny poziom dziennika

Domyślnie poziom dziennika AzCopy jest ustawiony na `INFO` . Jeśli chcesz zmniejszyć szczegółowość dziennika w celu zaoszczędzenia miejsca na dysku, Zastąp to ustawienie przy użyciu ``--log-level`` opcji. 

Dostępne poziomy dziennika:,,,,, `NONE` `DEBUG` `INFO` `WARNING` `ERROR` `PANIC` , i `FATAL` .

## <a name="remove-plan-and-log-files"></a>Usuń pliki planu i dziennika

Jeśli chcesz usunąć wszystkie pliki planu i dziennika z komputera lokalnego, aby zaoszczędzić miejsce na dysku, użyj `azcopy jobs clean` polecenia.

Aby usunąć pliki planu i dziennika skojarzone tylko z jednym zadaniem, użyj `azcopy jobs rm <job-id>` . Zastąp `<job-id>` symbol zastępczy w tym przykładzie identyfikatorem zadania zadania.


