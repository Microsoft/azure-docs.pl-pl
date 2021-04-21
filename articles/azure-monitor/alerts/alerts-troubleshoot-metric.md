---
title: Rozwiązywanie problemów z alertami metryk platformy Azure
description: Typowe problemy z alertami Azure Monitor metrykami i możliwymi rozwiązaniami.
author: harelbr
ms.author: harelbr
ms.topic: troubleshooting
ms.date: 04/12/2021
ms.openlocfilehash: fc9af94b07add5728201baaa8fa6992728a60a8c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786013"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>Rozwiązywanie problemów z alertami metryk usługi Azure Monitor 

W tym artykule omówiono typowe problemy związane z Azure Monitor [metrykami](alerts-metric-overview.md) oraz sposoby ich rozwiązywania.

Azure Monitor alerty proaktywnie powiadamiają o ważnych warunkach znalezionych w danych monitorowania. Pozwalają one identyfikować i rozsyłać problemy, zanim użytkownicy systemu ich zauważą. Aby uzyskać więcej informacji na temat alertów, zobacz [Omówienie alertów w Microsoft Azure](./alerts-overview.md).

## <a name="metric-alert-should-have-fired-but-didnt"></a>Alert metryki powinien zostać wyzowany, ale nie został 

Jeśli uważasz, że alert metryki powinien zostać wyzpalony, ale nie został wyzpalony i nie został odnaleziony w Azure Portal, spróbuj wykonać następujące czynności:

1. **Konfiguracja** — przejrzyj konfigurację reguły alertu metryki, aby upewnić się, że jest prawidłowo skonfigurowana:
    - Sprawdź, czy **ustawienia Typ agregacji** i **Poziom szczegółowości agregacji (okres)** są skonfigurowane zgodnie z oczekiwaniami. **Typ agregacji** określa sposób agregowania wartości metryk (dowiedz się więcej [tutaj),](../essentials/metrics-aggregation-explained.md#aggregation-types)a poziom szczegółowości agregacji **(okres)** określa, jak daleko wstecz ocena agreguje wartości metryk przy każdym uruchamianym ustaleniu reguły alertu.
    -  Sprawdź, czy **wartość progowa** lub **czułość** są skonfigurowane zgodnie z oczekiwaniami.
    - W przypadku reguły alertu, która używa progów dynamicznych, sprawdź, czy ustawienia  zaawansowane są skonfigurowane, ponieważ wartość Liczba naruszeń może filtrować alerty, a pole Ignoruj dane przed może mieć wpływ na sposób obliczania progów. 

       > [!NOTE] 
       > Progi dynamiczne wymagają co najmniej 3 dni i 30 próbek metryk, zanim stanie się aktywne.

2. **Wyzpalone, ale bez powiadomienia** — [przejrzyj](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) listę wyzwłaszanych alertów, aby sprawdzić, czy możesz zlokalizować wyzzowany alert. Jeśli widzisz alert na liście, ale masz problem z niektórymi jego akcjami lub powiadomieniami, zobacz więcej informacji [tutaj.](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)

3. **Już aktywne** — sprawdź, czy w szeregach czasu metryki, dla których oczekiwano uzyskania alertu, został już wyzzowany alert. Alerty metryk są stanowe, co oznacza, że po uruchomieniu alertu w ramach określonego szeregu czasowego metryki dodatkowe alerty w tym szeregu czasowym zostaną wyzwolone dopiero wtedy, gdy problem nie będzie już występował. Ten wybór projektu zmniejsza szum. Alert jest rozwiązywany automatycznie, gdy warunek alertu nie zostanie spełniony w przypadku trzech kolejnych ocen.

4. **Używane wymiary** — jeśli wybrano niektóre wartości wymiarów dla metryki, [](./alerts-metric-overview.md#using-dimensions)reguła alertu monitoruje poszczególne szeregi czasu metryk (zgodnie z kombinacją wartości wymiarów) pod uwagę naruszenia progu. Aby również monitorować szeregi czasu zagregowanej metryki (bez żadnych wybranych wymiarów), skonfiguruj dodatkową regułę alertu dla metryki bez wybierania wymiarów.

5. **Agregacja i poziom szczegółowości czasu** — jeśli wizualizujesz metrykę przy użyciu wykresów [metryk,](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)upewnij się, że:
    * Wybrana **agregacja na** wykresie metryki jest taka sama jak **Typ agregacji** w regułę alertu
    * Wybrany poziom **szczegółowości czasu** jest taki sam jak Poziom szczegółowości **agregacji (okres)** w regułę alertu (i nie jest ustawiony na wartość "Automatycznie")

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>Alert metryki został wyzowany, ale nie powinien

Jeśli uważasz, że alert metryki nie powinien zostać wyzowany, ale został, następujące kroki mogą pomóc rozwiązać ten problem.

1. Przejrzyj listę [wyzrzanych alertów,](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) aby zlokalizować wyzrzaony alert, a następnie kliknij, aby wyświetlić jego szczegóły. Przejrzyj informacje podane w obszarze Dlaczego ten alert został wyzwolony?, aby wyświetlić wykres **metryki,** wartość metryki i wartość progu w czasie wyzwolenia alertu.  

    > [!NOTE] 
    > Jeśli używasz typu warunku Progi dynamiczne i sądzisz, że użyte progi nie były poprawne, podaj opinię za pomocą ikony frustratora. Ta opinia będzie mieć wpływ na badania algorytmiczne uczenia maszynowego i pomoże ulepszyć wykrywanie w przyszłości.

2. Jeśli wybrano wiele wartości wymiarów dla metryki, alert  zostanie wyzwolony, gdy dowolny szereg czasowy metryki (zgodnie z kombinacją wartości wymiarów) przekroczy wartość progową. Więcej informacji o korzystaniu z wymiarów w alertach dotyczących metryk możesz znaleźć [tutaj](./alerts-metric-overview.md#using-dimensions).

3. Przejrzyj konfigurację reguły alertu, aby upewnić się, że jest ona prawidłowo skonfigurowana:
    - Sprawdź, czy **ustawienia Typ agregacji,** Poziom szczegółowości  **agregacji (okres)** i **Wartość progowa** lub Czułość są skonfigurowane zgodnie z oczekiwaniami
    - W przypadku reguły alertu, która używa progów dynamicznych, sprawdź, czy skonfigurowano ustawienia zaawansowane, ponieważ funkcja Liczba naruszeń może filtrować alerty, a funkcja Ignoruj dane przed może wpływać na sposób obliczania progów  

   > [!NOTE]
   > Progi dynamiczne wymagają co najmniej 3 dni i 30 próbek metryk, zanim stanie się aktywne.

4. Jeśli wizualizujesz metrykę przy użyciu [wykresu metryk,](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)upewnij się, że:
    - Wybrana **agregacja na** wykresie metryki jest taka sama jak **typ agregacji** w regułę alertu
    - Wybrany poziom **szczegółowości czasu jest** taki sam jak poziom szczegółowości **agregacji (okres)** w regułę alertu (i nie jest ustawiony na wartość "Automatycznie")

5. Jeśli alert został wyzstartowany, gdy zostały już wyzwane alerty monitorowane przy użyciu tych samych kryteriów (które nie zostały rozwiązane), sprawdź, czy reguła alertu została skonfigurowana z *właściwością autoMitigate* ustawioną na **wartość false** (tę właściwość można skonfigurować tylko za pomocą interfejsu REST,programu PowerShell/interfejsu wiersza polecenia, więc sprawdź skrypt używany do wdrażania reguły alertu). W takim przypadku reguła alertu nie rozwiązuje automatycznie wyzwalanych alertów i nie wymaga rozwiązania wyzwalanych alertów przed jego ponownego wyzwalania.


## <a name="cant-find-the-metric-to-alert-on---virtual-machines-guest-metrics"></a>Nie można znaleźć metryki, dla których ma być alert — metryki gościa maszyn wirtualnych

Aby ostrzegania o metrykach systemu operacyjnego gościa maszyn wirtualnych (na przykład pamięci, miejsca na dysku), upewnij się, że zainstalowano agenta wymaganego do zbierania tych danych do Azure Monitor Metryki:
- [Maszyny wirtualne z systemem Windows](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
- [Maszyny wirtualne z systemem Linux](../essentials/collect-custom-metrics-linux-telegraf.md)

Aby uzyskać więcej informacji na temat zbierania danych z systemu operacyjnego gościa maszyny wirtualnej, zobacz [tutaj](../vm/monitor-vm-azure.md#guest-operating-system).

> [!NOTE] 
> Jeśli skonfigurowano metryki gościa do wysłania do obszaru roboczego usługi Log Analytics, są  one wyświetlane w zasobie obszaru roboczego usługi Log Analytics i będą wyświetlane tylko po utworzeniu reguły alertu, która je monitoruje. W tym celu postępuj zgodnie z instrukcjami, aby [skonfigurować alert metryki na potrzeby dzienników](./alerts-metric-logs.md#configuring-metric-alert-for-logs).

> [!NOTE] 
> Monitorowanie metryki gościa dla wielu maszyn wirtualnych za pomocą jednej reguły alertu nie jest obecnie obsługiwane przez alerty metryk. Można to osiągnąć za pomocą reguły [alertu dziennika](./alerts-unified-log.md). W tym celu upewnij się, że metryki gościa są zbierane w obszarze roboczym usługi Log Analytics, i utwórz regułę alertu dziennika w obszarze roboczym.

## <a name="cant-find-the-metric-to-alert-on"></a>Nie można znaleźć metryki, dla których ma być alert

Jeśli szukasz alertu dotyczącego określonej metryki, ale nie widzisz go podczas tworzenia reguły alertu, sprawdź następujące kwestie:
- Jeśli nie widzisz żadnych metryk dla zasobu, [sprawdź, czy typ zasobu jest obsługiwany na potrzeby alertów dotyczących metryk](./alerts-metric-near-real-time.md).
- Jeśli widzisz pewne metryki dla zasobu, ale nie możesz znaleźć określonej metryki, [sprawdź, czy ta metryka jest dostępna](../essentials/metrics-supported.md), i jeśli tak, zobacz jej opis, aby sprawdzić, czy jest ona dostępna tylko w określonych wersjach lub wydaniach zasobu.
- Jeśli metryka nie jest dostępna dla zasobu, może być dostępna w dziennikach zasobu i monitorowana przy użyciu alertów dzienników. Zobacz tutaj, aby uzyskać więcej informacji o [gromadzeniu i analizowaniu dzienników z zasobu platformy Azure](../essentials/tutorial-resource-logs.md).

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>Nie można znaleźć wymiaru metryki, dla których ma być alert

Jeśli chcesz ostrzegania o określonych wartościach [wymiarów](./alerts-metric-overview.md#using-dimensions)metryki, ale nie możesz znaleźć tych wartości, zwróć uwagę na następujące kwestie:

1. Wyświetlenie wartości wymiarów na liście **Wartości wymiarów** może potrwać kilka minut
2. Wyświetlone wartości wymiarów są oparte na danych metryk zebranych w ciągu ostatniego dnia
3. Jeśli wartość wymiaru nie jest jeszcze emitowana lub nie jest wyświetlana, możesz użyć opcji „Dodaj wartość niestandardową”, aby dodać niestandardową wartość wymiaru
4. Jeśli chcesz ostrzegania o wszystkich możliwych wartościach wymiaru (w tym przyszłych wartościach), wybierz opcję "Zaznacz wszystkie bieżące i przyszłe wartości"
5. Wymiary metryk niestandardowych Application Insights są domyślnie wyłączone. Aby włączyć kolekcję wymiarów dla tych metryk niestandardowych, zobacz [tutaj](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>Reguły alertów metryk są nadal zdefiniowane dla usuniętego zasobu 

Podczas usuwania zasobu platformy Azure skojarzone reguły alertów metryk nie są automatycznie usuwane. Aby usunąć reguły alertów skojarzone z zasobem, który został usunięty:

1. Otwórz grupę zasobów, w której zdefiniowano usunięty zasób
1. Na liście wyświetlającej zasoby zaznacz pole wyboru **Pokaż ukryte typy**
1. Filtruj listę według typu **microsoft.insights/metricalerts**
1. Wybierz odpowiednie reguły alertów i wybierz pozycję **Usuń**

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>Czy alerty dotyczące metryk są pojawiają się za każdym razem, gdy warunek zostanie spełniony

Alerty dotyczące metryk są domyślnie stanowe, dlatego dodatkowe alerty nie są wyzjęne, jeśli w danym szeregu czasowym już wyzłoszony alert. Jeśli chcesz ustawić konkretną regułę alertu metryki jako bezstanową i otrzymywać alerty przy każdej ocenie, w której warunek alertu jest spełniony, utwórz regułę alertu programowo (na przykład za pomocą poleceń Resource Manager, [PowerShell,](/powershell/module/az.monitor/) [REST,](/rest/api/monitor/metricalerts/createorupdate) [CLI](/cli/azure/monitor/metrics/alert)) i ustaw właściwość *autoMitigate* na wartość ["False".](./alerts-metric-create-templates.md)

> [!NOTE] 
> Dzięki temu, że reguła alertu metryki jest bezstratna, nie można rozwiązać wyzbranych alertów, więc nawet po tym, jak warunek nie zostanie spełniony, wyzwane alerty pozostaną w stanie wyzłaszanym do 30-dniowego okresu przechowywania.

## <a name="define-an-alert-rule-on-a-custom-metric-that-isnt-emitted-yet"></a>Definiowanie reguły alertu dla metryki niestandardowej, która nie została jeszcze emitowana

Podczas tworzenia reguły alertu metryki nazwa metryki jest weryfikowana względem interfejsu [API](/rest/api/monitor/metricdefinitions/list) definicji metryk, aby upewnić się, że istnieje. W niektórych przypadkach chcesz utworzyć regułę alertu dla metryki niestandardowej jeszcze przed jej emitem. Na przykład podczas tworzenia (przy użyciu szablonu Resource Manager) zasobu Application Insights, który będzie emitować metrykę niestandardową, wraz z regułą alertu, która monitoruje metrykę.

Aby uniknąć błędu wdrażania podczas próby zweryfikowania definicji metryk niestandardowych, możesz użyć *parametru skipMetricValidation* w sekcji kryteriów reguły alertu, co spowoduje pominięcie walidacji metryki. Zobacz poniższy przykład, aby dowiedzieć się, jak używać tego parametru w Resource Manager szablonu. Aby uzyskać więcej informacji, zobacz [pełne przykłady szablonów Resource Manager tworzenia reguł alertów dotyczących metryk.](./alerts-metric-create-templates.md)

```json
"criteria": {
    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
        "allOf": [
            {
                "name" : "condition1",
                "metricName": "myCustomMetric",
                "metricNamespace": "myCustomMetricNamespace",
                "dimensions":[],
                "operator": "GreaterThan",
                "threshold" : 10,
                "timeAggregation": "Average",
                "skipMetricValidation": true
            }
        ]
    }
```

## <a name="export-the-azure-resource-manager-template-of-a-metric-alert-rule-via-the-azure-portal"></a>Eksportowanie Azure Resource Manager szablonu reguły alertu metryki za pośrednictwem Azure Portal

Wyeksportowanie Resource Manager reguły alertu metryki pomaga zrozumieć jej składnię i właściwości JSON i może służyć do automatyzowania przyszłych wdrożeń.
1. W Azure Portal otwórz regułę alertu, aby wyświetlić jej szczegóły.
2. Kliknij pozycję **Właściwości**.
3. W **obszarze Automatyzacja** wybierz **pozycję Eksportuj szablon**.

## <a name="metric-alert-rules-quota-too-small"></a>Zbyt mały limit przydziału reguł alertów metryk

Dozwolona liczba reguł alertów dotyczących metryk na subskrypcję podlega [limitom przydziału.](../service-limits.md)

Jeśli osiągnięto limit przydziału, następujące kroki mogą pomóc rozwiązać ten problem:
1. Spróbuj usunąć lub wyłączyć reguły alertów dotyczących metryk, które nie są już używane.

2. Zacznij korzystać z reguł alertów dotyczących metryk, które monitorują wiele zasobów. Dzięki tej możliwości pojedyncza reguła alertu może monitorować wiele zasobów przy użyciu tylko jednej reguły alertu wliczanych do limitu przydziału. Aby uzyskać więcej informacji na temat tej możliwości i obsługiwanych typów zasobów, zobacz [tutaj](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).

3. Jeśli chcesz zwiększyć limit przydziału, otwórz wniosek o pomoc techniczną i podaj następujące informacje:

    - Identyfikatory subskrypcji, dla których należy zwiększyć limit przydziału
    - Typ zasobu dla zwiększenia limitu przydziału: **Alerty dotyczące metryk** lub **Alerty metryk (klasyczne)**
    - Żądany limit przydziału

## <a name="check-total-number-of-metric-alert-rules"></a>Sprawdzanie łącznej liczby reguł alertów dotyczących metryk

Aby sprawdzić bieżące użycie reguł alertów dotyczących metryk, wykonaj poniższe kroki.

### <a name="from-the-azure-portal"></a>Z witryny Azure Portal

1. Otwórz ekran **Alerty** i kliknij pozycję **Zarządzaj regułami alertów**
2. Filtruj do odpowiedniej subskrypcji przy użyciu **kontrolki listy** rozwijanej Subskrypcja
3. Upewnij się, ŻE NIE należy filtrować do określonej grupy zasobów, typu zasobu lub zasobu
4. W **kontrolce listy** rozwijanej Typ sygnału wybierz **pozycję Metryki**
5. Sprawdź, czy **kontrolka** listy rozwijanej Stan jest ustawiona na wartość **Włączone**
6. Łączna liczba reguł alertów dotyczących metryk jest wyświetlana powyżej listy reguł alertów

### <a name="from-api"></a>Za pomocą interfejsu API

- PowerShell — [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2)
- Interfejs API REST — [lista według subskrypcji](/rest/api/monitor/metricalerts/listbysubscription)
- Interfejs wiersza polecenia platformy Azure — [az monitor metrics alert list](/cli/azure/monitor/metrics/alert#az_monitor_metrics_alert_list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Zarządzanie regułami alertów przy użyciu szablonów Resource Manager, interfejsu API REST, programu PowerShell lub interfejsu wiersza polecenia platformy Azure

Jeśli podczas tworzenia, aktualizowania, pobierania lub usuwania alertów dotyczących metryk przy użyciu szablonów usługi Resource Manager, interfejsu API REST, programu PowerShell lub interfejsu wiersza polecenia platformy Azure (CLI) występuje problem, poniższe kroki mogą pomóc rozwiązać ten problem.

### <a name="resource-manager-templates"></a>Szablony usługi Resource Manager

- Przejrzyj listę [typowych błędów wdrożeń platformy Azure](../../azure-resource-manager/templates/common-deployment-errors.md) i odpowiednie procedury rozwiązywania problemów
- Zapoznaj się z [alertami metryki Azure Resource Manager przykładami szablonów,](./alerts-metric-create-templates.md) aby upewnić się, że prawidłowo podajesz wszystkie parametry

### <a name="rest-api"></a>Interfejs API REST

Przejrzyj przewodnik [po interfejsie API REST,](/rest/api/monitor/metricalerts/) aby sprawdzić, czy wszystkie parametry są prawidłowo przekazywania

### <a name="powershell"></a>PowerShell

Upewnij się, że używasz odpowiednich poleceń cmdlet programu PowerShell dla alertów dotyczących metryk:

- Polecenia cmdlet programu PowerShell dla alertów dotyczących metryk są dostępne w [module AZ.Monitor](/powershell/module/az.monitor/)
- Upewnij się, że używasz polecenia cmdlet kończącego się na "V2" w przypadku nowych (nie klasycznych) alertów dotyczących metryk (na przykład [Add-AzMetricAlertRuleV2)](/powershell/module/az.monitor/add-azmetricalertrulev2)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Upewnij się, że używasz odpowiednich poleceń interfejsu wiersza polecenia dla alertów dotyczących metryk:

- Polecenia interfejsu wiersza polecenia dla alertów metryk rozpoczynają się od `az monitor metrics alert`. Przejrzyj [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure/monitor/metrics/alert), aby poznać składnię.
- Możesz zapoznać się z [przykładem przedstawiającym sposób użycia interfejsu wiersza polecenia alertów dotyczących metryk](./alerts-metric.md#with-azure-cli)
- Przed utworzeniem alertu dotyczącego metryki niestandardowej upewnij, że nazwa metryki jest poprzedzona odpowiednią przestrzenią nazw metryki: PRZESTRZEŃ_NAZW.METRYKA

### <a name="general"></a>Ogólne

- Jeśli występuje `Metric not found` błąd:

   - W przypadku metryk platformy: upewnij się,  że używasz nazwy metryki ze strony obsługiwanych metryk [Azure Monitor,](../essentials/metrics-supported.md)a nie nazwy **wyświetlanej metryki**

   - Dla metryki niestandardowej: upewnij się, że metryka jest już emitowana (nie można utworzyć reguły alertu dla metryki niestandardowej, która jeszcze nie istnieje) i że udostępniasz przestrzeń nazw metryki niestandardowej (zobacz przykład szablonu Resource Manager tutaj [)](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric)

- Jeśli tworzysz alerty [dotyczące metryk w dziennikach,](./alerts-metric-logs.md)upewnij się, że uwzględniono odpowiednie zależności. Zobacz [przykładowy szablon](./alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs).

- Jeśli tworzysz regułę alertu, która zawiera wiele kryteriów, zwróć uwagę na następujące ograniczenia:

   - W każdym kryterium można wybrać tylko jedną wartość dla każdego wymiaru
   - Nie można użyć ciągu „\*” jako wartości wymiaru
   - Jeśli metryki skonfigurowane w różnych kryteriach obsługują ten sam wymiar, skonfigurowana wartość wymiaru musi być jawnie ustawiona w taki sam sposób dla wszystkich tych metryk (zobacz przykładowy szablon Resource Manager [tutaj](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria))


## <a name="no-permissions-to-create-metric-alert-rules"></a>Brak uprawnień do tworzenia reguł alertów dotyczących metryk

Aby utworzyć regułę alertu metryki, musisz mieć następujące uprawnienia:

- Uprawnienie do odczytu dla zasobu docelowego reguły alertu
- Uprawnienie do zapisu dla grupy zasobów, w której tworzona jest reguła alertu (jeśli tworzysz regułę alertu na podstawie Azure Portal, reguła alertu jest tworzona domyślnie w tej samej grupie zasobów, w której znajduje się zasób docelowy)
- Uprawnienie do odczytu dla dowolnej grupy akcji skojarzonej z regułą alertu (jeśli dotyczy)


## <a name="naming-restrictions-for-metric-alert-rules"></a>Ograniczenia nazewnictwa reguł alertów dotyczących metryk

Rozważ następujące ograniczenia dotyczące nazw reguł alertów dotyczących metryk:

- Nazwy reguł alertów metryk nie można zmienić (zmienić ich nazwy) po utworzeniu
- Nazwy reguł alertów metryk muszą być unikatowe w obrębie grupy zasobów
- Nazwy reguł alertów metryk nie mogą zawierać następujących znaków: * # & + : < > ? @ % { } \ / 
- Nazwy reguł alertów metryk nie mogą kończyć się spacją ani okresem

> [!NOTE] 
> Jeśli nazwa reguły alertu zawiera znaki, które nie są alfabetyczne ani numeryczne (na przykład spacje, znaki interpunktowe lub symbole), te znaki mogą być zakodowane w adresie URL po pobraniu przez niektórych klientów.

## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>Ograniczenia w przypadku używania wymiarów w regułę alertu metryki z wieloma warunkami

Alerty metryk obsługują alerty dotyczące metryk wielowymiarowych, a także obsługują definiowanie wielu warunków (maksymalnie 5 warunków na regułę alertu).

W przypadku korzystania z wymiarów w regułę alertu, która zawiera wiele warunków, należy wziąć pod uwagę następujące ograniczenia:
- W każdym warunku można wybrać tylko jedną wartość na wymiar.
- Nie można użyć opcji "Zaznacz wszystkie bieżące i przyszłe wartości" (Wybierz \* ).
- Jeśli metryki skonfigurowane w różnych warunkach obsługują ten sam wymiar, skonfigurowana wartość wymiaru musi być jawnie ustawiona w taki sam sposób dla wszystkich tych metryk (w odpowiednich warunkach).
Na przykład:
    - Rozważ regułę alertu metryki, która jest zdefiniowana na koncie magazynu i monitoruje dwa warunki:
        * Łączna **liczba** transakcji > 5
        * Średnia **wartość SuccessE2ELatency** > 250 ms
    - Chcę zaktualizować pierwszy warunek i monitorować tylko transakcje, w których wymiar **ApiName** ma wartość *"GetBlob"*
    - Ponieważ zarówno metryki **Transactions,** jak i **SuccessE2ELatency** obsługują wymiar **ApiName,** należy zaktualizować oba warunki i określić wymiar **ApiName** za pomocą wartości *"GetBlob".*

## <a name="setting-the-alert-rules-period-and-frequency"></a>Ustawianie częstotliwości i okresu reguły alertu

Zalecamy wybranie opcji Poziom szczegółowości *agregacji (okres),* która jest większa niż częstotliwość oceny *,* aby zmniejszyć prawdopodobieństwo braku pierwszej oceny dodanego szeregu czasowego w następujących przypadkach:
-   Reguła alertu metryki, która monitoruje wiele wymiarów — po dodaniu nowej kombinacji wartości wymiaru
-   Reguła alertu metryki, która monitoruje wiele zasobów — po dodaniu nowego zasobu do zakresu
-   Reguła alertu metryki, która monitoruje metrykę, która nie jest stale emitowana (metryka rozrzednia) — gdy metryka jest emitowana po okresie dłuższym niż 24 godziny, w którym nie została emitowana

## <a name="the-dynamic-thresholds-borders-dont-seem-to-fit-the-data"></a>Wydaje się, że granice progów dynamicznych nie pasują do danych

Jeśli ostatnio zmieniono zachowanie metryki, zmiany niekoniecznie zostaną natychmiast odzwierciedlone w granicach progu dynamicznego (górne i dolne granice), ponieważ są one obliczane na podstawie danych metryk z ostatnich 10 dni. Podczas wyświetlania granic progu dynamicznego dla danej metryki pamiętaj, aby przyjrzeć się trendowi metryki w ostatnim tygodniu, a nie tylko w ostatnich godzinach lub dniach.

## <a name="why-is-weekly-seasonality-not-detected-by-dynamic-thresholds"></a>Dlaczego tygodniowa sezonowość nie jest wykrywana przez progi dynamiczne?

Aby zidentyfikować tygodniową sezonowość, model progów dynamicznych wymaga co najmniej trzech tygodni danych historycznych. Gdy dostępna będzie wystarczająca ilość danych historycznych, zostanie zidentyfikowana każda tygodniowa sezonowość, która istnieje w danych metryki, a model zostanie odpowiednio dostosowany. 

## <a name="dynamic-thresholds-shows-a-negative-lower-bound-for-a-metric-even-though-the-metric-always-has-positive-values"></a>Progi dynamiczne pokazują ujemną dolną granicą dla metryki, mimo że metryka zawsze ma wartości dodatnie

Gdy metryka wykazuje duże fluktuacje, progi dynamiczne będą tworzyć szerszy model wokół wartości metryki, co może spowodować, że dolna granica będzie poniżej zera. W szczególności może się to zdarzyć w następujących przypadkach:
1. Czułość jest ustawiona na niska 
2. Mediana wartości jest bliska zera
3. Metryka wykazuje nieregularne zachowanie z wysoką wariancją (w danych występują skoki lub spadki)

Gdy dolna granica ma wartość ujemną, oznacza to, że metryka może osiągnąć wartość zero, biorąc pod uwagę nieregularne zachowanie metryki. Możesz rozważyć wybranie wyższego poziomu poufności lub większego poziomu szczegółowości agregacji *(okres),* aby mniej uwzględniać model, lub użycie opcji Ignoruj dane przed, aby wykluczyć niedawne nieregule z danych historycznych użytych do skompilowania modelu. 

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać ogólne informacje dotyczące rozwiązywania problemów z alertami i powiadomieniami, zobacz Rozwiązywanie problemów w Azure Monitor [alertów.](alerts-troubleshoot.md)
