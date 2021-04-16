---
title: Kopiowanie z usługi Google Cloud Storage do usługi Azure Storage za pomocą narzędzia AzCopy | Microsoft Docs
description: Użyj narzędzia AzCopy, aby skopiować dane z usługi Google Cloud Storage do usługi Azure Storage. AzCopy to narzędzie wiersza polecenia, przy użyciu którego można kopiować obiekty blob lub pliki do lub z konta magazynu.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 3b2ad11abb7d1a3e64deef1ca49d9f84f03e5879
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498343"
---
# <a name="copy-data-from-google-cloud-storage-to-azure-storage-by-using-azcopy-preview"></a>Kopiowanie danych z usługi Google Cloud Storage do usługi Azure Storage przy użyciu narzędzia AzCopy (wersja zapoznawcza)

AzCopy to narzędzie wiersza polecenia, przy użyciu którego można kopiować obiekty blob lub pliki do lub z konta magazynu. Ten artykuł ułatwia kopiowanie obiektów, katalogów i zasobników z usługi Google Cloud Storage Azure Blob Storage pomocą narzędzia AzCopy.

> [!IMPORTANT]
> Kopiowanie danych z usługi Google Cloud Storage do usługi Azure Storage jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="choose-how-youll-provide-authorization-credentials"></a>Wybór sposobu dostarczania poświadczeń autoryzacji

* Aby autoryzować za pomocą usługi Azure Storage, Azure Active Directory tokenu sygnatury dostępu współdzielonego (SAS).

* Aby autoryzować za pomocą usługi Google Cloud Storage, użyj klucza konta usługi.

### <a name="authorize-with-azure-storage"></a>Autoryzowanie za pomocą usługi Azure Storage

Zobacz artykuł [Wprowadzenie do programu AzCopy,](storage-use-azcopy-v10.md) aby pobrać program AzCopy i poznać sposoby poświadczeń autoryzacji w usłudze magazynu.

> [!NOTE] 
> W przykładach w tym artykule założono, że podano poświadczenia autoryzacji przy użyciu Azure Active Directory (Azure AD).
>
> Jeśli wolisz użyć tokenu SAS do autoryzowania dostępu do danych obiektów blob, możesz dołączyć ten token do adresu URL zasobu w każdym poleceniu programu AzCopy. Na przykład: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

### <a name="authorize-with-google-cloud-storage"></a>Autoryzowanie za pomocą usługi Google Cloud Storage

Aby autoryzować za pomocą usługi Google Cloud Storage, użyjesz klucza konta usługi. Aby uzyskać informacje na temat sposobu tworzenia klucza konta usługi, zobacz [Creating and managing service account keys (Tworzenie](https://cloud.google.com/iam/docs/creating-managing-service-account-keys)kluczy konta usługi i zarządzanie nimi).

Po uzyskaniu klucza usługi ustaw zmienną środowiskową na `GOOGLE_APPLICATION_CREDENTIALS` ścieżkę bezwzględną do pliku klucza konta usługi:

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | `set GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **Linux** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **macOS** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |

## <a name="copy-objects-directories-and-buckets"></a>Kopiowanie obiektów, katalogów i zasobników

Program AzCopy używa interfejsu API [Put Block From URL,](/rest/api/storageservices/put-block-from-url) więc dane są kopiowane bezpośrednio między usługą Google Cloud Storage i serwerami magazynu. Te operacje kopiowania nie używają przepustowości sieci komputera.

> [!TIP]
> Przykłady w tej sekcji otaczają argumenty ścieżki pojedynczymi cudzysłowami (""). Używaj a cudzysłowów pojedynczych we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójny cudzysłów ("") zamiast cudzysłowów pojedynczych ("").

 Te przykłady działają również z kontami, które mają hierarchiczną przestrzeń nazw. [Dostęp do wielu protokołów na Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) umożliwia używanie tej samej składni adresu URL ( `blob.core.windows.net` ) na tych kontach. 

### <a name="copy-an-object"></a>Kopiowanie obiektu

Użyj tej samej składni adresu URL ( `blob.core.windows.net` ) dla kont, które mają hierarchiczną przestrzeń nazw.

**Składnia**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'`

**Przykład**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'
```


### <a name="copy-a-directory"></a>Kopiowanie katalogu

Użyj tej samej składni adresu URL ( `blob.core.windows.net` ) dla kont, które mają hierarchiczną przestrzeń nazw.

**Składnia**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Przykład**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

> [!NOTE]
> W tym przykładzie `--recursive` dołączona flaga kopiowania plików we wszystkich podkata folderach.

### <a name="copy-the-contents-of-a-directory"></a>Kopiowanie zawartości katalogu

Możesz skopiować zawartość katalogu bez kopiowania samego katalogu zawierającego przy użyciu symbolu wieloznacznego (*).

**Składnia**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Przykład**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

### <a name="copy-a-cloud-storage-bucket"></a>Kopiowanie zasobnika usługi Cloud Storage

Użyj tej samej składni adresu URL ( `blob.core.windows.net` ) dla kont, które mają hierarchiczną przestrzeń nazw.

**Składnia**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Przykład**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-all-buckets-in-a-google-cloud-project"></a>Kopiowanie wszystkich zasobników w projekcie Google Cloud 

Najpierw ustaw identyfikator `GOOGLE_CLOUD_PROJECT` projektu Google Cloud na .

Użyj tej samej składni adresu URL ( `blob.core.windows.net` ) dla kont, które mają hierarchiczną przestrzeń nazw.

**Składnia**

`azcopy copy 'https://storage.cloud.google.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Przykład**

```azcopy
azcopy copy 'https://storage.cloud.google.com/' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-a-subset-of-buckets-in-a-google-cloud-project"></a>Kopiowanie podzestawu zasobników w projekcie Google Cloud 

Najpierw ustaw identyfikator `GOOGLE_CLOUD_PROJECT` projektu Google Cloud na .

Skopiuj podzbiór zasobników przy użyciu symbolu wieloznacznego (*) w nazwie zasobnika. Użyj tej samej składni adresu URL ( `blob.core.windows.net` ) dla kont, które mają hierarchiczną przestrzeń nazw.

**Składnia**

`azcopy copy 'https://storage.cloud.google.com/<bucket*name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Przykład**

```azcopy
azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

## <a name="handle-differences-in-bucket-naming-rules"></a>Obsługa różnic w zasadach nazewnictwa zasobników

Usługa Google Cloud Storage ma inny zestaw konwencji nazewnictwa dla nazw zasobników w porównaniu z kontenerami obiektów blob platformy Azure. Możesz przeczytać o nich [tutaj.](https://cloud.google.com/storage/docs/naming-buckets) Jeśli zdecydujesz się skopiować grupę zasobników na konto usługi Azure Storage, operacja kopiowania może się nie powieść z powodu różnic nazw.

Program AzCopy obsługuje trzy najbardziej typowe problemy, które mogą wystąpić: zasobniki zawierające kropki, zasobniki zawierające kolejne łączniki i zasobniki zawierające podkreślenia. Nazwy zasobników usługi Google Cloud Storage mogą zawierać kropki i kolejne łączniki, ale kontener na platformie Azure nie może. Program AzCopy zastępuje kropki łącznikami i kolejnymi łącznikami liczbą, która reprezentuje liczbę kolejnych łączników (na przykład: zasobnik o nazwie staje `my----bucket` się `my-4-bucket` . Jeśli nazwa zasobnika ma podkreślenia ( ), to program AzCopy zastępuje podkreślenie łącznikiem (na przykład: zasobnik o `_` `my_bucket` nazwie staje się `my-bucket` . 

## <a name="handle-differences-in-object-naming-rules"></a>Obsługa różnic w zasadach nazewnictwa obiektów

Usługa Google Cloud Storage ma inny zestaw konwencji nazewnictwa dla nazw obiektów w porównaniu z obiektami blob platformy Azure. Możesz przeczytać o nich [tutaj.](https://cloud.google.com/storage/docs/naming-objects)

Usługa Azure Storage nie zezwala na to, aby nazwy obiektów (ani żaden segment w ścieżce katalogu wirtualnego) kończyły się kropkami na końcu (na przykład `my-bucket...` ). Końcowe kropki są przycinane podczas operacji kopiowania. 

## <a name="handle-differences-in-object-metadata"></a>Obsługa różnic w metadanych obiektów

Usługi Google Cloud Storage i Azure zezwalają na różne zestawy znaków w nazwach kluczy obiektów. Informacje o metadanych w usłudze Google Cloud Storage można [znaleźć tutaj.](https://cloud.google.com/storage/docs/metadata) Po stronie platformy Azure klucze obiektów blob są zgodne z regułami nazewnictwa [identyfikatorów języka C#.](/dotnet/csharp/language-reference/)

W ramach polecenia programu AzCopy można podać wartość opcjonalnej flagi, która określa sposób obsługi plików, w których metadane pliku zawierają niezgodne `copy` `s2s-handle-invalid-metadata` nazwy kluczy. W poniższej tabeli opisano każdą wartość flagi.

| Wartość flagi | Opis  |
|--------|-----------|
| **ExcludeIfInvalid** | (Opcja domyślna) Metadane nie są uwzględniane w przesyłanych obiektach. AzCopy rejestruje ostrzeżenie. |
| **FailIfInvalid** | Obiekty nie są kopiowane. Program AzCopy rejestruje błąd i uwzględnia go w liczniku nieudanym, który pojawia się w podsumowaniu transferu.  |
| **RenameIfInvalid**  | Narzędzie AzCopy rozwiązuje nieprawidłowy klucz metadanych i kopiuje obiekt na platformę Azure przy użyciu pary wartości rozpoznanego klucza metadanych. Aby dowiedzieć się, jakie dokładnie kroki podejmuje program AzCopy w celu zmiany nazw kluczy obiektów, zobacz sekcję Jak program [AzCopy](#rename-logic) zmienia nazwy kluczy obiektów poniżej. Jeśli program AzCopy nie może zmienić nazwy klucza, obiekt nie zostanie skopiowany. |

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
- [Przykłady: Zasobniki usługi Amazon S3](storage-use-azcopy-s3.md)
- [Przykłady: Azure Files](storage-use-azcopy-files.md)
- [Samouczek: Migracja danych lokalnych do magazynu w chmurze za pomocą narzędzia AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Zobacz następujące artykuły, aby skonfigurować ustawienia, zoptymalizować wydajność i rozwiązać problemy:

- [Ustawienia konfiguracji programu AzCopy](storage-ref-azcopy-configuration-settings.md)
- [Optymalizowanie wydajności programu AzCopy](storage-use-azcopy-optimize.md)
- [Rozwiązywanie problemów z programem AzCopy w wersji 10 w usłudze Azure Storage przy użyciu plików dziennika](storage-use-azcopy-configure.md)