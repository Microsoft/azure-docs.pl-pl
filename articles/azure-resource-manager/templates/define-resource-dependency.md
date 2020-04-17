---
title: Ustawianie kolejności wdrażania zasobów
description: W tym artykule opisano sposób ustawiania jednego zasobu jako zależnego od innego zasobu podczas wdrażania, aby upewnić się, że zasoby są wdrażane w odpowiedniej kolejności.
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: 764b718416e1185f56c7eb6b8335792a5822f212
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535472"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>Definiowanie kolejności wdrażania zasobów w szablonach ARM

Podczas wdrażania zasobu, może być konieczne, aby upewnić się, że istnieją inne zasoby przed jego wdrożeniem. Na przykład przed wdrożeniem bazy danych SQL potrzebny jest serwer SQL. Tę relację definiujesz, oznaczając jeden zasób jako zależny od drugiego zasobu. Definiujesz zależność za pomocą **elementu dependsOn** lub za pomocą funkcji **odwołania.**

Usługa Resource Manager ocenia zależności pomiędzy zasobami i wdraża je w kolejności opartej na zależności. Gdy zasoby nie zależą od siebie nawzajem, usługa Resource Manager wdraża je równolegle. Wystarczy zdefiniować zależności dla zasobów, które są wdrażane w tym samym szablonie.

## <a name="dependson"></a>dependsOn

W szablonie dependsOn element umożliwia zdefiniowanie jednego zasobu jako zależne od jednego lub więcej zasobów. Jego wartość jest tablica JSON ciągów, z których każdy jest nazwą zasobu. Tablica może zawierać zasoby, które są [warunkowo wdrożone](conditional-resource-deployment.md). Gdy zasób warunkowy nie jest wdrażany, usługa Azure Resource Manager automatycznie usuwa go z wymaganych zależności.

W poniższym przykładzie przedstawiono zestaw skalowania maszyny wirtualnej, który zależy od modułu równoważenia obciążenia, sieci wirtualnej i pętli, która tworzy wiele kont magazynu. Te inne zasoby nie są wyświetlane w poniższym przykładzie, ale muszą istnieć w innym miejscu w szablonie.

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

W poprzednim przykładzie zależność jest uwzględniana w zasobach, które są tworzone za pośrednictwem pętli kopiowania o nazwie **storageLoop**. Na przykład zobacz [Tworzenie wielu wystąpień zasobów w usłudze Azure Resource Manager](copy-resources.md).

Podczas definiowania zależności, można dołączyć obszar nazw dostawcy zasobów i typ zasobu, aby uniknąć niejednoznaczności. Na przykład w celu wyjaśnienia modułu równoważenia obciążenia i sieci wirtualnej, które mogą mieć takie same nazwy jak inne zasoby, użyj następującego formatu:

```json
"dependsOn": [
  "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
  "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
]
```

Chociaż może być skłonny do użycia dependsOn do mapowania relacji między zasobami, ważne jest, aby zrozumieć, dlaczego to robisz. Na przykład, aby udokumentować, jak zasoby są ze sobą połączone, dependsOn nie jest właściwym podejściem. Nie można zbadać, które zasoby zostały zdefiniowane w dependsOn element po wdrożeniu. Za pomocą dependsOn, potencjalnie wpływ na czas wdrożenia, ponieważ Menedżer zasobów nie wdraża równolegle dwa zasoby, które mają zależność.

## <a name="child-resources"></a>Zasoby podrzędne

Właściwość resources umożliwia określenie zasobów podrzędnych, które są związane z definiowanym zasobem. Zasoby podrzędne można zdefiniować tylko pięć poziomów głębokości. Należy pamiętać, że zależność niejawnego wdrożenia nie jest tworzona między zasobem podrzędnym a zasobem nadrzędnym. Jeśli potrzebujesz zasobu podrzędnego do wdrożenia po zasobu nadrzędnego, należy jawnie stwierdzić, że zależność z dependsOn właściwości.

Każdy zasób nadrzędny akceptuje tylko niektóre typy zasobów jako zasoby podrzędne. Akceptowane typy zasobów są określone w [schemacie szablonu](https://github.com/Azure/azure-resource-manager-schemas) zasobu nadrzędnego. Nazwa podrzędnego typu zasobu zawiera nazwę nadrzędnego typu zasobu, takiego jak **Microsoft.Web/sites/config** i **Microsoft.Web/sites/extensions są zasobami podrzędnymi** witryn **firmy Microsoft.Web/sites**.

W poniższym przykładzie przedstawiono serwer SQL i bazę danych SQL. Należy zauważyć, że jawna zależność jest zdefiniowana między bazą danych SQL i serwerem SQL, nawet jeśli baza danych jest elementem podrzędnym serwera.

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

## <a name="reference-and-list-functions"></a>funkcje referencyjne i listy

[Funkcja odwołania](template-functions-resource.md#reference) umożliwia wyrażenie wyprowadzić swoją wartość z innych par nazw JSON i wartości lub zasobów środowiska uruchomieniowego. Funkcja [listy*](template-functions-resource.md#list) zwraca wartości zasobu z operacji listy.  Wyrażenia odwołania i listy niejawnie deklarują, że jeden zasób zależy od innego, gdy zasób, do którego istnieje odwołanie, jest wdrażany w tym samym szablonie i określany przez jego nazwę (nie identyfikator zasobu). Jeśli przekażesz identyfikator zasobu do funkcji odwołania lub listy, niejawne odwołanie nie zostanie utworzone.

Ogólny format funkcji referencyjnej jest:

```json
reference('resourceName').propertyPath
```

Ogólny format funkcji klawiszy listy jest:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

W poniższym przykładzie punkt końcowy usługi CDN jawnie zależy od profilu sieci CDN i niejawnie zależy od aplikacji sieci web.

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

Można użyć tego elementu lub dependsOn element do określenia zależności, ale nie trzeba używać zarówno dla tego samego zasobu zależnego. Jeśli to możliwe, należy użyć odwołania niejawne, aby uniknąć dodawania niepotrzebne zależności.

Aby dowiedzieć się więcej, zobacz [funkcję odwołania](template-functions-resource.md#reference).

## <a name="circular-dependencies"></a>Zależności cykliczne

Menedżer zasobów identyfikuje zależności cykliczne podczas sprawdzania poprawności szablonu. Jeśli zostanie wyświetlony błąd informujący, że istnieje zależność cykliczna, należy ocenić szablon, aby sprawdzić, czy zależności nie są potrzebne i można je usunąć. Jeśli usunięcie zależności nie działa, można uniknąć zależności cykliczne, przenosząc niektóre operacje wdrażania do zasobów podrzędnych, które są wdrażane po zasobach, które mają zależność cykliczną. Załóżmy na przykład, że wdrażasz dwie maszyny wirtualne, ale należy ustawić właściwości na każdej z nich, które odwołują się do drugiej. Można je wdrożyć w następującej kolejności:

1. z o.o.
2. z o.o.
3. Rozszerzenie na vm1 zależy od vm1 i vm2. Rozszerzenie ustawia wartości na vm1, które pobiera z vm2.
4. Rozszerzenie na vm2 zależy od vm1 i vm2. Rozszerzenie ustawia wartości na vm2, które pobiera z vm1.

Aby uzyskać informacje dotyczące oceny kolejności wdrażania i rozwiązywania błędów zależności, zobacz [Rozwiązywanie typowych błędów wdrażania platformy Azure za pomocą usługi Azure Resource Manager](common-deployment-errors.md).

## <a name="next-steps"></a>Następne kroki

* Aby przejść przez samouczek, zobacz [Samouczek: tworzenie szablonów usługi Azure Resource Manager z zasobami zależnymi](template-tutorial-create-templates-with-dependent-resources.md).
* Aby uzyskać zalecenia dotyczące ustawiania zależności, zobacz [Najważniejsze wskazówki dotyczące szablonów usługi Azure Resource Manager](template-best-practices.md).
* Aby dowiedzieć się więcej na temat rozwiązywania problemów z zależnościami podczas wdrażania, zobacz [Rozwiązywanie typowych błędów wdrażania platformy Azure za pomocą usługi Azure Resource Manager](common-deployment-errors.md).
* Aby dowiedzieć się więcej o tworzeniu szablonów usługi Azure Resource Manager, zobacz [Tworzenie szablonów](template-syntax.md).
* Aby uzyskać listę dostępnych funkcji w szablonie, zobacz [Funkcje szablonu](template-functions.md).

