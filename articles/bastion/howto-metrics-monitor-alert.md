---
title: Konfigurowanie monitorowania i metryk przy użyciu Azure Monitor
titleSuffix: Azure Bastion
description: Dowiedz się więcej o usłudze Azure bastionu monitoring i metrykach korzystających z Azure Monitor, rozwiązania dla metryk, alertów, dzienników diagnostycznych na platformie Azure.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: how-to
ms.date: 03/12/2021
ms.author: mialdrid
ms.openlocfilehash: 3a5beba3938b5a845a378ede155f2f64e6baac7a
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2021
ms.locfileid: "103417947"
---
# <a name="how-to-configure-monitoring-and-metrics-for-azure-bastion-using-azure-monitor"></a>Jak skonfigurować monitorowanie i metryki dla usługi Azure bastionu za pomocą Azure Monitor

Ten artykuł pomaga w pracy z monitorowaniem i metrykami dla usługi Azure bastionu przy użyciu Azure Monitor.

>[!NOTE]
>Korzystanie z **metryk klasycznych** nie jest zalecane.
>

## <a name="about-metrics"></a>Informacje o metrykach

Usługa Azure bastionu ma różne dostępne metryki. W poniższej tabeli przedstawiono kategorię i wymiary dla każdej dostępnej metryki.

|**Metryka**|**Kategoria**|**Wymiary**|
| --- | --- | --- |
|Stan komunikacji bastionu * *|[Dostępność](#availability)|Nie dotyczy|
|Całkowita ilość pamięci|[Dostępność](#availability)|Wystąpienie|
|Używany procesor CPU|[Ruch](#traffic)|Wystąpienie
|Używana pamięć|[Ruch](#traffic)|Wystąpienie
|Liczba sesji|[Wydajność](#performance)|Wystąpienie|

* * Stan komunikacji bastionu dotyczy tylko hostów bastionu wdrożonych po listopadzie 2020.

### <a name="availability-metrics"></a><a name="availability"></a>Metryki dostępności

#### <a name="bastion-communication-status"></a><a name="communication-status"></a>Stan komunikacji bastionu

Możesz wyświetlić stan komunikacji usługi Azure bastionu, zagregowany dla wszystkich wystąpień składających się na hosta bastionu.

* Wartość **1** oznacza, że bastionu jest dostępny.
* Wartość **0** oznacza, że usługa bastionu jest niedostępna.

:::image type="content" source="./media/metrics-monitor-alert/communication-status.png" alt-text="Zrzut ekranu przedstawiający stan komunikacji.":::

#### <a name="total-memory"></a><a name="total-memory"></a>Całkowita ilość pamięci

Można wyświetlić łączną ilość pamięci usługi Azure bastionu, która jest dzielona przez poszczególne wystąpienia bastionu.

:::image type="content" source="./media/metrics-monitor-alert/total-memory.png" alt-text="Zrzut ekranu przedstawiający łączną ilość pamięci.":::

### <a name="traffic-metrics"></a><a name="traffic"></a>Metryki ruchu

#### <a name="used-cpu"></a><a name="used-cpu"></a>Używany procesor CPU

Można wyświetlić użycie procesora CPU przez usługę Azure bastionu, podzielić je na każde wystąpienie bastionu. Monitorowanie tej metryki pomoże ocenić dostępność i pojemność wystąpień wchodzących w skład platformy Azure bastionu.

:::image type="content" source="./media/metrics-monitor-alert/used-cpu.png" alt-text="Zrzut ekranu przedstawiający użycie procesora CPU.":::

#### <a name="used-memory"></a><a name="used-memory"></a>Używana pamięć

Możesz wyświetlić użycie pamięci dla każdego wystąpienia bastionu, dzieląc je na każde wystąpienie bastionu. Monitorowanie tej metryki pomoże ocenić dostępność i pojemność wystąpień wchodzących w skład platformy Azure bastionu.

:::image type="content" source="./media/metrics-monitor-alert/used-memory.png" alt-text="Zrzut ekranu przedstawiający użycie pamięci.":::

### <a name="performance-metrics"></a><a name="performance"></a>Metryki wydajności

#### <a name="session-count"></a>Liczba sesji

Można wyświetlić liczbę aktywnych sesji na wystąpienie bastionu, zagregowanych dla każdego typu sesji (RDP i SSH). Każda usługa Azure bastionu może obsługiwać wiele aktywnych sesji protokołu RDP i SSH. Monitorowanie tej metryki pomoże zrozumieć, czy konieczne jest dostosowanie liczby wystąpień z uruchomioną usługą bastionu. Aby uzyskać więcej informacji o liczbie sesji obsługiwanych przez usługę Azure bastionu, zobacz [często zadawane pytania dotyczące usługi Azure bastionu](bastion-faq.md).

:::image type="content" source="./media/metrics-monitor-alert/session-count.png" alt-text="Zrzut ekranu przedstawiający liczbę sesji.":::

## <a name="how-to-view-metrics"></a><a name="metrics"></a>Jak wyświetlić metryki

1. Aby wyświetlić metryki, przejdź do hosta bastionu.
1. Z listy **monitorowanie** wybierz pozycję **metryki**.
1. Wybierz parametry. Jeśli nie ustawiono żadnych metryk, kliknij przycisk **Dodaj metrykę**, a następnie wybierz parametry.

   * **Zakres:** Domyślnie zakres jest ustawiany na hosta bastionu.
   * **Przestrzeń nazw metryki:** Metryki standardowe.
   * **Metryka:** Wybierz metrykę, którą chcesz wyświetlić.

1. Po wybraniu metryki zostanie zastosowana domyślna agregacja. Opcjonalnie można zastosować podział, który będzie zawierać metrykę z różnymi wymiarami.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [bastionu często zadawanych pytań](bastion-faq.md).
  
