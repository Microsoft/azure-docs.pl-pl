---
title: Przekazywanie plików do usługi Azure Blob Storage za pomocą AzCopy v10 | Microsoft Docs
description: Ten artykuł zawiera kolekcję przykładowych poleceń AzCopy, które ułatwiają przekazywanie plików do usługi Azure Blob Storage.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 7cdc10720b6fa93cf1893d2040fd1c1f3e9e1f02
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728866"
---
# <a name="upload-files-to-azure-blob-storage-by-using-azcopy-v10"></a>Przekazywanie plików do usługi Azure Blob Storage za pomocą AzCopy v10

Pliki i katalogi można przekazać do usługi BLOB Storage za pomocą narzędzia wiersza polecenia AzCopy v10. 

Aby zobaczyć przykłady dla innych typów zadań, takich jak pobieranie obiektów blob, synchronizowanie z magazynem obiektów blob lub kopiowanie obiektów BLOB między kontami, zobacz linki przedstawione w sekcji [następne kroki](#next-steps) tego artykułu.

## <a name="get-started"></a>Rozpoczęcie pracy

Zapoznaj się z artykułem [wprowadzenie do AzCopy](storage-use-azcopy-v10.md) w celu pobrania AzCopy i Dowiedz się więcej na temat sposobów dostarczania poświadczeń autoryzacji do usługi magazynu.

> [!NOTE] 
> W przykładach w tym artykule przyjęto założenie, że poświadczenia autoryzacji zostały podane przy użyciu Azure Active Directory (Azure AD).
>
> Jeśli wolisz używać tokenu SAS do autoryzacji dostępu do danych obiektów blob, możesz dołączyć ten token do adresu URL zasobu w każdym poleceniu AzCopy. Na przykład: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Tworzenie kontenera

Aby utworzyć kontener, można użyć polecenia [AzCopy](storage-ref-azcopy-make.md) .

> [!TIP]
> Ten przykład obejmuje argumenty ścieżki z pojedynczym cudzysłowem (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

| Składnia/przykład | Kod |
|--------|-----------|
| **Składnia** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Przykład** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

Aby uzyskać szczegółowe dokumenty referencyjne, zobacz [AzCopy Make](storage-ref-azcopy-make.md).

## <a name="upload-a-file"></a>Przekazywanie pliku

Przekaż plik za pomocą polecenia [copy AzCopy](storage-ref-azcopy-copy.md) .

> [!TIP]
> Ten przykład obejmuje argumenty ścieżki z pojedynczym cudzysłowem (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Przykład** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

Możesz również przekazać plik za pomocą symbolu wieloznacznego (*) w dowolnym miejscu w ścieżce lub nazwie pliku. Na przykład: `'C:\myDirectory\*.txt'` , lub `C:\my*\*.txt` .

## <a name="upload-a-directory"></a>Przekaż katalog

Przekaż katalog przy użyciu polecenia [copy AzCopy](storage-ref-azcopy-copy.md) . 

Ten przykład kopiuje katalog (i wszystkie pliki w tym katalogu) do kontenera obiektów BLOB. Wynik jest katalogiem w kontenerze o tej samej nazwie.

> [!TIP]
> Ten przykład obejmuje argumenty ścieżki z pojedynczym cudzysłowem (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Aby skopiować do katalogu w kontenerze, należy po prostu określić nazwę tego katalogu w ciągu polecenia.

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

W przypadku określenia nazwy katalogu, który nie istnieje w kontenerze, AzCopy tworzy nowy katalog o tej nazwie.

## <a name="upload-directory-contents"></a>Przekaż zawartość katalogu

Przekaż zawartość katalogu za pomocą polecenia [copy AzCopy](storage-ref-azcopy-copy.md) . Użyj symbolu wieloznacznego (*), aby przekazać zawartość bez kopiowania katalogu zawierającego.

> [!TIP]
> Ten przykład obejmuje argumenty ścieżki z pojedynczym cudzysłowem (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Przykład** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |


Dołącz `--recursive` flagę do przekazywania plików we wszystkich podkatalogach.

## <a name="upload-specific-files"></a>Przekazywanie określonych plików

Można przekazać określone pliki przy użyciu pełnych nazw plików, częściowych nazw z symbolami wieloznacznymi (*) lub przy użyciu dat i godzin.

> [!TIP]
> Te przykłady otaczające argumenty ścieżki cudzysłowem pojedynczym (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

### <a name="specify-multiple-complete-file-names"></a>Określ wiele pełnych nazw plików

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z `--include-path` opcją. Oddziel poszczególne nazwy plików przy użyciu średnika ( `;` ).

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

W tym przykładzie AzCopy transferuje `C:\myDirectory\photos` katalog i `C:\myDirectory\documents\myFile.txt` plik. Dołącz `--recursive` opcję transferu wszystkich plików w `C:\myDirectory\photos` katalogu.

Można również wykluczyć pliki przy użyciu `--exclude-path` opcji. Aby dowiedzieć się więcej, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md) Reference docs.

### <a name="use-wildcard-characters"></a>Użyj symboli wieloznacznych

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z `--include-pattern` opcją. Określ częściowe nazwy, które zawierają symbole wieloznaczne. Oddziel nazwy przy użyciu semicolin ( `;` ). 

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

Można również wykluczyć pliki przy użyciu `--exclude-pattern` opcji. Aby dowiedzieć się więcej, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md) Reference docs.

`--include-pattern`Opcje i są `--exclude-pattern` stosowane tylko do nazw plików, a nie do ścieżki.  Jeśli chcesz skopiować wszystkie pliki tekstowe, które istnieją w drzewie katalogów, użyj `–recursive` opcji, aby pobrać całe drzewo katalogów, a następnie użyj `–include-pattern` i określ, `*.txt` Aby pobrać wszystkie pliki tekstowe.

### <a name="upload-files-that-were-modified-before-or-after-a-date-and-time"></a>Przekaż pliki, które zostały zmodyfikowane przed datą i godziną 

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z `--include-before` `--include-after` opcją or. Określ datę i godzinę w formacie ISO-8601 (na przykład: `2020-08-19T15:04:00Z` ). 

Poniższe przykłady przesłaniają pliki, które zostały zmodyfikowane w określonym dniu lub po nim.

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Przykład** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'` |

Aby uzyskać szczegółowe informacje, zobacz Dokumentacja [AzCopy Copy](storage-ref-azcopy-copy.md) Reference.

## <a name="upload-with-index-tags"></a>Przekaż ze znacznikami indeksu

Można przekazać plik i dodać [Tagi indeksu obiektów BLOB (wersja zapoznawcza)](../blobs/storage-manage-find-blobs.md) do docelowego obiektu BLOB.  

W przypadku korzystania z autoryzacji usługi Azure AD podmiot zabezpieczeń musi mieć przypisaną rolę [właściciela danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) lub musi mieć uprawnienia do `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [operacji dostawcy zasobów platformy Azure](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) za pośrednictwem niestandardowej roli platformy Azure. Jeśli używasz tokenu sygnatury dostępu współdzielonego (SAS), ten token musi zapewnić dostęp do tagów obiektu BLOB za pośrednictwem `t` uprawnienia SAS.

Aby dodać tagi, użyj `--blob-tags` opcji wraz z algorytmem par klucz-wartość w adresie URL. Na przykład, aby dodać klucz `my tag` i wartość `my tag value` , należy dodać `--blob-tags='my%20tag=my%20tag%20value'` do parametru docelowego. 

Oddziel wiele tagów indeksów przy użyciu znaku handlowego "i" ( `&` ).  Na przykład, jeśli chcesz dodać klucz `my second tag` i wartość `my second tag value` , ciąg opcji Complete `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

W poniższych przykładach pokazano, jak użyć `--blob-tags` opcji.

> [!TIP]
> Ten przykład obejmuje argumenty ścieżki z pojedynczym cudzysłowem (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Przekazywanie pliku** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Przekaż katalog** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`|
| **Przekaż zawartość katalogu** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

> [!NOTE]
> Jeśli określisz katalog dla źródła, wszystkie obiekty blob, które są kopiowane do lokalizacji docelowej, będą miały te same znaczniki, które są określone w poleceniu.

## <a name="upload-with-optional-flags"></a>Przekaż z opcjonalnymi flagami

Możesz dostosować operację przekazywania, używając opcjonalnych flag. Oto kilka przykładów.

|Scenariusz|Flaga|
|---|---|
|Przekaż pliki jako uzupełnialne lub stronicowe obiekty blob.|**--Typ** = \[ obiektu BLOB BlockBlob \| PageBlob \| AppendBlob\]|
|Przekaż dane do określonej warstwy dostępu (na przykład warstwy Archiwum).|**--Block-BLOB-warstw** = \[ Brak \| aktywnego \| archiwum chłodnego \|\]|

Aby uzyskać pełną listę, zobacz [Opcje](storage-ref-azcopy-copy.md#options).

## <a name="next-steps"></a>Następne kroki

Więcej przykładów znajdziesz w następujących artykułach:

- [Przykłady: Pobierz](storage-use-azcopy-blobs-download.md)
- [Przykłady: Kopiowanie między kontami](storage-use-azcopy-blobs-copy.md)
- [Przykłady: Synchronizowanie](storage-use-azcopy-blobs-synchronize.md)
- [Przykłady: Zasobniki usługi Amazon S3](storage-use-azcopy-s3.md)
- [Przykłady: Azure Files](storage-use-azcopy-files.md)
- [Samouczek: Migracja danych lokalnych do magazynu w chmurze za pomocą narzędzia AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)