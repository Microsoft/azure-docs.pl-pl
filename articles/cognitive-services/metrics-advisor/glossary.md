---
title: Słownik doradcy metryk
titleSuffix: Azure Cognitive Services
description: Najważniejsze pomysły i koncepcje dotyczące usługi Advisor metryk
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 6c461983053a145dfda58b9e3d26b39db0c339e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92893424"
---
# <a name="metrics-advisor-glossary-of-common-vocabulary-and-concepts"></a>Słownik doradcy metryk wspólnego słownictwa i koncepcji

W tym dokumencie wyjaśniono warunki techniczne używane w usłudze Advisor Metrics. Skorzystaj z tego artykułu, aby dowiedzieć się więcej na temat typowych pojęć i obiektów, które mogą wystąpić podczas korzystania z usługi.

## <a name="data-feed"></a>Strumieniowe źródło danych

> [!NOTE]
> Wiele metryk może współdzielić to samo źródło danych, a nawet to samo strumieniowe źródło danych.

Strumieniowe źródło danych to to, co Doradca metryk pozyskiwanie ze źródła danych, na przykład Cosmos DB lub programu SQL Server. Strumieniowe źródło danych zawiera następujące wiersze:
* sygnatury czasowe
* zero lub więcej wymiarów
* co najmniej jedna miara. 

## <a name="metric"></a>Metric

Metryka to miara wymierna, która jest używana do monitorowania i oceniania stanu określonego procesu biznesowego. Może to być kombinacja wielu wartości szeregów czasowych podzielona na wymiary. Na przykład Metryka *kondycji sieci Web* może zawierać wymiary *liczby użytkowników* i *rynku en-us*.

## <a name="dimension"></a>Wymiar

Wymiar to jedna lub więcej wartości kategorii. Kombinacja tych wartości identyfikuje konkretną univariate szeregów czasowych, na przykład: Country, language, dzierżawca i tak dalej.

## <a name="multi-dimensional-metric"></a>Metryka wielowymiarowa

Co to jest Metryka wielowymiarowa? Użyjmy dwóch przykładów. 

**Przychód biznesowy**

Załóżmy, że masz dane dotyczące przychodów firmy. Dane szeregów czasowych mogą wyglądać następująco:

| Znacznik czasu | Kategoria | Do | Przychód |
| ----------|----------|--------|----- |
| 2020-6-1 | Żywności | USA | 1000 |
| 2020-6-1 | Ubrania | USA | 2000 |
| 2020-6-2 | Żywności | Zjednoczone Królestwo | 800 | 
| ...      | ...  |... | ... |

W tym przykładzie *Kategoria* i *rynek* są wymiarami. *Przychód* to kluczowy wskaźnik wydajności (KPI), który można podzielić na różne kategorie i/lub rynki, a także może być zagregowany. Na przykład przychód *żywności* dla wszystkich rynków.
 
**Liczba błędów dla złożonej aplikacji**

Załóżmy, że masz dane dotyczące liczby błędów zalogowanych w aplikacji. Dane szeregów czasowych mogą wyglądać następująco:

| Znacznik czasu | Składnik aplikacji | Region (Region) | Liczba błędów |
| ----------|----------|--------|----- |
| 2020-6-1 | Baza danych pracowników | ZACHODNIE UE | 9000 |
| 2020-6-1 | Kolejka komunikatów | WSCHODNIE STANY USA | 1000 |
| 2020-6-2 | Kolejka komunikatów | WSCHODNIE STANY USA | 8000| 
| ...      | ...           | ...     |  ...|

W tym przykładzie *składnik aplikacji* i *region* są wymiarami. *Liczba błędów* to wskaźnik KPI, który można podzielić na różne kategorie i/lub rynki, a także może być zagregowany. Na przykład liczba błędów *kolejki komunikatów* we wszystkich regionach.

## <a name="measure"></a>Measure

Miara to podstawowy lub specyficzny dla jednostki termin oraz wartość wymierną metryki.

## <a name="time-series"></a>szereg czasowy

Szereg czasowy to seria punktów danych indeksowanych (lub wyświetlanych na wykresie) w kolejności chronologicznej. Najczęściej seria czasowa jest sekwencją wykonywaną po kolejnych, równomiernie rozmieszczeniu punktów w czasie. Jest to sekwencja danych dyskretnych.

W klasyfikatorach metryk wartości jednej metryki w określonej kombinacji wymiarów nazywa się jedną serią.

## <a name="granularity"></a>Poziom szczegółowości

Stopień szczegółowości wskazuje częstotliwość generowania punktów danych w źródle danych. Na przykład codziennie, co godzinę.

## <a name="start-time"></a>Godzina rozpoczęcia

Czas rozpoczęcia to czas, przez który klasyfikator metryk rozpoczyna pozyskiwanie danych ze źródła danych. Źródło danych musi mieć dane o określonym czasie rozpoczęcia.

## <a name="confidence-boundaries"></a>Granice zaufania

> [!NOTE]
> Granice zaufania nie są jedynymi pomiarami używanymi do znajdowania anomalii. Istnieje możliwość, że punkty danych poza tą granicą mają być oflagowane jako normalne przez model wykrywania. 

W klasyfikatorach metryk, granice zaufania reprezentują czułość używanego algorytmu i są używane do filtrowania zbyt wrażliwych anomalii. W portalu internetowym granice zaufania są wyświetlane jako przezroczysty niebieski pasek. Wszystkie punkty w paśmie są traktowane jako punkty normalne.

Klasyfikator metryk zapewnia narzędzia do dostosowywania czułości użytych algorytmów. Aby uzyskać więcej informacji [, zobacz How to: Configure Metrics i precyzyjne dostosowywanie konfiguracji wykrywania](how-tos/configure-metrics.md) .

![Granice zaufania](media/confidence-bounds.png)


## <a name="hook"></a>Łożona

Klasyfikator metryk umożliwia tworzenie i subskrybowanie alertów w czasie rzeczywistym. Te alerty są wysyłane przez Internet [przy użyciu punktu zaczepienia](how-tos/alerts.md).

## <a name="anomaly-incident"></a>Zdarzenie anomalii

Po zastosowaniu konfiguracji wykrywania do metryk zdarzenia są generowane zawsze wtedy, gdy jakaś seria w nim ma anomalie. W przypadku dużych zestawów danych może to spowodować przeciążenie, więc klasyfikator metryk grupuje anomalie w ramach metryki w ramach zdarzenia. Usługa oceni również ważność i oferuje narzędzia do [diagnozowania incydentu](how-tos/diagnose-incident.md).

### <a name="incident-tree"></a>Drzewo zdarzeń

W klasyfikatorze metryk można zastosować wykrywanie anomalii w metrykach, a następnie klasyfikator metryk automatycznie monitoruje wszystkie szeregi czasowe wszystkich kombinacji wymiarów. Za każdym razem, gdy wykryto jakiekolwiek anomalie, klasyfikator metryk agreguje anomalie do zdarzeń.
Po wystąpieniu zdarzenia klasyfikator metryk dostarczy drzewo zdarzeń z hierarchią współtworzenia anomalii i zidentyfikuje je o największym wpływie. Każde zdarzenie ma element główny powodujący anomalię, czyli górny węzeł drzewa.

### <a name="anomaly-grouping"></a>Grupowanie anomalii

Klasyfikator metryk umożliwia znalezienie powiązanej serii czasowej z podobnymi wzorcami. Może ona również zapewniać dokładniejsze informacje o wpływie na inne wymiary i skorelować anomalie.

### <a name="time-series-comparison"></a>Porównanie szeregów czasowych

Możesz wybrać wiele szeregów czasowych, aby porównać trendy w pojedynczej wizualizacji. Zapewnia to jasny i szczegółowy sposób wyświetlania i porównywania powiązanych serii.

## <a name="detection-configuration"></a>Konfiguracja wykrywania

>[!Note]
>Konfiguracje wykrywania są stosowane tylko w ramach pojedynczej metryki.

W portalu sieci Web usługi Advisor Metric konfiguracja wykrywania (taka jak czułość, autoodkładanie i kierunek) jest wyświetlana na panelu po lewej stronie podczas przeglądania metryki. Parametry można dostrajać i stosować do wszystkich serii w ramach tej metryki.

Konfiguracja wykrywania jest wymagana dla każdej szeregu czasowego i określa, czy punkt w szeregu czasowym jest anomalią. Doradca metryk skonfiguruje domyślną konfigurację dla całej metryki podczas pierwszego dołączania danych. 

Dodatkowo można uściślić konfigurację, stosując parametry dostrajania dla grupy serii lub konkretnej. Tylko jedna konfiguracja zostanie zastosowana do szeregów czasowych:
* Konfiguracje zastosowane do określonej serii zastąpią konfiguracje dla grupy
* Konfiguracje dla grupy zastąpią konfiguracje zastosowane do całej metryki.

Klasyfikator metryk zapewnia kilka [metod wykrywania](how-tos/configure-metrics.md#anomaly-detection-methods)i można połączyć je za pomocą operatorów logicznych.

### <a name="smart-detection"></a>Inteligentne wykrywanie

Wykrywanie anomalii przy użyciu wielu algorytmów uczenia maszynowego.

**Czułość**: wartość liczbowa umożliwiająca dostosowanie tolerancji wykrywania anomalii. Wizualnie, im wyższa wartość, tym węższe górne i dolne granice wokół szeregów czasowych.

### <a name="hard-threshold"></a>Próg sztywny

Wartości poza górną lub dolną granicą są anomaliami.

**Min**: Dolna granica

**Max**: górna granica

### <a name="change-threshold"></a>Próg zmiany

Użyj poprzedniej wartości punktu, aby określić, czy ten punkt jest anomalią.

**Procent zmian**: w porównaniu do poprzedniego punktu, bieżący punkt jest anomalią, jeśli wartość procentowa zmiany jest większa niż ten parametr.

**Przejdź do punktów**: ile punktów należy wyszukać.

### <a name="common-parameters"></a>Parametry wspólne

**Kierunek**: punkt jest anomalią tylko wtedy, gdy odchylenie występuje w *górę*, *w dół* lub w *obu* tych przypadkach.

**Nieprawidłowa anomalia do momentu**: punkt danych jest tylko anomalią, jeśli określony procent poprzednich punktów jest również anomalią.

## <a name="alert-settings"></a>Ustawienia alertu

Ustawienia alertów określają, które anomalie powinny wyzwolić alert. Można ustawić wiele alertów z różnymi ustawieniami. Można na przykład utworzyć alert dotyczący anomalii z niższym wpływem na działalność biznesową, a drugi w celu uzyskania dodatkowych alertów o ważności.

Możesz również utworzyć alert dla różnych metryk. Na przykład alert, który jest wyzwalany tylko wtedy, gdy dwie określone metryki mają anomalie.

### <a name="alert-scope"></a>Zakres alertów

Zakres alertu odnosi się do zakresu, do którego odnosi się ten alert. Do wyboru są cztery opcje:

**Anomalie wszystkich serii**: alerty będą wyzwalane w przypadku anomalii we wszystkich seriach w ramach metryki.

**Anomalie w grupie serii**: alerty będą wyzwalane tylko w przypadku anomalii w określonych wymiarach grupy serii. Liczba określonych wymiarów powinna być mniejsza niż łączna liczba wymiarów.

**Anomalie w ulubionej serii**: alerty będą wyzwalane tylko dla anomalii, które są dodawane jako Ulubione. Możesz wybrać grupę serii jako ulubioną dla każdego wykrycia konfiguracji.

**Anomalie w pierwszych n wszystkich seriach**: alerty będą wyzwalane tylko w przypadku anomalii w pierwszych n seriach. Można ustawić parametry, aby określić liczbę sygnatur czasowych do uwzględnienia i ile anomalii musi znajdować się w nich w celu wysłania alertu.

### <a name="severity"></a>Ważność

Ważność to Klasa wykorzystywana przez klasyfikatora metryki do opisywania ważności incydentu, w tym *wysokiej*, *średniej* i *niskiej*.

Obecnie klasyfikator metryk używa następujących czynników do mierzenia ważności alertu:
1. Wartość proporcjonalna i ilość części anomalii w metryce.
1. Zaufanie anomalii.
1. Twoje ulubione ustawienia również przyczyniają się do ważności.

### <a name="auto-snooze"></a>Autoodkładanie

Niektóre anomalie są problemami przejściowymi, szczególnie w przypadku małych metryk szczegółowości. Możesz *odłożyć* alert przez określoną liczbę punktów czasu. Jeśli anomalie zostaną znalezione w określonej liczbie punktów, żaden alert nie zostanie wyzwolony. Zachowanie autoodkładania można ustawić na poziomie poziomu metryk lub serii.

Zachowanie funkcji odkładania można ustawić na poziomie poziomu metryki lub serii.

## <a name="data-feed-settings"></a>Ustawienia strumieniowego źródła danych

### <a name="ingestion-time-offset"></a>Przesunięcie czasu pozyskiwania

Domyślnie dane są pozyskiwane według stopnia szczegółowości (na przykład *codzienne*). Używając dodatniej liczby całkowitej, można opóźnić pozyskiwanie danych przez określoną wartość. Przy użyciu liczby ujemnej można doliczyć pozyskanie przez określoną wartość.

### <a name="max-ingestion-per-minute"></a>Maksymalne pozyskanie na minutę

Ustaw ten parametr, jeśli źródło danych obsługuje ograniczoną współbieżność. W przeciwnym razie pozostaw ustawienia domyślne.

### <a name="stop-retry-after"></a>Zatrzymaj ponowną próbę po

Jeśli pozyskiwanie danych zakończyło się niepowodzeniem, Doradca metryk automatycznie ponowi próbę po upływie czasu. Początek okresu to godzina, o której nastąpiło pierwsze pozyskiwanie danych. Długość okresu ponawiania zależy od stopnia szczegółowości. Jeśli zostanie użyta wartość domyślna ( `-1` ), okres ponawiania zostanie określony zgodnie z stopnia szczegółowości:

| Poziom szczegółowości       | Zatrzymaj ponowną próbę po           |
| :------------ | :--------------- |
| Codziennie, niestandardowy (>= 1 dzień), co tydzień, co miesiąc, co rok     | 7 dni          |
| Co godzinę, niestandardowy (< 1 dzień)       | 72 godzin |

### <a name="min-retry-interval"></a>Minimalny interwał ponawiania prób

Można określić minimalny interwał podczas ponawiania próby ściągnięcia danych ze źródła. Jeśli zostanie użyta wartość domyślna ( `-1` ), interwał ponownych prób zostanie określony zgodnie z stopnia szczegółowości:

| Poziom szczegółowości       | Minimalny interwał ponawiania prób           |
| :------------ | :--------------- |
| Codziennie, niestandardowy (>= 1 dzień), co tydzień, co miesiąc     | 30 minut          |
| Co godzinę, niestandardowy (< 1 dzień)      | 10 minut |
| Rocznie | 1 dzień          |

### <a name="grace-period"></a>Okres prolongaty

> [!Note]
> Okres prolongaty rozpoczyna się od normalnego czasu pozyskiwania oraz od określonego przesunięcia czasu pozyskiwania.
    
Okres prolongaty to okres, w którym doradca metryk będzie kontynuował pobieranie danych ze źródła danych, ale nie uruchamia żadnych alertów. Jeśli nie pobrano żadnych danych po okresie prolongaty, zostanie wyzwolony alert dotyczący *strumieniowego źródła danych* .

### <a name="snooze-alerts-in"></a>Odkładanie alertów w

Gdy ta opcja jest ustawiona na zero, każde sygnatura czasowa z *niedostępnym* spowoduje wyzwolenie alertu. Po ustawieniu na wartość inną niż zero, określona liczba alertów, które *nie są dostępne* , będzie odkładana, jeśli nie pobrano żadnych danych.

## <a name="data-feed-permissions"></a>Uprawnienia strumieniowego źródła danych

Istnieją dwie role do zarządzania uprawnieniami strumieniowego źródła danych: *administrator* i *przeglądarka*. 

* *Administrator* ma pełną kontrolę nad strumieniowym źródłem danych i metrykami. Umożliwiają one aktywację, wstrzymywanie, usuwanie strumieniowego źródła danych oraz aktualizowanie kanałów informacyjnych i konfiguracji. *Administrator* jest zazwyczaj właścicielem metryk.

* *Podgląd* jest w stanie wyświetlić strumieniowe źródło danych lub metryki, ale nie może wprowadzać zmian. 

## <a name="next-steps"></a>Następne kroki
- [Omówienie klasyfikatora metryk](overview.md)
- [Używanie portalu internetowego](quickstarts/web-portal.md)