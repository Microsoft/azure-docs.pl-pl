---
title: Funkcje szablonu - data
description: W tym artykule opisano funkcje używane w szablonie usługi Azure Resource Manager do pracy z datami.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 364b41e9e92cb248a7bd2fac5a41eb535adbf440
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084791"
---
# <a name="date-functions-for-arm-templates"></a>Funkcje daty dla szablonów ARM

Menedżer zasobów udostępnia następujące funkcje do pracy z datami w szablonach usługi Azure Resource Manager (ARM):

* [dateTimeAdd](#datetimeadd)
* [Utcnow](#utcnow)

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

Dodaje czas trwania do wartości podstawowej. Oczekuje się formatu ISO 8601.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| base | Yes | ciąg | Wartość data początkowatime dla dodania. Użyj [formatu sygnatury czasowej ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
| czas trwania | Yes | ciąg | Wartość czasu, aby dodać do podstawy. Może to być wartość ujemna. Użyj [formatu czasu trwania ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| format | Nie | ciąg | Format wyjściowy dla wyniku daty. Jeśli nie podano, używany jest format wartości podstawowej. Użyj [ciągów w formacie standardowym](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [ciągów formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Wartość zwracana

Wartość datetime, która wynika z dodania wartości czasu trwania do wartości podstawowej.

### <a name="examples"></a>Przykłady

Poniższy przykładowy szablon pokazuje różne sposoby dodawania wartości czasu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "baseTime":{
            "type":"string",
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
        "add3Years": {
            "value": "[variables('add3Years')]",
            "type": "string"
        },
        "subtract9Days": {
            "value": "[variables('subtract9Days')]",
            "type": "string"
        },
        "add1Hour": {
            "value": "[variables('add1Hour')]",
            "type": "string"
        },
    }
}
```

Gdy poprzedni szablon jest wdrażany z `2020-04-07 14:53:14Z`czasem bazowym , dane wyjściowe są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| add3Years | Ciąg | 4.07.2023 14:53:14 |
| odejmowanie9Dni | Ciąg | 29.03.2020 14:53:14 |
| dodaj 1godzina | Ciąg | 4.07.2020 15:53:14 |

Następny przykładowy szablon pokazuje, jak ustawić godzinę rozpoczęcia dla harmonogramu automatyzacji.

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
        "baseTime":{
            "type":"string",
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
        {
            "name": "[concat(parameters('omsAutomationAccountName'), '/', parameters('scheduleName'))]",
            "type": "microsoft.automation/automationAccounts/schedules",
            "apiVersion": "2015-10-31",
            "location": "eastus2",
            "tags": {
            },
            "properties": {
                "description": "Demo Scheduler",
                "startTime": "[variables('startTime')]",
                "isEnabled": "true",
                "interval": 1,
                "frequency": "hour"
            }
        }
    ],
    "outputs": {
    }
}
```

## <a name="utcnow"></a>Utcnow

`utcNow(format)`

Zwraca bieżącą (UTC) wartość datetime w określonym formacie. Jeśli nie podano żadnego formatu, używany jest format ISO 8601 (yyyyMMddTHHmmssZ). **Ta funkcja może być używana tylko w wartości domyślnej parametru.**

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| format |Nie |ciąg |Wartość zakodowana w identyfikatorze URI do konwersji na ciąg. Użyj [ciągów w formacie standardowym](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [ciągów formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Uwagi

Tej funkcji można używać tylko w wyrażeniu dla domyślnej wartości parametru. Użycie tej funkcji w dowolnym miejscu w szablonie zwraca błąd. Funkcja nie jest dozwolona w innych częściach szablonu, ponieważ zwraca inną wartość za każdym razem, gdy jest wywoływana. Wdrożenie tego samego szablonu o tych samych parametrach nie będzie niezawodnie dawać takie same wyniki.

Jeśli [użyjesz opcji, aby ponownie wdrożyć wcześniejsze pomyślne wdrożenie](rollback-on-error.md), a wcześniejsze wdrożenie zawiera parametr, który używa utcNow, parametr nie jest ponownie oceniony. Zamiast tego wartość parametru z wcześniejszego wdrożenia jest automatycznie ponownie w wdrożeniu wycofywania.

Należy zachować ostrożność ponownego rozmieszczenia szablonu, który opiera się na funkcji utcNow dla wartości domyślnej. Po ponownym wedniesieniu i nie podasz wartości parametru, funkcja jest ponownie indeksowana. Jeśli chcesz zaktualizować istniejący zasób, a nie utworzyć nowy, przekaż wartość parametru z wcześniejszego wdrożenia.

### <a name="return-value"></a>Wartość zwracana

Bieżąca wartość datatime UTC.

### <a name="examples"></a>Przykłady

Poniższy przykładowy szablon przedstawia różne formaty dla wartości datetime.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Dane wyjściowe z poprzedniego przykładu różnią się dla każdego wdrożenia, ale będą podobne do:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| utcOutput (Polski) | ciąg | 20190305T175318Z |
| utcShortOutput | ciąg | 03/05/2019 |
| utcCustomOutput | ciąg | 3 5 |

W następnym przykładzie pokazano, jak używać wartości z funkcji podczas ustawiania wartości znacznika.

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
            "apiVersion": "2018-05-01",
            "name": "[parameters('rgName')]",
            "location": "westeurope",
            "tags":{
                "createdDate": "[parameters('utcShort')]"
            },
            "properties":{}
        }
    ],
    "outputs": {
        "utcShort": {
            "type": "string",
            "value": "[parameters('utcShort')]"
        }
    }
}
```