---
title: Funkcje szablonu — logiczne
description: Opisuje funkcje, które mają być używane w szablonie Azure Resource Manager, aby określić wartości logiczne.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 8fe1c00240fc24c3c1454b118f9e0d9a9d54fe4e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84677393"
---
# <a name="logical-functions-for-arm-templates"></a>Funkcje logiczne dla szablonów ARM

Menedżer zasobów udostępnia kilka funkcji służących do dokonywania porównań w szablonach Azure Resource Manager (ARM).

* [and](#and)
* [bool](#bool)
* [if](#if)
* [niemożliwe](#not)
* [lub](#or)

## <a name="and"></a>oraz

`and(arg1, arg2, ...)`

Sprawdza, czy wszystkie wartości parametrów mają wartość true.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |boolean |Pierwsza wartość, aby sprawdzić, czy wartość jest równa true. |
| arg2 |Tak |boolean |Druga wartość do sprawdzenia, czy ma wartość true. |
| dodatkowe argumenty |Nie |boolean |Dodatkowe argumenty do sprawdzenia, czy są spełnione. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **wartość true** , jeśli wszystkie wartości mają wartość true; w przeciwnym razie **false**.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) pokazuje, jak używać funkcji logicznych.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| andExampleOutput | Wartość logiczna | Fałsz |
| orExampleOutput | Wartość logiczna | Prawda |
| notExampleOutput | Wartość logiczna | Fałsz |

## <a name="bool"></a>bool

`bool(arg1)`

Konwertuje parametr na wartość logiczną.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |ciąg lub int |Wartość do przekonwertowania na wartość logiczną. |

### <a name="return-value"></a>Wartość zwracana
Wartość logiczna przekonwertowanej wartości.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) pokazuje, jak używać bool z ciągiem lub liczbą całkowitą.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "trueString": {
            "value": "[bool('true')]",
            "type" : "bool"
        },
        "falseString": {
            "value": "[bool('false')]",
            "type" : "bool"
        },
        "trueInt": {
            "value": "[bool(1)]",
            "type" : "bool"
        },
        "falseInt": {
            "value": "[bool(0)]",
            "type" : "bool"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| trueString | Wartość logiczna | Prawda |
| falseString | Wartość logiczna | Fałsz |
| trueInt | Wartość logiczna | Prawda |
| falseInt | Wartość logiczna | Fałsz |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Zwraca wartość na podstawie tego, czy warunek ma wartość true lub false.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| rozgrzewa |Tak |boolean |Wartość, aby sprawdzić, czy jest to wartość true lub false. |
| trueValue |Tak | ciąg, int, Object lub Array |Wartość, która ma zostać zwrócona, gdy warunek ma wartość true. |
| falseValue |Tak | ciąg, int, Object lub Array |Wartość, która ma zostać zwrócona, gdy warunek ma wartość false. |

### <a name="return-value"></a>Wartość zwracana

Zwraca drugi parametr, gdy pierwszy parametr ma **wartość true**; w przeciwnym razie zwraca trzeci parametr.

### <a name="remarks"></a>Uwagi

Gdy warunek ma **wartość true**, Szacowana jest tylko wartość true. Gdy warunek ma **wartość false**, Szacowana jest tylko wartość false. Za pomocą funkcji **if** można uwzględnić wyrażenia, które są warunkowo prawidłowe. Na przykład można odwołać się do zasobu, który istnieje w ramach jednego warunku, ale nie pod drugim warunkiem. Przykład warunkowego szacowania wyrażeń przedstawiono w poniższej sekcji.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) pokazuje, jak używać `if` funkcji.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "yesOutput": {
            "type": "string",
            "value": "[if(equals('a', 'a'), 'yes', 'no')]"
        },
        "noOutput": {
            "type": "string",
            "value": "[if(equals('a', 'b'), 'yes', 'no')]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| yesOutput | Ciąg | tak |
| NoOutput | Ciąg | nie |
| objectOutput | Obiekt | {"test": "wartość1"} |

Poniższy [przykładowy szablon](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) pokazuje, jak używać tej funkcji z wyrażeniami, które są warunkowo prawidłowe.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "logAnalytics": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "condition": "[not(empty(parameters('logAnalytics')))]",
            "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2017-03-30",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
                },
                "protectedSettings": {
                    "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
                }
            }
        }
    ],
    "outputs": {
        "mgmtStatus": {
            "type": "string",
            "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
        }
    }
}
```

## <a name="not"></a>not

`not(arg1)`

Konwertuje wartość logiczną na wartość odwrotną.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |boolean |Wartość do konwersji. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **wartość true** , jeśli parametr ma **wartość false**. Zwraca **wartość false** , jeśli parametr ma **wartość true**.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) pokazuje, jak używać funkcji logicznych.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| andExampleOutput | Wartość logiczna | Fałsz |
| orExampleOutput | Wartość logiczna | Prawda |
| notExampleOutput | Wartość logiczna | Fałsz |

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) używa **nie** z [równą](template-functions-comparison.md#equals).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| checkNotEquals | Wartość logiczna | Prawda |

## <a name="or"></a>lub

`or(arg1, arg2, ...)`

Sprawdza, czy parametr ma wartość true.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |boolean |Pierwsza wartość, aby sprawdzić, czy wartość jest równa true. |
| arg2 |Tak |boolean |Druga wartość do sprawdzenia, czy ma wartość true. |
| dodatkowe argumenty |Nie |boolean |Dodatkowe argumenty do sprawdzenia, czy są spełnione. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **wartość true** , jeśli dowolna wartość jest równa true; w przeciwnym razie **false**.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) pokazuje, jak używać funkcji logicznych.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| andExampleOutput | Wartość logiczna | Fałsz |
| orExampleOutput | Wartość logiczna | Prawda |
| notExampleOutput | Wartość logiczna | Fałsz |

## <a name="next-steps"></a>Następne kroki

* Opis sekcji w szablonie Azure Resource Manager można znaleźć w temacie [Omówienie struktury i składni szablonów usługi ARM](template-syntax.md).

