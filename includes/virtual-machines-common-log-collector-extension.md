---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 09c4420647043fccc408631fec75854667923721
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74085265"
---
Diagnozowanie problemów z usługą Microsoft Azure w chmurze wymaga zebrania plików dziennika usługi na maszynach wirtualnych, gdy wystąpią problemy. Możesz użyć rozszerzenia AzureLogCollector na żądanie, aby wykonać jednorazowe zbieranie dzienników z co najmniej jednej maszyny wirtualnej usługi w chmurze (z ról sieci Web i procesów roboczych) i przenieść zebrane pliki na konto usługi Azure Storage — bez zdalnego logowania do żadnej z maszyn wirtualnych.

> [!NOTE]
> Opisy dotyczące większości zarejestrowanych informacji można znaleźć pod adresem https://blogs.msdn.microsoft.com/kwill/2013/08/09/windows-azure-paas-compute-diagnostics-data/
> 
> 

Istnieją dwa tryby kolekcji zależne od typów plików, które mają być zbierane.

* **Tylko dzienniki agenta gościa platformy Azure (ga)**. Ten tryb kolekcji obejmuje wszystkie dzienniki powiązane z agentami gościa platformy Azure i innymi składnikami platformy Azure.
* **Wszystkie dzienniki (pełne)**. Ten tryb kolekcji zbiera wszystkie pliki w trybie GA oraz:
  
  * Dzienniki zdarzeń systemu i aplikacji
  * Dzienniki błędów HTTP
  * Dzienniki usług IIS
  * Dzienniki instalacji
  * inne dzienniki systemu

W obu trybach kolekcji można określić dodatkowe foldery zbierania danych przy użyciu kolekcji następującej struktury:

* **Name**: Nazwa kolekcji używana jako nazwa podfolderu w pliku zip z zebranymi plikami.
* **Lokalizacja**: ścieżka do folderu na maszynie wirtualnej, w której znajdują się pliki do zebrania.
* **SearchPattern**: wzorzec nazw plików do zebrania. Wartość domyślna to " \* "
* **Rekursywnie**: Jeśli pliki do zebrania znajdują się rekursywnie w określonej lokalizacji.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](./updated-for-az.md)]

* Posiadanie konta magazynu dla rozszerzenia w celu zapisania wygenerowanych plików zip.
* Azure PowerShell. Zobacz [install Azure PowerShell](/powershell/azure/install-az-ps)], aby uzyskać instrukcje dotyczące instalacji.

## <a name="add-the-extension"></a>Dodawanie rozszerzenia
Aby dodać rozszerzenie AzureLogCollector, można użyć poleceń cmdlet [Microsoft Azure PowerShell](https://msdn.microsoft.com/library/dn495240.aspx) lub [interfejsów API REST usługi Service Management](https://msdn.microsoft.com/library/ee460799.aspx) .

Aby uzyskać Cloud Services, można użyć istniejącego polecenia cmdlet programu Azure PowerShell, **Set-AzureServiceExtension**, aby włączyć rozszerzenie w wystąpieniach roli usługi w chmurze. Za każdym razem, gdy to rozszerzenie jest włączone za pomocą tego polecenia cmdlet, zbieranie dzienników jest wyzwalane w wybranych wystąpieniach roli wybranych ról.

Aby uzyskać Virtual Machines, można użyć istniejącego polecenia cmdlet programu Azure PowerShell, **Set-AzureVMExtension**, aby włączyć rozszerzenie na Virtual Machines. Za każdym razem, gdy to rozszerzenie jest włączone za pomocą poleceń cmdlet, zbieranie dzienników jest wyzwalane dla każdego wystąpienia.

Wewnętrznie to rozszerzenie korzysta z PublicConfiguration i PrivateConfiguration opartych na notacji JSON. Poniżej przedstawiono układ przykładowego pliku JSON dla konfiguracji publicznej i prywatnej.

### <a name="publicconfiguration"></a>PublicConfiguration

```json
{
    "Instances":  "*",
    "Mode":  "Full",
    "SasUri":  "SasUri to your storage account with sp=wl",
    "AdditionalData":
    [
      {
              "Name":  "StorageData",
              "Location":  "%roleroot%storage",
              "SearchPattern":  "*.*",
              "Recursive":  "true"
      },
      {
            "Name":  "CustomDataFolder2",
            "Location":  "c:\customFolder",
            "SearchPattern":  "*.log",
            "Recursive":  "false"
      },
    ]
}
```

### <a name="privateconfiguration"></a>PrivateConfiguration

```json
{

}
```

> [!NOTE]
> To rozszerzenie nie wymaga **privateConfiguration**. Można po prostu podać pustą strukturę dla argumentu **– PrivateConfiguration** .
> 
> 

Aby dodać AzureLogCollector do jednego lub większej liczby wystąpień usługi w chmurze lub maszyny wirtualnej wybranych ról, można wykonać jedną z dwóch poniższych kroków, która wyzwala kolekcje na każdej maszynie wirtualnej w celu uruchomienia i wysłania zebranych plików na konto platformy Azure.

## <a name="adding-as-a-service-extension"></a>Dodawanie jako rozszerzenie usługi
1. Postępuj zgodnie z instrukcjami, aby połączyć Azure PowerShell z subskrypcją.
2. Określ nazwę usługi, gniazdo, role i wystąpienia roli, do których chcesz dodać i włączyć rozszerzenie AzureLogCollector.

   ```powershell
   #Specify your cloud service name
   $ServiceName = 'extensiontest2'

   #Specify the slot. 'Production' or 'Staging'
   $slot = 'Production'

   #Specified the roles on which the extension will be installed and enabled
   $roles = @("WorkerRole1","WebRole1")

   #Specify the instances on which extension will be installed and enabled.  Use wildcard * for all instances
   $instances = @("*")

   #Specify the collection mode, "Full" or "GA"
   $mode = "GA"
   ```

3. Określ folder danych dodatkowych, dla którego będą zbierane pliki (ten krok jest opcjonalny).

   ```powershell
   #add one location
   $a1 = New-Object PSObject

   $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
   $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
   $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
   $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"

   $AdditionalDataList+= $a1
   #more locations can be added....
   ```

   > [!NOTE]
   > Możesz użyć tokenu, `%roleroot%` Aby określić dysk główny roli, ponieważ nie używa dysku stałego.
   > 
   > 
4. Podaj nazwę i klucz konta usługi Azure Storage, do którego zostaną przekazane zebrane pliki.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

5. Wywołaj SetAzureServiceLogCollector.ps1 (uwzględniony na końcu artykułu) w następujący sposób, aby włączyć rozszerzenie AzureLogCollector dla usługi w chmurze. Po zakończeniu wykonywania można znaleźć przekazany plik w obszarze `https://YourStorageAccountName.blob.core.windows.net/vmlogs`

   ```powershell
   .\SetAzureServiceLogCollector.ps1 -ServiceName YourCloudServiceName  -Roles $roles  -Instances $instances –Mode $mode -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -AdditionDataLocationList $AdditionalDataList
   ```

Poniżej przedstawiono definicje parametrów przesłanych do skryptu. (Jest to również kopiowane poniżej).

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string[]] $Roles ,

  [Parameter(Mandatory=$false)]
  [string[]] $Instances,

  [Parameter(Mandatory=$false)]
  [string]   $Slot = 'Production',

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)
```

* **ServiceName**: nazwa usługi w chmurze.
* **Role**: lista ról, takich jak "WebRole1" lub "WorkerRole1".
* **Wystąpienia**: Lista nazw wystąpień roli oddzielonych przecinkami — Użyj symbolu wieloznacznego ("*") dla wszystkich wystąpień roli.
* **Gniazdo**: Nazwa gniazda. "Produkcja" lub "przemieszczanie".
* **Tryb**: tryb zbierania danych. "Pełne" lub "GA".
* **StorageAccountName**: nazwa konta usługi Azure Storage do przechowywania zebranych danych.
* **StorageAccountKey**: Nazwa klucza konta usługi Azure Storage.
* **AdditionalDataLocationList**: Lista następujących struktur:

  ```powershell
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="adding-as-a-vm-extension"></a>Dodawanie jako rozszerzenie maszyny wirtualnej
Postępuj zgodnie z instrukcjami, aby połączyć Azure PowerShell z subskrypcją.

1. Określ nazwę usługi, maszynę wirtualną i tryb kolekcji.

   ```powershell
   #Specify your cloud service name
   $ServiceName = 'YourCloudServiceName'

   #Specify the VM name
   $VMName = "'YourVMName'"

   #Specify the collection mode, "Full" or "GA"
   $mode = "GA"

   Specify the additional data folder for which files will be collected (this step is optional).

   #add one location
   $a1 = New-Object PSObject

   $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
   $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
   $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
   $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"

   $AdditionalDataList+= $a1
        #more locations can be added....
   ```
  
2. Podaj nazwę i klucz konta usługi Azure Storage, do którego zostaną przekazane zebrane pliki.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

3. Wywołaj SetAzureVMLogCollector.ps1 (uwzględniony na końcu artykułu) w następujący sposób, aby włączyć rozszerzenie AzureLogCollector dla usługi w chmurze. Po zakończeniu wykonywania można znaleźć przekazany plik w obszarze `https://YourStorageAccountName.blob.core.windows.net/vmlogs`

Poniżej przedstawiono definicje parametrów przesłanych do skryptu. (Jest to również kopiowane poniżej).

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string] $VMName ,

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)
```

* **ServiceName**: nazwa usługi w chmurze.
* **VMName**: Nazwa maszyny wirtualnej.
* **Tryb**: tryb zbierania danych. "Pełne" lub "GA".
* **StorageAccountName**: nazwa konta usługi Azure Storage do przechowywania zebranych danych.
* **StorageAccountKey**: Nazwa klucza konta usługi Azure Storage.
* **AdditionalDataLocationList**: Lista następujących struktur:

  ```
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="extention-powershell-script-files"></a>Pliki skryptów programu PowerShell w zakresie
### <a name="setazureservicelogcollectorps1"></a>SetAzureServiceLogCollector.ps1

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string[]] $Roles ,

  [Parameter(Mandatory=$false)]
  [string[]] $Instances = '*',

  [Parameter(Mandatory=$false)]
  [string]   $Slot = 'Production',

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)

$publicConfig = New-Object PSObject

if ($Instances -ne $null -and $Instances.Count -gt 0)  #Instances should be separated by ,
{
  $instanceText = $Instances[0]
  for ($i = 1;$i -lt $Instances.Count;$i++)
  {
    $instanceText = $instanceText+ "," + $Instances[$i]
  }
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value $instanceText
}
else  #For all instances if not specified.  The value should be a space or *
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value " "
}

if ($Mode -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
}
else
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
}

#
#we need to get the Sasuri from StorageAccount and containers
#
$context = New-AzStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
  "Container ($ContainerName) doesn't exist. Creating it now.."
  New-AzStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
$publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

#
#Add AdditionalData to collect data from additional folders
#
if ($AdditionDataLocationList -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
}

#
# Convert it to JSON format
#
$publicConfigJSON = $publicConfig | ConvertTo-Json
"publicConfig is:  $publicConfigJSON"

#we just provide an empty privateConfig object
$privateconfig = "{
}"

if ($Roles -ne $null)
{
  Set-AzureServiceExtension -Service $ServiceName -Slot $Slot -Role $Roles -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
}
else
{
  Set-AzureServiceExtension -Service $ServiceName -Slot $Slot  -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
}

#
#This is an optional step: generate a sasUri to the container so it can be shared with other people if needed.
#
$SasExpireTime = [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rl -Context $context
$SasUri = $SasUri + "&restype=container&comp=list"
Write-Output "The container for uploaded file can be accessed using this link:`r`n$sasuri"
```

### <a name="setazurevmlogcollectorps1"></a>SetAzureVMLogCollector.ps1

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
              [Parameter(Mandatory=$true)]
              [string]   $ServiceName,

              [Parameter(Mandatory=$false)]
              [string] $VMName ,

              [Parameter(Mandatory=$false)]
              [string]   $Mode = 'Full',

              [Parameter(Mandatory=$false)]
              [string]   $StorageAccountName,

              [Parameter(Mandatory=$false)]
              [string]   $StorageAccountKey,

              [Parameter(Mandatory=$false)]
              [PSObject[]] $AdditionDataLocationList = $null
        )

$publicConfig = New-Object PSObject
$publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value "*"

if ($Mode -ne $null )
{
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
}
else
{
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
}

#
#we need to get the Sasuri from StorageAccount and containers
#
$context = New-AzStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
    "Container ($ContainerName) doesn't exist. Creating it now.."
    New-AzStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(90).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
$publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

#
#Add AdditionalData to collect data from additional folders
#
if ($AdditionDataLocationList -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
}

#
# Convert it to JSON format
#
$publicConfigJSON = $publicConfig | ConvertTo-Json

Write-Output "PublicConfiguration is: \r\n$publicConfigJSON"

#
#we just provide an empty privateConfig object
#
$privateconfig = "{
}"

if ($VMName -ne $null )
{
      $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
      $VM.VM.OSVirtualHardDisk.OS

      if ($VM.VM.OSVirtualHardDisk.OS -like '*Windows*')
      {
            Set-AzureVMExtension -VM $VM -ExtensionName "AzureLogCollector" -Publisher Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.* | Update-AzureVM -Verbose

            #
            #We will check the VM status to find if operation by extension has been completed or not. The completion of the operation,either succeed or fail, can be indicated by
            #the presence of SubstatusList field.
            #
            $Completed = $false
            while ($Completed -ne $true)
            {
                    $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
                    $status = $VM.ResourceExtensionStatusList | Where-Object {$_.HandlerName -eq "Microsoft.WindowsAzure.Compute.AzureLogCollector"}

                    if ( ($status.Code -ne 0) -and ($status.Status -like '*error*'))
                    {
                        Write-Output "Error status is returned: $($Status.ExtensionSettingStatus.FormattedMessage.Message)."
                          $Completed = $true
                    }
                    elseif (($status.ExtensionSettingStatus.SubstatusList -eq $null -or $status.ExtensionSettingStatus.SubstatusList.Count -lt 1))
                    {
                          $Completed = $false
                          Write-Output "Waiting for operation to complete..."
                    }
                    else
                    {
                          $Completed = $true
                          Write-Output "Operation completed."

                    $UploadedFileUri = $Status.ExtensionSettingStatus.SubStatusList[0].FormattedMessage.Message
                          $blob = New-Object Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob($UploadedFileUri)

                  #
                        # This is an optional step:  For easier access to the file, we can generate a read-only SasUri directly to the file
                          #
                          $ExpiryTimeRead =  [DateTime]::Now.AddMinutes(120).ToString("o")
                          $ReadSasUri = New-AzStorageBlobSASToken -ExpiryTime $ExpiryTimeRead  -FullUri  -Blob  $blob.name -Container $blob.Container.Name -Permission r -Context $context

                        Write-Output "The uploaded file can be accessed using this link: $ReadSasUri"

                          #
                          #This is an optional step:  Remove the extension after we are done
                          #
                          Get-AzureVM -ServiceName $ServiceName -Name $VMName | Set-AzureVMExtension -Publisher Microsoft.WindowsAzure.Compute -ExtensionName "AzureLogCollector" -Version 1.* -Uninstall | Update-AzureVM -Verbose

                    }
                    Start-Sleep -s 5
            }
      }
      else
      {
          Write-Output "VM OS Type is not Windows, the extension cannot be enabled"
      }

}
else
{
  Write-Output "VM name is not specified, the extension cannot be enabled"
}
```

## <a name="next-steps"></a>Następne kroki
Teraz można przeanalizować lub skopiować dzienniki z jednej prostej lokalizacji.

