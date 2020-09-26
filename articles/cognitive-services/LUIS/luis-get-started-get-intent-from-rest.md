---
title: 'Szybki Start: pobieranie zamierzeń przy użyciu interfejsów API REST — LUIS'
description: W tym przewodniku szybki start interfejsu API REST Użyj dostępnej publicznej aplikacji LUIS, aby określić zamiar użytkownika z tekstu konwersacji.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: d03ebc1ec5730f1905ce1ee5b9c484312f5aeee1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316353"
---
# <a name="quickstart-get-intent-with-rest-apis"></a>Szybki Start: pobieranie zamierzeń przy użyciu interfejsów API REST

W tym przewodniku szybki start użyjesz aplikacji LUIS do określenia zamiaru użytkownika z tekstu konwersacjowego. Wyślij zamiar użytkownika jako tekst do punktu końcowego przewidywania HTTP aplikacji Pizza. W punkcie końcowym LUIS stosuje model aplikacji Pizza w celu przeanalizowania tekstu w języku naturalnym pod kątem znaczenia, określania ogólnego zamiaru i wyodrębnienia danych związanych z domeną podmiotu aplikacji.

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
