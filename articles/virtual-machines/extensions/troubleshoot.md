---
title: Rozwiązywanie problemów z błędami rozszerzenia maszyny wirtualnej systemu Windows
description: Dowiedz się więcej na temat rozwiązywania problemów z błędami rozszerzenia maszyny wirtualnej
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/29/2016
ms.openlocfilehash: 8cc8a0b5ae0a83a152168b14a2c5577f8f7b48ab
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564802"
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


Możesz również wyzwolić nowe GoalState do maszyny wirtualnej, wykonując polecenie "ponownie Zastosuj maszynę wirtualną". [Ponowne zastosowanie](/rest/api/compute/virtualmachines/reapply) maszyny wirtualnej jest interfejsem API wprowadzonym w 2020 w celu ponownego zastosowania stanu maszyny wirtualnej. Zalecamy to w czasie, gdy można tolerować krótki czas przestoju maszyny wirtualnej. Gdy ponowne zastosowanie nie powoduje ponownego uruchomienia maszyny wirtualnej, a większość przypadków, w których ponowne zastosowanie nie spowoduje ponownego uruchomienia maszyny wirtualnej, istnieje bardzo małe ryzyko, że niektóre inne oczekujące aktualizacje modelu maszyny wirtualnej zostaną zastosowane po ponownym zastosowaniu wyzwalają nowe Stany celu, a inne zmiany mogą wymagać ponownego uruchomienia. 

Azure Portal:

W portalu wybierz maszynę wirtualną, a następnie w lewym okienku w obszarze **Pomoc techniczna i rozwiązywanie problemów** wybierz pozycję **ponowne wdrożenie + Zastosuj** ponownie, a następnie wybierz pozycję **ponownie Zastosuj**.


Azure PowerShell *(zastąp nazwę RG i nazwę maszyny wirtualnej wartościami)*:

```azurepowershell
Set-AzVM -ResourceGroupName <RG Name> -Name <VM Name> -Reapply
```

Interfejs wiersza polecenia platformy Azure *(zastąp nazwę RG i nazwę maszyny wirtualnej wartościami)*:

```azurecli
az vm reapply -g <RG Name> -n <VM Name>
```

Jeśli "ponowne zastosowanie maszyny wirtualnej" nie zadziałało, możesz dodać nowy pusty dysk danych do maszyny wirtualnej z usługi Azure portal zarządzania, a następnie usunąć go później po dodaniu certyfikatu do powrotem.