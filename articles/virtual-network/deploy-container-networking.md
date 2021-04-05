---
title: Wdrażanie sieci kontenerów w usłudze Azure Virtual Network | Microsoft Docs
description: Dowiedz się, jak wdrożyć wtyczkę usługi Azure Virtual Network Container Network Interface (CNI) dla klastrów Kubernetes.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/18/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: b95b3cfdf8fea6e31015d945566803569b4ba064
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222925"
---
# <a name="deploy-the-azure-virtual-network-container-network-interface-plug-in"></a>Wdrażanie wtyczki CNI w usłudze Azure Virtual Network

Wtyczka CNI w usłudze Azure Virtual Network jest instalowana na maszynie wirtualnej platformy Azure i dodaje funkcje sieci wirtualnej do zasobników Kubernetes i kontenerów platformy Docker. Aby uzyskać więcej informacji na temat tej wtyczki, zobacz [Enable containers to use Azure Virtual Network capabilities (Korzystanie z funkcji usługi Azure Virtual Network w kontenerach)](container-networking-overview.md). Wtyczka może być również używana w usłudze Azure Kubernetes Service (AKS). Wybranie opcji [Sieć zaawansowana](../aks/configure-azure-cni.md?toc=%2fazure%2fvirtual-network%2ftoc.json) automatycznie umieszcza kontenery usługi AKS w sieci wirtualnej.

## <a name="deploy-plug-in-for-acs-engine-kubernetes-cluster"></a>Wdrażanie wtyczki dla klastra Kubernetes usługi ACS-Engine

Usługa ACS-Engine wdraża klaster Kubernetes przy użyciu szablonu usługi Azure Resource Manager. Konfiguracja klastra jest określona w pliku JSON, który jest przekazywany do narzędzia podczas generowania szablonu. Aby uzyskać więcej informacji na temat wszystkich obsługiwanych ustawień klastra, zobacz [Microsoft Azure Container Service Engine — Cluster Definition (Usługa Microsoft Azure Container Service Engine — definicja klastra)](https://github.com/Azure/acs-engine/blob/master/docs/clusterdefinition.md). Ta wtyczka to domyślna wtyczka sieciowa w przypadku klastrów utworzonych przy użyciu usługi ACS Engine. Następujące ustawienia konfiguracji sieci mają znaczenie podczas konfigurowania wtyczki:

  | Ustawienie                              | Opis                                                                                                           |
  |--------------------------------------|------------------------------------------------------------------------------------------------------                 |
  | firstConsecutiveStaticIP             | Adres IP przydzielony do węzła głównego. Jest to ustawienie obowiązkowe.                                     |
  | clusterSubnet w obszarze kubernetesConfig | Zakres CIDR podsieci sieci wirtualnej, w której jest wdrażany klaster, zawierający adresy IP przydzielane do zasobników   |
  | vnetSubnetId w obszarze masterProfile     | Określa identyfikator zasobu usługi Azure Resource Manager podsieci, w której ma zostać wdrożony klaster                    |
  | vnetCidr                             | Zakres CIDR sieci wirtualnej, w której jest wdrażany klaster                                                             |
  | max-Pods w obszarze kubeletConfig         | Maksymalna liczba zasobników na każdej maszynie wirtualnej agenta. W przypadku wtyczki domyślna wartość to 30. Maksymalna wartość to 250.  |

### <a name="example-configuration"></a>Przykładowa konfiguracja

Poniższy przykład kodu json definiuje klaster o następujących właściwościach:
-   1 węzeł główny i 2 węzły agentów 
-   Jest wdrażany w podsieci o nazwie *KubeClusterSubnet* (10.0.0.0/20), w której znajdują się zarówno węzeł główny, jak i węzły agentów.

```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
        "clusterSubnet": "10.0.0.0/20" --> Subnet allocated for the cluster
      }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "ACSKubeMaster",
      "vmSize": "Standard_A2",
      "vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<Vnet Name>/subnets/KubeClusterSubnet",
      "firstConsecutiveStaticIP": "10.0.1.50", --> IP address allocated to the Master node
"vnetCidr": "10.0.0.0/16" --> Virtual network address space
    },
    "agentPoolProfiles": [
      {
        "name": "k8sagentpoo1",
        "count": 2,
        "vmSize": "Standard_A2_v2",
"vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<VNet Name>/subnets/KubeClusterSubnet",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
    "linuxProfile": {
      "adminUsername": "KubeServerAdmin",
      "ssh": {
        "publicKeys": [
          {…}
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "dd438987-aa12-4754-b47d-375811889714",
      "secret": "azure123"
    }
  }
}
```

## <a name="deploy-plug-in-for-a-kubernetes-cluster"></a>Wdrażanie wtyczki w przypadku klastra Kubernetes

Wykonaj następujące czynności, aby zainstalować wtyczkę na każdej maszynie wirtualnej platformy Azure w klastrze Kubernetes:

1. [Pobierz i zainstaluj wtyczkę](#download-and-install-the-plug-in).
2. Wstępnie przydziel na każdej maszynie wirtualnej pulę adresów IP sieci wirtualnej, zawierającą adresy IP, które zostaną przypisane do zasobników. Każda maszyna wirtualna ma prywatny adres IP podstawowej sieci wirtualnej na każdym interfejsie sieciowym. Pula adresów IP dla zasobników zostanie dodana jako adresy pomocnicze (*ipconfigs*) w interfejsie sieciowym maszyny wirtualnej przy użyciu jednej z następujących opcji:

   - **Interfejs wiersza polecenia**: [Assign multiple IP addresses using the Azure CLI (Przypisywanie wielu adresów IP przy użyciu interfejsu wiersza polecenia platformy Azure)](virtual-network-multiple-ip-addresses-cli.md)
   - **PowerShell**: [Assign multiple IP addresses using PowerShell (Przypisywanie wielu adresów IP przy użyciu programu PowerShell)](virtual-network-multiple-ip-addresses-powershell.md)
   - **Portal**: [Assign multiple IP addresses using the Azure portal (Przypisywanie wielu adresów IP przy użyciu witryny Azure Portal)](virtual-network-multiple-ip-addresses-portal.md)
   - **Szablon usługi Azure Resource Manager**: [Assign multiple IP addresses using templates (Przypisywanie wielu adresów IP przy użyciu szablonów)](./template-samples.md)

   Upewnij się, że dodano wystarczająco dużo adresów IP dla wszystkich zasobników, które chcesz uruchomić na maszynie wirtualnej.

3. Wybierz wtyczkę dostarczającą sieć dla klastra, przekazując do elementu Kubelet opcję wiersza polecenia `–network-plugin=cni` podczas tworzenia klastra. Domyślnie platforma Kubernetes szuka wtyczki i pliku konfiguracji w katalogach, w których zostały już zainstalowane.
4. Jeśli chcesz, aby zasobniki miały dostęp do Internetu, dodaj następującą regułę *iptables* na maszynach wirtualnych z systemem Linux dla ruchu internetowego opartego na źródle (SNAT). W poniższym przykładzie zakres adresów IP to 10.0.0.0/8.

   ```bash
   iptables -t nat -A POSTROUTING -m iprange ! --dst-range 168.63.129.16 -m
   addrtype ! --dst-type local ! -d 10.0.0.0/8 -j MASQUERADE
   ```

   Reguły ruchu NAT, który nie jest skierowany do konkretnych zakresów adresów IP. Zakłada się, że cały ruch wykraczający poza poprzednie zakresy to ruch internetowy. Możesz określić zakresy adresów IP sieci wirtualnej maszyny wirtualnej, równorzędnych sieci wirtualnych i sieci lokalnych.

   Maszyny wirtualne z systemem Windows automatycznie wyszukują ruch NAT, którego element docelowy znajduje się poza podsiecią, do której należy maszyna wirtualna. Nie można określić niestandardowych zakresów adresów IP.

Po wykonaniu poprzednich kroków zasobnikom uruchomionym na maszynach wirtualnych agenta platformy Kubernetes są automatycznie przypisywane prywatne adresy IP z sieci wirtualnej.

## <a name="deploy-plug-in-for-docker-containers"></a>Wdrażanie wtyczki w przypadku kontenerów platformy Docker

1. [Pobierz i zainstaluj wtyczkę](#download-and-install-the-plug-in).
2. Utwórz kontenery platformy Docker za pomocą następującego polecenia:

   ```
   ./docker-run.sh \<container-name\> \<container-namespace\> \<image\>
   ```

Kontenery automatycznie otrzymają adresy IP z przydzielonej puli. Jeśli chcesz równoważyć obciążenie w kontenerach platformy Docker, muszą one być umieszczone za programowym modułem równoważenia obciążenia i należy skonfigurować sondę modułu równoważenia obciążenia w taki sam sposób, w jaki utworzono zasadę i sondy dla maszyny wirtualnej.

### <a name="cni-network-configuration-file"></a>Plik konfiguracji wtyczki CNI

Plik konfiguracji wtyczki CNI jest opisany w formacie JSON. Jego domyślna lokalizacja to `/etc/cni/net.d` w systemie Linux, a `c:\cni\netconf` w systemie Windows. Ten plik określa konfigurację wtyczki i różni się między systemami Windows i Linux. Następujący kod json to przykładowy plik konfiguracji dla systemu Linux wraz z wyjaśnieniem niektórych kluczowych ustawień. Nie musisz wprowadzać żadnych zmian w pliku:

```json
{
       "cniVersion":"0.3.0",
       "name":"azure",
       "plugins":[
          {
             "type":"azure-vnet",
             "mode":"bridge",
             "bridge":"azure0",
             "ipam":{
                "type":"azure-vnet-ipam"
             }
          },
          {
             "type":"portmap",
             "capabilities":{
                "portMappings":true
             },
             "snat":true
          }
       ]
}
```

#### <a name="settings-explanation"></a>Wyjaśnienie ustawień

- **cniVersion**: wtyczki CNI usługi Azure Virtual Network obsługują wersje 0.3.0 i 0.3.1 [specyfikacji wtyczki CNI](https://github.com/containernetworking/cni/blob/master/SPEC.md).
- **name**: nazwa sieci. Tę właściwość można ustawić na dowolną unikatową wartość.
- **type**: nazwa wtyczki sieciowej. Ustaw tu wartość *azure-vnet*.
- **mode**: tryb działania. To pole jest opcjonalne. Jedynym obsługiwanym trybem jest tryb „bridge”. Aby uzyskać więcej informacji, zobacz sekcję poświęconą [trybom działania](https://github.com/Azure/azure-container-networking/blob/master/docs/network.md).
- **bridge**: nazwa mostka, który będzie służył do łączenia z kontenerów w siecią wirtualną. To pole jest opcjonalne. Jeśli pole zostanie pominięte, wtyczka automatycznie wybierze unikatową nazwę na podstawie indeksu interfejsu głównego.
- **ipam type**: nazwa wtyczki IPAM. Zawsze powinna być tu ustawiona wartość *azure-vnet-ipam*.

## <a name="download-and-install-the-plug-in"></a>Pobieranie i instalowanie wtyczki

Pobierz wtyczkę z witryny [GitHub](https://github.com/Azure/azure-container-networking/releases). Pobierz najnowszą wersję dla używanej platformy:

- **Linux**: [Azure-VNET-CNI-Linux-amd64- \<version no.\> . tgz](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-linux-amd64-v1.0.12-rc3.tgz)
- **Windows**: [Azure-VNET-CNI-Windows-amd64- \<version no.\> . zip](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-windows-amd64-v1.0.12-rc3.zip)

Skopiuj skrypt instalacji dla systemu [Linux](https://github.com/Azure/azure-container-networking/blob/master/scripts/install-cni-plugin.sh) lub [Windows](https://github.com/Azure/azure-container-networking/blob/master/scripts/Install-CniPlugin.ps1) na komputer. Zapisz skrypt w katalogu `scripts` na komputerze i nazwij plik `install-cni-plugin.sh` w przypadku systemu Linux lub `install-cni-plugin.ps1` w przypadku systemu Windows. Aby zainstalować wtyczkę, uruchom odpowiedni skrypt dla swojej platformy, określając wersję używanej wtyczki. Możesz na przykład określić wersję *v1.0.12 rc3*:

   ```bash
   \$scripts/install-cni-plugin.sh [version]
   ```

   ```powershell
   scripts\\ install-cni-plugin.ps1 [version]
   ```

Skrypt zainstaluje wtyczkę w lokalizacji `/opt/cni/bin` w przypadku systemu Linux, a `c:\cni\bin` w przypadku systemu Windows. Zainstalowana wtyczka zawiera prosty plik konfiguracji sieci, który działa po instalacji. Nie musi być aktualizowany. Aby dowiedzieć się więcej na temat ustawień w tym pliku, zobacz [CNI network configuration file (Plik konfiguracji sieci wtyczki CNI)](#cni-network-configuration-file).