---
title: Tematy Azure Event Grid partnera
description: Wysyłaj zdarzenia z partnerów Event Grid SaaS i PaaS bezpośrednio do usług platformy Azure z Azure Event Grid.
services: event-grid
author: femila
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: femila
ms.openlocfilehash: 5327efea8af734c723ba76d1a00b72c08f5c88b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84560271"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Tematy dotyczące partnerów w Azure Event Grid (wersja zapoznawcza)
Korzystając z tematów partnerskich, można połączyć źródła zdarzeń innych firm bezpośrednio z Azure Event Grid. Ta Integracja umożliwia subskrybowanie zdarzeń od partnerów w taki sam sposób, w jaki subskrybujesz zdarzenia z usług platformy Azure. 

## <a name="available-partners"></a>Dostępni partnerzy
Pierwszy partner dostępny za pomocą tematów Event Grid Partners to rozwiązanie Auth0. Możesz użyć [tematu partnera rozwiązanie Auth0](auth0-overview.md) , aby połączyć swoje konta rozwiązanie Auth0 i platformy Azure. Integracja umożliwia reagowanie na zdarzenia rozwiązanie Auth0, rejestrowanie ich i monitorowanie w czasie rzeczywistym.

[Aby ją wypróbować](auth0-how-to.md), zaloguj się do konta rozwiązanie Auth0 i Utwórz integrację Event Grid. Po wybraniu opcji **Utwórz** w programie rozwiązanie Auth0 zobaczysz oczekujący temat rozwiązanie Auth0 w Twoim koncie platformy Azure. Wybierz pozycję **Activate (Aktywuj**), a następnie możesz tworzyć subskrypcje Event Grid do kierowania, filtrowania i dostarczania zdarzeń tak samo jak w przypadku każdego innego źródła zdarzeń.

## <a name="pricing"></a>Cennik
Tematy dotyczące partnerów są rozliczone według tego samego współczynnika operacji jak tematy systemowe.

## <a name="limits"></a>Limity
Tematy dotyczące partnerów znajdują się w publicznej wersji zapoznawczej. W publicznej wersji zapoznawczej tematy dotyczące partnerów podlegają tym [samym limitom](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#event-grid-limits) , które są tematami systemowymi i tematami niestandardowymi.

## <a name="how-do-i-become-an-event-grid-partner"></a>Jak mogę zostać partnerem Event Grid?
Infrastruktura utworzona w celu obsługi tego uruchomienia ułatwia nowym partnerom integrację ich funkcji zdarzeń z Event Grid. Aby uzyskać więcej informacji, zapoznaj się z dokumentacją dołączania do [partnera](partner-onboarding-overview.md).

## <a name="next-steps"></a>Następne kroki

- [Rozwiązanie Auth0 partnera](auth0-overview.md)
- [Jak korzystać z tematu partnera rozwiązanie Auth0](auth0-how-to.md)
- [Zostań partnerem Event Grid](partner-onboarding-overview.md)