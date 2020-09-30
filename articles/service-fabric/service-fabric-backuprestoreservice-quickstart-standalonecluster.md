---
title: Okresowe tworzenie kopii zapasowej/przywracanie w autonomicznej Service Fabric platformy Azure
description: Funkcja okresowa i przywracanie kopii zapasowej autonomicznej Service Fabric umożliwia okresowe wykonywanie kopii zapasowych danych aplikacji.
ms.topic: conceptual
ms.date: 5/24/2019
ms.openlocfilehash: d20882ba5f7f31ef453c5d28f8bc37155cc99abd
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91538589"
---
# <a name="periodic-backup-and-restore-in-a-standalone-service-fabric"></a>Okresowe tworzenie kopii zapasowych i przywracanie w autonomicznej Service Fabric
> [!div class="op_single_selector"]
> * [Klastry na platformie Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Klastry autonomiczne](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric to platforma systemów rozproszonych, która ułatwia tworzenie niezawodnych i rozproszonych aplikacji w chmurze opartych na mikrousługach oraz zarządzanie nimi. Umożliwia uruchamianie zarówno bezstanowych, jak i bezstanowych mikrousług. Usługi stanowe mogą zachować modyfikowalny, autorytatywny stan wykraczający poza żądanie i odpowiedź lub pełną transakcję. Jeśli usługa stanowa przejdzie przez długi czas lub utraci informacje z powodu awarii, może być konieczne przywrócenie ostatniej kopii zapasowej stanu, aby można było kontynuować świadczenie usługi po jej utworzeniu.

Service Fabric replikuje stan w wielu węzłach, aby upewnić się, że usługa jest wysoce dostępna. Nawet jeśli jeden węzeł w klastrze ulegnie awarii, usługa będzie nadal dostępna. W niektórych przypadkach jednak nadal pożądane jest, aby dane usługi były niezawodne w przypadku szerszej awarii.
 
Na przykład może być konieczne utworzenie kopii zapasowej danych przez usługę w celu ochrony z następujących scenariuszy:
- Trwała utrata całego klastra Service Fabric.
- Stała utrata większości replik partycji usługi
- Błędy administracyjne, według których stan zostanie przypadkowo usunięty lub uszkodzony. Na przykład administrator z odpowiednimi uprawnieniami błędnie usuwa usługę.
- Usterki w usłudze, które powodują uszkodzenie danych. Na przykład może się to zdarzyć, gdy uaktualnienie kodu usługi rozpocznie zapisywanie uszkodzonych danych do niezawodnej kolekcji. W takim przypadku kod i dane mogą być przywracane do wcześniejszego stanu.
- Przetwarzanie danych w trybie offline. Może być wygodne przetwarzanie danych w trybie offline w celu przeprowadzenia analizy biznesowej niezależnie od usługi, która generuje dane.

Service Fabric udostępnia wbudowany interfejs API służący do [wykonywania kopii zapasowych i przywracania](service-fabric-reliable-services-backup-restore.md)w czasie. Deweloperzy aplikacji mogą korzystać z tych interfejsów API, aby okresowo tworzyć kopie zapasowe stanu usługi. Ponadto jeśli Administratorzy usługi chcą wyzwolić kopię zapasową spoza usługi w określonym czasie (na przykład przed uaktualnieniem aplikacji), deweloperzy muszą udostępniać kopie zapasowe (i przywracać je) jako interfejs API z usługi. Obsługa kopii zapasowych jest dodatkowym kosztem powyżej. Na przykład możesz chcieć wykonać pięć przyrostowych kopii zapasowych co pół godziny, a następnie utworzyć pełną kopię zapasową. Po pełnej kopii zapasowej możesz usunąć poprzednie przyrostowe kopie zapasowe. Takie podejście wymaga dodatkowego kodu prowadzącego do dodatkowego kosztu podczas tworzenia aplikacji.

Tworzenie kopii zapasowych danych aplikacji w regularnych odstępach czasu jest podstawową potrzebą do zarządzania aplikacją rozproszoną i ochrony przed utratą danych lub długotrwałej utraty dostępności usługi. Service Fabric udostępnia opcjonalną usługę tworzenia kopii zapasowych i przywracania, która umożliwia skonfigurowanie okresowej kopii zapasowej Reliable Services (w tym usług aktora) bez konieczności pisania dodatkowego kodu. Ułatwia również przywracanie wykonanych wcześniej kopii zapasowych. 

Service Fabric udostępnia zestaw interfejsów API do osiągnięcia następujących funkcji związanych z okresową funkcją wykonywania kopii zapasowych i przywracania:

- Zaplanuj okresowe wykonywanie kopii zapasowych niezawodnych usług stanowych i Reliable Actors z obsługą przekazywania kopii zapasowych do lokalizacji magazynu (zewnętrznej). Obsługiwane lokalizacje przechowywania
    - Azure Storage
    - Udział plików (lokalny)
- Wyliczanie kopii zapasowych
- Wyzwól nieplanowaną kopię zapasową partycji
- Przywracanie partycji przy użyciu poprzedniej kopii zapasowej
- Tymczasowe wstrzymywanie kopii zapasowych
- Zarządzanie przechowywaniem kopii zapasowych (nadchodzące)

## <a name="prerequisites"></a>Wymagania wstępne
* Service Fabric klaster z siecią szkieletową w wersji 6,4 lub nowszej. Zapoznaj się z tym [artykułem](service-fabric-cluster-creation-for-windows-server.md) , aby uzyskać instrukcje dotyczące pobierania wymaganego pakietu.
* Certyfikat X. 509 na potrzeby szyfrowania wpisów tajnych wymaganych do nawiązania połączenia z magazynem w celu przechowywania kopii zapasowych. Zapoznaj się z [artykułem](service-fabric-windows-cluster-x509-security.md) , aby dowiedzieć się, jak uzyskać lub utworzyć certyfikat X. 509 z podpisem własnym.

* Service Fabric niezawodnej aplikacji stanowej utworzonej przy użyciu zestawu SDK Service Fabric w wersji 3,0 lub nowszej. W przypadku aplikacji przeznaczonych dla platformy .NET Core 2,0 aplikacja powinna być skompilowana przy użyciu zestawu SDK Service Fabric w wersji 3,1 lub nowszej.
* Zainstaluj pakiet Microsoft. servicefabric. PowerShell. http (w wersji zapoznawczej) na potrzeby wykonywania wywołań konfiguracyjnych.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.PowerShell.Http -AllowPrerelease
```

* Upewnij się, że klaster jest połączony przy użyciu `Connect-SFCluster` polecenia przed wykonaniem dowolnego żądania konfiguracji przy użyciu modułu Microsoft. servicefabric. PowerShell. http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Włączanie usługi Backup i Restore
Najpierw należy włączyć _usługę tworzenia kopii zapasowych i przywracania_ w klastrze. Pobierz szablon klastra, który chcesz wdrożyć. Możesz użyć [przykładowych szablonów](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples). Włącz _usługę tworzenia kopii zapasowych i przywracania_ , wykonując następujące czynności:

1. Sprawdź, czy `apiversion` `10-2017` w pliku konfiguracji klastra jest ustawiona wartość, a jeśli nie, zaktualizuj ją, jak pokazano w poniższym fragmencie kodu:

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. Teraz Włącz _usługę tworzenia kopii zapasowych i przywracania_ , dodając następującą `addonFeatures` sekcję w `properties` sekcji, jak pokazano w poniższym fragmencie kodu: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. Skonfiguruj certyfikat X. 509 na potrzeby szyfrowania poświadczeń. Jest to ważne, aby upewnić się, że podane poświadczenia (jeśli istnieją) do połączenia z magazynem są szyfrowane przed utrwalaniem. Skonfiguruj certyfikat szyfrowania, dodając następującą `BackupRestoreService` sekcję w `fabricSettings` sekcji, jak pokazano w poniższym fragmencie kodu: 

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

4. Po zaktualizowaniu pliku konfiguracji klastra przy użyciu powyższych zmian należy je zastosować i umożliwić ukończenie wdrożenia/uaktualnienia. Po zakończeniu _usługa tworzenia kopii zapasowych i przywracania_ jest uruchamiana w klastrze. Identyfikator URI tej usługi to usługa `fabric:/System/BackupRestoreService` , która może znajdować się w sekcji usługi systemowej w eksploratorze Service Fabric. 



## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Włączanie okresowej kopii zapasowej dla niezawodnej usługi stanowej i Reliable Actors
Wykonajmy kroki, aby włączyć okresowe tworzenie kopii zapasowej dla niezawodnej usługi stanowej i Reliable Actors. W tych krokach przyjęto założenie
- Klaster jest skonfigurowany z service_ kopii zapasowych i przywracania.
- W klastrze wdrożono niezawodne usługi stanowe. Na potrzeby tego przewodnika Szybki Start identyfikator URI aplikacji jest `fabric:/SampleApp` i identyfikator URI niezawodnej usługi stanowej należącej do tej aplikacji `fabric:/SampleApp/MyStatefulService` . Ta usługa jest wdrażana z jedną partycją i IDENTYFIKATORem partycji `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7` .  

### <a name="create-backup-policy"></a>Tworzenie zasad kopii zapasowych

Pierwszym krokiem jest utworzenie zasad tworzenia kopii zapasowych z opisem harmonogramu kopii zapasowych, docelowego magazynu dla danych kopii zapasowej, nazwy zasad i maksymalnych przyrostowych kopii zapasowych, które będą dozwolone przed wyzwalaniem pełnych zasad tworzenia kopii zapasowych i przechowywania kopii zapasowych 

W przypadku magazynu kopii zapasowych Utwórz udział plików i nadaj ReadWrite dostęp do tego udziału plików dla wszystkich maszyn węzłów Service Fabric. W tym przykładzie przyjęto założenie, że udział o nazwie `BackupStore` jest obecny `StorageServer` .


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell przy użyciu modułu Microsoft. servicefabric. PowerShell. http

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -FileShare -Path '\\StorageServer\BackupStore' -Basic -RetentionDuration '10.00:00:00'

```
#### <a name="rest-call-using-powershell"></a>Wywołanie REST przy użyciu programu PowerShell

Wykonaj następujący skrypt programu PowerShell w celu wywołania wymaganego interfejsu API REST w celu utworzenia nowych zasad.

```powershell
$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}   

$StorageInfo = @{
    Path = '\\StorageServer\BackupStore'
    StorageKind = 'FileShare'
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
$url = "http://localhost:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
```

#### <a name="using-service-fabric-explorer"></a>Używanie Service Fabric Explorer

1. W Service Fabric Explorer przejdź do karty kopie zapasowe, a następnie wybierz pozycję akcje > Utwórz zasady tworzenia kopii zapasowych.

    ![Tworzenie zasad kopii zapasowych][6]

2. Uzupełnij informacje. W przypadku klastrów autonomicznych należy wybrać opcję udziału plików.

    ![Tworzenie udziału plików zasad kopii zapasowych][7]

### <a name="enable-periodic-backup"></a>Włącz okresowe wykonywanie kopii zapasowej
Po zdefiniowaniu zasad w celu spełnienia wymagań dotyczących ochrony danych aplikacji należy skojarzyć zasady tworzenia kopii zapasowych z aplikacją. W zależności od wymagań zasady tworzenia kopii zapasowych można kojarzyć z aplikacją, usługą lub partycją.


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
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

#### <a name="using-service-fabric-explorer"></a>Używanie Service Fabric Explorer

1. Wybierz aplikację i przejdź do akcji. Kliknij pozycję Włącz/zaktualizuj kopię zapasową aplikacji.

    ![Włącz tworzenie kopii zapasowej aplikacji][3] 

2. Na koniec wybierz odpowiednie zasady i wybierz pozycję *Włącz kopię zapasową*.

    ![Wybieranie zasad][4]

### <a name="verify-that-periodic-backups-are-working"></a>Sprawdź, czy okresowe kopie zapasowe działają

Po włączeniu tworzenia kopii zapasowej dla aplikacji wszystkie partycje należące do niezawodnych usług stanowych i Reliable Actors w ramach aplikacji zaczną okresowo otrzymywać kopie zapasowe zgodnie ze skojarzonymi zasadami tworzenia kopii zapasowych.

![Zdarzenie kondycji którego partycji][0]

### <a name="list-backups"></a>Utwórz listę kopii zapasowych

Kopie zapasowe skojarzone ze wszystkimi partycjami należącymi do wiarygodnych usług stanowych i Reliable Actors aplikacji można wyliczyć przy użyciu interfejsu API _Getbackups_ . W zależności od wymagań kopie zapasowe można wyliczyć dla aplikacji, usługi lub partycji.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell przy użyciu modułu Microsoft. servicefabric. PowerShell. http

```powershell
    Get-SFApplicationBackupList -ApplicationId WordCount     
```

#### <a name="rest-call-using-powershell"></a>Wywołanie REST przy użyciu programu PowerShell

Wykonaj następujący skrypt programu PowerShell, aby wywołać interfejs API protokołu HTTP, aby wyliczyć kopie zapasowe utworzone dla wszystkich partycji w `SampleApp` aplikacji.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Przykładowe dane wyjściowe dla powyższego przebiegu:

```
BackupId                : d7e4038e-2c46-47c6-9549-10698766e714
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.39.40.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2058
CreationTimeUtc         : 2018-04-01T19:39:40Z
FailureError            : 

BackupId                : 8c21398a-2141-4133-b4d7-e1a35f0d7aac
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.54.38.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2237
CreationTimeUtc         : 2018-04-01T19:54:38Z
FailureError            : 

BackupId                : fc75bd4c-798c-4c9a-beee-e725321f73b2
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 20.09.44.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2437
CreationTimeUtc         : 2018-04-01T20:09:44Z
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

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/create-bp-fileshare.png
