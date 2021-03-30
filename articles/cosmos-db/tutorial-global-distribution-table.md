---
title: Samouczek dystrybucji globalnej usługi Azure Cosmos DB dla interfejsu API tabel
description: Dowiedz się, w jaki sposób globalna dystrybucja działa w Azure Cosmos DB kontach interfejs API tabel i jak skonfigurować preferowaną listę regionów
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 01/30/2020
ms.reviewer: sngun
ms.openlocfilehash: f06fbe482327d01917f648ccdec34baa15c46e80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93073914"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Konfigurowanie dystrybucji globalnej usługi Azure Cosmos DB przy użyciu interfejsu API tabel
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

W tym artykule opisano następujące zadania: 

> [!div class="checklist"]
> * Konfigurowanie dystrybucji globalnej przy użyciu witryny Azure Portal
> * Konfigurowanie dystrybucji globalnej przy użyciu interfejsów [API tabel](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Nawiązywanie połączenia z preferowanym regionem przy użyciu interfejsu API tabel

Aby można było korzystać z [dystrybucji globalnej](distribute-data-globally.md), aplikacje klienckie powinny określić bieżącą lokalizację, w której działa aplikacja. Jest to realizowane przez ustawienie `CosmosExecutorConfiguration.CurrentRegion` właściwości. `CurrentRegion`Właściwość powinna zawierać jedną lokalizację. Każde wystąpienie klienta może określić swój własny region dla odczytu o małym opóźnieniu. Region musi być nazwany przy użyciu ich [nazw wyświetlanych](/previous-versions/azure/reference/gg441293(v=azure.100)) , takich jak "zachodnie stany USA". 

Zestaw SDK Azure Cosmos DB interfejs API tabel automatycznie wybiera najlepszy punkt końcowy do komunikowania się na podstawie konfiguracji konta i aktualnej dostępności regionalnej. Określa priorytet najbliższego regionu w celu zapewnienia lepszych opóźnień dla klientów. Po ustawieniu bieżącej `CurrentRegion` właściwości żądania odczytu i zapisu są kierowane w następujący sposób:

* **Żądania odczytu:** Wszystkie żądania odczytu są wysyłane do skonfigurowanego `CurrentRegion` . W oparciu o bliskość zestaw SDK automatycznie wybiera rezerwowy region replikowany geograficznie w celu zapewnienia wysokiej dostępności.

* **Żądania zapisu:** Zestaw SDK automatycznie wysyła wszystkie żądania zapisu do bieżącego regionu zapisu. Na koncie z zapisami w ramach wieloregionu bieżący region będzie również obsługiwał żądania zapisu. W oparciu o bliskość zestaw SDK automatycznie wybiera rezerwowy region replikowany geograficznie w celu zapewnienia wysokiej dostępności.

Jeśli nie określisz `CurrentRegion` właściwości, zestaw SDK używa bieżącego regionu zapisu dla wszystkich operacji.

Na przykład jeśli konto usługi Azure Cosmos jest w regionach "zachodnie stany USA" i "Wschodnie stany USA". Jeśli "zachodnie stany USA" to region zapisu, a aplikacja jest obecna w "Wschodnie stany USA". Jeśli nie skonfigurowano właściwości CurrentRegion, wszystkie żądania odczytu i zapisu są zawsze kierowane do regionu "zachodnie stany USA". W przypadku skonfigurowania właściwości CurrentRegion wszystkie żądania odczytu są obsługiwane z regionu "Wschodnie stany USA".

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie dystrybucji globalnej przy użyciu witryny Azure Portal
> * Konfigurowanie dystrybucji globalnej przy użyciu interfejsu API tabel usługi Azure Cosmos DB