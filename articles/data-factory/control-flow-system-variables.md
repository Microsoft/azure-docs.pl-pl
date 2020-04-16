---
title: Zmienne systemowe w usłudze Azure Data Factory
description: W tym artykule opisano zmienne systemowe obsługiwane przez usługę Azure Data Factory. Tych zmiennych można używać w wyrażeniach podczas definiowania jednostek usługi Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: 2690ded0ac45719cb1082c85ab535c91ad491172
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417967"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Zmienne systemowe obsługiwane przez usługę Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano zmienne systemowe obsługiwane przez usługę Azure Data Factory. Tych zmiennych można używać w wyrażeniach podczas definiowania jednostek usługi Data Factory.

## <a name="pipeline-scope"></a>Zakres rurociągu
Te zmienne systemowe mogą odwoływać się w dowolnym miejscu w potoku JSON.

| Nazwa zmiennej | Opis |
| --- | --- |
| @pipeline(). DataFactory (DaneFactory) |Nazwa fabryki danych, w ramach tej pracy uruchomiony jest potok |
| @pipeline(). Rurociągu |Nazwa rurociągu |
| @pipeline(). Nijid | Identyfikator określonego uruchomienia potoku |
| @pipeline(). Typ wyzwalacza | Typ wyzwalacza, który wywołał potok (Ręczny, Harmonogram) |
| @pipeline(). TriggerId (ida wyzwalacza)| Identyfikator wyzwalacza, który wywołuje potok |
| @pipeline(). Triggername| Nazwa wyzwalacza wywołującego potok |
| @pipeline(). Czas wyzwalania| Czas, kiedy wyzwalacz, który wywołał potoku. Czas wyzwalania jest rzeczywisty czas odpalone, a nie zaplanowany czas. Na przykład `13:20:08.0149599Z` jest zwracany zamiast`13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Zakres wyzwalacza harmonogramu
Te zmienne systemowe mogą odwoływać się w dowolnym miejscu wyzwalacza JSON, jeśli wyzwalacz jest typu: "ScheduleTrigger".

| Nazwa zmiennej | Opis |
| --- | --- |
| @trigger().scheduledTime |Czas, kiedy wyzwalacz został zaplanowany do wywołania uruchomienia potoku. Na przykład dla wyzwalacza, który uruchamia się co `2017-06-01T22:20:00Z` `2017-06-01T22:25:00Z`5 minut, ta zmienna zwróci , `2017-06-01T22:30:00Z` odpowiednio.|
| @trigger().startTime |Czas, kiedy wyzwalacz **faktycznie** uruchomiony do wywołania uruchomienia potoku. Na przykład dla wyzwalacza, który uruchamia się co 5 `2017-06-01T22:20:00.4061448Z` `2017-06-01T22:25:00.7958577Z`minut, ta zmienna może zwrócić coś takiego , `2017-06-01T22:30:00.9935483Z` odpowiednio. (Uwaga: Sygnatura czasowa jest domyślnie w formacie ISO 8601)|

## <a name="tumbling-window-trigger-scope"></a>Zakres wyzwalacza okna tumbling
Te zmienne systemowe mogą odwoływać się w dowolnym miejscu wyzwalacza JSON, jeśli wyzwalacz jest typu: "TumblingWindowTrigger".
(Uwaga: Sygnatura czasowa jest domyślnie w formacie ISO 8601)

| Nazwa zmiennej | Opis |
| --- | --- |
| @trigger().outputs.windowStartTime |Początek okna, gdy wyzwalacz został zaplanowany do wywołania uruchomienia potoku. Jeśli wyzwalacz okna tumbling ma częstotliwość "co godzinę" będzie to czas na początku godziny.|
| @trigger().outputs.windowEndTime |Koniec okna, gdy wyzwalacz został zaplanowany do wywołania uruchomienia potoku. Jeśli wyzwalacz okna tumbling ma częstotliwość "co godzinę" będzie to czas na koniec godziny.|
## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje o tym, jak te zmienne są używane w wyrażeniach, zobacz [Język wyrażenia & funkcje](control-flow-expression-language-functions.md).
