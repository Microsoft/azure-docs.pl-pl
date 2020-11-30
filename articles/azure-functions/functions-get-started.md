---
title: Wprowadzenie do usługi Azure Functions
description: Wykonaj pierwsze kroki w kierunku pracy z Azure Functions.
author: craigshoemaker
ms.topic: overview
ms.date: 11/19/2020
ms.author: cshoe
zone_pivot_groups: programming-languages-set-functions-lang-workers
ms.openlocfilehash: 78fd13825becfa186960a0dfd3dee83c312c9bcf
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326951"
---
# <a name="getting-started-with-azure-functions"></a>Wprowadzenie do usługi Azure Functions

## <a name="introduction"></a>Wprowadzenie

[Azure Functions](./functions-overview.md) umożliwia zaimplementowanie logiki systemu w łatwych do dyspozycji blokach kodu. Te bloki kodu są nazywane "funkcjami".

Aby rozpocząć, Skorzystaj z następujących zasobów.

::: zone pivot="programming-language-csharp"

| Akcja | Zasoby |
| --- | --- |
| **Tworzenie pierwszej funkcji** | Przy użyciu jednego z następujących narzędzi:<br><br><li>[Program Visual Studio](./functions-create-your-first-function-visual-studio.md)<li>[Visual Studio Code](./create-first-function-vs-code-csharp.md)<li>[Wiersz polecenia](./create-first-function-cli-csharp.md) |
| **Zobacz uruchomioną funkcję** | <li>[Przeglądarka przykładów platformy Azure](/samples/browse/?expanded=azure&languages=csharp&products=azure-functions)<li>[Biblioteka społeczności platformy Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=C%23) |
| **Eksploruj Interaktywny samouczek**| <li>[Wybór najlepszej technologii bezserwerowej platformy Azure dla scenariusza biznesowego](/learn/modules/serverless-fundamentals/)<li>[Dobrze zaprojektowane środowisko — wydajność](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Wykonywanie funkcji platformy Azure przy użyciu wyzwalaczy](/learn/modules/execute-azure-function-with-triggers/) <br><br>Zobacz Microsoft Learn, aby zapoznać się z [pełną listą interaktywnych samouczków](/learn/browse/?expanded=azure&products=azure-functions).|
| **Więcej szczegółowych informacji** | <li>Dowiedz się [, jak funkcje automatycznie zwiększają lub zmniejszają](./functions-scale.md) liczbę wystąpień w celu dopasowania do żądania<li>Poznaj różne dostępne [metody wdrażania](./functions-deployment-technologies.md)<li>Korzystanie z wbudowanych [narzędzi do monitorowania](./functions-monitoring.md) , które ułatwiają analizowanie funkcji<li>Przeczytaj [odwołanie do języka C#](./functions-dotnet-class-library.md)|

::: zone-end

::: zone pivot="programming-language-java"
| Akcja | Zasoby |
| --- | --- |
| **Tworzenie pierwszej funkcji** | Przy użyciu jednego z następujących narzędzi:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-java.md)<li>[Funkcja Java/Maven z terminalem/wierszem polecenia](./create-first-function-cli-java.md)<li>[Gradle](./functions-create-first-java-gradle.md)<li>[Eclipse](./functions-create-maven-eclipse.md)<li>[POMYSŁ IntelliJ](./functions-create-maven-intellij.md) |
| **Zobacz uruchomioną funkcję** | <li>[Przeglądarka przykładów platformy Azure](/samples/browse/?expanded=azure&languages=java&products=azure-functions)<li>[Biblioteka społeczności platformy Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Java) |
| **Eksploruj Interaktywny samouczek**| <li>[Wybór najlepszej technologii bezserwerowej platformy Azure dla scenariusza biznesowego](/learn/modules/serverless-fundamentals/)<li>[Dobrze zaprojektowane środowisko — wydajność](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Tworzenie aplikacji przy użyciu wtyczki Maven dla usługi Azure Functions](/learn/modules/develop-azure-functions-app-with-maven-plugin/) <br><br>Zobacz Microsoft Learn, aby zapoznać się z [pełną listą interaktywnych samouczków](/learn/browse/?expanded=azure&products=azure-functions).|
| **Więcej szczegółowych informacji** | <li>Dowiedz się [, jak funkcje automatycznie zwiększają lub zmniejszają](./functions-scale.md) liczbę wystąpień w celu dopasowania do żądania<li>Poznaj różne dostępne [metody wdrażania](./functions-deployment-technologies.md)<li>Korzystanie z wbudowanych [narzędzi do monitorowania](./functions-monitoring.md) , które ułatwiają analizowanie funkcji<li>Przeczytaj [Informacje o języku Java](./functions-reference-java.md)|
::: zone-end

::: zone pivot="programming-language-javascript"
| Akcja | Zasoby |
| --- | --- |
| **Tworzenie pierwszej funkcji** | Przy użyciu jednego z następujących narzędzi:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-node.md)<li>[Node.js terminalu/wiersza polecenia](./create-first-function-cli-java.md) |
| **Zobacz uruchomioną funkcję** | <li>[Przeglądarka przykładów platformy Azure](/samples/browse/?expanded=azure&languages=javascript%2ctypescript&products=azure-functions)<li>[Biblioteka społeczności platformy Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=JavaScript%2CTypeScript) |
| **Eksploruj Interaktywny samouczek** | <li>[Wybór najlepszej technologii bezserwerowej platformy Azure dla scenariusza biznesowego](/learn/modules/serverless-fundamentals/)<li>[Dobrze zaprojektowane środowisko — wydajność](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Tworzenie bezserwerowych interfejsów API przy użyciu usługi Azure Functions](/learn/modules/build-api-azure-functions/)<li>[Tworzenie logiki bezserwerowej za pomocą usługi Azure Functions](/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Refaktoryzacja interfejsów API Node.js i Express do bezserwerowych interfejsów API za pomocą usługi Azure Functions](/learn/modules/shift-nodejs-express-apis-serverless/) <br><br>Zobacz Microsoft Learn, aby zapoznać się z [pełną listą interaktywnych samouczków](/learn/browse/?expanded=azure&products=azure-functions).|
| **Więcej szczegółowych informacji** | <li>Dowiedz się [, jak funkcje automatycznie zwiększają lub zmniejszają](./functions-scale.md) liczbę wystąpień w celu dopasowania do żądania<li>Poznaj różne dostępne [metody wdrażania](./functions-deployment-technologies.md)<li>Korzystanie z wbudowanych [narzędzi do monitorowania](./functions-monitoring.md) , które ułatwiają analizowanie funkcji<li>Przeczytaj Skorowidz języka [JavaScript](./functions-reference-node.md) lub [TypeScript](./functions-reference-node.md#typescript)|
::: zone-end

::: zone pivot="programming-language-powershell"
| Akcja | Zasoby |
| --- | --- |
| **Tworzenie pierwszej funkcji** | <li>Używanie [Visual Studio Code](./create-first-function-vs-code-powershell.md) |
| **Zobacz uruchomioną funkcję** | <li>[Przeglądarka przykładów platformy Azure](/samples/browse/?expanded=azure&languages=powershell&products=azure-functions)<li>[Biblioteka społeczności platformy Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=PowerShell) |
| **Eksploruj Interaktywny samouczek** | <li>[Wybór najlepszej technologii bezserwerowej platformy Azure dla scenariusza biznesowego](/learn/modules/serverless-fundamentals/)<li>[Dobrze zaprojektowane środowisko — wydajność](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Tworzenie bezserwerowych interfejsów API przy użyciu usługi Azure Functions](/learn/modules/build-api-azure-functions/)<li>[Tworzenie logiki bezserwerowej za pomocą usługi Azure Functions](/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Wykonywanie funkcji platformy Azure przy użyciu wyzwalaczy](/learn/modules/execute-azure-function-with-triggers/) <br><br>Zobacz Microsoft Learn, aby zapoznać się z [pełną listą interaktywnych samouczków](/learn/browse/?expanded=azure&products=azure-functions).|
| **Więcej szczegółowych informacji** | <li>Dowiedz się [, jak funkcje automatycznie zwiększają lub zmniejszają](./functions-scale.md) liczbę wystąpień w celu dopasowania do żądania<li>Poznaj różne dostępne [metody wdrażania](./functions-deployment-technologies.md)<li>Korzystanie z wbudowanych [narzędzi do monitorowania](./functions-monitoring.md) , które ułatwiają analizowanie funkcji<li>Przeczytaj [informacje dotyczące języka programu PowerShell](./functions-reference-powershell.md))|
::: zone-end

::: zone pivot="programming-language-python"
| Akcja | Zasoby |
| --- | --- |
| **Tworzenie pierwszej funkcji** | Przy użyciu jednego z następujących narzędzi:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-csharp.md?pivots=programming-language-python)<li>[Terminal/wiersz polecenia](./create-first-function-cli-csharp.md?pivots=programming-language-python) |
| **Zobacz uruchomioną funkcję** | <li>[Przeglądarka przykładów platformy Azure](/samples/browse/?expanded=azure&languages=python&products=azure-functions)<li>[Biblioteka społeczności platformy Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Python) |
| **Eksploruj Interaktywny samouczek** | <li>[Wybór najlepszej technologii bezserwerowej platformy Azure dla scenariusza biznesowego](/learn/modules/serverless-fundamentals/)<li>[Dobrze zaprojektowane środowisko — wydajność](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Tworzenie bezserwerowych interfejsów API przy użyciu usługi Azure Functions](/learn/modules/build-api-azure-functions/)<li>[Tworzenie logiki bezserwerowej za pomocą usługi Azure Functions](/learn/modules/create-serverless-logic-with-azure-functions/) <br><br>Zobacz Microsoft Learn, aby zapoznać się z [pełną listą interaktywnych samouczków](/learn/browse/?expanded=azure&products=azure-functions).|
| **Więcej szczegółowych informacji** | <li>Dowiedz się [, jak funkcje automatycznie zwiększają lub zmniejszają](./functions-scale.md) liczbę wystąpień w celu dopasowania do żądania<li>Poznaj różne dostępne [metody wdrażania](./functions-deployment-technologies.md)<li>Korzystanie z wbudowanych [narzędzi do monitorowania](./functions-monitoring.md) , które ułatwiają analizowanie funkcji<li>Przeczytaj [Informacje o języku Python](./functions-reference-python.md)|
::: zone-end

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat anatomii aplikacji Azure Functions](./functions-reference.md)
