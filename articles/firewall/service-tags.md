---
title: Omówienie tagów Azure Firewall usługi
description: Tag usługi reprezentuje grupę prefiksów adresów IP, aby zminimalizować złożoność tworzenia reguły zabezpieczeń.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/5/2021
ms.author: victorh
ms.openlocfilehash: 3cc1e85a18eab1adb0a1dd8307a074cb43ba0c70
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529550"
---
# <a name="azure-firewall-service-tags"></a>Azure Firewall tagów usługi

Tag usługi reprezentuje grupę prefiksów adresów IP, aby zminimalizować złożoność tworzenia reguły zabezpieczeń. Nie można utworzyć własnego tagu usługi ani określić adresów IP uwzględnionych w tagu. Firma Microsoft zarządza prefiksami adresów obejmowanymi przez tag usługi i automatycznie aktualizuje tag usługi, gdy adresy ulegną zmianie.

Azure Firewall usługi można użyć w polu docelowym reguł sieci. Można ich używać w miejsce określonych adresów IP.

## <a name="supported-service-tags"></a>Obsługiwane tagi usługi

Listę [tagów usług,](../virtual-network/service-tags-overview.md#available-service-tags) które są dostępne do użycia w regułach sieciowych usługi Azure Firewall, zawiera temat Tagi usługi dla sieci wirtualnej.

## <a name="configuration"></a>Konfigurowanie

Azure Firewall obsługuje konfigurację tagów usługi za pośrednictwem programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu Azure Portal.

### <a name="configure-via-azure-powershell"></a>Konfigurowanie za pośrednictwem programu Azure PowerShell

W tym przykładzie musimy najpierw pobrać kontekst do wcześniej utworzonego Azure Firewall wystąpienia.

```Get the context to an existing Azure Firewall
$FirewallName = "AzureFirewall"
$ResourceGroup = "AzureFirewall-RG"
$azfirewall = Get-AzFirewall -Name $FirewallName -ResourceGroupName $ResourceGroup
```

Następnie musimy utworzyć nową regułę.  W polu Źródło lub Miejsce docelowe możesz określić wartość tekstową tagu usługi, którego chcesz użyć, jak wspomniano wcześniej w tym artykule.

````Create new Network Rules using Service Tags
$rule = New-AzFirewallNetworkRule -Name "AllowSQL" -Description "Allow access to Azure Database as a Service (SQL, MySQL, PostgreSQL, Datawarehouse)" -SourceAddress "10.0.0.0/16" -DestinationAddress Sql -DestinationPort 1433 -Protocol TCP
$ruleCollection = New-AzFirewallNetworkRuleCollection -Name "Data Collection" -Priority 1000 -Rule $rule -ActionType Allow
````

Następnie musimy zaktualizować zmienną zawierającą naszą definicję Azure Firewall przy użyciu nowo utworzonych reguł sieci.

````Merge the new rules into our existing Azure Firewall variable
$azFirewall.NetworkRuleCollections.add($ruleCollection)
`````

Na koniec musimy zatwierdzić zmiany reguły sieci w uruchomionym wystąpieniu Azure Firewall sieciowego.
````Commit the changes to Azure
Set-AzFirewall -AzureFirewall $azfirewall
````

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na Azure Firewall reguł, zobacz [Azure Firewall logiki przetwarzania reguł.](rule-processing.md)
