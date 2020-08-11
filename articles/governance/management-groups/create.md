---
title: Tworzenie grup zarządzania w celu organizowania zasobów — zarządzanie platformą Azure
description: Dowiedz się, jak utworzyć grupy zarządzania platformy Azure, aby zarządzać wieloma zasobami przy użyciu portalu, Azure PowerShell i interfejsu wiersza polecenia platformy Azure.
ms.date: 08/10/2020
ms.topic: conceptual
ms.openlocfilehash: 0739eafdf4cf41195b189a8496c331ab470bbec0
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056639"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Tworzenie grup zarządzania na potrzeby organizowania zasobów i zarządzania nimi

Grupy zarządzania to kontenery ułatwiające zarządzanie dostępem, zasadami i zgodnością w wielu subskrypcjach. Utwórz te kontenery, aby utworzyć efektywną i wydajną hierarchię, która może być używana z [Azure Policy](../policy/overview.md) i [kontroli dostępu opartej na rolach platformy Azure](../../role-based-access-control/overview.md). Aby uzyskać więcej informacji na temat grup zarządzania, zobacz [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](overview.md).

Wykonanie pierwszej grupy zarządzania utworzonej w katalogu może potrwać do 15 minut. Istnieją procesy, które są uruchamiane po raz pierwszy w celu skonfigurowania usługi grup zarządzania na platformie Azure dla katalogu. Po zakończeniu procesu otrzymasz powiadomienie. Aby uzyskać więcej informacji, zobacz [początkowa konfiguracja grup zarządzania](./overview.md#initial-setup-of-management-groups).

## <a name="create-a-management-group"></a>Tworzenie grupy zarządzania

Każdy użytkownik usługi Azure AD w dzierżawie może utworzyć grupę zarządzania bez uprawnienia do zapisu grupy zarządzania przypisanej do tego użytkownika. Ta nowa grupa zarządzania będzie elementem podrzędnym głównej grupy zarządzania, a twórca otrzyma przypisanie roli "właściciel". Usługa Grupa zarządzania umożliwia taką możliwość, aby przypisania ról nie były potrzebne na poziomie głównym. Brak dostępu użytkowników do głównej grupy zarządzania podczas jej tworzenia. Aby uniknąć progu wyszukiwania administratorów globalnych usługi Azure AD do rozpoczęcia korzystania z grup zarządzania, zezwalamy na tworzenie początkowych grup zarządzania w katalogu głównym  
poziomie.

Grupę zarządzania można utworzyć przy użyciu portalu, [szablonu Azure Resource Manager](../../azure-resource-manager/templates/deploy-to-tenant.md#create-management-group), programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

### <a name="create-in-portal"></a>Utwórz w portalu

1. Zaloguj się do [Azure Portal](https://portal.azure.com).

1. Wybierz kolejno pozycje **wszystkie usługi**  >  **Zarządzanie i nadzór**.

1. Wybierz **grupy zarządzania**.

1. Wybierz pozycję **+ Dodaj grupę zarządzania**.

   :::image type="content" source="./media/main.png" alt-text="Strona do pracy z grupami zarządzania" border="false":::

1. Wypełnij pole Identyfikator grupy zarządzania.

   - **Identyfikator grupy zarządzania** jest unikatowym identyfikatorem katalogu, który jest używany do przesyłania poleceń z tej grupy zarządzania. Tego identyfikatora nie można edytować po utworzeniu, ponieważ jest on używany w całym systemie Azure do identyfikowania tej grupy. [Główna Grupa zarządzania](overview.md#root-management-group-for-each-directory) jest automatycznie tworzona z identyfikatorem, który jest identyfikatorem Azure Active Directory. W przypadku wszystkich innych grup zarządzania przypisz unikatowy identyfikator.
   - Pole Nazwa wyświetlana to nazwa wyświetlana w Azure Portal. Oddzielna nazwa wyświetlana jest polem opcjonalnym podczas tworzenia grupy zarządzania i można ją zmienić w dowolnym  
     pierwszym.

   :::image type="content" source="./media/create_context_menu.png" alt-text="Okienko opcji do tworzenia nowej grupy zarządzania" border="false":::

1. Wybierz pozycję **Zapisz**.

### <a name="create-in-powershell"></a>Tworzenie w programie PowerShell

W przypadku programu PowerShell Użyj polecenia cmdlet [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) , aby utworzyć nową grupę zarządzania.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**Grupaname** jest tworzonym unikatowym identyfikatorem. Ten identyfikator jest używany przez inne polecenia do odwoływania się do tej grupy i nie można go zmienić później.

Jeśli chcesz, aby grupa zarządzania pokazywała inną nazwę w Azure Portal, Dodaj parametr **DisplayName** . Aby na przykład utworzyć grupę zarządzania z grupą Contoso i nazwę wyświetlaną "Grupa contoso", użyj następującego polecenia cmdlet:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

W powyższych przykładach Nowa grupa zarządzania jest tworzona w ramach głównej grupy zarządzania. Aby określić inną grupę zarządzania jako nadrzędną, użyj parametru **parentID** .

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Tworzenie w interfejsie wiersza polecenia platformy Azure

Aby utworzyć nową grupę zarządzania, użyj polecenia [AZ Account Management-Group Create](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) w interfejsie Azure.

```azurecli-interactive
az account management-group create --name Contoso
```

**Nazwa** jest tworzonym unikatowym identyfikatorem. Ten identyfikator jest używany przez inne polecenia do odwoływania się do tej grupy i nie można go zmienić później.

Jeśli chcesz, aby grupa zarządzania pokazywała inną nazwę w Azure Portal, Dodaj parametr **Display-Name** . Aby na przykład utworzyć grupę zarządzania z grupą Contoso i nazwę wyświetlaną "Grupa contoso", użyj następującego polecenia:

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

W powyższych przykładach Nowa grupa zarządzania jest tworzona w ramach głównej grupy zarządzania. Aby określić inną grupę zarządzania jako nadrzędną, należy użyć parametru **nadrzędnego** i podać nazwę grupy nadrzędnej.

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat grup zarządzania, zobacz:

- [Tworzenie grup zarządzania w celu organizowania zasobów platformy Azure](./create.md)
- [Jak zmienić lub usunąć grupy zarządzania oraz zarządzać nimi](./manage.md)
- [Grupy zarządzania w module zasobów programu Azure PowerShell](/powershell/module/az.resources#resources)
- [Grupy zarządzania w interfejsie API REST](/rest/api/resources/managementgroups)
- [Grupy zarządzania w interfejsie wiersza polecenia platformy Azure](/cli/azure/account/management-group)
