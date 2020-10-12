---
title: Application Insights platformy Azure | Microsoft Docs
description: Skorelowanie danych z Application Insights z innymi zestawami danych, takimi jak wzbogacanie lub tabele odnośników, nieApplication Insightse źródła danych i dane niestandardowe.
ms.topic: conceptual
author: eternovsky
ms.author: evternov
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 17de697686485d695586ffa798196efb4a34c251
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092919"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Skorelowanie danych Application Insights z niestandardowymi źródłami danych

Application Insights zbiera różne typy danych: wyjątki, ślady, wyświetlenia stron i inne. Chociaż jest to często wystarczające do zbadania wydajności, niezawodności i użycia aplikacji, istnieją przypadki, w których warto skorelować dane przechowywane w Application Insights z innymi całkowicie niestandardowymi zestawami danych.

Niektóre sytuacje, w których można chcieć, aby dane niestandardowe obejmowały:

- Wzbogacanie danych lub tabele odnośników: na przykład uzupełnij nazwę serwera z właścicielem serwera i lokalizacją laboratorium, w której można ją znaleźć 
- Korelacja ze źródłami danych innymi niż Application Insights: na przykład skorelowanie danych dotyczących zakupu w sklepie internetowym z informacjami z usługi realizacji zakupów, aby określić, jak dokładne oszacowania czasu wysyłki były 
- Dane niestandardowe: wielu naszych klientów lubię język zapytań i wydajność Azure Monitor platformy dzienników, która wykonuje kopię zapasową Application Insights, i chce używać jej do wykonywania zapytań dotyczących danych, które nie są w ogóle powiązane z Application Insights. Na przykład, aby śledzić wydajność panelu słonecznego w ramach inteligentnej instalacji domowej, jak [opisano tutaj](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/).

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Jak skorelować dane niestandardowe z danymi Application Insights 

Ponieważ Application Insights jest obsługiwana przez zaawansowaną platformę rejestrowania Azure Monitor, możemy używać pełnych możliwości Azure Monitor do pozyskiwania danych. Następnie będziemy pisać zapytania przy użyciu operatora "join", który będzie skorelować te dane niestandardowe z danymi dostępnymi dla nas w dziennikach Azure Monitor. 

## <a name="ingesting-data"></a>Pozyskiwanie danych

W tej sekcji zawarto informacje na temat pobierania danych do dzienników Azure Monitor.

Jeśli jeszcze tego nie masz, Zainicjuj obsługę nowego obszaru roboczego Log Analytics, wykonując [te instrukcje](../learn/quick-collect-azurevm.md) w systemie i uwzględniając krok "Utwórz obszar roboczy".

Aby rozpocząć wysyłanie danych dziennika do Azure Monitor. Istnieje kilka opcji:

- W przypadku mechanizmu synchronicznego można bezpośrednio wywołać [interfejs API modułu zbierającego dane](../platform/data-collector-api.md) lub użyć naszego łącznika aplikacji logiki — po prostu zapoznaj się z tematem "Azure log Analytics" i wybierz opcję "Wyślij dane":

  ![Wybór i akcja zrzutu ekranu](./media/custom-data-correlation/01-logic-app-connector.png)  

- Dla opcji asynchronicznej Użyj interfejsu API modułu zbierającego dane, aby skompilować potok przetwarzania. Zobacz [ten artykuł](../platform/create-pipeline-datacollector-api.md) , aby uzyskać szczegółowe informacje.

## <a name="correlating-data"></a>Korelowanie danych

Application Insights jest oparty na platformie dzienników Azure Monitor. W związku z tym możemy używać [sprzężeń między zasobami](../log-query/cross-workspace-query.md) w celu skorelowania dowolnych danych w Azure monitor z naszymi Application Insights danymi.

Można na przykład pozyskać nasze spisy i lokalizacje laboratorium w tabeli o nazwie "LabLocations_CL" w obszarze roboczym Log Analytics o nazwie "myLA". Jeśli chcemy przejrzeć nasze żądania śledzone w Application Insights aplikacji o nazwie "myAI" i skorelować nazwy maszyn, które obsługują żądania do lokalizacji tych maszyn przechowywanych w wcześniej wymienionej tabeli niestandardowej, można uruchomić następujące zapytanie z poziomu Application Insights lub Azure Monitor kontekstu:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Następne kroki

- Sprawdź informacje o [interfejsie API modułu zbierającego dane](../platform/data-collector-api.md) .
- Aby uzyskać więcej informacji na temat [sprzężeń między zasobami](../log-query/cross-workspace-query.md).
