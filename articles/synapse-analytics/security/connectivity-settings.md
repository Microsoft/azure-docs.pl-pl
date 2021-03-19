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
ms.openlocfilehash: e0d8a8e3320b49b6fbe3e8ab66c0b4569fac9afd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587937"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Ustawienia łączności usługi Azure Synapse Analytics

W tym artykule opisano ustawienia łączności w usłudze Azure Synapse Analytics i sposoby ich konfigurowania, gdy ma to zastosowanie.


## <a name="connection-policy"></a>Zasady połączenia
Zasady połączenia dla Synapse SQL w usłudze Azure Synapse Analytics są ustawione na *wartość default*. Nie można zmienić tej usługi na platformie Azure Synapse Analytics. Możesz dowiedzieć się więcej na temat tego, co ma wpływ na połączenia z usługą Synapse SQL w usłudze Azure [Synapse Analytics.](../../azure-sql/database/connectivity-architecture.md#connection-policy) 

## <a name="minimal-tls-version"></a>Minimalna wersja protokołu TLS
Synapse SQL w usłudze Azure Synapse Analytics umożliwia nawiązywanie połączeń przy użyciu wszystkich wersji protokołu TLS. Nie można ustawić minimalnej wersji protokołu TLS dla Synapse SQL w usłudze Azure Synapse Analytics.

## <a name="next-steps"></a>Następne kroki

Tworzenie [obszaru roboczego usługi Azure Synapse](./synapse-workspace-ip-firewall.md)