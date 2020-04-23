---
title: Rozszerzenie diagnostyki platformy Azure dla systemu Windows
description: Monitorowanie maszyn wirtualnych systemu Windows platformy Azure przy użyciu rozszerzenia diagnostyki platformy Azure
author: johnkemnetz
manager: ashwink
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/06/2018
ms.author: johnkem
ms.openlocfilehash: 1d38a3cac5525de6835bbb0f9873cbd0636d44a9
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869672"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Rozszerzenie diagnostyki platformy Azure dla maszyn wirtualnych systemu Windows

## <a name="overview"></a>Omówienie

Rozszerzenie maszyny Wirtualnej diagnostyki platformy Azure umożliwia zbieranie danych monitorowania, takich jak liczniki wydajności i dzienniki zdarzeń, z maszyny Wirtualnej systemu Windows. Można dokładnie określić, jakie dane mają być zbierane i gdzie mają być przechowywane dane, takie jak konto usługi Azure Storage lub usługi Azure Event Hub. Można również użyć tych danych do tworzenia wykresów w witrynie Azure portal lub tworzenia alertów metryk.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie diagnostyki platformy Azure można uruchomić dla klienta systemu Windows 10, Windows Server 2008 R2, 2012, 2012 R2 i 2016.

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie diagnostyki platformy Azure wymaga, aby docelowa maszyna wirtualna była połączona z Internetem. 

## <a name="extension-schema"></a>Schemat rozszerzenia

[Schemat rozszerzenia diagnostyki platformy Azure i wartości właściwości są opisane w tym dokumencie.](../../azure-monitor/platform/diagnostics-extension-schema-windows.md)

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrożyć za pomocą szablonów usługi Azure Resource Manager. Schemat JSON wyszczególniony w poprzedniej sekcji może służyć w szablonie usługi Azure Resource Manager do uruchamiania rozszerzenia diagnostyki platformy Azure podczas wdrażania szablonu usługi Azure Resource Manager. Zobacz [Korzystanie z monitorowania i diagnostyki za pomocą szablonów maszyny Wirtualnej systemu Windows i usługi Azure Resource Manager](extensions-diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Wdrożenie interfejsu wiersza polecenia platformy Azure

Interfejsu wiersza polecenia platformy Azure może służyć do wdrażania rozszerzenia diagnostyki platformy Azure do istniejącej maszyny wirtualnej. Zastąp chronione ustawienia i właściwości ustawień prawidłowym JSON z powyższego schematu rozszerzenia. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>Wdrożenie programu PowerShell

Polecenie `Set-AzVMDiagnosticsExtension` może służyć do dodawania rozszerzenia diagnostyki platformy Azure do istniejącej maszyny wirtualnej. Zobacz też [Używanie programu PowerShell do włączania diagnostyki platformy Azure na maszynie wirtualnej z systemem Windows](ps-extensions-diagnostics.md).

 


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

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z witryny Azure portal i przy użyciu interfejsu wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

[Zobacz ten artykuł,](../../azure-monitor/platform/diagnostics-extension-troubleshooting.md) aby uzyskać bardziej kompleksowy przewodnik rozwiązywania problemów z rozszerzeniem diagnostyki platformy Azure.

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje na temat korzystania z pomocy technicznej platformy Azure, przeczytaj często zadawane [pytania dotyczące pomocy technicznej platformy Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się więcej o rozszerzeniu diagnostyki platformy Azure](../../azure-monitor/platform/diagnostics-extension-overview.md)
* [Przejrzyj schemat rozszerzenia i wersje](../../azure-monitor/platform/diagnostics-extension-schema-windows.md)
