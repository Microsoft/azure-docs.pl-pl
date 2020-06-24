---
title: Zarządzana Sieć wirtualna w usłudze Azure Synapse Analytics
description: Artykuł objaśniający zarządzaną sieć wirtualną w usłudze Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: b46ce6f6164479853bc762cb1ca45d67f7f80930
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2020
ms.locfileid: "85194369"
---
# <a name="azure-synapse-analytics-managed-virtual-network-preview"></a>Virtual Network zarządzane przez usługę Azure Synapse Analytics (wersja zapoznawcza)

W tym artykule opisano Virtual Network zarządzane w usłudze Azure Synapse Analytics.

## <a name="managed-workspace-vnet"></a>Sieć wirtualna zarządzanego obszaru roboczego

Podczas tworzenia obszaru roboczego usługi Azure Synapse możesz wybrać opcję skojarzenia jej z siecią wirtualną. Sieć wirtualna skojarzona z obszarem roboczym jest zarządzana przez usługę Azure Synapse. Ta sieć wirtualna jest nazywana siecią *wirtualną obszaru roboczego*.

Sieć wirtualna zarządzanego obszaru roboczego zapewnia wartość na cztery sposoby:

- Za pomocą sieci wirtualnej zarządzanej przestrzeni roboczej można odciążyć obciążenie związane z zarządzaniem siecią wirtualną do usługi Azure Synapse.
- Nie musisz konfigurować reguł sieciowej grupy zabezpieczeń dla ruchu przychodzącego we własnych sieci wirtualnychach, aby umożliwić usłudze Azure Synapse Management ruch w celu wejścia do sieci wirtualnej. Błąd konfiguracji tych reguł sieciowej grupy zabezpieczeń powoduje przerwanie działania usługi dla klientów.
- Nie musisz tworzyć podsieci dla klastrów Spark opartych na szczytowym obciążeniu.
- Sieć wirtualna zarządzanego obszaru roboczego wraz z zarządzanymi prywatnymi punktami końcowymi chroni przed eksfiltracji danych. Zarządzane prywatne punkty końcowe można tworzyć tylko w obszarze roboczym, z którym skojarzona jest sieć wirtualna z zarządzanym obszarem roboczym.

Tworzenie obszaru roboczego z zarządzaną siecią wirtualną obszaru roboczego skojarzonego z nim zapewnia, że obszar roboczy jest odizolowany od innych obszarów roboczych. Usługa Azure Synapse oferuje różne możliwości analityczne w obszarze roboczym: integrację danych, Apache Spark, pulę SQL i SQL na żądanie.

Jeśli obszar roboczy ma zarządzaną sieć wirtualną, w niej są wdrażane zasoby integracji danych i platformy Spark. Sieć wirtualna zarządzanego obszaru roboczego zapewnia także izolację na poziomie użytkownika dla działań platformy Spark, ponieważ każdy klaster Spark znajduje się w jego własnej podsieci.

Pula SQL i SQL na żądanie są funkcjami wielodostępnymi i dlatego znajdują się poza zarządzaną siecią wirtualną obszaru roboczego. Komunikacja wewnątrz obszaru roboczego z pulą SQL i SQL na żądanie używają prywatnych linków platformy Azure. Te linki prywatne są tworzone automatycznie podczas tworzenia obszaru roboczego z skojarzoną z nim siecią wirtualną z zarządzanym obszarem roboczym.

>[!IMPORTANT]
>Nie można zmienić tej konfiguracji obszaru roboczego po utworzeniu obszaru roboczego. Na przykład nie można zmienić konfiguracji obszaru roboczego, do którego nie skojarzono sieci wirtualnej zarządzanej przestrzeni roboczej i skojarzyć z nim sieć wirtualną. Analogicznie nie można zmienić konfiguracji obszaru roboczego z zarządzaną siecią wirtualną obszaru roboczego, a następnie usunąć jego skojarzenie z siecią wirtualną.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-vnet"></a>Tworzenie obszaru roboczego usługi Azure Synapse z zarządzaną siecią wirtualną obszaru roboczego

Jeśli jeszcze tego nie zrobiono, zarejestruj dostawcę zasobów sieciowych. Rejestracja dostawcy zasobów umożliwia skonfigurowanie subskrypcji do pracy z dostawcą zasobów. Po [zarejestrowaniu](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)wybierz pozycję *Microsoft. Network* z listy dostawców zasobów.

Aby utworzyć obszar roboczy usługi Azure Synapse z skojarzoną z nią zarządzaną siecią wirtualną, wybierz kartę **zabezpieczenia i sieć** w Azure Portal a następnie zaznacz pole wyboru **Włącz zarządzane sieci wirtualne** .

Jeśli pole wyboru nie zostanie zaznaczone, do obszaru roboczego nie będzie skojarzona Sieć wirtualna.

>[!IMPORTANT]
>Linków prywatnych można używać tylko w obszarze roboczym z zarządzaną siecią wirtualną obszaru roboczego.

![Włączanie zarządzanej sieci wirtualnej obszaru roboczego](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

>[!NOTE]
>Cały ruch wychodzący z sieci wirtualnej zarządzanego obszaru roboczego poza zarządzanymi prywatnymi punktami końcowymi zostanie zablokowany w przyszłości. Zaleca się utworzenie zarządzanych prywatnych punktów końcowych w celu nawiązania połączenia ze wszystkimi źródłami danych platformy Azure spoza obszaru roboczego. 

Możesz sprawdzić, czy obszar roboczy usługi Azure Synapse jest skojarzony z zarządzaną siecią wirtualną obszaru roboczego, wybierając pozycję **Przegląd** z Azure Portal.

![Omówienie obszaru roboczego w Azure Portal](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>Następne kroki

Tworzenie [obszaru roboczego usługi Azure Synapse](../quickstart-create-workspace.md)

Dowiedz się więcej o [zarządzanych prywatnych punktach końcowych](./synapse-workspace-managed-private-endpoints.md)

[Tworzenie zarządzanych prywatnych punktów końcowych dla źródeł danych](./how-to-create-managed-private-endpoints.md)
