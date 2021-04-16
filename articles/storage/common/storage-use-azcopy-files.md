---
title: Transferowanie danych do lub z Azure Files za pomocą narzędzia AzCopy w wersji 10 | Microsoft Docs
description: Transferowanie danych za pomocą programu AzCopy i magazynu plików. AzCopy to narzędzie wiersza polecenia do kopiowania obiektów blob lub plików do lub z konta magazynu. Użyj programu AzCopy z Azure Files.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 04bf84c5dc8a63929900f1bc95d7274074ef9d5a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498360"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików 

AzCopy to narzędzie wiersza polecenia, które umożliwia kopiowanie plików do lub z konta magazynu. Ten artykuł zawiera przykładowe polecenia, które działają z Azure Files.

Przed rozpoczęciem zapoznaj się z [artykułem Wprowadzenie do narzędzia AzCopy,](storage-use-azcopy-v10.md) aby pobrać narzędzie AzCopy i zapoznać się z narzędziem.

> [!TIP]
> Przykłady w tym artykule otaczają argumenty ścieżki pojedynczymi cudzysłowami (""). Używaj a cudzysłowów pojedynczych we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w cudzysłów (""), a nie pojedynczy cudzysłów ("").

## <a name="create-file-shares"></a>Tworzenie udziałów plików

Aby utworzyć udział plików, możesz użyć polecenia [azcopy make.](storage-ref-azcopy-make.md) Przykład w tej sekcji tworzy udział plików o nazwie `myfileshare` .

**Składnia**

`azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>'`

**Przykład**

```azcopy
azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

Aby uzyskać szczegółowe dokumenty referencyjne, zobacz [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Przekazywanie plików

Możesz użyć polecenia [azcopy copy,](storage-ref-azcopy-copy.md) aby przekazać pliki i katalogi z komputera lokalnego.

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Przekazywanie pliku
> * Przekazywanie katalogu
> * Przekazywanie zawartości katalogu
> * Przekazywanie określonego pliku

> [!TIP]
> Możesz dostosować operację przekazywania przy użyciu opcjonalnych flag. Oto kilka przykładów.
>
> |Scenariusz|Flaga|
> |---|---|
> |Skopiuj listy kontroli dostępu (ACL) wraz z plikami.|**--preserve-smb-permissions** = \[ true \| false\]|
> |Skopiuj informacje o właściwościach SMB wraz z plikami.|**--preserve-smb-info** = \[ true \| false\]|
> 
> Aby uzyskać pełną listę, zobacz [opcje](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Program AzCopy nie oblicza automatycznie i nie przechowuje kodu skrótu md5 pliku. Jeśli chcesz, aby to robił program AzCopy, dołącz `--put-md5` flagę do każdego polecenia kopiowania. Dzięki temu podczas pobierania pliku program AzCopy oblicza skrót MD5 dla pobranych danych i sprawdza, czy skrót MD5 przechowywany we właściwości pliku jest taki sam jak obliczony `Content-md5` skrót.

### <a name="upload-a-file"></a>Przekazywanie pliku

**Składnia**

`azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'`

**Przykład**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

Plik można również przekazać przy użyciu symbolu wieloznacznego (*) w dowolnym miejscu ścieżki pliku lub nazwy pliku. Na przykład: `'C:\myDirectory\*.txt'` lub `C:\my*\*.txt` .

### <a name="upload-a-directory"></a>Przekazywanie katalogu

W tym przykładzie skopiowano katalog (oraz wszystkie pliki w tym katalogu) do udziału plików. Rezultatem jest katalog w udziale plików o tej samej nazwie.

**Składnia**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Przykład**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive
```

Aby skopiować do katalogu w ramach udziału plików, wystarczy określić nazwę tego katalogu w ciągu polecenia.

**Przykład**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive
```

Jeśli określisz nazwę katalogu, który nie istnieje w udziałach plików, program AzCopy utworzy nowy katalog według tej nazwy.

### <a name="upload-the-contents-of-a-directory"></a>Przekazywanie zawartości katalogu

Zawartość katalogu można przekazać bez kopiowania samego katalogu zawierającego przy użyciu symbolu wieloznacznego (*).

**Składnia**

`azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>`

**Przykład**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"
```

> [!NOTE]
> Dołącz `--recursive` flagę , aby przekazać pliki we wszystkich podkataktorach.

### <a name="upload-specific-files"></a>Przekazywanie określonych plików

Określone pliki można przekazać przy użyciu pełnych nazw plików, częściowych nazw ze znakami wieloznacznymi (*), lub przy użyciu dat i godzin.

#### <a name="specify-multiple-complete-file-names"></a>Określanie wielu pełnych nazw plików

Użyj [polecenia azcopy copy](storage-ref-azcopy-copy.md) z `--include-path` opcją . Poszczególne nazwy plików należy oddzielić średnikami `;` ().

**Składnia**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>`

**Przykład**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'
```

W tym przykładzie program AzCopy przesyła `C:\myDirectory\photos` katalog i `C:\myDirectory\documents\myFile.txt` plik. Należy dołączyć opcję `--recursive` transferu wszystkich plików w `C:\myDirectory\photos` katalogu.

Możesz również wykluczyć pliki przy użyciu `--exclude-path` opcji . Aby dowiedzieć się więcej, zobacz [azcopy copy reference](storage-ref-azcopy-copy.md) docs.

#### <a name="use-wildcard-characters"></a>Używanie symboli wieloznacznych

Użyj [polecenia azcopy copy](storage-ref-azcopy-copy.md) z `--include-pattern` opcją . Określ częściowe nazwy, które zawierają symbole wieloznaczne. Oddziel nazwy za pomocą średnika ( `;` ).

**Składnia**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**Przykład**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'
```

Możesz również wykluczyć pliki przy użyciu `--exclude-pattern` opcji . Aby dowiedzieć się więcej, zobacz [azcopy copy reference](storage-ref-azcopy-copy.md) docs .

Opcje `--include-pattern` i dotyczą tylko nazw `--exclude-pattern` plików, a nie ścieżki.  Jeśli chcesz skopiować wszystkie pliki tekstowe, które istnieją w drzewie katalogów, użyj opcji , aby pobrać całe drzewo katalogów, a następnie użyj i określ , aby pobrać `--recursive` `--include-pattern` wszystkie pliki `*.txt` tekstowe.

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>Przekazywanie plików, które zostały zmodyfikowane po dacie i godzinie 

Użyj [polecenia azcopy copy](storage-ref-azcopy-copy.md) z `--include-after` opcją . Określ datę i godzina w formacie ISO 8601 (na przykład: `2020-08-19T15:04:00Z` ). 

**Składnia**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>'  --include-after <Date-Time-in-ISO-8601-format>`

**Przykład**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-after '2020-08-19T15:04:00Z'
```

Aby uzyskać szczegółowe informacje, zobacz dokumenty referencyjne dotyczące kopiowania w programie [azcopy.](storage-ref-azcopy-copy.md)

## <a name="download-files"></a>Pobieranie plików

Możesz użyć polecenia [azcopy copy,](storage-ref-azcopy-copy.md) aby pobrać pliki, katalogi i udziały plików na komputer lokalny.

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Pobieranie pliku
> * Pobieranie katalogu
> * Pobieranie zawartości katalogu
> * Pobieranie określonych plików

> [!TIP]
> Operację pobierania można dostosować przy użyciu opcjonalnych flag. Oto kilka przykładów.
>
> |Scenariusz|Flaga|
> |---|---|
> |Skopiuj listy kontroli dostępu (ACL) wraz z plikami.|**--preserve-smb-permissions** = \[ true \| false\]|
> |Skopiuj informacje o właściwościach SMB wraz z plikami.|**--preserve-smb-info** = \[ true \| false\]|
> |Automatycznie dekompresuj pliki.|**--dekompresji**|
> 
> Aby uzyskać pełną listę, zobacz [opcje](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Jeśli wartość właściwości pliku zawiera skrót, program AzCopy oblicza skrót MD5 dla pobranych danych i sprawdza, czy skrót MD5 przechowywany we właściwości pliku jest taki, jak obliczony `Content-md5` `Content-md5` skrót. Jeśli te wartości nie są zgodne, pobieranie nie powiedzie się, chyba że zastąpisz to zachowanie, dołączając polecenie `--check-md5=NoCheck` `--check-md5=LogOnly` lub do polecenia kopiowania.

### <a name="download-a-file"></a>Pobieranie pliku

**Składnia**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'`

**Przykład**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'
```

### <a name="download-a-directory"></a>Pobieranie katalogu

**Składnia**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive`

**Przykład**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive
```

W tym przykładzie jest to katalog `C:\myDirectory\myFileShareDirectory` o nazwie zawierający wszystkie pobrane pliki.

### <a name="download-the-contents-of-a-directory"></a>Pobieranie zawartości katalogu

Możesz pobrać zawartość katalogu bez kopiowania samego katalogu, korzystając z symbolu wieloznacznego (*).

**Składnia**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'`

**Przykład**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'
```

> [!NOTE]
> Dołącz `--recursive` flagę , aby pobrać pliki we wszystkich katalogach podrzędnych.

### <a name="download-specific-files"></a>Pobieranie określonych plików

Określone pliki można pobrać przy użyciu pełnych nazw plików, częściowych nazw z symbolami wieloznacznymi (*), lub przy użyciu dat i godzin.

#### <a name="specify-multiple-complete-file-names"></a>Określanie wielu pełnych nazw plików

Użyj [polecenia azcopy copy](storage-ref-azcopy-copy.md) z `--include-path` opcją . Poszczególne nazwy plików należy oddzielić średnikami `;` ().

**Składnia**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>`

**Przykład**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive
```

W tym przykładzie program AzCopy przesyła `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` katalog i `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` plik. Dołącz opcję `--recursive` transferu wszystkich plików w `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` katalogu.

Możesz również wykluczyć pliki przy użyciu `--exclude-path` opcji . Aby dowiedzieć się więcej, zobacz [azcopy copy reference](storage-ref-azcopy-copy.md) docs .

#### <a name="use-wildcard-characters"></a>Używanie symboli wieloznacznych

Użyj [polecenia azcopy copy](storage-ref-azcopy-copy.md) z `--include-pattern` opcją . Określ częściowe nazwy, które zawierają symbole wieloznaczne. Oddziel nazwy średnikami ( `;` ).

**Składnia**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**Przykład**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'
```

Możesz również wykluczyć pliki przy użyciu `--exclude-pattern` opcji . Aby dowiedzieć się więcej, zobacz [azcopy copy reference](storage-ref-azcopy-copy.md) docs .

Opcje `--include-pattern` i dotyczą tylko nazw `--exclude-pattern` plików, a nie ścieżki.  Jeśli chcesz skopiować wszystkie pliki tekstowe, które istnieją w drzewie katalogów, użyj opcji , aby pobrać całe drzewo katalogów, a następnie użyj i określ , aby pobrać wszystkie `--recursive` `--include-pattern` pliki `*.txt` tekstowe.

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>Pobieranie plików, które zostały zmodyfikowane po dacie i godzinie 

Użyj [polecenia azcopy copy](storage-ref-azcopy-copy.md) z `--include-after` opcją . Określ datę i godzina w formacie ISO-8601 (na przykład: `2020-08-19T15:04:00Z` ). 

**Składnia**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>/*<SAS-token>' '<local-directory-path>'  --include-after <Date-Time-in-ISO-8601-format>`

**Przykład**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/*?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory' --include-after '2020-08-19T15:04:00Z'
```

Aby uzyskać szczegółowe informacje, zobacz dokumenty referencyjne dotyczące kopiowania w programie [azcopy.](storage-ref-azcopy-copy.md)

#### <a name="download-from-a-share-snapshot"></a>Pobieranie z migawki udziału

Możesz pobrać określoną wersję pliku lub katalogu, odwołując się do wartości **DateTime** migawki udziału. Aby dowiedzieć się więcej na temat migawek udziałów, zobacz [Overview of share snapshots for Azure Files](../files/storage-snapshots-files.md)(Omówienie migawek udziałów dla Azure Files). 

**Składnia**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path-or-directory-name><SAS-token>&sharesnapshot=<DateTime-of-snapshot>' '<local-file-or-directory-path>'`

**Przykład (pobieranie pliku)**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'
```

**Przykład (pobieranie katalogu)**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory'  --recursive
```

## <a name="copy-files-between-storage-accounts"></a>kopiować pliki między kontami magazynu;

Za pomocą programu AzCopy można kopiować pliki na inne konta magazynu. Operacja kopiowania jest synchroniczna, dlatego gdy polecenie zostanie zwrócone, oznacza to, że pliki zostały skopiowane.

Oprogramowanie AzCopy [używa interfejsów](/rest/api/storageservices/put-block-from-url) [API](/rest/api/storageservices/put-page-from-url)między serwerami, więc dane są kopiowane bezpośrednio między serwerami magazynu. Te operacje kopiowania nie używają przepustowości sieci komputera. Przepływność tych operacji można zwiększyć, ustawiając wartość zmiennej `AZCOPY_CONCURRENCY_VALUE` środowiskowej. Aby dowiedzieć się więcej, zobacz [Zwiększanie współbieżności](storage-use-azcopy-optimize.md#increase-concurrency).

Możesz również skopiować określone wersje plików, odwołując się do wartości **DateTime** migawki udziału. Aby dowiedzieć się więcej na temat migawek udziałów, zobacz [Overview of share snapshots for Azure Files](../files/storage-snapshots-files.md)(Omówienie migawek udziałów dla Azure Files). 

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Kopiowanie pliku na inne konto magazynu
> * Kopiowanie katalogu na inne konto magazynu
> * Kopiowanie udziału plików na inne konto magazynu
> * Kopiowanie wszystkich udziałów plików, katalogów i plików na inne konto usługi Storage

> [!TIP]
> Operację kopiowania można dostosować przy użyciu opcjonalnych flag. Oto kilka przykładów.
>
> |Scenariusz|Flaga|
> |---|---|
> |Skopiuj listy kontroli dostępu (ACL) wraz z plikami.|**--preserve-smb-permissions** = \[ true \| false\]|
> |Skopiuj informacje o właściwościach SMB wraz z plikami.|**--preserve-smb-info** = \[ true \| false\]|
> 
> Aby uzyskać pełną listę, zobacz [opcje](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-file-to-another-storage-account"></a>Kopiowanie pliku na inne konto magazynu

**Składnia**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'`

**Przykład**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'
```

**Przykład (udostępnianie migawki)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'
```

### <a name="copy-a-directory-to-another-storage-account"></a>Kopiowanie katalogu na inne konto magazynu

**Składnia**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Przykład**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

**Przykład (udostępnianie migawki)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="copy-a-file-share-to-another-storage-account"></a>Kopiowanie udziału plików na inne konto magazynu

**Składnia**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Przykład**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D
```

**Przykład (udostępnianie migawki)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Kopiowanie wszystkich udziałów plików, katalogów i plików na inne konto usługi Storage

**Składnia**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'`

**Przykład**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

**Przykład (udostępnianie migawki)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

## <a name="synchronize-files"></a>Synchronizowanie plików

Zawartość udziału plików można zsynchronizować z innym udziałem plików. Można również synchronizować zawartość katalogu w udziału plików z zawartością katalogu, który znajduje się w innym udział plików. Synchronizacja jest jednym ze sposobów. Innymi słowy, możesz wybrać, który z tych dwóch punktów końcowych jest źródłem, a który jest miejscem docelowym. Synchronizacja korzysta również z interfejsów API między serwerami.

> [!NOTE]
> Obecnie ten scenariusz jest obsługiwany tylko w przypadku kont, które nie mają hierarchicznej przestrzeni nazw. Bieżąca wersja programu AzCopy nie jest synchronizowana między Azure Files i Blob Storage.

Polecenie [synchronizacji](storage-ref-azcopy-sync.md) porównuje nazwy plików i ostatnio zmodyfikowane znaczniki czasu. Ustaw opcjonalną flagę na wartość lub , aby usunąć pliki w katalogu docelowym, jeśli te pliki już nie `--delete-destination` `true` istnieją w katalogu `prompt` źródłowym.

Jeśli ustawisz `--delete-destination` flagę `true` na wartość , program AzCopy usunie pliki bez monitowania. Jeśli chcesz, aby przed usunięciem pliku przez program AzCopy był wyświetlany monit, ustaw `--delete-destination` flagę na `prompt` wartość .

> [!TIP]
> Możesz dostosować operację synchronizacji przy użyciu opcjonalnych flag. Oto kilka przykładów.
>
> |Scenariusz|Flaga|
> |---|---|
> |Skopiuj listy kontroli dostępu (ACL) wraz z plikami.|**--preserve-smb-permissions** = \[ true \| false\]|
> |Skopiuj informacje o właściwościach SMB wraz z plikami.|**--preserve-smb-info** = \[ true \| false\]|
> |Wykluczanie plików na podstawie wzorca.|**--exclude-path**|
> |Określ, jak szczegółowe mają być wpisy dziennika związane z synchronizacją.|**--log-level** = \[ OSTRZEŻENIE \| O \| BŁĘDZIE : \| BRAK\]|
> 
> Aby uzyskać pełną listę, zobacz [opcje](storage-ref-azcopy-sync.md#options).

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Aktualizowanie udziału plików przy użyciu zmian do innego udziału plików

Pierwszy udział plików wyświetlany w tym poleceniu jest źródłem. Drugi to miejsce docelowe.

**Składnia**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Przykład**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Aktualizowanie katalogu za pomocą zmian w katalogu w innym udziałze plików

Pierwszy katalog wyświetlany w tym poleceniu jest źródłem. Drugi to miejsce docelowe.

**Składnia**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive`

**Przykład**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="update-a-file-share-to-match-the-contents-of-a-share-snapshot"></a>Aktualizowanie udziału plików w celu dopasowania do zawartości migawki udziału

Pierwszy udział plików wyświetlany w tym poleceniu jest źródłem. Na końcu wartości URI dołącz ciąg, po `&sharesnapshot=` którym następuje **wartość DateTime** migawki.

**Składnia**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>&sharesnapsot<snapshot-ID>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Przykład**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-03-03T20%3A24%3A13.0000000Z' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
``` 

Aby dowiedzieć się więcej na temat migawek udziałów, zobacz [Overview of share snapshots for Azure Files](../files/storage-snapshots-files.md)(Omówienie migawek udziałów dla Azure Files ).

## <a name="next-steps"></a>Następne kroki

Więcej przykładów można znaleźć w jednym z tych artykułów:

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Transfer danych](storage-use-azcopy-v10.md#transfer-data)

Zobacz następujące artykuły, aby skonfigurować ustawienia, zoptymalizować wydajność i rozwiązać problemy:

- [Ustawienia konfiguracji programu AzCopy](storage-ref-azcopy-configuration-settings.md)
- [Optymalizowanie wydajności programu AzCopy](storage-use-azcopy-optimize.md)
- [Rozwiązywanie problemów z programem AzCopy w wersji 10 w usłudze Azure Storage przy użyciu plików dziennika](storage-use-azcopy-configure.md)

