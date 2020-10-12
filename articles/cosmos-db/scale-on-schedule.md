---
title: Skalowanie Azure Cosmos DB według harmonogramu przy użyciu czasomierza Azure Functions
description: Dowiedz się, jak skalować zmiany w przepływności w Azure Cosmos DB przy użyciu programu PowerShell i Azure Functions.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: ec5c98d90facf9458769f235880f17d14708e425
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87923659"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>Skalowanie przepływności Azure Cosmos DB przy użyciu wyzwalacza Azure Functions czasomierza

Wydajność konta usługi Azure Cosmos zależy od ilości alokowanej przepływności wyrażonej w jednostkach żądań na sekundę (RU/s). Inicjowanie obsługi jest z drugiej stopnia szczegółowości i jest rozliczane na podstawie najwyższych jednostek RU/s na godzinę. Ten model pojemności zainicjowanej pozwala usłudze zapewnić przewidywalną i spójną przepływność, gwarantowane małe opóźnienia i wysoką dostępność. Większość obciążeń produkcyjnych te funkcje. Jednak w środowiskach deweloperskich i testowych, w których Azure Cosmos DB są używane tylko w godzinach pracy, można skalować przepustowość w górę i skalować ją z góry po godzinach pracy.

Przepływność można ustawić za pomocą [szablonów Azure Resource Manager](resource-manager-samples.md), [interfejsu wiersza polecenia platformy Azure](cli-samples.md)i [programu PowerShell](powershell-samples.md)dla kont interfejsu API programu Core (SQL) lub przy użyciu Azure Cosmos DB zestawów SDK specyficznych dla języka. Zaletą korzystania z szablonów Menedżer zasobów, interfejsu wiersza polecenia platformy Azure lub programu PowerShell jest obsługa wszystkich Azure Cosmos DB interfejsów API modelu.

## <a name="throughput-scheduler-sample-project"></a>Przykładowy projekt harmonogramu przepływności

Aby uprościć proces skalowania Azure Cosmos DB według harmonogramu utworzyliśmy przykładowy projekt o nazwie [harmonogram przepływności usługi Azure Cosmos](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler). Ten projekt jest aplikacją Azure Functionsą z dwoma wyzwalaczami czasomierza — "ScaleUpTrigger" i "ScaleDownTrigger". Wyzwalacze uruchamiają skrypt programu PowerShell, który ustawia przepływność dla każdego zasobu, zgodnie z definicją w `resources.json` pliku w każdym wyzwalaczu. ScaleUpTrigger jest skonfigurowany do uruchamiania w godzinach 8 czasu UTC, a ScaleDownTrigger jest skonfigurowany do uruchamiania o godzinie 6 godzin UTC, a czasy te można łatwo aktualizować w `function.json` pliku dla każdego wyzwalacza.

Można sklonować ten projekt lokalnie, zmodyfikować go w celu określenia zasobów Azure Cosmos DB do skalowania w górę i w dół oraz harmonogramu do uruchomienia. Później możesz wdrożyć go w ramach subskrypcji platformy Azure i zabezpieczyć przy użyciu tożsamości usługi zarządzanej z uprawnieniami [kontroli dostępu opartej na rolach (Azure RBAC)](role-based-access-control.md) z rolą "Azure Cosmos DB operator", aby ustawić przepływność na kontach usługi Azure Cosmos.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej i Pobierz przykład z usługi [Azure Cosmos DB Scheduler przepływności](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler).
