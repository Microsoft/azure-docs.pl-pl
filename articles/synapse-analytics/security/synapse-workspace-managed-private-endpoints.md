---
title: Zarządzane prywatne punkty końcowe
description: Artykuł objaśniający zarządzane prywatne punkty końcowe w usłudze Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 10/16/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 091f73aa2383094973db35efb05c5f93034efa39
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92738108"
---
# <a name="synapse-managed-private-endpoints-preview"></a>Zarządzane prywatne punkty końcowe Synapse (wersja zapoznawcza)

W tym artykule opisano zarządzane prywatne punkty końcowe w usłudze Azure Synapse Analytics.

## <a name="managed-private-endpoints"></a>Zarządzane prywatne punkty końcowe

Zarządzane prywatne punkty końcowe są prywatnymi punktami końcowymi utworzonymi w zarządzanym obszarze roboczym Microsoft Azure Virtual Network ustanawiania prywatnego linku do zasobów platformy Azure. Usługa Azure Synapse zarządza tymi prywatnymi punktami końcowymi w Twoim imieniu.

Usługa Azure Synapse obsługuje linki prywatne. Link prywatny umożliwia dostęp do usług platformy Azure (np. usługi Azure Storage i Azure Cosmos DB) oraz hostowanych usług klienta i partnerskich platformy Azure na platformie Azure Virtual Network.

W przypadku korzystania z prywatnego linku ruch między Virtual Networkami i obszarem roboczym odbywa się całkowicie za pośrednictwem sieci szkieletowej firmy Microsoft. Prywatne łącze chroniące przed ryzykiem eksfiltracji danych. Aby utworzyć prywatny link do zasobu, można utworzyć prywatny punkt końcowy.

Prywatny punkt końcowy używa prywatnego adresu IP z Virtual Network w celu efektywnego przełączenia usługi do Virtual Network. Prywatne punkty końcowe są mapowane na określony zasób na platformie Azure, a nie całej usługi. Klienci mogą ograniczyć łączność z określonym zasobem zatwierdzonym przez organizację. 

Dowiedz się więcej na temat [linków prywatnych i prywatnych punktów końcowych](https://docs.microsoft.com/azure/private-link/).

>[!IMPORTANT]
>Zarządzane prywatne punkty końcowe są obsługiwane tylko w obszarze roboczym usługi Azure Synapse z zarządzanym obszarem roboczym Virtual Network.

>[!NOTE]
>Cały ruch wychodzący z zarządzanego obszaru roboczego Virtual Network poza zarządzanymi prywatnymi punktami końcowymi zostanie zablokowany w przyszłości. Zaleca się utworzenie zarządzanych prywatnych punktów końcowych w celu nawiązania połączenia ze wszystkimi źródłami danych platformy Azure spoza obszaru roboczego. 

Połączenie prywatnego punktu końcowego jest tworzone w stanie "oczekiwanie" podczas tworzenia zarządzanego prywatnego punktu końcowego w usłudze Azure Synapse. Przepływ pracy zatwierdzania jest uruchamiany. Właściciel zasobu link prywatny jest odpowiedzialny za zaakceptowanie lub odrzucenie połączenia.

Jeśli właściciel zatwierdzi połączenie, zostanie nawiązane łącze prywatne. Jeśli jednak właściciel nie zaakceptuje połączenia, połączenie prywatne nie zostanie nawiązane. W obu przypadkach zarządzany prywatny punkt końcowy zostanie zaktualizowany przy użyciu stanu połączenia.

Tylko zarządzany prywatny punkt końcowy w zatwierdzonym stanie może wysyłać ruch do podanego zasobu linku prywatnego.

## <a name="managed-private-endpoints-for-sql-pool-and-sql-on-demand"></a>Zarządzane prywatne punkty końcowe dla puli SQL i SQL na żądanie

Pula SQL i usługa SQL na żądanie są funkcjami analitycznymi w obszarze roboczym usługi Azure Synapse. Te możliwości używają infrastruktury z wieloma dzierżawcami, która nie została wdrożona w [Virtual Network zarządzanym obszarze roboczym](./synapse-workspace-managed-vnet.md).

Po utworzeniu obszaru roboczego usługa Azure Synapse tworzy dwa zarządzane prywatne punkty końcowe do puli SQL i SQL na żądanie w tym obszarze roboczym. 

Te dwa zarządzane prywatne punkty końcowe są wymienione w usłudze Azure Synapse Studio. Wybierz pozycję **Zarządzaj** na lewym pasku nawigacyjnym, a następnie wybierz pozycję **zarządzane sieci wirtualne** , aby zobaczyć w programie Studio.

Zarządzany prywatny punkt końcowy, który jest przeznaczony dla puli SQL, nosi nazwę *Synapse-WS \<workspacename\> -SQL--* i ten, który jest przeznaczony dla SQL na żądanie, ma nazwę *Synapse \<workspacename\> -WS-sqlOnDemand--* .

![Zarządzane prywatne punkty końcowe dla puli SQL i SQL na żądanie](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Te dwa zarządzane prywatne punkty końcowe są tworzone automatycznie podczas tworzenia obszaru roboczego usługi Azure Synapse. Nie są naliczone opłaty za te dwa zarządzane prywatne punkty końcowe.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, przejdź do artykułu [Tworzenie zarządzanych prywatnych punktów końcowych do źródła danych](./how-to-create-managed-private-endpoints.md) .
