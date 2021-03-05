---
title: Sieć wirtualna dla usług platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak wdrożyć dedykowane usługi platformy Azure w sieci wirtualnej i poznać możliwości oferowane przez te wdrożenia.
services: virtual-network
documentationcenter: na
author: mohnader
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/06/2020
ms.author: kumud
ms.openlocfilehash: 79b6fa1043b1bb8add6b6beb5fd38312ee12ea75
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102210278"
---
# <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Wdrażanie dedykowanych usług platformy Azure w sieciach wirtualnych

Podczas wdrażania dedykowanych usług platformy Azure w [sieci wirtualnej](virtual-networks-overview.md)można komunikować się z zasobami usługi prywatnie, za pomocą prywatnych adresów IP.

![Usługi wdrożone w sieci wirtualnej](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Wdrażanie usług w ramach sieci wirtualnej zapewnia następujące możliwości:

- Zasoby w ramach sieci wirtualnej mogą komunikować się ze sobą prywatnie, za pomocą prywatnych adresów IP. Przykładowo bezpośrednie przesyłanie danych między usługą HDInsight i SQL Server uruchomione na maszynie wirtualnej w sieci wirtualnej.
- Zasoby lokalne mogą uzyskiwać dostęp do zasobów w sieci wirtualnej przy użyciu prywatnych adresów IP za pośrednictwem [sieci VPN typu lokacja-lokacja (VPN Gateway)](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) lub [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Sieci wirtualne mogą być połączone za pomocą [komunikacji równorzędnej](virtual-network-peering-overview.md) , aby umożliwić komunikację między sieciami wirtualnymi przy użyciu prywatnych adresów IP.
- Wystąpienia usługi w sieci wirtualnej są zwykle w pełni zarządzane przez usługę platformy Azure. Obejmuje to monitorowanie kondycji zasobów i skalowanie z obciążeniem.
- Wystąpienia usługi są wdrażane w podsieci w sieci wirtualnej. Przychodzący i wychodzący dostęp do sieci dla podsieci musi być otwarty za pomocą [sieciowych grup zabezpieczeń](./network-security-groups-overview.md#network-security-groups), zgodnie ze wskazówkami dostarczonymi przez usługę.
- Niektóre usługi również nakładają ograniczenia dotyczące podsieci, w których są wdrażane, ograniczają stosowanie zasad, trasy lub łączenie maszyn wirtualnych i zasobów usług w tej samej podsieci. Sprawdź każdą z usług zgodnie z określonymi ograniczeniami, ponieważ mogą one ulec zmianie w czasie. Przykładami takich usług są Azure NetApp Files, dedykowany moduł HSM Azure Container Instances, App Service. 
- Opcjonalnie usługi mogą wymagać [delegowanej podsieci](virtual-network-manage-subnet.md#add-a-subnet) jako jawnego identyfikatora, który może hostować określoną usługę w podsieci. Dzięki delegowaniu usługi uzyskują jawne uprawnienia do tworzenia zasobów specyficznych dla usługi w podsieci delegowanej.
- Zapoznaj się z przykładem odpowiedzi interfejsu API REST w [sieci wirtualnej z delegowaną podsiecią](/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet). Kompleksowa Lista usług, które korzystają z delegowanego modelu podsieci, można uzyskać za pośrednictwem interfejsu API [dostępne delegacje](/rest/api/virtualnetwork/availabledelegations/list) .

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Usługi, które mogą być wdrażane w sieci wirtualnej

|Kategoria|Usługa| Dedykowana<sup>1</sup> podsieć
|-|-|-|
| Compute | Maszyny wirtualne: [Linux](/previous-versions/azure/virtual-machines/linux/infrastructure-example?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Windows](/previous-versions/azure/virtual-machines/windows/infrastructure-example?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Zestawy skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Usługa w chmurze](/previous-versions/azure/reference/jj156091(v=azure.100)): tylko sieć wirtualna (klasyczna)<br/> [Azure Batch](../batch/nodes-and-pools.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Nie <br/> Nie <br/> Nie <br/> Nr<sup>2</sup>
| Sieć | [Application Gateway — WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  <br/> [Azure Bastion](../bastion/bastion-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Wirtualne urządzenia sieciowe](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn)| Tak <br/> Tak <br/> Tak <br/> Tak <br/> Nie
|Dane|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Wystąpienie zarządzane Azure SQL](../azure-sql/managed-instance/connectivity-architecture-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Tak <br/> Tak <br/> 
|Analiza | [Usługa Azure HDInsight](../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks?toc=%2fazure%2fvirtual-network%2ftoc.json) |Nr<sup>2</sup> <br/> Nr<sup>2</sup> <br/> 
| Tożsamość | [Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Nie <br/>
| Kontenery | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Wystąpienie kontenera platformy Azure (ACI)](https://www.aka.ms/acivnet)<br/>[Aparat Azure Container Service](https://github.com/Azure/acs-engine) z [dodatkiem plug-in](https://github.com/Azure/acs-engine/tree/master/examples/vnet) platformy Azure Virtual Network CNI<br/>[Azure Functions](../azure-functions/functions-networking-options.md#virtual-network-integration) |Nr<sup>2</sup><br/> Tak <br/> Nie <br/> Tak
| Internet | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Web Apps](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Środowisko usługi App Service](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Tak <br/> Tak <br/> Tak <br/> Tak
| Hostowane | [Dedykowany moduł HSM platformy Azure](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Tak <br/> Tak <br/>
| Azure Spring Cloud | [Wdróż w usłudze Azure Virtual Network (iniekcja sieci wirtualnej)](../spring-cloud/spring-cloud-tutorial-deploy-in-azure-virtual-network.md)<br/>| Tak <br/>
| | |

<sup>1</sup> "dedykowany" oznacza, że w tej podsieci można wdrożyć tylko zasoby określone dla usługi i nie można ich łączyć z maszyną wirtualną/VMSSs klienta <br/> 
<sup>2</sup> zaleca się, aby te usługi były dostępne w dedykowanej podsieci, ale nie jest to obowiązkowe wymaganie narzucone przez usługę.
