---
title: Instalowanie wirtualnego systemu plików w puli
description: Dowiedz się, jak zainstalować wirtualny system plików w puli wsadowej.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 03/26/2021
ms.openlocfilehash: dc5fbdf9ca0df8362a8999856c3f7163dd5e59b9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626031"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Instalowanie wirtualnego systemu plików w puli partii

Azure Batch obsługuje instalowanie magazynu w chmurze lub zewnętrznego systemu plików w węzłach obliczeniowych systemu Windows lub Linux w pulach usługi Batch. Gdy węzeł obliczeniowy jest przyłączany do puli, wirtualny system plików jest instalowany i traktowany jako dysk lokalny w tym węźle. Można instalować systemy plików, takie jak Azure Files, Azure Blob Storage, Network File System (NFS), w tym [pamięci podręcznej VFXT avere](../avere-vfxt/avere-vfxt-overview.md)lub Common Internet File System (CIFS).

W tym artykule dowiesz się, jak zainstalować wirtualny system plików w puli węzłów obliczeniowych przy użyciu [biblioteki zarządzania usługą Batch dla platformy .NET](/dotnet/api/overview/azure/batch).

> [!NOTE]
> Instalowanie wirtualnego systemu plików jest obsługiwane tylko w pulach wsadowych utworzonych w dniu lub po 8 sierpnia 2019. Pule wsadowe utworzone przed tą datą nie będą obsługiwały tej funkcji.

## <a name="benefits-of-mounting-on-a-pool"></a>Zalety instalowania w puli

Zainstalowanie systemu plików w puli, zamiast zezwalania na pobieranie własnych danych z dużego zestawu danych, sprawia, że zadania są łatwiejsze i wydajniejsze w celu uzyskania dostępu do wymaganych danych.

Rozważmy scenariusz z wieloma zadaniami wymagającymi dostępu do wspólnego zestawu danych, takich jak renderowanie filmu. Każde zadanie renderuje co najmniej jedną klatkę naraz z plików sceny. Zainstalowanie dysku, który zawiera pliki sceny, ułatwia korzystanie z węzłów obliczeniowych w celu uzyskania dostępu do udostępnionych danych.

Ponadto podstawowy system plików można wybrać i skalować niezależnie w zależności od wydajności i skali (przepływności i liczby IOPS) wymaganej przez liczbę węzłów obliczeniowych, które jednocześnie uzyskują dostęp do danych. Na przykład avere rozproszonej pamięci podręcznej [vFXT](../avere-vfxt/avere-vfxt-overview.md) może służyć do obsługi dużych ruchów na skalę obrazu z tysiącami współbieżnych węzłów renderowania, które uzyskują dostęp do danych źródłowych, które znajdują się lokalnie. Alternatywnie w przypadku danych, które już znajdują się w magazynie obiektów BLOB opartych na chmurze, [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) można użyć do zainstalowania tych danych jako lokalnego systemu plików. Blobfuse jest dostępna tylko w węzłach systemu Linux, chociaż [Azure Files](../storage/files/storage-files-introduction.md) zapewnia podobny przepływ pracy i jest dostępny zarówno w systemie Windows, jak i Linux.

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

Innym rozwiązaniem jest użycie usługi Azure Blob Storage za pośrednictwem [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md). Zainstalowanie systemu plików BLOB wymaga `AccountKey` lub `SasKey` dla konta magazynu. Aby uzyskać informacje na temat uzyskiwania tych kluczy, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../storage/common/storage-account-keys-manage.md) lub [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](../storage/common/storage-sas-overview.md). Aby uzyskać więcej informacji i porad dotyczących używania blobfuse, zobacz blobfuse.

Aby uzyskać domyślny dostęp do zainstalowanego katalogu blobfuse, uruchom zadanie jako **administrator**. Blobfuse instaluje katalog w miejscu użytkownika, a podczas tworzenia puli jest instalowany jako główny. W systemie Linux wszystkie zadania **administratora** są głównymi. Wszystkie opcje dla modułu bezpiecznik są opisane na [stronie odmowa](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html).

Zapoznaj się z [często zadawanymi pytaniami](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ) , aby uzyskać więcej informacji oraz porady dotyczące korzystania z programu blobfuse Możesz również przejrzeć [problemy z usługą GitHub w repozytorium blobfuse,](https://github.com/Azure/azure-storage-fuse/issues) aby sprawdzić bieżące problemy i rozwiązania blobfuse.

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

Systemy plików NFS można zainstalować w węzłach puli, umożliwiając dostęp do tradycyjnych systemów plików przez Azure Batch. Może to być jeden serwer NFS wdrożony w chmurze lub lokalny serwer NFS, do którego można uzyskać dostęp za pośrednictwem sieci wirtualnej. Alternatywnie możesz użyć rozwiązania [avere vFXT](../avere-vfxt/avere-vfxt-overview.md) Distributed cache w pamięci, aby uzyskać intensywne operacje obliczeniowe o wysokiej wydajności (HPC).

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

Instalowanie [wspólnych systemów plików CIFS (Common Internet File Systems)](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) w węzłach puli jest innym sposobem zapewnienia dostępu do tradycyjnych systemów plików. CIFS to protokół udostępniania plików, który zapewnia otwarty i Międzyplatformowy mechanizm do żądania plików i usług serwera sieciowego. Protokół CIFS jest oparty na rozszerzonej wersji protokołu [bloku komunikatów serwera (SMB)](/windows-server/storage/file-server/file-server-smb-overview) do udostępniania plików internetowych i intranetowych oraz może służyć do instalowania zewnętrznych systemów plików w węzłach systemu Windows.

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

Jeśli konfiguracja instalacji nie powiedzie się, węzeł obliczeniowy w puli zakończy się niepowodzeniem, a stan węzła zostanie ustawiony na `unusable` . Aby zdiagnozować błąd konfiguracji instalacji, zbadaj [`ComputeNodeError`](/rest/api/batchservice/computenode/get#computenodeerror) Właściwość w celu uzyskania szczegółowych informacji o błędzie.

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

## <a name="networking-requirements"></a>Wymagania dotyczące sieci

W przypadku korzystania z instalacji plików wirtualnych z [pulami Azure Batch w sieci wirtualnej](batch-virtual-network.md)należy pamiętać o następujących wymaganiach i upewnić się, że żaden wymagany ruch nie jest blokowany.

- **Azure Files**:
  - Wymaga otwartego portu TCP 445 dla ruchu do/z tagu usługi magazynu. Aby uzyskać więcej informacji, zobacz [Korzystanie z udziału plików platformy Azure w systemie Windows](../storage/files/storage-how-to-use-files-windows.md#prerequisites).
- **Blobfuse**:
  - Wymaga otwartego portu TCP 443 dla ruchu do/z tagu usługi magazynu.
  - https://packages.microsoft.comAby można było pobrać pakiety blobfuse i GPG, maszyny wirtualne muszą mieć dostęp do programu. W zależności od konfiguracji może być również konieczne uzyskanie dostępu do innych adresów URL w celu pobrania dodatkowych pakietów.
- **Sieciowy system plików (NFS)**:
  - Wymaga dostępu do portu 2049 (domyślnie; konfiguracja może mieć inne wymagania).
  - Aby można było pobrać pakiet plików NFS (Debian lub Ubuntu) lub NFS-narzędzia (dla CentOS), maszyny wirtualne muszą mieć dostęp do odpowiedniego Menedżera pakietów. Ten adres URL może się różnić w zależności od wersji systemu operacyjnego. W zależności od konfiguracji może być również konieczne uzyskanie dostępu do innych adresów URL w celu pobrania dodatkowych pakietów.
- **Common Internet File System (CIFS)**:
  - Wymaga dostępu do portu TCP 445.
  - Aby można było pobrać pakiet CIFS-narzędzia, maszyny wirtualne muszą mieć dostęp do odpowiednich menedżerów pakietów. Ten adres URL może się różnić w zależności od wersji systemu operacyjnego.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o instalowaniu udziału Azure Files w [systemie Windows](../storage/files/storage-how-to-use-files-windows.md) lub [Linux](../storage/files/storage-how-to-use-files-linux.md).
- Dowiedz się więcej o używaniu i instalowaniu wirtualnych systemów plików [blobfuse](https://github.com/Azure/azure-storage-fuse) .
- Zapoznaj się z tematem [system plików sieciowych](/windows-server/storage/nfs/nfs-overview) , aby uzyskać informacje na temat systemu plików NFS i jego aplikacji.
- Więcej informacji na temat protokołu CIFS można znaleźć w temacie [Omówienie protokołów SMB i protokołu CIFS firmy Microsoft](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) .
