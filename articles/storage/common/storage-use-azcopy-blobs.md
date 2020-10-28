---
title: Transferowanie danych do lub z usługi Azure Blob Storage za pomocą AzCopy v10 | Microsoft Docs
description: Ten artykuł zawiera kolekcję przykładowych poleceń AzCopy, które pomagają w tworzeniu kontenerów, kopiowaniu plików i synchronizowaniu katalogów między lokalnymi systemami plików i kontenerami.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 294adce3dc312003d72336bd0752ba3aba5eaace
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792858"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB

AzCopy to narzędzie wiersza polecenia, za pomocą którego można kopiować dane do, z lub między kontami magazynu. Ten artykuł zawiera przykładowe polecenia, które działają z usługą BLOB Storage.

> [!TIP]
> W przykładach w tym artykule zamieszczono argumenty Path z pojedynczymi cudzysłowami (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

## <a name="get-started"></a>Wprowadzenie

Zapoznaj się z artykułem [wprowadzenie do AzCopy](storage-use-azcopy-v10.md) w celu pobrania AzCopy i Dowiedz się więcej na temat sposobów dostarczania poświadczeń autoryzacji do usługi magazynu.

> [!NOTE]
> W przykładach w tym artykule przyjęto założenie, że tożsamość została uwierzytelniona przy użyciu `AzCopy login` polecenia. AzCopy następnie używa konta usługi Azure AD do autoryzacji dostępu do danych w usłudze BLOB Storage.
>
> Jeśli wolisz używać tokenu SAS do autoryzacji dostępu do danych obiektów blob, możesz dołączyć ten token do adresu URL zasobu w każdym poleceniu AzCopy.
>
> Na przykład: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Tworzenie kontenera

Aby utworzyć kontener, można użyć polecenia [AzCopy](storage-ref-azcopy-make.md) . Przykłady w tej sekcji tworzą kontener o nazwie `mycontainer` .

|    |     |
|--------|-----------|
| **Składnia** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Przykład** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

Aby uzyskać szczegółowe dokumenty referencyjne, zobacz [AzCopy Make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Przekazywanie plików

Aby przekazać pliki i katalogi z komputera lokalnego, można użyć polecenia [copy AzCopy](storage-ref-azcopy-copy.md) .

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Przekazywanie pliku
> * Przekaż katalog
> * Przekaż zawartość katalogu 
> * Przekazywanie określonych plików

> [!TIP]
> Możesz dostosować operację przekazywania, używając opcjonalnych flag. Oto kilka przykładów.
>
> |Scenariusz|Flaga|
> |---|---|
> |Przekaż pliki jako uzupełnialne lub stronicowe obiekty blob.|**--Typ** = \[ obiektu BLOB BlockBlob \| PageBlob \| AppendBlob\]|
> |Przekaż dane do określonej warstwy dostępu (na przykład warstwy Archiwum).|**--Block-BLOB-warstw** = \[ Brak \| aktywnego \| archiwum chłodnego \|\]|
> 
> Aby uzyskać pełną listę, zobacz [Opcje](storage-ref-azcopy-copy.md#options).

### <a name="upload-a-file"></a>Przekazywanie pliku

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Przykład** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

Możesz również przekazać plik za pomocą symbolu wieloznacznego (*) w dowolnym miejscu w ścieżce lub nazwie pliku. Na przykład: `'C:\myDirectory\*.txt'` , lub `C:\my*\*.txt` .

### <a name="upload-a-directory"></a>Przekaż katalog

Ten przykład kopiuje katalog (i wszystkie pliki w tym katalogu) do kontenera obiektów BLOB. Wynik jest katalogiem w kontenerze o tej samej nazwie.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Aby skopiować do katalogu w kontenerze, należy po prostu określić nazwę tego katalogu w ciągu polecenia.

|    |     |
|--------|-----------|
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

W przypadku określenia nazwy katalogu, który nie istnieje w kontenerze, AzCopy tworzy nowy katalog o tej nazwie.

### <a name="upload-the-contents-of-a-directory"></a>Przekaż zawartość katalogu

Zawartość katalogu można przekazać bez kopiowania samego katalogu zawierającego symbol wieloznaczny (*).

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Przykład** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> Dołącz `--recursive` flagę do przekazywania plików we wszystkich podkatalogach.

### <a name="upload-specific-files"></a>Przekazywanie określonych plików

Można przekazać określone pliki przy użyciu pełnych nazw plików, częściowych nazw z symbolami wieloznacznymi (*) lub przy użyciu dat i godzin.

#### <a name="specify-multiple-complete-file-names"></a>Określ wiele pełnych nazw plików

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z `--include-path` opcją. Oddziel poszczególne nazwy plików przy użyciu średnika ( `;` ).

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

W tym przykładzie AzCopy transferuje `C:\myDirectory\photos` katalog i `C:\myDirectory\documents\myFile.txt` plik. Musisz dołączyć `--recursive` opcję transferu wszystkich plików w `C:\myDirectory\photos` katalogu.

Można również wykluczyć pliki przy użyciu `--exclude-path` opcji. Aby dowiedzieć się więcej, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md) Reference docs.

#### <a name="use-wildcard-characters"></a>Użyj symboli wieloznacznych

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z `--include-pattern` opcją. Określ częściowe nazwy, które zawierają symbole wieloznaczne. Oddziel nazwy przy użyciu semicolin ( `;` ). 

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

Można również wykluczyć pliki przy użyciu `--exclude-pattern` opcji. Aby dowiedzieć się więcej, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md) Reference docs.

`--include-pattern`Opcje i są `--exclude-pattern` stosowane tylko do nazw plików, a nie do ścieżki.  Jeśli chcesz skopiować wszystkie pliki tekstowe, które istnieją w drzewie katalogów, użyj `–recursive` opcji, aby pobrać całe drzewo katalogów, a następnie użyj `–include-pattern` i określ, `*.txt` Aby pobrać wszystkie pliki tekstowe.

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>Przekaż pliki, które zostały zmodyfikowane po dacie i godzinie 

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z `--include-after` opcją. Określ datę i godzinę w formacie ISO-8601 (na przykład: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Przykład** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'` |

Aby uzyskać szczegółowe informacje, zobacz Dokumentacja [AzCopy Copy](storage-ref-azcopy-copy.md) Reference.

## <a name="download-files"></a>Pobieranie plików

Aby pobrać obiekty blob, katalogi i kontenery na komputer lokalny, można użyć polecenia [copy AzCopy](storage-ref-azcopy-copy.md) .

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Pobieranie pliku
> * Pobierz katalog
> * Pobierz zawartość katalogu
> * Pobieranie określonych plików

> [!TIP]
> Możesz dostosować operację pobierania, używając opcjonalnych flag. Oto kilka przykładów.
>
> |Scenariusz|Flaga|
> |---|---|
> |Automatycznie Kompresuj pliki.|**--dekompresowanie**|
> |Określ, jak Szczegółowa ma być liczba wpisów dziennika związanych z kopiowaniem.|**--poziom dziennika** = \[ Ostrzeżenie \| \| Brak informacji o błędzie \|\]|
> |Określ, czy i jak zastąpić pliki i obiekty blob powodujące konflikt w miejscu docelowym.|**--overwrite** = \[ prawdziwy \| \| ifSourceNewer \| monit\]|
> 
> Aby uzyskać pełną listę, zobacz [Opcje](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Jeśli `Content-md5` wartość właściwości obiektu BLOB zawiera skrót, AzCopy oblicza skrót MD5 dla pobranych danych i sprawdza, czy skrót MD5 przechowywany we właściwości obiektu BLOB jest `Content-md5` zgodny z obliczonym skrótem. Jeśli te wartości nie są zgodne, pobieranie nie powiedzie się, o ile nie zastąpisz tego zachowania przez dołączenie `--check-md5=NoCheck` lub `--check-md5=LogOnly` do polecenia copy.

### <a name="download-a-file"></a>Pobieranie pliku

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Pobierz katalog

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Ten przykład powoduje, że katalog o nazwie `C:\myDirectory\myBlobDirectory` zawiera wszystkie pobrane pliki.

### <a name="download-the-contents-of-a-directory"></a>Pobierz zawartość katalogu

Zawartość katalogu można pobrać bez kopiowania samego katalogu zawierającego symbol wieloznaczny (*).

> [!NOTE]
> Obecnie ten scenariusz jest obsługiwany tylko w przypadku kont, które nie mają hierarchicznej przestrzeni nazw.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> Dołącz `--recursive` flagę do pobierania plików we wszystkich podkatalogach.

### <a name="download-specific-files"></a>Pobieranie określonych plików

Określone pliki można pobrać przy użyciu pełnych nazw plików, częściowych nazw z symbolami wieloznacznymi (*) lub przy użyciu dat i godzin. 

#### <a name="specify-multiple-complete-file-names"></a>Określ wiele pełnych nazw plików

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z `--include-path` opcją. Oddziel poszczególne nazwy plików przy użyciu semicolin ( `;` ).

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

W tym przykładzie AzCopy transferuje `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` katalog i `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` plik. Musisz dołączyć `--recursive` opcję transferu wszystkich plików w `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` katalogu.

Można również wykluczyć pliki przy użyciu `--exclude-path` opcji. Aby dowiedzieć się więcej, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md) Reference docs.

#### <a name="use-wildcard-characters"></a>Użyj symboli wieloznacznych

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z `--include-pattern` opcją. Określ częściowe nazwy, które zawierają symbole wieloznaczne. Oddziel nazwy przy użyciu semicolin ( `;` ).

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Można również wykluczyć pliki przy użyciu `--exclude-pattern` opcji. Aby dowiedzieć się więcej, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md) Reference docs.

`--include-pattern`Opcje i są `--exclude-pattern` stosowane tylko do nazw plików, a nie do ścieżki.  Jeśli chcesz skopiować wszystkie pliki tekstowe, które istnieją w drzewie katalogów, użyj `–recursive` opcji, aby pobrać całe drzewo katalogów, a następnie użyj `–include-pattern` i określ, `*.txt` Aby pobrać wszystkie pliki tekstowe.

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>Pobierz pliki, które zostały zmodyfikowane po dacie i godzinie 

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z `--include-after` opcją. Określ datę i godzinę w formacie ISO-8601 (na przykład: `2020-08-19T15:04:00Z` ). 

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

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-directory-path>' --list-of-versions '<list-of-versions-file>'`|
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt' --list-of-versions 'D:\\list-of-versions.txt'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt' --list-of-versions 'D:\\list-of-versions.txt'` |

Nazwa każdego pobranego pliku rozpoczyna się od identyfikatora wersji, po którym następuje nazwa obiektu BLOB. 

## <a name="copy-blobs-between-storage-accounts"></a>Kopiowanie obiektów blob między kontami magazynu

Przy użyciu narzędzie AzCopy możesz kopiować obiekty blob do innych kont magazynu. Operacja kopiowania jest synchroniczna, dlatego gdy polecenie zostanie zwrócone, oznacza to, że pliki zostały skopiowane. 

AzCopy używa [interfejsów API](/rest/api/storageservices/put-page-from-url) [serwer-serwer](/rest/api/storageservices/put-block-from-url) , aby dane były kopiowane bezpośrednio między serwerami magazynu. Te operacje kopiowania nie korzystają z przepustowości sieci komputera. Przepływność tych operacji można zwiększyć, ustawiając wartość `AZCOPY_CONCURRENCY_VALUE` zmiennej środowiskowej. Aby dowiedzieć się więcej, zobacz [Optymalizowanie przepływności](storage-use-azcopy-configure.md#optimize-throughput).

> [!NOTE]
> Ten scenariusz ma następujące ograniczenia w bieżącej wersji.
>
> - Musisz dołączyć token sygnatury dostępu współdzielonego do każdego źródłowego adresu URL. W przypadku podania poświadczeń autoryzacji za pomocą Azure Active Directory (AD) można pominąć token sygnatury dostępu współdzielonego tylko z docelowego adresu URL. Upewnij się, że skonfigurowano odpowiednie role na koncie docelowym. Zobacz [opcję 1: użyj Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory).
>-  Konta magazynu blokowych obiektów BLOB w warstwie Premium nie obsługują warstw dostępu. Pomiń warstwę dostępu obiektu BLOB z operacji kopiowania, ustawiając wartość `s2s-preserve-access-tier` na `false` (na przykład: `--s2s-preserve-access-tier=false` ).

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Kopiowanie obiektu BLOB do innego konta magazynu
> * Kopiowanie katalogu do innego konta magazynu
> * Kopiowanie kontenera na inne konto magazynu
> * Kopiuj wszystkie kontenery, katalogi i pliki na inne konto magazynu

Te przykłady działają również z kontami, które mają hierarchiczną przestrzeń nazw. [Dostęp z użyciem protokołu wieloprotokołowego do Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) umożliwia używanie tej samej składni adresu URL ( `blob.core.windows.net` ) na tych kontach.

> [!TIP]
> Możesz dostosować operację kopiowania przy użyciu opcjonalnych flag. Oto kilka przykładów.
>
> |Scenariusz|Flaga|
> |---|---|
> |Kopiuj obiekty blob jako blokowe, stronicowe lub dołączane obiekty blob.|**--Typ** = \[ obiektu BLOB BlockBlob \| PageBlob \| AppendBlob\]|
> |Kopiuj do określonej warstwy dostępu (na przykład warstwy archiwum).|**--Block-BLOB-warstw** = \[ Brak \| aktywnego \| archiwum chłodnego \|\]|
> |Automatycznie Kompresuj pliki.|**--dekompresowanie** = \[ Wklęśnięcie (gzip) \|\]|
> 
> Aby uzyskać pełną listę, zobacz [Opcje](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-blob-to-another-storage-account"></a>Kopiowanie obiektu BLOB do innego konta magazynu

Użyj tej samej składni adresu URL ( `blob.core.windows.net` ) dla kont, które mają hierarchiczną przestrzeń nazw.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Kopiowanie katalogu do innego konta magazynu

Użyj tej samej składni adresu URL ( `blob.core.windows.net` ) dla kont, które mają hierarchiczną przestrzeń nazw.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Kopiowanie kontenera na inne konto magazynu

Użyj tej samej składni adresu URL ( `blob.core.windows.net` ) dla kont, które mają hierarchiczną przestrzeń nazw.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Kopiuj wszystkie kontenery, katalogi i obiekty blob na inne konto magazynu

Użyj tej samej składni adresu URL ( `blob.core.windows.net` ) dla kont, które mają hierarchiczną przestrzeń nazw.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Synchronizuj pliki

Zawartość lokalnego systemu plików można zsynchronizować z kontenerem obiektów BLOB. Możesz również synchronizować kontenery i katalogi wirtualne ze sobą. Synchronizacja jest jednokierunkowa. Innymi słowy, możesz wybrać, które z tych dwóch punktów końcowych są źródłem, a które są lokalizacją docelową. Synchronizacja używa także serwera do interfejsów API serwera. Przykłady przedstawione w tej sekcji działają również z kontami, które mają hierarchiczną przestrzeń nazw. 

> [!NOTE]
> Bieżąca wersja AzCopy nie jest synchronizowana między innymi źródłami i miejscami docelowymi (na przykład: Magazyn plików lub Amazon Web Services (AWS).

Polecenie [Sync](storage-ref-azcopy-sync.md) porównuje nazwy plików i ostatnio modyfikowane sygnatury czasowe. Ustaw `--delete-destination` opcjonalną flagę na wartość `true` lub, `prompt` Aby usunąć pliki w katalogu docelowym, jeśli te pliki nie znajdują się już w katalogu źródłowym.

Jeśli ustawisz `--delete-destination` flagę na `true` AzCopy usuwa pliki bez wyświetlania monitu. Jeśli chcesz, aby monit pojawił się zanim AzCopy usunie plik, ustaw `--delete-destination` flagę na `prompt` .

> [!NOTE]
> Aby zapobiec przypadkowemu usunięciu, upewnij się, że funkcja [usuwania nietrwałego](../blobs/soft-delete-blob-overview.md) została włączona przed użyciem `--delete-destination=prompt|true` flagi.

> [!TIP]
> Możesz dostosować operację synchronizacji przy użyciu opcjonalnych flag. Oto kilka przykładów.
>
> |Scenariusz|Flaga|
> |---|---|
> |Określ, jak ściśle skróty MD5 mają być weryfikowane podczas pobierania.|**--check-MD5** = \[ NOCHECK \| FailIfDifferent logowanie \| \| FailIfDifferentOrMissing\]|
> |Wyklucz pliki na podstawie wzorca.|**--Exclude-Path**|
> |Określ, jak Szczegółowa ma być liczba wpisów dziennika związanych z synchronizacją.|**--poziom dziennika** = \[ Ostrzeżenie \| \| Brak informacji o błędzie \|\]|
> 
> Aby uzyskać pełną listę, zobacz [Opcje](storage-ref-azcopy-sync.md#options).

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Aktualizowanie kontenera ze zmianami w lokalnym systemie plików

W tym przypadku kontener jest miejscem docelowym, a lokalny system plików jest źródłem. 

|    |     |
|--------|-----------|
| **Składnia** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Przykład** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Aktualizowanie lokalnego systemu plików ze zmianami w kontenerze

W takim przypadku lokalny system plików jest miejscem docelowym, a kontener jest źródłem.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Przykład** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>Aktualizowanie kontenera ze zmianami w innym kontenerze

Pierwszym kontenerem, który pojawia się w tym poleceniu jest źródło. Drugim z nich jest miejsce docelowe.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Przykład** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Aktualizowanie katalogu ze zmianami w katalogu w innym udziale plików

Pierwszym katalogiem, który pojawia się w tym poleceniu jest źródło. Drugim z nich jest miejsce docelowe.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Przykład** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>Następne kroki

Więcej przykładów znajdziesz w jednym z następujących artykułów:

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)

- [Samouczek: Migracja danych lokalnych do magazynu w chmurze za pomocą narzędzia AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)

- [Transferowanie danych za pomocą zasobników AzCopy i Amazon S3](storage-use-azcopy-s3.md)

- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)