---
title: Rozwiązywanie problemów z alertami usługi Azure Metric
description: Typowe problemy związane z alertami metryk Azure Monitor i możliwymi rozwiązaniami.
author: harelbr
ms.author: harelbr
ms.topic: troubleshooting
ms.date: 10/05/2020
ms.subservice: alerts
ms.openlocfilehash: 579729eca8269d75569166a5bda32a979544b164
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91715329"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>Rozwiązywanie problemów z alertami metryk Azure Monitor 

W tym artykule omówiono typowe problemy w Azure Monitor [alertach metryk](alerts-metric-overview.md) i sposobach ich rozwiązywania.

Alerty Azure Monitor z wyprzedzeniem powiadamiają Cię, gdy w danych monitorowania zostaną znalezione ważne warunki. Umożliwiają identyfikowanie i rozwiązywanie problemów przed zapisaniem ich przez użytkowników systemu. Aby uzyskać więcej informacji na temat alertów, zobacz [Omówienie alertów w Microsoft Azure](alerts-overview.md).

## <a name="metric-alert-should-have-fired-but-didnt"></a>Alert dotyczący metryki powinien zostać wywołany, ale nie był 

Jeśli uważasz, że alert dotyczący metryki powinien zostać wywołany, ale nie został uruchomiony i nie został znaleziony w Azure Portal, spróbuj wykonać następujące czynności:

1. **Konfiguracja** — Sprawdź konfigurację reguły alertu metryki, aby upewnić się, że została prawidłowo skonfigurowana:
    - Sprawdź, czy **typ agregacji**, **stopień szczegółowości agregacji (okres)** i **wartość progowa** lub **czułość** są skonfigurowane zgodnie z oczekiwaniami
    - Dla reguły alertu korzystającej z progów dynamicznych Sprawdź, czy skonfigurowano ustawienia zaawansowane, ponieważ **Liczba naruszeń** może odfiltrować alerty i **ignorować dane przed** wpływem na sposób obliczania progów

       > [!NOTE] 
       > Progi dynamiczne wymagają co najmniej 3 dni i 30 próbek metryk, zanim staną się aktywne.

2. **Wywołane, ale bez powiadomienia** — Przejrzyj [listę wywołanych alertów](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) , aby sprawdzić, czy można zlokalizować wywołany alert. Jeśli na liście widzisz alert, ale masz problem z niektórymi akcjami lub powiadomieniami, zobacz [więcej informacji.](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)

3. Jest **już aktywny** — Sprawdź, czy istnieje już wyzwolony alert dla szeregów czasowych metryk, dla których oczekiwano, aby otrzymać Alert. Alerty metryk są stanowe, co oznacza, że po uruchomieniu alertu w ramach określonego szeregu czasowego metryki dodatkowe alerty w tym szeregu czasowym zostaną wyzwolone dopiero wtedy, gdy problem nie będzie już występował. Ten wybór projektu zmniejsza szum. Alert jest rozpoznawany automatycznie, gdy warunek alertu nie jest spełniony dla trzech kolejnych ocen.

4. **Używane wymiary** — w przypadku wybrania [wartości wymiarów dla metryki](./alerts-metric-overview.md#using-dimensions)reguła alertu monitoruje każdą pojedynczą serię czasową metryk (zgodnie z definicją wartości wymiaru) w przypadku naruszenia progu. Aby również monitorować zagregowaną serię czasową metryk (bez wybranych wymiarów), skonfiguruj dodatkową regułę alertu dla metryki bez wybierania wymiarów.

5. **Stopień szczegółowości agregacji i czasu** — w przypadku wizualizacji metryk przy użyciu [wykresów metryk](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)upewnij się, że:
    * Wybrana **agregacja** w wykresie pomiarowym jest taka sama jak **typ agregacji** w regule alertu
    * Wybrany **stopień szczegółowości czasu** jest taki sam jak **stopień szczegółowości agregacji (okres)** w regule alertu (i nie jest ustawiony na wartość "Automatic").

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>Alert dotyczący metryki został wyzwolony, gdy nie powinien mieć

Jeśli uważasz, że alert dotyczący metryk nie powinien zostać wywołany, ale Poniższa procedura może pomóc w rozwiązaniu problemu.

1. Przejrzyj [listę wywołanych alertów](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) , aby zlokalizować wygenerowanego alertu, a następnie kliknij, aby wyświetlić jego szczegóły. Przejrzyj informacje podane w sekcji **dlaczego ten alert** jest uruchamiany? aby wyświetlić wykres metryki, **wartość metryki**i **wartość progową** w momencie wyzwolenia alertu.

    > [!NOTE] 
    > Jeśli używany jest typ warunku progów dynamicznych i uważasz, że użyte progi są niepoprawne, Prześlij opinię przy użyciu ikony niezadowolenia. Ta opinia będzie miała wpływ na badania algorytmów uczenia maszynowego i pomaga ulepszyć w przyszłości.

2. Jeśli wybrano wiele wartości wymiaru dla metryki, alert zostanie wyzwolony, gdy **którakolwiek** z szeregów czasowych metryk (zgodnie z definicją wartości wymiaru) narusza próg. Więcej informacji o korzystaniu z wymiarów w alertach dotyczących metryk możesz znaleźć [tutaj](./alerts-metric-overview.md#using-dimensions).

3. Przejrzyj konfigurację reguły alertu, aby upewnić się, że została prawidłowo skonfigurowana:
    - Sprawdź, czy **typ agregacji**, **stopień szczegółowości agregacji (okres)** i **wartość progowa** lub **czułość** są skonfigurowane zgodnie z oczekiwaniami
    - Dla reguły alertu korzystającej z progów dynamicznych Sprawdź, czy skonfigurowano ustawienia zaawansowane, ponieważ **Liczba naruszeń** może odfiltrować alerty i **ignorować dane przed** wpływem na sposób obliczania progów

   > [!NOTE]
   > Progi dynamiczne wymagają co najmniej 3 dni i 30 próbek metryk, zanim staną się aktywne.

4. W przypadku wizualizacji metryki przy użyciu [wykresu metryk](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)upewnij się, że:
    - Wybrana **agregacja** w wykresie pomiarowym jest taka sama jak **typ agregacji** w regule alertu
    - Wybrany **stopień szczegółowości czasu** jest taki sam jak **stopień szczegółowości agregacji (okres)** w regule alertu (i nie jest ustawiony na wartość "Automatic").

5. Jeśli alert został wywołany, gdy istnieją już alerty, które monitorują te same kryteria (które nie zostały rozwiązane), sprawdź, czy reguła alertu została skonfigurowana z właściwością *Autołagodzenie* o **wartości false** (Ta właściwość może być KONFIGUROWANA tylko za pomocą REST/PowerShell/interfejsu wiersza polecenia, więc sprawdź skrypt używany do wdrażania reguły alertu). W takim przypadku reguła alertu nie rozwiązuje automatycznie wyzwalanych alertów i nie wymaga rozpoznania wywołanego alertu przed ponownym uruchomieniem.


## <a name="cant-find-the-metric-to-alert-on---virtual-machines-guest-metrics"></a>Nie można znaleźć metryki do alertu na maszynie wirtualnej — metryki gościa

Aby otrzymywać alerty dotyczące metryk systemu operacyjnego gościa maszyn wirtualnych (na przykład: pamięć, miejsce na dysku), upewnij się, że zainstalowano wymaganego agenta w celu zebrania tych danych w celu Azure Monitor metryk:
- [Maszyny wirtualne z systemem Windows](./collect-custom-metrics-guestos-resource-manager-vm.md)
- [Maszyny wirtualne z systemem Linux](./collect-custom-metrics-linux-telegraf.md)

Aby uzyskać więcej informacji na temat zbierania danych z systemu operacyjnego gościa maszyny wirtualnej, zobacz [tutaj](../insights/monitor-vm-azure.md#guest-operating-system).
    
> [!NOTE] 
> W przypadku skonfigurowania metryk gościa do wysłania do obszaru roboczego Log Analytics metryki są wyświetlane w obszarze zasób obszaru roboczego Log Analytics i rozpoczną wyświetlanie danych **dopiero** po utworzeniu reguły alertu, która je monitoruje. W tym celu postępuj zgodnie z instrukcjami, aby [skonfigurować alert metryki na potrzeby dzienników](./alerts-metric-logs.md#configuring-metric-alert-for-logs).

> [!NOTE] 
> Monitorowanie metryk gościa dla wielu maszyn wirtualnych z pojedynczą regułą alertu nie jest obecnie obsługiwane przez alerty metryki. Można to osiągnąć za pomocą [reguły alertu dziennika](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log). W tym celu upewnij się, że metryki gościa są zbierane do obszaru roboczego Log Analytics i Utwórz regułę alertu dziennika w obszarze roboczym.

## <a name="cant-find-the-metric-to-alert-on"></a>Nie można znaleźć metryki do alertu

Jeśli chcesz otrzymywać alerty dotyczące określonej metryki, ale nie zobaczysz jej podczas tworzenia reguły alertu, sprawdź następujące informacje:
- Jeśli nie widzisz żadnych metryk dla zasobu, [Sprawdź, czy typ zasobu jest obsługiwany dla alertów metryk](./alerts-metric-near-real-time.md).
- Jeśli można zobaczyć pewne metryki dla zasobu, ale nie można znaleźć określonej metryki, [Sprawdź, czy ta Metryka jest dostępna](./metrics-supported.md), a jeśli tak, zobacz Opis metryki, aby sprawdzić, czy jest dostępna tylko w określonych wersjach lub wersjach tego zasobu.
- Jeśli Metryka nie jest dostępna dla zasobu, może być dostępna w dziennikach zasobów i może być monitorowana przy użyciu alertów dzienników. Zobacz tutaj, aby uzyskać więcej informacji na temat [zbierania i analizowania dzienników zasobów z zasobów platformy Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs).

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>Nie można znaleźć wymiaru metryki dla alertu

Jeśli chcesz otrzymywać alerty dotyczące [określonych wartości wymiarów metryki](./alerts-metric-overview.md#using-dimensions), ale nie można znaleźć tych wartości, zwróć uwagę na następujące kwestie:

1. Wyświetlenie wartości wymiarów na liście **Wartości wymiarów** może potrwać kilka minut
1. Wyświetlone wartości wymiarów są oparte na danych metryk zebranych w ciągu ostatnich trzech dni
1. Jeśli wartość wymiaru nie jest jeszcze emitowana, kliknij znak "+", aby dodać wartość niestandardową
1. Jeśli chcesz otrzymywać alerty dotyczące wszystkich możliwych wartości wymiaru (w tym przyszłych wartości), zaznacz pole wyboru "Select *"

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>Reguły alertów metryki są nadal zdefiniowane dla usuniętego zasobu 

Podczas usuwania zasobu platformy Azure skojarzone reguły alertów metryk nie są automatycznie usuwane. Aby usunąć reguły alertów skojarzone z zasobem, który został usunięty:

1. Otwórz grupę zasobów, w której zdefiniowano usunięty zasób
1. Na liście wyświetlającej zasoby zaznacz pole wyboru **Pokaż ukryte typy**
1. Filtruj listę według typu **microsoft.insights/metricalerts**
1. Wybierz odpowiednie reguły alertów i wybierz pozycję **Usuń** .

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>Alerty metryk są wykonywane za każdym razem, gdy mój warunek jest spełniony

Alerty metryk są domyślnie stanowe i w związku z tym dodatkowe alerty nie są wyzwalane, jeśli istnieje już niewyzwalany alert w danej szeregu czasowym. Jeśli chcesz ustawić konkretną regułę alertu metryki jako bezstanowe i otrzymywać alerty dla każdej oceny, w której spełniony jest warunek alertu, Utwórz regułę alertu programowo (na przykład za pośrednictwem [Menedżer zasobów](./alerts-metric-create-templates.md), [PowerShell](/powershell/module/az.monitor/?view=azps-3.6.1), [rest](/rest/api/monitor/metricalerts/createorupdate), [CLI](/cli/azure/monitor/metrics/alert?view=azure-cli-latest)) i ustaw właściwość *autołagodzenie* na wartość "false".

> [!NOTE] 
> Utworzenie reguły alertu metryki bezstanowej uniemożliwia rozpoznanie wyzwalanych alertów, nawet gdy nie zostanie już spełniony warunek, wyzwolone alerty pozostaną w stanie uruchomienia do 30-dniowego okresu przechowywania.

## <a name="define-an-alert-rule-on-a-custom-metric-that-isnt-emitted-yet"></a>Zdefiniuj regułę alertu na niestandardową metrykę, która nie jest jeszcze emitowana

Podczas tworzenia reguły alertu metryki Nazwa metryki jest sprawdzana pod kątem [interfejsu API definicji metryk](/rest/api/monitor/metricdefinitions/list) , aby upewnić się, że istnieje. W niektórych przypadkach chcesz utworzyć regułę alertu dla metryki niestandardowej nawet przed emisją. Na przykład podczas tworzenia (przy użyciu szablonu Menedżer zasobów) Application Insights zasobu, który będzie emitować metrykę niestandardową, wraz z regułą alertu, która monitoruje tę metrykę.

Aby uniknąć niepowodzenia wdrożenia podczas próby zweryfikowania definicji metryk niestandardowych, można użyć parametru *skipMetricValidation* w sekcji kryteria reguły alertu, co spowoduje Pominięcie sprawdzania poprawności metryki. Zapoznaj się z poniższym przykładem, jak używać tego parametru w szablonie Menedżer zasobów. Aby uzyskać więcej informacji, zobacz [pełne przykłady szablonów Menedżer zasobów do tworzenia reguł alertów dotyczących metryk](./alerts-metric-create-templates.md).

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

Eksportowanie Menedżer zasobów szablonu reguły alertu dotyczącego metryki ułatwia zrozumienie jej składni i właściwości JSON oraz może służyć do automatyzowania przyszłych wdrożeń.
1. Przejdź do sekcji **grupy zasobów** w portalu i wybierz grupę zasobów zawierającą regułę.
2. W sekcji Przegląd zaznacz pole wyboru **Pokaż ukryte typy** .
3. W polu Filtr **typu** wybierz pozycję *Microsoft. Insights/metricalerts*.
4. Wybierz odpowiednią regułę alertu, aby wyświetlić jej szczegóły.
5. W obszarze **Ustawienia**wybierz pozycję **Eksportuj szablon**.

## <a name="metric-alert-rules-quota-too-small"></a>Zbyt mały przydział reguł alertów dotyczących metryk

Dozwolona liczba reguł alertów dotyczących metryk na subskrypcję podlega [limitom przydziału](../service-limits.md).

Jeśli osiągnięto limit przydziału, następujące czynności mogą pomóc w rozwiązaniu problemu:
1. Spróbuj usunąć lub wyłączyć reguły alertów metryk, które nie są już używane.

2. Zacznij korzystać z reguł alertów dotyczących metryk, które monitorują wiele zasobów. Dzięki tej możliwości jedna reguła alertu może monitorować wiele zasobów, używając tylko jednej reguły alertu w stosunku do limitu przydziału. Aby uzyskać więcej informacji na temat tej możliwości i obsługiwanych typów zasobów, zobacz [tutaj](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).

3. Aby zwiększyć limit przydziału, Otwórz żądanie pomocy technicznej i podaj następujące informacje:

    - Identyfikatory subskrypcji, dla których należy zwiększyć limit przydziału
    - Typ zasobu do zwiększenia przydziału: **alerty metryki** lub **alerty metryk (klasyczne)**
    - Żądany limit przydziału

## <a name="check-total-number-of-metric-alert-rules"></a>Sprawdź łączną liczbę reguł alertów dotyczących metryk

Aby sprawdzić bieżące użycie reguł alertów metryk, wykonaj poniższe kroki.

### <a name="from-the-azure-portal"></a>Z witryny Azure Portal

1. Otwórz ekran **Alerty** i kliknij pozycję **Zarządzaj regułami alertów**
2. Filtrowanie do odpowiedniej subskrypcji przy użyciu kontrolki listy rozwijanej **subskrypcji**
3. Upewnij się, że nie Przefiltruj do określonej grupy zasobów, typu zasobu lub zasobu
4. W kontrolce listy rozwijanej **Typ sygnału** wybierz pozycję **metryki** .
5. Sprawdź, czy kontrolka listy rozwijanej **stan** ma wartość **włączone**
6. Łączna liczba reguł alertów dotyczących metryk są wyświetlane powyżej listy reguł alertów

### <a name="from-api"></a>Za pomocą interfejsu API

- PowerShell — [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2?view=azps-3.7.0)
- Interfejs API REST — [lista według subskrypcji](/rest/api/monitor/metricalerts/listbysubscription)
- Interfejs wiersza polecenia platformy Azure — [az monitor metrics alert list](/cli/azure/monitor/metrics/alert?view=azure-cli-latest#az-monitor-metrics-alert-list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Zarządzanie regułami alertów za pomocą szablonów Menedżer zasobów, interfejsu API REST, programu PowerShell lub interfejsu wiersza polecenia platformy Azure

W przypadku problemów z tworzeniem, aktualizowaniem, pobieraniem lub usuwaniem alertów metryk przy użyciu szablonów Menedżer zasobów, interfejsu API REST, programu PowerShell lub interfejsu wiersza polecenia (CLI) platformy Azure następujące kroki mogą pomóc w rozwiązaniu problemu.

### <a name="resource-manager-templates"></a>Szablony usługi Resource Manager

- Przejrzyj listę [typowych błędów wdrożeń platformy Azure](../../azure-resource-manager/templates/common-deployment-errors.md) i odpowiednie procedury rozwiązywania problemów
- Zapoznaj się z [alertami metryki Azure Resource Manager przykłady szablonów](./alerts-metric-create-templates.md) , aby upewnić się, że wszystkie parametry są prawidłowo przekazywane

### <a name="rest-api"></a>Interfejs API REST

Zapoznaj się z [przewodnikiem interfejsu API REST](/rest/api/monitor/metricalerts/) , aby upewnić się, że wszystkie parametry są prawidłowo przekazywane

### <a name="powershell"></a>Program PowerShell

Upewnij się, że używasz właściwych poleceń cmdlet programu PowerShell dla alertów metryk:

- Polecenia cmdlet programu PowerShell dla alertów dotyczących metryk są dostępne w [module AZ.Monitor](/powershell/module/az.monitor/?view=azps-3.6.1)
- Upewnij się, że używasz poleceń cmdlet kończących się na "v2" dla nowych (nieklasycznych) alertów metryk (na przykład [Add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2?view=azps-3.6.1))

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Upewnij się, że używasz właściwych poleceń interfejsu wiersza polecenia dla alertów metryk:

- Polecenia interfejsu wiersza polecenia dla alertów metryk rozpoczynają się od `az monitor metrics alert`. Przejrzyj [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure/monitor/metrics/alert?view=azure-cli-latest), aby poznać składnię.
- Możesz zapoznać się z [przykładem przedstawiającym sposób użycia interfejsu wiersza polecenia alertów dotyczących metryk](./alerts-metric.md#with-azure-cli)
- Przed utworzeniem alertu dotyczącego metryki niestandardowej upewnij, że nazwa metryki jest poprzedzona odpowiednią przestrzenią nazw metryki: PRZESTRZEŃ_NAZW.METRYKA

### <a name="general"></a>Ogólne

- Jeśli `Metric not found` wystąpi błąd:

   - Dla metryki platformy: Upewnij się, że używasz nazwy **metryki** na [stronie Azure monitor obsługiwane metryki](./metrics-supported.md), a nie **Nazwa wyświetlana metryki**

   - W przypadku metryki niestandardowej: Upewnij się, że Metryka jest już emitowana (nie można utworzyć reguły alertu dla niestandardowej metryki, która jeszcze nie istnieje) i że udostępniana jest przestrzeń nazw metryki niestandardowej (Zobacz przykład szablonu Menedżer zasobów [tutaj](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric))

- Jeśli tworzysz [alerty metryk w dziennikach](./alerts-metric-logs.md), upewnij się, że są uwzględnione odpowiednie zależności. Zobacz [przykładowy szablon](./alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs).

- Jeśli tworzysz regułę alertu, która zawiera wiele kryteriów, należy pamiętać o następujących ograniczeniach:

   - W każdym kryterium można wybrać tylko jedną wartość dla każdego wymiaru
   - Nie można użyć ciągu „\*” jako wartości wymiaru
   - Gdy metryki, które są skonfigurowane w różnych kryterium obsługują ten sam wymiar, skonfigurowana wartość wymiaru musi być jawnie ustawiona w taki sam sposób dla wszystkich tych metryk (Zobacz przykład szablonu Menedżer zasobów [tutaj](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria))


## <a name="no-permissions-to-create-metric-alert-rules"></a>Brak uprawnień do tworzenia reguł alertów dotyczących metryk

Aby utworzyć regułę alertu metryki, należy mieć następujące uprawnienia:

- Uprawnienie do odczytu w zasobie docelowym reguły alertu
- Uprawnienie do zapisu w grupie zasobów, w której jest tworzona reguła alertu (Jeśli tworzysz regułę alertu na podstawie Azure Portal, reguła alertu zostanie utworzona w tej samej grupie zasobów, w której znajduje się zasób docelowy).
- Uprawnienie Odczyt dla każdej grupy akcji skojarzonej z regułą alertu (jeśli dotyczy)


## <a name="naming-restrictions-for-metric-alert-rules"></a>Ograniczenia nazw reguł alertów dotyczących metryk

Należy wziąć pod uwagę następujące ograniczenia dotyczące nazw reguł alertów dotyczących metryk:

- Nie można zmienić nazwy reguły alertu metryki (zmieniono ich nazwę) po utworzeniu
- Nazwy reguł alertów metryk muszą być unikatowe w obrębie grupy zasobów
- Nazwy reguł alertów metryk nie mogą zawierać następujących znaków: * # & +:  < > ? @ % { } \ / 
- Nazwy reguł alertów metryk nie mogą kończyć się spacją ani kropką


## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>Ograniczenia w przypadku używania wymiarów w regule alertu metryki z wieloma warunkami

Alerty metryk obsługują generowanie alertów w przypadku metryk wielowymiarowych oraz obsługują definiowanie wielu warunków (do 5 warunków na regułę alertu).

Podczas używania wymiarów w regule alertu zawierającej wiele warunków należy wziąć pod uwagę następujące ograniczenia:
- Można wybrać tylko jedną wartość dla każdego wymiaru w każdym warunku.
- Nie można użyć opcji "zaznacz wszystkie bieżące i przyszłe wartości" (wybierz \* ).
- Gdy metryki, które są skonfigurowane w różnych warunkach, obsługują ten sam wymiar, wówczas skonfigurowana wartość wymiaru musi być jawnie ustawiona w taki sam sposób dla wszystkich metryk (w odpowiednich warunkach).
Na przykład:
    - Należy wziąć pod uwagę regułę alertu metryki zdefiniowaną na koncie magazynu i monitoruje dwa warunki:
        * Łączna liczba **transakcji** > 5
        * Średnia **SuccessE2ELatency** > 250 MS
    - Chcę zaktualizować pierwszy warunek i monitorować tylko transakcje, w przypadku których wymiar **ApiName** ma wartość *"GetBlob"*
    - Ponieważ metryki **Transactions** i **SuccessE2ELatency** obsługują wymiar **ApiName** , należy zaktualizować oba warunki i określić, że oba z nich określają wymiar **ApiName** z wartością *"GetBlob"* .

## <a name="setting-the-alert-rules-period-and-frequency"></a>Ustawianie okresu i częstotliwości dla reguły alertu

Zalecamy wybranie *stopnia szczegółowości agregacji (okres)* , który jest większy niż *częstotliwość obliczania*, aby zmniejszyć prawdopodobieństwo braku pierwszej oceny dodanej szeregu czasowego w następujących przypadkach:
-   Reguła alertu metryki, która monitoruje wiele wymiarów — po dodaniu nowej kombinacji wartości wymiaru
-   Reguła alertu metryki, która monitoruje wiele zasobów — po dodaniu nowego zasobu do zakresu
-   Reguła alertu dotyczącego metryki, która monitoruje metrykę nieemitowaną ciągle (metrykę rozrzedzoną) — gdy Metryka jest emitowana po okresie dłuższym niż 24 godziny, w którym nie została wyemitowana

## <a name="next-steps"></a>Następne kroki

- Ogólne informacje dotyczące rozwiązywania problemów dotyczących alertów i powiadomień znajdują się [w temacie Rozwiązywanie problemów w alertach Azure monitor](alerts-troubleshoot.md).
