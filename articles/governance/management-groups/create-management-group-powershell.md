---
title: 'Szybki start: tworzenie grupy zarządzania przy użyciu Azure PowerShell'
description: W tym przewodniku Szybki start utworzysz Azure PowerShell zarządzania w celu zorganizowania zasobów w hierarchię zasobów.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 0291bb2bfb439ad09531066f6bad4e20a3f4c6bd
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535980"
---
# <a name="quickstart-create-a-management-group-with-azure-powershell"></a>Szybki start: tworzenie grupy zarządzania przy użyciu Azure PowerShell

Grupy zarządzania to kontenery, które ułatwiają zarządzanie dostępem, zasadami i zgodnością w wielu subskrypcjach. Utwórz te kontenery, aby utworzyć efektywną i wydajną hierarchię, która może być używana z usługami [Azure Policy](../policy/overview.md) i kontrolą dostępu opartą [na rolach platformy Azure.](../../role-based-access-control/overview.md) Aby uzyskać więcej informacji na temat grup zarządzania, zobacz [Organize your resources with Azure management groups (Organizowanie zasobów przy użyciu grup zarządzania platformy Azure).](overview.md)

Ukończenie pierwszej grupy zarządzania utworzonej w katalogu może potrwać do 15 minut. Istnieją procesy uruchamiane po raz pierwszy w celu skonfigurowania usługi grup zarządzania na platformie Azure dla katalogu. Po zakończeniu procesu otrzymasz powiadomienie. Aby uzyskać więcej informacji, zobacz [początkową konfigurację grup zarządzania.](./overview.md#initial-setup-of-management-groups)

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

- Przed rozpoczęciem upewnij się, że masz zainstalowaną najnowszą wersję programu Azure PowerShell. Zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps), aby uzyskać szczegółowe informacje.

- Każdy użytkownik usługi Azure AD w dzierżawie może utworzyć grupę zarządzania bez przypisanego do niego uprawnienia do zapisu grupy zarządzania, jeśli ochrona hierarchii [nie](./how-to/protect-resource-hierarchy.md#setting---require-authorization) jest włączona. Ta nowa grupa zarządzania staje się elementem [](./how-to/protect-resource-hierarchy.md#setting---default-management-group) podrzędnym głównej grupy zarządzania lub domyślnej grupy zarządzania, a twórca ma przypisanie roli "Właściciel". Usługa grupy zarządzania umożliwia tę możliwość, dzięki czemu przypisania ról nie są wymagane na poziomie głównym. Żaden użytkownik nie ma dostępu do głównej grupy zarządzania po jej utworzeniu. Aby uniknąć przeszkód w znalezieniu administratorów globalnych usługi Azure AD w celu rozpoczęcia korzystania z grup zarządzania, zezwalamy na tworzenie początkowych grup zarządzania na poziomie głównym.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-azure-powershell"></a>Tworzenie w Azure PowerShell

W przypadku programu PowerShell użyj polecenia cmdlet [New-AzManagementGroup,](/powershell/module/az.resources/new-azmanagementgroup) aby utworzyć nową grupę zarządzania. W tym przykładzie grupa zarządzania **GroupName** to _Contoso_.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** to tworzony unikatowy identyfikator. Ten identyfikator jest używany przez inne polecenia w celu odwołania się do tej grupy i nie można go później zmienić.

Jeśli chcesz, aby grupa zarządzania wyświetlała inną nazwę w Azure Portal, dodaj parametr **DisplayName.** Aby na przykład utworzyć grupę zarządzania o nazwie GroupName firmy Contoso i nazwie wyświetlanej "Grupa Contoso", użyj następującego polecenia cmdlet:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

W poprzednich przykładach nowa grupa zarządzania jest tworzona w ramach głównej grupy zarządzania. Aby określić inną grupę zarządzania jako nadrzędną, użyj **parametru ParentId.**

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć utworzoną powyżej grupę zarządzania, użyj polecenia cmdlet [Remove-AzManagementGroup:](/powershell/module/az.resources/remove-azmanagementgroup)

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono grupę zarządzania w celu zorganizowania hierarchii zasobów. Grupa zarządzania może zawierać subskrypcje lub inne grupy zarządzania.

Aby dowiedzieć się więcej na temat grup zarządzania i zarządzania hierarchią zasobów, przejdź do:

> [!div class="nextstepaction"]
> [Zarządzanie zasobami przy użyciu grup zarządzania](./manage.md)
