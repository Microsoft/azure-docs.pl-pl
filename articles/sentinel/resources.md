---
title: Przydatne zasoby podczas pracy z platformą Azure — wskaźnikiem Microsoft Docs
description: Ten dokument zawiera listę przydatnych zasobów podczas pracy z platformą Azure — wskaźnikiem.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2021
ms.author: yelevin
ms.openlocfilehash: 11c5c77623fcc693210d5a42bf94c968e884fec3
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920024"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Przydatne zasoby do pracy z platformą Azure — wskaźnikiem



W tym artykule wymieniono zasoby, które mogą pomóc uzyskać więcej informacji na temat pracy z platformą Azure — wskaźnikiem.

- **Łączniki Azure Logic Apps**: <https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Inspekcja i raportowanie
Dzienniki inspekcji platformy Azure są przechowywane w [dziennikach aktywności platformy Azure](../azure-monitor/platform/platform-logs-overview.md).

Możliwe jest przeprowadzenie inspekcji następujących obsługiwanych operacji.

|Nazwa operacji|    Typ zasobu|
|----|----|
|Utwórz lub zaktualizuj skoroszyt  |Microsoft. Insights/skoroszyty|
|Usuń skoroszyt    |Microsoft. Insights/skoroszyty|
|Ustaw przepływ pracy   |Microsoft. Logic/przepływy pracy|
|Usuń przepływ pracy    |Microsoft. Logic/przepływy pracy|
|Utwórz zapisane wyszukiwanie    |Microsoft. OperationalInsights/Workspaces/savedSearches|
|Usuń zapisane wyszukiwanie    |Microsoft. OperationalInsights/Workspaces/savedSearches|
|Aktualizowanie reguł alertów |Microsoft. SecurityInsights/alertRules|
|Usuń reguły alertów |Microsoft. SecurityInsights/alertRules|
|Aktualizuj akcje odpowiedzi reguły alertu |Microsoft. SecurityInsights/alertRules/Actions|
|Usuń akcje odpowiedzi reguły alertu |Microsoft. SecurityInsights/alertRules/Actions|
|Aktualizuj zakładki   |Microsoft. SecurityInsights/zakładki|
|Usuń zakładki   |Microsoft. SecurityInsights/zakładki|
|Przypadki aktualizacji   |Microsoft. SecurityInsights/sprawy|
|Aktualizacja badania przypadku  |Microsoft. SecurityInsights/sprawy/badania|
|Utwórz Komentarze do wielkości liter   |Microsoft. SecurityInsights/sprawy/Komentarze|
|Aktualizuj łączniki danych |Microsoft. SecurityInsights/dataconnecters|
|Usuń łączniki danych |Microsoft. SecurityInsights/dataconnecters|
|Aktualizuj ustawienia    |Microsoft. SecurityInsights/ustawienia|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Wyświetlanie danych inspekcji i raportowania na platformie Azure — wskaźnik

Te dane można wyświetlić, przesyłając je strumieniowo z dziennika aktywności platformy Azure do platformy Azure

1. Połącz źródło danych [aktywności platformy Azure](connect-azure-activity.md) . Po wykonaniu tej czynności zdarzenia inspekcji są przesyłane strumieniowo do nowej tabeli na ekranie **dzienników** o nazwie Azure.

1. Następnie wykonaj zapytanie o dane przy użyciu KQL, tak jak w przypadku każdej innej tabeli.

    Na przykład aby dowiedzieć się, kto był ostatnim użytkownikiem edytującym daną regułę analizy, użyj następującego zapytania (zastępując `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` Identyfikator reguły dla reguły, którą chcesz sprawdzić):

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```



## <a name="vendor-documentation"></a>Dokumentacja dostawcy

| **Dostawca**  | **Korzystanie z zdarzeń na platformie Azure — wskaźnik** | **Link**|
|----|----|----|
| GitHub| Służy do uzyskiwania dostępu do strony społeczności| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto| Konfigurowanie CEF| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight | Kurs języka Kusto Query Language| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Blogi i fora

Chętnie poznamy od naszych użytkowników!

- **Opublikuj swoje pytania** w [obszarze TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) na potrzeby platformy Azure. 

- **Prześlij sugestie dotyczące ulepszeń** za pośrednictwem naszego programu [głosowego użytkownika](https://feedback.azure.com/forums/920458-azure-sentinel) .

- **Wyświetlaj** wpisy w blogu systemu Azure wskaźnikowego i Skomentuj je:

    - [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) 
    - [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/)

Aby uzyskać więcej informacji na temat zabezpieczeń i zgodności platformy Azure, zobacz Blog dotyczący [zabezpieczeń i zgodności Microsoft Azure](https://techcommunity.microsoft.com/t5/microsoft-security-and/bg-p/MicrosoftSecurityandCompliance).


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z certyfikatem.](/learn/paths/security-ops-sentinel/)

> [!div class="nextstepaction"]
> [Odczytaj historie przypadku użycia klienta](https://customers.microsoft.com/en-us/search?sq=%22Azure%20Sentinel%20%22&ff=&p=0&so=story_publish_date%20desc)

