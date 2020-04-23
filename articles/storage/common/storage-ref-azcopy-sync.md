---
title: synchronizacja azcopy | Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje referencyjne dla polecenia synchronizacji azkopii.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d855019be7f357a35a26d14e68ba3d427d984e17
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086032"
---
# <a name="azcopy-sync"></a>azcopy sync

Replikuje lokalizację źródłą do lokalizacji docelowej.

## <a name="synopsis"></a>Streszczenie

Ostatnie zmodyfikowane czasy są używane do porównania. Plik jest pomijany, jeśli ostatni zmodyfikowany czas w miejscu docelowym jest nowszy.

Obsługiwane pary to:

- lokalny <-> azure blob (można użyć uwierzytelniania Sygnatury dostępu Współdzielonego lub OAuth)
- Azure Blob <-> Azure Blob (Źródło musi zawierać sygnatury dostępu Współdzielonego lub jest publicznie dostępny; uwierzytelnianie Sygnatury dostępu Współdzielonego lub OAuth może służyć do miejsca docelowego)
- Plik azure <-> pliku Azure (Źródło musi zawierać sygnatury dostępu Współdzielonego lub jest publicznie dostępny; Uwierzytelnianie SAS powinno być używane dla miejsca docelowego)

Polecenie synchronizacji różni się od polecenia copy na kilka sposobów:

1. Domyślnie flaga cykliczna jest prawdziwa i synchronizacja kopiuje wszystkie podkatalogi. Synchronizacja kopiuje pliki najwyższego poziomu w katalogu tylko wtedy, gdy flaga cykliczna jest false.
2. Podczas synchronizacji między katalogami wirtualnymi dodaj ukośnik końcowy do ścieżki (odnieś się do przykładów), jeśli istnieje obiekt blob o takiej samej nazwie jak jeden z katalogów wirtualnych.
3. Jeśli flaga "deleteDestination" jest ustawiona na true lub prompt, następnie synchronizacja usunie pliki i obiekty blob w miejscu docelowym, które nie są obecne w źródle.

## <a name="related-conceptual-articles"></a>Powiązane artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Przesyłanie danych za pomocą pamięci masowej AzCopy i Blob](storage-use-azcopy-blobs.md)
- [Przesyłanie danych za pomocą AzCopy i przechowywania plików](storage-use-azcopy-files.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z programem AzCopy](storage-use-azcopy-configure.md)

### <a name="advanced"></a>Zaawansowane

Jeśli nie określisz rozszerzenia pliku, AzCopy automatycznie wykryje typ zawartości plików podczas przekazywania z dysku lokalnego na podstawie rozszerzenia pliku lub zawartości (jeśli nie określono rozszerzenia).

Wbudowana tabela odnośnictwa jest mała, ale w systemie Unix jest rozszerzana przez pliki mime.types systemu lokalnego, jeśli są dostępne pod jedną lub kilkoma z tych nazw:

- /etc/mime.typy
- /etc/apache2/mime.types
- /etc/apache/mime.types

W systemie Windows typy MIME są wyodrębniane z rejestru.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>Przykłady

Synchronizowanie pojedynczego pliku:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

> [!NOTE]
> Obiekt docelowy *musi istnieć.* Służy `azcopy copy` do kopiowania pojedynczego pliku, który jeszcze nie istnieje w miejscu docelowym. W przeciwnym razie wystąpi `Cannot perform sync due to error: sync must happen between source and destination of the same type, e.g. either file <-> file, or directory/container <-> directory/container`następujący błąd: .

Tak samo jak powyżej, ale tym razem obliczaj również skrót MD5 zawartości pliku i zapisz go jako właściwość Content-MD5 obiektu blob:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Synchronizowanie całego katalogu wraz z jego podkatasiami (zwróć uwagę, że cykliczność jest domyślnie włączona):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

lub

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Synchronizacja tylko najpopularniejszych plików w katalogu, ale nie jego podkatarzy:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Synchronizacja podzbioru plików w katalogu (na przykład: tylko pliki jpg i pdf lub jeśli nazwa pliku to "exactName"):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

Synchronizuj cały katalog, ale wykluczaj niektóre pliki z zakresu (na przykład każdy plik, który zaczyna się od foo lub kończy się na pasku):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

Synchronizacja pojedynczego obiektu blob:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Synchronizowanie katalogu wirtualnego:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Synchronizacja katalogu wirtualnego o takiej samej nazwie jak obiekt blob (dodaj do ścieżki końcowe ukośnik w celu odróżnienia):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Synchronizowanie katalogu plików platformy Azure (tej samej składni co obiekt blob):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Jeśli flagi include/exclude są używane razem, będą analizowane tylko pliki pasujące do wzorców dołączania, ale te pasujące do wzorców wykluczeń będą zawsze ignorowane.

## <a name="options"></a>Opcje

**--block-size-mb** float Użyj tego rozmiaru bloku (określonego w MiB) podczas przekazywania do usługi Azure Storage lub pobierania z usługi Azure Storage. Wartość domyślna jest obliczana automatycznie na podstawie rozmiaru pliku. Frakcje dziesiętne są dozwolone (na przykład: 0,25).

**--check-md5** string Określa, jak ściśle skróty MD5 powinny być sprawdzane podczas pobierania. Ta opcja jest dostępna tylko podczas pobierania. Dostępne wartości to: NoCheck, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (domyślnie "FailIfDifferent"). (domyślnie "FailIfDifferent")

**Ciąg --delete-destination** określa, czy usunąć dodatkowe pliki z miejsca docelowego, które nie są obecne w źródle. Może być ustawiona na true, false lub prompt. Jeśli ustawiona jest monit, użytkownik zostanie poproszony o pytanie przed zaplanowaniem plików i obiektów blob do usunięcia. (domyślnie "false"). (domyślnie "false")

**Ciąg --exclude-attributes** (tylko windows) Wyklucz pliki, których atrybuty pasują do listy atrybutów. Na przykład: A; S; R

**Ciąg --exclude-path** Wyklucz te ścieżki podczas kopiowania. Ta opcja nie obsługuje symboli wieloznacznych (*). Sprawdza względny prefiks ścieżki(Na przykład: mójFolder;mójFolder/subDirName/file.pdf). W połączeniu z przechodzeniem do konta ścieżki nie zawierają nazwy kontenera.

**Ciąg --exclude-pattern** Wyklucz pliki, w których nazwa jest zgodna z listą wzorców. Na przykład: \*.jpg; \*.pdf;exactName

**-h, --help** pomoc dla synchronizacji

**--include-attributes** string (tylko windows) Uwzględnij tylko pliki, których atrybuty pasują do listy atrybutów. Na przykład: A; S; R

**--include-pattern** string Uwzględnij tylko pliki, w których nazwa jest zgodna z listą wzorców. Na przykład: \*.jpg; \*.pdf;exactName

**--log-level** string Zdefiniuj szczegółowość dziennika dla pliku dziennika, dostępne poziomy: INFO (wszystkie żądania i odpowiedzi), OSTRZEŻENIE (powolne odpowiedzi), ERROR (tylko żądania nieudane) i NONE (brak dzienników wyjściowych). (domyślna WARTOŚĆ INFO). (domyślnie "INFO")

**--put-md5**                     Utwórz skrót MD5 każdego pliku i zapisz skrót jako właściwość Content-MD5 docelowego obiektu blob lub pliku. (Domyślnie skrót NIE jest tworzony). Dostępne tylko podczas przesyłania.

**--rekursywny**                   True domyślnie, spójrz na podkatarzy cyklicznie podczas synchronizacji między katalogami. (domyślna wartość true). (domyślna wartość true)

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--cap-mbps uint32|Limity szybkości transferu w megabitach na sekundę. Przepływność moment po chwili może się nieznacznie różnić od limitu. Jeśli ta opcja jest ustawiona na zero lub jest pomijana, przepływność nie jest ograniczona.|
|Ciąg typu --output|Format danych wyjściowych polecenia. Do wyboru są: tekst, json. Wartością domyślną jest "tekst".|

## <a name="see-also"></a>Zobacz też

- [azcopy](storage-ref-azcopy.md)
