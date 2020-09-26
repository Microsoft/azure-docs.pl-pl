---
title: Skalowanie funkcji Machine Learning w Azure Stream Analytics
description: W tym artykule opisano sposób skalowania Stream Analytics zadań korzystających z funkcji Machine Learning przez skonfigurowanie partycji i jednostek strumienia.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2020
ms.openlocfilehash: a0cc70f5bf994e03088511a0d10796746a434bd7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91300313"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Skalowanie zadania Stream Analytics za pomocą funkcji Azure Machine Learning Studio (klasycznych)

> [!TIP]
> Zdecydowanie zaleca się używanie [Azure Machine Learning UDF](machine-learning-udf.md) zamiast Azure Machine Learning Studio (klasycznego) UDF w celu zwiększenia wydajności i niezawodności.

W tym artykule omówiono sposób efektywnego skalowania Azure Stream Analytics zadań korzystających z funkcji Azure Machine Learning. Aby uzyskać informacje o tym, jak skalować zadania Stream Analytics, zobacz artykuł [skalowanie zadań](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Co to jest funkcja Azure Machine Learning w Stream Analytics?

Funkcja Machine Learning w Stream Analytics może być używana jak zwykłe wywołanie funkcji w języku zapytań Stream Analytics. W tle te wywołania funkcji są jednak w rzeczywistości Azure Machine Learning żądania usługi sieci Web.

Można zwiększyć przepływność Machine Learning żądań usługi sieci Web przez "wsadowe" wiele wierszy razem w tym samym wywołaniu interfejsu API usługi sieci Web. Ta grupa jest nazywana mini-Batch. Aby uzyskać więcej informacji, zobacz [Azure Machine Learning Studio (klasyczne) usługi sieci Web](../machine-learning/classic/consume-web-services.md). Obsługa Azure Machine Learning Studio (klasyczny) w Stream Analytics jest w wersji zapoznawczej.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Konfigurowanie zadania Stream Analytics za pomocą funkcji Machine Learning

Istnieją dwa parametry konfigurowania funkcji Machine Learning używanej przez zadanie Stream Analytics:

* Rozmiar wsadu wywołań funkcji Machine Learning.
* Liczba jednostek przesyłania strumieniowego (SUs) przywidzianych dla zadania Stream Analytics.

Aby określić odpowiednie wartości dla usług SUs, zdecyduj, czy chcesz zoptymalizować opóźnienie zadania Stream Analytics lub przepływność każdego elementu SU. Usługi SUs można zawsze dodać do zadania, aby zwiększyć przepływność dobrze partycjonowanego Stream Analytics zapytania. Dodatkowe usługi SUs zwiększają koszt uruchomienia zadania.

Określ *tolerancję* opóźnienia dla zadania Stream Analytics. Zwiększenie rozmiaru partii spowoduje zwiększenie opóźnienia żądań Azure Machine Learning i opóźnienia zadania Stream Analytics.

Zwiększenie rozmiaru partii umożliwia zadanie Stream Analytics przetwarzać **więcej zdarzeń** o **tej samej liczbie** Machine Learning żądań usług sieci Web. Zwiększenie opóźnienia usługi sieci Web Machine Learning jest zwykle liniowe w celu zwiększenia rozmiaru partii. 

Ważne jest, aby wziąć pod uwagę najbardziej ekonomiczny rozmiar partii dla usługi sieci Web Machine Learning w każdej sytuacji. Domyślny rozmiar wsadu dla żądań usług sieci Web to 1000. Domyślny rozmiar można zmienić Stream Analytics za pomocą [interfejsu API REST](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "Interfejs API REST usługi Stream Analytics") lub [klienta programu PowerShell dla Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

Po ustaleniu rozmiaru partii można ustawić liczbę jednostek przesyłania strumieniowego (SUs) na podstawie liczby zdarzeń, które funkcja musi przetworzyć na sekundę. Aby uzyskać więcej informacji na temat jednostek przesyłania strumieniowego, zobacz [Stream Analytics skalowanie zadań](stream-analytics-scale-jobs.md).

Co 6 usługi SUs otrzymują 20 współbieżnych połączeń z usługą sieci Web Machine Learning. Jednak 1 zadanie SU i 3 zadania SU otrzymują 20 współbieżnych połączeń.  

Jeśli aplikacja generuje 200 000 zdarzeń na sekundę, a rozmiar partii to 1000, wynikiem opóźnienia usługi sieci Web jest 200 ms. Ta częstotliwość oznacza, że każde połączenie może wykonywać pięć żądań do usługi sieci Web Machine Learning każda sekunda. W przypadku 20 połączeń zadanie Stream Analytics może przetwarzać zdarzenia 20 000 w 200 MS i 100 000 zdarzeń w drugim.

Aby przetworzyć 200 000 zdarzeń na sekundę, zadanie Stream Analytics potrzebuje współbieżnych połączeń 40, które wychodzą do 12 usług SUs. Na poniższym diagramie przedstawiono żądania z zadania Stream Analytics do punktu końcowego Machine Learning usługi sieci Web — co 6 usług SUs ma 20 współbieżnych połączeń do usługi sieci Web programu Machine Learning z maksymalną liczbą.

![Skalowanie Stream Analytics za pomocą funkcji Machine Learning dwa przykładowe zadania](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Skalowanie Stream Analytics za pomocą funkcji Machine Learning dwa przykładowe zadania")

Ogólnie rzecz biorąc, ***B*** dla rozmiaru partii, ***L*** dla opóźnienia usługi sieci Web w usłudze Batch o rozmiarze B w milisekundach, przepływność zadania Stream Analytics ***przy użyciu usługi*** SUs to:

![Skalowanie Stream Analytics za pomocą formuły funkcji Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Skalowanie Stream Analytics za pomocą formuły funkcji Machine Learning")

Możesz również skonfigurować "maksymalną liczbę współbieżnych wywołań" w usłudze sieci Web Machine Learning. Zalecane jest ustawienie dla tego parametru wartości maksymalnej (200 obecnie).

Aby uzyskać więcej informacji na temat tego ustawienia, zapoznaj się z [artykułem skalowanie dla Machine Learning usług sieci Web](../machine-learning/classic/create-endpoint.md).

## <a name="example--sentiment-analysis"></a>Przykład — analiza tonacji
Poniższy przykład obejmuje zadanie Stream Analytics przy użyciu funkcji Machine Learning analizy tonacji, zgodnie z opisem w [samouczku integracji Stream Analytics Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

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

W przypadku korzystania z 1 SU można to zadanie Stream Analytics obsłużyć ten ruch? Zadanie może zachować dane wejściowe przy użyciu domyślnego rozmiaru wsadu 1000. Domyślne opóźnienie usługi sieci Web tonacji Analysis Machine Learning (z domyślnym rozmiarem partii 1000) powoduje utworzenie nie więcej niż sekund opóźnienia.

**Całkowite** lub kompleksowe opóźnienie zadania Stream Analytics zwykle trwa kilka sekund. Zapoznaj się z bardziej szczegółowym opisem tego zadania Stream Analytics, w *szczególności* z wywołaniami funkcji Machine Learning. W przypadku partii o rozmiarze 1000 przepływność zdarzeń 10 000 obejmuje 10 żądań do usługi sieci Web. Nawet z jednym parametrem SU jest wystarczająca liczba współbieżnych połączeń, aby pomieścić ten ruch wejściowy.

Jeśli współczynnik zdarzeń wejściowych rośnie przez 100x, zadanie Stream Analytics musi przetwarzać tweety 1 000 000 na sekundę. Dostępne są dwie opcje osiągnięcia zwiększonej skali:

1. Zwiększ rozmiar wsadu.
2. Podziel strumień wejściowy, aby przetwarzać zdarzenia równolegle.

Przy pierwszej opcji **opóźnienia** zadania rosną.

Po drugiej opcji trzeba będzie udostępnić więcej usług SUs, aby mieć więcej współbieżnych żądań usługi sieci Web Machine Learning. Ta większa liczba usług SUs zwiększa **koszt**zadania.

Przyjrzyjmy się skalowaniu przy użyciu następujących pomiarów opóźnienia dla każdego rozmiaru wsadu:

| Opóźnienie | Rozmiar partii |
| --- | --- |
| 200 MS | 1000 — partie zdarzeń lub poniżej |
| 250 MS | 5 000 — partie zdarzeń |
| 300 ms | 10 000 — partie zdarzeń |
| 500 ms | 25 000 — partie zdarzeń |

1. Korzystanie z pierwszej opcji (**bez** aprowizacji więcej usług SUs). Rozmiar wsadu można zwiększyć do **25 000**. Zwiększenie rozmiaru partii w ten sposób umożliwi zadanie przetwarzania zdarzeń 1 000 000 z 20 współbieżnych połączeń z usługą sieci Web Machine Learning (z opóźnieniem 500 MS na wywołanie). W związku z tym dodatkowe opóźnienie zadania Stream Analytics ze względu na żądania funkcji tonacji względem żądań usługi sieci Web Machine Learning zostanie zwiększone z **200 MS** do **500 MS**. **Nie** można jednak zwiększyć rozmiaru wsadu, ponieważ usługi sieci Web Machine Learning wymagają, aby rozmiar ładunku żądania wynosił 4 MB lub mniejszy, a żądania usługi sieci Web przekroczyły limit czasu 100 sekund operacji.
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

Teraz warto już wiedzieć, jak działają funkcje Machine Learning w Stream Analytics. Możesz również zrozumieć, że Stream Analytics zadania "ściągania" ze źródeł danych, a każdy "ściągający" zwróci partię zdarzeń dla zadania Stream Analytics do przetworzenia. Jak ten model ściągania ma wpływ na żądania usługi sieci Web Machine Learning?

Zwykle rozmiar wsadu ustawiany dla funkcji Machine Learning nie będzie dokładnie widoczny dla liczby zdarzeń zwróconych przez poszczególne zadania Stream Analytics "ściągania". W takim przypadku usługa sieci Web Machine Learning jest wywoływana z "częściową" partiami. Użycie partii częściowych pozwala uniknąć ponoszenia dodatkowych kosztów opóźnienia zadania w przypadku zdarzeń łączących od ściągania do ściągania.

## <a name="new-function-related-monitoring-metrics"></a>Nowe metryki monitorowania powiązane z funkcją
W obszarze monitorowanie zadania Stream Analytics dodano trzy dodatkowe metryki powiązane z funkcjami. Są to **żądania funkcji**, **zdarzenia funkcji** i **Nieudane żądania funkcji**, jak pokazano na ilustracji poniżej.

![Skalowanie Stream Analytics za pomocą metryk funkcji Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Skalowanie Stream Analytics za pomocą metryk funkcji Machine Learning")

Są zdefiniowane w następujący sposób:

**Żądania funkcji**: liczba żądań funkcji.

**Zdarzenia funkcji**: liczba zdarzeń w żądaniach funkcji.

**Nieudane żądania funkcji**: liczba nieudanych żądań funkcji.

## <a name="key-takeaways"></a>Wnioski Key

Aby skalować zadanie Stream Analytics za pomocą funkcji Machine Learning, należy wziąć pod uwagę następujące czynniki:

1. Szybkość zdarzeń wejściowych.
2. Tolerowane opóźnienie dla uruchomionego zadania Stream Analytics (i w ten sposób rozmiar wsadu Machine Learning żądań usługi sieci Web).
3. Zainicjowana Stream Analytics usług SUs i liczba żądań usługi sieci Web Machine Learning (dodatkowe koszty związane z funkcją).

W pełni partycjonowane zapytanie Stream Analytics zostało użyte jako przykład. Jeśli jest wymagana bardziej złożona kwerenda, [firma Microsoft Q&stronę pytania dla Azure Stream Analytics](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html) jest doskonałym zasobem do uzyskania dodatkowej pomocy od zespołu Stream Analytics.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat Stream Analytics, zobacz:

* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
