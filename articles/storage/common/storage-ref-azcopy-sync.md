---
title: AzCopy synchronizacji | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia Sync AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: dc3451a4b46a317dccda0e4292dcb1712b4171f0
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878311"
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
3. Jeśli `deleteDestination` flaga ma wartość true lub Prompt, wówczas synchronizacja spowoduje usunięcie plików i obiektów BLOB w miejscu docelowym, które nie znajdują się w źródle.

## <a name="related-conceptual-articles"></a>Pokrewne artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB](./storage-use-azcopy-v10.md#transfer-data)
- [Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)
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

Tak samo jak powyżej, ale również oblicza skrót MD5 zawartości pliku, a następnie Zapisz ten skrót MD5 jako właściwość Content-MD5 obiektu BLOB. 

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Zsynchronizuj cały katalog, w tym jego podkatalogi (należy pamiętać, że rekursywa jest domyślnie włączona):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

lub

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Zsynchronizuj tylko pliki znajdujące się w katalogu, ale nie w podkatalogach lub w plikach znajdujących się w podkatalogach:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Zsynchronizuj podzestaw plików w katalogu (na przykład: tylko pliki jpg i PDF lub nazwa pliku `exactName` ):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include-pattern="*.jpg;*.pdf;exactName"
```

Zsynchronizuj cały katalog, ale Wyklucz określone pliki z zakresu (na przykład: każdy plik, który rozpoczyna się od foo lub kończąc z paskiem):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude-pattern="foo*;*bar"
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

**--rozmiar bloku-MB** zmiennoprzecinkowy Użyj tego rozmiaru bloku (określonego w MIB) podczas przekazywania do usługi Azure Storage lub pobierania z usługi Azure Storage. Wartość domyślna jest obliczana automatycznie na podstawie rozmiaru pliku. Ułamki dziesiętne są dozwolone (na przykład: `0.25` ).

**--check-MD5** ciąg Określa, jak ściśle skróty MD5 powinny być sprawdzane podczas pobierania. Ta opcja jest dostępna tylko podczas pobierania. Dostępne wartości to: `NoCheck` , `LogOnly` , `FailIfDifferent` , `FailIfDifferentOrMissing` . (wartość domyślna `FailIfDifferent` ). (ustawienie domyślne `FailIfDifferent` )

**--delete-Destination** ciąg definiuje, czy usunąć dodatkowe pliki z miejsca docelowego, które nie są obecne w źródle. Może być ustawiona na `true` , `false` , lub `prompt` . Jeśli ta wartość jest ustawiona na `prompt` , użytkownik zostanie poproszony o podanie pytania przed zaplanowaniem plików i obiektów BLOB do usunięcia. (wartość domyślna `false` ). (ustawienie domyślne `false` )

**--exclude-Attribute** (tylko system Windows) wyklucza pliki, których atrybuty pasują do listy atrybutów. Na przykład: `A;S;R`

**--exclude-Path** ciąg Wyklucz te ścieżki podczas porównywania źródła z miejscem docelowym. Ta opcja nie obsługuje symboli wieloznacznych (*). Sprawdza prefiks ścieżki względnej (na przykład: `myFolder;myFolder/subDirName/file.pdf` ).

**--exclude-String wykluczanie** plików, w których nazwa jest zgodna z listą wzorców. Na przykład: `*.jpg;*.pdf;exactName`

**--**    pomoc dla synchronizacji.

**--include — ciąg atrybutów** (tylko system Windows) zawiera tylko pliki, których atrybuty pasują do listy atrybutów. Na przykład: `A;S;R`

**--dołączany** ciąg do wzorca zawiera tylko pliki, w których nazwa jest zgodna z listą wzorców. Na przykład: `*.jpg;*.pdf;exactName`

**--ciąg na poziomie dziennika** definiuje szczegółowość dziennika dla pliku dziennika, dostępne poziomy: `INFO` (wszystkie żądania i odpowiedzi), `WARNING` (wolne odpowiedzi), `ERROR` (tylko Nieudane żądania) i `NONE` (bez dzienników wyjściowych). (wartość domyślna `INFO` ). 

**--Preserve-SMB-info**   Domyślnie wartość false. Zachowuje informacje o właściwościach protokołu SMB (czas ostatniego zapisu, czas utworzenia, bity atrybutów) między zasobami opartymi na protokole SMB (Windows i Azure Files). Ta flaga ma zastosowanie do plików i folderów, chyba że określony jest filtr tylko dla plików (na przykład include-Pattern). Informacje przesyłane do folderów są takie same jak dla plików, z wyjątkiem czasu ostatniego zapisu, który nie jest zachowywany dla folderów.

**--Preserve-SMB-uprawnienia**   Domyślnie wartość false. Zachowuje listy ACL protokołu SMB między zasobami zależnymi (Windows i Azure Files). Ta flaga ma zastosowanie do plików i folderów, chyba że określony jest filtr tylko dla plików (na przykład `include-pattern` ).

**--Put-MD5**     Utwórz skrót MD5 każdego pliku i Zapisz skrót jako właściwość Content-MD5 docelowego obiektu BLOB lub pliku. (Domyślnie skrót nie jest tworzony). Dostępne tylko podczas przekazywania.

**--rekursywnie** `True` Domyślnie poszukaj podkatalogów cyklicznie podczas synchronizacji między katalogami.     (wartość domyślna `True` ). 

**--S2S-Preserve-dostęp-warstwa**  Zachowaj warstwę dostępu podczas kopiowania usługi do usługi. Zapoznaj się z [usługą Azure Blob Storage: warstwami dostępu gorąca, chłodna i archiwalna](../blobs/storage-blob-storage-tiers.md) , aby upewnić się, że docelowe konto magazynu obsługuje ustawienie Warstwa dostępu. W przypadkach, gdy ustawienie warstwy dostępu nie jest obsługiwane, użyj s2sPreserveAccessTier = false, aby obejść kopiowanie warstwy dostępu. (wartość domyślna `true` ). 

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--Cap-MB/s UInt32|Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.|
|--ciąg typu wyjściowego|Format danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text".|
|--Zaufane — ciąg sufiksów firmy Microsoft   |Określa dodatkowe sufiksy domeny, w których mogą być wysyłane Azure Active Directory tokeny logowania.  Wartość domyślna to "*. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Wszystkie wymienione tutaj są dodawane do ustawień domyślnych. W celu zapewnienia bezpieczeństwa należy tu umieścić tylko domeny Microsoft Azure. Rozdziel wiele wpisów średnikami.|

## <a name="see-also"></a>Zobacz także

- [AzCopy](storage-ref-azcopy.md)
