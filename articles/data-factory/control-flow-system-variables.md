---
title: Zmienne systemowe w Azure Data Factory
description: W tym artykule opisano zmienne systemowe obsługiwane przez Azure Data Factory. Te zmienne można używać w wyrażeniach podczas definiowania jednostek Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: cb19b7e8c5271e1106e69c98d5bd17a1bcb822bf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385289"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Zmienne systemowe obsługiwane przez Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano zmienne systemowe obsługiwane przez Azure Data Factory. Te zmienne można używać w wyrażeniach podczas definiowania jednostek Data Factory.

## <a name="pipeline-scope"></a>Zakres potoku
Te zmienne systemowe mogą być przywoływane w dowolnym miejscu w kodzie JSON potoku.

| Nazwa zmiennej | Opis |
| --- | --- |
| @pipeline(). DataFactory |Nazwa fabryki danych, w której działa potok |
| @pipeline(). Proces |Nazwa potoku |
| @pipeline(). RunId |Identyfikator określonego uruchomienia potoku |
| @pipeline(). TriggerType |Typ wyzwalacza, który wywołał potok (na przykład `ScheduleTrigger` , `BlobEventsTrigger` ). Aby uzyskać listę obsługiwanych typów wyzwalaczy, zobacz temat [wykonywanie potoku i Wyzwalacze w Azure Data Factory](concepts-pipeline-execution-triggers.md). Typ wyzwalacza `Manual` wskazuje, że potok został wyzwolony ręcznie. |
| @pipeline(). TriggerId|Identyfikator wyzwalacza, który wywołał potok |
| @pipeline(). TriggerName|Nazwa wyzwalacza, który wywołał potok |
| @pipeline(). TriggerTime|Godzina uruchomienia wyzwalacza, który wywołał potok. Jest to godzina, o której uruchamiany jest **wyzwalacz w** celu wywołania uruchomienia potoku i może się nieco różnić od zaplanowanego czasu wyzwalacza.  |

>[!NOTE]
>Zmienne systemowe daty/godziny powiązane z wyzwalaczem (w obu zakresach potoków i wyzwalaczy) zwracają daty UTC w formacie ISO 8601, na przykład `2017-06-01T22:20:00.4061448Z` .

## <a name="schedule-trigger-scope"></a>Zakres wyzwalania harmonogramu
Te zmienne systemowe mogą być przywoływane w dowolnym miejscu w kodzie JSON wyzwalacza dla wyzwalaczy typu [ScheduleTrigger](concepts-pipeline-execution-triggers.md#schedule-trigger).

| Nazwa zmiennej | Opis |
| --- | --- |
| @trigger().scheduledTime |Godzina, o której wyzwalacz został zaplanowany do wywołania uruchomienia potoku. |
| @trigger(). startTime |Godzina, o której uruchamiany **jest wyzwalacz,** aby wywołać uruchomienie potoku. Może się nieco różnić od zaplanowanego czasu wyzwalacza. |

## <a name="tumbling-window-trigger-scope"></a>Zakres wyzwalacza okna wirowania
Te zmienne systemowe mogą być przywoływane w dowolnym miejscu w kodzie JSON wyzwalacza dla wyzwalaczy typu [TumblingWindowTrigger](concepts-pipeline-execution-triggers.md#tumbling-window-trigger).

| Nazwa zmiennej | Opis |
| --- | --- |
| @trigger(). Output. windowStartTime |Początek okna skojarzonego z uruchomieniem wyzwalacza. |
| @trigger(). Output. windowEndTime |Koniec okna skojarzonego z uruchomionym wyzwalaczem. |
| @trigger().scheduledTime |Godzina, o której wyzwalacz został zaplanowany do wywołania uruchomienia potoku. |
| @trigger(). startTime |Godzina, o której uruchamiany **jest wyzwalacz,** aby wywołać uruchomienie potoku. Może się nieco różnić od zaplanowanego czasu wyzwalacza. |

## <a name="event-based-trigger-scope"></a>Zakres wyzwalaczy oparty na zdarzeniach
Te zmienne systemowe mogą być przywoływane w dowolnym miejscu w kodzie JSON wyzwalacza dla wyzwalaczy typu [BlobEventsTrigger](concepts-pipeline-execution-triggers.md#event-based-trigger).

| Nazwa zmiennej | Opis |
| --- | --- |
| @triggerBody(). fileName  |Nazwa pliku, którego tworzenie lub usuwanie spowodowało uruchomienie wyzwalacza.   |
| @triggerBody(). nazwa_folderu  |Ścieżka do folderu, który zawiera plik określony przez `@triggerBody().fileName` . Pierwszy segment ścieżki folderu to nazwa kontenera Blob Storage platformy Azure.  |
| @trigger(). startTime |Godzina uruchomienia wyzwalacza w celu wywołania uruchomienia potoku. |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje o tym, jak te zmienne są używane w wyrażeniach, zobacz [Language Expression & Functions](control-flow-expression-language-functions.md).
