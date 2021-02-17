---
title: Jak korzystać z grup zarządzania — Zarządzanie platformą Azure
description: Dowiedz się, jak wyświetlać, obsługiwać, aktualizować i usuwać hierarchię grup zarządzania.
ms.date: 01/15/2021
ms.topic: conceptual
ms.openlocfilehash: 05e78d66c29e500842d14a6eeb563c4569ecf0bd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596485"
---
# <a name="manage-your-resources-with-management-groups"></a>Zarządzanie zasobami za pomocą grup zarządzania

Jeśli Twoja organizacja ma wiele subskrypcji, możesz potrzebować sposobu na wydajne zarządzanie dostępem, zasadami i zgodnością dla tych subskrypcji. Grupy zarządzania platformy Azure zapewniają poziom zakresu powyżej subskrypcji. Subskrypcje są organizowane w kontenerach nazywanych „grupami zarządzania”, do których należy zastosować swoje warunki nadzoru. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą warunki zastosowane do tej grupy zarządzania.

Grupy zarządzania umożliwiają zarządzanie klasy korporacyjnej na dużą skalę niezależnie od typu subskrypcji. Aby dowiedzieć się więcej na temat grup zarządzania, zobacz [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](./overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

> [!IMPORTANT]
> Azure Resource Manager tokenów użytkowników i pamięci podręcznej grup zarządzania trwa 30 minut, zanim zostanie wymuszone odświeżenie. Po wykonaniu akcji takich jak przeniesienie grupy zarządzania lub subskrypcji może potrwać do 30 minut. Aby wyświetlić aktualizacje wcześniej, musisz zaktualizować token przez odświeżenie przeglądarki, zalogowanie się i wyjście lub żądanie nowego tokenu.  

## <a name="change-the-name-of-a-management-group"></a>Zmień nazwę grupy zarządzania

Nazwę grupy zarządzania można zmienić przy użyciu portalu, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

### <a name="change-the-name-in-the-portal"></a>Zmień nazwę w portalu

1. Zaloguj się do [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **wszystkie**  >  **grupy zarządzania** usług.

1. Wybierz grupę zarządzania, której nazwę chcesz zmienić.

1. Wybierz pozycję **szczegóły**.

1. Wybierz opcję **Zmień nazwę grupy** w górnej części strony.

   :::image type="content" source="./media/detail_action_small.png" alt-text="Zrzut ekranu przedstawiający pasek akcji i przycisk &quot;Zmień grupę&quot; na stronie grupy zarządzania." border="false":::

1. Po otwarciu menu Wprowadź nową nazwę, którą chcesz wyświetlić.

   :::image type="content" source="./media/rename_context.png" alt-text="Zrzut ekranu okna nazwa grupy i opcje, aby zmienić nazwę grupy zarządzania." border="false":::

1. Wybierz pozycję **Zapisz**.

### <a name="change-the-name-in-powershell"></a>Zmiana nazwy w programie PowerShell

Aby zaktualizować nazwę wyświetlaną, użyj **Update-AzManagementGroup**. Aby na przykład zmienić nazwę wyświetlaną grup zarządzania z "contoso IT" na "Group contoso", uruchom następujące polecenie:

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Zmień nazwę w interfejsie wiersza polecenia platformy Azure

W przypadku interfejsu wiersza polecenia platformy Azure Użyj polecenie Update.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Usuwanie grupy zarządzania

Aby można było usunąć grupę zarządzania, muszą zostać spełnione następujące wymagania:

1. W grupie zarządzania nie ma podrzędnych grup ani subskrypcji zarządzania. Aby przenieść subskrypcję lub grupę zarządzania do innej grupy zarządzania, zobacz [przenoszenie grup zarządzania i subskrypcji w hierarchii](#moving-management-groups-and-subscriptions).

1. Potrzebujesz uprawnień do zapisu w grupie zarządzania ("właściciel", "Współautor" lub "Współautor grupy zarządzania"). Aby zobaczyć, jakie masz uprawnienia, wybierz grupę zarządzania, a następnie wybierz pozycję **IAM**. Aby dowiedzieć się więcej na temat ról platformy Azure, zobacz  
   [Kontrola dostępu oparta na rolach (Azure RBAC)](../../role-based-access-control/overview.md).

### <a name="delete-in-the-portal"></a>Usuwanie w portalu

1. Zaloguj się do [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **wszystkie**  >  **grupy zarządzania** usług.

1. Wybierz grupę zarządzania, którą chcesz usunąć.

1. Wybierz pozycję **szczegóły**.

1. Wybierz pozycję **Usuń**

   :::image type="content" source="./media/delete.png" alt-text="Zrzut ekranu strony grupy zarządzania z wyróżnionym przyciskiem &quot;Usuń&quot;." border="false":::

   > [!TIP]
   > Jeśli ikona jest wyłączona, umieszczenie selektora myszy na ikonie pokazuje przyczynę.

1. Istnieje okno z potwierdzeniem, że chcesz usunąć grupę zarządzania.

   :::image type="content" source="./media/delete_confirm.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe potwierdzenia grupy w celu usunięcia grupy zarządzania." border="false":::

1. Wybierz pozycję **Tak**.

### <a name="delete-in-powershell"></a>Usuwanie w programie PowerShell

Użyj polecenia **Remove-AzManagementGroup** w programie PowerShell, aby usunąć grupy zarządzania.

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Usuwanie w interfejsie wiersza polecenia platformy Azure

Korzystając z interfejsu wiersza polecenia platformy Azure, użyj polecenie AZ Account Management-Group Delete.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Wyświetlanie grup zarządzania

Możesz wyświetlić dowolną grupę zarządzania, dla której masz bezpośrednią lub dziedziczoną rolę platformy Azure.  

### <a name="view-in-the-portal"></a>Wyświetl w portalu

1. Zaloguj się do [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **wszystkie**  >  **grupy zarządzania** usług.

1. Zostanie załadowana strona hierarchii grupy zarządzania. Na tej stronie można eksplorować wszystkie grupy zarządzania i subskrypcje, do których masz dostęp. Wybranie nazwy grupy spowoduje przejście do niższego poziomu w hierarchii. Nawigacja działa tak samo, jak Eksplorator plików.

1. Aby wyświetlić szczegóły grupy zarządzania, wybierz łącze **(szczegóły)** obok tytułu grupy zarządzania. Jeśli ten link nie jest dostępny, nie masz uprawnień do wyświetlania tej grupy zarządzania.

   :::image type="content" source="./media/main.png" alt-text="Zrzut ekranu przedstawiający stronę grupy zarządzania z podrzędnymi grupami zarządzania i subskrypcjami." border="false":::

### <a name="view-in-powershell"></a>Wyświetl w programie PowerShell

Do pobrania wszystkich grup służy polecenie Get-AzManagementGroup. Zobacz [AZ. resources](/powershell/module/az.resources/Get-AzManagementGroup) , aby uzyskać pełną listę grup zarządzania, Pobierz polecenia programu PowerShell.  

```azurepowershell-interactive
Get-AzManagementGroup
```

Aby uzyskać informacje o pojedynczej grupie zarządzania, użyj parametru-GroupName

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

Aby zwrócić konkretną grupę zarządzania i wszystkie poziomy hierarchii w niej, użyj parametrów **-expand** i **-rekursywnie** .  

```azurepowershell-interactive
PS C:\> $response = Get-AzManagementGroup -GroupName TestGroupParent -Expand -Recurse
PS C:\> $response

Id                : /providers/Microsoft.Management/managementGroups/TestGroupParent
Type              : /providers/Microsoft.Management/managementGroups
Name              : TestGroupParent
TenantId          : 00000000-0000-0000-0000-000000000000
DisplayName       : TestGroupParent
UpdatedTime       : 2/1/2018 11:15:46 AM
UpdatedBy         : 00000000-0000-0000-0000-000000000000
ParentId          : /providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000
ParentName        : 00000000-0000-0000-0000-000000000000
ParentDisplayName : 00000000-0000-0000-0000-000000000000
Children          : {TestGroup1DisplayName, TestGroup2DisplayName}

PS C:\> $response.Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestGroup1
Name        : TestGroup1
DisplayName : TestGroup1DisplayName
Children    : {TestRecurseChild}

PS C:\> $response.Children[0].Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestRecurseChild
Name        : TestRecurseChild
DisplayName : TestRecurseChild
Children    :
```

### <a name="view-in-azure-cli"></a>Wyświetl w interfejsie wiersza polecenia platformy Azure

Do pobrania wszystkich grup służy polecenie list.  

```azurecli-interactive
az account management-group list
```

Aby uzyskać informacje o pojedynczej grupie zarządzania, użyj polecenia show

```azurecli-interactive
az account management-group show --name 'Contoso'
```

Aby zwrócić konkretną grupę zarządzania i wszystkie poziomy hierarchii w niej, użyj parametrów **-expand** i **-rekursywnie** .

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="moving-management-groups-and-subscriptions"></a>Przeniesienie grup zarządzania i subskrypcji   

Jednym z powodów tworzenia grupy zarządzania jest łączenie subskrypcji. Tylko grupy zarządzania i subskrypcje mogą być elementami podrzędnymi innej grupy zarządzania. Subskrypcja przenoszona do grupy zarządzania odziedziczy wszystkie uprawnienia dostępu użytkowników i zasad z nadrzędnej grupy zarządzania

Podczas przeniesienia grupy zarządzania lub subskrypcji jako elementu podrzędnego innej grupy zarządzania należy ocenić trzy reguły jako prawdziwe.

Jeśli wykonujesz akcję Przenieś, potrzebujesz: 

- Uprawnienia Zapis grup zarządzania i przypisywanie ról w podrzędnej subskrypcji lub grupie zarządzania.
  - Przykładowy **właściciel** roli wbudowanej
- Dostęp do zapisu grupy zarządzania w docelowej nadrzędnej grupie zarządzania.
  - Wbudowana rola — przykład: **właściciel**, **współautor**, **współautor grupy zarządzania**
- Dostęp do zapisu grupy zarządzania w istniejącej nadrzędnej grupie zarządzania.
  - Wbudowana rola — przykład: **właściciel**, **współautor**, **współautor grupy zarządzania**

**Wyjątek**: Jeśli obiekt docelowy lub istniejąca nadrzędna grupa zarządzania jest główną grupą zarządzania, wymagania dotyczące uprawnień nie są stosowane. Ponieważ główną grupą zarządzania jest domyślny punkt załadunkowy dla wszystkich nowych grup zarządzania i subskrypcji, nie musisz mieć uprawnień do przenoszenia elementu.

Jeśli rola właściciela w subskrypcji jest dziedziczona z bieżącej grupy zarządzania, cele przenoszenia są ograniczone. Subskrypcję można przenieść tylko do innej grupy zarządzania, w której masz rolę właściciela. Nie można przenieść subskrypcji do grupy zarządzania, w której jesteś współautorem, ponieważ utracisz własność subskrypcji. Jeśli masz bezpośrednio przypisaną rolę właściciela subskrypcji, możesz przenieść ją do dowolnej grupy zarządzania, w której jesteś współautorem.

Aby sprawdzić, jakie uprawnienia znajdują się w Azure Portal, wybierz grupę zarządzania, a następnie wybierz pozycję **IAM**. Aby dowiedzieć się więcej na temat ról platformy Azure, zobacz [Kontrola dostępu oparta na rolach (Azure RBAC)](../../role-based-access-control/overview.md).

## <a name="move-subscriptions"></a>Przenoszenie subskrypcji 

### <a name="add-an-existing-subscription-to-a-management-group-in-the-portal"></a>Dodawanie istniejącej subskrypcji do grupy zarządzania w portalu

1. Zaloguj się do [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **wszystkie**  >  **grupy zarządzania** usług.

1. Wybierz grupę zarządzania, która ma być elementem nadrzędnym.

1. W górnej części strony wybierz pozycję **Dodaj subskrypcję**.

1. Wybierz subskrypcję z listy z poprawnym IDENTYFIKATORem.

   :::image type="content" source="./media/add_context_sub.png" alt-text="Zrzut ekranu przedstawiający opcje &quot;Dodaj subskrypcję&quot; służące do wybierania istniejącej subskrypcji, która ma zostać dodana do grupy zarządzania." border="false":::

1. Wybierz pozycję "Zapisz".

### <a name="remove-a-subscription-from-a-management-group-in-the-portal"></a>Usuwanie subskrypcji z grupy zarządzania w portalu

1. Zaloguj się do [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **wszystkie**  >  **grupy zarządzania** usług.

1. Wybierz zaplanowaną grupę zarządzania, która jest bieżącym elementem nadrzędnym.  

1. Wybierz wielokropek na końcu wiersza dla subskrypcji na liście, która ma zostać przeniesiona.

   :::image type="content" source="./media/move_small.png" alt-text="Zrzut ekranu przedstawiający menu alternatywne dla subskrypcji, aby wybrać opcję &quot;Przenieś&quot;." border="false":::

1. Wybierz pozycję **Przenieś**.

1. W wyświetlonym menu wybierz **nadrzędną grupę zarządzania**.

   :::image type="content" source="./media/move_small_context.png" alt-text="Zrzut ekranu przedstawiający okno &quot;Przenieś&quot; i opcje przenoszenia subskrypcji do innej grupy zarządzania." border="false":::

1. Wybierz pozycję **Zapisz**.

### <a name="move-subscriptions-in-powershell"></a>Przenoszenie subskrypcji w programie PowerShell

Aby przenieść subskrypcję w programie PowerShell, użyj polecenia New-AzManagementGroupSubscription.  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Aby usunąć łącze między usługą a subskrypcją i grupą zarządzania, użyj polecenia Remove-AzManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Przenoszenie subskrypcji w interfejsie wiersza polecenia platformy Azure

Aby przenieść subskrypcję w interfejsie wiersza polecenia, należy użyć polecenie Dodaj.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Aby usunąć subskrypcję z grupy zarządzania, użyj polecenia Usuń subskrypcję.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-arm-template"></a>Przenieś subskrypcje w szablonie ARM

Aby przenieść subskrypcję w szablon Azure Resource Manager (szablon ARM), użyj poniższego szablonu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMgId": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the management group that you want to move the subscription to."
            }
        },
        "subscriptionId": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the existing subscription to move."
            }
        }
    },
    "resources": [
        {
            "scope": "/", 
            "type": "Microsoft.Management/managementGroups/subscriptions",
            "apiVersion": "2020-05-01",
            "name": "[concat(parameters('targetMgId'), '/', parameters('subscriptionId'))]",
            "properties": {
            }
        }
    ],
    "outputs": {}
}
```

## <a name="move-management-groups"></a>Przenoszenie grup zarządzania 

### <a name="move-management-groups-in-the-portal"></a>Przenoszenie grup zarządzania w portalu

1. Zaloguj się do [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **wszystkie**  >  **grupy zarządzania** usług.

1. Wybierz grupę zarządzania, która ma być elementem nadrzędnym.

1. W górnej części strony wybierz pozycję **Dodaj grupę zarządzania**.

1. W wyświetlonym menu wybierz, czy chcesz utworzyć nową, czy użyć istniejącej grupy zarządzania.

   - Wybranie opcji Nowy spowoduje utworzenie nowej grupy zarządzania.
   - Wybranie istniejącej opcji spowoduje wyświetlenie listy rozwijanej wszystkich grup zarządzania, które można przenieść do tej grupy zarządzania.  

   :::image type="content" source="./media/add_context_MG.png" alt-text="Zrzut ekranu przedstawiający opcje &quot;Dodaj grupę zarządzania&quot; służącą do tworzenia nowej grupy zarządzania." border="false":::

1. Wybierz pozycję **Zapisz**.

### <a name="move-management-groups-in-powershell"></a>Przenoszenie grup zarządzania w programie PowerShell

Użyj Update-AzManagementGroup polecenia w programie PowerShell, aby przenieść grupę zarządzania pod inną grupę.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName ContosoIT
Update-AzManagementGroup -GroupName 'Contoso' -ParentId $parentGroup.id
```  

### <a name="move-management-groups-in-azure-cli"></a>Przenoszenie grup zarządzania w interfejsie wiersza polecenia platformy Azure

Użyj polecenia Aktualizuj, aby przenieść grupę zarządzania przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent ContosoIT
```

## <a name="audit-management-groups-using-activity-logs"></a>Inspekcja grup zarządzania przy użyciu dzienników aktywności

Grupy zarządzania są obsługiwane w [dzienniku aktywności platformy Azure](../../azure-monitor/essentials/platform-logs-overview.md). Możesz badać wszystkie zdarzenia, które wystąpiły do grupy zarządzania w tej samej centralnej lokalizacji co inne zasoby platformy Azure. Na przykład widoczne są wszystkie przypisania ról i zmiany przypisań zasad w określonej grupie zarządzania.

:::image type="content" source="./media/al-mg.png" alt-text="Zrzut ekranu dzienników aktywności i operacji związanych z wybraną grupą zarządzania." border="false":::

Jeśli chcesz wykonać zapytanie dotyczące grup zarządzania spoza witryny Azure Portal, zakres docelowy grup zarządzania wygląda tak: **„/providers/Microsoft.Management/managementGroups/{identyfikator_grupy_zarządzania}”**.

## <a name="referencing-management-groups-from-other-resource-providers"></a>Odwoływanie się do grup zarządzania od innych dostawców zasobów

Podczas odwoływania się do grup zarządzania z innych akcji dostawcy zasobów należy użyć następującej ścieżki jako zakresu. Ta ścieżka jest używana podczas korzystania z programu PowerShell, interfejsu wiersza polecenia platformy Azure i interfejsów API REST.  

`/providers/Microsoft.Management/managementGroups/{yourMgID}`

Przykładem użycia tej ścieżki jest przypisanie nowego przypisania roli do grupy zarządzania w programie PowerShell:

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

Ta sama ścieżka zakresu jest używana podczas pobierania definicji zasad w grupie zarządzania.

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2019-09-01
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat grup zarządzania, zobacz:

- [Tworzenie grup zarządzania w celu organizowania zasobów platformy Azure](./create-management-group-portal.md)
- [Jak zmienić lub usunąć grupy zarządzania oraz zarządzać nimi](./manage.md)
- [Grupy zarządzania w module zasobów programu Azure PowerShell](/powershell/module/az.resources#resources)
- [Grupy zarządzania w interfejsie API REST](/rest/api/resources/managementgroups)
- [Grupy zarządzania w interfejsie wiersza polecenia platformy Azure](/cli/azure/account/management-group)