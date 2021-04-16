---
title: Adresy zarządzania
description: Znajdź adresy zarządzania używane do kontrolowania App Service Environment. Skonfigurowano je w tabeli tras, aby uniknąć problemów z routingiem asymetrycznym.
author: ccompy
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.topic: article
ms.date: 03/22/2021
ms.author: ccompy
ms.custom: seodec18, references_regions, devx-track-azurecli
ms.openlocfilehash: aaaa190935da8c016c43832712f553a116332974
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482572"
---
# <a name="app-service-environment-management-addresses"></a>App Service Environment zarządzania

Usługa App Service Environment (ASE) to wdrożenie pojedynczej dzierżawy usługi Azure App Service uruchamiane w usłudze Azure Virtual Network (VNet).  Mimo że ase działa w sieci wirtualnej, musi być nadal dostępny z wielu dedykowanych adresów IP, które są używane przez Azure App Service do zarządzania usługą.  W przypadku środowisk ASE ruch zarządzania przechodzi przez sieć kontrolowaną przez użytkownika. Jeśli ten ruch zostanie zablokowany lub nieprawidłowo przekierowany, zostanie wstrzymany. Aby uzyskać szczegółowe informacje na temat zależności sieciowych w programie ASE, zapoznaj się z [tematem Networking considerations and the App Service Environment][networking](Zagadnienia dotyczące sieci) i App Service Environment . Aby uzyskać ogólne informacje na temat programu ASE, możesz rozpocząć od wprowadzenia do [App Service Environment][intro].

Wszystkie grupy ASE mają publiczny adres VIP, do którego wchodzi ruch zarządzania. Przychodzący ruch zarządzania z tych adresów pochodzi z portów 454 i 455 na publicznym adresie VIP usługi ASE. Ten dokument zawiera listę App Service źródłowych dla ruchu zarządzania do ase. Te adresy znajdują się również w tagu usługi IP o nazwie AppServiceManagement.

Adresy zanotowane poniżej można skonfigurować w tabeli tras, aby uniknąć problemów z routingiem asymetrycznym związanych z ruchem zarządzania. Trasy działają na poziomie adresu IP i nie mają świadomości kierunku ruchu lub że ruch jest częścią komunikatu odpowiedzi TCP. Jeśli adres odpowiedzi dla żądania TCP jest inny niż adres, na który wysłano żądanie, występuje problem z routingiem asymetrycznym. Aby uniknąć problemów z routingiem asymetrycznym w przypadku ruchu związanego z zarządzaniem usługą ASE, należy upewnić się, że odpowiedzi są wysyłane z tego samego adresu, na który zostały wysłane. Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania środowiska ASE do działania w środowisku, w którym ruch wychodzący jest wysyłany lokalnie, przeczytaj konfigurowanie środowiska ASE przy [użyciu wymuszonego tunelowania][forcedtunnel]

## <a name="list-of-management-addresses"></a>Lista adresów zarządzania ##

| Region | Adresy |
|--------|-----------|
| Wszystkie regiony publiczne | 13.66.140.0, 13.67.8.128, 13.69.64.128, 13.69.227.128, 13.70.73.128, 13.71.170.64, 13.71.194.129, 13.75.34.192, 13.75.127.117, 13.77.50.128, 13.78.109.0, 13.89.171.0, 13.94.141.115, 13.94.143.126, 13.94.149.179, 20.36.106.128, 20.36.114.64, 20.37.74.128, 23.96.195.3, 23.102.135.246, 23.102.188.65, 40.69.106.128, 40.70.146.128, 40.71.13.64, 40.74.100.64, 40.78.194.128, 40.79.130.64, 40.79.178.128, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.112.242.192, 51.107.58.192, 51.107.154.192, 51.116.58.192, 51.116.155.0, 51.120.99.0, 51.120.219.0, 51.140.146.64, 51.140.210.128, 52.151.25.45, 52.162.106.192, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 52.231.18.64, 52.231.146.128, 65.52.172.237, 65.52.250.128, 70.37.57.58, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 104.211.81.64, 104.211.146.128, 157.55.208.185, 191.233.50.128, 191.233.203.64, 191.236.154.88 |
| Microsoft Azure Government | 23.97.29.209, 13.72.53.37, 13.72.180.105, 52.181.183.11, 52.227.80.100, 52.182.93.40, 52.244.79.34, 52.238.74.16 |
| Azure China | 42.159.4.236, 42.159.80.125 |

## <a name="configuring-a-network-security-group"></a>Konfigurowanie sieciowej grupy zabezpieczeń

W przypadku sieciowych grup zabezpieczeń nie trzeba martwić się o poszczególne adresy ani konserwować własnej konfiguracji. Istnieje tag usługi IP o nazwie AppServiceManagement, który jest na bieżąco z wszystkimi adresami. Aby użyć tego tagu usługi IP w sieciowej grupie zabezpieczeń, przejdź do portalu, otwórz interfejs użytkownika sieciowych grup zabezpieczeń i wybierz pozycję Reguły zabezpieczeń dla ruchu przychodzącego. Jeśli masz istniejącą regułę dla ruchu przychodzącego zarządzania, edytuj go. Jeśli ta sieciowa żadna z sieciowych sieci nie została utworzona w twoim asee lub jeśli jest nowa, wybierz pozycję **Dodaj**. Z listy rozwijanej Źródło wybierz pozycję **Tag usługi**.  W obszarze Tag usługi źródłowej wybierz **pozycję AppServiceManagement.** Ustaw zakresy portów źródłowych na , Destination (Miejsce docelowe) na Any (Dowolne), Destination port ranges (Zakresy portów docelowych) na \* **454–455**, Protocol (Protokół) na **TCP** i Action (Akcja) na **Zezwalaj.**  W przypadku tworzenia reguły należy ustawić priorytet. 

![tworzenie sieciowej sieciowej sieciowej sieciowej z tagiem usługi][1]

## <a name="configuring-a-route-table"></a>Konfigurowanie tabeli tras

Adresy zarządzania można umieścić w tabeli tras z następnym przeskoku Internetu, aby upewnić się, że cały ruch przychodzący zarządzania może wrócić przez tę samą ścieżkę. Te trasy są potrzebne podczas konfigurowania wymuszonego tunelowania. Aby utworzyć tabelę tras, możesz użyć portalu, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.  Poniżej przedstawiono polecenia służące do tworzenia tabeli tras przy użyciu interfejsu wiersza polecenia platformy Azure z poziomu wiersza polecenia programu PowerShell. 

```azurepowershell-interactive
$rg = "resource group name"
$rt = "route table name"
$location = "azure location"
$managementAddresses = "13.66.140.0", "13.67.8.128", "13.69.64.128", "13.69.227.128", "13.70.73.128", "13.71.170.64", "13.71.194.129", "13.75.34.192", "13.75.127.117", "13.77.50.128", "13.78.109.0", "13.89.171.0", "13.94.141.115", "13.94.143.126", "13.94.149.179", "20.36.106.128", "20.36.114.64", "20.37.74.128", "23.96.195.3", "23.102.135.246", "23.102.188.65", "40.69.106.128", "40.70.146.128", "40.71.13.64", "40.74.100.64", "40.78.194.128", "40.79.130.64", "40.79.178.128", "40.83.120.64", "40.83.121.56", "40.83.125.161", "40.112.242.192", "51.107.58.192", "51.107.154.192", "51.116.58.192", "51.116.155.0", "51.120.99.0", "51.120.219.0", "51.140.146.64", "51.140.210.128", "52.151.25.45", "52.162.106.192", "52.165.152.214", "52.165.153.122", "52.165.154.193", "52.165.158.140", "52.174.22.21", "52.178.177.147", "52.178.184.149", "52.178.190.65", "52.178.195.197", "52.187.56.50", "52.187.59.251", "52.187.63.19", "52.187.63.37", "52.224.105.172", "52.225.177.153", "52.231.18.64", "52.231.146.128", "65.52.172.237", "65.52.250.128", "70.37.57.58", "104.44.129.141", "104.44.129.243", "104.44.129.255", "104.44.134.255", "104.208.54.11", "104.211.81.64", "104.211.146.128", "157.55.208.185", "191.233.50.128", "191.233.203.64", "191.236.154.88"

az network route-table create --name $rt --resource-group $rg --location $location
foreach ($ip in $managementAddresses) {
    az network route-table route create -g $rg --route-table-name $rt -n $ip --next-hop-type Internet --address-prefix ($ip + "/32")
}
```

Po utworzeniu tabeli tras należy ustawić ją w podsieci ase.  

## <a name="get-your-management-addresses-from-api"></a>Uzyskiwanie adresów zarządzania z interfejsu API ##

Możesz wyświetlić listę adresów zarządzania, które są zgodne ze swoimi usługami ASE, za pomocą następującego wywołania interfejsu API.

```http
get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01
```

Interfejs API zwraca dokument JSON, który zawiera wszystkie adresy przychodzące dla twojego zestawu ase. Lista adresów zawiera adresy zarządzania, adres VIP używany przez usługę ASE oraz zakres adresów podsieci ase.  

Aby wywołać interfejs API za pomocą [klienta armclient,](https://github.com/projectkudu/ARMClient) użyj następujących poleceń, ale zastąp zmienną identyfikatorem subskrypcji, grupą zasobów i nazwą ase.  

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
