---
title: Składnia i wyrażenia szablonu
description: Zawiera opis deklaratywnej składni JSON dla szablonów Azure Resource Manager (szablony ARM).
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 44a386ed849771dfba717c8d1414e64422d0c7bd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97797047"
---
# <a name="syntax-and-expressions-in-arm-templates"></a>Składnia i wyrażenia w szablonach ARM

Podstawowa składnia szablonu Azure Resource Manager (szablon ARM) to JavaScript Object Notation (JSON). Można jednak użyć wyrażeń do rozszerania wartości JSON dostępnych w ramach szablonu.  Wyrażenia zaczynają się i kończą nawiasami kwadratowymi: odpowiednio `[` i `]`. Wartość wyrażenia jest obliczana podczas wdrażania szablonu. Wyrażenie może zwrócić ciąg, liczbę całkowitą, wartość logiczną, tablicę lub obiekt.

Wyrażenie szablonu nie może zawierać więcej niż 24 576 znaków.

## <a name="use-functions"></a>Korzystanie z funkcji

Azure Resource Manager udostępnia [funkcje](template-functions.md) , których można użyć w szablonie. Poniższy przykład przedstawia wyrażenie, które używa funkcji w wartości domyślnej parametru:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

W wyrażeniu składnia `resourceGroup()` wywołuje jedną z funkcji, które Menedżer zasobów zapewnia do użycia w ramach szablonu. W tym przypadku jest to funkcja obiektu [resources](template-functions-resource.md#resourcegroup) . Podobnie jak w języku JavaScript, wywołania funkcji są sformatowane jako `functionName(arg1,arg2,arg3)` . Składnia `.location` Pobiera jedną właściwość z obiektu zwróconego przez tę funkcję.

W funkcjach szablonów i ich parametrach nie jest rozróżniana wielkość liter. Na przykład Menedżer zasobów jest rozpoznawana `variables('var1')` i `VARIABLES('VAR1')` jako taka sama. W przypadku oceny, chyba że funkcja wyraźnie modyfikuje wielkość liter (na przykład `toUpper` lub `toLower` ), funkcja zachowuje wielkość liter. Niektóre typy zasobów mogą mieć wymagania dotyczące wielkości liter, które są niezależne od sposobu oceniania funkcji.

Aby przekazać wartość ciągu jako parametr do funkcji, należy użyć apostrofów.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

Większość funkcji działa tak samo, niezależnie od tego, czy zostały wdrożone w grupie zasobów, subskrypcji, grupie zarządzania czy dzierżawie. Następujące funkcje mają ograniczenia oparte na zakresie:

* Grupa [zasobów — może](template-functions-resource.md#resourcegroup) być używana tylko we wdrożeniach w ramach grupy.
* [ResourceID](template-functions-resource.md#resourceid) — można użyć w dowolnym zakresie, ale prawidłowe parametry zmieniają się w zależności od zakresu.
* [subskrypcja](template-functions-resource.md#subscription) — może być używana tylko we wdrożeniach w grupie zasobów lub subskrypcji.

## <a name="escape-characters"></a>Znaki ucieczki

Aby ciąg literału rozpoczynał się od lewego nawiasu `[` i kończyć się nawiasem klamrowym `]` , ale nie powinien być interpretowany jako wyrażenie, Dodaj dodatkowy nawias klamrowy, aby uruchomić ciąg `[[` . Na przykład zmienna:

```json
"demoVar1": "[[test value]"
```

Jest rozpoznawany jako `[test value]` .

Jeśli jednak ciąg literału nie kończy się nawiasem, nie należy określać pierwszego nawiasu. Na przykład zmienna:

```json
"demoVar2": "[test] value"
```

Jest rozpoznawany jako `[test] value` .

Aby wypróbować podwójne cudzysłowy w wyrażeniu, takie jak dodanie obiektu JSON w szablonie, użyj ukośnika odwrotnego.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

Podczas przekazywania wartości parametrów użycie znaków ucieczki zależy od tego, gdzie określono wartość parametru. Jeśli ustawisz wartość domyślną w szablonie, potrzebujesz dodatkowego nawiasu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1":{
            "type": "string",
            "defaultValue": "[[test value]"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput": {
            "type": "string",
            "value": "[parameters('demoParam1')]"
        }
    }
}
```

Jeśli zostanie użyta wartość domyślna, szablon zwraca `[test value]` .

Jednak w przypadku przekazania wartości parametru za pomocą wiersza polecenia znaki są interpretowane dosłownie. Wdrażanie poprzedniego szablonu przy użyciu:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[[test value]"
```

Zwraca wartość `[[test value]`. Zamiast tego należy użyć:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[test value]"
```

To samo formatowanie jest stosowane podczas przekazywania wartości z pliku parametrów. Znaki są interpretowane dosłownie. Gdy jest używany z poprzednim szablonem, następujący plik parametrów zwraca `[test value]` :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1": {
            "value": "[test value]"
        }
   }
}
```

## <a name="null-values"></a>Wartości null

Aby ustawić dla właściwości wartość null, można wpisać `null` lub `[json('null')]`. [Funkcja JSON](template-functions-object.md#json) zwraca pusty obiekt, gdy podajesz `null` jako parametr. W obu przypadkach Menedżer zasobów szablony traktują go tak, jakby właściwość nie jest obecna.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z pełną listą funkcji szablonu, zobacz [funkcje szablonu ARM](template-functions.md).
* Aby uzyskać więcej informacji na temat plików szablonów, zobacz [Omówienie struktury i składni szablonów usługi ARM](template-syntax.md).
