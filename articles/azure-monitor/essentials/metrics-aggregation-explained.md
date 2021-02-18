---
title: Azure Monitor agregowania metryk metryk i wyświetlenia
description: Szczegółowe informacje na temat sposobu agregowania metryk w Azure Monitor
author: rboucher
ms.author: robb
services: azure-monitor
ms.topic: conceptual
ms.date: 01/12/2020
ms.subservice: metrics
ms.openlocfilehash: c47c316c82d8c510b4446e4335a219f400d476e4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100615230"
---
# <a name="azure-monitor-metrics-metrics-aggregation-and-display-explained"></a>Azure Monitor agregowania metryk metryk i wyświetlenia

W tym artykule opisano agregację metryk w Azure Monitor bazie danych szeregów czasowych, która odAzure Monitor [metryki platformy](../data-platform.md) i [metryki niestandardowe](../essentials/metrics-custom-overview.md). Ten artykuł dotyczy również metryk standardowych [Application Insights](../app/app-insights-overview.md). 

Jest to złożony temat i nie jest konieczny, aby zrozumieć wszystkie informacje przedstawione w tym artykule, aby skutecznie używać metryk Azure Monitor.

## <a name="overview-and-terms"></a>Przegląd i postanowienia

Po dodaniu metryki do wykresu Eksplorator metryk automatycznie wybiera swoją domyślną agregację. Ustawienie domyślne jest zrozumiałe w scenariuszach podstawowych, ale można użyć różnych agregacji, aby uzyskać więcej szczegółowych informacji o metryki. Wyświetlanie różnych agregacji na wykresie wymaga zrozumienia sposobu, w jaki Eksplorator metryk je obsługuje.

Najpierw zdefiniujemy kilka warunków:

- **Wartość metryki** — pojedyncza wartość miary zebrana dla określonego zasobu.
- **Okres** — czas ogólny.
- **Przedział czasu** — okres między zbieraniem dwóch wartości metryk. 
- **Zakres czasu** — okres wyświetlany na wykresie. Typowa wartość domyślna to 24 godziny. Dostępne są tylko określone zakresy. 
- **Stopień szczegółowości czasu** lub **ziarno** czasu — okres używany do agregowania wartości w celu umożliwienia wyświetlania na wykresie. Dostępne są tylko określone zakresy. Bieżąca wartość minimalna to 1 minuta. Wartość stopnia szczegółowości czasu powinna być mniejsza niż wybrany zakres czasu, który ma być użyteczny. w przeciwnym wypadku dla całego wykresu jest pokazywana tylko jedna wartość. 
- **Typ agregacji** — typ statystyki obliczanej na podstawie wielu wartości metryk.  
- **Agreguj** — proces pobierania wielu wartości wejściowych, a następnie używania ich do generowania pojedynczej wartości wyjściowej za pomocą reguł zdefiniowanych przez typ agregacji. Na przykład pobieranie średniej z wielu wartości.  

Metryki są serią wartości metryk przechwytywane w regularnych odstępach czasu. Podczas wykreślania wykresu wartości wybranej metryki są agregowane na poziomie szczegółowości czasu (nazywanego również ziarnem czasu). Możesz wybrać rozmiar stopnia szczegółowości, korzystając z [panelu wyboru Eksplorator metrykego czasu](../essentials/metrics-getting-started.md#select-a-time-range). Jeśli nie utworzysz jawnie zaznaczenia, stopień szczegółowości czasu zostanie automatycznie wybrany na podstawie aktualnie wybranego zakresu czasu. Po wybraniu wartości metryk, które zostały przechwycone podczas każdego interwału szczegółowości, są agregowane i umieszczane na wykresie — jeden punkt danych na interwał.

## <a name="aggregation-types"></a>Typy agregacji 

W Eksploratorze metryk dostępne są pięć podstawowych typów agregacji. Eksplorator metryk ukrywa agregacje, które są istotne i nie mogą być używane dla danej metryki. 

- **Sum** — suma wszystkich wartości przechwytywanych przez interwał agregacji. Czasami nazywane agregacją łączną.
- **Count** — liczba pomiarów przechwyconych w interwale agregacji. Licznik nie sprawdza wartości miary, tylko liczby rekordów. 
- **Average** — średnia wartości metryk przechwyconych w interwale agregacji. W przypadku większości metryk ta wartość jest sumą/Count. 
- **Min** — najmniejsza wartość przechwycona przez interwał agregacji.
- **Max** — największa wartość przechwycona za pośrednictwem interwału agregacji.

Załóżmy na przykład, że wykres pokazuje całkowitą metrykę **sieci** dla maszyny wirtualnej korzystającą z agregacji **sum** w ciągu ostatnich 24 godzin. Zakres czasu i stopień szczegółowości można zmienić w prawym górnym rogu wykresu, jak pokazano na poniższym zrzucie ekranu.

:::image type="content" source="media/metrics-aggregation-explained/time-range-granularity-picker.png" alt-text="Zrzut ekranu przedstawiający wybór zakresu czasu i stopnia szczegółowości czasu" border="true":::

W przypadku stopnia szczegółowości czasu = 30 minut i zakresu czasu = 24 godziny:

- Wykres jest rysowany z 48 punktów kontrolnych. To jest 24-godzinny x 2 punkty kontrolne na godzinę (60 minut/fragmentach) zagregowane punkty DataPoint w liczbie 1-minutowy. 
- Wykres liniowy łączy 48 kropek w obszarze wykresu kreślenia. 
- Każdy punkt danych reprezentuje sumę wszystkich bajtów wychodzących z sieci wysyłanych podczas każdego z odpowiednich okresów 30-minutowych. 


 :::image type="content" source="media/metrics-aggregation-explained/24-hour-30-min-gran.png" alt-text="Zrzut ekranu przedstawiający dane na wykresie liniowym z ustawionym 24-godzinnym zakresem czasu i okresem szczegółowości 30 minut" border="true" lightbox="media/metrics-aggregation-explained/24-hour-30-min-gran.png":::

*Kliknij obrazy w tej sekcji, aby zobaczyć większe wersje.*

Po przełączeniu stopnia szczegółowości czasu na 15 minut wykres jest rysowany z 96 zagregowanych punktów danych. Oznacza to, że 60 minut/15 min = 4 punkty kontrolne na godzinę x 24 godziny.

:::image type="content" source="media/metrics-aggregation-explained/24-hour-15-min-gran.png" alt-text="Zrzut ekranu przedstawiający dane na wykresie liniowym z ustawionym 24-godzinnym zakresem czasu i dokładnością do 15 minut." border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

W przypadku okresu szczegółowości 5 minut uzyskasz 24 x (60/5) = 288 punktów. 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-5-min-gran.png" alt-text="Zrzut ekranu przedstawiający dane na wykresie liniowym ustawionym na 24-godzinny zakres czasu i okres szczegółowości 5 minut" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

W przypadku stopnia szczegółowości czasu 1 minuty (najmniejszy możliwy na wykresie) uzyskasz 24 x 60/1 = 1440 punktów. 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-1-min-gran.png" alt-text="Zrzut ekranu przedstawiający dane na wykresie liniowym z ustawionym 24-godzinnym zakresem czasu i dokładnością 1 minuty" border="true" lightbox="media/metrics-aggregation-explained/24-hour-1-min-gran.png":::

Wykresy wyglądają inaczej, jak pokazano na poprzednich zrzutach ekranu.  Zwróć uwagę, jak ta maszyna wirtualna ma dużo danych wyjściowych w małym okresie względem reszty przedziału czasu.  

Stopień szczegółowości czasu umożliwia dostosowanie współczynnika "sygnały do szumu" na wykresie. Wyższe agregacje usuwają szum i wygładzają skoki.  Zwróć uwagę na różnice w dolnym wykresie 1-minutowym i jak są one wygładzone po przejściu do wyższych wartości stopnia szczegółowości. 

To zachowanie płynne jest ważne w przypadku wysyłania tych danych do innych systemów — na przykład alertów. Zazwyczaj zazwyczaj nie chcesz otrzymywać alertów przez bardzo krótkie maksymalne wartości czasu procesora CPU niż 90%. Ale jeśli procesor CPU pozostaje na 90% przez 5 minut, jest to najważniejsza. Jeśli skonfigurujesz regułę alertu dla procesora CPU (lub dowolnej metryki), zwiększenie szczegółowości czasu może zmniejszyć liczbę otrzymywanych fałszywych alertów. 

Ważne jest, aby określić, co jest "normalne" dla obciążenia, aby dowiedzieć się, co jest najlepszy interwał czasu. Jest to jedna z korzyści związanych z [alertami dynamicznymi](../alerts/alerts-dynamic-thresholds.md), które nie są tutaj omówione.  

## <a name="how-the-system-collects-metrics"></a>Jak system zbiera metryki

Zbieranie danych zależy od metryki. Istnieją dwa typy okresów zbierania danych.

### <a name="measurement-collection-frequency"></a>Częstotliwość zbierania pomiarów 

- **Regularna** — Metryka jest zbierana w spójnym przedziale czasu, który nie jest różny.

- **Oparta na aktywności** — Metryka jest zbierana na podstawie czasu wystąpienia transakcji określonego typu. Każda transakcja ma wpis metryki i sygnaturę czasową. Nie są zbierane w regularnych odstępach czasu, dlatego w danym okresie istnieje różne liczby rekordów. 

### <a name="granularity"></a>Poziom szczegółowości

Minimalny przedział czasu to 1 minuta, ale system bazowy może przechwytywać dane szybciej w zależności od metryki. Na przykład procent procesora CPU jest śledzony co 15 sekund w regularnych odstępach czasu. Ponieważ błędy HTTP są śledzone jako transakcje, można je łatwo przekroczyć o wiele więcej niż jedną minutę. Inne metryki, takie jak magazyn SQL, są przechwytywane co 20 minut. Ten wybór dotyczy indywidualnego dostawcy zasobów i typu. Większość prób zapewnienia możliwie najmniejszego interwału.

### <a name="dimensions-splitting-and-filtering"></a>Wymiary, dzielenie i filtrowanie

Metryki są przechwytywane dla każdego pojedynczego zasobu. Jednak poziom, na którym metryki są zbierane, przechowywane i mogą być niezależne. Ten poziom jest reprezentowany przez dodatkowe metryki dostępne w **wymiarach metryk**. Każdy indywidualny dostawca zasobów umożliwia zdefiniowanie szczegółowości zbieranych danych. Azure Monitor definiuje tylko, jak te szczegółowe informacje powinny być prezentowane i przechowywane. 

W przypadku grafu metryki w Eksploratorze metryk istnieje możliwość "dzielenia" wykresu przez wymiar.  Dzielenie wykresu oznacza, że przeglądasz dane bazowe, aby uzyskać bardziej szczegółowe informacje i zobaczyć, że dane zostały przełączone lub przefiltrowane w Eksploratorze metryk.

Na przykład, [Microsoft. ApiManagement/Service](../platform/metrics-supported.md#microsoftapimanagementservice) ma *lokalizację* jako wymiar dla wielu metryk. 

- **Wydajność** to taka Metryka. Wymiar *lokalizacji* oznacza, że podstawowy system przechowuje rekord metryki dla pojemności poszczególnych lokalizacji, a nie tylko jeden dla zagregowanej wartości. Następnie można pobrać lub podzielić te informacje na wykresie pomiarowym.  

- Sprawdzając **łączny czas trwania żądań bramy**, należy ponownie uzyskać 2 *lokalizacje* i *nazwę hosta*, informując o lokalizacji czasu trwania oraz nazwę hosta, z którego pochodzi.  

- Jednym z bardziej elastycznych metryk, **żądania** ma 7 różnych wymiarów. 
 
Zapoznaj się z artykułem dotyczącym [metryk](../platform/metrics-supported.md) Azure monitor, aby uzyskać szczegółowe informacje o każdej metryce i dostępnych wymiarach. Ponadto Dokumentacja dla każdego dostawcy zasobów i typu może dostarczyć dodatkowych informacji na temat wymiarów i ich miary.

Do Dig problemu można używać dzielenia i filtrowania. Poniżej znajduje się przykład grafiki przedstawiający *średnią liczbę bajtów zapisu dysku* dla grupy maszyn wirtualnych w grupie zasobów. Mamy pakiet zbiorczy wszystkich maszyn wirtualnych z tą metryką, ale możemy chcieć DIG do sprawdzenia, które są rzeczywiście odpowiedzialne za szczyty wokół 6:00u. Czy są to te same maszyny? Ile maszyn jest używanych?  

:::image type="content" source="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png" alt-text="Zrzut ekranu przedstawiający łączną liczbę bajtów zapisu dysków dla wszystkich maszyn wirtualnych w grupie zasobów usługi contoso hoteli" border="true" lightbox="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png":::

*Kliknij obrazy w tej sekcji, aby zobaczyć większe wersje.*

Po zastosowaniu podziału można zobaczyć dane bazowe, ale jest to nieco trochę. Spowoduje to wyszukanie 20 maszyn wirtualnych agregowanych do wykresu powyżej. W tym przypadku korzystamy z myszy, aby umieścić kursor nad dużym szczytem w 6:00, który informuje nas o tym, że jest to przyczyna. jednak trudno jest zobaczyć resztę danych skojarzonych z tą maszyną wirtualną z powodu innych maszyn wirtualnych, które zasłaniają wykres. 

:::image type="content" source="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png" alt-text="Zrzut ekranu przedstawiający Bajty zapisu dysku dla wszystkich maszyn wirtualnych w grupie zasobów contoso hoteli podzielone według nazwy maszyny wirtualnej" border="true" lightbox="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png":::

Użycie funkcji filtrowania pozwala wyczyścić wykres, aby zobaczyć, co naprawdę się dzieje. Możesz zaznaczyć lub wyczyścić maszyny wirtualne, które mają być widoczne. Zwróć uwagę na linie kropkowane. Są one wymienione w dalszej części.

:::image type="content" source="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png" alt-text="Zrzut ekranu przedstawiający Bajty zapisu dysku dla wszystkich maszyn wirtualnych w hotelach contoso grupy zasobów podzielone i filtrowane według nazwy maszyny wirtualnej" border="true" lightbox="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png":::

Aby uzyskać więcej informacji na temat sposobu wyświetlania danych z podziałem wymiarów na wykresie Eksploratora metryk, zobacz [Zaawansowane funkcje Eksploratora metryk — filtry i dzielenie](../essentials/metrics-charts.md#filters).

### <a name="null-and-zero-values"></a>Wartości NULL i zero

Gdy system oczekuje danych metryk z zasobu, ale go nie otrzymuje, rejestruje wartość NULL.  Wartość NULL jest różna od wartości zerowej, która jest istotna dla obliczeń agregacji i wykresów. Wartości NULL nie są zliczane jako prawidłowe pomiary. 

Wartości NULL są wyświetlane inaczej dla różnych wykresów. Wykresy punktowe pomijają pokazywanie kropki na wykresie. Wykresy słupkowe pomijają wyświetlanie paska. Na wykresach liniowych wartość NULL może być wyświetlana jako [Linia kropkowana lub kreskowana](../essentials/metrics-troubleshoot.md#chart-shows-dashed-line) , jak pokazano na zrzucie ekranu w poprzedniej sekcji. Podczas obliczania średniej, które zawierają wartości NULL, jest mniej punktów danych, z których ma zostać obliczona średnia.  Takie zachowanie może czasami spowodować nieoczekiwane porzucenie wartości na wykresie, ale zazwyczaj mniejsze niż Jeśli wartość została przekonwertowana na zero i użyta jako prawidłowy punkt danych.  

[Metryki niestandardowe](../essentials/metrics-custom-overview.md) zawsze używają wartości null, gdy nie są odbierane żadne dane. W przypadku [metryk platformy](../data-platform.md)każdy dostawca zasobów decyduje, czy należy używać zer lub wartości null w oparciu o to, co jest największe dla danej metryki.

Alerty Azure Monitor używają wartości, które dostawca zasobów zapisuje do bazy danych metryk, dlatego ważne jest, aby wiedzieć, jak dostawca zasobów obsługuje wartości NULL, wyświetlając najpierw dane.

## <a name="how-aggregation-works"></a>Jak działa agregacja

Wykresy metryk w poprzednim systemie przedstawiają różne typy zagregowanych danych. System wstępnie agreguje dane, dzięki czemu żądane wykresy mogą być wyświetlane szybciej bez wielu powtarzających się obliczeń.  

W tym przykładzie:

- Zbieramy **fikcyjną** metrykę transakcyjną o nazwie **Błędy HTTP** 
- *Serwer* jest wymiarem dla metryki **błędów HTTP** .
- Mamy 3 serwery — Serwer A, B i C.

Aby uprościć wyjaśnienie, zaczynamy tylko od typu agregacji SUM. 

### <a name="sub-minute-to-1-minute-aggregation"></a>Agregacja od minuty do 1 minuty

Pierwsze nieprzetworzone dane metryk są zbierane i przechowywane w bazie danych metryk Azure Monitor. W takim przypadku każdy serwer zawiera rekordy transakcji przechowywane z sygnaturą czasową, ponieważ *serwer* jest wymiarem. Uwzględniając, że najmniejszy okres, który można wyświetlić jako klient, to 1 minuta, te sygnatury czasowe są najpierw agregowane do wartości 1-minutowej metryki dla każdego serwera.  Na poniższej ilustracji przedstawiono proces agregacji dla serwera B. Serwery A i C są wykonywane w taki sam sposób i mają inne dane.  

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction.png" alt-text="Zrzut ekranu przedstawiający jednominutowe wpisy transakcyjne w agregacjach 1-minutowych. " border="false":::

Wynikowe wartości zagregowane 1-minutowe są przechowywane jako nowe wpisy w bazie danych metryk, dzięki czemu można je zbierać do późniejszych obliczeń. 

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction-dimension-aggregated.png" alt-text="Zrzut ekranu przedstawiający wiele wpisów zagregowanych 1-minutowych w wymiarze serwera. Serwer A, B i C są wyświetlane indywidualnie" border="false":::

### <a name="dimension-aggregation"></a>Agregacja wymiarów

Obliczenia 1-minutowe są następnie zwijane według wymiarów i ponownie przechowywane jako pojedyncze rekordy.   W takim przypadku wszystkie dane ze wszystkich serwerów indywidualnych są agregowane do metryki interwału 1-minutowego i przechowywane w bazie danych metryk do użycia w późniejszych agregacjach.

:::image type="content" source="media/metrics-aggregation-explained/1-minute-transaction-dimension-flattened-aggregated.png" alt-text="Zrzut ekranu przedstawiający wiele 1-minutowych zagregowanych wpisów serwera A, B i C zagregowanych na 1-minutowe wszystkie serwery" border="false":::

Dla jasności Poniższa tabela przedstawia metodę agregacji.

| Okres   | Serwer A | Serwer B | Serwer C | Suma (A + B + C)|
| -------- | -------- | -------- | -------- | --------   |  
| Minuta 1 | 1        | 1        | 1        | 3          |
| Minuta 2 | 0        | 5        | 1        | 6          |
| Minuta 3 | 0        | 5        | 1        | 6          |
| Minuta 4 | 2        | 3        | 4        | 9          |
| Minuta 5 | 1        | 0        | 3        | 4          |
| Minuta 6 | 1        | 0        | 4        | 5          |
| Minuta 7 | 1        | 2        | 4        | 7          |
| Minutę 8 | 0        | 1        | 0        | 1          |
| Minutę 9 | 1        | 1        | 4        | 6          |
| Minutę 10| 2        | 1        | 0        | 3          |

Powyżej podano tylko jeden wymiar, ale ten sam proces agregacji i magazynowania jest realizowany dla **wszystkich wymiarów** obsługiwanych przez metrykę.

- Zbierz wartości do 1-minutowej agregacji ustawionej przez ten wymiar. Zapisz te wartości. 
- Zwiń wymiar do 1-minutowej sumy zagregowanej. Zapisz te wartości. 

Wprowadźmy kolejny wymiar błędów HTTP o nazwie karty sieciowej. Załóżmy, że mamy różną liczbę kart na serwer.

- Serwer A ma 1 kartę
- Serwer B ma 2 karty
- Serwer C ma 3 karty

Dane dla następujących transakcji są zbierane osobno. Zostaną one oznaczone:

- Czas
- Wartość
- Serwer, z którego pochodzi transakcja
- Karta, z której pochodzi transakcja

Każdy z tych strumieni podminut będzie następnie agregowany do 1-minutowych wartości szeregów czasowych i przechowywany w bazie danych metryk Azure Monitor:

- Serwer A, adapter 1
- Serwer B, adapter 1
- Serwer B, adapter 2
- Serwer C, adapter 1
- Serwer C, adapter 2
- Serwer C, adapter 3

Ponadto przechowywane są również następujące zwinięte agregacje:

- Serwer A, adapter 1 (ponieważ nie ma niczego do zwinięcia, zostanie on zapisany ponownie)
- Serwer B, karta 1 + 2
- Serwer C, karta 1 + 2 + 3
- WSZYSTKIE serwery, wszystkie karty

Pokazuje to, że metryki o dużej liczbie wymiarów mają większą liczbę agregacji. Nie jest ważne, aby znać wszystkie permutacje i zrozumieć powód. System chce mieć zarówno dane osobowe, jak i zagregowane dane przechowywane do szybkiego pobrania w celu uzyskania dostępu na dowolnym wykresie. System wybiera najbardziej odpowiednie przechowywane agregacje lub podstawowe dane pierwotne w zależności od tego, co ma być wyświetlane. 

### <a name="aggregation-with-no-dimensions"></a>Agregacja bez wymiarów

Ponieważ ta Metryka ma *serwer* wymiarów, można uzyskać dane bazowe dla serwera a, B i C powyżej za pomocą dzielenia i filtrowania, jak wyjaśniono wcześniej w tym artykule. Jeśli Metryka nie ma *serwera* jako wymiaru, klient może uzyskać dostęp tylko do zagregowanych sum 1-minutowych widocznych w czerni na diagramie. Oznacza to, że wartości 3, 6, 6, 9 itd. System nie może również wykonywać bazowej pracy agregowania wartości, które nigdy nie będą używane w Eksploratorze metryk ani wysyłać do nich za pośrednictwem interfejsu API REST metryk. 

## <a name="viewing-time-granularities-above-1-minute"></a>Wyświetlanie stopnia szczegółowości czasu powyżej 1 minuty

W przypadku podawania metryk na większym poziomie szczegółowości system używa wartości zagregowanych 1-minutowych w celu obliczenia sum dla większego stopnia szczegółowości.  Poniżej, kropkowane linie pokazują metodę podsumowania dla okresów szczegółowości 2-minutowy i 5 minut. Ponownie wyświetlamy tylko typ agregacji SUM dla uproszczenia.

:::image type="content" source="media/metrics-aggregation-explained/1-minute-to-2-min-5-min.png" alt-text="Zrzut ekranu przedstawiający wiele wpisów zagregowanych 1-minutowych między wymiarami serwera zagregowanymi w okresach 2-min i 5-minutowych." border="false":::

Na 2-minutowy stopień szczegółowości czasu.

| Okres       | Przeznaczony       
| -------------|-------------|  
| Minuta 1 & 2 | (3 + 6) = 9 |
| Minuta 3 & 4 | (6 + 9) = 15|
| Minuta 4 & 5 | (4 + 5) = 9 |
| Minuta 6 & 7 | (7 + 1) = 8 |
| Minuta 8 & 9 | (6 + 3) = 9 |

Dla 5-minutowego stopnia szczegółowości czasu.

| Okres              |            Przeznaczony        |
|---------------------|------------------------|  
| Minuta od 1 do 5  | 3 + 6 + 6 + 9 + 4 = 28 |
| Minuta od 6 do 10 | 5 + 7 + 1 + 6 + 3 = 22 |

System używa przechowywanych zagregowanych danych, które zapewniają najlepszą wydajność. 

Poniżej znajduje się większy diagram dla powyższego 1-minutowego procesu agregacji z niektórymi strzałkami, aby poprawić czytelność.

:::image type="content" source="media/metrics-aggregation-explained/sum-aggregation-full.png" alt-text="Zrzut ekranu przedstawiający konsolidację poprzednich 3 zrzutów ekranu. Wiele wpisów zagregowanych 1-minutowych między wymiarami serwera zagregowanymi w 1-minutowym, 2-minutowym i 5-minutowym interwałie. Serwer A, B i C są wyświetlane indywidualnie" border="false":::

## <a name="more-complex-example"></a>Bardziej złożony przykład

Poniżej przedstawiono większy przykład użycia wartości dla fikcyjnej metryki o nazwie czas odpowiedzi HTTP w milisekundach.  Tutaj wprowadzamy dodatkowe poziomy złożoności.

1. Pokazujemy agregację wartości sum, Count, min i Max oraz obliczeń średniej.
2. Wyświetlamy wartości NULL i wpływ na obliczenia. 

Rozważmy następujący przykład. Pola i strzałki pokazują przykłady sposobu agregowania i obliczania wartości. 

Ten sam jednominutowy proces agregacji, zgodnie z opisem w poprzedniej sekcji, występuje w przypadku sum, Count, minimum i maksimum.  Jednak średnia nie jest wstępnie agregowana. Jest on ponownie obliczany przy użyciu zagregowanych danych, aby uniknąć błędów obliczeń.
 
:::image type="content" source="media/metrics-aggregation-explained/full-aggregation-example-all-types.png" alt-text="Zrzut ekranu przedstawiający złożone przykłady agregacji i obliczeń sum, Count, min, Max i Average z 1 minuty do 10 minut." border="false" lightbox="media/metrics-aggregation-explained/full-aggregation-example-all-types.png":::

Rozważmy minutę 6 dla agregacji 1 minuty, jak zostało to wyróżnione powyżej. Ta minuta wskazuje, że serwer B przeszedł do trybu offline i zatrzymał dane raportowania, prawdopodobnie z powodu ponownego uruchomienia. 

Od minuty 6 powyżej obliczone typy agregacji 1-minutowe są następujące: 

| Typ agregacji | Wartość        | Uwagi |
|------------------|--------------|-------|
| Sum              | 53 + 20 = 73 | |
| Liczba            | 2            | Pokazuje wpływ wartości NULL.  Wartość byłaby równa 3, jeśli serwer był w trybie online.  |
| Minimum          | 20           | |
| Maksimum          | 53           | |
| Średnia          | 73/2       | Zawsze suma dzielona przez licznik. Nigdy nie są przechowywane i zawsze ponownie obliczane dla każdego stopnia szczegółowości czasu przy użyciu zagregowanych liczb dla tego stopnia szczegółowości. Zwróć uwagę na ponowne obliczenie dla 5-minutowych i 10-minutowych szczegółowości, jak zostało to wyróżnione powyżej. |

Czerwony kolor tekstu wskazuje wartości, które mogą być uznawane za normalne od normalnego zakresu i pokazuje, jak są one propagowane (lub niepowodzenie) jako stopień szczegółowości. Zwróć uwagę na to, jak *minimalna* i *Maksymalna wartość* wskazuje, że istnieją podstawowe anomalie, a *średnia* i *kwoty* tracą te informacje w miarę osiągnięcia szczegółowości czasu.  

Można także sprawdzić, czy wartości NULL zapewniają lepsze obliczenie średniej niż Jeśli zamiast nich użyto zer.  

> [!NOTE] 
> Chociaż w tym przykładzie nie jest to przypadek, *Liczba* jest równa *sum* w przypadkach, gdy Metryka jest zawsze przechwytywana z wartością 1. Jest to typowe, gdy Metryka śledzi wystąpienie zdarzenia transakcyjnego — na przykład liczba błędów HTTP wymienionych w poprzednim przykładzie w tym artykule.

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do Eksploratora metryk](../essentials/metrics-getting-started.md)
- [Zaawansowany Eksplorator metryk](../essentials/metrics-charts.md)
