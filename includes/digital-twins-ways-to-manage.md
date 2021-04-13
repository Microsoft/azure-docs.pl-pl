---
author: baanders
description: plik dołączany do usługi Azure Digital bliźniaczych reprezentacji — sposoby zarządzania wystąpieniem
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 1f66101fc1231be2e5ce36dc348b1ca850113867
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303768"
---
W tym artykule opisano sposób wykonywania różnych operacji zarządzania przy użyciu [ **zestawu SDK** usługi Azure Digital bliźniaczych reprezentacji .NET (C#)](/dotnet/api/overview/azure/digitaltwins/management). Te same wywołania zarządzania można także połączyć przy użyciu innych zestawów SDK języka opisanych w temacie [*How to: use Digital bliźniaczych reprezentacji API and SDK*](../articles/digital-twins/how-to-use-apis-sdks.md).

> [!TIP] 
> Należy pamiętać, że wszystkie metody zestawu SDK są dostępne w wersjach synchronicznych i asynchronicznych. W przypadku wywołań stronicowania metody asynchroniczne zwracają, `AsyncPageable<T>` gdy zwracane są wersje synchroniczne `Pageable<T>` .

Kolejną opcją zarządzania jest wywołanie [**interfejsów API REST**](/rest/api/azure-digitaltwins/) usługi Azure Digital bliźniaczych reprezentacji dla tego obszaru tematu bezpośrednio za pomocą klienta REST, takiego jak Poster. Aby uzyskać instrukcje, jak to zrobić, zobacz [*How to: wykonywanie żądań za pomocą programu Poster*](../articles/digital-twins/how-to-use-postman.md).

Na koniec można wykonać te same operacje zarządzania przy użyciu **interfejsu wiersza polecenia** usługi Azure Digital bliźniaczych reprezentacji. Aby dowiedzieć się więcej o korzystaniu z interfejsu wiersza polecenia, zobacz [*How to: Use the Azure Digital bliźniaczych reprezentacji CLI*](../articles/digital-twins/how-to-use-cli.md).