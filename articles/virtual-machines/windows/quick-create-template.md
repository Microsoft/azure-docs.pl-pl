---
title: 'Szybki Start: Tworzenie maszyny wirtualnej z systemem Windows przy użyciu szablonu Menedżer zasobów'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć maszynę wirtualną z systemem Windows przy użyciu szablonu Menedżer zasobów
author: cynthn
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/04/2020
ms.author: cynthn
ms.custom: subject-armqs
ms.openlocfilehash: 6ef929a2934d8480ce6d1eca8bb7ba3b70580110
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84551995"
---
# <a name="quickstart-create-a-windows-virtual-machine-using-a-resource-manager-template"></a>Szybki Start: Tworzenie maszyny wirtualnej z systemem Windows przy użyciu szablonu Menedżer zasobów

W tym przewodniku szybki start pokazano, jak za pomocą szablonu Menedżer zasobów wdrożyć maszynę wirtualną z systemem Windows na platformie Azure. 

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Brak.

## <a name="create-a-windows-virtual-machine"></a>Tworzenie maszyny wirtualnej z systemem Windows

### <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

:::code language="json" source="~/quickstart-templates/101-vm-simple-windows/azuredeploy.json" range="1-225" highlight="67-224":::


W szablonie zdefiniowano kilka zasobów:

- [**Microsoft. Network/virtualNetworks/podsieci**](/azure/templates/Microsoft.Network/virtualNetworks/subnets): Utwórz podsieć.
- [**Microsoft. Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts): Tworzenie konta magazynu.
- [**Microsoft. Network/adresów publicipaddress**](/azure/templates/Microsoft.Network/publicIPAddresses): Tworzenie publicznego adresu IP.
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/Microsoft.Network/networkSecurityGroups): Tworzenie sieciowej grupy zabezpieczeń.
- [**Microsoft. Network/virtualNetworks**](/azure/templates/Microsoft.Network/virtualNetworks): tworzenie sieci wirtualnej.
- [**Microsoft. Network/networkInterfaces**](/azure/templates/Microsoft.Network/networkInterfaces): Tworzenie karty sieciowej.
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/Microsoft.Compute/virtualMachines): Tworzenie maszyny wirtualnej.



### <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Ten szablon umożliwia utworzenie magazynu kluczy oraz wpisu tajnego.

    [![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-windows%2fazuredeploy.json)

1. Wybierz lub wprowadź następujące wartości. Użyj wartości domyślnych, jeśli są dostępne.

    - **Subskrypcja**: wybierz subskrypcję platformy Azure.
    - **Grupa zasobów**: wybierz istniejącą grupę zasobów z listy rozwijanej lub wybierz pozycję **Utwórz nową**, wprowadź unikatową nazwę grupy zasobów, a następnie kliknij przycisk **OK**.
    - **Lokalizacja**: wybierz lokalizację.  Na przykład **Środkowe stany USA**.
    - **Nazwa użytkownika administratora**: Podaj nazwę użytkownika, taką jak *azureuser*.
    - **Hasło administratora**: Podaj hasło do użycia dla konta administratora. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](faq.md#what-are-the-password-requirements-when-creating-a-vm).
    - **Prefiks etykiety DNS**: wprowadź unikatowy identyfikator, który ma być używany jako część etykiety DNS.
    - **Wersja systemu operacyjnego Windows**: Wybierz wersję systemu Windows, która ma być uruchamiana na maszynie wirtualnej.
    - **Rozmiar maszyny wirtualnej**: Wybierz [rozmiar](sizes.md) , który ma być używany przez maszynę wirtualną.
    - **Lokalizacja**: domyślnie jest to ta sama lokalizacja, w której znajduje się grupa zasobów, jeśli już istnieje.
1. Wybierz pozycję **Przegląd + utwórz**. Po zakończeniu walidacji wybierz pozycję **Utwórz** , aby utworzyć i wdrożyć maszynę wirtualną.


Szablon jest wdrażany za pomocą witryny Azure Portal. Oprócz Azure Portal można również użyć Azure PowerShell, interfejsu wiersza polecenia platformy Azure i API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Za pomocą Azure Portal można sprawdzić maszynę wirtualną i inne utworzone zasoby. Po zakończeniu wdrażania wybierz pozycję **Przejdź do grupy zasobów** , aby wyświetlić maszynę wirtualną i inne zasoby.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po usunięciu grupy zasobów, która usunie maszynę wirtualną i wszystkie zasoby w grupie zasobów, nie są już potrzebne. 

1. Wybierz **grupę zasobów**.
1. Na stronie grupy zasobów wybierz pozycję **Usuń**.
1. Po wyświetleniu monitu wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.


## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono prostą maszynę wirtualną przy użyciu szablonu Menedżer zasobów. Aby dowiedzieć się więcej o maszynach wirtualnych platformy Azure, przejdź do samouczka dla maszyn wirtualnych z systemem Linux.


> [!div class="nextstepaction"]
> [Samouczki dla maszyny wirtualnej platformy Azure z systemem Windows](./tutorial-manage-vm.md)
