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
ms.openlocfilehash: a62c25aa5fd9d25b7330dde89c8544e79785b9d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103495453"
---
# <a name="quickstart-add-chat-to-your-app"></a>Szybki Start: Dodawanie rozmowy do aplikacji

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


Rozpocznij pracę z usługami Azure Communications Services przy użyciu biblioteki klienta rozmowy usług komunikacyjnych w celu dodania rozmowy w czasie rzeczywistym do aplikacji. W tym przewodniku szybki start użyjemy biblioteki klienta czatu, aby utworzyć wątki rozmów, które umożliwiają użytkownikom konwersację ze sobą. Aby dowiedzieć się więcej na temat pojęć związanych z rozmowami, zapoznaj się z [dokumentacją dotyczącą koncepcji rozmowy](../../concepts/chat/concepts.md)

::: zone pivot="programming-language-javascript"
[!INCLUDE [Chat with JavaScript client library](./includes/chat-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Chat with Python client library](./includes/chat-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Chat with Java client library](./includes/chat-java.md)]
::: zone-end

::: zone pivot="programming-language-android"
[!INCLUDE [Chat with Android client library](./includes/chat-android.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Chat with C# client library](./includes/chat-csharp.md)]
::: zone-end

::: zone pivot="programming-language-swift"
[!INCLUDE [Chat with iOS client library](./includes/chat-swift.md)]
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
 - Zapoznaj się z [biblioteką klienta rozmowy](../../concepts/chat/sdk-features.md)
