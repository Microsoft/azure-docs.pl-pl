---
title: Alerty dzienników w Azure Monitor
description: Wyzwalaj wiadomości e-mail, powiadomienia, wywołaj adresy URL witryn sieci Web (webhook) lub automatyzację, gdy określone warunki kwerendy analitycznej są spełnione dla alertów platformy Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 5/31/2019
ms.subservice: alerts
ms.openlocfilehash: 1d3b3215fe05ef2f57805b5df2b441f360f45df2
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87322350"
---
# <a name="log-alerts-in-azure-monitor"></a>Alerty dzienników w Azure Monitor

Alerty dzienników są jednym z typów alertów, które są obsługiwane w [alertach platformy Azure](./alerts-overview.md). Alerty dzienników umożliwiają użytkownikom korzystanie z platformy Azure Analytics jako podstawy do zgłaszania alertów.

Alert dziennika składa się z reguł przeszukiwania dzienników utworzonych dla [dzienników Azure monitor](../log-query/get-started-portal.md) lub [Application Insights](../app/cloudservices.md#view-azure-diagnostics-events). Aby dowiedzieć się więcej na temat użycia, zobacz [tworzenie alertów dziennika na platformie Azure](./alerts-log.md)

> [!NOTE]
> Popularne dane dzienników z [dzienników Azure monitor](../log-query/get-started-portal.md) są teraz również dostępne na platformie metryk w Azure monitor. Aby wyświetlić szczegóły, [alert dotyczący metryk dzienników](./alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Reguła alertu wyszukiwania w dzienniku — definicja i typy

Reguły przechowywania dzienników są tworzone przez usługę Azure Alerts w celu automatycznego wykonywania określonych zapytań dotyczących dzienników w regularnych odstępach czasu.  Jeśli wyniki zapytania pasują do określonych kryteriów, jest tworzony rekord alertu. Reguła może wtedy automatycznie uruchomić jedną lub więcej akcji przy użyciu [grup akcji](./action-groups.md). Może być wymagana rola [współautor monitorowania platformy Azure](./roles-permissions-security.md) służąca do tworzenia, modyfikowania i aktualizowania alertów dzienników. wraz z dostępem & praw wykonywania zapytań dla obiektów docelowych analiz w regule alertów lub kwerendzie alertu. Jeśli użytkownik nie ma dostępu do wszystkich obiektów docelowych analizy w regule alertu lub kwerendzie alertu — Tworzenie reguły może zakończyć się niepowodzeniem lub reguła alertu dziennika zostanie wykonana z częściowymi wynikami.

Reguły przeszukiwania dzienników są definiowane przez następujące szczegóły:

- **Zapytanie dziennika**.  zapytanie uruchamiane po każdym wyzwoleniu reguły alertu.  Rekordy zwrócone przez to zapytanie są używane do określenia, czy alert ma zostać wyzwolony. Zapytanie analityczne może dotyczyć określonego obszaru roboczego Log Analytics lub Application Insights aplikacji, a nawet w [wielu log Analytics i Application Insights zasobach](../log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) , pod warunkiem, że użytkownik ma dostęp, a także uprawnienia zapytania do wszystkich zasobów. 
    > [!IMPORTANT]
    > Obsługa [zapytań między zasobami](../log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) w alertach dziennika Application Insights i rejestrowania alertów dotyczących [log Analytics skonfigurowanych tylko przy użyciu interfejsu API scheduledQueryRules](./alerts-log-api-switch.md) .

    Niektóre polecenia i kombinacje analityczne są niezgodne z użyciem w alertach dziennika; Aby uzyskać więcej szczegółów, [Rejestruj zapytania alertów w Azure monitor](./alerts-log-query.md).

- **Okres**.  Określa zakres czasu dla zapytania. Zapytanie zwraca tylko rekordy utworzone w tym zakresie czasu bieżącego. Przedział czasu ogranicza dane pobierane dla zapytania dziennika, aby zapobiec nadużyciu i obejść każde polecenie czasu (na przykład temu) używane w zapytaniu dziennika. <br>*Na przykład jeśli okres jest ustawiony na 60 minut, a zapytanie jest uruchamiane o godzinie 1:15 PM, do wykonania kwerendy dziennika jest zwracane tylko rekordy utworzone między 12:15 PM i 1:15 PM. Teraz, jeśli zapytanie dziennika używa polecenia czasu, takiego jak temu (7D), zapytanie dziennika zostanie uruchomione tylko dla danych między 12:15 PM i 1:15 PM-tak, jakby dane istniały tylko dla ostatnich 60 minut. I nie przez siedem dni dane określone w zapytaniu dziennika.*

- **Częstotliwość**.  Określa, jak często zapytanie powinno być uruchamiane. Może mieć dowolną wartość z przedziału od 5 minut do 24 godzin. Wartość powinna być równa lub mniejsza niż przedział czasu.  Jeśli wartość jest większa niż przedział czasu, oznacza to, że zostaną pominięte rekordy ryzyka.<br>*Rozważmy na przykład przedział czasu 30 minut i częstotliwość 60 minut.  Jeśli zapytanie jest uruchamiane o godzinie 1:00, zwraca rekordy z przedziału od 12:30 do 1:00 PM.  Przy następnym uruchomieniu zapytania jest 2:00, gdy zwróci rekordy z zakresu od 1:30 do 2:00.  Wszystkie rekordy utworzone w zakresie od 1:00 do 1:30 nigdy nie zostaną ocenione.*

- **Próg**.  Wyniki przeszukiwania dzienników są oceniane, aby określić, czy ma zostać utworzony alert.  Próg jest różny dla różnych typów reguł alertów wyszukiwania w dziennikach.

Reguły przeszukiwania dzienników dla [Azure monitor dzienników](../log-query/get-started-portal.md) lub [Application Insights](../app/cloudservices.md#view-azure-diagnostics-events)mogą być dwa typy. Każdy z tych typów jest szczegółowo opisany w poniższych sekcjach.

- **[Liczba wyników](#number-of-results-alert-rules)**. Pojedynczy alert utworzony, gdy rekordy liczbowe zwrócone przez przeszukiwanie dzienników przekraczają określoną liczbę.
- **[Pomiar metryki](#metric-measurement-alert-rules)**.  Utworzono alert dla każdego obiektu w wynikach przeszukiwania dzienników z wartościami przekraczającymi określony próg.

Różnice między typami reguł alertów są następujące.

- *Liczba* reguł alertów dotyczących wyników zawsze powoduje utworzenie pojedynczego alertu, podczas gdy reguła alertu *pomiaru metryki* tworzy alert dla każdego obiektu, który przekracza wartość progową.
- Liczba reguł alertów *dotyczących wyników* Utwórz alert, gdy próg zostanie przekroczony pojedynczo. Reguły alertów *pomiaru metryki* mogą utworzyć alert, gdy próg zostanie przekroczony określoną liczbę razy w określonym przedziale czasu.

### <a name="number-of-results-alert-rules"></a>Liczba reguł alertów dotyczących wyników

Liczba reguł alertów **z wynikami** tworzenia pojedynczego alertu, gdy liczba rekordów zwróconych przez zapytanie wyszukiwania przekracza określoną wartość progową. Ten typ reguły alertów jest idealny do pracy ze zdarzeniami, takimi jak dzienniki zdarzeń systemu Windows, dziennik systemowy, odpowiedź WebApp i dzienniki niestandardowe.  Możesz chcieć utworzyć alert po utworzeniu określonego zdarzenia błędu lub w przypadku utworzenia wielu zdarzeń błędów w określonym przedziale czasu.

**Próg**: próg dla wielu reguł alertów o wynikach jest większy lub mniejszy niż określona wartość.  Jeśli liczba rekordów zwróconych przez przeszukiwanie dziennika spełnia te kryteria, zostanie utworzony alert.

Aby otrzymywać alerty dotyczące pojedynczego zdarzenia, należy ustawić liczbę wyników na wartość większą niż 0 i wyszukać wystąpienie pojedynczego zdarzenia, które zostało utworzone od czasu ostatniego uruchomienia zapytania. Niektóre aplikacje mogą rejestrować sporadyczne błędy, które nie powinny być przyczyną zgłoszenia alertu.  Na przykład aplikacja może ponowić próbę wykonania procesu, który utworzył zdarzenie błędu, a następnie powiodło się następnym razem.  W takim przypadku można nie chcieć utworzyć alertu, chyba że w określonym przedziale czasu są tworzone wiele zdarzeń.  

W niektórych przypadkach może być konieczne utworzenie alertu w przypadku nieobecności zdarzenia.  Na przykład proces może rejestrować regularne zdarzenia, aby wskazać, że działa prawidłowo.  Jeśli nie rejestruje jednego z tych zdarzeń w określonym przedziale czasu, należy utworzyć alert.  W tym przypadku należy ustawić próg na wartość **mniejszą niż 1**.

#### <a name="example-of-number-of-records-type-log-alert"></a>Przykład liczby rekordów typ alertu dziennika

Rozważmy scenariusz, w którym chcesz wiedzieć, kiedy aplikacja oparta na sieci Web daje odpowiedzi użytkownikom z kodem 500 (oznacza to, że jest to wewnętrzny błąd serwera). Można utworzyć regułę alertu z następującymi szczegółami:  

- **Zapytanie:** żądania | gdzie resultCode = = "500"<br>
- **Okres:** 30 minut<br>
- **Częstotliwość alertów:** pięć minut<br>
- **Wartość progowa:** Większe niż 0<br>

Następnie alert będzie uruchamiał zapytanie co 5 minut, z 30-minutową ilością danych — w celu wyszukania dowolnego rekordu, gdzie kod wyniku to 500. Jeśli jeden taki rekord zostanie znaleziony, wyzwala alert i wyzwala skonfigurowaną akcję.

### <a name="metric-measurement-alert-rules"></a>Reguły alertów pomiaru metryki

Reguły alertów **pomiaru metryki** tworzą alert dla każdego obiektu w zapytaniu o wartości przekraczającej określony próg i określony warunek wyzwalacza. W przeciwieństwie do liczby reguł alertów **dotyczących wyników** pomiarów metryk, gdy wynik analizy zawiera szeregi czasowe, działają reguły alertów o **pomiarach** . Mają one następujące odrębne różnice od liczby reguł alertów **dotyczących wyników** .

- **Funkcja agregująca**: określa obliczenie, które jest wykonywane, a potencjalnie wartość pola liczbowego do zagregowania.  Na przykład **liczba ()** zwraca liczbę rekordów w zapytaniu, **średnia (CounterValue)** zwraca średnią pola CounterValue w interwale. Funkcja agregująca w zapytaniu musi mieć nazwę/nazwę: AggregatedValue i podać wartość liczbową. 

- **Pole grupy**: rekord z zagregowaną wartością jest tworzony dla każdego wystąpienia tego pola, a dla każdego z nich można wygenerować alert.  Jeśli na przykład chcesz wygenerować alert dla każdego komputera, użyj **komputera**. W przypadku w zapytaniu alertu określono wiele pól grup, użytkownik może określić pole, które ma być używane do sortowania wyników przy użyciu parametru **Aggregate on** (metricColumn).

    > [!NOTE]
    > Opcja *agregowania* (metricColumn) jest dostępna dla alertów dziennika typu pomiaru metryki dla alertów dotyczących Application Insights i dzienników [log Analytics skonfigurowanych tylko przy użyciu interfejsu API scheduledQueryRules](./alerts-log-api-switch.md) .

- **Interwał**: określa przedział czasu, w którym dane są agregowane.  Na przykład jeśli określono **pięć minut**, rekord zostanie utworzony dla każdego wystąpienia pola grupy zagregowanego w 5-minutowych odstępach czasu w okresie określonym dla alertu.

    > [!NOTE]
    > Funkcja bin musi być użyta w zapytaniu, aby określić interwał. W miarę jak bin () może powodować nierówne przedziały czasu — alert automatycznie konwertuje bin polecenie na polecenie bin_at przy odpowiednim czasie w czasie wykonywania, aby zapewnić wynik ze stałym punktem. Typ pomiaru metryki alertu dziennika jest przeznaczony do pracy z zapytaniami zawierającymi maksymalnie trzy wystąpienia polecenia bin ()
    
- **Próg**: próg dla reguł alertów pomiaru metryki jest definiowany przez wartość zagregowaną i liczbę naruszeń.  Jeśli którykolwiek z punktów danych w przeszukiwaniu dzienników przekroczy tę wartość, jest traktowany jako naruszenie.  Jeśli liczba naruszeń dla każdego obiektu w wynikach przekroczy określoną wartość, zostanie utworzony alert dla tego obiektu.

Błąd konfiguracji opcji *Aggregate on* lub *metricColumn* może spowodować awarię reguł alertów. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów, gdy reguła alertu dotyczącego pomiaru metryki jest nieprawidłowa](./alerts-troubleshoot-log.md#metric-measurement-alert-rule-is-incorrect).

#### <a name="example-of-metric-measurement-type-log-alert"></a>Przykład alertu dziennika typu pomiaru metryki

Rozważmy scenariusz, w którym chcesz otrzymywać alerty w przypadku, gdy dowolny komputer przekroczy użycie procesora o 90% trzy razy w ciągu 30 minut.  Można utworzyć regułę alertu z następującymi szczegółami:  

- **Zapytanie:** Wydajność | gdzie ObjectName = = "Processor" i CounterName = = "czas procesora (%)" | podsumowujący AggregatedValue = AVG (CounterValue) według bin (TimeGenerated, 5 m), Computer<br>
- **Okres:** 30 minut<br>
- **Częstotliwość alertów:** pięć minut<br>
- **Logika alertu — warunek &:** Większe niż 90<br>
- **Pole grupy (agregowane):** Komputerem
- **Wyzwalaj alert w oparciu o:** Całkowite naruszenia większe niż 2<br>

Zapytanie utworzy średnią wartość dla każdego komputera w odstępach 5-minutowych.  To zapytanie będzie uruchamiane co 5 minut w przypadku danych zbieranych w ciągu ostatnich 30 minut. Ponieważ wybrane pole grupy (agregowany) jest kolumną "Computer" — AggregatedValue jest podzielony na różne wartości "Computer" i średnie użycie procesora dla każdego komputera jest określony dla czasu, który jest 5 minut.  Przykładowy wynik zapytania dla (Powiedz) trzy komputery, będzie jak poniżej.


|TimeGenerated [UTC] |Computer (Komputer)  |AggregatedValue  |
|---------|---------|---------|
|20xx-XX-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-XX-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-XX-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-XX-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-XX-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-XX-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

Jeśli wynik kwerendy miał zostać wykreślony, zostanie wyświetlony jako.

![Przykładowe wyniki zapytania](media/alerts-unified-log/metrics-measurement-sample-graph.png)

W tym przykładzie zobaczymy w pojemnikach 5 minut dla każdego z trzech komputerów — średnie użycie procesora jako obliczone dla 5 minut. Próg 90 został naruszony przez Srw01 tylko raz w zasobniku 1:25. W porównaniu srv02 znajdowały przekracza 90 próg na 1:10, 1:15 i 1:25. Choć srv03 przekracza 90 próg w 1:10, 1:15, 1:20 i 1:30.
Ponieważ alert jest skonfigurowany do wyzwalania na podstawie łącznej liczby naruszeń jest więcej niż dwa, zobaczymy, że srv02 znajdowały i srv03 spełniają tylko kryteria. W związku z tym należy utworzyć oddzielne alerty dla srv02 znajdowały i srv03, ponieważ próg 90% dwa razy zostanie naruszony w wielu pojemnikach czasowych.  W przypadku *alertu wyzwalacza opartego na:* parametr został skonfigurowany dla opcji *ciągłego naruszania* , a następnie alert zostanie wyzwolony **tylko** dla srv03, ponieważ spowodował naruszenie progu dla trzech kolejnych przedziałów czasu od 1:10 do 1:20. A **nie** dla srv02 znajdowały, ponieważ narusza próg dla dwóch kolejnych przedziałów czasowych od 1:10 do 1:15.

## <a name="log-search-alert-rule---firing-and-state"></a>Reguła alertu wyszukiwania w dzienniku — uruchamianie i stan

Reguły alertów wyszukiwania w dzienniku działają tylko na logice skompilowanej w zapytaniu. System alertów nie ma żadnego innego kontekstu stanu systemu, Twojego zamiaru lub głównej przyczyny implikowanej przez zapytanie. W związku z tym alerty dzienników są określane jako Stany mniejsze. Warunki są oceniane jako "TRUE" lub "FALSE" przy każdym uruchomieniu.  Alert zostanie uruchomiony za każdym razem, gdy wynikiem oceny warunku alertu jest "TRUE", niezależnie od tego, że jest on wcześniej uruchamiany.    

Zobaczmy to zachowanie w działaniu z praktycznym przykładem. Załóżmy, że mamy regułę alertu dziennika o nazwie *contoso-log-alert*, która jest skonfigurowana tak, jak pokazano w przykładzie podanym w [przykładowym alertu dziennika typu liczba wyników](#example-of-number-of-records-type-log-alert). Warunek to niestandardowa kwerenda alertu zaprojektowana w celu wyszukania 500 kodu wyniku w dziennikach. Jeśli w dziennikach znaleziono co najmniej jeden kod wyniku 500, warunek alertu to true. 

W każdym przedziale czasowym system alertów platformy Azure oblicza warunek dotyczący *alertu contoso-log*.


| Czas    | Liczba rekordów zwracanych przez zapytanie przeszukiwania dzienników | Evalution warunku dziennika | Wynik 
| ------- | ----------| ----------| ------- 
| 1:05 PM | 0 rekordów | 0 nie jest > 0, więc FALSE |  Alert nie jest wyzwalany. Nie wywołano żadnych akcji.
| 1:10 PM | 2 rekordy | 2 > 0  | Wyzwolone alerty i grupy akcji o nazwie. Stan alertu jest aktywny.
| 1:15 PM | 5 rekordów | 5 > 0, tak więc prawda  | Wyzwolone alerty i grupy akcji o nazwie. Stan alertu jest aktywny.
| 1:20 PM | 0 rekordów | 0 nie jest > 0, więc FALSE |  Alert nie jest wyzwalany. Nie wywołano żadnych akcji. Stan alertu pozostał aktywny.

Jeśli na przykład użyto poprzedniego przypadku:

W przypadku alertów platformy Azure na 1:15 PM nie można ustalić, czy podstawowe problemy występujące w 1:10 utrzymują się, a jeśli rekordy są nowymi awariami netto lub powtarzają się starsze błędy w lokalizacji 1:10PM. Zapytanie udostępnione przez użytkownika może lub nie może uwzględniać wcześniejszych rekordów, a system nie wie. System alertów platformy Azure jest zbudowany z błędem na stronie przestroga i wyzwala alert i skojarzone akcje ponownie o godzinie 1:15. 

O godzinie 1:20 PM, gdy nie są wyświetlane rekordy z 500 kod wyniku, alerty platformy Azure nie mogą mieć pewności, że przyczyna 500 kodu wynikowego w 1:10 PM i 1:15 PM zostanie rozwiązany. Nie wiadomo, czy dla tych samych powodów wystąpią błędy 500. Z tego powodu *firma Contoso-log-alert* nie jest zmieniana na **rozwiązany** w pulpicie nawigacyjnym alertów platformy Azure i/lub powiadomienia nie są wysyłane z informacją o rozwiązaniu alertu. Tylko ty, który rozumie dokładny warunek lub powód dla logiki osadzonej w zapytaniu analitycznym, można [oznaczyć alert jako zamknięty](alerts-managing-alert-states.md) zgodnie z wymaganiami.

## <a name="pricing-and-billing-of-log-alerts"></a>Cennik i rozliczanie alertów dziennika

Cennik dotyczący alertów dotyczących dzienników znajduje się na stronie z [cennikiem Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) . Na rachunkach systemu Azure alerty dzienników są reprezentowane jako typ `microsoft.insights/scheduledqueryrules` z:

- Alerty dzienników dla Application Insights wyświetlane z dokładną nazwą alertu wraz z właściwościami grupy zasobów i alertu
- Alerty dzienników dla Log Analytics wyświetlane z dokładną nazwą alertu wraz z właściwościami grupy zasobów i alertu; podczas tworzenia przy użyciu [interfejsu API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules)

[Starsza wersja interfejsu API log Analytics](./api-alerts.md) zawiera akcje alertów i harmonogramy w ramach log Analytics zapisanego wyszukiwania, a nie odpowiednie [zasoby platformy Azure](../../azure-resource-manager/management/overview.md). W związku z tym, aby włączyć rozliczenia dla takich starszych alertów dziennika utworzonych dla Log Analytics przy użyciu Azure Portal **bez** [przełączania do nowego interfejsu API](./alerts-log-api-switch.md) lub za pośrednictwem [starszych log Analytics](./api-alerts.md) `microsoft.insights/scheduledqueryrules` Ukryte reguły dotyczące alertów, które zostały utworzone na potrzeby rozliczania `microsoft.insights/scheduledqueryrules` , jak pokazano na stronie `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` z właściwościami grupy zasobów i alertu.

> [!NOTE]
> Jeśli istnieją nieprawidłowe znaki, takie jak `<, >, %, &, \, ?, /` są obecne, zostaną zastąpione przez `_` wartość w polu Ukryta Nazwa reguły noalertu i w związku z tym również na rachunku na platformie Azure.

Aby usunąć ukryte zasoby scheduleQueryRules utworzone w celu rozliczenia reguł alertów przy użyciu [starszej wersji interfejsu API log Analytics](api-alerts.md), użytkownik może wykonać jedną z następujących czynności:

- Każdy użytkownik może [przełączyć preferencję interfejsu API dla reguł alertów w obszarze roboczym log Analytics](./alerts-log-api-switch.md) i bez utraty ich reguł alertów ani monitorować przechodzenie do Azure Resource Manager zgodnych [interfejsów API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules). Eliminuje to konieczność podejmowania dla rozliczeń bardzo ukrytych reguł alertów.
- Lub jeśli użytkownik nie chce przełączać preferencji interfejsu API, użytkownik będzie musiał **usunąć** oryginalny harmonogram i akcję alertu przy użyciu [STARSZEJ wersji interfejsu API log Analytics](api-alerts.md) lub usunąć w [Azure Portal oryginalnej regule alertu dziennika](./alerts-log.md#view--manage-log-alerts-in-azure-portal)

Ponadto w przypadku ukrytych zasobów scheduleQueryRules utworzonych na potrzeby rozliczania reguł alertów przy użyciu [starszej wersji interfejsu API log Analytics](api-alerts.md)wszelkie operacje modyfikacji, takie jak Put, zakończą się niepowodzeniem `microsoft.insights/scheduledqueryrules`Zasady dotyczące typu są przeznaczone do celów rozliczania reguł alertów utworzonych przy użyciu [starszej wersji interfejsu API log Analytics](api-alerts.md). Wszelkie modyfikacje reguły alertów należy wykonać przy użyciu [starszej wersji interfejsu api log Analytics](api-alerts.md) (lub) użytkownik może [przełączyć preferencję interfejsu API, aby reguły alertów](./alerts-log-api-switch.md) korzystały z [interfejsu API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) .

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej [na temat tworzenia alertów dziennika na platformie Azure](./alerts-log.md).
* Informacje [o elementach webhook w alertach dziennika na platformie Azure](alerts-log-webhook.md).
* Dowiedz się więcej o [alertach platformy Azure](./alerts-overview.md).
* Dowiedz się więcej o [Application Insights](../log-query/log-query-overview.md).
* Dowiedz się więcej o [log Analytics](../log-query/log-query-overview.md).

