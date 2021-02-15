---
title: Adresy zarządzania
description: Znajdź adresy zarządzania używane do kontrolowania App Service Environment. Zostały one skonfigurowane w tabeli tras, aby uniknąć problemów z routingiem asymetrycznym.
author: ccompy
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.topic: article
ms.date: 02/11/2021
ms.author: ccompy
ms.custom: seodec18, references_regions
ms.openlocfilehash: 7810ca71ccdd8714773ffd459ef38b4bf80de02c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371621"
---
# <a name="app-service-environment-management-addresses"></a>Adresy zarządzania App Service Environment

App Service Environment (ASE) jest wdrożeniem pojedynczej dzierżawy Azure App Service uruchamianej w Virtual Network platformy Azure.  Chociaż środowisko ASE działa w sieci wirtualnej, nadal musi być dostępne z wielu dedykowanych adresów IP, które są używane przez Azure App Service do zarządzania usługą.  W przypadku środowiska ASE ruch związany z zarządzaniem przechodzi przez sieć sterowaną przez użytkownika. Jeśli ten ruch jest blokowany lub ulega uszkodzeniu, środowisko ASE zostanie zawieszone. Aby uzyskać szczegółowe informacje na temat zależności sieci ASE, Przeczytaj [zagadnienia dotyczące sieci i App Service Environment][networking]. Aby uzyskać ogólne informacje na temat środowiska ASE, możesz zacząć od [wprowadzenia do App Service Environment][intro].

Wszystkie środowisk ASE mają publiczny adres VIP, do którego trafia ruch związany z zarządzaniem. Ruch przychodzący zarządzania z tych adresów pochodzi z portów 454 i 455 w publicznym adresie IP środowiska ASE. Ten dokument zawiera listę adresów źródłowych App Service dla ruchu związanego z zarządzaniem do środowiska ASE. Te adresy są również w tagu usługi IP o nazwie AppServiceManagement.

Adresy wymienione poniżej można skonfigurować w tabeli tras, aby uniknąć asymetrycznych problemów z routingiem z ruchem związanym z zarządzaniem. Trasy działają na poziomie IP i nie mają świadomości dotyczącej kierunku ruchu ani tego, że ruch jest częścią komunikatu odpowiedzi TCP. Jeśli adres odpowiedzi dla żądania TCP jest inny niż adres, na który został wysłany, wystąpił problem z routingiem asymetrycznym. Aby uniknąć problemów z routingiem asymetrycznym w przypadku ruchu związanego z zarządzaniem ASE, należy się upewnić, że odpowiedzi są wysyłane z tego samego adresu, do którego zostały wysłane. Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania środowiska ASE do działania w środowisku, w którym ruch wychodzący jest wysyłany lokalnie, przeczytaj artykuł [Konfigurowanie środowiska ASE z wymuszonym tunelowaniem][forcedtunnel]

## <a name="list-of-management-addresses"></a>Lista adresów zarządzania ##

| Region (Region) | Adresy |
|--------|-----------|
| Wszystkie regiony publiczne | 13.66.140.0, 13.67.8.128, 13.69.64.128, 13.69.227.128, 13.70.73.128, 13.71.170.64, 13.71.194.129, 13.75.34.192, 13.75.127.117, 13.77.50.128, 13.78.109.0, 13.89.171.0, 13.94.141.115, 13.94.143.126, 13.94.149.179, 20.36.106.128, 20.36.114.64, 23.102.135.246, 23.102.188.65, 40.69.106.128, 40.70.146.128, 40.71.13.64, 40.74.100.64, 40.78.194.128, 40.79.130.64, 40.79.178.128, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.112.242.192, 51.140.146.64, 51.140.210.128, 52.151.25.45, 52.162.106.192, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 52.231.18.64, 52.231.146.128, 65.52.172.237, 70.37.57.58, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 104.211.81.64, 104.211.146.128, 157.55.208.185, 191.233.203.64, 191.236.154.88, 52.181.183.11 |
| Microsoft Azure Government | 23.97.29.209, 13.72.53.37, 13.72.180.105, 52.181.183.11, 52.227.80.100, 52.182.93.40, 52.244.79.34, 52.238.74.16 |
| Azure China | 42.159.4.236, 42.159.80.125 |

## <a name="configuring-a-network-security-group"></a>Konfigurowanie sieciowej grupy zabezpieczeń

W przypadku sieciowych grup zabezpieczeń nie trzeba martwić się o poszczególne adresy ani utrzymywać własnej konfiguracji. Istnieje tag usługi IP o nazwie AppServiceManagement, który jest bieżąco ze wszystkimi adresami. Aby użyć tego tagu usługi IP w sieciowej grupy zabezpieczeń, przejdź do portalu, Otwórz interfejs użytkownika sieciowych grup zabezpieczeń, a następnie wybierz pozycję Reguły zabezpieczeń dla ruchu przychodzącego. Jeśli masz już istniejącą regułę dla ruchu związanego z zarządzaniem przychodzącą, Edytuj ją. Jeśli sieciowej grupy zabezpieczeń nie został utworzony przy użyciu środowiska ASE lub jeśli jest to wszystko nowe, a następnie wybierz pozycję **Dodaj**. Z listy rozwijanej Źródło wybierz pozycję **tag usługi**.  W tagu usługi źródłowej wybierz pozycję **AppServiceManagement**. Ustaw zakresy portów źródłowych na wartość \* , docelowy dla **dowolnego**, docelowy zakres portów na **454-455**, protokół do **TCP** i akcję **Zezwalaj**. W przypadku tworzenia reguły należy ustawić priorytet. 

![Tworzenie sieciowej grupy zabezpieczeń przy użyciu tagu usługi][1]

## <a name="configuring-a-route-table"></a>Konfigurowanie tabeli tras

Adresy zarządzania można umieścić w tabeli tras z następnym przeskokiem Internetu, aby upewnić się, że cały ruch przychodzący zarządzania będzie w stanie wrócić w tej samej ścieżce. Te trasy są konieczne podczas konfigurowania wymuszonego tunelowania. Aby utworzyć tabelę tras, można użyć portalu, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.  Poniżej przedstawiono polecenia służące do tworzenia tabeli tras przy użyciu interfejsu wiersza polecenia platformy Azure z poziomu wiersza polecenia programu PowerShell. 

```azurepowershell-interactive
$rg = "resource group name"
$rt = "route table name"
$location = "azure location"
$managementAddresses = "13.66.140.0", "13.67.8.128", "13.69.64.128", "13.69.227.128", "13.70.73.128", "13.71.170.64", "13.71.194.129", "13.75.34.192", "13.75.127.117", "13.77.50.128", "13.78.109.0", "13.89.171.0", "13.94.141.115", "13.94.143.126", "13.94.149.179", "20.36.106.128", "20.36.114.64", "23.102.135.246", "23.102.188.65", "40.69.106.128", "40.70.146.128", "40.71.13.64", "40.74.100.64", "40.78.194.128", "40.79.130.64", "40.79.178.128", "40.83.120.64", "40.83.121.56", "40.83.125.161", "40.112.242.192", "51.140.146.64", "51.140.210.128", "52.151.25.45", "52.162.106.192", "52.165.152.214", "52.165.153.122", "52.165.154.193", "52.165.158.140", "52.174.22.21", "52.178.177.147", "52.178.184.149", "52.178.190.65", "52.178.195.197", "52.187.56.50", "52.187.59.251", "52.187.63.19", "52.187.63.37", "52.224.105.172", "52.225.177.153", "52.231.18.64", "52.231.146.128", "65.52.172.237", "70.37.57.58", "104.44.129.141", "104.44.129.243", "104.44.129.255", "104.44.134.255", "104.208.54.11", "104.211.81.64", "104.211.146.128", "157.55.208.185", "191.233.203.64", "191.236.154.88", "52.181.183.11"

az network route-table create --name $rt --resource-group $rg --location $location
foreach ($ip in $managementAddresses) {
    az network route-table route create -g $rg --route-table-name $rt -n $ip --next-hop-type Internet --address-prefix ($ip + "/32")
}
```

Po utworzeniu tabeli tras należy ją ustawić w podsieci środowiska ASE.  

## <a name="get-your-management-addresses-from-api"></a>Pobierz adresy zarządzania z interfejsu API ##

Możesz wyświetlić listę adresów zarządzania pasujących do środowiska ASE z następującym wywołaniem interfejsu API.

```http
get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01
```

Interfejs API zwraca dokument JSON obejmujący wszystkie adresy przychodzące dla środowiska ASE. Lista adresów zawiera adresy zarządzania, adres VIP używany przez środowisko ASE i zakres adresów podsieci środowiska ASE.  

Aby wywołać interfejs API za pomocą [armclient](https://github.com/projectkudu/ARMClient) , użyj następujących poleceń, ale zastąp go identyfikatorem subskrypcji, grupą zasobów i nazwą środowiska ASE.  

```azurepowershell-interactive
armclient login
armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01
```

<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
