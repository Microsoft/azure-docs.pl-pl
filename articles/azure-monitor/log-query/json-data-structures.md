---
title: Praca z ciągami w zapytaniach dziennika Azure Monitor | Microsoft Docs
description: Ten artykuł zawiera samouczek dotyczący używania Log Analytics Azure Monitor w Azure Portal do wykonywania zapytań i analizowania danych dziennika w Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 5021b815da1419052ceb4d6114fbb2bc676fe8d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90088403"
---
# <a name="working-with-json-and-data-structures-in-azure-monitor-log-queries"></a>Praca ze strukturami JSON i danych w zapytaniach dziennika Azure Monitor

> [!NOTE]
> Przed ukończeniem tej lekcji należy ukończyć pracę [z Azure Monitor Log Analytics](get-started-portal.md) i [zacząć korzystać z zapytań dziennika Azure monitor](get-started-queries.md) .

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Obiekty zagnieżdżone to obiekty, które zawierają inne obiekty w tablicy lub mapę par klucz-wartość. Te obiekty są reprezentowane jako ciągi JSON. W tym artykule opisano, jak kod JSON jest używany do pobierania danych i analizowania obiektów zagnieżdżonych.

## <a name="working-with-json-strings"></a>Praca z ciągami JSON
Służy `extractjson` do uzyskiwania dostępu do określonego elementu JSON w znanej ścieżce. Ta funkcja wymaga wyrażenia ścieżki, które używa następujących konwencji.

- _$_ Aby odwołać się do folderu głównego
- Aby odwoływać się do indeksów i elementów, jak pokazano w poniższych przykładach, użyj notacji z nawiasem lub kropką.


Użyj nawiasów dla indeksów i kropek, aby oddzielić elementy:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Jest to ten sam wynik przy użyciu tylko notacji z nawiasów kwadratowych:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Jeśli istnieje tylko jeden element, można użyć tylko notacji kropkowej:

```Kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Praca z obiektami

### <a name="parsejson"></a>parsejson
Aby uzyskać dostęp do wielu elementów w strukturze JSON, łatwiej jest uzyskać do nich dostęp jako obiekt dynamiczny. Służy `parsejson` do rzutowania danych tekstowych do obiektu dynamicznego. Po przekonwertowaniu na typ dynamiczny, za pomocą dodatkowych funkcji można analizować dane.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
Użyj `arraylength` , aby obliczyć liczbę elementów w tablicy:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Użyj `mvexpand` , aby przerwać właściwości obiektu w osobnych wierszach.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![Zrzut ekranu przedstawia hosts_0 z wartościami lokalizacji, stanu i szybkości.](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
Użyj, `buildschema` Aby uzyskać schemat, który dopuszcza wszystkie wartości obiektu:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

Dane wyjściowe są schematem w formacie JSON:
```json
{
    "hosts":
    {
        "indexer":
        {
            "location": "string",
            "rate": "int",
            "status": "string"
        }
    }
}
```
Dane wyjściowe opisują nazwy pól obiektów i ich zgodne typy danych. 

Obiekty zagnieżdżone mogą mieć różne schematy, takie jak w poniższym przykładzie:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Schemat kompilacji](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z innymi lekcjami dotyczącymi używania zapytań dzienników w Azure Monitor:

- [Operacje dotyczące ciągów](string-operations.md)
- [Operacje dotyczące daty i godziny](datetime-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Agregacje zaawansowane](advanced-aggregations.md)
- [Pisanie zapytań zaawansowanych](advanced-query-writing.md)
- [Sprzężenia](joins.md)
- [Wykresy](charts.md)
