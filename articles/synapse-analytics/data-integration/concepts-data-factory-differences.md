---
title: Różnice w stosunku do usługi Azure Data Factory
description: Dowiedz się, jak możliwości integracji danych Azure Synapse Analytics się od tych w Azure Data Factory
services: synapse-analytics
author: kromerm
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: makromer
ms.reviewer: jrasnick
ms.openlocfilehash: 144bdf5e94f753090dd73e5839b6c1fd25f11811
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567642"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Integracja danych w Azure Synapse Analytics a Azure Data Factory

W Azure Synapse Analytics możliwości integracji danych, takie jak potoki synapse i przepływy danych, są oparte na tych, które Azure Data Factory. Aby uzyskać więcej informacji, [zobacz co to jest Azure Data Factory](../../data-factory/introduction.md).


## <a name="available-features-in-adf--azure-synapse-analytics"></a>Dostępne funkcje w UDF & Azure Synapse Analytics

Sprawdź dostępność funkcji w poniższej tabeli:

| Kategoria                 | Cecha    |  Azure Data Factory  | Azure Synapse Analytics |
| ------------------------ | ---------- | :------------------: | :---------------------: |
| **Integration Runtime**  | Korzystanie z usług SSIS i SSIS Integration Runtime | ✓ | ✗ |
|                          | Obsługa przepływów danych Integration Runtime między regionami | ✓ | ✗ |
|                          | Integration Runtime udostępniania | ✓<br><small>*Mogą być udostępniane w różnych fabrykach danych* | ✗ |
|                          | Czas do transmisji | ✓ | ✗ |
| **Działania potoków** | Działanie pakietu SSIS | ✓ | ✗ |
|                          | Obsługa Power Query danych | ✓ | ✓ |
| **Galeria szablonów i Centrum wiedzy** | Szablony rozwiązań | ✓<br><small>*Azure Data Factory galerii szablonów* | ✓<br><small>*Centrum wiedzy obszaru roboczego synapse* |
| **Integracja repozytorium GIT** | Integracja z usługą GIT | ✓ | ✓ |
| **Monitorowanie**           | Monitorowanie zadań Platformy Spark dla Przepływ danych | ✗ | ✓<br><small>*Korzystanie z pul synapse Spark* |
|                          | Integracja z Azure Monitor | ✓ | ✗ |
| **Pochodzenie** | Obsługuje publikowanie danych z potoku w aplikacji Purview  | ✓ | ✗ |  

> [!Note]
> **Czas do transmisji na** żywo to Azure Integration Runtime, które  umożliwia zachować ciepło klastra Spark przez okres po wykonaniu przepływu danych.
>


## <a name="next-steps"></a>Następne kroki

Rozpoczynanie pracy z integracją danych w obszarze roboczym synapse przez uczenie się, jak pozyskać dane do konta [Azure Data Lake Storage gen2.](data-integration-data-lake.md)
