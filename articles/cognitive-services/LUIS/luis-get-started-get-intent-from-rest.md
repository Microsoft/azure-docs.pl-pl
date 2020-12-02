---
title: Jak uzyskać zamiar przy użyciu interfejsu API REST
description: W tym artykule Użyj dostępnej publicznej aplikacji LUIS do określenia zamiaru użytkownika z tekstu konwersacji.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: b49d90ffe2a262895727fdd4ef5f8a7950104102
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96437018"
---
# <a name="how-to-get-an-intent-using-the-rest-apis"></a>Jak uzyskać zamiar przy użyciu interfejsów API REST

W tym artykule zostanie użyta aplikacja LUIS do określenia zamiaru użytkownika z tekstu konwersacjowego. Wyślij zamiar użytkownika jako tekst do punktu końcowego przewidywania HTTP aplikacji Pizza. W punkcie końcowym LUIS stosuje model aplikacji Pizza w celu przeanalizowania tekstu w języku naturalnym pod kątem znaczenia, określania ogólnego zamiaru i wyodrębnienia danych związanych z domeną podmiotu aplikacji.

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
