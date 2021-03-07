---
title: Włączanie szyfrowania opartego na hoście w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak skonfigurować szyfrowanie oparte na hoście w klastrze usługi Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.openlocfilehash: f4e599ae7aa81c15f86d0e8b1c934824010ea45b
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430160"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Szyfrowanie oparte na hoście w usłudze Azure Kubernetes Service (AKS) (wersja zapoznawcza)

Przy użyciu szyfrowania opartego na hoście dane przechowywane na hoście maszyny wirtualnej maszyn wirtualnych węzłów agenta AKS są szyfrowane w stanie spoczynku i są zaszyfrowane w usłudze Storage. Oznacza to, że dyski tymczasowe są szyfrowane w stanie spoczynku przy użyciu kluczy zarządzanych przez platformę. Pamięć podręczna systemu operacyjnego i dysków z danymi jest szyfrowana przy użyciu kluczy zarządzanych przez platformę lub kluczy zarządzanych przez klienta w zależności od typu szyfrowania ustawionego na tych dyskach. Domyślnie w przypadku korzystania z AKS, dyski systemu operacyjnego i danych są szyfrowane w stanie spoczynku przy użyciu kluczy zarządzanych przez platformę, co oznacza, że pamięć podręczna dla tych dysków jest również domyślnie szyfrowana w stanie spoczynku przy użyciu kluczy zarządzanych przez platformę.  Możesz określić własne klucze zarządzane, aby przystąpić [do własnych kluczy (BYOK) z dyskami platformy Azure w usłudze Azure Kubernetes Service](azure-disk-customer-managed-keys.md). Pamięć podręczna dla tych dysków zostanie również zaszyfrowana przy użyciu klucza określonego w tym kroku.


## <a name="before-you-begin"></a>Zanim rozpoczniesz

Tę funkcję można ustawić tylko podczas tworzenia klastra lub tworzenia puli węzłów.

> [!NOTE]
> Szyfrowanie oparte na hoście jest dostępne w [regionach platformy Azure][supported-regions] , które obsługują szyfrowanie po stronie serwera dla usługi Azure Managed disks i tylko z określonymi [obsługiwanymi rozmiarami maszyn wirtualnych][supported-sizes].

### <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że masz `aks-preview` zainstalowaną 0.4.73 lub nowszą wersję rozszerzenia interfejsu wiersza polecenia.
- Upewnij się, że masz `EnableEncryptionAtHostPreview` flagę funkcji w obszarze `Microsoft.ContainerService` włączone.

Aby można było używać szyfrowania na hoście dla maszyn wirtualnych lub zestawów skalowania maszyn wirtualnych, należy włączyć tę funkcję w ramach subskrypcji. Wyślij wiadomość e-mail **encryptionAtHost@microsoft.com** z identyfikatorami subskrypcji, aby włączyć funkcję dla subskrypcji. 

> [!IMPORTANT]
> Musisz wysłać wiadomość e-mail **encryptionAtHost@microsoft.com** z identyfikatorami subskrypcji, aby włączyć funkcję dla zasobów obliczeniowych. Nie można włączyć go samodzielnie w przypadku zasobów obliczeniowych.


### <a name="install-aks-preview-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia aks-preview

Aby utworzyć klaster AKS, który jest szyfrowany przy użyciu hosta, potrzebne jest najnowsze rozszerzenie wiersza polecenia *AKS-Preview* . Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure w *wersji zapoznawczej* przy użyciu poleceń [AZ Extension Add][az-extension-add] lub sprawdź, czy są dostępne aktualizacje za pomocą polecenia [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Ograniczenia

- Można włączyć tylko dla nowych pul węzłów.
- Można ją włączyć tylko w [regionach platformy Azure][supported-regions] , które obsługują szyfrowanie po stronie serwera dla usługi Azure Managed disks i tylko z określonymi [obsługiwanymi rozmiarami maszyn wirtualnych][supported-sizes].
- Wymaga klastra AKS i puli węzłów na podstawie Virtual Machine Scale Sets (VMSS) jako *typu zestawu maszyn wirtualnych*.

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>Używanie szyfrowania opartego na hoście w nowych klastrach (wersja zapoznawcza)

Skonfiguruj węzły agenta klastra do używania szyfrowania opartego na hoście podczas tworzenia klastra. 

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Jeśli chcesz utworzyć klastry bez szyfrowania opartego na hoście, możesz to zrobić, pomijając `--enable-encryption-at-host` parametr.

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>Używanie szyfrowania opartego na hoście w istniejących klastrach (wersja zapoznawcza)

Można włączyć szyfrowanie oparte na hoście dla istniejących klastrów, dodając do klastra nową pulę węzłów. Skonfiguruj nową pulę węzłów do korzystania z szyfrowania opartego na hoście przy użyciu `--enable-encryption-at-host` parametru.

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Jeśli chcesz utworzyć nowe pule węzłów bez funkcji szyfrowania opartego na hoście, możesz to zrobić, pomijając `--enable-encryption-at-host` parametr.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi zabezpieczeń klastra AKS][best-practices-security] więcej informacji [na temat szyfrowania opartego na hoście](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
