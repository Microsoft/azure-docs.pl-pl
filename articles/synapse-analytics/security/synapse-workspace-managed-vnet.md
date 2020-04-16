---
title: Zarządzana sieć wirtualna w usłudze Azure Synapse Analytics
description: Artykuł wyjaśniający zarządzaną sieć wirtualną w usłudze Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 162d96244b01f8c5e1acf224475aadb9508f0aa5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423635"
---
# <a name="azure-synapse-analytics-managed-virtual-network-preview"></a>Zarządzana sieć wirtualna usługi Azure Synapse Analytics (wersja zapoznawcza)

W tym artykule wyjaśniono zarządzana sieć wirtualna w usłudze Azure Synapse Analytics.

## <a name="managed-workspace-vnet"></a>Zarządzana siecią wirtualną obszaru roboczego

Podczas tworzenia obszaru roboczego usługi Azure Synapse, można skojarzyć go z siecią wirtualną. Sieci wirtualnej skojarzonej z obszarem roboczym jest zarządzana przez platformę Azure Synapse. Ta sieci wirtualnej jest nazywany *siecią wirtualną zarządzanego obszaru roboczego*.

Zarządzana sieci wirtualnej obszaru roboczego zapewnia wartość na cztery sposoby:

- Za pomocą sieci wirtualnej zarządzanego obszaru roboczego można odciążyć obciążenie związane z zarządzaniem siecią wirtualną do usługi Azure Synapse.
- Nie trzeba konfigurować przychodzących reguł sieciowych sieciowych na własnych sieciach wirtualnych, aby umożliwić ruch zarządzania usługi Azure Synapse, aby wprowadzić sieci wirtualnej. Błędna konfiguracja tych reguł sieciowej sieciowej powoduje zakłócenia w świadczeniu usług dla klientów.
- Nie trzeba tworzyć podsieci dla klastrów platformy Spark na podstawie obciążenia szczytowego.
- Sieć wirtualna zarządzanego obszaru roboczego wraz z zarządzanymi prywatnymi punktami końcowymi chroni przed eksfiltracją danych. Zarządzane prywatne punkty końcowe można tworzyć tylko w obszarze roboczym, z którą skojarzona jest sieć wirtualna zarządzanego obszaru roboczego.

Utworzenie obszaru roboczego z skojarzoną z nią siecią wirtualną zarządzanego obszaru roboczego gwarantuje, że obszar roboczy jest odizolowany od innych obszarów roboczych. Usługa Azure Synapse udostępnia różne funkcje analityczne w obszarze roboczym: integracja danych, platforma Apache Spark, pula SQL i sql na żądanie.

Jeśli obszar roboczy ma zarządzaną witrynę wirtualną obszaru roboczego, wdrożono w niej zasoby integracji danych i platformy Spark. A Managed workspace VNet zapewnia również izolację na poziomie użytkownika dla działań platformy Spark, ponieważ każdy klaster platformy Spark znajduje się we własnej podsieci.

Pula SQL i SQL na żądanie są możliwości wielu dzierżawców i dlatego znajdują się poza siecią wirtualną zarządzanego obszaru roboczego. Komunikacja wewnątrz obszaru roboczego z pulą SQL i sql na żądanie używa łączy prywatnych platformy Azure. Te łącza prywatne są tworzone automatycznie podczas tworzenia obszaru roboczego z skojarzoną z nim siecią wirtualną zarządzanego obszaru roboczego.

>[!IMPORTANT]
>Nie można zmienić tej konfiguracji obszaru roboczego po utworzeniu obszaru roboczego. Na przykład nie można ponownie skonfigurować obszaru roboczego, który nie ma skojarzonej z nim sieci wirtualnej zarządzanego obszaru roboczego i skojarzyć z nią sieci wirtualnej. Podobnie nie można ponownie skonfigurować obszaru roboczego z skojarzoną z nim siecią wirtualną zarządzanego obszaru roboczego i odłączyć od niej sieci wirtualnej.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-vnet"></a>Tworzenie obszaru roboczego Usługi Azure Synapse za pomocą sieci wirtualnej zarządzanego obszaru roboczego

Aby utworzyć obszar roboczy Usługi Azure Synapse, z którą skojarzona jest zarządzana sieć wirtualna obszaru roboczego, wybierz kartę Zabezpieczenia + sieć w **witrynie** Azure portal i zaznacz pole wyboru **Włącz zarządzaną sieć wirtualną.**

Jeśli pole wyboru nie zostanie zaznaczone, obszar roboczy nie będzie skojarzony z siecią wirtualną.

>[!IMPORTANT]
>Łącza prywatne można używać tylko w obszarze roboczym, który ma sieć wirtualną zarządzanego obszaru roboczego.

![Włącz sieci wirtualne zarządzanego obszaru roboczego](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

>[!NOTE]
>Cały ruch wychodzący z sieci wirtualnej zarządzanego obszaru roboczego zostanie zablokowany w przyszłości. Zaleca się łączenie się ze wszystkimi źródłami danych przy użyciu zarządzanych prywatnych punktów końcowych.

Możesz sprawdzić, czy obszar roboczy usługi Azure Synapse jest skojarzony z siecią wirtualną zarządzanego obszaru roboczego, wybierając **omówienie** z witryny Azure portal.

![Omówienie obszaru roboczego w witrynie Azure portal](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>Następne kroki

Tworzenie [obszaru roboczego synapsa platformy Azure](../quickstart-create-workspace.md)

Dowiedz się więcej o [zarządzanych prywatnych punktach końcowych](./synapse-workspace-managed-private-endpoints.md)

[Tworzenie zarządzanych prywatnych punktów końcowych w źródłach danych](./how-to-create-managed-private-endpoints.md)
