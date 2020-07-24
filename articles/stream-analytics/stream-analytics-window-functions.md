---
title: Wprowadzenie do funkcji okna Azure Stream Analytics
description: W tym artykule opisano cztery funkcje okna (wirowania, przeskoku, przesuwane, sesja), które są używane w Azure Stream Analytics zadaniach.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: fd741a9401a3936ec02939562e8e85046e829d31
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075926"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Wprowadzenie do funkcji okna Stream Analytics

W scenariuszach przesyłania strumieniowego w czasie wykonywanie operacji na danych zawartych w oknach czasowych jest typowym wzorcem. Stream Analytics ma natywną obsługę funkcji okna, dzięki czemu deweloperzy mogą tworzyć złożone zadania przetwarzania strumieniowego z minimalnym nakładem pracy.

Istnieją cztery rodzaje danych czasowych do wyboru: [**wirowania**](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics), [**przeskoku**](https://docs.microsoft.com/stream-analytics-query/hopping-window-azure-stream-analytics), [**przesuwane**](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics)i okna [**sesji**](https://docs.microsoft.com/stream-analytics-query/session-window-azure-stream-analytics) .  Używasz funkcji okna w klauzuli [**Group by**](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) składni zapytania w zadaniach Stream Analytics. Można również agregować zdarzenia w wielu oknach przy użyciu funkcji [ **Windows ()** ](https://docs.microsoft.com/stream-analytics-query/windows-azure-stream-analytics).

Wszystkie operacje [okna](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) są wyprowadzane na **końcu** okna. Dane wyjściowe okna będą pojedynczym zdarzeniem na podstawie użytej funkcji agregującej. Zdarzenie wyjściowe będzie miało sygnaturę czasową końca okna, a wszystkie funkcje okna są definiowane ze stałą długością. 

![Pojęcia dotyczące funkcji okna Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Okno wirowania
Funkcje okna wirowania są używane do segmentacji strumienia danych w różne segmenty czasu i wykonywania na nich funkcji, takich jak przykład poniżej. Kluczowe wyróżniki okna wirowania: okna te się powtarzają, nie nakładają się, a zdarzenia nie mogą należeć do więcej niż jednego okna wirowania.

![Stream Analytics okno wirowania](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Okno przeskoku
Funkcje okien powtarzanych powodują przeskoki w czasie do przodu o stały okres. Okna te przypominają okna wirowania, ale mogą się nakładać, więc zdarzenia mogą należeć do więcej niż jednego zestawu wyników okien powtarzanych. Aby okno przeskoku było takie samo jak okno wirowania, określ rozmiar przeskoku tak samo jak rozmiar okna. 

![Stream Analytics okno przeskoku](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Okno przewijania

Okna przewijania, w przeciwieństwie do okien wirowania lub przeskoku, zdarzenia wyjściowe tylko dla punktów w czasie, gdy zawartość okna rzeczywiście ulega zmianie. Innymi słowy, gdy zdarzenie wchodzi lub opuszcza okno. Każde okno ma co najmniej jedno zdarzenie, takie jak w przypadku systemu Windows przeskoku, zdarzenia mogą należeć do więcej niż jednego przedziału ruchomego

![Stream Analytics przesuwanego okna](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Okno sesji
Funkcja okna sesji grupuje zdarzenia, które docierają w podobnym czasie, filtrując okresy, w których nie ma żadnych danych. Okno ma trzy główne parametry: limit czasu, maksymalny czas trwania i klucz partycjonowania (opcjonalny).

![Okno sesji Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Okno sesji rozpoczyna się w momencie wystąpienia pierwszego zdarzenia. Jeśli inne zdarzenie wystąpi w określonym limicie czasu od ostatniego pozyskanego zdarzenia, okno rozszerza się, aby uwzględnić nowe zdarzenie. W przeciwnym razie, jeśli żadne zdarzenia nie wystąpią w limicie czasu, okno zostanie zamknięte po upływie limitu czasu.

Jeśli zdarzenia będą wykonywane w określonym limicie czasu, okno sesji będzie nadal rozszerzane do czasu osiągnięcia maksymalnego czasu trwania. Interwał sprawdzania maksymalny czas trwania jest ustawiony na taki sam rozmiar jak określony maksymalny czas trwania. Na przykład jeśli maksymalny czas trwania wynosi 10, sprawdza, czy okno przekracza maksymalny czas trwania w t = 0, 10, 20, 30 itd.

Po podaniu klucza partycji zdarzenia są pogrupowane w oknie klucz i sesja są stosowane niezależnie do poszczególnych grup. Partycjonowanie jest przydatne w przypadku, gdy potrzebne są różne okna sesji dla różnych użytkowników lub urządzeń.

## <a name="snapshot-window"></a>Okno migawki

Tworzenie migawek zdarzeń grup systemu Windows, które mają taką samą sygnaturę czasową. W przeciwieństwie do innych typów okien, które wymagają określonej funkcji okna (takiej jak [SessionWindow ()](https://docs.microsoft.com/stream-analytics-query/session-window-azure-stream-analytics), można zastosować okno migawki przez dodanie elementu System. timestamp () do klauzuli Group by.

![Okno migawki Stream Analytics](media/stream-analytics-window-functions/snapshot.png)

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

