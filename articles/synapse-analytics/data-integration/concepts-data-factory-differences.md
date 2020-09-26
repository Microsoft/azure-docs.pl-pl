---
title: Różnice między Azure Data Factory
description: Dowiedz się, jak możliwości integracji danych usługi Azure Synapse Analytics różnią się w zależności od Azure Data Factory
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: be098977ba51e529aaecfb5dc3b7a03444f55a7e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91345166"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Integracja danych w usłudze Azure Synapse Analytics a Azure Data Factory

W usłudze Azure Synapse Analytics funkcje integracji danych, takie jak potoki Synapse i przepływy danych, opierają się na tych Azure Data Factory. Aby uzyskać więcej informacji, zobacz [co to jest Azure Data Factory](../../data-factory/introduction.md). Prawie wszystkie funkcje są identyczne lub podobne, a dokumentacja jest udostępniana między obiema usługami. W tym artykule przedstawiono i wyjaśniono bieżące różnice między Azure Data Factory i Azure Synapse.

Aby sprawdzić, czy Azure Data Factory funkcja lub artykuł dotyczy usługi Azure Synapse, sprawdź moniker w górnej części artykułu.

![Dotyczy monikera](../media/concepts-data-factory-differences/applies-to-moniker.png "Dotyczy monikera")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>Funkcje w Azure Data Factory nie są planowane na platformie Azure Synapse

Poniższe funkcje są dostępne w Azure Data Factory, ale nie są planowane dla usługi Azure Synapse.

* Możliwość podnoszenia i przesunięcia pakietów SSIS.
* Płatka śniegu jako ujścia w przepływie danych działania kopiowania i mapowania.
* Ustawienie czas przepływu danych mapowania na żywo w środowisku Azure Integration Runtime.

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Funkcje usługi Azure Synapse nie są obsługiwane w Azure Data Factory

Następujące funkcje są dostępne w usłudze Azure Synapse, ale nie są planowane dla Azure Data Factory.

* Monitorowanie zadań w usłudze Spark dla mapowania przepływów danych jest dostępne tylko w Synapse. W programie Synapse aparat Spark jest zawarty w subskrypcji użytkownika, aby użytkownicy mogli wyświetlać szczegółowe dzienniki platformy Spark. W Azure Data Factory wykonywanie zadania odbywa się na zarządzanym przez Azure Data Factory klastrze Spark. 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>Azure Data Factory funkcje, które działają inaczej w Synapse

Poniższe funkcje zachowywać się inaczej lub nie są obecnie dostępne w usłudze Azure Synapse. 

* Przetwarzanie przepływy danych
* Galeria szablonów rozwiązań
* Integracja z usługą git i natywne rozwiązanie CI/CD
* Integracja z usługą Azure monitor
* Zmiana nazwy zasobów po opublikowaniu
* Konfiguracja hybrydowego środowiska Integration Runtime w obszarze roboczym Synapse. Użytkownik nie może mieć zarządzanej sieci wirtualnej i Azure IR.
* Udostępnianie środowiska Integration Runtime między obszarami roboczymi Synapse

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z integracją danych w obszarze roboczym Synapse, aby dowiedzieć się, jak pozyskać [dane na koncie Azure Data Lake Storage Gen2](data-integration-data-lake.md).
