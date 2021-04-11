---
title: Funkcje szablonu — Data
description: Opisuje funkcje, które mają być używane w szablonie Azure Resource Manager (szablon ARM) do pracy z datami.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: abff5b86ad1e10042596b11f613cdb594e307209
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889930"
---
# <a name="date-functions-for-arm-templates"></a>Funkcje daty dla szablonów ARM

Menedżer zasobów udostępnia następujące funkcje do pracy z datami w szablonie Azure Resource Manager (szablon ARM):

* [dateTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

Dodaje czas trwania do wartości podstawowej. Oczekiwano formatu ISO 8601.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| base | Tak | ciąg | Wartość daty i godziny dla dodania. Użyj [formatu znacznika czasu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
| czas trwania | Tak | ciąg | Wartość czasu, która ma zostać dodana do bazy. Może być wartością ujemną. Użyj [formatu czasu trwania ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| format | Nie | ciąg | Format danych wyjściowych dla wyniku daty i godziny. Jeśli nie zostanie podany, zostanie użyty format wartości podstawowej. Użyj [standardowych ciągów formatu](/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [niestandardowych ciągów formatu](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Wartość zwracana

Wartość daty/godziny, która wynika z dodania wartości czasu trwania do wartości podstawowej.

### <a name="examples"></a>Przykłady

Poniższy przykładowy szablon pokazuje różne sposoby dodawania wartości czasu.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "baseTime": {
      "type": "string",
      "defaultValue": "[utcNow('u')]"
    }
  },
  "variables": {
    "add3Years": "[dateTimeAdd(parameters('baseTime'), 'P3Y')]",
    "subtract9Days": "[dateTimeAdd(parameters('baseTime'), '-P9D')]",
    "add1Hour": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
  },
  "resources": [],
  "outputs": {
    "add3YearsOutput": {
      "value": "[variables('add3Years')]",
      "type": "string"
    },
    "subtract9DaysOutput": {
      "value": "[variables('subtract9Days')]",
      "type": "string"
    },
    "add1HourOutput": {
      "value": "[variables('add1Hour')]",
      "type": "string"
    },
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param baseTime string = utcNow('u')

var add3Years = dateTimeAdd(baseTime, 'P3Y')
var subtract9Days = dateTimeAdd(baseTime, '-P9D')
var add1Hour = dateTimeAdd(baseTime, 'PT1H')

output add3YearsOutput string = add3Years
output subtract9DaysOutput string = subtract9Days
output add1HourOutput string = add1Hour
```

---

Gdy poprzedni szablon zostanie wdrożony z czasem podstawowym programu `2020-04-07 14:53:14Z` , dane wyjściowe są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| add3YearsOutput | Ciąg | 4/7/2023 2:53:14 PM |
| subtract9DaysOutput | Ciąg | 3/29/2020 2:53:14 PM |
| add1HourOutput | Ciąg | 4/7/2020 3:53:14 PM |

Następny przykładowy szablon pokazuje, jak ustawić godzinę rozpoczęcia harmonogramu automatyzacji.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "omsAutomationAccountName": {
      "type": "string",
      "defaultValue": "demoAutomation",
      "metadata": {
        "description": "Use an existing Automation account."
      }
    },
    "scheduleName": {
      "type": "string",
      "defaultValue": "demoSchedule1",
      "metadata": {
        "description": "Name of the new schedule."
      }
    },
    "baseTime": {
      "type": "string",
      "defaultValue": "[utcNow('u')]",
      "metadata": {
        "description": "Schedule will start one hour from this time."
      }
    }
  },
  "variables": {
    "startTime": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
  },
  "resources": [
    ...
    {
      "type": "Microsoft.Automation/automationAccounts/schedules",
      "apiVersion": "2015-10-31",
      "name": "[concat(parameters('omsAutomationAccountName'), '/', parameters('scheduleName'))]",

      "properties": {
        "description": "Demo Scheduler",
        "startTime": "[variables('startTime')]",
        "interval": 1,
        "frequency": "Hour"
      }
    }
  ],
  "outputs": {
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param omsAutomationAccountName string = 'demoAutomation'
param scheduleName string = 'demSchedule1'
param baseTime string = utcNow('u')

var startTime = dateTimeAdd(baseTime, 'PT1H')

...

resource scheduler 'Microsoft.Automation/automationAccounts/schedules@2015-10-31' = {
  name: concat(omsAutomationAccountName, '/', scheduleName)
  properties: {
    description: 'Demo Scheduler'
    startTime: startTime
    interval: 1
    frequency: 'Hour'
  }
}
```

---

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Zwraca bieżącą wartość daty/godziny w określonym formacie. Jeśli format nie jest podany, `yyyyMMddTHHmmssZ` używany jest format ISO 8601 (). **Tej funkcji można użyć tylko w wartości domyślnej dla parametru.**

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| format |Nie |ciąg |Zakodowana wartość identyfikatora URI w celu przekonwertowania na ciąg. Użyj [standardowych ciągów formatu](/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [niestandardowych ciągów formatu](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Uwagi

Tej funkcji można użyć tylko w wyrażeniu dla wartości domyślnej parametru. Użycie tej funkcji w dowolnym miejscu w szablonie powoduje zwrócenie błędu. Funkcja nie jest dozwolona w innych częściach szablonu, ponieważ zwraca inną wartość za każdym razem, gdy jest wywoływana. Wdrożenie tego samego szablonu z tymi samymi parametrami nie da niezawodnego wygenerowania tych samych wyników.

W przypadku użycia [opcji Wycofaj przy błędzie](rollback-on-error.md) do wcześniejszego pomyślnego wdrożenia, a wcześniejsze wdrożenie zawiera parametr, który używa UtcNow, parametr nie jest ponownie obliczany. Zamiast tego wartość parametru z wcześniejszego wdrożenia zostanie automatycznie ponownie użyta w ramach wdrożenia wycofywania.

Należy zachować ostrożność wdrażania szablonu, który opiera się na funkcji utcNow dla wartości domyślnej. Po ponownym wdrożeniu i niepodaniu wartości parametru funkcja jest ponownie Szacowana. Jeśli chcesz zaktualizować istniejący zasób zamiast tworzyć nowe, przekaż wartość parametru z wcześniejszego wdrożenia.

### <a name="return-value"></a>Wartość zwracana

Bieżąca wartość DateTime czasu UTC.

### <a name="examples"></a>Przykłady

Poniższy przykładowy szablon pokazuje różne formaty wartości daty i godziny.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "utcValue": {
      "type": "string",
      "defaultValue": "[utcNow()]"
    },
    "utcShortValue": {
      "type": "string",
      "defaultValue": "[utcNow('d')]"
    },
    "utcCustomValue": {
      "type": "string",
      "defaultValue": "[utcNow('M d')]"
    }
  },
  "resources": [
  ],
  "outputs": {
    "utcOutput": {
      "type": "string",
      "value": "[parameters('utcValue')]"
    },
    "utcShortOutput": {
      "type": "string",
      "value": "[parameters('utcShortValue')]"
    },
    "utcCustomOutput": {
      "type": "string",
      "value": "[parameters('utcCustomValue')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param utcValue string = utcNow()
param utcShortValue string = utcNow('d')
param utcCustomValue string = utcNow('M d')

output utcOutput string = utcValue
output utcShortOutput string = utcShortValue
output utcCustomOutput string = utcCustomValue
```

---

Dane wyjściowe z powyższego przykładu różnią się w zależności od wdrożenia, ale będą podobne do następujących:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| utcOutput | ciąg | 20190305T175318Z |
| utcShortOutput | ciąg | 03/05/2019 |
| utcCustomOutput | ciąg | 3 5 |

W następnym przykładzie pokazano, jak używać wartości z funkcji podczas ustawiania wartości tagu.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "utcShort": {
      "type": "string",
      "defaultValue": "[utcNow('d')]"
    },
    "rgName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2020-10-01",
      "name": "[parameters('rgName')]",
      "location": "westeurope",
      "tags": {
        "createdDate": "[parameters('utcShort')]"
      },
      "properties": {}
    }
  ],
  "outputs": {
    "utcShortOutput": {
      "type": "string",
      "value": "[parameters('utcShort')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param utcShort string = utcNow('d')
param rgName string

resource myRg 'Microsoft.Resources/resourceGroups@2020-10-01' = {
  name: rgName
  location: 'westeurope'
  tags: {
    createdDate: utcShort
  }
}

output utcShortOutput string = utcShort
```

---

## <a name="next-steps"></a>Następne kroki

* Opis sekcji w szablonie ARM można znaleźć [w temacie Omówienie struktury i składni szablonów usługi ARM](template-syntax.md).
