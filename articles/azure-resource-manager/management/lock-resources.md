---
title: Zablokuj zasoby, aby uniemożliwić zmiany
description: Zablokuj użytkownikom możliwość aktualizowania lub usuwania zasobów platformy Azure, stosując blokadę dla wszystkich użytkowników i ról.
ms.topic: conceptual
ms.date: 03/09/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 28c31681b8fbe981cd51db294c91276dfd65d71f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102619175"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Blokowanie zasobów w celu uniemożliwienia nieoczekiwanych zmian

Jako administrator możesz zablokować subskrypcję, grupę zasobów lub zasób, aby zapobiec przypadkowemu usunięciu lub zmodyfikowaniu zasobów krytycznych przez innych użytkowników w organizacji. Blokada zastępuje wszystkie uprawnienia, które użytkownik może mieć.

Poziom blokady można ustawić na wartość **CanNotDelete** lub **ReadOnly**. W portalu blokady są nazywane odpowiednio **usuwaniem** i **tylko do odczytu** .

* **CanNotDelete** oznacza, że autoryzowani użytkownicy nadal mogą odczytywać i modyfikować zasób, ale nie mogą usunąć tego zasobu.
* **ReadOnly** oznacza, że autoryzowani użytkownicy mogą odczytywać zasoby, ale nie mogą usuwać ani aktualizować zasobu. Zastosowanie tej blokady jest podobne do ograniczenia wszystkich autoryzowanych użytkowników do uprawnień udzielonych przez rolę **czytelnika** .

## <a name="how-locks-are-applied"></a>Jak są stosowane blokady

W przypadku zastosowania blokady w zakresie nadrzędnym wszystkie zasoby w tym zakresie dziedziczą tę samą blokadę. Nawet zasoby dodawane później dziedziczą blokadę z elementu nadrzędnego. Pierwszeństwo ma najbardziej restrykcyjną blokadę dziedziczenia.

W przeciwieństwie do kontroli dostępu opartej na rolach blokady zarządzania są używane do stosowania ograniczenia do wszystkich użytkowników i ról. Aby dowiedzieć się więcej o ustawianiu uprawnień dla użytkowników i ról, zobacz [Kontrola dostępu oparta na rolach na platformie Azure (RBAC)](../../role-based-access-control/role-assignments-portal.md).

Blokady usługi Resource Manager dotyczą tylko operacji wykonywanych na płaszczyźnie zarządzania, która składa się z operacji wysyłanych do witryny `https://management.azure.com`. Blokady nie ograniczają sposobu wykonywania własnych funkcji przez zasoby. Zmiany zasobów są ograniczone, ale operacje zasobów nie są ograniczone. Na przykład blokada tylko do odczytu na serwerze logicznym SQL Database uniemożliwia usunięcie lub modyfikację serwera. Nie uniemożliwia to tworzenia, aktualizowania ani usuwania danych w bazach danych na tym serwerze. Transakcje danych są dozwolone, ponieważ te operacje nie są wysyłane do witryny `https://management.azure.com`.

## <a name="considerations-before-applying-locks"></a>Zagadnienia przed zastosowaniem blokad

Zastosowanie blokad może prowadzić do nieoczekiwanych wyników, ponieważ niektóre operacje, które nie pozornie modyfikują zasobu, rzeczywiście wymagają akcji blokowanych przez blokadę. Blokady uniemożliwią wykonywanie operacji, które wymagają żądania POST do interfejsu API Azure Resource Manager. Niektóre typowe przykłady operacji blokowanych przez blokady są następujące:

* Blokada tylko do odczytu na **koncie magazynu** uniemożliwia użytkownikom wyświetlanie listy kluczy konta. Operacja [kluczy list](/rest/api/storagerp/storageaccounts/listkeys) usługi Azure Storage jest obsługiwana za pomocą żądania post w celu ochrony dostępu do kluczy konta, co zapewnia pełny dostęp do danych na koncie magazynu. W przypadku skonfigurowania blokady tylko do odczytu dla konta magazynu użytkownicy, którzy nie posiadają kluczy konta, muszą używać poświadczeń usługi Azure AD w celu uzyskania dostępu do danych obiektów blob lub kolejek. Blokada tylko do odczytu uniemożliwia również Przypisanie ról RBAC platformy Azure objętych zakresem do konta magazynu lub kontenera danych (kontenera obiektów blob lub kolejki).

* Blokada nie może zostać usunięta na **koncie magazynu** nie zapobiega usuwaniu ani modyfikowaniu danych znajdujących się na tym koncie. Ten typ blokady chroni tylko samo konto magazynu przed jego usunięciem i nie chroni danych obiektów blob, kolejek, tabel i plików w ramach tego konta magazynu. 

* Blokada tylko do odczytu na **koncie magazynu** nie zapobiega usuwaniu ani modyfikowaniu danych znajdujących się na tym koncie. Ten typ blokady chroni tylko konto magazynu przed jego usunięciem lub modyfikacją i nie chroni danych obiektów blob, kolejek, tabel i plików w ramach tego konta magazynu. 

* Blokada tylko do odczytu w ramach zasobu **App Service** uniemożliwia programowi Visual Studio Eksplorator serwera wyświetlanie plików dla zasobu, ponieważ ta interakcja wymaga dostępu do zapisu.

* Blokada tylko do odczytu w **grupie zasobów** zawierającej **maszynę wirtualną** uniemożliwia wszystkim użytkownikom uruchamianie lub ponowne uruchamianie maszyny wirtualnej. Te operacje wymagają żądania POST.

* Nie można usunąć blokady **grupy zasobów** uniemożliwia Azure Resource Manager [Automatyczne usuwanie wdrożeń](../templates/deployment-history-deletions.md) w historii. Jeśli w historii osiągniesz 800 wdrożeń, wdrożenia zakończą się niepowodzeniem.

* Nie można usunąć blokady w **grupie zasobów** utworzonej przez **usługę Azure Backup** powoduje niepowodzenie wykonywania kopii zapasowych. Usługa obsługuje maksymalnie 18 punktów przywracania. Po zablokowaniu usługa Backup nie może oczyścić punktów przywracania. Aby uzyskać więcej informacji, zobacz [często zadawane pytania — tworzenie kopii zapasowych maszyn wirtualnych platformy Azure](../../backup/backup-azure-vm-backup-faq.yml).

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

W przypadku używania szablonu Azure Resource Manager (szablonu ARM) do wdrożenia blokady należy mieć świadomość zakresu blokady i zakresu wdrożenia. Aby zastosować blokadę w zakresie wdrożenia, na przykład blokując grupę zasobów lub subskrypcję, nie ustawiaj właściwości Scope. W przypadku blokowania zasobu w ramach zakresu wdrożenia ustaw właściwość Scope.

Poniższy szablon stosuje blokadę do grupy zasobów, w której jest wdrażana. Zwróć uwagę, że w zasobie nie ma właściwości Scope, ponieważ zakres blokady jest zgodny z zakresem wdrożenia. Ten szablon jest wdrażany na poziomie grupy zasobów.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {  
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/locks",
            "apiVersion": "2016-09-01",
            "name": "rgLock",
            "properties": {
                "level": "CanNotDelete",
                "notes": "Resource Group should not be deleted."
            }
        }
    ]
}
```

Aby utworzyć grupę zasobów i zablokować ją, wdróż następujący szablon na poziomie subskrypcji.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2019-10-01",
            "name": "[parameters('rgName')]",
            "location": "[parameters('rgLocation')]",
            "properties": {}
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "lockDeployment",
            "resourceGroup": "[parameters('rgName')]",
            "dependsOn": [
                "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Authorization/locks",
                            "apiVersion": "2016-09-01",
                            "name": "rgLock",
                            "properties": {
                                "level": "CanNotDelete",
                                "notes": "Resource group and its resources should not be deleted."
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ],
    "outputs": {}
}
```

W przypadku zastosowania blokady do **zasobu** w grupie zasobów Dodaj właściwość Scope. Ustaw zakres na nazwę zasobu do zablokowania.

Poniższy przykład przedstawia szablon, który tworzy plan usługi App Service, witrynę sieci Web i blokadę w witrynie sieci Web. Zakres blokady jest ustawiany na witrynę sieci Web.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "hostingPlanName": {
      "type": "string"
    },
    "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2020-06-01",
      "name": "[parameters('hostingPlanName')]",
      "location": "[parameters('location')]",
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
      "apiVersion": "2020-06-01",
      "name": "[variables('siteName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      }
    },
    {
      "type": "Microsoft.Authorization/locks",
      "apiVersion": "2016-09-01",
      "name": "siteLock",
      "scope": "[concat('Microsoft.Web/sites/', variables('siteName'))]",
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

Aby usunąć blokadę dla zasobu, użyj:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

Aby usunąć blokadę dla grupy zasobów, użyj:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup).LockId
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

Aby usunąć blokadę dla zasobu, użyj:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

Aby usunąć blokadę dla grupy zasobów, użyj:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup  --output tsv --query id)
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
