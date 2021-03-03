---
title: Uruchamianie zadań w tle za pomocą zadań WebJob
description: Dowiedz się, jak uruchamiać zadania w tle za pomocą zadań WebJob w Azure App Service. Wybieraj spośród różnych formatów skryptów i uruchamiaj je za pomocą wyrażeń firmy cronus.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
zone_pivot_groups: app-service-webjob
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4737f0f19acf199190df02386ecb2ece65fa571e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746680"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Uruchamianie zadań w tle za pomocą zadań WebJob w Azure App Service

Koncepcje zadań w [tle](./webjobs-create-ieux-conceptual.md) Runn na platformie Azure są udostępniane za pomocą zadań w sieci Web usługi Azure App Service. Dowiedz się, jak wdrożyć <abbr title="Program lub skrypt w tym samym wystąpieniu co aplikacja sieci Web, aplikacja interfejsu API lub aplikacja mobilna.">Zadania WebJob</abbr> Użycie [Azure Portal](https://portal.azure.com) do przekazania pliku wykonywalnego lub skryptu. 

Dostępne są trzy obsługiwane Zadania WebJob:

* **Ciągły**: zaczyna się natychmiast, zazwyczaj działa w pętli nieskończonej.
* **Zaplanowane**: zaczyna od zaplanowanego wyzwalacza
* **Ręczne**: zaczyna się od wyzwalacza ręcznego

::: zone pivot="webjob-type-continuous"

[!INCLUDE [Continuous](./includes/webjobs-create-ieux-continuous.md)]

::: zone-end

::: zone pivot="webjob-type-scheduled"

[!INCLUDE [Scheduled](./includes/webjobs-create-ieux-scheduled.md)]

::: zone-end

::: zone pivot="webjob-type-manual"

[!INCLUDE [Manual](./includes/webjobs-create-ieux-manual.md)]

::: zone-end
   
## <a name="next-steps"></a><a name="NextSteps"></a> Następne kroki

* [Dowiedz się więcej o zadaniach w tle jako zadania WebJob](./webjobs-create-ieux-conceptual.md)
* [Wyświetl historię dzienników zadań WebJob](./webjobs-create-ieux-view-log.md)

* Używanie [zestawu SDK zadań WebJob](https://github.com/Azure/azure-webjobs-sdk/wiki) do uproszczenia wielu zadań programistycznych

* Naucz się [opracowywać i wdrażać Zadania WebJob za pomocą programu Visual Studio](webjobs-dotnet-deploy-vs.md)