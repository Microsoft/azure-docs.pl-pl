---
title: Wprowadzenie do funkcji okna Azure Stream Analytics
description: W tym artykule opisano cztery funkcje okna (wirowania, przeskoku, przesuwane, sesja), które są używane w Azure Stream Analytics zadaniach.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/16/2020
ms.openlocfilehash: feea44741fbc2c81d781f0bba12b280abdb9f68a
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020252"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Wprowadzenie do funkcji okna Stream Analytics

W scenariuszach przesyłania strumieniowego w czasie wykonywanie operacji na danych zawartych w oknach czasowych jest typowym wzorcem. Stream Analytics ma natywną obsługę funkcji okna, dzięki czemu deweloperzy mogą tworzyć złożone zadania przetwarzania strumieniowego z minimalnym nakładem pracy.

Dostępne są pięć rodzajów danych czasowych do wyboru: [**wirowania**](/stream-analytics-query/tumbling-window-azure-stream-analytics), [**przeskoku**](/stream-analytics-query/hopping-window-azure-stream-analytics), [**przesuwane**](/stream-analytics-query/sliding-window-azure-stream-analytics), [**sesji**](/stream-analytics-query/session-window-azure-stream-analytics)i [**migawki**](/stream-analytics-query/snapshot-window-azure-stream-analytics) systemu Windows.  Używasz funkcji okna w klauzuli [**Group by**](/stream-analytics-query/group-by-azure-stream-analytics) składni zapytania w zadaniach Stream Analytics. Można również agregować zdarzenia w wielu oknach przy użyciu funkcji [ **Windows ()**](/stream-analytics-query/windows-azure-stream-analytics).

Wszystkie operacje [okna](/stream-analytics-query/windowing-azure-stream-analytics) są wyprowadzane na **końcu** okna. Należy pamiętać, że po uruchomieniu zadania usługi Stream Analytics można określić *godzinę rozpoczęcia danych wyjściowych zadania* , a system automatycznie pobierze poprzednie zdarzenia w strumieniach przychodzących w celu wygenerowania pierwszego okna w określonym czasie. na przykład po rozpoczęciu pracy z opcją *Now* rozpocznie się natychmiastowe wyemitowanie danych. Dane wyjściowe okna będą pojedynczym zdarzeniem na podstawie użytej funkcji agregującej. Zdarzenie wyjściowe będzie miało sygnaturę czasową końca okna, a wszystkie funkcje okna są definiowane ze stałą długością. 

![Pojęcia dotyczące funkcji okna Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Okno wirowania
Funkcje okna wirowania są używane do segmentacji strumienia danych w różne segmenty czasu i wykonywania na nich funkcji, takich jak przykład poniżej. Kluczowe wyróżniki okna wirowania: okna te się powtarzają, nie nakładają się, a zdarzenia nie mogą należeć do więcej niż jednego okna wirowania.

![Stream Analytics okno wirowania](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Okno przeskoku
Funkcje okien powtarzanych powodują przeskoki w czasie do przodu o stały okres. Można je łatwo traktować jako wirowania okna, które mogą się nakładać i być emitowane częściej niż rozmiar okna. Zdarzenia mogą należeć do więcej niż jednego zestawu wyników okna przeskoku. Aby okno przeskoku było takie samo jak okno wirowania, określ rozmiar przeskoku tak samo jak rozmiar okna. 

![Stream Analytics okno przeskoku](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Okno przewijania

Okna przewijania, w przeciwieństwie do okien wirowania lub przeskoku, zdarzenia wyjściowe tylko dla punktów w czasie, gdy zawartość okna rzeczywiście ulega zmianie. Innymi słowy, gdy zdarzenie wchodzi lub opuszcza okno. Dlatego każde okno ma co najmniej jedno zdarzenie. Podobnie jak w przypadku systemu Windows przeskoku, zdarzenia mogą należeć do więcej niż jednego przedziału.

![Stream Analytics przesuwanego okna](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Okno sesji
Funkcja okna sesji grupuje zdarzenia, które docierają w podobnym czasie, filtrując okresy, w których nie ma żadnych danych. Okno ma trzy główne parametry: limit czasu, maksymalny czas trwania i klucz partycjonowania (opcjonalny).

![Okno sesji Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Okno sesji rozpoczyna się w momencie wystąpienia pierwszego zdarzenia. Jeśli inne zdarzenie wystąpi w określonym limicie czasu od ostatniego pozyskanego zdarzenia, okno rozszerza się, aby uwzględnić nowe zdarzenie. W przeciwnym razie, jeśli żadne zdarzenia nie wystąpią w limicie czasu, okno zostanie zamknięte po upływie limitu czasu.

Jeśli zdarzenia będą wykonywane w określonym limicie czasu, okno sesji będzie nadal rozszerzane do czasu osiągnięcia maksymalnego czasu trwania. Interwał sprawdzania maksymalny czas trwania jest ustawiony na taki sam rozmiar jak określony maksymalny czas trwania. Na przykład jeśli maksymalny czas trwania wynosi 10, sprawdza, czy okno przekracza maksymalny czas trwania w t = 0, 10, 20, 30 itd.

Po podaniu klucza partycji zdarzenia są pogrupowane w oknie klucz i sesja są stosowane niezależnie do poszczególnych grup. Partycjonowanie jest przydatne w przypadku, gdy potrzebne są różne okna sesji dla różnych użytkowników lub urządzeń.

## <a name="snapshot-window"></a>Okno migawki

Tworzenie migawek zdarzeń grup systemu Windows, które mają taką samą sygnaturę czasową. W przeciwieństwie do innych typów okien, które wymagają określonej funkcji okna (takiej jak [SessionWindow ()](/stream-analytics-query/session-window-azure-stream-analytics), można zastosować okno migawki przez dodanie elementu System. timestamp () do klauzuli Group by.

![Okno migawki Stream Analytics](media/stream-analytics-window-functions/snapshot.png)

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](/rest/api/streamanalytics/)
