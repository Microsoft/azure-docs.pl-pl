---
title: Tworzenie zasobu Cognitive Services przy użyciu biblioteki klienta zarządzania Azure
titleSuffix: Azure Cognitive Services
description: Tworzenie zasobów Cognitive Services platformy Azure i zarządzanie nimi przy użyciu biblioteki klienta zarządzania Azure.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 07/29/2020
ms.author: pafarley
zone_pivot_groups: programming-languages-set-seventeen
ms.openlocfilehash: e369d942c5df03cdb42ad6a30c7af0a555ee487a
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607783"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-management-client-library"></a>Szybki Start: Tworzenie zasobu Cognitive Services przy użyciu biblioteki klienta zarządzania Azure

Ten przewodnik Szybki Start umożliwia tworzenie zasobów usługi Azure Cognitive Services i zarządzanie nimi przy użyciu biblioteki klienta zarządzania Azure. Poszczególne usługi AI są reprezentowane przez [zasoby](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) platformy Azure utworzone w ramach subskrypcji platformy Azure. Po utworzeniu zasobu można użyć wygenerowanych kluczy i punktów końcowych w celu uwierzytelniania aplikacji.

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](includes/quickstarts/management-csharp.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](includes/quickstarts/management-node.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](includes/quickstarts/management-python.md)]

::: zone-end