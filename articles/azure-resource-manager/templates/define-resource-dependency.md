---
title: Ustaw kolejność wdrażania dla zasobów
description: Opisuje sposób ustawiania jednego zasobu jako zależnego od innego zasobu podczas wdrażania, aby zapewnić, że zasoby są wdrażane w odpowiedniej kolejności.
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 933764f1930bd6c9e21d4ccffbde1bb93bbc9613
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672818"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>Definiowanie kolejności wdrażania zasobów w szablonach ARM

Podczas wdrażania zasobu może być konieczne upewnienie się, że inne zasoby istnieją przed jego wdrożeniem. Na przykład do wdrożenia bazy danych jest potrzebny logiczny serwer SQL. Ta relacja jest definiowana przez oznaczenie jednego zasobu jako zależnego od innego zasobu. Należy zdefiniować zależność z elementem **dependsOn** lub za pomocą funkcji **Reference** .

Usługa Resource Manager ocenia zależności pomiędzy zasobami i wdraża je w kolejności opartej na zależności. Gdy zasoby nie zależą od siebie nawzajem, usługa Resource Manager wdraża je równolegle. Wystarczy zdefiniować zależności dla zasobów wdrożonych w tym samym szablonie.

## <a name="dependson"></a>dependsOn

W ramach szablonu element dependsOn umożliwia zdefiniowanie jednego zasobu jako zależnego od jednego lub większej liczby zasobów. Jej wartość jest tablicą JSON ciągów, z których każdy jest nazwą zasobu. Tablica może zawierać zasoby, które są [wdrażane warunkowo](conditional-resource-deployment.md). Gdy zasób warunkowy nie zostanie wdrożony, Azure Resource Manager automatycznie usuwa go z wymaganych zależności.

Poniższy przykład przedstawia zestaw skalowania maszyn wirtualnych, który zależy od modułu równoważenia obciążenia, sieci wirtualnej i pętli, która tworzy wiele kont magazynu. Te inne zasoby nie są wyświetlane w poniższym przykładzie, ale muszą istnieć w innym miejscu szablonu.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "apiVersion": "2016-03-30",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
  "tags": {
    "displayName": "VMScaleSet"
  },
  "dependsOn": [
    "[variables('loadBalancerName')]",
    "[variables('virtualNetworkName')]",
    "storageLoop",
  ],
  ...
}
```

W poprzednim przykładzie zależność jest uwzględniona w zasobach, które są tworzone za pomocą pętli kopiowania o nazwie **storageLoop**. Aby zapoznać się z przykładem, zobacz [Tworzenie wielu wystąpień zasobów w Azure Resource Manager](copy-resources.md).

Podczas definiowania zależności można uwzględnić przestrzeń nazw dostawcy zasobów i typ zasobu, aby uniknąć niejednoznaczności. Aby na przykład wyjaśnić moduł równoważenia obciążenia i sieć wirtualną, które mogą mieć takie same nazwy jak inne zasoby, użyj następującego formatu:

```json
"dependsOn": [
  "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
  "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
]
```

Podczas gdy nastąpi Nachylony do użycia dependsOn do mapowania relacji między zasobami, ważne jest, aby zrozumieć, dlaczego jest to konieczne. Na przykład w celu dokumentowania sposobu łączenia zasobów dependsOn nie jest właściwe podejście. Po wdrożeniu nie można zbadać, które zasoby zostały zdefiniowane w elemencie dependsOn. Korzystając z dependsOn, może to mieć wpływ na czas wdrażania, ponieważ Menedżer zasobów nie jest wdrażany w równoległych dwóch zasobach, które mają zależność.

## <a name="child-resources"></a>Zasoby podrzędne

Właściwość Resources pozwala określić zasoby podrzędne, które są powiązane ze zdefiniowanym zasobem. Zasoby podrzędne można zdefiniować tylko na poziomie pięciu poziomów. Należy pamiętać, że niejawna zależność wdrożenia nie jest tworzona między zasobem podrzędnym i zasobem nadrzędnym. Jeśli zasób podrzędny ma zostać wdrożony po zasobie nadrzędnym, należy jawnie podać tę zależność przy użyciu właściwości dependsOn.

Każdy zasób nadrzędny akceptuje tylko niektóre typy zasobów jako zasoby podrzędne. Akceptowane typy zasobów są określone w [schemacie szablonu](https://github.com/Azure/azure-resource-manager-schemas) zasobu nadrzędnego. Nazwa typu zasobu podrzędnego obejmuje nazwę nadrzędnego typu zasobu, takiego jak **Microsoft. Web/Sites/config** i **Microsoft. Web/Sites/Extensions** , są zasobami podrzędnymi **firmy Microsoft. Web/Sites**.

Poniższy przykład przedstawia logiczny serwer SQL i bazę danych. Należy zauważyć, że dla bazy danych i serwera jest zdefiniowana jawna zależność, nawet jeśli baza danych jest elementem podrzędnym serwera.

```json
"resources": [
  {
    "name": "[variables('sqlserverName')]",
    "apiVersion": "2014-04-01-preview",
    "type": "Microsoft.Sql/servers",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "SqlServer"
    },
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "name": "[parameters('databaseName')]",
        "apiVersion": "2014-04-01-preview",
        "type": "databases",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[variables('sqlserverName')]"
        ],
        "tags": {
          "displayName": "Database"
        },
        "properties": {
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
        }
      }
    ]
  }
]
```

## <a name="reference-and-list-functions"></a>funkcje odwołania i listy

[Funkcja Reference](template-functions-resource.md#reference) umożliwia wyrażeniu uzyskanie wartości z innych par nazw i wartości JSON lub zasobów środowiska uruchomieniowego. [Lista * funkcje](template-functions-resource.md#list) zwracają wartości dla zasobu z operacji listy.  Wyrażenia odwołania i listy niejawnie deklarują, że jeden zasób zależy od innego, gdy zasób, do którego się odwoływano, jest wdrażany w tym samym szablonie i jest określany przez jego nazwę (nie identyfikator zasobu). W przypadku przekazania identyfikatora zasobu do funkcji odwołania lub listy niejawne odwołanie nie zostanie utworzone.

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

Możesz użyć tego elementu lub elementu dependsOn, aby określić zależności, ale nie musisz używać obu dla tego samego zasobu zależnego. Jeśli to możliwe, użyj niejawnego odwołania, aby uniknąć dodawania niepotrzebnej zależności.

Aby dowiedzieć się więcej, zobacz [Funkcja Reference](template-functions-resource.md#reference).

## <a name="circular-dependencies"></a>Zależności cykliczne

Menedżer zasobów identyfikuje zależności cykliczne podczas walidacji szablonu. Jeśli zostanie wyświetlony komunikat o błędzie informujący, że istnieje zależność cykliczna, należy oszacować szablon, aby sprawdzić, czy jakiekolwiek zależności nie są potrzebne i można je usunąć. Jeśli Usuwanie zależności nie działa, można uniknąć cyklicznych zależności przez przeniesienie niektórych operacji wdrażania do zasobów podrzędnych, które są wdrażane po zasobach, które mają zależność cykliczną. Załóżmy na przykład, że wdrażasz dwie maszyny wirtualne, ale musisz ustawić właściwości dla każdej z nich, która odwołuje się do drugiego. Można je wdrożyć w następującej kolejności:

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

