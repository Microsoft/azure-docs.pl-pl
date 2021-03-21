---
title: Skalowanie Machine Learning Studio (klasycznych) w Azure Stream Analytics
description: W tym artykule opisano sposób skalowania Stream Analytics zadań korzystających z funkcji Machine Learning Studio (klasycznych) przez skonfigurowanie partycji i jednostek strumienia.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: 1ee1411aba7724d76ed8626de9b8b038d02339dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103574258"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Skalowanie zadania Stream Analytics za pomocą funkcji Azure Machine Learning Studio (klasycznych)

> [!TIP]
> Zdecydowanie zaleca się używanie [Azure Machine Learning UDF](machine-learning-udf.md) zamiast Azure Machine Learning Studio (klasycznego) UDF w celu zwiększenia wydajności i niezawodności.

W tym artykule omówiono sposób efektywnego skalowania Azure Stream Analytics zadań korzystających z funkcji Azure Machine Learning Studio (klasycznych). Aby uzyskać informacje o tym, jak skalować zadania Stream Analytics, zobacz artykuł [skalowanie zadań](stream-analytics-scale-jobs.md).

## <a name="what-is-a-studio-classic-function-in-stream-analytics"></a>Co to jest funkcja programu Studio (klasyczna) w Stream Analytics?

Funkcja Machine Learning Studio (klasyczna) w Stream Analytics może być używana jak zwykłe wywołanie funkcji w języku zapytań Stream Analytics. W tle te wywołania funkcji to w rzeczywistości żądania usługi sieci Web programu Studio (klasyczne).

Można zwiększyć przepływność żądań usługi sieci Web programu Studio (klasycznych) przez "przetwarzanie wsadowe" wielu wierszy razem w tym samym wywołaniu interfejsu API usługi sieci Web. Ta grupa jest nazywana mini-Batch. Aby uzyskać więcej informacji, zobacz [Azure Machine Learning Studio (klasyczne) usługi sieci Web](../machine-learning/classic/consume-web-services.md). Obsługa programu Studio (klasyczna) w Stream Analytics.

## <a name="configure-a-stream-analytics-job-with-studio-classic-functions"></a>Konfigurowanie zadania Stream Analytics za pomocą funkcji programu Studio (klasycznych)

Istnieją dwa parametry konfigurowania funkcji Studio (klasycznej) używanej przez zadanie Stream Analytics:

* Rozmiar wsadu wywołań funkcji programu Studio (klasycznych).
* Liczba jednostek przesyłania strumieniowego (SUs) przywidzianych dla zadania Stream Analytics.

Aby określić odpowiednie wartości dla usług SUs, zdecyduj, czy chcesz zoptymalizować opóźnienie zadania Stream Analytics lub przepływność każdego elementu SU. Usługi SUs można zawsze dodać do zadania, aby zwiększyć przepływność dobrze partycjonowanego Stream Analytics zapytania. Dodatkowe usługi SUs zwiększają koszt uruchomienia zadania.

Określ *tolerancję* opóźnienia dla zadania Stream Analytics. Zwiększenie rozmiaru partii spowoduje zwiększenie opóźnień żądań programu Studio (klasycznych) i opóźnienia zadania Stream Analytics.

Zwiększenie rozmiaru partii umożliwia zadanie Stream Analytics przetwarzać **więcej zdarzeń** z taką **samą liczbą** żądań usług sieci Web programu Studio (klasycznych). Zwiększenie opóźnienia usługi sieci Web (klasycznej) jest zwykle liniowe w celu zwiększenia rozmiaru partii. 

Ważne jest, aby wziąć pod uwagę najbardziej ekonomiczny rozmiar partii dla usługi sieci Web programu Studio (klasycznej) w każdej sytuacji. Domyślny rozmiar wsadu dla żądań usług sieci Web to 1000. Domyślny rozmiar można zmienić Stream Analytics za pomocą [interfejsu API REST](/previous-versions/azure/mt653706(v=azure.100) "Interfejs API REST usługi Stream Analytics") lub [klienta programu PowerShell dla Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

Po ustaleniu rozmiaru partii można ustawić liczbę jednostek przesyłania strumieniowego (SUs) na podstawie liczby zdarzeń, które funkcja musi przetworzyć na sekundę. Aby uzyskać więcej informacji na temat jednostek przesyłania strumieniowego, zobacz [Stream Analytics skalowanie zadań](stream-analytics-scale-jobs.md).

Co 6 usługi SUs mają 20 współbieżnych połączeń z usługą sieci Web Studio (klasyczną). Jednak 1 zadanie SU i 3 zadania SU otrzymują 20 współbieżnych połączeń.  

Jeśli aplikacja generuje 200 000 zdarzeń na sekundę, a rozmiar partii to 1000, wynikiem opóźnienia usługi sieci Web jest 200 ms. Ta częstotliwość oznacza, że każde połączenie może wykonywać pięć żądań do usługi sieci Web programu Studio (klasycznej) co sekundę. W przypadku 20 połączeń zadanie Stream Analytics może przetwarzać zdarzenia 20 000 w 200 MS i 100 000 zdarzeń w drugim.

Aby przetworzyć 200 000 zdarzeń na sekundę, zadanie Stream Analytics potrzebuje współbieżnych połączeń 40, które wychodzą do 12 usług SUs. Na poniższym diagramie przedstawiono żądania z zadania Stream Analytics do punktu końcowego usługi sieci Web programu Studio (klasycznego) — co 6 usług SUs ma 20 współbieżnych połączeń z usługą sieci Web w wersji Studio (klasyczną).

![Skalowanie Stream Analytics przy użyciu funkcji programu Studio (klasycznej) dwa przykładowe zadania](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Skalowanie Stream Analytics przy użyciu funkcji programu Studio (klasycznej) dwa przykładowe zadania")

Ogólnie rzecz biorąc, ***B** _ dla rozmiaru partii, _*_L_*_ w przypadku opóźnienia usługi sieci Web w partii o rozmiarze B w milisekundach, przepływność zadania Stream Analyticsowego z _ *_N_** SUs to:

![Stream Analytics skalowania przy użyciu formuły funkcji w programie Studio (klasycznej)](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Stream Analytics skalowania przy użyciu formuły funkcji w programie Studio (klasycznej)")

Możesz również skonfigurować "maksymalną liczbę współbieżnych wywołań" w usłudze sieci Web Studio (klasyczna). Zalecane jest ustawienie dla tego parametru wartości maksymalnej (200 obecnie).

Aby uzyskać więcej informacji na temat tego ustawienia, zapoznaj się z [artykułem skalowanie dla usług sieci Web Machine Learning Studio (klasycznych)](../machine-learning/classic/create-endpoint.md).

## <a name="example--sentiment-analysis"></a>Przykład — analiza tonacji
Poniższy przykład obejmuje zadanie Stream Analytics przy użyciu funkcji tonacji Analysis Studio (klasycznej), zgodnie z opisem w [samouczku integracji Stream Analytics Machine Learning Studio (klasyczne)](stream-analytics-machine-learning-integration-tutorial.md).

Zapytanie jest prostym w pełni partycjonowanym zapytaniem, a następnie funkcją **tonacji** , jak pokazano w następującym przykładzie:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Sprawdźmy konfigurację niezbędną do utworzenia zadania Stream Analytics, które tonacji analizę tweetów według stawki 10 000 tweetów na sekundę.

W przypadku korzystania z 1 SU można to zadanie Stream Analytics obsłużyć ten ruch? Zadanie może zachować dane wejściowe przy użyciu domyślnego rozmiaru wsadu 1000. Domyślne opóźnienie usługi sieci Web tonacji Analysis Studio (klasycznej) (z domyślnym rozmiarem partii 1000) powoduje utworzenie nie więcej niż sekund opóźnienia.

**Całkowite** lub kompleksowe opóźnienie zadania Stream Analytics zwykle trwa kilka sekund. Zapoznaj się z bardziej szczegółowym opisem tego zadania Stream Analytics, *zwłaszcza* wywołań funkcji Studio (klasycznych). W przypadku partii o rozmiarze 1000 przepływność zdarzeń 10 000 obejmuje 10 żądań do usługi sieci Web. Nawet z jednym parametrem SU jest wystarczająca liczba współbieżnych połączeń, aby pomieścić ten ruch wejściowy.

Jeśli współczynnik zdarzeń wejściowych rośnie przez 100x, zadanie Stream Analytics musi przetwarzać tweety 1 000 000 na sekundę. Dostępne są dwie opcje osiągnięcia zwiększonej skali:

1. Zwiększ rozmiar wsadu.
2. Podziel strumień wejściowy, aby przetwarzać zdarzenia równolegle.

Przy pierwszej opcji **opóźnienia** zadania rosną.

Po drugiej opcji trzeba będzie udostępnić więcej usług SUs, aby mieć więcej współbieżnych żądań usługi sieci Web (klasycznych). Ta większa liczba usług SUs zwiększa **koszt** zadania.

Przyjrzyjmy się skalowaniu przy użyciu następujących pomiarów opóźnienia dla każdego rozmiaru wsadu:

| Opóźnienie | Rozmiar partii |
| --- | --- |
| 200 MS | 1000 — partie zdarzeń lub poniżej |
| 250 MS | 5 000 — partie zdarzeń |
| 300 ms | 10 000 — partie zdarzeń |
| 500 ms | 25 000 — partie zdarzeń |

1. Korzystanie z pierwszej opcji (**bez** aprowizacji więcej usług SUs). Rozmiar wsadu można zwiększyć do **25 000**. Zwiększenie rozmiaru partii w ten sposób umożliwi zadanie przetwarzania zdarzeń 1 000 000 z 20 współbieżnych połączeń z usługą sieci Web programu Studio (klasyczną) (z opóźnieniem 500 MS na wywołanie). W związku z tym dodatkowe opóźnienie zadania Stream Analytics ze względu na żądania funkcji tonacji w stosunku do żądań usługi sieci Web programu Studio (klasyczny) zostałyby zwiększone z **200 MS** do **500 MS**. **Nie** można jednak zwiększyć rozmiaru wsadu, ponieważ usługi sieci Web programu Studio (klasycznej) wymagają, aby rozmiar ładunku żądania wynosił 4 MB lub mniejszy, a żądania usługi sieci Web przekroczyły limit czasu 100 sekund operacji.
1. Przy użyciu drugiej opcji rozmiar wsadu jest pozostawiony o 1000 z opóźnieniem usługi sieci Web 200 – MS, co 20 współbieżnych połączeń z usługą sieci Web może przetwarzać 1000 * 20 * 5 zdarzeń = 100 000 na sekundę. W celu przetworzenia zdarzeń 1 000 000 na sekundę zadanie będzie wymagało 60 programu SUs. W porównaniu do pierwszej opcji zadanie Stream Analytics zwiększy liczbę żądań wsadowych usługi sieci Web, z kolei generując zwiększony koszt.

Poniżej znajduje się tabela przepływności zadania Stream Analytics dla różnych rozmiarów usług SUs i wsadowych (liczba zdarzeń na sekundę).

| rozmiar wsadu (opóźnienie w ML) | 500 (200 ms) | 1 000 (200 ms) | 5 000 (250 MS) | 10 000 (300 ms) | 25 000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2500 |5000 |20 000 |30 000 |50 000 |
| **3 usługi SUs** |2500 |5000 |20 000 |30 000 |50 000 |
| **6 usługi SUs** |2500 |5000 |20 000 |30 000 |50 000 |
| **12 usług SUs** |5000 |10 000 |40 000 |60 000 |100 000 |
| **18 usług SUs** |7500 |15 000 |60 000 |90 000 |150 000 |
| **24 usługi SUs** |10 000 |20 000 |80 000 |120 000 |200,000 |
| **…** |… |… |… |… |… |
| **60 usługi SUs** |25 000 |50 000 |200,000 |300 000 |500 000 |

Teraz warto już wiedzieć, jak działają funkcje programu Studio (klasyczne) w Stream Analytics. Możesz również zrozumieć, że Stream Analytics zadania "ściągania" ze źródeł danych, a każdy "ściągający" zwróci partię zdarzeń dla zadania Stream Analytics do przetworzenia. Jak ten model ściągania ma wpływ na żądania usługi sieci Web programu Studio (klasycznej)?

Zwykle rozmiar wsadu ustawiany dla programu Studio (klasyczny) nie będzie dokładnie widoczny przez liczbę zdarzeń zwróconych przez każde zadanie Stream Analytics "ściągania". W takim przypadku usługa sieci Web Studio (klasyczna) jest wywoływana ze partiami "częściowe". Użycie partii częściowych pozwala uniknąć ponoszenia dodatkowych kosztów opóźnienia zadania w przypadku zdarzeń łączących od ściągania do ściągania.

## <a name="new-function-related-monitoring-metrics"></a>Nowe metryki monitorowania powiązane z funkcją
W obszarze monitorowanie zadania Stream Analytics dodano trzy dodatkowe metryki powiązane z funkcjami. Są to **żądania funkcji**, **zdarzenia funkcji** i **Nieudane żądania funkcji**, jak pokazano na ilustracji poniżej.

![Skalowanie Stream Analytics za pomocą funkcji Studio (klasyczne)](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Skalowanie Stream Analytics za pomocą funkcji Studio (klasyczne)")

Są zdefiniowane w następujący sposób:

**Żądania funkcji**: liczba żądań funkcji.

**Zdarzenia funkcji**: liczba zdarzeń w żądaniach funkcji.

**Nieudane żądania funkcji**: liczba nieudanych żądań funkcji.

## <a name="key-takeaways"></a>Wnioski Key

Aby skalować zadanie Stream Analytics przy użyciu funkcji programu Studio (klasyczny), należy wziąć pod uwagę następujące czynniki:

1. Szybkość zdarzeń wejściowych.
2. Tolerowane opóźnienie dla uruchomionego zadania Stream Analytics (i w ten sposób rozmiar wsadu żądań usługi sieci Web programu Studio (klasyczny)).
3. Zainicjowana Stream Analytics usług SUs i liczba żądań usług sieci Web programu Studio (klasycznych) (z dodatkowymi kosztami związanymi z funkcją).

W pełni partycjonowane zapytanie Stream Analytics zostało użyte jako przykład. Jeśli jest wymagana bardziej złożona kwerenda, [firma Microsoft Q&stronę pytania dla Azure Stream Analytics](/answers/topics/azure-stream-analytics.html) jest doskonałym zasobem do uzyskania dodatkowej pomocy od zespołu Stream Analytics.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat Stream Analytics, zobacz:

* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](/rest/api/streamanalytics/)
