---
title: Funkcje szablonu — logiczne
description: Opisuje funkcje, które mają być używane w szablonie Azure Resource Manager (szablon ARM) do określenia wartości logicznych.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 27d94f10374daf0b9a351469579a5eb659cf5445
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96920484"
---
# <a name="logical-functions-for-arm-templates"></a>Funkcje logiczne dla szablonów ARM

Menedżer zasobów udostępnia kilka funkcji do dokonywania porównań w szablonie Azure Resource Manager (szablon ARM):

* [and](#and)
* [bool](#bool)
* [false](#false)
* [if](#if)
* [niemożliwe](#not)
* [lub](#or)
* [oznacza](#true)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="and"></a>oraz

`and(arg1, arg2, ...)`

Sprawdza, czy wszystkie wartości parametrów mają wartość true. `and`Funkcja nie jest obsługiwana w Bicep. `&&`Zamiast tego użyj operatora.

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

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "type": "bool",
      "value": "[and(bool('true'), bool('false'))]"
    },
    "orExampleOutput": {
      "type": "bool",
      "value": "[or(bool('true'), bool('false'))]"
    },
    "notExampleOutput": {
      "type": "bool",
      "value": "[not(bool('true'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

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

### <a name="remarks"></a>Uwagi

Można również użyć wartości [true ()](#true) i [false ()](#false) , aby uzyskać wartość logiczną.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) pokazuje, jak używać bool z ciągiem lub liczbą całkowitą.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "trueString": {
      "type": "bool",
      "value": "[bool('true')]",
    },
    "falseString": {
      "type": "bool",
      "value": "[bool('false')]"
    },
    "trueInt": {
      "type": "bool",
      "value": "[bool(1)]"
    },
    "falseInt": {
      "type": "bool",
      "value": "[bool(0)]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output trueString bool = bool('true')
output falseString bool = bool('false')
output trueInt bool = bool(1)
output falseInt bool = bool(0)
```

---
Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| trueString | Wartość logiczna | Prawda |
| falseString | Wartość logiczna | Fałsz |
| trueInt | Wartość logiczna | Prawda |
| falseInt | Wartość logiczna | Fałsz |

## <a name="false"></a>fałsz

`false()`

Zwraca wartość false. `false`Funkcja jest niedostępna w Bicep.  `false`Zamiast tego użyj słowa kluczowego.

### <a name="parameters"></a>Parametry

Funkcja false nie akceptuje żadnych parametrów.

### <a name="return-value"></a>Wartość zwracana

Wartość logiczna, która ma zawsze wartość false.

### <a name="example"></a>Przykład

Poniższy przykład zwraca wartość false.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "falseOutput": {
      "type": "bool",
      "value": "[false()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output falseOutput bool = false
```

---

Dane wyjściowe z poprzedniego przykładu to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| falseOutput | Wartość logiczna | Fałsz |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Zwraca wartość na podstawie tego, czy warunek ma wartość true lub false. `if`Funkcja nie jest obsługiwana w Bicep. `?:`Zamiast tego użyj operatora.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| rozgrzewa |Tak |boolean |Wartość, aby sprawdzić, czy jest to wartość true lub false. |
| trueValue |Tak | ciąg, int, Object lub Array |Wartość, która ma zostać zwrócona, gdy warunek ma wartość true. |
| falseValue |Tak | ciąg, int, Object lub Array |Wartość, która ma zostać zwrócona, gdy warunek ma wartość false. |

### <a name="return-value"></a>Wartość zwracana

Zwraca drugi parametr, gdy pierwszy parametr ma **wartość true**; w przeciwnym razie zwraca trzeci parametr.

### <a name="remarks"></a>Uwagi

Gdy warunek ma **wartość true**, Szacowana jest tylko wartość true. Gdy warunek ma **wartość false**, Szacowana jest tylko wartość false. Za pomocą `if` funkcji można uwzględnić wyrażenia, które są warunkowo prawidłowe. Na przykład można odwołać się do zasobu, który istnieje w ramach jednego warunku, ale nie pod drugim warunkiem. Przykład warunkowego szacowania wyrażeń przedstawiono w poniższej sekcji.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) pokazuje, jak używać `if` funkcji.

# <a name="json"></a>[JSON](#tab/json)

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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output yesOutput string = 'a' == 'a' ? 'yes' : 'no'
output noOutput string = 'a' == 'b' ? 'yes' : 'no'
output objectOutput object = 'a' == 'a' ? json('{"test": "value1"}') : json('null')
```

---

Dane wyjściowe z poprzedniego przykładu to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| yesOutput | Ciąg | tak |
| NoOutput | Ciąg | nie |
| objectOutput | Obiekt | {"test": "wartość1"} |

Poniższy [przykładowy szablon](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) pokazuje, jak używać tej funkcji z wyrażeniami, które są warunkowo prawidłowe.

# <a name="json"></a>[JSON](#tab/json)

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
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "apiVersion": "2017-03-30",
      "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
      "location": "[parameters('location')]",
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> `Conditions` nie zostały jeszcze zaimplementowane w Bicep. Zobacz [warunki](https://github.com/Azure/bicep/issues/186).

---

## <a name="not"></a>not

`not(arg1)`

Konwertuje wartość logiczną na wartość odwrotną. `not`Funkcja nie jest obsługiwana w Bicep. `!`Zamiast tego użyj operatora.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |boolean |Wartość do konwersji. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **wartość true** , jeśli parametr ma **wartość false**. Zwraca **wartość false** , jeśli parametr ma **wartość true**.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) pokazuje, jak używać funkcji logicznych.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "type": "bool",
      "value": "[and(bool('true'), bool('false'))]",
    },
    "orExampleOutput": {
      "type": "bool",
      "value": "[or(bool('true'), bool('false'))]"
    },
    "notExampleOutput": {
      "type": "bool",
      "value": "[not(bool('true'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

Dane wyjściowe z poprzedniego przykładu to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| andExampleOutput | Wartość logiczna | Fałsz |
| orExampleOutput | Wartość logiczna | Prawda |
| notExampleOutput | Wartość logiczna | Fałsz |

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) używa **nie** z [równą](template-functions-comparison.md#equals).

# <a name="json"></a>[JSON](#tab/json)

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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output checkNotEquals bool = !(1 == 2)
```

---

Dane wyjściowe z poprzedniego przykładu to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| checkNotEquals | Wartość logiczna | Prawda |

## <a name="or"></a>lub

`or(arg1, arg2, ...)`

Sprawdza, czy parametr ma wartość true. `or`Funkcja nie jest obsługiwana w Bicep. `||`Zamiast tego użyj operatora.

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

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

Dane wyjściowe z poprzedniego przykładu to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| andExampleOutput | Wartość logiczna | Fałsz |
| orExampleOutput | Wartość logiczna | Prawda |
| notExampleOutput | Wartość logiczna | Fałsz |

## <a name="true"></a>true

`true()`

Zwraca wartość true. `true`Funkcja jest niedostępna w Bicep.  `true`Zamiast tego użyj słowa kluczowego.

### <a name="parameters"></a>Parametry

Funkcja true nie akceptuje żadnych parametrów. `true`Funkcja jest niedostępna w Bicep.  `true`Zamiast tego użyj słowa kluczowego.

### <a name="return-value"></a>Wartość zwracana

Wartość logiczna, która zawsze ma wartość true.

### <a name="example"></a>Przykład

Poniższy przykład zwraca wartość true.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "trueOutput": {
      "type": "bool",
      "value": "[true()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output trueOutput bool = true
```

---

Dane wyjściowe z poprzedniego przykładu to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| trueOutput | Wartość logiczna | Prawda |

## <a name="next-steps"></a>Następne kroki

* Opis sekcji w szablonie ARM można znaleźć [w temacie Omówienie struktury i składni szablonów usługi ARM](template-syntax.md).
