---
title: Nadmiarowość danych w Azure Data Factory | Microsoft Docs
description: Informacje na temat mechanizmów nadmiarowości metadanych w programie Azure Data Factory
services: data-factory
documentationcenter: ''
author: nabhishek
manager: weehyongtok
ms.reviewer: abnarain
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: abnarain
ms.openlocfilehash: 7f95adc264ed91e75eef668b43f674ddeb7d9e89
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350028"
---
# <a name="azure-data-factory-data-redundancy"></a>**Nadmiarowość danych Azure Data Factory**

Azure Data Factory dane obejmują metadane (potok, zestawy danych, połączone usługi, środowisko Integration Runtime i wyzwalacze) oraz dane monitorowania (potok, wyzwalacz i uruchomienia działania). 

We wszystkich regionach (z wyjątkiem Brazylii Południowe i Azja Południowo-Wschodnia) Azure Data Factory dane są przechowywane i replikowane w [sparowanym regionie](../best-practices-availability-paired-regions.md#azure-regional-pairs) , aby chronić przed utratą metadanych. W trakcie regionalnej awarii centrum danych firma Microsoft może zainicjować regionalną pracę w trybie failover wystąpienia Azure Data Factory. W większości przypadków żadna akcja nie jest wymagana w tej części. Po zakończeniu pracy w trybie failover zarządzanego przez firmę Microsoft dostęp do Azure Data Factory będzie możliwy w regionie trybu failover. 

Ze względu na wymagania dotyczące danych miejsca zamieszkania w Brazylii Południowej i Azji Południowo-Wschodnia Azure Data Factory dane są przechowywane [tylko w regionie lokalnym](../storage/common/storage-redundancy.md#locally-redundant-storage). W przypadku Azja Południowo-Wschodnia wszystkie dane są przechowywane w Singapurze. W regionie Brazylia Południowa wszystkie dane są przechowywane w Brazylii. Gdy region zostanie utracony ze względu na znaczną awarię, firma Microsoft nie będzie mogła odzyskać danych Azure Data Factory.  

> [!NOTE]
> Tryb failover zarządzany przez firmę Microsoft nie ma zastosowania do własnego środowiska Integration Runtime (SHIR), ponieważ ta infrastruktura jest zazwyczaj zarządzana przez klienta. Jeśli SHIR jest skonfigurowany na maszynie wirtualnej platformy Azure, zaleca się skorzystanie z [usługi Azure Site Recovery](../site-recovery/site-recovery-overview.md) do obsługi [trybu failover maszyny wirtualnej platformy Azure](../site-recovery/azure-to-azure-architecture.md) w innym regionie.



## <a name="using-source-control-in-azure-data-factory"></a>**Używanie kontroli źródła w Azure Data Factory**

Aby upewnić się, że można śledzić i przeprowadzać inspekcję zmian wprowadzonych w metadanych usługi Azure Data Factory, należy rozważyć skonfigurowanie kontroli źródła dla Azure Data Factory. Umożliwi to również dostęp do plików JSON metadanych dla potoków, zestawów danych, połączonych usług i wyzwalacza. Azure Data Factory umożliwia korzystanie z różnych repozytorium git (Azure DevOps i GitHub). 

 Dowiedz się, jak skonfigurować [kontrolę źródła w Azure Data Factory](./source-control.md). 

> [!NOTE]
> W przypadku awarii (utrata regionu) nową fabrykę danych można zainicjować ręcznie lub w zautomatyzowany sposób. Po utworzeniu nowej fabryki danych można przywrócić potoki, zestawy danych i dane JSON połączonych usług z istniejącego repozytorium git. 



## <a name="data-stores"></a>**Magazyny danych**

Azure Data Factory umożliwia przenoszenie danych między magazynami danych znajdującymi się w środowisku lokalnym i w chmurze. Aby zapewnić ciągłość działania w magazynach danych, należy zapoznać się z zaleceniami dotyczącymi ciągłości działania dla każdego z tych magazynów danych. 

 

## <a name="see-also"></a>Zobacz też

- [Pary regionalne platformy Azure](../best-practices-availability-paired-regions.md)
- [Dane znajdujące się na platformie Azure](https://azure.microsoft.com/global-infrastructure/data-residency/)