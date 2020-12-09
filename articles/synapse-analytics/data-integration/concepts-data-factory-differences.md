---
title: Różnice w stosunku do usługi Azure Data Factory
description: Dowiedz się, jak możliwości integracji danych usługi Azure Synapse Analytics różnią się w zależności od Azure Data Factory
services: synapse-analytics
author: shirleywangmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: shwang
ms.reviewer: jrasnick
ms.openlocfilehash: db5d05e1a211ce14926ee4031054669fff5110d9
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930215"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Integracja danych w usłudze Azure Synapse Analytics a Azure Data Factory

W usłudze Azure Synapse Analytics funkcje integracji danych, takie jak potoki Synapse i przepływy danych, opierają się na tych Azure Data Factory. Aby uzyskać więcej informacji, zobacz [co to jest Azure Data Factory](../../data-factory/introduction.md). Prawie wszystkie funkcje są identyczne lub podobne, a dokumentacja jest udostępniana między obiema usługami. W tym artykule przedstawiono i wyjaśniono bieżące różnice między Azure Data Factory i Azure Synapse.

Aby sprawdzić, czy Azure Data Factory funkcja lub artykuł dotyczy usługi Azure Synapse, sprawdź moniker w górnej części artykułu.

![Dotyczy monikera](../media/concepts-data-factory-differences/applies-to-moniker.png "Dotyczy monikera")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>Funkcje w Azure Data Factory nie są planowane na platformie Azure Synapse

Poniższe funkcje są dostępne w Azure Data Factory, ale nie są planowane dla usługi Azure Synapse.

* **Pakiety SSIS i Shift w okresie życia:** W Azure Data Factory istnieje możliwość podnoszenia i przesunięcia pakietów SSIS przy użyciu programu SSIS Integration Runtime. Działanie programu SSIS Integration Runtime i wykonywanie pakietu SSIS nie jest dostępne w obszarze roboczym usługi Synapse. 
* **Czas wygaśnięcia:** Czas wygaśnięcia to ustawienie w środowisku uruchomieniowym platformy Azure, które umożliwia klastrowi Spark mapowanie przepływów danych *na bieżąco przez* pewien czas po zakończeniu przepływu danych. Ta funkcja jest niedostępna w obszarze roboczym Synapse.

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Funkcje usługi Azure Synapse nie są obsługiwane w Azure Data Factory

Następujące funkcje są dostępne w usłudze Azure Synapse, ale nie są planowane dla Azure Data Factory.

* **Monitorowanie zadań w usłudze Spark dotyczące mapowania przepływów danych:** W programie Synapse aparat Spark jest zawarty w subskrypcji użytkownika, aby użytkownicy mogli wyświetlać szczegółowe dzienniki platformy Spark. W Azure Data Factory wykonywanie zadania odbywa się na zarządzanym przez Azure Data Factory klastrze Spark i te informacje nie są dostępne. 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>Azure Data Factory funkcje, które działają inaczej w Synapse

Poniższe funkcje zachowywać się inaczej lub nie są obecnie dostępne w usłudze Azure Synapse. 

* **Przetwarzanie przepływy danych:** Działanie przepływu danych przetwarzanie jest teraz dostępne tylko w Azure Data Factory.
* **Galeria szablonów rozwiązań:** W Azure Data Factory użytkownicy mogą znaleźć szablony potoku w galerii szablonów rozwiązań. W obszarze roboczym Synapse centrum wiedzy zawiera inny zestaw szablonów wraz z dodatkowymi zestawami danych i skryptami SQL. 
* **Integracja z usługą Azure Monitor:** Obszary robocze Synapse nie są integrowane z Azure Monitor jak Azure Data Factory.
* **Konfiguracja hybrydowego środowiska uruchomieniowego integracji:** W obszarze roboczym Synapse użytkownik nie może mieć zarządzanej sieci wirtualnej i Azure IR. Ta funkcja jest obsługiwana w Azure Data Factory.
* **Udostępnianie środowiska Integration Runtime:** Własne środowiska Integration Runtime nie mogą być współużytkowane przez obszary robocze Synapse. Ta funkcja jest obsługiwana w Azure Data Factory.
* **Środowiska Integration Runtime dla przepływów danych:** Przepływy danych nie mogą być uruchamiane w ramach środowiska Integration Runtime w różnych regionach niż obszar roboczy Synapse. Ta funkcja jest obsługiwana w Azure Data Factory.

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z integracją danych w obszarze roboczym Synapse, aby dowiedzieć się, jak pozyskać [dane na koncie Azure Data Lake Storage Gen2](data-integration-data-lake.md).
