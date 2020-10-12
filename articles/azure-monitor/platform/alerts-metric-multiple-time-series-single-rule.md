---
title: Monitoruj wiele szeregów czasowych w jednej regule alertu metryki
description: Zgłoś alert w skali przy użyciu jednej reguły alertu dla wielu szeregów czasowych
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 10/04/2020
ms.subservice: alerts
ms.openlocfilehash: 81e09e6d9c6a57339f1d6f1eb5ce4f494555fa19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91704484"
---
# <a name="monitor-multiple-time-series-in-a-single-metric-alert-rule"></a>Monitoruj wiele szeregów czasowych w jednej regule alertu metryki

Pojedyncza reguła alertu metryki może służyć do monitorowania jednej lub wielu szeregów czasowych, co ułatwia monitorowanie zasobów na dużą skalę.

## <a name="metric-time-series"></a>Czas metryk — seria

Seria czasowa metryk jest serią pomiarów (lub "wartości metryk") przechwytywanych w danym okresie czasu. 

Na przykład:

- Użycie procesora CPU przez maszynę wirtualną
- Przychodzące bajty (ruch przychodzący) do konta magazynu
- Liczba nieudanych żądań aplikacji sieci Web



## <a name="alert-rule-on-a-single-time-series"></a>Reguła alertu dla pojedynczej serii czasu
Reguła alertu monitoruje pojedynczą serię czasową, gdy spełnia ona wszystkie następujące warunki:
-   Reguła monitoruje pojedynczy zasób docelowy 
-   Zawiera pojedynczy warunek
-   Oblicza metrykę bez wybierania wymiarów (przy założeniu, że Metryka obsługuje wymiary)

Przykład takiej reguły alertu (z tylko odpowiednimi właściwościami):
-   Zasób docelowy: *myVM1*
-   Metryka: *procentowy procesor CPU*
-   Operator: *większe niż*
-   Próg: *70*


Dla tej reguły alertu monitorowana jest pojedyncza seria czasowa metryk:
-   Procent użycia procesora, gdzie *Resource*= "myVM1" > 70%

![Reguła alertu dla pojedynczej serii czasu](media/alerts-metric-multiple-time-series-single-rule/simple-alert-rule.png)

## <a name="alert-rule-on-multiple-time-series"></a>Reguła alertów w wielu seriach czasowych
Reguła alertu monitoruje wiele szeregów czasowych, jeśli używa co najmniej jednej z następujących funkcji: 
-   Wiele zasobów
-   Wiele warunków 
-   Wiele wymiarów


## <a name="multiple-resources-multi-resource"></a>Wiele zasobów (wiele zasobów)

Reguła alertu o pojedynczej metryce może monitorować wiele zasobów, pod warunkiem, że te zasoby są tego samego typu i istnieją w tym samym regionie świadczenia usługi Azure. Użycie tego typu reguły zmniejsza złożoność i łączną liczbę reguł alertów, które należy zachować. 

Przykład takiej reguły alertu:
-   Zasób docelowy: *myVM1, myVM2*
-   Metryka: *procentowy procesor CPU*
-   Operator: *większe niż*
-   Próg: *70*

Dla tej reguły alertu dwie serie czasu metryk są monitorowane osobno:
-   Procent użycia procesora, gdzie *Resource*= "myVM1" > 70%
-   Procent użycia procesora, gdzie *Resource*= "myVM2" > 70%

![Reguła alertu o wiele zasobów](media/alerts-metric-multiple-time-series-single-rule/multi-resource-alert-rule.png)
 
W regule alertów o wielu zasobach warunek jest oceniany **osobno** dla każdego z zasobów (lub dokładniej, dla każdej z szeregów czasowych metryk odpowiadających poszczególnych zasobów). Oznacza to, że alerty są również uruchamiane osobno dla każdego zasobu.

Załóżmy na przykład, że ustawimy powyżej regułę alertu do monitorowania dla procesora CPU powyżej 70%. W obliczonym okresie (czyli w ciągu ostatnich 5 minut)
-   *Wartość procentowa* czasu procesora *myVM1* jest większa niż 70% 
-   *Wartość procentowa myVM2 procesora CPU* wynosi 50% *myVM2*

Reguła alertu jest wyzwalana w *myVM1*, ale nie *myVM2*. Te wyzwalane alerty są niezależne. Mogą być również rozwiązywane w różnym czasie w zależności od zachowań poszczególnych maszyn wirtualnych.

Aby uzyskać więcej informacji na temat reguł alertów dotyczących wielu zasobów i typów zasobów obsługiwanych w tej funkcji, zobacz [monitorowanie w skali przy użyciu alertów metryk w Azure monitor](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).

> [!NOTE] 
> W regule alertu dotyczącego metryki, która monitoruje wiele zasobów, dozwolony jest tylko jeden warunek.

## <a name="multiple-conditions-multi-condition"></a>Wiele warunków (wielokrotne warunki)

Reguła alertu o pojedynczej metryce może również monitorować maksymalnie pięć warunków dla każdej reguły alertu. 

Na przykład:

- Zasób docelowy: *myVM1*
- Condition1
  - Metryka: *procentowy procesor CPU*
  - Operator: *większe niż*
  - Próg: *70*
- Condition2
  - Metryka: *Sieć w sumie*
  - Operator: *większe niż*
  - Próg: *20 MB*

Dla tej reguły alertu monitorowane są dwie metryki czasowe:

- Procent użycia procesora, gdzie *Resource*= "myVM1" > 70%
- Sieć łącznie, gdzie *Resource*= "myVM1" > 20 MB

![Reguła alertu wielowarunkowego](media/alerts-metric-multiple-time-series-single-rule/multi-condition-alert-rule.png)
 
Między warunkami jest używany operator "i". Reguła alertu wyzwala alert po spełnieniu **wszystkich** warunków. Wyzwolony alert rozwiązuje, jeśli co najmniej jeden z warunków nie jest już spełniany. 

> [!NOTE]
> Istnieją ograniczenia w przypadku używania wymiarów w regule alertów z wieloma warunkami. Aby uzyskać więcej informacji, zobacz [ograniczenia w przypadku używania wymiarów w regule alertu metryki z wieloma warunkami](alerts-troubleshoot-metric.md#restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions).


## <a name="multiple-dimensions-multi-dimension"></a>Wiele wymiarów (wiele wymiarów)

Pojedyncza reguła alertu metryki może również monitorować wiele wartości wymiarów metryki. Wymiary metryki to pary nazwa-wartość, które zawierają dodatkowe dane do opisywania wartości metryki. Na przykład Metryka **transakcji** konta magazynu ma wymiar o nazwie **API Name**, opisujący nazwę interfejsu API wywoływanego przez poszczególne transakcje (na przykład GetBlob, DeleteBlob, PutPage). Użycie wymiarów jest opcjonalne, ale umożliwia filtrowanie metryk i tylko monitorowanie określonej serii czasu, zamiast monitorować metrykę jako zagregowaną wszystkie wartości wymiarowe. 

Można na przykład wybrać opcję uruchamiania alertu, gdy liczba transakcji jest duża dla wszystkich nazw interfejsów API (czyli zagregowanych danych), lub później podzielić na alerty, gdy liczba transakcji jest wysoka dla określonych nazw interfejsów API. 

Przykładem reguły alertu monitorującej wiele wymiarów jest:

- Zasób docelowy: *myStorage1*
- Metryka: *transakcje*
- Wymiary
  * Nazwa interfejsu API = *GetBlob, DeleteBlob, PutPage*
- Operator: *większe niż*
- Próg: *70*

Dla tej reguły alertu są monitorowane trzy szeregi czasowe metryk:

- Transakcje, w których *zasób*= "myStorage1" i *nazwa interfejsu API*= "getblob" > 70
- Transakcje, *w których myStorage1*' i *API Name*= ' DeleteBlob ' > 70
- Transakcje, *w których myStorage1*' i *API Name*= ' PutPage ' > 70

![Reguła alertu wielowymiarowego z wartościami z jednego wymiaru](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-1.png)

Reguła alertu metryki z wieloma wymiarami może również monitorować wiele wartości wymiarów z **różnych** wymiarów metryki. W takim przypadku reguła alertów **osobno** monitoruje wszystkie kombinacje wartości wymiarów wybranych wartości wymiarów.

Przykład tego typu reguły alertu:

- Zasób docelowy: *myStorage1*
- Metryka: *transakcje*
- Wymiary
  * Nazwa interfejsu API = *GetBlob, DeleteBlob, PutPage*
  * Uwierzytelnianie = *SAS, AccountKey*
- Operator: *większe niż*
- Próg: *70*

Dla tej reguły alertu sześć serii czasowych jest monitorowanych osobno:

- Transakcje, w których *Resource*= "myStorage1" i *API Name*= "GetBlob" i *Authentication*= "SAS" > 70
- Transakcje, w których *zasób*= "myStorage1" i *nazwa interfejsu API*= "GetBlob" i *Authentication*= "AccountKey" > 70
- Transakcje, w których *Resource*= "myStorage1" i *API Name*= "DeleteBlob" i *Authentication*= "SAS" > 70
- Transakcje, w których *Resource*= "myStorage1" i *API Name*= "DeleteBlob" i *Authentication*= "AccountKey" > 70
- Transakcje, w których *Resource*= "myStorage1" i *API Name*= "PutPage" i *Authentication*= "SAS" > 70
- Transakcje, w których *Resource*= "myStorage1" i *API Name*= "PutPage" i *Authentication*= "AccountKey" > 70

![Reguła alertu wielowymiarowego z wartościami z wielu wymiarów](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-2.png)
 
### <a name="advanced-multi-dimension-features"></a>Zaawansowane funkcje wielowymiarowe

1.  **Wybierając wszystkie bieżące i przyszłe wymiary** — możesz monitorować wszystkie możliwe wartości wymiaru, w tym przyszłe wartości. Taka reguła alertów zostanie automatycznie przeskalowana w celu monitorowania wszystkich wartości wymiaru, bez konieczności modyfikowania reguły alertu przy każdym dodawaniu lub usunięciu wartości wymiaru.
2.  **Wykluczanie wymiarów** — wybranie operatora "≠" (Wyklucz) dla wartości wymiaru jest równoznaczne z wybraniem wszystkich innych wartości tego wymiaru, w tym przyszłych wartości.
3.  **Nowe i niestandardowe wymiary** — wartości wymiarów wyświetlane w Azure Portal są oparte na danych metryk zebranych w ciągu ostatnich trzech dni. Jeśli wartość wymiaru, którego szukasz, nie jest jeszcze emitowana, można dodać niestandardową wartość wymiaru.

![Zaawansowane funkcje wielowymiarowe](media/alerts-metric-multiple-time-series-single-rule/advanced-features.png)


## <a name="metric-alerts-pricing"></a>Cennik alertów dotyczących metryk

Cennik reguł alertów dotyczących metryk są dostępne na [stronie cennik Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

Podczas tworzenia reguły alertu dotyczącego metryki podane oszacowanie cen bazuje na wybranych funkcjach i liczbie monitorowanych szeregów czasowych, co jest określane na podstawie konfiguracji reguły i bieżących wartości metryk. Miesięczna opłata jest naliczana w oparciu o rzeczywiste oceny szeregów czasowych, a w związku z tym różni się od oryginalnego oszacowania, jeśli niektóre serie czasowe nie mają danych do oceny lub jeśli reguła alertu używa funkcji, które mogą skalować ją dynamicznie.

Na przykład reguła alertu może przedstawiać wysoki poziom oszacowania cen, jeśli wykorzystuje funkcję wielowymiarową, a wybrano dużą liczbę kombinacji wartości wymiarów, co prowadzi do monitorowania wielu szeregów czasowych. Jednak rzeczywista opłata dla tej reguły alertu może być niższa, jeśli nie wszystkie serie czasowe, które wynikają z kombinacji wartości wymiarów, faktycznie mają dane do obliczenia.

## <a name="number-of-time-series-monitored-by-a-single-alert-rule"></a>Liczba szeregów czasowych monitorowanych przez pojedynczą regułę alertu

Aby uniknąć nadmiernych kosztów, Każda reguła alertów może domyślnie monitorować do 5000 serii czasowych. Aby podnieść ten limit z subskrypcji, Otwórz bilet pomocy technicznej.


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o monitorowaniu na dużą skalę przy użyciu alertów metryk i [dynamicznych progów](alerts-dynamic-thresholds.md).
