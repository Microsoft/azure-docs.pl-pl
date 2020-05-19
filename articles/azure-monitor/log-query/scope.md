---
title: Zakres zapytania dziennika w Azure Monitor Log Analytics | Microsoft Docs
description: Opisuje zakres i zakres czasu dla zapytania dziennika w Azure Monitor Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2020
ms.openlocfilehash: 2840e5b8ff16d44f76aaafcf68264c65e4401ff7
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199030"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Zakres i zakres czasu zapytania dziennika w Azure Monitor Log Analytics
Po uruchomieniu [zapytania dziennika](log-query-overview.md) w [log Analytics w Azure Portal](get-started-portal.md), zestaw danych ocenianych przez zapytanie zależy od zakresu i wybranego zakresu czasu. W tym artykule opisano zakres i zakres czasu oraz sposób, w jaki można ustawić każdą z nich w zależności od wymagań. Opisano w nim również zachowanie różnych typów zakresów.


## <a name="query-scope"></a>Zakres zapytania
Zakres zapytania definiuje rekordy, które są oceniane przez zapytanie. Zwykle obejmuje to wszystkie rekordy w pojedynczym obszarze roboczym Log Analytics lub Application Insights aplikacji. Log Analytics umożliwia również ustawienie zakresu dla określonego monitorowanego zasobu platformy Azure. Dzięki temu Właściciel zasobu może skupić się tylko na danych, nawet jeśli ten zasób zostanie zapisywany w wielu obszarach roboczych.

Zakres jest zawsze wyświetlany w lewym górnym rogu okna Log Analytics. Ikona wskazuje, czy zakres jest obszarem roboczym Log Analytics czy Application Insights aplikacją. Żadna ikona nie wskazuje innego zasobu platformy Azure.

![Zakres](media/scope/scope.png)

Zakres jest określany przez metodę, która jest używana do uruchamiania Log Analytics, a w niektórych przypadkach można zmienić zakres, klikając go. W poniższej tabeli wymieniono różne typy używanych zakresów i różne szczegóły dotyczące każdego z nich.

> [!IMPORTANT]
> Jeśli używasz APM 2,1, Application Insights aplikacje są przechowywane w obszarze roboczym Log Analytics ze wszystkimi innymi danymi dziennika, a zakres Application Insights jest niedostępny. Jeśli wybierzesz pozycję **dzienniki** z menu Application Insights, będzie ona działać tak samo jak w przypadku **innych zakresów zasobów platformy Azure** , a tylko te dane, w których znajdują się aplikacje znajdujące się w tabelach Application Insights.

| Zakres zapytania | Rekordy w zakresie | Jak wybrać | Zmienianie zakresu |
|:---|:---|:---|:---|
| Obszar roboczy usługi Log Analytics | Wszystkie rekordy w obszarze roboczym Log Analytics. | Wybierz pozycję **dzienniki** z menu **Azure monitor** lub z menu **log Analytics obszary robocze** .  | Można zmienić zakres na dowolny inny typ zasobu. |
| Aplikacja Application Insights | Wszystkie rekordy w aplikacji Application Insights. | Wybierz pozycję **Analiza** na stronie **Przegląd** Application Insights. | Można zmieniać zakres tylko do innej aplikacji Application Insights. |
| Grupa zasobów | Rekordy utworzone przez wszystkie zasoby w grupie zasobów. Może zawierać dane z wielu obszarów roboczych Log Analytics. | Wybierz pozycję **dzienniki** z menu Grupa zasobów. | Nie można zmienić zakresu.|
| Subskrypcja | Rekordy utworzone przez wszystkie zasoby w subskrypcji. Może zawierać dane z wielu obszarów roboczych Log Analytics. | Z menu subskrypcja wybierz pozycję **dzienniki** .   | Nie można zmienić zakresu. |
| Inne zasoby platformy Azure | Rekordy utworzone przez zasób. Może zawierać dane z wielu obszarów roboczych Log Analytics.  | Wybierz pozycję **dzienniki** z menu zasób.<br>LUB<br>Wybierz pozycję **dzienniki** z menu **Azure monitor** a następnie wybierz nowy zakres. | Można zmienić tylko zakres na ten sam typ zasobu. |

### <a name="limitations-when-scoped-to-a-resource"></a>Ograniczenia dotyczące zakresu zasobów

Gdy zakres zapytania jest obszarem roboczym Log Analytics lub aplikacją Application Insights, dostępne są wszystkie opcje w portalu i wszystkie polecenia zapytań. W przypadku określania zakresu zasobu następujące opcje w portalu nie są dostępne, ponieważ są skojarzone z pojedynczym obszarem roboczym lub aplikacją:

- Zapisywanie
- Eksplorator zapytań
- Nowa reguła alertu

Nie można użyć następujących poleceń w zapytaniu w zakresie zasobu, ponieważ zakres zapytania obejmuje już wszystkie obszary robocze zawierające dane dla tego zasobu lub zestawu zasobów:

- [aplikacje](app-expression.md)
- [obszary](workspace-expression.md)
 

## <a name="query-limits"></a>Limity zapytań
Aby zasób platformy Azure mógł zapisywać dane w wielu obszarach roboczych Log Analytics, mogą istnieć wymagania biznesowe. Obszar roboczy nie musi znajdować się w tym samym regionie co zasób, a pojedynczy obszar roboczy może zbierać dane z zasobów w różnych regionach.  

Ustawienie zakresu dla zasobu lub zestawu zasobów jest szczególnie zaawansowaną funkcją Log Analytics, ponieważ umożliwia automatyczne konsolidowanie danych rozproszonych w jednym zapytaniu. Jeśli dane wymagają pobrania z obszarów roboczych w wielu regionach platformy Azure, może to znacząco wpłynąć na wydajność.

Log Analytics pomaga chronić przed nadmiernym obciążeniem z zapytań, które rozciągają się na obszary robocze w wielu regionach, wydając ostrzeżenie lub błąd w przypadku korzystania z określonej liczby regionów. Zapytanie zostanie wyświetlone, jeśli zakres obejmuje obszary robocze w 5 lub więcej regionów. nadal będzie działać, ale jego ukończenie może zająć dużo czasu.

![Ostrzeżenie dotyczące zapytania](media/scope/query-warning.png)

Jeśli zakres obejmuje obszary robocze w 20 lub więcej regionach, uruchomienie zapytania będzie blokowane. W takim przypadku zostanie wyświetlony monit o zmniejszenie liczby regionów obszaru roboczego i próba ponownego uruchomienia zapytania. Na liście rozwijanej będą wyświetlane wszystkie regiony w zakresie zapytania i należy zmniejszyć liczbę regionów przed próbą ponownego uruchomienia zapytania.

![Zapytanie nie powiodło się](media/scope/query-failed.png)


## <a name="time-range"></a>Przedział czasu
Zakres czasu określa zestaw rekordów, które są oceniane dla zapytania w oparciu o czas utworzenia rekordu. Jest to zdefiniowane przez standardową właściwość dla każdego rekordu w obszarze roboczym lub aplikacji, zgodnie z opisem w poniższej tabeli.

| Lokalizacja | Właściwość |
|:---|:---|
| Obszar roboczy usługi Log Analytics          | TimeGenerated |
| Aplikacja Application Insights | sygnatura czasowa     |

Ustaw zakres czasu, wybierając go z selektora czas u góry okna Log Analytics.  Możesz wybrać wstępnie zdefiniowany okres lub wybrać opcję **niestandardowe** , aby określić konkretny zakres czasu.

![Wybór godziny](media/scope/time-picker.png)

Jeśli ustawisz filtr w zapytaniu, które używa właściwości czasu standardowego, jak pokazano w powyższej tabeli, selektor czasu zostanie zmieniony na **Ustaw w kwerendzie**, a selektor czasu jest wyłączony. W takim przypadku najlepiej jest umieścić filtr w górnej części zapytania, tak aby każde kolejne przetwarzanie działało tylko z filtrowanymi rekordami.

![Filtrowane zapytanie](media/scope/query-filtered.png)

Jeśli używasz [obszaru roboczego](workspace-expression.md) lub [aplikacji](app-expression.md) do pobierania danych z innego obszaru roboczego lub aplikacji, selektor czasu może zachowywać się inaczej. Jeśli zakres jest obszarem roboczym Log Analytics i używasz **aplikacji**lub jeśli zakres jest aplikacją Application Insights i używasz **obszaru roboczego**, log Analytics może nie rozumieć, że właściwość użyta w filtrze powinna określać filtr czasu.

W poniższym przykładzie zakres jest ustawiany na obszar roboczy Log Analytics.  Zapytanie używa **obszaru roboczego** do pobierania danych z innego obszaru roboczego log Analytics. Selektor godziny zmienia się **w celu ustawienia w zapytaniu** , ponieważ widzi filtr, który używa oczekiwanej właściwości **TimeGenerated** .

![Zapytanie z obszarem roboczym](media/scope/query-workspace.png)

Jeśli zapytanie używa **aplikacji** do pobierania danych z aplikacji Application Insights, log Analytics nie rozpoznaje właściwości **timestamp** w filtrze, a selektor czasu pozostaje niezmieniony. W takim przypadku stosowane są oba filtry. W tym przykładzie tylko rekordy utworzone w ciągu ostatnich 24 godzin są zawarte w zapytaniu, mimo że określa 7 dni w klauzuli **WHERE** .

![Zapytanie z aplikacją](media/scope/query-app.png)

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [samouczkiem dotyczącym korzystania z log Analytics w Azure Portal](get-started-portal.md).
- Zapoznaj się z [samouczkiem dotyczącym pisania zapytań](get-started-queries.md).