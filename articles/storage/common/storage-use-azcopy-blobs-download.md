---
title: Pobieranie obiektów blob z usługi Azure Blob Storage przy użyciu narzędzia AzCopy w wersji 10 | Microsoft Docs
description: Ten artykuł zawiera kolekcję przykładowych poleceń narzędzia AzCopy, które ułatwiają pobieranie obiektów blob z usługi Azure Blob Storage.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 56bb36cfda9d0cf1a8882950c862a73ad1e77898
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502950"
---
# <a name="download-blobs-from-azure-blob-storage-by-using-azcopy"></a>Pobieranie obiektów blob z usługi Azure Blob Storage przy użyciu narzędzia AzCopy

Obiekty blob i katalogi można pobrać z usługi Blob Storage za pomocą narzędzia wiersza polecenia AzCopy w wersji 10. 

Aby wyświetlić przykłady innych typów zadań, takich jak przekazywanie plików, synchronizowanie z usługą Blob Storage [](#next-steps) lub kopiowanie obiektów blob między kontami, zobacz linki przedstawione w sekcji Następne kroki tego artykułu.

## <a name="get-started"></a>Rozpoczęcie pracy

Zobacz artykuł [Wprowadzenie do programu AzCopy,](storage-use-azcopy-v10.md) aby pobrać program AzCopy i poznać sposoby poświadczeń autoryzacji w usłudze magazynu.

> [!NOTE] 
> W przykładach w tym artykule założono, że podano poświadczenia autoryzacji przy użyciu Azure Active Directory (Azure AD).
>
> Jeśli wolisz użyć tokenu SAS do autoryzowania dostępu do danych obiektów blob, możesz dołączyć ten token do adresu URL zasobu w każdym poleceniu programu AzCopy. Na przykład: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="download-a-blob"></a>Pobieranie obiektu blob

Pobierz obiekt blob za pomocą [polecenia azcopy copy.](storage-ref-azcopy-copy.md)

> [!TIP]
> W tym przykładzie argumenty ścieżki są ujęte w a cudzysłów pojedynczy (""). Używaj a cudzysłowów pojedynczych we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w cudzysłów (""), a nie pojedynczy cudzysłów ("").

**Składnia**

``azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'``

**Przykład**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'
```

**Przykład (hierarchiczna przestrzeń nazw)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'
```

> [!NOTE]
> Jeśli wartość właściwości obiektu blob zawiera skrót, program AzCopy oblicza skrót MD5 dla pobranych danych i sprawdza, czy skrót MD5 przechowywany we właściwości obiektu blob jest taki, jak obliczony `Content-md5` `Content-md5` skrót. Jeśli te wartości nie są zgodne, pobieranie nie powiedzie się, chyba że zastąpisz to zachowanie, dołączając polecenie `--check-md5=NoCheck` `--check-md5=LogOnly` lub do polecenia kopiowania.

## <a name="download-a-directory"></a>Pobieranie katalogu

Pobierz katalog za pomocą [polecenia azcopy copy.](storage-ref-azcopy-copy.md)

> [!TIP]
> W tym przykładzie argumenty ścieżki są ujęte w a cudzysłów pojedynczy (""). Używaj a cudzysłowów pojedynczych we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójny cudzysłów ("") zamiast cudzysłowów pojedynczych ("").

**Składnia**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive`

**Przykład**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive
```

**Przykład (hierarchiczna przestrzeń nazw)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive
```

W tym przykładzie jest to katalog o `C:\myDirectory\myBlobDirectory` nazwie zawierający wszystkie pobrane obiekty blob.

## <a name="download-directory-contents"></a>Pobieranie zawartości katalogu

Możesz pobrać zawartość katalogu bez kopiowania samego katalogu, korzystając z symbolu wieloznacznego (*).

> [!TIP]
> W tym przykładzie argumenty ścieżki są ujęte w a cudzysłów pojedynczy (""). Używaj a cudzysłowów pojedynczych we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w cudzysłów ("") zamiast a cudzysłowów pojedynczych ("").

> [!NOTE]
> Obecnie ten scenariusz jest obsługiwany tylko w przypadku kont, które nie mają hierarchicznej przestrzeni nazw.

**Składnia**

`azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'`

**Przykład**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'
```

Dołącz `--recursive` flagę , aby pobrać pliki we wszystkich podkatalogach.

## <a name="download-specific-blobs"></a>Pobieranie określonych obiektów blob

Określone obiekty blob można pobrać przy użyciu pełnych nazw plików, częściowych nazw z symbolami wieloznacznymi (*), lub przy użyciu dat i godzin. 

> [!TIP]
> W tych przykładach argumenty ścieżki są ujęte w a cudzysłów pojedynczy (""). Używaj a cudzysłowów pojedynczych we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w cudzysłów ("") zamiast a cudzysłowów pojedynczych ("").

#### <a name="specify-multiple-complete-blob-names"></a>Określanie wielu pełnych nazw obiektów blob

Użyj [polecenia azcopy copy](storage-ref-azcopy-copy.md) z `--include-path` opcją . Poszczególne nazwy obiektów blob należy oddzielić za pomocą średnika ( `;` ).

**Składnia**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>`

**Przykład**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive
```

**Przykład (hierarchiczna przestrzeń nazw)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive
```

W tym przykładzie program AzCopy przesyła `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` katalog i `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` plik. Dołącz opcję `--recursive` przeniesienia wszystkich obiektów blob w `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` katalogu.

Obiekty blob można również wykluczyć przy użyciu `--exclude-path` opcji . Aby dowiedzieć się więcej, zobacz [dokumenty referencyjne kopiowania azcopy.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Używanie symboli wieloznacznych

Użyj [polecenia azcopy copy](storage-ref-azcopy-copy.md) z `--include-pattern` opcją . Określ częściowe nazwy, które zawierają symbole wieloznaczne. Oddziel nazwy za pomocą średnika ( `;` ).

**Składnia**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**Przykład**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'
```

**Przykład (hierarchiczna przestrzeń nazw)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'
```

Obiekty blob można również wykluczyć przy użyciu `--exclude-pattern` opcji . Aby dowiedzieć się więcej, zobacz [dokumenty referencyjne kopiowania azcopy.](storage-ref-azcopy-copy.md)

Opcje `--include-pattern` i dotyczą tylko nazw obiektów `--exclude-pattern` blob, a nie ścieżki.  Jeśli chcesz skopiować wszystkie pliki tekstowe (obiekty blob) istniejące w drzewie katalogów, użyj opcji , aby pobrać całe drzewo katalogów, a następnie użyj wartości i , aby pobrać wszystkie pliki `–recursive` `–include-pattern` `*.txt` tekstowe.

#### <a name="download-blobs-that-were-modified-before-or-after-a-date-and-time"></a>Pobieranie obiektów blob, które zostały zmodyfikowane przed datą i godziną lub po tej dacie 

Użyj [polecenia azcopy copy](storage-ref-azcopy-copy.md) z `--include-before` opcją `--include-after` lub . Określ datę i czas w formacie ISO-8601 (na przykład: `2020-08-19T15:04:00Z` ). 

Poniższe przykłady pobierają pliki, które zostały zmodyfikowane w określonym dniu lub później.

**Składnia**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>`

**Przykład**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'
```

**Przykład (hierarchiczna przestrzeń nazw)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'
```

Aby uzyskać szczegółowe informacje, zobacz dokumenty referencyjne dotyczące kopiowania w programie [azcopy.](storage-ref-azcopy-copy.md)

#### <a name="download-previous-versions-of-a-blob"></a>Pobieranie poprzednich wersji obiektu blob

Jeśli włączono obsługę wersji [obiektów blob,](../blobs/versioning-enable.md)możesz pobrać co najmniej jedną poprzednią wersję obiektu blob. 

Najpierw utwórz plik tekstowy zawierający listę [identyfikatorów wersji](../blobs/versioning-overview.md). Każdy identyfikator wersji musi znajdować się w osobnym wierszu. Na przykład: 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

Następnie użyj [polecenia azcopy copy](storage-ref-azcopy-copy.md) z `--list-of-versions` opcją . Określ lokalizację pliku tekstowego zawierającego listę wersji (na przykład: `D:\\list-of-versions.txt` ).  

#### <a name="download-a-blob-snapshot"></a>Pobieranie migawki obiektu blob

Migawkę obiektu [blob można pobrać,](../blobs/snapshots-overview.md) odwołując się do **wartości DateTime** migawki obiektu blob. 

**Składnia**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>?sharesnapshot=<DateTime-of-snapshot>' '<local-file-path>'`

**Przykład**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'
```

**Przykład (hierarchiczna przestrzeń nazw)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'
```

> [!NOTE]
> Jeśli używasz tokenu SAS do autoryzowania dostępu do danych obiektów blob, dołącz migawkę **DateTime po** tokenie SAS. Na przykład: `'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z'`.

## <a name="download-with-optional-flags"></a>Pobieranie z opcjonalnymi flagami

Możesz dostosować operację pobierania przy użyciu opcjonalnych flag. Oto kilka przykładów.

|Scenariusz|Flaga|
|---|---|
|Automatyczne dekompresowanie plików.|**— dekompresja**|
|Określ, jak szczegółowe mają być wpisy dziennika dotyczące kopiowania.|**--poziom dziennika** = \[ OSTRZEŻENIE \| O \| BŁĘDZIE: \| BRAK\]|
|Określ, czy i jak zastąpić pliki i obiekty blob powodujące konflikt w miejscu docelowym.|**--overwrite** = \[ true \| false \| ifSourceNewer \| prompt\]|

Aby uzyskać pełną listę, zobacz [opcje](storage-ref-azcopy-copy.md#options).

## <a name="next-steps"></a>Następne kroki

Więcej przykładów można znaleźć w tych artykułach:

- [Przykłady: Upload](storage-use-azcopy-blobs-upload.md)
- [Przykłady: Kopiowanie między kontem](storage-use-azcopy-blobs-copy.md)
- [Przykłady: Synchronizowanie](storage-use-azcopy-blobs-synchronize.md)
- [Przykłady: Zasobniki usługi Amazon S3](storage-use-azcopy-s3.md)
- [Przykłady: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Przykłady: Azure Files](storage-use-azcopy-files.md)
- [Samouczek: Migracja danych lokalnych do magazynu w chmurze za pomocą narzędzia AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Zapoznaj się z tymi artykułami, aby skonfigurować ustawienia, zoptymalizować wydajność i rozwiązać problemy:

- [Ustawienia konfiguracji programu AzCopy](storage-ref-azcopy-configuration-settings.md)
- [Optymalizowanie wydajności programu AzCopy](storage-use-azcopy-optimize.md)
- [Rozwiązywanie problemów z programem AzCopy w wersji 10 w usłudze Azure Storage przy użyciu plików dziennika](storage-use-azcopy-configure.md)
