---
title: Wyłączenie i ponowne włączenie dodatku Application Gateway transferu danych przychodzących dla klastra usługi Azure Kubernetes Service
description: Ten artykuł zawiera informacje dotyczące sposobu wyłączania i ponownego włączania dodatku AGIC dla klastra AKS
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: fe4da0435731c536a723cb2cb43428166456360b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84807956"
---
# <a name="disable-and-re-enable-agic-add-on-for-your-aks-cluster"></a>Wyłączenie i ponowne włączenie dodatku AGIC dla klastra AKS
Application Gateway, który został wdrożony jako dodatek AKS, umożliwia włączenie i wyłączenie dodatku z jednym wierszem w interfejsie wiersza polecenia platformy Azure. Cykl życia Application Gateway różni się w zależności od tego, czy Application Gateway został utworzony przez dodatek AGIC, czy został on wdrożony niezależnie od dodatku AGICu w systemie. Można uruchomić to samo polecenie, aby ponownie włączyć dodatek AGIC, jeśli kiedykolwiek go wyłączysz, lub włączyć dodatek AGIC przy użyciu istniejącego klastra AKS i Application Gateway.

## <a name="disabling-agic-add-on-with-associated-application-gateway"></a>Wyłączanie dodatku AGIC przy użyciu skojarzonych Application Gateway 
Jeśli dodatek AGIC automatycznie wdrożon Application Gateway po pierwszym ustawieniu wszystkiego, a następnie wyłączenie dodatku AGIC domyślnie spowoduje usunięcie Application Gateway na podstawie kilku kryteriów. Istnieją dwa kryteria, których szuka dodatek AGIC, aby określić, czy należy usunąć skojarzone Application Gateway po jego wyłączeniu:
- Czy Application Gateway jest skojarzony z dodatkiem AGIC w grupie zasobów węzła MC_ *? 
- Czy Application Gateway, do którego jest skojarzony dodatek AGIC, ma tag "created-by: Ingres-appgw"? Tag jest używany przez AGIC do określenia, czy Application Gateway został wdrożony przez dodatek, czy nie. 

Jeśli spełnione są oba kryteria, dodatek AGIC usunie Application Gateway utworzony, gdy dodatek jest wyłączony; nie spowoduje to jednak usunięcia publicznego adresu IP lub podsieci, w której Application Gateway został wdrożony przy użyciu/in. Jeśli pierwsze kryterium nie jest spełnione, nie ma znaczenia, czy Application Gateway ma tag "utworzony przez: appgw" — wyłączenie dodatku nie spowoduje usunięcia Application Gateway. Analogicznie, jeśli drugie kryteria nie są spełnione, tj. Application Gateway brak tego tagu, a następnie wyłączenie dodatku nie spowoduje usunięcia Application Gateway w grupie zasobów węzła MC_ *. 

> [!TIP] 
> Jeśli nie chcesz, aby Application Gateway usunięty podczas wyłączania dodatku, ale spełnia on oba kryteria, usuń tag "utworzony przez: appgw", aby zapobiec usunięciu Application Gateway przez dodatek. 

Aby wyłączyć dodatek AGIC, uruchom następujące polecenie: 
```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a ingress-appgw 
```

## <a name="enable-agic-add-on-on-existing-application-gateway-and-aks-cluster"></a>Włącz dodatek AGIC w istniejącym klastrze Application Gateway i AKS
Jeśli kiedykolwiek wyłączysz dodatek AGIC i konieczne będzie ponowne włączenie dodatku lub włączenie dodatku przy użyciu istniejącego klastra Application Gateway i AKS, uruchom następujące polecenie:.

```azurecli-interactive
appgwId=$(az network application-gateway show -n <application-gateway-name> -g <resource-group-name> -o tsv --query "id") 
az aks enable-addons -n <AKS-cluster-name> -g <AKS-cluster-resource-group> -a ingress-appgw --appgw-id $appgwId
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat włączania dodatku AGIC przy użyciu istniejącego klastra Application Gateway i AKS, zobacz [AGIC Deployment brownfield](tutorial-ingress-controller-add-on-existing.md).