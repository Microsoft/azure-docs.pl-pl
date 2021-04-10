---
title: Kopiowanie danych z magazynu Google Cloud Storage do usługi Azure Storage za pomocą AzCopy | Microsoft Docs
description: Użyj AzCopy, aby skopiować dane z magazynu Google Cloud Storage do usługi Azure Storage. AzCopy to narzędzie wiersza polecenia, przy użyciu którego można kopiować obiekty blob lub pliki do lub z konta magazynu.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: c6a53acd63b6aa882674f6aa29e1f7152f5b0a30
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728814"
---
# <a name="copy-data-from-google-cloud-storage-to-azure-storage-by-using-azcopy-preview"></a>Kopiowanie danych z magazynu Google Cloud Storage do usługi Azure Storage za pomocą AzCopy (wersja zapoznawcza)

AzCopy to narzędzie wiersza polecenia, przy użyciu którego można kopiować obiekty blob lub pliki do lub z konta magazynu. Ten artykuł pomaga w kopiowaniu obiektów, katalogów i zasobników z usługi Google Cloud Storage do platformy Azure Blob Storage przy użyciu programu AzCopy.

> [!IMPORTANT]
> Kopiowanie danych z magazynu Google Cloud Storage do usługi Azure Storage jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="choose-how-youll-provide-authorization-credentials"></a>Wybór sposobu dostarczania poświadczeń autoryzacji

* Aby autoryzować usługę Azure Storage, użyj Azure Active Directory (AD) lub tokenu sygnatury dostępu współdzielonego (SAS).

* Aby autoryzować w usłudze Google Cloud Storage, użyj klucza konta usługi.

### <a name="authorize-with-azure-storage"></a>Autoryzuj w usłudze Azure Storage

Zapoznaj się z artykułem [wprowadzenie do AzCopy](storage-use-azcopy-v10.md) w celu pobrania AzCopy i Dowiedz się więcej na temat sposobów dostarczania poświadczeń autoryzacji do usługi magazynu.

> [!NOTE] 
> W przykładach w tym artykule przyjęto założenie, że poświadczenia autoryzacji zostały podane przy użyciu Azure Active Directory (Azure AD).
>
> Jeśli wolisz używać tokenu SAS do autoryzacji dostępu do danych obiektów blob, możesz dołączyć ten token do adresu URL zasobu w każdym poleceniu AzCopy. Na przykład: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

### <a name="authorize-with-google-cloud-storage"></a>Autoryzuj w usłudze Google Cloud Storage

Do autoryzacji w usłudze Google Cloud Storage zostanie użyty klucz konta usługi. Informacje o sposobie tworzenia klucza konta usługi znajdują się w temacie [Tworzenie kluczy konta usługi i zarządzanie nimi](https://cloud.google.com/iam/docs/creating-managing-service-account-keys).

Po uzyskaniu klucza usługi należy ustawić `GOOGLE_APPLICATION_CREDENTIALS` zmienną środowiskową na ścieżkę bezwzględną do pliku klucza konta usługi:

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | `set GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **Linux** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **macOS** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |

## <a name="copy-objects-directories-and-buckets"></a>Kopiowanie obiektów, katalogów i zasobników

AzCopy korzysta z interfejsu API [Put Block z adresu URL](/rest/api/storageservices/put-block-from-url) , więc dane są kopiowane bezpośrednio między serwerami magazynu i magazynów Google Cloud. Te operacje kopiowania nie korzystają z przepustowości sieci komputera.

> [!TIP]
> W przykładach w tej sekcji zamieszczono argumenty Path z pojedynczymi cudzysłowami (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

 Te przykłady działają również z kontami, które mają hierarchiczną przestrzeń nazw. [Dostęp z użyciem protokołu wieloprotokołowego do Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) umożliwia używanie tej samej składni adresu URL ( `blob.core.windows.net` ) na tych kontach. 

### <a name="copy-an-object"></a>Kopiowanie obiektu

Użyj tej samej składni adresu URL ( `blob.core.windows.net` ) dla kont, które mają hierarchiczną przestrzeń nazw.

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Przykład** | `azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |


### <a name="copy-a-directory"></a>Kopiowanie katalogu

Użyj tej samej składni adresu URL ( `blob.core.windows.net` ) dla kont, które mają hierarchiczną przestrzeń nazw.

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Przykład** | `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Przykład** (hierarchiczna przestrzeń nazw)| `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

> [!NOTE]
> Ten przykład dołącza `--recursive` flagę do kopiowania plików we wszystkich podkatalogach.

### <a name="copy-the-contents-of-a-directory"></a>Skopiuj zawartość katalogu

Zawartość katalogu można skopiować bez kopiowania samego katalogu zawierającego symbol wieloznaczny (*).

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Przykład** | `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Przykład** (hierarchiczna przestrzeń nazw)| `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-cloud-storage-bucket"></a>Kopiowanie zasobnika magazynu w chmurze

Użyj tej samej składni adresu URL ( `blob.core.windows.net` ) dla kont, które mają hierarchiczną przestrzeń nazw.

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Przykład** | `azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Przykład** (hierarchiczna przestrzeń nazw)| `azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

### <a name="copy-all-buckets-in-a-google-cloud-project"></a>Kopiuj wszystkie zasobniki w projekcie usługi Google Cloud 

Najpierw ustaw `GOOGLE_CLOUD_PROJECT` Identyfikator projektu na projekt Google Cloud.

Użyj tej samej składni adresu URL ( `blob.core.windows.net` ) dla kont, które mają hierarchiczną przestrzeń nazw.

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://storage.cloud.google.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Przykład** | `azcopy copy 'https://storage.cloud.google.com/' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Przykład** (hierarchiczna przestrzeń nazw)| `azcopy copy 'https://storage.cloud.google.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

### <a name="copy-a-subset-of-buckets-in-a-google-cloud-project"></a>Kopiowanie podzbioru zasobników w projekcie Google Cloud 

Najpierw ustaw `GOOGLE_CLOUD_PROJECT` Identyfikator projektu na projekt Google Cloud.

Skopiuj podzestaw zasobników przy użyciu symbolu wieloznacznego (*) w nazwie zasobnika. Użyj tej samej składni adresu URL ( `blob.core.windows.net` ) dla kont, które mają hierarchiczną przestrzeń nazw.

| Składnia/przykład  |  Kod |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://storage.cloud.google.com/<bucket*name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Przykład** | `azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Przykład** (hierarchiczna przestrzeń nazw)| `azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

## <a name="handle-differences-in-bucket-naming-rules"></a>Obsługa różnic w regułach nazewnictwa zasobników

Magazyn w chmurze Google ma inny zestaw konwencji nazewnictwa dla nazw zasobników w porównaniu do kontenerów obiektów blob platformy Azure. [Tutaj](https://cloud.google.com/storage/docs/naming-buckets)możesz przeczytać o nich. Jeśli zdecydujesz się na skopiowanie grupy zasobników na konto usługi Azure Storage, operacja kopiowania może zakończyć się niepowodzeniem z powodu różnic nazewnictwa.

AzCopy obsługuje trzy najczęstsze problemy, które mogą wystąpić; zasobniki zawierające okresy, przedziały, które zawierają kolejne łączniki i zasobniki, które zawierają znaki podkreślenia. Nazwy zasobników magazynu Google Cloud mogą zawierać kropki i kolejne łączniki, ale nie może być kontenera na platformie Azure. AzCopy zastępuje okresy łącznikami i kolejne łączniki o liczbie reprezentującej liczbę kolejnych łączników (na przykład: zasobnik o nazwie `my----bucket` zmieni się na `my-4-bucket` . Jeśli nazwa zasobnika ma znak podkreślenia ( `_` ), AzCopy zastępuje znak podkreślenia łącznikiem (na przykład: zasobnik o nazwie `my_bucket` zmieni się na `my-bucket` . 

## <a name="handle-differences-in-object-naming-rules"></a>Obsługa różnic w regułach nazewnictwa obiektów

Magazyn w chmurze Google ma inny zestaw konwencji nazewnictwa dla nazw obiektów w porównaniu z obiektami blob platformy Azure. [Tutaj](https://cloud.google.com/storage/docs/naming-objects)możesz przeczytać o nich.

Usługa Azure Storage nie zezwala na zakończenie używania nazw obiektów (lub dowolnego segmentu w ścieżce katalogu wirtualnego) z kropkami końcowymi (na przykład `my-bucket...` ). Kropki końcowe są obcinane podczas wykonywania operacji kopiowania. 

## <a name="handle-differences-in-object-metadata"></a>Obsługa różnic w metadanych obiektu

Magazyn w chmurze Google i system Azure umożliwiają różne zestawy znaków w nazwach kluczy obiektów. Informacje o metadanych w magazynie Google Cloud Storage można znaleźć [tutaj](https://cloud.google.com/storage/docs/metadata). Po stronie platformy Azure klucze obiektów BLOB są zgodne z regułami nazewnictwa dla [identyfikatorów C#](/dotnet/csharp/language-reference/).

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

- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)
