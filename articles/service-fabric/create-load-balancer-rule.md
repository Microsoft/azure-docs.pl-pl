---
title: Tworzenie reguły Azure Load Balancer dla klastra
description: Skonfiguruj Azure Load Balancer, aby otworzyć porty dla klastra Service Fabric platformy Azure.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: 7e09c7b0b3e2bfa5a5ff834e243f5098cbbd947b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92319900"
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Open ports for a Service Fabric cluster (Otwieranie portów dla klastra usługi Service Fabric)

Moduł równoważenia obciążenia wdrożony z klastrem Service Fabric platformy Azure kieruje ruch do aplikacji uruchomionej w węźle. Jeśli zmienisz aplikację w taki sposób, aby korzystała z innego portu, musisz uwidocznić ten port (lub skierować inny port) w Azure Load Balancer.

Po wdrożeniu klastra Service Fabric na platformie Azure moduł równoważenia obciążenia został automatycznie utworzony. Jeśli nie masz modułu równoważenia obciążenia, zobacz [Konfigurowanie modułu równoważenia obciążenia połączonego z Internetem](../load-balancer/quickstart-load-balancer-standard-public-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-service-fabric"></a>Konfigurowanie usługi Service Fabric

Plik konfiguracji **ServiceManifest.xml** aplikacji Service Fabric definiuje punkty końcowe używane przez aplikację. Po zaktualizowaniu pliku konfiguracji w celu zdefiniowania punktu końcowego należy zaktualizować moduł równoważenia obciążenia, aby udostępnić ten (lub inny) port. Aby uzyskać więcej informacji na temat tworzenia punktu końcowego usługi Service Fabric, zobacz [konfigurowanie punktu końcowego](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła Load Balancer otwiera port internetowy i przekazuje ruch do portu wewnętrznego węzła używanego przez aplikację. Jeśli nie masz modułu równoważenia obciążenia, zobacz [Konfigurowanie modułu równoważenia obciążenia połączonego z Internetem](../load-balancer/quickstart-load-balancer-standard-public-portal.md).

Aby utworzyć regułę Load Balancer, należy zebrać następujące informacje:

- Nazwa usługi równoważenia obciążenia.
- Grupa zasobów modułu równoważenia obciążenia i klastra usługi Service Fabric.
- Port zewnętrzny.
- Port wewnętrzny.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Tylko jedno polecenie tworzy regułę modułu równoważenia obciążenia za pomocą **interfejsu wiersza polecenia platformy Azure**. Wystarczy znać zarówno nazwę usługi równoważenia obciążenia, jak i grupę zasobów, aby utworzyć nową regułę.

>[!NOTE]
>Jeśli musisz określić nazwę modułu równoważenia obciążenia, użyj tego polecenia, aby szybko uzyskać listę wszystkich modułów równoważenia obciążenia i skojarzonych grup zasobów.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

Polecenie interfejsu wiersza polecenia platformy Azure zawiera kilka parametrów, które zostały opisane w poniższej tabeli:

| Parametr | Opis |
| --------- | ----------- |
| `--backend-port`  | Port, na który nasłuchuje aplikacja Service Fabric. |
| `--frontend-port` | Port, który jest udostępniany przez moduł równoważenia obciążenia dla połączeń zewnętrznych. |
| `-lb-name` | Nazwa modułu równoważenia obciążenia, który ma zostać zmieniony. |
| `-g`       | Grupa zasobów, która ma zarówno moduł równoważenia obciążenia, jak i klaster Service Fabric. |
| `-n`       | Wymagana nazwa reguły. |


>[!NOTE]
>Aby uzyskać więcej informacji na temat tworzenia modułu równoważenia obciążenia za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Tworzenie modułu równoważenia obciążenia za pomocą interfejsu wiersza polecenia platformy Azure](../load-balancer/quickstart-load-balancer-standard-internal-cli.md).

## <a name="powershell"></a>PowerShell

Program PowerShell jest nieco bardziej skomplikowany niż interfejs wiersza polecenia platformy Azure. Wykonaj następujące kroki koncepcyjne, aby utworzyć regułę:

1. Pobierz moduł równoważenia obciążenia z platformy Azure.
2. Utwórz regułę.
3. Dodaj regułę do modułu równoważenia obciążenia.
4. Aktualizowanie modułu równoważenia obciążenia.

>[!NOTE]
>Jeśli musisz określić nazwę modułu równoważenia obciążenia, użyj tego polecenia, aby szybko uzyskać listę wszystkich modułów równoważenia obciążenia i skojarzonych grup zasobów.
>
>`Get-AzLoadBalancer | Select Name, ResourceGroupName`

```powershell
# Get the load balancer
$lb = Get-AzLoadBalancer -Name LB-svcfab3 -ResourceGroupName svcfab_cli

# Create the rule based on information from the load balancer.
$lbrule = New-AzLoadBalancerRuleConfig -Name my-app-rule7 -Protocol Tcp -FrontendPort 39990 -BackendPort 40009 `
                                            -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
                                            -BackendAddressPool  $lb.BackendAddressPools[0] `
                                            -Probe $lb.Probes[0]

# Add the rule to the load balancer
$lb.LoadBalancingRules.Add($lbrule)

# Update the load balancer on Azure
$lb | Set-AzLoadBalancer
```

`New-AzLoadBalancerRuleConfig`W przypadku polecenia `-FrontendPort` reprezentuje port modułu równoważenia obciążenia, który jest udostępniany dla połączeń zewnętrznych, i `-BackendPort` reprezentuje port, do którego nasłuchuje aplikacja usługi Service Fabric.

>[!NOTE]
>Aby uzyskać więcej informacji na temat tworzenia modułu równoważenia obciążenia za pomocą programu PowerShell, zobacz [Tworzenie modułu równoważenia obciążenia za pomocą programu PowerShell](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [sieci w Service Fabric](service-fabric-patterns-networking.md).