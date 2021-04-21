---
title: 'Szybki start: wyszukiwanie tekstu przy użyciu biblioteki analiza tekstu klienta'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki start, aby przeprowadzić analizę tonacji i nie tylko przy użyciu interfejsu API analiza tekstu z Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 04/19/2021
ms.author: aahi
keywords: wyszukiwanie tekstu, analiza tonacji, analiza tekstu
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: 527936e1da385a3622358fe12efa0f87af4b3fd9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765098"
---
# <a name="quickstart-use-the-text-analytics-client-library-and-rest-api"></a>Szybki start: korzystanie z biblioteki analiza tekstu klienta i interfejsu API REST

Skorzystaj z tego artykułu, aby rozpocząć pracę z biblioteką analiza tekstu klienta i interfejsem API REST. Wykonaj następujące kroki, aby wypróbować przykładowy kod wyszukiwania tekstu:

* Analiza tonacji
* Wyszukiwanie opinii
* Wykrywanie języka
* Rozpoznawanie jednostek
* Rozpoznawanie osobistych informacji identyfikujących
* Wyodrębnianie kluczowych fraz


::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * Najnowsza stabilna wersja interfejsu API analiza tekstu to `3.0` .
>    * Pamiętaj, aby postępować zgodnie tylko z instrukcjami dotyczącymi wersji, z których korzystasz.
> * Kod w tym artykule używa metod synchronicznych i niezabędnego magazynu poświadczeń dla uproszczenia. W przypadku scenariuszy produkcyjnych zalecamy używanie wsadowych metod asynchronicznych w celu oceny wydajności i skalowalności. Zapoznaj się z dokumentacją referencyjną poniżej.
> * Jeśli chcesz używać poleceń w analiza tekstu kondycji lub operacji asynchronicznych, zobacz przykłady dla języka [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) lub [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics) w witrynie Github

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Najnowsza stabilna wersja interfejsu API analiza tekstu to `3.0` .
> * Kod w tym artykule używa metod synchronicznych i niezabędnego magazynu poświadczeń dla uproszczenia. W przypadku scenariuszy produkcyjnych zalecamy używanie wsadowych metod asynchronicznych w celu oceny wydajności i skalowalności. Zapoznaj się z dokumentacją referencyjną poniżej.
Jeśli chcesz używać poleceń w analiza tekstu kondycji lub operacji asynchronicznych, zobacz przykłady dla języka [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) lub [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics) w witrynie Github

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * Najnowsza stabilna wersja interfejsu API analiza tekstu to `3.0` .
>    * Pamiętaj, aby postępować zgodnie z instrukcjami dotyczącymi wersji, z których korzystasz.
> * Kod w tym artykule używa metod synchronicznych i niezabędnego magazynu poświadczeń dla uproszczenia. W przypadku scenariuszy produkcyjnych zalecamy używanie wsadowych metod asynchronicznych w celu oceny wydajności i skalowalności. Zapoznaj się z dokumentacją referencyjną poniżej.
> * Możesz również uruchomić tę wersję biblioteki klienta analiza tekstu [w przeglądarce.](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md)

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * Najnowsza stabilna wersja interfejsu API analiza tekstu to `3.0` .
>    * Pamiętaj, aby postępować zgodnie z instrukcjami dotyczącymi wersji, z których korzystasz.
> * Kod w tym artykule używa metod synchronicznych i niezabędnego magazynu poświadczeń dla uproszczenia. W przypadku scenariuszy produkcyjnych zalecamy użycie wsadowych metod asynchronicznych w celu oceny wydajności i skalowalności. Zapoznaj się z dokumentacją referencyjną poniżej. Jeśli chcesz używać poleceń analiza tekstu kondycji lub operacji asynchronicznych, zobacz przykłady w witrynie Github dla języków [C#,](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics) [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) lub [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="rest-api"

> [!IMPORTANT]
> * Najnowsza stabilna wersja interfejsu API analiza tekstu to `3.0` .
>    * Pamiętaj, aby postępować zgodnie z instrukcjami dotyczącymi wersji, z których korzystasz.

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services zasobów, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie wszystkich innych skojarzonych z nią zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Eksplorowanie rozwiązania](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Przegląd analizy tekstu](../overview.md)
* [Analiza tonacji](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rozpoznawanie jednostek](../how-tos/text-analytics-how-to-entity-linking.md)
* [Wykrywanie języka](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Rozpoznawanie języka](../how-tos/text-analytics-how-to-language-detection.md)
