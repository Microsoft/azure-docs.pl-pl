---
title: Monitorowanie zasobu platformy Azure za pomocą Azure Monitor
description: Dowiedz się, jak zbierać i analizować dane dla zasobu platformy Azure w Azure Monitor.
ms. subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: a17546f11db881ecacd39986bcd80d9d63b24644
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448211"
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

    ![Zrzut ekranu dziennika aktywności zasobów w Azure Portal przedstawiający listę operacji dla zasobu wygenerowanego przy użyciu domyślnych kryteriów filtrowania.](media/quick-monitor-azure-resource/activity-log-resource.png)

4. Jeśli chcesz zobaczyć zdarzenia z innych zasobów w ramach subskrypcji, Zmień kryteria w filtrze, a nawet Usuń właściwości filtru.

    ![Zrzut ekranu dziennika aktywności zasobów w Azure Portal przedstawiający listę operacji dla zasobu wygenerowanego przy użyciu zmodyfikowanych kryteriów filtrowania.](media/quick-monitor-azure-resource/activity-log-all.png)



## <a name="view-metrics"></a>Wyświetlanie metryk
Metryki to wartości liczbowe, które opisują pewne aspekty zasobu w określonym czasie. Azure Monitor automatycznie zbiera metryki platformy w ciągu jednej minuty od wszystkich zasobów platformy Azure. Możesz wyświetlić te metryki przy użyciu Eksploratora metryk.

1. W sekcji **monitorowanie** menu zasobu wybierz pozycję **metryki**. Spowoduje to otwarcie Eksploratora metryk z zakresem ustawionym na zasób.
2. Kliknij pozycję **Dodaj metrykę** , aby dodać do wykresu metrykę.
   
   ![Zrzut ekranu Eksploratora metryk w Azure Portal pokazujący metryki dla zasobu. Lista rozwijana Metryka jest otwarta, aby dodać nowe metryki.](media/quick-monitor-azure-resource/metrics-explorer-01.png)
   
4. Wybierz **metrykę** z listy rozwijanej, a następnie **agregację**. Definiuje sposób próbkowania zebranych wartości w każdym przedziale czasu.

    ![Zrzut ekranu Eksploratora metryk w Azure Portal pokazujący metryki dla zasobu. Lista rozwijana agregacji jest zaznaczona i została wybrana wartość średnia.](media/quick-monitor-azure-resource/metrics-explorer-02.png)

5. Kliknij pozycję **Dodaj metrykę** , aby dodać do wykresu dodatkowe kombinacje metryk i agregacji.

    ![Zrzut ekranu Eksploratora metryk w Azure Portal pokazujący metryki dla zasobu.](media/quick-monitor-azure-resource/metrics-explorer-03.png)



## <a name="next-steps"></a>Następne kroki
W tym przewodniku szybki start Oglądasz dziennik aktywności i metryki dla zasobu platformy Azure, które są automatycznie zbierane przez Azure Monitor. Przejdź do następnego przewodnika Szybki Start, który pokazuje, jak zebrać dziennik aktywności do obszaru roboczego Log Analytics, gdzie można je analizować przy użyciu [zapytań dzienników](../log-query/log-query-overview.md).

> [!div class="nextstepaction"]
> [Wyślij dziennik aktywności platformy Azure do obszaru roboczego Log Analytics]()

