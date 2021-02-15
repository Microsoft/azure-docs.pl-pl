---
title: 'Szybki Start: Tworzenie szablonu Data Science VM-Menedżer zasobów'
titleSuffix: Azure Data Science Virtual Machine
description: W tym przewodniku szybki start użyjesz szablonu Azure Resource Manager, aby szybko wdrożyć Data Science Virtual Machine
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 06/10/2020
ms.service: data-science-vm
ms.topic: quickstart
ms.openlocfilehash: 7feacec9f0e78749f17359a9080411b6d9696136
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518219"
---
# <a name="quickstart-create-an-ubuntu-data-science-virtual-machine-using-an-arm-template"></a>Szybki Start: Tworzenie Ubuntu Data Science Virtual Machine przy użyciu szablonu ARM

Ten przewodnik Szybki Start przedstawia sposób tworzenia Ubuntu 18,04 Data Science Virtual Machine przy użyciu szablonu Azure Resource Manager (szablon ARM). Virtual Machines analizy danych są wstępnie załadowanymi maszynami wirtualnymi z zestawem narzędzi do nauki o danych i środowisk uczenia maszynowego. Po wdrożeniu w zasobach obliczeniowych opartych na procesorach GPU wszystkie narzędzia i biblioteki są skonfigurowane do korzystania z procesora GPU.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-ubuntu-DSVM-GPU-or-CPU%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/services/machine-learning/).

* Aby korzystać z poleceń interfejsu wiersza polecenia w tym dokumencie ze **środowiska lokalnego**, wymagany jest [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-vm-ubuntu-DSVM-GPU-or-CPU/).

:::code language="json" source="~/quickstart-templates/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json":::

Następujące zasoby są zdefiniowane w szablonie:

* [Microsoft. Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
* [Microsoft. Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
* [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft. Network/adresów publicipaddress](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft. COMPUTE/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): Tworzenie maszyny wirtualnej opartej na chmurze. W tym szablonie maszyna wirtualna jest skonfigurowana jako Data Science Virtual Machine z systemem Ubuntu 18,04.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Aby użyć szablonu w interfejsie wiersza polecenia platformy Azure, zaloguj się i wybierz swoją subskrypcję (zobacz [Logowanie za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli)). Następnie uruchom polecenie:

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

Po uruchomieniu powyższego polecenia wpisz:

1. Nazwa grupy zasobów, którą chcesz utworzyć, zawiera DSVM i skojarzone zasoby.
1. Lokalizacja platformy Azure, w której ma zostać wdrożone wdrożenie.
1. Typ uwierzytelniania, którego chcesz użyć (wprowadź ciąg `password` lub `sshPublicKey` ).
1. Nazwa logowania konta administratora (ta wartość może nie być taka `admin` ).
1. Wartość hasła lub klucza publicznego SSH dla konta.

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Aby wyświetlić Data Science Virtual Machine:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
1. Zaloguj się.
1. Wybierz utworzoną grupę zasobów.

Zobaczysz informacje o grupie zasobów:

:::image type="content" source="media/dsvm-tutorial-resource-manager/resource-group-home.png" alt-text="Zrzut ekranu przedstawiający podstawową grupę zasobów zawierającą DSVM":::

Kliknij zasób maszyny wirtualnej, aby przejść do strony z informacjami. Tutaj można znaleźć informacje na temat maszyny wirtualnej, w tym szczegóły połączenia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz używać tej maszyny wirtualnej, usuń ją. Ponieważ DSVM jest skojarzony z innymi zasobami, takimi jak konto magazynu, prawdopodobnie trzeba będzie usunąć całą utworzoną grupę zasobów. Aby usunąć grupę zasobów za pomocą portalu, kliknij przycisk **Usuń** i potwierdź. Można też usunąć grupę zasobów z interfejsu wiersza polecenia przy użyciu:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Data Science Virtual Machine z szablonu ARM.

> [!div class="nextstepaction"]
> [Przykładowe programy & podprzewodniki dotyczące ML](dsvm-samples-and-walkthroughs.md)
