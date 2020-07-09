---
title: Określ wersję modelu w analiza tekstu v3
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak określić model interfejs API analizy tekstu używany w danych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 04/21/2020
ms.author: aahi
ms.openlocfilehash: c9c598d385768e604d9b42e3ea055629e6ae2866
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108553"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>Przechowywanie wersji modeli w interfejs API analizy tekstu

Wersja 3 interfejs API analizy tekstu umożliwia wybranie wersji modelu, która będzie używana w danych. Użyj opcjonalnego `model-version` parametru, aby wybrać wersję modelu w żądaniach interfejsu API. Na przykład: `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`. Jeśli ten parametr nie jest określony, interfejs API domyślnie będzie miał najnowszą stabilną wersję. 

## <a name="available-versions"></a>Dostępne wersje

Skorzystaj z poniższej tabeli, aby sprawdzić, które wersje modeli są obsługiwane przez każdy punkt końcowy.


| Endpoint                        | Obsługiwane wersje                       | Najnowsza wersja |
|---------------------------------|------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`               | `2020-04-01`   |
| `/languages`                    | `2019-10-01`                             | `2019-10-01`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`               | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01` | `2020-04-01`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01` | `2020-04-01`   |
| `/keyphrases`                   | `2019-10-01`                             | `2019-10-01`   |


Szczegóły dotyczące aktualizacji tych modeli można znaleźć w temacie [co nowego](../whats-new.md).

## <a name="text-analytics-for-health"></a>analiza tekstu dla kondycji

[Analiza tekstu dla kontenera kondycji](../how-tos/text-analytics-for-health.md) używa oddzielnej wersji modelu niż powyższe punkty końcowe interfejsu API.  Należy pamiętać, że na obraz kontenera dostępna jest tylko jedna wersja modelu.

| Endpoint                        | Tag obrazu kontenera                     | Wersja modelu |
|---------------------------------|-----------------------------------------|---------------|
| `/domains/health`               | `1.1.012640001-amd64-preview`lub Najnowsza | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |




## <a name="next-steps"></a>Następne kroki

* [Przegląd analiza tekstu](../overview.md)
* [Analiza tonacji](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rozpoznawanie jednostek](../how-tos/text-analytics-how-to-entity-linking.md)
