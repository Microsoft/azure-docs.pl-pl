---
title: Omówienie usługi Azure Application Gateway Resource Health
description: Ten artykuł zawiera omówienie funkcji kondycji zasobów dla usługi Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67659504"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Omówienie usługi Azure Application Gateway Resource Health

Usługa [Azure Resource Health](../service-health/resource-health-overview.md) pomaga diagnozować i uzyskać pomoc techniczną, gdy problem z usługą platformy Azure wpłynie na Twoje zasoby. Informuje ona o bieżącej i wcześniejszej kondycji zasobów. Zapewnia pomoc techniczną, która pomaga ograniczyć problemy.

W przypadku Application Gateway Resource Health opiera się na sygnałach emitowanych przez bramę w celu oceny, czy jest ona w dobrej kondycji. Jeśli Brama jest w złej kondycji, Resource Health analizuje dodatkowe informacje w celu określenia źródła problemu. Identyfikuje także akcje podejmowane przez firmę Microsoft lub czynności, które należy wykonać w celu rozwiązania problemu.

Aby uzyskać dodatkowe informacje na temat oceny kondycji, zapoznaj się z pełną listą typów zasobów i kontroli kondycji w [Azure Resource Health](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways).


Stan kondycji Application Gateway jest wyświetlany jako jeden z następujących stanów:

## <a name="available"></a>Dostępne

**Dostępny** stan oznacza, że usługa nie wykryła żadnych zdarzeń, które mają wpływ na kondycję zasobu. Zobaczysz **ostatnio rozpoznane** powiadomienie w przypadku, gdy Brama odzyskał sprawność z nieplanowanego przestoju w ciągu ostatnich 24 godzin.

![Dostępny stan kondycji](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Niedostępny

Stan **niedostępny** oznacza, że usługa wykryła trwającą platformę lub zdarzenie niebędące platformą, które ma wpływ na kondycję bramy.

### <a name="platform-events"></a>Zdarzenia platformy

Zdarzenia platformy są wyzwalane przez wiele składników infrastruktury platformy Azure. Obejmują one zarówno zaplanowane akcje (na przykład zaplanowaną konserwację), jak i nieoczekiwane zdarzenia (na przykład nieplanowany ponowny rozruch hosta).

Resource Health zawiera dodatkowe szczegóły dotyczące zdarzenia i procesu odzyskiwania. Pozwala również kontaktować się z pomocą techniczną, nawet jeśli nie masz aktywnej umowy pomocy technicznej firmy Microsoft.

![Stan niedostępny](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Nieznane

**Nieznany** stan kondycji oznacza, Resource Health nie otrzymał informacji o bramie dłużej niż 10 minut. Ten stan nie jest ostatecznym oznaczeniem stanu bramy. Jest to ważny punkt danych w procesie rozwiązywania problemów.

Jeśli Brama działa zgodnie z oczekiwaniami, stan zmieni się na **dostępny** po kilku minutach.

Jeśli występują problemy, **nieznany** stan kondycji może zasugerować, że zdarzenie na platformie ma wpływ na bramę.

![Nieznany stan](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Obniżona wydajność

**Obniżony** stan kondycji wskazuje, że brama wykryła utratę wydajności, chociaż nadal dostępna do użycia.

![Stan w stanie roztartym](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o rozwiązywaniu problemów Application Gateway zaporę aplikacji sieci Web (WAF), zobacz [Rozwiązywanie problemów z zaporą aplikacji sieci Web (WAF) dla platformy Application Gateway Azure](web-application-firewall-troubleshoot.md)