---
title: 'Szybki Start: wyszukiwanie tekstu przy użyciu biblioteki klienta analiza tekstu'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki Start, aby przeprowadzić analizę tonacji i nie tylko przy użyciu interfejs API analizy tekstu z usługi Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 01/20/2021
ms.author: aahi
keywords: Wyszukiwanie tekstu, analiza tonacjiów, analizy tekstu
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: 78d2f0a7b247ac6a9b7bdf5f4fc8b6c7b5ef6eea
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090714"
---
# <a name="quickstart-use-the-text-analytics-client-library-and-rest-api"></a>Szybki Start: korzystanie z biblioteki klienta analiza tekstu i interfejsu API REST

Skorzystaj z tego artykułu, aby rozpocząć pracę z biblioteką klienta analiza tekstu i interfejsem API REST. Wykonaj następujące kroki, aby wypróbować przykłady kodu dla tekstu wyszukiwania:

* Analiza tonacji
* Wyszukiwanie opinii
* Wykrywanie języka
* Rozpoznawanie jednostek
* Rozpoznawanie informacji osobistych
* Wyodrębnianie kluczowych fraz


::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * Najnowsza stabilna wersja interfejs API analizy tekstu to `3.0` .
>    * Pamiętaj, aby postępować zgodnie z instrukcjami dotyczącymi używanej wersji.
> * Kod w tym artykule używa metod synchronicznych i niezabezpieczonych magazynów poświadczeń z przyczyn uproszczenia. W przypadku scenariuszy produkcyjnych zaleca się użycie wsadowych metod asynchronicznych w celu zapewnienia wydajności i skalowalności. Zapoznaj się z dokumentacją referencyjną poniżej.
> * Jeśli chcesz używać analiza tekstu do obsługi kondycji lub operacji asynchronicznych, zobacz przykłady w witrynie GitHub dla [języków C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) lub [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Najnowsza stabilna wersja interfejs API analizy tekstu to `3.0` .
> * Kod w tym artykule używa metod synchronicznych i niezabezpieczonych magazynów poświadczeń z przyczyn uproszczenia. W przypadku scenariuszy produkcyjnych zaleca się użycie wsadowych metod asynchronicznych w celu zapewnienia wydajności i skalowalności. Zapoznaj się z dokumentacją referencyjną poniżej.
Jeśli chcesz używać analiza tekstu do obsługi kondycji lub operacji asynchronicznych, zobacz przykłady w witrynie GitHub dla [języków C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) lub [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * Najnowsza stabilna wersja interfejs API analizy tekstu to `3.0` .
>    * Pamiętaj, aby postępować zgodnie z instrukcjami dotyczącymi używanej wersji.
> * Kod w tym artykule używa metod synchronicznych i niezabezpieczonych magazynów poświadczeń z przyczyn uproszczenia. W przypadku scenariuszy produkcyjnych zaleca się użycie wsadowych metod asynchronicznych w celu zapewnienia wydajności i skalowalności. Zapoznaj się z dokumentacją referencyjną poniżej.
> * Tę wersję biblioteki klienta analiza tekstu można również uruchomić [w przeglądarce](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * Najnowsza stabilna wersja interfejs API analizy tekstu to `3.0` .
>    * Pamiętaj, aby postępować zgodnie z instrukcjami dotyczącymi używanej wersji.
> * Kod w tym artykule używa metod synchronicznych i niezabezpieczonych magazynów poświadczeń z przyczyn uproszczenia. W przypadku scenariuszy produkcyjnych zaleca się użycie wsadowych metod asynchronicznych w celu zapewnienia wydajności i skalowalności. Zapoznaj się z dokumentacją referencyjną poniżej. Jeśli chcesz używać analiza tekstu do obsługi kondycji lub operacji asynchronicznych, zobacz przykłady w witrynie GitHub dla [języków C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) lub [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="rest-api"

> [!IMPORTANT]
> * Najnowsza stabilna wersja interfejs API analizy tekstu to `3.0` .
>    * Pamiętaj, aby postępować zgodnie z instrukcjami dotyczącymi używanej wersji.

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Eksplorowanie rozwiązania](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Przegląd analizy tekstu](../overview.md)
* [Analiza tonacji](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rozpoznawanie jednostek](../how-tos/text-analytics-how-to-entity-linking.md)
* [Wykryj język](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Rozpoznawanie języka](../how-tos/text-analytics-how-to-language-detection.md)
