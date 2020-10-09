---
title: Zasady sieciowe usługi Azure Kubernetes | Microsoft Docs
description: Dowiedz się więcej na temat zasad sieciowych Kubernetes do zabezpieczania klastra Kubernetes.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: 5a6da7e65a9a3e962a2df37b062792fbb990d04d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "73159681"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Zasady sieciowe platformy Azure Kubernetes — przegląd

Zasady sieciowe zapewniają mikrosegmenty dla zasobników, tak jak sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń), zapewniają mikrosegmentowanie dla maszyn wirtualnych. Implementacja zasad sieciowych platformy Azure obsługuje standardową specyfikację zasad sieci Kubernetes. Możesz użyć etykiet, aby wybrać grupę wartości i zdefiniować listę reguł ruchu przychodzącego i wychodzącego, które określają, jaki jest dozwolony i pochodzący z tych zasobników. Dowiedz się więcej na temat zasad sieciowych Kubernetes w [dokumentacji Kubernetes](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Omówienie zasad sieciowych Kubernetes](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Zasady sieciowe platformy Azure działają w połączeniu z usługą Azure CNI, która zapewnia integrację sieci wirtualnej dla kontenerów. Jest ona obsługiwana tylko w węzłach systemu Linux. Implementacje konfigurują reguły tabeli adresów IP systemu Linux na podstawie zdefiniowanych zasad w celu wymuszenia filtrowania ruchu.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Planowanie zabezpieczeń dla klastra Kubernetes
W przypadku implementowania zabezpieczeń klastra należy użyć sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) do filtrowania ruchu North-South, czyli ruch wprowadzający i opuszczający podsieć klastra oraz używać zasad sieciowych Kubernetes dla ruchu East-West, czyli ruch między zasobnikami w klastrze.

## <a name="using-azure-kubernetes-network-policies"></a>Korzystanie z zasad sieciowych usługi Azure Kubernetes
Zasad sieciowych platformy Azure można używać w następujący sposób, aby zapewnić mikrosegmentację dla zasobników.

### <a name="acs-engine"></a>ACS-Engine
ACS-Engine to narzędzie, które generuje szablon Azure Resource Manager na potrzeby wdrożenia klastra Kubernetes na platformie Azure. Konfiguracja klastra jest określona w pliku JSON, który jest przekazywany do narzędzia podczas generowania szablonu. Aby uzyskać więcej informacji na temat wszystkich obsługiwanych ustawień klastra, zobacz Microsoft Azure Container Service Engine — Cluster Definition (Usługa Microsoft Azure Container Service Engine — definicja klastra).

Aby włączyć zasady dla klastrów wdrożonych przy użyciu aparatu ACS, należy określić wartość ustawienia networkPolicy w pliku definicji klastra na "Azure".

#### <a name="example-configuration"></a>Przykładowa konfiguracja

Poniższy Przykładowa konfiguracja JSON tworzy nową sieć wirtualną i podsieć, a następnie wdraża w niej klaster Kubernetes przy użyciu usługi Azure CNI. Zalecamy, aby edytować plik JSON przy użyciu "Notatnika". 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="creating-your-own-kubernetes-cluster-in-azure"></a>Tworzenie własnego klastra Kubernetes na platformie Azure
Implementacja może służyć do zapewnienia zasad sieciowych dla jednostek w klastrach Kubernetes, które wdrażasz samodzielnie, bez polegania na narzędziach takich jak aparat ACS. W takim przypadku należy najpierw zainstalować wtyczkę CNI i włączyć ją na każdej maszynie wirtualnej w klastrze. Aby uzyskać szczegółowe instrukcje, zobacz [Wdrażanie wtyczki dla samodzielnie wdrażanego klastra Kubernetes](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Po wdrożeniu klastra uruchom następujące polecenie, `kubectl` Aby pobrać i zastosować zasady sieci platformy Azure *elementu daemonset* do klastra.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
Rozwiązanie to również Open Source, a kod jest dostępny w [repozytorium sieci kontenera platformy Azure](https://github.com/Azure/azure-container-networking/tree/master/npm).



## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [usłudze Azure Kubernetes Service](../aks/intro-kubernetes.md).
-  Dowiedz się więcej na temat [sieci kontenerów](container-networking-overview.md).
- [Wdróż wtyczkę](deploy-container-networking.md) dla klastrów Kubernetes lub kontenerów platformy Docker.
