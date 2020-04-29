---
title: Co to jest Azure Monitor dla maszyn wirtualnych?
description: Omówienie Azure Monitor dla maszyn wirtualnych, które monitorują kondycję i wydajność maszyn wirtualnych platformy Azure, a także automatycznie odnajdują i mapują składniki aplikacji oraz ich zależności.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/11/2020
ms.openlocfilehash: 6f65ba96f768a4b88aa13c073cc66920b8c5e049
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79480491"
---
# <a name="what-is-azure-monitor-for-vms"></a>Co to jest Azure Monitor dla maszyn wirtualnych?

Azure Monitor dla maszyn wirtualnych monitoruje maszyny wirtualne platformy Azure i zestawy skalowania maszyn wirtualnych na dużą skalę. Analizuje ona wydajność i kondycję Twoich maszyn wirtualnych z systemami Windows i Linux oraz monitoruje ich procesy i zależności od innych zasobów oraz procesów zewnętrznych. Obejmuje ona obsługę monitorowania wydajności i zależności aplikacji dla maszyn wirtualnych hostowanych lokalnie lub w innym dostawcy chmury. Następujące kluczowe funkcje zapewniają szczegółowe informacje:

- **Wstępnie zdefiniowane wykresy wydajności trendu**: Wyświetl Podstawowe metryki wydajności z systemu operacyjnego gościa.

- **Mapa zależności**: zawiera składniki połączone z maszyną wirtualną z różnych grup zasobów i subskrypcji.  

>[!NOTE]
>Niedawno [ogłoszono zmiany wprowadzane](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) do funkcji kondycji w oparciu o Opinie otrzymane od naszych klientów publicznej wersji zapoznawczej. Mając na względzie liczbę wprowadzonych zmian, będziemy mogli przestać oferować funkcję kondycji dla nowych klientów. Istniejący klienci mogą nadal korzystać z funkcji kondycji. Aby uzyskać więcej informacji, zobacz nasze [często zadawane pytania dotyczące ogólnej dostępności](vminsights-ga-release-faq.md).  

Integracja z dziennikami Azure Monitor zapewnia zaawansowaną agregację i filtrowanie, umożliwiając Azure Monitor dla maszyn wirtualnych analizowanie trendów danych w czasie. Możesz wyświetlić te dane bezpośrednio z maszyny wirtualnej lub użyć Azure Monitor, aby dostarczyć Zagregowany widok maszyn wirtualnych, gdzie widok obsługuje tryby zasobów platformy Azure lub kontekstu obszaru roboczego. Aby uzyskać więcej informacji, zobacz [Omówienie trybów dostępu](../platform/design-logs-deployment.md#access-mode).

![Perspektywa usługi Virtual Machine Insights w Azure Portal](media/vminsights-overview/vminsights-azmon-directvm.png)

Azure Monitor dla maszyn wirtualnych może zapewnić przewidywalną wydajność i dostępność ważnych aplikacji. Identyfikuje ona wąskie gardła wydajności i problemy z siecią, a także pomaga zrozumieć, czy problem jest związany z innymi zależnościami.  

## <a name="data-usage"></a>Użycie danych

Podczas wdrażania Azure Monitor dla maszyn wirtualnych dane zbierane przez maszyny wirtualne są pozyskiwane i przechowywane w Azure Monitor. Zebrane dane dotyczące wydajności i zależności są przechowywane w obszarze roboczym Log Analytics. W oparciu o Cennik opublikowany na [stronie cennika Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/)Azure monitor dla maszyn wirtualnych opłaty są naliczane za:

- Dane, które są pozyskiwane i przechowywane.
- Utworzone reguły alertów.
- Wysyłane powiadomienia. 

Rozmiar dziennika zmienia się w zależności od długości ciągu liczników wydajności i może zwiększyć liczbę dysków logicznych i kart sieciowych przypisanych do maszyny wirtualnej. Jeśli masz już obszar roboczy i zbierasz te liczniki, nie są stosowane żadne powtarzające się opłaty. Jeśli już używasz Service Map, jedyną zmianą, która zobaczysz, jest dodatkowe dane połączenia, które są wysyłane do Azure Monitor.

## <a name="next-steps"></a>Następne kroki

Aby poznać wymagania i metody, które ułatwiają monitorowanie maszyn wirtualnych, zobacz [wdrażanie Azure monitor dla maszyn wirtualnych](vminsights-enable-overview.md).
