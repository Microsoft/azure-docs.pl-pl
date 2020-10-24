---
title: akcelerator zapisu platformy Azure
description: Dokumentacja dotycząca sposobu włączania i używania akcelerator zapisu
author: raiye
manager: markkie
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 2/20/2019
ms.author: raiye
ms.subservice: disks
ms.openlocfilehash: e4f6cefd56c12162b370c78b6df2cd29ece030f1
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515716"
---
# <a name="enable-write-accelerator"></a>Włącz akcelerator zapisu

Akcelerator zapisu to możliwość dysku dla Virtual Machines serii M (VM) na Premium Storage z platformą Azure Managed Disks. Podobnie jak w przypadku nazw, celem funkcji jest poprawa opóźnienia operacji we/wy na platformie Azure Premium Storage. Akcelerator zapisu doskonale nadaje się do miejsca, w którym są wymagane aktualizacje plików dziennika, aby zachować wysoką dostępność nowoczesnych baz danych.

Akcelerator zapisu jest ogólnie dostępna dla maszyn wirtualnych serii M w chmurze publicznej.

## <a name="planning-for-using-write-accelerator"></a>Planowanie korzystania z akcelerator zapisu

Akcelerator zapisu należy używać w przypadku woluminów zawierających dziennik transakcji lub dzienników ponownego wykonywania w systemie DBMS. Nie zaleca się używania akcelerator zapisu dla woluminów danych systemu DBMS, ponieważ funkcja została zoptymalizowana pod kątem używania z dyskami dzienników.

Akcelerator zapisu działa tylko w połączeniu z [usługą Azure Managed disks](https://azure.microsoft.com/services/managed-disks/).

> [!IMPORTANT]
> Włączenie akcelerator zapisu dla dysku systemu operacyjnego maszyny wirtualnej spowoduje ponowne uruchomienie maszyny wirtualnej.
>
> Aby umożliwić akcelerator zapisu na istniejącym dysku platformy Azure, który nie jest częścią kompilacji woluminu z wielu dysków z systemem Windows lub menedżerami woluminów, funkcja miejsca do magazynowania systemu Windows, serwer plików skalowalny w poziomie systemu Windows (SOFS), Linux LVM lub MDADM, musi zostać wyłączana. Aplikacje baz danych korzystające z dysku platformy Azure muszą zostać zamknięte.
>
> Jeśli chcesz włączyć lub wyłączyć akcelerator zapisu istniejący wolumin, który jest zbudowany z wielu dysków usługi Azure Premium Storage i rozłożony przy użyciu dysku systemu Windows lub menedżerów woluminów, funkcji miejsca do magazynowania systemu Windows, serwera plików skalowalnego w poziomie systemu Windows (SOFS), Linux LVM lub MDADM, wszystkie dyski tworzące wolumin muszą być włączone lub wyłączone dla akcelerator zapisu w oddzielnych krokach. **Przed włączeniem lub wyłączeniem akcelerator zapisu w takiej konfiguracji należy zamknąć maszynę wirtualną platformy Azure**.

Włączanie akcelerator zapisu dysków systemu operacyjnego nie powinno być konieczne w przypadku konfiguracji maszyn wirtualnych związanych z systemem SAP.

### <a name="restrictions-when-using-write-accelerator"></a>Ograniczenia w przypadku korzystania z akceleratora zapisu

W przypadku korzystania z akcelerator zapisu dysku/dysku VHD platformy Azure obowiązują następujące ograniczenia:

- Buforowanie dysku w warstwie Premium musi mieć wartość "none" lub "Read Only". Wszystkie inne tryby buforowania nie są obsługiwane.
- Migawka nie jest obecnie obsługiwana w przypadku dysków z obsługą akcelerator zapisu. Podczas wykonywania kopii zapasowej usługa Azure Backup automatycznie wyklucza dyski z włączoną obsługą akcelerator zapisu dołączone do maszyny wirtualnej.
- Tylko mniejsze rozmiary operacji we/wy (<= 512 KiB) zajmują przyspieszoną ścieżkę. W przypadku obciążeń, w których dane są ładowane zbiorczo lub gdy bufory dziennika transakcji w różnych systemach DBMS są wypełniane w większym stopniu przed utrwaleniem magazynu, prawdopodobieństwo jest to, że we/wy zapisywana na dysku nie jest przygotowywana ścieżka przyspieszona.

Istnieją limity wirtualnych dysków twardych Premium Storage platformy Azure dla maszyny wirtualnej, które mogą być obsługiwane przez akcelerator zapisu. Bieżące limity to:

| Jednostka SKU maszyny wirtualnej | Liczba dysków akcelerator zapisu | akcelerator zapisu liczba operacji we/wy dysku na maszynę wirtualną |
| --- | --- | --- |
| M416ms_v2, M416s_v2| 16 | 20000 |
| M208ms_v2, M208s_v2| 8 | 10 000 |
| M128ms, M128s | 16 | 20000 |
| M64ms, M64ls, M64s | 8 | 10 000 |
| M32ms, M32ls, M32ts, M32s | 4 | 5000 |
| M16ms, M16s | 2 | 2500 |
| M8ms, M8s | 1 | 1250 |

Limity IOPS są na maszynę wirtualną, a *nie* na dysku. Wszystkie dyski akcelerator zapisu mają ten sam limit operacji we/wy na maszynę wirtualną. Dołączone dyski nie mogą przekraczać limitu liczby IOPS akceleratora zapisu dla maszyny wirtualnej. Przykładowo, mimo że dołączone dyski mogą wykonywać 30 000 operacji we/wy na sekundę, system nie zezwala na przechodzenie dysków powyżej 20 000 IOPS dla M416ms_v2.

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Włączanie akceleratora zapisu na określonym dysku

W następnych sekcjach opisano, jak akcelerator zapisu można włączyć na platformie Azure Premium Storage VHD.

### <a name="prerequisites"></a>Wymagania wstępne

Poniższe wymagania wstępne dotyczą użycia akcelerator zapisu w tym momencie:

- Dyski, które mają być stosowane do akcelerator zapisu platformy Azure, muszą być [dyskami zarządzanymi przez platformę Azure](https://azure.microsoft.com/services/managed-disks/) w Premium Storage.
- Musisz używać maszyny wirtualnej z serii M

## <a name="enabling-azure-write-accelerator-using-azure-powershell"></a>Włączanie akceleratora zapisu platformy Azure przy użyciu programu Azure PowerShell

Moduł Azure PowerShell Shell z wersji 5.5.0 zawiera zmiany odpowiednich poleceń cmdlet, aby włączyć lub wyłączyć akcelerator zapisu dla określonych dysków Premium Storage platformy Azure.
Aby można było włączyć lub wdrożyć dyski obsługiwane przez akcelerator zapisu, następujące polecenia powłoki pomogą zmienić i rozszerzyć, aby zaakceptować parametr akcelerator zapisu.

Dodano nowy parametr przełącznika **-WriteAccelerator** do następujących poleceń cmdlet:

- [Set-AzVMOsDisk](/powershell/module/az.compute/set-azvmosdisk?view=azurermps-6.0.0)
- [Add-AzVMDataDisk](/powershell/module/az.compute/Add-AzVMDataDisk?view=azurermps-6.0.0)
- [Set-AzVMDataDisk](/powershell/module/az.compute/Set-AzVMDataDisk?view=azurermps-6.0.0)
- [Add-AzVmssDataDisk](/powershell/module/az.compute/Add-AzVmssDataDisk?view=azurermps-6.0.0)

Nie podawanie parametru ustawia właściwość na false i spowoduje wdrożenie dysków bez obsługi przez akcelerator zapisu.

Nowy parametr przełącznika **-OsDiskWriteAccelerator** został dodany do następujących poleceń cmdlet:

- [Set-AzVmssStorageProfile](/powershell/module/az.compute/Set-AzVmssStorageProfile?view=azurermps-6.0.0)

Nieokreślanie parametru domyślnie ustawia właściwość false, zwracając dyski, które nie wykorzystują akcelerator zapisu.

Nowy opcjonalny parametr logiczny (niedopuszczający wartości null) **— OsDiskWriteAccelerator** został dodany do następujących poleceń cmdlet:

- [Update-AzVM](/powershell/module/az.compute/Update-AzVM?view=azurermps-6.0.0)
- [Update-AzVmss](/powershell/module/az.compute/Update-AzVmss?view=azurermps-6.0.0)

Określ $true lub $false do kontrolowania obsługi akcelerator zapisu platformy Azure z dyskami.

Przykłady poleceń może wyglądać następująco:

```powershell
New-AzVMConfig | Set-AzVMOsDisk | Add-AzVMDataDisk -Name "datadisk1" | Add-AzVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVM

Get-AzVM | Update-AzVM -OsDiskWriteAccelerator $true

New-AzVmssConfig | Set-AzVmssStorageProfile -OsDiskWriteAccelerator | Add-AzVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVmss

Get-AzVmss | Update-AzVmss -OsDiskWriteAccelerator:$false
```

Dwa główne scenariusze można wykonać w skrypcie, jak pokazano w poniższych sekcjach.

### <a name="adding-a-new-disk-supported-by-write-accelerator-using-powershell"></a>Dodawanie nowego dysku obsługiwanego przez akcelerator zapisu przy użyciu programu PowerShell

Za pomocą tego skryptu można dodać nowy dysk do maszyny wirtualnej. Dysk utworzony za pomocą tego skryptu używa akcelerator zapisu.

Zamień `myVM` , `myWAVMs` , `log001` , rozmiar dysku i LunID dysku na wartości odpowiednie dla określonego wdrożenia.

```powershell
# Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

### <a name="enabling-write-accelerator-on-an-existing-azure-disk-using-powershell"></a>Włączanie akcelerator zapisu na istniejącym dysku platformy Azure przy użyciu programu PowerShell

Za pomocą tego skryptu można włączyć akcelerator zapisu na istniejącym dysku. Zastąp `myVM` `myWAVMs` wartości, i `test-log001` wartościami odpowiednimi dla określonego wdrożenia. Skrypt dodaje akcelerator zapisu do istniejącego dysku, gdzie wartość dla **$newStatus** jest ustawiona na "$true". Użycie wartości "$false" spowoduje wyłączenie akcelerator zapisu na danym dysku.

```powershell
#Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

> [!Note]
> Wykonanie skryptu powyżej spowoduje odłączenie określonego dysku, włączenie akcelerator zapisu na dysku, a następnie ponowne dołączenie dysku

## <a name="enabling-write-accelerator-using-the-azure-portal"></a>Włączanie akceleratora zapisu platformy Azure przy użyciu witryny Azure Portal

Akcelerator zapisu można włączyć za pośrednictwem portalu, w którym można określić ustawienia buforowania dysku:

![akcelerator zapisu na Azure Portal](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

## <a name="enabling-write-accelerator-using-the-azure-cli"></a>Włączanie akceleratora zapisu platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Aby włączyć akcelerator zapisu, można użyć [interfejsu wiersza polecenia platformy Azure](/cli/azure/?view=azure-cli-latest) .

Aby włączyć akcelerator zapisu na istniejącym dysku, użyj [AZ VM Update](/cli/azure/vm?view=azure-cli-latest#az-vm-update), można użyć następujących przykładów w przypadku zastąpienia wartości diskname, VMName i Source z własnymi wartościami: `az vm update -g group1 -n vm1 -write-accelerator 1=true`

Aby dołączyć dysk z włączonym akcelerator zapisu przy użyciu opcji [AZ VM Disk Attach](/cli/azure/vm/disk?view=azure-cli-latest#az-vm-disk-attach), można użyć poniższego przykładu w przypadku zastąpienia własnych wartości: `az vm disk attach -g group1 -vm-name vm1 -disk d1 --enable-write-accelerator`

Aby wyłączyć akcelerator zapisu, użyj [AZ VM Update](/cli/azure/vm?view=azure-cli-latest#az-vm-update), ustawiając właściwości na false: `az vm update -g group1 -n vm1 -write-accelerator 0=false 1=false`

## <a name="enabling-write-accelerator-using-rest-apis"></a>Włączanie akcelerator zapisu przy użyciu interfejsów API REST

Aby przeprowadzić wdrożenie za pomocą interfejsu API REST platformy Azure, należy zainstalować usługę Azure armclient.

### <a name="install-armclient"></a>Zainstaluj armclient

Aby uruchomić armclient, należy zainstalować ją za poorednictwem czekolady. Można go zainstalować za poorednictwem cmd.exe lub PowerShell. Użyj podwyższonych uprawnień dla tych poleceń ("Uruchom jako administrator").

Korzystając z cmd.exe, uruchom następujące polecenie: `@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"`

Korzystając z usługi PowerShell, uruchom następujące polecenie: `Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

Teraz można zainstalować armclient za pomocą następującego polecenia w cmd.exe lub PowerShell `choco install armclient`

### <a name="getting-your-current-vm-configuration"></a>Pobieranie bieżącej konfiguracji maszyny wirtualnej

Aby zmienić atrybuty konfiguracji dysku, należy najpierw pobrać bieżącą konfigurację w pliku JSON. Bieżącą konfigurację można uzyskać, wykonując następujące polecenie: `armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>`

Zamień warunki w elemencie "<<   >>" na dane, łącznie z nazwą pliku, który powinien zawierać plik JSON.

Dane wyjściowe mogą wyglądać następująco:

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

Następnie zaktualizuj plik JSON i Włącz akcelerator zapisu na dysku o nazwie "LOG1". Można to osiągnąć przez dodanie tego atrybutu do pliku JSON po wpisie pamięci podręcznej dysku.

```JSON
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Następnie zaktualizuj istniejące wdrożenie za pomocą tego polecenia: `armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>`

Dane wyjściowe powinny wyglądać podobnie do przedstawionego poniżej. Można zobaczyć, że akcelerator zapisu włączone dla jednego dysku.

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"
```

Po wprowadzeniu tej zmiany dysk powinien być obsługiwany przez akcelerator zapisu.
