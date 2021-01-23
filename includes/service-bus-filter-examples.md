---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 01/22/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7ddc0234accd9f434aad850d2404e2f3001c4bae
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742691"
---
## <a name="examples"></a>Przykłady

### <a name="filter-on-system-properties"></a>Filtrowanie właściwości systemu
Aby odwołać się do właściwości System w filtrze, użyj następującego formatu: `sys.<system-property-name>` . 

```csharp
sys.Label LIKE '%bus%'`
sys.messageid = 'xxxx'
sys.correlationid like 'abc-%'
```

### <a name="filter-on-message-properties"></a>Filtrowanie właściwości komunikatu
Poniżej przedstawiono przykłady użycia właściwości komunikatów w filtrze. Możesz uzyskać dostęp do właściwości wiadomości za pomocą `user.property-name` lub tylko `property-name` .

```csharp
MessageProperty = 'A'
SuperHero like 'SuperMan%'
```

### <a name="filter-on-message-properties-with-special-characters"></a>Filtrowanie właściwości wiadomości za pomocą znaków specjalnych
Jeśli nazwa właściwości komunikatu zawiera znaki specjalne, należy użyć cudzysłowu podwójnego ( `"` ), aby ująć nazwę właściwości. Na przykład jeśli nazwa właściwości to `"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName"` , użyj następującej składni w filtrze. 

```csharp
"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName" = 'account'
```

### <a name="filter-on-message-properties-with-numeric-values"></a>Filtrowanie właściwości komunikatu z wartościami liczbowymi
W poniższych przykładach pokazano, jak można użyć właściwości z wartościami liczbowymi w filtrach. 

```csharp
MessageProperty = 1
MessageProperty > 1
MessageProperty > 2.08
MessageProperty = 1 AND MessageProperty2 = 3
MessageProperty = 1 OR MessageProperty2 = 3
```

### <a name="parameter-based-filters"></a>Filtry oparte na parametrach
Poniżej przedstawiono kilka przykładów użycia filtrów opartych na parametrach. W tych przykładach `DataTimeMp` jest właściwością komunikatu typu `DateTime` i `@dtParam` jest parametrem przesłanym do filtru jako `DateTime` obiekt.

```csharp
DateTimeMp < @dtParam
DateTimeMp > @dtParam

(DateTimeMp2-DateTimeMp1) <= @timespan //@timespan is a parameter of type TimeSpan
DateTimeMp2-DateTimeMp1 <= @timespan
```

### <a name="using-in-and-not-in"></a>Używanie w i nie w

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

Aby zapoznać się z przykładem w języku C#, zobacz [przykładowe filtry tematu w witrynie GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters).


### <a name="set-rule-action-for-a-sql-filter"></a>Ustawianie akcji reguły dla filtru SQL

```csharp
// instantiate the ManagementClient
this.mgmtClient = new ManagementClient(connectionString);

// create the SQL filter
var sqlFilter = new SqlFilter("source = @stringParam");

// assign value for the parameter
sqlFilter.Parameters.Add("@stringParam", "orders");

// instantiate the Rule = Filter + Action
var filterActionRule = new RuleDescription
{
    Name = "filterActionRule",
    Filter = sqlFilter,
    Action = new SqlRuleAction("SET source='routedOrders'")
};

// create the rule on Service Bus
await this.mgmtClient.CreateRuleAsync(topicName, subscriptionName, filterActionRule);
```

### <a name="correlation-filter-using-correlationid"></a>Filtr korelacji przy użyciu korelacji

```csharp
new CorrelationFilter("Contoso");
```

Filtruje komunikaty z `CorrelationID` ustawionym na `Contoso` . 

### <a name="correlation-filter-using-system-and-user-properties"></a>Filtr korelacji przy użyciu właściwości systemu i użytkownika

```csharp
var filter = new CorrelationFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

Jest to równoważne: `sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'`

