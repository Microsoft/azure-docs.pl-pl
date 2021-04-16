---
title: 'Szybki start: tworzenie szablonu Data Science VM — Resource Manager szablonu'
titleSuffix: Azure Data Science Virtual Machine
description: W tym przewodniku Szybki start użyjemy szablonu Azure Resource Manager, aby szybko wdrożyć Data Science Virtual Machine
services: machine-learning
author: lobrien
ms.author: laobri
ms.date: 06/10/2020
ms.topic: quickstart
ms.service: data-science-vm
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 0683634223a63281ce2b42ebb02f87f9211a589e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530646"
---
# <a name="quickstart-create-an-ubuntu-data-science-virtual-machine-using-an-arm-template"></a>Szybki start: tworzenie aplikacji ubuntu Data Science Virtual Machine użyciu szablonu usługi ARM

W tym przewodniku Szybki start opisano, jak utworzyć szablon systemu Ubuntu 18.04 Data Science Virtual Machine szablonu Azure Resource Manager (arm). Data Science Virtual Machines to oparte na chmurze maszyny wirtualne wstępnie załadowane z pakietem platform i narzędzi do nauki o danych i uczenia maszynowego. Po wdrożeniu w zasobach obliczeniowych opartych na procesorze GPU wszystkie narzędzia i biblioteki są skonfigurowane do korzystania z procesora GPU.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-ubuntu-DSVM-GPU-or-CPU%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/services/machine-learning/).

* Aby używać poleceń interfejsu wiersza polecenia w tym dokumencie ze środowiska **lokalnego,** potrzebny jest interfejs wiersza [polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-vm-ubuntu-DSVM-GPU-or-CPU/).

:::code language="json" source="~/quickstart-templates/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json":::

W szablonie zdefiniowano następujące zasoby:

* [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
* [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Compute/virtualMachines:](/azure/templates/microsoft.compute/virtualmachines)utwórz maszynę wirtualną opartą na chmurze. W tym szablonie maszyna wirtualna jest skonfigurowana jako maszyna wirtualna z systemem Data Science Virtual Machine Ubuntu 18.04.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Aby użyć szablonu z interfejsu wiersza polecenia platformy Azure, zaloguj się i wybierz subskrypcję (zobacz Logowanie się za pomocą interfejsu [wiersza polecenia platformy Azure).](/cli/azure/authenticate-azure-cli) Następnie uruchom polecenie:

```azurecli-interactive
read -p "Enter the name of the resource group to create:" resourceGroupName &&
read -p "Enter the Azure location (e.g., centralus):" location &&
read -p "Enter the authentication type (must be 'password' or 'sshPublicKey') :" authenticationType &&
read -p "Enter the login name for the administrator account (may not be 'admin'):" adminUsername &&
read -p "Enter administrator account secure string (value of password or ssh public key):" adminPasswordOrKey &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters adminUsername=$adminUsername authenticationType=$authenticationType adminPasswordOrKey=$adminPasswordOrKey &&
echo "Press [ENTER] to continue ..." &&
read
```

Po uruchomieniu powyższego polecenia wprowadź:

1. Nazwa grupy zasobów, która ma być skojarzona z urządzeniem DSVM i skojarzonymi zasobami.
1. Lokalizacja platformy Azure, w której chcesz wykonać wdrożenie.
1. Typ uwierzytelniania, który chcesz użyć (wprowadź ciąg `password` lub `sshPublicKey` ).
1. Nazwa logowania konta administratora (ta wartość może nie być `admin` nazwą ).
1. Wartość hasła lub klucza publicznego SSH dla konta.

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

Aby wyświetlić Data Science Virtual Machine:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
1. Zaloguj się.
1. Wybierz właśnie utworzoną grupę zasobów.

Zobaczysz informacje o grupie zasobów:

:::image type="content" source="media/dsvm-tutorial-resource-manager/resource-group-home.png" alt-text="Zrzut ekranu przedstawiający podstawową grupę zasobów zawierającą urządzenie DSVM":::

Kliknij zasób Maszyna wirtualna, aby przejść do jego strony informacji. W tym miejscu można znaleźć informacje na temat maszyny wirtualnej, w tym szczegóły połączenia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz używać tej maszyny wirtualnej, usuń ją. Ponieważ urządzenie DSVM jest skojarzone z innymi zasobami, takimi jak konto magazynu, prawdopodobnie chcesz usunąć całą utworzoną grupę zasobów. Grupę zasobów można usunąć przy użyciu portalu, klikając przycisk **Usuń** i potwierdzając. Możesz też usunąć grupę zasobów z interfejsu wiersza polecenia za pomocą polecenia:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono Data Science Virtual Machine szablonu usługi ARM.

> [!div class="nextstepaction"]
> [Przykładowe programy & uczenia maszynowego](dsvm-samples-and-walkthroughs.md)
