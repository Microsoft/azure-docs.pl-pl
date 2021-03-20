---
title: Zarządzane prywatne punkty końcowe
description: Artykuł objaśniający zarządzane prywatne punkty końcowe w usłudze Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 01/12/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 65794c695fa4b36586b23a308845b1f12a20b7cb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98569950"
---
# <a name="synapse-managed-private-endpoints"></a>Zarządzane prywatne punkty końcowe Synapse

W tym artykule opisano zarządzane prywatne punkty końcowe w usłudze Azure Synapse Analytics.

## <a name="managed-private-endpoints"></a>Zarządzane prywatne punkty końcowe

Zarządzane prywatne punkty końcowe są prywatnymi punktami końcowymi utworzonymi w zarządzanym Virtual Network skojarzonym z obszarem roboczym usługi Azure Synapse. Zarządzane prywatne punkty końcowe nawiązują prywatny link do zasobów platformy Azure. Usługa Azure Synapse zarządza tymi prywatnymi punktami końcowymi w Twoim imieniu. Możesz tworzyć zarządzane prywatne punkty końcowe z poziomu obszaru roboczego usługi Azure Synapse, aby uzyskiwać dostęp do usług platformy Azure (takich jak Azure Storage lub Azure Cosmos DB) oraz hostowanych usług klientów i partnerów platformy Azure.

W przypadku korzystania z zarządzanych prywatnych punktów końcowych ruch między obszarem roboczym usługi Azure Synapse i innymi zasobami platformy Azure odbywa się całkowicie za pośrednictwem sieci szkieletowej firmy Microsoft. Zarządzane prywatne punkty końcowe chronią przed eksfiltracji danych. Zarządzany prywatny punkt końcowy używa prywatnego adresu IP z zarządzanego Virtual Network, aby efektywnie przenieść usługę platformy Azure do obszaru roboczego usługi Azure Synapse, która komunikuje się z Virtual Network. Zarządzane prywatne punkty końcowe są mapowane na określony zasób na platformie Azure, a nie całej usługi. Klienci mogą ograniczyć łączność z określonym zasobem zatwierdzonym przez organizację. 

Dowiedz się więcej na temat [linków prywatnych i prywatnych punktów końcowych](../../private-link/index.yml).

>[!IMPORTANT]
>Zarządzane prywatne punkty końcowe są obsługiwane tylko w obszarze roboczym usługi Azure Synapse z zarządzanym obszarem roboczym Virtual Network.

>[!NOTE]
>Podczas tworzenia obszaru roboczego usługi Azure Synapse można skojarzyć z nim zarządzaną Virtual Network. Jeśli zdecydujesz się na zarządzanie zarządzaną Virtual Network skojarzoną z Twoim obszarem roboczym, możesz również ograniczyć ruch wychodzący z obszaru roboczego tylko do zatwierdzonych celów. Należy utworzyć zarządzane prywatne punkty końcowe dla tych obiektów docelowych. 


Połączenie prywatnego punktu końcowego jest tworzone w stanie "oczekiwanie" podczas tworzenia zarządzanego prywatnego punktu końcowego w usłudze Azure Synapse. Przepływ pracy zatwierdzania jest uruchamiany. Właściciel zasobu link prywatny jest odpowiedzialny za zaakceptowanie lub odrzucenie połączenia. Jeśli właściciel zatwierdzi połączenie, zostanie nawiązane łącze prywatne. Jeśli jednak właściciel nie zaakceptuje połączenia, połączenie prywatne nie zostanie nawiązane. W obu przypadkach zarządzany prywatny punkt końcowy zostanie zaktualizowany przy użyciu stanu połączenia. Tylko zarządzany prywatny punkt końcowy w zatwierdzonym stanie może służyć do wysyłania ruchu do prywatnego zasobu linku połączonego z zarządzanym prywatnym punktem końcowym.

## <a name="managed-private-endpoints-for-dedicated-sql-pool-and-serverless-sql-pool"></a>Zarządzane prywatne punkty końcowe dla dedykowanej puli SQL i bezserwerowego

Dedykowana Pula SQL i bezserwerowa Pula SQL to możliwości analityczne w obszarze roboczym usługi Azure Synapse. Te możliwości używają infrastruktury z wieloma dzierżawcami, która nie została wdrożona w [Virtual Network zarządzanym obszarze roboczym](./synapse-workspace-managed-vnet.md).

Po utworzeniu obszaru roboczego usługa Azure Synapse tworzy dwa zarządzane prywatne punkty końcowe w obszarze roboczym, jeden dla dedykowanej puli SQL i jeden dla puli SQL bezserwerowej. 

Te dwa zarządzane prywatne punkty końcowe są wymienione w Synapse Studio. Wybierz pozycję **Zarządzaj** na lewym pasku nawigacyjnym, a następnie wybierz pozycję **zarządzane prywatne punkty końcowe** , aby wyświetlić je w Studio.

Zarządzany prywatny punkt końcowy, który jest przeznaczony dla puli SQL, nosi nazwę *Synapse-WS \<workspacename\> -SQL--* i ten, który jest przeznaczony dla puli SQL bezserwerowej, nosi nazwę *Synapse-WS-sqlOnDemand-- \<workspacename\>*.

![Zarządzane prywatne punkty końcowe dla dedykowanej puli SQL i bezserwerowego](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Te dwa zarządzane prywatne punkty końcowe są tworzone automatycznie podczas tworzenia obszaru roboczego usługi Azure Synapse. Nie są naliczone opłaty za te dwa zarządzane prywatne punkty końcowe.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, przejdź do artykułu [Tworzenie zarządzanych prywatnych punktów końcowych do źródła danych](./how-to-create-managed-private-endpoints.md) .