---
title: Szybki Start — Dodawanie rozmowy do aplikacji
titleSuffix: An Azure Communication Services quickstart
description: Ten przewodnik Szybki Start przedstawia sposób dodawania rozmowy z usługami komunikacyjnymi do aplikacji.
author: fanche
manager: phans
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python-swift-android
ms.openlocfilehash: 362c8c875ab878b23cffae45f4c653255f489c81
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107052"
---
# <a name="quickstart-add-chat-to-your-app"></a>Szybki Start: Dodawanie rozmowy do aplikacji

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


Rozpocznij pracę z usługami Azure Communication Services przy użyciu zestawu SDK rozmowy usług komunikacyjnych w celu dodania rozmowy w czasie rzeczywistym do aplikacji. W tym przewodniku szybki start użyjemy zestawu SDK programu Chat do tworzenia wątków rozmów, które umożliwiają użytkownikom konwersację ze sobą. Aby dowiedzieć się więcej na temat pojęć związanych z rozmowami, zapoznaj się z [dokumentacją dotyczącą koncepcji rozmowy](../../concepts/chat/concepts.md)

::: zone pivot="programming-language-javascript"
[!INCLUDE [Chat with JavaScript SDK](./includes/chat-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Chat with Python SDK](./includes/chat-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Chat with Java SDK](./includes/chat-java.md)]
::: zone-end

::: zone pivot="programming-language-android"
[!INCLUDE [Chat with Android SDK](./includes/chat-android.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Chat with C# SDK](./includes/chat-csharp.md)]
::: zone-end

::: zone pivot="programming-language-swift"
[!INCLUDE [Chat with iOS SDK](./includes/chat-swift.md)]
::: zone-end

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług komunikacyjnych, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów. Dowiedz się więcej o [czyszczeniu zasobów](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start zawarto informacje na temat wykonywania tych instrukcji:

> [!div class="checklist"]
> * Tworzenie klienta czatu
> * Tworzenie wątku dla dwóch użytkowników
> * Wyślij komunikat do wątku
> * Odbieranie komunikatów z wątku
> * Usuwanie użytkowników z wątku

> [!div class="nextstepaction"]
> [Wypróbuj aplikację Chat Hero](../../samples/chat-hero-sample.md)

Możesz również chcieć:

 - Informacje o [pojęciach związanych z rozmowami](../../concepts/chat/concepts.md)
 - Zapoznaj się z [zestawem SDK rozmowy](../../concepts/chat/sdk-features.md)
