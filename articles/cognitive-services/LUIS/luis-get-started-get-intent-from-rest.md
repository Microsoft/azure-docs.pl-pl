---
title: 'Szybki start: Uzyskaj intencję za pomocą interfejsów API REST — USŁUGA LUIS'
description: W tym interfejsie Szybki start interfejsu API REST użyj dostępnej publicznej aplikacji usługi LUIS, aby określić intencję użytkownika na podstawie tekstu konwersacyjnego.
ms.topic: quickstart
ms.date: 04/20/2020
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 03f1c1539233872d9b98fab317c9caf997d5df69
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733267"
---
# <a name="quickstart-get-intent-with-rest-apis"></a>Szybki start: uzyskaj intencję dzięki interfejsom API REST

W tym przewodniku Szybki start użyjesz dostępnej publicznie aplikacji LUIS, aby określić intencję użytkownika w tekście konwersacji. Wyślesz intencję użytkownika jako tekst do punktu końcowego przewidywania HTTP aplikacji publicznej. W punkcie końcowym usługa LUIS zastosuje model aplikacji publicznej, aby przeanalizować tekst w języku naturalnym pod kątem znaczenia, określając ogólną intencję i wyodrębniając dane dotyczące domeny podmiotu aplikacji.

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

