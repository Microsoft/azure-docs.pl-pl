---
title: Przywracanie kopii zapasowej na platformie Azure Service Fabric
description: Użyj funkcji okresowej kopii zapasowej i przywracania w Service Fabric, aby przywrócić dane z kopii zapasowej danych aplikacji.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 8566d82ef0d91caff47ff17a9cb12fcdc8241884
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98928020"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Przywracanie kopii zapasowej na platformie Azure Service Fabric

Na platformie Azure Service Fabric niezawodne usługi stanowe i Reliable Actors mogą zachować modyfikowalny, autorytatywny stan po zakończeniu żądania i transakcji odpowiedzi. Usługa stanowa może działać przez długi czas lub utracić informacje z powodu awarii. Jeśli tak się stanie, usługa musi zostać przywrócona z najnowszej akceptowalnej kopii zapasowej, aby mogła ona nadal działać.

Można na przykład skonfigurować usługę do tworzenia kopii zapasowych danych, aby chronić je przed następującymi scenariuszami:

- **Przypadek odzyskiwania po awarii**: trwała utrata całego klastra Service Fabric.
- **Wypadek utraty danych**: trwała utrata większości replik partycji usługi.
- **Wypadek utraty danych**: Przypadkowe usunięcie lub uszkodzenie usługi. Na przykład administrator błędnie usuwa usługę.
- **Przypadek uszkodzenia danych**: usterki w usłudze powodują uszkodzenie danych. Na przykład uszkodzenie danych może wystąpić, gdy uaktualnienie kodu usługi zapisuje błędne dane do niezawodnej kolekcji. W takim przypadku może być konieczne przywrócenie kodu i danych do wcześniejszego stanu.

## <a name="prerequisites"></a>Wymagania wstępne

- Aby wyzwolić przywracanie, dla klastra musi być włączona _Usługa analizy błędów (FAS)_ .
- _Usługa przywracania kopii zapasowych (BRS)_ utworzyła kopię zapasową.
- Przywracanie może być wyzwalane tylko w partycji.
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


## <a name="triggered-restore"></a>Wyzwolono przywracanie

Przywracanie może zostać wyzwolone dla jednego z następujących scenariuszy:

- Przywracanie danych na potrzeby _odzyskiwania po awarii_.
- Przywracanie danych w celu _uszkodzenia i utraty_ danych.

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>Przywracanie danych w przypadku odzyskiwania po awarii

Jeśli cały klaster Service Fabric zostanie utracony, można odzyskać dane dla partycji niezawodnej usługi stanowej i Reliable Actors. Żądaną kopię zapasową można wybrać z listy, jeśli używasz [GetBackupAPI z informacjami o magazynie kopii zapasowych](/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). Wyliczenie kopii zapasowej może dotyczyć aplikacji, usługi lub partycji.

W poniższym przykładzie Załóżmy, że utracony klaster jest tym samym klastrem, który jest określany jako [umożliwia okresowe tworzenie kopii zapasowej niezawodnej usługi stanowej i Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). W tym przypadku program `SampleApp` jest wdrażany z włączonymi zasadami tworzenia kopii zapasowych, a kopie zapasowe są konfigurowane do usługi Azure Storage.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell przy użyciu modułu Microsoft. servicefabric. PowerShell. http

```powershell
Get-SFBackupsFromBackupLocation -Application -ApplicationName 'fabric:/SampleApp' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Wywołanie REST przy użyciu programu PowerShell

Wykonaj skrypt programu PowerShell, aby użyć interfejsu API REST w celu zwrócenia listy kopii zapasowych utworzonych dla wszystkich partycji w `SampleApp` aplikacji. Interfejs API wymaga informacji o magazynie kopii zapasowych, aby wyświetlić listę dostępnych kopii zapasowych.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$BackupEntity = @{
    EntityKind = 'Application'
    ApplicationName='fabric:/SampleApp'
}

$BackupLocationAndEntityInfo = @{
    Storage = $StorageInfo
    BackupEntity = $BackupEntity
}

$body = (ConvertTo-Json $BackupLocationAndEntityInfo)
$url = "https://myalternatesfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Przykładowe dane wyjściowe dla powyższego przebiegu:

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 
*
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
*
BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            :
```

Aby wyzwolić przywracanie, wybierz jedną z kopii zapasowych. Na przykład bieżąca kopia zapasowa odzyskiwania po awarii może być następująca:

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

W przypadku interfejsu API przywracania należy podać szczegóły dotyczące _BackupId_ i _BackupLocation_ .

Należy również wybrać partycję docelową w klastrze alternatywnym, zgodnie z opisem w [schemacie partycji](service-fabric-concepts-partitioning.md#get-started-with-partitioning). Alternatywna kopia zapasowa klastra jest przywracana do partycji określonej w schemacie partycji z oryginalnego utraconego klastra.

Jeśli identyfikator partycji w klastrze alternatywnym to `1c42c47f-439e-4e09-98b9-88b8f60800c6` , można go zmapować na oryginalny identyfikator partycji klastra, `974bd92a-b395-4631-8a7f-53bd4ae9cf22` porównując klucz wysoki i niski dla _partycjonowania z podziałem na partycje (UniformInt64Partition)_.

W przypadku _nazwanego partycjonowania_ wartość nazwy jest porównywana z rozpoznawaniem partycji docelowej w alternatywnym klastrze.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell przy użyciu modułu Microsoft. servicefabric. PowerShell. http

```powershell

Restore-SFPartition  -PartitionId '1c42c47f-439e-4e09-98b9-88b8f60800c6' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Wywołanie REST przy użyciu programu PowerShell

Użytkownik żąda odzyskania względem partycji klastra kopii zapasowej za pomocą następującego [interfejsu API przywracania](/rest/api/servicefabric/sfclient-api-restorepartition):

```powershell

$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
    BackupStorage  = $StorageInfo
}

$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Postęp przywracania można śledzić za pomocą TrackRestoreProgress.

> [!NOTE]
> W przypadku przywracania partycji przy użyciu programu PowerShell, jeśli BackupLocation ma znak "$", należy użyć znaku "~"
>

### <a name="using-service-fabric-explorer"></a>Używanie Service Fabric Explorer
Możesz wyzwolić przywracanie z Service Fabric Explorer. Upewnij się, że tryb zaawansowany został włączony w ustawieniach Service Fabric Explorer.
1. Wybierz żądane partycje i kliknij akcje. 
2. Wybierz pozycję wyzwalanie przywracania partycji i wprowadź informacje dotyczące platformy Azure:

    ![Wyzwalanie przywracania partycji][2]

    lub przeudziale:

    ![Wyzwól udziały przywracania partycji][3]

### <a name="data-restore-for-_data-corruption__data-loss_"></a>Przywracanie danych na potrzeby / _utraty danych_ dotyczących uszkodzenia danych

W przypadku _utraty danych_ lub _uszkodzenia danych_ kopie zapasowe partycji dla niezawodnej usługi stanowej i partycji Reliable Actors mogą zostać przywrócone do dowolnej z wybranych kopii zapasowych.

Poniższy przykład to kontynuacja [włączania okresowych kopii zapasowych dla niezawodnej usługi stanowej i Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). W tym przykładzie zasady tworzenia kopii zapasowej są włączone dla partycji, a usługa tworzy kopie zapasowe z odpowiednią częstotliwością w usłudze Azure Storage.

Wybierz kopię zapasową z danych wyjściowych  [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). W tym scenariuszu kopia zapasowa jest generowana z tego samego klastra co poprzednio.

Aby wyzwolić przywracanie, wybierz kopię zapasową z listy. W przypadku bieżącego  / _uszkodzenia danych_ utraty danych wybierz następujące kopie zapasowe:

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

W przypadku interfejsu API przywracania Podaj szczegóły dotyczące _BackupId_ i _BackupLocation_ . W klastrze włączono funkcję tworzenia kopii zapasowych, więc _usługa Service Fabric Backup Restore (BRS)_ identyfikuje poprawną lokalizację magazynu na podstawie skojarzonych zasad kopii zapasowych.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell przy użyciu modułu Microsoft. servicefabric. PowerShell. http

```powershell
Restore-SFPartition  -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'

```

#### <a name="rest-call-using-powershell"></a>Wywołanie REST przy użyciu programu PowerShell

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4',
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Postęp przywracania można śledzić przy użyciu TrackRestoreProgress.

> [!NOTE]
> W przypadku przywracania partycji przy użyciu programu PowerShell, jeśli BackupLocation ma znak "$", należy użyć znaku "~"
>

## <a name="track-restore-progress"></a>Śledź postęp przywracania

Partycja niezawodnej usługi stanowej lub niezawodnego aktora akceptuje tylko jedno żądanie przywracania jednocześnie. Partycja akceptuje tylko kolejne żądanie po zakończeniu bieżącego żądania przywracania. Wiele żądań przywracania może być wyzwalanych w różnych partycjach w tym samym czasie.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell przy użyciu modułu Microsoft. servicefabric. PowerShell. http

```powershell
    Get-SFPartitionRestoreProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'
```

#### <a name="rest-call-using-powershell"></a>Wywołanie REST przy użyciu programu PowerShell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

Żądanie przywracania postępuje w następującej kolejności:

1. **Zaakceptowano**: _zaakceptowany_ stan przywracania wskazuje, że żądana partycja została wyzwolona przy użyciu prawidłowych parametrów żądania.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. W **toku**: stan przywracania stanu wskazuje, że przywracanie jest _wykonywane_ w partycji przy użyciu kopii zapasowej wymienionej w żądaniu. Partycja raportuje stan _datastraty_ .
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **Powodzenie**, **Niepowodzenie** lub **przekroczenie limitu czasu**: żądane przywrócenie można wykonać w jednym z następujących stanów. Każdy stan ma następujące istotne znaczenie i szczegóły odpowiedzi:
    - **Sukces**: stan przywracania _sukcesu_ wskazuje stan odzyskanej partycji. Partycja raportuje Stany _RestoredEpoch_ i _RestoredLSN_ wraz z czasem UTC.

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **Niepowodzenie**: stan przywracania _awarii_ wskazuje niepowodzenie żądania przywracania. Przyczyna błędu jest raportowana.

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **Przekroczenie** _limitu czasu: stan przywracania wskazuje_ , że żądanie ma limit czasu. Utwórz nowe żądanie przywracania o większej [RestoreTimeout](/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout). Domyślny limit czasu wynosi 10 minut. Upewnij się, że partycja nie jest w stanie utraty danych przed ponownym zażądaniem przywracania.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>Automatyczne przywracanie

Można skonfigurować niezawodne usługi stanowe i Reliable Actors partycje w klastrze Service Fabric na potrzeby _przywracania automatycznie_. W zasadach tworzenia kopii zapasowych ustawiono `AutoRestore` _wartość true_. Włączenie _automatycznego przywracania_ automatycznie przywraca dane z najnowszej kopii zapasowej partycji po zgłoszeniu utraty danych. Aby uzyskać więcej informacji, zobacz:

- [Włączanie funkcji autoprzywracania w zasadach tworzenia kopii zapasowych](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [Dokumentacja interfejsu API RestorePartition](/rest/api/servicefabric/sfclient-api-restorepartition)
- [Dokumentacja interfejsu API GetPartitionRestoreProgress](/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Następne kroki
- [Opis konfiguracji okresowej kopii zapasowej](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Dokumentacja interfejsu API REST przywracania kopii zapasowych](/rest/api/servicefabric/sfclient-index-backuprestore)

[2]: ./media/service-fabric-backuprestoreservice/restore-partition-backup.png
[3]: ./media/service-fabric-backuprestoreservice/restore-partition-fileshare.png
