---
title: Migrowanie z usługi Azure Red Hat OpenShift 3.11 do usługi Azure Red Hat OpenShift 4
description: Migrowanie z usługi Azure Red Hat OpenShift 3.11 do usługi Azure Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 08/13/2020
keywords: Migracja, ARO, OpenShift, Red Hat
ms.openlocfilehash: f9bfc924581d5dbe33c7c2683a0f6083cb2abc23
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071039"
---
# <a name="migrate-from-azure-red-hat-openshift-311-to-azure-red-hat-openshift-4"></a>Migrowanie z usługi Azure Red Hat OpenShift 3,11 do platformy Azure Red Hat OpenShift 4

Usługa Azure Red Hat OpenShift na OpenShift 4 zapewnia Kubernetes 1,16 w systemie operacyjnym Red Hat Core, prywatnych klastrach, udostępnia obsługę sieci wirtualnej i pełną rolę administratora klastra. Ponadto dostępne są liczne nowe funkcje, takie jak obsługa platformy operatorów, centrum operatora i sieci OpenShift usługi.

Aby pomyślnie przejść z usługi Azure Red Hat OpenShift 3,11 do platformy Azure Red Hat OpenShift 4, należy zapoznać się z [różnicami w obszarze magazyn, Sieć, rejestrowanie, zabezpieczenia i monitorowanie](https://docs.openshift.com/container-platform/4.4/migration/migrating_3_4/planning-migration-3-to-4.html).

W tym artykule pokazano, jak przeprowadzić migrację z klastra Red Hat OpenShift 3,11 na platformie Azure Red Hat 4.

> [!NOTE]
> Narzędzi do migracji Red Hat OpenShift, takich jak narzędzie do migracji płaszczyzny kontroli i narzędzie do migracji aplikacji klastra (kamera) nie mogą być używane w przypadku klastrów Red Hat OpenShift 3,11 platformy Azure.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster usługi Azure Red Hat OpenShift 3,11.

## <a name="create-a-target-azure-red-hat-openshift-4-cluster"></a>Tworzenie docelowego klastra usługi Azure Red Hat OpenShift 4

Najpierw [Utwórz klaster usługi Azure Red Hat OpenShift 4](tutorial-create-cluster.md) , który ma być używany jako klaster docelowy. W tym miejscu zostanie użyta Podstawowa konfiguracja. Jeśli interesuje Cię inne ustawienia, zobacz [samouczek Tworzenie klastra usługi Azure Red Hat OpenShift 4](tutorial-create-cluster.md).

Utwórz sieć wirtualną z dwiema pustymi podsieciami dla węzłów głównych i procesów roboczych.

```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
```

Następnie użyj następującego polecenia, aby utworzyć klaster.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

## <a name="configure-the-target-openshift-4-cluster"></a>Konfigurowanie docelowego klastra OpenShift 4

### <a name="authentication"></a>Authentication

Aby użytkownicy mogli korzystać z usługi Azure Red Hat OpenShift, muszą najpierw uwierzytelniać się w klastrze. Warstwa uwierzytelniania identyfikuje użytkownika skojarzonego z żądaniami do interfejsu API Red Hat OpenShift platformy Azure. Następnie warstwa autoryzacji używa informacji o użytkowniku żądającym, aby określić, czy żądanie jest dozwolone.

Podczas tworzenia klastra usługi Azure Red Hat OpenShift 4 zostaje utworzony tymczasowy użytkownik administracyjny. [Połącz się z klastrem](tutorial-connect-cluster.md), Dodaj użytkowników i grupy oraz [Skonfiguruj odpowiednie uprawnienia](https://docs.openshift.com/container-platform/4.6/authentication/understanding-authentication.html) dla obu tych programów.

### <a name="networking"></a>Sieć

W przypadku usługi Azure Red Hat OpenShift 4 używane są kilka różnych operatorów do konfigurowania sieci w klastrze: [operator sieci klastra](https://docs.openshift.com/container-platform/4.6/networking/cluster-network-operator.html#nw-cluster-network-operator_cluster-network-operator), [operator DNS](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html)i [operator](https://docs.openshift.com/container-platform/4.6/networking/ingress-operator.html)transferu danych przychodzących. Aby uzyskać więcej informacji na temat konfigurowania sieci w klastrze Red Hat OpenShift 4 na platformie Azure, zobacz [diagram sieci](concepts-networking.md) i [Informacje o sieci](https://docs.openshift.com/container-platform/4.6/networking/understanding-networking.html).

### <a name="storage"></a>Storage
Usługa Azure Red Hat OpenShift 4 obsługuje następujące wtyczki PersistentVolume:

- Magazyn elastycznych bloków AWS (EBS)
- Dysk platformy Azure
- Plik platformy Azure
- Dysk trwały GCE
- HostPath
- iSCSI
- Wolumin lokalny
- NFS
- Magazyn kontenerów Red Hat OpenShift

Aby uzyskać informacje dotyczące konfigurowania tych typów magazynów, zobacz [Konfigurowanie magazynu trwałego](https://access.redhat.com/documentation/azure_red_hat_openshift/4/html/storage/configuring-persistent-storage).

### <a name="registry"></a>Rejestr

W systemie Azure Red Hat OpenShift 4 można tworzyć obrazy z kodu źródłowego, wdrażać je i zarządzać ich cyklem życia. Aby to umożliwić, usługa Azure Red Hat OpenShift zapewnia 4 [wewnętrzny, zintegrowany rejestr obrazów kontenerów](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html) , który można wdrożyć w środowisku usługi Azure Red Hat OpenShift do lokalnego zarządzania obrazami.

W przypadku korzystania z rejestrów zewnętrznych, takich jak [Azure Container Registry](../container-registry/index.yml), rejestrów z systemem [Red Hat](ttps://docs.openshift.com/container-platform/4.6/registry/registry-options.html#registry-quay-overview_registry-options)lub [uwierzytelnianie z włączonym systemem Red Hat](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html#registry-authentication-enabled-registry-overview_registry-options), wykonaj kroki w celu dostarczenia poświadczeń do klastra, aby umożliwić klastrowi dostęp do repozytoriów.

### <a name="monitoring"></a>Monitorowanie

System Red Hat OpenShift obejmuje wstępnie skonfigurowany, wstępnie zainstalowany i samoczynny stos monitorowania, który jest oparty na projekcie Prometheus Open Source i szerszym systemie ekonomicznym. Zapewnia ona monitorowanie składników klastra i zawiera zestaw alertów umożliwiających natychmiastowe powiadomienie administratora klastra o wszelkich występujących problemach i zestawie pulpitów nawigacyjnych Grafana. Stos monitorowania klastra jest obsługiwany tylko w przypadku monitorowania klastrów Red Hat OpenShift platformy Azure. Aby uzyskać więcej informacji, zobacz [monitorowanie klastrów na platformie Azure Red Hat OpenShift](https://docs.openshift.com/container-platform/4.6/monitoring/understanding-the-monitoring-stack.html).

Jeśli używasz [Azure monitor do kontenerów dla usługi Azure Red Hat OpenShift 3,11](../azure-monitor/insights/container-insights-azure-redhat-setup.md), możesz również włączyć Azure monitor dla kontenerów dla [klastrów usługi Azure Red Hat OpenShift 4](../azure-monitor/insights/container-insights-azure-redhat4-setup.md) i nadal korzystać z tego samego obszaru roboczego log Analytics.

## <a name="move-your-dns-or-load-balancer-configuration-to-the-new-cluster"></a>Przenoszenie konfiguracji usługi DNS lub równoważenia obciążenia do nowego klastra

Jeśli używasz usługi Azure Traffic Manager, Dodaj punkty końcowe, aby odwoływać się do klastra docelowego i określić priorytety tych punktów końcowych.

## <a name="deploy-application-to-your-target-cluster"></a>Wdrażanie aplikacji w klastrze docelowym

Po poprawnym skonfigurowaniu klastra docelowego dla obciążenia, [Nawiąż połączenie z klastrem](tutorial-connect-cluster.md) i Utwórz wymagane aplikacje, składniki lub usługi dla projektów. W systemie Azure Red Hat OpenShift można tworzyć te dane przy użyciu usług git, obrazów kontenerów, wykazu dla deweloperów Red Hat, pliku dockerfile, definicji YAML/JSON lub wybierając usługę bazy danych z wykazu.

## <a name="delete-your-source-cluster"></a>Usuwanie klastra źródłowego
Po potwierdzeniu, że klaster Red Hat OpenShift 4 jest prawidłowo skonfigurowany, Usuń klaster Red Hat OpenShift 3,11 platformy Azure.

```
az openshift delete --name $CLUSTER_NAME
                    --resource-group $RESOURCE_GROUP
                    [--no-wait]
                    [--subscription]
                    [--yes]
```
## <a name="next-steps"></a>Następne kroki
Zapoznaj się z dokumentacją Red Hat OpenShift [tutaj](https://docs.openshift.com/container-platform/4.6/welcome/index.html).