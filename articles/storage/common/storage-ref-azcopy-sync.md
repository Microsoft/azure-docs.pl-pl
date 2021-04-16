---
title: azcopy sync | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia azcopy sync.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 83d10a7a6e9eb14379d32cc88800a2c443feac60
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503052"
---
# <a name="azcopy-sync"></a>azcopy sync

Replikuje lokalizację źródłową do lokalizacji docelowej. Ten artykuł zawiera szczegółowe informacje dotyczące polecenia azcopy sync. Aby dowiedzieć się więcej na temat synchronizowania obiektów blob między lokalizacjami źródłowymi i docelowymi, zobacz Synchronizowanie z usługą [Azure Blob Storage przy użyciu narzędzia AzCopy w wersji 10.](storage-use-azcopy-blobs-synchronize.md) Aby Azure Files, zobacz [Synchronize files (Synchronizowanie plików).](storage-use-azcopy-files.md#synchronize-files)

## <a name="synopsis"></a>Streszczenie

Czasy ostatniej modyfikacji są używane do porównania. Plik zostanie pominięty, jeśli czas ostatniej modyfikacji w miejscu docelowym jest niedawny.

Obsługiwane pary to:

- lokalne <-> Azure Blob (można użyć uwierzytelniania SAS lub OAuth)
- Azure Blob <-> Azure Blob (źródło musi zawierać sygnaturę dostępu współdzielonego lub jest publicznie dostępne; uwierzytelnianie SAS lub OAuth może służyć jako miejsce docelowe)
- Azure File <-> Azure File (źródło musi zawierać sygnaturę dostępu współdzielonego lub jest publicznie dostępne; Uwierzytelnianie sygnatury dostępu współdzielonego powinno być używane dla miejsca docelowego)

Polecenie synchronizacji różni się od polecenia kopiowania na kilka sposobów:

1. Domyślnie flaga cykliczna ma wartość true, a synchronizacja kopiuje wszystkie podkatalogi. Synchronizacja kopiuje pliki najwyższego poziomu wewnątrz katalogu tylko wtedy, gdy flaga cykliczna ma wartość false.
2. Podczas synchronizacji między katalogami wirtualnymi dodaj do ścieżki ukośnik (zapoznaj się z przykładami), jeśli istnieje obiekt blob o takiej samej nazwie jak jeden z katalogów wirtualnych.
3. Jeśli flaga jest ustawiona na wartość true lub prompt, synchronizacja spowoduje usunięcie plików i obiektów blob w miejscu docelowym, które `deleteDestination` nie są obecne w źródle.

## <a name="related-conceptual-articles"></a>Powiązane artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Samouczek: migrowanie danych lokalnych do magazynu w chmurze za pomocą programu AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Transferowanie danych za pomocą programu AzCopy i usługi Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)

### <a name="advanced"></a>Zaawansowany

Jeśli nie określisz rozszerzenia pliku, narzędzie AzCopy automatycznie wykryje typ zawartości plików podczas przekazywania z dysku lokalnego na podstawie rozszerzenia pliku lub zawartości (jeśli nie określono rozszerzenia).

Wbudowana tabela odnośników jest mała, ale w systemie Unix jest rozszerzana przez pliki mime.types systemu lokalnego, jeśli są dostępne w co najmniej jednej z tych nazw:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

W systemie Windows typy MIME są wyodrębnione z rejestru.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>Przykłady

Synchronizuj pojedynczy plik:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Tak samo jak powyżej, ale również oblicza skrót MD5 zawartości pliku, a następnie zapisuje ten skrót MD5 jako właściwość Content-MD5 obiektu blob. 

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Zsynchronizuj cały katalog wraz z jego podkatalogami (pamiętaj, że opcja cykliczna jest domyślnie włączona):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

lub

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Synchronizuj tylko pliki wewnątrz katalogu, ale nie podkatalogi ani pliki wewnątrz podkatalogów:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Zsynchronizuj podzbiór plików w katalogu (na przykład: tylko pliki jpg i pdf lub jeśli nazwa pliku to `exactName` ):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include-pattern="*.jpg;*.pdf;exactName"
```

Zsynchronizuj cały katalog, ale wyklucz niektóre pliki z zakresu (na przykład: każdy plik, który rozpoczyna się od foo lub kończy się na pasku):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude-pattern="foo*;*bar"
```

Synchronizuj pojedynczy obiekt blob:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Synchronizuj katalog wirtualny:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Zsynchronizuj katalog wirtualny o takiej samej nazwie jak obiekt blob (dodaj ukośnik na końcowej ścieżce w celu uujednoznania):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Synchronizacja katalogu plików platformy Azure (taka sama składnia jak w przypadku obiektu blob):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Jeśli flagi dołączania/wykluczania są używane razem, będą analizowane tylko pliki pasujące do wzorców dołączania, ale te pasujące do wzorców wykluczania będą zawsze ignorowane.

## <a name="options"></a>Opcje

**--block-size-mb** float Użyj tego rozmiaru bloku (określonego w MiB) podczas przekazywania do usługi Azure Storage lub pobierania z usługi Azure Storage. Wartość domyślna jest obliczana automatycznie na podstawie rozmiaru pliku. Ułamki dziesiętne są dozwolone (na przykład: `0.25` ).

**--check-md5** string Określa, jak ściśle mają być weryfikowane skróty MD5 podczas pobierania. Ta opcja jest dostępna tylko podczas pobierania. Dostępne wartości to: `NoCheck` , `LogOnly` , , `FailIfDifferent` `FailIfDifferentOrMissing` . (wartość `FailIfDifferent` domyślna). (wartość `FailIfDifferent` domyślna )

**--delete-destination** ciąg Definiuje, czy usunąć dodatkowe pliki z miejsca docelowego, które nie są obecne w źródle. Można ustawić wartość `true` , `false` lub `prompt` . W przypadku ustawienia na wartość , użytkownik zostanie poproszony o pytanie przed zaplanowaniem usunięcia plików `prompt` i obiektów blob. (wartość `false` domyślna). (wartość `false` domyślna )

**--exclude-attributes** string (tylko system Windows) Wyklucza pliki, których atrybuty są zgodne z listą atrybutów. Na przykład: `A;S;R`

**--exclude-path** string Wyklucz te ścieżki podczas porównywania źródła z miejscem docelowym. Ta opcja nie obsługuje symboli wieloznacznych (*). Sprawdza prefiks ścieżki względnej (na przykład: `myFolder;myFolder/subDirName/file.pdf` ).

**--exclude-pattern** string Wyklucza pliki, w których nazwa pasuje do listy wzorców. Na przykład: `*.jpg;*.pdf;exactName`

**— pomoc w**    przypadku synchronizacji.

**--include-attributes** string (tylko system Windows) Zawiera tylko pliki, których atrybuty są zgodne z listą atrybutów. Na przykład: `A;S;R`

**--include-pattern** string Uwzględnij tylko pliki, w których nazwa pasuje do listy wzorców. Na przykład: `*.jpg;*.pdf;exactName`

**--log-level** string Define the log verbosity for the log file, available levels: `INFO` (all requests and responses), `WARNING` (slow responses), `ERROR` (only failed requests) and `NONE` (no output logs). (wartość `INFO` domyślna). 

**--preserve-smb-info**   Wartość false jest domyślnie fałszem. Zachowuje informacje o właściwościach SMB (czas ostatniego zapisu, czas tworzenia, bity atrybutów) między zasobami z informacjami o smb (systemach Windows i Azure Files). Ta flaga ma zastosowanie zarówno do plików, jak i folderów, chyba że określono filtr tylko do plików (na przykład include-pattern). Informacje przesyłane dla folderów są takie same jak w przypadku plików, z wyjątkiem czasu ostatniego zapisu, który nie jest zachowywany dla folderów.

**--preserve-smb-permissions**   Wartość false jest domyślnie fałszem. Zachowuje adresy ACL SMB między świadomymi zasobami (windows i Azure Files). Ta flaga ma zastosowanie zarówno do plików, jak i folderów, chyba że określono filtr tylko do plików (na przykład `include-pattern` ).

**--put-md5**     Utwórz skrót MD5 każdego pliku i zapisz skrót jako właściwość Content-MD5 docelowego obiektu blob lub pliku. (Domyślnie skrót NIE jest tworzony). Dostępne tylko podczas przekazywania.

**--rekursywne** `True` Domyślnie rekursywnie przyjrzyj się podkatalogom podczas synchronizacji między katalogami.     (wartość `True` domyślna). 

**--s2s-preserve-access-tier**  Zachowaj warstwę dostępu podczas kopiowania z usługi do usługi. Zapoznaj się [z tematem Azure Blob Storage: hot, cool, and archive access tiers](../blobs/storage-blob-storage-tiers.md) (Usługa Azure Blob Storage: warstwy dostępu Gorąca, Chłodna i Archiwum), aby upewnić się, że docelowe konto magazynu obsługuje ustawianie warstwy dostępu. W przypadkach, gdy ustawienie warstwy dostępu nie jest obsługiwane, użyj s2sPreserveAccessTier=false, aby pominąć kopiowanie warstwy dostępu. (wartość `true` domyślna). 

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--cap-mbps uint32|Limituje szybkość transferu w megabitach na sekundę. Przepływność chwila po chwili może się nieznacznie różnić od limitu. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie jest ograniczona.|
|--output-type string|Format danych wyjściowych polecenia. Dostępne opcje to: text, json. Wartość domyślna to "text".|
|--trusted-microsoft-suffixes string   |Określa dodatkowe sufiksy domeny, Azure Active Directory tokeny logowania mogą być wysyłane.  Wartość domyślna to '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Wszystkie wymienione w tym miejscu są dodawane do wartości domyślnej. Ze względów bezpieczeństwa należy w tym miejscu Microsoft Azure tylko domen. Oddziel wiele wpisów średnikami.|

## <a name="see-also"></a>Zobacz też

- [azcopy](storage-ref-azcopy.md)
