---
title: Wyświetl metryki z Azure Monitor
titleSuffix: Azure Digital Twins
description: Zobacz jak wyświetlić metryki usługi Azure Digital bliźniaczych reprezentacji w Azure Monitor.
author: baanders
ms.author: baanders
ms.date: 8/4/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 63817c8a65678579ce535a3c2e667e4eb0971a63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102434070"
---
# <a name="troubleshooting-azure-digital-twins-metrics"></a>Rozwiązywanie problemów z usługą Azure Digital bliźniaczych reprezentacji: metryki

Metryki opisane w tym artykule zawierają informacje o stanie zasobów usługi Azure Digital bliźniaczych reprezentacji w ramach subskrypcji platformy Azure. Metryki Digital bliźniaczych reprezentacji na platformie Azure ułatwiają ocenę ogólnej kondycji usługi Azure Digital bliźniaczych reprezentacji i podłączonych do niej zasobów. Te dane statystyczne związane z użytkownikiem pomagają zobaczyć, co się dzieje z usługą Azure Digital bliźniaczych reprezentacji, oraz jak przeprowadzić analizę głównych przyczyn problemów bez konieczności kontaktowania się z pomocą techniczną platformy Azure.

Metryki są domyślnie włączone. Możesz wyświetlić metryki usługi Azure Digital bliźniaczych reprezentacji z poziomu [Azure Portal](https://portal.azure.com).

## <a name="how-to-view-azure-digital-twins-metrics"></a>Jak wyświetlić metryki usługi Azure Digital bliźniaczych reprezentacji

1. Utwórz wystąpienie usługi Azure Digital bliźniaczych reprezentacji. Instrukcje dotyczące sposobu konfigurowania wystąpienia usługi Azure Digital bliźniaczych reprezentacji można znaleźć w temacie [*jak to zrobić: Konfigurowanie wystąpienia i uwierzytelniania*](how-to-set-up-instance-portal.md).

2. Znajdź wystąpienie usługi Azure Digital bliźniaczych reprezentacji w [Azure Portal](https://portal.azure.com) (możesz otworzyć stronę, wpisując jej nazwę na pasku wyszukiwania portalu). 

    Z menu wystąpienie wybierz pozycję **metryki**.
   
    :::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Zrzut ekranu przedstawiający stronę metryki usługi Azure Digital bliźniaczych reprezentacji":::

    Na tej stronie są wyświetlane metryki wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Widoki niestandardowe metryk można również utworzyć, wybierając te, które mają zostać wyświetlone na liście.
    
3. Możesz wysłać dane metryk do punktu końcowego Event Hubs lub konta usługi Azure Storage, wybierając z menu pozycję **Ustawienia diagnostyczne** , a następnie **Dodaj ustawienie diagnostyczne**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Zrzut ekranu przedstawiający stronę ustawień diagnostycznych i przycisk do dodania":::

    Aby uzyskać więcej informacji o tym procesie, zobacz [*Rozwiązywanie problemów: Konfigurowanie diagnostyki*](troubleshoot-diagnostics.md).

4. Możesz skonfigurować alerty dla danych metryk, wybierając pozycję **alerty** z menu, a następnie **+ Nowa reguła alertu**.
    :::image type="content" source="media/troubleshoot-alerts/alerts-pre.png" alt-text="Zrzut ekranu przedstawiający stronę alerty i przycisk do dodania":::

    Aby uzyskać więcej informacji o tym procesie, zobacz [*Rozwiązywanie problemów: Konfigurowanie alertów*](troubleshoot-alerts.md).

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Metryki Digital bliźniaczych reprezentacji na platformie Azure i sposoby ich używania

Usługa Azure Digital bliźniaczych reprezentacji zawiera kilka metryk, które umożliwiają przedstawienie informacji o kondycji wystąpienia i skojarzonych z nim zasobów. Możesz również połączyć informacje z wielu metryk, aby malować większy obraz stanu wystąpienia. 

W poniższych tabelach opisano metryki śledzone przez poszczególne wystąpienia usługi Azure Digital bliźniaczych reprezentacji oraz sposób, w jaki każda Metryka odnosi się do ogólnego stanu wystąpienia.

#### <a name="metrics-for-tracking-service-limits"></a>Metryki dla limitów usługi śledzenia

Te metryki można skonfigurować do śledzenia, gdy zbliża się [Limit usług opublikowanych](reference-service-limits.md#functional-limits) dla pewnego aspektu rozwiązania. 

Aby je skonfigurować, użyj funkcji [alertów](troubleshoot-alerts.md) w Azure monitor. Można zdefiniować progi dla tych metryk, aby otrzymywać alerty, gdy Metryka osiągnie określoną wartość procentową opublikowanego limitu.

| Metric | Nazwa wyświetlana metryki | Jednostka | Typ agregacji| Opis | Wymiary |
| --- | --- | --- | --- | --- | --- |
| TwinCount | Liczba sznurów (wersja zapoznawcza) | Liczba | Łącznie | Łączna liczba bliźniaczych reprezentacji w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji. Użyj tej metryki, aby określić, czy zbliża się [Limit usługi](reference-service-limits.md#functional-limits) dla maksymalnej liczby bliźniaczych reprezentacji dozwolonych na wystąpienie. |  Brak |
| ModelCount | Liczba modeli (wersja zapoznawcza) | Liczba | Łącznie | Łączna liczba modeli w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji. Użyj tej metryki, aby określić, czy zbliża się [Limit usługi](reference-service-limits.md#functional-limits) dla maksymalnej liczby modeli dozwolonych na wystąpienie. | Brak |

#### <a name="api-request-metrics"></a>Metryki żądania interfejsu API

Metryki mające na celu wykonywanie żądań interfejsu API:

| Metric | Nazwa wyświetlana metryki | Jednostka | Typ agregacji| Opis | Wymiary |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | Żądania interfejsu API | Liczba | Łącznie | Liczba żądań interfejsu API dla operacji odczytu, zapisu, usuwania i zapytań cyfrowych bliźniaczych reprezentacji. |  Ponowne <br>Operacje <br>Protokol <br>Kod stanu, <br>Klasa kodu stanu, <br>Tekst stanu |
| ApiRequestsFailureRate | Częstotliwość niepowodzeń żądań interfejsu API | Procent | Średnia | Procent żądań interfejsu API odbieranych przez usługę dla wystąpienia, które dają wewnętrzny błąd (500) kod odpowiedzi dla operacji odczytu, zapisu, usuwania i zapytań cyfrowych bliźniaczych reprezentacji. | Ponowne <br>Operacje <br>Protokol <br>Kod stanu, <br>Klasa kodu stanu, <br>Tekst stanu
| ApiRequestsLatency | Opóźnienie żądań interfejsu API | ) | Średnia | Czas odpowiedzi dla żądań interfejsu API. Odnosi się to do czasu od momentu odebrania żądania przez usługę Azure Digital bliźniaczych reprezentacji, dopóki usługa nie wyśle wyniku sukcesu/niepowodzenia dla operacji odczytu, zapisu, usuwania i wykonywania zapytań w funkcji Digital bliźniaczych reprezentacji. | Ponowne <br>Operacje <br>Protokół |

#### <a name="billing-metrics"></a>Metryki rozliczeń

Metryki mające na celu rozliczanie:

| Metric | Nazwa wyświetlana metryki | Jednostka | Typ agregacji| Opis | Wymiary |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperations | Operacje interfejsu API rozliczeń | Liczba | Łącznie | Metryka rozliczeń dla wszystkich żądań interfejsu API w usłudze Azure Digital bliźniaczych reprezentacji. | Meter ID |
| BillingMessagesProcessed | Przetworzone komunikaty dotyczące rozliczeń | Liczba | Łącznie | Metryka rozliczania dla liczby komunikatów wysyłanych z usługi Azure Digital bliźniaczych reprezentacji do zewnętrznych punktów końcowych.<br><br>Aby można było traktować pojedynczy komunikat do celów rozliczania, ładunek nie może być większy niż 1 KB. Ładunki większe niż ten będą zliczane jako dodatkowe komunikaty w przyrostach 1 KB (więc komunikat z przedziału od 1 do 2 KB będzie liczony jako 2 komunikaty, między 2 a 3 KB będzie 3 komunikaty itd.).<br>To ograniczenie ma zastosowanie również do odpowiedzi, więc wywołanie zwracające wartość 1,5 KB w treści odpowiedzi, na przykład, będzie rozliczane jako dwie operacje. | Meter ID |
| BillingQueryUnits | Jednostki zapytań dotyczących rozliczeń | Liczba | Łącznie | Liczba jednostek zapytania, wewnętrznie obliczona miara użycia zasobów usługi używana do wykonywania zapytań. Dostępny jest również pomocnik interfejsu API do mierzenia jednostek zapytania: [Klasa QueryChargeHelper](/dotnet/api/azure.digitaltwins.core.querychargehelper) | Meter ID |

Aby uzyskać więcej informacji na temat sposobu obciążania usługi Azure Digital bliźniaczych reprezentacji, zobacz [*Cennik usługi Azure Digital bliźniaczych reprezentacji*](https://azure.microsoft.com/pricing/details/digital-twins/).

#### <a name="ingress-metrics"></a>Metryki transferu danych przychodzących

Metryki mające na celu wykonywanie danych wejściowych:

| Metric | Nazwa wyświetlana metryki | Jednostka | Typ agregacji| Opis | Wymiary |
| --- | --- | --- | --- | --- | --- |
| IngressEvents | Zdarzenia związane z transferem danych przychodzących | Liczba | Łącznie | Liczba przychodzących zdarzeń telemetrii do usługi Azure Digital bliźniaczych reprezentacji. | Wynik |
| IngressEventsFailureRate | Współczynnik błędów zdarzeń związanych z transferem danych przychodzących | Procent | Średnia | Procent przychodzących zdarzeń telemetrii, dla których usługa zwraca kod odpowiedzi z błędu wewnętrznego (500). | Wynik |
| IngressEventsLatency | Opóźnienie zdarzeń związanych z transferem danych przychodzących | ) | Średnia | Czas od momentu nadejścia zdarzenia do momentu, gdy jest on gotowy do egressed przez usługę Azure Digital bliźniaczych reprezentacji, w którym usługa wysyła wynik sukces/niepowodzenie. | Wynik |

#### <a name="routing-metrics"></a>Metryki routingu

Metryki, które mają wykonywać Routing:

| Metric | Nazwa wyświetlana metryki | Jednostka | Typ agregacji| Opis | Wymiary |
| --- | --- | --- | --- | --- | --- |
| MessagesRouted | Komunikaty kierowane | Liczba | Łącznie | Liczba komunikatów kierowanych do usługi platformy Azure dla punktów końcowych, takich jak centrum zdarzeń, Service Bus lub Event Grid. | Typ punktu końcowego, <br>Wynik |
| RoutingFailureRate | Współczynnik błędów routingu | Procent | Średnia | Wartość procentowa zdarzeń spowodowanych błędem, ponieważ są one kierowane z usługi Azure Digital bliźniaczych reprezentacji do usługi platformy Azure w punkcie końcowym, takiej jak centrum zdarzeń, Service Bus lub Event Grid. | Typ punktu końcowego, <br>Wynik |
| RoutingLatency | Opóźnienie routingu | ) | Średnia | Czas między zdarzeniem, które ma być przesyłane z usługi Azure Digital bliźniaczych reprezentacji do momentu opublikowania w usłudze Azure Endpoint Service, takiej jak centrum zdarzeń, Service Bus lub Event Grid. | Typ punktu końcowego, <br>Wynik |

## <a name="dimensions"></a>Wymiary

Wymiary ułatwiają znalezienie dodatkowych informacji o metrykach. Niektóre metryki routingu zawierają informacje na punkt końcowy. W poniższej tabeli wymieniono możliwe wartości dla tych wymiarów.

| Wymiar | Wartości |
| --- | --- |
| Authentication | OAuth |
| Operacja (dla żądań interfejsu API) | Microsoft. DigitalTwins/DigitalTwins/Delete, <br>Microsoft. DigitalTwins/DigitalTwins/Write, <br>Microsoft. DigitalTwins/DigitalTwins/odczyt, <br>Microsoft. DigitalTwins/eventroutes/odczyt, <br>Microsoft. DigitalTwins/eventroutes/Write, <br>Microsoft. DigitalTwins/eventroutes/Delete, <br>Microsoft. DigitalTwins/modele/odczyt, <br>Microsoft. DigitalTwins/modele/zapis, <br>Microsoft. DigitalTwins/modele/usuwanie, <br>Microsoft. DigitalTwins/kwerenda/akcja |
| Typ punktu końcowego | Event Grid <br>Centrum zdarzeń, <br>Service Bus |
| Protokół | HTTPS |
| Wynik | Prawnego <br>Niepowodzenie |
| Kod stanu | 200, 404, 500 i tak dalej. |
| Klasa kodu stanu | 2xx, 4xx, 5xx itd. |
| Tekst stanu | Wewnętrzny błąd serwera, nie znaleziono i tak dalej. |

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zarządzaniu zarejestrowanymi metrykami dla usługi Azure Digital bliźniaczych reprezentacji, zobacz [*Rozwiązywanie problemów: Konfigurowanie diagnostyki*](troubleshoot-diagnostics.md).
