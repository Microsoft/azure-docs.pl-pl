---
title: Wdrażanie aplikacji w zestawie skalowania maszyn wirtualnych platformy Azure
description: Dowiedz się, jak wdrażać aplikacje w wystąpieniach maszyn wirtualnych z systemami Linux i Windows w zestawie skalowania
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 078c78f9fe9e52ee2a71784d5c5ae5c2a478fbe4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484259"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Wdrażanie aplikacji na zestawach skalowania maszyn wirtualnych

Aby uruchamiać aplikacje na wystąpieniach maszyn wirtualnych w zestawie skalowania, musisz najpierw zainstalować składniki aplikacji i wymagane pliki. W tym artykule oprowadzono sposoby tworzenia niestandardowego obrazu maszyny wirtualnej dla wystąpień w zestawie skalowania lub automatycznego uruchamiania skryptów instalacji na istniejących wystąpieniach maszyn wirtualnych. Dowiesz się również, jak zarządzać aktualizacjami aplikacji lub systemu operacyjnego w zestawie skalowania.


## <a name="build-a-custom-vm-image"></a>Tworzenie niestandardowego obrazu maszyny wirtualnej
Jeśli do utworzenia wystąpień w zestawie skalowania używasz jednego z obrazów platformy Azure, nie jest instalowane ani konfigurowane żadne dodatkowe oprogramowanie. Instalację tych składników można zautomatyzować, ale zwiększa to czas aprowizowania wystąpień maszyn wirtualnych w zestawach skalowania. W przypadku zastosowania wielu zmian konfiguracji do wystąpień maszyn wirtualnych istnieje obciążenie związane z zarządzaniem tymi skryptami i zadaniami konfiguracyjnymi.

Aby skrócić czas zarządzania konfiguracją i aprowizować maszynę wirtualną, możesz utworzyć niestandardowy obraz maszyny wirtualnej, który będzie gotowy do uruchomienia aplikacji natychmiast po aprowizowi wystąpienia w zestawie skalowania. Aby uzyskać więcej informacji na temat tworzenia i używania niestandardowego obrazu maszyny wirtualnej z zestawem skalowania, zobacz następujące samouczki:

- [Interfejs wiersza polecenia platformy Azure](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="install-an-app-with-the-custom-script-extension"></a><a name="already-provisioned"></a>Instalowanie aplikacji za pomocą rozszerzenia niestandardowego skryptu
Rozszerzenie niestandardowego skryptu pobiera i wykonuje skrypty na maszynach wirtualnych platformy Azure. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalowania oprogramowania lub każdego innego zadania związanego z konfiguracją lub zarządzaniem. Skrypty można pobrać z usługi Azure Storage lub GitHub bądź można je dostarczyć do witryny Azure Portal w czasie wykonywania rozszerzenia. Aby uzyskać więcej informacji na temat sposobu instalowania aplikacji z rozszerzeniem niestandardowego skryptu, zobacz następujące samouczki:

- [Interfejs wiersza polecenia platformy Azure](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Szablon usługi Azure Resource Manager](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Instalowanie aplikacji na maszynie wirtualnej z systemem Windows przy użyciu konfiguracji DSC programu PowerShell
[Program PowerShell Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) to platforma zarządzania do definiowania konfiguracji maszyn docelowych. Konfiguracje DSC definiują, co zainstalować na maszynie i jak skonfigurować hosta. Aparat LCM (Local Menedżer konfiguracji) działa w każdym węźle docelowym, który przetwarza żądane akcje w oparciu o wypychane konfiguracje.

Rozszerzenie DSC programu PowerShell umożliwia dostosowywanie wystąpień maszyn wirtualnych w zestawie skalowania przy użyciu programu PowerShell. Poniższy przykład:

- Instruuje wystąpienia maszyn wirtualnych, aby pobierały pakiet DSC z usługi GitHub — *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Ustawia rozszerzenie do uruchamiania skryptu instalacji — `configure-http.ps1`
- Pobiera informacje o zestawie skalowania za pomocą [get-AzVmss](/powershell/module/az.compute/get-azvmss)
- Stosuje rozszerzenie do wystąpień maszyn wirtualnych za pomocą [update-AzVmss](/powershell/module/az.compute/update-azvmss)

Rozszerzenie DSC jest stosowane do wystąpień maszyn wirtualnych *myScaleSet* w grupie zasobów *o nazwie myResourceGroup.* Wprowadź własne nazwy w następujący sposób:

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

Jeśli zasady uaktualniania w zestawie skalowania są *ręczne,* zaktualizuj wystąpienia maszyn wirtualnych za pomocą [update-AzVmssInstance.](/powershell/module/az.compute/update-azvmssinstance) To polecenie cmdlet stosuje zaktualizowaną konfigurację zestawu skalowania do wystąpień maszyn wirtualnych i instaluje aplikację.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Instalowanie aplikacji na maszynie wirtualnej z systemem Linux przy użyciu pakietu cloud-init
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) to powszechnie używana metoda dostosowywania maszyny wirtualnej z systemem Linux podczas jej pierwszego rozruchu. Za pomocą pakietu cloud-init można instalować pakiety i zapisywać pliki lub konfigurować użytkowników i zabezpieczenia. Pakiet cloud-init jest uruchamiany w trakcie początkowego rozruchu, więc do zastosowania konfiguracji nie są wymagane żadne dodatkowe kroki ani agenci.

Pakiet cloud-init działa również w różnych dystrybucjach. Przykładowo nie używa się poleceń **apt-get install** lub **yum install** do zainstalowania pakietu. Zamiast tego możesz zdefiniować listę pakietów do zainstalowania. Pakiet cloud-init automatycznie używa natywnego narzędzia do zarządzania pakietami dla wybranej dystrybucji.

Aby uzyskać więcej informacji, w tym *przykładowycloud-init.txt,* zobacz Dostosowywanie maszyn wirtualnych platformy Azure za pomocą [pliku cloud-init.](../virtual-machines/linux/using-cloud-init.md)

Aby utworzyć zestaw skalowania i użyć pliku cloud-init, dodaj parametr do polecenia az vmss create i określ nazwę pliku `--custom-data` cloud-init. [](/cli/azure/vmss) Poniższy przykład tworzy zestaw skalowania o nazwie *myScaleSet* w *grupie myResourceGroup* i konfiguruje wystąpienia maszyn wirtualnych przy użyciu pliku *o nazwiecloud-init.txt*. Wprowadź własne nazwy w następujący sposób:

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


### <a name="install-applications-with-os-updates"></a>Instalowanie aplikacji za pomocą aktualizacji systemu operacyjnego
Gdy są dostępne nowe wersje systemu operacyjnego, można użyć lub skompilować nowy obraz niestandardowy i wdrożyć uaktualnienia systemu operacyjnego [do](virtual-machine-scale-sets-upgrade-scale-set.md) zestawu skalowania. Każde wystąpienie maszyny wirtualnej zostanie uaktualnione do najnowszego obrazu, który określisz. Możesz użyć obrazu niestandardowego ze wstępnie zainstalowaną aplikacją, rozszerzeniem niestandardowego skryptu lub rozszerzeniem DSC programu PowerShell, aby aplikacja została automatycznie dostępna podczas uaktualniania. Podczas wykonywania tego procesu może być konieczne zaplanowanie konserwacji aplikacji, aby upewnić się, że nie ma problemów ze zgodnością wersji.

Jeśli używasz niestandardowego obrazu maszyny wirtualnej ze wstępnie zainstalowaną aplikacją, możesz zintegrować aktualizacje aplikacji z potokiem wdrażania w celu skompilowania nowych obrazów i wdrożenia uaktualnień systemu operacyjnego w zestawie skalowania. Takie podejście umożliwia potokowi odebranie najnowszych kompilacji aplikacji, utworzenie i zweryfikowanie obrazu maszyny wirtualnej, a następnie uaktualnienie wystąpień maszyn wirtualnych w zestawie skalowania. Aby uruchomić potok wdrażania, który tworzy i wdraża aktualizacje aplikacji w niestandardowych obrazach maszyn wirtualnych, można utworzyć obraz programu [Packer](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)i wdrożyć go za pomocą programu Azure DevOps Services lub użyć innej platformy, takiej jak [Spinnaker](https://www.spinnaker.io/) lub [Jenkins.](https://jenkins.io/)


## <a name="next-steps"></a>Następne kroki
Podczas kompilowania i wdrażania aplikacji w zestawach skalowania można zapoznać się z tematem [Scale Set Design Overview (Omówienie projektu zestawu skalowania).](virtual-machine-scale-sets-design-overview.md) Aby uzyskać więcej informacji na temat zarządzania zestawem skalowania, zobacz Use PowerShell to manage your scale set (Zarządzanie [zestawem skalowania przy użyciu programu PowerShell).](./virtual-machine-scale-sets-manage-powershell.md)
