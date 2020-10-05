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
ms.openlocfilehash: e8628d051db7f5066a81171567f6f7e54fb0ab97
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91262454"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-management-client-library"></a>Szybki Start: Tworzenie zasobu Cognitive Services przy użyciu biblioteki klienta zarządzania Azure

Ten przewodnik Szybki Start umożliwia tworzenie zasobów usługi Azure Cognitive Services i zarządzanie nimi przy użyciu biblioteki klienta zarządzania Azure.

Usługa Azure Cognitive Services to usługi w chmurze z interfejsami API REST, a także dostępne zestawy SDK dla deweloperów, które ułatwiają deweloperom tworzenie analiz poznawczych w aplikacjach, bez konieczności bezpośredniego sztucznej analizy danych lub wiedzy. Dzięki platformie Azure Cognitive Services deweloperzy mogą łatwo dodawać funkcje poznawcze do swoich aplikacji przy użyciu rozwiązań poznawczych, które widzą, słyszą, mówią, wiedzą, a nawet z przyczyn.

Poszczególne usługi AI są reprezentowane przez [zasoby](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) platformy Azure utworzone w ramach subskrypcji platformy Azure. Po utworzeniu zasobu można użyć wygenerowanych kluczy i punktów końcowych w celu uwierzytelniania aplikacji.

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
