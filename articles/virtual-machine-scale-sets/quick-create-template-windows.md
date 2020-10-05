---
title: Szybki Start — Tworzenie zestawu skalowania maszyn wirtualnych z systemem Windows przy użyciu szablonu platformy Azure
description: Dowiedz się, jak szybko utworzyć skalę maszyny wirtualnej z systemem Windows za pomocą szablonu usługi Azure Resource Manager, który wdraża przykładową aplikację i konfiguruje reguły automatycznego skalowania
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.subservice: windows
ms.date: 03/27/2020
ms.reviewer: mimckitt
ms.custom: mimckitt, subject-armqs
ms.openlocfilehash: 03863171cb05c0557611fb8cd3c0ade667e7d79e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88650081"
---
# <a name="quickstart-create-a-windows-virtual-machine-scale-set-with-an-arm-template"></a>Szybki Start: Tworzenie zestawu skalowania maszyn wirtualnych z systemem Windows przy użyciu szablonu ARM

Zestaw skalowania maszyn wirtualnych umożliwia wdrożenie zestawu automatycznego skalowania maszyn wirtualnych i zarządzanie nim. Maszyny wirtualne w zestawie skalowania możesz skalować ręcznie lub możesz zdefiniować reguły skalowania automatycznego na podstawie użycia takich zasobów jak procesor CPU, zapotrzebowanie na pamięć lub ruch sieciowy. Moduł równoważenia obciążenia platformy Azure następnie dystrybuuje ruch do wystąpień maszyn wirtualnych w zestawie skalowania. W tym przewodniku szybki start utworzysz zestaw skalowania maszyn wirtualnych i wdrożono przykładową aplikację z szablonem Azure Resource Manager (szablon ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Szablony ARM umożliwiają wdrażanie grup powiązanych zasobów. W jednym szablonie można utworzyć zestaw skalowania maszyn wirtualnych, zainstalować aplikacje i skonfigurować reguły automatycznego skalowania. Korzystając ze zmiennych i parametrów, można ponownie użyć tego szablonu, aby zaktualizować istniejące zestawy skalowania lub utworzyć dodatkowe. Szablony można wdrażać za pośrednictwem Azure Portal, interfejsu wiersza polecenia platformy Azure, Azure PowerShell lub z potoków ciągłej integracji/ciągłego dostarczania (CI/CD).

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/201-vmss-windows-webapp-dsc-autoscale/).

:::code language="json" source="~/quickstart-templates/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json":::

Te zasoby są zdefiniowane w tych szablonach:

- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Network/adresów publicipaddress**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Compute/virtualMachineScaleSets**](/azure/templates/microsoft.compute/virtualmachinescalesets)
- [**Microsoft. Insights/autoscaleSettings**](/azure/templates/microsoft.insights/autoscalesettings)

### <a name="define-a-scale-set"></a>Definiowanie zestawu skalowania

Aby utworzyć skalę przy użyciu szablonu, należy zdefiniować odpowiednie zasoby. Podstawowe elementy typu zasobu zestawu skalowania maszyn wirtualnych są następujące:

| Właściwość                     | Opis właściwości                                  | Przykładowa wartość szablonu                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| typ                         | Typ zasobu platformy Azure do utworzenia                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | Nazwa zestawu skalowania                                       | myScaleSet                                |
| location                     | Lokalizacja utworzenia zestawu skalowania                     | East US                                   |
| sku.name                     | Rozmiar maszyny wirtualnej dla każdego wystąpienia zestawu skalowania                  | Standardowa_A1                               |
| sku.capacity                 | Liczba wystąpień maszyn wirtualnych do początkowego utworzenia           | 2                                         |
| upgradePolicy.mode           | Tryb uaktualniania wystąpienia maszyny wirtualnej w przypadku wprowadzenia zmian              | Automatyczny                                 |
| imageReference               | Platforma lub obraz niestandardowy do użycia na potrzeby wystąpień maszyn wirtualnych | Microsoft Windows Server 2016 Datacenter  |
| osProfile.computerNamePrefix | Prefiks nazwy dla każdego wystąpienia maszyny wirtualnej                     | myvmss                                    |
| osProfile.adminUsername      | Nazwa użytkownika dla każdego wystąpienia maszyny wirtualnej                        | użytkownik_azure                                 |
| osProfile.adminPassword      | Hasło dla każdego wystąpienia maszyny wirtualnej                        | P@ssw0rd!                                 |

Aby dostosować szablon zestawu skalowania, można zmienić rozmiar maszyny wirtualnej lub pojemność początkową. Innym rozwiązaniem jest użycie innej platformy lub obrazu niestandardowego.

### <a name="add-a-sample-application"></a>Dodawanie przykładowej aplikacji

Aby przetestować zestaw skalowania, należy zainstalować podstawową aplikację internetową. Gdy wdrażasz zestaw skalowania, rozszerzenia maszyn wirtualnych mogą dostarczać konfigurację po wdrożeniu oraz zadania automatyzacji, takie jak instalowanie aplikacji. Skrypty można pobrać z usługi Azure Storage lub GitHub bądź można je dostarczyć do witryny Azure Portal w czasie wykonywania rozszerzenia. Aby zastosować rozszerzenie do zestawu skalowania, dodaj sekcję *extensionProfile* do przedstawionego wcześniej przykładu zasobów. Profil rozszerzenia zwykle definiuje następujące właściwości:

- Typ rozszerzenia
- Wydawca rozszerzenia
- Wersja rozszerzenia
- Lokalizacja konfiguracji lub skryptów instalacji
- Polecenia do wykonania na wystąpieniach maszyn wirtualnych

Szablon używa rozszerzenia DSC programu PowerShell, aby zainstalować aplikację ASP.NET MVC działającą w usługach IIS.

Skrypt instalacji jest pobierany z usługi GitHub, jak zdefiniowano we właściwości *url*. Następnie rozszerzenie uruchamia polecenie *InstallIIS* ze skryptu *IISInstall.ps1*, jak zdefiniowano we właściwościach *function* i *Script*. Sama aplikacja ASP.NET jest dostarczana jako pakiet Web Deploy, który także jest pobierany z usługi GitHub, jak zdefiniowano we właściwości *WebDeployPackagePath*:

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Szablon można wdrożyć, wybierając przycisk **Wdróż na platformie Azure** . Ten przycisk otwiera witrynę Azure Portal, ładuje pełny szablon i wyświetla monit o podanie kilku parametrów, takich jak nazwa zestawu skalowania, liczba wystąpień i poświadczenia administratora.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

Szablon Menedżer zasobów można również wdrożyć przy użyciu Azure PowerShell:

```azurepowershell-interactive
# Create a resource group
New-AzResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateURI https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json

# Update the scale set and apply the extension
Update-AzVmss `
    -ResourceGroupName myResourceGroup `
    -VmScaleSetName myVMSS `
    -VirtualMachineScaleSet $vmssConfig
```

Wprowadź dane w monitach, aby podać nazwę zestawu skalowania i poświadczenia administratora dla wystąpień maszyn wirtualnych. Utworzenie zestawu skalowania i zastosowanie rozszerzenia w celu skonfigurowania aplikacji może potrwać 10 – 15 minut.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Aby zapoznać się z działaniem zestawu skalowania, uzyskaj dostęp do przykładowej aplikacji internetowej w przeglądarce internetowej. Uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) w następujący sposób:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Wprowadź publiczny adres IP modułu równoważenia obciążenia w przeglądarce internetowej w formacie *http: \/ /publicIpAddress/MyApp*. Moduł równoważenia obciążenia kieruje ruch do jednego z wystąpień maszyn wirtualnych, jak pokazano w poniższym przykładzie:

![Uruchamianie witryny usług IIS](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i zestaw skalowania nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Parametr `-Force` potwierdza, że chcesz usunąć zasoby bez wyświetlania dodatkowego monitu. Parametr `-AsJob` zwraca kontrolę do wiersza polecenia bez oczekiwania na zakończenie operacji.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono zestaw skalowania systemu Windows przy użyciu szablonu ARM i użyto rozszerzenia DSC programu PowerShell w celu zainstalowania podstawowej aplikacji ASP.NET na wystąpieniach maszyn wirtualnych. Aby dowiedzieć się więcej, przejdź do samouczka dotyczącego sposobu tworzenia zestawów skalowania maszyn wirtualnych platformy Azure i zarządzania nimi.

> [!div class="nextstepaction"]
> [Tworzenie zestawów skalowania maszyn wirtualnych platformy Azure i zarządzanie nimi](tutorial-create-and-manage-powershell.md)
