---
title: azcopy remove | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia azcopy remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: bd221215d6be3c14ce1200e8bd374a97cb7608a0
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503018"
---
# <a name="azcopy-remove"></a>azcopy remove

Usuwanie obiektów blob lub plików z konta usługi Azure Storage.

## <a name="synopsis"></a>Streszczenie

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Powiązane artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Transferowanie danych za pomocą programu AzCopy i usługi Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)

## <a name="examples"></a>Przykłady

Usuwanie pojedynczego obiektu blob przy użyciu tokenu SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Usuń cały katalog wirtualny przy użyciu tokenu SAS:
 
```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Usuń tylko obiekty blob wewnątrz katalogu wirtualnego, ale nie usuwaj żadnych podkatalogów ani obiektów blob w tych podkatalogach:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Usuń podzbiór obiektów blob z katalogu wirtualnego (na przykład: usuń tylko pliki jpg i pdf lub jeśli nazwa obiektu blob to `exactName` ):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include-pattern="*.jpg;*.pdf;exactName"
```

Usuń cały katalog wirtualny, ale wyklucz niektóre obiekty blob z zakresu (na przykład: każdy obiekt blob, który rozpoczyna się od foo lub kończy się na pasku):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude-pattern="foo*;*bar"
```

Usuń określone obiekty blob i katalogi wirtualne, umieszczając ich ścieżki względne (NIE zakodowane w adresie URL) w pliku:

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

Usuń pojedynczy katalog z konta Blob Storage, które ma hierarchiczną przestrzeń nazw (dołączanie/wykluczanie nie jest obsługiwane):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Opcje

**Ciąg --delete-snapshots** Domyślnie operacja usuwania kończy się niepowodzeniem, jeśli obiekt blob zawiera migawki. Określ, aby usunąć główny obiekt blob i wszystkie jego migawki; alternatywnie określ, aby usunąć tylko migawki, `include` ale zachować główny obiekt `only` blob.

**--exclude-path** string Wyklucz te ścieżki podczas usuwania. Ta opcja nie obsługuje symboli wieloznacznych (*). Sprawdza prefiks ścieżki względnej. Na przykład: `myFolder;myFolder/subDirName/file.pdf`

**--exclude-pattern** string Wyklucza pliki, w których nazwa pasuje do listy wzorców. Na przykład: `*.jpg` ; `*.pdf` ;`exactName`

**--force-if-tylko do odczytu**   Podczas usuwania Azure Files lub folderu wymusz, aby usunięcie działało, nawet jeśli istniejący obiekt ma ustawiony atrybut tylko do odczytu.

**— pomoc w**   przypadku usuwania.

**--include-path** ciąg obejmuje tylko te ścieżki podczas usuwania. Ta opcja nie obsługuje symboli wieloznacznych (*). Sprawdza prefiks ścieżki względnej. Na przykład: `myFolder;myFolder/subDirName/file.pdf`

**--include-pattern** string Uwzględnij tylko pliki, których nazwa pasuje do listy wzorców. Na przykład: *`.jpg` ;* `.pdf` ;`exactName`

**--list-of-files** string Definiuje lokalizację pliku, który zawiera listę plików i katalogów do usunięcia. Ścieżki względne powinny być rozdzielane podziałami wierszy, a ścieżki NIE powinny być zakodowane w adresie URL. 

**--list-of-versions** string Określa plik, w którym każdy identyfikator wersji jest wymieniony w osobnym wierszu. Upewnij się, że źródło musi wskazać pojedynczy obiekt blob, a wszystkie identyfikatory wersji określone w pliku przy użyciu tej flagi muszą należeć tylko do źródłowego obiektu blob. Określone identyfikatory wersji danego obiektu blob zostaną usunięte z usługi Azure Storage. 

**--log-level** string Zdefiniuj szczegółowość dziennika dla pliku dziennika. Dostępne poziomy to: `INFO` (wszystkie żądania/odpowiedzi), (wolne odpowiedzi), (tylko żądania, które zakończyły się `WARNING` niepowodzeniem) i `ERROR` `NONE` (brak dzienników wyjściowych). (wartość `INFO` domyślna) (wartość `INFO` domyślna)

**--rekursywne**    Rekursywnie przyjrzyj się podkatalogom podczas synchronizacji między katalogami.

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--cap-mb/s float|Limituje szybkość transferu w megabitach na sekundę. Przepływność moment po chwili może się nieznacznie różnić od limitu. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie jest ograniczona.|
|--output-type string|Format danych wyjściowych polecenia. Dostępne opcje to: text, json. Wartość domyślna to "text".|
|--trusted-microsoft-suffixes string   |Określa dodatkowe sufiksy domeny, Azure Active Directory tokeny logowania mogą być wysyłane.  Wartość domyślna to '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Wszystkie wymienione w tym miejscu są dodawane do wartości domyślnej. Ze względów bezpieczeństwa należy w tym miejscu Microsoft Azure tylko domen. Oddziel wiele wpisów średnikami.|

## <a name="see-also"></a>Zobacz też

- [azcopy](storage-ref-azcopy.md)
