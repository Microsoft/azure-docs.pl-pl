---
title: Wykonywanie zapytania dotyczącego danych — Azure Time Series Insights Gen2 | Microsoft Docs
description: Omówienie pojęć związanych z wykonywaniem zapytań dotyczących danych i interfejsu API REST w Azure Time Series Insights Gen2.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: e9491757852b42faef40c107540e0ce3da3c7f99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650905"
---
# <a name="querying-data-from-azure-time-series-insights-gen2"></a>Wykonywanie zapytania dotyczącego danych z Azure Time Series Insights Gen2

Azure Time Series Insights Gen2 umożliwia wykonywanie zapytań dotyczących danych dotyczących zdarzeń i metadanych przechowywanych w środowisku za pośrednictwem publicznych interfejsów API. Te interfejsy API są również używane przez [eksploratora Azure Time Series INSIGHTS TSI](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).

Trzy podstawowe kategorie interfejsu API są dostępne w Azure Time Series Insights Gen2:

* **Interfejsy API środowiska**: te interfejsy API umożliwiają zapytania dotyczące środowiska Azure Time Series Insights Gen2. Mogą one służyć do zbierania listy środowisk, do których obiekt wywołujący ma dostęp i metadanych środowiska.
* **Interfejsy api Model-Query (TSM-Q)**: włącza operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) dla metadanych przechowywanych w modelu szeregów czasowych środowiska. Mogą one służyć do uzyskiwania dostępu do wystąpień, typów i hierarchii oraz do ich edycji.
* **Interfejsy API kwerendy szeregów czasowych (TSQ)**: umożliwiają pobieranie danych telemetrycznych lub zdarzeń, które są rejestrowane przez dostawcę źródłowego i umożliwiają wykonywanie obliczeń i agregacji danych przy użyciu zaawansowanych funkcji skalarnych i agregujących.

Azure Time Series Insights Gen2 korzysta z zaawansowanego, opartego na ciągach języka wyrażeń, [wyrażenia szeregów czasowych (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax), do obliczeń wyrażających w [zmiennych szeregów czasowych](./concepts-variables.md).

## <a name="azure-time-series-insights-gen2-apis-overview"></a>Omówienie interfejsów API Azure Time Series Insights Gen2

Obsługiwane są następujące podstawowe interfejsy API.

[![Omówienie kwerendy szeregów czasowych](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Interfejsy API środowiska

* [Interfejs API środowiska Get](https://docs.microsoft.com/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/listbyenvironment)environments: zwraca listę środowisk, do których obiekt wywołujący ma autoryzację dostęp.
* [Pobierz interfejs API dostępności środowisk](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/getavailability): Zwraca rozkład liczby zdarzeń przez sygnaturę czasową zdarzenia `$ts` . Ten interfejs API pomaga ustalić, czy w środowisku znajdują się jakieś zdarzenia, zwracając liczbę zdarzeń, które zostały podzielone na przedziały czasu (jeśli istnieją).
* [Pobierz interfejs API schematu zdarzeń](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/geteventschema): zwraca metadane schematu zdarzenia dla danego zakresu wyszukiwania. Ten interfejs API pomaga pobrać wszystkie metadane i właściwości dostępne w schemacie dla danego przedziału wyszukiwania.

## <a name="time-series-model-query-tsm-q-apis"></a>Interfejsy API Model-Query (TSM-Q) dla szeregów czasowych

Większość z tych interfejsów API obsługuje operację wykonywania wsadowego, aby włączyć operacje wsadowe CRUD na wielu jednostkach modelu szeregów czasowych:

* [Interfejs API ustawień modelu](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis): Włącza funkcję *Get* i *patch* dla typu domyślnego oraz nazwy modelu środowiska.
* [Interfejs API](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#types-api): włącza CRUD w typach szeregów czasowych i skojarzonych z nimi zmiennych.
* [Interfejs API hierarchii](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#hierarchies-api): włącza CRUD w hierarchiach szeregów czasowych i skojarzonych z nimi ścieżkach pól.
* [Interfejs API wystąpień](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#instances-api): włącza CRUD dla wystąpień szeregów czasowych i skojarzonych z nimi pól wystąpień. Ponadto interfejs API wystąpień obsługuje następujące operacje:
  * [Wyszukiwanie](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search): Pobiera częściową listę trafień podczas wyszukiwania wystąpień szeregów czasowych na podstawie atrybutów wystąpienia.
  * [Sugeruj](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest): wyszukuje i sugeruje częściową listę trafień podczas wyszukiwania wystąpień szeregów czasowych na podstawie atrybutów wystąpienia.

## <a name="time-series-query-tsq-apis"></a>Interfejsy API zapytań szeregów czasowych (TSQ)

Te interfejsy API są dostępne w ramach rozwiązania magazynu wielowarstwowego (grzane i zimne). Parametry adresu URL zapytania służą do określania [typu magazynu](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters) , na którym ma zostać wykonane zapytanie:

* [Interfejs API pobierania zdarzeń](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents): umożliwia wykonywanie zapytań i pobieranie zdarzeń nieprzetworzonych i skojarzonych sygnatur czasowych zdarzeń, które są rejestrowane w Azure Time Series Insights Gen2 od dostawcy źródłowego. Ten interfejs API umożliwia pobieranie nieprzetworzonych zdarzeń dla danego identyfikatora szeregów czasowych i zakresu wyszukiwania. Ten interfejs API obsługuje stronicowanie w celu pobrania kompletnego zestawu danych odpowiedzi dla wybranych danych wejściowych.

  > [!IMPORTANT]

  > * W ramach [nadchodzących zmian zasad spłaszczania i ucieczki JSON](https://docs.microsoft.com/azure/time-series-insights/ingestion-rules-update)tablice będą przechowywane jako typ **dynamiczny** . Właściwości ładunku przechowywane jako ten typ są **dostępne tylko za pomocą interfejsu API pobierania zdarzeń**.

* [Pobierz interfejs API serii](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries): umożliwia wykonywanie zapytań i pobieranie wartości obliczanych oraz skojarzonych sygnatur czasowych zdarzeń przez stosowanie obliczeń zdefiniowanych przez zmienne w zdarzeniach nieprzetworzonych. Te zmienne można definiować w modelu szeregów czasowych lub w zapytaniu. Ten interfejs API obsługuje stronicowanie w celu pobrania kompletnego zestawu danych odpowiedzi dla wybranych danych wejściowych.

* [Interfejs API serii agregujących](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries): umożliwia wykonywanie zapytań i pobieranie zagregowanych wartości oraz skojarzonych sygnatur czasowych interwałów przez stosowanie obliczeń zdefiniowanych przez zmienne dla nieprzetworzonych zdarzeń. Te zmienne można definiować w modelu szeregów czasowych lub w zapytaniu. Ten interfejs API obsługuje stronicowanie w celu pobrania kompletnego zestawu danych odpowiedzi dla wybranych danych wejściowych.
  
  Dla określonego przedziału wyszukiwania i interwału ten interfejs API zwraca zagregowaną odpowiedź na interwał dla każdej zmiennej dla identyfikatora szeregów czasowych. Liczba interwałów w zestawie danych odpowiedzi jest obliczana przez liczenie taktów epoki (liczbę milisekund, które upłynęły od systemu UNIX epoki 1 stycznia 1st, 1970) i dzielenie taktów przez rozmiar zakresu interwału określonego w zapytaniu.

  Sygnatury czasowe zwracane w zestawie odpowiedzi mają granice interwału po lewej stronie, a nie zdarzenia próbkowane z interwału.

## <a name="next-steps"></a>Następne kroki

* Przeczytaj więcej na temat różnych zmiennych, które można zdefiniować w [modelu szeregów czasowych](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm).
* Przeczytaj więcej na temat wykonywania zapytań dotyczących danych w [eksploratorze Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).
