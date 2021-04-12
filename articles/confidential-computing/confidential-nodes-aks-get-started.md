---
title: 'Szybki Start: Wdrażanie klastra AKS z poufnymi węzłami obliczeniowymi przy użyciu interfejsu wiersza polecenia platformy Azure'
description: Dowiedz się, jak utworzyć klaster usługi Azure Kubernetes Service (AKS) z węzłami poufnymi i wdrożyć aplikację Hello world przy użyciu interfejsu wiersza polecenia platformy Azure.
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 04/08/2021
ms.author: amgowda
ms.custom: contentperf-fy21q3
ms.openlocfilehash: b012a8a5856b344b366f1ddd89fc5059a6f3c8ae
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107283528"
---
# <a name="quickstart-deploy-an-aks-cluster-with-confidential-computing-nodes-by-using-the-azure-cli"></a>Szybki Start: Wdrażanie klastra AKS z poufnymi węzłami obliczeniowymi przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku szybki start użyjesz interfejsu wiersza polecenia platformy Azure, aby wdrożyć klaster usługi Azure Kubernetes Service (AKS) z węzłami do przetwarzania poufnego (DCsv2). Następnie uruchomisz prostą aplikację Hello world w enklawy. Można również zainicjować obsługę administracyjną klastra i dodać poufne węzły przetwarzania z Azure Portal, ale ten przewodnik Szybki Start koncentruje się na interfejsie wiersza polecenia platformy Azure.

AKS to zarządzana usługa Kubernetes, która umożliwia deweloperom i operatorom klastrów szybkie wdrażanie klastrów i zarządzanie nimi. Aby dowiedzieć się więcej, zapoznaj się z [wprowadzeniem do AKS](../aks/intro-kubernetes.md) i [omówieniem AKS poufne węzły](confidential-nodes-aks-overview.md).

Funkcje poufne węzłów obliczeniowych obejmują:

- Węzły procesu roboczego systemu Linux obsługujące kontenery systemu Linux.
- Maszyna wirtualna 2. generacji z węzłami maszyny wirtualnej z Ubuntu 18,04.
- Procesor Intel SGX umożliwia uruchamianie kontenerów w celu poufności chronionej enklawy przy użyciu zaszyfrowanej pamięci podręcznej stron (EPC). Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące usługi Azure poufnego przetwarzania](./faq.md).
- Sterownik Intel SGX DCAP wstępnie zainstalowany w węzłach, które są poufne. Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące usługi Azure poufnego przetwarzania](./faq.md).

> [!NOTE]
> Maszyny wirtualne DCsv2 korzystają z wyspecjalizowanego sprzętu, który podlega wyższej cenie i dostępności regionów. Aby uzyskać więcej informacji, zobacz [dostępne jednostki SKU i obsługiwane regiony](virtual-machine-solutions.md).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

- Aktywna subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Interfejs wiersza polecenia platformy Azure w wersji 2.0.64 lub nowszej został zainstalowany i skonfigurowany na komputerze wdrożenia. 

  Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](../container-registry/container-registry-get-started-azure-cli.md).
- Co najmniej sześć rdzeni DCsv2 dostępnych w ramach subskrypcji. 

  Domyślnie przydział dla poufnych danych na subskrypcję platformy Azure to osiem rdzeni maszyn wirtualnych. Jeśli planujesz obsługę klastra wymagającego więcej niż ośmiu rdzeni, postępuj zgodnie z [tymi instrukcjami](../azure-portal/supportability/per-vm-quota-requests.md) , aby zgłosić bilet wzrostu limitu przydziału.

## <a name="create-an-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Tworzenie klastra AKS z poufnymi węzłami obliczeniowymi i dodatkiem

Skorzystaj z poniższych instrukcji, aby utworzyć klaster AKS z włączonym dodatkiem do obsługi danych poufnych, dodać pulę węzłów do klastra i sprawdzić, co zostało utworzone.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>Tworzenie klastra AKS z pulą węzłów systemowych

> [!NOTE]
> Jeśli masz już klaster AKS spełniający wymienione wcześniej kryteria wymagań wstępnych, [Przejdź do następnej sekcji](#add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster) , aby dodać poufną pulę węzłów obliczeniowych.

Najpierw utwórz grupę zasobów dla klastra za pomocą polecenia [AZ Group Create][az-group-create] . Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w regionie *westus2* :

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Teraz Utwórz klaster AKS z włączonym dodatkiem poufnym, korzystając z polecenia [AZ AKS Create][az-aks-create] :

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```

### <a name="add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster"></a>Dodawanie puli węzłów użytkownika z możliwościami przetwarzania poufnego do klastra AKS 

Uruchom następujące polecenie, aby dodać pulę węzłów użytkownika o `Standard_DC2s_v2` rozmiarze z trzema węzłami do klastra AKS. Możesz wybrać inną jednostkę SKU z [listy obsługiwanych jednostek SKU i regionów DCsv2](../virtual-machines/dcv2-series.md).

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

Po uruchomieniu polecenia, Nowa pula węzłów z DCsv2 powinna być widoczna z użyciem poufnego przetwarzania dodatków DaemonSets ([SGX urządzenia](confidential-nodes-aks-overview.md#confidential-computing-add-on-for-aks)).

### <a name="verify-the-node-pool-and-add-on"></a>Weryfikowanie puli węzłów i dodatku

Pobierz poświadczenia dla klastra AKS za pomocą polecenia [AZ AKS Get-Credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Użyj `kubectl get pods` polecenia, aby sprawdzić, czy węzły zostały prawidłowo utworzone, a DaemonSets powiązane z SGX są uruchomione w pulach węzłów DCsv2:

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Jeśli dane wyjściowe są zgodne z powyższym kodem, klaster AKS jest teraz gotowy do uruchamiania aplikacji poufnych.

W tym przewodniku Szybki Start możesz przejść do sekcji [wdrażanie Hello World z aplikacji izolowanej enklawy](#hello-world) w celu przetestowania aplikacji w enklawy. Lub Skorzystaj z poniższych instrukcji, aby dodać więcej pul węzłów w AKS. (AKS obsługuje mieszanie pul węzłów SGX i pul węzłów innych niż SGX.)

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>Dodawanie poufnej puli węzłów obliczeniowych do istniejącego klastra AKS<a id="existing-cluster"></a>

W tej sekcji założono, że jest już uruchomiony klaster AKS, który spełnia kryteria wymagań wstępnych wymienione wcześniej w tym przewodniku Szybki Start.

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Włącz dodatek AKS do przetwarzania poufnego w istniejącym klastrze

Uruchom następujące polecenie, aby włączyć dodatek do przetwarzania poufnego:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>Dodawanie puli węzłów użytkownika DCsv2 do klastra

> [!NOTE]
> Aby można było korzystać z możliwości przetwarzania poufnego, istniejący klaster AKS musi mieć co najmniej jedną pulę węzłów opartą na jednostce SKU maszyny wirtualnej DCsv2. Aby dowiedzieć się więcej o jednostkach SKU maszyn wirtualnych DC-V2 na potrzeby poufnego przetwarzania danych, zobacz [dostępne jednostki SKU i obsługiwane regiony](virtual-machine-solutions.md).

Uruchom następujące polecenie, aby utworzyć pulę węzłów:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

Sprawdź, czy utworzono nową pulę węzłów o nazwie *confcompool1* :

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Sprawdź, czy DaemonSets są uruchomione na poufnych pulach węzłów

Zaloguj się do istniejącego klastra AKS w celu przeprowadzenia następującej weryfikacji:

```console
kubectl get nodes
```

Dane wyjściowe powinny zawierać nowo dodaną pulę *confcompool1* w klastrze AKS. Zobaczysz również inne DaemonSets.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Jeśli dane wyjściowe są zgodne z powyższym kodem, klaster AKS jest teraz gotowy do uruchamiania aplikacji poufnych. 

## <a name="deploy-hello-world-from-an-isolated-enclave-application"></a>Wdróż Hello world z izolowanej aplikacji enklawy <a id="hello-world"></a>
Teraz można przystąpić do wdrażania aplikacji testowej. 

Utwórz plik o nazwie *Hello-World-enklawy. YAML* i wklej go w następującym manifeście YAML. Ten przykładowy kod aplikacji można znaleźć w [otwartym projekcie enklawy](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). W tym wdrożeniu przyjęto założenie, że został wdrożony dodatek *confcom* .

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

Teraz Użyj `kubectl apply` polecenia, aby utworzyć przykładowe zadanie, które będzie otwierane w bezpiecznej enklawy, jak pokazano w następujących przykładowych danych wyjściowych:

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

Można potwierdzić, że obciążenie pomyślnie utworzyło zaufane środowisko wykonawcze (enklawy), uruchamiając następujące polecenia:

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

Aby usunąć poufną pulę węzłów, która została utworzona w tym przewodniku Szybki Start, użyj następującego polecenia: 

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup
```

Aby usunąć klaster AKS, użyj następującego polecenia: 

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

## <a name="next-steps"></a>Następne kroki

* Uruchamianie języka Python, węzła lub innych aplikacji za pośrednictwem kontenerów poufnych przy użyciu [przykładów kontenera poufnego w usłudze GitHub](https://github.com/Azure-Samples/confidential-container-samples).

* Uruchamiaj aplikacje obsługujące enklawy przy użyciu [przykładów kontenera platformy Azure z obsługą enklawyów w witrynie GitHub](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/).

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
