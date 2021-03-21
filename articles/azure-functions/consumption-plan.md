---
title: Azure Functions hosting planu zużycia
description: Dowiedz się, jak hosting planu zużycia funkcji platformy Azure umożliwia uruchamianie kodu w środowisku, które jest skalowane dynamicznie, ale płacisz tylko za zasoby używane podczas wykonywania.
ms.date: 8/31/2020
ms.topic: conceptual
ms.openlocfilehash: d292a70a8dfaa4cebdb99f2bcb5420c8b8ab9cd8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98760549"
---
# <a name="azure-functions-consumption-plan-hosting"></a>Azure Functions hosting planu zużycia

Gdy używasz planu zużycia, wystąpienia hosta Azure Functions są dynamicznie dodawane i usuwane na podstawie liczby zdarzeń przychodzących. Plan zużycia to w pełni opcja hostingu <em>bezserwerowego</em> dla Azure Functions.

## <a name="benefits"></a>Korzyści

Plan zużycia jest skalowany automatycznie, nawet w okresach dużego obciążenia. Podczas uruchamiania funkcji w planie zużycia opłaty są naliczane za zasoby obliczeniowe tylko wtedy, gdy funkcje są uruchomione. W ramach Planu użycia limit czasu wykonywania funkcji zostaje przekroczony po konfigurowalnym okresie czasu.

Aby zapoznać się z porównaniem planu zużycia z innymi typami planów i hostingu, zobacz [Funkcja skalowanie i opcje hostingu](functions-scale.md).

## <a name="billing"></a>Rozliczenia

Rozliczenia zależą od liczby wykonań, czasu wykonania oraz użytej pamięci. Użycie jest agregowane we wszystkich funkcjach w ramach aplikacji funkcji. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Aby dowiedzieć się więcej na temat szacowania kosztów podczas pracy w planie zużycia, zobacz [Opis kosztów planu zużycia](functions-consumption-costs.md).

## <a name="create-a-consumption-plan-function-app"></a>Tworzenie aplikacji funkcji planu zużycia

Podczas tworzenia aplikacji funkcji w Azure Portal jest to domyślny plan zużycia. Gdy korzystasz z interfejsów API do tworzenia aplikacji funkcji, nie musisz najpierw utworzyć planu App Service w ramach planów Premium i dedykowanych.

Skorzystaj z poniższych linków, aby dowiedzieć się, jak utworzyć aplikację funkcji bezserwerowej w planie zużycia, programowo lub w Azure Portal:

+ [Interfejs wiersza polecenia platformy Azure](./scripts/functions-cli-create-serverless.md)
+ [Witryna Azure Portal](./functions-get-started.md)
+ [Szablon usługi Azure Resource Manager](functions-create-first-function-resource-manager.md)

Możesz również tworzyć aplikacje funkcji w planie zużycia podczas publikowania projektu funkcji z [Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) lub [Visual Studio](functions-create-your-first-function-visual-studio.md#publish-the-project-to-azure).

## <a name="multiple-apps-in-the-same-plan"></a>Wiele aplikacji w tym samym planie

Aplikacje funkcji w tym samym regionie mogą być przypisane do tego samego planu zużycia. Nie ma Minusem ani wpływu na wiele aplikacji uruchomionych w tym samym planie zużycia. Przypisywanie wielu aplikacji do tego samego planu zużycia nie ma wpływu na odporność, skalowalność ani niezawodność każdej aplikacji.

## <a name="next-steps"></a>Następne kroki

+ [Opcje hostingu Azure Functions](functions-scale.md)
+ [Skalowanie oparte na zdarzeniach w Azure Functions](event-driven-scaling.md)
