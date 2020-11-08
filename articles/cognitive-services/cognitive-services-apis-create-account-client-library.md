---
title: Tworzenie zasobu Cognitive Services przy użyciu biblioteki klienta zarządzania Azure
titleSuffix: Azure Cognitive Services
description: Tworzenie zasobów Cognitive Services platformy Azure i zarządzanie nimi przy użyciu biblioteki klienta zarządzania Azure.
services: cognitive-services
keywords: usługi poznawcze, analiza poznawcze, rozwiązania poznawcze, usługi AI
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: pafarley
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: 1fd748a0184c1718ac9450aaca3e2db1a185051a
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368954"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-management-client-library"></a>Szybki Start: Tworzenie zasobu Cognitive Services przy użyciu biblioteki klienta zarządzania Azure

Ten przewodnik Szybki Start umożliwia tworzenie zasobów usługi Azure Cognitive Services i zarządzanie nimi przy użyciu biblioteki klienta zarządzania Azure.

Usługa Azure Cognitive Services to usługi w chmurze z interfejsami API REST, a także dostępne zestawy SDK dla deweloperów, które ułatwiają deweloperom tworzenie analiz poznawczych w aplikacjach, bez konieczności bezpośredniego sztucznej analizy danych lub wiedzy. Dzięki platformie Azure Cognitive Services deweloperzy mogą łatwo dodawać funkcje poznawcze do swoich aplikacji przy użyciu rozwiązań poznawczych, które widzą, słyszą, mówią, wiedzą, a nawet z przyczyn.

Poszczególne usługi AI są reprezentowane przez [zasoby](../azure-resource-manager/management/manage-resources-portal.md) platformy Azure utworzone w ramach subskrypcji platformy Azure. Po utworzeniu zasobu można użyć wygenerowanych kluczy i punktów końcowych w celu uwierzytelniania aplikacji.

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](includes/quickstarts/management-csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](includes/quickstarts/management-java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](includes/quickstarts/management-node.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](includes/quickstarts/management-python.md)]

::: zone-end