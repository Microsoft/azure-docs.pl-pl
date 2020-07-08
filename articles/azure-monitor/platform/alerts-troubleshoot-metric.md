---
title: Rozwiązywanie problemów z alertami usługi Azure Metric
description: Typowe problemy związane z alertami metryk Azure Monitor i możliwymi rozwiązaniami.
author: harelbr
ms.author: harelbr
ms.topic: reference
ms.date: 06/21/2020
ms.subservice: alerts
ms.openlocfilehash: 36ff80bc0858d6d08cc120d126628de02ba6e703
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85130742"
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

2. **Wywołane, ale bez powiadomienia** — Przejrzyj [listę wywołanych alertów](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) , aby sprawdzić, czy można zlokalizować wywołany alert. Jeśli na liście widzisz alert, ale masz problem z niektórymi akcjami lub powiadomieniami, zobacz [więcej informacji.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-troubleshoot#action-or-notification-on-my-alert-did-not-work-as-expected)

3. Jest **już aktywny** — Sprawdź, czy istnieje już wyzwolony alert dla szeregów czasowych metryk, dla których oczekiwano, aby otrzymać Alert. Alerty metryk są stanowe, co oznacza, że po uruchomieniu alertu z określoną serią czasową metryki dodatkowe alerty w tej serii czasowej nie są uruchamiane do momentu, gdy problem nie zostanie już zaobserwowany. Ten wybór projektu zmniejsza szum. Alert jest rozpoznawany automatycznie, gdy warunek alertu nie jest spełniony dla trzech kolejnych ocen.

4. **Używane wymiary** — w przypadku wybrania [wartości wymiarów dla metryki](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#using-dimensions)reguła alertu monitoruje każdą pojedynczą serię czasową metryk (zgodnie z definicją wartości wymiaru) dla naruszenia progu. Aby również monitorować zagregowaną serię czasową metryk (bez wybranych wymiarów), skonfiguruj dodatkową regułę alertu dla metryki bez wybierania wymiarów.

5. **Stopień szczegółowości agregacji i czasu** — w przypadku wizualizacji metryk przy użyciu [wykresów metryk](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)upewnij się, że:
    * Wybrana **agregacja** w wykresie pomiarowym jest taka sama jak **typ agregacji** w regule alertu
    * Wybrany **stopień szczegółowości czasu** jest taki sam jak **stopień szczegółowości agregacji (okres)** w regule alertu (i nie jest ustawiony na wartość "Automatic").

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>Alert dotyczący metryki został wyzwolony, gdy nie powinien mieć

Jeśli uważasz, że alert dotyczący metryk nie powinien zostać wywołany, ale Poniższa procedura może pomóc w rozwiązaniu problemu.

1. Przejrzyj [listę wywołanych alertów](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) , aby zlokalizować wygenerowanego alertu, a następnie kliknij, aby wyświetlić jego szczegóły. Przejrzyj informacje podane w sekcji **dlaczego ten alert** jest uruchamiany? aby wyświetlić wykres metryki, **wartość metryki**i **wartość progową** w momencie wyzwolenia alertu.

    > [!NOTE] 
    > Jeśli używasz typu warunku Progi dynamiczne i sądzisz, że użyte progi nie były poprawne, prześlij opinię za pomocą ikony niezadowolenia. Ta opinia będzie miała wpływ na badania algorytmów uczenia maszynowego i pomaga ulepszyć w przyszłości.

2. Jeśli wybrano wiele wartości wymiaru dla metryki, alert zostanie wyzwolony, gdy **którakolwiek** z szeregów czasowych metryk (zgodnie z definicją wartości wymiaru) narusza próg. Więcej informacji o korzystaniu z wymiarów w alertach dotyczących metryk możesz znaleźć [tutaj](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#using-dimensions).

3. Przejrzyj konfigurację reguły alertu, aby upewnić się, że została prawidłowo skonfigurowana:
    - Sprawdź, czy **typ agregacji**, **stopień szczegółowości agregacji (okres)** i **wartość progowa** lub **czułość** są skonfigurowane zgodnie z oczekiwaniami
    - Dla reguły alertu korzystającej z progów dynamicznych Sprawdź, czy skonfigurowano ustawienia zaawansowane, ponieważ **Liczba naruszeń** może odfiltrować alerty i **ignorować dane przed** wpływem na sposób obliczania progów

   > [!NOTE]
   > Progi dynamiczne wymagają co najmniej 3 dni i 30 próbek metryk, zanim staną się aktywne.

4. W przypadku wizualizacji metryki przy użyciu [wykresu metryk](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)upewnij się, że:
    - Wybrana **agregacja** w wykresie pomiarowym jest taka sama jak **typ agregacji** w regule alertu
    - Wybrany **stopień szczegółowości czasu** jest taki sam jak **stopień szczegółowości agregacji (okres)** w regule alertu (i nie jest ustawiony na wartość "Automatic").

5. Jeśli alert został wywołany, gdy istnieją już alerty, które monitorują te same kryteria (które nie zostały rozwiązane), sprawdź, czy reguła alertu została skonfigurowana z właściwością *Autołagodzenie* o **wartości false** (Ta właściwość może być KONFIGUROWANA tylko za pomocą REST/PowerShell/interfejsu wiersza polecenia, więc sprawdź skrypt używany do wdrażania reguły alertu). W takim przypadku reguła alertu nie rozwiązuje automatycznie wyzwalanych alertów i nie wymaga rozpoznania wywołanego alertu przed ponownym uruchomieniem.


## <a name="cant-find-metric-to-alert-on---virtual-machines"></a>Nie można znaleźć metryki do alertu na maszynach wirtualnych 

Aby ostrzec metryki gościa na maszynach wirtualnych (w przypadku pamięci, miejsca na dysku), upewnij się, że skonfigurowano ustawienia diagnostyczne do wysyłania danych do Azure Monitor ujścia:
    * [Maszyny wirtualne z systemem Windows](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm)
    * [Maszyny wirtualne z systemem Linux](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-linux-telegraf)
    
> [!NOTE] 
> W przypadku skonfigurowania metryk gościa do wysłania do obszaru roboczego Log Analytics metryki są wyświetlane w obszarze zasób obszaru roboczego Log Analytics i rozpoczną wyświetlanie danych **dopiero** po utworzeniu reguły alertu, która je monitoruje. W tym celu postępuj zgodnie z instrukcjami, aby [skonfigurować alert metryki na potrzeby dzienników](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-logs#configuring-metric-alert-for-logs).

## <a name="cant-find-the-metric-to-alert-on"></a>Nie można znaleźć metryki do alertu

Jeśli chcesz otrzymywać alerty dotyczące określonej metryki, ale nie widzisz żadnych metryk dla zasobu, [Sprawdź, czy typ zasobu jest obsługiwany dla alertów dotyczących metryk](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-near-real-time).
Jeśli można zobaczyć pewne metryki dla zasobu, ale nie można znaleźć określonej metryki, [Sprawdź, czy ta Metryka jest dostępna](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported), a jeśli tak, zobacz Opis metryki, aby sprawdzić, czy jest dostępna tylko w określonych wersjach lub wersjach tego zasobu.

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>Nie można znaleźć wymiaru metryki dla alertu

Jeśli chcesz otrzymywać alerty dotyczące [określonych wartości wymiarów metryki](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#using-dimensions), ale nie można znaleźć tych wartości, zwróć uwagę na następujące kwestie:

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

Alerty metryk są domyślnie stanowe i w związku z tym dodatkowe alerty nie są wyzwalane, jeśli istnieje już niewyzwalany alert w danej szeregu czasowym. Jeśli chcesz ustawić konkretną regułę alertu metryki jako bezstanowe i otrzymywać alerty dla każdej oceny, w której spełniony jest warunek alertu, Utwórz regułę alertu programowo (na przykład za pośrednictwem [Menedżer zasobów](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates), [PowerShell](https://docs.microsoft.com/powershell/module/az.monitor/?view=azps-3.6.1), [rest](https://docs.microsoft.com/rest/api/monitor/metricalerts/createorupdate), [CLI](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)) i ustaw właściwość *autołagodzenie* na wartość "false".

> [!NOTE] 
> Utworzenie reguły alertu metryki bezstanowej uniemożliwia rozpoznanie wyzwalanych alertów, nawet gdy nie zostanie już spełniony warunek, wyzwolone alerty pozostaną w stanie uruchomienia do 30-dniowego okresu przechowywania.


## <a name="metric-alert-rules-quota-too-small"></a>Zbyt mały przydział reguł alertów dotyczących metryk

Dozwolona liczba reguł alertów dotyczących metryk na subskrypcję podlega [limitom przydziału](https://docs.microsoft.com/azure/azure-monitor/service-limits).

Jeśli osiągnięto limit przydziału, następujące czynności mogą pomóc w rozwiązaniu problemu:
1. Spróbuj usunąć lub wyłączyć reguły alertów metryk, które nie są już używane.

2. Zacznij korzystać z reguł alertów dotyczących metryk, które monitorują wiele zasobów. Dzięki tej możliwości jedna reguła alertu może monitorować wiele zasobów, używając tylko jednej reguły alertu w stosunku do limitu przydziału. Aby uzyskać więcej informacji na temat tej możliwości i obsługiwanych typów zasobów, zobacz [tutaj](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor).

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
6. Łączna liczba reguł alertów dotyczących metryk są wyświetlane powyżej listy reguł

### <a name="from-api"></a>Za pomocą interfejsu API

- PowerShell — [Get-AzMetricAlertRuleV2](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricalertrulev2?view=azps-3.7.0)
- Interfejs API REST — [lista według subskrypcji](https://docs.microsoft.com/rest/api/monitor/metricalerts/listbysubscription)
- Interfejs wiersza polecenia platformy Azure — [az monitor metrics alert list](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest#az-monitor-metrics-alert-list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Zarządzanie regułami alertów za pomocą szablonów Menedżer zasobów, interfejsu API REST, programu PowerShell lub interfejsu wiersza polecenia platformy Azure

W przypadku problemów z tworzeniem, aktualizowaniem, pobieraniem lub usuwaniem alertów metryk przy użyciu szablonów Menedżer zasobów, interfejsu API REST, programu PowerShell lub interfejsu wiersza polecenia (CLI) platformy Azure następujące kroki mogą pomóc w rozwiązaniu problemu.

### <a name="resource-manager-templates"></a>Szablony usługi Resource Manager

- Przejrzyj listę [typowych błędów wdrożeń platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors) i odpowiednie procedury rozwiązywania problemów
- Zapoznaj się z [alertami metryki Azure Resource Manager przykłady szablonów](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates) , aby upewnić się, że wszystkie parametry zostały prawidłowo przeprzekazywane

### <a name="rest-api"></a>Interfejs API REST

Przejrzyj [podręcznik interfejsu API REST](https://docs.microsoft.com/rest/api/monitor/metricalerts/), aby sprawdzić, czy wszystkie parametry są przekazywane prawidłowo

### <a name="powershell"></a>PowerShell

Upewnij się, że używasz właściwych poleceń cmdlet programu PowerShell dla alertów metryk:

- Polecenia cmdlet programu PowerShell dla alertów dotyczących metryk są dostępne w [module AZ.Monitor](https://docs.microsoft.com/powershell/module/az.monitor/?view=azps-3.6.1)
- Upewnij się, że używasz poleceń cmdlet kończących się na "v2" dla nowych (nieklasycznych) alertów metryk (na przykład [Add-AzMetricAlertRuleV2](https://docs.microsoft.com/powershell/module/az.monitor/Add-AzMetricAlertRuleV2?view=azps-3.6.1))

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Upewnij się, że używasz właściwych poleceń interfejsu wiersza polecenia dla alertów metryk:

- Polecenia interfejsu wiersza polecenia dla alertów metryk rozpoczynają się od `az monitor metrics alert`. Przejrzyj [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest), aby poznać składnię.
- Możesz zapoznać się z [przykładem przedstawiającym sposób użycia interfejsu wiersza polecenia alertów dotyczących metryk](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric#with-azure-cli)
- Przed utworzeniem alertu dotyczącego metryki niestandardowej upewnij, że nazwa metryki jest poprzedzona odpowiednią przestrzenią nazw metryki: PRZESTRZEŃ_NAZW.METRYKA

### <a name="general"></a>Ogólne

- Jeśli otrzymujesz błąd `Metric not found`:

   - Dla metryki platformy: Upewnij się, że używasz nazwy **metryki** na [stronie Azure monitor obsługiwane metryki](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported), a nie **Nazwa wyświetlana metryki**

   - W przypadku metryki niestandardowej: Upewnij się, że Metryka jest już emitowana (nie można utworzyć reguły alertu dla niestandardowej metryki, która jeszcze nie istnieje) i że udostępniana jest przestrzeń nazw metryki niestandardowej (Zobacz przykład szablonu ARM [tutaj](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric))

- Jeśli tworzysz [metryki alertów dotyczące dzienników](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-logs), upewnij się, że uwzględniono odpowiednie zależności. Zobacz [przykładowy szablon](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-logs#resource-template-for-metric-alerts-for-logs).

- Jeśli tworzysz regułę alertu, która zawiera wiele kryteriów, należy pamiętać o następujących ograniczeniach:

   - W każdym kryterium można wybrać tylko jedną wartość dla każdego wymiaru
   - Nie można użyć ciągu „\*” jako wartości wymiaru
   - Gdy metryki, które są skonfigurowane w różnych kryterium obsługują ten sam wymiar, skonfigurowana wartość wymiaru musi być jawnie ustawiona w taki sam sposób dla wszystkich tych metryk (Zobacz przykład szablonu Menedżer zasobów [tutaj](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria))


## <a name="no-permissions-to-create-metric-alert-rules"></a>Brak uprawnień do tworzenia reguł alertów dotyczących metryk

Aby utworzyć regułę alertu metryki, należy mieć następujące uprawnienia:

- Uprawnienie do odczytu w zasobie docelowym reguły alertu
- Uprawnienie do zapisu w grupie zasobów, w której jest tworzona reguła alertu (Jeśli tworzysz regułę alertu na podstawie Azure Portal, reguła alertu zostanie utworzona w tej samej grupie zasobów, w której znajduje się zasób docelowy).
- Uprawnienie Odczyt dla każdej grupy akcji skojarzonej z regułą alertu (jeśli dotyczy)


## <a name="naming-restrictions-for-metric-alert-rules"></a>Ograniczenia nazw reguł alertów dotyczących metryk

Należy zwrócić uwagę na następujące ograniczenia dotyczące nazw reguł alertów dotyczących metryk:

- Nie można zmienić nazwy reguły alertu metryki (zmieniono ich nazwę) po utworzeniu
- Nazwy reguł alertów metryk muszą być unikatowe w obrębie grupy zasobów
- Nazwy reguł alertów metryk nie mogą zawierać następujących znaków: * # & +:  < > ? @ % { } \ / 
- Nazwy reguł alertów metryk nie mogą kończyć się znakiem:.


## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>Ograniczenia w przypadku używania wymiarów w regule alertu metryki z wieloma warunkami

Alerty metryk obsługują generowanie alertów w przypadku metryk wielowymiarowych oraz obsługują definiowanie wielu warunków (do 5 warunków na regułę alertu).

Należy pamiętać o następujących ograniczeniach w przypadku używania wymiarów w regule alertu, która zawiera wiele warunków:
1. Można wybrać tylko jedną wartość dla każdego wymiaru w każdym warunku.
2. Nie można użyć opcji "zaznacz wszystkie bieżące i przyszłe wartości" (wybierz \* ).
3. Gdy metryki, które są skonfigurowane w różnych warunkach, obsługują ten sam wymiar, wówczas skonfigurowana wartość wymiaru musi być jawnie ustawiona w taki sam sposób dla wszystkich metryk (w odpowiednich warunkach).
Przykład:
    - Należy wziąć pod uwagę regułę alertu metryki zdefiniowaną na koncie magazynu i monitoruje dwa warunki:
        * Łączna liczba **transakcji** > 5
        * Średnia **SuccessE2ELatency** > 250 MS
    - Chcę zaktualizować pierwszy warunek i monitorować tylko transakcje, w przypadku których wymiar **ApiName** ma wartość *"GetBlob"*
    - Ponieważ metryki **Transactions** i **SuccessE2ELatency** obsługują wymiar **ApiName** , należy zaktualizować oba warunki i określić, że oba z nich określają wymiar **ApiName** z wartością *"GetBlob"* .


## <a name="next-steps"></a>Następne kroki

- Ogólne informacje dotyczące rozwiązywania problemów dotyczących alertów i powiadomień znajdują się [w temacie Rozwiązywanie problemów w alertach Azure monitor](alerts-troubleshoot.md).
