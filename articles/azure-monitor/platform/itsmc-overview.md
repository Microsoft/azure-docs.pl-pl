---
title: Przegląd łącznik zarządzania usługami IT
description: Ten artykuł zawiera omówienie łącznik zarządzania usługami IT (ITSMC).
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/16/2020
ms.custom: references_regions
ms.openlocfilehash: 93759cf239a2e7ef79c719c83299740ea3722130
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614561"
---
# <a name="it-service-management-connector-overview"></a>Przegląd łącznik zarządzania usługami IT

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Łącznik zarządzania usługami IT (ITSMC) umożliwia połączenie platformy Azure z obsługiwanym produktem lub usługą zarządzania usługami IT (narzędzia ITSM).

Usługi platformy Azure, takie jak Azure Log Analytics i Azure Monitor, udostępniają narzędzia umożliwiające wykrywanie, analizowanie i rozwiązywanie problemów z zasobami platformy Azure i spoza niej. Jednak elementy robocze związane z problemem zwykle znajdują się w narzędzia ITSM produktu lub usłudze. ITSMC zapewnia dwukierunkową połączenie między narzędziami platformy Azure i narzędzia ITSM, które ułatwiają szybsze rozwiązywanie problemów.

## <a name="configuration-steps"></a>Kroki konfiguracji

ITSMC obsługuje połączenia z następującymi narzędziami narzędzia ITSM:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

   >[!NOTE]
> Firma Microsoft proponuje naszym klientom Cherwell i Provance użycie [akcji elementu webhook](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) w Cherwell i Provance Endpoint jako innego rozwiązania do integracji.

Za pomocą ITSMC można:

-  Utwórz elementy robocze w narzędziu narzędzia ITSM na podstawie alertów platformy Azure (alertów metryk, alertów dziennika aktywności i alertów Log Analytics).
-  Opcjonalnie możesz zsynchronizować zdarzenie i dane żądania zmiany z narzędzia Narzędzia ITSM w obszarze roboczym usługi Azure Log Analytics.

Aby uzyskać informacje na temat warunków prawnych i zasad zachowania poufności informacji, zobacz zasady [zachowania poufności informacji firmy Microsoft](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Możesz rozpocząć korzystanie z ITSMC, wykonując następujące czynności:

1. [Połącz narzędzia ITSM produkty/usługi z łącznik zarządzania usługami IT.](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections)
2. [Dodaj ITSMC.](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#add-it-service-management-connector)
3. [Utwórz połączenie narzędzia ITSM.](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#create-an-itsm-connection)
4. [Użyj połączenia.](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#use-itsmc)

## <a name="next-steps"></a>Następne kroki

[Dodaj narzędzia ITSM produkty/usługi do łącznik zarządzania usługami IT](./itsmc-connections.md)
