---
title: Zarządzanie urządzeniami StorSimple przy użyciu skryptów AzureRM PowerShell
description: Dowiedz się, jak za pomocą skryptów opartych na zestawie SDK Azure Resource Manager zarządzać urządzeniem z serii StorSimple 8000.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: e3339f883d177366a64187e0c090752527802c8d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968778"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Używanie Azure Resource Manager skryptów opartych na zestawie SDK do zarządzania urządzeniami StorSimple

W tym artykule opisano sposób, w jaki Azure Resource Manager można używać skryptów opartych na zestawie SDK do zarządzania urządzeniem z serii StorSimple 8000. Przykładowy skrypt zawiera również instrukcje konfigurowania środowiska do uruchamiania tych skryptów.

Ten artykuł dotyczy tylko urządzeń z serii StorSimple 8000 z systemem Azure Portal.

## <a name="sample-scripts"></a>Przykładowe skrypty

Następujące przykładowe skrypty są dostępne do automatyzowania różnych zadań StorSimple.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Tabela przykładowych skryptów Azure Resource Manager opartej na zestawie SDK

| Skrypt Azure Resource Manager                    | Opis                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Authorize-ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Ten skrypt umożliwia autoryzowanie urządzenia StorSimple do zmiany klucza szyfrowania danych usługi.                                                                                                           |
| [Create-StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Ten skrypt tworzy urządzenie w chmurze 8010 lub 8020 StorSimple. Urządzenie w chmurze można następnie skonfigurować i zarejestrować w usłudze StorSimple Data Manager.                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Ten skrypt tworzy lub modyfikuje woluminy StorSimple.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Ten skrypt zawiera listę wszystkich kopii zapasowych urządzenia zarejestrowanego w usłudze StorSimple Menedżer urządzeń.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Ten skrypt wszystkie zasady tworzenia kopii zapasowej urządzenia StorSimple.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Ten skrypt pobiera wszystkie zadania StorSimple uruchomione w usłudze StorSimple Menedżer urządzeń.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Ten skrypt skanuje serwer aktualizacji i informuje o tym, czy aktualizacje są dostępne do zainstalowania na urządzeniu StorSimple.                                                                                          |
| [Install-DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Ten skrypt instaluje dostępne aktualizacje na urządzeniu StorSimple.                                                                                                                                           |
| [Manage-CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Ten skrypt uruchamia ręczną migawkę w chmurze i usuwa migawki w chmurze starsze niż określone dni przechowywania.                                                                                                   |
| [Monitor-Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Ten skrypt Azure Automation Runbook PowerShell zgłasza stan wszystkich zadań tworzenia kopii zapasowej.                                                                                                              |
| [Remove-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Ten skrypt usuwa pojedynczy obiekt kopii zapasowej.                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Ten skrypt uruchamia ręczne tworzenie kopii zapasowej na urządzeniu StorSimple.                                                                                                                                       |
| [Update-CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Ten skrypt aktualizuje klucz szyfrowania danych usługi dla wszystkich urządzeń w chmurze 8010/8020 StorSimple zarejestrowanych w usłudze StorSimple Menedżer urządzeń.                                     |
| [Verify-BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Ten skrypt wyróżnia brakujące kopie zapasowe po przeanalizowaniu wszystkich harmonogramów skojarzonych z zasadami tworzenia kopii zapasowych. Sprawdza również wykaz kopii zapasowych z listą dostępnych kopii zapasowych.             |




## <a name="configure-and-run-a-sample-script"></a>Konfigurowanie i uruchamianie przykładowego skryptu

Ta sekcja zawiera przykładowy skrypt i szczegółowe informacje o różnych krokach wymaganych do uruchomienia skryptu.

### <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz:

*   Azure PowerShell zainstalowane. Aby zainstalować moduły Azure PowerShell:
    * W środowisku systemu Windows wykonaj kroki opisane w temacie [Instalowanie i konfigurowanie Azure PowerShell](/powershell/azure/install-az-ps). Azure PowerShell można zainstalować na hoście z systemem Windows Server, jeśli jest on używany przez StorSimple.
    * W środowisku Linux lub MacOS wykonaj kroki opisane w temacie [Instalowanie i konfigurowanie Azure PowerShell w systemie MacOS lub Linux](/powershell/azure/install-az-ps).

Aby uzyskać więcej informacji na temat korzystania z Azure PowerShell, przejdź do obszaru wprowadzenie do [korzystania z Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="run-azure-powershell-script"></a>Uruchom skrypt Azure PowerShell

Skrypt używany w tym przykładzie zawiera listę wszystkich zadań na urządzeniu StorSimple. Obejmuje to zadania zakończone powodzeniem, zakończone niepowodzeniem lub w toku. Wykonaj następujące kroki, aby pobrać i uruchomić skrypt.

1. Uruchom program Azure PowerShell. Utwórz nowy folder i zmień katalog na nowy.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Pobierz interfejs wiersza polecenia NuGet](https://www.nuget.org/downloads) w folderze utworzonym w poprzednim kroku. Istnieją różne wersje _nuget.exe_. Wybierz wersję odpowiadającą zestawowi SDK. Każde łącze pobierania wskazuje bezpośrednio plik _. exe_ . Upewnij się, że po kliknięciu prawym przyciskiem myszy i Zapisz plik na komputerze zamiast uruchamiania go z przeglądarki.

    Możesz również uruchomić następujące polecenie, aby pobrać i zapisać skrypt w tym samym folderze, który został utworzony wcześniej.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Pobierz zależny zestaw SDK.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Pobierz skrypt z przykładowego projektu GitHub.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Uruchom skrypt. Po wyświetleniu monitu o uwierzytelnienie podaj swoje poświadczenia platformy Azure. Ten skrypt powinien wyprowadzić przefiltrowaną listę wszystkich zadań na urządzeniu StorSimple.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Przykładowe dane wyjściowe

Poniższe dane wyjściowe są prezentowane po uruchomieniu przykładowego skryptu. Dane wyjściowe zawierają wszystkie zadania uruchomione na zarejestrowanym urządzeniu, które rozpoczęło się od 25 września 2017 i ukończone do 2 października 2017.

```
-----------------------------------------
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\Scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1
PS C:\Scripts\StorSimpleSDKTools> .\Get-DeviceJobs.ps1 -SubscriptionId 1234ab5c-678d-910e-9fc4-0accc9c0166e -TenantId 12a345bc-67d8-91ef-01ab-2c7cd123ef45 -DeviceName 8600-ABC1234567D89EF -ResourceGroupName Contoso -ManagerName ContosoDeviceMgr -FilterByStartTime "09/25/2017 08:10:02" -FilterByEndTime "10/02/2017 08:10:02"


Status            : Succeeded
StartTime         : 10/2/2017 10:30:02 PM
EndTime           : 10/2/2017 10:31:36 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : ss-asr-policy1
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC/jobs/75905c48-b153-4af1-8b21-4b9a2ff9
                    825b
Name              : 75905c48-b153-4af1-8b21-4b9a2ff9825b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000
-------------------------------------------
CUT              CUT  
-------------------------------------------
Status            : Succeeded
StartTime         : 9/26/2017 5:00:02 PM
EndTime           : 9/26/2017 5:01:20 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : 8010 policy
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF/jobs/3cfd8108-db60-4e9a-a8da-6d8fe457
                    8d2b
Name              : 3cfd8108-db60-4e9a-a8da-6d8fe4578d2b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000



PS C:\Scripts\StorSimpleSDKTools>
--------------------------------------------

```


## <a name="next-steps"></a>Następne kroki

[Zarządzanie urządzeniem StorSimple przy użyciu usługi StorSimple Menedżer urządzeń](storsimple-8000-manager-service-administration.md).