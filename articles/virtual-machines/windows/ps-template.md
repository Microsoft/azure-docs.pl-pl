---
title: Tworzenie maszyny wirtualnej z systemem Windows na podstawie szablonu na platformie Azure
description: Użyj szablonu Menedżer zasobów i programu PowerShell, aby łatwo utworzyć nową maszynę wirtualną z systemem Windows.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ddd8166dd29d2210fecdd72d5be446df47a6c5df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89072302"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Tworzenie maszyny wirtualnej z systemem Windows na podstawie szablonu usługi Resource Manager

Dowiedz się, jak utworzyć maszynę wirtualną z systemem Windows przy użyciu szablonu Azure Resource Manager i Azure PowerShell z usługi Azure Cloud Shell. Szablon używany w tym artykule wdraża pojedynczą maszynę wirtualną z systemem Windows Server w nowej sieci wirtualnej z jedną podsiecią. Aby utworzyć maszynę wirtualną z systemem Linux, zobacz [How to Create a Virtual Machine Machine z szablonami Azure Resource Manager](../linux/create-ssh-secured-vm-from-template.md).

Alternatywą jest wdrożenie szablonu na podstawie Azure Portal. Aby otworzyć szablon w portalu, wybierz przycisk **Wdróż na platformie Azure** .

[![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json)

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Tworzenie maszyny wirtualnej platformy Azure zwykle obejmuje dwa kroki:

- Utwórz grupę zasobów. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Grupę zasobów należy utworzyć przed maszyną wirtualną.
- Tworzy maszynę wirtualną.

Poniższy przykład tworzy maszynę wirtualną na podstawie [szablonu szybkiego startu platformy Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json). Oto kopia szablonu:

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

Aby uruchomić skrypt programu PowerShell, wybierz pozycję **Wypróbuj** , aby otworzyć usługę Azure Cloud Shell. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie **Wklej**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter an unique DNS name for the public IP"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" `
    -adminUsername $adminUsername `
    -adminPassword $adminPassword `
    -dnsLabelPrefix $dnsLabelPrefix

 (Get-AzVm -ResourceGroupName $resourceGroupName).name

```

Jeśli zdecydujesz się zainstalować program PowerShell i używać go lokalnie, a nie z poziomu usługi Azure Cloud Shell, ten samouczek wymaga modułu Azure PowerShell. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

W poprzednim przykładzie określono szablon przechowywany w serwisie GitHub. Możesz również pobrać lub utworzyć szablon i określić ścieżkę lokalną za pomocą `--template-file` parametru.

Oto kilka dodatkowych zasobów:

- Aby dowiedzieć się, jak opracowywać szablony Menedżer zasobów, zobacz [dokumentację dotyczącą Azure Resource Manager](../../azure-resource-manager/index.yml).
- Aby wyświetlić schematy maszyn wirtualnych platformy Azure, zobacz [Dokumentacja szablonu platformy Azure](/azure/templates/microsoft.compute/allversions).
- Aby zobaczyć więcej przykładów szablonów maszyn wirtualnych, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Ostatnie polecenie programu PowerShell z poprzedniego skryptu pokazuje nazwę maszyny wirtualnej. Aby nawiązać połączenie z maszyną wirtualną, zobacz [jak nawiązać połączenie i zalogować się do maszyny wirtualnej platformy Azure z systemem Windows](./connect-logon.md).

## <a name="next-steps"></a>Następne kroki

- Jeśli wystąpiły problemy ze wdrożeniem, można zapoznać się z tematem [Rozwiązywanie typowych błędów wdrażania platformy Azure przy użyciu Azure Resource Manager](../../azure-resource-manager/templates/common-deployment-errors.md).
- Dowiedz się, jak utworzyć maszynę wirtualną i zarządzać nią w temacie [Tworzenie maszyn wirtualnych z systemem Windows i zarządzanie nimi za pomocą modułu Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Aby dowiedzieć się więcej na temat tworzenia szablonów, Wyświetl składnię i właściwości JSON dla wdrożonych typów zasobów:

- [Microsoft. Network/adresów publicipaddress](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft. Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft. COMPUTE/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
