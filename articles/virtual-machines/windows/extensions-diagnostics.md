---
title: Diagnostyka Azure rozszerzenie dla systemu Windows
description: Monitorowanie maszyn wirtualnych z systemem Windows Azure przy użyciu rozszerzenia Diagnostyka Azure
author: johnkemnetz
manager: ashwink
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/06/2018
ms.author: johnkem
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 06c614d57002c7944d88df123d06ac63d04b288f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100591153"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Diagnostyka Azure rozszerzenie dla maszyn wirtualnych z systemem Windows

## <a name="overview"></a>Omówienie

Rozszerzenie maszyny wirtualnej Diagnostyka Azure umożliwia zbieranie danych monitorowania, takich jak liczniki wydajności i dzienniki zdarzeń, z maszyny wirtualnej z systemem Windows. Można szczegółowo określić, jakie dane mają być zbierane, oraz miejsce, w którym mają być wykonywane dane, takie jak konto usługi Azure Storage lub centrum zdarzeń platformy Azure. Możesz również użyć tych danych do kompilowania wykresów w Azure Portal lub tworzenia alertów dotyczących metryk.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie Diagnostyka Azure można uruchomić dla klienta systemu Windows 10, Windows Server 2008 R2, 2012, 2012 R2 i 2016.

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie Diagnostyka Azure wymaga, aby docelowa maszyna wirtualna była połączona z Internetem. 

## <a name="extension-schema"></a>Schemat rozszerzenia

[W tym dokumencie opisano Diagnostyka Azure schematu i wartości właściwości rozszerzenia.](../../azure-monitor/agents/diagnostics-extension-schema-windows.md)

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyny wirtualnej platformy Azure można wdrażać za pomocą szablonów Azure Resource Manager. Schemat JSON opisany w poprzedniej sekcji można użyć w szablonie Azure Resource Manager, aby uruchomić rozszerzenie Diagnostyka Azure podczas wdrażania szablonu Azure Resource Manager. Zobacz [Używanie monitorowania i diagnostyki z maszynami wirtualnymi z systemem Windows i szablonami Azure Resource Manager](../extensions/diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Wdrożenie interfejsu wiersza polecenia platformy Azure

Interfejsu wiersza polecenia platformy Azure można użyć do wdrożenia rozszerzenia Diagnostyka Azure na istniejącej maszynie wirtualnej. Zastąp właściwości ustawienia i ustawienia chronione prawidłowym kodem JSON z powyższego schematu rozszerzenia. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>Wdrażanie przy użyciu programu PowerShell

`Set-AzVMDiagnosticsExtension`Polecenia można użyć do dodania rozszerzenia Diagnostyka Azure do istniejącej maszyny wirtualnej. [Aby włączyć Diagnostyka Azure w maszynie wirtualnej z systemem Windows, zobacz również korzystanie z programu PowerShell](../extensions/diagnostics-windows.md).

 


```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z Azure Portal i przy użyciu interfejsu wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia dla danej maszyny wirtualnej, uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Zapoznaj się z [tym artykułem](../../azure-monitor/agents/diagnostics-extension-troubleshooting.md) , aby uzyskać bardziej szczegółowy przewodnik rozwiązywania problemów dla rozszerzenia Diagnostyka Azure.

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się więcej o rozszerzeniu Diagnostyka Azure](../../azure-monitor/agents/diagnostics-extension-overview.md)
* [Przejrzyj schemat i wersje rozszerzeń](../../azure-monitor/agents/diagnostics-extension-schema-windows.md)
