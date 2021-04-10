---
title: Przesyłanie danych do lub z Azure Files przy użyciu AzCopy v10 | Microsoft Docs
description: Transferowanie danych za pomocą AzCopy i magazynu plików. AzCopy to narzędzie wiersza polecenia do kopiowania obiektów blob lub plików do lub z konta magazynu. Użyj AzCopy z Azure Files.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: fb229aeb20950be137e0954bc2e20ff0fca8bcb6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728831"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików 

AzCopy to narzędzie wiersza polecenia, które służy do kopiowania plików do lub z konta magazynu. Ten artykuł zawiera przykładowe polecenia, które współpracują z Azure Files.

Przed rozpoczęciem Zobacz artykuł [wprowadzenie do AzCopy](storage-use-azcopy-v10.md) w celu pobrania AzCopy i zapoznaj się z narzędziem.

> [!TIP]
> W przykładach w tym artykule zamieszczono argumenty Path z pojedynczymi cudzysłowami (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

## <a name="create-file-shares"></a>Tworzenie udziałów plików

Możesz użyć [AzCopy](storage-ref-azcopy-make.md) Utwórz polecenie, aby utworzyć udział plików. W przykładzie w tej sekcji jest tworzony udział plików o nazwie `myfileshare` .

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>'` |
| **Przykład** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Aby uzyskać szczegółowe dokumenty referencyjne, zobacz [AzCopy Make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Przekazywanie plików

Aby przekazać pliki i katalogi z komputera lokalnego, można użyć polecenia [copy AzCopy](storage-ref-azcopy-copy.md) .

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Przekazywanie pliku
> * Przekaż katalog
> * Przekaż zawartość katalogu
> * Przekaż określony plik

> [!TIP]
> Możesz dostosować operację przekazywania, używając opcjonalnych flag. Oto kilka przykładów.
>
> |Scenariusz|Flaga|
> |---|---|
> |Kopiuj listy kontroli dostępu (ACL) wraz z plikami.|**--Preserve-SMB-uprawnienia** = \[ prawda \| Fałsz\]|
> |Skopiuj informacje o właściwościach protokołu SMB wraz z plikami.|**--Preserve-SMB-info** = \[ prawda \| Fałsz\]|
> 
> Aby uzyskać pełną listę, zobacz [Opcje](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> AzCopy nie oblicza automatycznie ani nie zapisuje kodu skrótu MD5 pliku. Jeśli chcesz, aby AzCopy to zrobić, Dodaj `--put-md5` flagę do poszczególnych poleceń kopiowania. Dzięki temu, gdy plik zostanie pobrany, AzCopy oblicza skrót MD5 dla pobranych danych i sprawdza, czy skrót MD5 przechowywany we właściwości pliku jest `Content-md5` zgodny z obliczonym skrótem.

### <a name="upload-a-file"></a>Przekazywanie pliku

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'` |
| **Przykład** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Możesz również przekazać plik za pomocą symbolu wieloznacznego (*) w dowolnym miejscu w ścieżce lub nazwie pliku. Na przykład: `'C:\myDirectory\*.txt'` , lub `C:\my*\*.txt` .

### <a name="upload-a-directory"></a>Przekaż katalog

W tym przykładzie skopiowano katalog (oraz wszystkie pliki w tym katalogu) do udziału plików. Rezultatem jest katalog w udziale plików o tej samej nazwie.

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Aby skopiować do katalogu w udziale plików, po prostu podaj nazwę tego katalogu w ciągu polecenia.

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

W przypadku określenia nazwy katalogu, który nie istnieje w udziale plików, AzCopy tworzy nowy katalog o tej nazwie.

### <a name="upload-the-contents-of-a-directory"></a>Przekaż zawartość katalogu

Zawartość katalogu można przekazać bez kopiowania samego katalogu zawierającego symbol wieloznaczny (*).

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>` |
| **Przykład** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Dołącz `--recursive` flagę do przekazywania plików we wszystkich podkatalogach.

### <a name="upload-specific-files"></a>Przekazywanie określonych plików

Można przekazać określone pliki przy użyciu pełnych nazw plików, częściowych nazw z symbolami wieloznacznymi (*) lub przy użyciu dat i godzin.

#### <a name="specify-multiple-complete-file-names"></a>Określ wiele pełnych nazw plików

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z `--include-path` opcją. Oddziel poszczególne nazwy plików przy użyciu średnika ( `;` ).

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>` |
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

W tym przykładzie AzCopy transferuje `C:\myDirectory\photos` katalog i `C:\myDirectory\documents\myFile.txt` plik. Musisz dołączyć `--recursive` opcję transferu wszystkich plików w `C:\myDirectory\photos` katalogu.

Można również wykluczyć pliki przy użyciu `--exclude-path` opcji. Aby dowiedzieć się więcej, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md) Reference docs.

#### <a name="use-wildcard-characters"></a>Użyj symboli wieloznacznych

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z `--include-pattern` opcją. Określ częściowe nazwy, które zawierają symbole wieloznaczne. Oddzielaj nazwy przy użyciu średnika ( `;` ).

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

Można również wykluczyć pliki przy użyciu `--exclude-pattern` opcji. Aby dowiedzieć się więcej, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md) Reference docs.

`--include-pattern`Opcje i są `--exclude-pattern` stosowane tylko do nazw plików, a nie do ścieżki.  Jeśli chcesz skopiować wszystkie pliki tekstowe, które istnieją w drzewie katalogów, użyj `--recursive` opcji, aby pobrać całe drzewo katalogów, a następnie użyj `--include-pattern` i określ, `*.txt` Aby pobrać wszystkie pliki tekstowe.

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>Przekaż pliki, które zostały zmodyfikowane po dacie i godzinie 

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z `--include-after` opcją. Określ datę i godzinę w formacie ISO 8601 (na przykład: `2020-08-19T15:04:00Z` ). 

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Przykład** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-after '2020-08-19T15:04:00Z'` |

Aby uzyskać szczegółowe informacje, zobacz Dokumentacja [AzCopy Copy](storage-ref-azcopy-copy.md) Reference.

## <a name="download-files"></a>Pobieranie plików

Można użyć polecenia [copy AzCopy](storage-ref-azcopy-copy.md) do pobrania plików, katalogów i udziałów plików na komputer lokalny.

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
> |Kopiuj listy kontroli dostępu (ACL) wraz z plikami.|**--Preserve-SMB-uprawnienia** = \[ prawda \| Fałsz\]|
> |Skopiuj informacje o właściwościach protokołu SMB wraz z plikami.|**--Preserve-SMB-info** = \[ prawda \| Fałsz\]|
> |Automatycznie Kompresuj pliki.|**--dekompresowanie**|
> 
> Aby uzyskać pełną listę, zobacz [Opcje](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Jeśli `Content-md5` wartość właściwości pliku zawiera skrót, AzCopy oblicza skrót MD5 dla pobranych danych i sprawdza, czy skrót MD5 przechowywany we właściwości pliku jest `Content-md5` zgodny z obliczonym skrótem. Jeśli te wartości nie są zgodne, pobieranie nie powiedzie się, o ile nie zastąpisz tego zachowania przez dołączenie `--check-md5=NoCheck` lub `--check-md5=LogOnly` do polecenia copy.

### <a name="download-a-file"></a>Pobieranie pliku

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Pobierz katalog

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

Ten przykład powoduje, że katalog o nazwie `C:\myDirectory\myFileShareDirectory` zawiera wszystkie pobrane pliki.

### <a name="download-the-contents-of-a-directory"></a>Pobierz zawartość katalogu

Możesz pobrać zawartość katalogu bez kopiowania samego katalogu, korzystając z symbolu wieloznacznego (*).

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Dołącz `--recursive` flagę do pobierania plików we wszystkich podkatalogach.

### <a name="download-specific-files"></a>Pobieranie określonych plików

Określone pliki można pobrać przy użyciu pełnych nazw plików, częściowych nazw z symbolami wieloznacznymi (*) lub przy użyciu dat i godzin.

#### <a name="specify-multiple-complete-file-names"></a>Określ wiele pełnych nazw plików

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z `--include-path` opcją. Oddziel poszczególne nazwy plików przy użyciu średnika ( `;` ).

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

W tym przykładzie AzCopy transferuje `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` katalog i `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` plik. Dołącz `--recursive` opcję transferu wszystkich plików w `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` katalogu.

Można również wykluczyć pliki przy użyciu `--exclude-path` opcji. Aby dowiedzieć się więcej, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md) Reference docs.

#### <a name="use-wildcard-characters"></a>Użyj symboli wieloznacznych

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z `--include-pattern` opcją. Określ częściowe nazwy, które zawierają symbole wieloznaczne. Oddzielaj nazwy przy użyciu średnika ( `;` ).

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Można również wykluczyć pliki przy użyciu `--exclude-pattern` opcji. Aby dowiedzieć się więcej, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md) Reference docs.

`--include-pattern`Opcje i są `--exclude-pattern` stosowane tylko do nazw plików, a nie do ścieżki.  Jeśli chcesz skopiować wszystkie pliki tekstowe, które istnieją w drzewie katalogów, użyj `--recursive` opcji, aby pobrać całe drzewo katalogów, a następnie użyj `--include-pattern` i określ, `*.txt` Aby pobrać wszystkie pliki tekstowe.

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>Pobierz pliki, które zostały zmodyfikowane po dacie i godzinie 

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z `--include-after` opcją. Określ datę i godzinę w formacie ISO-8601 (na przykład: `2020-08-19T15:04:00Z` ). 

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>/*<SAS-token>' '<local-directory-path>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/*?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory' --include-after '2020-08-19T15:04:00Z'` |


Aby uzyskać szczegółowe informacje, zobacz Dokumentacja [AzCopy Copy](storage-ref-azcopy-copy.md) Reference.

#### <a name="download-from-a-share-snapshot"></a>Pobieranie z migawki udziału

Możesz pobrać określoną wersję pliku lub katalogu, odwołując się do wartości **DateTime** migawki udziału. Aby dowiedzieć się więcej na temat migawek udziałów, zobacz [Omówienie migawek udziałów dla Azure Files](../files/storage-snapshots-files.md). 

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path-or-directory-name><SAS-token>&sharesnapshot=<DateTime-of-snapshot>' '<local-file-or-directory-path>'` |
| **Przykład** (Pobierz plik) | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |
| **Przykład** (Pobierz katalog) | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory'  --recursive`|


## <a name="copy-files-between-storage-accounts"></a>kopiować pliki między kontami magazynu;

Można użyć AzCopy do kopiowania plików na inne konta magazynu. Operacja kopiowania jest synchroniczna, dlatego gdy polecenie zostanie zwrócone, oznacza to, że pliki zostały skopiowane.

AzCopy używa [interfejsów API](/rest/api/storageservices/put-page-from-url) [serwer-serwer](/rest/api/storageservices/put-block-from-url) , aby dane były kopiowane bezpośrednio między serwerami magazynu. Te operacje kopiowania nie korzystają z przepustowości sieci komputera. Przepływność tych operacji można zwiększyć, ustawiając wartość `AZCOPY_CONCURRENCY_VALUE` zmiennej środowiskowej. Aby dowiedzieć się więcej, zobacz [Optymalizowanie przepływności](storage-use-azcopy-configure.md#optimize-throughput).

Możesz również skopiować określone wersje plików, odwołując się do wartości **DateTime** migawki udziału. Aby dowiedzieć się więcej na temat migawek udziałów, zobacz [Omówienie migawek udziałów dla Azure Files](../files/storage-snapshots-files.md). 

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Kopiowanie pliku na inne konto magazynu
> * Kopiowanie katalogu do innego konta magazynu
> * Kopiuj udział plików na inne konto magazynu
> * Kopiowanie wszystkich udziałów plików, katalogów i plików na inne konto usługi Storage

> [!TIP]
> Możesz dostosować operację kopiowania przy użyciu opcjonalnych flag. Oto kilka przykładów.
>
> |Scenariusz|Flaga|
> |---|---|
> |Kopiuj listy kontroli dostępu (ACL) wraz z plikami.|**--Preserve-SMB-uprawnienia** = \[ prawda \| Fałsz\]|
> |Skopiuj informacje o właściwościach protokołu SMB wraz z plikami.|**--Preserve-SMB-info** = \[ prawda \| Fałsz\]|
> 
> Aby uzyskać pełną listę, zobacz [Opcje](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-file-to-another-storage-account"></a>Kopiowanie pliku na inne konto magazynu

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |
| **Przykład** (migawka udziału) | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Kopiowanie katalogu do innego konta magazynu

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |
| **Przykład** (migawka udziału) | `azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |


### <a name="copy-a-file-share-to-another-storage-account"></a>Kopiuj udział plików na inne konto magazynu

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |
| **Przykład** (migawka udziału) | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |


### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Kopiowanie wszystkich udziałów plików, katalogów i plików na inne konto usługi Storage

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |
| **Przykład** (migawka udziału) | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |


## <a name="synchronize-files"></a>Synchronizuj pliki

Zawartość udziału plików można zsynchronizować z innym udziałem plików. Możesz również zsynchronizować zawartość katalogu w udziale plików z zawartością katalogu znajdującego się w innym udziale plików. Synchronizacja jest jednym ze sposobów. Innymi słowy, możesz wybrać, które z tych dwóch punktów końcowych są źródłem, a które są lokalizacją docelową. Synchronizacja używa także serwera do interfejsów API serwera.

> [!NOTE]
> Obecnie ten scenariusz jest obsługiwany tylko w przypadku kont, które nie mają hierarchicznej przestrzeni nazw. Bieżąca wersja programu AzCopy nie jest synchronizowana między Azure Files i Blob Storage.

Polecenie [Sync](storage-ref-azcopy-sync.md) porównuje nazwy plików i ostatnio modyfikowane sygnatury czasowe. Ustaw `--delete-destination` opcjonalną flagę na wartość `true` lub, `prompt` Aby usunąć pliki w katalogu docelowym, jeśli te pliki nie znajdują się już w katalogu źródłowym.

Jeśli ustawisz `--delete-destination` flagę na `true` , AzCopy usuwa pliki bez wyświetlania monitu. Jeśli chcesz, aby monit pojawił się zanim AzCopy usunie plik, ustaw `--delete-destination` flagę na `prompt` .

> [!TIP]
> Możesz dostosować operację synchronizacji przy użyciu opcjonalnych flag. Oto kilka przykładów.
>
> |Scenariusz|Flaga|
> |---|---|
> |Kopiuj listy kontroli dostępu (ACL) wraz z plikami.|**--Preserve-SMB-uprawnienia** = \[ prawda \| Fałsz\]|
> |Skopiuj informacje o właściwościach protokołu SMB wraz z plikami.|**--Preserve-SMB-info** = \[ prawda \| Fałsz\]|
> |Wyklucz pliki na podstawie wzorca.|**--Exclude-Path**|
> |Określ, jak Szczegółowa ma być liczba wpisów dziennika związanych z synchronizacją.|**--poziom dziennika** = \[ Ostrzeżenie \| \| Brak informacji o błędzie \|\]|
> 
> Aby uzyskać pełną listę, zobacz [Opcje](storage-ref-azcopy-sync.md#options).

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Aktualizowanie udziału plików ze zmianami w innym udziale plików

Pierwszym udziałem plików, który pojawia się w tym poleceniu jest źródło. Drugim z nich jest miejsce docelowe.

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Przykład** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Aktualizowanie katalogu ze zmianami w katalogu w innym udziale plików

Pierwszym katalogiem, który pojawia się w tym poleceniu jest źródło. Drugim z nich jest miejsce docelowe.

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive` |
| **Przykład** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-file-share-to-match-the-contents-of-a-share-snapshot"></a>Aktualizowanie udziału plików w celu dopasowania do zawartości migawki udziału

Pierwszym udziałem plików, który pojawia się w tym poleceniu jest źródło. Na końcu identyfikatora URI Dołącz ciąg, `&sharesnapshot=` a po nim wartość **DateTime** migawki. 

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>&sharesnapsot<snapshot-ID>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Przykład** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-03-03T20%3A24%3A13.0000000Z' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

Aby dowiedzieć się więcej na temat migawek udziałów, zobacz [Omówienie migawek udziałów dla Azure Files](../files/storage-snapshots-files.md).

## <a name="next-steps"></a>Następne kroki

Więcej przykładów znajdziesz w jednym z następujących artykułów:

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)

- [Transfer danych](storage-use-azcopy-v10.md#transfer-data)

- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)
