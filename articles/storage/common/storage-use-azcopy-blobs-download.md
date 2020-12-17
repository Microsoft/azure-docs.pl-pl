---
title: Pobieranie obiektów blob z usługi Azure Blob Storage za pomocą AzCopy v10 | Microsoft Docs
description: Ten artykuł zawiera kolekcję przykładowych poleceń AzCopy, które ułatwiają pobieranie obiektów blob z usługi Azure Blob Storage.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 382adb36712fbf4bee83044c8b2d096223eb6269
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630041"
---
# <a name="download-blobs-from-azure-blob-storage-by-using-azcopy-v10"></a>Pobieranie obiektów blob z usługi Azure Blob Storage za pomocą AzCopy v10

Obiekty blob i katalogi można pobrać z usługi BLOB Storage za pomocą narzędzia wiersza polecenia AzCopy v10. 

Aby zobaczyć przykłady dla innych typów zadań, takich jak przekazywanie plików, synchronizacja z magazynem obiektów blob lub kopiowanie obiektów BLOB między kontami, zobacz linki przedstawione w sekcji [następne kroki](#next-steps) w tym artykule.

## <a name="get-started"></a>Wprowadzenie

Zapoznaj się z artykułem [wprowadzenie do AzCopy](storage-use-azcopy-v10.md) w celu pobrania AzCopy i Dowiedz się więcej na temat sposobów dostarczania poświadczeń autoryzacji do usługi magazynu.

> [!NOTE] 
> W przykładach w tym artykule przyjęto założenie, że poświadczenia autoryzacji zostały podane przy użyciu Azure Active Directory (Azure AD).
>
> Jeśli wolisz używać tokenu SAS do autoryzacji dostępu do danych obiektów blob, możesz dołączyć ten token do adresu URL zasobu w każdym poleceniu AzCopy. Na przykład: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="download-a-blob"></a>Pobieranie obiektu blob

Pobierz obiekt BLOB za pomocą polecenia [copy AzCopy](storage-ref-azcopy-copy.md) .

> [!TIP]
> Ten przykład obejmuje argumenty ścieżki z pojedynczym cudzysłowem (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

> [!NOTE]
> Jeśli `Content-md5` wartość właściwości obiektu BLOB zawiera skrót, AzCopy oblicza skrót MD5 dla pobranych danych i sprawdza, czy skrót MD5 przechowywany we właściwości obiektu BLOB jest `Content-md5` zgodny z obliczonym skrótem. Jeśli te wartości nie są zgodne, pobieranie nie powiedzie się, o ile nie zastąpisz tego zachowania przez dołączenie `--check-md5=NoCheck` lub `--check-md5=LogOnly` do polecenia copy.

## <a name="download-a-directory"></a>Pobierz katalog

Pobierz katalog za pomocą polecenia [copy AzCopy](storage-ref-azcopy-copy.md) .

> [!TIP]
> Ten przykład obejmuje argumenty ścieżki z pojedynczym cudzysłowem (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Ten przykład powoduje, że katalog o nazwie `C:\myDirectory\myBlobDirectory` zawiera wszystkie pobrane obiekty blob.

## <a name="download-directory-contents"></a>Pobierz zawartość katalogu

Możesz pobrać zawartość katalogu bez kopiowania samego katalogu, korzystając z symbolu wieloznacznego (*).

> [!TIP]
> Ten przykład obejmuje argumenty ścieżki z pojedynczym cudzysłowem (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

> [!NOTE]
> Obecnie ten scenariusz jest obsługiwany tylko w przypadku kont, które nie mają hierarchicznej przestrzeni nazw.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

Dołącz `--recursive` flagę do pobierania plików we wszystkich podkatalogach.

## <a name="download-specific-blobs"></a>Pobieranie określonych obiektów BLOB

Konkretne obiekty blob można pobrać przy użyciu pełnych nazw plików, częściowych nazw z symbolami wieloznacznymi (*) lub przy użyciu dat i godzin. 

> [!TIP]
> Te przykłady otaczające argumenty ścieżki cudzysłowem pojedynczym (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

#### <a name="specify-multiple-complete-blob-names"></a>Określ wiele pełnych nazw obiektów BLOB

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z `--include-path` opcją. Oddziel poszczególne nazwy obiektów BLOB za pomocą semicolin ( `;` ).

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

W tym przykładzie AzCopy transferuje `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` katalog i `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` plik. Dołącz `--recursive` opcję transferu wszystkich obiektów BLOB w `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` katalogu.

Można również wykluczyć obiekty blob przy użyciu `--exclude-path` opcji. Aby dowiedzieć się więcej, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md) Reference docs.

#### <a name="use-wildcard-characters"></a>Użyj symboli wieloznacznych

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z `--include-pattern` opcją. Określ częściowe nazwy, które zawierają symbole wieloznaczne. Oddziel nazwy przy użyciu semicolin ( `;` ).

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Można również wykluczyć obiekty blob przy użyciu `--exclude-pattern` opcji. Aby dowiedzieć się więcej, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md) Reference docs.

`--include-pattern`Opcje i są `--exclude-pattern` stosowane tylko do nazw obiektów blob, a nie do ścieżki.  Jeśli chcesz skopiować wszystkie pliki tekstowe (obiekty blob), które istnieją w drzewie katalogów, użyj `–recursive` opcji, aby pobrać całe drzewo katalogów, a następnie użyj `–include-pattern` i określ, `*.txt` Aby pobrać wszystkie pliki tekstowe.

#### <a name="download-blobs-that-were-modified-before-or-after-a-date-and-time"></a>Pobieranie obiektów blob, które zostały zmodyfikowane przed datą i godziną 

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z `--include-before` `--include-after` opcją or. Określ datę i godzinę w formacie ISO-8601 (na przykład: `2020-08-19T15:04:00Z` ). 

Poniższe przykłady pobierają pliki, które zostały zmodyfikowane w określonym dniu lub po nim.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |

Aby uzyskać szczegółowe informacje, zobacz Dokumentacja [AzCopy Copy](storage-ref-azcopy-copy.md) Reference.

#### <a name="download-previous-versions-of-a-blob"></a>Pobierz poprzednie wersje obiektu BLOB

Jeśli włączono obsługę [wersji obiektów BLOB](../blobs/versioning-enable.md), można pobrać co najmniej jedną poprzednią wersję obiektu BLOB. 

Najpierw utwórz plik tekstowy zawierający listę [identyfikatorów wersji](../blobs/versioning-overview.md). Każdy identyfikator wersji musi znajdować się w osobnym wierszu. Na przykład: 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

Następnie użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z `--list-of-versions` opcją. Określ lokalizację pliku tekstowego zawierającego listę wersji (na przykład: `D:\\list-of-versions.txt` ).  

#### <a name="download-a-blob-snapshot"></a>Pobierz migawkę obiektu BLOB

[Migawkę obiektu BLOB](/azure/storage/blobs/snapshots-overview) można pobrać, odwołując się do wartości **DateTime** migawki obiektu BLOB. 

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>?sharesnapshot=<DateTime-of-snapshot>' '<local-file-path>'` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |

> [!NOTE]
> Jeśli używasz tokenu SAS do autoryzacji dostępu do danych obiektów blob, Dołącz **datę i godzinę** elementu Snapshot po tokenie SAS. Na przykład: `'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z'`.

## <a name="download-with-optional-flags"></a>Pobierz z opcjonalnymi flagami

Możesz dostosować operację pobierania, używając opcjonalnych flag. Oto kilka przykładów.

|Scenariusz|Flaga|
|---|---|
|Automatycznie Kompresuj pliki.|**--dekompresowanie**|
|Określ, jak Szczegółowa ma być liczba wpisów dziennika związanych z kopiowaniem.|**--poziom dziennika** = \[ Ostrzeżenie \| \| Brak informacji o błędzie \|\]|
|Określ, czy i jak zastąpić pliki i obiekty blob powodujące konflikt w miejscu docelowym.|**--overwrite** = \[ prawdziwy \| \| ifSourceNewer \| monit\]|

Aby uzyskać pełną listę, zobacz [Opcje](storage-ref-azcopy-copy.md#options).

## <a name="next-steps"></a>Następne kroki

Więcej przykładów znajdziesz w następujących artykułach:

- [Przykłady: Upload](storage-use-azcopy-blobs-upload.md)
- [Przykłady: Kopiuj między kontami](storage-use-azcopy-blobs-copy.md)
- [Przykłady: Synchronizowanie](storage-use-azcopy-blobs-synchronize.md)
- [Przykłady: Zasobniki usługi Amazon S3](storage-use-azcopy-s3.md)
- [Przykłady: Azure Files](storage-use-azcopy-files.md)
- [Samouczek: Migracja danych lokalnych do magazynu w chmurze za pomocą narzędzia AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)