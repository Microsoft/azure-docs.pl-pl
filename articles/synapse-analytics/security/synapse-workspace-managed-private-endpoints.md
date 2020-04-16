---
title: Zarządzane prywatne punkty końcowe
description: Artykuł wyjaśniający zarządzane prywatne punkty końcowe w usłudze Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 08c6610541d987cddd7cf2aeb71c526cb2359598
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423684"
---
# <a name="synapse-managed-private-endpoints-preview"></a>Prywatne punkty końcowe zarządzane przez synapsy (wersja zapoznawcza)

W tym artykule wyjaśniono zarządzane prywatne punkty końcowe w usłudze Azure Synapse Analytics.

## <a name="managed-private-endpoints"></a>Zarządzane prywatne punkty końcowe

Zarządzane prywatne punkty końcowe są prywatnymi punktami końcowymi utworzonymi w sieci wirtualnej zarządzanego obszaru roboczego ustanawiającego prywatne łącze do zasobów platformy Azure. Usługa Azure Synapse zarządza tymi prywatnymi punktami końcowymi w Twoim imieniu.

Usługa Azure Synapse obsługuje łącza prywatne. Łącze prywatne umożliwia bezpieczny dostęp do usług platformy Azure (takich jak usługa Azure Storage, usługa Azure Cosmos DB i usługa Azure SQL Data Warehouse) oraz hostowane usługi klienta/partnera platformy Azure z sieci wirtualnej platformy Azure.

Podczas korzystania z łącza prywatnego ruch między siecią wirtualną a obszarem roboczym przechodzi całkowicie przez sieć szkieletową firmy Microsoft. Funkcja Private Link chroni przed ryzykiem eksfiltracji danych. Ustanawiasz prywatne łącze do zasobu, tworząc prywatny punkt końcowy.

Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, aby skutecznie przenieść usługę do sieci wirtualnej. Prywatne punkty końcowe są mapowane na określony zasób na platformie Azure, a nie na całą usługę. Klienci mogą ograniczyć łączność z określonym zasobem zatwierdzonym przez ich organizację. Dowiedz się więcej o [łączach prywatnych i prywatnych punktach końcowych](https://docs.microsoft.com/azure/private-link/).

>[!IMPORTANT]
>Zarządzane prywatne punkty końcowe są obsługiwane tylko w obszarach roboczych usługi Azure Synapse z siecią wirtualną zarządzanego obszaru roboczego.
>[!NOTE]
>Zaleca się utworzenie zarządzanych prywatnych punktów końcowych w celu połączenia się ze wszystkimi źródłami danych platformy Azure. Cały ruch wychodzący z >sieci wirtualnej zarządzanego obszaru roboczego zostanie zablokowany w przyszłości.

Połączenie prywatnego punktu końcowego jest tworzony w stanie "Oczekujące" podczas tworzenia zarządzanego prywatnego punktu końcowego w usłudze Azure Synapse. Zainicjowany jest przepływ pracy zatwierdzania. Właściciel zasobu łącza prywatnego jest odpowiedzialny za zatwierdzenie lub odrzucenie połączenia.

Jeśli właściciel zatwierdzi połączenie, zostanie ustanowione łącze prywatne. W przeciwnym razie łącze prywatne nie zostanie ustanowione. W obu przypadkach zarządzany prywatny punkt końcowy zostanie zaktualizowany o stan połączenia.

Tylko zarządzany prywatny punkt końcowy w zatwierdzonym stanie może wysyłać ruch do danego zasobu łącza prywatnego.

## <a name="managed-private-endpoints-for-sql-pool-and-sql-on-demand"></a>Zarządzane prywatne punkty końcowe dla puli SQL i SQL na żądanie

Pula SQL i SQL na żądanie są funkcje analityczne w obszarze roboczym usługi Azure Synapse. Te funkcje używają infrastruktury wielodostępnej, która nie jest wdrażana w [sieci wirtualnej zarządzanego obszaru roboczego.](./synapse-workspace-managed-vnet.md)

Po utworzeniu obszaru roboczego usługa Azure Synapse tworzy dwa zarządzane prywatne punkty końcowe do puli SQL i SQL na żądanie w tym obszarze roboczym. 

Te dwa zarządzane prywatne punkty końcowe są wymienione w usłudze Azure Synapse Studio. Wybierz **pozycję Zarządzaj** w lewej nawigacji, a następnie wybierz pozycję **Zarządzane sieci wirtualne,** aby wyświetlić je w Studio.

Zarządzana prywatna grupa punktów końcowych, która jest przeznaczona dla puli SQL, nazywana jest *nazwą obszaru roboczego synapse-ws-sql--\<\> * a nazwa obszaru roboczego docelowego docelowego nazywana jest *nazwą synapse-ws-sqlOnDemand--\<workspacename\>*.
![Zarządzane prywatne punkty końcowe dla puli SQL i SQL na żądanie](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Te dwa zarządzane prywatne punkty końcowe są tworzone automatycznie podczas tworzenia obszaru roboczego Usługi Azure Synapse. Nie są naliczane opłaty za te dwa zarządzane prywatne punkty końcowe.

## <a name="next-steps"></a>Następne kroki

[Tworzenie zarządzanych prywatnych punktów końcowych w źródłach danych](./how-to-create-managed-private-endpoints.md)
