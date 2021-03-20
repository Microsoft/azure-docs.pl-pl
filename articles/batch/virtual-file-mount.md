---
title: Instalowanie wirtualnego systemu plików w puli
description: Dowiedz się, jak zainstalować wirtualny system plików w puli wsadowej.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 08/13/2019
ms.openlocfilehash: df03275fdeea88df1a2f2b6e2cda55021497cdf7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89145488"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Instalowanie wirtualnego systemu plików w puli partii

Azure Batch teraz obsługuje instalowanie magazynu w chmurze lub zewnętrznego systemu plików w węzłach obliczeniowych systemu Windows lub Linux w pulach usługi Batch. Gdy węzeł obliczeniowy jest przyłączany do puli, wirtualny system plików jest instalowany i traktowany jako dysk lokalny w tym węźle. Można instalować systemy plików, takie jak Azure Files, Azure Blob Storage, Network File System (NFS), w tym [pamięci podręcznej VFXT avere](../avere-vfxt/avere-vfxt-overview.md)lub Common Internet File System (CIFS).

W tym artykule dowiesz się, jak zainstalować wirtualny system plików w puli węzłów obliczeniowych przy użyciu [biblioteki zarządzania usługą Batch dla platformy .NET](/dotnet/api/overview/azure/batch).

> [!NOTE]
> Instalowanie wirtualnego systemu plików jest obsługiwane w pulach wsadowym utworzonym w dniu lub po 2019-08-19. Pule wsadowe utworzone przed 2019-08-19 nie obsługują tej funkcji.
> 
> Interfejsy API służące do instalowania systemów plików w węźle obliczeniowym są częścią biblioteki usługi [Batch .NET](/dotnet/api/microsoft.azure.batch) .

## <a name="benefits-of-mounting-on-a-pool"></a>Zalety instalowania w puli

Zainstalowanie systemu plików w puli, zamiast zezwalania na pobieranie własnych danych z dużego zestawu danych, sprawia, że zadania są łatwiejsze i wydajniejsze w celu uzyskania dostępu do wymaganych danych.

Rozważmy scenariusz z wieloma zadaniami wymagającymi dostępu do wspólnego zestawu danych, takich jak renderowanie filmu. Każde zadanie renderuje co najmniej jedną klatkę naraz z plików sceny. Zainstalowanie dysku, który zawiera pliki sceny, ułatwia korzystanie z węzłów obliczeniowych w celu uzyskania dostępu do udostępnionych danych. Ponadto podstawowy system plików można wybrać i skalować niezależnie w zależności od wydajności i skali (przepływności i liczby IOPS) wymaganej przez liczbę węzłów obliczeniowych, które jednocześnie uzyskują dostęp do danych. Na przykład avere rozproszonej pamięci podręcznej [vFXT](../avere-vfxt/avere-vfxt-overview.md) może służyć do obsługi dużych ruchów na skalę obrazu z tysiącami współbieżnych węzłów renderowania, które uzyskują dostęp do danych źródłowych, które znajdują się lokalnie. Alternatywnie w przypadku danych, które już znajdują się w magazynie obiektów BLOB opartych na chmurze, [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) można użyć do zainstalowania tych danych jako lokalnego systemu plików. Blobfuse jest dostępna tylko w węzłach systemu Linux, ale [Azure Files](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/) udostępnia podobny przepływ pracy i jest dostępny zarówno w systemie Windows, jak i Linux.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Instalowanie wirtualnego systemu plików w puli  

Zainstalowanie wirtualnego systemu plików w puli sprawia, że system plików jest dostępny dla wszystkich węzłów obliczeniowych w puli. System plików jest konfigurowany, gdy węzeł obliczeniowy jest przyłączany do puli lub gdy węzeł jest ponownie uruchamiany lub odtwarzany z obrazu.

Aby zainstalować system plików w puli, Utwórz `MountConfiguration` obiekt. Wybierz obiekt, który pasuje do wirtualnego systemu plików: `AzureBlobFileSystemConfiguration` , `AzureFileShareConfiguration` , `NfsMountConfiguration` lub `CifsMountConfiguration` .

Wszystkie obiekty konfiguracji instalacji muszą mieć następujące parametry podstawowe. Niektóre konfiguracje instalacji mają parametry specyficzne dla używanego systemu plików, które zostały omówione bardziej szczegółowo w przykładach kodu.

- **Nazwa konta lub źródło**: Aby zainstalować wirtualny udział plików, potrzebna jest nazwa konta magazynu lub jego źródła.
- **Względna ścieżka lub źródło instalacji**: Lokalizacja systemu plików zainstalowanego w węźle obliczeniowym względem `fsmounts` katalogu standardowego dostępnego w węźle za pośrednictwem `AZ_BATCH_NODE_MOUNTS_DIR` . Dokładna lokalizacja różni się w zależności od systemu operacyjnego używanego w węźle. Na przykład fizyczna lokalizacja w węźle Ubuntu jest zamapowana na `mnt\batch\tasks\fsmounts` , a w węźle CentOS, do którego jest zamapowany `mnt\resources\batch\tasks\fsmounts` .
- **Opcje instalacji lub opcje blobfuse**: te opcje opisują konkretne parametry instalowania systemu plików.

Po `MountConfiguration` utworzeniu obiektu Przypisz obiekt do `MountConfigurationList` właściwości podczas tworzenia puli. System plików jest instalowany, gdy węzeł jest przyłączony do puli lub gdy węzeł jest ponownie uruchamiany lub odtwarzany z obrazu.

Gdy system plików jest zainstalowany, tworzona jest zmienna środowiskowa, `AZ_BATCH_NODE_MOUNTS_DIR` która wskazuje lokalizację zainstalowanych systemów plików, a także pliki dziennika, które są przydatne do rozwiązywania problemów i debugowania. Pliki dziennika są wyjaśnione bardziej szczegółowo w sekcji [Diagnozowanie błędów instalacji](#diagnose-mount-errors) .  

> [!IMPORTANT]
> Maksymalna liczba zainstalowanych systemów plików w puli wynosi 10. Zobacz [przydziały usługi Batch i limity](batch-quota-limit.md#other-limits) dotyczące szczegółów i innych limitów.

## <a name="examples"></a>Przykłady

Poniższe przykłady kodu przedstawiają Instalowanie różnych udziałów plików w puli węzłów obliczeniowych.

### <a name="azure-files-share"></a>Udział Azure Files

Azure Files to standardowa oferta systemu plików w chmurze platformy Azure. Aby dowiedzieć się więcej na temat pobierania dowolnego z parametrów w przykładowym kodzie konfiguracji instalacji, zobacz [Korzystanie z udziału Azure Files](../storage/files/storage-how-to-use-files-windows.md).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "{storage-account-name}",
                AzureFileUrl = "https://{storage-account-name}.file.core.windows.net/{file-share-name}",
                AccountKey = "{storage-account-key}",
                RelativeMountPath = "S",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>System plików obiektów blob platformy Azure

Innym rozwiązaniem jest użycie usługi Azure Blob Storage za pośrednictwem [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md). Zainstalowanie systemu plików BLOB wymaga `AccountKey` lub `SasKey` dla konta magazynu. Aby uzyskać informacje na temat uzyskiwania tych kluczy, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../storage/common/storage-account-keys-manage.md)lub [przy użyciu sygnatur dostępu współdzielonego (SAS)](../storage/common/storage-sas-overview.md). Aby uzyskać więcej informacji na temat korzystania z programu blobfuse, zobacz temat [Rozwiązywanie problemów](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ)z blobfuse. Aby uzyskać domyślny dostęp do zainstalowanego katalogu blobfuse, uruchom zadanie jako **administrator**. Blobfuse instaluje katalog w miejscu użytkownika, a podczas tworzenia puli jest instalowany jako główny. W systemie Linux wszystkie zadania **administratora** są głównymi. Wszystkie opcje dla modułu bezpiecznik są opisane na [stronie odmowa](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html).

Oprócz przewodnika rozwiązywania problemów problemy z usługą GitHub w repozytorium blobfuse są przydatnym sposobem na sprawdzenie bieżących problemów i rozwiązań związanych z blobfuse. Aby uzyskać więcej informacji, zobacz [blobfuse problemy](https://github.com/Azure/azure-storage-fuse/issues).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "",
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

### <a name="network-file-system"></a>Sieciowy system plików

Systemy plików NFS można również zainstalować w węzłach puli, co umożliwia łatwe uzyskiwanie dostępu do tradycyjnych systemów plików przez węzły Azure Batch. Może to być jeden serwer NFS wdrożony w chmurze lub lokalny serwer NFS, do którego można uzyskać dostęp za pośrednictwem sieci wirtualnej. Alternatywnie możesz skorzystać z rozwiązania rozproszonej pamięci podręcznej [avere vFXT](../avere-vfxt/avere-vfxt-overview.md) , które zapewnia bezproblemową łączność z lokalnym magazynem, odczytywanie danych na żądanie do pamięci podręcznej oraz zapewnia wysoką wydajność i skalowalność węzłów obliczeniowych opartych na chmurze.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=1.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>Wspólny internetowy system plików

W celu łatwego dostępu do węzłów puli można także zainstalować systemy plików CIFS (Common Internet File System), które umożliwiają korzystanie z tych samych węzłów Azure Batch. CIFS to protokół udostępniania plików, który zapewnia otwarty i Międzyplatformowy mechanizm do żądania plików i usług serwera sieciowego. Protokół CIFS jest oparty na rozszerzonej wersji protokołu SMB (Server Message Block) firmy Microsoft do udostępniania plików internetowych i intranetowych oraz służy do instalowania zewnętrznych systemów plików w węzłach systemu Windows. Aby dowiedzieć się więcej o protokole SMB, zobacz [serwer plików i protokół SMB](/windows-server/storage/file-server/file-server-smb-overview).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>Diagnozuj błędy instalacji

Jeśli konfiguracja instalacji nie powiedzie się, węzeł obliczeniowy w puli zakończy się niepowodzeniem, a stan węzła stanie się bezużyteczny. Aby zdiagnozować błąd konfiguracji instalacji, zbadaj [`ComputeNodeError`](/rest/api/batchservice/computenode/get#computenodeerror) Właściwość w celu uzyskania szczegółowych informacji o błędzie.

Aby pobrać pliki dziennika do debugowania, użyj [OutputFiles](batch-task-output-files.md) do przekazania `*.log` plików. `*.log`Pliki zawierają informacje o instalacji systemu plików w `AZ_BATCH_NODE_MOUNTS_DIR` lokalizacji. Pliki dziennika instalacji mają format: `<type>-<mountDirOrDrive>.log` dla każdej instalacji. Na przykład `cifs` Instalacja w katalogu instalacji o nazwie `test` będzie miała plik dziennika instalacji o nazwie: `cifs-test.log` .

## <a name="supported-skus"></a>Obsługiwane jednostki SKU

| Publisher | Oferta | SKU | Udział Azure Files | Blobfuse | Instalacja systemu plików NFS | Instalacja CIFS |
|---|---|---|---|---|---|---|
| partia | Renderowanie — centos73 | dawania | :heavy_check_mark: <br>Uwaga: zgodność z CentOS 7,7</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16,04 – LTS, 18,04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8| :heavy_check_mark: | y | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft — ads | Linux — dane-nauka — maszyna wirtualna | linuxdsvm | :heavy_check_mark: <br>Uwaga: zgodne z CentOS 7,4. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-Azure-Batch | CentOS — kontener | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-Azure-Batch | CentOS — kontener — RDMA | 7,4 | :heavy_check_mark: <br>Uwaga: obsługuje magazyn A_8 lub 9</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-Azure-Batch | Ubuntu — serwer-kontener | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-dsvm | Linux-Data-nauka-VM-Ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS — HPC | 7,4, 7,3, 7,1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | y | y | y | y |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | y | y | y |

## <a name="next-steps"></a>Następne kroki

- Więcej informacji na temat instalowania udziału Azure Files w [systemie Windows](../storage/files/storage-how-to-use-files-windows.md) lub [Linux](../storage/files/storage-how-to-use-files-linux.md).
- Dowiedz się więcej o używaniu i instalowaniu wirtualnych systemów plików [blobfuse](https://github.com/Azure/azure-storage-fuse) .
- Zapoznaj się z tematem [system plików sieciowych](/windows-server/storage/nfs/nfs-overview) , aby uzyskać informacje na temat systemu plików NFS i jego aplikacji.
- Więcej informacji na temat protokołu CIFS można znaleźć w temacie [Omówienie protokołów SMB i protokołu CIFS firmy Microsoft](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) .
