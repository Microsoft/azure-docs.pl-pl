---
title: Konfiguracja klastra w usłudze Azure Kubernetes Services (AKS)
description: Dowiedz się, jak skonfigurować klaster w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 06/20/2020
ms.openlocfilehash: 43aadd52f17367b488fcec086404caaba9158f33
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85205779"
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

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Jeśli chcesz utworzyć klaster regularnego Ubuntu 16,04, możesz to zrobić, pomijając `--aks-custom-headers` znacznik niestandardowy.

### <a name="existing-clusters"></a>Istniejące klastry

Skonfiguruj nową pulę węzłów do używania Ubuntu 18,04. Użyj `--aks-custom-headers` flagi, aby ustawić Ubuntu 18,04 jako domyślny system operacyjny dla tej puli węzłów.

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Jeśli chcesz utworzyć regularne pule węzłów Ubuntu 16,04, możesz to zrobić, pomijając `--aks-custom-headers` tag niestandardowy.

## <a name="generation-2-virtual-machines-preview"></a>Maszyny wirtualne generacji 2 (wersja zapoznawcza)
Platforma Azure obsługuje [maszyny wirtualne generacji 2 (Gen2)](../virtual-machines/windows/generation-2.md). Maszyny wirtualne generacji 2 obsługują kluczowe funkcje, które nie są obsługiwane w maszynach wirtualnych generacji 1 (Gen1). Te funkcje obejmują zwiększoną ilość pamięci, rozszerzenia funkcji ochrony oprogramowania firmy Intel (Intel SGX) i zwirtualizowaną pamięć trwałą (vPMEM).

Maszyny wirtualne generacji 2 wykorzystują nową architekturę rozruchową opartą na interfejsie UEFI zamiast architektury opartej na systemie BIOS używanej przez maszyny wirtualne generacji 1.
Tylko określone jednostki SKU i rozmiary obsługują maszyny wirtualne Gen2. Sprawdź [listę obsługiwanych rozmiarów](../virtual-machines/windows/generation-2.md#generation-2-vm-sizes), aby sprawdzić, czy jednostka SKU obsługuje lub wymaga Gen2.

Ponadto nie wszystkie obrazy maszyn wirtualnych obsługują Gen2, na maszynach wirtualnych AKS Gen2 będzie używany nowy [obraz AKS Ubuntu 18,04](#os-configuration-preview). Ten obraz obsługuje wszystkie jednostki SKU i rozmiary Gen2.

Aby korzystać z maszyn wirtualnych Gen2 w wersji zapoznawczej, wymagane są:
- `aks-preview`Rozszerzenie interfejsu wiersza polecenia zostało zainstalowane.
- `Gen2VMPreview`Zarejestrowano flagę funkcji.

Zarejestruj `Gen2VMPreview` funkcję:

```azurecli
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```

Wyświetlenie stanu jako **zarejestrowanego**może potrwać kilka minut. Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```

Gdy stan jest wyświetlany jako zarejestrowane, Odśwież rejestrację `Microsoft.ContainerService` dostawcy zasobów przy użyciu polecenia [AZ Provider Register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) :

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Aby zainstalować rozszerzenie interfejsu wiersza polecenia AKS-Preview, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli
az extension add --name aks-preview
```

Aby zaktualizować rozszerzenie interfejsu wiersza polecenia AKS-Preview, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli
az extension update --name aks-preview
```

### <a name="new-clusters"></a>Nowe klastry
Skonfiguruj klaster tak, aby korzystał z maszyn wirtualnych Gen2 dla wybranej jednostki SKU podczas tworzenia klastra. Użyj `--aks-custom-headers` flagi, aby ustawić Gen2 jako generację maszyny wirtualnej w nowym klastrze.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Jeśli chcesz utworzyć zwykły klaster przy użyciu maszyn wirtualnych generacji 1 (Gen1), możesz to zrobić, pomijając `--aks-custom-headers` znacznik niestandardowy. Możesz również dodać więcej maszyn wirtualnych Gen1 lub Gen2, tak jak na poniższej liście.

### <a name="existing-clusters"></a>Istniejące klastry
Skonfiguruj nową pulę węzłów do używania maszyn wirtualnych Gen2. Użyj `--aks-custom-headers` flagi, aby ustawić Gen2 jako generację maszyny wirtualnej dla tej puli węzłów.

```azure-cli
az aks nodepool add --name gen2 --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Jeśli chcesz utworzyć regularne pule węzłów Gen1, możesz to zrobić, pomijając `--aks-custom-headers` znacznik niestandardowy.

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
