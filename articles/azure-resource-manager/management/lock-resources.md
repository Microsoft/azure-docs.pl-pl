---
title: Blokowanie zasobów w celu zapobiegania zmianom
description: Uniemożliwianie użytkownikom aktualizowania lub usuwania zasobów platformy Azure przez zastosowanie blokady dla wszystkich użytkowników i ról.
ms.topic: conceptual
ms.date: 04/07/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 71637318a60e66bf5000de2f564d740cc101cc60
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768727"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Blokowanie zasobów w celu uniemożliwienia nieoczekiwanych zmian

Jako administrator możesz zablokować subskrypcję, grupę zasobów lub zasób, aby uniemożliwić innym użytkownikom w organizacji przypadkowe usunięcie lub zmodyfikowanie krytycznych zasobów. Blokada zastępuje wszelkie uprawnienia, które może mieć użytkownik.

Poziom blokady można ustawić na wartość **CanNotDelete** lub **ReadOnly**. W portalu blokady są odpowiednio nazywane **Usuwaniem** i **Tylko do** odczytu.

* **CanNotDelete oznacza,** że autoryzowani użytkownicy nadal mogą odczytywać i modyfikować zasób, ale nie mogą go usunąć.
* **Tylko do odczytu oznacza,** że autoryzowani użytkownicy mogą odczytywać zasób, ale nie mogą go usunąć ani zaktualizować. Zastosowanie tej blokady jest podobne do ograniczania wszystkich autoryzowanych użytkowników do uprawnień przyznanych przez rolę **Czytelnik.**

## <a name="how-locks-are-applied"></a>Jak są stosowane blokady

Po zastosowaniu blokady w zakresie nadrzędnym wszystkie zasoby w tym zakresie dziedziczą tę samą blokadę. Nawet zasoby, które później dodasz, dziedziczą blokadę z elementu nadrzędnego. Pierwszeństwo ma najbardziej restrykcyjna blokada w dziedziczeniu.

W przeciwieństwie do kontroli dostępu opartej na rolach blokady zarządzania są używane do stosowania ograniczenia do wszystkich użytkowników i ról. Aby dowiedzieć się więcej na temat ustawiania uprawnień dla użytkowników i ról, zobacz [Azure role-based access control (Azure RBAC) (Kontrola](../../role-based-access-control/role-assignments-portal.md)dostępu oparta na rolach na platformie Azure ).

Blokady usługi Resource Manager dotyczą tylko operacji wykonywanych na płaszczyźnie zarządzania, która składa się z operacji wysyłanych do witryny `https://management.azure.com`. Blokady nie ograniczają sposobu wykonywania własnych funkcji przez zasoby. Zmiany zasobów są ograniczone, ale operacje zasobów nie są ograniczone. Na przykład blokada Tylko do odczytu na serwerze logicznym SQL Database uniemożliwia usunięcie lub zmodyfikowanie serwera. Nie uniemożliwia to tworzenia, aktualizowania ani usuwania danych w bazach danych na tym serwerze. Transakcje danych są dozwolone, ponieważ te operacje nie są wysyłane do witryny `https://management.azure.com`.

## <a name="considerations-before-applying-locks"></a>Zagadnienia dotyczące przed zastosowaniem blokad

Zastosowanie blokad może prowadzić do nieoczekiwanych wyników, ponieważ niektóre operacje, które nie wydają się modyfikować zasobu, w rzeczywistości wymagają akcji zablokowanych przez blokadę. Blokady uniemożliwią wszelkie operacje, które wymagają żądania POST do Azure Resource Manager API. Oto kilka typowych przykładów operacji blokowanych przez blokady:

* Blokada tylko do odczytu na **koncie magazynu uniemożliwia** użytkownikom wyświetlanie kluczy kont. Operacja kluczy [listy usługi](/rest/api/storagerp/storageaccounts/listkeys) Azure Storage jest przetwarzana za pośrednictwem żądania POST w celu ochrony dostępu do kluczy kont, które zapewniają pełny dostęp do danych na koncie magazynu. Jeśli dla konta magazynu skonfigurowano blokadę tylko do odczytu, użytkownicy, którzy nie mają kluczy kont, muszą używać poświadczeń usługi Azure AD, aby uzyskać dostęp do danych obiektów blob lub kolejek. Blokada tylko do odczytu uniemożliwia również przypisywanie ról RBAC platformy Azure, które są w zakresie konta magazynu lub kontenera danych (kontenera obiektów blob lub kolejki).

* Blokada nie można usunąć na **koncie magazynu nie** uniemożliwia usunięcia ani zmodyfikowania danych w ramach tego konta. Ten typ blokady chroni tylko samo konto magazynu przed usunięciem i nie chroni danych obiektów blob, kolejek, tabel ani plików w ramach tego konta magazynu. 

* Blokada tylko do odczytu na **koncie magazynu** nie uniemożliwia usunięcia ani zmodyfikowania danych na tym koncie. Ten typ blokady chroni tylko samo konto magazynu przed usunięciem lub modyfikacją i nie chroni danych obiektów blob, kolejek, tabel ani plików w ramach tego konta magazynu. 

* Blokada tylko do odczytu zasobu **App Service** uniemożliwia Visual Studio Eksplorator serwera wyświetlania plików dla zasobu, ponieważ ta interakcja wymaga dostępu do zapisu.

* Blokada tylko do odczytu dla **grupy zasobów** zawierającej plan App Service **uniemożliwia** skalowanie planu w górę lub [w górę.](../../app-service/manage-scale-up.md)

* Blokada tylko do odczytu w grupie **zasobów zawierającej** maszynę **wirtualną** uniemożliwia wszystkim użytkownikom uruchamianie lub ponowne uruchamianie maszyny wirtualnej. Te operacje wymagają żądania POST.

* Blokada nie można usunąć w grupie zasobów **uniemożliwia** Azure Resource Manager [automatycznego usuwania wdrożeń](../templates/deployment-history-deletions.md) w historii. Jeśli w historii osiągniesz 800 wdrożeń, wdrożenia nie powiodą się.

* Nie można usunąć blokady grupy zasobów **utworzonej** przez **usługę Azure Backup powoduje** niepowodzenie tworzenia kopii zapasowych. Usługa obsługuje maksymalnie 18 punktów przywracania. Po zablokowaniu usługa tworzenia kopii zapasowej nie może wyczyścić punktów przywracania. Aby uzyskać więcej informacji, zobacz Często zadawane pytania dotyczące kopii [zapasowej maszyn wirtualnych platformy Azure.](../../backup/backup-azure-vm-backup-faq.yml)

* Blokada tylko do odczytu subskrypcji **uniemożliwia** prawidłowe **Azure Advisor** subskrypcji. Advisor nie może przechowywać wyników swoich zapytań.

## <a name="who-can-create-or-delete-locks"></a>Kto może tworzyć lub usuwać blokady

Aby utworzyć lub usunąć blokady zarządzania, musisz mieć dostęp do lub `Microsoft.Authorization/*` `Microsoft.Authorization/locks/*` akcji. Spośród wbudowanych ról tylko **Właściciel** i **Administrator dostępu użytkowników** mają dostęp do tych akcji.

## <a name="managed-applications-and-locks"></a>Blokady i aplikacje zarządzane

Niektóre usługi platformy Azure, takie Azure Databricks, używają [zarządzanych aplikacji](../managed-applications/overview.md) do zaimplementowania usługi. W takim przypadku usługa tworzy dwie grupy zasobów. Jedna grupa zasobów zawiera omówienie usługi i nie jest zablokowana. Druga grupa zasobów zawiera infrastrukturę usługi i jest zablokowana.

Jeśli spróbujesz usunąć grupę zasobów infrastruktury, wystąpi błąd informujący, że grupa zasobów jest zablokowana. Jeśli spróbujesz usunąć blokadę dla grupy zasobów infrastruktury, zostanie wyświetlany komunikat o błędzie informujący, że nie można usunąć blokady, ponieważ jest ona własnością aplikacji systemowej.

Zamiast tego usuń usługę, co spowoduje również usunięcie grupy zasobów infrastruktury.

W przypadku aplikacji zarządzanych wybierz wdrożoną usługę.

![Wybieranie usługi](./media/lock-resources/select-service.png)

Zwróć uwagę, że usługa zawiera link do **zarządzanej grupy zasobów.** Ta grupa zasobów przechowuje infrastrukturę i jest zablokowana. Nie można go bezpośrednio usunąć.

![Pokaż grupę zarządzaną](./media/lock-resources/show-managed-group.png)

Aby usunąć wszystko dla usługi, w tym zablokowaną grupę zasobów infrastruktury, wybierz **pozycję Usuń** dla usługi.

![Usuwanie usługi](./media/lock-resources/delete-service.png)

## <a name="configure-locks"></a>Konfigurowanie blokad

### <a name="portal"></a>Portal

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

### <a name="arm-template"></a>Szablon ARM

W przypadku Azure Resource Manager blokady (szablonu ARM) należy pamiętać o zakresie blokady i zakresie wdrożenia. Aby zastosować blokadę w zakresie wdrożenia, na przykład zablokować grupę zasobów lub subskrypcję, nie należy ustawiać właściwości zakresu. Podczas blokowania zasobu w zakresie wdrożenia ustaw właściwość scope.

Poniższy szablon stosuje blokadę do grupy zasobów, w która jest wdrażana. Zwróć uwagę, że dla zasobu blokady nie ma właściwości scope, ponieważ zakres blokady odpowiada zakresowi wdrożenia. Ten szablon jest wdrażany na poziomie grupy zasobów.

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

Aby utworzyć grupę zasobów i zablokować ją, wd wdrażaj następujący szablon na poziomie subskrypcji.

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

Podczas stosowania blokady do **zasobu** w grupie zasobów dodaj właściwość scope. Ustaw zakres na nazwę zasobu, który ma być blokowany.

Poniższy przykład przedstawia szablon, który tworzy plan usługi App Service, witrynę internetową i blokadę witryny internetowej. Zakres blokady jest ustawiony na witrynę internetową.

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

Wdrożone zasoby można zablokować za Azure PowerShell za pomocą [polecenia New-AzResourceLock.](/powershell/module/az.resources/new-azresourcelock)

Aby zablokować zasób, podaj nazwę zasobu, jego typ zasobu i nazwę grupy zasobów.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Aby zablokować grupę zasobów, podaj nazwę grupy zasobów.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Aby uzyskać informacje na temat blokady, użyj [get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock). Aby uzyskać wszystkie blokady w subskrypcji, użyj:

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

Aby usunąć blokadę zasobu, użyj:

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

Wdrożone zasoby można zablokować za pomocą interfejsu wiersza polecenia platformy Azure za pomocą [polecenia az lock create.](/cli/azure/lock#az_lock_create)

Aby zablokować zasób, podaj nazwę zasobu, jego typ i nazwę grupy zasobów.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Aby zablokować grupę zasobów, podaj nazwę grupy zasobów.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Aby uzyskać informacje o blokadę, użyj [az lock list](/cli/azure/lock#az_lock_list). Aby uzyskać wszystkie blokady w subskrypcji, użyj:

```azurecli
az lock list
```

Aby uzyskać wszystkie blokady zasobu, użyj:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Aby uzyskać wszystkie blokady dla grupy zasobów, użyj:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Aby usunąć blokadę zasobu, użyj:

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

Wdrożone zasoby można zablokować za pomocą interfejsu [API REST, aby zablokować blokadę zarządzania.](/rest/api/resources/managementlocks) Interfejs API REST umożliwia tworzenie i usuwanie blokad oraz pobieranie informacji o istniejących blokadach.

Aby utworzyć blokadę, uruchom:

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}
```

Zakresem może być subskrypcja, grupa zasobów lub zasób. Nazwa blokady jest nazwą, którą chcesz wywołać blokadę. W przypadku wersji interfejsu API użyj **ciągu 2016-09-01.**

W żądaniu dołącz obiekt JSON, który określa właściwości blokady.

```json
{
  "properties": {
  "level": "CanNotDelete",
  "notes": "Optional text notes."
  }
}
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o logicznym organizowaniu zasobów, zobacz Organizowanie zasobów [przy użyciu tagów.](tag-resources.md)
* Możesz stosować ograniczenia i konwencje w ramach subskrypcji za pomocą dostosowanych zasad. Aby uzyskać więcej informacji, zobacz artykuł [Co to jest usługa Azure Policy?](../../governance/policy/overview.md).
* Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).
