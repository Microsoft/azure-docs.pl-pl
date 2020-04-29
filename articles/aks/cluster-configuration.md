---
title: Konfiguracja klastra w usłudze Azure Kubernetes Services (AKS)
description: Dowiedz się, jak skonfigurować klaster w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 94f84beee2d7a76e48ac1470a0ce0b387929cc08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79479165"
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

Gdy stan jest wyświetlany jako zarejestrowane, Odśwież rejestrację dostawcy `Microsoft.ContainerService` zasobów przy użyciu polecenia [AZ Provider Register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) :

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Skonfiguruj klaster tak, aby używał Ubuntu 18,04 podczas tworzenia klastra. Użyj `--aks-custom-headers` flagi, aby ustawić Ubuntu 18,04 jako domyślny system operacyjny.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Jeśli chcesz utworzyć klaster regularnego Ubuntu 16,04, możesz to zrobić, pomijając znacznik niestandardowy `--aks-custom-headers` .

## <a name="custom-resource-group-name"></a>Nazwa niestandardowej grupy zasobów

Podczas wdrażania klastra usługi Azure Kubernetes na platformie Azure zostanie utworzona druga grupa zasobów dla węzłów procesu roboczego. Domyślnie AKS będzie nazwać grupę `MC_resourcegroupname_clustername_location`zasobów węzła, ale możesz również podać własną nazwę.

Aby określić własną nazwę grupy zasobów, zainstaluj rozszerzenie AKS-Preview interfejsu wiersza polecenia platformy Azure w wersji 0.3.2 lub nowszej. Korzystając z interfejsu wiersza `--node-resource-group` `az aks create` polecenia platformy Azure, użyj parametru polecenie, aby określić niestandardową nazwę grupy zasobów. W przypadku wdrażania klastra AKS za pomocą szablonu Azure Resource Manager można zdefiniować nazwę grupy zasobów za pomocą `nodeResourceGroup` właściwości.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Dodatkowa grupa zasobów jest automatycznie tworzona przez dostawcę zasobów platformy Azure we własnej subskrypcji. Należy pamiętać, że podczas tworzenia klastra można określić niestandardową nazwę grupy zasobów. 

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