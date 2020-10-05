---
title: 'Szybki Start: Tworzenie maszyny wirtualnej Ubuntu Linux przy użyciu szablonu Menedżer zasobów'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć maszynę wirtualną z systemem Linux przy użyciu szablonu Menedżer zasobów
author: cynthn
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/04/2020
ms.author: cynthn
ms.custom: subject-armqs
ms.openlocfilehash: bd22d20703dc72e220f9b479b4a4005033f964ae
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88649775"
---
# <a name="quickstart-create-an-ubuntu-linux-virtual-machine-using-an-arm-template"></a>Szybki Start: Tworzenie maszyny wirtualnej Ubuntu Linux przy użyciu szablonu ARM

W tym przewodniku szybki start pokazano, jak wdrożyć Ubuntu Linux maszynę wirtualną na platformie Azure przy użyciu szablonu Azure Resource Manager (szablon ARM).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-linux%2fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/).

:::code language="json" source="~/quickstart-templates/101-vm-simple-linux/azuredeploy.json":::


W szablonie zdefiniowano kilka zasobów:

- [**Microsoft. Network/virtualNetworks/podsieci**](/azure/templates/Microsoft.Network/virtualNetworks/subnets): Utwórz podsieć.
- [**Microsoft. Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts): Tworzenie konta magazynu.
- [**Microsoft. Network/networkInterfaces**](/azure/templates/Microsoft.Network/networkInterfaces): Tworzenie karty sieciowej.
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/Microsoft.Network/networkSecurityGroups): Tworzenie sieciowej grupy zabezpieczeń.
- [**Microsoft. Network/virtualNetworks**](/azure/templates/Microsoft.Network/virtualNetworks): tworzenie sieci wirtualnej.
- [**Microsoft. Network/adresów publicipaddress**](/azure/templates/Microsoft.Network/publicIPAddresses): Tworzenie publicznego adresu IP.
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/Microsoft.Compute/virtualMachines): Tworzenie maszyny wirtualnej.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Ten szablon umożliwia utworzenie magazynu kluczy oraz wpisu tajnego.

    [![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-linux%2fazuredeploy.json)

1. Wybierz lub wprowadź następujące wartości. Użyj wartości domyślnych, jeśli są dostępne.

    - **Subskrypcja**: wybierz subskrypcję platformy Azure.
    - **Grupa zasobów**: wybierz istniejącą grupę zasobów z listy rozwijanej lub wybierz pozycję **Utwórz nową**, wprowadź unikatową nazwę grupy zasobów, a następnie kliknij przycisk **OK**.
    - **Lokalizacja**: wybierz lokalizację.  Na przykład **Środkowe stany USA**.
    - **Nazwa użytkownika administratora**: Podaj nazwę użytkownika, taką jak *azureuser*.
    - **Typ uwierzytelniania**: można wybrać opcję użycia klucza SSH lub hasła.
    - **Hasło administratora lub klucz** w zależności od wybranego typu uwierzytelniania:
        - W przypadku wybrania **hasła**hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](faq.md#what-are-the-password-requirements-when-creating-a-vm).
        - Jeśli wybierzesz pozycję **sshPublicKey**, wklej zawartość klucza publicznego.
    - **Prefiks etykiety DNS**: wprowadź unikatowy identyfikator, który ma być używany jako część etykiety DNS.
    - **Ubuntu wersja systemu operacyjnego**: Wybierz wersję Ubuntu, która ma być uruchamiana na maszynie wirtualnej.
    - **Lokalizacja**: domyślnie jest to ta sama lokalizacja, w której znajduje się grupa zasobów, jeśli już istnieje.
    - **Rozmiar maszyny wirtualnej**: Wybierz [rozmiar](../sizes.md) , który ma być używany przez maszynę wirtualną.
    - **Nazwa Virtual Network**: nazwa, która ma być używana dla sieci wirtualnej.
    - **Nazwa podsieci**: Nazwa podsieci, która powinna być używana przez maszynę wirtualną.
    - **Nazwa grupy zabezpieczeń sieci**: nazwa dla sieciowej grupy zabezpieczeń.
1. Wybierz pozycję **Przeglądanie + tworzenie**. Po zakończeniu walidacji wybierz pozycję **Utwórz** , aby utworzyć i wdrożyć maszynę wirtualną.


Szablon jest wdrażany za pomocą witryny Azure Portal. Oprócz Azure Portal można również użyć interfejsu wiersza polecenia platformy Azure, Azure PowerShell i API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Za pomocą Azure Portal można sprawdzić maszynę wirtualną i inne utworzone zasoby. Po zakończeniu wdrażania wybierz pozycję **Przejdź do grupy zasobów** , aby wyświetlić maszynę wirtualną i inne zasoby.


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po usunięciu grupy zasobów, która usunie maszynę wirtualną i wszystkie zasoby w grupie zasobów, nie są już potrzebne. 

1. Wybierz **grupę zasobów**.
1. Na stronie grupy zasobów wybierz pozycję **Usuń**.
1. Po wyświetleniu monitu wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.


## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono prostą maszynę wirtualną przy użyciu szablonu ARM. Aby dowiedzieć się więcej o maszynach wirtualnych platformy Azure, przejdź do samouczka dla maszyn wirtualnych z systemem Linux.


> [!div class="nextstepaction"]
> [Samouczki dla maszyny wirtualnej platformy Azure z systemem Linux](./tutorial-manage-vm.md)