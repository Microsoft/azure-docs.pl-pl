---
title: Włączanie szyfrowania opartego na hoście w Azure Kubernetes Service (AKS)
description: Dowiedz się, jak skonfigurować szyfrowanie oparte na hoście w klastrze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7eb3215aeb1f7c6508092d18fbebd90f852efe63
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772923"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Szyfrowanie oparte na hoście w Azure Kubernetes Service (AKS) (wersja zapoznawcza)

W przypadku szyfrowania opartego na hoście dane przechowywane na hoście maszyny wirtualnej maszyn wirtualnych węzłów agenta usługi AKS są szyfrowane podczas magazynowania i są szyfrowane w usłudze Storage. Oznacza to, że dyski tymczasowe są szyfrowane w spoczynku przy użyciu kluczy zarządzanych przez platformę. Pamięć podręczna dysków systemu operacyjnego i danych jest szyfrowana przy użyciu kluczy zarządzanych przez platformę lub kluczy zarządzanych przez klienta w zależności od typu szyfrowania ustawionego na tych dyskach. Domyślnie w przypadku korzystania z usługi AKS dyski systemu operacyjnego i danych są szyfrowane przy użyciu kluczy zarządzanych przez platformę, co oznacza, że pamięci podręczne dla tych dysków są również domyślnie szyfrowane przy użyciu kluczy zarządzanych przez platformę.  Możesz określić własne klucze zarządzane, korzystając z usługi [Bring your own keys (BYOK) z](azure-disk-customer-managed-keys.md)dyskami platformy Azure w usłudze Azure Kubernetes Service . Pamięć podręczna tych dysków również zostanie zaszyfrowana przy użyciu klucza, który został określony w tym kroku.


## <a name="before-you-begin"></a>Zanim rozpoczniesz

Tę funkcję można ustawić tylko podczas tworzenia klastra lub tworzenia puli węzłów.

> [!NOTE]
> Szyfrowanie oparte na hoście jest dostępne w regionach świadczenia [usługi Azure,][supported-regions] które obsługują szyfrowanie dysków zarządzanych platformy Azure po stronie serwera i tylko z określonymi [obsługiwanymi rozmiarami maszyn wirtualnych.][supported-sizes]

### <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że masz zainstalowane rozszerzenie interfejsu wiersza polecenia w wersji `aks-preview` 0.4.73 lub wyższej.
- Upewnij się, że `EnableEncryptionAtHostPreview` flaga funkcji jest `Microsoft.ContainerService` włączona.

Należy włączyć tę funkcję dla subskrypcji przed użyciem właściwości EncryptionAtHost dla klastra Azure Kubernetes Service klastra. Wykonaj poniższe kroki, aby włączyć funkcję dla subskrypcji:

1. Wykonaj następujące polecenie, aby zarejestrować funkcję dla subskrypcji

```azurecli-interactive
Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"
```
2. Przed wypróbowaniem funkcji sprawdź, czy stan rejestracji to Zarejestrowano (trwa kilka minut) przy użyciu poniższego polecenia.

```azurecli-interactive
Get-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"
```

### <a name="install-aks-preview-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia aks-preview

Aby utworzyć klaster AKS z szyfrowaniem opartym na hoście, potrzebne jest najnowsze rozszerzenie interfejsu wiersza *polecenia aks-preview.* Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure *aks-preview* za pomocą [polecenia az extension add][az-extension-add] lub sprawdź dostępne aktualizacje za pomocą polecenia az extension [update:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Ograniczenia

- Można ją włączyć tylko w nowych pulach węzłów.
- Można ją włączyć tylko w [regionach platformy Azure,][supported-regions] które obsługują szyfrowanie dysków zarządzanych platformy Azure po stronie serwera i tylko z określonymi [obsługiwanymi rozmiarami maszyn wirtualnych.][supported-sizes]
- Wymaga klastra usługi AKS i puli węzłów na podstawie Virtual Machine Scale Sets(VMSS) jako *typu zestawu maszyn wirtualnych.*

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>Używanie szyfrowania opartego na hoście w nowych klastrach (wersja zapoznawcza)

Skonfiguruj węzły agenta klastra do używania szyfrowania opartego na hoście podczas tworzenia klastra. 

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Jeśli chcesz utworzyć klastry bez szyfrowania opartego na hoście, możesz to zrobić, pomijając `--enable-encryption-at-host` parametr .

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>Używanie szyfrowania opartego na hoście w istniejących klastrach (wersja zapoznawcza)

Możesz włączyć szyfrowanie oparte na hoście w istniejących klastrach, dodając nową pulę węzłów do klastra. Skonfiguruj nową pulę węzłów do używania szyfrowania opartego na hoście przy użyciu `--enable-encryption-at-host` parametru .

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Jeśli chcesz utworzyć nowe pule węzłów bez funkcji szyfrowania opartego na hoście, możesz to zrobić, pomijając `--enable-encryption-at-host` parametr .

## <a name="next-steps"></a>Następne kroki

Zapoznaj [się z najlepszymi rozwiązaniami w zakresie zabezpieczeń klastra usługi AKS][best-practices-security] Przeczytaj więcej na [temat szyfrowania opartego na hoście.](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
