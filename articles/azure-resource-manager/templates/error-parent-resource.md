---
title: Błędy zasobów nadrzędnych
description: Opisuje sposób rozwiązywania błędów podczas pracy z zasobem nadrzędnym w szablonie Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.openlocfilehash: 474cb85d16382136e24e5502b87ba8a1a65488ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84673664"
---
# <a name="resolve-errors-for-parent-resources"></a>Rozwiązywanie błędów dotyczących zasobów nadrzędnych

W tym artykule opisano błędy, które mogą wystąpić podczas wdrażania zasobu zależnego od zasobu nadrzędnego.

## <a name="symptom"></a>Objaw

Podczas wdrażania zasobu, który jest elementem podrzędnym innego zasobu, może zostać wyświetlony następujący błąd:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Przyczyna

Jeśli jeden zasób jest elementem podrzędnym innego zasobu, musi istnieć zasób nadrzędny przed utworzeniem zasobu podrzędnego. Nazwa zasobu podrzędnego definiuje połączenie z zasobem nadrzędnym. Nazwa zasobu podrzędnego jest w formacie `<parent-resource-name>/<child-resource-name>` . Na przykład SQL Database może być zdefiniowana jako:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Jeśli serwer i baza danych programu zostały wdrożone w tym samym szablonie, ale nie określisz zależności na serwerze, wdrożenie bazy danych może się zacząć przed wdrożeniem serwera.

Jeśli zasób nadrzędny już istnieje i nie jest wdrożony w tym samym szablonie, ten błąd występuje, gdy Menedżer zasobów nie można skojarzyć zasobu podrzędnego z elementem nadrzędnym. Ten błąd może wystąpić, gdy zasób podrzędny nie ma poprawnego formatu lub zasób podrzędny jest wdrażany w grupie zasobów, która jest inna niż grupa zasobów dla zasobu nadrzędnego.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, gdy zasoby nadrzędne i podrzędne są wdrażane w tym samym szablonie, należy uwzględnić zależność.

```json
"dependsOn": [
  "[variables('databaseServerName')]"
]
```

Aby rozwiązać ten problem, gdy zasób nadrzędny został wcześniej wdrożony w innym szablonie, nie ustawisz zależności. Zamiast tego należy wdrożyć element podrzędny w tej samej grupie zasobów i podać nazwę zasobu nadrzędnego.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "sqlServerName": {
      "type": "string"
    },
    "databaseName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers/databases",
      "apiVersion": "2014-04-01",
      "name": "[concat(parameters('sqlServerName'), '/', parameters('databaseName'))]",
      "location": "[resourceGroup().location]",
      "properties": {
        "collation": "SQL_Latin1_General_CP1_CI_AS",
        "edition": "Basic"
      }
    }
  ],
  "outputs": {}
}
```

Aby uzyskać więcej informacji, zobacz [Definiowanie kolejności wdrażania zasobów w szablonach Azure Resource Manager](define-resource-dependency.md).