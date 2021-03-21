---
title: Zadanie WebJob, zadania w tle, na platformie Azure
description: Dowiedz się więcej o zadaniach WebJob.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7714b090399b0b184e2e216ff6da7b10f2bf4386
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452275"
---
# <a name="webjobs-run-background-tasks-in-azure-app-service"></a>Zadania WebJob uruchamiają zadania w tle w Azure App Service

W tym artykule przedstawiono sposób wdrażania zadań WebJob przy użyciu [Azure Portal](https://portal.azure.com) do przekazania pliku wykonywalnego lub skryptu. Aby uzyskać informacje na temat sposobu tworzenia i wdrażania zadań WebJob przy użyciu programu Visual Studio, zobacz [wdrażanie zadań WebJob za pomocą programu Visual Studio](webjobs-dotnet-deploy-vs.md).

## <a name="overview"></a>Omówienie
WebJobs to funkcja [Azure App Service](index.yml) , która umożliwia uruchamianie programu lub skryptu w tym samym wystąpieniu, w którym znajduje się aplikacja sieci Web, aplikacja interfejsu API lub aplikacja mobilna. Nie ma dodatkowych opłat za korzystanie z zadań WebJob.

> [!IMPORTANT]
> Usługi WebJobs nie są jeszcze obsługiwane w przypadku App Service w systemie Linux.

Zestawu SDK Azure WebJobs można używać z zadaniami WebJob w celu uproszczenia wielu zadań programistycznych. Aby uzyskać więcej informacji, zobacz [co to jest zestaw SDK usługi WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).

Azure Functions zapewnia inny sposób uruchamiania programów i skryptów. Aby uzyskać porównanie między zadaniami WebJob i funkcjami, zobacz [Wybieranie między przepływem, Logic Apps, funkcjami i zadaniami WebJob](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Typy zadań WebJob

W poniższej tabeli opisano różnice między *ciągłymi* i *wyzwalanymi* zadaniami WebJob.


|Ciągłe  |Wyzwalane  |
|---------|---------|
| Uruchamiany natychmiast po utworzeniu zadania WebJob. Aby zachować zadanie od końca, program lub skrypt zwykle wykonuje swoją pracę wewnątrz pętli nieskończonej. Jeśli zadanie zakończy się, możesz uruchomić je ponownie. | Uruchamiany tylko w przypadku wyzwolenia ręcznie lub zgodnie z harmonogramem. |
| Działa na wszystkich wystąpieniach, na których działa aplikacja sieci Web. Opcjonalnie możesz ograniczyć zadanie WebJob do jednego wystąpienia. |Działa w ramach jednego wystąpienia, które wybierze platformę Azure do równoważenia obciążenia.|
| Obsługuje debugowanie zdalne. | Nie obsługuje debugowania zdalnego.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="add-webjob-to-source-control"></a>Dodawanie zadania WebJob do kontroli źródła

Jeśli masz kontrolę źródła skonfigurowaną z aplikacją, Zadania WebJob powinny zostać wdrożone w ramach integracji kontroli źródła. Po skonfigurowaniu kontroli źródła za pomocą aplikacji Zadania WebJob nie można dodać do witryny Azure Portal.

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Obsługiwane typy plików dla skryptów lub programów

Obsługiwane są następujące typy plików:

* . cmd,. bat,. exe (przy użyciu polecenia Windows cmd)
* . ps1 (przy użyciu programu PowerShell)
* . sh (przy użyciu bash)
* . php (przy użyciu języka PHP)
* . PR (przy użyciu języka Python)
* . js (przy użyciu Node.js)
* . jar (przy użyciu języka Java)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak utworzyć zadanie WebJob](./webjobs-create-ieux.md)
* Wyświetl historię dzienników [zadań WebJob](./webjobs-create-ieux-view-log.md)
