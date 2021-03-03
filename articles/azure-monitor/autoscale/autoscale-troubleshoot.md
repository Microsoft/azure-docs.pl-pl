---
title: Rozwiązywanie problemów z funkcją automatycznego skalowania platformy Azure
description: Śledzenie problemów z funkcją automatycznego skalowania platformy Azure używanej w usługach Service Fabric, Virtual Machines, Web Apps i w chmurze.
ms.topic: conceptual
ms.date: 11/4/2019
ms.subservice: autoscale
ms.openlocfilehash: a0fed6c2d06edcb2c9eb8d715feb0ef6c6ade46f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711403"
---
# <a name="troubleshooting-azure-autoscale"></a>Rozwiązywanie problemów z funkcją automatycznego skalowania platformy Azure
 
Funkcja automatycznego skalowania Azure Monitor pomaga w wykorzystaniu odpowiedniej ilości zasobów, aby obsłużyć obciążenie aplikacji. Pozwala to na dodawanie zasobów w celu zwiększenia obciążenia, a także oszczędność pieniędzy dzięki usunięciu zasobów znajdujących się w stanie bezczynności. Możesz skalować na podstawie harmonogramu, ustalonej wartości daty i godziny lub wybranej metryki zasobów. Aby uzyskać więcej informacji, zobacz [Omówienie automatycznego skalowania](autoscale-overview.md).

Usługa automatycznego skalowania udostępnia metryki i dzienniki, aby zrozumieć, jakie akcje skalowania wystąpiły, oraz ocenę warunków, które doprowadziły do tych działań. Odpowiedzi na pytania, takie jak:

- Dlaczego moja usługa została przeskalowana w poziomie?
- Dlaczego moja usługa nie jest skalowana?
- Dlaczego akcja skalowania automatycznego kończy się niepowodzeniem?
- Dlaczego akcja skalowania automatycznego trwa z czasem skalowania?
  
## <a name="autoscale-metrics"></a>Metryki skalowania automatycznego

Funkcja automatycznego skalowania udostępnia [cztery metryki](../essentials/metrics-supported.md#microsoftinsightsautoscalesettings) , aby zrozumieć ich działanie. 

- **Obserwowana wartość metryki** — wartość metryki wybranej do wykonania akcji skalowania, która jest widoczna lub obliczana przez aparat skalowania automatycznego. Ponieważ pojedyncze ustawienie automatycznego skalowania może mieć wiele reguł i w związku z tym wiele źródeł metryk, można filtrować przy użyciu "źródła metryk" jako wymiaru.
- **Próg metryki** — próg ustawiony do wykonania akcji skalowania. Ponieważ pojedyncze ustawienie automatycznego skalowania może mieć wiele reguł i w związku z tym wiele źródeł metryk, można filtrować przy użyciu "reguły metryki" jako wymiaru.
- **Zaobserwowana pojemność** — aktywna liczba wystąpień zasobu docelowego widzianych przez aparat skalowania automatycznego.
- **Zainicjowane akcje skalowania** — liczba akcji zwiększania i zmniejszania skali inicjowanych przez aparat skalowania automatycznego. Można filtrować według skalowania w poziomie i skalowania w działaniach.

Możesz użyć [Eksplorator metryk](../essentials/metrics-getting-started.md) do grafowania powyższych metryk wszystko w jednym miejscu. Wykres powinien pokazać:

  - rzeczywista Metryka
  - Metryka widziana/obliczana przez aparat automatycznego skalowania
  - próg akcji skalowania
  - Zmiana pojemności 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>Przykład 1 — analizowanie prostej reguły automatycznego skalowania 

Mamy proste ustawienie automatycznego skalowania dla zestawu skalowania maszyn wirtualnych:

- skalowanie w górę, gdy średni procent procesora CPU zestawu jest większy niż 70% przez 10 minut 
- skaluje się, gdy wartość procentowa procesora CPU zestawu jest mniejsza niż 5% przez więcej niż 10 minut. 

Przejrzyjmy metryki z usługi skalowania automatycznego.
 
![Zrzut ekranu przedstawia przykład użycia procesora CPU zestawu skalowania maszyn wirtualnych.](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![Przykład procesora CPU zestawu skalowania maszyn wirtualnych (%)](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

***Rysunek 1a-procentową metrykę procesora dla zestawu skalowania maszyn wirtualnych i zaobserwowana Metryka wartości metryki dla ustawienia skalowania automatycznego***

![Próg metryki i zaobserwowana pojemność](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

***Rysunek 1B — próg metryki i zaobserwowana pojemność***

Na rysunku 1b **próg metryki** (jasnoniebieski wiersz) dla reguły skalowania w poziomie wynosi 70.  **Obserwowana pojemność** (ciemny niebieski wiersz) pokazuje liczbę aktywnych wystąpień, która jest obecnie 3. 

> [!NOTE]
> Aby wyświetlić próg skalowania w poziomie i regułę skalowania w poziomie (Zmniejsz), należy przefiltrować **próg metryki** przez regułę wyzwalacza skalowania w poziomie (zwiększenie). 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>Przykład 2 — zaawansowane Skalowanie automatyczne dla zestawu skalowania maszyn wirtualnych

Istnieje ustawienie automatycznego skalowania, które umożliwia skalowanie w poziomie zasobów zestawu skalowania maszyn wirtualnych w oparciu o własne **przepływy wychodzące** metryk. Zauważ, że zaznaczono opcję **Podziel metrykę według liczby wystąpień** dla progu metryki. 

Reguła akcji skalowania to: 

Jeśli wartość **przepływu wychodzącego na wystąpienie** jest większa niż 10, usługa automatycznego skalowania powinna skalować w poziomie o 1 wystąpienie. 

W takim przypadku wartość metryki obsługiwanego przez aparat automatycznego skalowania jest obliczana jako rzeczywista wartość metryki podzielona przez liczbę wystąpień. Jeśli obserwowana wartość metryki jest mniejsza od progu, nie zostanie zainicjowana akcja skalowania w poziomie. 
 
![Zrzut ekranu przedstawia stronę średnia przepływy wychodzące z przykładem wykresów metryk skalowania maszyn wirtualnych.](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![Przykład wskaźników automatycznego skalowania dla zestawu skalowania maszyn wirtualnych](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

***Rysunek 2. przykład wskaźników automatycznego skalowania dla zestawu skalowania maszyn wirtualnych***

Na rysunku 2 widoczne są dwa wykresy metryczne. 

Wykres na górze pokazuje rzeczywistą wartość metryki **przepływy wychodzące** . Rzeczywista wartość to 6. 

Wykres u dołu pokazuje kilka wartości. 
 - **Obserwowana wartość metryki** (jasne niebieskie) to 3, ponieważ istnieją 2 aktywne wystąpienia, a 6 podzielone przez 2 to 3. 
 - **Obserwowana pojemność** (purpurowa) pokazuje liczbę wystąpień widzianą przez aparat skalowania automatycznego. 
 - **Próg metryki** (jasnozielony) jest ustawiony na wartość 10. 

Jeśli istnieje wiele reguł akcji skalowania, można użyć dzielenia lub opcji **Dodaj filtr** na wykresie Eksploratora metryk, aby sprawdzić metrykę według określonego źródła lub zasady. Aby uzyskać więcej informacji na temat dzielenia wykresu metryki, zobacz [Zaawansowane funkcje wykresów metryk — dzielenie](../essentials/metrics-charts.md#apply-splitting)

## <a name="example-3---understanding-autoscale-events"></a>Przykład 3 — Omówienie zdarzeń skalowania automatycznego

Na ekranie ustawienia skalowania automatycznego przejdź do karty **historia uruchamiania** , aby zobaczyć najnowsze akcje skalowania. Na karcie jest również wyświetlana zmiana **zaobserwowanej wydajności** w czasie. Aby uzyskać więcej szczegółów na temat wszystkich akcji automatycznego skalowania, w tym operacji, takich jak aktualizowanie/usuwanie ustawień automatycznego skalowania, zobacz Dziennik aktywności i filtrowanie według operacji skalowania automatycznego.

![Historia uruchamiania ustawień automatycznego skalowania](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>Automatyczne skalowanie dzienników zasobów

Tak samo jak w przypadku każdego innego zasobu platformy Azure, usługa skalowania automatycznego udostępnia [dzienniki zasobów](../essentials/platform-logs-overview.md). Istnieją dwie kategorie dzienników.

- **Obliczenia skalowania automatycznego** — aparat skalowania automatycznego rejestruje wpisy dziennika dla każdej oceny pojedynczego warunku przy każdym sprawdzaniu.  Wpis zawiera szczegółowe informacje o obserwowanych wartościach metryk, obliczeniach reguł, a jeśli Ocena spowodowała akcję skalowania lub nie.

- **Akcje skalowania automatycznego skalowania** : aparat rejestruje zdarzenia akcji skalowania zainicjowane przez usługę skalowania automatycznego i wyniki tych akcji skalowania (sukces, Niepowodzenie i ilość wykorzystanych przez usługę skalowania automatycznego).

Podobnie jak w przypadku dowolnej Azure Monitor obsługiwanej usługi, można użyć [ustawień diagnostycznych](../essentials/diagnostic-settings.md) do kierowania tych dzienników:

- do obszaru roboczego Log Analytics na potrzeby szczegółowej analizy
- Aby Event Hubs, a następnie do narzędzi niezwiązanych z platformą Azure
- do konta usługi Azure Storage w celu archiwizacji  

![Ustawienia diagnostyki automatycznego skalowania](media/autoscale-troubleshoot/diagnostic-settings.png)

Na poprzedniej ilustracji przedstawiono ustawienia diagnostyki automatycznego skalowania Azure Portal. Istnieje możliwość wybrania karty Diagnostyka/dzienniki zasobów i włączenia zbierania dzienników i routingu. Tę samą akcję można także wykonać przy użyciu interfejsów API REST, interfejsu wiersza polecenia, programu PowerShell, Menedżer zasobów szablonów dla ustawień diagnostycznych, wybierając typ zasobu jako *Microsoft. Insights/AutoscaleSettings*. 

## <a name="troubleshooting-using-autoscale-logs"></a>Rozwiązywanie problemów przy użyciu dzienników automatycznego skalowania 

W celu uzyskania najlepszego środowiska rozwiązywania problemów zalecamy kierowanie dzienników do Azure Monitor dzienników (Log Analytics) za pomocą obszaru roboczego podczas tworzenia ustawienia skalowania automatycznego. Ten proces jest pokazywany na rysunku w poprzedniej sekcji. Możesz sprawdzić poprawność ocen i akcji skalowania lepiej przy użyciu Log Analytics.

Po skonfigurowaniu dzienników automatycznego skalowania do wysłania do obszaru roboczego Log Analytics można wykonać następujące zapytania, aby sprawdzić dzienniki. 

Aby rozpocząć, wypróbuj to zapytanie w celu wyświetlenia najnowszych dzienników oceny automatycznego skalowania:

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

Lub spróbuj wykonać następujące zapytanie, aby wyświetlić najnowsze dzienniki akcji skalowania:

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

Poniższe sekcje zawierają odpowiedzi na te pytania. 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>Wystąpiła nieoczekiwana akcja skalowania

Najpierw wykonaj zapytanie dotyczące akcji skalowania, aby znaleźć wybraną akcję skalowania. Jeśli jest to najnowsza akcja skalowania, należy użyć następującej kwerendy:

```Kusto
AutoscaleScaleActionsLog
| take 1
```

Wybierz pole Identyfikator korelacji z dziennika akcji skalowania. Użyj identyfikator korelacji, aby znaleźć odpowiedni dziennik oceny. Wykonanie poniższego zapytania spowoduje wyświetlenie wszystkich reguł i warunków ocenionych na potrzeby tej akcji skalowania.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>Jaki profil spowodował akcję skalowania?

Wystąpiła akcja skalowana, ale istnieją nakładające się reguły i profile, które spowodowały wygenerowanie tej akcji. 

Znajdź identyfikator korelacji akcji skalowania (zgodnie z opisem w przykładzie 1), a następnie wykonaj zapytanie dotyczące dzienników oceny, aby dowiedzieć się więcej o profilu.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

Całą ocenę profilu można także zrozumieć lepiej przy użyciu następującego zapytania

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>Akcja skalowania nie została wykonana

Oczekiwano akcji skalowania i nie została ona wykonana. Nie mogą istnieć zdarzenia ani dzienniki akcji skalowania.

Przejrzyj metryki skalowania automatycznego, jeśli używasz reguły skalowania opartej na metrykach. Istnieje możliwość, że **zaobserwowana wartość metryki** lub **zaobserwowana pojemność** nie są oczekiwane i dlatego reguła skalowania nie została wyzwolona. Nadal będą widoczne oceny, ale nie reguła skalowania w poziomie. Istnieje również możliwość, że czas chłodzenia zachowa akcję skalowania z wystąpienia. 

 Przejrzyj dzienniki oceny skalowania automatycznego w czasie oczekiwania na wykonanie akcji skalowania. Przejrzyj wszystkie oceny, które zakończyło się, i przyczynę niewyzwalania akcji skalowania.


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>Akcja skalowania nie powiodła się

Może istnieć przypadek, w którym usługa automatycznego skalowania wykonała akcję skalowania, ale system nie może skalować lub zakończyć działania skalowania. Użyj tego zapytania, aby znaleźć nieudane akcje skalowania.

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

Utwórz reguły alertów, aby otrzymywać powiadomienia o akcjach lub błędach skalowania automatycznego. Możesz również utworzyć reguły alertów, aby otrzymywać powiadomienia o zdarzeniach skalowania automatycznego.

## <a name="schema-of-autoscale-resource-logs"></a>Schemat dzienników zasobów automatycznego skalowania

Aby uzyskać więcej informacji, zobacz [dzienniki zasobów automatycznego skalowania](autoscale-resource-log-schema.md)

## <a name="next-steps"></a>Następne kroki
Przeczytaj informacje o [najlepszych rozwiązaniach dotyczących skalowania automatycznego](autoscale-best-practices.md).