---
title: Okresowe wykonywanie kopii zapasowej i przywracanie w usłudze Azure Service Fabric
description: Użyj funkcji okresowej kopii zapasowej i przywracania Service Fabric, aby umożliwić okresowe wykonywanie kopii zapasowych danych aplikacji.
ms.topic: conceptual
ms.date: 5/24/2019
ms.openlocfilehash: 42097b50277e78b3f0e8f5e61a2bf70cc08dbc02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103198727"
---
# <a name="periodic-backup-and-restore-in-an-azure-service-fabric-cluster"></a>Okresowe tworzenie kopii zapasowych i przywracanie w klastrze Service Fabric platformy Azure
> [!div class="op_single_selector"]
> * [Klastry na platformie Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Klastry autonomiczne](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric to platforma systemów rozproszonych, która ułatwia tworzenie niezawodnych i rozproszonych aplikacji w chmurze opartych na mikrousługach oraz zarządzanie nimi. Umożliwia uruchamianie zarówno bezstanowych, jak i bezstanowych mikrousług. Usługi stanowe mogą zachować modyfikowalny, autorytatywny stan wykraczający poza żądanie i odpowiedź lub pełną transakcję. Jeśli usługa stanowa przejdzie przez długi czas lub utraci informacje z powodu awarii, może być konieczne przywrócenie ostatniej kopii zapasowej stanu, aby kontynuować świadczenie usługi po jej utworzeniu.

Service Fabric replikuje stan w wielu węzłach, aby upewnić się, że usługa jest wysoce dostępna. Nawet jeśli jeden węzeł w klastrze ulegnie awarii, usługa będzie nadal dostępna. W niektórych przypadkach jednak nadal pożądane jest, aby dane usługi były niezawodne w przypadku szerszej awarii.
 
Na przykład usługa może chcieć utworzyć kopię zapasową danych w celu ochrony z następujących scenariuszy:
- W przypadku trwałej utraty całego klastra Service Fabric.
- Stała utrata większości replik partycji usługi
- Błędy administracyjne, według których stan zostanie przypadkowo usunięty lub uszkodzony. Na przykład administrator z odpowiednimi uprawnieniami błędnie usuwa usługę.
- Usterki w usłudze, które powodują uszkodzenie danych. Na przykład może się to zdarzyć, gdy uaktualnienie kodu usługi rozpocznie zapisywanie uszkodzonych danych do niezawodnej kolekcji. W takim przypadku kod i dane mogą być przywracane do wcześniejszego stanu.
- Przetwarzanie danych w trybie offline. Może być wygodne przetwarzanie danych w trybie offline w celu przeprowadzenia analizy biznesowej niezależnie od usługi, która generuje dane.

Service Fabric udostępnia wbudowany interfejs API do [wykonywania kopii zapasowych i przywracania](service-fabric-reliable-services-backup-restore.md)w czasie. Deweloperzy aplikacji mogą korzystać z tych interfejsów API, aby okresowo tworzyć kopie zapasowe stanu usługi. Ponadto jeśli Administratorzy usługi chcą wyzwolić kopię zapasową spoza usługi w określonym czasie, tak jak przed uaktualnieniem aplikacji, deweloperzy muszą uwidocznić kopię zapasową (i przywrócić) jako interfejs API z usługi. Obsługa kopii zapasowych jest dodatkowym kosztem powyżej. Na przykład możesz chcieć wykonać pięć przyrostowych kopii zapasowych co pół godziny, a następnie utworzyć pełną kopię zapasową. Po pełnej kopii zapasowej możesz usunąć poprzednie przyrostowe kopie zapasowe. Takie podejście wymaga dodatkowego kodu prowadzącego do dodatkowego kosztu podczas tworzenia aplikacji.

Usługa tworzenia kopii zapasowych i przywracania w programie Service Fabric umożliwia łatwe i automatyczne tworzenie kopii zapasowych informacji przechowywanych w usługach stanowych. Okresowe tworzenie kopii zapasowych danych aplikacji ma podstawowe znaczenie dla ochrony przed utratą danych i niedostępnością usługi. Service Fabric udostępnia opcjonalną usługę tworzenia kopii zapasowych i przywracania, która umożliwia skonfigurowanie okresowej kopii zapasowej Reliable Services (w tym usług aktora) bez konieczności pisania dodatkowego kodu. Ułatwia również przywracanie wykonanych wcześniej kopii zapasowych. 


Service Fabric udostępnia zestaw interfejsów API do osiągnięcia następujących funkcji związanych z okresową funkcją wykonywania kopii zapasowych i przywracania:

- Zaplanuj okresowe wykonywanie kopii zapasowych niezawodnych usług stanowych i Reliable Actors z obsługą przekazywania kopii zapasowych do lokalizacji magazynu (zewnętrznej). Obsługiwane lokalizacje przechowywania
    - Azure Storage
    - Udział plików (lokalny)
- Wyliczanie kopii zapasowych
- Wyzwalanie tworzenia kopii zapasowej partycji ad hoc
- Przywracanie partycji przy użyciu poprzedniej kopii zapasowej
- Tymczasowe wstrzymywanie kopii zapasowych
- Zarządzanie przechowywaniem kopii zapasowych (nadchodzące)

## <a name="prerequisites"></a>Wymagania wstępne
* Service Fabric klaster z siecią szkieletową w wersji 6,4 lub nowszej. Zapoznaj się z tym [artykułem](service-fabric-cluster-creation-via-arm.md) , aby uzyskać instrukcje dotyczące tworzenia klastra Service Fabric przy użyciu szablonu zasobów platformy Azure.
* Certyfikat X. 509 na potrzeby szyfrowania wpisów tajnych wymaganych do nawiązania połączenia z magazynem w celu przechowywania kopii zapasowych. Zapoznaj się z [artykułem](service-fabric-cluster-creation-via-arm.md) , aby dowiedzieć się, jak uzyskać lub utworzyć certyfikat X. 509.
* Service Fabric niezawodnej aplikacji stanowej utworzonej przy użyciu zestawu SDK Service Fabric w wersji 3,0 lub nowszej. W przypadku aplikacji przeznaczonych dla platformy .NET Core 2,0 aplikacja powinna być skompilowana przy użyciu zestawu SDK Service Fabric w wersji 3,1 lub nowszej.
* Utwórz konto usługi Azure Storage do przechowywania kopii zapasowych aplikacji.
* Zainstaluj pakiet Microsoft. servicefabric. PowerShell. http module (wersja zapoznawcza) na potrzeby wykonywania wywołań konfiguracyjnych.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

> [!NOTE]
> Jeśli wersja PowerShellGet jest mniejsza niż 1.6.0, należy ją zaktualizować, aby dodać obsługę flagi *-AllowPrerelease* :
>
> `Install-Module -Name PowerShellGet -Force`

* Upewnij się, że klaster jest połączony przy użyciu `Connect-SFCluster` polecenia przed wykonaniem dowolnego żądania konfiguracji przy użyciu modułu Microsoft. servicefabric. PowerShell. http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Włączanie usługi Backup i Restore

### <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal

Włącz `Include backup restore service` pole wyboru w obszarze `+ Show optional settings` na `Cluster Configuration` karcie.

![Włączanie usługi przywracania kopii zapasowych przy użyciu portalu][1]


### <a name="using-azure-resource-manager-template"></a>Korzystanie z szablonu Azure Resource Manager
Najpierw należy włączyć _usługę tworzenia kopii zapasowych i przywracania_ w klastrze. Pobierz szablon klastra, który chcesz wdrożyć. Możesz użyć [przykładowych szablonów](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) lub utworzyć szablon Menedżer zasobów. Włącz _usługę tworzenia kopii zapasowych i przywracania_ , wykonując następujące czynności:

1. Sprawdź, czy `apiversion` ustawiono **`2018-02-01`** dla `Microsoft.ServiceFabric/clusters` zasobu, a jeśli nie, zaktualizuj go, jak pokazano w poniższym fragmencie kodu:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Teraz Włącz _usługę tworzenia kopii zapasowych i przywracania_ , dodając następującą `addonFeatures` sekcję w `properties` sekcji, jak pokazano w poniższym fragmencie kodu: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Skonfiguruj certyfikat X. 509 na potrzeby szyfrowania poświadczeń. Jest to ważne, aby upewnić się, że poświadczenia podane w celu nawiązania połączenia z magazynem są szyfrowane przed utrwalaniem. Skonfiguruj certyfikat szyfrowania, dodając następującą `BackupRestoreService` sekcję w `fabricSettings` sekcji, jak pokazano w poniższym fragmencie kodu: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            }]
        }
        ...
    }
    ```

4. Po zaktualizowaniu szablonu klastra z poprzednimi zmianami zastosuj je i pozwól na ukończenie wdrożenia/uaktualnienia. Po zakończeniu _usługa tworzenia kopii zapasowych i przywracania_ jest uruchamiana w klastrze. Identyfikator URI tej usługi to usługa `fabric:/System/BackupRestoreService` , która może znajdować się w sekcji usługi systemowej w eksploratorze Service Fabric. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Włączanie okresowej kopii zapasowej dla niezawodnej usługi stanowej i Reliable Actors
Wykonajmy kroki, aby włączyć okresowe tworzenie kopii zapasowej dla niezawodnej usługi stanowej i Reliable Actors. W tych krokach przyjęto założenie
- Klaster jest skonfigurowany przy użyciu zabezpieczeń X. 509 z _usługą tworzenia kopii zapasowych i przywracania_.
- W klastrze wdrożono niezawodne usługi stanowe. Na potrzeby tego przewodnika Szybki Start identyfikator URI aplikacji jest `fabric:/SampleApp` i identyfikator URI niezawodnej usługi stanowej należącej do tej aplikacji `fabric:/SampleApp/MyStatefulService` . Ta usługa jest wdrażana z jedną partycją i IDENTYFIKATORem partycji `974bd92a-b395-4631-8a7f-53bd4ae9cf22` .
- Certyfikat klienta z rolą administratora programu jest instalowany w _mojej_ (_osobistej_) lokalizacji magazynu certyfikatów _CurrentUser_ na komputerze, na którym będą wywoływane poniższe skrypty. Ten przykład używa `1b7ebe2174649c45474a4819dafae956712c31d3` odcisku palca tego certyfikatu. Aby uzyskać więcej informacji na temat certyfikatów klientów, zobacz [kontroli dostępu opartej na rolach dla klientów Service Fabric](service-fabric-cluster-security-roles.md).

### <a name="create-backup-policy"></a>Tworzenie zasad kopii zapasowych

Pierwszym krokiem jest utworzenie zasad tworzenia kopii zapasowych z opisem harmonogramu kopii zapasowych, docelowego magazynu dla danych kopii zapasowej, nazwy zasad i maksymalnych przyrostowych kopii zapasowych, które będą dozwolone przed wyzwalaniem pełnych zasad tworzenia kopii zapasowych i przechowywania kopii zapasowych 

W przypadku magazynu kopii zapasowych Użyj utworzonego powyżej konta usługi Azure Storage. Kontener `backup-container` jest skonfigurowany do przechowywania kopii zapasowych. Kontener o tej nazwie zostanie utworzony, jeśli jeszcze nie istnieje, podczas przekazywania kopii zapasowej. Wypełnij `ConnectionString` prawidłowymi parametrami połączenia dla konta usługi Azure Storage, zastępując je `account-name` nazwą konta magazynu i `account-key` kluczem konta magazynu.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell przy użyciu modułu Microsoft. servicefabric. PowerShell. http

Wykonaj następujące polecenia cmdlet programu PowerShell, aby utworzyć nowe zasady tworzenia kopii zapasowych. Zastąp ciąg `account-name` nazwą konta magazynu i `account-key` kluczem konta magazynu.

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container' -Basic -RetentionDuration '10.00:00:00'

```

#### <a name="rest-call-using-powershell"></a>Wywołanie REST przy użyciu programu PowerShell

Wykonaj następujący skrypt programu PowerShell w celu wywołania wymaganego interfejsu API REST w celu utworzenia nowych zasad. Zastąp ciąg `account-name` nazwą konta magazynu i `account-key` kluczem konta magazynu.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}

$RetentionPolicy = @{ 
    RetentionPolicyType = 'Basic'
    RetentionDuration =  'P10D'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
    RetentionPolicy = $RetentionPolicy
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

```

#### <a name="using-service-fabric-explorer"></a>Używanie Service Fabric Explorer

1. W Service Fabric Explorer przejdź do karty kopie zapasowe, a następnie wybierz pozycję akcje > Utwórz zasady tworzenia kopii zapasowych.

    ![Tworzenie zasad kopii zapasowych][6]

2. Uzupełnij informacje. W przypadku klastrów platformy Azure należy wybrać AzureBlobStore.

    ![Tworzenie zasad tworzenia kopii zapasowych Blob Storage Azure][7]

### <a name="enable-periodic-backup"></a>Włącz okresowe wykonywanie kopii zapasowej
Po zdefiniowaniu zasad tworzenia kopii zapasowych w celu spełnienia wymagań dotyczących ochrony danych aplikacji, zasady tworzenia kopii zapasowej powinny być skojarzone z aplikacją. W zależności od wymagań zasady tworzenia kopii zapasowych można kojarzyć z aplikacją, usługą lub partycją.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell przy użyciu modułu Microsoft. servicefabric. PowerShell. http

```powershell

Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'

```
#### <a name="rest-call-using-powershell"></a>Wywołanie REST przy użyciu programu PowerShell

Wykonaj następujący skrypt programu PowerShell, aby wywołać wymagany interfejs API REST w celu skojarzenia zasad tworzenia kopii zapasowych o nazwie `BackupPolicy1` utworzonej w powyższym kroku z aplikacją `SampleApp` .

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

#### <a name="using-service-fabric-explorer"></a>Używanie Service Fabric Explorer
Upewnij się, że [Tryb zaawansowany](service-fabric-visualizing-your-cluster.md#backup-and-restore) dla Service Fabric Explorer jest włączony 

1. Wybierz aplikację i przejdź do akcji. Kliknij pozycję Włącz/zaktualizuj kopię zapasową aplikacji.

    ![Włącz tworzenie kopii zapasowej aplikacji][3]

2. Na koniec wybierz odpowiednie zasady i kliknij pozycję Włącz kopię zapasową.

    ![Wybieranie zasad][4]


### <a name="verify-that-periodic-backups-are-working"></a>Sprawdź, czy okresowe kopie zapasowe działają

Po włączeniu tworzenia kopii zapasowej na poziomie aplikacji wszystkie partycje należące do niezawodnych usług stanowych i Reliable Actors w ramach aplikacji zaczną okresowo otrzymywać kopie zapasowe zgodnie ze skojarzonymi zasadami tworzenia kopii zapasowych. 

![Zdarzenie kondycji którego partycji][0]

### <a name="list-backups"></a>Utwórz listę kopii zapasowych

Kopie zapasowe skojarzone ze wszystkimi partycjami należącymi do wiarygodnych usług stanowych i Reliable Actors aplikacji można wyliczyć przy użyciu interfejsu API _Getbackups_ . Kopie zapasowe można wyliczyć dla aplikacji, usługi lub partycji.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell przy użyciu modułu Microsoft. servicefabric. PowerShell. http

```powershell
    
Get-SFApplicationBackupList -ApplicationId WordCount
```

#### <a name="rest-call-using-powershell"></a>Wywołanie REST przy użyciu programu PowerShell

Wykonaj następujący skrypt programu PowerShell, aby wywołać interfejs API protokołu HTTP, aby wyliczyć kopie zapasowe utworzone dla wszystkich partycji w `SampleApp` aplikacji.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

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

#### <a name="using-service-fabric-explorer"></a>Używanie Service Fabric Explorer

Aby wyświetlić kopie zapasowe w Service Fabric Explorer, przejdź do partycji i wybierz kartę kopie zapasowe.

![Wyliczanie kopii zapasowych][5]

## <a name="limitation-caveats"></a>Ograniczenia/zastrzeżenia
- Polecenia cmdlet programu PowerShell Service Fabric są w trybie podglądu.
- Brak obsługi klastrów Service Fabric w systemie Linux.

## <a name="next-steps"></a>Następne kroki
- [Opis konfiguracji okresowej kopii zapasowej](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Dokumentacja interfejsu API REST przywracania kopii zapasowych](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event-azure.png
[1]: ./media/service-fabric-backuprestoreservice/enable-backup-restore-service-with-portal.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/creation-bp.png
