---
title: 'Szybki Start: Wdrażanie klastra usługi Azure Kubernetes Service (AKS) przy użyciu interfejsu wiersza polecenia platformy Azure z poufnymi węzłami obliczeniowymi'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć klaster AKS z węzłami poufnymi i wdrożyć aplikację Hello World przy użyciu interfejsu wiersza polecenia platformy Azure.
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 03/18/2020
ms.author: amgowda
ms.custom: contentperf-fy21q3
ms.openlocfilehash: a7566cdb22d62bc46df82a3ef0aa78a748769531
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657709"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli"></a>Szybki Start: Wdrażanie klastra usługi Azure Kubernetes Service (AKS) z węzłami w postaci poufnego przetwarzania danych (DCsv2) przy użyciu interfejsu wiersza polecenia platformy Azure

Ten przewodnik Szybki Start jest przeznaczony dla deweloperów lub operatorów klastrów, którzy chcą szybko utworzyć klaster AKS i wdrożyć aplikację do monitorowania aplikacji przy użyciu usługi Managed Kubernetes na platformie Azure. Można również zainicjować obsługę administracyjną klastra i dodać poufne węzły obliczeniowe z Azure Portal.

## <a name="overview"></a>Omówienie

W tym przewodniku szybki start dowiesz się, jak wdrożyć klaster usługi Azure Kubernetes Service (AKS) z węzłami poufnymi, korzystając z interfejsu wiersza polecenia platformy Azure i uruchamiając prostą aplikację Hello World w enklawy. AKS to zarządzana usługa Kubernetes, która umożliwia szybkie wdrażanie klastrów i zarządzanie nimi. Aby dowiedzieć się więcej, przeczytaj artykuł [wprowadzenie do AKS](../aks/intro-kubernetes.md) i [AKS poufne węzły](confidential-nodes-aks-overview.md).

> [!NOTE]
> Maszyny wirtualne służące do przetwarzania danych poufnych DCsv2 korzystają z wyspecjalizowanego sprzętu, który podlega wyższej cenie i dostępności regionów. Aby uzyskać więcej informacji, zobacz stronę maszyny wirtualne w obszarze [dostępne jednostki SKU i obsługiwane regiony](virtual-machine-solutions.md).

### <a name="confidential-computing-node-features-dcsv2"></a>Funkcje węzła poufnego obliczeń (DCsv2)

1. Węzły procesu roboczego systemu Linux obsługujące kontenery systemu Linux.
1. Maszyna wirtualna 2. generacji z Ubuntu 18,04 Virtual Machines węzłami.
1. Procesor Intel SGX oparty na procesorze z zaszyfrowaną pamięcią podręczną strony (EPC). Przeczytaj więcej [tutaj](./faq.md).
1. Obsługa Kubernetes w wersji 1.16 +.
1. Sterownik Intel SGX DCAP został wstępnie zainstalowany na węzłach AKS. Przeczytaj więcej [tutaj](./faq.md).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

1. Aktywna subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
1. Interfejs wiersza polecenia platformy Azure w wersji 2.0.64 lub nowszej został zainstalowany i skonfigurowany na komputerze wdrożenia (Uruchom, `az --version` Aby znaleźć wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](../container-registry/container-registry-get-started-azure-cli.md).
1. Co najmniej sześć rdzeni **DCsv2** dostępnych w ramach subskrypcji do użycia. Domyślnie przydział rdzeni maszyn wirtualnych na potrzeby poufnego przetwarzania danych na subskrypcję platformy Azure to osiem rdzeni. Jeśli planujesz obsługę klastra wymagającego więcej niż ośmiu rdzeni, postępuj zgodnie z [tymi](../azure-portal/supportability/per-vm-quota-requests.md) instrukcjami, aby zgłosić bilet wzrostu limitu przydziału.

## <a name="create-a-new-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Utwórz nowy klaster AKS z poufnymi węzłami obliczeniowymi i dodatkiem

Postępuj zgodnie z poniższymi instrukcjami, aby dodać poufne węzły z obsługą obliczeniową z dodatkiem.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>Tworzenie klastra AKS z pulą węzłów systemowych

Jeśli masz już klaster AKS, który spełnia powyższe wymagania, [Przejdź do istniejącej sekcji klastra](#existing-cluster) , aby dodać nową pulę węzłów w postaci poufnej.

Najpierw utwórz grupę zasobów dla klastra za pomocą polecenia [AZ Group Create][az-group-create] . Poniższy przykład *tworzy nazwę grupy zasobów w* regionie *westus2* :

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Teraz Utwórz klaster AKS przy użyciu polecenia [AZ AKS Create][az-aks-create] :

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```

Powyżej zostanie utworzony nowy klaster AKS z pulą węzłów systemowych z włączonym dodatkiem. Następnie Dodaj pulę węzłów użytkownika z poufnymi możliwościami obliczeniowymi do klastra AKS.

### <a name="add-a-confidential-computing-node-pool-to-the-aks-cluster"></a>Dodawanie poufnej puli węzłów obliczeniowych do klastra AKS 

Uruchom następujące polecenie, aby dodać pulę węzłów użytkownika o `Standard_DC2s_v2` rozmiarze z trzema węzłami. Możesz wybrać inną jednostkę SKU z listy obsługiwanych [jednostek SKU i regionów DCsv2](../virtual-machines/dcv2-series.md).

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

Po uruchomieniu nowej puli węzłów z **DCsv2** powinna być widoczna z dodatkiem Daemonsets ([SGX Device plugin](confidential-nodes-aks-overview.md#sgx-plugin)).

### <a name="verify-the-node-pool-and-add-on"></a>Weryfikowanie puli węzłów i dodatku

Pobierz poświadczenia dla klastra AKS za pomocą polecenia [AZ AKS Get-Credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Sprawdź, czy węzły zostały prawidłowo utworzone, a daemonsets powiązane z SGXą są uruchomione w pulach węzłów **DCsv2** za pomocą polecenia polecenia kubectl & Get węzłów, jak pokazano poniżej:

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Jeśli dane wyjściowe są zgodne z powyższymi, klaster AKS jest teraz gotowy do uruchamiania aplikacji poufnych.

Przejdź do sekcji [Hello World z wdrożenia enklawy](#hello-world) , aby przetestować aplikację w enklawy. Lub postępuj zgodnie z poniższymi instrukcjami, aby dodać dodatkowe pule węzłów w AKS (AKS obsługuje łączenie pul węzłów SGX i puli węzłów innych niż SGX).

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>Dodawanie poufnej puli węzłów obliczeniowych do istniejącego klastra AKS<a id="existing-cluster"></a>

W tej sekcji założono, że klaster AKS, który jest już uruchomiony, spełnia kryteria wymienione w sekcji wymagania wstępne (dotyczy dodatku).

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Włącz dodatek AKS do przetwarzania poufnego w istniejącym klastrze

Uruchom następujące polecenie, aby włączyć dodatek do przetwarzania poufnego:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>Dodawanie puli węzłów użytkownika **DCsv2** do klastra

> [!NOTE]
> Aby można było korzystać z możliwości przetwarzania poufnego, istniejący klaster AKS musi mieć co najmniej jedną pulę węzłów opartą na maszynie wirtualnej **DCsv2** . Aby dowiedzieć się więcej o jednostkach SKU maszyn wirtualnych z systemem v2, zobacz [dostępne jednostki SKU i obsługiwane regiony](virtual-machine-solutions.md).

Uruchom następujące polecenie, aby utworzyć nową pulę węzłów:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

Sprawdź, czy utworzono nową pulę węzłów o nazwie confcompool1:

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Sprawdź, czy daemonsets są uruchomione na poufnych pulach węzłów

Zaloguj się do istniejącego klastra AKS, aby przeprowadzić następującą weryfikację.

```console
kubectl get nodes
```

Dane wyjściowe powinny zawierać nowo dodane confcompool1 w klastrze AKS. Zobaczysz również inne daemonsets.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Jeśli dane wyjściowe są zgodne z powyższymi, klaster AKS jest teraz gotowy do uruchamiania aplikacji poufnych. Postępuj zgodnie z poniższymi instrukcjami, aby wdrożyć aplikację testową.

## <a name="hello-world-from-isolated-enclave-application"></a>Hello world z izolowanej aplikacji enklawy <a id="hello-world"></a>
Utwórz plik o nazwie *Hello-World-enklawy. YAML* i wklej następujący manifest YAML. Ten otwarty kod przykładowej aplikacji na podstawie enklawy można znaleźć w [otwartym projekcie enklawy](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). W poniższym wdrożeniu przyjęto założenie, że został wdrożony dodatek "confcom".

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
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 5 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
  ```

Teraz użyj polecenia polecenia kubectl Apply, aby utworzyć przykładowe zadanie, które zostanie uruchomione w zabezpieczonym enklawy, jak pokazano w następujących przykładowych danych wyjściowych:

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

Aby usunąć skojarzone pule węzłów lub usunąć klaster AKS, użyj następujących poleceń:

### <a name="remove-the-confidential-computing-node-pool"></a>Usuń poufną pulę węzłów obliczeniowych

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
```

### <a name="delete-the-aks-cluster"></a>Usuwanie klastra AKS

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

## <a name="next-steps"></a>Następne kroki

* Uruchamiaj w języku Python, węźle itd. aplikacje poufne za pomocą kontenerów poufnych, odwiedzając [poufne przykłady kontenerów](https://github.com/Azure-Samples/confidential-container-samples).

* Uruchom aplikacje obsługujące enklawy, odwiedzając [przykłady kontenerów platformy Azure z obsługą enklawy](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/).

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials