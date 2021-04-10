---
title: Tworzenie kopii zapasowych na żądanie na platformie Azure Service Fabric
description: Użyj funkcji tworzenia kopii zapasowych i przywracania w Service Fabric, aby utworzyć kopię zapasową danych aplikacji w zależności od potrzeb.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: d7986c8cd8d0714215c7b4dc57170be346e627ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98928036"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Tworzenie kopii zapasowych na żądanie na platformie Azure Service Fabric

Można utworzyć kopię zapasową danych niezawodnych usług stanowych i Reliable Actors, aby rozwiązać awarie lub sytuacje utraty danych.

Usługa Azure Service Fabric ma funkcje do [regularnego tworzenia kopii](service-fabric-backuprestoreservice-quickstart-azurecluster.md) zapasowych danych i tworzenia kopii zapasowych danych. Tworzenie kopii zapasowych na żądanie jest przydatne, ponieważ chroni przed / _uszkodzeniem danych_ utraty danych z powodu planowanych zmian w podstawowej usłudze lub jego środowisku.

Funkcje tworzenia kopii zapasowych na żądanie są przydatne do przechwytywania stanu usług przed ręcznym wyzwalaniem operacji środowiska usługi lub usługi. Jeśli na przykład wprowadzisz zmiany w plikach binarnych usługi podczas uaktualniania lub obniżania poziomu usługi. W takim przypadku kopia zapasowa na żądanie może pomóc w ochronie danych przed uszkodzeniem przez błędy kodu aplikacji.
## <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj pakiet Microsoft. servicefabric. PowerShell. http module (wersja zapoznawcza) na potrzeby wykonywania wywołań konfiguracyjnych.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

> [!NOTE]
> Jeśli wersja PowerShellGet jest mniejsza niż 1.6.0, należy ją zaktualizować, aby dodać obsługę flagi *-AllowPrerelease* :
>
> `Install-Module -Name PowerShellGet -Force`

- Upewnij się, że klaster jest połączony przy użyciu `Connect-SFCluster` polecenia przed wykonaniem dowolnego żądania konfiguracji przy użyciu modułu Microsoft. servicefabric. PowerShell. http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggering-on-demand-backup"></a>Wyzwalanie kopii zapasowej na żądanie

Tworzenie kopii zapasowych na żądanie wymaga podania szczegółowych informacji o magazynie. Lokalizację kopii zapasowej na żądanie należy określić w ramach okresowych zasad tworzenia kopii zapasowych lub w żądaniu kopii zapasowej na żądanie.

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>Tworzenie kopii zapasowych na żądanie do magazynu określonego przez okresowe zasady tworzenia kopii zapasowych

Można skonfigurować okresowe zasady tworzenia kopii zapasowych, aby używać partycji niezawodnej usługi stanowej lub niezawodnego aktora w celu uzyskania dodatkowej kopii zapasowej na żądanie do magazynu.

Poniższy przypadek to kontynuacja scenariusza w [włączaniu okresowej kopii zapasowej dla niezawodnej usługi stanowej i Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). W takim przypadku należy włączyć zasady tworzenia kopii zapasowych, aby używać partycji i kopii zapasowej, z ustawioną częstotliwością w usłudze Azure Storage.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell przy użyciu modułu Microsoft. servicefabric. PowerShell. http

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' 

```

#### <a name="rest-call-using-powershell"></a>Wywołanie REST przy użyciu programu PowerShell

Użyj interfejsu API [BackupPartition](/rest/api/servicefabric/sfclient-api-backuppartition) , aby skonfigurować wyzwalanie dla kopii zapasowej na żądanie dla identyfikatora partycji `974bd92a-b395-4631-8a7f-53bd4ae9cf22` .

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Użyj interfejsu API [GetBackupProgress](/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) , aby włączyć śledzenie [postępu tworzenia kopii zapasowej na żądanie](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="on-demand-backup-to-specified-storage"></a>Tworzenie kopii zapasowej na żądanie do określonego magazynu

Możesz zażądać kopii zapasowej na żądanie dla partycji niezawodnej usługi stanowej lub niezawodnego aktora. Podaj informacje o magazynie jako część żądania kopii zapasowej na żądanie.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell przy użyciu modułu Microsoft. servicefabric. PowerShell. http

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -AzureBlobStore -ConnectionString  'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Wywołanie REST przy użyciu programu PowerShell

Użyj interfejsu API [BackupPartition](/rest/api/servicefabric/sfclient-api-backuppartition) , aby skonfigurować wyzwalanie dla kopii zapasowej na żądanie dla identyfikatora partycji `974bd92a-b395-4631-8a7f-53bd4ae9cf22` . Uwzględnij następujące informacje dotyczące usługi Azure Storage:

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$OnDemandBackupRequest = @{
    BackupStorage = $StorageInfo
}

$body = (ConvertTo-Json $OnDemandBackupRequest)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Za pomocą interfejsu API [GetBackupProgress](/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) można skonfigurować śledzenie [postępu tworzenia kopii zapasowej na żądanie](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="using-service-fabric-explorer"></a>Używanie Service Fabric Explorer
Upewnij się, że tryb zaawansowany został włączony w ustawieniach Service Fabric Explorer.
1. Wybierz żądane partycje i kliknij akcje. 
2. Wybierz pozycję Wyzwalaj kopię zapasową partycji i wprowadź informacje dotyczące platformy Azure:

    ![Wyzwalanie tworzenia kopii zapasowej partycji][0]

    lub przeudziale:

    ![Wywołaj udział plików kopii zapasowej partycji][1]

## <a name="tracking-on-demand-backup-progress"></a>Śledzenie postępu tworzenia kopii zapasowej na żądanie

Partycja niezawodnej usługi stanowej lub niezawodnego aktora akceptuje tylko jedno żądanie kopii zapasowej na żądanie. Inne żądanie można zaakceptować dopiero po zakończeniu bieżącego żądania tworzenia kopii zapasowej na żądanie.

Różne partycje mogą wyzwalać żądania kopii zapasowych na żądanie w tym samym czasie.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell przy użyciu modułu Microsoft. servicefabric. PowerShell. http

```powershell

Get-SFPartitionBackupProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'

```
#### <a name="rest-call-using-powershell"></a>Wywołanie REST przy użyciu programu PowerShell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Żądania kopii zapasowej na żądanie mogą znajdować się w następujących stanach:

- **Zaakceptowano**: kopia zapasowa została uruchomiona na partycji i jest w toku.
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **Powodzenie**, **Niepowodzenie** lub **przekroczenie limitu czasu**: żądana kopia zapasowa na żądanie może zostać wykonana w jednym z następujących stanów:
  - **Sukces**: stan kopii zapasowej _zakończony sukcesem_ wskazuje, że kopia zapasowa stanu partycji została zakończona pomyślnie. Odpowiedź zawiera _BackupEpoch_ i _BackupLSN_ dla partycji oraz godzinę w formacie UTC.
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **Błąd**: stan kopii zapasowej _nie powiodło_ się wskazuje, że wystąpił błąd podczas tworzenia kopii zapasowej stanu partycji. Przyczyna niepowodzenia jest określona w odpowiedzi.
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **Limit czasu**: _przekroczenie limitu czasu_ kopii zapasowej wskazuje, że nie można utworzyć kopii zapasowej stanu partycji w danym czasie. Domyślna wartość limitu czasu to 10 minut. Zainicjuj nowe żądanie kopii zapasowej na żądanie z większą [BackupTimeout](/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) w tym scenariuszu.
    ```
    BackupState             : Timeout
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
    ```

## <a name="next-steps"></a>Następne kroki

- [Omówienie okresowej konfiguracji kopii zapasowej](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Dokumentacja interfejsu API REST usługi BackupRestore](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/trigger-partition-backup.png
[1]: ./media/service-fabric-backuprestoreservice/trigger-backup-fileshare.png
