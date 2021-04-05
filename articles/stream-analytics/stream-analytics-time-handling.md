---
title: Opis obsługi czasu w Azure Stream Analytics
description: Dowiedz się, jak wybrać najlepszy czas rozpoczęcia, obsłużyć zdarzenia opóźnione i wczesne oraz metryki obsługi czasu w Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: f9dbdb3907b376df8de988730c6c48ed01bfccd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019945"
---
# <a name="understand-time-handling-in-azure-stream-analytics"></a>Opis obsługi czasu w Azure Stream Analytics

W tym artykule dowiesz się, jak dokonać wyboru projektu w celu rozwiązywania praktycznych problemów z obsługą czasu w Azure Stream Analytics zadaniach. Decyzje dotyczące projektowania obsługi czasu są ściśle powiązane z czynnikami porządkowania zdarzeń.

## <a name="background-time-concepts"></a>Koncepcje czasu w tle

Aby lepiej utworzyć ramkę w dyskusji, zdefiniujmy niektóre koncepcje w tle:

- **Czas zdarzenia**: czas wystąpienia oryginalnego zdarzenia. Na przykład, gdy przesuwanie samochodu na autostradę zbliża się do kabiny.

- **Czas przetwarzania**: czas, gdy zdarzenie osiągnie system przetwarzania i jest zaobserwowane. Na przykład, gdy czujnik kabiny z opłatami jest widoczny dla samochodu i system komputerowy zajmuje kilka chwil, aby przetwarzać dane.

- **Znak wodny**: znacznik czasu zdarzenia wskazujący, które zdarzenia punktu zostały ingressed do procesora przesyłania strumieniowego. Znaki wodne pozwalają systemowi wskazywać na wyczyszczenie zdarzeń. Ze względu na charakter strumieni, przychodzące dane zdarzeń nigdy nie są przerywane, dlatego znaki wodne wskazują postęp do określonego punktu w strumieniu.

   Koncepcja znaku wodnego jest ważna. Znaki wodne umożliwiają Stream Analytics określenie, kiedy system może generować kompletne, poprawne i powtarzalne wyniki, które nie muszą zostać wycofane. Przetwarzanie może odbywać się w przewidywalny i powtarzalny sposób. Na przykład jeśli należy wykonać ponowną inwentaryzację dla określonego warunku obsługi błędów, znaki wodne są bezpiecznymi punktami początkowymi i końcowymi.

Aby uzyskać dodatkowe zasoby związane z tym tematem, zobacz wpis w blogu Tyler Akidau, [przesyłania strumieniowego 101](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-101) i [przesyłania strumieniowego 102](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-102).

## <a name="choose-the-best-starting-time"></a>Wybierz najlepszy czas rozpoczęcia

Stream Analytics zapewnia użytkownikom dwie opcje czasu wybierania: czas dojazdu i czas aplikacji.

### <a name="arrival-time"></a>Czas przybycia

Czas przybycia jest przypisywany do źródła danych wejściowych, gdy zdarzenie osiągnie źródło. Można uzyskać dostęp do czasu przybycia przy użyciu właściwości **EventEnqueuedUtcTime** Event Hubs dla danych wejściowych, właściwości **IoTHub. EnqueuedTime** dla danych wejściowych IoT Hub i właściwości **BlobProperties. LastModified** dla danych wejściowych obiektu BLOB.

Czas przybycia jest używany domyślnie i najlepiej jest używany w scenariuszach archiwizowania danych, w których logika czasowa nie jest konieczna.

### <a name="application-time-also-named-event-time"></a>Czas aplikacji (nazywany także czasem zdarzenia)

Czas aplikacji jest przypisywany podczas generowania zdarzenia i jest częścią ładunku zdarzenia. Aby przetwarzać zdarzenia według czasu aplikacji, należy użyć klauzuli **timestamp by** w zapytaniu SELECT. Jeśli **sygnatura czasowa przez** nie jest obecna, zdarzenia są przetwarzane przez czas przybycia.

Ważne jest używanie sygnatury czasowej w ładunku, gdy logika czasowa jest uwzględniana w przypadku opóźnień w systemie źródłowym lub w sieci. Czas przypisany do zdarzenia jest dostępny w [systemie. SYGNATURa CZASowa](/stream-analytics-query/system-timestamp-stream-analytics).

## <a name="how-time-progresses-in-azure-stream-analytics"></a>Jak postęp Azure Stream Analytics

W przypadku korzystania z czasu aplikacji postęp czasu jest oparty na zdarzeniach przychodzących. Trudne jest, aby system przetwarzania strumienia wiedział, czy nie ma żadnych zdarzeń, lub jeśli zdarzenia są opóźnione. Z tego powodu Azure Stream Analytics generuje znaki wodne heurystyczne w następujący sposób dla każdej partycji wejściowej:

* Gdy istnieje zdarzenie przychodzące, znak wodny to największe Stream Analytics czasu zdarzenia, które wystąpiły do tej pory pomniejszone o rozmiar okna tolerancji niezależnej od kolejności.

* Gdy nie ma żadnego przychodzącego zdarzenia, jest to bieżący szacowany czas przybycia pomniejszony o opóźnione okno tolerancji przybycia. Szacowany czas przybycia to czas, który upłynął od momentu ostatniego wyświetlenia zdarzenia wejściowego, a także czas przybycia zdarzenia wejściowego.

   Czas przybycia można oszacować tylko w przypadku, gdy rzeczywisty czas przybycia jest generowany dla wejściowego brokera zdarzeń, takiego jak Event Hubs, ani na Azure Stream Analytics maszyny wirtualnej przetwarzają zdarzenia.

Projekt obsługuje dwa dodatkowe cele inne niż generowanie znaków wodnych:

1. System generuje wyniki w odpowiednim czasie z lub bez zdarzeń przychodzących.

   Masz kontrolę nad tym, jak długo chcesz zobaczyć wyniki wyjściowe. W Azure Portal na stronie **porządkowanie zdarzeń** zadania Stream Analytics można skonfigurować ustawienie **zdarzenia poza kolejnością** . Po skonfigurowaniu tego ustawienia należy rozważyć wymianę osi czasu z tolerancją zdarzeń poza kolejnością w strumieniu zdarzeń.

   Okno tolerancji opóźnionego przybycia jest niezbędne, aby zachować generowanie znaków wodnych nawet w przypadku braku zdarzeń przychodzących. Czasami może istnieć okres, w którym nie występują żadne przychodzące zdarzenia, na przykład gdy strumień wejściowy zdarzenia jest rozrzedzony. Ten problem jest zaostrzany przez użycie wielu partycji w ramach wejściowego brokera zdarzeń.

   Systemy przetwarzania danych przesyłanych strumieniowo bez okna tolerancji opóźnionego przybycia mogą mieć wpływ na opóźnione dane wyjściowe, gdy dane wejściowe są rozrzedzone i używane są wiele partycji.

2. Zachowanie systemu musi być powtarzane. Powtarzalność to ważna właściwość systemu przetwarzania danych przesyłanych strumieniowo.

   Znak wodny jest uzyskiwany od czasu wejścia i czasu aplikacji. Oba elementy są utrwalane w brokerze zdarzeń i w rezultacie powtarzane. W przypadku oszacowania czasu nadejścia w przypadku braku zdarzeń Azure Stream Analytics dziennik szacowany czas przybycia dla powtarzania podczas odtwarzania w przypadku odzyskiwania po awarii.

Jeśli zdecydujesz się na użycie **czasu dojazdu** jako czasu zdarzenia, nie musisz konfigurować wartości tolerancja niezależna od kolejności i opóźnionego przybycia. Ponieważ **czas przybycia** jest gwarantowany do zwiększenia w brokerze zdarzeń wejściowych, Azure Stream Analytics po prostu nie uwzględnia konfiguracji.

## <a name="late-arriving-events"></a>Późne nadejście zdarzeń

Według definicji okna tolerancji opóźnionego przybycia dla każdego przychodzącego zdarzenia Azure Stream Analytics porównuje **czas zdarzenia** z **czasem przybycia**. Jeśli czas zdarzenia znajduje się poza oknem tolerancji, można skonfigurować system do porzucenia zdarzenia lub dostosować czas do przekroczenia limitu czasu.

Po wygenerowaniu znaków wodnych usługa może potencjalnie odbierać zdarzenia ze zdarzeniem krótszym niż wartość limitu czasu. Można skonfigurować usługę do **porzucenia** tych zdarzeń lub **dostosować** czas do wartości limitu.

W ramach korekty zdarzenie **System. timestamp** ma ustawioną nową wartość, ale samo pole **czasu zdarzenia** nie jest zmieniane. Ta korekta jest jedyną sytuacją, w której zdarzenie **System. timestamp** może różnić się od wartości w polu czas zdarzenia i może spowodować wygenerowanie nieoczekiwanych wyników.

## <a name="handle-time-variation-with-substreams"></a>Obsługa zmian czasu z podstrumieniami

Opisany mechanizm generowania znaków wodnych algorytmu heurystycznego działa dobrze w większości przypadków, gdy czas jest przeważnie zsynchronizowany z różnymi nadawcami zdarzeń. Jednak w rzeczywistości, szczególnie w wielu scenariuszach IoT, system ma małą kontrolę nad zegarem dla nadawców zdarzeń. Nadawcy zdarzeń mogą wyróżnić wszystkie urządzenia w polu, na przykład na różnych wersjach sprzętu i oprogramowania.

Zamiast używać znaku wodnego, który jest globalny dla wszystkich zdarzeń na partycji wejściowej, Stream Analytics ma inny mechanizm nazywany **podstrumieniami**. Można użyć podstrumieńów w zadaniu, pisząc kwerendę zadania, która używa klauzuli [**timestamp by**](/stream-analytics-query/timestamp-by-azure-stream-analytics) **i słowa kluczowego w.** Aby wyznaczyć Podstrumień, podaj nazwę kolumny klucza po słowie kluczowym **over** , na przykład `deviceid` , tak aby system stosował zasady czasu według tej kolumny. Każdy Podstrumień pobiera własny niezależny znak wodny. Mechanizm ten jest przydatny do zezwalania na generowanie czasowych danych wyjściowych w przypadku dużych pochylenia zegara lub opóźnień sieci między nadawcami zdarzeń.

Podstrumieńy są unikatowym rozwiązaniem oferowanym przez Azure Stream Analytics i nie są oferowane przez inne systemy przetwarzania danych przesyłanych strumieniowo.

W przypadku korzystania z podstrumieni, Stream Analytics ma zastosowanie do przychodzących zdarzeń. Tolerancja późnego przybycia decyduje o maksymalnej ilości, o jaką różne podstrumienie mogą się różnić od siebie. Na przykład jeśli urządzenie 1 ma sygnaturę czasową 1, a urządzenie 2 ma sygnaturę czasową 2, najwyżej późna tolerancja przybycia ma sygnaturę czasową 2 minus timestamp 1. Ustawienie domyślne to 5 sekund i jest prawdopodobnie zbyt małe w przypadku urządzeń z rozbieżnymi sygnaturami czasowymi. Zalecamy rozpoczęcie od 5 minut i dostosowanie ich zgodnie z wzorcem odchylenia zegara urządzenia.

## <a name="early-arriving-events"></a>Wczesne nadejście zdarzeń

Być może zauważono inną koncepcję o nazwie okno wczesnego przybycia, która wygląda jak przeciwieństwo okna tolerancji opóźnionego przybycia. To okno jest ustalone na 5 minut i służy innym przeznaczeniu w oknie tolerancji opóźnionego przybycia.

Ponieważ Azure Stream Analytics gwarantuje pełne wyniki, można określić **godzinę rozpoczęcia zadania** jako pierwszy czas wyjścia zadania, a nie czas wejściowy. Godzina rozpoczęcia zadania jest wymagana, aby całe okno zostało przetworzone, a nie tylko od środka okna.

Stream Analytics określa czas rozpoczęcia od specyfikacji zapytania. Jednak ze względu na to, że wejściowy Broker zdarzeń jest indeksowany tylko przez czas przybycia, system musi przetłumaczyć czas zdarzenia rozpoczęcia na czas przybycia. System może rozpocząć przetwarzanie zdarzeń od tego momentu w danych wejściowych brokera zdarzeń. W przypadku wczesnego limitu okna tłumaczenie jest proste: czas rozpoczęcia zdarzenia minus 5 minut wczesnego przybycia okna. To obliczenie oznacza również, że system porzuca wszystkie zdarzenia, które są widoczne jako czas zdarzenia 5 minut wcześniejszy niż czas przybycia. [Metryka zdarzeń wczesnego wejścia](stream-analytics-monitoring.md) jest zwiększana, gdy zdarzenia są porzucane.

Ta koncepcja służy do zapewnienia powtarzania przetwarzania niezależnie od tego, gdzie rozpoczyna się wyprowadzanie danych wyjściowych. Bez takiego mechanizmu nie można zagwarantowania powtarzalności, tak jak wiele innych systemów przesyłania strumieniowego.

## <a name="side-effects-of-event-ordering-time-tolerances"></a>Skutki uboczne dla tolerancji czasu porządkowania zdarzeń

Stream Analytics zadania mają kilka opcji **określania kolejności zdarzeń** . Dwie można skonfigurować w Azure Portal: ustawienia **zdarzenia poza kolejnością** (Tolerancja braku kolejności) oraz **zdarzenia, które docierają do późnego** ustawienia (Tolerancja późnego przybycia). Tolerancja **wczesnego przybycia** jest stała i nie można jej skorygować. Te zasady czasu są używane przez Stream Analytics, aby zapewnić silne gwarancje. Jednak te ustawienia mają pewne nieoczekiwane konsekwencje:

1. Przypadkowe wysyłanie zdarzeń, które są zbyt wczesne.

   Wczesne zdarzenia nie powinny być normalnie wychodzące. Istnieje możliwość, że wczesne zdarzenia są wysyłane do danych wyjściowych, jeśli zegar nadawcy jest uruchamiany zbyt szybko. Wszystkie wczesne zdarzenia, które są porzucane, więc nie będą wyświetlane z danych wyjściowych.

2. Wysyłanie starych zdarzeń do Event Hubs do przetworzenia przez Azure Stream Analytics.

   Stare zdarzenia mogą wydawać się nieszkodliwe w pierwszej kolejności, ze względu na zastosowanie tolerancji opóźnionego przybycia, stare zdarzenia mogą zostać usunięte. Jeśli zdarzenia są zbyt stare, wartość **System. timestamp** jest zmieniana podczas pozyskiwania zdarzeń. Ze względu na to zachowanie obecnie Azure Stream Analytics jest bardziej odpowiednie dla scenariuszy przetwarzania zdarzeń niemal w czasie rzeczywistym, a nie do historycznych scenariuszy przetwarzania zdarzeń. Można ustawić **zdarzenia, które docierają** do największej możliwej wartości (20 dni) w celu obejścia tego zachowania w niektórych przypadkach.

3. Dane wyjściowe pozornie są opóźnione.

   Pierwszy znak wodny jest generowany w obliczonym czasie: **Maksymalny czas zdarzenia** , który został dotychczas obserwowany przez system, pomniejszony o rozmiar okna tolerancji niezależnej od kolejności. Domyślnie tolerancja poza kolejnością jest skonfigurowana na wartość zero (00 minut i 00 sekund). Gdy ustawisz ją na wyższą, niezerową wartość czasu, pierwsze wyjście zadania przesyłania strumieniowego jest opóźnione o tę wartość czasu (lub większą) z powodu wyliczania czasu pierwszego znaku wodnego.

4. Dane wejściowe są rozrzedzone.

   Gdy nie ma danych wejściowych w danej partycji, czas znaku wodnego jest obliczany jako **czas przybycia** pomniejszony o opóźnione okno tolerancja przybycia. W związku z tym, jeśli zdarzenia wejściowe są rzadko i rozrzedzone, dane wyjściowe można opóźnić o ten czas. Domyślne **zdarzenia, które docierają do wartości późnej,** to 5 sekund. Należy się spodziewać, że podczas wysyłania zdarzeń wejściowych po jednym naraz będzie widoczne pewne opóźnienie, na przykład. Opóźnienia mogą uzyskać gorszą wartość, gdy ustawiasz **zdarzenia, które docierają do późnego** okna do dużej wartości.

5. Wartość **System. timestamp** jest różna od godziny w polu **czas zdarzenia** .

   Jak opisano wcześniej, system dostosowuje czas zdarzenia przez okna tolerancja niezależna od kolejności lub opóźnienia przybycia. Wartość **System. timestamp** zdarzenia jest korygowana, ale nie jest to pole **czasu zdarzenia** . Może służyć do identyfikowania zdarzeń, dla których są dostosowane znaczniki czasu. Jeśli system zmienił sygnaturę czasową z powodu jednej z tolerancji, zwykle są one takie same.

## <a name="metrics-to-observe"></a>Metryki do obserwowania

Można obserwować liczbę efektów tolerancji czasu porządkowania zdarzeń za pomocą [metryk zadania Stream Analytics](stream-analytics-monitoring.md). Następujące metryki są istotne:

|Metric  | Opis  |
|---------|---------|
| **Zdarzenia poza kolejnością** | Wskazuje liczbę zdarzeń odebranych poza kolejnością, które zostały porzucone lub miały skorygowaną sygnaturę czasową. Ta Metryka jest bezpośrednio objęta konfiguracją ustawienia **zdarzenia poza kolejnością** na stronie **porządkowania zdarzeń** w zadaniu w Azure Portal. |
| **Opóźnione zdarzenia wejściowe** | Wskazuje liczbę zdarzeń opóźnionych ze źródła. Ta Metryka obejmuje zdarzenia, które zostały usunięte lub miały sygnaturę czasową. Ta Metryka ma bezpośredni wpływ na konfigurację **zdarzeń, które docierają do późnego** ustawienia na stronie **porządkowanie zdarzeń** w zadaniu w Azure Portal. |
| **Wczesne zdarzenia wejściowe** | Wskazuje liczbę zdarzeń, które docierają wcześniej ze źródła, które zostały usunięte, lub jeśli ich sygnatura czasowa została dostosowana, jeśli są dłuższe niż 5 minut. |
| **Opóźnienie znaku wodnego** | Wskazuje opóźnienie zadania przetwarzania danych przesyłanych strumieniowo. Więcej informacji znajduje się w poniższej sekcji.|

## <a name="watermark-delay-details"></a>Szczegóły opóźnienia znaku wodnego

Metryka **opóźnienia znaku wodnego** jest obliczana jako czas zegara ściany węzła przetwarzania pomniejszona o największy znak wodny, który wcześniej widział. Aby uzyskać więcej informacji, zobacz [wpis w blogu z opóźnieniem dla znaku wodnego](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/).

Może istnieć kilka powodów, dla których ta wartość metryki jest większa niż 0 w przypadku normalnego działania:

1. Nieodłączne Opóźnienie przetwarzania potoku przesyłania strumieniowego. Zwykle to opóźnienie jest nominalne.

2. Okno tolerancji poza kolejnością zostało wprowadzone, ponieważ znak wodny jest zmniejszany o rozmiar okna tolerancji.

3. Okno opóźnionego przybycia zostało wprowadzone z opóźnieniem, ponieważ znak wodny jest zmniejszany o rozmiar okna tolerancji.

4. Przesunięcie zegara przez węzeł przetwarzania generującego metrykę.

Istnieje wiele innych ograniczeń zasobów, które mogą spowodować spowolnienie potoku przesyłania strumieniowego. Metryka opóźnienia może wzrosnąć z powodu:

1. Za mało zasobów przetwarzania w Stream Analytics, aby obsłużyć woluminy zdarzeń wejściowych. Aby skalować zasoby, zobacz [Opis i Dostosowywanie jednostek przesyłania strumieniowego](stream-analytics-streaming-unit-consumption.md).

2. Nie ma wystarczającej przepływności w ramach brokerów zdarzeń wejściowych, dlatego ograniczenia są ograniczone. Aby poznać możliwe rozwiązania, zobacz [Automatyczne skalowanie jednostek przepływności na platformie Azure Event Hubs](../event-hubs/event-hubs-auto-inflate.md).

3. Nie zainicjowano obsługi ujścia danych wyjściowych za pomocą wystarczającej pojemności, dzięki czemu są one ograniczone. Możliwe rozwiązania różnią się znacznie w zależności od wersji używanej usługi wyjściowej.

## <a name="output-event-frequency"></a>Częstotliwość zdarzeń wyjściowych

Azure Stream Analytics używa postępu wodnego jako jedynego wyzwalacza do tworzenia zdarzeń wyjściowych. Ponieważ znak wodny jest wyprowadzany z danych wejściowych, można go powtarzać podczas odzyskiwania po awarii, a także w przypadku ponownego przetwarzania przez użytkownika. W przypadku korzystania z [agregacji okna](stream-analytics-window-functions.md)Usługa tworzy tylko dane wyjściowe na końcu systemu Windows. W niektórych przypadkach użytkownicy mogą chcieć zobaczyć częściowe agregaty wygenerowane w systemie Windows. Częściowe agregacje nie są obecnie obsługiwane w Azure Stream Analytics.

W innych rozwiązaniach przesyłania strumieniowego zdarzenia wyjściowe mogą być w różnych punktach wyzwalacza, w zależności od sytuacji zewnętrznych. Istnieje możliwość, że w niektórych rozwiązaniach zdarzenia wyjściowe dla danego przedziału czasu można generować wiele razy. Ponieważ wartości wejściowe są rafinowane, zagregowane wyniki stają się dokładniejsze. Zdarzenia mogą być w pierwszej kolejności i skorygowane w miarę upływu czasu. Na przykład, gdy określone urządzenie jest w trybie offline z sieci, system może użyć szacowanej wartości. Później to samo urządzenie przejdzie w tryb online do sieci. Następnie rzeczywiste dane zdarzenia mogą być uwzględnione w strumieniu wejściowym. Dane wyjściowe z przetwarzania tego przedziału czasu tworzą dokładniejsze dane wyjściowe.

## <a name="illustrated-example-of-watermarks"></a>Zilustrowano przykład znaków wodnych

Na poniższych ilustracjach przedstawiono, jak ma postępować w różnych sytuacjach.

W tej tabeli przedstawiono przykładowe dane przedstawione poniżej. Należy zauważyć, że czas zdarzenia i czas przybycia różnią się, czasami dopasowuje i czasami nie.

| Czas zdarzenia | Czas przybycia | DeviceId |
| --- | --- | --- |
| 12:07 | 12:07 | device1
| 12:08 | 12:08 | device2
| 12:17 | 12:11 | device1
| 12:08 | 12:13 | device3
| 12:19 | 12:16 | device1
| 12:12 | 12:17 | device3
| 12:17 | 12:18 | device2
| 12:20 | 12:19 | device2
| 12:16 | 12:21 | device3
| 12:23 | 12:22 | device2
| 12:22 | 12:24 | device2
| 12:21 | 12:27 | device3

Na tej ilustracji są używane następujące tolerancje:

- Okna wczesnego przybycia to 5 minut
- Późne przebycie okna to 5 minut
- Okno zmiany kolejności wynosi 2 minuty

1. Ilustracja przedstawiająca przechodzenie między następującymi zdarzeniami:

   ![Ilustracja przedstawiająca Azure Stream Analytics znak wodny](media/stream-analytics-time-handling/WaterMark-graph-1.png)

   Znaczące procesy zilustrowane na poprzedniej ilustracji:

   1. Pierwsze zdarzenie (device1) i drugie zdarzenie (device2) mają wyrównane godziny i są przetwarzane bez korekt. Znak wodny postępuje na każdym zdarzeniu.

   2. Gdy trzecie zdarzenie (device1) jest przetwarzane, czas przybycia (12:11) poprzedza czas zdarzenia (12:17). Zdarzenie zostało porzucone na początku 6 minut, więc zdarzenie jest porzucane z powodu 5-minutowej tolerancji przybycia na wczesny czas.

      Ten znak wodny nie postępuje w tym przypadku wczesnego zdarzenia.

   3. Czwarte zdarzenie (device3) i piąte zdarzenie (device1) zostały wyrównane i są przetwarzane bez dostosowania. Znak wodny postępuje na każdym zdarzeniu.

   4. Po przetworzeniu szóstego zdarzenia (device3) godzina przybycia (12:17) i czas zdarzenia (12:12) są poniżej poziomu znaku wodnego. Czas zdarzenia jest dostosowywany do poziomu znaku wodnego (12:17).

   5. Po przetworzeniu dwunastego zdarzenia (device3) czas przybycia (12:27) wynosi 6 minut przed czasem zdarzenia (12:21). Zasady późnego przybycia są stosowane. Czas zdarzenia jest dostosowywany (12:22), który znajduje się powyżej znaku wodnego (12:21), co oznacza, że dalsze korekty nie są stosowane.

2. Druga ilustracja przedstawiająca znak wodny postępu bez zasad wczesnego przybycia:

   ![Nie Azure Stream Analytics ilustracja przedstawiająca górny limit zasad](media/stream-analytics-time-handling/watermark-graph-2.png)

   W tym przykładzie nie są stosowane zasady wczesnego przybycia. Nieprzestające zdarzenia, które docierają wczesnie, powodują znaczne zwiększenie wartości znaku wodnego. Zwróć uwagę, że trzecie zdarzenie (deviceId1 w czasie 12:11) nie zostanie porzucone w tym scenariuszu, a znak wodny zostanie podniesiony do 12:15. Czwarty czas zdarzenia jest dostosowywany do przodu o 7 minut (12:08 do 12:15).

3. Na ostatniej ilustracji są używane podstrumienie (za pośrednictwem DeviceId). Wiele znaków wodnych jest śledzonych, jeden na strumień. W wyniku tego są mniej zdarzeń, które zostały odpowiednio dostosowane.

   ![Ilustracja przedstawiająca znak wodny Azure Stream Analytics podstrumienia](media/stream-analytics-time-handling/watermark-graph-3.png)

## <a name="next-steps"></a>Następne kroki

- [Zagadnienia dotyczące kolejności zdarzeń Azure Stream Analytics]()
- [Metryki zadania Stream Analytics](stream-analytics-monitoring.md)