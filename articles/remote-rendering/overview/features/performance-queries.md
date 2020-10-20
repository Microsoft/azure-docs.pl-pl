---
title: Zapytania wydajności po stronie serwera
description: Jak wykonywać zapytania dotyczące wydajności po stronie serwera za pomocą wywołań interfejsu API
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 95993b35174b80dae8c878c22554ee60afeb8a14
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92206224"
---
# <a name="server-side-performance-queries"></a>Zapytania wydajności po stronie serwera

Lepsza wydajność renderowania na serwerze ma kluczowe znaczenie dla stabilnych szybkości klatek i dobrego środowiska użytkownika. Ważne jest, aby uważnie monitorować charakterystyki wydajności na serwerze i zoptymalizować je w razie potrzeby. Dane dotyczące wydajności mogą być wysyłane za poorednictwem dedykowanych funkcji interfejsu API.

Najbardziej wpływ na wydajność renderowania to dane wejściowe modelu. Dane wejściowe można dostosować zgodnie z opisem w temacie [Konfigurowanie konwersji modelu](../../how-tos/conversion/configure-model-conversion.md).

Wydajność aplikacji po stronie klienta może być również wąskim gardłem. Aby zapoznać się ze szczegółową analizą wydajności po stronie klienta, zaleca się wykonanie [:::no-loc text="performance trace":::](../../how-tos/performance-tracing.md) .

## <a name="clientserver-timeline"></a>Oś czasu klient/serwer

Przed przystąpieniem do szczegółowych informacji dotyczących różnych wartości opóźnienia wartościowa się na punkty synchronizacji między klientem a serwerem na osi czasu:

![Oś czasu potoku](./media/server-client-timeline.png)

Ilustracja pokazuje, jak:

* *oszacowanie ułożenia* jest uruchamiane przez klienta przy stałej szybkości 60-Hz klatek (co 16,6 ms)
* serwer uruchamia renderowanie na podstawie ułożenia
* Serwer wysyła zakodowany obraz wideo
* Klient dekoduje obraz, wykonuje pewne działania procesora i procesora GPU, a następnie wyświetla obraz.

## <a name="frame-statistics-queries"></a>Zapytania statystyk ramki

Statystyka klatek zawiera pewne informacje wysokiego poziomu dla ostatniej ramki, takie jak opóźnienia. Dane podane w `FrameStatistics` strukturze są mierzone po stronie klienta, więc interfejs API jest wywołaniem synchronicznym:

```cs
void QueryFrameData(AzureSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
```

```cpp
void QueryFrameData(ApiHandle<AzureSession> session)
{
    FrameStatistics frameStatistics;
    if (*session->GetGraphicsBinding()->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        // do something with the result
    }
}
```

Pobrany `FrameStatistics` obiekt zawiera następujące elementy członkowskie:

| Członek | Objaśnienie |
|:-|:-|
| latencyPoseToReceive | Opóźnienie z aparatu jest szacowane na urządzeniu klienckim do momentu, gdy ramka serwera dla tego ułożenia jest w pełni dostępna dla aplikacji klienckiej. Ta wartość obejmuje sieć w sieci, czas renderowania serwera, dekodowanie wideo i kompensację wahania. Zobacz **interwał 1 na powyższej ilustracji.**|
| latencyReceiveToPresent | Opóźnienie od dostępności odebranej ramki zdalnej do momentu, gdy aplikacja kliencka wywoła PresentFrame na procesorze CPU. |
| latencyPresentToDisplay  | Opóźnienie wyświetlania ramki na procesorze CPU do momentu wyświetlenia świateł w górę. Ta wartość obejmuje czas procesora GPU, wszelkie bufory klatek wykonywane przez system operacyjny, reprojektowanie sprzętu i czas skanowania ekranu zależnego od urządzenia. Zobacz **Interwał 2 na powyższej ilustracji.**|
| timeSinceLastPresent | Czas między kolejnymi wywołaniami do PresentFrame na PROCESORze. Wartości większe niż czas trwania wyświetlania (na przykład 16,6 MS na urządzeniu klienckim 60-Hz) wskazują problemy spowodowane przez aplikację kliencką, która nie kończy obciążenia procesora w czasie. Zobacz **Interwał 3 na powyższej ilustracji.**|
| videoFramesReceived | Liczba ramek odebranych z serwera w ciągu ostatniej sekundy. |
| videoFrameReusedCount | Liczba odebranych ramek w ciągu ostatniej sekundy, które były używane na urządzeniu więcej niż raz. Wartości inne niż zero wskazują, że ramki musiały być ponownie używane i przeprojektować ze względu na wahania sieci lub nadmierny czas renderowania serwera. |
| videoFramesSkipped | Liczba odebranych ramek w ostatniej sekundzie, które zostały zdekodowane, ale nie są wyświetlane na ekranie, ponieważ dotarła do niej nowsza ramka. Wartości inne niż zero wskazują, że zakłócenia sieci powodowało opóźnione przełączenie wielu ramek, a następnie dotarcie na urządzenie klienckie razem do serii. |
| videoFramesDiscarded | Bardzo podobny do **videoFramesSkipped**, ale powód odrzucania polega na tym, że ramka została umieszczona w tym czasie, aby nie mogła być nawet skorelowane z żadnym oczekującym terminem. W takim przypadku istnieje pewna poważna rywalizacja sieciowa.|
| videoFrameMinDelta | Minimalny czas między dwiema kolejnymi klatkami docierających w ciągu ostatniej sekundy. Wraz z videoFrameMaxDelta, ten zakres daje wskaźnik wahania spowodowany przez koder-dekoder sieciowy lub wideo. |
| videoFrameMaxDelta | Maksymalny czas między dwoma kolejnymi klatkami przychodzącymi w ciągu ostatniej sekundy. Wraz z videoFrameMinDelta, ten zakres daje wskaźnik wahania spowodowany przez koder-dekoder sieciowy lub wideo. |

Suma wszystkich wartości opóźnienia jest zwykle znacznie większa niż czas dostępności ramki o 60 Hz. Jest to prawidłowe, ponieważ wiele ramek jest jednocześnie w locie, a nowe żądania ramki są uruchamiane z pożądaną szybkością klatek, jak pokazano na ilustracji. Jednak opóźnienie jest zbyt duże, ma wpływ na jakość [opóźnionego projektu](../../overview/features/late-stage-reprojection.md)i może naruszyć ogólne środowisko pracy.

`videoFramesReceived`, `videoFrameReusedCount` i `videoFramesDiscarded` mogą służyć do oceny wydajności sieci i serwera. Jeśli `videoFramesReceived` jest niska i `videoFrameReusedCount` wysoka, może to wskazywać na przeciążenie sieci lub niską wydajność serwera. Wysoka `videoFramesDiscarded` wartość wskazuje również Przeciążenie sieci.

Na koniec, `timeSinceLastPresent` , `videoFrameMinDelta` i `videoFrameMaxDelta` daje pomysł odchylenia przychodzących klatek wideo i lokalnych połączeń. Wysoka WARIANCJA oznacza niestabilną szybkość klatek.

Żadna z powyższych wartości nie daje wyraźnego wskazania czystego opóźnienia sieci (czerwona strzałka na ilustracji), ponieważ dokładna ilość czasu renderowania zajętego serwera musi zostać odjęta od wartości dwukierunkowej `latencyPoseToReceive` . Część ogólnego opóźnienia po stronie serwera to informacje, które są niedostępne dla klienta. Jednak w następnym akapicie wyjaśniono, jak ta wartość jest przybliżona przez dodatkowe dane wejściowe z serwera i uwidaczniane przez `networkLatency` wartość.

## <a name="performance-assessment-queries"></a>Zapytania dotyczące oceny wydajności

*Zapytania oceny wydajności* zawierają bardziej szczegółowe informacje na temat obciążenia procesora CPU i procesora GPU na serwerze. Ponieważ dane są żądane z serwera, wykonanie zapytania dotyczącego migawki wydajności jest zgodne ze zwykłym wzorcem asynchronicznym:

```cs
PerformanceAssessmentAsync _assessmentQuery = null;

void QueryPerformanceAssessment(AzureSession session)
{
    _assessmentQuery = session.Actions.QueryServerPerformanceAssessmentAsync();
    _assessmentQuery.Completed += (PerformanceAssessmentAsync res) =>
    {
        // do something with the result:
        PerformanceAssessment result = res.Result;
        // ...

        _assessmentQuery = null;
    };
}
```

```cpp
void QueryPerformanceAssessment(ApiHandle<AzureSession> session)
{
    ApiHandle<PerformanceAssessmentAsync> assessmentQuery = *session->Actions()->QueryServerPerformanceAssessmentAsync();
    assessmentQuery->Completed([] (ApiHandle<PerformanceAssessmentAsync> res)
    {
        // do something with the result:
        PerformanceAssessment result = res->GetResult();

        // ...

    });
}
```

W przeciwieństwie do `FrameStatistics` obiektu, `PerformanceAssessment` obiekt zawiera informacje po stronie serwera:

| Członek | Objaśnienie |
|:-|:-|
| timeCPU | Średni czas procesora CPU serwera na klatkę w milisekundach |
| timeGPU | Średni czas procesora GPU serwera na klatkę w milisekundach |
| utilizationCPU | Całkowite użycie procesora serwera w procentach |
| utilizationGPU | Całkowite użycie procesora GPU serwera w procentach |
| memoryCPU | Łączne wykorzystanie pamięci głównej serwera (w procentach) |
| memoryGPU | Całkowite Użycie dedykowanej pamięci wideo w procentach procesora GPU serwera |
| networkLatency | Przybliżone średnie opóźnienie sieci dwukierunkowej w milisekundach. Na powyższej ilustracji odpowiada sumie czerwonych strzałek. Wartość jest obliczana przez odjęcie rzeczywistego czasu renderowania serwera od `latencyPoseToReceive` wartości `FrameStatistics` . Chociaż to przybliżenie nie jest dokładne, daje kilka oznak opóźnienia sieci, odizolowanych od wartości opóźnienia obliczonych na kliencie. |
| polygonsRendered | Liczba trójkątów renderowanych w jednej ramce. Ta liczba obejmuje również Trójkąty, które są wyłączane później podczas renderowania. Oznacza to, że ta liczba nie różni się od siebie w różnych położeniach kamer, ale wydajność może się nieco różnić w zależności od współczynnika usuwania.|

Aby ułatwić ocenę wartości, każda część zawiera klasyfikację jakości, taką jak **doskonały**, **dobry**, **mediocre**lub **zły**.
Ta Metryka oceny zapewnia przybliżoną wskazówkę dotyczącą kondycji serwera, ale nie powinna być traktowana jako bezwzględna. Załóżmy na przykład, że dla czasu procesora GPU zobaczysz wynik "mediocre". Jest on uznawany za mediocre, ponieważ zbliża się do limitu ogólnego budżetu czasu ramki. W takim przypadku może być jednak dobrą wartością, ponieważ renderuje skomplikowany model.

## <a name="statistics-debug-output"></a>Statystyka danych wyjściowych debugowania

Klasa `ARRServiceStats` jest klasą języka C#, która otacza zarówno dane statystyczne ramki, jak i kwerendy oceny wydajności, i zapewnia wygodną funkcjonalność do zwracania statystyk jako zagregowanych wartości lub wstępnie skompilowanego ciągu. Poniższy kod stanowi najprostszy sposób wyświetlania statystyk po stronie serwera w aplikacji klienckiej.

```cs
ARRServiceStats _stats = null;

void OnConnect()
{
    _stats = new ARRServiceStats();
}

void OnDisconnect()
{
    _stats = null;
}

void Update()
{
    if (_stats != null)
    {
        // update once a frame to retrieve new information and build average values
        _stats.Update(Service.CurrentActiveSession);

        // retrieve a string with relevant stats information
        InfoLabel.text = _stats.GetStatsString();
    }
}
```

Powyższy kod wypełnia etykietę tekstową następującym tekstem:

![Dane wyjściowe ciągu ArrServiceStats](./media/arr-service-stats.png)

`GetStatsString`Interfejs API formatuje ciąg wszystkich wartości, ale dla każdej pojedynczej wartości można także programowo wykonać zapytanie z `ARRServiceStats` wystąpienia.

Istnieją także warianty elementów członkowskich, które agregują wartości w czasie. Zobacz elementy członkowskie z sufiksem `*Avg` , `*Max` lub `*Total` . Element członkowski `FramesUsedForAverage` wskazuje liczbę ramek, które zostały użyte dla tej agregacji.

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [Zdalnymanager. QueryServerPerformanceAssessmentAsync ()](/dotnet/api/microsoft.azure.remoterendering.remotemanager.queryserverperformanceassessmentasync)
* [Zdalnymanager:: QueryServerPerformanceAssessmentAsync ()](/cpp/api/remote-rendering/remotemanager#queryserverperformanceassessmentasync)

## <a name="next-steps"></a>Następne kroki

* [Utwórz ślady wydajności](../../how-tos/performance-tracing.md)
* [Konfigurowanie konwersji modelu](../../how-tos/conversion/configure-model-conversion.md)