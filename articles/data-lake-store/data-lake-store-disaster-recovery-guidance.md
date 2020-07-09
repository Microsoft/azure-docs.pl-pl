---
title: Wskazówki dotyczące odzyskiwania po awarii dla Azure Data Lake Storage Gen1 | Microsoft Docs
description: Wskazówki dotyczące odzyskiwania po awarii dla Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: twooley
ms.openlocfilehash: b33977ca5184ea07b5651be18e3a132d30ce4b39
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75966054"
---
# <a name="disaster-recovery-guidance-for-data-in-azure-data-lake-storage-gen1"></a>Wskazówki dotyczące odzyskiwania po awarii dla danych w Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 zapewnia Magazyn lokalnie nadmiarowy (LRS). W związku z tym dane na koncie Data Lake Storage Gen1 są odporne na przejściowe awarie sprzętowe w centrum danych za pomocą automatycznych replik. Zapewnia to trwałość i wysoką dostępność, spełniając Data Lake Storage Gen1 umowy SLA. Ten artykuł zawiera wskazówki dotyczące dalszej ochrony danych przed rzadkimi przypadkami wypadków obejmującymi cały region lub przypadkowe usunięcie.

## <a name="disaster-recovery-guidance"></a>Wskazówki dotyczące odzyskiwania po awarii
Jest to bardzo ważne, aby każdy klient przygotował własny plan odzyskiwania po awarii. Przeczytaj informacje przedstawione w tym artykule, aby skompilować plan odzyskiwania po awarii. Poniżej przedstawiono niektóre zasoby, które mogą pomóc w tworzeniu własnego planu.

* [Odzyskiwanie aplikacji platformy Azure po awarii i ich wysoka dostępność](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Odporność platformy Azure — wskazówki techniczne](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Najlepsze rozwiązania
Zalecamy skopiowanie danych o kluczowym znaczeniu do innego konta Data Lake Storage Gen1 w innym regionie o częstotliwości dopasowanej do potrzeb planu odzyskiwania po awarii. Istnieją różne metody kopiowania danych, takie jak [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) lub [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). Azure Data Factory to usługa przydatna w przypadku cyklicznego tworzenia i wdrażania potoków przepływu danych.

Jeśli wystąpi awaria regionalna, można uzyskać dostęp do danych w regionie, w którym dane zostały skopiowane. [Pulpit nawigacyjny Azure Service Health](https://azure.microsoft.com/status/) można monitorować, aby określić stan usługi platformy Azure na całym świecie.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Uszkodzenie lub przypadkowe usunięcie danych — wskazówki
Chociaż Data Lake Storage Gen1 zapewnia odporność danych za pośrednictwem zautomatyzowanych replik, nie uniemożliwia to aplikacji (lub deweloperów/użytkowników) uszkadzania danych ani ich przypadkowego usunięcia.

### <a name="best-practices"></a>Najlepsze rozwiązania
Aby zapobiec przypadkowemu usunięciu, zalecamy najpierw ustawić odpowiednie zasady dostępu dla konta usługi Data Lake Storage Gen1.  Obejmuje to zastosowanie [blokad zasobów platformy Azure](../azure-resource-manager/management/lock-resources.md) w celu zablokowania ważnych zasobów oraz stosowania kontroli dostępu na poziomie konta i pliku przy użyciu dostępnych [funkcji zabezpieczeń Data Lake Storage Gen1](data-lake-store-security-overview.md). Zalecamy również, aby regularnie tworzyć kopie krytycznych danych przy użyciu [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) lub [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) w innym Data Lake Storage Gen1 koncie, folderze lub subskrypcji platformy Azure.  Umożliwi to odzyskanie danych po ich uszkodzeniu lub usunięciu. Azure Data Factory to usługa przydatna w przypadku cyklicznego tworzenia i wdrażania potoków przepływu danych.

Organizacje mogą również włączyć [rejestrowanie diagnostyczne](data-lake-store-diagnostic-logs.md) dla swojego konta Data Lake Storage Gen1, aby zebrać ślady inspekcji dostępu do danych, które zawierają informacje o tym, kto może usunąć lub zaktualizować plik.

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)

