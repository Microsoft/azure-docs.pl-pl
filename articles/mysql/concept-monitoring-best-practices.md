---
title: Najlepsze praktyki dotyczące monitorowania — Azure Database for MySQL
description: W tym artykule opisano najlepsze rozwiązania dotyczące monitorowania Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 59301e26f4d42056322ba5a7cdaff1555c531096
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96355051"
---
# <a name="best-practices-for-monitoring-azure-database-for-mysql--single-server"></a>Najlepsze rozwiązania dotyczące monitorowania Azure Database for MySQL — pojedynczy serwer

Zapoznaj się z najlepszymi rozwiązaniami, które mogą służyć do monitorowania operacji bazy danych i zapewnienia, że wydajność nie zostanie naruszona w miarę wzrostu rozmiaru danych. Po dodaniu nowych funkcji do platformy będziemy nadal udoskonalać najlepsze rozwiązania opisane w tej sekcji.

## <a name="layout-of-the-current-monitoring-toolkit"></a>Układ bieżącego zestawu narzędzi monitorowania

Azure Database for MySQL udostępnia narzędzia i metody, których można użyć do łatwego monitorowania użycia, dodawania lub usuwania zasobów (takich jak procesor CPU, pamięć lub we/wy), rozwiązywania potencjalnych problemów i zwiększania wydajności bazy danych. Można regularnie [monitorować metryki wydajności](concepts-monitoring.md#metrics) , aby wyświetlić średnie, maksymalne i minimalne wartości dla różnych zakresów czasu.

Można [skonfigurować alerty](howto-alert-on-metric.md#create-an-alert-rule-on-a-metric-from-the-azure-portal) dla progu metryki, aby uzyskać informacje o tym, czy serwer osiągnął te limity i podjąć odpowiednie działania.  

Należy monitorować serwer bazy danych, aby upewnić się, że zasoby przypisane do bazy danych mogą obsługiwać obciążenie aplikacji. Jeśli baza danych powoduje przekroczenie limitów zasobów, należy rozważyć następujące kwestie:
    * Identyfikowanie i optymalizowanie najważniejszych zapytań zużywających zasoby. 
    * Dodawanie większej liczby zasobów przez uaktualnienie warstwy usług.

### <a name="cpu-utilization"></a>Wykorzystanie procesora
Monitoruj użycie procesora i, jeśli baza danych zawiera wyczerpanie zasobów procesora CPU. Jeśli użycie procesora CPU wynosi 90% lub więcej, należy skalować w górę obliczenia przez zwiększenie liczby rdzeni wirtualnych lub skalowanie do następnej warstwy cenowej.  Upewnij się, że przepływność lub współbieżność jest zgodnie z oczekiwaniami podczas skalowania w górę/w dół procesora CPU. 

### <a name="memory"></a>Pamięć 
Ilość pamięci dostępnej dla serwera bazy danych jest proporcjonalna do [liczby rdzeni wirtualnych](concepts-pricing-tiers.md). Upewnij się, że pamięć jest wystarczająca dla obciążenia. Przetestuj test, aby upewnić się, że pamięć jest wystarczająca dla operacji odczytu i zapisu. Jeśli zużycie pamięci bazy danych jest często zwiększane poza określoną wartość progową, oznacza to, że należy uaktualnić wystąpienie przez zwiększenie warstwy wydajności rdzeni wirtualnych lub wyższej. Użyj [magazynu zapytań](concepts-query-store.md), [zaleceń dotyczących wydajności zapytań](concepts-performance-recommendations.md) , aby identyfikować zapytania o najdłuższym czasie trwania, najczęściej wykonywanym. Poznaj możliwości optymalizacji. 

### <a name="storage"></a>Storage 
[Ilość miejsca do magazynowania](howto-create-manage-server-portal.md#scale-compute-and-storage) obsługiwana dla serwera MySQL określa liczbę operacji we/wy dla serwera. Magazyn używany przez usługę obejmuje pliki bazy danych, dzienniki transakcji, Dzienniki serwera i migawki kopii zapasowych. Upewnij się, że zajęte miejsce na dysku nie przekracza ponad 85% całkowitego miejsca na dysku. W takim przypadku należy usunąć lub zarchiwizować dane z serwera bazy danych, aby zwolnić miejsce. 

### <a name="network-traffic"></a>Ruch sieciowy 

**Przepływność odbierania sieci, przepływność transmisji w sieci** — szybkość ruchu sieciowego do i z wystąpienia MySQL w megabajtach na sekundę. Należy oszacować wymagania dotyczące przepływności serwera i stale monitorować ruch, jeśli przepływność jest mniejszy niż oczekiwano. 

### <a name="database-connections"></a>Połączenia bazy danych 
**Połączenia z bazą danych** — liczba sesji klientów podłączonych do Azure Database for MySQL powinna być wyrównana przy użyciu [limitów połączeń dla wybranego rozmiaru jednostki SKU](concepts-server-parameters.md#max_connections) . 


## <a name="next-steps"></a>Następne kroki

- [Najlepsze rozwiązanie w zakresie wydajności Azure Database for MySQL](concept-performance-best-practices.md)
- [Najlepsze rozwiązanie w zakresie operacji serwera przy użyciu Azure Database for MySQL](concept-operation-excellence-best-practices.md)
