---
title: Kopiowanie obiektów BLOB między kontami usługi Azure Storage za pomocą AzCopy v10 | Microsoft Docs
description: Ten artykuł zawiera kolekcję przykładowych poleceń AzCopy, które ułatwiają kopiowanie obiektów BLOB między kontami magazynu.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 524e3f70f9588cfae2c739722fc1a44e683f9a7f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97617293"
---
# <a name="copy-blobs-between-azure-storage-accounts-by-using-azcopy-v10"></a>Kopiowanie obiektów BLOB między kontami usługi Azure Storage za pomocą AzCopy v10

Obiekty blob, katalogi i kontenery można kopiować między kontami magazynu za pomocą narzędzia wiersza polecenia AzCopy v10. 

Aby zobaczyć przykłady dla innych typów zadań, takich jak przekazywanie plików, pobieranie obiektów blob i synchronizowanie z usługą BLOB Storage, zobacz linki przedstawione w sekcji [następne kroki](#next-steps) w tym artykule.

AzCopy używa [interfejsów API](/rest/api/storageservices/put-page-from-url) [serwer-serwer](/rest/api/storageservices/put-block-from-url) , aby dane były kopiowane bezpośrednio między serwerami magazynu. Te operacje kopiowania nie korzystają z przepustowości sieci komputera.

Aby pobrać AzCopy i poznać sposoby dostarczania poświadczeń autoryzacji do usługi magazynu, zobacz [Rozpoczynanie pracy z AzCopy](storage-use-azcopy-v10.md). 

## <a name="guidelines"></a>Wytyczne

Zastosuj poniższe wskazówki do poleceń AzCopy. 

- Klient musi mieć dostęp sieciowy zarówno do konta magazynu źródłowego, jak i docelowego. Aby dowiedzieć się, jak skonfigurować ustawienia sieci dla każdego konta magazynu, zobacz [Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych](storage-network-security.md?toc=/azure/storage/blobs/toc.json).

- Dołącz token sygnatury dostępu współdzielonego do każdego źródłowego adresu URL. 

  W przypadku podania poświadczeń autoryzacji za pomocą Azure Active Directory (Azure AD) można pominąć token sygnatury dostępu współdzielonego tylko z docelowego adresu URL. Upewnij się, że skonfigurowano odpowiednie role na koncie docelowym. Zobacz [opcję 1: użyj Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory). 

  W przykładach w tym artykule przyjęto założenie, że tożsamość została uwierzytelniona przy użyciu usługi Azure AD, więc przykłady pomijają tokeny sygnatury dostępu współdzielonego z docelowego adresu URL.

-  Jeśli kopiujesz do konta magazynu blokowego Premium BLOB, Pomiń warstwę dostępu obiektu BLOB w operacji kopiowania, ustawiając polecenie `s2s-preserve-access-tier` na `false` (na przykład: `--s2s-preserve-access-tier=false` ). Konta magazynu blokowych obiektów BLOB w warstwie Premium nie obsługują warstw dostępu. 

- W przypadku kopiowania do lub z konta, które ma hierarchiczną przestrzeń nazw, należy użyć `blob.core.windows.net` zamiast `dfs.core.windows.net` w składni adresu URL. [Dostęp z użyciem protokołu wieloprotokołowego do Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) umożliwia korzystanie z programu `blob.core.windows.net` , a jest to jedyna obsługiwana składnia w przypadku scenariuszy kopiowania konta. 

- Przepływność operacji kopiowania można zwiększyć, ustawiając wartość `AZCOPY_CONCURRENCY_VALUE` zmiennej środowiskowej. Aby dowiedzieć się więcej, zobacz [Optymalizowanie przepływności](storage-use-azcopy-configure.md#optimize-throughput). 

- Jeśli źródłowe obiekty blob mają Tagi indeksów i chcesz zachować te Tagi, należy ponownie zastosować je do docelowych obiektów BLOB. Informacje o sposobie ustawiania tagów indeksu znajdują się w sekcji [Kopiowanie obiektów BLOB do innego konta magazynu z tagami indeksów](#copy-between-accounts-and-add-index-tags) w tym artykule.

## <a name="copy-a-blob"></a>Kopiowanie obiektu BLOB

Skopiuj obiekt BLOB do innego konta magazynu za pomocą polecenia [copy AzCopy](storage-ref-azcopy-copy.md) . 

> [!TIP]
> Ten przykład obejmuje argumenty ścieżki z pojedynczym cudzysłowem (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

Operacja kopiowania jest synchroniczna, dlatego gdy polecenie zostanie zwrócone, oznacza to, że pliki zostały skopiowane. 

## <a name="copy-a-directory"></a>Kopiowanie katalogu

Skopiuj katalog na inne konto magazynu za pomocą polecenia [copy AzCopy](storage-ref-azcopy-copy.md) . 

> [!TIP]
> Ten przykład obejmuje argumenty ścieżki z pojedynczym cudzysłowem (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

Operacja kopiowania jest synchroniczna, dlatego gdy polecenie zostanie zwrócone, oznacza to, że pliki zostały skopiowane.

## <a name="copy-a-container"></a>Kopiowanie kontenera

Skopiuj kontener na inne konto magazynu za pomocą polecenia [copy AzCopy](storage-ref-azcopy-copy.md) .

> [!TIP]
> Ten przykład obejmuje argumenty ścieżki z pojedynczym cudzysłowem (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

Operacja kopiowania jest synchroniczna, dlatego gdy polecenie zostanie zwrócone, oznacza to, że pliki zostały skopiowane.

## <a name="copy-containers-directories-and-blobs"></a>Kopiowanie kontenerów, katalogów i obiektów BLOB

Skopiuj wszystkie kontenery, katalogi i obiekty blob na inne konto magazynu za pomocą polecenia [copy AzCopy](storage-ref-azcopy-copy.md) .

> [!TIP]
> Ten przykład obejmuje argumenty ścieżki z pojedynczym cudzysłowem (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

Operacja kopiowania jest synchroniczna, dlatego gdy polecenie zostanie zwrócone, oznacza to, że pliki zostały skopiowane.

<a id="copy-between-accounts-and-add-index-tags"></a>

## <a name="copy-blobs-and-add-index-tags"></a>Kopiowanie obiektów blob i dodawanie tagów indeksów

Skopiuj obiekty blob do innego konta magazynu i Dodaj [Tagi indeksu obiektów BLOB (wersja zapoznawcza)](../blobs/storage-manage-find-blobs.md) do docelowego obiektu BLOB.

W przypadku korzystania z autoryzacji usługi Azure AD podmiot zabezpieczeń musi mieć przypisaną rolę [właściciela danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) lub musi mieć uprawnienia do `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [operacji dostawcy zasobów platformy Azure](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) za pośrednictwem niestandardowej roli platformy Azure. Jeśli używasz tokenu sygnatury dostępu współdzielonego (SAS), ten token musi zapewnić dostęp do tagów obiektu BLOB za pośrednictwem `t` uprawnienia SAS.

Aby dodać tagi, użyj `--blob-tags` opcji wraz z algorytmem par klucz-wartość w adresie URL. 

Na przykład, aby dodać klucz `my tag` i wartość `my tag value` , należy dodać `--blob-tags='my%20tag=my%20tag%20value'` do parametru docelowego. 

Oddziel wiele tagów indeksów przy użyciu znaku handlowego "i" ( `&` ).  Na przykład, jeśli chcesz dodać klucz `my second tag` i wartość `my second tag value` , ciąg opcji Complete `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

W poniższych przykładach pokazano, jak użyć `--blob-tags` opcji.

> [!TIP]
> Te przykłady otaczające argumenty ścieżki cudzysłowem pojedynczym (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

|    |     |
|--------|-----------|
| **Obiekt blob** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Katalog** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Kontener** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Konto** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

Operacja kopiowania jest synchroniczna, dlatego gdy polecenie zostanie zwrócone, oznacza to, że pliki zostały skopiowane.

> [!NOTE]
> Jeśli określisz katalog, kontener lub konto dla źródła, wszystkie obiekty blob, które są kopiowane do miejsca docelowego będą miały te same znaczniki, które są określone w poleceniu.

## <a name="copy-with-optional-flags"></a>Kopiuj z opcjonalnymi flagami

Możesz dostosować operację kopiowania przy użyciu opcjonalnych flag. Oto kilka przykładów.

|Scenariusz|Flaga|
|---|---|
|Kopiuj obiekty blob jako blokowe, stronicowe lub dołączane obiekty blob.|**--Typ** = \[ obiektu BLOB BlockBlob \| PageBlob \| AppendBlob\]|
|Kopiuj do określonej warstwy dostępu (na przykład warstwy archiwum).|**--Block-BLOB-warstw** = \[ Brak \| aktywnego \| archiwum chłodnego \|\]|
|Automatycznie Kompresuj pliki.|**--dekompresowanie** = \[ Wklęśnięcie (gzip) \|\]|

Aby uzyskać pełną listę, zobacz [Opcje](storage-ref-azcopy-copy.md#options). 

## <a name="next-steps"></a>Następne kroki

Więcej przykładów znajdziesz w następujących artykułach:

- [Przykłady: Upload](storage-use-azcopy-blobs-upload.md)
- [Przykłady: Pobierz](storage-use-azcopy-blobs-download.md)
- [Przykłady: Synchronizowanie](storage-use-azcopy-blobs-synchronize.md)
- [Przykłady: Zasobniki usługi Amazon S3](storage-use-azcopy-s3.md)
- [Przykłady: Azure Files](storage-use-azcopy-files.md)
- [Samouczek: Migracja danych lokalnych do magazynu w chmurze za pomocą narzędzia AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)