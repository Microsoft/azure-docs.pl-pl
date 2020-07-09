---
title: AzCopy synchronizacji | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia Sync AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d4b43b590b147335a70877a7c3c0b07f8b818e3c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84221055"
---
# <a name="azcopy-sync"></a>azcopy sync

Replikuje lokalizację źródłową do lokalizacji docelowej.

## <a name="synopsis"></a>Streszczenie

Czas ostatniej modyfikacji jest używany do porównania. Plik zostanie pominięty, jeśli godzina ostatniej modyfikacji w miejscu docelowym jest nowsza.

Obsługiwane są następujące pary:

- < lokalnego — > obiektu blob platformy Azure (można użyć uwierzytelniania SAS lub OAuth)
- < obiektów blob platformy Azure — > obiekt blob platformy Azure (Źródło musi zawierać sygnaturę dostępu współdzielonego lub być publicznie dostępna; można używać uwierzytelniania SAS lub OAuth na potrzeby miejsca docelowego).
- Usługa Azure File < — > plik platformy Azure (Źródło musi zawierać sygnaturę dostępu współdzielonego lub być publicznie dostępna; Uwierzytelnianie SAS powinno być używane na potrzeby lokalizacji docelowej.

Polecenie Sync różni się od polecenia copy na kilka sposobów:

1. Domyślnie flaga cykliczna to true, a Sync kopiuje wszystkie podkatalogi. Synchronizacja kopiuje tylko pliki najwyższego poziomu znajdujące się w katalogu, jeśli flaga cykliczna ma wartość false.
2. Podczas synchronizowania katalogów wirtualnych Dodaj końcowy ukośnik do ścieżki (Zobacz przykłady), jeśli istnieje obiekt BLOB o takiej samej nazwie jak jeden z katalogów wirtualnych.
3. Jeśli flaga "deleteDestination" ma wartość true lub Prompt, wówczas synchronizacja spowoduje usunięcie plików i obiektów BLOB w miejscu docelowym, które nie znajdują się w źródle.

## <a name="related-conceptual-articles"></a>Pokrewne artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB](storage-use-azcopy-blobs.md)
- [Transferowanie danych za pomocą AzCopy i magazynu plików](storage-use-azcopy-files.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)

### <a name="advanced"></a>Zaawansowany

Jeśli nie określisz rozszerzenia pliku, AzCopy automatycznie wykryje typ zawartości plików podczas przekazywania z dysku lokalnego na podstawie rozszerzenia lub zawartości pliku (jeśli nie określono rozszerzenia).

Wbudowana tabela odnośnika jest mała, ale w systemie UNIX, została uzupełniona o pliki MIME. Types systemu lokalnego, jeśli są dostępne w jednej lub kilku z tych nazw:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

W systemie Windows typy MIME są wyodrębniane z rejestru.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>Przykłady

Synchronizuj pojedynczy plik:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

> [!NOTE]
> Docelowy obiekt BLOB *musi* istnieć. Służy `azcopy copy` do kopiowania pojedynczego pliku, który nie istnieje jeszcze w miejscu docelowym. W przeciwnym razie wystąpi następujący błąd: `Cannot perform sync due to error: sync must happen between source and destination of the same type, e.g. either file <-> file, or directory/container <-> directory/container` .

Analogicznie jak powyżej, ale ten czas również oblicza skrót MD5 zawartości pliku i zapisuje go jako właściwość Content-MD5 obiektu BLOB:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Zsynchronizuj cały katalog wraz z jego podkatalogami (należy zauważyć, że rekursywa jest domyślnie włączona):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

lub

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Synchronizuj tylko najważniejsze pliki znajdujące się w katalogu, ale nie jego podkatalogach:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Zsynchronizuj podzbiór plików w katalogu (na przykład: tylko pliki jpg i PDF, lub jeśli nazwa pliku to "exactname"):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

Zsynchronizuj cały katalog, ale Wyklucz określone pliki z zakresu (na przykład: każdy plik, który rozpoczyna się od foo lub kończąc z paskiem):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

Synchronizuj pojedynczy obiekt BLOB:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Synchronizuj katalog wirtualny:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Zsynchronizuj katalog wirtualny o takiej samej nazwie jak obiekt BLOB (Dodaj końcowy ukośnik do ścieżki, aby usunąć niejednoznaczność):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Zsynchronizuj katalog plików platformy Azure (w tej samej składni co obiekt BLOB):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Jeśli wszystkie flagi dołączania/wykluczania są używane razem, wyszukiwane są tylko pliki zgodne ze wzorcami dołączania, ale te zgodne ze wzorcami wykluczania byłyby zawsze ignorowane.

## <a name="options"></a>Opcje

**--rozmiar bloku-MB** zmiennoprzecinkowy Użyj tego rozmiaru bloku (określonego w MIB) podczas przekazywania do usługi Azure Storage lub pobierania z usługi Azure Storage. Wartość domyślna jest obliczana automatycznie na podstawie rozmiaru pliku. Dozwolone są ułamki dziesiętne (na przykład: 0,25).

**--check-MD5** ciąg Określa, jak ściśle skróty MD5 powinny być sprawdzane podczas pobierania. Ta opcja jest dostępna tylko podczas pobierania. Dostępne wartości to: NOCHECK, LogOnd, FailIfDifferent, FailIfDifferentOrMissing. (wartość domyślna to "FailIfDifferent"). (domyślnie "FailIfDifferent")

**--delete-Destination** ciąg definiuje, czy usunąć dodatkowe pliki z miejsca docelowego, które nie są obecne w źródle. Może być ustawiona na wartość true, false lub Prompt. Jeśli ustawiono opcję Monituj, użytkownik zostanie poproszony o podanie pytania przed zaplanowaniem plików i obiektów BLOB do usunięcia. (wartość domyślna to "false"). (wartość domyślna to "false")

**--exclude-Attribute** (tylko system Windows) wyklucza pliki, których atrybuty pasują do listy atrybutów. Na przykład: A; Wolumin ®

**--exclude-Path** ciąg Wyklucz te ścieżki podczas kopiowania. Ta opcja nie obsługuje symboli wieloznacznych (*). Sprawdza prefiks ścieżki względnej (na przykład: moje folderu; folder/subDirName/file.pdf). W przypadku użycia w połączeniu z przechodzeniem konta ścieżki nie uwzględniają nazwy kontenera.

**--exclude-String wykluczanie** plików, w których nazwa jest zgodna z listą wzorców. Na przykład: \* . jpg; \* . PDF; exactname

**-h,--** Pomoc dotycząca synchronizacji

**--include — ciąg atrybutów** (tylko system Windows) Uwzględnij tylko pliki, których atrybuty pasują do listy atrybutów. Na przykład: A; Wolumin ®

**--dołączany** ciąg do wzorca zawiera tylko pliki, w których nazwa jest zgodna z listą wzorców. Na przykład: \* . jpg; \* . PDF; exactname

**--ciąg na poziomie dziennika** definiuje szczegółowość dziennika dla pliku dziennika, dostępne poziomy: info (wszystkie żądania i odpowiedzi), ostrzeżenie (wolne odpowiedzi), błąd (tylko Nieudane żądania) i brak (bez dzienników wyjściowych). (informacje domyślne). (wartość domyślna: "INFO")

**--Put-MD5**                     Utwórz skrót MD5 każdego pliku i Zapisz skrót jako właściwość Content-MD5 docelowego obiektu BLOB lub pliku. (Domyślnie skrót nie jest tworzony). Dostępne tylko podczas przekazywania.

**--rekursywnie**                   Domyślnie wartość true powoduje cykliczne poszukiwanie katalogów podrzędnych podczas synchronizacji między katalogami. (wartość domyślna to true). (wartość domyślna to true)

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--Cap-MB/s UInt32|Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.|
|--ciąg typu wyjściowego|Format danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text".|
|--Zaufane — ciąg sufiksów firmy Microsoft   |Określa dodatkowe sufiksy domeny, w których mogą być wysyłane Azure Active Directory tokeny logowania.  Wartość domyślna to "*. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Wszystkie wymienione tutaj są dodawane do ustawień domyślnych. W celu zapewnienia bezpieczeństwa należy tu umieścić tylko domeny Microsoft Azure. Rozdziel wiele wpisów średnikami.|

## <a name="see-also"></a>Zobacz także

- [AzCopy](storage-ref-azcopy.md)
