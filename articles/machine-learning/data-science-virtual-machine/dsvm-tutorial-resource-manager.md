---
title: Samouczek — Tworzenie szablonu Menedżer zasobów Data Science VM
titleSuffix: Azure Data Science Virtual Machine
description: W tym samouczku użyjesz szablonu Azure Resource Manager, aby szybko wdrożyć Data Science Virtual Machine
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 06/10/2020
ms.service: machine-learning
ms.subservice: data-science-vm
ms.topic: tutorial
ms.openlocfilehash: a0bce8a3de7832b589646668b877ce8396d16bc8
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84562050"
---
# <a name="tutorial-create-an-ubuntu-data-science-virtual-machine-using-a-resource-manager-template"></a>Samouczek: Tworzenie Ubuntu Data Science Virtual Machine przy użyciu szablonu Menedżer zasobów
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym samouczku przedstawiono sposób tworzenia Ubuntu 18,04 Data Science Virtual Machine przy użyciu szablonu Azure Resource Manager. Virtual Machines analizy danych są wstępnie załadowanymi maszynami wirtualnymi z zestawem narzędzi do nauki o danych i środowisk uczenia maszynowego. Po wdrożeniu w zasobach obliczeniowych opartych na procesorach GPU wszystkie narzędzia i biblioteki są skonfigurowane do korzystania z procesora GPU. 

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://aka.ms/AMLFree)

* Aby korzystać z poleceń interfejsu wiersza polecenia w tym dokumencie ze **środowiska lokalnego**, wymagany jest [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

### <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-vm-ubuntu-DSVM-GPU-or-CPU/). Pełny szablon tego artykułu jest zbyt długi, aby można go było wyświetlić w tym miejscu. Aby wyświetlić cały szablon, zobacz [azuredeploy. JSON](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json). Część definiująca szczegóły DSVM jest pokazana tutaj:

:::code language="json" source="~/quickstart-templates/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" range="235-276":::

Następujące zasoby są zdefiniowane w szablonie:

* [Microsoft. COMPUTE/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): Tworzenie maszyny wirtualnej opartej na chmurze. W tym szablonie maszyna wirtualna jest skonfigurowana jako Data Science Virtual Machine z systemem Ubuntu 18,04.

### <a name="deploy-the-template"></a>Wdrożenie szablonu 

Aby użyć szablonu w interfejsie wiersza polecenia platformy Azure, zaloguj się i wybierz swoją subskrypcję (zobacz [Logowanie za pomocą interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)). Następnie uruchom polecenie:

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
1. Lokalizacja platformy Azure, w której ma zostać wdrożone wdrożenie
1. Typ uwierzytelniania, którego chcesz użyć (wprowadź ciąg `password` lub `sshPublicKey` )
1. Nazwa logowania konta administratora (ta wartość nie może być równa `admin` )
1. Wartość hasła lub klucza publicznego SSH dla konta

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Aby wyświetlić Data Science Virtual Machine:

1. Przejdź do strony https://portal.azure.com 
1. Zaloguj 
1. Wybierz grupę zasobów, która została właśnie utworzona

Zobaczysz informacje o grupie zasobów: 

:::image type="content" source="media/dsvm-tutorial-resource-manager/resource-group-home.png" alt-text="Zrzut ekranu przedstawiający podstawową grupę zasobów zawierającą DSVM":::

Kliknij zasób maszyny wirtualnej, aby przejść do strony z informacjami. Tutaj można znaleźć informacje na temat maszyny wirtualnej, w tym szczegóły połączenia. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz używać tej maszyny wirtualnej, usuń ją. Ponieważ DSVM jest skojarzony z innymi zasobami, takimi jak konto magazynu, prawdopodobnie trzeba będzie usunąć całą utworzoną grupę zasobów. Aby usunąć grupę zasobów za pomocą portalu, kliknij przycisk "Usuń" i potwierdź. Można też usunąć grupę zasobów z interfejsu wiersza polecenia przy użyciu: 

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzysz Data Science Virtual Machine z szablonu Azure Resource Manager. 

> [!div class="nextstepaction"]
> [Przykładowe programy & podprzewodniki dotyczące ML](dsvm-samples-and-walkthroughs.md)
