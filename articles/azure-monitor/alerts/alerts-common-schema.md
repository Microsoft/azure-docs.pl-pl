---
title: Typowy schemat alertów dla alertów Azure Monitor
description: Informacje o typowym schemacie alertów, dlaczego należy z nich korzystać i jak go włączyć
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 39ea469b4109f4715db0d0a73ad069121058a46f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038261"
---
# <a name="common-alert-schema"></a>Typowe schematy alertów

W tym artykule opisano, jakie są typowe schematy alertów, zalety korzystania z nich oraz sposób ich włączania.

## <a name="what-is-the-common-alert-schema"></a>Co to jest wspólny schemat alertów?

Typowy schemat alertów umożliwia standaryzację użycia powiadomień o alertach na platformie Azure już dziś. W przeszłości trzy typy alertów na platformie Azure dzisiaj (metryki, dzienniki i dziennik aktywności) miały własne szablony wiadomości e-mail, schematy elementu webhook itp. Ze wspólnym schematem alertów można teraz odbierać powiadomienia o alertach ze spójnym schematem.

Każde wystąpienie alertu opisuje **zasób, którego dotyczy problem** , i **przyczynę alertu**, a te wystąpienia są opisane w typowym schemacie w następujących sekcjach:
* **Podstawowe**: zestaw **standardowych pól**, wspólnych dla wszystkich typów alertów, opisujących **zasób** , w którym znajduje się alert, wraz z dodatkowymi typowymi metadanymi alertów (na przykład ważności lub opisu). 
* **Kontekst alertu**: zestaw pól opisujących **przyczynę alertu**, z polami, które różnią się **w zależności od typu alertu**. Na przykład alert dotyczący metryki będzie zawierał pola, takie jak nazwa metryki i wartość metryki w kontekście alertu, podczas gdy alert dziennika aktywności będzie zawierał informacje o zdarzeniu, które wygenerowało alert. 

Typowe scenariusze integracji występujące od klientów obejmują Routing wystąpienia alertu do danego zespołu w oparciu o część tabeli przestawnej (na przykład grupy zasobów), po którym zespół odpowiedzialny rozpoczyna pracę nad nim. Ze wspólnym schematem alertów można mieć ustandaryzowaną logikę routingu dla różnych typów alertów, wykorzystując zasadnicze pola, pozostawiając pola kontekstowe jako przeznaczone dla zainteresowanych zespołów do dalszej analizy.

Oznacza to, że można potencjalnie mieć mniejszą liczbę integracji, dzięki czemu proces zarządzania i konserwowania ich _znacznie_ prostszego zadania. Ponadto przyszłe wzbogacanie ładunku alertów (na przykład dostosowanie, wzbogacanie diagnostyki itp.) będzie się pojawić tylko w wspólnym schemacie.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>Jakie ulepszenia są wykonywane przez wspólny schemat alertów?

Typowy schemat alertów będzie przede wszystkim używany w powiadomieniach o alertach. Poniżej znajdują się ulepszenia, które zobaczysz poniżej:

| Akcja | Ulepszenia |
|:---|:---|
| SMS | Spójny szablon programu SMS dla wszystkich typów alertów. |
| E-mail | Spójny i szczegółowy szablon wiadomości e-mail, który umożliwia błyskawiczne diagnozowanie problemów. Wbudowane głębokie łącza do wystąpienia alertów w portalu i zaatakowany zasób zapewniają możliwość szybkiego przejścia do procesu korygowania. |
| Element webhook/aplikacja logiki/funkcja Azure Function/Automation | Spójna struktura JSON dla wszystkich typów alertów, która umożliwia łatwe tworzenie integracji dla różnych typów alertów. |

Nowy schemat umożliwi również bardziej zaawansowane środowisko korzystania z alertów w ramach zarówno Azure Portal, jak i aplikacja mobilna platformy Azure w przyszłości. 

[Dowiedz się więcej na temat definicji schematu dla elementów webhook/Logic Apps/Azure Functions/elementy Runbook usługi Automation.](./alerts-common-schema-definitions.md)

> [!NOTE]
> Następujące akcje nie obsługują wspólnego schematu alertu: łącznik ITSM.

## <a name="how-do-i-enable-the-common-alert-schema"></a>Jak mogę włączyć wspólny schemat alertów?

Możesz wybrać lub zrezygnować ze wspólnego schematu alertu za pomocą grup akcji w portalu i za pomocą interfejsu API REST. Przełącznik do przełączenia do nowego schematu istnieje na poziomie akcji. Na przykład należy osobno wyrazić zgodę na akcję poczty e-mail i akcję elementu webhook.

> [!NOTE]
> 1. Następujące typy alertów obsługują domyślnie wspólny schemat (brak zgody na wymagane):
>     * Alerty inteligentnego wykrywania
> 1. Następujące typy alertów nie obsługują obecnie wspólnego schematu:
>     * Alerty wygenerowane przez [szczegółowe informacje o maszynie wirtualnej](../vm/vminsights-overview.md)
>     * Alerty wygenerowane przez [Azure Cost Management](../../cost-management-billing/manage/cost-management-budget-scenario.md)

### <a name="through-the-azure-portal"></a>Za pomocą Azure Portal

![Wybór typowego schematu alertu](media/alerts-common-schema/portal-opt-in.png)

1. Otwórz dowolną istniejącą lub nową akcję w grupie akcji. 
1. Wybierz opcję "tak" dla przełącznika, aby włączyć wspólny schemat alertów, jak pokazano.

### <a name="through-the-action-groups-rest-api"></a>Za pomocą interfejsu API REST grup akcji

Możesz również użyć [interfejsu API grup akcji](/rest/api/monitor/actiongroups) , aby zadecydować o wspólnym schemacie alertów. Podczas [tworzenia lub aktualizowania](/rest/api/monitor/actiongroups/createorupdate) wywołania interfejsu API REST można ustawić flagę "useCommonAlertSchema" na wartość "true" (Aby zrezygnować z) lub "false" (wycofać) dla dowolnej z następujących akcji — wiadomości e-mail/elementu webhook/aplikacji logiki/elementu Runbook usługi Azure Function/Automation.

Na przykład następująca treść żądania utworzona w interfejsie API REST usługi [Create lub Update](/rest/api/monitor/actiongroups/createorupdate) wykona następujące czynności:

* Włącz wspólny schemat alertów dla akcji poczty e-mail "Jan Nowak"
* Wyłącz wspólny schemat alertów dla akcji poczty e-mail "adres e-mail w firmie Jan Kowalski"
* Włącz wspólny schemat alertów dla akcji elementu webhook "przykładowy element webhook"

```json
{
  "properties": {
    "groupShortName": "sample",
    "enabled": true,
    "emailReceivers": [
      {
        "name": "John Doe's email",
        "emailAddress": "johndoe@email.com",
        "useCommonAlertSchema": true
      },
      {
        "name": "Jane Smith's email",
        "emailAddress": "janesmith@email.com",
        "useCommonAlertSchema": false
      }
    ],
    "smsReceivers": [
      {
        "name": "John Doe's mobile",
        "countryCode": "1",
        "phoneNumber": "1234567890"
      },
      {
        "name": "Jane Smith's mobile",
        "countryCode": "1",
        "phoneNumber": "0987654321"
      }
    ],
    "webhookReceivers": [
      {
        "name": "Sample webhook",
        "serviceUri": "http://www.example.com/webhook",
        "useCommonAlertSchema": true
      }
    ]
  },
  "location": "Global",
  "tags": {}
}
```





## <a name="next-steps"></a>Następne kroki

- [Definicje schematów typowych alertów dla elementów webhook/Logic Apps/Azure Functions/elementy Runbook usługi Automation.](./alerts-common-schema-definitions.md)
- [Dowiedz się, jak utworzyć aplikację logiki, która wykorzystuje wspólny schemat alertów do obsługi wszystkich alertów.](./alerts-common-schema-integrations.md)