---
title: Zarządzana Sieć wirtualna
description: Artykuł objaśniający zarządzaną sieć wirtualną w usłudze Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 00920f30061832bd1d685f04113a63781df718b4
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93313702"
---
# <a name="azure-synapse-analytics-managed-virtual-network-preview"></a>Virtual Network zarządzane przez usługę Azure Synapse Analytics (wersja zapoznawcza)

W tym artykule opisano Virtual Network zarządzane w usłudze Azure Synapse Analytics.

## <a name="managed-workspace-virtual-network"></a>Virtual Network zarządzanego obszaru roboczego

Podczas tworzenia obszaru roboczego usługi Azure Synapse możesz wybrać opcję skojarzenia jej z Microsoft Azure Virtual Network. Virtual Network skojarzona z obszarem roboczym jest zarządzana przez usługę Azure Synapse. Ta Virtual Network jest nazywana *zarządzanym Virtual Network obszaru roboczego*.

Zarządzany obszar roboczy Virtual Network zapewnia wartość na cztery sposoby:

- Za pomocą zarządzanego obszaru roboczego Virtual Network można odciążyć obciążenie zarządzaniem Virtual Network do usługi Azure Synapse.
- Nie musisz konfigurować reguł sieciowej grupy zabezpieczeń dla ruchu przychodzącego w własnych sieciach wirtualnych, aby umożliwić usłudze Azure Synapse Management ruch w celu wprowadzenia Virtual Network. Błąd konfiguracji tych reguł sieciowej grupy zabezpieczeń powoduje przerwanie działania usługi dla klientów.
- Nie musisz tworzyć podsieci dla klastrów Spark opartych na szczytowym obciążeniu.
- Zarządzane Virtual Network obszarów roboczych wraz z zarządzanymi prywatnymi punktami końcowymi chronią się przed eksfiltracji danych. Zarządzane prywatne punkty końcowe można tworzyć tylko w obszarze roboczym, z którym jest skojarzony Virtual Network zarządzany obszar roboczy.

Tworzenie obszaru roboczego z zarządzanym Virtual Network obszarem roboczym zapewnia, że obszar roboczy jest odizolowany od innych obszarów roboczych. Usługa Azure Synapse udostępnia różne możliwości analityczne w obszarze roboczym: integrację danych, bezserwerową pulę Apache Spark, dedykowaną pulę SQL i bezserwerową pulę SQL.

Jeśli obszar roboczy ma zarządzaną przestrzeń roboczą Virtual Network, zostanie w niej wdrożona integracja danych i zasoby platformy Spark. Zarządzany obszar roboczy Virtual Network również zapewnia izolację poziomu użytkownika dla działań platformy Spark, ponieważ każdy klaster Spark znajduje się w jego własnej podsieci.

Dedykowana Pula SQL i bezserwerowa Pula SQL są funkcjami wielu dzierżawców i dlatego znajdują się poza zarządzanym obszarem roboczym Virtual Network. Komunikacja wewnątrz obszaru roboczego z dedykowaną pulą SQL i bezserwerową pulą SQL używają linków prywatnych platformy Azure. Te linki prywatne są tworzone automatycznie podczas tworzenia obszaru roboczego z zarządzanym obszarem roboczym, Virtual Network skojarzonym z nim.

>[!IMPORTANT]
>Nie można zmienić tej konfiguracji obszaru roboczego po utworzeniu obszaru roboczego. Na przykład nie można zmienić konfiguracji obszaru roboczego, do którego nie skojarzono Virtual Networkego obszaru roboczego, i skojarzyć z nim Virtual Network. Podobnie nie można zmienić konfiguracji obszaru roboczego z zarządzanym obszarem roboczym, Virtual Network skojarzonym z nim i usunąć skojarzenia Virtual Network z nim.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-virtual-network"></a>Tworzenie obszaru roboczego usługi Azure Synapse z zarządzanym obszarem roboczym Virtual Network

Jeśli jeszcze tego nie zrobiono, zarejestruj dostawcę zasobów sieciowych. Rejestracja dostawcy zasobów umożliwia skonfigurowanie subskrypcji do pracy z dostawcą zasobów. Po [zarejestrowaniu](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)wybierz pozycję *Microsoft. Network* z listy dostawców zasobów.

Aby utworzyć obszar roboczy usługi Azure Synapse z zarządzanym obszarem roboczym, z którym jest skojarzony Virtual Network, wybierz kartę **Sieć** w Azure Portal i zaznacz pole wyboru **Włącz zarządzane sieci wirtualne** .

Jeśli pole wyboru nie zostanie zaznaczone, obszar roboczy nie będzie z nim skojarzony Virtual Network.

>[!IMPORTANT]
>Linków prywatnych można używać tylko w obszarze roboczym z zarządzanym obszarem roboczym Virtual Network.

![Włącz zarządzaną Virtual Network obszaru roboczego](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

>[!NOTE]
>Cały ruch wychodzący z zarządzanego obszaru roboczego Virtual Network poza zarządzanymi prywatnymi punktami końcowymi zostanie zablokowany w przyszłości. Zaleca się utworzenie zarządzanych prywatnych punktów końcowych w celu nawiązania połączenia ze wszystkimi źródłami danych platformy Azure spoza obszaru roboczego. 

Możesz sprawdzić, czy obszar roboczy usługi Azure Synapse jest skojarzony z zarządzanym Virtual Network obszarem roboczym, wybierając pozycję **Przegląd** z Azure Portal.

![Omówienie obszaru roboczego w Azure Portal](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>Następne kroki

Tworzenie [obszaru roboczego usługi Azure Synapse](../quickstart-create-workspace.md)

Dowiedz się więcej o [zarządzanych prywatnych punktach końcowych](./synapse-workspace-managed-private-endpoints.md)

[Tworzenie zarządzanych prywatnych punktów końcowych dla źródeł danych](./how-to-create-managed-private-endpoints.md)
