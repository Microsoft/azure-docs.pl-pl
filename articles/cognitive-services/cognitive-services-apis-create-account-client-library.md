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
ms.date: 03/15/2021
ms.author: pafarley
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: e042ac263d3a30a9790ba6a3a3d404e5e9cb9aad
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472158"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-management-client-library"></a>Szybki Start: Tworzenie zasobu Cognitive Services przy użyciu biblioteki klienta zarządzania Azure

Ten przewodnik Szybki Start umożliwia tworzenie zasobów usługi Azure Cognitive Services i zarządzanie nimi przy użyciu biblioteki klienta zarządzania Azure.

Azure Cognitive Services to rodzina usług w chmurze, która oferuje interfejsy API REST i dostępne biblioteki klienckie ułatwiające deweloperom tworzenie analiz poznawczych w aplikacjach. Deweloperzy nie potrzebują bezpośredniej sztucznej analizy lub umiejętności związanych z nauką o danych, aby osiągnąć sukces. Dzięki platformie Azure Cognitive Services deweloperzy mogą łatwo dodawać funkcje poznawcze do swoich aplikacji przy użyciu rozwiązań poznawczych, które widzą, słyszą, mówią, wiedzą, a nawet z przyczyn.

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
