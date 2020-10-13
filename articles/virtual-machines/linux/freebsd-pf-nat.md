---
title: Tworzenie zapory na platformie Azure przy użyciu filtru pakietów FreeBSD
description: Dowiedz się, jak wdrożyć zaporę NAT przy użyciu funkcji PF FreeBSD na platformie Azure.
author: KylieLiang
ms.service: virtual-machines-linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
ms.openlocfilehash: 6a20708c5564075c24eb031a39292b020a2ecc00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371324"
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Jak utworzyć bezpieczną zaporę na platformie Azure przy użyciu filtru pakietów FreeBSD
W tym artykule przedstawiono sposób wdrażania zapory NAT przy użyciu filtru Azure Resource Manager Pack dla wspólnego scenariusza serwera sieci Web.

## <a name="what-is-pf"></a>Co to jest PF?
PF (filtr pakietów, również zapisany PF) to filtr pakietów stanowych z licencją BSD, centralne oprogramowanie do obsługi zapór. PF został szybko rozwijający i ma kilka zalet w porównaniu do innych dostępnych zapór. Translator adresów sieciowych (NAT) jest w wersji PF od pierwszego dnia, a następnie usługa Harmonogram pakietów i zarządzanie kolejkami aktywnymi została zintegrowana z PF, przez integrację ALTQ i Konfigurowanie jej przy użyciu konfiguracji PF. Funkcje, takie jak pfsync i KARPia na potrzeby przełączania do trybu failover i nadmiarowości, authpf do uwierzytelniania sesji i FTP-proxy w celu ułatwienia zapory dla trudnego protokołu FTP, również rozszerzonego programu PF. W skrócie PF to zaawansowane i bogate w funkcje zapory. 

## <a name="get-started"></a>Rozpoczęcie pracy
Jeśli chcesz skonfigurować bezpieczną zaporę w chmurze dla serwerów sieci Web, zacznijmy pracę. Można również zastosować skrypty używane w tym Azure Resource Manager szablonu do skonfigurowania topologii sieci.
Szablon Azure Resource Manager skonfiguruje maszynę wirtualną FreeBSD, która wykonuje translację NAT/Redirection przy użyciu PF i dwóch maszyn wirtualnych FreeBSD z zainstalowanym i skonfigurowanym serwerem sieci Web Nginx. Oprócz wykonywania translacji adresów sieciowych dla dwóch serwerów sieci Web ruch wychodzący, maszyna wirtualna NAT/przekierowanie przechwytuje żądania HTTP i przekierowuje je do dwóch serwerów sieci Web w sposób okrężny. Sieć wirtualna korzysta z prywatnej przestrzeni adresowej IP bez obsługi routingu 10.0.0.2/24 i można modyfikować parametry szablonu. Szablon Azure Resource Manager definiuje również tabelę tras dla całej sieci wirtualnej, która jest kolekcją pojedynczych tras służących do przesłaniania domyślnych tras platformy Azure na podstawie docelowego adresu IP. 

![Na diagramie przedstawiono publiczny I P adres w wystąpieniu translatora adresów sieciowych, który przekierowuje metodę okrężną do dwóch maszyn wirtualnych zaplecza, które obsługują serwery sieci Web Nginx.](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Wdrażanie za pomocą interfejsu wiersza polecenia platformy Azure
Potrzebujesz najnowszego [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) , który zalogował się na konto platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index). Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy nazwę grupy zasobów `myResourceGroup` w `West US` lokalizacji.

```azurecli
az group create --name myResourceGroup --location westus
```

Następnie wdróż szablon PF-FreeBSD-Setup za pomocą polecenie [AZ Group Deployment Create](/cli/azure/group/deployment). Pobierz azuredeploy.parameters.jsw tej samej ścieżce i zdefiniuj własne wartości zasobów, takie jak `adminPassword` , `networkPrefix` , i `domainNamePrefix` . 

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Po około pięciu minutach uzyskasz informacje o programie `"provisioningState": "Succeeded"` . Następnie można użyć protokołu SSH na serwerze sieci Web frontonu (NAT) lub dostępu Nginx w przeglądarce przy użyciu publicznego adresu IP lub nazwy FQDN maszyny wirtualnej frontonu (NAT). Poniższy przykład zawiera listę nazw FQDN i publicznych adresów IP przypisanych do maszyny wirtualnej frontonu (NAT) w `myResourceGroup` grupie zasobów. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>Następne kroki
Czy chcesz skonfigurować własny translator adresów sieciowych na platformie Azure? Bezpłatnie i wydajnie korzystać z funkcji Open Source? Następnie PF to dobry wybór. Za pomocą szablonu PF-FreeBSD-Setup wystarczy pięć minut, aby skonfigurować zaporę NAT z równoważeniem obciążenia z działaniem okrężnym przy użyciu funkcji PF FreeBSD na platformie Azure dla wspólnego scenariusza serwera sieci Web. 

Jeśli chcesz poznać ofertę FreeBSD na platformie Azure, zapoznaj się z artykułem [wprowadzenie do FreeBSD na platformie Azure](freebsd-intro-on-azure.md).

Jeśli chcesz dowiedzieć się więcej o PF, zapoznaj się z tematem podręcznika [FreeBSD](https://www.freebsd.org/doc/handbook/firewalls-pf.html) lub [podręcznika użytkownika](https://www.freebsd.org/doc/handbook/firewalls-pf.html).
