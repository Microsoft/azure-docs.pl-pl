---
title: Wdrażanie aplikacji w zestawie skalowania maszyn wirtualnych platformy Azure
description: Dowiedz się, jak wdrażać aplikacje w wystąpieniach maszyn wirtualnych z systemem Linux i Windows w zestawie skalowania
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurepowershell
ms.openlocfilehash: e7a4ddaf74df6e04c1597b9c106cd458ddebac55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079595"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Wdrażanie aplikacji na zestawach skalowania maszyn wirtualnych

Aby uruchamiać aplikacje na wystąpieniach maszyn wirtualnych w zestawie skalowania, musisz najpierw zainstalować składniki aplikacji i wymagane pliki. W tym artykule przedstawiono sposoby tworzenia niestandardowego obrazu maszyny wirtualnej dla wystąpień w zestawie skalowania lub automatycznego uruchamiania skryptów instalacji na istniejących wystąpieniach maszyn wirtualnych. Dowiesz się również, jak zarządzać aktualizacjami aplikacji lub systemu operacyjnego w zestawie skalowania.


## <a name="build-a-custom-vm-image"></a>Tworzenie niestandardowego obrazu maszyny wirtualnej
W przypadku tworzenia wystąpień w zestawie skalowania przy użyciu jednego z obrazów platformy Azure żadne dodatkowe oprogramowanie nie jest instalowane ani konfigurowane. Instalację tych składników można zautomatyzować, jednak dodanie do czasu potrzebnego do aprowizacji wystąpień maszyn wirtualnych w zestawach skalowania. W przypadku zastosowania wielu zmian konfiguracji wystąpień maszyn wirtualnych istnieje obciążenie związane z zarządzaniem za pomocą tych skryptów i zadań konfiguracyjnych.

Aby zmniejszyć liczbę zarządzania konfiguracją i czas na zainicjowanie obsługi maszyny wirtualnej, możesz utworzyć niestandardowy obraz maszyny wirtualnej, który jest gotowy do uruchomienia aplikacji, gdy tylko wystąpienie zostanie udostępnione w zestawie skalowania. Aby uzyskać więcej informacji na temat tworzenia i używania niestandardowego obrazu maszyny wirtualnej z zestawem skalowania, zobacz następujące samouczki:

- [Interfejs wiersza polecenia platformy Azure](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="install-an-app-with-the-custom-script-extension"></a><a name="already-provisioned"></a>Instalowanie aplikacji z rozszerzeniem niestandardowego skryptu
Rozszerzenie niestandardowego skryptu pobiera i wykonuje skrypty na maszynach wirtualnych platformy Azure. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalowania oprogramowania lub każdego innego zadania związanego z konfiguracją lub zarządzaniem. Skrypty można pobrać z usługi Azure Storage lub GitHub bądź można je dostarczyć do witryny Azure Portal w czasie wykonywania rozszerzenia. Aby uzyskać więcej informacji na temat sposobu instalowania aplikacji z rozszerzeniem niestandardowego skryptu, zobacz następujące samouczki:

- [Interfejs wiersza polecenia platformy Azure](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Szablon usługi Azure Resource Manager](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Instalowanie aplikacji na maszynie wirtualnej z systemem Windows przy użyciu programu PowerShell DSC
[Konfiguracja żądanego stanu programu PowerShell (DSC)](/powershell/scripting/dsc/overview/overview) to platforma zarządzania służąca do definiowania konfiguracji maszyn docelowych. Konfiguracje DSC definiują, co należy zainstalować na komputerze i jak skonfigurować hosta. Lokalny aparat Configuration Manager (LCM) działa na każdym węźle docelowym, który przetwarza żądane akcje w oparciu o wypychaną konfigurację.

Rozszerzenie DSC programu PowerShell umożliwia dostosowywanie wystąpień maszyn wirtualnych w zestawie skalowania przy użyciu programu PowerShell. Poniższy przykład:

- Powoduje, że wystąpienia maszyn wirtualnych pobierają pakiet DSC z usługi GitHub *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Ustawia rozszerzenie do uruchamiania skryptu instalacji — `configure-http.ps1`
- Pobiera informacje o zestawie skalowania za pomocą [Get-AzVmss](/powershell/module/az.compute/get-azvmss)
- Stosuje rozszerzenie do wystąpień maszyny wirtualnej za pomocą [Update-AzVmss](/powershell/module/az.compute/update-azvmss)

Rozszerzenie DSC jest stosowane do wystąpień maszyn wirtualnych *myScaleSet* w grupie zasobów o nazwie Moja *resourceName*. Wprowadź własne nazwy w następujący sposób:

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

Jeśli zasady uaktualniania zestawu skalowania są *Ręczne*, zaktualizuj wystąpienia maszyn wirtualnych za pomocą funkcji [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance). To polecenie cmdlet stosuje zaktualizowaną konfigurację zestawu skalowania do wystąpień maszyn wirtualnych i instaluje aplikację.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Instalowanie aplikacji na maszynie wirtualnej z systemem Linux przy użyciu funkcji Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) to powszechnie używana metoda dostosowywania maszyny wirtualnej z systemem Linux podczas jej pierwszego rozruchu. Za pomocą pakietu cloud-init można instalować pakiety i zapisywać pliki lub konfigurować użytkowników i zabezpieczenia. Pakiet cloud-init jest uruchamiany w trakcie początkowego rozruchu, więc do zastosowania konfiguracji nie są wymagane żadne dodatkowe kroki ani agenci.

Pakiet cloud-init działa również w różnych dystrybucjach. Przykładowo nie używa się poleceń **apt-get install** lub **yum install** do zainstalowania pakietu. Zamiast tego możesz zdefiniować listę pakietów do zainstalowania. Pakiet cloud-init automatycznie używa natywnego narzędzia do zarządzania pakietami dla wybranej dystrybucji.

Aby uzyskać więcej informacji, w tym przykładowy plik *cloud-init.txt* , zobacz [Używanie funkcji Cloud-init do dostosowywania maszyn wirtualnych platformy Azure](../virtual-machines/linux/using-cloud-init.md).

Aby utworzyć zestaw skalowania i użyć pliku Cloud-init, należy dodać `--custom-data` parametr do polecenia [AZ VMSS Create](/cli/azure/vmss) i określić nazwę pliku Cloud-init. Poniższy przykład tworzy zestaw skalowania o nazwie *myScaleSet* w liście *zasobów* i konfiguruje wystąpienia maszyn wirtualnych przy użyciu pliku o nazwie *cloud-init.txt*. Wprowadź własne nazwy w następujący sposób:

```azurecli
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```


### <a name="install-applications-with-os-updates"></a>Instalowanie aplikacji z aktualizacjami systemu operacyjnego
Gdy dostępne są nowe wersje systemu operacyjnego, można użyć lub utworzyć nowy obraz niestandardowy i [wdrożyć uaktualnienia systemu operacyjnego](virtual-machine-scale-sets-upgrade-scale-set.md) w zestawie skalowania. Każde wystąpienie maszyny wirtualnej zostanie uaktualnione do najnowszego obrazu, który określisz. Możesz użyć niestandardowego obrazu z wstępnie zainstalowaną aplikacją, rozszerzeniem niestandardowego skryptu lub DSC programu PowerShell, aby aplikacja była automatycznie dostępna podczas przeprowadzania uaktualnienia. Może być konieczne zaplanowanie konserwacji aplikacji podczas wykonywania tego procesu, aby upewnić się, że nie występują problemy ze zgodnością wersji.

W przypadku korzystania z niestandardowego obrazu maszyny wirtualnej z wstępnie zainstalowaną aplikacją można zintegrować aktualizacje aplikacji z potokiem wdrożenia w celu utworzenia nowych obrazów i wdrożenia uaktualnień systemu operacyjnego w zestawie skalowania. Takie podejście umożliwia potokowi pobranie najnowszych kompilacji aplikacji, utworzenie i zweryfikowanie obrazu maszyny wirtualnej, a następnie uaktualnienie wystąpień maszyn wirtualnych w zestawie skalowania. Aby uruchomić potok wdrożenia, który kompiluje i wdraża aktualizacje aplikacji w niestandardowych obrazach maszyn wirtualnych, można [utworzyć obraz programu pakujący i wdrożyć go przy użyciu Azure DevOps Services](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)lub użyć innej platformy, takiej jak [Spinnaker](https://www.spinnaker.io/) lub [Jenkins](https://jenkins.io/).


## <a name="next-steps"></a>Następne kroki
Podczas kompilowania i wdrażania aplikacji w zestawach skalowania można przejrzeć [projekt zestawu skalowania](virtual-machine-scale-sets-design-overview.md). Aby uzyskać więcej informacji na temat zarządzania zestawem skalowania, zobacz [Używanie programu PowerShell do zarządzania zestawem skalowania](./virtual-machine-scale-sets-manage-powershell.md).
