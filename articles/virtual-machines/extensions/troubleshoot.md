---
title: Rozwiązywanie problemów z błędami rozszerzenia maszyny wirtualnej systemu Windows
description: Dowiedz się więcej na temat rozwiązywania problemów z błędami rozszerzenia maszyny wirtualnej
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: gwallace
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: bca826cda8dfe47c341886faaf4a0d66f09d37d2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966347"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Rozwiązywanie problemów z błędami rozszerzenia maszyny wirtualnej systemu Windows Azure
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Wyświetlanie stanu rozszerzenia
Szablony Azure Resource Manager mogą być wykonywane z Azure PowerShell. Po wykonaniu szablonu stan rozszerzenia może być wyświetlany z poziomu Azure Resource Explorer lub narzędzi wiersza polecenia.

Oto przykład:

Azure PowerShell:

```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

Oto przykładowe dane wyjściowe:

```output
Extensions:  {
  "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
  "Name": "myCustomScriptExtension",
  "SubStatuses": [
    {
      "Code": "ComponentStatus/StdOut/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
          \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
          test.txt                          \\n\\n",
                  "Time": null
      },
    {
      "Code": "ComponentStatus/StdErr/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "",
      "Time": null
    }
  ]
}
```

## <a name="troubleshooting-extension-failures"></a>Rozwiązywanie problemów z błędami rozszerzeń

### <a name="rerun-the-extension-on-the-vm"></a>Uruchom ponownie rozszerzenie na maszynie wirtualnej
W przypadku uruchamiania skryptów na maszynie wirtualnej przy użyciu rozszerzenia niestandardowego skryptu można czasami uruchomić polecenie w przypadku, gdy maszyna wirtualna została utworzona pomyślnie, ale skrypt nie powiódł się. W tych warunkach zalecany sposób odzyskania po tym błędzie polega na usunięciu rozszerzenia i ponownym uruchomieniu szablonu.
Uwaga: w przyszłości ta funkcja zostanie rozszerzona w celu usunięcia konieczności odinstalowania rozszerzenia.

#### <a name="remove-the-extension-from-azure-powershell"></a>Usuń rozszerzenie z Azure PowerShell
```azurepowershell
Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"
```

Po usunięciu rozszerzenia można ponownie wykonać szablon w celu uruchomienia skryptów na maszynie wirtualnej.

### <a name="trigger-a-new-goalstate-to-the-vm"></a>Wyzwalanie nowego GoalState na maszynie wirtualnej
Można zauważyć, że rozszerzenie nie zostało wykonane lub kończy się niepowodzeniem z powodu braku "generatora certyfikatów CRP systemu Windows Azure" (ten certyfikat jest używany do zabezpieczenia transportu ustawień chronionych przez rozszerzenie).
Ten certyfikat zostanie automatycznie wygenerowany ponownie przez ponowne uruchomienie agenta gościa systemu Windows od wewnątrz maszyny wirtualnej:
- Otwórz Menedżera zadań
- Przejdź do karty szczegóły
- Lokalizowanie procesu WindowsAzureGuestAgent.exe
- Kliknij prawym przyciskiem myszy, a następnie wybierz pozycję "Zakończ zadanie". Proces zostanie automatycznie uruchomiony ponownie


Możesz również wyzwolić nowe GoalState do maszyny wirtualnej, wykonując "pustą aktualizację":

Azure PowerShell:

```azurepowershell
$vm = Get-AzureRMVM -ResourceGroupName <RGName> -Name <VMName>  
Update-AzureRmVM -ResourceGroupName <RGName> -VM $vm  
```

Interfejs wiersza polecenia platformy Azure:

```azurecli
az vm update -g <rgname> -n <vmname>
```

Jeśli "pusta aktualizacja" nie zadziałała, można dodać nowy pusty dysk danych do maszyny wirtualnej z usługi Azure portal zarządzania, a następnie usunąć go później po dodaniu certyfikatu.
