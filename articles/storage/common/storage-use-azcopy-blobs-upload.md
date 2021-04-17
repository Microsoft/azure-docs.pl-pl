---
title: Przekazywanie plików do usługi Azure Blob Storage przy użyciu narzędzia AzCopy w wersji 10 | Microsoft Docs
description: Ten artykuł zawiera kolekcję przykładowych poleceń narzędzia AzCopy, które ułatwiają przekazywanie plików do usługi Azure Blob Storage.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 352497f0f4d23250abe9f84121f358589664002b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502916"
---
# <a name="upload-files-to-azure-blob-storage-by-using-azcopy"></a>Przekazywanie plików do usługi Azure Blob Storage przy użyciu narzędzia AzCopy

Pliki i katalogi można przekazywać do usługi Blob Storage za pomocą narzędzia wiersza polecenia AzCopy w wersji 10. 

Aby wyświetlić przykłady innych typów zadań, takich jak pobieranie obiektów blob, synchronizowanie z usługą Blob [](#next-steps) Storage lub kopiowanie obiektów blob między kontami, zobacz linki przedstawione w sekcji Następne kroki tego artykułu.

## <a name="get-started"></a>Rozpoczęcie pracy

Zobacz artykuł [Wprowadzenie do programu AzCopy,](storage-use-azcopy-v10.md) aby pobrać program AzCopy i poznać sposoby poświadczeń autoryzacji w usłudze magazynu.

> [!NOTE] 
> W przykładach w tym artykule założono, że podano poświadczenia autoryzacji przy użyciu Azure Active Directory (Azure AD).
>
> Jeśli wolisz użyć tokenu SAS do autoryzowania dostępu do danych obiektów blob, możesz dołączyć ten token do adresu URL zasobu w każdym poleceniu programu AzCopy. Na przykład: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Tworzenie kontenera

Aby utworzyć kontener, możesz użyć polecenia [azcopy make.](storage-ref-azcopy-make.md)

> [!TIP]
> W tym przykładzie argumenty ścieżki są ujęte w a cudzysłów pojedynczy (""). Używaj a cudzysłowów pojedynczych we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w cudzysłów (""), a nie pojedynczy cudzysłów ("").

Jest to przykład tabeli:

**Składnia**

`azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'`

**Przykład**

```azcopy
https://mystorageaccount.blob.core.windows.net/mycontainer
```

**Przykład (hierarchiczna przestrzeń nazw)**

```azcopy
https://mystorageaccount.dfs.core.windows.net/mycontainer
```

Aby uzyskać szczegółowe dokumenty referencyjne, zobacz [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-a-file"></a>Przekazywanie pliku

Przekaż plik za pomocą [polecenia azcopy copy.](storage-ref-azcopy-copy.md)

> [!TIP]
> W tym przykładzie argumenty ścieżki są ujęte w a cudzysłów pojedynczy (""). Używaj a cudzysłowów pojedynczych we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w cudzysłów (""), a nie pojedynczy cudzysłów ("").

**Składnia**

`azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'`

**Przykład**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

**Przykład (hierarchiczna przestrzeń nazw)**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'
```

Plik można również przekazać przy użyciu symbolu wieloznacznego (*) w dowolnym miejscu ścieżki pliku lub nazwy pliku. Na przykład: `'C:\myDirectory\*.txt'` lub `C:\my*\*.txt` .

## <a name="upload-a-directory"></a>Przekazywanie katalogu

Przekaż katalog za pomocą [polecenia azcopy copy.](storage-ref-azcopy-copy.md) 

W tym przykładzie katalog (i wszystkie pliki w tym katalogu) są kopiowane do kontenera obiektów blob. Wynikiem jest katalog w kontenerze o tej samej nazwie.

> [!TIP]
> W tym przykładzie argumenty ścieżki są ujęte w a cudzysłów pojedynczy (""). Używaj a cudzysłowów pojedynczych we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójny cudzysłów ("") zamiast cudzysłowów pojedynczych ("").

**Składnia**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive`

**Przykład**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive
```

**Przykład (hierarchiczna przestrzeń nazw)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive
```

Aby skopiować do katalogu w kontenerze, wystarczy określić nazwę tego katalogu w ciągu polecenia.

**Przykład**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive
```

**Przykład (hierarchiczna przestrzeń nazw)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive
```

Jeśli określisz nazwę katalogu, który nie istnieje w kontenerze, program AzCopy utworzy nowy katalog według tej nazwy.

## <a name="upload-directory-contents"></a>Przekazywanie zawartości katalogu

Przekaż zawartość katalogu za pomocą polecenia [azcopy copy.](storage-ref-azcopy-copy.md) Użyj symbolu wieloznacznego (*), aby przekazać zawartość bez kopiowania samego katalogu zawierającego.

> [!TIP]
> W tym przykładzie argumenty ścieżki są ujęte w a cudzysłów pojedynczy (""). Używaj a cudzysłowów pojedynczych we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójny cudzysłów ("") zamiast cudzysłowów pojedynczych ("").

**Składnia**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` 

**Przykład**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'
```

**Przykład (hierarchiczna przestrzeń nazw)**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'
```

Dołącz `--recursive` flagę , aby przekazać pliki we wszystkich podkatalogach.

## <a name="upload-specific-files"></a>Przekazywanie określonych plików

Określone pliki można przekazać przy użyciu pełnych nazw plików, częściowych nazw z symbolami wieloznacznymi (*), lub przy użyciu dat i godzin.

> [!TIP]
> W tych przykładach argumenty ścieżki są ujęte w a cudzysłów pojedynczy (""). Używaj a cudzysłowów pojedynczych we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w cudzysłów (""), a nie pojedynczy cudzysłów ("").

### <a name="specify-multiple-complete-file-names"></a>Określanie wielu pełnych nazw plików

Użyj [polecenia azcopy copy](storage-ref-azcopy-copy.md) z `--include-path` opcją . Oddziel poszczególne nazwy plików przy użyciu średnika ( `;` ).

**Składnia** 

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` 

**Przykład**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive'
```

**Przykład (hierarchiczna przestrzeń nazw)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive'
```

W tym przykładzie program AzCopy przesyła `C:\myDirectory\photos` katalog i `C:\myDirectory\documents\myFile.txt` plik. Dołącz opcję `--recursive` transferu wszystkich plików w `C:\myDirectory\photos` katalogu.

Możesz również wykluczyć pliki przy użyciu `--exclude-path` opcji . Aby dowiedzieć się więcej, zobacz [dokumenty referencyjne kopiowania azcopy.](storage-ref-azcopy-copy.md)

### <a name="use-wildcard-characters"></a>Używanie symboli wieloznacznych

Użyj [polecenia azcopy copy](storage-ref-azcopy-copy.md) z `--include-pattern` opcją . Określ częściowe nazwy, które zawierają symbole wieloznaczne. Oddziel nazwy za pomocą średnika ( `;` ). 

**Składnia**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` 

**Przykład**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'
```

**Przykład (hierarchiczna przestrzeń nazw)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'
```

Możesz również wykluczyć pliki przy użyciu `--exclude-pattern` opcji . Aby dowiedzieć się więcej, zobacz [azcopy copy reference](storage-ref-azcopy-copy.md) docs.

Opcje `--include-pattern` i dotyczą tylko nazw `--exclude-pattern` plików, a nie ścieżki.  Jeśli chcesz skopiować wszystkie pliki tekstowe, które istnieją w drzewie katalogów, użyj opcji , aby pobrać całe drzewo katalogów, a następnie użyj i określ , aby pobrać `–recursive` `–include-pattern` wszystkie pliki `*.txt` tekstowe.

### <a name="upload-files-that-were-modified-before-or-after-a-date-and-time"></a>Przekazywanie plików, które zostały zmodyfikowane przed datą i godziną lub po tej dacie 

Użyj [polecenia azcopy copy](storage-ref-azcopy-copy.md) z `--include-before` opcją `--include-after` lub . Określ datę i godzina w formacie ISO-8601 (na przykład: `2020-08-19T15:04:00Z` ). 

Poniższe przykłady przekażą pliki, które zostały zmodyfikowane w określonym dniu lub później.

**Składnia**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` 

**Przykład**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'
```

**Przykład (hierarchiczna przestrzeń nazw)**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'
```

Aby uzyskać szczegółowe informacje, zobacz dokumenty referencyjne dotyczące kopiowania w programie [azcopy.](storage-ref-azcopy-copy.md)

## <a name="upload-with-index-tags"></a>Przekazywanie za pomocą tagów indeksu

Możesz przekazać plik i dodać tagi indeksu obiektów [blob (wersja zapoznawcza)](../blobs/storage-manage-find-blobs.md) do docelowego obiektu blob.  

Jeśli używasz autoryzacji usługi Azure AD, podmiot zabezpieczeń musi mieć przypisaną rolę właściciela danych obiektu blob usługi [Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) lub musi mieć uprawnienia do operacji dostawcy zasobów platformy Azure za pośrednictwem niestandardowej roli `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) platformy Azure. Jeśli używasz tokenu sygnatury dostępu współdzielonego (SAS), ten token musi zapewniać dostęp do tagów obiektu blob za pośrednictwem uprawnień `t` sygnatury dostępu współdzielonego.

Aby dodać tagi, użyj opcji wraz z parą klucz-wartość zakodowaną `--blob-tags` w adresie URL. Aby na przykład dodać klucz `my tag` i wartość , należy dodać parametr do `my tag value` `--blob-tags='my%20tag=my%20tag%20value'` parametru docelowego. 

Oddziel wiele tagów indeksu przy użyciu ampersand ( `&` ).  Jeśli na przykład chcesz dodać klucz i wartość , kompletny `my second tag` `my second tag value` ciąg opcji to `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

Poniższe przykłady pokazują, jak używać `--blob-tags` opcji .

> [!TIP]
> W tym przykładzie argumenty ścieżki są ujęte w a cudzysłów pojedynczy (""). Używaj a cudzysłowów pojedynczych we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w cudzysłów (""), a nie pojedynczy cudzysłów ("").

**Przekazywanie pliku**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

**Przekazywanie katalogu**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

**Przekazywanie zawartości katalogu**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

> [!NOTE]
> Jeśli określisz katalog dla źródła, wszystkie obiekty blob, które zostaną skopiowane do miejsca docelowego, będą mieć te same tagi, które określisz w poleceniu .

## <a name="upload-with-optional-flags"></a>Przekazywanie z opcjonalnymi flagami

Możesz dostosować operację przekazywania przy użyciu opcjonalnych flag. Oto kilka przykładów.

|Scenariusz|Flaga|
|---|---|
|Przekaż pliki jako uzupełnialne lub stronicowe obiekty blob.|**--blob-type** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
|Przekaż dane do określonej warstwy dostępu (na przykład warstwy Archiwum).|**--block-blob-tier** = \[ Brak \| \| gorącego \| archiwum chłodnego\]|

Aby uzyskać pełną listę, zobacz [opcje](storage-ref-azcopy-copy.md#options).

## <a name="next-steps"></a>Następne kroki

Więcej przykładów można znaleźć w tych artykułach:

- [Przykłady: Pobierz](storage-use-azcopy-blobs-download.md)
- [Przykłady: Kopiowanie między kontami](storage-use-azcopy-blobs-copy.md)
- [Przykłady: Synchronizowanie](storage-use-azcopy-blobs-synchronize.md)
- [Przykłady: Zasobniki usługi Amazon S3](storage-use-azcopy-s3.md)
- [Przykłady: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Przykłady: Azure Files](storage-use-azcopy-files.md)
- [Samouczek: Migracja danych lokalnych do magazynu w chmurze za pomocą narzędzia AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Zapoznaj się z tymi artykułami, aby skonfigurować ustawienia, zoptymalizować wydajność i rozwiązać problemy:

- [Ustawienia konfiguracji programu AzCopy](storage-ref-azcopy-configuration-settings.md)
- [Optymalizowanie wydajności programu AzCopy](storage-use-azcopy-optimize.md)
- [Rozwiązywanie problemów z programem AzCopy w wersji 10 w usłudze Azure Storage przy użyciu plików dziennika](storage-use-azcopy-configure.md)