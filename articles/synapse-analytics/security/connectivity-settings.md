---
title: Ustawienia łączności z usługą Azure Synapse
description: Artykuł, który uczy się skonfigurować ustawienia łączności w usłudze Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 03/15/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: ce1a4808833cbd897da17f9ad75af346538d23d1
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103473089"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Ustawienia łączności usługi Azure Synapse Analytics

W tym artykule opisano ustawienia łączności w usłudze Azure Synapse Analytics i sposoby ich konfigurowania, gdy ma to zastosowanie.


## <a name="connection-policy"></a>Zasady połączenia
Zasady połączenia dla Synapse SQL w usłudze Azure Synapse Analytics są ustawione na *wartość default*. Nie można zmienić tej usługi na platformie Azure Synapse Analytics. Możesz dowiedzieć się więcej na temat tego, co ma wpływ na połączenia z usługą Synapse SQL w usłudze Azure [Synapse Analytics.](https://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policy) 

## <a name="minimal-tls-version"></a>Minimalna wersja protokołu TLS
Synapse SQL w usłudze Azure Synapse Analytics umożliwia nawiązywanie połączeń przy użyciu wszystkich wersji protokołu TLS. Nie można ustawić minimalnej wersji protokołu TLS dla Synapse SQL w usłudze Azure Synapse Analytics.

## <a name="next-steps"></a>Następne kroki

Tworzenie [obszaru roboczego usługi Azure Synapse](./synapse-workspace-ip-firewall.md)
