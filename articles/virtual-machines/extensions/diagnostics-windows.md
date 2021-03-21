---
title: Używanie Azure PowerShell do włączania diagnostyki na maszynie wirtualnej z systemem Windows
description: Dowiedz się, jak włączyć Diagnostyka Azure w maszynie wirtualnej z systemem Windows przy użyciu programu PowerShell
ms.topic: article
author: amjads1
ms.author: amjads
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.date: 12/15/2015
ms.openlocfilehash: e242a45e2c0f8247a1c23bf4d43a6e8b0a033d9b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560098"
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Włączanie Diagnostyka Azure w maszynie wirtualnej z systemem Windows przy użyciu programu PowerShell

Diagnostyka Azure jest możliwością platformy Azure, która umożliwia zbieranie danych diagnostycznych we wdrożonej aplikacji. Można użyć rozszerzenia diagnostyki do zbierania danych diagnostycznych, takich jak Dzienniki aplikacji lub liczniki wydajności, z maszyny wirtualnej platformy Azure z systemem Windows. 

 

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Włącz rozszerzenie diagnostyki, jeśli używasz modelu wdrażania Menedżer zasobów
Rozszerzenie diagnostyki można włączyć podczas tworzenia maszyny wirtualnej z systemem Windows za pomocą modelu wdrażania Azure Resource Manager, dodając konfigurację rozszerzenia do szablonu Menedżer zasobów. Zobacz [Tworzenie maszyny wirtualnej z systemem Windows przy użyciu monitorowania i diagnostyki przy użyciu szablonu Azure Resource Manager](diagnostics-template.md).

Aby włączyć rozszerzenie diagnostyki na istniejącej maszynie wirtualnej, która została utworzona za pomocą modelu wdrażania Menedżer zasobów, można użyć polecenia cmdlet programu PowerShell [Set-AzVMDiagnosticsExtension](/powershell/module/az.compute/set-azvmdiagnosticsextension) , jak pokazano poniżej.

```azurepowershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path
```


*$diagnosticsconfig _path* jest ścieżką do pliku, który zawiera konfigurację diagnostyki w formacie XML, zgodnie z opisem w poniższym [przykładzie](#sample-diagnostics-configuration) .  

Jeśli plik konfiguracji diagnostyki określa element **StorageAccount** z nazwą konta magazynu, skrypt *Set-AzVMDiagnosticsExtension* automatycznie ustawi rozszerzenie diagnostyki do wysyłania danych diagnostycznych do tego konta magazynu. Aby to działało, konto magazynu musi znajdować się w tej samej subskrypcji co maszyna wirtualna.

Jeśli w konfiguracji diagnostyki nie określono żadnych **StorageAccount** , należy przekazać parametr *StorageAccountName* do polecenia cmdlet. Jeśli parametr *StorageAccountName* jest określony, polecenie cmdlet zawsze będzie używać konta magazynu, które jest określone w parametrze, a nie tego, który jest określony w pliku konfiguracji diagnostyki.

Jeśli konto magazynu diagnostyki znajduje się w innej subskrypcji z maszyny wirtualnej, należy jawnie przekazać parametry *StorageAccountName* i *StorageAccountKey* do polecenia cmdlet. Parametr *StorageAccountKey* nie jest wymagany, jeśli konto magazynu diagnostyki znajduje się w tej samej subskrypcji, ponieważ polecenie cmdlet może automatycznie wysyłać zapytania i ustawić wartość klucza podczas włączania rozszerzenia diagnostyki. Jeśli jednak konto magazynu diagnostyki znajduje się w innej subskrypcji, polecenie cmdlet może nie być w stanie automatycznie uzyskać klucza i należy jawnie określić klucz za pomocą parametru *StorageAccountKey* .  

```azurepowershell
Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

Po włączeniu rozszerzenia diagnostyki na maszynie wirtualnej można pobrać bieżące ustawienia za pomocą polecenia cmdlet [Get-AzVmDiagnosticsExtension](/powershell/module/az.compute/get-azvmdiagnosticsextension) .

```azurepowershell
Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name
```

Polecenie cmdlet zwraca *PublicSettings*, który zawiera konfigurację diagnostyki. Istnieją dwa rodzaje obsługiwanych konfiguracji, WadCfg i xmlCfg. WadCfg jest konfiguracją JSON, a xmlCfg jest konfiguracją XML w formacie zakodowanym algorytmem Base64. Aby odczytać plik XML, należy go zdekodować.

```azurepowershell
$publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
$encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
$xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
Write-Host $xmlconfig
```

Polecenie cmdlet [Remove-AzVmDiagnosticsExtension](/powershell/module/az.compute/remove-azvmdiagnosticsextension) może służyć do usuwania rozszerzenia diagnostyki z maszyny wirtualnej.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>Włącz rozszerzenie diagnostyki, jeśli korzystasz z klasycznego modelu wdrażania

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Można użyć polecenia cmdlet [Set-AzureVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) , aby włączyć rozszerzenie diagnostyki na maszynie wirtualnej utworzonej za pomocą klasycznego modelu wdrażania. Poniższy przykład pokazuje, jak utworzyć nową maszynę wirtualną za pomocą klasycznego modelu wdrażania z włączonym rozszerzeniem diagnostyki.

```azurepowershell
$VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
$VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
$VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
New-AzVM -Location $Location -ServiceName $Service_Name -VM $VM
```

Aby włączyć rozszerzenie diagnostyki dla istniejącej maszyny wirtualnej, która została utworzona za pomocą klasycznego modelu wdrażania, należy najpierw użyć polecenia cmdlet [Get-AzureVM](/powershell/module/servicemanagement/azure.service/get-azurevm) , aby pobrać konfigurację maszyny wirtualnej. Następnie zaktualizuj konfigurację maszyny wirtualnej, aby uwzględnić rozszerzenie diagnostyki za pomocą polecenia cmdlet [Set-AzureVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) . Na koniec zastosuj zaktualizowaną konfigurację do maszyny wirtualnej za pomocą polecenia [Update-AzureVM](/powershell/module/servicemanagement/azure.service/update-azurevm).

```azurepowershell
$VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
$VM_Update = Set-AzureVMDiagnosticsExtension  -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM
```

## <a name="sample-diagnostics-configuration"></a>Przykładowa konfiguracja diagnostyki
Poniższy kod XML może służyć do publicznej konfiguracji diagnostyki z powyższymi skryptami. Ta Przykładowa konfiguracja przetransferuje różne liczniki wydajności na konto magazynu diagnostyki, a także błędy z aplikacji, zabezpieczeń i kanałów systemowych w dziennikach zdarzeń systemu Windows oraz błędy z dzienników infrastruktury diagnostyki.

Należy zaktualizować konfigurację, aby uwzględnić następujące elementy:

* Atrybut *ResourceID* elementu **Metrics** należy zaktualizować przy użyciu identyfikatora zasobu maszyny wirtualnej.
  
  * Identyfikator zasobu można utworzyć przy użyciu następującego wzorca: "*Identyfikator subskrypcji/subscriptions/{dla subskrypcji z maszyną wirtualną*}/resourceGroups/{*nazwę elementu zasobów dla maszyny wirtualnej*}/Providers/Microsoft.COMPUTE/virtualMachines/{*nazwę maszyny wirtualnej*}".
  * Na przykład jeśli Identyfikator subskrypcji dla subskrypcji, w której uruchomiono maszynę wirtualną, **to 11111111-1111-1111-1111-111111111111**, nazwa grupy zasobów dla grupy zasobów jest grupą **zasobów, a** Nazwa maszyny wirtualnej to **MyWindowsVM**, a następnie wartość identyfikatora *ResourceID* :
    
      ```xml
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * Aby uzyskać więcej informacji na temat sposobu generowania metryk na podstawie liczników wydajności i konfiguracji metryk, zobacz [Diagnostyka Azure metryk tabeli w magazynie](diagnostics-template.md#wadmetrics-tables-in-storage).
* Element **StorageAccount** musi zostać zaktualizowany przy użyciu nazwy konta magazynu diagnostyki.
  
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
          <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU utilization" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU privileged time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU user time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
            <annotation displayName="CPU frequency" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Processes" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Threads" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Handles" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Memory usage" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory available" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory committed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory commit limit" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory non-paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active read time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active write time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk read operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk write operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk read speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk write speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average read queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average write queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk free space (percentage)" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk free space (MB)" locale="en-us"/>
          </PerformanceCounterConfiguration>
        </PerformanceCounters>
        <Metrics resourceId="(Update with resource ID for the VM)" >
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
          <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
          <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
        </WindowsEventLog>
          </DiagnosticMonitorConfiguration>
        </WadCfg>
        <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
    </PublicConfig>
    ```

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać dodatkowe wskazówki dotyczące korzystania z możliwości Diagnostyka Azure i innych technik rozwiązywania problemów, zobacz [Włączanie diagnostyki na platformie Azure Cloud Services i Virtual Machines](../../cloud-services/cloud-services-dotnet-diagnostics.md).
* [Schemat konfiguracji diagnostyki](../../azure-monitor/agents/diagnostics-extension-versions.md) objaśnia różne opcje konfiguracji XML dla rozszerzenia diagnostyki.