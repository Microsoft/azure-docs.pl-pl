---
title: Zablokuj zasoby, aby uniemożliwić zmiany
description: Zablokuj użytkownikom możliwość aktualizowania lub usuwania krytycznych zasobów platformy Azure, stosując blokadę dla wszystkich użytkowników i ról.
ms.topic: conceptual
ms.date: 10/20/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3830c7e78cf3cc607c7abfca63e6ae74f89b7aff
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92281742"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Blokowanie zasobów w celu uniemożliwienia nieoczekiwanych zmian

Jako administrator możesz zablokować subskrypcję, grupę zasobów lub zasób, aby zapobiec przypadkowemu usunięciu lub zmodyfikowaniu kluczowych zasobów przez innych użytkowników w organizacji. Poziom blokady można ustawić na wartość **CanNotDelete** lub **ReadOnly**. W portalu blokady są nazywane odpowiednio **usuwaniem** i **tylko do odczytu** .

* **CanNotDelete** oznacza, że autoryzowani użytkownicy nadal mogą odczytywać i modyfikować zasób, ale nie mogą usunąć tego zasobu.
* **ReadOnly** oznacza, że autoryzowani użytkownicy mogą odczytywać zasoby, ale nie mogą usuwać ani aktualizować zasobu. Zastosowanie tej blokady jest podobne do ograniczenia wszystkich autoryzowanych użytkowników do uprawnień udzielonych przez rolę **czytelnika** .

## <a name="how-locks-are-applied"></a>Jak są stosowane blokady

W przypadku zastosowania blokady w zakresie nadrzędnym wszystkie zasoby w tym zakresie dziedziczą tę samą blokadę. Nawet zasoby dodawane później dziedziczą blokadę z elementu nadrzędnego. Pierwszeństwo ma najbardziej restrykcyjną blokadę dziedziczenia.

W przeciwieństwie do kontroli dostępu opartej na rolach blokady zarządzania są używane do stosowania ograniczenia do wszystkich użytkowników i ról. Aby dowiedzieć się więcej o ustawianiu uprawnień dla użytkowników i ról, zobacz [Kontrola dostępu oparta na rolach na platformie Azure (RBAC)](../../role-based-access-control/role-assignments-portal.md).

Blokady usługi Resource Manager dotyczą tylko operacji wykonywanych na płaszczyźnie zarządzania, która składa się z operacji wysyłanych do witryny `https://management.azure.com`. Blokady nie ograniczają sposobu wykonywania własnych funkcji przez zasoby. Zmiany zasobów są ograniczone, ale operacje zasobów nie są ograniczone. Na przykład blokada tylko do odczytu na SQL Database uniemożliwia usunięcie lub zmodyfikowanie bazy danych. Nie uniemożliwia tworzenia, aktualizowania ani usuwania danych w bazie danych. Transakcje danych są dozwolone, ponieważ te operacje nie są wysyłane do witryny `https://management.azure.com`.

## <a name="considerations-before-applying-locks"></a>Zagadnienia przed zastosowaniem blokad

Zastosowanie blokad może prowadzić do nieoczekiwanych wyników, ponieważ niektóre operacje, które nie pozornie modyfikują zasobu, rzeczywiście wymagają akcji blokowanych przez blokadę. Niektóre typowe przykłady operacji blokowanych przez blokady są następujące:

* Blokada tylko do odczytu na **koncie magazynu** uniemożliwia wszystkim użytkownikom wyświetlanie kluczy. Operacje listy kluczy są obsługiwane za pomocą żądania POST, ponieważ zwrócone klucze są dostępne dla operacji zapisu.

* Blokada tylko do odczytu w ramach zasobu **App Service** uniemożliwia programowi Visual Studio Eksplorator serwera wyświetlanie plików dla zasobu, ponieważ ta interakcja wymaga dostępu do zapisu.

* Blokada tylko do odczytu w **grupie zasobów** zawierającej **maszynę wirtualną** uniemożliwia wszystkim użytkownikom uruchamianie lub ponowne uruchamianie maszyny wirtualnej. Te operacje wymagają żądania POST.

* Nie można usunąć blokady **grupy zasobów** uniemożliwia Azure Resource Manager [Automatyczne usuwanie wdrożeń](../templates/deployment-history-deletions.md) w historii. Jeśli w historii osiągniesz 800 wdrożeń, wdrożenia zakończą się niepowodzeniem.

* Nie można usunąć blokady w **grupie zasobów** utworzonej przez **usługę Azure Backup** powoduje niepowodzenie wykonywania kopii zapasowych. Usługa obsługuje maksymalnie 18 punktów przywracania. Po zablokowaniu usługa Backup nie może oczyścić punktów przywracania. Aby uzyskać więcej informacji, zobacz [często zadawane pytania — tworzenie kopii zapasowych maszyn wirtualnych platformy Azure](../../backup/backup-azure-vm-backup-faq.md).

* Blokada tylko do odczytu w **ramach subskrypcji** uniemożliwia poprawne działanie **Azure Advisor** . W usłudze Advisor nie można przechowywać wyników zapytań.

## <a name="who-can-create-or-delete-locks"></a>Kto może tworzyć lub usuwać blokady

Aby utworzyć lub usunąć blokady zarządzania, musisz mieć dostęp do `Microsoft.Authorization/*` akcji lub `Microsoft.Authorization/locks/*` . Spośród wbudowanych ról tylko **Właściciel** i **Administrator dostępu użytkowników** mają dostęp do tych akcji.

## <a name="managed-applications-and-locks"></a>Zarządzane aplikacje i blokady

Niektóre usługi platformy Azure, takie jak Azure Databricks, używają [aplikacji zarządzanych](../managed-applications/overview.md) do implementowania usługi. W takim przypadku usługa tworzy dwie grupy zasobów. Jedna grupa zasobów zawiera przegląd usługi i nie jest zablokowana. Inna grupa zasobów zawiera infrastrukturę usługi i jest zablokowana.

Jeśli spróbujesz usunąć grupę zasobów infrastruktury, zostanie wyświetlony komunikat o błędzie informujący, że grupa zasobów jest zablokowana. Jeśli spróbujesz usunąć blokadę dla grupy zasobów infrastruktury, zostanie wyświetlony komunikat o błędzie informujący, że nie można usunąć blokady, ponieważ należy ona do aplikacji systemowej.

Zamiast tego należy usunąć usługę, która również usuwa grupę zasobów infrastruktury.

W przypadku aplikacji zarządzanych wybierz wdrożoną usługę.

![Wybieranie usługi](./media/lock-resources/select-service.png)

Zwróć uwagę, że usługa zawiera link do **zarządzanej grupy zasobów**. Ta grupa zasobów zawiera infrastrukturę i jest zablokowana. Nie można go bezpośrednio usunąć.

![Pokaż grupę zarządzaną](./media/lock-resources/show-managed-group.png)

Aby usunąć wszystkie elementy usługi, w tym zablokowaną grupę zasobów infrastruktury, wybierz pozycję **Usuń** dla usługi.

![Usuń usługę](./media/lock-resources/delete-service.png)

## <a name="configure-locks"></a>Konfigurowanie blokad

### <a name="portal"></a>Portal

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

### <a name="arm-template"></a>Szablon ARM

W przypadku użycia szablonu Menedżer zasobów do wdrożenia blokady należy użyć różnych wartości dla nazwy i typu w zależności od zakresu blokady.

W przypadku zastosowania blokady do **zasobu**należy użyć następujących formatów:

* Nazwij `{resourceName}/Microsoft.Authorization/{lockName}`
* Wprowadź `{resourceProviderNamespace}/{resourceType}/providers/locks`

W przypadku stosowania blokady do **grupy zasobów** lub **subskrypcji**należy użyć następujących formatów:

* Nazwij `{lockName}`
* Wprowadź `Microsoft.Authorization/locks`

Poniższy przykład przedstawia szablon, który tworzy plan usługi App Service, witrynę sieci Web i blokadę w witrynie sieci Web. Typ zasobu blokady to typ zasobu zasobu do zablokowania i **/providers/Locks**. Nazwa blokady jest tworzona przez połączenie nazwy zasobu z **/Microsoft.Authorization/** i nazwę blokady.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "hostingPlanName": {
      "type": "string"
    }
  },
  "variables": {
    "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2019-08-01",
      "name": "[parameters('hostingPlanName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "tier": "Free",
        "name": "f1",
        "capacity": 0
      },
      "properties": {
        "targetWorkerCount": 1
      }
    },
    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2019-08-01",
      "name": "[variables('siteName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      }
    },
    {
      "type": "Microsoft.Web/sites/providers/locks",
      "apiVersion": "2016-09-01",
      "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
      ],
      "properties": {
        "level": "CanNotDelete",
        "notes": "Site should not be deleted."
      }
    }
  ]
}
```

Aby zapoznać się z przykładem ustawienia blokady grupy zasobów, zobacz [Tworzenie grupy zasobów i blokowanie jej](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments/create-rg-lock-role-assignment).

### <a name="azure-powershell"></a>Azure PowerShell

Wdrożone zasoby można blokować za pomocą Azure PowerShell przy użyciu polecenia [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) .

Aby zablokować zasób, podaj nazwę zasobu, jego typ zasobu i nazwę grupy zasobów.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Aby zablokować grupę zasobów, podaj nazwę grupy zasobów.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Aby uzyskać informacje na temat blokady, użyj polecenie [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock). Aby uzyskać wszystkie blokady w ramach subskrypcji, użyj:

```azurepowershell-interactive
Get-AzResourceLock
```

Aby uzyskać wszystkie blokady dla zasobu, użyj:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Aby uzyskać wszystkie blokady dla grupy zasobów, użyj:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Aby usunąć blokadę, użyj:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Wdrożone zasoby można zablokować za pomocą polecenia [AZ Lock Create](/cli/azure/lock#az-lock-create) .

Aby zablokować zasób, podaj nazwę zasobu, jego typ zasobu i nazwę grupy zasobów.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Aby zablokować grupę zasobów, podaj nazwę grupy zasobów.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Aby uzyskać informacje na temat blokady, użyj [AZ Lock list](/cli/azure/lock#az-lock-list). Aby uzyskać wszystkie blokady w ramach subskrypcji, użyj:

```azurecli
az lock list
```

Aby uzyskać wszystkie blokady dla zasobu, użyj:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Aby uzyskać wszystkie blokady dla grupy zasobów, użyj:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Aby usunąć blokadę, użyj:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

### <a name="rest-api"></a>Interfejs API REST

Wdrożone zasoby można zablokować za pomocą [interfejsu API REST dla blokad zarządzania](/rest/api/resources/managementlocks). Interfejs API REST umożliwia tworzenie i usuwanie blokad oraz pobieranie informacji o istniejących blokadach.

Aby utworzyć blokadę, uruchom polecenie:

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}
```

Zakresem może być subskrypcja, Grupa zasobów lub zasób. Nazwa blokady jest taka, którą chcesz wywołać blokadę. W przypadku interfejsu API-Version należy użyć **2016-09-01**.

W żądaniu Dołącz obiekt JSON, który określa właściwości blokady.

```json
{
  "properties": {
  "level": "CanNotDelete",
  "notes": "Optional text notes."
  }
}
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak logicznie organizować zasoby, zobacz [Używanie tagów do organizowania zasobów](tag-resources.md).
* Ograniczenia i konwencje w ramach subskrypcji można stosować przy użyciu zasad niestandardowych. Aby uzyskać więcej informacji, zobacz artykuł [Co to jest usługa Azure Policy?](../../governance/policy/overview.md).
* Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).
