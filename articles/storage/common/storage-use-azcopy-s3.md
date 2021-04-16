---
title: Kopiowanie danych z usługi Amazon S3 do usługi Azure Storage przy użyciu narzędzia AzCopy | Microsoft Docs
description: Użyj narzędzia AzCopy, aby skopiować dane z usługi Amazon S3 do usługi Azure Storage. AzCopy to narzędzie wiersza polecenia, przy użyciu którego można kopiować obiekty blob lub pliki do lub z konta magazynu.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: ac73d0e57377a8922691ea06c8de3df5ef577680
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502440"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>Kopiowanie danych z usługi Amazon S3 do usługi Azure Storage przy użyciu narzędzia AzCopy

AzCopy to narzędzie wiersza polecenia, przy użyciu którego można kopiować obiekty blob lub pliki do lub z konta magazynu. Ten artykuł ułatwia kopiowanie obiektów, katalogów i zasobników z usługi Amazon Web Services (AWS) S3 Azure Blob Storage pomocą narzędzia AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Wybór sposobu dostarczania poświadczeń autoryzacji

* Aby autoryzować za pomocą usługi Azure Storage, użyj Azure Active Directory (AD) lub tokenu sygnatury dostępu współdzielonego (SAS).

* Aby autoryzować za pomocą usług AWS S3, użyj klucza dostępu do usług AWS i klucza dostępu tajnego.

### <a name="authorize-with-azure-storage"></a>Autoryzowanie za pomocą usługi Azure Storage

Zobacz artykuł [Wprowadzenie do programu AzCopy,](storage-use-azcopy-v10.md) aby pobrać program AzCopy i wybrać sposób poświadczeń autoryzacji dla usługi magazynu.

> [!NOTE]
> W przykładach w tym artykule przyjęto założenie, że tożsamość jest uwierzytelniona przy użyciu `AzCopy login` polecenia . Narzędzie AzCopy następnie autoryzuje dostęp do danych w usłudze Blob Storage przy użyciu konta usługi Azure AD.
>
> Jeśli wolisz użyć tokenu SAS do autoryzowania dostępu do danych obiektów blob, możesz dołączyć ten token do adresu URL zasobu w każdym poleceniu programu AzCopy.
>
> Na przykład: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Autoryzacja przy użyciu usług AWS S3

Zbierz klucz dostępu do usług AWS i klucz dostępu do klucza tajnego, a następnie ustaw następujące zmienne środowiskowe:

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **macOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Kopiowanie obiektów, katalogów i zasobników

Program AzCopy używa interfejsu API [put block from URL,](/rest/api/storageservices/put-block-from-url) więc dane są kopiowane bezpośrednio między serwerami usług AWS S3 i storage. Te operacje kopiowania nie używają przepustowości sieci komputera.

> [!TIP]
> Przykłady w tej sekcji otaczają argumenty ścieżki pojedynczymi cudzysłowami (""). Używaj a cudzysłowów pojedynczych we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w cudzysłów (""), a nie pojedynczy cudzysłów ("").

 Te przykłady działają również z kontami, które mają hierarchiczną przestrzeń nazw. [Dostęp do wielu protokołów na Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) umożliwia używanie tej samej składni adresu URL ( `blob.core.windows.net` ) na tych kontach. 

### <a name="copy-an-object"></a>Kopiowanie obiektu

Użyj tej samej składni adresu URL ( `blob.core.windows.net` ) dla kont, które mają hierarchiczną przestrzeń nazw.

**Składnia**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'`

**Przykład**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'
```

> [!NOTE]
> Przykłady w tym artykule używają adresów URL w stylu ścieżki dla zasobników S3 usług AWS (na przykład: `http://s3.amazonaws.com/<bucket-name>` ). 
>
> Możesz również użyć wirtualnych adresów URL w stylu hostowania (na przykład `http://bucket.s3.amazonaws.com` : ). 
>
> Aby dowiedzieć się więcej na temat wirtualnego hostingu zasobników, zobacz [Virtual Hosting of Buckets (Wirtualny hosting zasobników).](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html)

### <a name="copy-a-directory"></a>Kopiowanie katalogu

Użyj tej samej składni adresu URL ( `blob.core.windows.net` ) dla kont, które mają hierarchiczną przestrzeń nazw.

**Składnia**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Przykład**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

> [!NOTE]
> W tym przykładzie `--recursive` dołączona jest flaga w celu skopiowania plików we wszystkich katalogach podrzędnych.

### <a name="copy-the-contents-of-a-directory"></a>Kopiowanie zawartości katalogu

Możesz skopiować zawartość katalogu bez kopiowania samego katalogu zawierającego przy użyciu symbolu wieloznacznego (*).

**Składnia**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Przykład**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

### <a name="copy-a-bucket"></a>Kopiowanie zasobnika

Użyj tej samej składni adresu URL ( `blob.core.windows.net` ) dla kont, które mają hierarchiczną przestrzeń nazw.

**Składnia**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true`

**Przykład**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true
```

### <a name="copy-all-buckets-in-all-regions"></a>Kopiowanie wszystkich zasobników we wszystkich regionach

Użyj tej samej składni adresu URL ( `blob.core.windows.net` ) dla kont, które mają hierarchiczną przestrzeń nazw.

**Składnia**

`azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Przykład**

```azcopy
azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Kopiowanie wszystkich zasobników w określonym regionie S3

Użyj tej samej składni adresu URL ( `blob.core.windows.net` ) dla kont, które mają hierarchiczną przestrzeń nazw.

**Składnia**

`azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Przykład**

```azcopy
azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

## <a name="handle-differences-in-object-naming-rules"></a>Obsługa różnic w zasadach nazewnictwa obiektów

Usługi AWS S3 mają inny zestaw konwencji nazewnictwa dla nazw zasobników niż kontenery obiektów blob platformy Azure. Więcej informacji na ich temat można [znaleźć tutaj.](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules) Jeśli zdecydujesz się skopiować grupę zasobników na konto usługi Azure Storage, operacja kopiowania może się nie powieść z powodu różnic nazw.

Program AzCopy obsługuje dwa najczęściej spotykane problemy: zasobniki zawierające kropki i zasobniki zawierające kolejne łączniki. Nazwy zasobników usługi AWS S3 mogą zawierać kropki i kolejne łączniki, ale kontener na platformie Azure nie może. Program AzCopy zastępuje kropki łącznikami i kolejnymi łącznikami liczbą, która reprezentuje liczbę kolejnych łączników (na przykład: zasobnik o nazwie staje `my----bucket` się `my-4-bucket` . 

Ponadto, gdy azCopy kopiuje pliki, sprawdza, czy nie ma kolizji nazw i próbuje je rozwiązać. Jeśli na przykład istnieją zasobniki o nazwie i , program AzCopy rozpozna zasobnik o nazwie jako pierwszy do , `bucket-name` `bucket.name` a następnie na `bucket.name` `bucket-name` `bucket-name-2` .

## <a name="handle-differences-in-object-metadata"></a>Obsługa różnic w metadanych obiektu

Usługi AWS S3 i Azure zezwalają na różne zestawy znaków w nazwach kluczy obiektów. Informacje o znakach, których używa AWS S3, można [znaleźć tutaj.](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys) Po stronie platformy Azure klucze obiektów blob są zgodne z regułami nazewnictwa [identyfikatorów języka C#.](/dotnet/csharp/language-reference/)

W ramach polecenia programu AzCopy można podać wartość opcjonalnej flagi , która określa sposób obsługi plików, w których metadane pliku zawierają niezgodne `copy` `s2s-handle-invalid-metadata` nazwy kluczy. W poniższej tabeli opisano każdą wartość flagi.

| Wartość flagi | Opis  |
|--------|-----------|
| **ExcludeIfInvalid** | (Opcja domyślna) Metadane nie są zawarte w przeniesionym obiekcie. AzCopy rejestruje ostrzeżenie. |
| **FailIfInvalid** | Obiekty nie są kopiowane. Program AzCopy rejestruje błąd i uwzględnia go w liczniku nieudanym, który pojawia się w podsumowaniu transferu.  |
| **RenameIfInvalid**  | Narzędzie AzCopy rozwiązuje nieprawidłowy klucz metadanych i kopiuje obiekt na platformę Azure przy użyciu pary wartości rozpoznanego klucza metadanych. Aby dowiedzieć się, jakie dokładnie kroki program AzCopy podejmuje w celu zmiany nazw kluczy obiektów, zobacz sekcję Jak program [AzCopy](#rename-logic) zmienia nazwy kluczy obiektów poniżej. Jeśli program AzCopy nie może zmienić nazwy klucza, obiekt nie zostanie skopiowany. |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>Jak polecenie AzCopy zmienia nazwy kluczy obiektów

Program AzCopy wykonuje następujące kroki:

1. Zastępuje nieprawidłowe znaki znakiem "_".

2. Dodaje ciąg `rename_` na początku nowego prawidłowego klucza.

   Ten klucz zostanie użyty do zapisania oryginalnej wartości **metadanych**.

3. Dodaje ciąg `rename_key_` na początku nowego prawidłowego klucza.
   Ten klucz zostanie użyty do zapisania nieprawidłowego klucza oryginalnych **metadanych.**
   Możesz użyć tego klucza, aby spróbować odzyskać metadane po stronie platformy Azure, ponieważ klucz metadanych jest zachowywany jako wartość w usłudze Blob Storage.

## <a name="next-steps"></a>Następne kroki

Więcej przykładów można znaleźć w tych artykułach:

- [Przykłady: Upload](storage-use-azcopy-blobs-upload.md)
- [Przykłady: Pobierz](storage-use-azcopy-blobs-download.md)
- [Przykłady: Kopiowanie między kontami](storage-use-azcopy-blobs-copy.md)
- [Przykłady: Synchronizowanie](storage-use-azcopy-blobs-synchronize.md)
- [Przykłady: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Przykłady: Azure Files](storage-use-azcopy-files.md)
- [Samouczek: Migracja danych lokalnych do magazynu w chmurze za pomocą narzędzia AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Zapoznaj się z tymi artykułami, aby skonfigurować ustawienia, zoptymalizować wydajność i rozwiązać problemy:

- [Ustawienia konfiguracji programu AzCopy](storage-ref-azcopy-configuration-settings.md)
- [Optymalizowanie wydajności programu AzCopy](storage-use-azcopy-optimize.md)
- [Rozwiązywanie problemów z programem AzCopy w wersji 10 w usłudze Azure Storage przy użyciu plików dziennika](storage-use-azcopy-configure.md)
