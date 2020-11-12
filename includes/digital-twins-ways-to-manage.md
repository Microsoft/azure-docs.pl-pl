---
author: baanders
description: plik dołączany do usługi Azure Digital bliźniaczych reprezentacji — sposoby zarządzania wystąpieniem
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 47de00780fb259e7476c16dea9b7c63bc512a211
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533909"
---
W tym artykule opisano sposób wykonywania różnych operacji zarządzania przy użyciu [ **zestawu SDK** usługi Azure Digital bliźniaczych reprezentacji .NET (C#)](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true). Te same wywołania zarządzania można także połączyć przy użyciu innych zestawów SDK języka opisanych w temacie [*How to: use Digital bliźniaczych reprezentacji API and SDK*](../articles/digital-twins/how-to-use-apis-sdks.md).

> [!TIP] 
> Należy pamiętać, że wszystkie metody zestawu SDK są dostępne w wersjach synchronicznych i asynchronicznych. W przypadku wywołań stronicowania metody asynchroniczne zwracają, `AsyncPageable<T>` gdy zwracane są wersje synchroniczne `Pageable<T>` .

Kolejną opcją zarządzania jest wywołanie [**interfejsów API REST**](/rest/api/azure-digitaltwins/) usługi Azure Digital bliźniaczych reprezentacji dla tego obszaru tematu bezpośrednio.

Na koniec można wykonać te same operacje zarządzania przy użyciu **interfejsu wiersza polecenia** usługi Azure Digital bliźniaczych reprezentacji. Aby dowiedzieć się więcej o korzystaniu z interfejsu wiersza polecenia, zobacz [*How to: Use the Azure Digital bliźniaczych reprezentacji CLI*](../articles/digital-twins/how-to-use-cli.md).