---
title: Interfejs API REST bibliotek klienta usługi Advisor metryk
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki Start, aby połączyć aplikacje z interfejsem API usługi Metrics Advisor z platformy Azure Cognitive Services.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 10/14/2020
ms.author: mbullwin
zone_pivot_groups: programming-languages-metrics-monitor
ms.openlocfilehash: 5b3df5f4b41b2beeec68b667863f6ca7715df47b
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186856"
---
# <a name="quickstart-use-the-client-libraries-or-rest-apis-to-customize-your-solution"></a>Szybki Start: korzystanie z bibliotek klienta lub interfejsów API REST w celu dostosowania rozwiązania

Rozpocznij pracę z interfejsem API REST usługi Advisor lub bibliotekami klienckimi. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Użyj klasyfikatora metryk do wykonania:

* Dodaj strumieniowe źródło danych ze źródła danych
* Sprawdź stan pozyskiwania
* Konfigurowanie wykrywania i alertów 
* Wykonywanie zapytania dotyczącego wyników wykrywania anomalii
* Diagnozowanie anomalii


::: zone pivot="programming-language-csharp"

[!INCLUDE [REST API quickstart](../includes/quickstarts/csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [REST API quickstart](../includes/quickstarts/java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [REST API quickstart](../includes/quickstarts/javascript.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [REST API quickstart](../includes/quickstarts/python.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

- [Używanie portalu internetowego](web-portal.md)
- [Dołączanie źródeł danych](../how-tos/onboard-your-data.md)
    - [Zarządzanie strumieniowymi źródłami danych](../how-tos/manage-data-feeds.md)
    - [Konfiguracje dla różnych źródeł danych](../data-feeds-from-different-sources.md)
- [Konfigurowanie metryk i dostrajanie konfiguracji wykrywania](../how-tos/configure-metrics.md)
- [Dostosowywanie wykrywania anomalii przy użyciu opinii](../how-tos/anomaly-feedback.md)