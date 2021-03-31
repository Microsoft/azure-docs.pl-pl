---
title: Zaobserwowanie na platformie Azure API Management | Microsoft Docs
description: Przegląd wszystkich opcji zaobserwowania na platformie Azure API Management.
services: api-management
documentationcenter: ''
author: begim
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/01/2020
ms.author: apimpm
ms.openlocfilehash: 1ebebed465952bbb5d3e8f82ae1c7776c441c6b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87097825"
---
# <a name="observability-in-azure-api-management"></a>Zaobserwowanie na platformie Azure API Management

Zaobserwowanie to możliwość zrozumienia wewnętrznego stanu systemu z danych, które generuje, oraz możliwości eksplorowania tych danych w odpowiedzi na pytania dotyczące tego, co się stało i dlaczego. 

Usługa Azure API Management ułatwia organizacjom scentralizowanie zarządzania wszystkimi interfejsami API. Ponieważ służy on jako pojedynczy punkt wejścia całego ruchu interfejsu API, jest idealnym miejscem do obserwowania interfejsów API. 

## <a name="observability-tools"></a>Narzędzia do zaobserwowania

Poniższa tabela podsumowuje wszystkie narzędzia obsługiwane przez API Management do obserwowania interfejsów API, każdy z nich jest przydatny dla jednego lub wielu scenariuszy:

| Narzędzie        | Przydatne dla    | Zwłoka danych | Przechowywanie | Próbkowanie | Rodzaj danych | Enabled (Włączony)|
|:------------- |:-------------|:---- |:----|:---- |:--- |:---- 
| **[Inspektor interfejsu API](api-management-howto-api-inspector.md)** | Testowanie i debugowanie | Błyskawiczne | Ostatnie 100 śladów | Włączone na żądanie | Śledzenie żądań | Always (Zawsze)
| **Wbudowana analiza** | Raportowanie i monitorowanie | Minuty | Okres istnienia | 100% | Raporty i dzienniki | Always (Zawsze) |
| **[Metryki usługi Azure Monitor](api-management-howto-use-azure-monitor.md)** | Raportowanie i monitorowanie | Minuty | 93 dni (uaktualnienie do rozszerzenia) | 100% | Metryki | Always (Zawsze) |
| **[Dzienniki usługi Azure Monitor](api-management-howto-use-azure-monitor.md)** | Raportowanie, monitorowanie i debugowanie | Minuty | 31 dni/5 GB (uaktualnienie do rozszerzenia) | 100% (regulowane) | Dzienniki | Opcjonalne |
| **[Application Insights platformy Azure](api-management-howto-app-insights.md)** | Raportowanie, monitorowanie i debugowanie | Sekundy | 90 dni/5 GB (uaktualnienie do rozszerzenia) | Niestandardowy | Dzienniki, metryki | Opcjonalne |
| **[Logowanie za pomocą usługi Azure Event Hub](api-management-howto-log-event-hubs.md)** | Scenariusze niestandardowe | Sekundy | Zarządzane przez użytkownika | Niestandardowy | Niestandardowy | Opcjonalne |

### <a name="self-hosted-gateway"></a>Brama samoobsługowa

Wszystkie narzędzia wymienione powyżej są obsługiwane przez zarządzaną bramę w chmurze. [Brama samoobsługowa](self-hosted-gateway-overview.md) obecnie nie wysyła dzienników diagnostycznych do Azure monitor. Istnieje jednak możliwość skonfigurowania i utrwalenia dzienników lokalnie, w których wdrożono bramę samohostowaną. Aby uzyskać więcej informacji, zobacz [Konfigurowanie metryk w chmurze i dzienników dla bramy samohostowanej](how-to-configure-cloud-metrics-logs.md) oraz [Konfigurowanie lokalnych metryk i dzienników dla bramy samoobsługowej](how-to-configure-local-metrics-logs.md).

## <a name="next-steps"></a>Następne kroki

* [Postępuj zgodnie z samouczkami, aby dowiedzieć się więcej o API Management](import-and-publish.md)
