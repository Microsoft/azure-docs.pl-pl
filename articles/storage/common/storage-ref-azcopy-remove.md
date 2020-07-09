---
title: AzCopy Usuń | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia AzCopy Remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 05/04/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ab085b9a41120a9f56c1c2e39a89def8c3893747
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84221082"
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
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include-pattern="*.jpg;*.pdf;exactName"
```

Usuń cały katalog wirtualny, ale Wyklucz określone obiekty blob z zakresu (na przykład: każdy obiekt BLOB, który rozpoczyna się od foo lub kończąc z paskiem):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude-pattern="foo*;*bar"
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

**--Usuń ciąg migawek** domyślnie, operacja usuwania kończy się niepowodzeniem, jeśli obiekt BLOB ma migawki. Określ element "include", aby usunąć główny obiekt BLOB i wszystkie jego migawki; Alternatywnie można określić tylko wartość "tylko", aby usunąć tylko migawki, ale zachować główny obiekt BLOB.

**--exclude-Path** ciąg Wyklucz te ścieżki podczas usuwania. Ta opcja nie obsługuje symboli wieloznacznych (*). Sprawdza prefiks ścieżki względnej. Na przykład: moje folderu; folder/subDirName/file.pdf.

**--exclude-String wykluczanie** plików, w których nazwa jest zgodna z listą wzorców. Na przykład: *. jpg;*. PDF; exactname

**--Wymuś-if-tylko do odczytu**    Podczas usuwania pliku Azure Files lub folderu Wymuś działanie operacji usuwania, nawet jeśli istniejący obiekt ma ustawiony atrybut tylko do odczytu

**-h,--** Pomoc dotycząca usuwania

**--include-Path** ciąg Uwzględnij tylko te ścieżki podczas usuwania. Ta opcja nie obsługuje symboli wieloznacznych (*). Sprawdza prefiks ścieżki względnej. Na przykład: moje folderu; folder/subDirName/file.pdf

**--dołączany** ciąg do wzorca zawiera tylko pliki, w których nazwa jest zgodna z listą wzorców. Na przykład: *. jpg;*. PDF; exactname

**--ciąg listy plików** definiuje lokalizację pliku, który zawiera listę plików i katalogów do usunięcia. Ścieżki względne powinny być rozdzielane przez podziały wierszy, a ścieżki nie powinny być kodowane przy użyciu adresu URL.

**--ciąg poziomu dziennika** definiuje szczegółowość dziennika dla pliku dziennika. Dostępne poziomy obejmują: informacje (wszystkie żądania/odpowiedzi), ostrzeżenie (wolne odpowiedzi), błąd (tylko Nieudane żądania) i brak (bez dzienników wyjściowych). (wartość domyślna to "INFO") (wartość domyślna: "INFO")

**--rekursywnie**                Poszukaj podkatalogów cyklicznie podczas synchronizacji między katalogami.

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--Cap-MB/s UInt32|Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.|
|--ciąg typu wyjściowego|Format danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text".|
|--Zaufane — ciąg sufiksów firmy Microsoft   |Określa dodatkowe sufiksy domeny, w których mogą być wysyłane Azure Active Directory tokeny logowania.  Wartość domyślna to "*. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Wszystkie wymienione tutaj są dodawane do ustawień domyślnych. W celu zapewnienia bezpieczeństwa należy tu umieścić tylko domeny Microsoft Azure. Rozdziel wiele wpisów średnikami.|

## <a name="see-also"></a>Zobacz także

- [AzCopy](storage-ref-azcopy.md)
