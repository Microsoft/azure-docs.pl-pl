---
title: Samouczek — zarządzanie maszynami wirtualnymi za pomocą interfejsu wiersza polecenia
description: W ramach tego samouczka nauczysz się używać interfejsu wiersza polecenia platformy Azure do zarządzania maszynami wirtualnymi platformy Azure przez zastosowanie RBAC, zasad, blokad i tagów.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tfitzmac
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: tomfitz
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 890afadc82acc90ab0324058e07aa5c4d34d04e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87926165"
---
# <a name="tutorial-learn-about-linux-virtual-machine-management-with-azure-cli"></a>Samouczek: informacje o zarządzaniu maszynami wirtualnymi z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia platformy Azure i korzystać z niego lokalnie, ten samouczek będzie wymagać interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="understand-scope"></a>Objaśnienie zakresu

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

W tym samouczku zastosujesz wszystkie ustawienia zarządzania do grupy zasobów, aby móc łatwo usunąć te ustawienia po zakończeniu.

Utwórzmy tę grupę zasobów.

```azurecli-interactive
az group create --name myResourceGroup --location "East US"
```

Grupa zasobów jest obecnie pusta.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Upewnij się, że użytkownicy w organizacji mają odpowiedni poziom dostępu do tych zasobów. Nie udzielaj użytkownikom nieograniczonego dostępu, ale upewnij się, że mogą oni wykonywać swoją pracę. [Kontrola dostępu oparta na rolach (Azure RBAC)](../../role-based-access-control/overview.md) umożliwia zarządzanie użytkownikami, którzy mają uprawnienia do wykonywania określonych działań w zakresie.

Aby móc tworzyć i usuwać przypisania roli, użytkownicy muszą mieć dostęp `Microsoft.Authorization/roleAssignments/*`. Ten dostęp jest udzielany za pośrednictwem ról Właściciel lub Administrator dostępu użytkowników.

W przypadku zarządzania rozwiązaniami maszyn wirtualnych dostępne są 3 role specyficzne dla zasobów, które zapewniają najczęściej potrzebny dostęp:

* [Współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Współautor sieci](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Współautor konta magazynu](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Zamiast przypisywać role poszczególnym użytkownikom, często łatwiej jest użyć grupy usługi Azure Active Directory zawierającej użytkowników, którzy muszą wykonywać podobne działania. Następnie należy przypisać tę grupę do odpowiedniej roli. Na potrzeby tego artykułu użyj istniejącej grupy do zarządzania maszyną wirtualną lub użyj portalu do [utworzenia grupy usługi Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Po utworzeniu nowej grupy lub znalezieniu istniejącej grupy przypisz nową grupę usługi Azure Active Directory do roli współautora maszyny wirtualnej dla grupy zasobów za pomocą polecenia [az role assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create).

```azurecli-interactive
adgroupId=$(az ad group show --group <your-group-name> --query objectId --output tsv)

az role assignment create --assignee-object-id $adgroupId --role "Virtual Machine Contributor" --resource-group myResourceGroup
```

Jeśli zostanie zgłoszony błąd wskazujący, że **jednostka\<guid> nie istnieje w katalogu**, oznacza to, że nowa grupa nie została rozpropagowana w usłudze Azure Active Directory. Spróbuj ponownie uruchomić polecenie.

Zazwyczaj należy powtórzyć ten proces dla roli *Współautor sieci* i *Współautor konta magazynu*, aby upewnić się, że użytkownicy mogą zarządzać wdrożonymi zasobami. W tym artykule można pominąć te kroki.

## <a name="azure-policy"></a>Azure Policy

Usługa [Azure Policy](../../governance/policy/overview.md) ułatwia zapewnienie, że wszystkie zasoby w subskrypcji spełniają standardy firmy. Subskrypcja ma już kilka definicji zasad. Aby wyświetlić dostępne definicje zasad, użyj polecenia [az policy definition list](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-list):

```azurecli-interactive
az policy definition list --query "[].[displayName, policyType, name]" --output table
```

Zostaną wyświetlone istniejące definicje zasad. Typ zasad to **Wbudowane** albo **Niestandardowe**. Przejrzyj definicje i znajdź te opisujące warunek, który chcesz przypisać. W tym artykule przypiszesz zasady określające następujące działania:

* Ograniczanie lokalizacji dla wszystkich zasobów.
* Ograniczanie jednostek SKU dla maszyn wirtualnych.
* Przeprowadzanie inspekcji maszyn wirtualnych, które nie korzystają z dysków zarządzanych.

W poniższym przykładzie pobierane są 3 definicje zasad na podstawie nazwy wyświetlanej. Aby przypisać te definicje do grupy zasobów, użyj polecenia [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create). W przypadku niektórych zasad należy podać wartości parametrów, aby określić dozwolone wartości.

```azurecli-interactive
# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
locationDefinition=$(az policy definition list --query "[?displayName=='Allowed locations'].name | [0]" --output tsv)
skuDefinition=$(az policy definition list --query "[?displayName=='Allowed virtual machine SKUs'].name | [0]" --output tsv)
auditDefinition=$(az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks'].name | [0]" --output tsv)

# Assign policy for allowed locations
az policy assignment create --name "Set permitted locations" \
  --resource-group myResourceGroup \
  --policy $locationDefinition \
  --params '{ 
      "listOfAllowedLocations": {
        "value": [
          "eastus", 
          "eastus2"
        ]
      }
    }'

# Assign policy for allowed SKUs
az policy assignment create --name "Set permitted VM SKUs" \
  --resource-group myResourceGroup \
  --policy $skuDefinition \
  --params '{ 
      "listOfAllowedSKUs": {
        "value": [
          "Standard_DS1_v2", 
          "Standard_E2s_v2"
        ]
      }
    }'

# Assign policy for auditing unmanaged disks
az policy assignment create --name "Audit unmanaged disks" \
  --resource-group myResourceGroup \
  --policy $auditDefinition
```

W poprzednim przykładzie przyjęto, że znasz już parametry dla zasad. Aby wyświetlić parametry, użyj polecenia:

```azurecli-interactive
az policy definition show --name $locationDefinition --query parameters
```

## <a name="deploy-the-virtual-machine"></a>Wdrażanie maszyny wirtualnej

Role i zasady są przypisane, a zatem pora na wdrożenie rozwiązania. Rozmiar domyślny to Standard_DS1_v2, który jest jedną z dozwolonych jednostek SKU. To polecenie tworzy klucze SSH, jeśli nie ma ich w domyślnej lokalizacji.

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

Po zakończeniu wdrażania możesz zastosować do rozwiązania więcej ustawień zarządzania.

## <a name="lock-resources"></a>Blokowanie zasobów

[Blokady zasobów](../../azure-resource-manager/management/lock-resources.md) uniemożliwiają użytkownikom w organizacji przypadkowe usunięcie lub zmodyfikowanie krytycznych zasobów. W przeciwieństwie do kontroli dostępu opartej na rolach blokady zasobów stosują ograniczenie do wszystkich użytkowników i ról. Poziom blokady można ustawić na wartość *CanNotDelete* lub *ReadOnly*.

Aby móc tworzyć lub usuwać blokady zarządzania, musisz mieć dostęp do akcji `Microsoft.Authorization/locks/*`. Spośród wbudowanych ról tylko **Właściciel** i **Administrator dostępu użytkowników** mają dostęp do tych akcji.

Aby zablokować maszynę wirtualną i sieciową grupę zabezpieczeń, użyj polecenia [az lock create](/cli/azure/resource/lock?view=azure-cli-latest#az-resource-lock-create):

```azurecli-interactive
# Add CanNotDelete lock to the VM
az lock create --name LockVM \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVM \
  --resource-type Microsoft.Compute/virtualMachines

# Add CanNotDelete lock to the network security group
az lock create --name LockNSG \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVMNSG \
  --resource-type Microsoft.Network/networkSecurityGroups
```

Aby przetestować blokadę, spróbuj uruchomić następujące polecenie:

```azurecli-interactive 
az group delete --name myResourceGroup
```

Zostanie wyświetlony komunikat o błędzie informujący, że nie można zakończyć operacji usuwania z powodu blokady. Grupę zasobów można usunąć tylko po zdjęciu blokad. Ten krok przedstawiono w sekcji [Oczyszczanie zasobów](#clean-up-resources).

## <a name="tag-resources"></a>Tagowanie zasobów

[Znaczniki](../../azure-resource-manager/management/tag-resources.md) do zasobów platformy Azure są stosowane w celu logicznego organizowania ich według kategorii. Każdy tag składa się z nazwy i wartości. Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym.

[!INCLUDE [Resource Manager governance tags CLI](../../../includes/resource-manager-governance-tags-cli.md)]

Aby zastosować tagi do maszyny wirtualnej, użyj polecenia [az resource tag](/cli/azure/resource?view=azure-cli-latest#az-resource-list). Nie są zachowywane żadne istniejące tagi zasobu.

```azurecli-interactive
az resource tag -n myVM \
  -g myResourceGroup \
  --tags Dept=IT Environment=Test Project=Documentation \
  --resource-type "Microsoft.Compute/virtualMachines"
```

### <a name="find-resources-by-tag"></a>Znajdowanie zasobów według tagów

Aby znaleźć zasoby z wartością i nazwą tagu, użyj polecenia [az resource list](/cli/azure/resource?view=azure-cli-latest#az-resource-list):

```azurecli-interactive
az resource list --tag Environment=Test --query [].name
```

Zwróconych wartości można użyć na potrzeby zadań zarządzania, takich jak zatrzymanie wszystkich maszyn wirtualnych z daną wartością tagu.

```azurecli-interactive
az vm stop --ids $(az resource list --tag Environment=Test --query "[?type=='Microsoft.Compute/virtualMachines'].id" --output tsv)
```

### <a name="view-costs-by-tag-values"></a>Wyświetlanie kosztów według wartości tagów

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Zablokowanej sieciowej grupy zabezpieczeń nie można usunąć, dopóki blokada nie zostanie zdjęta. Aby zdjąć blokadę, pobierz identyfikatory blokad i podaj je w poleceniu [az lock delete](/cli/azure/resource/lock?view=azure-cli-latest#az-resource-lock-delete):

```azurecli-interactive
vmlock=$(az lock show --name LockVM \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Compute/virtualMachines \
  --resource-name myVM --output tsv --query id)
nsglock=$(az lock show --name LockNSG \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Network/networkSecurityGroups \
  --resource-name myVMNSG --output tsv --query id)
az lock delete --ids $vmlock $nsglock
```

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [AZ Group Delete](/cli/azure/group?view=azure-cli-latest#az-group-delete) . Zakończ sesję SSH i wróć do maszyny wirtualnej, a następnie usuń zasoby w następujący sposób:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony obraz niestandardowy maszyny wirtualnej. W tym samouczku omówiono:

> [!div class="checklist"]
> * Przypisywanie użytkowników do roli
> * Stosowanie zasad wymuszających standardy
> * Ochrona krytycznych zasobów za pomocą blokad
> * Tagowanie zasobów na potrzeby rozliczeń i zarządzania

Przejdź do następnego samouczka, aby dowiedzieć się, jak identyfikować zmiany i zarządzać aktualizacjami pakietów na maszynie wirtualnej.

> [!div class="nextstepaction"]
> [Zarządzanie maszynami wirtualnymi](tutorial-config-management.md)
