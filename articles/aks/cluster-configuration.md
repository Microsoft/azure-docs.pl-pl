---
title: Konfiguracja klastra w usłudze Azure Kubernetes Services (AKS)
description: Dowiedz się, jak skonfigurować klaster w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fe5ce13d9db8f2bc2231f87de7e602e63d239bfa
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725150"
---
# <a name="configure-an-aks-cluster"></a>Konfigurowanie klastra AKS

W ramach tworzenia klastra AKS może być konieczne dostosowanie konfiguracji klastra zgodnie z potrzebami. W tym artykule przedstawiono kilka opcji dostosowywania klastra AKS.

## <a name="os-configuration-preview"></a>Konfiguracja systemu operacyjnego (wersja zapoznawcza)

Program AKS obsługuje teraz Ubuntu 18,04 jako system operacyjny węzła (OS) w wersji zapoznawczej. W trakcie okresu zapoznawczego dostępne są zarówno Ubuntu 16,04, jak i Ubuntu 18,04.

Wymagane są następujące zasoby:

- Interfejs wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej
- Rozszerzenie AKS-Preview 0.4.35

Aby zainstalować rozszerzenie AKS-Preview 0.4.35 lub nowsze, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli
az extension add --name aks-preview
az extension list
```

Zarejestruj `UseCustomizedUbuntuPreview` funkcję:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Wyświetlenie stanu jako **zarejestrowanego**może potrwać kilka minut. Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Gdy stan jest wyświetlany jako zarejestrowane, Odśwież rejestrację `Microsoft.ContainerService` dostawcy zasobów przy użyciu polecenia [AZ Provider Register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) :

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="new-clusters"></a>Nowe klastry

Skonfiguruj klaster tak, aby używał Ubuntu 18,04 podczas tworzenia klastra. Użyj `--aks-custom-headers` flagi, aby ustawić Ubuntu 18,04 jako domyślny system operacyjny.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Jeśli chcesz utworzyć klaster regularnego Ubuntu 16,04, możesz to zrobić, pomijając `--aks-custom-headers` znacznik niestandardowy.

### <a name="existing-clusters"></a>Istniejące klastry

Skonfiguruj nową pulę węzłów do używania Ubuntu 18,04. Użyj `--aks-custom-headers` flagi, aby ustawić Ubuntu 18,04 jako domyślny system operacyjny dla tej puli węzłów.

```azure-cli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Jeśli chcesz utworzyć regularne pule węzłów Ubuntu 16,04, możesz to zrobić, pomijając `--aks-custom-headers` tag niestandardowy.


## <a name="custom-resource-group-name"></a>Nazwa niestandardowej grupy zasobów

Podczas wdrażania klastra usługi Azure Kubernetes na platformie Azure zostanie utworzona druga grupa zasobów dla węzłów procesu roboczego. Domyślnie AKS będzie nazwać grupę zasobów węzła `MC_resourcegroupname_clustername_location` , ale możesz również podać własną nazwę.

Aby określić własną nazwę grupy zasobów, zainstaluj rozszerzenie AKS-Preview interfejsu wiersza polecenia platformy Azure w wersji 0.3.2 lub nowszej. Korzystając z interfejsu wiersza polecenia platformy Azure, użyj `--node-resource-group` parametru `az aks create` polecenie, aby określić niestandardową nazwę grupy zasobów. W przypadku wdrażania klastra AKS za pomocą szablonu Azure Resource Manager można zdefiniować nazwę grupy zasobów za pomocą `nodeResourceGroup` właściwości.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Dodatkowa grupa zasobów jest automatycznie tworzona przez dostawcę zasobów platformy Azure we własnej subskrypcji. Podczas tworzenia klastra można określić niestandardową nazwę grupy zasobów. 

Podczas pracy z grupą zasobów węzła należy pamiętać, że nie można:

- Określ istniejącą grupę zasobów dla grupy zasobów węzła.
- Określ inną subskrypcję dla grupy zasobów węzła.
- Zmień nazwę grupy zasobów węzła po utworzeniu klastra.
- Określ nazwy zarządzanych zasobów w grupie zasobów węzła.
- Modyfikuj lub Usuń Tagi zarządzane przez platformę Azure w ramach grupy zasobów węzła.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się `Kured` , jak [stosować aktualizacje zabezpieczeń i jądra do węzłów systemu Linux](node-updates-kured.md) w klastrze.
- Zobacz [Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)](upgrade-cluster.md) , aby dowiedzieć się, jak uaktualnić klaster do najnowszej wersji Kubernetes.
- Zapoznaj się z listą [często zadawanych pytań dotyczących AKS](faq.md) , aby znaleźć odpowiedzi na niektóre często zadawane pytania dotyczące AKS.