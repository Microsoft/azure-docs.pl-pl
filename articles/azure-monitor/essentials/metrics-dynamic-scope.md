---
title: Wyświetlanie wielu zasobów w Eksploratorze metryk platformy Azure
description: Dowiedz się, jak wizualizować wiele zasobów przy użyciu Eksploratora metryk platformy Azure.
author: ritaroloff
services: azure-monitor
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: riroloff
ms.openlocfilehash: 85fa252f999407d5cd7f75e954554e3b14357071
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037323"
---
# <a name="view-multiple-resources-in-the-azure-metrics-explorer"></a>Wyświetlanie wielu zasobów w Eksploratorze metryk platformy Azure

Selektor zakresu zasobów umożliwia wyświetlanie metryk dla wielu zasobów, które znajdują się w tej samej subskrypcji i regionie. W tym artykule opisano sposób wyświetlania wielu zasobów przy użyciu funkcji Eksploratora metryk platformy Azure Azure Monitor. 

## <a name="select-a-resource"></a>Wybieranie zasobu 

Wybierz pozycję **metryki** z menu **Azure monitor** lub z sekcji **monitorowanie** menu zasobu. Następnie wybierz **pozycję Wybierz zakres** , aby otworzyć selektor zakresu. 

Użyj selektora zakresu, aby wybrać zasoby, których metryki chcesz zobaczyć. Należy wypełnić zakres, Jeśli Eksplorator metryk został otwarty z menu zasobów. 

![Zrzut ekranu przedstawiający sposób otwierania selektora zakresu zasobów.](./media/metrics-dynamic-scope/019.png)

## <a name="select-multiple-resources"></a>Wybierz wiele zasobów 

Niektóre typy zasobów mogą wykonywać zapytania dotyczące metryk dla wielu zasobów. Metryki muszą znajdować się w tej samej subskrypcji i lokalizacji. Znajdź te typy zasobów w górnej części menu **typy zasobów** .

![Zrzut ekranu pokazujący menu zasobów, które są zgodne z wieloma zasobami.](./media/metrics-dynamic-scope/020.png)

> [!WARNING] 
> Musisz mieć uprawnienia do monitorowania czytnika na poziomie subskrypcji, aby wizualizować metryki dla wielu zasobów, grup zasobów lub subskrypcji. Aby uzyskać więcej informacji, zobacz [Przypisywanie ról platformy Azure przy użyciu Azure Portal](../../role-based-access-control/role-assignments-portal.md).

Aby wizualizować metryki dla wielu zasobów, Zacznij od wybrania wielu zasobów w selektorze zakresu zasobów. 

![Zrzut ekranu pokazujący sposób wybierania wielu zasobów.](./media/metrics-dynamic-scope/021.png)

> [!NOTE]
> Wybrane zasoby muszą znajdować się w tym samym typie zasobu, lokalizacji i subskrypcji. Nie są wybierane zasoby, które nie pasują do tych kryteriów. 

Po zakończeniu wybierz pozycję **Zastosuj** , aby zapisać wybrane opcje. 

## <a name="select-a-resource-group-or-subscription"></a>Wybierz grupę zasobów lub subskrypcję 

> [!WARNING]
> Musisz mieć uprawnienia do monitorowania czytnika na poziomie subskrypcji, aby wizualizować metryki dla wielu zasobów, grup zasobów lub subskrypcji. 

W przypadku typów, które są zgodne z wieloma zasobami, można wysyłać zapytania o metryki w ramach subskrypcji lub wielu grup zasobów. Zacznij od wybrania subskrypcji lub jednej lub większej liczby grup zasobów: 

![Zrzut ekranu pokazujący sposób wykonywania zapytań w wielu grupach zasobów.](./media/metrics-dynamic-scope/022.png)

Wybierz typ zasobu i lokalizację. 

![Zrzut ekranu pokazujący wybrane grupy zasobów.](./media/metrics-dynamic-scope/023.png)

Można rozwinąć wybrane zakresy, aby sprawdzić zasoby, których dotyczą wybrane wybory.

![Zrzut ekranu pokazujący wybrane zasoby w grupach.](./media/metrics-dynamic-scope/024.png)

Po zakończeniu wybierania zakresów wybierz pozycję **Zastosuj**. 

## <a name="split-and-filter-by-resource-group-or-resources"></a>Dzielenie i filtrowanie według grupy zasobów lub zasobów

Po wykreśleniu zasobów można używać dzielenia i filtrowania, aby uzyskać więcej szczegółowych informacji na temat danych. 

Dzielenie pozwala wizualizować sposób porównywania różnych segmentów metryk ze sobą. Na przykład podczas wykreślania metryki dla wielu zasobów można wybrać pozycję **Zastosuj podział** , aby podzielić według identyfikatora zasobu lub grupy zasobów. Podział umożliwia porównanie pojedynczej metryki dla wielu zasobów lub grup zasobów.  

Na przykład poniższy wykres pokazuje procentowy procesor CPU w dziewięciu maszynach wirtualnych. Podział według identyfikatora zasobu pozwala zobaczyć, jak procentowy procesor różni się w stosunku do maszyny wirtualnej. 

![Zrzut ekranu, który pokazuje, jak używać dzielenia, aby zobaczyć procentowy procesor między maszynami wirtualnymi.](./media/metrics-dynamic-scope/026.png)

Podobnie jak w przypadku dzielenia, można użyć filtrowania, aby wyświetlić tylko grupy zasobów, które mają być wyświetlane.  Aby na przykład wyświetlić procentowy procesor dla maszyn wirtualnych dla określonej grupy zasobów, możesz wybrać opcję **Dodaj filtr** do filtrowania według grupy zasobów. 

W tym przykładzie filtruje według TailspinToysDemo. W tym miejscu filtr usuwa metryki skojarzone z zasobami w TailspinToys. 

![Zrzut ekranu pokazujący sposób filtrowania według grupy zasobów.](./media/metrics-dynamic-scope/027.png)

## <a name="pin-multiple-resource-charts"></a>Przypnij wykresy z wieloma zasobami 

Wykresy obejmujące wiele zasobów, które wizualizują metryki w grupach zasobów i subskrypcjach, wymagają, aby użytkownik miał uprawnienia do *monitorowania czytnika* na poziomie subskrypcji. Upewnij się, że wszyscy użytkownicy pulpitów nawigacyjnych, do których są przypinane wykresy z wieloma zasobami, mają wystarczające uprawnienia. Aby uzyskać więcej informacji, zobacz [Przypisywanie ról platformy Azure przy użyciu Azure Portal](../../role-based-access-control/role-assignments-portal.md).

Aby przypiąć wykres z wieloma zasobami do pulpitu nawigacyjnego, zobacz [przypinanie do pulpitów nawigacyjnych](../essentials/metrics-charts.md#pinning-to-dashboards). 

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z Eksploratorem metryk](../essentials/metrics-troubleshoot.md)
* [Zobacz listę dostępnych metryk dla usług platformy Azure](./metrics-supported.md)
* [Zobacz przykłady skonfigurowanych wykresów](../essentials/metric-chart-samples.md)