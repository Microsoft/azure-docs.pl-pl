---
title: Różnice w stosunku do usługi Azure Data Factory
description: Dowiedz się, jak możliwości integracji danych usługi Azure Synapse Analytics różnią się w zależności od Azure Data Factory
services: synapse-analytics
author: kromerm
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: makromer
ms.reviewer: jrasnick
ms.openlocfilehash: 9cd3fc9353c684ec6617761616b958477ca83ee3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183645"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Integracja danych w usłudze Azure Synapse Analytics a Azure Data Factory

W usłudze Azure Synapse Analytics funkcje integracji danych, takie jak potoki Synapse i przepływy danych, opierają się na tych Azure Data Factory. Aby uzyskać więcej informacji, zobacz [co to jest Azure Data Factory](../../data-factory/introduction.md).


## <a name="available-features-in-adf--azure-synapse-analytics"></a>Dostępne funkcje w module ADF & Azure Synapse Analytics

Sprawdź w poniższej tabeli dostępność funkcji:

| Kategoria                 | Cecha    |  Azure Data Factory  | Azure Synapse Analytics |
| ------------------------ | ---------- | :------------------: | :---------------------: |
| **Integration Runtime**  | Korzystanie z Integration Runtime SSIS i SSIS | ✓ | ✗ |
|                          | Obsługa Integration Runtime między regionami (przepływy danych) | ✓ | ✗ |
|                          | Udostępnianie Integration Runtime | ✓<br><small>*Może być współużytkowany przez różne fabryki danych* | ✗ |
|                          | Czas wygaśnięcia | ✓ | ✗ |
| **Działania potoków** | Działanie pakietu SSIS | ✓ | ✗ |
|                          | Obsługa działania Power Query | ✓ | ✓ |
| **Galeria szablonów i centrum wiedzy** | Szablony rozwiązań | ✓<br><small>*Galeria szablonów Azure Data Factory* | ✓<br><small>*Centrum wiedzy Synapse Workspace* |
| **Integracja repozytorium GIT** | Integracja z usługą GIT | ✓ | ✓ |
| **Monitorowanie**           | Monitorowanie zadań platformy Spark dla przepływu danych | ✗ | ✓<br><small>*Korzystanie z pul Synapse Spark* |
|                          | Integracja z usługą Azure Monitor | ✓ | ✗ |
| **Pochodzenie** | Obsługuje publikowanie danych z potoku w programie kontrolą  | ✓ | ✗ |  

> [!Note]
> **Czas wygaśnięcia** jest ustawieniem Azure Integration Runtime, które umożliwia klastrowi Spark *przeprowadzenie pracy* przez pewien czas po wykonaniu przepływu danych.
>


## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z integracją danych w obszarze roboczym Synapse, aby dowiedzieć się, jak pozyskać [dane na koncie Azure Data Lake Storage Gen2](data-integration-data-lake.md).
