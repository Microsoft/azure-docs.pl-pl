---
title: Monitorowanie zasobu platformy Azure za pomocą Azure Monitor
description: Dowiedz się, jak zbierać i analizować dane dla zasobu platformy Azure w Azure Monitor.
ms. subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: c4b80e62d3800392b847a411dfc66c3278e72bba
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77661868"
---
# <a name="quickstart-monitor-an-azure-resource-with-azure-monitor"></a>Szybki Start: monitorowanie zasobu platformy Azure za pomocą Azure Monitor
[Azure monitor](../overview.md) uruchamia zbieranie danych z zasobów platformy Azure w momencie ich tworzenia. Ten przewodnik Szybki Start zawiera krótki przewodnik dotyczący danych, które są automatycznie zbierane dla zasobu i sposobu wyświetlania go w Azure Portal dla określonego zasobu. Później można dodać konfigurację w celu zebrania dodatkowych danych i można przejść do menu Azure Monitor, aby użyć tych samych narzędzi do uzyskiwania dostępu do danych zebranych dla wszystkich zasobów w ramach subskrypcji.

Aby uzyskać bardziej szczegółowe opisy danych monitorowania zbieranych z zasobów platformy Azure, zobacz [monitorowanie zasobów platformy Azure za pomocą Azure monitor](../insights/monitor-azure-resource.md).


## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com). 


## <a name="overview-page"></a>Strona przeglądu
Wiele usług będzie obejmować dane monitorowania na stronie **przeglądowej** w celu szybkiego wglądu w ich działania. Zwykle jest to oparte na podzestawie metryk platformy przechowywanych w metrykach Azure Monitor.

1. Znajdź zasób platformy Azure w ramach subskrypcji.
2. Przejdź do strony **Przegląd** i sprawdź, czy są wyświetlane jakieś dane wydajności. Te dane zostaną dostarczone przez Azure Monitor. Poniższy przykład to strona **Przegląd** dla konta usługi Azure Storage, która umożliwia wyświetlenie wielu metryk.

    ![Strona przeglądu](media/quick-monitor-azure-resource/overview.png)

3. Możesz kliknąć dowolny wykres, aby otworzyć dane w Eksploratorze metryk, który jest opisany poniżej.

## <a name="view-the-activity-log"></a>Wyświetlanie dziennika aktywności
Dziennik aktywności zawiera szczegółowe informacje o operacjach dotyczących poszczególnych zasobów platformy Azure w ramach subskrypcji. Obejmuje to takie informacje jak w przypadku utworzenia lub zmodyfikowania zasobu, gdy zadanie zostanie uruchomione lub gdy wystąpi określona operacja.

1. W górnej części menu zasobu wybierz pozycję **Dziennik aktywności**.
2. Bieżący filtr jest ustawiony na zdarzenia związane z zasobem. Jeśli nie widzisz żadnych zdarzeń, spróbuj zmienić wartość **TimeSpan** , aby zwiększyć zakres czasu.

    ![Dziennik aktywności](media/quick-monitor-azure-resource/activity-log-resource.png)

4. Jeśli chcesz zobaczyć zdarzenia z innych zasobów w ramach subskrypcji, Zmień kryteria w filtrze, a nawet Usuń właściwości filtru.

    ![Dziennik aktywności](media/quick-monitor-azure-resource/activity-log-all.png)



## <a name="view-metrics"></a>Wyświetlanie metryk
Metryki to wartości liczbowe, które opisują pewne aspekty zasobu w określonym czasie. Azure Monitor automatycznie zbiera metryki platformy w ciągu jednej minuty od wszystkich zasobów platformy Azure. Możesz wyświetlić te metryki przy użyciu Eksploratora metryk.

1. W sekcji **monitorowanie** menu zasobu wybierz pozycję **metryki**. Spowoduje to otwarcie Eksploratora metryk z zakresem ustawionym na zasób.
2. Kliknij pozycję **Dodaj metrykę** , aby dodać do wykresu metrykę.
   
   ![Eksplorator metryk](media/quick-monitor-azure-resource/metrics-explorer-01.png)
   
4. Wybierz **metrykę** z listy rozwijanej, a następnie **agregację**. Definiuje sposób próbkowania zebranych wartości w każdym przedziale czasu.

    ![Eksplorator metryk](media/quick-monitor-azure-resource/metrics-explorer-02.png)

5. Kliknij pozycję **Dodaj metrykę** , aby dodać do wykresu dodatkowe kombinacje metryk i agregacji.

    ![Eksplorator metryk](media/quick-monitor-azure-resource/metrics-explorer-03.png)



## <a name="next-steps"></a>Następne kroki
W tym przewodniku szybki start Oglądasz dziennik aktywności i metryki dla zasobu platformy Azure, które są automatycznie zbierane przez Azure Monitor. Dzienniki zasobów zapewniają wgląd w szczegółowe operacje dotyczące zasobu, ale muszą być skonfigurowane w celu ich zebrania. Przejdź do samouczka dotyczącego zbierania dzienników zasobów w obszarze roboczym Log Analytics, gdzie można je analizować przy użyciu zapytań dzienników.

> [!div class="nextstepaction"]
> [Zbieranie i analizowanie dzienników zasobów przy użyciu Azure Monitor](tutorial-resource-logs.md)
