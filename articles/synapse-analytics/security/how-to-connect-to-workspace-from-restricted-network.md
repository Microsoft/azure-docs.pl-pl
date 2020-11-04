---
title: Łączenie z zasobem obszaru roboczego programu Synapse Studio z sieci z ograniczeniami
description: W tym artykule przedstawiono sposób nawiązywania połączenia z zasobami obszaru roboczego usługi Azure Synapse Studio z sieci z ograniczeniami
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: 5d28b8f2ff3045c9fdf5e8a866419a22bfbc6504
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321818"
---
# <a name="connect-to-synapse-studio-workspace-resources-from-a-restricted-network"></a>Łączenie z zasobami obszaru roboczego programu Synapse Studio z sieci z ograniczeniami

Czytelnik docelowy tego artykułu jest firmowym administratorem IT, który zarządza siecią z ograniczeniami firmy. Administrator IT ma umożliwić włączenie połączenia sieciowego między platformą Azure Synapse Studio a stacją roboczą w ramach tej sieci z ograniczeniami.

Z tego artykułu dowiesz się, jak nawiązać połączenie z obszarem roboczym usługi Azure Synapse ze środowiska sieciowego z ograniczeniami. 

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure** : Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .
* **Obszar roboczy usługi Azure Synapse** : Jeśli nie masz programu Synapse Studio, Utwórz obszar roboczy Synapse z usługi Azure Synapse Analytics. Nazwa obszaru roboczego będzie wymagana w następnym kroku 4.
* **Sieć z ograniczeniami** : sieć z ograniczeniami jest obsługiwana przez administratora IT firmy. Administrator IT ma uprawnienia do konfigurowania zasad sieciowych. W następnym kroku 3 będzie wymagana nazwa sieci wirtualnej i jej podsieć.



## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>Krok 1. Dodawanie sieci wychodzących reguł zabezpieczeń do sieci z ograniczeniami

Należy dodać cztery sieciowe reguły zabezpieczeń dla ruchu wychodzącego z czterema tagami usług. Dowiedz się więcej o [tagach usług — Omówienie](/azure/virtual-network/service-tags-overview.md) 
* AzureResourceManager
* AzureFrontDoor. frontonu
* Usługi azureactivedirectory
* AzureMonitor (opcjonalnie. Ten typ reguły należy dodać tylko wtedy, gdy chcesz udostępnić dane firmie Microsoft.

**Azure Resource Manager** szczegóły reguły wychodzącej poniżej. Podczas tworzenia innych trzech reguł należy zastąpić wartość " **tag usługi docelowej** ", wybierając nazwę tagu usługi " **AzureFrontDoor. fronton** ", " **usługi azureactivedirectory** ", " **AzureMonitor** " z listy rozwijanej wyboru.

![AzureResourceManager](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)


## <a name="step-2-create-azure-synapse-analytics-private-link-hubs"></a>Krok 2. Tworzenie usługi Azure Synapse Analytics (prywatnych centrów łączy)

Należy utworzyć usługę Azure Synapse Analytics (centra linków prywatnych) na podstawie Azure Portal. Wyszukaj frazę " **Azure Synapse Analytics (centra linków prywatnych)** " za pomocą Azure Portal, a następnie wypełnij pole i utwórz je. 

> [!Note]
> Region powinien być taki sam jak ten, w którym znajduje się obszar roboczy Synapse.

![Tworzenie centrów prywatnych linków Synapse Analytics](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-private-link-endpoint-for-synapse-studio-gateway"></a>Krok 3. Tworzenie prywatnego linku Endpoint dla bramy Synapse Studio

Aby uzyskać dostęp do bramy Synapse Studio, musisz utworzyć prywatny punkt końcowy linku z Azure Portal. Wyszukaj " **link prywatny** " za pomocą Azure Portal. Wybierz pozycję " **Utwórz prywatny punkt końcowy** " w " **prywatnym centrum łączy** ", a następnie wypełnij pole i utwórz je. 

> [!Note]
> Region powinien być taki sam jak ten, w którym znajduje się obszar roboczy Synapse.

![Tworzenie prywatnego punktu końcowego dla programu Synapse Studio 1](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

Na następnej karcie " **zasób** " Wybierz prywatne centrum linków, które zostało utworzone w kroku 2 powyżej.

![Tworzenie prywatnego punktu końcowego dla programu Synapse Studio 2](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

Na następnej karcie " **Konfiguracja** " 
* Wybierz nazwę sieci wirtualnej z ograniczeniami dla " **Sieć wirtualna** ".
* Wybierz podsieć z ograniczoną siecią wirtualną dla " **Subnet** ". 
* Wybierz pozycję " **tak** ", aby " **zintegrować z prywatną strefą DNS** ".

![Tworzenie prywatnego punktu końcowego dla programu Synapse Studio 3](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

Po utworzeniu punktu końcowego linku prywatnego można uzyskać dostęp do strony logowania narzędzia sieci Web Synapse Studio. Nie można jednak uzyskać dostępu do zasobów w obszarze roboczym Synapse, dopóki nie będzie trzeba wykonać kolejnego kroku.

## <a name="step-4-create-private-link-endpoints-for-synapse-studio-workspace-resource"></a>Krok 4. tworzenie prywatnych punktów końcowych linku dla zasobu obszaru roboczego programu Synapse Studio

Aby uzyskać dostęp do zasobów w ramach zasobu obszaru roboczego programu Synapse Studio, należy utworzyć co najmniej jeden prywatny punkt końcowy z typem " **dev** " elementu " **Target sub-Resource** ", a dwa inne opcjonalne prywatne punkty końcowe z typami " **SQL** " lub " **SqlOnDemand** " są zależne od zasobów w obszarze roboczym programu Synapse Studio, do którego chcesz uzyskać dostęp. Ten prywatny link do tworzenia punktów końcowych dla programu Synapse Studio jest podobny jak w przypadku tworzenia punktów końcowych.  

Zwróć uwagę na poniższe obszary na karcie " **zasób** ":
* Wybierz pozycję " **Microsoft. Synapse/Workspaces** " na " **Typ zasobu** ".
* Wybierz pozycję " **YourWorkSpaceName** " do **zasobu** , który został utworzony wcześniej.
* Wybierz typ punktu końcowego w obszarze " **docelowy zasób podrzędny** ":
  * **SQL** : dotyczy wykonywania zapytań SQL w puli SQL.
  * **SqlOnDemand** : jest dla wbudowanego wykonywania zapytań SQL.
  * **Dev** : służy do uzyskiwania dostępu do wszystkiego innego wewnątrz obszarów roboczych programu Synapse Studio. Należy utworzyć co najmniej prywatny punkt końcowy linku z tym typem.

![Tworzenie prywatnego punktu końcowego dla obszaru roboczego programu Synapse Studio](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)

Teraz wszystkie zestawy. Możesz uzyskać dostęp do zasobu obszaru roboczego programu Synapse Studio.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [zarządzanym obszarze roboczym Virtual Network](./synapse-workspace-managed-vnet.md)

Dowiedz się więcej o [zarządzanych prywatnych punktach końcowych](./synapse-workspace-managed-private-endpoints.md)
