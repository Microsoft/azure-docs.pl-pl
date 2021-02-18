---
title: Informacje o ustawieniach automatycznego skalowania w Azure Monitor
description: Szczegółowy podział ustawień automatycznego skalowania i sposób ich działania. Dotyczy Virtual Machines, Cloud Services Web Apps
ms.topic: conceptual
ms.date: 12/18/2017
ms.subservice: autoscale
ms.openlocfilehash: a914f6d71c013acea8dfde0f6578985bc009bb26
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100617590"
---
# <a name="understand-autoscale-settings"></a>Omówienie ustawień automatycznego skalowania
Ustawienia automatycznego skalowania pomagają upewnić się, że dysponujesz odpowiednią ilością zasobów, aby obsłużyć wahanie obciążenia aplikacji. Można skonfigurować ustawienia skalowania automatycznego, które mają być wyzwalane na podstawie metryk, które wskazują obciążenie lub wydajność, lub wyzwalane w zaplanowanym dniu i o określonej godzinie. Ten artykuł zawiera szczegółowy opis ustawienia skalowania automatycznego. Artykuł zaczyna się od schematu i właściwości ustawienia, a następnie analizuje różne typy profilów, które można skonfigurować. W tym artykule omówiono sposób, w jaki funkcja skalowania automatycznego na platformie Azure szacuje profil do wykonania w danym momencie.

## <a name="autoscale-setting-schema"></a>Schemat ustawień skalowania automatycznego
Aby zilustrować schemat ustawienia skalowania automatycznego, używane jest następujące ustawienie skalowania automatycznego. Należy pamiętać, że to ustawienie automatycznego skalowania ma następujące znaczenie:
- Jeden profil. 
- Dwie reguły metryk w tym profilu: jeden do skalowania w poziomie i jeden dla skalowania w poziomie.
  - Reguła skalowania w poziomie jest wyzwalana, gdy średnia wartość procentowa CPU zestawu skalowania maszyn wirtualnych wynosi powyżej 85% w ciągu ostatnich 10 minut.
  - Reguła skalowania w poziomie jest wyzwalana, gdy wartość średnia zestawu skalowania maszyn wirtualnych jest mniejsza niż 60% dla ostatniej minuty.

> [!NOTE]
> Ustawienie może mieć wiele profilów. Aby dowiedzieć się więcej, zobacz sekcję [Profile](#autoscale-profiles) . W profilu może być również zdefiniowanych wiele reguł skalowania w poziomie i reguł skalowania w poziomie. Aby zobaczyć, jak są oceniane, zobacz sekcję [Ocena](#autoscale-evaluation) .

```JSON
{
  "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/setting1",
  "name": "setting1",
  "type": "Microsoft.Insights/autoscaleSettings",
  "location": "East US",
  "properties": {
    "enabled": true,
    "targetResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
    "profiles": [
      {
        "name": "mainProfile",
        "capacity": {
          "minimum": "1",
          "maximum": "4",
          "default": "1"
        },
        "rules": [
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "GreaterThan",
              "threshold": 85
            },
            "scaleAction": {
              "direction": "Increase",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          },
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "LessThan",
              "threshold": 60
            },
            "scaleAction": {
              "direction": "Decrease",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          }
        ]
      }
    ]
  }
}
```

| Sekcja | Nazwa elementu | Opis |
| --- | --- | --- |
| Ustawienie | ID (Identyfikator) | Identyfikator zasobu ustawienia skalowania automatycznego. Ustawienia automatycznego skalowania są zasobem Azure Resource Manager. |
| Ustawienie | name | Nazwa ustawienia skalowania automatycznego. |
| Ustawienie | location | Lokalizacja ustawienia skalowania automatycznego. Ta lokalizacja może różnić się od lokalizacji skalowanego zasobu. |
| properties | targetResourceUri | Identyfikator zasobu dla skalowanego zasobu. Dla każdego zasobu może istnieć tylko jedno ustawienie skalowania automatycznego. |
| properties | Profil | Ustawienie automatycznego skalowania składa się z co najmniej jednego profilu. Za każdym razem, gdy działa silnik skalowania automatycznego, wykonuje jeden profil. |
| profil | name | Nazwa profilu. Możesz wybrać dowolną nazwę, która pomoże zidentyfikować profil. |
| profil | Pojemność. maksimum | Maksymalna dozwolona pojemność. Zapewnia to automatyczne skalowanie podczas wykonywania tego profilu, nie skaluje zasobu powyżej tej liczby. |
| profil | Pojemność. minimum | Minimalna dozwolona pojemność. Zapewnia to automatyczne skalowanie podczas wykonywania tego profilu, nie skaluje zasobu poniżej tej liczby. |
| profil | Pojemność. default | Jeśli wystąpił problem z odczytem metryki zasobów (w tym przypadku procesor "vmss1"), a bieżąca pojemność jest poniżej wartości domyślnej, Skalowanie automatyczne jest skalowane do wartości domyślnej. Ma to na celu zapewnienie dostępności zasobu. Jeśli bieżąca pojemność jest już wyższa niż domyślna pojemność, Skalowanie automatyczne nie jest skalowane. |
| profil | rules | Skalowanie automatyczne automatycznie skaluje się między maksymalną i minimalną pojemnością przy użyciu reguł w profilu. W profilu może być wiele reguł. Zwykle istnieją dwie reguły: jeden do określenia, kiedy należy skalować w poziomie, i drugi, aby określić, kiedy ma być skalowane. |
| Rule | metricTrigger | Definiuje warunek metryki reguły. |
| metricTrigger | metricName | Nazwa metryki. |
| metricTrigger |  metricResourceUri | Identyfikator zasobu, który emituje metrykę. W większości przypadków jest to taka sama jak w przypadku skalowanego zasobu. W niektórych przypadkach może się to różnić. Na przykład można skalować zestaw skalowania maszyn wirtualnych na podstawie liczby komunikatów w kolejce magazynu. |
| metricTrigger | timeGrain | Czas trwania próbkowania metryki. Na przykład **TimeGrain = "PT1M"** oznacza, że metryki powinny być agregowane co 1 minutę przy użyciu metody agregacji określonej w elemencie Statistics. |
| metricTrigger | Statystyka | Metoda agregacji w okresie timeGrain. Na przykład **Statystyka = "Average"** i **TIMEGRAIN = "PT1M"** oznacza, że metryki powinny być agregowane co 1 minutę, pobierając średnią. Ta właściwość określa sposób próbkowania metryki. |
| metricTrigger | timeWindow | Ilość czasu, aby wyszukać metryki. Na przykład **timeWindow = "PT10M"** oznacza, że za każdym razem, gdy uruchamiane jest automatyczne skalowanie, bada metryki w ciągu ostatnich 10 minut. Przedział czasu umożliwia znormalizowanie metryk i pozwala uniknąć reakcji na przejścia przejściowe. |
| metricTrigger | timeAggregation | Metoda agregacji używana do agregowania metryk próbkowanych. Na przykład **TimeAggregation = "Average"** powinna agregować metryki próbkowane, pobierając średnią. W poprzednim przypadku należy pobrać 10 1-minutowy próbek i obliczyć ich średnią. |
| Rule | scaleAction | Akcja, która ma zostać podjęta po wyzwoleniu metricTrigger reguły. |
| scaleAction | kierunek | "Zwiększ", aby skalować w poziomie lub "Zmniejsz" w celu skalowania w poziomie.|
| scaleAction | wartość | Jak znacznie zwiększyć lub zmniejszyć pojemność zasobu. |
| scaleAction | cooldown | Czas oczekiwania po operacji skalowania przed ponownym skalowaniem. Na przykład jeśli **cooldown = "PT10M"**, funkcja automatycznego skalowania nie próbuje ponownie skalować przez kolejne 10 minut. Cooldown to umożliwienie ustabilizowania metryk po dodaniu lub usunięciu wystąpień. |

## <a name="autoscale-profiles"></a>Profile skalowania automatycznego

Istnieją trzy typy profilów automatycznego skalowania:

- **Zwykły profil:** Najpopularniejszy profil. Jeśli nie musisz skalować zasobu na podstawie dnia tygodnia lub w określonym dniu, możesz użyć zwykłego profilu. Ten profil można następnie skonfigurować przy użyciu reguł metryk, które określają, kiedy skalować w poziomie i kiedy skalowanie ma być skalowane. Powinien być zdefiniowany tylko jeden profil regularny.

    Przykładowy profil użyty wcześniej w tym artykule stanowi przykład zwykłego profilu. Należy zauważyć, że można również ustawić profil do skalowania do liczby wystąpień statycznych dla zasobu.

- **Profil daty ustalonej:** Ten profil dotyczy specjalnych przypadków. Załóżmy na przykład, że masz ważne zdarzenie w dniu 26 grudnia 2017 (PST). Chcesz, aby minimalne i maksymalne pojemności zasobu były różne w danym dniu, ale nadal Skaluj te same metryki. W takim przypadku należy dodać profil daty ustalonej do listy profilów ustawień. Profil jest skonfigurowany do uruchamiania tylko w dniu zdarzenia. W przypadku każdego innego dnia automatyczne skalowanie używa zwykłego profilu.

    ```json
    "profiles": [
        {
            "name": " regularProfile",
            "capacity": {
                ...
            },
            "rules": [
                {
                ...
                },
                {
                ...
                }
            ]
        },
        {
            "name": "eventProfile",
            "capacity": {
            ...
            },
            "rules": [
                {
                ...
                }, 
                {
                ...
                }
            ],
            "fixedDate": {
                "timeZone": "Pacific Standard Time",
                "start": "2017-12-26T00:00:00",
                "end": "2017-12-26T23:59:00"
            }
        }
    ]
    ```
    
- **Profil cyklu:** Ten typ profilu umożliwia upewnienie się, że ten profil jest zawsze używany w określonym dniu tygodnia. Profile cyklu mają tylko godzinę rozpoczęcia. Są one uruchamiane do momentu, gdy zostanie ustawiony profil następnego cyklu lub ustalonej daty. Ustawienie skalowania automatycznego z tylko jednym profilem cyklu powoduje uruchomienie tego profilu nawet w przypadku, gdy w tym samym ustawieniu jest zdefiniowany zwykły profil. Poniższe dwa przykłady ilustrują sposób korzystania z tego profilu:

    **Przykład 1: dni tygodnia a weekendy**
    
    Załóżmy, że w weekendy potrzebujesz maksymalnej pojemności 4. W dniach tygodnia, ponieważ oczekujesz większej liczby obciążeń, Maksymalna pojemność wynosi 10. W takim przypadku Twoje ustawienie będzie zawierać dwa profile cyklu — jeden do uruchamiania w weekendy i drugi w dniach tygodnia.
    To ustawienie wygląda następująco:

    ``` JSON
    "profiles": [
    {
    "name": "weekdayProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }}
    },
    {
    "name": "weekendProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Saturday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```

    Powyższe ustawienie pokazuje, że każdy profil cyklu ma harmonogram. Ten harmonogram określa, kiedy zostanie uruchomiony profil. Profil zostaje zatrzymany, gdy jest czas na uruchomienie innego profilu.

    Na przykład w poprzednim ustawieniu opcja "weekdayProfile" jest ustawiana jako uruchamiana w poniedziałek o godzinie 12:00. Oznacza to, że ten profil zaczyna działać w poniedziałek o godzinie 12:00. Kontynuuje do soboty o godzinie 12:00, gdy zaplanowano uruchomienie "weekendProfile".

    **Przykład 2: godziny pracy**
    
    Załóżmy, że chcesz mieć jeden próg metryki w godzinach pracy (9:00 AM do 5:00 PM), a drugi dla pozostałych godzin. To ustawienie będzie wyglądać następująco:
    
    ``` JSON
    "profiles": [
    {
    "name": "businessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                9
            ],
            "minutes": [
                0
            ]
        }
    }
    },
    {
    "name": "nonBusinessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                17
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```
    
    Powyższe ustawienie pokazuje, że "businessHoursProfile" zaczyna działać w poniedziałek o godzinie 9:00 AM i nadal 5:00 PM. Jest to wykonywane po uruchomieniu "nonBusinessHoursProfile". "NonBusinessHoursProfile" jest uruchamiany do 9:00 wtorek, a następnie ponownie trwa wykonywanie "businessHoursProfile". Powtarza się to do piątku o godzinie 5:00 PM. W tym momencie "nonBusinessHoursProfile" jest uruchamiany cały poniedziałek o godzinie 9:00.
    
> [!Note]
> Interfejs użytkownika automatycznego skalowania w Azure Portal wymusza czasy zakończenia dla profilów cyklu i rozpoczyna uruchamianie domyślnego profilu ustawienia skalowania automatycznego w profilach cykli.
    
## <a name="autoscale-evaluation"></a>Obliczanie automatycznego skalowania
Uwzględniając, że ustawienia skalowania automatycznego mogą mieć wiele profilów, a każdy profil może mieć wiele reguł metryk, ważne jest, aby zrozumieć, jak jest oceniane ustawienie skalowania automatycznego. Za każdym razem, gdy uruchamiane jest zadanie automatycznego skalowania, rozpoczyna się od wybrania odpowiedniego profilu. Następnie funkcja automatycznego skalowania szacuje wartości minimalne i maksymalne oraz wszystkie reguły metryk w profilu i decyduje o konieczności wykonania akcji skalowania.

### <a name="which-profile-will-autoscale-pick"></a>Który profil zostanie automatycznie przeskalowany?

Automatyczne skalowanie używa następującej sekwencji do wybrania profilu:
1. Najpierw szuka dowolnego stałego profilu daty, który jest skonfigurowany do uruchamiania teraz. Jeśli jest, automatyczne skalowanie jest wykonywane. Jeśli istnieje wiele profilów dat stałych, które powinny być uruchamiane, funkcja automatycznego skalowania wybiera pierwszy.
2. Jeśli nie ma żadnych stałych profilów dat, funkcja automatycznego skalowania wyszukuje profile cyklu. Jeśli zostanie znaleziony profil cyklu, zostanie on uruchomiony.
3. Jeśli nie ma żadnych ustalonych profilów dat lub cykli, funkcja automatycznego skalowania uruchamia zwykły profil.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Jak automatyczne skalowanie jest oceniane w wielu regułach?

Gdy funkcja automatycznego skalowania określi profil do uruchomienia, oblicza wszystkie reguły skalowania w poziomie w profilu (są to reguły z **kierunekem "Zwiększ"**).

Jeśli co najmniej jedna reguła skalowania w poziomie jest wyzwalana, funkcja automatycznego skalowania oblicza nową pojemność określoną przez **scaleAction** każdej z tych reguł. Następnie można skalować do maksymalnie tych pojemności, aby zapewnić dostępność usług.

Załóżmy na przykład, że istnieje zestaw skalowania maszyn wirtualnych z aktualną pojemnością 10. Istnieją dwie reguły skalowania w poziomie: jeden, który zwiększa wydajność o 10%, a drugi zwiększa pojemność o 3 zliczenia. Pierwsza reguła spowoduje powstanie nowej pojemności 11, a druga reguła spowoduje, że zostanie osiągnięta pojemność 13. W celu zapewnienia dostępności usługi funkcja automatycznego skalowania wybiera akcję, która skutkuje maksymalną pojemnością, więc zostanie wybrana druga reguła.

Jeśli nie zostanie wyzwolona żadna reguła skalowania w poziomie, funkcja automatycznego skalowania oceni wszystkie reguły skalowania (reguły z **kierunkiem "Zmniejsz"**). Funkcja automatycznego skalowania przyjmuje tylko akcję skalowania, jeśli zostaną wyzwolone wszystkie reguły skalowania w poziomie.

Funkcja automatycznego skalowania oblicza nową pojemność określoną przez **scaleAction** każdej z tych reguł. Następnie wybiera akcję skalowania, która daje w wyniku maksymalną liczbę tych zdolności do zapewnienia dostępności usługi.

Załóżmy na przykład, że istnieje zestaw skalowania maszyn wirtualnych z aktualną pojemnością 10. Istnieją dwie reguły skalowania: jeden, który zmniejsza pojemność o 50%, i jeden, który zmniejsza pojemność o 3 zliczenia. Pierwsza reguła spowoduje powstanie nowej pojemności 5, a druga reguła spowoduje zwiększenie pojemności 7. W celu zapewnienia dostępności usługi funkcja automatycznego skalowania wybiera akcję, która skutkuje maksymalną pojemnością, więc zostanie wybrana druga reguła.

## <a name="next-steps"></a>Następne kroki
Więcej informacji na temat automatycznego skalowania można znaleźć w następujących tematach:

* [Omówienie skalowania automatycznego](./autoscale-overview.md)
* [Azure Monitor metryki automatycznego skalowania](./autoscale-common-metrics.md)
* [Najlepsze rozwiązania dotyczące skalowania automatycznego w usłudze Azure Monitor](./autoscale-best-practices.md)
* [Używanie akcji skalowania automatycznego do wysyłania powiadomień o alertach dotyczących wiadomości e-mail i elementów webhook](./autoscale-webhook-email.md)
* [Interfejs API REST automatycznego skalowania](/rest/api/monitor/autoscalesettings)

