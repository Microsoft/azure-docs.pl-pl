---
title: Kopiowanie obiektów blob między kontami usługi Azure Storage za pomocą narzędzia AzCopy w wersji 10 | Microsoft Docs
description: Ten artykuł zawiera kolekcję przykładowych poleceń programu AzCopy, które ułatwiają kopiowanie obiektów blob między kontami magazynu.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: bfdc91ac8f4ce618052cc78e76b27e8bdeabeb77
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502984"
---
# <a name="copy-blobs-between-azure-storage-accounts-by-using-azcopy"></a>Kopiowanie obiektów blob między kontami usługi Azure Storage przy użyciu narzędzia AzCopy

Obiekty blob, katalogi i kontenery można kopiować między kontami magazynu za pomocą narzędzia wiersza polecenia AzCopy w wersji 10. 

Aby wyświetlić przykłady innych typów zadań, takich jak przekazywanie plików, pobieranie obiektów blob i synchronizowanie z magazynem obiektów blob, zobacz linki przedstawione w sekcji [Następne](#next-steps) kroki tego artykułu.

Oprogramowanie AzCopy [używa interfejsów](/rest/api/storageservices/put-block-from-url) [API](/rest/api/storageservices/put-page-from-url)między serwerami, więc dane są kopiowane bezpośrednio między serwerami magazynu. Te operacje kopiowania nie używają przepustowości sieci komputera.

Aby pobrać program AzCopy i poznać sposoby podania poświadczeń autoryzacji dla usługi magazynu, zobacz Rozpoczynanie pracy [z programem AzCopy.](storage-use-azcopy-v10.md) 

## <a name="guidelines"></a>Wytyczne

Zastosuj poniższe wskazówki do poleceń programu AzCopy. 

- Klient musi mieć dostęp sieciowy do źródłowego i docelowego konta magazynu. Aby dowiedzieć się, jak skonfigurować ustawienia sieciowe dla każdego konta magazynu, zobacz Konfigurowanie zapór i sieci [wirtualnych usługi Azure Storage.](storage-network-security.md?toc=/azure/storage/blobs/toc.json)

- Dołącz token SAS do każdego źródłowego adresu URL. 

  W przypadku podania poświadczeń autoryzacji przy użyciu usługi Azure Active Directory (Azure AD) możesz pominąć token SAS tylko z docelowego adresu URL. Upewnij się, że na koncie docelowym zostały ustawione odpowiednie role. Zobacz [Opcję 1: Użyj Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory). 

  W przykładach w tym artykule założono, że tożsamość jest uwierzytelniana przy użyciu usługi Azure AD, dlatego w przykładach pominięto tokeny SAS z docelowego adresu URL.

-  W przypadku kopiowania na konto magazynu blokowych obiektów blob w warstwie Premium pomiń warstwę dostępu obiektu blob z operacji kopiowania, ustawiając na `s2s-preserve-access-tier` `false` (na przykład: `--s2s-preserve-access-tier=false` ). Konta magazynu blokowych obiektów blob w warstwie Premium nie obsługują warstw dostępu. 

- W przypadku kopiowania do lub z konta, które ma hierarchiczną przestrzeń nazw, użyj polecenia zamiast w `blob.core.windows.net` `dfs.core.windows.net` składni adresu URL. Dostęp do wielu protokołów [na platformie Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) umożliwia korzystanie z funkcji i jest to jedyna obsługiwana składnia scenariuszy kopiowania `blob.core.windows.net` kont. 

- Przepływność operacji kopiowania można zwiększyć, ustawiając wartość zmiennej `AZCOPY_CONCURRENCY_VALUE` środowiskowej. Aby dowiedzieć się więcej, zobacz [Zwiększanie współbieżności](storage-use-azcopy-optimize.md#increase-concurrency). 

- Jeśli źródłowe obiekty blob mają tagi indeksu i chcesz je zachować, musisz ponownie je dodać do docelowych obiektów blob. Aby uzyskać informacje na temat sposobu ustawienia tagów indeksu, zobacz sekcję Kopiowanie [obiektów blob](#copy-between-accounts-and-add-index-tags) do innego konta magazynu z tagami indeksu w tym artykule.

## <a name="copy-a-blob"></a>Kopiowanie obiektu blob

Skopiuj obiekt blob na inne konto magazynu za pomocą [polecenia azcopy copy.](storage-ref-azcopy-copy.md) 

> [!TIP]
> W tym przykładzie argumenty ścieżki są ujęte w a cudzysłów pojedynczy (""). Używaj a cudzysłowów pojedynczych we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w cudzysłów (""), a nie pojedynczy cudzysłów ("").

**Składnia**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'`

**Przykład**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

Operacja kopiowania jest synchroniczna, dlatego gdy polecenie zostanie zwrócone, oznacza to, że pliki zostały skopiowane. 

## <a name="copy-a-directory"></a>Kopiowanie katalogu

Skopiuj katalog do innego konta magazynu za pomocą [polecenia azcopy copy.](storage-ref-azcopy-copy.md) 

> [!TIP]
> W tym przykładzie argumenty ścieżki są ujęte w a cudzysłów pojedynczy (""). Używaj a cudzysłowów pojedynczych we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w cudzysłów (""), a nie pojedynczy cudzysłów ("").

**Składnia**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Przykład**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

Operacja kopiowania jest synchroniczna, dlatego gdy polecenie zostanie zwrócone, oznacza to, że pliki zostały skopiowane.

## <a name="copy-a-container"></a>Kopiowanie kontenera

Skopiuj kontener na inne konto magazynu za pomocą [polecenia azcopy copy.](storage-ref-azcopy-copy.md)

> [!TIP]
> W tym przykładzie argumenty ścieżki są ujęte w a cudzysłów pojedynczy (""). Używaj a cudzysłowów pojedynczych we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w cudzysłów (""), a nie pojedynczy cudzysłów ("").

**Składnia**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Przykład**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

Operacja kopiowania jest synchroniczna, dlatego gdy polecenie zostanie zwrócone, oznacza to, że pliki zostały skopiowane.

## <a name="copy-containers-directories-and-blobs"></a>Kopiowanie kontenerów, katalogów i obiektów blob

Skopiuj wszystkie kontenery, katalogi i obiekty blob na inne konto magazynu za pomocą [polecenia azcopy copy.](storage-ref-azcopy-copy.md)

> [!TIP]
> W tym przykładzie argumenty ścieżki są ujęte w a cudzysłów pojedynczy (""). Używaj a cudzysłowów pojedynczych we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójny cudzysłów ("") zamiast cudzysłowów pojedynczych ("").

**Składnia**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive`

**Przykład**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive
```

Operacja kopiowania jest synchroniczna, dlatego gdy polecenie zostanie zwrócone, oznacza to, że pliki zostały skopiowane.

<a id="copy-between-accounts-and-add-index-tags"></a>

## <a name="copy-blobs-and-add-index-tags"></a>Kopiowanie obiektów blob i dodawanie tagów indeksu

Skopiuj obiekty blob na inne konto magazynu i dodaj tagi indeksu obiektów [blob (wersja zapoznawcza)](../blobs/storage-manage-find-blobs.md) do docelowego obiektu blob.

Jeśli używasz autoryzacji usługi Azure AD, podmiot zabezpieczeń musi mieć przypisaną rolę właściciela danych obiektu blob usługi [Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) lub musi mieć uprawnienia do operacji dostawcy zasobów platformy Azure za pośrednictwem niestandardowej roli `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) platformy Azure. Jeśli używasz tokenu sygnatury dostępu współdzielonego (SAS), ten token musi zapewniać dostęp do tagów obiektu blob za pośrednictwem uprawnień `t` sygnatury dostępu współdzielonego.

Aby dodać tagi, użyj opcji wraz z parą klucz-wartość zakodowaną `--blob-tags` w adresie URL. 

Aby na przykład dodać klucz `my tag` i wartość , należy dodać parametr do `my tag value` `--blob-tags='my%20tag=my%20tag%20value'` parametru docelowego. 

Oddziel wiele tagów indeksu przy użyciu ampersand ( `&` ).  Jeśli na przykład chcesz dodać klucz i wartość , kompletny `my second tag` `my second tag value` ciąg opcji to `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

Poniższe przykłady pokazują, jak używać `--blob-tags` opcji .

> [!TIP]
> W tych przykładach argumenty ścieżki są ujęte w pojedynczy cudzysłów (""). Używaj a cudzysłowów pojedynczych we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w cudzysłów ("") zamiast a cudzysłowów pojedynczych ("").

**Przykład obiektu blob**

```azcopy

`azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`
```

**Przykład katalogu**

```azcopy
`azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`
```

 **Przykład kontenera**

```azcopy
`azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`
```

**Przykład konta**

```azcopy
`azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`
```

Operacja kopiowania jest synchroniczna, dlatego gdy polecenie zostanie zwrócone, oznacza to, że pliki zostały skopiowane.

> [!NOTE]
> Jeśli określisz katalog, kontener lub konto dla źródła, wszystkie obiekty blob skopiowane do miejsca docelowego będą mieć te same tagi, które określisz w poleceniu .

## <a name="copy-with-optional-flags"></a>Kopiowanie z opcjonalnymi flagami

Operację kopiowania można dostosować przy użyciu opcjonalnych flag. Oto kilka przykładów.

|Scenariusz|Flaga|
|---|---|
|Skopiuj obiekty blob jako blokowe, stronicowe lub uzupełnialne obiekty blob.|**--blob-type** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
|Skopiuj do określonej warstwy dostępu (takiej jak warstwa Archiwum).|**--block-blob-tier** = \[ Brak \| \| gorącego \| archiwum chłodnego\]|
|Automatycznie dekompresuj pliki.|**--dekompresji** = \[ gzip \| deflate\]|

Aby uzyskać pełną listę, zobacz [opcje](storage-ref-azcopy-copy.md#options). 

## <a name="next-steps"></a>Następne kroki

Więcej przykładów można znaleźć w tych artykułach:

- [Przykłady: Upload](storage-use-azcopy-blobs-upload.md)
- [Przykłady: Pobierz](storage-use-azcopy-blobs-download.md)
- [Przykłady: Synchronizowanie](storage-use-azcopy-blobs-synchronize.md)
- [Przykłady: Zasobniki usługi Amazon S3](storage-use-azcopy-s3.md)
- [Przykłady: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Przykłady: Azure Files](storage-use-azcopy-files.md)
- [Samouczek: Migracja danych lokalnych do magazynu w chmurze za pomocą narzędzia AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Zapoznaj się z tymi artykułami, aby skonfigurować ustawienia, zoptymalizować wydajność i rozwiązać problemy:

- [Ustawienia konfiguracji programu AzCopy](storage-ref-azcopy-configuration-settings.md)
- [Optymalizowanie wydajności programu AzCopy](storage-use-azcopy-optimize.md)
- [Rozwiązywanie problemów z programem AzCopy w wersji 10 w usłudze Azure Storage przy użyciu plików dziennika](storage-use-azcopy-configure.md)