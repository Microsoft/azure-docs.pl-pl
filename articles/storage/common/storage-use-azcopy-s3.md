---
title: Kopiowanie danych z usługi Amazon S3 do magazynu Azure Storage przy użyciu programu AzCopy | Microsoft Docs
description: Użyj AzCopy, aby skopiować dane z usługi Amazon S3 do magazynu Azure. AzCopy to narzędzie wiersza polecenia, przy użyciu którego można kopiować obiekty blob lub pliki do lub z konta magazynu.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: b94cb6d6302cd92816fe25f6e672b1ce3bb9398d
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792001"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>Kopiowanie danych z usług Amazon S3 do usługi Azure Storage za pomocą AzCopy

AzCopy to narzędzie wiersza polecenia, przy użyciu którego można kopiować obiekty blob lub pliki do lub z konta magazynu. Ten artykuł pomaga w kopiowaniu obiektów, katalogów i zasobników z Amazon Web Services (AWS) S3 do platformy Azure Blob Storage przy użyciu AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Wybór sposobu dostarczania poświadczeń autoryzacji

* Aby autoryzować usługę Azure Storage, użyj Azure Active Directory (AD) lub tokenu sygnatury dostępu współdzielonego (SAS).

* Aby autoryzować za pomocą AWS S3, użyj klucza dostępu AWS i klucza dostępu tajnego.

### <a name="authorize-with-azure-storage"></a>Autoryzuj w usłudze Azure Storage

Zapoznaj się z artykułem [wprowadzenie do AzCopy](storage-use-azcopy-v10.md) w celu pobrania AzCopy i wybrania sposobu dostarczania poświadczeń autoryzacji do usługi magazynu.

> [!NOTE]
> W przykładach w tym artykule przyjęto założenie, że tożsamość została uwierzytelniona przy użyciu `AzCopy login` polecenia. AzCopy następnie używa konta usługi Azure AD do autoryzacji dostępu do danych w usłudze BLOB Storage.
>
> Jeśli wolisz używać tokenu SAS do autoryzacji dostępu do danych obiektów blob, możesz dołączyć ten token do adresu URL zasobu w każdym poleceniu AzCopy.
>
> Na przykład: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Autoryzuj przy użyciu AWS S3

Zbierz klucz dostępu AWS i klucz dostępu tajnego, a następnie ustaw następujące zmienne środowiskowe:

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **macOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Kopiowanie obiektów, katalogów i zasobników

AzCopy korzysta z interfejsu API [Put Block z adresu URL](/rest/api/storageservices/put-block-from-url) , więc dane są kopiowane bezpośrednio między AWS S3 i serwerami magazynu. Te operacje kopiowania nie korzystają z przepustowości sieci komputera.

> [!TIP]
> W przykładach w tej sekcji zamieszczono argumenty Path z pojedynczymi cudzysłowami (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

 Te przykłady działają również z kontami, które mają hierarchiczną przestrzeń nazw. [Dostęp z użyciem protokołu wieloprotokołowego do Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) umożliwia używanie tej samej składni adresu URL ( `blob.core.windows.net` ) na tych kontach. 

### <a name="copy-an-object"></a>Kopiowanie obiektu

Użyj tej samej składni adresu URL ( `blob.core.windows.net` ) dla kont, które mają hierarchiczną przestrzeń nazw.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Przykład** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> Przykłady w tym artykule używają adresów URL w stylu ścieżki dla zasobników AWS S3 (na przykład: `http://s3.amazonaws.com/<bucket-name>` ). 
>
> Możesz również użyć wirtualnych adresów URL w stylu hostowanym (na przykład: `http://bucket.s3.amazonaws.com` ). 
>
> Aby dowiedzieć się więcej na temat wirtualnego hostowania zasobników, zobacz [Virtual Hostinging datazasobniks]] ( https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html) .

### <a name="copy-a-directory"></a>Kopiowanie katalogu

Użyj tej samej składni adresu URL ( `blob.core.windows.net` ) dla kont, które mają hierarchiczną przestrzeń nazw.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Przykład** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Przykład** (hierarchiczna przestrzeń nazw)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

> [!NOTE]
> Ten przykład dołącza `--recursive` flagę do kopiowania plików we wszystkich podkatalogach.

### <a name="copy-the-contents-of-a-directory"></a>Skopiuj zawartość katalogu

Zawartość katalogu można skopiować bez kopiowania samego katalogu zawierającego symbol wieloznaczny (*).

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Przykład** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Przykład** (hierarchiczna przestrzeń nazw)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>Kopiowanie zasobnika

Użyj tej samej składni adresu URL ( `blob.core.windows.net` ) dla kont, które mają hierarchiczną przestrzeń nazw.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **Przykład** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |
| **Przykład** (hierarchiczna przestrzeń nazw)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Kopiuj wszystkie przedziały we wszystkich regionach

Użyj tej samej składni adresu URL ( `blob.core.windows.net` ) dla kont, które mają hierarchiczną przestrzeń nazw.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Przykład** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Przykład** (hierarchiczna przestrzeń nazw)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Kopiuj wszystkie zasobniki w określonym regionie S3

Użyj tej samej składni adresu URL ( `blob.core.windows.net` ) dla kont, które mają hierarchiczną przestrzeń nazw.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Przykład** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Przykład** (hierarchiczna przestrzeń nazw)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Obsługa różnic w regułach nazewnictwa obiektów

AWS S3 ma inny zestaw konwencji nazewnictwa dla nazw zasobników w porównaniu do kontenerów obiektów blob platformy Azure. [Tutaj](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules)możesz przeczytać o nich. Jeśli zdecydujesz się na skopiowanie grupy zasobników na konto usługi Azure Storage, operacja kopiowania może zakończyć się niepowodzeniem z powodu różnic nazewnictwa.

AzCopy obsługuje dwa najczęstsze problemy, które mogą wystąpić; zasobniki zawierające okresy i przedziały, które zawierają kolejne łączniki. Nazwy zasobników AWS S3 mogą zawierać kropki i kolejne łączniki, ale nie może mieć kontenera na platformie Azure. AzCopy zastępuje okresy łącznikami i kolejne łączniki o liczbie reprezentującej liczbę kolejnych łączników (na przykład: zasobnik o nazwie `my----bucket` zmieni się na `my-4-bucket` . 

Ponadto, jako AzCopy kopiuje pliki, sprawdza, czy nadaje się kolizje nazw i próbuje je rozwiązać. Na przykład jeśli istnieją zasobniki o nazwie `bucket-name` i `bucket.name` , AzCopy rozpoznaje zasobnik o nazwie `bucket.name` pierwszy do `bucket-name` , a następnie do `bucket-name-2` .

## <a name="handle-differences-in-object-metadata"></a>Obsługa różnic w metadanych obiektu

AWS S3 i platforma Azure dopuszczają różne zestawy znaków w nazwach kluczy obiektów. Informacje o znakach, które AWS S3 używają tutaj, można znaleźć [tutaj](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys). Po stronie platformy Azure klucze obiektów BLOB są zgodne z regułami nazewnictwa dla [identyfikatorów C#](/dotnet/csharp/language-reference/).

W ramach `copy` polecenia AzCopy można podać wartość opcjonalnej `s2s-handle-invalid-metadata` flagi, która określa, jak chcesz obsługiwać pliki, w których metadane pliku zawierają niezgodne nazwy kluczy. W poniższej tabeli opisano każdą wartość flagi.

| Wartość flagi | Opis  |
|--------|-----------|
| **ExcludeIfInvalid** | (Opcja domyślna) Metadane nie znajdują się w transferowanym obiekcie. AzCopy rejestruje ostrzeżenie. |
| **FailIfInvalid** | Obiekty nie są kopiowane. AzCopy rejestruje błąd i zawiera ten błąd w liczniku niepowodzenia, który pojawia się w podsumowaniu transferu.  |
| **RenameIfInvalid**  | AzCopy rozpoznaje nieprawidłowy klucz metadanych i kopiuje obiekt na platformę Azure przy użyciu pary wartość klucza metadanych rozpoznanych. Aby dokładnie dowiedzieć się, jakie kroki AzCopy, aby zmienić nazwy kluczy obiektów, zobacz sekcję [how AzCopy renames Object Keys](#rename-logic) poniżej. Jeśli AzCopy nie może zmienić nazwy klucza, obiekt nie będzie kopiowany. |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>Jak AzCopy zmienia nazwy kluczy obiektów

AzCopy wykonuje następujące kroki:

1. Zastępuje nieprawidłowe znaki znakiem "_".

2. Dodaje ciąg `rename_` do początku nowego prawidłowego klucza.

   Ten klucz zostanie użyty do zapisania oryginalnej **wartości** metadanych.

3. Dodaje ciąg `rename_key_` do początku nowego prawidłowego klucza.
   Ten klucz zostanie użyty do zapisania oryginalnych metadanych nieprawidłowy **klucz**.
   Możesz użyć tego klucza do próby odzyskania metadanych na platformie Azure, ponieważ klucz metadanych jest zachowywany jako wartość w usłudze BLOB Storage.

## <a name="next-steps"></a>Następne kroki

Więcej przykładów znajdziesz w jednym z następujących artykułów:

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)

- [Transfer danych](storage-use-azcopy-v10.md#transfer-data)

- [Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)

- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)
