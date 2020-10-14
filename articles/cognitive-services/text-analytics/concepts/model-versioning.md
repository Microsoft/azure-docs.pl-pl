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
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: d53c1c137e8359e3379e2403cf655c1f1dc592f5
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016403"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>Przechowywanie wersji modeli w interfejs API analizy tekstu

Wersja 3 interfejs API analizy tekstu umożliwia wybranie wersji modelu, która będzie używana w danych. Użyj opcjonalnego `model-version` parametru, aby wybrać wersję modelu w żądaniach interfejsu API. Przykład: `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`. Jeśli ten parametr nie jest określony, interfejs API domyślnie będzie miał najnowszą stabilną wersję. 

## <a name="available-versions"></a>Dostępne wersje

Skorzystaj z poniższej tabeli, aby sprawdzić, które wersje modeli są obsługiwane przez każdy punkt końcowy.


| Punkt końcowy                        | Obsługiwane wersje                                     | Najnowsza wersja |
|---------------------------------|--------------------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`                             | `2020-04-01`   |
| `/languages`                    | `2019-10-01`, `2020-07-01`, `2020-09-01`               | `2020-09-01`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`                             | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01`,              | `2020-04-01`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2020-07-01`  | `2020-07-01`   |
| `/keyphrases`                   | `2019-10-01`, `2020-07-01`                             | `2020-07-01`   |


Szczegóły dotyczące aktualizacji tych modeli można znaleźć w temacie [co nowego](../whats-new.md).

## <a name="text-analytics-for-health"></a>Analiza tekstu dla opieki zdrowotnej

[Analiza tekstu dla kontenera kondycji](../how-tos/text-analytics-for-health.md) używa oddzielnej wersji modelu niż powyższe punkty końcowe interfejsu API.  Należy pamiętać, że na obraz kontenera dostępna jest tylko jedna wersja modelu.

| Punkt końcowy                        | Tag obrazu kontenera                     | Wersja modelu |
|---------------------------------|-----------------------------------------|---------------|
| `/entities/health`              | `1.1.013530001-amd64-preview` lub Najnowsza          | `2020-09-03`  |
| `/entities/health`              | `1.1.013150001-amd64-preview`           | `2020-07-24`  |
| `/domains/health`               | `1.1.012640001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |


## <a name="next-steps"></a>Następne kroki

* [Przegląd analizy tekstu](../overview.md)
* [Analiza tonacji](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rozpoznawanie jednostek](../how-tos/text-analytics-how-to-entity-linking.md)
