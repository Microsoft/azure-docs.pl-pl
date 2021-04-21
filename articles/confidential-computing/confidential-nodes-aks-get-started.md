---
title: 'Szybki start: wdrażanie klastra AKS z węzłami poufnego przetwarzania przy użyciu interfejsu wiersza polecenia platformy Azure'
description: Dowiedz się, jak utworzyć klaster usługi Azure Kubernetes Service (AKS) z węzłami poufnymi i wdrożyć aplikację Hello world przy użyciu interfejsu wiersza polecenia platformy Azure.
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 04/08/2021
ms.author: amgowda
ms.custom: contentperf-fy21q3, devx-track-azurecli
ms.openlocfilehash: b1ef397c1a3f6770d197ca8fd0faa83c9e8360f5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816462"
---
# <a name="quickstart-deploy-an-aks-cluster-with-confidential-computing-nodes-by-using-the-azure-cli"></a>Szybki start: wdrażanie klastra AKS z węzłami poufnego przetwarzania przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku Szybki start użyjesz interfejsu wiersza polecenia platformy Azure do wdrożenia klastra usługi Azure Kubernetes Service (AKS) z węzłami poufnego przetwarzania (DCsv2). Następnie będziesz uruchamiać prostą aplikację Hello world w enklawie. Możesz również aprowizowanie klastra i dodawanie poufnych węzłów obliczeniowych z witryny Azure Portal, ale ten przewodnik Szybki start koncentruje się na interfejsie wiersza polecenia platformy Azure.

AKS to zarządzana usługa Kubernetes, która umożliwia deweloperom lub operatorom klastrów szybkie wdrażanie klastrów i zarządzanie nimi. Aby dowiedzieć się więcej, przeczytaj wprowadzenie [do usługi AKS](../aks/intro-kubernetes.md) i [omówienie węzłów poufnych usługi AKS.](confidential-nodes-aks-overview.md)

Funkcje węzłów poufnego przetwarzania obejmują:

- Węzły procesu roboczego systemu Linux, które obsługują kontenery systemu Linux.
- Maszyna wirtualna generacji 2 z węzłami maszyny wirtualnej z systemem Ubuntu 18.04.
- Procesor CPU z możliwością obsługi technologii Intel SGX ułatwiający uruchamianie kontenerów w enklawie chronionej poufnością przy użyciu zaszyfrowanej pamięci podręcznej stronicowania (EPC). Aby uzyskać więcej informacji, zobacz Często zadawane pytania dotyczące poufnego przetwarzania [na platformie Azure.](./faq.md)
- Sterownik Intel SGX DCAP wstępnie zainstalowany w węzłach poufnego przetwarzania. Aby uzyskać więcej informacji, zobacz Często zadawane pytania dotyczące poufnego przetwarzania [na platformie Azure.](./faq.md)

> [!NOTE]
> Maszyny wirtualne DCsv2 używają wyspecjalizowanego sprzętu, który podlega wyższym cenom i dostępności regionów. Aby uzyskać więcej informacji, zobacz dostępne [jednostki SKU i obsługiwane regiony.](virtual-machine-solutions.md)

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

- Aktywna subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Interfejs wiersza polecenia platformy Azure w wersji 2.0.64 lub nowszej zainstalowany i skonfigurowany na maszynie wdrażania. 

  Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](../container-registry/container-registry-get-started-azure-cli.md).
- Co najmniej sześć rdzeni DCsv2 dostępnych w ramach subskrypcji. 

  Domyślnie limit przydziału dla poufnego przetwarzania na subskrypcję platformy Azure wynosi osiem rdzeni maszyn wirtualnych. Jeśli planujesz aprowizować klaster, który [](../azure-portal/supportability/per-vm-quota-requests.md) wymaga więcej niż ośmiu rdzeni, postępuj zgodnie z tymi instrukcjami, aby podnieść bilet zwiększenia limitu przydziału.

## <a name="create-an-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Tworzenie klastra usługi AKS z poufnymi węzłami obliczeniowymi i dodatku

Skorzystaj z poniższych instrukcji, aby utworzyć klaster usługi AKS z włączonym dodatku poufnego przetwarzania, dodać pulę węzłów do klastra i sprawdzić, co utworzono.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>Tworzenie klastra usługi AKS z pulą węzłów systemu

> [!NOTE]
> Jeśli masz już klaster usługi AKS spełniający wymienione wcześniej kryteria [wstępne,](#add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster) przejdź do następnej sekcji, aby dodać pulę poufnych węzłów obliczeniowych.

Najpierw utwórz grupę zasobów dla klastra za pomocą [polecenia az group create.][az-group-create] Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *regionie westus2:*

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Teraz utwórz klaster usługi AKS z włączonym dodatku poufnego przetwarzania za pomocą [polecenia az aks create:][az-aks-create]

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```

### <a name="add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster"></a>Dodawanie puli węzłów użytkownika z możliwościami poufnego przetwarzania do klastra usługi AKS 

Uruchom następujące polecenie, aby dodać pulę węzłów użytkownika `Standard_DC2s_v2` o rozmiarze z trzema węzłami do klastra usługi AKS. Możesz wybrać inną jednostkę SKU z listy obsługiwanych regionów i jednostki [SKU DCsv2.](../virtual-machines/dcv2-series.md)

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

Po uruchomieniu polecenia nowa pula węzłów z DCsv2 powinna być widoczna z dodatku DaemonSets poufnego przetwarzania[(wtyczka urządzenia SGX).](confidential-nodes-aks-overview.md#confidential-computing-add-on-for-aks)

### <a name="verify-the-node-pool-and-add-on"></a>Weryfikowanie puli węzłów i dodatku

Pobierz poświadczenia dla klastra usługi AKS za pomocą polecenia [az aks get-credentials:][az-aks-get-credentials]

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Użyj polecenia , aby sprawdzić, czy węzły zostały utworzone prawidłowo, a zestawy DaemonSet powiązane z SGX są uruchomione w pulach `kubectl get pods` węzłów DCsv2:

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Jeśli dane wyjściowe są zgodnie z powyższym kodem, klaster usługi AKS jest teraz gotowy do uruchamiania poufnych aplikacji.

Możesz przejść do sekcji [Deploy Hello world from an isolated enclave application](#hello-world) (Wdrażanie aplikacji z izolowaną enklawą) w tym przewodniku Szybki start, aby przetestować aplikację w enklawie. Możesz też użyć poniższych instrukcji, aby dodać więcej pul węzłów w udatku AKS. (AKS obsługuje łączenie pul węzłów SGX i pul węzłów innych niż SGX).

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>Dodawanie poufnej puli węzłów obliczeniowych do istniejącego klastra usługi AKS<a id="existing-cluster"></a>

W tej sekcji założono, że masz już uruchomiony klaster usługi AKS spełniający kryteria wymagań wstępnych wymienione wcześniej w tym przewodniku Szybki start.

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Włączanie poufnego przetwarzania dodatku usługi AKS w istniejącym klastrze

Uruchom następujące polecenie, aby włączyć dodatek poufne przetwarzanie:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>Dodawanie puli węzłów użytkownika DCsv2 do klastra

> [!NOTE]
> Aby można było korzystać z funkcji poufnego przetwarzania, istniejący klaster usługi AKS musi mieć co najmniej jedną pulę węzłów opartą na sku maszyny wirtualnej DCsv2. Aby dowiedzieć się więcej na temat jednostki SKU maszyn wirtualnych DCs-v2 do poufnego przetwarzania, zobacz dostępne jednostki [SKU i obsługiwane regiony.](virtual-machine-solutions.md)

Uruchom następujące polecenie, aby utworzyć pulę węzłów:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

Sprawdź, czy została utworzona nowa pula węzłów o nazwie *confcompool1:*

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Sprawdź, czy zestawy DaemonSet są uruchomione w pulach węzłów poufnych

Zaloguj się do istniejącego klastra usługi AKS, aby przeprowadzić następującą weryfikację:

```console
kubectl get nodes
```

Dane wyjściowe powinny pokazywać nowo dodaną *pulę confcompool1* w klastrze usługi AKS. Możesz również zobaczyć inne zestawy DaemonSet.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Jeśli dane wyjściowe są zgodnie z poprzednim kodem, klaster usługi AKS jest teraz gotowy do uruchamiania poufnych aplikacji. 

## <a name="deploy-hello-world-from-an-isolated-enclave-application"></a>Wdrażanie Hello world z aplikacji izolowanej enklawy <a id="hello-world"></a>
Teraz możesz wdrożyć aplikację testową. 

Utwórz plik o nazwie *hello-world-enclave.yaml* i wklej następujący manifest YAML. Ten przykładowy kod aplikacji można znaleźć w [projekcie Open Enclave](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). W tym wdrożeniu przyjęto założenie, że wdrożono dodatek *confcom.*

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            sgx.intel.com/epc: 5Mi # This limit will automatically place the job into a confidential computing node and mount the required driver volumes. Alternatively, you can target deployment to node pools with node selector.
      restartPolicy: Never
  backoffLimit: 0
  ```

Teraz użyj polecenia , aby utworzyć przykładowe zadanie, które zostanie otwarte w bezpiecznej enklawie, jak pokazano `kubectl apply` w następujących przykładowych danych wyjściowych:

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

Możesz potwierdzić, że obciążenie pomyślnie utworzyło zaufane środowisko wykonawcze (enklawę), uruchamiając następujące polecenia:

```console
$ kubectl get jobs -l app=sgx-test

NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test

NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test

Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć pulę węzłów poufnego przetwarzania utworzoną w tym przewodniku Szybki start, użyj następującego polecenia: 

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup
```

Aby usunąć klaster usługi AKS, użyj następującego polecenia: 

```azurecli-interactive
az aks delete --resource-group myResourceGroup --cluster-name myAKSCluster
```

## <a name="next-steps"></a>Następne kroki

* Uruchamianie języka Python, środowiska Node lub innych aplikacji za pośrednictwem poufnych kontenerów przy użyciu [poufnych przykładów kontenerów w usłudze GitHub.](https://github.com/Azure-Samples/confidential-container-samples)

* Uruchamianie aplikacji z wykorzystaniem enklawy przy użyciu przykładów kontenerów platformy Azure z wykorzystaniem [enklawy w usłudze GitHub.](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/)

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
