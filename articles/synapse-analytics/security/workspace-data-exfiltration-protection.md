---
title: Ochrona danych eksfiltracji dla obszarów roboczych usługi Azure Synapse Analytics
description: W tym artykule opisano ochronę danych eksfiltracji w usłudze Azure Synapse Analytics
author: NanditaV
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 71210cdcc2b3758a59a1b41816e6468556e94808
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518264"
---
# <a name="data-exfiltration-protection-for-azure-synapse-analytics-workspaces"></a>Ochrona danych eksfiltracji dla obszarów roboczych usługi Azure Synapse Analytics
W tym artykule opisano ochronę danych eksfiltracji w usłudze Azure Synapse Analytics

## <a name="securing-data-egress-from-synapse-workspaces"></a>Zabezpieczanie danych wyjściowych z obszarów roboczych Synapse
Obszary robocze analiz usługi Azure Synapse obsługują Włączanie ochrony danych eksfiltracji dla obszarów roboczych. Dzięki ochronie eksfiltracji można chronić przed złośliwymi testerami dostępnymi do zasobów platformy Azure i jest wykradzenie dane poufne do lokalizacji poza zasięgiem organizacji. W momencie tworzenia obszaru roboczego można skonfigurować obszar roboczy przy użyciu zarządzanej sieci wirtualnej i dodatkową ochronę przed eksfiltracji danych. Po utworzeniu obszaru roboczego za pomocą [zarządzanej sieci wirtualnej](./synapse-workspace-managed-vnet.md), integracja danych i zasoby platformy Spark są wdrażane w zarządzanej sieci wirtualnej. Dedykowane pule SQL obszaru roboczego i pule SQL bezserwerowe mają funkcje wielodostępne i muszą istnieć poza zarządzaną siecią wirtualną. W przypadku obszarów roboczych z funkcją ochrony danych eksfiltracji zasoby w zarządzanej sieci wirtualnej zawsze komunikują się za pośrednictwem [zarządzanych prywatnych punktów końcowych](./synapse-workspace-managed-private-endpoints.md) , a Synapse zasoby SQL mogą łączyć się tylko z autoryzowanymi zasobami platformy Azure (celami zatwierdzonych zarządzanych prywatnych punktów końcowych z obszaru roboczego). 

>[!Note]
>Po utworzeniu obszaru roboczego nie można zmienić konfiguracji obszaru roboczego dla zarządzanej sieci wirtualnej i ochrony eksfiltracji danych.

## <a name="managing-synapse-workspace-data-egress-to-approved-targets"></a>Zarządzanie danymi obszaru roboczego Synapse do zatwierdzonych celów
Po utworzeniu obszaru roboczego z włączonym eksfiltracji danych właściciele zasobów obszaru roboczego mogą zarządzać listą zatwierdzonych dzierżawców usługi Azure AD dla obszaru roboczego. Użytkownicy z [prawidłowymi uprawnieniami](./synapse-workspace-access-control-overview.md) w obszarze roboczym mogą używać programu Synapse Studio do tworzenia zarządzanych prywatnych połączeń punktów końcowych do zasobów w zatwierdzonych dzierżawach usługi Azure AD w obszarze roboczym. Tworzenie zarządzanego prywatnego punktu końcowego zostanie zablokowane, jeśli użytkownik spróbuje utworzyć połączenie prywatnego punktu końcowego z zasobem w niezatwierdzonej dzierżawie.

## <a name="sample-workspace-with-data-exfiltration-protection-enabled"></a>Przykładowa przestrzeń robocza z włączoną ochroną eksfiltracji danych
Przekaż nam przykład, aby zilustrować ochronę danych eksfiltracji dla obszarów roboczych Synapse. Firma Contoso ma zasoby platformy Azure w dzierżawie a i w dzierżawie B i istnieje potrzeba, aby te zasoby łączyły się bezpiecznie. Obszar roboczy Synapse został utworzony w dzierżawie A z dzierżawcą B dodany jako zatwierdzoną dzierżawę usługi Azure AD. Diagram przedstawia prywatne połączenia punktów końcowych z kontami usługi Azure Storage w dzierżawie a i w dzierżawie B, które zostały zatwierdzone przez właścicieli kont magazynu. Na diagramie przedstawiono również zablokowane tworzenie prywatnych punktów końcowych. Tworzenie tego prywatnego punktu końcowego zostało zablokowane, ponieważ jest ono przeznaczone dla konta usługi Azure Storage w dzierżawie usługi Azure AD firmy Fabrikam, która nie jest zatwierdzoną dzierżawą usługi Azure AD dla obszaru roboczego contoso. 
:::image type="content" source="media/workspace-data-exfiltration-protection/workspace-data-exfiltration-protection-diagram.png" alt-text="Ten diagram przedstawia sposób implementacji ochrony danych eksfiltracji dla obszarów roboczych Synapse":::

>[!IMPORTANT]
>Zasoby w dzierżawach innych niż dzierżawa obszaru roboczego nie mogą blokować reguł zapory, aby pule SQL mogły się z nimi łączyć. Zasoby w zarządzanej sieci wirtualnej obszaru roboczego, takie jak klastry Spark, mogą łączyć zarządzane linki prywatne z zasobami chronionymi przez zaporę.
## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [utworzyć obszar roboczy z włączoną ochroną eksfiltracji danych](./how-to-create-a-workspace-with-data-exfiltration-protection.md)

Dowiedz się więcej o [zarządzanym obszarze roboczym Virtual Network](./synapse-workspace-managed-vnet.md)

Dowiedz się więcej o [zarządzanych prywatnych punktach końcowych](./synapse-workspace-managed-private-endpoints.md)

[Tworzenie zarządzanych prywatnych punktów końcowych dla źródeł danych](./how-to-create-managed-private-endpoints.md)
