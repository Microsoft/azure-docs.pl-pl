---
title: Wyświetlanie wielu zasobów w Eksplorator metryk
description: Informacje na temat wizualizacji wielu zasobów na Azure Monitor Eksplorator metryk
author: ritaroloff
services: azure-monitor
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: riroloff
ms.subservice: metrics
ms.openlocfilehash: 9d1460a8bebe75a3ee471eb304fcf642d566b5dd
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614646"
---
# <a name="viewing-multiple-resources-in-metrics-explorer"></a>Wyświetlanie wielu zasobów w Eksplorator metryk

Selektor zakresu zasobów umożliwia wyświetlanie metryk dla wielu zasobów, które znajdują się w tej samej subskrypcji i regionie. Poniżej znajdują się instrukcje dotyczące wyświetlania wielu zasobów w Eksplorator metryk Azure Monitor. 

## <a name="selecting-a-resource"></a>Wybieranie zasobu 

Wybierz pozycję **metryki** z menu **Azure monitor** lub z sekcji **monitorowanie** menu zasobu. Kliknij przycisk "Wybierz zakres", aby otworzyć selektor zakresu zasobów, który umożliwi wybranie zasobów, dla których chcesz wyświetlić metryki. Powinno to być już wypełnione, jeśli otwarto Eksploratora metryk z menu zasobów. 

![Zrzut ekranu przedstawiający selektor zakresu zasobów wyróżniony kolorem czerwonym](./media/metrics-charts/019.png)

## <a name="selecting-multiple-resources"></a>Wybieranie wielu zasobów 

Niektóre typy zasobów umożliwiły możliwość wykonywania zapytań o metryki w wielu zasobach, o ile znajdują się w tej samej subskrypcji i lokalizacji. Te typy zasobów można znaleźć w górnej części listy rozwijanej "typy zasobów". 

![Zrzut ekranu przedstawiający listę rozwijaną zasobów, które są zgodne z obsługą wiele zasobów ](./media/metrics-charts/020.png)

> [!WARNING] 
> Musisz mieć uprawnienia do monitorowania czytnika na poziomie subskrypcji, aby wizualizować metryki dla wielu zasobów, grup zasobów lub subskrypcji. Aby to zrobić, postępuj zgodnie z instrukcjami w [tym dokumencie](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

Aby wizualizować metryki dla wielu zasobów, Zacznij od wybrania wielu zasobów w selektorze zakresu zasobów. 

![Zrzut ekranu pokazujący sposób wybierania wielu zasobów](./media/metrics-charts/021.png)

> [!NOTE]
> Można wybrać wiele zasobów w ramach tego samego typu zasobu, lokalizacji i subskrypcji. Nie można wybierać zasobów poza tymi kryteriami. 

Po zakończeniu zaznaczania kliknij przycisk "Zastosuj", aby zapisać swój wybór. 

## <a name="selecting-a-resource-group-or-subscription"></a>Wybieranie grupy zasobów lub subskrypcji 

> [!WARNING]
> Musisz mieć uprawnienia do monitorowania czytnika na poziomie subskrypcji, aby wizualizować metryki dla wielu zasobów, grup zasobów lub subskrypcji. 

W przypadku typów zgodnych z wieloma zasobami można także wysyłać zapytania o metryki w ramach subskrypcji lub wielu grup zasobów. Zacznij od wybrania subskrypcji lub jednej lub większej liczby grup zasobów: 

![Zrzut ekranu pokazujący sposób wykonywania zapytań w wielu grupach zasobów ](./media/metrics-charts/022.png)

Następnie należy wybrać typ zasobu i lokalizację, aby można było kontynuować stosowanie nowego zakresu. 

![Zrzut ekranu pokazujący wybrane grupy zasobów ](./media/metrics-charts/023.png)

Można również rozszerzyć wybrane zakresy, aby sprawdzić, które zasoby zostaną zastosowane.

![Zrzut ekranu pokazujący wybrane zasoby w grupach ](./media/metrics-charts/024.png)

Po zakończeniu wybierania zakresów kliknij przycisk "Zastosuj", aby zapisać wybrane opcje. 

## <a name="splitting-and-filtering-by-resource-group-or-resources"></a>Dzielenie i filtrowanie według grupy zasobów lub zasobów

Po wykreśleniu zasobów można użyć narzędzia do dzielenia i filtrowania, aby uzyskać więcej szczegółowych informacji na temat danych. 

Dzielenie pozwala wizualizować sposób porównywania różnych segmentów metryk ze sobą. Na przykład podczas wykreślania metryki dla wielu zasobów można użyć narzędzia "Zastosuj podział", aby podzielić według identyfikatora zasobu lub grupy zasobów. Pozwoli to łatwo porównać pojedynczą metrykę z wieloma zasobami lub grupami zasobów.  

Na przykład poniżej znajduje się wykres procentowego procesora CPU w 9VMs. Dzielenie według identyfikatora zasobu pozwala łatwo zobaczyć, jak procentowy procesor CPU jest różny dla maszyny wirtualnej. 

![Zrzut ekranu pokazujący, jak można używać dzielenia, aby zobaczyć procentowy procesor CPU na maszynę wirtualną](./media/metrics-charts/026.png)

Oprócz dzielenia, można użyć funkcji filtrowania, aby wyświetlić tylko te grupy zasobów, które mają być wyświetlane.  Jeśli na przykład chcesz wyświetlić procentowy procesor dla maszyn wirtualnych dla określonej grupy zasobów, możesz użyć narzędzia Dodaj filtr, aby odfiltrować według grupy zasobów. W tym przykładzie filtr TailspinToysDemo, który usuwa metryki skojarzone z zasobami w TailspinToys. 

![Zrzut ekranu pokazujący, jak można filtrować według grupy zasobów](./media/metrics-charts/027.png)

## <a name="pinning-your-multi-resource-charts"></a>Przypinanie wykresów wielozasobówowych 

> [!WARNING] 
> Musisz mieć uprawnienia do monitorowania czytnika na poziomie subskrypcji, aby wizualizować metryki dla wielu zasobów, grup zasobów lub subskrypcji. Aby to zrobić, postępuj zgodnie z instrukcjami w [tym dokumencie](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal). 

Aby przypiąć Wykres wielowymiarowy, postępuj zgodnie z instrukcjami znajdującymi się [tutaj](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts#pin-charts-to-dashboards). 

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z Eksploratorem metryk](metrics-troubleshoot.md)
* [Zobacz listę dostępnych metryk dla usług platformy Azure](metrics-supported.md)
* [Zobacz przykłady skonfigurowanych wykresów](metric-chart-samples.md)

