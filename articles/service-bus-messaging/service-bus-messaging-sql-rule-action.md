---
title: Składnia akcji SQL Azure Service Bus reguły subskrypcji | Microsoft Docs
description: Ten artykuł zawiera odwołanie do składni akcji reguły SQL. Akcje są zapisywane w składni opartej na języku SQL, która jest wykonywana względem wiadomości.
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: 606281d42d5598d7f73312990d3a19775a202c08
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632815"
---
# <a name="subscription-rule-sql-action-syntax"></a>Składnia akcji SQL reguły subskrypcji

*Akcja SQL* służy do manipulowania metadanymi komunikatów po wybraniu komunikatu przez filtr reguły subskrypcji. Jest to wyrażenie tekstowe, które jest chude w podzestawie standardu SQL-92. Wyrażenia akcji są używane z `sqlExpression` elementem właściwości "Action" Service Bus `Rule` w [szablonie Azure Resource Manager](service-bus-resource-manager-namespace-topic-with-rule.md)lub z `az servicebus topic subscription rule create` argumentem polecenia platformy Azure [`--action-sql-expression`](/cli/azure/servicebus/topic/subscription/rule#az_servicebus_topic_subscription_rule_create) , a także z kilkoma funkcjami zestawu SDK, które umożliwiają zarządzanie regułami subskrypcji.
  
  
```  
<statements> ::=
    <statement> [, ...n]  
  
```  
  
```  
<statement> ::=
    <action> [;]
    Remarks
    -------
    Semicolon is optional.  
  
```  
  
```  
<action> ::=
    SET <property> = <expression>
    REMOVE <property>  
```

```
<expression> ::=
    <constant>
    | <function>
    | <property>
    | <expression> { + | - | * | / | % } <expression>
    | { + | - } <expression>
    | ( <expression> )
``` 

```
<property> := 
    [<scope> .] <property_name>
``` 
  
## <a name="arguments"></a>Argumenty  
  
-   `<scope>` jest opcjonalnym ciągiem wskazującym zakres `<property_name>` . Prawidłowe wartości to `sys` lub `user` . `sys`Wartość wskazuje zakres systemowy, gdzie `<property_name>` jest publiczną nazwą właściwości [klasy BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` wskazuje zakres użytkownika `<property_name>` , gdzie jest kluczem słownika [klasy BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) . `user` zakres jest zakresem domyślnym, jeśli `<scope>` nie jest określony.  
  
### <a name="remarks"></a>Uwagi  

Próba uzyskania dostępu do nieistniejącej właściwości systemu jest błędem, podczas gdy próba uzyskania dostępu do nieistniejącej właściwości użytkownika nie jest błędem. Zamiast tego nieistniejąca Właściwość użytkownika jest obliczana wewnętrznie jako nieznana wartość. Nieznana wartość jest traktowana specjalnie podczas obliczania operatora.  
  
## <a name="property_name"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumenty  
 `<regular_identifier>` jest ciągiem przedstawionym przez następujące wyrażenie regularne:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
 Oznacza to dowolny ciąg, który rozpoczyna się od litery, po której następuje jeden lub więcej podkreśleń/liter/cyfr.  
  
 `[:IsLetter:]` oznacza dowolny znak Unicode, który jest kategoryzowany jako litera Unicode. `System.Char.IsLetter(c)` zwraca `true` wartość `c` , jeśli jest literą Unicode.  
  
 `[:IsDigit:]` oznacza dowolny znak Unicode, który jest kategoryzowany jako cyfra dziesiętna. `System.Char.IsDigit(c)` zwraca `true` wartość `c` , jeśli jest cyfrą Unicode.  
  
 A `<regular_identifier>` nie może być zastrzeżonym słowem kluczowym.  
  
 `<delimited_identifier>` jest dowolnym ciągiem, który jest ujęty w nawiasy kwadratowe ([]). Prawy nawias kwadratowy jest reprezentowany jako dwa prawy nawias kwadratowy. Poniżej przedstawiono przykłady `<delimited_identifier>` :  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>` to dowolny ciąg, który jest ujęty w znaki podwójnego cudzysłowu. Podwójny cudzysłów w identyfikatorze jest reprezentowany jako dwa podwójne cudzysłowy. Nie zaleca się używania identyfikatorów w cudzysłowie, ponieważ można je łatwo pomylić z stałą typu String. Jeśli to możliwe, należy użyć oddzielonego identyfikatora. Oto przykład `<quoted_identifier>` :  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>znaczne  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Uwagi
  
 `<pattern>` musi być wyrażeniem, które jest oceniane jako ciąg. Jest używany jako wzorzec dla operatora LIKE.      Może zawierać następujące symbole wieloznaczne:  
  
-   `%`: Dowolny ciąg składający się z zero lub więcej znaków.  
  
-   `_`: Dowolny pojedynczy znak.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Uwagi
  
 `<escape_char>` musi być wyrażeniem, które jest oceniane jako ciąg o długości 1. Jest używany jako znak ucieczki dla operatora LIKE.  
  
 Na przykład `property LIKE 'ABC\%' ESCAPE '\'` pasuje do `ABC%` ciągu, który rozpoczyna się od `ABC` .  
  
## <a name="constant"></a> — stała  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumenty  
  
-   `<integer_constant>` jest ciągiem liczb, które nie są ujęte w cudzysłów i nie zawierają punktów dziesiętnych. Wartości są przechowywane jako `System.Int64` wewnętrznie i zgodne z tym samym zakresem.  
  
     Poniżej przedstawiono przykłady długich stałych:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` jest ciągiem liczb, które nie są ujęte w cudzysłów i zawierają separator dziesiętny. Wartości są przechowywane jako `System.Double` wewnętrznie i zgodne z tym samym zakresem/dokładnością.  
  
     W przyszłych wersjach ten numer może być przechowywany w innym typie danych do obsługi dokładnej semantyki liczb, dlatego nie należy polegać na tym, że jest to typ danych bazowych `System.Double` `<decimal_constant>` .  
  
     Poniżej przedstawiono przykłady stałych dziesiętnych:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` to liczba zapisywana w notacji wykładniczej. Wartości są przechowywane jako `System.Double` wewnętrznie i zgodne z tym samym zakresem/dokładnością. Poniżej przedstawiono przykłady przybliżonych stałych liczbowych:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="boolean_constant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Uwagi
  
Stałe logiczne są reprezentowane przez słowa kluczowe `TRUE` lub `FALSE` . Wartości są przechowywane jako `System.Boolean` .  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Uwagi
  
Stałe ciągów są ujęte w znaki pojedynczego cudzysłowu i zawierają wszystkie prawidłowe znaki Unicode. Pojedynczy cudzysłów osadzony w stałej ciągu jest reprezentowany jako dwa znaki pojedynczego cudzysłowu.  
  
## <a name="function"></a> — funkcja  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Uwagi  

`newid()`Funkcja zwraca obiekt **System. GUID** wygenerowany przez `System.Guid.NewGuid()` metodę.  
  
`property(name)`Funkcja zwraca wartość właściwości, do której odwołuje się `name` . `name`Wartość może być dowolnym prawidłowym wyrażeniem zwracającym wartość ciągu.  
  
## <a name="considerations"></a>Zagadnienia do rozważenia

- ZESTAW służy do tworzenia nowej właściwości lub aktualizowania wartości istniejącej właściwości.
- Usuń służy do usuwania właściwości.
- Ustaw wykonuje niejawną konwersję, jeśli jest to możliwe, gdy typ wyrażenia i typ istniejącej właściwości są różne.
- Akcja kończy się niepowodzeniem, jeśli odwołuje się do nieistniejących właściwości systemu.
- Akcja nie powiedzie się, jeśli istnieją odwołania do nieistniejących właściwości użytkownika.
- Nieistniejąca Właściwość użytkownika jest szacowana jako "nieznana" wewnętrznie, przy użyciu tej samej semantyki jako [sqlfilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) podczas oceniania operatorów.

## <a name="next-steps"></a>Następne kroki

- [Klasa SQLRuleAction (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [Klasa SQLRuleAction (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlruleaction)
- [SqlRuleAction — Klasa (Java)](/java/api/com.microsoft.azure.servicebus.rules.sqlruleaction)
- [SqlRuleAction (JavaScript)](/javascript/api/@azure/service-bus/sqlruleaction)
- [AZ ServiceBus — reguła subskrypcji tematu](/cli/azure/servicebus/topic/subscription/rule)
- [New-AzServiceBusRule](/powershell/module/az.servicebus/new-azservicebusrule)