---
title: 'Szybki Start: pobieranie zamierzeń przy użyciu interfejsów API REST — LUIS'
description: W tym przewodniku szybki start interfejsu API REST Użyj dostępnej publicznej aplikacji LUIS, aby określić zamiar użytkownika z tekstu konwersacji.
ms.topic: quickstart
ms.date: 05/18/2020
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 90d98e56e53e28991fb5aada9eab5a7e9c2e69c3
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654275"
---
# <a name="quickstart-get-intent-with-rest-apis"></a>Szybki Start: pobieranie zamierzeń przy użyciu interfejsów API REST

W tym przewodniku szybki start użyjesz aplikacji LUIS do określenia zamiaru użytkownika z tekstu konwersacjowego. Wyślij zamiar użytkownika jako tekst do punktu końcowego przewidywania HTTP aplikacji Pizza. W punkcie końcowym LUIS stosuje model aplikacji Pizza w celu przeanalizowania tekstu w języku naturalnym pod kątem znaczenia, określania ogólnego zamiaru i wyodrębnienia danych związanych z domeną podmiotu aplikacji.

W tym przewodniku Szybki start jest używany punkt końcowy interfejsu API REST. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API punktu końcowego](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78).

W przypadku tego artykułu jest potrzebne bezpłatne konto [usługi LUIS](https://www.luis.ai).

<a name="create-luis-subscription-key"></a>

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-intent-rest-csharp.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-intent-rest-go.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-intent-rest-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-intent-rest-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-intent-rest-python.md)]
::: zone-end
