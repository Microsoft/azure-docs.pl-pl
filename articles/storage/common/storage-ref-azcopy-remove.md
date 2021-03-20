---
title: AzCopy Usuń | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia AzCopy Remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d28a20a6afc78939491e56f46a32782b4c0844cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879007"
---
# <a name="azcopy-remove"></a>azcopy remove

Usuwanie obiektów blob lub plików z konta usługi Azure Storage.

## <a name="synopsis"></a>Streszczenie

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Pokrewne artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB](./storage-use-azcopy-v10.md#transfer-data)
- [Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Przykłady

Usuń pojedynczy obiekt BLOB przy użyciu tokenu sygnatury dostępu współdzielonego:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Usuń cały katalog wirtualny przy użyciu tokenu sygnatury dostępu współdzielonego:
 
```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Usuń tylko obiekty blob wewnątrz katalogu wirtualnego, ale nie usuwaj żadnych podkatalogów ani obiektów BLOB w ramach tych podkatalogów:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Usuń podzestaw obiektów BLOB w katalogu wirtualnym (na przykład: Usuń tylko pliki jpg i PDF lub nazwę obiektu BLOB `exactName` ):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include-pattern="*.jpg;*.pdf;exactName"
```

Usuń cały katalog wirtualny, ale Wyklucz określone obiekty blob z zakresu (na przykład: każdy obiekt BLOB, który rozpoczyna się od foo lub kończąc z paskiem):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude-pattern="foo*;*bar"
```

Usuń określone obiekty blob i katalogi wirtualne, umieszczając ich ścieżki względne (nie kodowane w adresie URL) w pliku:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
- file content:
    dir1/dir2
    blob1
    blob2
```
Usuń pojedynczy plik z konta Blob Storage, które ma hierarchiczną przestrzeń nazw (dołączanie/wykluczanie nie jest obsługiwane):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Usuń pojedynczy katalog z konta Blob Storage, które ma hierarchiczną przestrzeń nazw (nie jest obsługiwana/wykluczanie):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Opcje

**--Usuń ciąg migawek** domyślnie, operacja usuwania kończy się niepowodzeniem, jeśli obiekt BLOB ma migawki. Określ, `include` Aby usunąć główny obiekt BLOB i wszystkie jego migawki; Alternatywnie możesz określić, `only` Aby usunąć tylko migawki, ale zachować główny obiekt BLOB.

**--exclude-Path** ciąg Wyklucz te ścieżki podczas usuwania. Ta opcja nie obsługuje symboli wieloznacznych (*). Sprawdza prefiks ścieżki względnej. Na przykład: `myFolder;myFolder/subDirName/file.pdf`

**--exclude-String wykluczanie** plików, w których nazwa jest zgodna z listą wzorców. Na przykład: `*.jpg` ; `*.pdf``exactName`

**--Wymuś-if-tylko do odczytu**   Podczas usuwania pliku Azure Files lub folderu Wymuś działanie operacji usuwania, nawet jeśli istniejący obiekt ma ustawiony atrybut tylko do odczytu.

**--**   Pomoc dotycząca usuwania.

**--include-Path** ciąg Uwzględnij tylko te ścieżki podczas usuwania. Ta opcja nie obsługuje symboli wieloznacznych (*). Sprawdza prefiks ścieżki względnej. Na przykład: `myFolder;myFolder/subDirName/file.pdf`

**--dołączany** ciąg do wzorca zawiera tylko pliki, w których nazwa jest zgodna z listą wzorców. Na przykład: *`.jpg` ;* `.pdf``exactName`

**--ciąg listy plików** definiuje lokalizację pliku, który zawiera listę plików i katalogów do usunięcia. Ścieżki względne powinny być rozdzielane przez podziały wierszy, a ścieżki nie powinny być kodowane przy użyciu adresu URL. 

**--ciąg listy wersji** określa plik, w którym każdy identyfikator wersji jest wymieniony w osobnym wierszu. Upewnij się, że źródło musi wskazywać na pojedynczy obiekt BLOB, a wszystkie identyfikatory wersji określone w pliku używające tej flagi muszą należeć tylko do źródłowego obiektu BLOB. Określone identyfikatory wersji danego obiektu BLOB zostaną usunięte z usługi Azure Storage. 

**--ciąg poziomu dziennika** definiuje szczegółowość dziennika dla pliku dziennika. Dostępne poziomy to: `INFO` (wszystkie żądania/odpowiedzi), `WARNING` (wolne odpowiedzi), `ERROR` (tylko żądania zakończone niepowodzeniem) i `NONE` (bez dzienników wyjściowych). (ustawienie domyślne `INFO` ) (ustawienie domyślne `INFO` )

**--rekursywnie**    Poszukaj podkatalogów cyklicznie podczas synchronizacji między katalogami.

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--Cap-MB/s|Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.|
|--ciąg typu wyjściowego|Format danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text".|
|--Zaufane — ciąg sufiksów firmy Microsoft   |Określa dodatkowe sufiksy domeny, w których mogą być wysyłane Azure Active Directory tokeny logowania.  Wartość domyślna to "*. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Wszystkie wymienione tutaj są dodawane do ustawień domyślnych. W celu zapewnienia bezpieczeństwa należy tu umieścić tylko domeny Microsoft Azure. Rozdziel wiele wpisów średnikami.|

## <a name="see-also"></a>Zobacz też

- [AzCopy](storage-ref-azcopy.md)
