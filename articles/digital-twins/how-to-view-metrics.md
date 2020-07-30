---
title: Wyświetl metryki z Azure Monitor
titleSuffix: Azure Digital Twins
description: Zobacz jak wyświetlić metryki usługi Azure Digital bliźniaczych reprezentacji w Azure Monitor.
author: baanders
ms.author: baanders
ms.date: 7/24/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c6db571d64b0fd276519f15a3984848e80c4e18a
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387680"
---
# <a name="view-and-understand-azure-digital-twins-metrics"></a>Wyświetl i poznanie metryk usługi Azure Digital bliźniaczych reprezentacji

Te metryki zawierają informacje o stanie zasobów usługi Azure Digital bliźniaczych reprezentacji w ramach subskrypcji platformy Azure. Metryki Digital bliźniaczych reprezentacji na platformie Azure ułatwiają ocenę ogólnej kondycji usługi Azure Digital bliźniaczych reprezentacji i podłączonych do niej zasobów. Te dane statystyczne związane z użytkownikiem pomagają zobaczyć, co się dzieje z usługą Azure Digital bliźniaczych reprezentacji, oraz jak przeprowadzić analizę głównych przyczyn problemów bez konieczności kontaktowania się z pomocą techniczną platformy Azure.

Metryki są domyślnie włączone. Możesz wyświetlić metryki usługi Azure Digital bliźniaczych reprezentacji z poziomu [Azure Portal](https://portal.azure.com).

## <a name="how-to-view-azure-digital-twins-metrics"></a>Jak wyświetlić metryki usługi Azure Digital bliźniaczych reprezentacji

1. Utwórz wystąpienie usługi Azure Digital bliźniaczych reprezentacji. Instrukcje dotyczące sposobu konfigurowania wystąpienia usługi Azure Digital bliźniaczych reprezentacji można znaleźć w temacie [*jak to zrobić: Konfigurowanie wystąpienia i uwierzytelniania*](how-to-set-up-instance-scripted.md).

2. Znajdź wystąpienie usługi Azure Digital bliźniaczych reprezentacji w [Azure Portal](https:/portal.azure.com) (możesz otworzyć stronę, wpisując jej nazwę na pasku wyszukiwania portalu). 

    Z menu wystąpienie wybierz pozycję **metryki**.
   
    :::image type="content" source="media/how-to-view-metrics/azure-digital-twins-metrics.png" alt-text="Zrzut ekranu przedstawiający stronę metryki usługi Azure Digital bliźniaczych reprezentacji":::

    Na tej stronie są wyświetlane metryki wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Widoki niestandardowe metryk można również utworzyć, wybierając te, które mają zostać wyświetlone na liście.
    
3. Możesz wysłać dane metryk do punktu końcowego Event Hubs lub konta usługi Azure Storage, klikając pozycję **Ustawienia diagnostyczne** z menu, a następnie **Dodaj ustawienie diagnostyczne**.

    :::image type="content" source="media/how-to-view-metrics/diagnostic-settings.png" alt-text="Zrzut ekranu przedstawiający stronę ustawień diagnostycznych i przycisk do dodania":::

    Aby uzyskać więcej informacji o tym procesie, zobacz [*Rozwiązywanie problemów: Konfigurowanie diagnostyki*](troubleshoot-diagnostics.md).

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Metryki Digital bliźniaczych reprezentacji na platformie Azure i sposoby ich używania

Usługa Azure Digital bliźniaczych reprezentacji zawiera kilka metryk, które umożliwiają przedstawienie informacji o kondycji wystąpienia i skojarzonych z nim zasobów. Możesz również połączyć informacje z wielu metryk, aby malować większy obraz stanu wystąpienia. 

W poniższych tabelach opisano metryki śledzone przez poszczególne wystąpienia usługi Azure Digital bliźniaczych reprezentacji oraz sposób, w jaki każda Metryka odnosi się do ogólnego stanu wystąpienia.

#### <a name="api-request-metrics"></a>Metryki żądania interfejsu API

Metryki mające na celu wykonywanie żądań interfejsu API:

| Metryka | Nazwa wyświetlana metryki | Jednostka | Typ agregacji| Opis | Wymiary |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | Żądania interfejsu API (wersja zapoznawcza) | Liczba | Łącznie | Liczba żądań interfejsu API dla operacji odczytu, zapisu, usuwania i zapytań cyfrowych bliźniaczych reprezentacji. |  Authentication </br>Operacja </br>Protokół </br>Kod stanu </br>Klasa kodu stanu </br>Tekst stanu |
| ApiRequestsLatency | Opóźnienie żądań interfejsu API (wersja zapoznawcza) | ) | Średnia | Czas odpowiedzi dla żądań interfejsu API. Odnosi się to do czasu od momentu odebrania żądania przez usługę Azure Digital bliźniaczych reprezentacji, dopóki usługa nie wyśle wyniku sukcesu/niepowodzenia dla operacji odczytu, zapisu, usuwania i wykonywania zapytań w funkcji Digital bliźniaczych reprezentacji. | Authentication </br>Operacja </br>Protokół |
| ApiRequestsFailureRate | Współczynnik błędów żądań interfejsu API (wersja zapoznawcza) | Procent | Średnia | Procent żądań interfejsu API odbieranych przez usługę dla wystąpienia, które dają wewnętrzny błąd (500) kod odpowiedzi dla operacji odczytu, zapisu, usuwania i zapytań cyfrowych bliźniaczych reprezentacji. | Authentication </br>Operacja </br>Protokół </br>Kod stanu </br>Klasa kodu stanu </br>Tekst stanu

#### <a name="routing-metrics"></a>Metryki routingu

Metryki, które mają wykonywać Routing:

| Metryka | Nazwa wyświetlana metryki | Jednostka | Typ agregacji| Opis | Wymiary |
| --- | --- | --- | --- | --- | --- |
| Routing | Routing (wersja zapoznawcza) | Liczba | Łącznie | Liczba komunikatów kierowanych do usługi platformy Azure dla punktów końcowych, takich jak centrum zdarzeń, Service Bus lub Event Grid. | Operacja </br>Wynik |
| RoutingLatency | Opóźnienie routingu (wersja zapoznawcza) | ) | Średnia | Czas między zdarzeniem, które ma być przesyłane z usługi Azure Digital bliźniaczych reprezentacji do momentu opublikowania w usłudze Azure Endpoint Service, takiej jak centrum zdarzeń, Service Bus lub Event Grid. | Operacja </br>Wynik |
| RoutingFailureRate | Współczynnik błędów routingu (wersja zapoznawcza) | Procent | Średnia | Wartość procentowa zdarzeń spowodowanych błędem, ponieważ są one kierowane z usługi Azure Digital bliźniaczych reprezentacji do usługi platformy Azure w punkcie końcowym, takiej jak centrum zdarzeń, Service Bus lub Event Grid. | Operacja </br>Wynik |

#### <a name="billing-metrics"></a>Metryki rozliczeń

Metryki mające na celu rozliczanie:

>[!NOTE]
> W okresie zapoznawczym **opłaty nie są**naliczane. Mimo że te metryki nadal pojawiają się na liście możliwej do wyboru, nie mają zastosowania w wersji zapoznawczej i pozostaną na zero, dopóki usługa nie przejdzie poza wersję zapoznawczą.

| Metryka | Nazwa wyświetlana metryki | Jednostka | Typ agregacji| Opis | Wymiary |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperations | Operacje interfejsu API rozliczeń (wersja zapoznawcza) | Liczba | Łącznie | Metryka rozliczeń dla wszystkich żądań interfejsu API w usłudze Azure Digital bliźniaczych reprezentacji. | `MeterId` |
| BillingQueryUnits | Jednostki zapytań rozliczeń (wersja zapoznawcza) | Liczba | Łącznie | Liczba jednostek zapytania, wewnętrznie obliczona miara użycia zasobów usługi używana do wykonywania zapytań. Dostępny jest również pomocnik interfejsu API do mierzenia jednostek zapytania: [Klasa QueryChargeHelper](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.querychargehelper?view=azure-dotnet-preview) | `MeterId` |
| BillingMessagesProcessed | Przetworzone komunikaty rozliczeń (wersja zapoznawcza) | Liczba | Łącznie | Metryka rozliczania dla liczby komunikatów wysyłanych z usługi Azure Digital bliźniaczych reprezentacji do zewnętrznych punktów końcowych. | `MeterId` |

## <a name="dimensions"></a>Wymiary

Wymiary ułatwiają znalezienie dodatkowych informacji o metrykach. Niektóre metryki routingu zawierają informacje na punkt końcowy. W poniższej tabeli wymieniono możliwe wartości dla tych wymiarów.

| Wymiar | Wartości |
| --- | --- |
| Authentication | OAuth |
| Operacja (dla żądań interfejsu API) | Microsoft. DigitalTwins/DigitalTwins/Delete</br>Microsoft. DigitalTwins/DigitalTwins/Write</br>Microsoft. DigitalTwins/DigitalTwins/odczyt </br>Microsoft. DigitalTwins/eventroutes/odczyt </br>Microsoft. DigitalTwins/eventroutes/Write </br>Microsoft. DigitalTwins/eventroutes/Delete </br>Microsoft. DigitalTwins/modele/odczyt </br>Microsoft. DigitalTwins/modele/zapis </br>Microsoft. DigitalTwins/modele/usuwanie </br>Microsoft. DigitalTwins/kwerenda/akcja |
Operacja (dla routingu) | Event Grid </br>Centrum zdarzeń </br>Service Bus |
| Protokół | HTTPS |
| Wynik | Powodzenie </br>Niepowodzenie |
| Kod stanu | 200, 404, 500 i tak dalej. |
| Klasa kodu stanu | 2xx, 4xx, 5xx itd. |
| Tekst stanu | Wewnętrzny błąd serwera, nie znaleziono i tak dalej. |

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zarządzaniu zarejestrowanymi metrykami dla usługi Azure Digital bliźniaczych reprezentacji, zobacz [*Rozwiązywanie problemów: Konfigurowanie diagnostyki*](troubleshoot-diagnostics.md).

Możesz też zapoznać się z omówieniem metryk usługi Azure Digital bliźniaczych reprezentacji, korzystając z poniższych linków, aby dowiedzieć się więcej na temat zarządzania usługą Azure Digital bliźniaczych reprezentacji:

* [*Instrukcje: korzystanie z interfejsów API i zestawów SDK Digital bliźniaczych reprezentacji na platformie Azure*](how-to-use-apis-sdks.md)
* [*Instrukcje: Zarządzanie modelami niestandardowymi*](how-to-manage-model.md)
* [*Instrukcje: Zarządzanie bliźniaczych reprezentacji cyfrowym*](how-to-manage-twin.md)
* [*Instrukcje: Zarządzanie wykresem bliźniaczym z relacjami*](how-to-manage-graph.md)
* [*Instrukcje: Zarządzanie punktami końcowymi i trasami*](how-to-manage-routes.md)