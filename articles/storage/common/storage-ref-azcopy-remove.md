---
title: AzCopy Usuń | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia AzCopy Remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: abce1acb88e920c0de7bbb6447ec9d838f10486c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "74034000"
---
# <a name="azcopy-remove"></a>azcopy remove

Usuwanie obiektów blob lub plików z konta usługi Azure Storage.

## <a name="synopsis"></a>Streszczenie

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Pokrewne artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB](storage-use-azcopy-blobs.md)
- [Transferowanie danych za pomocą AzCopy i magazynu plików](storage-use-azcopy-files.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Przykłady

Usuń pojedynczy obiekt BLOB z sygnaturą dostępu współdzielonego:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Usuń cały katalog wirtualny z sygnaturą dostępu współdzielonego:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Usuń tylko najpopularniejsze obiekty blob znajdujące się w katalogu wirtualnym, ale nie jego podkatalogach:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Usuń podzestaw obiektów BLOB w katalogu wirtualnym (na przykład: tylko pliki jpg i PDF lub nazwa obiektu BLOB to "exactname"):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

Usuń cały katalog wirtualny, ale Wyklucz określone obiekty blob z zakresu (na przykład: każdy obiekt BLOB, który rozpoczyna się od foo lub kończąc z paskiem):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

Usuń określone obiekty blob i katalogi wirtualne, umieszczając ich ścieżki względne (nie kodowane w adresie URL) w pliku:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

Usuń pojedynczy plik z konta Blob Storage, które ma hierarchiczną przestrzeń nazw (dołączanie/wykluczanie nie jest obsługiwane).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Usuń pojedynczy katalog a Blob Storage konto, które ma hierarchiczną przestrzeń nazw (nie obsługiwane/wykluczone):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Opcje

**--exclude-Path — ciąg**      Wyklucz te ścieżki podczas usuwania. Ta opcja nie obsługuje symboli wieloznacznych (*). Sprawdza prefiks ścieżki względnej. Na przykład: folder. webfolders/subDirName/plik. PDF.

**--exclude-String wykluczanie** plików, w których nazwa jest zgodna z listą wzorców. Na przykład: *. jpg;*. PDF; exactname

**-h,--** Pomoc dotycząca usuwania

**--include-Path** ciąg Uwzględnij tylko te ścieżki podczas usuwania. Ta opcja nie obsługuje symboli wieloznacznych (*). Sprawdza prefiks ścieżki względnej. Na przykład: Moja folder; folder/subDirName/plik. PDF

**--dołączany** ciąg do wzorca zawiera tylko pliki, w których nazwa jest zgodna z listą wzorców. Na przykład: *. jpg;*. PDF; exactname

**--ciąg listy plików** definiuje lokalizację pliku, który zawiera listę plików i katalogów do usunięcia. Ścieżki względne powinny być rozdzielane przez podziały wierszy, a ścieżki nie powinny być kodowane przy użyciu adresu URL.

**--ciąg poziomu dziennika** definiuje szczegółowość dziennika dla pliku dziennika. Dostępne poziomy obejmują: informacje (wszystkie żądania/odpowiedzi), ostrzeżenie (wolne odpowiedzi), błąd (tylko Nieudane żądania) i brak (bez dzienników wyjściowych). (wartość domyślna to "INFO") (wartość domyślna: "INFO")

**--rekursywnie**                Poszukaj podkatalogów cyklicznie podczas synchronizacji między katalogami.

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--Cap-MB/s UInt32|Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.|
|--ciąg typu wyjściowego|Format danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text".|

## <a name="see-also"></a>Zobacz także

- [azcopy](storage-ref-azcopy.md)
