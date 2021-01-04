---
title: Ustaw kolejność wdrażania dla zasobów
description: Opisuje sposób ustawiania jednego zasobu jako zależnego od innego zasobu podczas wdrażania. Zależności zapewniają, że zasoby są wdrażane w odpowiedniej kolejności.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: a96dca0ab30d0baee2688427d78867ea128e673a
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97722015"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>Definiowanie kolejności wdrażania zasobów w szablonach ARM

Podczas wdrażania zasobów może być konieczne upewnienie się, że niektóre zasoby istnieją przed innymi zasobami. Na przykład do wdrożenia bazy danych jest potrzebny logiczny serwer SQL. Ta relacja jest określana przez oznaczenie jednego zasobu jako zależnego od innego zasobu. Użyj elementu **dependsOn** , aby zdefiniować jawną zależność. Użyj funkcji **Reference** lub **list** , aby zdefiniować zależność niejawną.

Usługa Resource Manager ocenia zależności pomiędzy zasobami i wdraża je w kolejności opartej na zależności. Gdy zasoby nie zależą od siebie nawzajem, usługa Resource Manager wdraża je równolegle. Wystarczy zdefiniować zależności dla zasobów wdrożonych w tym samym szablonie.

## <a name="dependson"></a>dependsOn

W ramach szablonu element dependsOn umożliwia zdefiniowanie jednego zasobu jako zależnego od jednego lub większej liczby zasobów. Jej wartość jest tablicą JSON ciągów, z których każdy jest nazwą zasobu lub IDENTYFIKATORem. Tablica może zawierać zasoby, które są [wdrażane warunkowo](conditional-resource-deployment.md). Gdy zasób warunkowy nie zostanie wdrożony, Azure Resource Manager automatycznie usuwa go z wymaganych zależności.

Poniższy przykład przedstawia interfejs sieciowy, który zależy od sieci wirtualnej, sieciowej grupy zabezpieczeń i publicznego adresu IP. Aby zapoznać się z pełnym szablonem, zobacz [szablon szybkiego startu dla maszyny wirtualnej z systemem Linux](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-simple-linux/azuredeploy.json).

```json
{
    "type": "Microsoft.Network/networkInterfaces",
    "apiVersion": "2020-06-01",
    "name": "[variables('networkInterfaceName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
      "[resourceId('Microsoft.Network/networkSecurityGroups/', parameters('networkSecurityGroupName'))]",
      "[resourceId('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
      "[resourceId('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
    ],
    ...
}
```

Podczas gdy nastąpi Nachylony do użycia dependsOn do mapowania relacji między zasobami, ważne jest, aby zrozumieć, dlaczego jest to konieczne. Na przykład w celu dokumentowania sposobu łączenia zasobów dependsOn nie jest właściwe podejście. Po wdrożeniu nie można zbadać, które zasoby zostały zdefiniowane w elemencie dependsOn. Ustawienie niepotrzebnych zależności spowalnia czas wdrożenia, ponieważ Menedżer zasobów nie może wdrożyć tych zasobów równolegle.

## <a name="child-resources"></a>Zasoby podrzędne

Niejawna zależność wdrożenia nie jest automatycznie tworzona między [zasobem podrzędnym](child-resource-name-type.md) a zasobem nadrzędnym. Jeśli zachodzi potrzeba wdrożenia zasobu podrzędnego po zasobie nadrzędnym, należy ustawić właściwość dependsOn.

Poniższy przykład przedstawia logiczny serwer SQL i bazę danych. Należy zauważyć, że dla bazy danych i serwera jest zdefiniowana jawna zależność, nawet jeśli baza danych jest elementem podrzędnym serwera.

```json
"resources": [
  {
    "type": "Microsoft.Sql/servers",
    "apiVersion": "2020-02-02-preview",
    "name": "[parameters('serverName')]",
    "location": "[parameters('location')]",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "type": "databases",
        "apiVersion": "2020-08-01-preview",
        "name": "[parameters('sqlDBName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
          },
        "dependsOn": [
          "[resourceId('Microsoft.Sql/servers', concat(parameters('serverName')))]"
        ]
      }
    ]
  }
]
```

Aby zapoznać się z pełnym szablonem, zobacz [szablon szybkiego startu dla Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/blob/master/101-sql-database/azuredeploy.json).

## <a name="reference-and-list-functions"></a>funkcje odwołania i listy

[Funkcja Reference](template-functions-resource.md#reference) umożliwia wyrażeniu uzyskanie wartości z innych par nazw i wartości JSON lub zasobów środowiska uruchomieniowego. [Lista * funkcje](template-functions-resource.md#list) zwracają wartości dla zasobu z operacji listy.

Wyrażenia odwołania i listy niejawnie deklarują, że jeden zasób zależy od innego. Jeśli to możliwe, użyj niejawnego odwołania, aby uniknąć dodawania niepotrzebnej zależności.

Aby wymusić zależność niejawną, zapoznaj się z zasobem według nazwy, a nie z IDENTYFIKATORem zasobu. W przypadku przekazania identyfikatora zasobu do funkcji odwołania lub listy niejawne odwołanie nie zostanie utworzone.

Ogólny format funkcji referencyjnej to:

```json
reference('resourceName').propertyPath
```

Ogólny format funkcji listKeys to:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

W poniższym przykładzie punkt końcowy usługi CDN jest jawnie zależny od profilu CDN i niejawnie zależy od aplikacji sieci Web.

```json
{
    "name": "[variables('endpointName')]",
    "apiVersion": "2016-04-02",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "dependsOn": [
      "[variables('profileName')]"
    ],
    "properties": {
      "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
      ...
    }
```

Aby dowiedzieć się więcej, zobacz [Funkcja Reference](template-functions-resource.md#reference).

## <a name="depend-on-resources-in-a-loop"></a>Zależą od zasobów w pętli

Aby wdrożyć zasoby, które są zależne od zasobów w [pętli kopiowania](copy-resources.md), dostępne są dwie opcje. Można ustawić zależność od poszczególnych zasobów w pętli lub w całej pętli.

> [!NOTE]
> W przypadku większości scenariuszy należy ustawić zależność od poszczególnych zasobów w pętli kopiowania. Zależą od całej pętli, gdy potrzebne są wszystkie zasoby w pętli, aby istniały przed utworzeniem następnego zasobu. Ustawienie zależności w całej pętli powoduje znaczne powiększenie wykresu zależności, szczególnie jeśli te zasoby zapętlenia zależą od innych zasobów. Rozwinięte zależności utrudniają wydajne wdrażanie.

Poniższy przykład pokazuje, jak wdrożyć wiele maszyn wirtualnych. Szablon tworzy taką samą liczbę interfejsów sieciowych. Każda maszyna wirtualna jest zależna od jednego interfejsu sieciowego, a nie całej pętli.

```json
{
  "type": "Microsoft.Network/networkInterfaces",
  "apiVersion": "2020-05-01",
  "name": "[concat(variables('nicPrefix'),'-',copyIndex())]",
  "location": "[parameters('location')]",
  "copy": {
    "name": "nicCopy",
    "count": "[parameters('vmCount')]"
  },
  ...
},
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "name": "[concat(variables('vmPrefix'),copyIndex())]",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]"
  ],
  "copy": {
    "name": "vmCopy",
    "count": "[parameters('vmCount')]"
  },
  "properties": {
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]",
          "properties": {
            "primary": "true"
          }
        }
      ]
    },
    ...
  }
}
```

Poniższy przykład pokazuje, jak wdrożyć trzy konta magazynu przed wdrożeniem maszyny wirtualnej. Zwróć uwagę, że element Copy ma ustawioną nazwę `storagecopy` i element dependsOn dla maszyny wirtualnej jest również ustawiony na `storagecopy` .

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

## <a name="circular-dependencies"></a>Zależności cykliczne

Menedżer zasobów identyfikuje zależności cykliczne podczas walidacji szablonu. Jeśli wystąpi błąd dla zależności cyklicznej, Oceń szablon, aby sprawdzić, czy można usunąć dowolne zależności. Jeśli Usuwanie zależności nie działa, można uniknąć cyklicznych zależności przez przeniesienie niektórych operacji wdrażania do zasobów podrzędnych. Wdróż zasoby podrzędne po zasobach, które mają zależność cykliczną. Załóżmy na przykład, że wdrażasz dwie maszyny wirtualne, ale musisz ustawić właściwości dla każdej z nich, która odwołuje się do drugiego. Można je wdrożyć w następującej kolejności:

1. vm1
2. VM2
3. Rozszerzenie na VM1 zależy od VM1 i VM2. Rozszerzenie ustawia wartości w VM1, które pobiera z VM2.
4. Rozszerzenie na VM2 zależy od VM1 i VM2. Rozszerzenie ustawia wartości w VM2, które pobiera z VM1.

Aby uzyskać informacje o ocenie kolejności wdrażania i rozwiązywaniu błędów zależności, zobacz [Rozwiązywanie typowych błędów wdrażania platformy Azure przy użyciu Azure Resource Manager](common-deployment-errors.md).

## <a name="next-steps"></a>Następne kroki

* Aby przejść przez samouczek, zobacz [Samouczek: Tworzenie szablonów Azure Resource Manager z zasobami zależnymi](template-tutorial-create-templates-with-dependent-resources.md).
* W przypadku modułu Microsoft Learn, który obejmuje zależności zasobów, zobacz [zarządzanie złożonymi wdrożeniami w chmurze za pomocą zaawansowanych funkcji szablonów usługi ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
* Zalecenia dotyczące konfigurowania zależności można znaleźć w temacie [Azure Resource Manager Best Practices Template](template-best-practices.md).
* Aby dowiedzieć się więcej o rozwiązywaniu problemów podczas wdrażania, zobacz [Rozwiązywanie typowych błędów wdrażania platformy Azure przy użyciu Azure Resource Manager](common-deployment-errors.md).
* Aby dowiedzieć się więcej na temat tworzenia szablonów Azure Resource Manager, zobacz Tworzenie [szablonów](template-syntax.md).
* Aby uzyskać listę dostępnych funkcji w szablonie, zobacz [funkcje szablonu](template-functions.md).

