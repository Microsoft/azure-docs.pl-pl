---
title: Transferowanie danych do lub z usługi Azure Blob Storage za pomocą AzCopy v10 | Microsoft Docs
description: Ten artykuł zawiera kolekcję przykładowych poleceń AzCopy, które pomagają w tworzeniu kontenerów, kopiowaniu plików i synchronizowaniu katalogów między lokalnymi systemami plików i kontenerami.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: b676c2647fbf7c93d271e1d7f68653452125e39b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82137199"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB

AzCopy to narzędzie wiersza polecenia, za pomocą którego można kopiować dane do, z lub między kontami magazynu. Ten artykuł zawiera przykładowe polecenia, które działają z usługą BLOB Storage.

> [!TIP]
> W przykładach w tym artykule zamieszczono argumenty Path z pojedynczymi cudzysłowami (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd. exe). Jeśli używasz powłoki poleceń systemu Windows (cmd. exe), ujmij argumenty ścieżki z podwójnymi cudzysłowami ("") zamiast pojedynczego cudzysłowu ("").

## <a name="get-started"></a>Wprowadzenie

Zapoznaj się z artykułem [wprowadzenie do AzCopy](storage-use-azcopy-v10.md) w celu pobrania AzCopy i Dowiedz się więcej na temat sposobów dostarczania poświadczeń autoryzacji do usługi magazynu.

> [!NOTE]
> W przykładach w tym artykule przyjęto założenie, że tożsamość została uwierzytelniona `AzCopy login` przy użyciu polecenia. AzCopy następnie używa konta usługi Azure AD do autoryzacji dostępu do danych w usłudze BLOB Storage.
>
> Jeśli wolisz używać tokenu SAS do autoryzacji dostępu do danych obiektów blob, możesz dołączyć ten token do adresu URL zasobu w każdym poleceniu AzCopy.
>
> Na przykład: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Tworzenie kontenera

Aby utworzyć kontener, można użyć polecenia [AzCopy](storage-ref-azcopy-make.md) . Przykłady w tej sekcji tworzą kontener o nazwie `mycontainer`.

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Przyklad** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
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
> |Przekazywanie plików jako dołączanie obiektów blob lub stronicowych obiektów BLOB.|**--Typ**=\[obiektu BLOB BlockBlob\|PageBlob\|AppendBlob\]|
> |Przekaż do określonej warstwy dostępu (na przykład warstwy archiwum).|**--Block-BLOB-warstwy**=\[none\|Hot\|\|archiwalny\]|
> 
> Aby uzyskać pełną listę, zobacz [Opcje](storage-ref-azcopy-copy.md#options).

### <a name="upload-a-file"></a>Przekazywanie pliku

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Przyklad** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

Możesz również przekazać plik za pomocą symbolu wieloznacznego (*) w dowolnym miejscu w ścieżce lub nazwie pliku. Na przykład: `'C:\myDirectory\*.txt'`, lub `C:\my*\*.txt`.

### <a name="upload-a-directory"></a>Przekaż katalog

Ten przykład kopiuje katalog (i wszystkie pliki w tym katalogu) do kontenera obiektów BLOB. Wynik jest katalogiem w kontenerze o tej samej nazwie.

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Przyklad** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Aby skopiować do katalogu w kontenerze, należy po prostu określić nazwę tego katalogu w ciągu polecenia.

|    |     |
|--------|-----------|
| **Przyklad** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

W przypadku określenia nazwy katalogu, który nie istnieje w kontenerze, AzCopy tworzy nowy katalog o tej nazwie.

### <a name="upload-the-contents-of-a-directory"></a>Przekaż zawartość katalogu

Zawartość katalogu można przekazać bez kopiowania samego katalogu zawierającego symbol wieloznaczny (*).

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Przyklad** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> Dołącz `--recursive` flagę do przekazywania plików we wszystkich podkatalogach.

### <a name="upload-specific-files"></a>Przekazywanie określonych plików

Możesz określić pełne nazwy plików lub użyć częściowych nazw z symbolami wieloznacznymi (*).

#### <a name="specify-multiple-complete-file-names"></a>Określ wiele pełnych nazw plików

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z `--include-path` opcją. Oddziel poszczególne nazwy plików przy użyciu średnika`;`().

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Przyklad** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

W tym przykładzie AzCopy transferuje `C:\myDirectory\photos` katalog i `C:\myDirectory\documents\myFile.txt` plik. Musisz dołączyć `--recursive` opcję transferu wszystkich plików w `C:\myDirectory\photos` katalogu.

Można również wykluczyć pliki przy użyciu `--exclude-path` opcji. Aby dowiedzieć się więcej, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md) Reference docs.

#### <a name="use-wildcard-characters"></a>Użyj symboli wieloznacznych

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z `--include-pattern` opcją. Określ częściowe nazwy, które zawierają symbole wieloznaczne. Oddziel nazwy przy użyciu semicolin (`;`). 

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Przyklad** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

Można również wykluczyć pliki przy użyciu `--exclude-pattern` opcji. Aby dowiedzieć się więcej, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md) Reference docs.

Opcje `--include-pattern` i `--exclude-pattern` są stosowane tylko do nazw plików, a nie do ścieżki.  Jeśli chcesz skopiować wszystkie pliki tekstowe, które istnieją w drzewie katalogów, użyj `–recursive` opcji, aby pobrać całe drzewo katalogów, a następnie użyj `–include-pattern` i określ `*.txt` , aby pobrać wszystkie pliki tekstowe.

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
> |Określ, jak Szczegółowa ma być liczba wpisów dziennika związanych z kopiowaniem.|**--poziom**=\[rejestrowania ostrzeżeń\|informacje\|\|o błędzie brak\]|
> |Określ, czy i jak zastąpić pliki i obiekty blob powodujące konflikt w miejscu docelowym.|**--Zastąp**=\[wartość\|true\|\|false ifSourceNewer Prompt\]|
> 
> Aby uzyskać pełną listę, zobacz [Opcje](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Jeśli wartość `Content-md5` właściwości obiektu BLOB zawiera skrót, AzCopy oblicza skrót MD5 dla pobranych danych i sprawdza, czy skrót MD5 przechowywany we `Content-md5` właściwości obiektu BLOB jest zgodny z obliczonym skrótem. Jeśli te wartości nie są zgodne, pobieranie nie powiedzie się, o ile nie zastąpisz tego zachowania przez dołączenie `--check-md5=NoCheck` lub `--check-md5=LogOnly` do polecenia copy.

### <a name="download-a-file"></a>Pobieranie pliku

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Przyklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Pobierz katalog

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Przyklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Ten przykład powoduje, że katalog o `C:\myDirectory\myBlobDirectory` nazwie zawiera wszystkie pobrane pliki.

### <a name="download-the-contents-of-a-directory"></a>Pobierz zawartość katalogu

Zawartość katalogu można pobrać bez kopiowania samego katalogu zawierającego symbol wieloznaczny (*).

> [!NOTE]
> Obecnie ten scenariusz jest obsługiwany tylko w przypadku kont, które nie mają hierarchicznej przestrzeni nazw.

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Przyklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> Dołącz `--recursive` flagę do pobierania plików we wszystkich podkatalogach.

### <a name="download-specific-files"></a>Pobieranie określonych plików

Możesz określić pełne nazwy plików lub użyć częściowych nazw z symbolami wieloznacznymi (*).

#### <a name="specify-multiple-complete-file-names"></a>Określ wiele pełnych nazw plików

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z `--include-path` opcją. Oddziel poszczególne nazwy plików przy użyciu semicolin (`;`).

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Przyklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

W tym przykładzie AzCopy transferuje `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` katalog i `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` plik. Musisz dołączyć `--recursive` opcję transferu wszystkich plików w `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` katalogu.

Można również wykluczyć pliki przy użyciu `--exclude-path` opcji. Aby dowiedzieć się więcej, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md) Reference docs.

#### <a name="use-wildcard-characters"></a>Użyj symboli wieloznacznych

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z `--include-pattern` opcją. Określ częściowe nazwy, które zawierają symbole wieloznaczne. Oddziel nazwy przy użyciu semicolin (`;`).

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Przyklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Można również wykluczyć pliki przy użyciu `--exclude-pattern` opcji. Aby dowiedzieć się więcej, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md) Reference docs.

Opcje `--include-pattern` i `--exclude-pattern` są stosowane tylko do nazw plików, a nie do ścieżki.  Jeśli chcesz skopiować wszystkie pliki tekstowe, które istnieją w drzewie katalogów, użyj `–recursive` opcji, aby pobrać całe drzewo katalogów, a następnie użyj `–include-pattern` i określ `*.txt` , aby pobrać wszystkie pliki tekstowe.

## <a name="copy-blobs-between-storage-accounts"></a>Kopiowanie obiektów blob między kontami magazynu

Możesz użyć AzCopy, aby skopiować obiekty blob do innych kont magazynu. Operacja kopiowania jest synchroniczna, dlatego gdy polecenie zwróci wartość, która wskazuje, że wszystkie pliki zostały skopiowane. 

AzCopy używa [interfejsów API](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url) [serwer-serwer](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) , aby dane były kopiowane bezpośrednio między serwerami magazynu. Te operacje kopiowania nie korzystają z przepustowości sieci komputera. Przepływność tych operacji można zwiększyć, ustawiając wartość zmiennej `AZCOPY_CONCURRENCY_VALUE` środowiskowej. Aby dowiedzieć się więcej, zobacz [Optymalizowanie przepływności](storage-use-azcopy-configure.md#optimize-throughput).

> [!NOTE]
> Ten scenariusz ma następujące ograniczenia w bieżącej wersji.
>
> - Musisz dołączyć token sygnatury dostępu współdzielonego do każdego źródłowego adresu URL. W przypadku podania poświadczeń autoryzacji za pomocą Azure Active Directory (AD) można pominąć token sygnatury dostępu współdzielonego tylko z docelowego adresu URL.
>-  Konta magazynu blokowych obiektów BLOB w warstwie Premium nie obsługują warstw dostępu. Pomiń warstwę dostępu obiektu BLOB z operacji kopiowania, ustawiając wartość `s2s-preserve-access-tier` na `false` (na przykład: `--s2s-preserve-access-tier=false`).

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Kopiowanie obiektu BLOB do innego konta magazynu
> * Kopiowanie katalogu do innego konta magazynu
> * Kopiowanie kontenera na inne konto magazynu
> * Kopiuj wszystkie kontenery, katalogi i pliki na inne konto magazynu

Te przykłady działają również z kontami, które mają hierarchiczną przestrzeń nazw. [Dostęp z użyciem protokołu wieloprotokołowego do Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) umożliwia używanie tej samej składni adresu`blob.core.windows.net`URL () na tych kontach.

> [!TIP]
> Możesz dostosować operację kopiowania przy użyciu opcjonalnych flag. Oto kilka przykładów.
>
> |Scenariusz|Flaga|
> |---|---|
> |Kopiuj pliki jako Dołącz obiekty blob lub stronicowe obiekty blob.|**--Typ**=\[obiektu BLOB BlockBlob\|PageBlob\|AppendBlob\]|
> |Kopiuj do określonej warstwy dostępu (na przykład warstwy archiwum).|**--Block-BLOB-warstwy**=\[none\|Hot\|\|archiwalny\]|
> |Automatycznie Kompresuj pliki.|**--Dekompresuj**=\[korektę gzip\|\]|
> 
> Aby uzyskać pełną listę, zobacz [Opcje](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-blob-to-another-storage-account"></a>Kopiowanie obiektu BLOB do innego konta magazynu

Użyj tej samej składni adresu URL`blob.core.windows.net`() dla kont, które mają hierarchiczną przestrzeń nazw.

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Przyklad** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Kopiowanie katalogu do innego konta magazynu

Użyj tej samej składni adresu URL`blob.core.windows.net`() dla kont, które mają hierarchiczną przestrzeń nazw.

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Przyklad** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Kopiowanie kontenera na inne konto magazynu

Użyj tej samej składni adresu URL`blob.core.windows.net`() dla kont, które mają hierarchiczną przestrzeń nazw.

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Przyklad** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Kopiuj wszystkie kontenery, katalogi i obiekty blob na inne konto magazynu

Użyj tej samej składni adresu URL`blob.core.windows.net`() dla kont, które mają hierarchiczną przestrzeń nazw.

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Przyklad** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Synchronizuj pliki

Zawartość lokalnego systemu plików można zsynchronizować z kontenerem obiektów BLOB. Możesz również synchronizować kontenery i katalogi wirtualne ze sobą. Synchronizacja jest jednokierunkowa. Innymi słowy, możesz wybrać, które z tych dwóch punktów końcowych są źródłem, a które są lokalizacją docelową. Synchronizacja używa także serwera do interfejsów API serwera. Przykłady przedstawione w tej sekcji działają również z kontami, które mają hierarchiczną przestrzeń nazw. 

> [!NOTE]
> Bieżąca wersja AzCopy nie jest synchronizowana między innymi źródłami i miejscami docelowymi (na przykład: Magazyn plików lub Amazon Web Services (AWS).

Polecenie [Sync](storage-ref-azcopy-sync.md) porównuje nazwy plików i ostatnio modyfikowane sygnatury czasowe. Ustaw `--delete-destination` opcjonalną flagę na wartość `true` lub `prompt` , aby usunąć pliki w katalogu docelowym, jeśli te pliki nie znajdują się już w katalogu źródłowym.

Jeśli ustawisz `--delete-destination` flagę na `true` AzCopy usuwa pliki bez wyświetlania monitu. Jeśli chcesz, aby monit pojawił się zanim AzCopy usunie plik, ustaw `--delete-destination` flagę na. `prompt`

> [!NOTE]
> Aby zapobiec przypadkowemu usunięciu, upewnij się, że funkcja [usuwania nietrwałego](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) została włączona `--delete-destination=prompt|true` przed użyciem flagi.

> [!TIP]
> Możesz dostosować operację synchronizacji przy użyciu opcjonalnych flag. Oto kilka przykładów.
>
> |Scenariusz|Flaga|
> |---|---|
> |Określ, jak ściśle skróty MD5 mają być weryfikowane podczas pobierania.|**--Check-MD5**=\[NOCHECK\|Logon\|FailIfDifferent\|FailIfDifferentOrMissing\]|
> |Wyklucz pliki na podstawie wzorca.|**--Exclude-Path**|
> |Określ, jak Szczegółowa ma być liczba wpisów dziennika związanych z synchronizacją.|**--poziom**=\[rejestrowania ostrzeżeń\|informacje\|\|o błędzie brak\]|
> 
> Aby uzyskać pełną listę, zobacz [Opcje](storage-ref-azcopy-sync.md#options).

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Aktualizowanie kontenera ze zmianami w lokalnym systemie plików

W takim przypadku kontener jest miejscem docelowym, a lokalny system plików jest źródłem. 

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Przyklad** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Aktualizowanie lokalnego systemu plików przy użyciu zmian w kontenerze

W takim przypadku lokalny system plików jest miejscem docelowym, a kontener jest źródłem.

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Przyklad** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>Aktualizowanie kontenera ze zmianami w innym kontenerze

Pierwszym kontenerem, który pojawia się w tym poleceniu jest źródło. Drugim z nich jest miejsce docelowe.

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Przyklad** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Aktualizowanie katalogu ze zmianami w katalogu w innym udziale plików

Pierwszym katalogiem, który pojawia się w tym poleceniu jest źródło. Drugim z nich jest miejsce docelowe.

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Przyklad** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>Następne kroki

Więcej przykładów znajdziesz w jednym z następujących artykułów:

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)

- [Samouczek: Migracja danych lokalnych do magazynu w chmurze za pomocą narzędzia AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Transferowanie danych za pomocą AzCopy i magazynu plików](storage-use-azcopy-files.md)

- [Transferowanie danych za pomocą zasobników AzCopy i Amazon S3](storage-use-azcopy-s3.md)

- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)
