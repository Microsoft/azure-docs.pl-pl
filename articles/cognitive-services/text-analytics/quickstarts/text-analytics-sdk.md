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
ms.date: 09/09/2020
ms.author: aahi
keywords: Wyszukiwanie tekstu, analiza tonacjiów, analizy tekstu
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: 0fbd8b92846f6436128f64680a1fa9b77763a2c8
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710784"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>Szybki Start: korzystanie z biblioteki klienta analiza tekstu

Skorzystaj z tego artykułu, aby rozpocząć pracę z biblioteką klienta analiza tekstu. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla tekstu wyszukiwania.

Użyj biblioteki klienta analiza tekstu do wykonania:

* Analiza tonacji
* Wykrywanie języka
* Rozpoznawanie jednostek
* Wyodrębnianie kluczowych fraz

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * Najnowsza stabilna wersja interfejs API analizy tekstu to `3.0` .
>    * Pamiętaj, aby postępować zgodnie z instrukcjami dotyczącymi używanej wersji.
> * Kod w tym artykule używa metod synchronicznych i niezabezpieczonych magazynów poświadczeń z przyczyn uproszczenia. W przypadku scenariuszy produkcyjnych zaleca się użycie wsadowych metod asynchronicznych w celu zapewnienia wydajności i skalowalności. Zapoznaj się z dokumentacją referencyjną poniżej.

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Najnowsza stabilna wersja interfejs API analizy tekstu to `3.0` .
> * Kod w tym artykule używa metod synchronicznych i niezabezpieczonych magazynów poświadczeń z przyczyn uproszczenia. W przypadku scenariuszy produkcyjnych zaleca się użycie wsadowych metod asynchronicznych w celu zapewnienia wydajności i skalowalności. Zapoznaj się z dokumentacją referencyjną poniżej.

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
> * Kod w tym artykule używa metod synchronicznych i niezabezpieczonych magazynów poświadczeń z przyczyn uproszczenia. W przypadku scenariuszy produkcyjnych zaleca się użycie wsadowych metod asynchronicznych w celu zapewnienia wydajności i skalowalności. Zapoznaj się z dokumentacją referencyjną poniżej. 

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>Obsługa dodatkowych języków

Jeśli kliknięto tę kartę, prawdopodobnie nie widzisz przewodnika Szybki Start w ulubionym języku programowania. Nie martw się, możemy korzystać z dodatkowych przewodników Szybki Start. Użyj tabeli, aby znaleźć odpowiedni przykład dla języka programowania.

| Język | Dostępna wersja | 
|----------|------------------------|
| Ruby     | [Wersja 2,1](ruby-sdk.md) | 
| Przejdź       | [Wersja 2,1](go-sdk.md) | 

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
* [Wykrywanie języka](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Rozpoznawanie języka](../how-tos/text-analytics-how-to-language-detection.md)
