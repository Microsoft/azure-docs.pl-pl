---
title: Najlepsze rozwiązania dotyczące automatycznego skalowania
description: Automatyczne skalowanie wzorców na platformie Azure dla Web Apps, zestawów skalowania maszyn wirtualnych i Cloud Services
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 9aaf9525f2fedee67a86011e938b8e995ccfe9fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100617823"
---
# <a name="best-practices-for-autoscale"></a>Najlepsze rozwiązania dotyczące automatycznego skalowania
Automatyczne skalowanie Azure Monitor ma zastosowanie tylko do [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)i [usług API Management](../../api-management/api-management-key-concepts.md).

## <a name="autoscale-concepts"></a>Pojęcia dotyczące skalowania automatycznego

* Zasób może mieć tylko *jedno* ustawienie automatycznego skalowania
* Ustawienie automatycznego skalowania może mieć co najmniej jeden profil, a każdy profil może mieć co najmniej jedną regułę skalowania automatycznego.
* Ustawienie skalowania automatycznego skaluje wystąpienia w poziomie, *co jest spowodowane* zwiększeniem wystąpień i *w* wyniku zmniejszenia liczby wystąpień.
  Ustawienie skalowania automatycznego ma wartość maksymalną, minimalną i domyślną dla wystąpień.
* Zadanie automatycznego skalowania zawsze odczytuje skojarzoną metrykę do skalowania przez, sprawdzając, czy przekroczy skonfigurowany próg skalowania w poziomie lub w poziomie. Możesz wyświetlić listę metryk, które Skalowanie automatyczne może skalować, w [Azure monitor często spotykane metryki](autoscale-common-metrics.md).
* Wszystkie progi są obliczane na poziomie wystąpienia. Na przykład "Skaluj w poziomie o jedno wystąpienie, gdy średniej procesora > 80%, gdy liczba wystąpień wynosi 2", oznacza skalowanie w poziomie, gdy średni czas CPU we wszystkich wystąpieniach jest większy niż 80%.
* Wszystkie błędy automatycznego skalowania są rejestrowane w dzienniku aktywności. Następnie można skonfigurować [alert dziennika aktywności](../alerts/activity-log-alerts.md) , aby można było otrzymywać powiadomienia za pośrednictwem poczty e-mail, wiadomości SMS lub elementów webhook, gdy występuje błąd automatycznego skalowania.
* Podobnie wszystkie pomyślne akcje skalowania są ogłaszane w dzienniku aktywności. Następnie można skonfigurować alert dziennika aktywności, aby można było otrzymywać powiadomienia za pośrednictwem poczty e-mail, wiadomości SMS lub elementów webhook, gdy istnieje pomyślna akcja automatycznego skalowania. Możesz również skonfigurować powiadomienia e-mail lub elementy webhook, aby otrzymywać powiadomienia o pomyślnych akcjach skalowania za pośrednictwem karty powiadomienia w ustawieniu Skalowanie automatyczne.

## <a name="autoscale-best-practices"></a>Najlepsze rozwiązania dotyczące skalowania automatycznego

Użyj następujących najlepszych rozwiązań w przypadku korzystania z funkcji automatycznego skalowania.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Upewnij się, że wartości maksymalne i minimalne są różne, a między nimi jest odpowiedni margines

Jeśli ustawienie określa, że minimum i maksimum to 2, a bieżąca liczba wystąpień to 2, żadna akcja skalowania nie zostanie wykonana. Zachowaj odpowiedni odstęp między maksymalną i minimalną liczbą wystąpień. Liczby określone przez wartości minimum i maksimum są także dozwolone. Autoskalowanie odbywa się zawsze między tymi limitami.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Skalowanie ręczne jest resetowane przez wartości minimalne i maksymalne autoskalowania

Jeśli ręcznie zaktualizujesz liczbę wystąpień do wartości powyższej lub mniejszej niż wartość maksymalna, aparat automatycznego skalowania automatycznie skaluje się z powrotem do minimum (jeśli poniżej) lub maksymalnego (Jeśli powyżej). Na przykład należy ustawić zakres od 3 do 6. Jeśli masz jedno uruchomione wystąpienie, aparat skalowania automatycznego skaluje się do trzech wystąpień w następnym uruchomieniu. Podobnie, jeśli ręcznie ustawisz skalę na osiem wystąpień, podczas następnego uruchomienia automatyczne skalowanie zostanie przeskalowane z powrotem do sześciu wystąpień.  Ręczne skalowanie jest tymczasowe, chyba że zostaną zresetowane także reguły skalowania automatycznego.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Zawsze używaj kombinacji reguł skalowania w poziomie i skalowalności, która wykonuje zwiększenie i zmniejszenie
Jeśli używasz tylko jednej części kombinacji, funkcja automatycznego skalowania podejmie akcję tylko w jednym kierunku (skalowanie w poziomie lub w) do momentu osiągnięcia maksymalnej lub minimalnej liczby wystąpień zdefiniowanej w profilu. Nie jest to optymalny sposób, co jest potrzebne do skalowania zasobów w górę w celu zapewnienia dostępności. Podobnie w przypadku niskiego użycia zasób ma być skalowany w dół, dzięki czemu możesz zrealizować oszczędności.

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Wybierz odpowiednią statystykę dla metryki diagnostycznej
W przypadku metryk diagnostycznych można wybrać wartość *średnia*, *minimum*, *maksimum* i *Suma* jako metrykę do skalowania przez. Najbardziej typowa Statystyka jest *średnia*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Ostrożnie wybieraj progi dla wszystkich typów metryk
Zalecamy staranne wybranie różnych progów skalowania w poziomie i skalowania w zależności od praktycznych sytuacji.

*Nie zalecamy* ustawień automatycznego skalowania, takich jak poniższe przykłady, z tymi samymi lub podobnymi wartościami progowymi dla warunków i w warunkach:

* Zwiększ liczbę wystąpień o 1, gdy liczba wątków >= 600
* Zmniejsz liczbę wystąpień o 1, gdy liczba wątków <= 600

Przyjrzyjmy się przykładowi, co może prowadzić do zachowania, które może wydawać się mylące. Weź pod uwagę poniższą sekwencję.

1. Załóżmy, że istnieją dwa wystąpienia, które zaczynają się od, a następnie średnia liczba wątków na wystąpienie rośnie do 625.
2. Skalowanie automatyczne w poziomie dodaje trzecie wystąpienie.
3. Następnie założono, że średnia liczba wątków w wystąpieniu spadnie do 575.
4. Przed skalowaniem w dół funkcja automatycznego skalowania próbuje oszacować stan końcowy w przypadku jego skalowania w poziomie. Na przykład 575 x 3 (bieżąca liczba wystąpień) = 1 725/2 (końcowa liczba wystąpień podczas skalowania w dół) = 862,5 wątki. Oznacza to, że automatyczne skalowanie będzie musiało natychmiast skalować w poziomie nawet po skalowaniu w, jeśli średnia liczba wątków pozostaje taka sama lub nawet zostanie wyświetlona tylko niewielka ilość. Jeśli jednak skalowanie zostanie ponowione, cały proces zostanie powtórzony, co prowadzi do nieskończonej pętli.
5. Aby uniknąć tej sytuacji ("niestabilny"), Skalowanie automatyczne nie jest skalowane w dół. Zamiast tego pomija i ponownie oblicza warunek przy następnym uruchomieniu zadania usługi. Stan niestabilny może mylić wielu osób, ponieważ automatyczne skalowanie nie będzie działało, gdy średnia liczba wątków była 575.

Szacowanie w trakcie skalowania w poziomie ma na celu uniknięcie sytuacji "niestabilny", w przypadku których akcje skalowania i skalowania w poziomie są ciągle wycofywane. Zachowanie tego zachowania należy mieć na uwadze w przypadku wybrania tych samych progów dla skalowania w poziomie i w.

Zalecamy wybranie odpowiedniego marginesu między skalowaniem w poziomie i w progach. Na przykład rozważmy następującą ulepszoną kombinację reguł.

* Zwiększ wystąpienia o 1 licznik, gdy procesor% >= 80
* Zmniejsz wystąpienia o 1 licznik, gdy procesor% <= 60

W tym przypadku  

1. Załóżmy, że istnieją 2 wystąpienia, z którymi można zacząć.
2. Jeśli średni czas procesora CPU (%) między wystąpieniami przekroczy 80, Skalowanie automatyczne jest skalowane w celu dodania trzeciego wystąpienia.
3. Teraz Załóżmy, że w czasie CPU% spadnie do 60.
4. Reguła skalowania automatycznego skalowania szacuje stan końcowy w przypadku skalowania w poziomie. Na przykład 60 x 3 (bieżąca liczba wystąpień) = 180/2 (końcowa liczba wystąpień podczas skalowania w dół) = 90. Dlatego Skalowanie automatyczne nie jest skalowane, ponieważ będzie musiało być od razu skalowane. Zamiast tego pomija skalowanie w dół.
5. W następnym czasie kontrole automatycznego skalowania procesora CPU w dalszym ciągu spadnie do 50. To polecenie szacuje się ponownie — 50 x 3 wystąpienie = 150/2 wystąpień = 75, które jest poniżej progu skalowania w poziomie wynoszącym 80, dlatego skaluje się w pomyślnie do 2 wystąpień.

> [!NOTE]
> Jeśli aparat skalowania automatycznego wykrywa niestabilny może wystąpić w wyniku skalowania do docelowej liczby wystąpień, będzie również próbować skalować do innej liczby wystąpień między bieżącą liczbą i liczbą docelową. Jeśli niestabilny nie występuje w tym zakresie, automatyczne skalowanie będzie kontynuowało operację skalowania z nowym elementem docelowym.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Zagadnienia dotyczące skalowania wartości progowych dla metryk specjalnych
 W przypadku metryk specjalnych, takich jak magazyn lub Service Bus długość kolejki, próg to średnia liczba komunikatów dostępnych na bieżącą liczbę wystąpień. Starannie wybieraj wartość progową dla tej metryki.

Ilustrujemy przykład, aby lepiej zrozumieć zachowanie.

* Zwiększ wystąpienia o 1 licznik, gdy liczba komunikatów w kolejce magazynu >= 50
* Zmniejsz wystąpienia o 1 licznik, gdy liczba komunikatów w kolejce magazynu <= 10

Rozważ poniższą sekwencję:

1. Istnieją dwa wystąpienia kolejki magazynu.
2. Komunikaty są zachowywane i podczas przeglądania kolejki magazynu łączna liczba odczytuje 50. Można założyć, że funkcja automatycznego skalowania powinna uruchamiać akcję skalowania w poziomie. Należy jednak pamiętać, że nadal 50/2 = 25 komunikatów na wystąpienie. Tak więc skalowanie w poziomie nie następuje. Aby pierwsze skalowanie w poziomie miało miejsce, Łączna liczba komunikatów w kolejce magazynu powinna wynosić 100.
3. Następnie Załóżmy, że całkowita liczba komunikatów osiągnie 100.
4. Zostało dodane trzecie wystąpienie kolejki magazynu z powodu akcji skalowania w poziomie.  Kolejna akcja skalowania w poziomie nie zostanie wykonana do momentu, aż całkowita liczba komunikatów w kolejce osiągnie 150, ponieważ 150/3 = 50.
5. Teraz liczba komunikatów w kolejce jest mniejsza. W przypadku trzech wystąpień pierwsza akcja skalowania odbywa się, gdy całkowita liczba komunikatów we wszystkich kolejkach zostanie dodana do 30, ponieważ 30/3 = 10 komunikatów na wystąpienie, czyli próg skalowania w poziomie.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Zagadnienia dotyczące skalowania w przypadku skonfigurowania wielu profilów w ustawieniu autoskalowania
W ustawieniu automatycznego skalowania można wybrać profil domyślny, który jest zawsze stosowany bez żadnej zależności od harmonogramu lub czasu, lub wybrać profil cykliczny lub profil dla stałego okresu z zakresem dat i godzin.

Gdy usługa skalowania automatycznego przetwarza je, zawsze sprawdza w następującej kolejności:

1. Profil daty ustalonej
2. Profil cykliczny
3. Domyślny profil ("zawsze")

Jeśli warunek profilu zostanie spełniony, funkcja automatycznego skalowania nie sprawdza następnego warunku profilu poniżej. Skalowanie automatyczne przetwarza tylko jeden profil w danym momencie. Oznacza to, że jeśli chcesz również uwzględnić warunek przetwarzania z poziomu profilu niższej warstwy, musisz uwzględnić te reguły również w bieżącym profilu.

Zapoznaj się z przykładem:

Na poniższej ilustracji przedstawiono ustawienie automatycznego skalowania z domyślnym profilem minimalnych wystąpień = 2 i Maksymalna liczba wystąpień = 10. W tym przykładzie reguły są skonfigurowane do skalowania w poziomie, gdy liczba komunikatów w kolejce jest większa niż 10 i skalowanie, gdy liczba komunikatów w kolejce jest mniejsza niż 3. Teraz zasób można skalować między dwoma i dziesięciu wystąpieniami.

Ponadto istnieje powtarzalny profil ustawiony dla poniedziałku. Jest ustawiany dla minimalnych wystąpień = 3 i Maksymalna liczba wystąpień = 10. Oznacza to, że w poniedziałek pierwszy raz podczas pierwszego skalowania sprawdza się w przypadku tego warunku, jeśli liczba wystąpień jest równa 2, jest skalowana do nowej wartości minimum trzy. Tak długo, jak funkcja automatycznego skalowania nadal znajdzie ten warunek profilu dopasowany (poniedziałek), tylko przetwarza oparte na PROCESORAch reguł skalowanie w poziomie i w ustawieniach dla tego profilu. W tej chwili nie sprawdza długości kolejki. Jeśli jednak chcesz również sprawdzić warunek długości kolejki, należy uwzględnić te reguły z profilu domyślnego, jak również w profilu w poniedziałek.

Podobnie, gdy automatyczne skalowanie przełącza się z powrotem do domyślnego profilu, najpierw sprawdza, czy spełnione są minimalne i maksymalne warunki. Jeśli liczba wystąpień w danym momencie wynosi 12, skaluje się do 10, maksymalną dozwoloną dla profilu domyślnego.

![ustawienia skalowania automatycznego](./media/autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Zagadnienia dotyczące skalowania w przypadku skonfigurowania wielu reguł w profilu

Istnieją przypadki, w których może być konieczne ustawienie wielu reguł w profilu. Następujące reguły automatycznego skalowania są używane przez aparat skalowania automatycznego, gdy ustawiono wiele reguł.

W przypadku *skalowania w poziomie* funkcja automatycznego skalowania jest uruchamiana, jeśli dowolna reguła zostanie spełniona.
W przypadku *skalowania w* poziomie Funkcja automatycznego skalowania wymaga spełnienia wszystkich reguł.

Aby zilustrować, założono, że masz cztery następujące reguły automatycznego skalowania:

* Jeśli procesor CPU < 30%, skalowanie w górę o 1
* Jeśli pamięć < 50%, skalowanie w poziomie o 1
* Jeśli procesor CPU > 75%, skalowanie w poziomie o 1
* Jeśli pamięć > 75%, skalowanie w poziomie o 1

Następnie następuje:

* Jeśli procesor CPU wynosi 76%, a pamięć to 50%, skalowanie w poziomie.
* Jeśli procesor CPU wynosi 50%, a pamięć to 76%, skalowanie w poziomie.

Z drugiej strony, jeśli procesor CPU wynosi 25%, a pamięć jest 51, Skalowanie automatyczne nie **jest skalowane.** Aby można było skalować w poziomie, procesor musi mieć 29% i pamięć 49%.

### <a name="always-select-a-safe-default-instance-count"></a>Zawsze wybieraj bezpieczną domyślną liczbę wystąpień
Domyślna liczba wystąpień jest ważna, ponieważ Skalowanie automatyczne skaluje usługę do tej liczby, gdy metryki są niedostępne. W związku z tym wybierz domyślną liczbę wystąpień, która jest bezpieczna dla obciążeń.

### <a name="configure-autoscale-notifications"></a>Skonfiguruj powiadomienia autoskalowania
Automatyczne skalowanie będzie ogłaszane w dzienniku aktywności w przypadku wystąpienia dowolnego z następujących warunków:

* Automatyczne skalowanie problemów z operacją skalowania.
* Usługa automatycznego skalowania pomyślnie ukończy akcję skalowania.
* Usługa automatycznego skalowania nie może wykonać akcji skalowania.
* Metryki nie są dostępne dla usługi automatycznego skalowania, aby podejmować decyzje dotyczące skalowania.
* Metryki są dostępne ponownie (Odzyskiwanie), aby przeprowadzić decyzję skalowania.
* Funkcja automatycznego skalowania wykrywa niestabilny i przerywa próbę skalowania. W tej sytuacji zobaczysz typ dziennika `Flapping` . Jeśli widzisz ten element, zastanów się, czy progi są zbyt wąskie.
* Funkcja automatycznego skalowania wykrywa niestabilny, ale nadal może skalować. W tej sytuacji zobaczysz typ dziennika `FlappingOccurred` . Jeśli zobaczysz, aparat skalowania automatycznego podjął próbę skalowania (np. z 4 wystąpień do 2), ale stwierdził, że może to spowodować niestabilny. Zamiast tego aparat skalowania automatycznego został przeskalowany do innej liczby wystąpień (np. przy użyciu 3 wystąpień zamiast 2), która nie powoduje już niestabilny, więc została przeskalowana do tej liczby wystąpień.

Możesz również użyć alertu dziennika aktywności do monitorowania kondycji aparatu skalowania automatycznego. Poniżej przedstawiono przykłady [tworzenia alertu dziennika aktywności w celu monitorowania wszystkich operacji aparatu automatycznego skalowania w ramach subskrypcji](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) lub [tworzenia alertu dziennika aktywności w celu monitorowania wszystkich niezakończonych operacji skalowania automatycznego w poziomie w ramach subskrypcji](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).

Oprócz korzystania z alertów dziennika aktywności można także skonfigurować powiadomienia e-mail lub elementy webhook w celu uzyskania powiadomień o pomyślnych akcjach skalowania za pomocą karty powiadomienia w ustawieniu Skalowanie automatyczne.

## <a name="next-steps"></a>Następne kroki
- [Utwórz alert dziennika aktywności, aby monitorować wszystkie operacje aparatu automatycznego skalowania w ramach subskrypcji.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Tworzenie alertu dziennika aktywności w celu monitorowania wszystkich niezakończonych operacji skalowania automatycznego w poziomie w ramach subskrypcji](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

